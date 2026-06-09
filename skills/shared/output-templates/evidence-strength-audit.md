# Evidence-Strength Audit Report
**Project:** {project-name}
**Date:** {ISO date}
**Scope:** {n} cited claims across {f} files
**Source verification:** reused from citation-audit.md / fetched via CrossRef · Semantic Scholar · OpenAlex

## Summary

| Severity | Count |
|---|---|
| Critical | {n} |
| Major | {n} |
| Minor | {n} |
| Info | {n} |

**Status:** {PASS / REVIEW REQUIRED}

## Verdict Distribution

| Verdict | Count | Meaning |
|---|---|---|
| accurate | {n} | within source scope and strength |
| accurate-with-caveat | {n} | right but needs a scope qualifier |
| overstated | {n} | claim stronger than the source establishes |
| indirect | {n} | source is motivation/analogy read as direct evidence |
| misattributed | {n} | source does not provide the attributed method/result |
| counterevidence | {n} | source constrains/contradicts but cited as support |
| unsupported | {n} | no source supports the claim |
| strength-uncheckable | {n} | source content not retrievable (Info) |

## Critical Findings — Counterevidence Cited as Support

{enumerate, each with file:line, claim sentence, "source establishes" vs "claim asserts" one-liners, and the reframe-as-constraint remediation}

## Major Findings — Overstated

{enumerate: citation key, file:line, claim, the dimension inflated (universality / causality / effect-size / mechanism / cross-domain), evidence-layer jump if any, and the claim-downgrade remediation. Cross-link cross-lang / scope / argument-structure where relevant. Citation is KEPT.}

## Major Findings — Misattributed

{enumerate: source establishes vs claim asserts; repair-or-move-attribution remediation; flag site-level removals (handled in Removal Candidates)}

## Major Findings — Indirect (motivation read as evidence)

{enumerate: the reword-as-"motivated by / by analogy with" remediation; citation KEPT as motivation}

## Major Findings — Unsupported

{enumerate: bare quantitative/mechanistic claims with no support; remediation = bind to an own-experiment / mark Type E (route to falsifiability) OR remove. Removal candidates listed below.}

## Minor Findings — Accurate-with-Caveat

{enumerate: the missing scope qualifier; suggested inline qualifier}

## Removal Candidates (propose-only — never auto-applied)

> Each entry passed the removal gate: the source was actually checked (or
> confirmed not to exist) and provides no support for the claim at this site.
> Removal is shown as a diff and applied only on explicit per-item confirmation.
> Removing an inline citation does not prune its bibliography entry — that is a
> separate suggestion routed to cite-audit's unused-entry check.

| # | Citation | File:line | Verdict | Load-bearing? | Bib entry orphaned by removal? |
|---|---|---|---|---|---|
| {i} | {key} | {file}:{line} | unsupported / misattributed-here | {yes → claim-downgrade alternative shown / no} | {yes → route to cite-audit unused / no} |

{for each load-bearing entry, inline the warning: "removing this leaves {claim} unsupported — downgrade the claim or drop it"}

## Info — Strength-Uncheckable

{enumerate: source content not retrievable; recommend manual check. Never escalated.}

## Statistics

- Claims within source strength (accurate / accurate-with-caveat): {n}/{total}
- Over-leverage rate (overstated + indirect): {pct}
- Misattributed + counterevidence + unsupported: {n}
- Removal candidates (gate-passed): {n}
- Sources reused from citation-audit.md: {n}; freshly fetched: {n}; uncheckable: {n}
