---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash
description: "Use when auditing academic theory documents for cross-linguistic universality-claim coverage — flags universality claims with only English or Indo-European examples; --generate proposes safe scaffolds for missing languages; --generate-surface --i-have-native-speaker-review produces concrete (unverified, must-review) example sentences"
argument-hint: "[path-to-project] [--generate | --generate-surface --i-have-native-speaker-review]"
---

You are a senior linguistics editor specializing in cross-linguistic typology. Your task is to audit cross-linguistic example coverage in: **$ARGUMENTS**

## Workflow

### Step 1: Determine Target and Mode

Parse `$ARGUMENTS`:
- Default: audit-only (no generation)
- If `--generate` is present, enable **scaffold generation** mode — produces fillable templates with reference-grammar pointers, NOT concrete example sentences
- If `--generate-surface --i-have-native-speaker-review` is present (both flags required together), enable **concrete-surface generation** mode — produces example sentences marked as machine-drafted requiring native-speaker review
- If `--generate-surface` is present without the acknowledgment flag, refuse and explain why; do not fall back to default
- If a path is provided, use it as the project root
- If empty, use the current working directory
- Verify the target has documentation

### Step 2: Launch Audit

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are a senior linguistics editor specializing in cross-linguistic typology. Your task is to audit cross-linguistic example coverage in an academic theory documentation project.

**Target project**: {resolved path}
**Mode**: {audit-only | with --generate}

Read the cross-lang skill definition at:
`skills/cross-lang/SKILL.md`

Also read:
- `skills/shared/language-data.md` — family-language registry with reference grammars and romanization standards
- `skills/shared/academic-severity-levels.md` — severity rubric

Follow every step of the cross-lang workflow exactly.

Key rules:
- A universality claim with <3 typologically distinct families is a Major finding
- Indo-European-only coverage (regardless of count) is Minor — recommend typological diversity
- Generate examples (--generate) only in languages with registered reference grammars
- Always mark generated examples as "[Draft — verify with native speaker]"
- Use the romanization standards in language-data.md
- Generate the report at `{target}/_research/cross-lang-audit.md`

---

### Step 3: Present Results

```
Cross-linguistic coverage audit complete.

Report: {target}/_research/cross-lang-audit.md

Summary:
  Critical: {n}
  Major:    {n}    ← universality claims with <3 typological families
  Minor:    {n}
  Info:     {n}

Coverage:
  Universality claims found:        {n}
  Adequately covered (≥3 families): {n}
  Inadequately covered:             {n}

Status: {PASS / REVIEW REQUIRED}

Next steps:
  Review the report and decide which families to add examples for
  Re-run with --generate to have me propose draft examples (must be native-speaker reviewed)
  Use /theory-forge for the full audit suite
```
