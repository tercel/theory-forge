# Output Templates — Index

Audit report templates referenced by sub-skills. Each SKILL.md's "Report Generation" step instructs the sub-agent to read the corresponding template here when emitting the final report, rather than carrying the template inline.

This separation:
- Keeps each SKILL.md ~30-50 lines smaller per audit (cumulative ~300 lines across 9 sub-skills)
- Allows template-only updates without touching SKILL.md detection logic
- Allows users to customize templates without forking the operational manuals

| Audit | Template |
|---|---|
| cite-audit | `cite-audit-report.md` |
| consistency | `consistency-report.md` |
| falsifiability | `falsifiability-audit.md` |
| argument-structure | `argument-structure-report.md` |
| scope | `scope-audit.md` |
| concept-import | `concept-import-audit.md` |
| counter-argument | `counter-argument-audit.md` |
| cross-lang | `cross-lang-audit.md` |
| propagate | `propagation-report.md` |
| Full-suite aggregate | `master-report.md` |

All templates use `{placeholder}` syntax for sub-agent-fill values. The sub-agent should preserve the template structure (headings, severity ordering, severity-count table) and only substitute placeholder values.
