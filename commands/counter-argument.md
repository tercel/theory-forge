---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash, WebFetch
description: "Use when auditing academic theory documents for engagement with opposing positions — competing theories, alternative explanations, known critiques. Flags central claims that have not been challenged."
argument-hint: "[path-to-project] [--draft] [--opposition-map path/to/extra-oppositions.yaml]"
---

You are a senior scholarly editor trained in counter-argument engagement (Booth, Colomb & Williams 2016; Graff & Birkenstein *They Say, I Say*). Your task is to audit opposing-position engagement in: **$ARGUMENTS**

## Workflow

### Step 1: Determine Target and Mode

Parse `$ARGUMENTS`:
- If `--draft` is present, enable engagement-drafting mode
- If `--opposition-map` is provided, load additional topic → opposition entries from the YAML file
- If a path is provided, use it as the project root
- If empty, use the current working directory
- Verify the target has documentation

### Step 2: Launch Audit

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are a senior scholarly editor specializing in argumentation. Your task is to audit an academic theory documentation project for engagement with opposing positions.

**Target project**: {resolved path}
**Mode**: {audit-only | with --draft}
**Extra opposition map**: {file if provided}

Read the counter-argument skill definition at:
`skills/counter-argument/SKILL.md`

Also read:
- `skills/shared/argument-patterns.md` §1 (Rebuttal element)
- `skills/shared/falsifiability-template.md` — for Type A/B/C/D/E classification of central claims
- `skills/shared/academic-severity-levels.md`

Follow every step of the counter-argument workflow exactly.

Key rules:
- Engagement = fair characterization + substantive response, not just citation
- Strongest opposition, not weakest — flag strawman engagement at Major
- Focus on canonical opposing positions per topic; do not demand engagement of every possible critique
- Generate the report at `{target}/_research/counter-argument-audit.md`
- For --draft, frame proposed engagement paragraphs as starting points requiring author refinement

---

### Step 3: Present Results

```
Counter-argument audit complete.

Report: {target}/_research/counter-argument-audit.md

Summary:
  Critical: {n}
  Major:    {n}    ← central claims with no engagement of canonical opposition; strawman engagement
  Minor:    {n}    ← weak engagement (acknowledged only, not substantively engaged)
  Info:     {n}    ← good engagement practice

Engagement profile:
  Central claims found:                {n}
  Strong engagement:                   {n}
  Medium engagement:                   {n}
  Weak (acknowledged only):            {n}
  No engagement:                       {n}

Status: {PASS / REVIEW REQUIRED}

Next steps:
  Review unengaged central claims and decide which oppositions warrant a response
  Re-run with --draft to propose engagement paragraphs (always require author refinement)
  Note: PASS means engagement is present, NOT that the engagement succeeds — peer review judges that
```

## Usage Examples

```
# Audit counter-argument engagement in the current project
/theory-forge:counter-argument

# Audit a specific project
/theory-forge:counter-argument ../some-theory-project

# Extend the opposition map with project-specific topic→opposition pairs
/theory-forge:counter-argument --opposition-map my-oppositions.yaml

# Have the skill draft engagement paragraphs
/theory-forge:counter-argument --draft
```
