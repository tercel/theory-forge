# theory-forge — Dashboard Output Template

Rendered by `commands/theory-forge.md` Route A and Route A-help. The orchestrator main agent reads this file **only when actually rendering the dashboard** — never as part of routing logic, so it stays out of the main agent context during dispatching to sub-commands.

## Full dashboard (`/theory-forge:theory-forge` with no args)

Render this template, filling in the `{placeholder}` values:

```
theory-forge 0.2.3 — Academic-Rigor Toolkit for Theory Documentation

Project detected: {project-name or "(not a theory project — pass a path)"}
Bibliography: {bibliography-path or "(not found)"}
Last full-suite run: {date or "(never)"}

Recent audit reports (_research/):
  # | Report                                    | Date       | Status
  1 | citation-audit.md                         | 2026-05-11 | REVIEW REQUIRED (3 Major)
  2 | consistency-report.md                     | 2026-05-10 | PASS

──────────────────────────────────────────────────────────────────────────────
COMMANDS — usage examples
──────────────────────────────────────────────────────────────────────────────

Full suite (run all 8 audits and aggregate, parallel-wave execution)
  /theory-forge:theory-forge .                              # current project
  /theory-forge:theory-forge ../another-theory-project      # different project
  /theory-forge:theory-forge . --sequential                 # sequential fallback (slower, observable)

cite-audit  — verify every citation is real (CrossRef + Semantic Scholar + OpenAlex)
  /theory-forge:cite-audit                     # audit current project
  /theory-forge:cite-audit ../paper            # audit a specific path
  → writes _research/citation-audit.md         (WebFetch: yes)

consistency  — cross-section coherence (granularity-aware T1/T2/T3)
  /theory-forge:consistency
  → writes _research/consistency-report.md

falsifiability  — Type A/B/C/D/E claim classification + Popperian framing
  /theory-forge:falsifiability                 # audit only
  /theory-forge:falsifiability --draft         # propose four-block expansions
  → writes _research/falsifiability-audit.md

argument-structure  — Toulmin completeness + 10 fallacy classes
  /theory-forge:argument-structure
  /theory-forge:argument-structure --draft     # propose missing warrants
  → writes _research/argument-structure-report.md

scope  — boundary-condition discipline (catches unbounded "for all X" claims)
  /theory-forge:scope
  /theory-forge:scope --draft                  # propose inline qualifiers
  → writes _research/scope-audit.md

concept-import  — cross-disciplinary borrowing tier marking (T1/T2/T3)
  /theory-forge:concept-import
  /theory-forge:concept-import --lexicon extra-terms.txt
  /theory-forge:concept-import --draft         # propose tier markers
  → writes _research/concept-import-audit.md

counter-argument  — engagement with canonical opposing positions
  /theory-forge:counter-argument
  /theory-forge:counter-argument --opposition-map oppositions.yaml
  /theory-forge:counter-argument --draft       # propose engagement paragraphs
  → writes _research/counter-argument-audit.md

cross-lang  — cross-linguistic universality coverage (scaffold-default generation)
  /theory-forge:cross-lang                              # audit only (safest)
  /theory-forge:cross-lang --generate                   # safe scaffolds (templates)
  /theory-forge:cross-lang --generate-surface \
        --i-have-native-speaker-review                  # concrete drafts (must-review)
  → writes _research/cross-lang-audit.md

propagate  — after editing an upstream doc, propagate downstream
  /theory-forge:propagate docs/foundations/core-concept.md
  /theory-forge:propagate --since HEAD~3
  /theory-forge:propagate <doc> --dry-run               # report only
  /theory-forge:propagate <doc> --save                  # persist propagation report
  → writes _research/propagation-report-{timestamp}.md

──────────────────────────────────────────────────────────────────────────────
INVOCATION FORMS
──────────────────────────────────────────────────────────────────────────────

  Claude Code requires the `plugin:command` form. Two equivalent ways to run
  any sub-audit:

    /theory-forge:theory-forge cite-audit       # through the orchestrator
    /theory-forge:cite-audit                    # direct (skips routing)

  There is NO bare `/theory-forge` or `/cite-audit` form. Typing those alone
  produces `Unknown command`.

──────────────────────────────────────────────────────────────────────────────
GETTING HELP
──────────────────────────────────────────────────────────────────────────────

  /theory-forge:theory-forge help              # this view (no project-status header)
  /theory-forge:theory-forge help <command>    # detailed help for one command
  /theory-forge:help                           # same as above (direct skill)
  /theory-forge:help <command>                 # e.g. /theory-forge:help cite-audit

Full reference:
  README.md  — overview, design principles, counter-argument engagement
  docs/usage.md  — end-to-end workflow recipes (5 recipes)
  _research/self-audit-report.md  — known gaps in this tool itself
```

## Help view (`/theory-forge:theory-forge help`)

Same as the dashboard above, but **omit** the "Project detected", "Bibliography", "Last full-suite run", and "Recent audit reports" header lines (lines 1–7). Start directly at the `──── COMMANDS ────` separator.

## Detailed help (`/theory-forge:theory-forge help <command>`)

Render this per-command template, sourcing the values from `commands/{command}.md` (description, argument-hint, Usage Examples block) and `skills/{command}/SKILL.md` (Anti-patterns, severity rules):

```
theory-forge — Detailed help for: {command}

Purpose:
  {description from command frontmatter}

Argument hint:
  {argument-hint from command frontmatter}

Usage:
  {full Usage block from the command file's "Usage Examples" section}

Output:
  {output file path — extracted from SKILL.md "Report Generation" step}

WebFetch:
  {yes/no, with which services if yes — extracted from SKILL.md or command allowed-tools}

Anti-patterns / safety guards:
  {bulleted list, sourced verbatim from the SKILL.md's Anti-patterns section}

Severity rules (summary):
  {one-line summary per severity level — extracted from SKILL.md severity table}

See also:
  README.md §{command name}  — overview
  skills/{command}/SKILL.md  — full operational manual
  docs/usage.md  — workflow recipes that use this command
```

For an unknown command (`/theory-forge:theory-forge help foo`):

```
Unknown command: foo.

Available commands:
  cite-audit, consistency, falsifiability, argument-structure,
  scope, concept-import, counter-argument, cross-lang, propagate

Run `/theory-forge:theory-forge help` to see all commands with usage examples.
```
