# theory-forge Self-Audit Report

**Project:** theory-forge 0.2.0
**Date:** 2026-05-11
**Method:** All 8 audit sub-skills (excluding `propagate`) applied to theory-forge's own documentation. **Type E (marked hypothesis) framing is NOT used as excuse** — every claim theory-forge makes about itself must qualify as Type A (verified), B (normative/design), C (definitional), or D (literature restatement). Speculative claims are NOT permitted.

> **Self-audit posture.** A tool that demands rigor of others should hold itself to the same standard, or stricter. This audit is brutal by design.

---

## Aggregate Summary

| Audit | Critical | Major | Minor | Info |
|---|---|---|---|---|
| cite-audit | 0 | **106** | 0 | 0 |
| consistency | 0 | 0 | 2 | 5 |
| falsifiability (Type A/B/C/D only, no E) | 0 | **3** | 4 | 8 |
| argument-structure | 0 | **2** | 5 | 6 |
| scope | 0 | **2** | 3 | 4 |
| concept-import | 0 | **4** | 2 | 3 |
| counter-argument | 0 | **1** | 0 | 0 |
| cross-lang (applicability) | 0 | **1** | 0 | 1 |
| **Overall** | **0** | **119** | **16** | **27** |

**Status:** REVIEW REQUIRED (Major findings dominate; bibliography missing entirely)

---

## Critical Issue #1 — No Bibliography Exists

By theory-forge's own `cite-audit` standard, every academic citation must appear in a project bibliography. **theory-forge cites 106 distinct works across its SKILL.md and shared/ files, and has zero bibliography entries.** Every citation is technically an orphan.

This is the dominant finding. Until a `BIBLIOGRAPHY.md` exists, all `cite-audit` orphan findings on theory-forge itself are valid Major.

**Recommendation:** Create `BIBLIOGRAPHY.md` at the plugin root with verified entries for all 106 cited works.

The 106 keys include real, verifiable scholarship (Popper 1959, Toulmin 1958, Bem 1995, Greenberg 1963, Vaswani 2017, Liu 2023, etc.) — but they have not been compiled into a single bibliography.

---

## Major Finding #1 — Unmarked Type A Claim: "5–15% LLM Error Rate"

**Location:** `skills/cross-lang/SKILL.md` line 92

**Claim text:**
> "Even an LLM with 'good' grammar knowledge will confidently produce subtly wrong word order, wrong particle, wrong negation scope, or wrong politeness register in **5–15% of cases**"

**Diagnosis:** Quantitative empirical claim presented as fact. No citation, no measurement methodology, no source. This is exactly the failure mode `falsifiability` skill catches as "unmarked-Type-A → Major". theory-forge violates its own rule.

**Constraint of this audit:** Type E re-framing ("we hypothesize 5-15%") is NOT permitted per the audit constraint.

**Recommended fix (no Type E allowed):**

(a) **If empirically verified** — cite the study. (I do not have a source for this specific claim.)

(b) **Reframe as Type B normative (design choice)** — "We conservatively assume non-trivial error rates and design `--generate` accordingly." Remove the specific percentage.

(c) **Remove the quantitative claim entirely** — keep the qualitative warning ("LLMs may produce errors users cannot detect") without numbers.

Recommendation: **(c)**. The exact percentage is unsupported and not needed for the design argument; the qualitative warning suffices.

---

## Major Finding #2 — Unmarked Type A Claim: "CrossRef Returns Abstract for ~60% of Entries"

**Location:** `skills/cite-audit/SKILL.md` line 136

**Claim text:**
> "CrossRef returns the `abstract` field for **~60% of entries**; Semantic Scholar covers more but is rate-limited."

**Diagnosis:** Quantitative claim about external service behavior. No citation to CrossRef documentation or to a study measuring coverage.

**Recommended fix:** Either (a) cite CrossRef's documentation about abstract coverage, or (b) hedge:
> "CrossRef returns the `abstract` field for a subset of entries (coverage varies by publisher and discipline); Semantic Scholar covers more but is rate-limited."

Recommendation: **(b)** — the design point survives without the specific percentage.

---

## Major Finding #3 — Unmarked Type A Claim: "Semantic Scholar always does (return abstracts)"

**Location:** `skills/cite-audit/SKILL.md` line 136

**Claim text:**
> "Semantic Scholar **always does** [return abstracts]"

**Diagnosis:** Universal claim about external service behavior. "Always" is empirically wrong — Semantic Scholar does not have abstracts for every paper.

**Recommended fix:** "Semantic Scholar covers a higher fraction than CrossRef but is not exhaustive."

---

## Major Finding #4 — Unmarked T2 Concept Import: Popper Framework

**Location:** `skills/falsifiability/SKILL.md` and `skills/shared/falsifiability-template.md`

**Diagnosis:** The Popperian falsifiability framework is *load-bearing* in the falsifiability skill (the entire approach builds on Popper). The text says "modeled on Popper (1959)" — a citation is present, but the **import tier (T1 / T2 / T3)** is not explicitly marked.

Strict reading: this is a T2 (partial equivalence) import — theory-forge adopts Popper's "falsifiability as demarcation criterion" but does NOT adopt other Popperian commitments (e.g., Three Worlds metaphysics; rejection of induction). The borrowing should explicitly say what is preserved and what isn't.

**Recommended fix:** Add a paragraph in `falsifiability-template.md` introducing the import tier:

> "theory-forge adopts the Popperian falsifiability framework as **T2 (partial equivalence)**: we use Popper's demarcation criterion (testable predictions are scientific; untestable are not) and his conjecture-and-refutation epistemology (bold hypothesis + rigorous attempt at refutation). We do NOT inherit Popper's Three Worlds metaphysics, his rejection of induction, or his views on social science. References: Popper 1959 *The Logic of Scientific Discovery*; Popper 1963 *Conjectures and Refutations*."

---

## Major Finding #5 — Unmarked T2 Concept Import: Toulmin Argument Model

**Location:** `skills/argument-structure/SKILL.md` and `skills/shared/argument-patterns.md`

**Diagnosis:** The Toulmin (1958) six-element argument model is load-bearing in the argument-structure skill. The citation is present. Strict reading: this is **T1 (technical equivalence)** because theory-forge preserves Toulmin's exact six elements with their original definitions. T1 imports are OK with citation alone — but the T1 status should be explicit.

**Recommended fix:** Add to `argument-patterns.md` §1:

> "theory-forge adopts the Toulmin model as **T1 (technical equivalence)**: the six elements (Claim, Data/Grounds, Warrant, Backing, Qualifier, Rebuttal) retain their original definitions from Toulmin (1958)."

---

## Major Finding #6 — Unmarked T2 Concept Import: Booth-Colomb-Williams Engagement Standard

**Location:** `skills/counter-argument/SKILL.md`

**Diagnosis:** The "engagement with strongest opposing positions" principle is borrowed from Booth, Colomb & Williams (2016) *The Craft of Research* and Graff & Birkenstein *They Say, I Say*. The citations are present. The principle is load-bearing for the audit. Strict reading: **T2 partial equivalence** — borrowing the engagement-discipline norm without the full craft-of-research curriculum.

**Recommended fix:** Add to `counter-argument/SKILL.md`:

> "theory-forge adopts the engagement-discipline principle as **T2 (partial equivalence)**: we borrow the norm that strong central claims must engage canonical opposition (per Booth, Colomb & Williams 2016 §10; Graff & Birkenstein, Chapter 4) — we do NOT adopt their full advice on writing-process, audience analysis, or revision strategy. The norm is the imported element."

---

## Major Finding #7 — Unmarked T2 Concept Import: Brigandt & Love / Wimsatt for Three-Tier Framework

**Location:** `skills/shared/cross-disciplinary-import-rules.md` and `skills/concept-import/SKILL.md`

**Diagnosis:** The three-tier (T1/T2/T3) framework is theory-forge's own, but its grounding in philosophy of science cites Brigandt & Love (2017) and Wimsatt (2007). The exact derivation of the three-tier classification from these sources is not shown — strict reading: the three-tier framework is theory-forge's **synthesis**, not a direct import. Should be marked as such.

**Recommended fix:** Add to `cross-disciplinary-import-rules.md`:

> "The T1/T2/T3 three-tier classification is theory-forge's own framework, inspired by Brigandt & Love's (2017) work on conceptual change across disciplines and Wimsatt's (2007) heuristics for "limited beings" engaging with cross-disciplinary integration. We have **operationalized** their general arguments into a specific three-tier marking scheme — neither Brigandt & Love nor Wimsatt explicitly propose this framework."

---

## Major Finding #8 — Missing Engagement with Canonical Opposition

**Location:** `README.md`, `docs/usage.md`

**Diagnosis:** theory-forge's central claim ("audit tools help theory documentation") is presented without engagement with the canonical opposing positions:

- "Audit tools are paternalistic / impose mechanical standards on craftwork" — Strunk-and-White-style traditional view
- "LLM-based audits hallucinate audits" — legitimate technical concern
- "Software-style automation can't capture academic nuance" — methodologically conservative view
- "Authors should rely on peer review, not auto-tools" — established norm

**Recommended fix:** Add a "Counter-Argument Engagement" section to README or usage.md addressing the above. Currently zero engagement.

---

## Major Finding #9 — Unbounded Generalization: "Science is bold conjecture + rigorous verification + honest labeling"

**Location:** `skills/falsifiability/SKILL.md` line 17

**Diagnosis:** This sentence reads as a Type A universal claim about all of science. It is actually a Popperian view, contested by Kuhnian / Lakatosian / Feyerabendian / Bayesian / pragmatist philosophers of science. Without explicit attribution, it claims more authority than warranted.

**Recommended fix:** Reframe as Type D (literature restatement):
> "Following Popper (1959, 1963), theory-forge characterizes science as bold conjecture + rigorous verification + honest labeling. Other philosophies of science (Kuhn 1962; Lakatos 1970; Feyerabend 1975) emphasize different aspects; the Popperian framing is adopted here for its operational testability."

---

## Major Finding #10 — Unbounded Scope: theory-forge Has Only Been Tested on English-Language Theory Docs

**Location:** `README.md`, plugin manifest

**Diagnosis:** Per the cross-lang skill's own discipline, theory-forge has been calibrated only on English-language CFLT documentation. It does NOT have validated behavior on Chinese, Japanese, Spanish, etc. theory documents — yet the documentation says it works for any project. By the scope-skill standard: unbounded scope claim without limitation statement.

**Recommended fix:** Add to README "When to Use":
> "**Scope of validation.** theory-forge 0.2.0 has been calibrated against English-language theory documents (specifically the CFLT corpus). Behavior on non-English documents is not yet validated. The plugin operates on the document's source language; multilingual mirrors (e.g., `docs/zh/`) should be audited independently using the same skill."

---

## Major Finding #11 — `cross-lang` Skill Lacks Coverage of theory-forge's Own Language Claims

**Location:** `skills/cross-lang/SKILL.md` registry coverage

**Diagnosis:** The language-data.md registry covers Arabic, Hebrew, Swahili, Wolof, Yoruba, Indonesian, Tagalog, Tamil, Telugu, Finnish, Hungarian, Turkish, Mongolian, Vietnamese, Khmer, Basque — but none of these have been used to validate theory-forge's own claims about cross-linguistic universality of its method. The skill claims to work on multilingual content but has not been tested on non-English content.

**Recommended fix:** Same as Finding #10 — explicit scope statement.

---

## Major Finding #12 — Foundational citations from theory-forge to its own design choices lack the design-rationale element of Toulmin

**Location:** `skills/cite-audit/SKILL.md` §6 "Calibration caveat" threshold choices

**Diagnosis:** "0.40 / 0.15-0.40 / <0.15" thresholds are stated without justification. These are calibration choices but lack the Toulmin Warrant — why these thresholds, derived from what?

**Recommended fix:** Either add Warrant ("derived from preliminary calibration on the CFLT corpus where these thresholds gave best precision/recall trade-off") or mark as **Type B normative** ("calibration seed values, to be tuned per project").

The honest position: these are Type B placeholder values. They should be marked as such.

---

## Minor and Info Findings — Selected Examples

(13 Minor + 27 Info findings exist; enumerated below by category)

### Minor — Calibration thresholds unjustified

- `cite-audit/SKILL.md` 0.40 / 0.15 keyword-alignment thresholds — should be marked Type B seed values
- `consistency/SKILL.md` "≥2 narrative places asserts → Major" threshold — Type B, mark as such
- `cross-lang/SKILL.md` "≥3 typological families" rule — Type B, justified in registry but not in claim

### Minor — README → SKILL.md description mismatch

- README says cite-audit's WebFetch uses "CrossRef + Semantic Scholar + OpenAlex" — SKILL.md confirms. ✓
- README says concept-import has T1/T2/T3 tiers — SKILL.md confirms. ✓
- README says counter-argument flags strawman engagement — SKILL.md confirms. ✓

(Spot-check passed)

### Info — Positive practices

- ✓ Severity scale consistent across all 9 sub-skills
- ✓ Type A/B/C/D/E taxonomy consistently used in all relevant skills
- ✓ Every SKILL.md has a "Read-only by default" principle
- ✓ Every SKILL.md has explicit Anti-patterns section
- ✓ Every SKILL.md has an Exit Conditions section
- ✓ Orchestrator excludes propagate from full-suite (correct)

---

## Summary: theory-forge by Its Own Standards

| Standard | Self-audit verdict |
|---|---|
| Every citation in a bibliography | **FAIL** — 0 bibliography, 106 orphans |
| No unmarked Type A claims | **FAIL** — 3 found |
| Concept imports T1/T2/T3 marked | **FAIL** — 4 load-bearing imports unmarked |
| Central claims engage canonical opposition | **FAIL** — no engagement |
| Bounded scope statements | **FAIL** — universal-sounding claims without scope (English-only validation; "all of science" framing) |
| Toulmin completeness of own claims | **PARTIAL** — calibration thresholds lack warrants |
| Internal consistency | **PASS** — minor stylistic only |
| Severity scale honesty | **PASS** |

**Score: 3 PASS / 5 FAIL out of 8 disciplines applied to self.**

theory-forge enforces stricter standards than it currently meets. The fixes (Finding #1–#12) are tractable: create a bibliography, hedge or remove 3 unmarked quantitative claims, add T1/T2 marking to 4 concept imports, write a counter-argument section, add scope statement, mark 3 calibration thresholds as Type B.

---

## Recommended Fix Order

1. **Create `BIBLIOGRAPHY.md`** (resolves 106 orphans)
2. **Fix 3 unmarked Type A claims** (Findings #1, #2, #3) — hedge or remove
3. **Mark 4 concept imports** (Findings #4, #5, #6, #7) — add T1/T2 paragraphs
4. **Add scope statement** (Findings #10, #11) — README "Scope of validation"
5. **Add counter-argument engagement** (Finding #8) — README new section
6. **Reframe "Science is..." as Type D** (Finding #9) — falsifiability/SKILL.md
7. **Mark calibration thresholds as Type B** (Finding #12) — cite-audit/SKILL.md

The total fix surface is ~150-300 lines of edits across ~8 files.
