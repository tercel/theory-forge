# theory-forge Full Audit Report
**Project:** {project-name}
**Date:** {ISO date}
**Suite version:** {plugin version}
**Execution model:** parallel-wave (Wave 1: cite-audit + consistency + falsifiability + cross-lang; Wave 2: argument-structure + scope + concept-import + counter-argument)

## Aggregate Summary

| Audit | Critical | Major | Minor | Info | Status |
|---|---|---|---|---|---|
| cite-audit         | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| consistency        | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| falsifiability     | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| argument-structure | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| scope              | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| concept-import     | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| counter-argument   | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| cross-lang         | {n} | {n} | {n} | {n} | {PASS/REVIEW REQUIRED} |
| **Overall**        | {n} | {n} | {n} | {n} | {PASS / REVIEW REQUIRED / REVIEW REQUIRED — CRITICAL} |

## Sub-Reports

- [Citation Audit](./citation-audit.md)
- [Consistency Report](./consistency-report.md)
- [Falsifiability Audit](./falsifiability-audit.md)
- [Argument Structure Report](./argument-structure-report.md)
- [Scope Audit](./scope-audit.md)
- [Concept Import Audit](./concept-import-audit.md)
- [Counter-Argument Audit](./counter-argument-audit.md)
- [Cross-Linguistic Audit](./cross-lang-audit.md)

## Cross-Audit Patterns

{Surface findings that recur across multiple audits — e.g., a section that has both a cite-audit Major finding and a falsifiability Major finding is a higher-priority fix target than two issues in separate sections.}

## Recommended Fix Order

1. **Critical findings** (if any) — across all audits, listed first; halt before publication.
2. **Major findings**, grouped by section to enable coordinated edits.
3. **Minor findings**, deferred to a polish pass.

## Audit Execution Diagnostics

- Wave 1 wall-clock: {n} seconds
- Wave 2 wall-clock: {n} seconds
- Total wall-clock: {n} seconds (vs sequential estimate: ~{n}× longer)
- Failed audits (if any): {none / list with reason}
- WebFetch fallback statistics: {CrossRef hits / Semantic Scholar hits / OpenAlex hits / inconclusive}
