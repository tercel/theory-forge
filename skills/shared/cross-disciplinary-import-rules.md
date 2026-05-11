# Cross-Disciplinary Concept Import Rules

Reference for the `concept-import` sub-skill. Codifies how concepts borrowed from one discipline into another should be marked, what failure modes look like, and which CFLT-relevant terms are commonly imported.

## Self-declaration: this framework is theory-forge's own synthesis

The T1/T2/T3 three-tier framework presented below is **theory-forge's own synthesis**, not a direct import from a single source. It is **inspired by** philosophy-of-science work on conceptual change across disciplines (Brigandt & Love 2017 "Conceptual change in biology") and Wimsatt (2007 *Re-Engineering Philosophy for Limited Beings*) on heuristics for cross-disciplinary integration — but neither source explicitly proposes a three-tier marking scheme. theory-forge has **operationalized** their general arguments into a specific marking discipline. *[Type B — theory-forge design choice]*.

## The three import tiers

When a theory borrows a concept (e.g., "Attention" from Transformer architecture, "Salience Network" from neuroscience, "Information" from Shannon's information theory) into a new domain (e.g., second-language pedagogy), the import is one of three types — and the type **must be explicit in the prose** (Brigandt & Love 2017; Wimsatt 2007 *Re-Engineering Philosophy for Limited Beings*).

| Tier | What it means | Required marking | Failure mode |
|---|---|---|---|
| **T1 — Technical equivalence** | The borrowed concept retains its **original technical content**; the same operational definition, the same predicted behavior, the same measurement methodology | None required — the technical content is the marker | Borrowing a term without preserving its technicity (concept smuggling) |
| **T2 — Partial equivalence with explicit limits** | The borrowed concept retains *some* technical content; specific features apply, others do not — and the prose says which | Explicit statement: *"X-from-field-F applies in respect of Y; we do **not** import Z"* | Borrowing without specifying which features are retained |
| **T3 — Metaphorical analogy** | The borrowed concept is used as an inspirational analogy only; no technical content is preserved | Explicit metaphorical framing: *"by analogy with...", "metaphorically...", "loosely after..."* | Using a technical-sounding term metaphorically without marking |

## Why the three-tier discipline matters

The failure mode this prevents is called **concept smuggling** or **ad hoc cross-disciplinary import**: a term carries scientific authority from its source field, and importing it into a new field without preserving its technical content **transfers unearned authority**. The reader sees "Attention" and thinks of the precise mathematical operation in Transformers; the author has actually meant "what the speaker is focused on" — informal English. This is a form of jargon laundering.

theory-forge does not prevent borrowing. It enforces that borrowing is **labeled**.

## CFLT-relevant import catalog

The following table catalogs cross-disciplinary terms commonly used in CFLT-style work (linguistics + cognitive science + LLMs + pedagogy). For each, the source field, the typical CFLT use, and the expected import tier.

| Term | Source field | Source definition | Typical CFLT use | Expected tier |
|---|---|---|---|---|
| **Attention** | Transformer ML (Vaswani 2017) | A mathematical operation: weighted sum of values keyed by query-key dot product | "What the learner attends to" / "model's attention to position 0" | **T2** when discussing LLM attention sinks (technical); **T3** when discussing learner attention (metaphorical from cognitive psych side) |
| **Salience Network** | Neuroscience (Seeley et al. 2007) | Specific brain network: anterior insula + ACC + frontal operculum involved in salience detection | "Salience anchor" / "salience-driven prioritization in language" | **T2** — borrows the prioritization function but not the specific brain regions |
| **Working Memory** | Cognitive psychology (Baddeley 2000) | A limited-capacity system with phonological loop / visuospatial sketchpad / central executive / episodic buffer | "Cognitive load" / "working-memory limits in L2 production" | **T1** when measured; **T2** when invoked theoretically |
| **Information** | Shannon (1948) | Entropy / mutual information / KL divergence — strictly quantitative | "Information density" / "high-surprisal tokens" | **T1** when actually quantified; **T3** when used as "what the speaker conveys" |
| **Primacy effect** | Memory research (Murdock 1962) | Serial position effect: better recall of early list items | "Position 0 advantage" / "early-prefix priority" | **T2** — borrows the recall finding into linguistic prediction |
| **Figure / Ground** | Gestalt psychology (Rubin 1921) | Perceptual segregation of foreground from background | "Salience anchor" / "Core vs ground frame" | **T2** with Talmy's (2000) cognitive-linguistic adaptation as the bridge |
| **Schema** | Cognitive psychology (Bartlett 1932; Anderson 1982) | Mental structure organizing knowledge | "Cognitive schema for unmarked default" | **T2** in skill-acquisition contexts |
| **Cognitive Load** | Educational psychology (Sweller 1988) | Intrinsic + extrinsic + germane load on working memory | "Reduced restructuring cost" / "lower PFC activation" | **T1** when measuring PFC; **T2** when used as a theoretical metric |
| **Reanalysis** | Psycholinguistics (Frazier 1979) | Reparsing cost when initial parse fails | "Reanalysis cost in L1→L2 production" | **T2** |
| **Lost in the Middle** | LLM research (Liu et al. 2023) | Empirical finding about long-context positional accuracy | "LLM attention bias supports Core-First" | **T2** with explicit caveat — finding is for retrieval, claim is about prompt design |
| **Prosodic / Phonological** | Phonetics-phonology | Specific stress / intonation phenomena | "Prosodic transfer" / "phonological loop" | **T1** when describing actual phonology; **T2** elsewhere |

This catalog is not exhaustive. The skill detects new candidate imports via heuristics (Step 2 of `concept-import/SKILL.md`).

## Detection of import tier from prose

| Tier | Detection signals |
|---|---|
| **T1 — Technical equivalence** | The same mathematical formula / operational definition appears as in the source field; cited paper provides the technical definition |
| **T2 — Partial equivalence** | Statements of form: *"we adopt X's notion of Y in respect of Z, but not W"*; *"after Author (Year), modulo X"*; explicit specifying of preserved features |
| **T3 — Metaphorical analogy** | Hedging language: *"by analogy with", "metaphorically", "loosely after", "this is reminiscent of"*; or scare-quotes around the term |
| **No marking — likely concept smuggling** | None of the above; the term appears as if it had a stable technical meaning the reader will recognize, but the author's use is informal or different from the source-field definition |

## Severity rules

| Situation | Severity |
|---|---|
| Term imported with explicit T1 marker + citation | OK |
| Term imported with explicit T2 marker (partial-equivalence statement) | OK — best practice; log at Info |
| Term imported with explicit T3 marker (metaphor) | OK |
| **Term imported with no marker AND used in a load-bearing inferential role** (the argument depends on the import) | **Major** — concept smuggling risk |
| Term imported with no marker, used in a non-load-bearing context (passing mention) | **Minor** |
| Term used inconsistently across the document (T1 in §3, T3 in §7) | **Major** — equivocation; cross-reference `consistency` skill |
| Term imported with marker but the source-field definition is misrepresented | **Major** — verify the source's actual definition |

## Drop-in templates for marking imports

**T1 example:**

> "We use *attention* in the technical Transformer sense (Vaswani et al. 2017) — the weighted sum of value vectors keyed by query-key dot product."

**T2 example:**

> "We borrow Salience Network (Seeley et al. 2007) **only** for its prioritization function — we do not claim CFLT activates the same anterior-insula / ACC nodes; the network name is used to denote the cognitive role, not the neural substrate."

**T3 example:**

> "*[Metaphorical analogy]:* The learner's mental schema for the four-slot protocol is *reminiscent of* a server template — slots are filled in canonical order. This is a heuristic illustration, not a computational claim."

## See Also

- `argument-patterns.md` §2.10 — concept smuggling fallacy
- `falsifiability-template.md` — Type A/B/C/D/E (concept-import findings are typically Type A or B)
- `consistency/SKILL.md` — terminology drift / equivocation cross-checks
