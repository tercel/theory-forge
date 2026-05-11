---
name: theory-forge
description: >
  Main entry point invoked by /theory-forge (no colon suffix). Routes to
  dashboard (no args), full-suite audit across all 8 sub-audits with
  parallel-wave execution (path arg), help view (help arg), or any
  individual sub-audit by name. Use this for the top-level /theory-forge
  command — not /theory-forge:theory-forge.
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
instead of this entry for the `/theory-forge` command.

**Full workflow**: see `commands/theory-forge.md`.

## Anti-patterns

- Do **not** invoke this skill for a sub-audit — use `theory-forge:scope`,
  `theory-forge:cite-audit`, etc. directly for isolated runs.
- Do **not** confuse `/theory-forge` (this skill) with `/theory-forge:theory-forge`
  (a deprecated alias). The user types `/theory-forge` with no colon.
