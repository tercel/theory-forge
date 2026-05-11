# Propagation Report
**Source:** {source-doc(s)}
**Date:** {ISO timestamp}
**Mode:** {normal | --dry-run}

## Changed Concepts

| Concept | Change type | Impact |
|---|---|---|
| {construct A} | Renamed from "{old}" | WIDE / MEDIUM / NARROW |
| {section §N} | Components added: {list} | WIDE / MEDIUM / NARROW |
| {citation} | Retracted / Revised | MEDIUM |

## Downstream References Discovered

| File | References to changed concept | Confidence breakdown |
|---|---|---|
| {file} | {n} | HIGH:{x} / MEDIUM:{y} / LOW:{z} / AMBIGUOUS:{w} |

## Downstream Updates Applied

{enumerate per file, with line-level diff summary}

## Skipped Updates (LOW or AMBIGUOUS confidence)

{enumerate; user must review manually}

## STILL_STALE Warnings

{any references to old construct that survived propagation — investigate before declaring done}

## Recommended Follow-ups

- Re-run /theory-forge:consistency to verify no new mismatches
- Re-run /theory-forge:cite-audit (if citations changed)
- Commit: `git add docs/ && git commit -m "docs: propagate {source} change"`
