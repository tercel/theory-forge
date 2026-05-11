---
name: argument-structure
description: >
  Audit each Major claim in an academic theory document for Toulmin argument
  completeness — Claim + Data + Warrant + Qualifier + (Rebuttal). Surfaces bare
  assertions (claim with no data or warrant), argument-from-authority patterns,
  and common fallacies (affirming the consequent, ad hoc rescue, no-true-Scotsman,
  scope creep, equivocation). Read-only by default.
instructions: >
  Apply Toulmin (1958) charitably. A "complete" argument has at minimum Claim +
  Data + Warrant. Backing and Rebuttal raise the grade. Do NOT flag short
  Type-B-normative or Type-C-definitional claims as "bare assertions" — they
  don't need Toulmin elements. Always cross-reference falsifiability/SKILL.md's
  claim taxonomy before flagging.
---

# argument-structure — Toulmin Completeness Audit

This skill audits the **inferential anatomy** of theory documents. It does not check whether the conclusion is right; it checks whether the *argument is well-formed*. A well-formed argument has explicit Claim, Data, Warrant, optional Qualifier, optional Rebuttal — per Toulmin (1958).

## Core Principles

1. **Charitable reading.** A claim is bare only if no nearby (≤200 words within the same `##`/`###` section) sentence provides Data or Warrant. Toulmin elements may be distributed across sentences and paragraphs.
2. **Type-aware gating.** Type B (normative) and Type C (definitional) claims do not need Toulmin elements. Only Type A (verified empirical), Type E (marked hypothesis), and exceptionally strong Type B (foundational design choices) are checked.
3. **Severity discipline.** Bare Type A assertions → Major. Type A with citation but no warrant → Minor. Fallacies → variable by fallacy class.
4. **Read-only by default.** Findings only; no auto-edit.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/argument-patterns.md` — full Toulmin model, fallacy catalog, detection heuristics, output schema
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/falsifiability-template.md` — load at Step 2 ONLY for Type A/B/C/D/E gating (skip Type B/C/D claims from Toulmin checks)
- `../shared/output-templates/argument-structure-report.md` — load at Step 6 (Report Generation)

## Workflow

### Step 1: Scope Selection

Default scope: foundation / theory documents (same as `falsifiability`):
- `docs/**/foundations/*.md`, `docs/**/manifesto.md`, `docs/**/core-concept.md`
- Out: methodology / how-to / glossary / `_research/` / `_drafts/`

If scope is unclear, ask the user.

### Step 2: Locate and Classify Claims

Reuse the claim extraction and Type A/B/C/D/E classification from `falsifiability/SKILL.md` §2–§3. For this audit, only Type A and Type E claims are in scope (Type B / C / D are skipped; literature restatements are not the document's own arguments).

For each in-scope claim, also note the **role** the claim plays:
- **Central claim** — appears in the abstract / introduction / first paragraph of a foundation section; the doc's thesis
- **Supporting claim** — used to argue for a central claim
- **Auxiliary claim** — definitional support or scope-restriction
- **Predictive claim** — Type E hypothesis

Central claims get the strictest Toulmin checks; auxiliary claims get more permissive treatment.

### Step 3: Toulmin Element Extraction

For each in-scope claim, scan the surrounding ≤200 words (within the same section) for the five Toulmin elements per the heuristics in `argument-patterns.md` §1.2.

Build a coverage record:

```yaml
- claim_id: AS001
  claim_text: "..."
  claim_role: central | supporting | auxiliary | predictive
  claim_type: A | E
  toulmin:
    claim: present
    data: present | absent
    warrant: present | absent
    qualifier: present | absent
    backing: present | absent
    rebuttal: present | absent
  location:
    file: ...
    line: ...
    section: ...
```

### Step 4: Coverage Grading

Severity table:

| Element coverage | Type A claim severity | Type E claim severity |
|---|---|---|
| Claim only | **Major** (bare assertion) | **Major** (unmarked speculation — also flagged by `falsifiability`) |
| Claim + Data (citation only, no warrant) | **Minor** (citation-supported but inferentially unmotivated) | Minor |
| Claim + Data + Warrant | OK (minimum complete) | OK |
| + Qualifier | Info (positive — scope honest) | Info (positive) |
| + Rebuttal | Info (positive — engages limits) | Info (positive) |
| + Backing | Info (positive — full Toulmin) | Info (positive) |

**Central claims** add a one-level severity bump:
- Central Type A claim with no warrant → bumped from Minor to Major
- Central Type A claim with no qualifier → bumped from Info to Minor

### Step 5: Fallacy Scan

In addition to Toulmin completeness, scan for the fallacies catalogued in `argument-patterns.md` §2:

1. **Affirming the Consequent** — pattern "Our theory predicts P. P observed. ∴ theory supported." without ruling out alternatives
2. **Ad Hoc Rescue** — "But X is an exception..." without principled reason
3. **No-True-Scotsman** — definition shift after counterexample
4. **Argument from Authority** — citation as substitute for argument
5. **Scope Creep** — same construct, scope widens silently across sections
6. **Equivocation** — same term used with different meanings
7. **Texas Sharpshooter** — confirming examples only, no exception discussion
8. **Base-Rate Neglect** — empirical claim without baseline
9. **Argument from Lack of Counterexample** — "no counterexample found" as proof
10. **Ad Hoc Concept Smuggling** — handled by `concept-import` skill; cross-link only

For each detection, report with the specific pattern that matched, the location, and a recommendation.

### Step 6: Argument-Structure Report

Write the report to `_research/argument-structure-report.md`.

**Report structure**: read the template at `../shared/output-templates/argument-structure-report.md` and fill in the `{placeholder}` values, including the Toulmin Completeness table and the Fallacy detections table. Loaded only at this step.

### Step 7: Offer Fix Drafts (opt-in)

Use AskUserQuestion:

> Found {N} Major findings on argument structure. Would you like me to:
> 1. Draft warrant-or-rebuttal additions for each Major finding (you confirm each — `--draft` mode)
> 2. Just save the report and exit (default)

If user picks 1: for each Major finding, generate a draft (≤2 sentences) supplying the missing Toulmin element, referencing nearby citations where possible. Mark drafts as `[theory-forge: argument-structure draft]` and apply only on per-finding confirmation.

## Anti-patterns

Do **not**:
- Flag Type B / C / D claims as bare assertions — they don't need Toulmin elements
- Treat every short claim sentence as bare — check ≤200 surrounding words first
- Require full six-element Toulmin everywhere — three (Claim + Data + Warrant) is the minimum bar
- Flag fallacies on single sentences — fallacies span inferential structure across paragraphs
- Suggest specific warrants when the user has not opted into `--draft` mode

## Exit Conditions

- **PASS**: 0 Major. All Type A central claims have Data + Warrant; no fallacies detected.
- **Review required**: ≥1 Major. Surface bare assertions on central claims and fallacy detections for user attention.
