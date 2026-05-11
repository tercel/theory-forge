---
name: counter-argument
description: >
  Audit academic theory documents for engagement with opposing positions —
  competing theories, alternative explanations, known critiques, and
  counterexamples. Theory papers making strong central claims should
  acknowledge and address objections. Flags central claims that go
  unchallenged. Read-only by default.
instructions: >
  Be charitable. A theory paper doesn't need to enumerate every possible
  objection; it needs to engage with the *salient* ones. Look for a
  "Limitations", "Alternative Explanations", "Competing Theories", or
  "Counterarguments" subsection; if absent, check whether central claims
  are surrounded by any acknowledgment of opposing positions.
---

# counter-argument — Opposing-Position Engagement Audit

This skill enforces a single discipline: **central claims should engage with the strongest opposing positions, not the weakest**.

## Concept-import declaration (T2 — partial equivalence)

theory-forge adopts the **engagement-discipline norm** as a **T2 (partial equivalence)** concept import per `cross-disciplinary-import-rules.md`. Specifically:

- **Imported**: the norm that strong central claims should engage canonical opposition, drawn from Booth, Colomb & Williams (2016 *The Craft of Research* §10) and Graff & Birkenstein (2018 *They Say, I Say*, Ch. 4).
- **NOT imported**: their full advice on writing process, audience analysis, revision strategy, or genre conventions.

The norm — and its operationalization as "no engagement of canonical opposition for a central claim → Major finding" — is the imported element. The rest is theory-forge's own.

Theory papers that fail this check tend to read like manifestos — internally coherent, externally dismissive. The audit's job is to surface central claims that have not been challenged so the author can decide whether to engage.

## Core Principles

1. **Strongest opponents, not weakest.** A paper that addresses only weak strawmen of opposing positions is worse than one that addresses none — it creates the false appearance of engagement. The audit prefers no engagement to strawman engagement.
2. **Salient objections, not every objection.** Not every possible critique requires engagement. The audit looks for objections from the **field's canonical opposing positions** (per topic).
3. **Central claims, not auxiliary.** Engagement is required for the document's thesis-level claims; auxiliary claims need not have full rebuttal structures.
4. **Read-only by default.**

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/argument-patterns.md` — §1 Rebuttal element of Toulmin
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/falsifiability-template.md` — load at Step 1 ONLY for Type A/E classification of central claims (skip B/C/D)
- `../shared/output-templates/counter-argument-audit.md` — load at Step 6 (Report Generation)

## Workflow

### Step 1: Locate Central Claims

Reuse `argument-structure/SKILL.md` Step 2 to identify central claims (Type A + Type E claims appearing in: abstract, introduction, first paragraph of foundation sections, manifesto / core-concept thesis statements).

### Step 2: Identify Canonical Opposing Positions Per Topic

For each central claim, look up known opposing positions. theory-forge ships with a curated map of common topics → canonical opposition (project authors can extend via a `--opposition-map` flag).

Example map (CFLT-relevant):

| Central claim topic | Canonical opposing positions to engage |
|---|---|
| Word-order universality | Greenberg-style typological diversity (Dryer 2013); Newmeyer (2005) functional-only resistance to universals |
| Cognitive primacy of speaker-intent | Behaviorist / connectionist alternatives (Christiansen & Chater 2008); usage-based grammar (Tomasello 2003) |
| LLM attention-position effects | Counter-evidence: Min et al. (2022) showing demo-content can dominate position; positional embeddings cancel primacy in some architectures |
| L2 fluency from explicit instruction | Strong Krashen (1985) input-only position; Long (2015) interactionist position |
| Universal Grammar | Construction Grammar (Goldberg 1995, 2006); Usage-Based Grammar (Tomasello 2003); Connectionism |
| Information structure / Theme-Rheme | Pragmatic alternatives (Gundel 1985); intonation-based theories |

For topics not in the map, the skill will:
- Surface a "no canonical opposition known to theory-forge" note
- Recommend the user verify they have engaged the strongest critic of their position

### Step 3: Scan for Engagement

For each central claim and its known opposing positions, scan the document for engagement signals:

| Signal | Strength |
|---|---|
| Section labeled "Counterarguments" / "Alternative Explanations" / "Competing Theories" that names opposing positions | Strong |
| In-line "A reader might object..." / "Critics have argued..." / "Against this view..." | Medium |
| Citation of opposing position with a substantive response (not just citation) | Medium |
| Acknowledgment in a "Limitations" / "What X does NOT claim" section | Weak (acknowledges limit but doesn't engage opposition) |
| No reference to the opposing position anywhere | None — gap |

### Step 4: Engagement Grading

| Engagement level | Severity |
|---|---|
| Strong engagement on all central claims' canonical oppositions | OK |
| Medium engagement on central claims (in-line acknowledgment + substantive response) | OK |
| Weak engagement (acknowledged as limitation only, not actively engaged) | **Minor** — recommend a substantive response |
| **No engagement with the canonical opposition of a central claim** | **Major** — significant gap |
| Engagement with a strawman version of opposition (engages but oversimplifies) | **Major** — the appearance of engagement is worse than none |

### Step 5: Strawman Detection

For each detected engagement, evaluate whether the opposing position is **fairly characterized**:

- Compare the document's summary of the opponent's view against the opponent's actual published statements (Claude can do this via training data or WebFetch if needed)
- Flag oversimplifications where the document's "opponent" claim is weaker than what the opponent actually argued

This is a hard NLP problem; the skill is **conservative** — flag only when the misrepresentation is unambiguous (e.g., the document says "Chomsky claims X" but Chomsky's actual published position is the opposite).

### Step 6: Report Generation

Write the report to `_research/counter-argument-audit.md`.

**Report structure**: read the template at `../shared/output-templates/counter-argument-audit.md` and fill in the `{placeholder}` values, including the Engagement Profile table and the Strawman Engagement section. Loaded only at this step.

### Step 7: Offer Engagement Drafts (opt-in)

Use AskUserQuestion:

> Found {N} central claims lacking engagement of canonical opposition. Would you like me to:
> 1. Draft an "Engagement of Opposing Positions" subsection per finding (you confirm each)
> 2. Just save the report and exit (default)

`--draft` mode: for each Major finding, propose a ≤200-word engagement paragraph that fairly characterizes the opposing position and gives a substantive response. **Always frame the draft as a starting point requiring author refinement** — engagement is intellectually substantive work that the audit cannot fully automate.

## Anti-patterns

Do **not**:
- Demand engagement of every possible opposing position — focus on canonical ones
- Generate engagement drafts that strawman the opposition
- Flag the absence of a "Counterarguments" section when in-line engagement is present
- Re-flag central claims that `falsifiability` already flagged as unmarked-Type-A — cross-link instead
- Treat citation alone as engagement — engagement requires a substantive response, not just acknowledgment

## Exit Conditions

- **PASS**: 0 Major. Every central claim has at least medium engagement of its canonical opposition (or the claim is scoped narrowly enough that no canonical opposition exists).
- **Review required**: ≥1 Major. Surface gaps for author attention.
- **Note**: a PASS does not mean the document has *successfully* refuted all critics — only that it has *engaged* them. Whether the engagement succeeds is a matter for peer review.
