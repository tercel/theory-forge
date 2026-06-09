---
name: evidence-strength
description: >
  Audit the claim-to-source support relation in an academic theory document.
  For every claim that cites a source (or asserts a quantitative / mechanistic
  result implying one), judge whether the source actually establishes what the
  claim asserts — and at the same strength and scope. Assigns a seven-level
  evidence-status verdict (accurate / accurate-with-caveat / overstated /
  indirect / misattributed / counterevidence / unsupported) and a graded
  remediation, including — at the bottom rung only — a propose-only
  recommendation to remove a citation whose support is absent. Read-only by
  default.
instructions: >
  This is the deep read that cite-audit's keyword screen and falsifiability's
  marker check both leave open: a citation can be real, correctly attributed,
  and marked as evidence while still over-leveraging its source. Be a charitable
  reader — prefer the lower-severity verdict when in doubt, and require the
  source to be actually checked before any finding above Info. Removal of a
  citation is NEVER auto-applied and fires only for genuinely unsupported claims
  — see the removal gate in shared/evidence-status-ladder.md. Most weak-support
  cases call for downgrading the claim, not deleting the source.
---

# evidence-strength — Claim-vs-Source Support Audit

This skill audits the **relation between a claim and the source cited to back
it**. It does not re-check that the paper exists (`cite-audit` does that) or that
the claim is marked as hypothesis vs. fact (`falsifiability` does that). It asks
the one question those two leave open:

> Does the source actually establish what the claim asserts — same direction,
> same strength, same scope?

The dominant failure mode in citation-dense theory work is not fabrication; it
is **over-leverage**: adjacent theory, background results, and design motivation
quietly upgraded into the theory's own mechanism or effect. A correlation read as
a cause; an English-only result read as a universal; a paper cited as motivation
read as direct evidence. Topics align, so keyword screens pass it — only reading
what the source concludes catches it.

## Core Principles

1. **Read the source, don't match keywords.** A verdict above Info requires
   knowing what the source actually concludes — from its abstract, findings, or
   full text — not from title/abstract word overlap. If the source cannot be
   checked, the verdict is Info ("strength-uncheckable"), never Major.
2. **Charitable reading.** When a claim could be `accurate-with-caveat` or
   `overstated`, prefer the lower severity unless the source clearly fails to
   reach the claim's strength. Audit the writing, not the science.
3. **Weaken the claim before deleting the source.** Most weak-support cases want
   the *claim* downgraded or scoped — the citation legitimately supports the
   weaker version. Removal is the narrow exception (see §Step 6).
4. **Stay in lane.** Audit claims in the *source-result* and *analogy/motivation*
   registers (and bare quantitative claims). Definitional, normative, and marked
   -hypothesis claims belong to `falsifiability` / `consistency` — cross-reference.
5. **Read-only by default.** Findings only; fixes are opt-in and removal is
   per-item confirmed.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/evidence-status-ladder.md` — the seven verdicts, the two axes, the
  evidence registers, the evidence-layer ladder, the remediation ladder, and the
  removal gate
- `../shared/academic-severity-levels.md` — severity rubric
- `../shared/citation-parser.md` — inline-citation regex set + canonical key +
  WebFetch query construction (to locate cited claims and verify sources)

**ON-DEMAND (load only at the listed step):**
- `../shared/falsifiability-template.md` — load at Step 2 for Type A/B/C/D/E
  gating (skip B/C/E claims; they are not asserting external support)
- `../shared/argument-patterns.md` — load at Step 4 for the affirming-the-
  consequent (§2.1) and scope-creep (§2.5) cross-links
- `../shared/output-templates/evidence-strength-audit.md` — load at Step 7

## Workflow

### Step 1: Scope Selection

Default scope is **broader** than `falsifiability`: any doc that carries
citations or quantitative effect claims is in scope — including methodology
files, because that is where unsourced effect numbers (token savings, latency,
load reduction) often live.

- `docs/**/*.md`, `README*.md`, and any canonical paper file (e.g.
  `*_Working_Paper.md`) — in scope
- `_research/`, `_drafts/`, anything underscore-prefixed — out
- The bibliography file itself — out (parsed for lookup, not audited as claims)

If the project has a prior `_research/citation-audit.md`, read it first and
**reuse** its inline-citation index and paper-verification results — do not
re-fetch sources `cite-audit` already resolved.

### Step 2: Build the Cited-Claim Inventory

Glob in-scope files. A **cited claim** is a sentence that either:
- carries ≥1 inline citation and asserts something *on the document's own behalf*
  (not "Author (Year) shows…" — that is Type D literature restatement, in scope
  only when the restatement is then *used* to support the theory's claim), OR
- asserts a **quantitative or mechanistic result** (a number, a causal verb
  — "reduces / improves / preserves / bypasses", a mechanism explanation) that
  *implies* external evidential support even if no citation is attached.

Classify each candidate with the `falsifiability` Type A/B/C/D/E taxonomy and
**drop** Type B (normative), Type C (definitional), and Type E (marked
hypothesis) — those do not assert external support. Keep:
- **Type A** claims (the document's own empirical assertions)
- **Type D** restatements *that are recruited as support* for a theory claim
- **Unmarked quantitative / mechanistic** claims (no citation, no marker)

For each kept claim record: file, line, the claim sentence, the cited key(s) (if
any), and the surrounding ≤300 words within the same section.

Display a summary:

```
Cited-claim inventory:
  Type A claims with citations:      {n}
  Recruited Type D restatements:     {n}
  Unmarked quantitative/mechanistic: {n}
  Total in scope:                    {n}
```

### Step 3: Establish What Each Source Establishes

For each distinct cited source backing an in-scope claim:
1. Reuse `cite-audit`'s verification if available; otherwise fetch the abstract
   via CrossRef → Semantic Scholar → OpenAlex (per `citation-parser.md` §5).
2. Extract the source's **actual conclusion**: its main result, its **scope**
   (population, language sample, domain), its **method**, and its **evidence
   layer** (behavioral / descriptive-internal / causal / domain-specific — see
   the ladder).
3. If no source has retrievable content (abstract missing, no DOI, book chapter):
   mark every claim relying on it **strength-uncheckable → Info**. Do not guess a
   verdict. Do not escalate.

For **unmarked quantitative / mechanistic** claims with no citation at all, there
is no source to establish anything → these are candidate `unsupported` (confirm
in Step 4 that no nearby citation covers them).

### Step 4: Verdict Assignment

For each in-scope claim, compare claim vs source on the **two axes** (direction,
then strength/scope) and assign exactly one of the seven verdicts from the
ladder. Apply the dimension checks the claim invokes:

- universality words ("all / every / across languages") vs a bounded source
  sample → **overstated** (cross-link `cross-lang`, `scope`)
- causal verb vs a correlational / visualization source, or an evidence-layer
  jump (cites Layer-1/2 for a Layer-3/4 conclusion) → **overstated** (cross-link
  `argument-structure` §2.1)
- a named effect size vs a source with no such figure → **overstated** or
  **unsupported**
- a mechanism / cross-domain "shared mechanism" claim vs a single-domain
  behavioral source → **overstated** or **indirect**
- source is motivation/analogy but read as direct evidence → **indirect**
- source does not contain the attributed method/result → **misattributed**
- source documents diversity / a limit but is cited as support → **counterevidence**
- no source supports the claim → **unsupported**

Record, for each finding, **what the source establishes** vs **what the claim
asserts**, in one line each — this evidence is what makes the verdict defensible
and is mandatory for any Major/Critical finding.

### Step 5: Support-Strength Score and Remediation

Attach to each finding:
- a **support-strength** read (full / high-scoped / partial / motivational-only /
  none / negative), and
- the **remediation** from the ladder's remediation table (none / add-qualifier /
  downgrade-claim / reword-as-motivation / reframe-as-constraint / repair-or-move
  -attribution / **remove-citation**).

Severity per the ladder's severity-mapping table (central claims escalate one
level; `counterevidence` on a central claim is Critical).

### Step 6: Apply the Removal Gate

For any finding whose remediation is **remove-citation** (only `unsupported`) or
**remove the inline attribution at this site** (site-level `misattributed`),
run the removal gate from `evidence-status-ladder.md` before listing it as a
removal candidate:

1. Confirm the verdict is `unsupported` or site-`misattributed` — never the
   over-leverage verdicts.
2. Confirm **corroboration**: the source was actually checked (or confirmed not
   to exist). If the verdict rests only on a low automatic score → move to the
   human-review queue, not the removal list.
3. If the citation is the **sole support for a central claim** → emit the
   load-bearing warning ("removing this leaves the central claim unsupported —
   downgrade the claim or drop it"), not a bare "remove."
4. **Downweight** classic (5+ uses) and methodology citations to a review note.
5. If removal would orphan the **bibliography entry**, flag a *separate* unused-
   entry prune suggestion and route it to `cite-audit` — do not prune the
   bibliography here.

Removal candidates are reported, never auto-applied.

### Step 7: Report Generation

Write the report to `_research/evidence-strength-audit.md` (create `_research/`
if absent; warn it should be gitignored / excluded from the site build).

**Report structure**: read `../shared/output-templates/evidence-strength-audit.md`
and fill in the `{placeholder}` values — the verdict-distribution table, the
per-verdict finding sections, and the **Removal Candidates** section (kept
visually separate, with the load-bearing warnings inlined). Loaded only at this
step.

### Step 8: Offer Fixes (opt-in, severity- and verdict-gated)

Use AskUserQuestion:

> Found {N} findings on claim-vs-source support ({c} Critical, {m} Major).
> Would you like me to:
> 1. Draft claim-downgrade / scope-qualifier / "motivated-by" rewordings for the
>    overstated, indirect, and accurate-with-caveat findings (you confirm each)
> 2. Review the **Removal Candidates** list (unsupported citations) one by one
> 3. Just save the report and exit (default)

Fix policy:

| Remediation | Auto-draft offered? | Behavior |
|---|---|---|
| add-qualifier / downgrade-claim / reword-as-motivation / reframe-as-constraint | Yes, per-item | Edit the **claim/prose**, keep the citation. Show as a diff; apply on "yes". |
| repair-or-move-attribution | Propose-only | Show the mismatch; do not invent a replacement citation. The user decides. |
| **remove-citation** | **Never auto-applied** | Show as a diff with the corroboration evidence inline; apply only on explicit per-item "yes". If load-bearing, present the claim-downgrade alternative alongside. |

Never delete a citation, and never weaken a claim, the user did not approve. Never
prune a bibliography entry in the same step as an inline removal.

## Output Schema (per Finding)

```yaml
- id: ES001
  severity: Major
  verdict: overstated          # accurate-with-caveat | overstated | indirect |
                               # misattributed | counterevidence | unsupported
  claim_role: central          # central | supporting | auxiliary
  citation_key: "vig 2019"     # or null for unsupported-uncited claims
  location:
    file: docs/en/foundations/core-concept.md
    line: 426
    claim: "Attention visualization shows the model preserves the Core at position 0."
  source_establishes: >
    Vig (2019) provides an attention-visualization tool (descriptive-internal,
    Layer 2); it makes no causal claim.
  claim_asserts: >
    A causal mechanism (Layer 3) — that early position causes semantic
    preservation.
  axis: strength/causality (evidence-layer jump 2→3)
  support_strength: partial
  remediation: downgrade-claim
  remove_candidate: false
  fix_suggestion: >
    Label the source descriptive ("Vig 2019 visualizes, does not establish
    causation"); route the causal claim to intervention-based methods
    (ablation / activation patching) reported separately.
```

## Anti-patterns

Do **not**:
- Assign a verdict above Info without having read what the source concludes —
  mark it strength-uncheckable instead.
- Recommend removing a citation that is `overstated` / `indirect` /
  `accurate-with-caveat` — those keep the citation and change the claim.
- Auto-apply any removal, or prune a bibliography entry in the same step as an
  inline-citation removal.
- Re-audit Type B / C / E claims — they assert no external support; defer to
  `falsifiability`.
- Flag a methodology citation as misattributed because its abstract is about a
  different topic than the method it is cited for (downweight, as `cite-audit` §6
  does).
- Duplicate `cross-lang` / `scope` findings — when over-leverage is a universality
  jump, cross-reference rather than re-report.

## Exit Conditions

- **Clean exit (PASS)**: 0 Critical, 0 Major. Every cited claim sits within its
  source's strength and scope.
- **Review required**: ≥1 Critical or ≥1 Major. Surface overstated / misattributed
  / counterevidence findings and the Removal Candidates list for the author.
- **Inconclusive**: sources unreadable for >20% of cited claims (offline / missing
  abstracts). Report partial results and flag the limitation; do not infer
  verdicts for unchecked sources.
