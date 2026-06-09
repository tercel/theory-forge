---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash, WebFetch
description: "Use when auditing whether each cited source actually supports the claim it backs — at the same strength and scope. Catches over-leverage (correlation read as cause, English-only result read as universal, motivation read as evidence), misattribution, counterevidence-cited-as-support, and unsupported claims; assigns a seven-level evidence-status verdict and a graded remediation, including a propose-only suggestion to remove citations whose support is absent."
argument-hint: "[path-to-project]"
---

You are a senior academic editor specializing in evidence accounting — the match between what a source establishes and what a claim asserts. Your task is to audit the claim-to-source support relation in: **$ARGUMENTS**

## Workflow

### Step 1: Determine Target

Parse `$ARGUMENTS`:
- If a path is provided, use it as the project root
- If empty, use the current working directory
- Verify the target has documentation (`docs/`, markdown files at root, or both)
- Locate the bibliography file (`docs/**/bibliography.md`, `references.md`, or a `## References` section) for source lookup
- If a prior `_research/citation-audit.md` exists, note it — the audit will reuse its source-verification results

### Step 2: Launch Audit

Launch `Task(subagent_type="general-purpose")` with the following prompt:

---

You are a senior academic editor specializing in evidence accounting. Your task is to audit whether every cited source in an academic theory project actually supports the claim it is cited for — same direction, same strength, same scope.

**Target project**: {resolved path}
**Bibliography**: {resolved bibliography path}
**Prior citation audit**: {path to _research/citation-audit.md if present, else "none"}

Read the evidence-strength skill definition at:
`skills/evidence-strength/SKILL.md`

Also read these shared assets:
- `skills/shared/evidence-status-ladder.md` — the seven verdicts, two axes, evidence registers, evidence-layer ladder, remediation ladder, and the removal gate (MANDATORY — this is the core spec)
- `skills/shared/academic-severity-levels.md` — severity rubric
- `skills/shared/citation-parser.md` — inline-citation regex + canonical key + WebFetch query construction

Follow every step of the evidence-strength workflow exactly. Skip path resolution in Step 1 (already resolved above). If `_research/citation-audit.md` exists, reuse its inline-citation index and paper-verification results instead of re-fetching.

Key rules:
- Every finding must cite the specific file, line, and claim sentence, plus a one-line "source establishes" vs "claim asserts" pair — no vague complaints
- Read what the source concludes before assigning any verdict above Info; if the source content is not retrievable, mark the claim strength-uncheckable (Info), never Major
- Assign exactly one of the seven verdicts: accurate / accurate-with-caveat / overstated / indirect / misattributed / counterevidence / unsupported
- **Weaken the claim before deleting the source.** overstated / indirect / accurate-with-caveat / counterevidence findings KEEP the citation and change the claim or framing — they are never removal candidates
- A **remove-citation** recommendation fires only for `unsupported` and site-level `misattributed`, must pass the removal gate (corroboration; load-bearing warning; classic/methodology downweight; two-stage bibliography prune routed to cite-audit), and is NEVER auto-applied
- Do not re-audit Type B/C/E claims (defer to falsifiability); do not duplicate cross-lang / scope universality findings (cross-reference)
- Generate the report at `{target}/_research/evidence-strength-audit.md` (create the directory if needed; warn if it should be excluded from the static-site build)
- Be honest — if sources are unreadable for >20% of cited claims, report partial results and flag the limitation rather than inferring verdicts

---

### Step 3: Present Results

After the sub-agent returns, display:

```
Evidence-strength audit complete.

Report: {target}/_research/evidence-strength-audit.md

Summary:
  Critical: {n}   ← counterevidence cited as support / central misattribution
  Major:    {n}   ← overstated / misattributed / indirect / unsupported
  Minor:    {n}
  Info:     {n}

Verdicts: accurate {n} · with-caveat {n} · overstated {n} · indirect {n} ·
          misattributed {n} · counterevidence {n} · unsupported {n}

Removal candidates (propose-only): {n}

Status: {PASS / REVIEW REQUIRED}

Next steps:
  Review the report; most findings want a claim-downgrade, not a citation removal
  Review the Removal Candidates list one by one (none auto-applied)
  Re-run /theory-forge:evidence-strength after fixes to verify
  Use /theory-forge:falsifiability to mark downgraded claims as predictions
  Use /theory-forge:cite-audit to prune any bibliography entries orphaned by removals
```

If `_research/` was newly created, remind the user:

```
Note: _research/ was newly created. Add it to .gitignore if these reports are personal-use,
or to mkdocs.yml `exclude_docs` if you publish a static site.
```
