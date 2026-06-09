# Evidence-Status Ladder: Claim-vs-Source Support Strength

Reference for the `evidence-strength` sub-skill. Codifies the relation between a
document's **claim** and the **source** cited to support it — how strong the
support actually is, and what remediation that calls for (including, at the
bottom rung, removing the citation).

## The gap this fills

`cite-audit` answers *"does the cited paper exist and is it attributed to the
right author?"* `falsifiability` answers *"is this claim marked as verified,
normative, definitional, or hypothesis?"* Neither answers the question this
ladder is built for:

> **Does the source actually establish what the claim asserts — and at the same
> strength and scope?**

A citation can pass `cite-audit` (the paper is real, the author/year is right)
and pass `falsifiability` (the claim has a citation, so it reads as Type A
"verified") while still being an over-leverage of the source: the paper shows a
*correlation* and the text claims a *mechanism*; the paper shows an effect *in
English* and the text claims it *cross-linguistically*; the paper is *cited as
motivation* but read as *direct evidence*. That gap is invisible to
keyword-overlap heuristics because the topics align — only reading what the
source actually concludes surfaces it.

## Self-declaration: this framework is theory-forge's own synthesis

The seven-level ladder below is theory-forge's operationalization of the
evidence-status vocabulary used in source-by-source citation audits of
theory documents. *[Type B — theory-forge design choice]*. It is not imported
from a single published source; it codifies a practice (compare each claim
against what its cited source genuinely supports) that careful reviewers apply
by hand.

## The seven evidence-status verdicts

For each claim that cites a source (or asserts a quantitative / mechanistic
result that *implies* external evidential support), assign exactly one verdict.

| Verdict | What it means | Default severity |
|---|---|---|
| **accurate** | The claim is within the scope and strength of what the source establishes. | OK |
| **accurate-with-caveat** | Basically right, but the source's scope is narrower than the claim's phrasing implies; a qualifier is needed. | Minor |
| **overstated** | The source supports a *weaker* version of the claim. The direction is right; the strength/scope is inflated (universality, causality, effect size, or mechanism asserted beyond the source). | Major if central; Minor otherwise |
| **indirect** | The source is genuine **motivation, analogy, or background** — not direct evidence for *this* claim — but the text reads it as direct support. | Major if load-bearing; Minor otherwise |
| **misattributed** | The source does not provide the method or result attributed to it here. (The paper may be real and valid elsewhere; it just doesn't say *this*.) | Major |
| **counterevidence** | The source actually **constrains or contradicts** the claim, yet is cited as if it supports it. The most misleading case — the reader infers scholarship backs a claim that scholarship opposes. | Critical if central; Major otherwise |
| **unsupported** | No source supports the claim. Either it cites nothing (a bare quantitative / mechanistic assertion) or the cited source provides zero support. | Major |

## Two axes that decide the verdict

Read the source's actual conclusion, then compare against the claim on:

1. **Direction** — does the source *support*, *not address*, or *contradict* the
   claim?
   - supports → accurate / accurate-with-caveat / overstated / indirect
   - does not address → misattributed / unsupported
   - contradicts → counterevidence
2. **Strength / scope** — does the source establish *as much as* the claim
   asserts? Check each dimension the claim invokes:
   - **Universality** — claim says "all/every/across languages"; source surveys a
     bounded sample → overstated (forward to `cross-lang` / `scope`).
   - **Causality** — claim says "X causes / reduces / improves Y"; source shows a
     correlation, a visualization, or a descriptive pattern → overstated (often
     an evidence-layer jump; see below).
   - **Effect size** — claim names a number (2×, 30–50%, ~100%); source gives no
     such figure, or a different one → overstated or unsupported.
   - **Mechanism** — claim explains *why* via an internal mechanism; source only
     reports a behavior → overstated / indirect.
   - **Cross-domain transfer** — claim infers a *shared mechanism* across domains
     (e.g., humans and LLMs) from a *shared behavior*; source is single-domain →
     overstated / indirect.

## Evidence registers (what the source IS to the claim)

The remediation depends on what role the source genuinely plays. Distinguish
five registers — the same vocabulary a careful manual revision uses:

| Register | The source is… | Honest phrasing |
|---|---|---|
| **source result** | direct evidence for the claim | "X (Author Year) shows…" |
| **analogy / motivation** | a reason to find the claim plausible, not evidence | "motivated by / by analogy with X (Author Year)" |
| **definition** | the document's own stipulation (out of scope here) | "we define…" → defer to `falsifiability` Type C |
| **prediction** | the document's own untested hypothesis (out of scope here) | "we hypothesize…" → defer to `falsifiability` Type E |
| **measured result** | the document's *own* completed experiment | "our study (§N) found…" |

`evidence-strength` audits claims in the **source result** and
**analogy/motivation** registers (and bare quantitative claims that imply one of
them). Definition / prediction / measured-result claims are the territory of
`falsifiability` and `consistency` — cross-reference, do not duplicate.

## The evidence-layer ladder (for mechanism / causal claims)

When a claim is about an internal mechanism, locate the source on this ladder.
A claim may not sit higher than its source establishes.

1. **Behavioral** — an observed input→output pattern (e.g., positional accuracy
   dips in long context).
2. **Descriptive-internal** — a visualization or correlational read of internals
   (attention maps, rollout) — *descriptive, not causal*.
3. **Causal** — an intervention (ablation, activation patching, causal tracing)
   that establishes a mechanism.
4. **Domain-specific** — the mechanism holds for *this* theory's construct,
   tested directly.

A claim that cites a Layer-1 or Layer-2 source for a Layer-3/4 conclusion ("the
attention visualization shows the model preserves the Core") is **overstated** —
the honest move is to label the source descriptive and route the causal claim to
intervention-based methods.

## Remediation ladder (where the remove-recommendation lives)

Each verdict maps to a remediation. **Removing the reference is the bottom rung,
not the default** — most weak-support cases call for weakening the *claim*, not
deleting the *source*, because the source legitimately supports the weaker claim.

| Verdict | Recommended remediation | Touches the citation? |
|---|---|---|
| accurate | none | no |
| accurate-with-caveat | add a scope qualifier to the **claim** | keep citation |
| overstated | **downgrade the claim** to a scoped statement or a marked prediction (route marking to `falsifiability`) | **keep** — it supports the weaker claim |
| indirect | reword as "motivated by / by analogy with"; keep the source as motivation | **keep** as motivation |
| counterevidence | reframe the source as a **constraint / scope condition**; stop citing it as support | **keep, flip framing** |
| misattributed | repair the attribution, or move the citation to the claim it *does* support; if it supports nothing at this site, **remove the inline citation here** | remove **the inline attribution at this site** |
| unsupported | **suggest removing the citation**, and either drop the claim or mark it a hypothesis (`falsifiability` Type E) | **removal candidate** |

## The removal gate (safety discipline)

A "suggest removing the reference" recommendation is consequential — a deleted
citation can destroy a legitimate reference. It is therefore gated exactly like
`cite-audit`'s mis-attribution policy: **propose-only, never auto-applied.**

1. **Removal fires only for `unsupported` and site-level `misattributed`.** Never
   for overstated / indirect / accurate-with-caveat / counterevidence — those
   keep the citation and change the claim or the framing. Recommending removal
   for an over-leveraged-but-valid citation is itself an error.
2. **Corroboration required.** A removal recommendation requires that the source
   was *actually checked* (abstract or full text read, or confirmed not to exist)
   and found to provide no support. A low automatic alignment score **alone** is
   never sufficient — those go to a human-review queue, not the removal list.
3. **Load-bearing warning.** If the citation is the *sole* support for a central
   claim, do not glibly say "remove citation." Surface: *"removing this would
   leave the central claim unsupported — decide whether the claim itself should
   be downgraded (→ prediction) or dropped."* The decision is the author's.
4. **Classic / methodology downweight.** A work cited 5+ times across the project,
   or cited as the source of a *method* rather than a result, is not a removal
   candidate from one weak site — it is used in many contexts. Downweight to a
   review note.
5. **Two-stage bibliography prune.** Removing an inline citation does not
   automatically remove its **bibliography entry**. If removal would leave the
   entry with zero remaining inline uses, raise that as a *separate* unused-entry
   suggestion and route it to `cite-audit`'s "unused" check — never prune the
   bibliography in the same step.
6. **Never auto-apply.** Removal recommendations are shown as diffs and applied
   only on explicit per-item confirmation. The skill never deletes a citation the
   user did not approve.

## Severity mapping summary

| Verdict | Central claim | Peripheral claim |
|---|---|---|
| accurate | OK | OK |
| accurate-with-caveat | Minor | Minor |
| overstated | **Major** | Minor |
| indirect | **Major** (load-bearing) | Minor |
| misattributed | **Major** | Major |
| counterevidence | **Critical** | Major |
| unsupported | **Major** | Major |

`Critical` and `Major` findings must carry a concrete remediation per
`academic-severity-levels.md`. Apply charitable reading: when a claim could be
read as accurate-with-caveat or overstated, prefer the lower severity unless the
source clearly fails to reach the claim's strength.

## See Also

- `falsifiability-template.md` — Type A/B/C/D/E (this skill audits A and the
  evidence-leaning subset of D; defers B/C/E)
- `argument-patterns.md` §2.1 (affirming the consequent), §2.5 (scope creep) —
  fallacies that often co-occur with `overstated`
- `cite-audit/SKILL.md` §6 — keyword-alignment mis-attribution (coarse screen;
  this ladder is the deep read) and §4 unused-entry check (two-stage prune)
- `cross-disciplinary-import-rules.md` — when over-leverage is a borrowed-term
  authority transfer, cross-reference `concept-import`
