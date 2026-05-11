---
allowed-tools: Read
description: "Display theory-forge command reference and usage examples. Invoke as /theory-forge:help or /theory-forge help [command-name]."
argument-hint: "[command-name]"
---

You are the theory-forge help display. The user invoked: `/theory-forge:help $ARGUMENTS`

## Workflow

1. Read `skills/shared/templates/dashboard-output.md` (the single source of truth for all help content).

2. Parse `$ARGUMENTS`:
   - **Empty** → render the "Help view" section (COMMANDS / ALIASES / GETTING HELP blocks; omit project-status header lines).
   - **Known command name** (`cite-audit`, `consistency`, `falsifiability`, `argument-structure`, `scope`, `concept-import`, `counter-argument`, `cross-lang`, `propagate`) → render the "Detailed help" template:
     - Read `commands/{command}.md` for: description, argument-hint, and Usage Examples block.
     - Read `skills/{command}/SKILL.md` for: Anti-patterns section and severity table.
     - Fill in the "Detailed help" template from `dashboard-output.md`.
   - **Unknown token** → render the "Unknown command" template from `dashboard-output.md`.

3. Output the rendered help text. Done — do not run any audits.

## Constraints

- Read-only. Never write files or launch audits.
- Only read the three files listed above (dashboard template, command file, SKILL.md). No other reads.
