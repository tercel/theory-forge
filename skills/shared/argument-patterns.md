# Argument Patterns: Toulmin Model + Fallacies in Theory Work

Reference for `argument-structure` and `counter-argument` sub-skills. Codifies the Toulmin (1958) argument model and a curated catalog of logical fallacies common in theoretical/foundational writing.

## Concept-import declaration (T1 — technical equivalence)

theory-forge adopts the **Toulmin six-element argument model** as a **T1 (technical equivalence)** concept import per `cross-disciplinary-import-rules.md`. The six elements (Claim, Data/Grounds, Warrant, Backing, Qualifier, Rebuttal) retain their original definitions from Toulmin (1958) *The Uses of Argument* — no semantic drift is introduced. The Toulmin model is also expanded with operational detection heuristics for theory documents in §1.2.

The **fallacy catalog** in §2 is a synthesized collection drawn from Walton (2008) *Informal Logic* and standard logic-textbook treatments; the specific operationalization for theory-document detection is theory-forge's own work and should be regarded as **T2 (partial equivalence)** — the fallacy names and forms are preserved; the detection heuristics are adapted.

## 1. The Toulmin Model

A well-formed scholarly argument has up to six elements (Toulmin 1958 *The Uses of Argument*; Toulmin, Rieke & Janik 1984 *An Introduction to Reasoning*). For theory-forge purposes the audit checks for the first five:

| Element | What it is | Example (from CFLT) |
|---|---|---|
| **Claim** | The conclusion the author wants accepted | "*The cognitive core of an utterance is its universally-prioritized linear position.*" |
| **Data / Grounds** | The evidence offered for the claim | Cross-linguistic worked examples in 5 families; LLM positional-bias studies (Liu 2023; Xiao 2023) |
| **Warrant** | The inferential bridge from data to claim | "Salience anchors map to position 0 because the Salience Network prioritizes attention to foregrounded events (Seeley 2007)" |
| **Backing** | Theoretical justification for the warrant itself | Talmy's Figure-Ground; Langacker's profile; Halliday's intonational nucleus |
| **Qualifier** | Scope-restricting language ("usually", "in adult learners", "for languages with X property") | "**unmarked default**, not the only permitted form" |
| **Rebuttal** | Cases / conditions where the claim does NOT hold | §6 marked deviations; §8.5 "What CFLT Does *Not* Claim" |

### 1.1 Argument completeness

A claim is **complete** when it has at least Claim + Data + Warrant + Qualifier. Backing strengthens; Rebuttal is the mark of intellectual honesty.

| Element coverage | Verdict |
|---|---|
| Claim only ("X is true.") | **Bare assertion** — Major finding unless it is Type B normative or Type C definitional |
| Claim + Data ("X is true. See Smith 2010.") | **Citation-supported but unmotivated** — Minor (reader cannot tell why Smith's data supports X) |
| Claim + Data + Warrant | **Adequate** — minimum complete |
| Claim + Data + Warrant + Qualifier | **Solid** — scope is honest |
| Claim + Data + Warrant + Qualifier + Rebuttal | **Excellent** — engages limitations |
| Claim + Data + Warrant + Qualifier + Rebuttal + Backing | **Mature** — full Toulmin |

### 1.2 Audit heuristics for each element

**Claim** — easy to detect (any declarative sentence) — already located by `falsifiability` skill's claim extraction.

**Data / Grounds** — look for nearby (≤200 words):
- Citation `(Author Year)` or `Author (Year)`
- Worked example (italicized in prose, table row, code block)
- Numerical result with statistical claim
- A reference to another section: "(see §N)"

**Warrant** — look for inferential connectives:
- Because, since, given that, by virtue of, owing to, due to
- This follows from, derives from, is predicted by, is implied by
- A causal chain: "X → Y → Z"

**Backing** — look for theoretical-framework references:
- "In the framework of...", "following the X tradition", "per the Y model"
- Citation of foundational works (not direct evidence)

**Qualifier** — look for scope-restricting modifiers:
- "Usually", "typically", "in most cases", "for X but not Y"
- "Under conditions of W", "in the regime where V"
- "At the protocol layer", "in the unmarked case"

**Rebuttal** — look for limitation/exception statements:
- "Except when...", "this does not hold for..."
- "However, ...", "but note that..."
- "A reader might object that..." — explicit anticipation
- Section labels: "Limitations", "Honest Limitations", "Caveats", "What X does NOT claim"

## 2. Common Fallacies in Theory Work

Fallacies the audit explicitly checks for. Each entry has the fallacy name, what it looks like in theory prose, and how the skill detects it.

### 2.1 Affirming the Consequent

**Form**: "If X then Y. Y. Therefore X." This is invalid — Y might be caused by something other than X.

**In theory prose**: "Our theory predicts higher attention to position 0. LLMs show higher attention to position 0 (Liu 2023). Therefore our theory is supported."

**Detection**: Pattern of "Our theory predicts P. P is observed. Therefore our theory is correct." Without ruling out alternative theories that also predict P.

**Severity**: Major.

### 2.2 Ad Hoc Rescue

**Form**: When a theory faces a counterexample, an arbitrary exception is added to save the theory rather than revising the core claim.

**In theory prose**: "But this is a special case where the protocol does not apply." (Without principled reason for the exception.)

**Detection**: Patterns like "But in case X..." or "However, X is an exception..." that immediately follow a discussion of counterexamples, without a principled rationale citing prior theory or independent evidence.

**Severity**: Major when the exception class is novel; Minor when there's prior theoretical justification.

### 2.3 No-True-Scotsman

**Form**: When a counterexample emerges, redefining the category to exclude it.

**In theory prose**: "But that's not a *real* universality claim — we mean unmarked, structural, conceptual universality." (After the original claim was just "universality".)

**Detection**: Definition shifts after the introduction of a counterexample. Cross-check `consistency` findings.

**Severity**: Major.

### 2.4 Argument from Authority (alone)

**Form**: "X is true because Author Y said so." Citation as a substitute for argument, not as evidence.

**In theory prose**: "As Chomsky (1986) argues, X." — without the actual argument shown or summarized.

**Detection**: Citations immediately preceding/following a claim, with no warrant linking them. If the inline citation pattern is `As Author (Year) argues, X.` followed by no further support, flag.

**Severity**: Minor. Authority citations are not invalid — they are weakly supportive without warrant.

### 2.5 Scope Creep

**Form**: A claim is introduced with narrow scope, then later used as if it had wide scope.

**In theory prose**: §3 says "for adult intermediate L2 learners"; §7 says "for all learners."

**Detection**: Same construct, different scope qualifiers across sections. Forwarded to `consistency` and `scope` skills.

**Severity**: Major.

### 2.6 Equivocation

**Form**: Using the same word with different meanings in different parts of the argument.

**In theory prose**: "Salience" used as cognitive-psychology technical term in §2, then as informal "importance" in §5.

**Detection**: Same lemma with different definitions across sections. Cross-references `consistency` skill's terminology drift check.

**Severity**: Major when the equivocation supports an inference; Minor otherwise.

### 2.7 Texas Sharpshooter

**Form**: Drawing the target around the bullet holes — selecting data that confirms a pattern, ignoring data that doesn't.

**In theory prose**: Cross-linguistic examples chosen specifically because they fit the protocol, without acknowledging languages where the protocol fails.

**Detection**: Hard to detect automatically; surface at Info if the `cross-lang` audit notes that all examples confirm the claim and none discuss exceptions.

**Severity**: Info.

### 2.8 Base-Rate Neglect

**Form**: Ignoring how common the claimed effect is in unrelated populations.

**In theory prose**: "Adult L2 learners trained on CFLT showed improvement." — without saying what the improvement rate is for untrained adult L2 learners (base rate).

**Detection**: Empirical claims about effects without baseline data. Forwarded to `falsifiability` skill's methodology block check.

**Severity**: Minor.

### 2.9 Argument from Lack of Counterexample

**Form**: "No counterexample has been found, therefore X is true."

**In theory prose**: "We have not yet encountered a language that violates the protocol."

**Detection**: Phrases like "no counterexample", "yet to find", "we have not seen". This is weak evidence (absence of evidence is not evidence of absence) but legitimate when combined with explicit scope statement.

**Severity**: Info if scope is stated; Minor otherwise.

### 2.10 Ad Hoc Concept Smuggling (cross-disciplinary)

**Form**: A concept defined precisely in field A is imported into field B without preserving its technical content.

**In theory prose**: "Attention" used as a technical term from Transformer architecture in one paragraph, then as informal "what the speaker is attending to" in another.

**Detection**: Forwarded to `concept-import` skill.

**Severity**: Major when the imported concept does heavy lifting; Minor when used metaphorically and so marked.

## 3. Output Schema (per finding from argument-structure audit)

```yaml
- id: AS001
  severity: Major
  fallacy_or_gap: missing_warrant
  location:
    file: docs/en/foundations/core-concept.md
    line: 31
  claim_text: "Core selection is a semantic decision the speaker makes."
  toulmin_coverage:
    claim: present
    data: absent
    warrant: absent
    qualifier: absent
    rebuttal: absent
  recommended_fix: >
    The claim asserts a cognitive process (speaker's semantic decision) without
    citing evidence (data) or explaining the inferential link (warrant). Consider:
    - Add citation to a speech-production model (e.g., Levelt 1989) that supports
      "semantic-decision" framing.
    - Add a one-sentence warrant: "This follows from Levelt's Conceptualizer →
      Formulator → Articulator separation, which places intentional-content selection
      before linguistic encoding."
    - Add a qualifier: "for utterances expressing the speaker's primary intent."
```

## 4. Anti-patterns for the auditor

Do **not**:
- Flag every short sentence as "claim only" — Toulmin elements may span multiple sentences
- Require Rebuttal on every claim — Rebuttal is a mark of excellence, not a baseline requirement
- Treat Type B normative or Type C definitional claims as bare assertions — they don't need data/warrant
- Score a paragraph as "fallacy = ad hoc" without considering whether the prior context provides principled justification
- Flag affirming-the-consequent on a single sentence — the fallacy spans the *inferential structure* across sentences

## See Also

- `falsifiability-template.md` — claim type taxonomy (Type A/B/C/D/E) used to gate which claims need Toulmin checks
- `academic-severity-levels.md` — severity calibration
