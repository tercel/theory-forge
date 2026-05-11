# Research Methodology Taxonomy

Reference for theory-forge sub-skills. Catalogs the six broad methodology types in academic research and their hallmarks. Used by `argument-structure` (to grade Toulmin completeness appropriate to each methodology), `falsifiability` (to set the right evidentiary standard), and `cite-audit` (to interpret citation context).

## The six methodology types

Adapted from Shadish, Cook & Campbell (2002) *Experimental and Quasi-Experimental Designs* and Creswell (2014) *Research Design*.

### 1. Formal / Theoretical

The work develops a formal model, axiomatic system, or mathematical framework. Empirical data is not the primary evidence; mathematical / logical consistency and predictive power are.

**Hallmarks:**
- Definitions, axioms, theorems, proofs
- Formal notation (logic, math, type theory)
- Predictive claims framed as derivations from the formal apparatus

**CFLT example:** `core-concept.md` §9 Formal Definition; `logic.md` lambda-nesting argument; `mathematics.md` four-element search-space derivation

**Falsifiability standard:**
- Internal: prove the system is consistent / non-trivial
- External: derive predictions that diverge from rival theories

**Argument-structure standard:** full Toulmin elements with proofs as Backing

### 2. Experimental

The work establishes causal claims via controlled manipulation. Hallmarks: independent variable manipulated; dependent variable measured; random assignment or matched comparison; control / baseline group.

**Hallmarks:**
- Between-subjects or within-subjects design
- Independent variable + dependent variable + controls
- Statistical analysis with effect sizes and confidence intervals

**CFLT example:** §8.5 P1 prediction (CFLT-trained vs free-form-instructed L2 learners)

**Falsifiability standard:**
- Pre-registration or transparent reporting
- Effect size + confidence interval
- Replication condition stated

**Argument-structure standard:** Claim + Data (statistical) + Warrant (causal inference) + Qualifier (population) + Rebuttal (alternative explanations)

### 3. Observational / Quasi-Experimental

Same as experimental but without random assignment. The work establishes associations or naturally-occurring contrasts.

**Hallmarks:**
- Natural groups (e.g., L1-Mandarin vs L1-Spanish L2-English learners)
- Confound control via statistical adjustment, not randomization
- "Quasi-experimental" qualifier

**Falsifiability standard:** as experimental, with additional concern about unmeasured confounds

**Argument-structure standard:** as experimental, with stronger requirement for Rebuttal (alternative explanations for the observed association)

### 4. Meta-analytical / Systematic Review

The work synthesizes prior empirical findings. Hallmarks: systematic search protocol; inclusion / exclusion criteria; risk-of-bias assessment; quantitative or qualitative synthesis.

**Hallmarks:**
- PRISMA flow diagram
- Risk-of-bias table per included study
- Forest plots / synthesis summaries

**CFLT example:** none currently — would be the methodology if CFLT included a systematic review of cross-linguistic word-order data

**Falsifiability standard:** transparent search criteria; replicable retrieval

**Argument-structure standard:** Claim + Data (synthesized findings) + Warrant (synthesis method) + Rebuttal (heterogeneity / publication bias)

### 5. Qualitative / Interpretive

The work develops categories, themes, or interpretations from interviews, ethnographic observation, or text analysis. Hallmarks: coding schemes; inter-coder agreement; theoretical sampling.

**Hallmarks:**
- Coding categories developed (grounded theory or thematic)
- Sample described in terms of theoretical relevance, not statistical representativeness
- Transparent positionality / reflexivity statement

**Falsifiability standard:** softer — but transparent coding decisions and reflexivity matter

**Argument-structure standard:** Claim + Data (excerpted quotations / observations) + Warrant (coding-scheme logic) + Qualifier (sample's theoretical scope)

### 6. Historical / Documentary

The work reconstructs and interprets historical events, intellectual lineages, or document trajectories. Hallmarks: primary-source citations; chronological argument; contextual situating.

**Hallmarks:**
- Primary vs secondary source distinction
- Chronological framework
- Historiographical positioning (which historians' approach the work follows)

**Falsifiability standard:** soft, but transparent source-base required

**Argument-structure standard:** Claim + Data (primary sources) + Warrant (historiographical interpretation) + Rebuttal (alternative interpretations)

## How sub-skills use the taxonomy

| Skill | How it uses methodology type |
|---|---|
| `argument-structure` | Tunes Toulmin-completeness expectations per type. Formal/theoretical work needs proofs as Backing. Experimental work needs effect-size + CI as Data. |
| `falsifiability` | Sets the appropriate falsifiability standard. Formal work falsifies via consistency / contradiction; experimental work via statistical refutation. |
| `cite-audit` | Interprets citation context. A citation in a meta-analytical study should match the included-study list. A citation in a theoretical paper should match the chain-of-derivation. |
| `scope` | Type-aware scope expectations. Experimental work needs population specification; formal work needs domain of definition. |
| `counter-argument` | Different methodology types have different canonical opponents. Experimental: alternative-explanation engagement. Formal: rival formalisms. |

## Self-classification by document

theory-forge does not auto-classify; the document author should declare the methodology type. Detection signals (for a `--detect-methodology` flag in a future release):

| Signal | Likely type |
|---|---|
| Heavy formal notation, theorems, derivations | Formal / theoretical |
| Statistical results, effect sizes, p-values | Experimental or observational |
| PRISMA, included-study tables | Meta-analytical |
| Quoted interview excerpts, coding schemes | Qualitative |
| Primary-source archival citations, chronology | Historical |

A document can be mixed-methodology (CFLT is **formal-theoretical + speculative-empirical**, where §8.5 P1–P3 sketch experimental designs that have not yet been run). Mixed-methodology documents should declare the mix and apply the appropriate standard to each part.

## See Also

- `argument-patterns.md` — Toulmin elements per methodology type
- `falsifiability-template.md` — Type A/B/C/D/E classification with methodology context
- `replication-and-preregistration.md` — additional requirements for experimental / observational methods
