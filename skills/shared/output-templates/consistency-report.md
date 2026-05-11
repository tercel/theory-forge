# Cross-Section Consistency Report
**Project:** {project-name}
**Date:** {ISO date}

## Summary

| Severity | Count |
|---|---|
| Critical | 0 |
| Major | {n} |
| Minor | {n} |
| Info | {n} |

**Status:** {PASS / REVIEW REQUIRED}

## Granularity Tier Inventory

| Construct | T1 (gloss) | T2 (intro) | T3 (formal) | Canonical T3 |
|---|---|---|---|---|
| {Construct A} | {file:line} | {file:line} | {file:line} | {file:line} |
| ... | | | | |

## Major Findings — Component-List Mismatches

### F001 — Construct: {construct name}
**Canonical T3 definition**: {file:line}
- Lists: {components in T3}
- Missing from T3 but asserted in narrative: {components}

**Narrative assertions** of missing components:
- {file:line}: "{quoted assertion}"
- {file:line}: "{quoted assertion}"

**Recommended fix**: {one-paragraph guidance — update T3, or move missing components out if they don't belong}

### F002 — ...

## Major Findings — Direct Contradictions

{enumerate, with both contradicting quotations}

## Hypothesis-vs-Refinement Pairs (Info — positive practice)

{when §3 is Type E hypothesis and §7 is refined Type A — log as good practice, NOT as contradiction}

## Minor Findings

{group by category: single-narrative assertion / alias drift / suspected equivocation}

## Info Findings

{summary}
