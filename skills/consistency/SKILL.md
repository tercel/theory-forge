---
name: consistency
description: >
  Audit cross-section semantic coherence in academic theory documents. Detects the
  "formal definition omits component listed in narrative" class of bug — when §N
  enumerates components A/B/C/D but §M's formal definition only lists A/B/C, or
  when two sections make contradictory claims about the same construct. Pure local
  analysis, no WebFetch needed.
instructions: >
  Read-only by default. Reports findings with file:line locations and concrete
  fix suggestions. Be specific — vague "these sections might disagree" reports are
  not useful. Quote the exact contradicting phrases.
---

# consistency — Cross-Section Semantic Coherence

The single most common failure mode in long-form theory documents is **drift between a formal definition and the prose that surrounds it**. As authors revise the narrative to add components, examples, and edge cases, the formal definition section in the same document gets left behind. The discovery in the CFLT 2026-05 audit round — where §9 formal definition listed `predicate + valence + manner` but §2.2 and §2.4 had already moved to `predicate + valence + manner + modal + negation + aspect + degree` — is the canonical example.

This skill catches that class of bug.

## Core Principles

1. **Component-list semantics.** A "component list" is a structured enumeration of what makes up a defined construct. It usually appears as a bulleted list, a numbered list, an inline `(X, Y, Z)` enumeration, or a markdown table column.
2. **Definition sections vs narrative sections.** Definition sections (identified heuristically) are the **source of truth**. Narrative sections elsewhere in the doc should not assert components the definition section omits, and the definition should not omit components the narrative repeatedly asserts.
3. **No code grounding needed.** Unlike `spec-forge:audit`, this skill does not cross-reference against a codebase. It is text-only.
4. **Specific findings.** Every finding quotes the exact two strings that disagree and proposes which one should change.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/falsifiability-template.md` — load at Step 4.5 ONLY for Type E recognition (hypothesis-vs-refinement check)
- `../shared/argument-patterns.md` — load at Step 5 ONLY when scanning for equivocation cross-references
- `../shared/output-templates/consistency-report.md` — load at Step 6 (Report Generation)

## Workflow

### Step 1: Locate Definition Sections

Glob `docs/**/*.md` (excluding `_research/`, `_drafts/`, anything underscore-prefixed).

For each file, identify **definition sections** by heading heuristic. A section is a definition section if its heading (any level) matches any of:

- `Formal Definition`, `Formal Structure`, `Definition`, `Definitions`
- `Glossary`
- `Specification`, `Spec`
- `Schema`
- Inline sentence pattern: a sentence starting `X is defined as` or `X is formally defined as` or `Define X as`

Display:

```
Definition sections found:
  docs/en/foundations/core-concept.md §9 Formal Definition for Implementers
  docs/en/foundations/core-concept.md §2.4 Formal Structure of the Four Core Types
  docs/en/glossary.md (entire file)
  docs/en/manifesto.md §2 Core Definition
```

### Step 2: Extract Defined Constructs and Their Component Lists

For each definition section, parse:

1. **The construct being defined** — typically the first noun phrase in the section or the heading. Examples: `Core`, `Event Nucleus`, `Ground Frame`, `Slot`, `Identity Core`.
2. **The component list** — bulleted/numbered/inline enumeration of what makes up the construct.

Use multiple heuristics to extract components; they should agree, but report disagreement at Minor severity:

- **Bulleted lists** under the definition statement
- **Inline enumerations** of form `X, Y, Z, and W` or `(X, Y, Z)`
- **Markdown tables** where one column lists components
- **Mermaid diagrams** with nodes listed as components

For each construct, build a canonical component set:

```yaml
construct: Core
defined_in:
  - file: docs/en/foundations/core-concept.md
    section: §9 Formal Definition for Implementers
    components:
      - predicate
      - valence-bound participants
      - nuclear / core-level manner adverbials
      - scope-internal operators (negation, modality, aspect, degree)
  - file: docs/en/foundations/core-concept.md
    section: §2.4 Formal Structure of the Four Core Types
    components:
      - predicate (Action/Identity/State/Request)
      - valence-bound participants
      - manner adverbs
      - modal & negation operators
      - aspect
      - degree (State Core only)
```

### Step 3: Cross-Section Narrative Scan

For each defined construct, scan the entire document corpus for sentences that **describe the construct's components** without being inside a definition section. These are narrative claims.

Heuristics for narrative-component claims:

- Sentences containing the construct name followed by `includes`, `contains`, `consists of`, `comprises`, `is made up of`, `is composed of`
- Sentences with the construct name as subject and a list as object
- Diagnostic tables that enumerate the construct's components by example

Extract the component sets these narratives assert.

### Step 4: Compute Component-Set Diffs (Granularity-Aware)

> **Critical guard against false-positives.** Two definition sections in the same project legitimately have **different scopes**:
>
> - A **glossary entry** is a one-line gloss intended for quick lookup.
> - A **manifesto / introduction definition** is a teaser that omits secondary components for accessibility.
> - A **full formal definition** (typically labeled "Formal Definition", "Formal Structure", or appearing in a foundations doc) is the comprehensive list.
>
> Expecting a glossary entry to enumerate every component of the formal definition is wrong. The skill must classify definition sections by **granularity tier** before comparing:
>
> | Granularity tier | Typical heading patterns | Expected exhaustiveness |
> |---|---|---|
> | **T1 — Gloss** | Glossary entries; `## See Also`; single-line definitions; tooltips | Components may be summarized or abbreviated |
> | **T2 — Introduction** | Manifesto definitions; "What is X?" sections; doc abstracts | Major components named; minor ones may be omitted |
> | **T3 — Formal definition** | "Formal Definition", "Formal Structure", "Specification" headings; sections in foundations docs that exhaustively enumerate; "for implementers" framing | **Must** enumerate all components |
>
> **Only T3-vs-T3 component-set comparisons can produce a `Major` finding for component drift.** A T1 or T2 section omitting a component that T3 has is **OK** — not a finding. A T3 section omitting a component that T2 has is a real finding because T3 should be more exhaustive.

For each construct:

1. Find the **union** of all components asserted anywhere
2. Identify the **canonical T3 definition** (if multiple T3 sections exist, the most recently revised by `git log` is canonical)
3. For each location, compute:
   - **Missing**: components in the canonical T3 but not in this location
   - **Extra**: components in this location but not in any T3

Severity rules (granularity-aware):

| Finding type | Severity |
|---|---|
| **Two T3 definition sections disagree on components** | **Major** |
| **T3 missing a component that a T2 or narrative repeatedly (≥2 places) asserts** | **Major** |
| T3 missing a component that narrative asserts once | **Minor** |
| **T2 missing a component that T3 has** | OK (not a finding) — T2 is allowed to be partial |
| **T1 missing a component that T3 has** | OK — gloss is allowed to summarize |
| Narrative asserting a component no T3 has | **Minor** — flag for review (narrative may be ahead of formal def) |
| Definition section uses different name for same component (suspected alias drift) | **Minor** — but only flag when confidence is high; see anti-pattern below |

#### Anti-pattern: phantom alias drift

The skill should **only** flag alias drift when at least one of:

1. The two terms appear in the same author's prior works as confirmed synonyms (e.g., "valence-bound participants" and "argument-structure-licensed participants" — clearly the same in any RRG/event-structure tradition)
2. A glossary entry explicitly maps one to the other
3. The terms differ only in word order, plural/singular, or minor punctuation

Do **not** flag as alias drift when:

- The two terms might denote different things to a careful reader (e.g., "manner" vs "manner-of-action" — these can be distinct)
- One term is generic and the other is specific (e.g., "arguments" vs "core arguments")
- The skill is uncertain whether they are aliases

When uncertain, downgrade to Info: "Possible alias drift — confirm whether {term1} and {term2} denote the same component."

### Step 4.5: Hypothesis-vs-Refinement Recognition

> **Anti-false-positive rule.** Two statements about the same construct in different sections may look contradictory but actually represent **scientific maturation**, not drift:
>
> - §3 (early draft): *"We hypothesize the four-slot protocol is universal across natural languages."*
> - §7 (later in the doc): *"The four-slot protocol holds across the five families shown in §2.5; coverage of the remaining ~10 families is an open research question."*
>
> These are **not contradictions** — they are: (a) initial hypothesis, then (b) refined empirical scope. This is **good scientific practice**, not drift.

Before flagging any "Two definition sections disagree" finding (Step 4 severity table):

1. Check whether one of the two sections is **Type E** (marked hypothesis — per `shared/falsifiability-template.md`). Markers: "we hypothesize", "we conjecture", "we predict", "[unverified]", "open question", or section heading containing "Hypotheses", "Predictions", "Open Questions".
2. Check whether the other section is **Type A** (verified empirical, with citation or measurement) OR a refined narrower claim ("holds across X; open for Y").
3. **If a Type E + refined-Type-A pair is detected on the same construct**, this is a hypothesis-refinement pair — log at **Info** (positive practice: bold hypothesis followed by scoped empirical validation), NOT as a contradiction.
4. **If both sections are Type E but disagree** (different hypotheses about the same construct), log at **Minor** — the document has two competing hypotheses; surface for author attention.
5. **If both sections are Type A but disagree** (two empirical claims contradict each other), log at **Major** — real drift.

### Step 5: Contradiction Scan

In addition to component-list mismatches, scan for **direct contradictions**: two sections asserting incompatible facts. Apply the Step 4.5 hypothesis-vs-refinement check first; do not flag a Type E ↔ Type A pair as contradiction.

Heuristics:

- `X is Y` in one section vs `X is not Y` (or `X is Z` where `Z` excludes `Y`) in another
- Numerical mismatches (`four slots` vs `five slots`)
- Categorical mismatches (`obligatory` vs `optional`)
- Citation attributions (Section A attributes a claim to author X; Section B attributes the same claim to author Y)

For each contradiction, classify:

| Type | Severity |
|---|---|
| Numerical or categorical contradiction in central definition | Major |
| Citation attribution conflict (different authors for same claim) | Major |
| Different terminology for the same concept (terminology drift) | Minor |
| One section more nuanced than another (not strictly contradictory) | Info |

### Step 6: Report Generation

Write the report to `_research/consistency-report.md`. Create the directory if needed.

**Report structure**: read the template at `../shared/output-templates/consistency-report.md` and fill in the `{placeholder}` values. Preserve heading hierarchy and severity ordering. The template is loaded only at this step.

### Step 7: Offer Fixes (opt-in)

Use AskUserQuestion:

> Found {N} Major+ consistency issues. Would you like me to:
> 1. Propose specific text edits for each issue (you confirm each)
> 2. Just save the report and exit (default)

If the user picks 1:
- For each finding, show a proposed diff
- Apply only after explicit per-finding confirmation
- For contradictions, ask the user which side is correct before proposing the fix

## Anti-patterns

Do **not**:
- Report on minor wording differences that preserve meaning (e.g., "events" vs "actions" used interchangeably).
- Flag a narrative section for being less exhaustive than the definition section — narrative is allowed to be terser.
- Bundle stylistic preferences (sentence length, paragraph structure) into consistency findings.
- Report the same construct mismatch multiple times if it appears across many narrative sections — aggregate with all locations cited.
- Run on `_research/`, `_drafts/`, or underscore-prefixed directories.

## Exit Conditions

- **Clean exit (PASS)**: 0 Critical, 0 Major.
- **Review required**: ≥1 Major.
- Critical severity is reserved for direct factual contradictions in central definitions; rare.
