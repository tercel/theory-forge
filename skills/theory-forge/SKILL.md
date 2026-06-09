---
name: theory-forge
description: >
  Main entry point invoked as `/theory-forge:theory-forge`. Routes to
  dashboard (no args), full-suite audit across all 9 sub-audits with
  parallel-wave execution (path arg), help view (help arg), or any
  individual sub-audit by name. Sub-audits are also callable directly
  (`/theory-forge:scope`, `/theory-forge:cite-audit`, etc.) — use those
  for isolated runs, and use this entry only for the orchestrator.
instructions: >
  The full workflow is in commands/theory-forge.md. Parse $ARGUMENTS and
  route: empty → dashboard, path-shaped → full-suite, known subcommand →
  delegate to that command, 'help' → help view, 'help <cmd>' → detailed
  help for that command.
---

# theory-forge — Main Orchestrator

This SKILL.md exists to ensure `theory-forge:theory-forge` appears in the
skill registry's second listing block (sourced from `skills/*/SKILL.md`),
preventing routing ambiguity where the model might pick `theory-forge:scope`
instead of this entry for the orchestrator.

**Full workflow**: see `commands/theory-forge.md`.

## Invocation

Claude Code slash commands require the `plugin:command` form. The valid
invocations for this plugin are:

- `/theory-forge:theory-forge` — the orchestrator (this entry)
- `/theory-forge:theory-forge <subcommand>` — routed through the orchestrator
- `/theory-forge:<subcommand>` — direct call to a sub-audit, bypassing routing

There is **no** bare `/theory-forge` (no colon) form. Typing `/theory-forge`
alone produces `Unknown command` — the parser requires the colon and a
command name after it.

## Anti-patterns

- Do **not** invoke this skill for a sub-audit — use `theory-forge:scope`,
  `theory-forge:cite-audit`, etc. directly for isolated runs.
- Do **not** document or suggest `/theory-forge` (no colon) as a valid form
  in user-facing examples; it does not work.
