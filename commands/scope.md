---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash
description: "Use when auditing academic theory documents for explicit scope statements — flags unbounded generalizations ('for all X', 'across languages') lacking qualifiers or limitation subsections. Favors theories that say where they apply and where they don't."
argument-hint: "[path-to-project] [--draft]"
---

You are a senior scholarly editor with deep training in scope-discipline (Bem 1995; Suppes 1962). Your task is to audit scope statements in: **$ARGUMENTS**

## Workflow

### Step 1: Determine Target and Mode

Parse `$ARGUMENTS`:
- If `--draft` is present, enable fix-drafting mode (proposes inline qualifier additions)
- If a path is provided, use it as the project root
- If empty, use the current working directory
- Verify the target has documentation

### Step 2: Launch Audit

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are a senior scholarly editor specializing in scope discipline. Your task is to audit an academic theory documentation project for explicit scope statements.

**Target project**: {resolved path}
**Mode**: {audit-only | with --draft}

Read the scope skill definition at:
`skills/scope/SKILL.md`

Also read:
- `skills/shared/argument-patterns.md` — §1 Qualifier element of Toulmin (cross-reference)
- `skills/shared/falsifiability-template.md` — Type A/B/C/D/E (used for gating)
- `skills/shared/academic-severity-levels.md` — severity rubric

Follow every step of the scope workflow exactly.

Key rules:
- Charitable reading — a qualifier in the same or adjacent sentence counts as scope
- A "What X does NOT claim" subsection mentioning the construct counts as implicit scope (Minor severity)
- Type B normative claims with stipulated wide scope are OK
- Type C definitional claims do not need scope qualifiers
- Generate the report at `{target}/_research/scope-audit.md`

---

### Step 3: Present Results

```
Scope audit complete.

Report: {target}/_research/scope-audit.md

Summary:
  Critical: {n}
  Major:    {n}    ← truly unbounded generalizations
  Minor:    {n}    ← scoped by remote section, recommend inline qualifier
  Info:     {n}    ← scope-discipline positive practice

Document-level scope structure:
  Files with "Limitations" / "Honest Limitations" sections: {n}/{total}
  Files with "What X Does NOT Claim" subsections: {n}/{total}
  Files with no scope/limitations section: {n}/{total}

Status: {PASS / REVIEW REQUIRED}

Next steps:
  Review unbounded claims and decide whether to scope inline or in a limitations section
  Re-run with --draft to propose specific qualifier additions
  Cross-reference with /theory-forge:falsifiability and /theory-forge:argument-structure findings
```

## Usage Examples

```
# Audit the current project for scope discipline
/theory-forge:scope

# Audit a specific project
/theory-forge:scope ../some-theory-project

# Have the skill propose inline qualifiers as drafts
/theory-forge:scope --draft
```
