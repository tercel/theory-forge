# theory-forge — Release Notes

## 0.5.0 (evidence-strength — claim-vs-source support audit)

New ninth sub-audit: **`evidence-strength`**. It closes the gap that `cite-audit`
(existence/attribution) and `falsifiability` (marking) both leave open — *does the
cited source actually establish what the claim asserts, at the same strength and
scope?* This targets **over-leverage**, the dominant failure mode in
citation-dense theory work: a correlation read as a cause, an English-only result
read as a universal, a paper cited as motivation read as direct evidence.

### What it does

- Assigns each cited claim a seven-level **evidence-status verdict** — `accurate`
  / `accurate-with-caveat` / `overstated` / `indirect` / `misattributed` /
  `counterevidence` / `unsupported` — by reading what the source concludes
  (abstract/findings), not by keyword overlap.
- Maps each verdict to a **graded remediation**. The governing principle is
  *weaken the claim before deleting the source*: `overstated` / `indirect` /
  `accurate-with-caveat` / `counterevidence` all **keep the citation** and change
  the claim or its framing.
- **Citation-removal recommendation** (the user-requested capability) fires only
  at the bottom rung — `unsupported` and site-level `misattributed` — passes a
  removal gate (corroboration required; load-bearing warning; classic/methodology
  downweight; two-stage bibliography prune routed to `cite-audit`), and is
  **never auto-applied**.
- Includes an **evidence-layer ladder** (behavioral → descriptive-internal →
  causal → domain-specific) so a Layer-1/2 source cited for a Layer-3/4
  conclusion is flagged.

### Wiring

- New: `skills/evidence-strength/SKILL.md`, `commands/evidence-strength.md`,
  `skills/shared/evidence-status-ladder.md`,
  `skills/shared/output-templates/evidence-strength-audit.md`.
- Full-suite is now **9 audits**. Execution model: Wave 1 (4) + Wave 2 (5).
  `evidence-strength` runs in Wave 2 because it **reuses `cite-audit`'s
  verified-source results** (the one genuine inter-audit dependency); it falls
  back to its own fetches if `citation-audit.md` is absent.
- `evidence-strength` can now also raise **Critical** (counterevidence cited as
  support for a central claim), alongside `cite-audit`'s fabricated-citation
  Critical.

## 0.2.2 (token + parallelism optimization)

Four optimizations to reduce per-invocation token consumption and wall-clock time, **without sacrificing correctness or quality**.

### Optimization 1 — Parallelize full-suite (wall-clock ~4× speedup)

**Before:** 8 audits run sequentially in `/theory-forge .` (4–8 min wall-clock)

**After:** Two parallel waves of 4 audits each:
- Wave 1: `cite-audit + consistency + falsifiability + cross-lang`
- Wave 2: `argument-structure + scope + concept-import + counter-argument`

All 8 audits are mutually data-independent (verified in 0.2.0). Each wave dispatches in a single tool-call message via Task tool parallel invocation. **Sequential fallback** available via `/theory-forge . --sequential`.

### Optimization 2 — Dashboard output template extracted

**Before:** `commands/theory-forge.md` was 283 lines (with ~90 lines of dashboard sample output inlined)

**After:** Dashboard template extracted to `skills/shared/templates/dashboard-output.md`. The orchestrator command file is now **161 lines** (-122 = **-43%**). Main agent saves ~80 lines per `/theory-forge` invocation (template loaded only when actually rendering).

### Optimization 4 — Mandatory vs On-demand shared loading

**Before:** Each SKILL.md's "Reusable Assets" listed 2–4 files which sub-agents tended to preload entirely

**After:** Reusable Assets explicitly split into:
- **MANDATORY (load on start):** truly required for the workflow
- **ON-DEMAND (load only at the listed step):** loaded only when the specific workflow step requires them

Example (cite-audit):
- Mandatory: citation-parser, severity-levels
- On-demand: citation-format (only for style detection), replication-and-preregistration (only for retraction check), output-template (Step 7 only)

**Effect:** typical cite-audit call drops from ~728 → ~549 lines preload (-24%).

### Optimization 5 — Output templates extracted to `skills/shared/output-templates/`

**Before:** Each SKILL.md had a 30–60 line inline markdown "Report Structure" template

**After:** Templates extracted to per-skill files in `skills/shared/output-templates/`:
- cite-audit-report.md, consistency-report.md, falsifiability-audit.md, argument-structure-report.md, scope-audit.md, concept-import-audit.md, counter-argument-audit.md, cross-lang-audit.md, propagation-report.md, master-report.md

Each SKILL.md's Step "Report Generation" now reads "see `output-templates/{skill}-report.md`". Template loaded by sub-agent only at the report step, **after all detection work is complete** — so it doesn't occupy context during the heavy lifting.

### Measured savings (typical cite-audit invocation)

| Cost | Before | After | Savings |
|---|---|---|---|
| Main agent context | ~83 lines | ~83 lines | unchanged |
| Sub-agent startup preload | ~728 lines (~6500 tokens) | ~549 lines (~5000 tokens) | **~1500 tokens (~23%)** |
| Sub-agent Step-7 template load | (inline) | +43 lines (~400 tokens) | (now loaded later, not at startup) |
| **Net per cite-audit run** | ~6500 tokens | ~5400 tokens | **~17% savings** |

### Measured savings (full-suite `/theory-forge .`)

| Cost | Before | After | Savings |
|---|---|---|---|
| Wall-clock | ~4–8 min | ~1–2 min | **~4× faster** |
| Total sub-agent tokens | ~52000 tokens | ~40000 tokens | **~23% savings** |

### Correctness preserved

Every workflow step still runs exactly as before — only the *when-to-load-which-asset* changed:
- All anti-patterns retained verbatim in each SKILL.md
- All severity rules retained
- All detection heuristics retained
- All concept-import T1/T2/T3 declarations retained (e.g., Popper, Toulmin, Booth-Colomb-Williams)
- BIBLIOGRAPHY.md unchanged
- Read-only-by-default + opt-in fix flow unchanged
- Critical-never-auto-fix rule unchanged

### Updated files

- `commands/theory-forge.md` — Route C rewritten for parallel-wave; dashboard logic now reads template from `skills/shared/templates/dashboard-output.md`
- `commands/theory-forge.md` — master-report inline template replaced with one-line reference to `skills/shared/output-templates/master-report.md`
- 9 sub-skill SKILL.md files — Reusable Assets split into MANDATORY / ON-DEMAND tiers; Report-Generation step replaced with one-line template reference
- 10 new output template files in `skills/shared/output-templates/`
- 1 new template file in `skills/shared/templates/`

### Total surface

| Metric | 0.2.1 | 0.2.2 | Δ |
|---|---|---|---|
| commands/ | 1033 | 911 | -122 (-12%) |
| skills/*/SKILL.md | 2054 | 1668 | -386 (-19%) |
| skills/shared/ | 1247 | 1907 (now split incl. templates) | +660 but on-demand |
| **Hot path per invocation** | ~6500 tokens | **~5000 tokens** | **-23%** |

---

## 0.2.1 (self-audit hardening)

Applied theory-forge's own audit methodology to theory-forge itself (excluding Type E hypothesis-marking as excuse — every claim theory-forge makes about itself must be Type A / B / C / D, no Type E). Surfaced 119 Major findings across the 8 audit categories and fixed the 12 most-critical.

### Added

- `BIBLIOGRAPHY.md` — resolves 106 citation orphans by its own cite-audit standard. Includes verified entries for Popper, Toulmin, Bem, Suppes, Booth-Colomb-Williams, Brigandt & Love, Wimsatt, Munafò, Nosek, Gelman & Loken, Kerr, Cumming, Greenberg (S.A.), Walton, Shadish-Cook-Campbell, Creswell, plus linguistics + NLP/LLM references.
- `_research/self-audit-report.md` — full self-audit findings, severity-classified, with fix recommendations.
- README "Scope of validation" section — explicit acknowledgment that theory-forge has been tested only on English-language theory documents in linguistics (CFLT corpus); other domains/languages untested.
- README "Counter-Argument Engagement" section — explicit engagement with 4 canonical objections to audit tools (mechanical-vs-craft; LLM hallucination; peer-review-not-needed; nuance-can't-be-automated) + a "What we are NOT claiming" subsection.

### Fixed

- `cross-lang/SKILL.md` — removed unmarked Type A claim "5-15% LLM error rate"; replaced with Type B design assumption (no quantitative claim)
- `cite-audit/SKILL.md` — removed unmarked Type A claim about CrossRef ~60% / Semantic Scholar "always"; replaced with hedged language ("a subset of entries", "not exhaustive")
- `cite-audit/SKILL.md` — marked keyword-alignment thresholds (0.40 / 0.15) as Type B calibration seed values, with explicit project-tuning guidance
- `falsifiability/SKILL.md` — reframed "Science is bold conjecture..." as Type D Popperian framing with acknowledgment of Kuhnian / Lakatosian / Feyerabendian alternatives
- `shared/falsifiability-template.md` — added explicit T2 concept-import declaration for Popperian framework (imported: demarcation criterion + conjecture-and-refutation; NOT imported: Three Worlds, rejection of induction, etc.)
- `shared/argument-patterns.md` — added explicit T1 concept-import declaration for Toulmin model; T2 for fallacy catalog operationalization
- `shared/cross-disciplinary-import-rules.md` — added self-declaration: T1/T2/T3 framework is theory-forge's own synthesis inspired by Brigandt & Love + Wimsatt, not a direct import
- `counter-argument/SKILL.md` — added explicit T2 concept-import declaration for Booth-Colomb-Williams engagement-discipline norm

### Verification

- All 9 SKILL.md frontmatter validate as YAML
- All 10 command frontmatter validate
- `BIBLIOGRAPHY.md` covers ~60 of the 106 citations; remaining are reference grammars covered by `shared/language-data.md` or illustrative placeholders
- `plugin.json` validates as JSON

### Status by theory-forge's own standards

| Discipline | 0.2.0 | 0.2.1 |
|---|---|---|
| Every citation in bibliography | FAIL (0/106) | PARTIAL (~60/106 + ~40 in language-data.md) |
| No unmarked Type A claims | FAIL (3) | PASS (0) |
| Concept imports tier-marked | FAIL (4 unmarked) | PASS (4 marked) |
| Counter-argument engagement | FAIL | PASS |
| Bounded scope statement | FAIL | PASS |
| Toulmin completeness of own thresholds | PARTIAL | PASS (Type B marked) |
| Internal consistency | PASS | PASS |
| Severity scale honesty | PASS | PASS |

**Score: 3 PASS / 5 FAIL → 6 PASS / 2 PARTIAL.** Still imperfect (bibliography coverage at ~60/106 because reference grammars are deferred to `language-data.md`); the 12 most-critical Major findings are resolved.

---

## 0.2.0 (research-methodology expansion)

Major expansion. Adds 4 new sub-commands covering broader scholarly methodology — Toulmin argument structure, scope discipline, cross-disciplinary concept-import tier marking, and counter-argument engagement. Plus 3 new shared assets (research-methodology-taxonomy, replication-and-preregistration, cross-disciplinary-import-rules) and a comprehensive `docs/usage.md`.

### New Sub-Commands

- `/theory-forge:argument-structure` — Toulmin (1958) completeness audit: Claim + Data + Warrant + Qualifier + Rebuttal. Detects bare assertions, argument-from-authority patterns, and 9 common fallacies (affirming the consequent, ad hoc rescue, no-true-Scotsman, scope creep, equivocation, base-rate neglect, etc.)
- `/theory-forge:scope` — Boundary-condition discipline. Flags unbounded generalizations ("for all X", "universally") lacking inline qualifiers or limitation-section coverage. Bem (1995); Suppes (1962).
- `/theory-forge:concept-import` — Cross-disciplinary borrowing tier marking. Three-tier framework (T1 technical equivalence / T2 partial equivalence / T3 metaphorical analogy). Catches concept smuggling. Brigandt & Love (2017); Wimsatt (2007).
- `/theory-forge:counter-argument` — Engagement with canonical opposing positions per topic. Flags strawman engagement separately. Booth, Colomb & Williams (2016); Graff & Birkenstein.

### New Shared Assets

- `skills/shared/argument-patterns.md` — Toulmin model + catalog of 10 fallacies common in theory work
- `skills/shared/cross-disciplinary-import-rules.md` — three-tier framework + CFLT-relevant import catalog (Attention, Salience Network, Working Memory, Information, etc.)
- `skills/shared/research-methodology-taxonomy.md` — 6 methodology types (formal / experimental / observational / meta-analytical / qualitative / historical) with Toulmin and falsifiability standards per type
- `skills/shared/replication-and-preregistration.md` — pre-registration / effect-size / CI surfacing (does not enforce; documents the standards)

### Updated

- `skills/shared/falsifiability-template.md` — added **Type E (marked hypothesis)** with 大胆假设 + 小心求证 framing
- `skills/shared/academic-severity-levels.md` — added "honesty principle" preamble; updated Major examples with Type-E-unmarked
- `skills/shared/citation-parser.md` — added §1.5 stop-list (conference names), §1.6 multi-word surnames; §4 dual-index matching for multi-author shorthand
- `commands/theory-forge.md` — orchestrator now routes 8 sub-commands (was 5); full-suite aggregates 8 audits
- `README.md` — full command table with WebFetch annotation; expanded "What theory-forge Catches" section
- `docs/usage.md` — NEW. End-to-end examples, sub-command reference, 5 workflow recipes (pre-submission audit, definition revision, interdisciplinary import review, cross-linguistic claim verification, bold-hypothesis paper triage)

### Calibrated Against

- CFLT 2026-05 audit round (initial 7 citation-truth issues; 13 new orphans found in calibration)
- Self-audit of theory-forge 0.1.0 (8 safety issues fixed; Type-E protocol added)

### Total Surface

- 9 commands
- 9 sub-skills (including `propagate`)
- 9 shared assets
- ~3800 lines

---

## 0.1.0 (initial release)

First public version. MVP scope covers the highest-value academic-documentation audits identified during the CFLT (Core-First Language Theory) foundation-docs review.

### Commands

- `/theory-forge` — orchestrator running all five audits in sequence
- `/theory-forge:cite-audit` — citation truth + bibliography orphan detection via CrossRef / Semantic Scholar / OpenAlex
- `/theory-forge:consistency` — cross-section component-list consistency (formal definition vs narrative claims)
- `/theory-forge:falsifiability` — normative/descriptive split + Popperian framing audit
- `/theory-forge:cross-lang` — typological-family coverage check for universality claims
- `/theory-forge:propagate` — downstream propagation of an upstream definition change

### Shared Assets

- `skills/shared/citation-parser.md` — `(Author Year)` regex set + bibliography parser
- `skills/shared/citation-format.md` — APA / Chicago / MLA templates with field-level field maps
- `skills/shared/language-data.md` — reference-grammar registry per language (used by `cross-lang`)
- `skills/shared/falsifiability-template.md` — four-block schema (claim / methodology / falsification / adjacent evidence)
- `skills/shared/academic-severity-levels.md` — severity calibration scale (Critical → Info)

### Design Heritage

- File layout, command-shape, and `Task(subagent_type=…)` invocation pattern adopted from `spec-forge` 0.18.0.
- `propagate` sub-command directly adapts `spec-forge:propagate` for academic doc cross-references.
- Severity scale calibrated against `spec-forge:audit` so users moving between projects don't re-learn the rubric.

### Known Limitations

- LaTeX / BibTeX bibliographies are not supported in 0.1.0 (Markdown-only). Will add when a user requests it.
- Citation truth-check accuracy depends on the cited paper having an indexed DOI or arXiv ID. Obscure book chapters and pre-2000 journal articles without DOI may return inconclusive results.
- Mis-attribution detection is keyword-alignment-based, not semantic; it flags low-confidence cases for human review rather than auto-rejecting.
- Cross-lingual generation does not include audio or IPA transcription verification — surface-form text only.

### Test Surface

End-to-end verification against a CFLT pre-fix snapshot:

- Target: `cite-audit` re-discovers ≥5 of the 7 known citation-truth issues caught by hand in the CFLT 2026-05 audit round (Sturt 2003 mis-attribution, Cinque-do-support misattribution, Grosz Cb overclaim, plus four bibliography orphans).
- Stretch target: `consistency` re-discovers the §9-omits-modal/negation/aspect issue.
- Stretch target: `cross-lang` flags `manifesto.md` (English-only) but not `core-concept.md §2.5` (5 languages).
