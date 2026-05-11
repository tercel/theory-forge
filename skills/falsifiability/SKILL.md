---
name: falsifiability
description: >
  Audit claims in academic theory documents for Popperian falsifiability. Classifies
  every claim as descriptive empirical, normative/design, or definitional/terminological;
  for descriptive claims, checks that the surrounding text includes a methodology, an
  explicit falsification condition, and adjacent empirical evidence. Flags unmarked
  normative claims that read as empirical.
instructions: >
  Read-only by default. Be a charitable reader — surface what is plausibly empirical,
  but do not invent falsifiability where the author clearly meant a design choice. The
  goal is to help authors mark normative claims and supply falsification conditions for
  empirical ones, not to gatekeep what counts as science.
---

# falsifiability — Verified vs Hypothesized vs Normative vs Definitional

This skill helps academic theory authors distinguish **five claim types** and ensure each is appropriately framed. The pattern is calibrated against the CFLT `core-concept.md §8.5 P1–P3` exemplar.

## Guiding principle: 大胆假设，小心求证 (Bold Hypothesis, Rigorous Verification)

theory-forge adopts the Popperian framing (Popper 1959, 1963) of science as **bold conjecture + rigorous verification + honest labeling** — see the T2 concept-import declaration in `shared/falsifiability-template.md`. Other philosophies of science (Kuhn 1962; Lakatos 1970; Feyerabend 1975) emphasize different aspects of scientific practice; theory-forge uses the Popperian framing because of its operational testability, not because it is uniquely correct.

Within the Popperian framing: the skill does NOT suppress speculative claims — bold hypotheses are scientifically valuable. The skill enforces **honest labeling**: a hypothesis must be marked as a hypothesis; a verified claim must be defensible against scrutiny. Unmarked speculation passed off as established fact is the dishonesty the audit must catch.

## The five claim types

| Type | Source of truth | Empirical status | Needs marker? | Example |
|---|---|---|---|---|
| **A. Verified empirical** | The world (data) | Verified — has citation + replicable evidence | No (the citation IS the marker) | "Liu et al. 2023 demonstrated U-shaped positional accuracy in LLMs" |
| **B. Normative / design** | Author's choice | n/a — a design decision | Yes ("we propose...", "we define...") | "CFLT places Core at position 0" |
| **C. Definitional / terminological** | Stipulation | n/a — terminology | No (the equational form IS the marker) | "Core = salience anchor" |
| **D. Literature restatement** | An external publication | Verified by the external source | n/a — the citation IS the source | "Halliday (1967) defined the intonational nucleus as..." |
| **E. Marked hypothesis** | Theoretical reasoning / analogy / extrapolation | **Unverified — by the author's own admission** | **Yes ("we hypothesize...", "[unverified]")** | "We hypothesize CFLT scaffolding produces 15–30% fluency advantage; this is provisional pending P1 study." |

## Core Principles

1. **Charitable reading.** When a claim could be read as either Type A or Type B, prefer Type B (normative) unless the surrounding context (verbs like "shows", "demonstrates", "find", "evidence", numbers, citations to empirical studies) clearly signals empirical intent.
2. **Mark, don't quarantine.** The skill flags unmarked Type B claims so they can be reframed (e.g., "We propose..." or "By convention..."), not deleted.
3. **Falsifiability ≠ refuted-ness.** A claim being falsifiable does not mean it is wrong — it means it is the kind of claim that *could* be wrong, which is the Popperian criterion for science.
4. **Honest evidence accounting.** Adjacent evidence should describe its strength honestly ("methodologically analogous, not direct evidence") rather than overclaim.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/falsifiability-template.md` — the four-block schema (claim / methodology / falsification / adjacent evidence) plus claim taxonomy A/B/C/D/E
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/output-templates/falsifiability-audit.md` — load at Step 7 (Report Generation)

## Workflow

### Step 1: Scope Selection

Parse the target path. Default scope: foundation / theory documents only (not methodology / how-to guides). Heuristic:

- `docs/**/foundations/*.md` — in scope
- `docs/**/manifesto.md` — in scope
- `docs/**/core-concept.md` or similar — in scope
- `docs/**/methodology/*.md` — out of scope by default (these are usually how-to)
- `docs/**/glossary.md` — out of scope (Type C — handled by `consistency`)
- `_research/`, `_drafts/` — out of scope

Use AskUserQuestion if the scope is unclear:
- "Audit all theory docs?" / "Specific files?"

### Step 2: Claim Extraction

For each in-scope file:

1. Identify candidate "claim-shaped" sentences. Patterns:
   - `X is Y` (declarative copular)
   - `X has Y`, `X requires Y`, `X causes Y`
   - `When X, Y`, `If X, then Y`
   - `All X are Y`, `Every X has Y`
   - Numerical / comparative: `X is faster than Y`, `X reduces Y by N%`
   - "Shows that", "demonstrates that", "find that", "evidence that"

2. Skip:
   - Direct quotations from cited authors
   - Paraphrased external findings — sentences whose grammatical subject is `Author (Year)` or `Author et al.` (e.g., *"Liu et al. (2023) show a U-shaped curve..."*). These are **literature review** statements, not the document's own claims; classify as a fourth type, **Type D — literature restatement**, and do not demand falsifiability of them
   - Examples (inline `*italicized examples*`, code blocks)
   - Headings and titles
   - Footnotes and asides
   - Sentences inside a "## See Also" / "## References" / "## Bibliography" section

3. For each claim, record the file path, line number, and surrounding **≤500 words within the same `##` or `###` section**. Do not cross section boundaries — methodology in a different section is not "surrounding" context.

### Step 3: Claim Classification

For each candidate claim, classify as A / B / C using these heuristics:

**Type A (descriptive empirical)** signals:
- Verbs of empirical observation: *show, demonstrate, find, observe, measure, predict*
- Numerical results, percentages, statistical claims
- Citations to empirical studies (psycholinguistic, neurocognitive, computational measurements)
- Quantifier-bound claims about populations: "L2 learners", "speakers", "participants"
- Future-conditional empirical: "would be observed", "should manifest as"

**Type B (normative)** signals:
- Verbs of stipulation: *propose, define, stipulate, prescribe, posit, requires*
- Subject = the theory itself: "CFLT places...", "the protocol enforces..."
- Author-pronoun introductions: "We assume...", "We propose..."
- Should-statements that prescribe behavior

**Type C (definitional)** signals:
- Equational copular: `X = Y`, `X means Y`
- "Refers to", "is defined as", "denotes"
- Inside a Glossary section or definition block

**Type D (literature restatement)** signals:
- Grammatical subject is `Author (Year)` or a research finding citation
- Verbs like *show, demonstrate, find, observe* applied to external authors, not to the theory
- Example: "Liu et al. (2023) **show** that LLMs have a U-shaped accuracy curve" — this is restatement of Liu's empirical claim, not a claim of CFLT itself
- Type D is **never** subject to falsifiability checks — the original publication's review handled that

**Type E (marked hypothesis)** signals (see `shared/falsifiability-template.md` for the full list):
- Hedging verbs: *hypothesize, conjecture, predict, expect, tentatively propose*
- Status framing: *"It remains an open question whether..."*, *"Provisionally..."*
- Status tags: `[unverified]`, `[hypothesis]`, `[open]`, `[provisional]`
- Inside a section labeled "Hypotheses", "Open Questions", "Predictions", "Conjectures"
- Example: *"We hypothesize CFLT scaffolding will produce a 15–30% fluency advantage at intermediate-L2 stage. This prediction has not been tested."*

When ambiguous, prefer this priority: **D > C > E > B > A** (most charitable to least). I.e.:
- If it could be either D or anything else, prefer D (literature restatement — not the document's own claim)
- If it could be either C (definitional) or something else, prefer C
- **If it could be either E (hypothesis with marker) or A (verified) — prefer E only if a marker is actually present.** Type E is not a fallback for unmarked uncertain claims; it requires the marker.
- If it could be either B (normative) or A (empirical), prefer B (design choice over empirical assertion)
- Only fall through to A when the sentence clearly asserts the document's own empirical claim AND has citation / measurement support

**Critical distinction — Type E vs unmarked-Type-A:**

A speculative-sounding sentence WITH a marker → Type E (Info-level positive practice)
The same sentence WITHOUT a marker → unmarked-Type-A → **Major finding** (the failure mode the principle exists to catch)

Examples:
- ✅ "*[Hypothesis, unverified]*: CFLT scaffolding produces 15–30% fluency advantage." → Type E, properly marked, **Info**
- ❌ "CFLT scaffolding produces 15–30% fluency advantage." (no marker, no citation, no measurement) → unmarked-Type-A, **Major** — flag as "unmarked speculation: either add evidence (→ Type A) or add a hypothesis marker (→ Type E)"

### Step 4: Four-Block Coverage Check (Type A only)

For each Type A claim, check whether the surrounding ≤500 words include:

| Block | Indicators of presence |
|---|---|
| **Claim** (already extracted) | The sentence itself |
| **Methodology** | "measured by", "study design", "between-subjects", "within-subjects", "operationalized as", specific protocol description |
| **Falsification condition** | "would be refuted by", "if X is not observed", "if the effect size is below", "the prediction fails when" |
| **Adjacent evidence** | Citation(s) to related empirical work with explicit relationship — "methodologically analogous", "predicts the same effect", "shows X in a related context" |

For each missing block, note which one. The severity rule:

| Coverage | Severity |
|---|---|
| 0 of 4 blocks beyond the claim itself | **Major** — likely unmarked-speculation; suggest either add evidence (→ Type A) or add a hypothesis marker (→ Type E) |
| Methodology present, falsification missing | **Major** |
| Methodology + falsification present, adjacent evidence missing | **Minor** |
| All four blocks present | **OK** |
| Trivially-satisfied or tautological falsification | **Minor** |

### Step 4.5: Type E Coverage Check

Type E (marked hypothesis) is the **opposite** check: instead of requiring evidence, the skill verifies that the claim **stays appropriately hedged**.

| Type E coverage | Severity |
|---|---|
| Marker present + rationale ("by analogy with X (Smith 2010)") | **Info** — good practice, log as positive |
| Marker present + rationale + falsification condition (can in principle be tested) | **Info** — best practice |
| Marker present but NO rationale (just "we hypothesize that Y" — no reason given) | **Minor** — recommend adding theoretical or analogical justification |
| Marker present AND surrounding prose claims empirical support that isn't actually cited | **Major** — internal contradiction; either drop the empirical-support claim or remove the hypothesis marker |
| Marker absent but content is clearly speculative (no citation, no measurement, declarative tone) | **Major** — unmarked speculation; route to "Step 5 — Unmarked-Type-A Check" |

### Step 5: Unmarked Speculation / Normative Check

This step catches **two** failure modes — both involve claims that read as Type A (verified empirical) but lack the supporting structure:

**5a — Unmarked normative (Type B without marker):**

For sentences that *appear* to make an empirical claim but are actually design decisions, check whether they are **marked**. Markers include:

- "We propose...", "We stipulate..."
- "CFLT defines...", "the protocol enforces..."
- "By convention...", "For our purposes..."
- A `Status: normative` / `(by design)` tag

If unmarked: **Minor** — recommend reframing.

**5b — Unmarked speculation (Type E without marker):**

For sentences that make a *speculative* claim — no citation, no measurement, no methodology, but declarative tone — check whether they have a hypothesis marker. Markers include those listed in §`falsifiability-template.md` (We hypothesize..., [unverified], etc.).

If unmarked: **Major** — this is the failure mode the principle exists to catch. The author is either:
- Passing speculation as established fact (dishonest framing, even if accidental), OR
- Has actually verified the claim but hasn't surfaced the evidence (incomplete documentation)

The recommended fix is one of:
1. **If verified:** add the citation / measurement → claim becomes Type A
2. **If speculative:** add a hypothesis marker → claim becomes Type E

The skill never tells the user which choice to make — it surfaces the ambiguity.

### Step 6: Does-Not-Claim Section Check

Scan each in-scope file for a subsection that explicitly enumerates what the theory **does not** claim. The CFLT pattern:

> #### What CFLT Does *Not* Claim
> CFLT explicitly does **not** claim: ...

If such a section is **present**, log at Info as good practice.

If **absent** in a foundational doc, log at Info with a suggested template (copy from `falsifiability-template.md`).

### Step 7: Report Generation

Write the report to `_research/falsifiability-audit.md`.

**Report structure**: read the template at `../shared/output-templates/falsifiability-audit.md` and fill in the `{placeholder}` values. The template includes the Type-A-vs-E "choose one fix" template (a/b/c) for unmarked-speculation findings. Loaded only at this step.

### Step 8: Offer Fixes (opt-in)

Use AskUserQuestion:

> Found {N} Major findings on claims lacking falsifiability framing. Would you like me to:
> 1. Draft four-block expansions for each (you confirm each — `--draft` mode)
> 2. Just save the report and exit (default)

If the user picks 1, for each Major finding:
- Generate a four-block draft following the template
- Show the proposed insertion as a diff
- Apply only after explicit confirmation

## Anti-patterns

Do **not**:
- Flag every sentence with a verb as a Type A claim — sentences need empirical signaling to qualify.
- Demand falsification for citation-supported statements that are essentially restating prior literature (those are reviewed, not novel claims).
- Treat hedged claims ("may", "could", "tentatively") as full Type A claims — these are speculative and need a different framing.
- Flag a theory's central design choice (e.g., "CFLT uses four slots") as empirical and demand falsification.
- Recommend deletion of unmarked normative claims — recommend reframing.

## Exit Conditions

- **Clean exit (PASS)**: 0 Major. All Type A claims have at least methodology + falsification.
- **Review required**: ≥1 Major.
- A document with **zero** Type A claims is not failing — it may be a purely normative protocol document. Note this at Info but do not escalate.
