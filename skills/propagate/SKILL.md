---
name: propagate
description: >
  Walk an upstream academic-theory doc change downstream — when a foundation doc
  (e.g., a formal definition section) is edited, find every dependent doc that
  references the changed concept and interactively update them. Adapted from
  spec-forge:propagate for academic cross-reference patterns (citations, glossary,
  foundations ↔ methodology ↔ manifesto).
---

# theory-forge — Propagate

## Execution Entry Point

When this skill is loaded, execute the workflow below immediately. Skills are operational manuals, not reference documents. Read Step 1 (Determine Source Doc), perform it, then Steps 2, 3, 4 in order, stopping at the `AskUserQuestion` checkpoint in Step 5.

## Iron Law

**EVERY UPSTREAM CHANGE PROPAGATES OR FAILS LOUDLY. Partial propagation is worse than no propagation — it creates the illusion of consistency where there is none.**

In an academic theory project this matters even more than in software: a stale cross-reference to a retracted citation, a definition that disagrees with its glossary entry, or a methodology doc that references the pre-revision §2.1 wording — these all undermine the work's scholarly integrity.

## When to Use

- After editing a foundation doc (e.g., `core-concept.md` definition section)
- After retracting or revising a citation
- After renaming a defined construct
- Periodically, with `--since {git-ref}`, to catch accumulated drift
- When `consistency` or `cite-audit` flags an upstream-downstream mismatch

This skill adapts `spec-forge:propagate` (a code/spec-aligned propagation skill) for academic cross-reference patterns. The core workflow is the same; the patterns are different.

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/academic-severity-levels.md` — severity rubric (for STILL_STALE / Critical findings during propagation)

**ON-DEMAND (load only at the listed step):**
- `../shared/output-templates/propagation-report.md` — load at Step 8 (Report Generation)

## Command Format

```
/theory-forge:propagate [@source-doc.md] [--since git-ref] [--dry-run] [--save]
```

| Argument / Flag | Default | Description |
|---|---|---|
| `@source-doc.md` | — | The upstream doc whose changes should propagate. If omitted, auto-detect from `git diff HEAD`. |
| `--since {git-ref}` | — | Compute the diff between `{git-ref}` and `HEAD`. |
| `--dry-run` | off | Generate the impact report; do NOT apply any edits. |
| `--save` | off | Save the propagation report to `_research/propagation-report-{timestamp}.md`. |

## Workflow

### Step 1: Determine Source

Resolve the source per spec-forge:propagate §1.1 logic. Glob pattern for academic projects:

```
git diff --name-only HEAD -- 'docs/**/*.md' '*.md' ':!_research/**' ':!_drafts/**'
```

If auto-detection finds 0 files, prompt the user to specify `@source-doc.md`.

### Step 2: Extract Changed Concepts

For each source file, get the diff and extract:

1. **Renamed constructs** — heading changes, definition-block heading changes, glossary entry renames
2. **Definition revisions** — bulleted component lists added/removed, formal-definition prose revisions
3. **Retracted citations** — citations removed from one section that may need removal elsewhere
4. **New citations** — bibliography additions that may need to be cited in dependent docs
5. **Renamed sections** — `## 9 → ## 8.5` style renumbering (downstream cross-refs break)

For each changed concept, classify by **impact radius**:

| Impact | Heuristic |
|---|---|
| **Wide** | Foundation doc (foundations/, manifesto, glossary) | All downstream docs in scope |
| **Medium** | Methodology doc | All references in glossary + foundations |
| **Narrow** | Single methodology subsection | References only in tightly-coupled docs |

### Step 3: Discover Downstream References

For each changed concept, grep across the corpus for downstream references. Patterns:

| Reference type | Grep pattern |
|---|---|
| **Direct mention** | The construct name (with word boundaries) |
| **Section cross-ref** | `core-concept.md` §N, `core-concept.md§N`, `[file](path)#anchor` |
| **Citation reuse** | `(Author Year)` appearing elsewhere |
| **Glossary entry** | Lines in `glossary.md` matching the construct |
| **Inline definition** | "See {file} §N" patterns |
| **Mermaid diagram nodes** | Nodes in `.mmd` blocks or inline mermaid referencing the construct |
| **Anchor link** | `#some-anchor` referencing a renamed heading |

Build the downstream reference table:

```
Concept "Core" (renamed from "Salience Anchor" in core-concept.md §1):
  References:
    glossary.md line 23 — Glossary entry "Salience anchor"
    manifesto.md line 113 — "The Core in CFLT is a salience anchor"
    foundations/linguistics.md line 25 — "The 'Core' in CFLT is a salience anchor"
    methodology/slot-disambiguation.md line 22 — "two-tier model"
  Impact: WIDE (foundation rename — every doc referencing the term is affected)
```

### Step 4: Per-Downstream Impact Analysis

For each downstream file containing references, launch a sub-agent (parallel-safe, max 4 concurrent):

> Read {file}, identify each reference to {concept}, and classify whether it requires:
> - **HIGH confidence** — exact rename/update with no semantic change (e.g., `salience anchor` → `Core` consistently)
> - **MEDIUM confidence** — likely the same update but semantic check needed
> - **LOW confidence** — the reference might mean something different in this context
> - **AMBIGUOUS** — could be either; needs human review

Each sub-agent returns a per-reference recommendation with the proposed replacement text.

### Step 5: Interactive Review

For each file with proposed changes:

1. Display a unified diff of proposed edits, grouped by confidence band
2. Use AskUserQuestion: `Apply changes to {file}?` with options:
   - `Apply HIGH only` — apply only HIGH-confidence changes (safest)
   - `Apply HIGH + MEDIUM with confirmation` — show diff for each MEDIUM and ask before applying; LOW/AMBIGUOUS skipped
   - `Inspect line by line` — review every change one at a time
   - `Skip this file`

**Confidence-band auto-apply policy:**

| Confidence | Auto-apply allowed? | Default behavior |
|---|---|---|
| HIGH | Yes (with file-level confirmation) | Listed in summary; applied on "Apply HIGH only" |
| MEDIUM | Only with per-change confirmation | Show diff, ask user |
| LOW | **Never auto-apply** | Listed for user review; not edited |
| AMBIGUOUS | **Never auto-apply** | Listed for user review; not edited |

### Step 6: Apply Changes — Pre-flight Safety Checks

Before any `Edit` call:

1. **Source-doc protection.** Re-verify the target file is NOT in the source set computed in Step 1. If it is, abort with error: `Refusing to edit source document {file} during propagation. Sources are immutable in this workflow.`
2. **Uncommitted-changes protection.** Run `git status --porcelain {file}`. If the target file has uncommitted changes, prompt: `{file} has uncommitted changes. Applying propagation will entangle your edits with the propagation. Proceed?` (default: No).
3. **Tool discipline.** Use `Edit` (never `Write`) for surgical replacements. `Write` would lose any content not in the propagation diff and is forbidden.
4. **Per-change confirmation for MEDIUM.** Even within the "Apply HIGH + MEDIUM" mode, each MEDIUM change requires its own diff display + per-change yes.

After all edits applied, run `git diff --stat` and display the summary so the user sees the actual change footprint before any commit.

### Step 7: Verify

After applying:

1. Re-grep for the original (pre-change) construct names — any surviving instance is a `STILL_STALE` warning
2. Run `/theory-forge:consistency` over the modified files to confirm no new mismatches
3. If the source change involved a citation revision, suggest re-running `/theory-forge:cite-audit`

### Step 8: Report

Write the report (always, even without `--save`) to `_research/propagation-report-{timestamp}.md`.

**Report structure**: read the template at `../shared/output-templates/propagation-report.md` and fill in the `{placeholder}` values, including the Changed Concepts table, per-file downstream updates, and STILL_STALE warnings. Loaded only at this step.

## Anti-patterns

Do **not**:
- Modify the source document itself
- Use `Write` to overwrite a downstream file — only `Edit`
- Auto-apply LOW or AMBIGUOUS changes
- Touch `_research/`, `_drafts/`, or any underscore-prefixed file as downstream
- Propagate stylistic preferences (sentence rewording) — only propagate concept/citation/structural changes
- Silently skip a downstream reference because it looked unclear — emit it as AMBIGUOUS for user review

## Exit Conditions

- **Clean propagation**: all references updated, 0 STILL_STALE warnings, consistency re-check passes.
- **Partial propagation**: some references skipped by user or remain AMBIGUOUS — report which.
- **Failed propagation**: STILL_STALE warnings present after apply — investigate before declaring done.
