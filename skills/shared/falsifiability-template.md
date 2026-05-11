# Falsifiability Template

Shared four-block schema for falsifiability audits, modeled on Popper (1959 *The Logic of Scientific Discovery*) and operationalized for academic theory documents.

## Concept-import declaration (T2 — partial equivalence)

theory-forge adopts the **Popperian falsifiability framework** as a **T2 (partial equivalence)** concept import per `cross-disciplinary-import-rules.md`. Specifically:

- **Imported**: the demarcation criterion (testable predictions are scientific; untestable claims are not) and the conjecture-and-refutation epistemology (Popper 1963 *Conjectures and Refutations*) — bold hypothesis paired with rigorous attempts at refutation.
- **NOT imported**: Popper's Three Worlds metaphysics; his rejection of induction; his critique of historicism; his Open Society political philosophy.

This is a deliberate partial borrowing: theory-forge uses Popper's epistemological framework as an operational discipline, without committing to the entirety of his philosophy of science. Other philosophies (Kuhn 1962; Lakatos 1970; Feyerabend 1975) emphasize different aspects; the Popperian framing is adopted here for its operational testability.

## Guiding principle: 大胆假设，小心求证 (Bold Hypothesis, Rigorous Verification)

Science progresses by **bold conjecture followed by rigorous attempts at refutation** (Popper 1963 *Conjectures and Refutations*). Theory-forge's role is **not** to suppress speculative claims — that would impoverish the work. Its role is to enforce **honest labeling**:

- **Verified claims** (cited evidence, replicated findings) carry the document's empirical weight; they must be defensible against peer review.
- **Bold hypotheses** are welcome, scientifically valuable, and often the most interesting parts of a theory paper — **but they must be explicitly marked as unverified**.
- **Unmarked speculation passed off as established fact** is the failure mode the audit must catch. It is a form of dishonesty regardless of whether the speculation later turns out to be right.

The skill's job is to keep these three categories distinct in the prose, not to police which speculations are allowed.

## The four blocks

Any descriptive empirical claim in a theory document should be paired with the following four blocks within ≤500 surrounding words. The exemplar is the CFLT `core-concept.md §8.5 P1–P3` pattern.

### Block 1 — Claim (mandatory)

A single declarative sentence stating a measurable prediction. Must be:
- **Specific**: "fluency increases" not "things improve"
- **Quantifiable** where possible: "by ≥10%" beats "noticeably"
- **Conditional on a population / context**: "for adult intermediate L2 learners" not "for everyone"

**Template:**

```markdown
**Claim**: {Population} {action} will {outcome}, controlled for {confounders}.
```

**Example (good):**

> **Claim**: Adult L2 learners taught the CFLT unmarked default will produce sentences with **higher fluency** (words per minute) and **lower hesitation rate** than learners taught a free-order baseline, controlled for vocabulary and grammar coverage.

**Example (bad — too vague to falsify):**

> CFLT helps people learn languages better.

### Block 2 — Methodology (mandatory)

A concrete way to measure whether the claim holds. Must include:
- **Study design** (between-subjects, within-subjects, observational, cross-sectional, longitudinal)
- **Operationalization** of the outcome variable
- **Population** specification
- **Controls / confounders** addressed

**Template:**

```markdown
**Methodology**: {Study design} — {participant description}. Manipulate {independent variable}.
Measure: (a) {outcome 1 operationalization}, (b) {outcome 2 operationalization}.
```

**Example:**

> **Methodology**: A between-subjects intervention study — two groups of intermediate L2 learners receive identical lexical/grammatical content for N weeks; one group additionally drills the CFLT four-slot template, the other group receives free-form output instruction. Measure: (a) words per minute in elicited monologue, (b) hesitation pauses ≥ 250 ms (after Pawley & Syder 1983), (c) error rate.

### Block 3 — Falsification Condition (mandatory)

A specific outcome that would **refute** the claim. This is the Popperian core: if the claim is right, what could happen that would prove it wrong?

**Template:**

```markdown
**Falsification condition**: If {specific empirical outcome contrary to claim}, the claim is refuted.
```

**Example:**

> **Falsification condition**: If the CFLT-trained group does **not** show statistically significant fluency advantage at p < .05 across at least two replication sites, the production-side claim is refuted.

**Anti-patterns** to flag:
- Tautological falsification: "If it doesn't work, it doesn't work" — circular, says nothing
- Untestable: "If consciousness is involved" — not operationalizable
- Trivially-satisfied: "If anyone, anywhere, ever, fails to..." — never refutable

### Block 4 — Adjacent Evidence (optional but strongly preferred)

Existing peer-reviewed work that bears on the claim, even if not direct evidence. Helps the reader judge the claim's prior plausibility.

**Template:**

```markdown
**Adjacent evidence already available**:
- **{Author Year}** — {what it shows; how it relates to the claim}; {strong/weak/methodologically analogous} evidence
- ...
```

**Example:**

> **Adjacent evidence already available**:
> - **VanPatten (1996, 2004) Processing Instruction** — controlled input training of a single grammatical default produces faster acquisition than free-form exposure. Methodologically analogous, not direct evidence for CFLT.
> - **DeKeyser (2007) Practice in a Second Language** — declarative-to-procedural skill-acquisition curves support the existence of a "default schema" benefit. Again, methodologically adjacent.
> - **Kormos (2006) Speech Production and Second Language Acquisition** — L2 speech-production bottleneck is at the formulator stage; structural templates reduce formulator load. Predicts CFLT effect; does not measure it directly.

Be honest about evidence strength. "Methodologically analogous, not direct evidence" is more useful than overclaiming.

## Claim taxonomy

theory-forge's `falsifiability` skill classifies every claim-shaped sentence as one of three types:

### Type A — Descriptive empirical

Makes a claim about the world that could in principle be tested. Requires all four blocks.

Examples:
- "L2 learners taught CFLT produce sentences faster than controls."
- "LLMs trained on CFLT-formatted prompts show higher attention at position 0."
- "PFC activation is reduced for bilinguals using a stable scaffold."

### Type B — Normative / design

A choice the author has made about how the protocol / framework / system should work. Not empirically testable in principle; defensible by argument, not by experiment.

Examples:
- "CFLT places Core in position 0."
- "The four slots are ordered Core → R → S → T."
- "Marked deviations should be principled."

Normative claims should be **marked as such** in the document — typically by introducing them with phrasing like "We propose...", "CFLT defines...", "The protocol stipulates...", or "By convention...". Unmarked normative claims read as empirical claims, which invites readers to demand empirical evidence inappropriately.

### Type C — Definitional / terminological

A statement about how a term is being used. Not empirical, not a design choice — just terminology.

Examples:
- "Core = salience anchor."
- "L1 = the learner's native language."
- "Periphery refers to adjuncts."

Definitional claims do not need falsification conditions. They do need to be **consistent with usage elsewhere** — see `consistency` skill.

### Type E — Marked hypothesis / conjecture / open prediction

A claim the document is making **on principled grounds** (theoretical reasoning, analogy with established findings, extrapolation from adjacent domains) but **without direct empirical verification**. Type E claims are the "大胆假设" half of scientific work. They are legitimate, even valuable — but **only when marked**.

**Markers that classify a claim as Type E:**

- *"We hypothesize..."*, *"We conjecture..."*, *"We predict..."*, *"We tentatively propose..."*
- *"It remains an open question whether..."*, *"We expect..."*, *"It is plausible that..."*
- *"Provisional claim:"*, *"Open hypothesis:"*, *"Unverified prediction:"*
- Status tags: `[unverified]`, `[hypothesis]`, `[open]`, `[provisional]`
- Section structure: nested inside a section explicitly labeled "Hypotheses", "Open Questions", "Predictions", "Conjectures", or "What CFLT predicts but has not yet tested"
- Hedged certainty: *"...likely...", "...probably...", "...we believe..."* — these mark **weaker** Type E (uncertainty acknowledged) and are acceptable but less rigorous than explicit markers

**Examples (good — properly marked):**

> *"We hypothesize that CFLT scaffolding will produce a 15–30% fluency advantage at the intermediate-L2 stage. This prediction has not been tested; it is derived from the methodological analogy with VanPatten (1996) Processing Instruction and the cognitive load reduction argument in §2.1. Falsification: see P1 in §8.5."*

> *"We tentatively propose that the four-element protocol is recoverable across all known natural languages. Evidence is shown for five typologically diverse families (§2.5); evidence for the remaining families is an open research question."*

**Examples (bad — unmarked speculation):**

> *"CFLT scaffolding produces a 15–30% fluency advantage at the intermediate-L2 stage."* ← reads as Type A (verified empirical claim) but has no citation and no measurement; should be Type E and marked

> *"The four-element protocol is universal across all natural languages."* ← reads as Type A; should be marked Type E or partial-evidence Type A

### What Type E does and does not require

**Type E DOES require:**
1. An explicit marker (one of the patterns above), placed within ≤30 words of the claim
2. A brief rationale — *why* the author thinks the hypothesis is reasonable (theoretical analogy, extrapolation from related findings, etc.)
3. Optionally: a falsification condition (when the hypothesis could be tested in principle)

**Type E does NOT require:**
- Direct empirical evidence (that's what makes it Type E, not Type A)
- A four-block structure (that's for Type A)
- A claim to be "small" or "safe" — bold hypotheses are encouraged

### Severity rules for Type E

| Situation | Severity |
|---|---|
| Type E claim with explicit marker + rationale | **Info** (positive — good scientific practice) |
| Type E claim with explicit marker but no rationale | **Minor** — recommend adding rationale |
| **Unmarked speculation passed as Type A** (no marker, no citation, no methodology, but presented declaratively as if verified) | **Major** — this is the failure mode the principle exists to catch |
| Type E claim with falsification condition (becoming a soft Type A prediction) | **Info** (positive — best practice) |

### How to mark a hypothesis (drop-in templates)

For an inline single-sentence hypothesis:

```markdown
*[Hypothesis, unverified]:* We expect {claim}, by analogy with {established finding from cited source}.
```

For a fuller treatment in a dedicated subsection:

```markdown
### Hypothesis H{n}: {one-line statement}

**Status:** Unverified prediction.

**Claim:** {full sentence}

**Rationale:** {Why we expect this. Cite analogous established findings.}

**Falsification:** {Specific empirical outcome that would refute. May be "no known test available" — say so.}

**Adjacent evidence:** {Bullet list of citations to related work that bears on this.}
```

This is just the Type A four-block schema with **Status: Unverified prediction** explicitly set. The structural commitment is the same; only the empirical claim's status differs.

### Type D — Literature restatement (not the document's own claim)

Examples:
- "Liu et al. (2023) **show** a U-shaped positional-accuracy curve in long contexts."
- "Halliday (1967) defined the intonational nucleus as the most prominent tonic element."
- "VanPatten (1996) found that controlled input training accelerates acquisition."

These are paraphrases of someone else's published findings — not claims the document is making on its own behalf. The original publication has already been peer-reviewed; the document is not asserting them as novel predictions.

**Type D claims never trigger falsifiability findings.** The skill recognizes the citation as the source-of-truth and moves on. If a Type D claim contains the *only* empirical content of the document, the skill notes this at Info — the document is doing literature review, not advancing testable claims, which may or may not be the author's intent.

Type D is distinguished from Type A by **grammatical subject**: if the subject is an external citation (`Author (Year)` or `Author et al.`) and the verb attributes the claim to that source, it is Type D.

## What gets reported as a falsifiability finding

| Situation | Severity |
|---|---|
| Type A (descriptive empirical) claim with **no methodology, no falsification condition, no adjacent evidence** in ≤500 surrounding words | **Major** |
| Type A claim with methodology but **no falsification condition** | **Major** |
| Type A claim with methodology + falsification but **no adjacent evidence** | **Minor** |
| Type B (normative) claim presented as if it were empirical (no marking) | **Minor** |
| Type C (definitional) claim contradicting same term used elsewhere | (forwarded to `consistency`) |
| Trivially-falsifiable or tautological falsification condition | **Minor** |
| Honest "what CFLT does not claim" enumeration present | **Info** (positive — note as good practice) |

## The "What does the theory not claim?" subsection

A mature theory document includes an explicit subsection listing claims the theory deliberately **does not** make. This circumscribes the falsifiability frontier and prevents readers from attributing claims the theory does not own.

**Example from CFLT §8.5:**

> #### What CFLT Does *Not* Claim
>
> CFLT explicitly does **not** claim:
> - That all natural languages exhibit Core-First as their dominant word order (descriptive typology — clearly false; English uses SVO, Japanese SOV, Arabic VSO, etc.).
> - That Core-First produces idiomatic native output without further processing (the Grammar Overlay layer handles idiomaticity).
> - That marked deviations are bad style (they are essential to mature fluency).
> - That the four-slot count is mathematically optimal (acknowledged as an open question).

`falsifiability` flags theory documents lacking such a subsection at Info severity, with a suggested template.
