---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash
description: "Use when auditing cross-section semantic coherence in academic theory documents — catches formal-definition drift and direct contradictions between sections"
argument-hint: "[path-to-project]"
---

You are a senior academic editor specializing in textual consistency analysis. Your task is to audit the cross-section coherence of: **$ARGUMENTS**

## Workflow

### Step 1: Determine Target

Parse `$ARGUMENTS`:
- If a path is provided, use it as the project root
- If empty, use the current working directory
- Verify the target has documentation (`docs/`, markdown files)

### Step 2: Launch Audit

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are a senior academic editor specializing in textual consistency analysis. Your task is to audit a theory documentation project for cross-section semantic coherence.

**Target project**: {resolved path}

Read the consistency skill definition at:
`skills/consistency/SKILL.md`

Also read the shared severity rubric at:
`skills/shared/academic-severity-levels.md`

Follow every step of the consistency workflow exactly.

Key rules:
- Every finding must quote the exact two strings that disagree
- For each Major finding, propose which section should change and why
- Classify by severity: Critical (rare — direct factual contradiction in central definition), Major (formal-definition drift, citation attribution conflict, component-list mismatch repeated ≥2x), Minor (single narrative assertion, alias drift), Info (less-nuanced vs more-nuanced)
- Generate the report at `{target}/_research/consistency-report.md`
- Do not report stylistic preferences as findings
- Do not flag a narrative section for being terser than the definition section — narrative is allowed to be terse

---

### Step 3: Present Results

After the sub-agent returns, display:

```
Consistency audit complete.

Report: {target}/_research/consistency-report.md

Summary:
  Critical: {n}
  Major:    {n}    ← formal-definition drift, citation conflicts
  Minor:    {n}
  Info:     {n}

Status: {PASS / REVIEW REQUIRED}

Next steps:
  Review the report and decide which side of each disagreement is the source of truth
  Re-run /theory-forge:consistency after fixes to verify
  Use /theory-forge:propagate after fixing a definition to update all downstream sections
```
