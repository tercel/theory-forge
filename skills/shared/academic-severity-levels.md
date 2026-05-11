# Academic Severity Levels

Canonical severity rubric shared across all theory-forge sub-commands. Calibrated to match `spec-forge:audit` severities so users moving between projects don't re-learn the scale.

## The honesty principle (read first)

theory-forge severities encode one guiding principle: **honest labeling**. A claim that is genuinely speculative but transparently marked as such is **healthier** than a claim that *might* be verified but isn't framed clearly.

- A marked hypothesis ("*[Hypothesis, unverified]:* X is true because Y") → **Info** (good practice)
- An unmarked equivalent ("X is true.") with the same evidentiary base → **Major** (passes speculation as fact)

The severity is set by **framing**, not by whether the underlying claim turns out to be right. Severity is not a judgment about the science; it is a judgment about the writing. (See `shared/falsifiability-template.md` for Type A/B/C/D/E classification.)

| Severity | Meaning | Typical examples |
|---|---|---|
| **Critical** | Documentation is wrong in a way that misleads readers or undermines a central claim of the theory. A reader who trusts the document will form a false belief about scholarship or empirical reality. | Cited paper does not exist (fabricated citation); cited author/year is wrong (attribution error); the formal definition directly contradicts the narrative; universality claim asserted with zero non-English examples; descriptive claim presented without any empirical anchor. |
| **Major** | Significant inconsistency, gap, or under-justified claim that materially weakens the work. The reader can still navigate but the work would not survive peer review in its current state. | Bibliography orphan (claim cited but not in bibliography); cross-section component-list mismatch (e.g., §2.4 lists X, §9 omits X); **Type A claim missing methodology or falsification condition**; **unmarked Type E** (speculation passed as established fact — no citation, no measurement, declarative tone, no hypothesis marker); universality claim with **only 1 typological family** represented (or zero examples); mis-attribution of a real claim to the wrong paper (citation exists but says something different). |
| **Minor** | Quality issue that degrades polish but does not affect correctness. | Inconsistent citation format (Author, Year vs Author Year); bibliography entry never cited (unused); marked deviation example missing a pragmatic-license citation; cross-lang example uses outdated romanization; **universality claim with 2 typological families represented** (some evidence but typologically thin); ambiguous-shorthand citation (`Aslin & Newport 1996` vs bibliography `Saffran, Aslin & Newport 1996`); alias drift (same construct named differently across docs); trailing whitespace or formatting drift. |
| **Info** | Observation that may be useful but does not require action. Also: **positive practice notes** worth highlighting. | A claim could be strengthened by an additional citation; a worked example could be added; a section is short relative to others; **a properly-marked Type E hypothesis with rationale** (positive practice — log the document is doing science honestly); **a "What X does NOT claim" subsection present** (positive practice). |

## How to apply

1. **Default to honesty over thoroughness.** A 4-issue audit with 4 real Major findings is more valuable than a 40-issue audit padded with Info noise.
2. **Severity is a property of the issue, not of the section.** A foundational section can have all-Info findings; a marginal section can have a Critical finding. Don't anchor severity to perceived section importance.
3. **Major and above must include a fix recommendation.** Critical/Major findings without a concrete fix path are half-done.
4. **Critical findings stop the press.** If `cite-audit` finds a fabricated citation, halt and require user attention before any auto-fix flow proceeds.
5. **Info findings are batched.** Don't enumerate Info-level issues individually if there are many — report a count and a representative sample.

## Output ordering

Every report lists findings in this order:

1. Critical (in narrative order — first occurrence in the doc first)
2. Major (same)
3. Minor (grouped by category)
4. Info (summarized, not enumerated)

Reports must include a header table summarizing counts:

```
| Severity | Count |
|---|---|
| Critical | 0 |
| Major    | 3 |
| Minor    | 7 |
| Info     | 12 |
```

A clean report (0 Critical, 0 Major) ends with `Status: PASS` and a brief congratulatory line; otherwise `Status: REVIEW REQUIRED`.
