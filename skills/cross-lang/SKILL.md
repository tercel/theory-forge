---
name: cross-lang
description: >
  Audit academic theory documents for cross-linguistic universality-claim coverage.
  Flags claims of universality with insufficient typologically-distinct worked examples
  (default threshold: ≥3 distinct language families). Optionally generates draft examples
  in missing families, grounded in the language-data registry of reference grammars.
instructions: >
  Read-only by default. Generation mode (--generate) creates draft examples that must be
  marked for native-speaker review before publication. Never generate examples in languages
  not covered by the language-data registry — fail loudly instead of guessing.
---

# cross-lang — Cross-Linguistic Universality-Claim Coverage

A claim of cross-linguistic universality requires evidence across typologically diverse languages. A "universal" demonstrated only in English, German, and Spanish (three Indo-European languages) is not a universal — it is a Standard-Average-European observation. theory-forge's `cross-lang` skill enforces this evidentiary standard.

## Core Principles

1. **Family-based coverage.** Languages are grouped by family (see `../shared/language-data.md`). A document needs ≥3 distinct families per universality claim for adequate coverage.
2. **Reference-grammar grounding.** Generated examples must cite a registered reference grammar; never hallucinate examples in languages without registry coverage.
3. **Native-speaker review required.** Generated examples are drafts. The skill marks them clearly so they are not mistaken for verified data.
4. **Read-only by default.** Generation requires explicit `--generate` flag.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/language-data.md` — family-language registry with reference grammars and romanization standards
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/falsifiability-template.md` — load at Step 3a ONLY for Type E (marked-hypothesis) detection on universality claims
- `../shared/output-templates/cross-lang-audit.md` — load at Step 5 (Report Generation)

## Workflow

### Step 1: Identify Universality Claims

For each in-scope file (default: `docs/**/*.md` excluding `_research/` and `_drafts/`), scan for sentences asserting cross-linguistic universality. Triggers:

- Phrases: *cross-linguistic*, *universal*, *all languages*, *every language*, *language-agnostic*, *language-independent*, *across languages*, *cross-typological*, *typologically*
- Modifiers like *Greenberg-universal*, *implicational universal*, *absolute universal*

For each match, record:
- File path, line number
- Surrounding section heading
- The full claim sentence
- The surrounding ≤500 words (the *example block* the claim depends on)

### Step 2: Extract Worked Examples in the Example Block

For each universality claim, scan the example block for language-tagged examples. Detection patterns:

- **Explicit language tags**: `| **English** |`, `| **Mandarin** (...)|`, `**Japanese**:`, "*In English, ..."
- **Native scripts**: Chinese characters (U+4E00–U+9FFF), Japanese kana (U+3040–U+30FF), Hangul (U+AC00–U+D7AF), Arabic (U+0600–U+06FF), Devanagari (U+0900–U+097F), Cyrillic (U+0400–U+04FF), Greek (U+0370–U+03FF), Hebrew (U+0590–U+05FF)
- **Romanization conventions**: pinyin tone marks (mā/má/mǎ/mà), Hepburn rōmaji (ō, ū), DIN 31635 (ḥ, ṣ, ʿ)
- **IPA brackets**: `/.../` or `[...]`

For each example detected, identify the language and look up its family in the registry.

### Step 3: Coverage Analysis (Hypothesis-Aware)

> **Anti-false-positive rule.** A universality claim that is **explicitly marked as a hypothesis** ("We hypothesize universality holds; evidence shown for X/Y; coverage of [other families] is an open research question") is doing science honestly even with partial coverage. The skill must distinguish:
>
> - **Verified Type A universality claim** ("The four-slot protocol is universal across natural languages.") → requires ≥3 typological families for adequate coverage
> - **Marked Type E universality hypothesis** ("We hypothesize the four-slot protocol is universal; evidence shown for 5 families in §2.5; coverage of additional families is an open question.") → coverage requirement scales down; ≥2 families with explicit open-scope marker is acceptable
>
> See `shared/falsifiability-template.md` for Type E marker patterns.

#### Step 3a: Classify the universality claim's framing

For each claim, check for hypothesis markers within ≤50 words:
- "we hypothesize", "we conjecture", "we predict", "[unverified]", "[provisional]"
- "open question", "remains to be tested", "evidence for X; further work needed for Y"
- Inside a section labeled "Hypotheses", "Open Questions", "Predictions", "Scope"

If markers found → classify as **Type E (marked-hypothesis universality)**.
If absent → classify as **Type A (verified-universality)** — apply stricter coverage requirements.

#### Step 3b: Coverage requirements by classification

| Distinct families | Type A (verified) | Type E (marked-hypothesis) |
|---|---|---|
| ≥3 | **Adequate** | **Adequate** |
| 2 | **Minor** — recommend +1 family | **OK if scope-marked**; Minor if scope not stated |
| 1 (e.g., English-only) | **Major** | **Minor** if "[hypothesis, evidence so far for X family]" framing present; **Major** otherwise |
| 0 (claim made but no examples) | **Major** | **Major** even with hypothesis marker — a bold hypothesis still needs at least one worked example |

Additional checks (apply to both Type A and Type E):

- **Indo-European-only**: ≥3 examples but all from Indo-European → **Minor** for Type A (typologically thin); **Info** for Type E if scope-marked
- **Family imbalance**: 4 Indo-European + 1 other → **Minor** for Type A; **Info** for Type E if scope-marked
- **Excellent practice (Info, positive)**: Type E with explicit per-family scope statement, e.g., "evidence shown for Indo-European, Sino-Tibetan, Japonic, Koreanic, Afro-Asiatic; remaining families are open" — log as good scope discipline

### Step 4: Generation Mode (`--generate` — experimental, scaffold-by-default)

> **Safety principle.** Generating concrete sentences in languages the project author cannot personally verify is the single highest hallucination risk in theory-forge. *[Type B — design assumption]*: even an LLM with general competence in a language can produce subtly wrong word order, wrong particle, wrong negation scope, or wrong politeness register at a non-trivial rate. A `[Draft]` tag does not eliminate this risk; users routinely under-read disclaimers and over-trust generated content. (The specific error rate is not quantified here because we lack a calibrated measurement; the design conservatively assumes errors will occur.)
>
> **Therefore `--generate` produces SCAFFOLDS by default, not concrete sentences.** A scaffold is a template that tells the user what to look up; the user (or a native-speaker collaborator) fills in the concrete surface form. Generating concrete surface forms requires the explicit `--generate-surface` flag AND a `--i-have-native-speaker-review` acknowledgment, AND the SKILL.md emits a louder warning.

#### Default mode: `--generate` produces scaffolds

For each Major-flagged claim:

1. Identify which families are missing from the example block.
2. Pick 1 target language per missing family.
3. Emit a **scaffold** in this format:

   ```
   **Japanese** (Japonic) — SCAFFOLD
   Proposition to translate: "I didn't go out, because it rained, at home, yesterday"
   Following the CFLT-L2 unmarked order: [Core] → [Reason] → [Space] → [Time]

   Reference grammar consultation needed:
     - Negation pattern: Kuno (1973) §3.2; Tsujimura (2014) §4
     - Reason clause connector: Tsujimura (2014) §11 (kara / node)
     - Locative postposition (action-locative): Kuno (1973) §2; Tsujimura (2014) §6
     - Temporal adverb position: Shibatani (1990) §3

   Translator: [ ] please fill the four slots
     Slot 0 (Core):       ____________________________
     Slot 1 (Reason):     ____________________________
     Slot 2 (Space):      ____________________________
     Slot 3 (Time):       ____________________________

   [ ] Native-speaker review completed (initial: ____)
   ```

4. The user/collaborator fills in the scaffold by consulting the cited grammar.

#### Explicit-surface mode: `--generate-surface --i-have-native-speaker-review`

ONLY when the user passes both flags together:

1. Generate a concrete surface form following the scaffold above.
2. The generated text **must** be prefixed in the document with a highly-visible warning block:

   ```
   > ⚠️ **MACHINE-DRAFTED EXAMPLE — REQUIRES NATIVE-SPEAKER VERIFICATION**
   > This example was machine-generated and has NOT been verified for grammaticality, naturalness,
   > or register-appropriateness. Do not publish without confirmation from a competent native
   > speaker of {language}. Confidence: {LLM-self-reported}/10. Reference grammar consulted:
   > {citation}.
   ```

3. Mark the example in document with `<!-- theory-forge: machine-drafted, unverified -->` so future audits can find and flag it.

4. **Never invent forms.** If you cannot derive the form from registered reference grammars with high confidence (≥8/10 self-reported, which the model is poorly-calibrated on — so default to "low confidence" doubt), do NOT produce a concrete form — fall back to scaffold mode for that language.

5. **Never generate examples in languages without a registry entry.** Fail loudly: "Cannot generate example in {language} — no reference grammar registered. Add an entry to `language-data.md` first, or use a language from the registry."

#### Audit of previously machine-drafted examples

A new check: scan the corpus for `<!-- theory-forge: machine-drafted, unverified -->` markers. Each unverified marker is a **Minor** finding (potentially Major if it appears in a foundational doc) — recommend native-speaker review.

If the corpus contains examples *added by the user without machine markers* (e.g., the CFLT §2.5 5-language table added pre-theory-forge), the skill cannot automatically know whether they have been native-speaker reviewed. Surface this as an Info finding suggesting the user audit pre-existing examples.

### Step 5: Report Generation

Write the report to `_research/cross-lang-audit.md`.

**Report structure**: read the template at `../shared/output-templates/cross-lang-audit.md` and fill in the `{placeholder}` values, including the per-claim example-block coverage breakdown and (if --generate or --generate-surface) the draft scaffolds. Loaded only at this step.

### Step 6: Offer Insertion (opt-in)

In `--generate` mode, after generating drafts:

> Generated {N} draft cross-linguistic examples. Would you like me to:
> 1. Insert all drafts inline (each marked `[Draft — verify with native speaker]`)
> 2. Save drafts in `_research/cross-lang-drafts.md` for review before insertion (default)
> 3. Discard drafts

## Anti-patterns

Do **not**:
- Generate examples in a language not in the registry — flag as "synthesis not possible" instead
- Treat a single multi-language table as multiple claims — one claim, one example block
- Flag every section using one language as a violation — only flag when a **universality claim** is made
- Recommend adding examples from endangered or under-documented languages without an established reference grammar
- Auto-insert drafts without the user confirming each (in `--generate --apply` mode)

## Exit Conditions

- **Clean exit (PASS)**: 0 Major. All universality claims have ≥3-family coverage.
- **Review required**: ≥1 Major.
- If `--generate` ran successfully, the report includes drafts that the user can incorporate selectively.
