---
name: scope
description: >
  Audit academic theory documents for explicit scope statements — where the
  theory applies, where it does not, what boundary conditions exist, what
  populations / domains / regimes are in or out. Flags unbounded generalizations
  ("for all X", "across languages", "universally") that lack explicit
  applicability or limitation statements. Read-only by default.
instructions: >
  Be charitable. A claim with a qualifier in the same sentence ("typically",
  "in adult L2 learners") already has scope. Only flag truly unbounded claims.
  Type B normative claims often legitimately have wide scope by stipulation —
  do not over-flag them. The skill complements falsifiability and argument-structure.
---

# scope — Boundary Conditions and Applicability Audit

This skill enforces a single discipline: **theories must say where they apply and where they don't**. A claim that applies "always, everywhere, to everything" without explicit scope is either trivially true (and uninformative) or false (and unfalsifiable for the wrong reason).

Bem (1995) "Writing the Empirical Journal Article" and Suppes (1962) "Models of Data" both stress that scope statements are the difference between a theory and a slogan.

## Core Principles

1. **Scope is information.** A theory that says "X holds for Y but not Z" is more informative than a theory that says "X holds." The audit favors scoping over universalism.
2. **Charitable reading.** A claim with an in-line qualifier ("typically", "for adult L2 learners", "at the protocol layer") already has scope; don't flag.
3. **A "What X does NOT claim" subsection counts.** A foundation doc with an explicit limitation subsection (CFLT §8.5 pattern) earns scope credit for its central claims.
4. **Read-only by default.**

## Reusable Assets

**MANDATORY (load on start):**
- `../shared/academic-severity-levels.md` — severity rubric

**ON-DEMAND (load only at the listed step):**
- `../shared/argument-patterns.md` — load at Step 2 ONLY for the Toulmin Qualifier cross-reference
- `../shared/falsifiability-template.md` — load at Step 3 ONLY for Type A/B/C/D/E gating (Type B/C don't require scope qualifiers)
- `../shared/output-templates/scope-audit.md` — load at Step 5 (Report Generation)

## Workflow

### Step 1: Locate Generalization-Shaped Claims

Scan all in-scope files for sentences with **universalizing language**:

| Pattern | Example |
|---|---|
| "all X" | "all natural languages" |
| "every X" | "every learner" |
| "any X" | "any utterance" |
| "across X" | "across languages", "across domains" |
| "universally" | "universally prioritized position" |
| "for all" | "for all speakers" |
| "in general" | "in general, X follows from Y" |
| "always" | "always places Core first" |
| "X is Y" (with bare-noun subject) | "Theories are testable" |

For each match, record file:line and surrounding ≤200 words within the same section.

### Step 2: Check for In-Line Scope Qualifiers

For each generalizing claim, check whether scope qualifiers are present **in the same sentence or the immediately preceding/following sentence**. Qualifier types:

| Qualifier type | Examples |
|---|---|
| **Population restriction** | "for adult intermediate L2 learners", "in proficient bilinguals" |
| **Domain restriction** | "at the protocol layer", "in unmarked default utterances", "in non-fiction discourse" |
| **Conditional** | "when the speaker has a clear primary intent", "given a stable scaffold" |
| **Statistical hedge** | "typically", "usually", "in most cases", "in the majority of" |
| **Theoretical hedge** | "we hypothesize", "we expect", "tentatively" (forwarded to `falsifiability` Type E check) |
| **Negative scope** | "but not in case X", "except when Y" |

A claim with **at least one qualifier** of any type is **scoped**. Skip to Step 3.

A claim with **no qualifiers** is **unbounded**. Continue to Step 2b.

#### Step 2b — Check for limitation subsection

For unbounded claims, look in the same file for a section heading matching:

- "Limitations"
- "Honest Limitations"
- "Caveats"
- "What X Does NOT Claim" (the CFLT pattern)
- "Scope"
- "Boundary Conditions"
- "Where this does not apply"

If such a section exists and **explicitly mentions** the unbounded claim's construct (the noun the claim is about), credit the claim with **implicit scope** (Minor severity, not Major).

If neither in-line qualifiers nor a limitation subsection covers the claim, the claim is **truly unbounded** (Major severity).

### Step 3: Severity Grading

| Situation | Severity |
|---|---|
| Generalizing claim with explicit in-line qualifier | OK (no finding) |
| Generalizing claim with no qualifier, but covered by a limitation subsection | **Minor** — recommend moving the qualifier inline for visibility |
| **Generalizing claim with no qualifier AND no limitation subsection coverage** | **Major** — true unbounded claim |
| Generalizing claim that is Type B (normative — author's design choice) AND clearly marked as such | OK — design choices can have stipulated wide scope |
| Generalizing claim that is Type C (definitional) | OK — definitions are stipulative |
| Generalizing claim that is Type D (literature restatement) | OK — scope is the original author's responsibility |
| Generalizing claim that is Type E (marked hypothesis) | Minor only if scope-of-evidence is stated; OK otherwise (hypotheses can be bold) |

### Step 4: Scope Discipline at Document Level

For each foundation doc, also check:

1. **Has a "Scope" or "Limitations" subsection?** (Info — positive practice if yes)
2. **Has a "What X does NOT claim" subsection?** (Info — positive practice if yes)
3. **Does the abstract / introduction state the theory's scope?** (Look for phrases like "we focus on", "we restrict to", "the scope of this work is")
4. **Is there a distinct "Honest Limitations" subsection per major section?** (CFLT pattern — log as Info good practice)

### Step 5: Report Generation

Write the report to `_research/scope-audit.md`.

**Report structure**: read the template at `../shared/output-templates/scope-audit.md` and fill in the `{placeholder}` values. The template includes the Document-Level Findings section that catalogs per-file presence of Limitations / "What X Does NOT Claim" subsections. Loaded only at this step.

### Step 6: Offer Fix Drafts (opt-in)

> Found {N} Major findings of unbounded claims. Would you like me to:
> 1. Draft inline qualifier additions for each (you confirm each — `--draft` mode)
> 2. Just save the report and exit (default)

`--draft` mode generates ≤1-sentence qualifier additions for each Major finding, applied only on per-finding confirmation.

## Anti-patterns

Do **not**:
- Flag Type C definitional claims as unbounded — definitions stipulate scope
- Flag Type B normative claims for wide scope when the author marked them as design choices ("CFLT places Core at position 0" applies to all CFLT-formatted text by definition; it doesn't need a qualifier)
- Re-flag claims that `falsifiability` already flagged as unmarked-Type-A — surface as "see falsifiability F00X" cross-link instead
- Treat "we" / "in this paper" / "here" as scope qualifiers — they're authorial deictics, not scope statements

## Exit Conditions

- **PASS**: 0 Major. Every generalizing claim has either inline qualifier or limitation-section coverage.
- **Review required**: ≥1 Major. Surface unbounded claims for author attention.
