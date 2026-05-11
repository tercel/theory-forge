# theory-forge — Usage Guide

End-to-end usage examples for the theory-forge plugin, covering the dashboard, each sub-command, common workflows, and the full audit suite.

## Quick Start

After installing the plugin:

```
# 1. From the root of an academic theory project, get the dashboard (with usage examples)
/theory-forge

# 1b. Get the same usage view without the project-status header
/theory-forge help

# 1c. Get detailed help for a specific command
/theory-forge help cite-audit

# 2. Run the full audit suite
/theory-forge .

# 3. Or pick a single sub-command
/theory-forge:cite-audit
/theory-forge:argument-structure
/theory-forge:falsifiability
/theory-forge:scope
/theory-forge:concept-import
/theory-forge:counter-argument
/theory-forge:cross-lang
/theory-forge:consistency

# 4. After editing an upstream doc, propagate downstream
/theory-forge:propagate docs/foundations/core-concept.md
```

## Sub-Command Reference

### `/theory-forge` (orchestrator + dashboard)

**Purpose:** Top-level entry. With no arguments, shows the project's audit dashboard. With a path argument, runs all 8 audits in sequence and aggregates them.

**Examples:**

```
# Dashboard
/theory-forge

# Full suite on current project
/theory-forge .

# Full suite on a different project
/theory-forge <path-to-other-theory>
```

**Output:** Master report at `<project>/_research/theory-forge-master-report.md` with cross-audit findings highlighted.

**When to use:** Periodic comprehensive check, e.g., before a major release or after a substantial revision round.

---

### `/theory-forge:cite-audit` (citation truth + bibliography integrity)

**Purpose:** Verify every citation exists, is correctly attributed, and is in the bibliography. Uses CrossRef → Semantic Scholar → OpenAlex for paper-existence verification.

**Examples:**

```
# Audit current project
/theory-forge:cite-audit

# Audit a specific project
/theory-forge:cite-audit ../some-other-project
```

**Output:** `_research/citation-audit.md` with severity-classified findings:
- **Critical**: Fabricated citations (paper does not exist)
- **Major**: Mis-attribution (paper exists but doesn't say what the inline claim asserts); bibliography orphans
- **Minor**: Unused entries, malformed entries, ambiguous-shorthand citations
- **Info**: Unverifiable (pre-1980 chapters, dissertations without DOI)

**When to use:** Before submitting a paper, after a literature-review revision pass, or when reviewing a borrowed manuscript.

**Safety guards:**
- Never auto-fixes Critical findings — requires user acknowledgment
- For Major mis-attribution: propose-only, does not auto-replace
- WebFetch is rate-limited; report flags partial results if network issues

---

### `/theory-forge:consistency` (cross-section semantic coherence)

**Purpose:** Catch the "formal definition omits component listed in narrative" class of bug — granularity-aware (T1 gloss vs T2 intro vs T3 full formal definition). Also hypothesis-vs-refinement recognition (a hypothesis in §3 + a refined empirical claim in §7 is *not* a contradiction).

**Examples:**

```
# Audit current project
/theory-forge:consistency

# Audit a specific project
/theory-forge:consistency ../other-project
```

**Output:** `_research/consistency-report.md` with component-list mismatches and direct contradictions.

**When to use:** After editing a foundational definition, after a multi-author edit pass, or before a release.

---

### `/theory-forge:falsifiability` (Popperian framing + Type A/B/C/D/E classification)

**Purpose:** Classify every claim into 5 types and verify each is appropriately framed:

- **Type A** (verified empirical) — citation + measurement required
- **Type B** (normative / design) — author-stipulation marker required
- **Type C** (definitional / terminological) — consistency-checked
- **Type D** (literature restatement) — citation IS the marker, exempt from falsifiability
- **Type E** (marked hypothesis) — explicit marker ("we hypothesize", "[unverified]") required; bold conjectures welcome when marked

**Examples:**

```
# Audit current project
/theory-forge:falsifiability

# Audit with auto-draft mode (proposes four-block expansions)
/theory-forge:falsifiability --draft
```

**Output:** `_research/falsifiability-audit.md` with each claim categorized and gaps flagged.

**When to use:** After drafting a section with empirical predictions; periodically to catch unmarked speculation.

**Guiding principle:** 大胆假设，小心求证 — bold hypothesis is welcome **when explicitly marked**. The audit catches *unmarked* speculation passed as established fact, not bold marked hypotheses.

---

### `/theory-forge:cross-lang` (cross-linguistic universality coverage)

**Purpose:** For any claim of cross-linguistic universality, verify there are worked examples across **≥3 typologically distinct language families** (default rule). Optionally generate scaffolds or concrete drafts for missing families.

**Examples:**

```
# Default: audit only (read-only)
/theory-forge:cross-lang

# Generate safe scaffolds for missing families (does not produce concrete sentences)
/theory-forge:cross-lang --generate

# Generate concrete example sentences (ONLY with explicit user acknowledgment)
/theory-forge:cross-lang --generate-surface --i-have-native-speaker-review
```

**Output:** `_research/cross-lang-audit.md` with coverage analysis per universality claim.

**Safety guards:**
- Default `--generate` produces *scaffolds* (fillable templates with reference-grammar citations), not concrete sentences
- Concrete-surface generation requires BOTH flags and produces examples marked with HTML comments for future audits
- Never generates examples in languages without a registered reference grammar in `skills/shared/language-data.md`

---

### `/theory-forge:argument-structure` (Toulmin completeness + fallacy detection)

**Purpose:** Audit each Major claim for Toulmin elements (Claim + Data + Warrant + Qualifier + optional Rebuttal). Also detect common fallacies: affirming the consequent, ad hoc rescue, no-true-Scotsman, scope creep, equivocation, argument from authority, etc.

**Examples:**

```
# Audit current project
/theory-forge:argument-structure

# Audit with --draft to propose missing warrants
/theory-forge:argument-structure --draft
```

**Output:** `_research/argument-structure-report.md` with Toulmin coverage per claim and fallacy detections.

**When to use:** When a foundation doc reads like a manifesto rather than an argument; when readers complain that claims feel asserted-not-defended.

---

### `/theory-forge:scope` (boundary-condition discipline)

**Purpose:** Flag unbounded generalizations ("for all X", "across languages", "universally") lacking explicit qualifiers or limitation-section coverage. Favors theories that state where they apply and where they don't.

**Examples:**

```
/theory-forge:scope

# With --draft to propose inline qualifiers
/theory-forge:scope --draft
```

**Output:** `_research/scope-audit.md` listing truly unbounded claims, claims bounded by remote sections only (recommend inline qualifier), and document-level scope structure.

**When to use:** When the theory's claims feel "too big to be true"; before peer review.

---

### `/theory-forge:concept-import` (cross-disciplinary borrowing tier marking)

**Purpose:** When a document borrows technical terms from another field (Attention from Transformer ML, Salience Network from neuroscience, Information from Shannon's IT), enforce three-tier marking:

- **T1 — Technical equivalence**: full preservation of source-field definition
- **T2 — Partial equivalence with explicit limits**: specific features kept, others not
- **T3 — Metaphorical analogy**: inspirational only, no technical content

**Examples:**

```
/theory-forge:concept-import

# With a project-specific extra lexicon
/theory-forge:concept-import --lexicon my-imports.txt

# With --draft to propose tier markers
/theory-forge:concept-import --draft
```

**Output:** `_research/concept-import-audit.md` with all detected imports, their tier, and cross-doc consistency findings.

**When to use:** Especially for interdisciplinary work (linguistics + neuroscience + LLMs + pedagogy — i.e., CFLT-style projects); periodically.

---

### `/theory-forge:counter-argument` (engagement with opposing positions)

**Purpose:** For each central claim, check whether the document engages with the **canonical opposing position** in the relevant subfield. Surface strawman engagement separately (worse than no engagement).

**Examples:**

```
/theory-forge:counter-argument

# With project-specific opposition map (topic → canonical opponents)
/theory-forge:counter-argument --opposition-map my-oppositions.yaml

# With --draft to propose engagement paragraphs
/theory-forge:counter-argument --draft
```

**Output:** `_research/counter-argument-audit.md` with engagement profile per central claim.

**When to use:** When the doc feels insular; before submitting to a journal that values engagement with the field.

**Note:** A PASS does not mean the engagement has *succeeded* — only that it is present. Peer review judges success.

---

### `/theory-forge:propagate` (downstream change propagation)

**Purpose:** After editing an upstream foundation doc, find every dependent doc that references the changed concepts and propose coordinated updates. Adapted from `spec-forge:propagate` for academic doc cross-reference patterns.

**Examples:**

```
# Propagate from a specific source
/theory-forge:propagate docs/foundations/core-concept.md

# Propagate all doc changes since a git ref
/theory-forge:propagate --since HEAD~3

# Dry-run (report only, do not apply)
/theory-forge:propagate docs/foundations/core-concept.md --dry-run

# Save the propagation report
/theory-forge:propagate docs/foundations/core-concept.md --save
```

**Output:** `_research/propagation-report-{timestamp}.md` plus per-confirmed-file Edits to downstream docs.

**Safety guards:**
- Never modifies the source document
- Never uses Write (only surgical Edit)
- Never auto-applies LOW or AMBIGUOUS confidence changes
- Pre-flight check: warns if a downstream file has uncommitted changes

---

## Workflow Recipes

### Recipe 1 — Pre-submission audit

Before submitting a paper to a journal:

```
# 1. Full suite to surface all issues
/theory-forge .

# 2. Address Critical and Major findings
#    (review _research/theory-forge-master-report.md and act on each section)

# 3. Re-run the full suite to verify clean status
/theory-forge .

# 4. Check that the bibliography is hygienic
/theory-forge:cite-audit
```

Expected outcome: all 8 audits status PASS or near-PASS (≤2 Minor findings each).

---

### Recipe 2 — Editing a foundational definition

When you edit `core-concept.md` §9 to change what "Core" means:

```
# 1. Make the edit in core-concept.md
#    (edit manually or via /spec-forge / Edit tool)

# 2. Check that the formal definition still aligns with narrative claims
/theory-forge:consistency

# 3. Check that arguments still hold with the new definition
/theory-forge:argument-structure

# 4. Propagate the definition change to all dependent docs
/theory-forge:propagate docs/en/foundations/core-concept.md

# 5. Re-run consistency to verify the propagation closed the gaps
/theory-forge:consistency
```

---

### Recipe 3 — Interdisciplinary import review

When the doc borrows from neuroscience, NLP, and cognitive psychology:

```
# 1. Audit which borrowed terms appear and how they're marked
/theory-forge:concept-import

# 2. For unmarked imports, decide which tier (T1/T2/T3) fits and add markers
#    (use --draft to get proposed markers)
/theory-forge:concept-import --draft

# 3. Verify consistency — same term should have the same tier across the doc
/theory-forge:concept-import

# 4. Cross-check that the cited source-field papers actually exist and say what's claimed
/theory-forge:cite-audit
```

---

### Recipe 4 — Cross-linguistic claim verification

When the doc makes universality claims:

```
# 1. Audit existing coverage
/theory-forge:cross-lang

# 2. For Major findings (universality with only 1 family), decide:
#    (a) reframe as Type E hypothesis with marker
#    (b) add examples from missing families

# 3. For option (b), generate scaffolds for the missing families
/theory-forge:cross-lang --generate

# 4. Fill the scaffolds by consulting the cited reference grammars
#    (or, if you have a native-speaker reviewer, use --generate-surface --i-have-native-speaker-review)
```

---

### Recipe 5 — Bold-hypothesis paper triage

When a paper is intentionally speculative (proposes a new framework based on theoretical reasoning, not yet empirically validated):

```
# 1. Make sure every speculative claim is properly marked as Type E
/theory-forge:falsifiability

# 2. Each Type E should have rationale (why is the hypothesis reasonable?)
#    --draft mode proposes four-block expansions
/theory-forge:falsifiability --draft

# 3. Check that the scope is honestly stated
/theory-forge:scope

# 4. Engage with the strongest opposing positions
/theory-forge:counter-argument

# 5. Verify argument structure is sound (even hypotheses need warrants)
/theory-forge:argument-structure
```

---

## Output Directory Convention

All audit reports are written to `<project>/_research/`. The underscore prefix:

- Marks the directory as out-of-publication scope
- Allows easy `.gitignore` rule: `**/docs/_research/**`
- For MkDocs sites, add to `exclude_docs` in `mkdocs.yml`

If `_research/` does not exist, the skill creates it and prompts you to verify your `.gitignore` / static-site exclusion is in place.

---

## Severity Reference

All audits use the same 4-level severity scale (`skills/shared/academic-severity-levels.md`):

| Severity | Meaning |
|---|---|
| **Critical** | Documentation is wrong in a way that misleads readers or undermines a central claim. Halt before publication. |
| **Major** | Significant gap that would not survive peer review. Address before submission. |
| **Minor** | Polish issue. Address in a cleanup pass. |
| **Info** | Observation or positive practice. No action required; sometimes worth highlighting. |

**Status reporting:**
- `PASS` — 0 Critical, 0 Major
- `REVIEW REQUIRED` — ≥1 Critical or ≥1 Major

---

## See Also

- `../README.md` — high-level introduction
- `../RELEASE-NOTES.md` — version history
- `../tests/eval-suite.md` — end-to-end test fixtures including the CFLT calibration set
