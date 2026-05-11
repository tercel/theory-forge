---
name: cite-audit
description: >
  Audit every citation in an academic theory project for truth, attribution, and bibliography
  completeness. Catches four failure modes: fabricated citations (paper does not exist),
  mis-attribution (paper exists but does not support the claim), bibliography orphans (cited
  but not in bibliography), and unused entries (in bibliography but never cited). Verifies
  paper existence via CrossRef / Semantic Scholar / OpenAlex.
instructions: >
  This skill is read-only by default. It produces a findings report and only proposes edits
  at the end with explicit user opt-in. Every finding must cite the specific file, line, and
  surrounding claim text. Be conservative on "fabricated" — only mark as fabricated when
  multiple external sources independently fail to match.
---

# cite-audit — Citation Truth and Bibliography Integrity

Audit an academic theory project for citation accuracy. The four failure modes:

| Failure mode | What it means | Severity |
|---|---|---|
| **Fabricated** | Cited paper does not exist in any indexed source | Critical |
| **Mis-attributed** | Paper exists, authorship is correct, but the paper does not say what the inline claim asserts | Major |
| **Orphan** | Inline citation has no matching entry in the project bibliography | Major |
| **Unused** | Bibliography entry has no matching inline citation | Minor |
| **Malformed** | Bibliography entry does not parse as a valid citation | Minor |
| **Ambiguous** | Two bibliography entries share `(author, year)`; inline citation lacks disambiguating letter | Minor |
| **Unverifiable** | Paper could not be confirmed in any source (often pre-1980 chapters, dissertations) | Info |

## Core Principles

1. **Conservative on Critical.** "Fabricated" findings are corrosive to trust if wrong. Only mark a citation as fabricated when CrossRef + Semantic Scholar + OpenAlex all return no match AND the title has ≥3 specific content words.
2. **Read-only by default.** Edits to bibliography or inline citations require explicit user opt-in at Step 7.
3. **Cite locations precisely.** Every finding records the file path and line number (or section heading) where the citation appears, plus the surrounding sentence.
4. **No silent skipping.** If a citation cannot be parsed, report it as malformed; do not omit it from the audit.
5. **Severity discipline.** Apply the rubric in `../shared/academic-severity-levels.md`.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/citation-parser.md` — inline-citation regex set, bibliography schema, canonical key, WebFetch query construction
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/citation-format.md` — load at Step 3 ONLY IF the bibliography style needs detection (e.g., mixed-format suspicion)
- `../shared/replication-and-preregistration.md` — load at Step 5 ONLY IF performing retraction checks on entries with DOIs
- `../shared/output-templates/cite-audit-report.md` — load at Step 7 (Report Generation)

## Workflow

### Step 1: Locate Project and Bibliography

Parse the target path (default: current working directory).

1. Verify the target has a `docs/` directory or markdown files at root.
2. Locate the bibliography file. Search in order:
   - `docs/**/bibliography.md`
   - `docs/**/references.md`
   - `bibliography.md` at root
   - A heading `## References` or `## Bibliography` in `README.md`
3. If multiple bibliography files exist (e.g., per-language mirrors), ask the user which is canonical.
4. If none found, report: *No bibliography detected. cite-audit requires a bibliography file. Suggest creating `docs/bibliography.md`.* Stop.

### Step 2: Build the Inline-Citation Index

Glob all `docs/**/*.md` files (excluding `_research/`, `_drafts/`, anything underscore-prefixed).

For each file:
1. Apply the inline-citation regex set from `citation-parser.md` §1.
2. For each match, record:
   - file path
   - line number
   - the surrounding sentence (the **claim**)
   - the parsed canonical key (`{surname} {year}`)
3. Deduplicate by canonical key per file; preserve all locations across files.

Display a summary:

```
Inline citations:
  Distinct canonical keys: 87
  Total occurrences: 234
  Files scanned: 19
```

### Step 3: Parse the Bibliography

Apply the bibliography schema from `citation-parser.md` §3.

For each entry:
1. Extract `authors`, `year`, `title`, `venue`, optional `doi` / `url`.
2. Compute canonical key.
3. Flag malformed entries (those that fail to parse) as **Minor — malformed**.

Display:

```
Bibliography entries:
  Total: 92
  Parsed successfully: 90
  Malformed: 2
```

### Step 4: Match Inline Citations to Bibliography

For each canonical key in the inline-citation index:

| Bibliography lookup result | Severity | Category |
|---|---|---|
| Exact match | — | OK |
| No match | Major | **Orphan** |
| Multiple matches (e.g., `Levy 2007` and `Levy 2007a`) | Minor | **Ambiguous** |

For each canonical key in the bibliography with no matching inline citation: Minor — **Unused**.

### Step 5: WebFetch Paper-Existence Verification

For each bibliography entry that has at least one inline citation (skip unused entries — already flagged Minor):

1. Construct CrossRef query per `citation-parser.md` §5.1.
2. WebFetch the query. Parse top 3 results.
3. Match criterion: top result's first-author surname matches AND year matches ± 1.
4. If no CrossRef match: fall back to Semantic Scholar (§5.2).
5. If no Semantic Scholar match: fall back to OpenAlex (§5.3).
6. Classify:
   - At least one source matches → **Verified** (no finding)
   - All sources return zero results AND title has ≥3 specific content words → **Fabricated** (Critical)
   - All sources return zero results AND title is generic → **Unverifiable** (Info)
   - Sources return results but none match author + year + title-keyword overlap → **Author/year mismatch** (Major)

Rate-limit considerately: max 3 concurrent WebFetch calls; introduce 200ms spacing between batches. If the project has >100 bibliography entries, batch in groups of 20 and display progress.

### Step 6: Mis-attribution Analysis

For each verified citation, perform keyword-alignment check.

> **Calibration caveat.** Keyword overlap is a coarse heuristic, not a semantic test. It is reliable for catching gross mis-attributions (a paper on "syntactic priming" cited for a claim about "phonological assimilation") but unreliable for legitimate tangential citations (a methodology-of-X paper cited for a remark about Y). The thresholds below are seed values; calibrate against false-positive rate on the project's specific corpus before treating them as authoritative.

#### Procedure

1. **Fetch the paper's abstract.** CrossRef returns the `abstract` field for a subset of entries (coverage varies by publisher and discipline; many older entries lack abstracts). Semantic Scholar covers a higher fraction than CrossRef but is rate-limited and is not exhaustive. OpenAlex provides abstract reconstructions from inverted indexes for a substantial portion of papers but accuracy varies. *[Type B — design rule]*: attempt all three sources before declaring an abstract unavailable; do not assume any single source has the abstract.
2. **Abstract-missing fallback.** If no source has an abstract:
   - Use the title only as `paper_topics`
   - Flag the finding as **alignment-uncheckable** at Info severity (never escalate to Major)
   - Do not guess
3. **Extract content words** from the title + abstract — call this `paper_topics`. Claude can do this inline by filtering stopwords (English: Penn Treebank closed-class POS; for other languages, a short hand-curated list) and very common abstract boilerplate ("paper", "study", "research", "results").
4. **Extract content words** from the inline-citation **claim** (the surrounding sentence and ≤30 words before/after) — call this `claim_topics`.
5. **Compute keyword overlap**: `|claim_topics ∩ paper_topics| / |claim_topics|` (asymmetric — what fraction of the claim's content words appear in the paper's topic set).

#### Thresholds (heuristic seeds) — *[Type B — design choice, project-tunable]*

The following thresholds are **calibration seed values**, not empirically validated optima. They are theory-forge's initial defaults; projects with unusual citation styles (e.g., heavy formal-notation use, very terse claim sentences, or technical-jargon-dense abstracts) may need re-tuning. There is no published study validating these specific cut-points.

| Overlap | Provisional verdict |
|---|---|
| ≥ 0.40 | **Aligned** — no finding |
| 0.15–0.40 | **Weak alignment** — Info (human review queue, not auto-flagged) |
| < 0.15, claim has ≥ 8 content words | **Mis-attributed** — Major |
| < 0.15, claim has < 8 content words | **Insufficient signal** — Info (claim too short for reliable comparison) |
| Abstract missing for paper | **Alignment-uncheckable** — Info |

If a project's first cite-audit run produces too many false-positive Major findings, tune the 0.15 threshold upward; if too many real mis-attributions go undetected, tune downward.

#### Downweighting rules (false-positive defense)

- **Classic citations**: papers cited 5+ times across the project (Chomsky 1986, Halliday 1967, Lambrecht 1994, etc.) are used in many contexts. Downweight mis-attribution severity from Major to Info — too many context-dependent uses to flag reliably.
- **Methodology citations**: a paper cited as the source of a *method* may have an abstract about a *different topic*. If the claim contains words like "method", "approach", "framework", "technique" — downweight severity by one level.
- **Edited-volume chapters**: when the bibliography entry is a book chapter (heuristic: "In *...*" pattern), the CrossRef abstract may be for the entire volume, not the chapter — flag at Info, never Major.
- **Co-author scoped claims**: if the inline citation uses non-first-author shorthand (per `citation-parser.md` §4.1), the paper's primary topic may differ from the cited author's contribution — flag at Info.
- **Hypothesis-extension citations** (大胆假设 pattern): when a citation supports a *related but different* claim than what the inline sentence asserts AND the surrounding prose contains a hypothesis marker, the author is **extending the citation's finding to a new hypothesis** — not mis-attributing. Pattern:
  > "*[Hypothesis, unverified]:* We extend Smith (2010)'s finding about X to predict Y in the CFLT context."
  > "Following the methodology of Liu et al. (2023), we predict the same positional bias holds for CFLT-formatted prompts (untested)."

  The citation supports the *anchor* (X / the methodology), not the *extension* (Y / the prediction). This is honest scholarship, not mis-attribution. Downweight to **Info** (positive practice — extension is clearly framed as a hypothesis).

  Detection: if (a) the claim sentence contains a hypothesis marker AND (b) the cited paper's abstract aligns with a *subset* of the claim (the anchor) but not the full claim, classify as hypothesis-extension. Do NOT flag as mis-attribution.

#### Per-skill self-test

Before a fix offer is shown to the user, the skill should self-check: are at least 50% of citations flagged at "Aligned"? If fewer than 50% of citations are aligned, that suggests either the threshold is too strict OR there is a systematic project-wide style issue (e.g., terse claim sentences) — surface this and recommend human calibration before trusting the report.

### Step 7: Report Generation

Write the report to `_research/citation-audit.md`. Create the `_research/` directory if it does not exist (warn the user it should be added to `.gitignore` or the mkdocs `exclude_docs` list if not already).

**Report structure**: read the template at `../shared/output-templates/cite-audit-report.md` and fill in the `{placeholder}` values. Preserve the heading hierarchy and severity-ordering convention. The template is loaded only at this step — not on skill startup — to save token budget for the main detection workflow.

### Step 8: Offer Fixes (opt-in, severity-gated)

**Critical findings are NEVER auto-fix candidates.** A "fabricated" citation may be a false positive (mis-classified obscure source) or a legitimate problem requiring human judgement. Auto-deleting or auto-replacing a Critical-flagged citation can cause the audit tool to silently destroy a real reference. **Halt instead.**

Severity-by-severity fix policy:

| Severity | Auto-fix offered? | Behavior |
|---|---|---|
| **Critical** | **NEVER** | Display the finding prominently. Require user to acknowledge before continuing. No auto-replacement, no auto-deletion. |
| **Major (orphan)** | Yes, with per-item confirmation | Build a candidate bibliography entry from CrossRef metadata. Show as diff. Apply only on explicit "yes". |
| **Major (mis-attribution)** | **No auto-fix.** Propose-only mode. | Show the suspected mis-attribution and the user is told what to investigate. Do NOT propose a replacement citation — that would compound the error. The user must decide. |
| **Minor** | Yes, batchable | Format fixes, unused-entry pruning suggestions. |
| **Info** | Reporting only | Never auto-fix. |

If Critical findings exist, the fix-offer prompt is:

> **{N} Critical findings (fabricated citations). These require your direct review — I cannot safely auto-fix.**
> Once you have reviewed and decided on each Critical finding, run `/theory-forge:cite-audit` again to re-audit.

If Critical = 0 but Major/Minor exist:

> Found {N} Major + {M} Minor issues (no Critical).
> 1. Auto-add bibliography entries for orphans with high-confidence CrossRef matches (per-item confirmation)
> 2. List proposed batch fixes for Minor issues
> 3. Just save the report and exit (default)

If the user picks 1:
- For each orphan with a verified CrossRef match, show the proposed entry as a diff
- Apply only after explicit "yes" per item (or "yes to all of category X")
- If CrossRef match confidence is below threshold (no exact title/author match), do NOT auto-suggest — list as user-action-required
- Never modify a bibliography entry the user did not approve
- Never modify an inline citation in mis-attribution mode

## Output Schema (per Finding)

Each finding in the report uses this YAML-style structure for machine readability:

```yaml
- id: F001
  severity: Critical
  category: fabricated
  citation_key: "smith 2024"
  inline_locations:
    - file: docs/en/foundations/core-concept.md
      line: 142
      claim: "Smith (2024) showed that..."
  search_log:
    crossref: 0 results
    semantic_scholar: 0 results
    openalex: 0 results
  title_specificity: 4 content words ("framework cognitive linguistic theory")
  fix_suggestion: >
    Remove citation, or replace with a verifiable source for the claim about
    cognitive linguistic frameworks.
```

## Anti-patterns

Do **not**:
- Mark `(Smith 2024)` as fabricated when the claim is a generic statement that Smith might have made — verify first.
- Over-trust CrossRef for pre-2000 chapters, edited volumes, and dissertations.
- Auto-fix citations without showing the user the diff.
- Bundle Minor and Info findings into the "Status: REVIEW REQUIRED" trigger — only Critical + Major escalate status.
- Run keyword alignment on citations where the abstract is unavailable; mark those Info-level "alignment-uncheckable" instead of guessing.
- Report the same orphan multiple times if it appears in N files; report once with N inline locations.

## Exit Conditions

- **Clean exit (Status: PASS)**: 0 Critical, 0 Major. Suggest re-running after future edits.
- **Review required**: ≥1 Critical or ≥3 Major. Stop short of auto-fix; require user attention.
- **Inconclusive**: WebFetch failed for >20% of entries (e.g., network issue). Report partial results and recommend retry.
