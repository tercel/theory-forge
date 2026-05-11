---
name: help
description: >
  Display theory-forge command reference and usage examples. Invoked by
  /theory-forge:help (no args) or /theory-forge:help <command-name> for
  detailed per-command help. Read-only — never runs audits.
instructions: >
  Read skills/shared/templates/dashboard-output.md and render the
  appropriate section: Help view (no args), Detailed help (known command),
  or Unknown command (unknown arg). Never write files or launch audits.
---

# help — Command Reference Display

Read-only skill. Renders the theory-forge command reference from the
dashboard template. Full workflow in `commands/help.md`.

## Anti-patterns

- Do **not** run any audits — this skill only displays help text.
- Do **not** read more than three files (dashboard template, one command
  file, one SKILL.md).
