# Concept Import Audit Report
**Project:** {project-name}
**Date:** {ISO date}

## Summary

| Severity | Count |
|---|---|
| Critical | 0 |
| Major | {n}   ← unmarked load-bearing imports + cross-doc inconsistency |
| Minor | {n}   ← unmarked passing mentions |
| Info  | {n}   ← properly-marked T2 imports (best practice) |

## Candidate-Import Lexicon Coverage

| Term | Occurrences | Marked T1 | Marked T2 | Marked T3 | Unmarked |
|---|---|---|---|---|---|
| Attention | {n} | {n} | {n} | {n} | {n} |
| Salience Network | {n} | {n} | {n} | {n} | {n} |
| Working Memory | {n} | {n} | {n} | {n} | {n} |
| ... | | | | | |

**Status:** {PASS / REVIEW REQUIRED}

## Major Findings — Unmarked Load-Bearing Imports

### CI001 — {file}:{line}
**Term:** {imported term}

**Context:**
> "{surrounding sentence}"

**Tier classification:** None detected (no source-field citation, no hedging, no partial-equivalence statement).

**Role:** Load-bearing (the inference depends on the technical meaning).

**Recommended fix:** Mark the import explicitly:
- T1: "{example with technical citation}"
- T2: "{example with partial-equivalence statement}"
- T3: "{example with metaphorical framing}"

## Cross-Doc Consistency Findings

### CI00X — Term "{term}" used inconsistently

- {file:line} uses T1 ({citation} + technical content)
- {file:line} uses T3 (metaphor)
- The reader cannot tell which meaning is intended in cross-references

**Recommended fix:** Either use the same tier consistently, or explicitly disambiguate.

## Info Findings — Best-Practice Markings

{enumerate positive practices — properly-marked T2 imports with explicit partial-equivalence statements}
