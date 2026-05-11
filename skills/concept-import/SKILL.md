---
name: concept-import
description: >
  Audit cross-disciplinary concept imports in academic theory documents. When a
  document borrows technical terms from another field (e.g., "Attention" from
  Transformer ML, "Salience Network" from neuroscience, "Information" from
  Shannon's IT), enforce three-tier marking — T1 technical equivalence /
  T2 partial equivalence with explicit limits / T3 metaphorical analogy.
  Catches concept smuggling (importing a term's authority without its content).
instructions: >
  Be charitable. Many imports are appropriately marked through citation alone.
  Only flag when the import is load-bearing in an inferential argument AND
  the marking is absent. Use the catalog in cross-disciplinary-import-rules.md
  to identify common imports; do not invent novel "imports" for ordinary words.
---

# concept-import — Cross-Disciplinary Concept Borrowing Audit

This skill catches **concept smuggling**: importing a term's technical authority from its source field into a new field without preserving its technical content. The failure mode is high-risk in interdisciplinary work (cognitive linguistics + LLMs + neuroscience + pedagogy — i.e., exactly the CFLT space).

## The Principle

A theory paper that says "*the model's attention concentrates at position 0*" is making a different claim depending on whether "attention" means:

1. **T1 — Technical**: the Vaswani et al. (2017) Transformer attention operation — a measurable quantity
2. **T2 — Partial**: borrowing the prioritization function but not the math
3. **T3 — Metaphor**: poetic-grade analogy with no technical content

The reader cannot tell which without explicit marking. Authority transfers without the technical content, which is a form of unearned credibility.

## Core Principles

1. **Imports are legitimate.** Cross-disciplinary borrowing is essential to integrative theories. The audit does not discourage it.
2. **Imports must be tiered.** Each borrowed term needs T1 / T2 / T3 marking, either explicitly in prose or unambiguously through citation context.
3. **Charitable reading.** A term used with a clear technical citation (`(Author 2017)` immediately following) is typically T1 or T2 — flag only when marking is genuinely absent and the import is load-bearing.
4. **Catalog-aware.** Use `shared/cross-disciplinary-import-rules.md` to identify common imports; do not flag ordinary English words.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/cross-disciplinary-import-rules.md` — three-tier framework + CFLT-relevant import catalog
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/argument-patterns.md` — load at Step 4 ONLY for the §2.10 concept-smuggling fallacy cross-link
- `../shared/output-templates/concept-import-audit.md` — load at Step 7 (Report Generation)

## Workflow

### Step 1: Build Candidate-Import Lexicon

Load the catalog in `cross-disciplinary-import-rules.md` "CFLT-relevant import catalog" table. This gives the **default lexicon** of cross-disciplinary terms to scan for. Examples:

```
Attention, Salience Network, Working Memory, Information,
Primacy effect, Figure / Ground, Schema, Cognitive Load,
Reanalysis, Lost in the Middle, Prosodic, Phonological,
Activation, Inhibition, Embedding, Recurrent, Recursive, ...
```

The user may extend this list at runtime via `--lexicon` flag or by adding entries to the catalog.

### Step 2: Scan for Candidate-Import Occurrences

Grep each in-scope file for each lexicon term (case-insensitive, word-boundary). For each occurrence, record:

- File path, line number
- The surrounding ≤100 words within the same section
- Whether the term appears with a citation in adjacent context (≤30 words)
- Whether the term appears with hedging language (scare quotes, "by analogy", "metaphorically")

### Step 3: Classify Each Occurrence by Import Tier

For each occurrence, apply detection rules from `cross-disciplinary-import-rules.md`:

- **T1**: same mathematical/operational definition as source-field; citation provides technical definition
- **T2**: explicit partial-equivalence statement ("we adopt X's notion of Y in respect of Z, but not W")
- **T3**: hedging language ("by analogy with", "metaphorically", "loosely after"), or scare quotes
- **No marking**: none of the above

### Step 4: Identify Load-Bearing Role

For each unmarked occurrence, check whether the term plays a **load-bearing inferential role**:

| Role | Heuristic |
|---|---|
| **Load-bearing** | The term appears in a Toulmin Warrant or in a Type A claim's evidence chain; removing the term collapses the argument |
| **Passing mention** | The term appears in a citation aside, a "see also", a footnote, or a parenthetical |
| **Definition / glossary** | The term is being defined in a definition section — already handled by `consistency` |

Only load-bearing unmarked occurrences trigger Major findings. Passing mentions are Minor.

### Step 5: Severity Grading

| Situation | Severity |
|---|---|
| Term imported with T1 marker + technical citation | OK |
| Term imported with explicit T2 marker | OK — log at Info as good practice |
| Term imported with T3 metaphor marker | OK |
| **Term imported with no marker AND load-bearing** | **Major** (concept smuggling risk) |
| Term imported with no marker AND passing mention | **Minor** |
| Term used inconsistently across the doc (T1 in §3, T3 in §7) | **Major** — forward to `consistency` |
| Term imported with marker but source-field definition is misrepresented | **Major** — verify with source |
| Source-field definition cannot be verified (citation orphan / fabricated) | Forward to `cite-audit` |

### Step 6: Cross-Doc Consistency

For each candidate-import term that appears multiple times across the corpus, check whether the import tier is **consistent**. The failure mode:

- §3.1 uses "Attention" as T1 (citing Vaswani 2017, quoting the operation)
- §7.2 uses "Attention" as T3 (metaphor for "what the speaker focuses on")
- The reader is confused about which is meant

This is equivocation — flag at **Major**, cross-reference `consistency`.

### Step 7: Report Generation

Write the report to `_research/concept-import-audit.md`.

**Report structure**: read the template at `../shared/output-templates/concept-import-audit.md` and fill in the `{placeholder}` values, including the Candidate-Import Lexicon Coverage table and the Cross-Doc Consistency Findings section. Loaded only at this step.

### Step 8: Offer Fix Drafts (opt-in)

Use AskUserQuestion:

> Found {N} unmarked load-bearing imports. Would you like me to:
> 1. Draft tier markers for each (you confirm each)
> 2. Just save the report and exit (default)

`--draft` mode: for each Major finding, propose one T1 / T2 / T3 marker template, applied only on per-finding confirmation.

## Anti-patterns

Do **not**:
- Flag ordinary English words just because they happen to be technical in some field (e.g., "model", "structure", "function")
- Treat citations in the bibliography section as imports
- Flag a term that's been defined in a Glossary entry — that defines the local usage
- Re-flag every occurrence of a term — once per unique meaning per file is enough
- Demand T1 marking for terms used in literature-restatement contexts (Type D — see falsifiability)
- Penalize Type C definitional usage — definitions are stipulative

## Exit Conditions

- **PASS**: 0 Major. All load-bearing cross-disciplinary terms are marked.
- **Review required**: ≥1 Major. Surface concept-smuggling risks.
