---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash
description: "Use after editing an upstream academic theory doc (foundation, glossary, definition section) to propagate the change downstream — keeps glossary, manifesto, methodology, and foundations docs consistent"
argument-hint: "[@source-doc.md] [--since git-ref] [--dry-run] [--save]"
---

You are a senior academic editor responsible for keeping a theory project's doc chain coherent. When an upstream document (a foundational definition, glossary entry, or formal-definition section) changes, every downstream document that references the changed concept must be updated, or the chain loses internal consistency.

Your task is to propagate changes for: **$ARGUMENTS**

## Workflow

### Step 1: Determine Source

Parse `$ARGUMENTS`:
- If `@source-doc.md` is provided, that is the source
- If `--since {git-ref}` is provided, the source is all `.md` files in `docs/` changed between that ref and `HEAD`
- Otherwise, auto-detect from `git status --porcelain` (uncommitted) or `git diff HEAD~1 HEAD`
- Exclude `_research/`, `_drafts/`, underscore-prefixed paths

If no source can be detected:

```
No doc changes detected. Provide an explicit @source-doc.md or use --since {ref}.
```

and stop.

### Step 2: Launch Propagate

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are responsible for propagating academic theory documentation changes from an upstream foundation doc downstream through the doc chain.

**Source documents**: {resolved source files}
**Mode**: {normal | dry-run}

Read the propagate skill definition at:
`skills/propagate/SKILL.md`

Follow every step of the workflow exactly:
1. Step 1 (Determine Source) is already done — use the resolved sources above
2. Continue from Step 2 (Extract Changed Concepts) — renamed constructs, definition revisions, retracted/new citations, renumbered sections
3. Step 3 (Discover Downstream References) — grep for direct mentions, cross-refs, citation reuse, glossary entries, inline `see X §N`, mermaid nodes, anchor links
4. Step 4 (Per-Downstream Impact Analysis) — parallel sub-agents, max 4 at a time, classify each reference HIGH/MEDIUM/LOW/AMBIGUOUS
5. Step 5 (Interactive Review) — AskUserQuestion per downstream file
6. Step 6 (Apply Changes) — surgical Edit only, never Write
7. Step 7 (Verify) — re-grep for stale references; suggest re-running consistency / cite-audit
8. Step 8 (Report) — write `_research/propagation-report-{timestamp}.md`

Key rules:
- Never modify the source documents — they are the upstream truth
- Never use `Write` to overwrite a downstream file — use `Edit` for surgical changes
- Never auto-apply LOW-confidence or AMBIGUOUS changes — always ask the user
- Skip `_research/`, `_drafts/`, underscore-prefixed paths as downstream targets
- Surface every stale reference that survives the propagation as a `STILL_STALE` warning
- If `--dry-run`, generate the full report but apply nothing

---

### Step 3: Present Results

After the sub-agent returns, display the propagation summary it produced and suggest next steps:

```
Propagation complete.

Report: {target}/_research/propagation-report-{timestamp}.md

Summary:
  Concepts changed:        {n}
  Downstream refs found:   {n}
  Auto-applied (HIGH):     {n}
  Manually confirmed:      {n}
  STILL_STALE warnings:    {n}

Next steps:
  Review the diff:           git diff
  Commit:                    git add docs/ && git commit -m "docs: propagate {source} change"
  Re-run consistency check:  /theory-forge:consistency
  Re-run citation audit:     /theory-forge:cite-audit (if citations changed)
```
