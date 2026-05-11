# Scope Audit Report
**Project:** {project-name}
**Date:** {ISO date}

## Summary

| Severity | Count |
|---|---|
| Critical | 0 |
| Major | {n}    ← truly unbounded claims |
| Minor | {n}    ← bounded by remote limitation section, recommend inline qualifier |
| Info  | {n}    ← positive practice + scope subsections found |

## Major Findings — Unbounded Generalizations

### S001 — {file}:{line}
**Claim** (extracted):
> "{claim text}"

**Generalizing language:** {the universal-shape pattern that triggered detection — e.g., "All languages", "universally"}

**Qualifiers found nearby:** {none / list}

**Limitation subsection coverage:** {file:line of any limitation-section coverage, or "none found"}

**Recommended fix:** {add an inline qualifier such as ___ ; or extend the limitation subsection to mention the construct}

## Document-Level Findings (Info)

- {✓/✗} `{file}` has §X "Limitations" / "Honest Limitations" subsection
- {✓/✗} `{file}` has "What X Does NOT Claim" subsection
- {✓/✗} `{file}` introduction states the scope

## Minor / Info findings

{group}
