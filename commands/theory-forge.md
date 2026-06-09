---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task, Bash, WebFetch
description: "MAIN ENTRY POINT — invoke as `/theory-forge:theory-forge`. Orchestrates: dashboard (no args), full-suite audit across all 9 sub-audits in parallel waves (path arg), help view (`/theory-forge:theory-forge help`), and routing to any individual sub-audit (`/theory-forge:theory-forge cite-audit`, `scope`, etc.). Sub-audits also callable directly: `/theory-forge:cite-audit`, `/theory-forge:scope`, etc."
argument-hint: "[subcommand] [path-or-doc] | (empty for dashboard)"
---

You are the theory-forge orchestrator. Your job is to route subcommands or run the full audit suite on an academic theory documentation project.

The user invoked: `/theory-forge:theory-forge $ARGUMENTS`

## Step 1: Parse Arguments

Parse `$ARGUMENTS` into `subcommand` and `argument`:

| Input Pattern | subcommand | argument |
|---|---|---|
| `cite-audit ../some-project` | `cite-audit` | `../some-project` |
| `evidence-strength` | `evidence-strength` | (cwd) |
| `consistency` | `consistency` | (cwd) |
| `falsifiability ../some-project` | `falsifiability` | `../some-project` |
| `cross-lang` | `cross-lang` | (cwd) |
| `argument-structure` | `argument-structure` | (cwd) |
| `scope` | `scope` | (cwd) |
| `concept-import` | `concept-import` | (cwd) |
| `counter-argument` | `counter-argument` | (cwd) |
| `propagate docs/foundations/core-concept.md` | `propagate` | `docs/foundations/core-concept.md` |
| `help` | `help` | — |
| `help cite-audit` | `help` | `cite-audit` |
| (empty) | `dashboard` | — |
| `../some-project` (path-shaped) | `full-suite` | `../some-project` |
| any other single token | `full-suite` | (cwd, with the token as a tag) |

## Step 2: Route

Routing table summary:

| Subcommand value | Route handler |
|---|---|
| `dashboard` (no args) | Route A — full dashboard with project status + command usage |
| `help` (no command argument) | Route A-help — command usage only (no project status) |
| `help <command>` | Route A-help-detail — detailed help for one command |
| `cite-audit`, `evidence-strength`, `consistency`, `falsifiability`, `argument-structure`, `scope`, `concept-import`, `counter-argument`, `cross-lang`, `propagate` | Route B — hand off to that command file |
| `full-suite` | Route C — run all 9 audits and aggregate |

### Route A: `dashboard` (no arguments)

Display theory-forge dashboard:

1. Detect whether the cwd is a theory project (has `docs/` with foundation or theory markdown files, plus a bibliography).
2. Scan `_research/` for prior audit reports and their dates (top 5).
3. Read the dashboard template: `skills/shared/templates/dashboard-output.md` §"Full dashboard"
4. Fill in the `{placeholder}` values with project state.
5. Print the rendered dashboard.

Then stop.

> **Note on token economy:** the dashboard template is NOT inlined here. It lives in `skills/shared/templates/dashboard-output.md` and is read only when actually rendering. This keeps `commands/theory-forge.md` lean for the common case of routing to a sub-command, where the dashboard text would otherwise occupy ~90 lines of the main agent's context for no reason.

### Route A-help: `help` or `help <command>`

If the user types `/theory-forge:theory-forge help` (no command argument):
1. Read `skills/shared/templates/dashboard-output.md` §"Help view"
2. Render the COMMANDS / INVOCATION FORMS / GETTING HELP sections (omit the project-status header).

If the user types `/theory-forge:theory-forge help <command>` (e.g. `/theory-forge:theory-forge help cite-audit`):
1. Verify `<command>` is one of: cite-audit, evidence-strength, consistency, falsifiability, argument-structure, scope, concept-import, counter-argument, cross-lang, propagate.
2. If unknown: print the "Unknown command" template (in `dashboard-output.md` §"Unknown command").
3. If known: read `commands/{command}.md` (for description, argument-hint, Usage Examples) and `skills/{command}/SKILL.md` (for Anti-patterns + severity rules).
4. Render the "Detailed help" template (in `dashboard-output.md` §"Detailed help"), filling in the per-command values.

### Route B: subcommand routing

For `cite-audit`, `evidence-strength`, `consistency`, `falsifiability`, `cross-lang`, `argument-structure`, `scope`, `concept-import`, `counter-argument`, `propagate`:

Invoke the corresponding command. For example, for `cite-audit`:

```
Hand off to /theory-forge:cite-audit {argument}
```

This is a hand-off — load and execute that command's workflow (read the file at `commands/{subcommand}.md` and follow it with `$ARGUMENTS` replaced by `argument`).

### Route C: `full-suite` — Run all audits (parallel-wave execution)

This is the orchestrator's core value-add. **Execution model: two parallel waves — 4 audits in Wave 1, 5 in Wave 2.** Eight of the nine audits are mutually independent (no inter-audit data dependency — each reads the project state independently). The one exception is `evidence-strength`, which **reuses `cite-audit`'s verified-source results** to avoid re-fetching; it is therefore placed in Wave 2, after `cite-audit` (Wave 1) has produced `citation-audit.md`. If that report is absent (e.g., cite-audit failed), `evidence-strength` falls back to fetching its own sources. The parallel-wave model exploits the remaining independence for ~4× wall-clock speedup while maintaining safety.

**Why waves of 4 + 5 rather than all 9 at once:** Claude Code's Task tool supports parallel sub-agent invocations within a single message; running ~4–5 concurrent is a known-stable bound. 9-at-once may exceed rate limits or system constraints; the split is the conservative default. The wave boundary also enforces the one real dependency — `evidence-strength` (Wave 2) must run after `cite-audit` (Wave 1).

**Wave 1** (launch all 4 in a single message via parallel `Task` calls):

| Audit | Why this wave | WebFetch |
|---|---|---|
| `cite-audit` | Largest single audit (WebFetch-bound) — start it earliest so WebFetch overlaps Wave 1 wall-clock | **yes** |
| `consistency` | Local-only, fast | no |
| `falsifiability` | Local-only, fast | no |
| `cross-lang` | Local-only, independent | no |

**Wave 2** (after Wave 1 completes, launch all 5 in a single message):

| Audit | Why this wave |
|---|---|
| `evidence-strength` | **Real data dependency** — reuses `cite-audit`'s verified-source results from Wave 1 (falls back to its own fetches if `citation-audit.md` is absent) |
| `argument-structure` | Falls in Wave 2 because some readers find it most useful after seeing falsifiability output (UX consideration only — not a data dependency) |
| `scope` | Similar UX rationale |
| `concept-import` | Independent |
| `counter-argument` | Independent |

**Why not run all 9 in Wave 1:** partly a wall-clock-safety choice, partly the one genuine dependency (`evidence-strength` after `cite-audit`). If empirically a wider wave works reliably for a particular Claude Code version, the independent audits can be collapsed — but keep `evidence-strength` strictly after `cite-audit`.

**`propagate`** is skipped in full-suite mode — it requires a specific upstream-edited doc as input.

#### Wave execution mechanic

For each wave, in a **single tool-call message**, dispatch all sub-agents:

```
Wave 1 dispatch (one message, four Task tool calls in parallel):
  Task(subagent_type="general-purpose", prompt=<cite-audit launch prompt>)
  Task(subagent_type="general-purpose", prompt=<consistency launch prompt>)
  Task(subagent_type="general-purpose", prompt=<falsifiability launch prompt>)
  Task(subagent_type="general-purpose", prompt=<cross-lang launch prompt>)

Wait for all four results.

Wave 2 dispatch (one message, five Task tool calls in parallel):
  Task(subagent_type="general-purpose", prompt=<evidence-strength launch prompt>)  # reuses Wave 1 cite-audit output
  Task(subagent_type="general-purpose", prompt=<argument-structure launch prompt>)
  Task(subagent_type="general-purpose", prompt=<scope launch prompt>)
  Task(subagent_type="general-purpose", prompt=<concept-import launch prompt>)
  Task(subagent_type="general-purpose", prompt=<counter-argument launch prompt>)

Wait for all five results.

Aggregate.
```

Each launch prompt is identical to what the individual command file (e.g., `commands/cite-audit.md`) uses — the orchestrator reuses those prompts verbatim. This guarantees that running a sub-audit through the orchestrator behaves identically to invoking it standalone.

**Failure handling**: if any Wave 1 sub-agent fails (timeout, network error for cite-audit's WebFetch, etc.), capture the failure and proceed to Wave 2 — do not block Wave 2 on Wave 1 failures. The master report flags any failed audit clearly. Partial aggregation is better than no report.

#### Sequential fallback (`--sequential` flag)

For users who prefer sequential execution (older Claude Code versions, manual progress monitoring, or debugging an individual audit), the `--sequential` flag falls back to the previous behavior:

```
/theory-forge:theory-forge . --sequential
```

In sequential mode, the orchestrator runs each audit in turn (cite-audit → evidence-strength → consistency → falsifiability → argument-structure → scope → concept-import → counter-argument → cross-lang). `evidence-strength` runs immediately after `cite-audit` so it can reuse the just-written `citation-audit.md`. Wall-clock is slower but progress is observable one audit at a time.

After both waves complete, write the master report at `_research/theory-forge-master-report.md`.

**Report structure**: read the template at `skills/shared/output-templates/master-report.md` and fill in the `{placeholder}` values across the Aggregate Summary, Cross-Audit Patterns, and Recommended Fix Order sections. The template is loaded only at this step, not on dashboard or routing. This keeps the orchestrator command file lean for the common case of routing to a sub-command.

### Step 3: Present Results

After all sub-audits complete, display the aggregate summary and recommend the top 3 fixes by severity × cross-audit overlap.

## Notes

- The orchestrator does not run `propagate` in full-suite mode because `propagate` requires a specific changed-document argument. Mention this in the dashboard.
- If WebFetch is unavailable (offline mode), `cite-audit` and `evidence-strength` are affected — both verify sources over the network and produce partial reports flagged as such (`evidence-strength` marks claims strength-uncheckable rather than guessing). `consistency`, `falsifiability`, `argument-structure`, `scope`, `concept-import`, `counter-argument`, and `cross-lang` are all local-only and unaffected.
- The orchestrator never auto-applies fixes. Each sub-audit's Step 8 (opt-in fix offer) runs independently if invoked directly; in full-suite mode all fix offers are deferred until the master report is presented and the user can decide which audits to act on.
- **Critical findings escalate.** If any sub-audit produces a Critical finding (`cite-audit` — fabricated citations; `evidence-strength` — counterevidence cited as support for a central claim), the master report's status is `REVIEW REQUIRED — CRITICAL` and the fix-offer is suppressed until the user acknowledges the Critical findings.
