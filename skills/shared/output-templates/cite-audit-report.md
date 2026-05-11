# Citation Audit Report
**Project:** {project-name}
**Date:** {ISO date}
**Bibliography:** {bibliography-path}
**Sources checked:** CrossRef, Semantic Scholar, OpenAlex

## Summary

| Severity | Count |
|---|---|
| Critical | {n} |
| Major | {n} |
| Minor | {n} |
| Info | {n} |

**Status:** {PASS / REVIEW REQUIRED}

## Critical Findings — Fabricated Citations

{enumerate, each with file:line, claim text, search attempt log (which sources tried, what they returned), suggested action}

## Major Findings — Mis-attribution

{enumerate, each with: citation key, file:line, claim sentence, paper topic vs claim topic mismatch summary, suggested replacement citation if known}

## Major Findings — Bibliography Orphans

{enumerate, including a suggested bibliography entry built from CrossRef metadata where possible}

## Minor Findings

{group by category: unused / ambiguous-shorthand / malformed}

## Info Findings — Unverifiable

{enumerate, indicate why verification was inconclusive (e.g., pre-1980 chapter; book entry without DOI)}

## Statistics

- Citations verified: {n}/{total}
- Verification sources used: CrossRef {n}, Semantic Scholar {n}, OpenAlex {n}, inconclusive {n}
- Most-cited paper: {citation} ({n} occurrences)
- Citations per 1000 words: {ratio}
