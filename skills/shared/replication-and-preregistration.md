# Replication and Preregistration

Reference for `falsifiability` and `cite-audit` sub-skills. When a theory document **cites experimental or observational findings**, certain metadata about those findings should be transparently reported. This document codifies the expectations (Munafò et al. 2017 *A Manifesto for Reproducible Science*; Nosek et al. 2018 *Preregistration revolution*).

## Why this matters for theory documents

A theory document does not run experiments itself. But it **cites** experimental work, and those citations are stronger or weaker evidence depending on the cited study's reliability indicators:

- A pre-registered RCT with effect-size + confidence interval is stronger evidence than a single exploratory study
- A study without effect size and without pre-registration may still be valid but is more likely to suffer from "garden of forking paths" issues (Gelman & Loken 2014)

Theory-forge does **not** down-grade citations based on these indicators. It surfaces them so the author can decide whether the strength of evidence is appropriate to the claim being supported.

## Indicators to surface

For each citation supporting an empirical claim (Type A or Type E with empirical hedging), surface in the audit report:

| Indicator | What it tells the reader | How to check |
|---|---|---|
| **Pre-registration** | The hypothesis was committed to before data collection | Look for "OSF", "AsPredicted", "ClinicalTrials.gov", "registered report" in the bibliography entry or accessible via DOI |
| **Effect size** | The magnitude of the effect, not just statistical significance | Look for Cohen's *d*, *r*, *η²*, OR, RR in the cited claim's surrounding context |
| **Confidence interval** | Range of plausible effect values | Look for "(95% CI: x.xx, y.yy)" or "[lower, upper]" |
| **Sample size** | Statistical power | Look for "N = ..." in surrounding context |
| **Replication status** | Has the finding been replicated? | Look for "replicated by", "Many Labs", "Registered Replication Report" |
| **Open data / code** | Can readers verify the analysis? | Look for OSF link, GitHub link, "code available at" |
| **Preprint vs peer-reviewed** | Has it been through peer review? | bioRxiv / arXiv / OSF preprints are preprints; journals are peer-reviewed |

## Severity-mapping

For a citation in support of a Type A empirical claim:

| Citation indicators | Audit response |
|---|---|
| Effect size + CI present in cited paper AND surfaced in the citing claim | OK — Info (good practice) |
| Effect size + CI in cited paper, NOT surfaced in citing claim | Info — recommend surfacing |
| Cited paper lacks effect size / CI (pre-2010 common; many specific subfields) | Info — note the evidentiary limitation, don't penalize |
| Cited paper is a preprint that has not been peer-reviewed | Info — note status, don't penalize (preprints are valid sources) |
| Cited claim's effect size in citing doc differs from cited paper's actual effect size | **Major** — citation distortion (Greenberg 2009 JAMA pattern) |
| Cited paper has been **retracted** | **Critical** — retraction overlooked |

## Retraction check

For each bibliography entry with a DOI, `cite-audit` can WebFetch CrossRef to check for a `update-to` field indicating retraction. If retracted:

- **Severity**: Critical
- **Action**: halt fix-offer flow; require user acknowledgment
- **Report wording**: "Cited paper {Author Year} has been retracted (retraction notice: {DOI}). Review whether the underlying claim is still supported by other sources."

CrossRef's retraction coverage is incomplete; absence of retraction status is **not proof of non-retraction**. The skill notes this caveat in its report.

## What theory-forge does NOT enforce

- Pre-registration is not required for *cited* studies — many valid prior studies pre-date the pre-registration movement
- Effect-size-presence is not a quality gatekeeper — qualitative and theoretical work may not have effect sizes
- Preprint citations are valid; the skill notes peer-review status without penalty

The skill's posture: **surface indicators, do not enforce them**. The author judges whether the strength of evidence matches the strength of the claim.

## CFLT-specific applicability

CFLT cites empirical work in two contexts:

1. **As adjacent evidence for §8.5 P1-P3 hypotheses** — these are Type E claims, the cited studies are supporting analogous findings. The audit should surface effect sizes when available but not enforce them.
2. **As foundational support for cognitive / LLM claims** — Seeley 2007 Salience Network; Liu 2023 Lost in the Middle. These are Type A or Type D citations and should have effect-size / sample-size context surfaced.

## See Also

- `falsifiability-template.md` — Type A vs Type E evidence requirements
- `cite-audit/SKILL.md` — citation verification pipeline including retraction check
- `research-methodology-taxonomy.md` — methodology-aware citation standards
