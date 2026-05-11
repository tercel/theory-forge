# Cross-Linguistic Coverage Audit
**Project:** {project-name}
**Date:** {ISO date}
**Scope:** {file count}
**Mode:** {audit-only | --generate (scaffolds) | --generate-surface (concrete drafts)}

## Summary

| Severity | Count |
|---|---|
| Critical | 0 |
| Major | {n} |
| Minor | {n} |
| Info | {n} |

| Universality claims found | {n} |
| Adequately covered (≥3 families) | {n} |
| Inadequately covered | {n} |
| Marked Type E (hypothesis) with scope statement | {n} |

**Status:** {PASS / REVIEW REQUIRED}

## Major Findings — Universality Claim with Insufficient Coverage

### F001 — {file}:{line}

**Claim:** "{claim text}"

**Framing classification:** {Type A — verified universality / Type E — marked-hypothesis universality}

**Example block** ({line range}):
- {Language} ({family}): {✓ / ✗}
- ...

**Distinct families:** {n}

**Severity:** {Major / Minor — based on Type A vs Type E rule in cross-lang/SKILL.md}

**Recommended fix:** Add worked examples in at least {N} additional families. Suggested:
- {Family} ({Language}) — registry: {reference grammar citation}
- {Family} ({Language}) — registry: {reference grammar citation}
- {Family} ({Language}) — registry: {reference grammar citation}

{If --generate or --generate-surface: scaffolds or draft examples follow per per-language entries}

## Minor Findings — Indo-European-Only Coverage

{enumerate Type A claims with ≥3 examples but all from Indo-European}

## Info Findings

{positive practice: marked-Type-E with explicit per-family scope statement; ≥5 families covered; etc.}
