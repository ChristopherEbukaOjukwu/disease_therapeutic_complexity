# Project 3: Disease Polygenicity and Therapeutic Diversity

## Research Question

**Is greater disease polygenicity associated with a larger and more diverse set of successful therapeutic interventions?**

> If a disease is influenced by many genetic variants, does successful treatment tend to involve
> more distinct therapeutic targets or mechanisms — or do many genetic causes converge on a few
> druggable processes?

## Motivation

Complex diseases vary in genetic architecture: some are influenced by relatively few loci, others
are highly polygenic, with risk distributed across many variants and biological processes. Diseases
also differ in therapeutic landscape: some are treated through a few molecular targets, others
through many.

It is often assumed that biologically complex diseases require mechanistically diverse treatment.
It is unclear whether measured polygenicity actually tracks the therapeutic strategies that have
succeeded in humans. This project tests that relationship directly across diseases.

## Core Hypothesis (symmetric — both outcomes are informative)

**More polygenic diseases will have a larger and more diverse set of successful therapeutic targets
and mechanisms.**

The opposite result is equally informative: if highly polygenic diseases are treated through a small
number of targets, complex genetic architectures may converge on a limited set of therapeutically
actionable processes — **therapeutic bottlenecks**.

## Unit of Analysis

The **disease/indication**. For each disease, combine a polygenicity estimate with a profile of
successful therapeutic interventions.

| Disease | Polygenicity | GWAS N | Approved drugs | Unique targets | Distinct mechanisms |
|---|--:|--:|--:|--:|--:|
| Disease A | … | … | … | … | … |
| Disease B | … | … | … | … | … |

---

## The load-bearing weakness: the polygenicity axis

The x-axis is the fragile part of this study and must be treated as such, not as a settled input.

- **Method disagreement.** Published polygenicity estimates (e.g. SBayesS-style effect-distribution
  models, effective number of independently associated signals) disagree depending on method. Pick
  **one** primary resource and one estimator, pre-commit to it, and treat alternatives as a
  sensitivity analysis — do not silently mix estimates across methods.
- **Power coupling.** Measured polygenicity rises with GWAS sample size almost mechanically: bigger
  studies resolve more independent signals. Sample size tracks funding/attention, which tracks drug
  count. So polygenicity and target-count share a common cause **through the measurement of
  polygenicity itself**. This is why GWAS sample size is a mandatory covariate (below), not optional.
- **Exclude non-disease traits.** Quantitative traits with no therapeutic target (height, BMI,
  hair colour) must be dropped — they have no y-axis. Restrict to diseases/indications that can, in
  principle, be drugged.

### Kill-test (run before anything else)

How many diseases have **both** a published polygenicity estimate **and** a populated approved-drug
therapeutic profile after disease-ID harmonization? This intersection — likely tens, not hundreds —
is the real sample size and caps the entire study's power. Size it first.

---

## Genetic Exposure

**Disease polygenicity**, continuous (never binarized). Drawn from one pre-specified published
cross-disease resource. GWAS sample size for each disease recorded alongside it as a covariate.

## Therapeutic Outcomes

### Primary outcome

**Number of unique approved therapeutic targets per disease** (preferable to drug count, since
multiple drugs may share a target).

### Secondary outcomes

- Number of approved drugs.
- Number of distinct mechanisms of action.
- Diversity vs. concentration of therapeutic mechanisms.

Focus on **approved/successful** interventions, not all experimental programs.

### Mechanism grain must be pre-committed

"Distinct mechanism" is not self-defining — whether two drugs count as the same or different depends
on the grain: molecular target → mechanism-of-action string → pathway → therapeutic class. The grain
chosen largely determines the answer. Therefore:

- Pre-specify the primary grain (recommend **molecular target** for the primary outcome and
  **mechanism-of-action class** for the mechanism-diversity secondary).
- Report at least two grains; if the relationship holds at both, it is robust; if it flips, that is
  itself the finding.
- Treat the grain as a stated decision, not the database's default.

## Data Sources

### Polygenicity
One pre-specified published resource (chosen at the kill-test stage). Record its disease labels and
per-disease GWAS sample size.

### Therapeutic data
Open Targets / ChEMBL-type resources in the data catalog: `drug_mechanisms`, `drugs`,
`clinical_targets_diseases`, `clinical_indications`, `targets` + `targets_pathways`/`targets_classes`
for grain roll-up. These supply drug → target → mechanism → disease and clinical stage.

### Disease-ID harmonization (not a formality)
Polygenicity resources and Open Targets label diseases differently (ad hoc vs. EFO/MONDO). This
mapping step is where diseases silently drop or double-count, and it directly shrinks the already
small intersection. Harmonize to a single ontology (EFO/MONDO) and budget real time for it.

## Primary Analysis

```text
Unique approved targets ~ polygenicity + covariates
```

Count outcome → negative-binomial regression (check for over-dispersion; consider zero-inflation if
many diseases have zero approved targets). Report the polygenicity coefficient before and after
adding covariates.

## Covariates and Alternative Explanations

- **GWAS sample size** — mandatory; the direct driver of measured polygenicity (see above).
- Research/publication attention (publication or funding proxy per disease).
- Number of drugs developed for the disease.
- Years since first approved therapy (development runway).
- Disease prevalence/burden, if available.
- Therapeutic area.

Attention cannot be fully removed; the goal is to show the result is not *only* attention. Report the
slope with and without controls, and lean on confound-resistant signals (below).

### Oncology is a result, not just a covariate

Cancer has high target diversity, high measured polygenicity, and the most drugs — for reasons
(somatic biology, market size) unrelated to the hypothesis. If a positive slope is just "oncology in
the top-right and everything else clustered," there is no finding. **Show the relationship with
oncology excluded** (or at minimum that it survives exclusion). This robustness check is
credibility-critical.

### Confound-resistant reading

- **Off-diagonal diseases** — polygenic but few mechanisms (bottleneck), or modest polygenicity but
  many mechanisms — don't fit "more attention → more of both," so they carry the most weight.
- **Shape over slope** — a plateau (mechanisms rising then flattening as polygenicity climbs) is hard
  to explain by attention alone and is the signature of the bottleneck hypothesis.

## Secondary Question

**Does greater polygenicity correspond to greater diversity of therapeutic *mechanisms*, not merely
more drugs?** Distinguishes many drugs through one pathway from many genuinely distinct mechanisms.

## Interpretation

- **Positive association:** distributed genetic architecture has consequences for the structure of
  successful intervention — more polygenic disease, broader mechanistic treatment.
- **Little/no association (or plateau):** genetically complex diseases can be treated through few
  targets → therapeutic bottlenecks where many perturbations converge and can be drugged.

Either way:

> **Does the complexity of genetic disease causation constrain the complexity of successful
> therapeutic intervention?**

Association only — no causal claim; residual confounding stated as an honest limitation.

## Initial Scope / Order of Work

1. **Kill-test:** size the polygenicity × approved-drug intersection after harmonization.
2. Pick and freeze the polygenicity resource + estimator; record GWAS N per disease.
3. Harmonize disease IDs to EFO/MONDO.
4. Build disease-level counts of approved targets and mechanisms at two pre-committed grains.
5. Fit the primary target ~ polygenicity model with covariates (incl. GWAS N).
6. Oncology-excluded robustness; off-diagonal and shape inspection.
7. Mechanism-diversity secondary analysis.

### Out of scope
Running new GWAS; estimating polygenicity from raw summary statistics; PPINs; LLM analysis; MCP
development; ancestry analysis; predicting individual drug approval.

## One-Sentence Summary

**Test whether diseases with more distributed genetic causation also require a broader set of
therapeutic targets and mechanisms to treat successfully — or whether complex causation funnels
through a few druggable bottlenecks.**
