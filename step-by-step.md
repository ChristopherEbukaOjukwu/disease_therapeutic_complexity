# Project 3: Step-by-Step Analysis Plan

## Phase 1 — Data Validity Proof of Concept

Goal is not the biological hypothesis yet. It is to prove we can cleanly measure three things:
polygenicity, indication-specific approved therapies, and their unique molecular targets. If any is
dirty, the project stops.

### Step 1. Identify candidate polygenicity resources
Find published disease-level polygenicity estimates. Requirements: European ancestry; one estimator
across diseases; disease label; GWAS N; ideally SE; enough real diseases, not mostly quantitative
traits. Compare candidates, but the primary analysis uses one pre-specified resource/estimator.

Record per resource: `disease_name, population, polygenicity, polygenicity_se, gwas_n, method, source`.

### Step 2. Size the polygenicity disease universe
Per resource: keep European estimates, drop non-disease traits (height, hair colour, educational
attainment, blood-cell measures), count remaining diseases. This is the maximum possible N before
therapeutic matching.

### Step 3. Validate on one easy disease — with a pre-registered expectation
Use **rheumatoid arthritis**. Before pulling anything, **write down the expected target classes**
(~TNF, IL6R, JAK, CD20, CTLA4, IL1 — roughly 6). Step 7 tests the extraction against *this* written
expectation, not against whatever the database happens to return — otherwise the validation validates
nothing.

### Step 4. Direct indications only
Retrieve drug–disease relationships for the validation disease using **direct indication-specific**
links. Do **not** use ontology-propagated associations (RA → autoimmune → inflammatory). Keep:
`disease_id, drug_id, drug_name, clinical_stage, indication_source, direct_indication`.

### Step 5. Indication-specific approval — decide the ambiguous-fraction rule NOW
Confirm approval is attached to `drug + this indication`, not `drug approved somewhere`. Expect
~15–20% of drugs to be ambiguous in the data — this will not resolve to one clean universal rule, so
pre-commit both:
- **Conservative:** require explicit approved-indication encoding.
- **Inclusive:** max_phase 4 for that drug–indication pair.

Run the pilot **both ways**. If target counts and the eventual slope are stable across rules, the
ambiguity is harmless (and you've pre-empted the reviewer). If not, that instability is a *finding* to
report, not bury. Stop only if approval cannot be determined at all.

### Step 6. Link approved drugs to molecular targets
Per approved drug, human target(s): `disease_id, drug_id, drug_name, target_id, target_gene,
target_protein, action_type, mechanism_of_action`.

### Step 7. Validate target count against the Step-3 expectation
Count approved drugs, count unique human targets, inspect the list, compare to the pre-registered RA
expectation. Confirms `n_unique_targets` is meaningful.

### Step 8. Repeat on an awkward disease — and don't "fix" the result
Repeat 4–7 on **schizophrenia**. Expect few clean targets: most approved drugs are old dirty-D2
polypharmacology compounds, which Open Targets may map to one canonical target or a smear. Few unique
targets is **not** an extraction failure — it may be the actual bottleneck signal (massive
polygenicity funnelling through D2). Decide here how historical dirty drugs vs. modern clean-target
biologics are handled, because that choice systematically shapes the high-polygenicity
(psychiatric/neurological) end of the x-axis that drives the slope.

### Step 9. Disease-grain compatibility
GWAS phenotype and therapeutic indication must be the same entity. Flag mismatches
(`exact / close synonym / parent-child mismatch / ambiguous / unresolved`). Do not fold child
indications into a broad GWAS phenotype. Primary analysis favours exact/equivalent mappings.

### Step 10. Kill-test + GO/NO-GO
Intersect: European polygenicity ∩ clean mapping ∩ indication-specific approval ∩ known human target.
Count survivors — this is the effective N, and it sets the covariate budget and whether therapeutic-
area sensitivity is even possible.
**Proceed if:** extraction behaves; mappings defensible; target counts vary; N large enough.
A visible association is *not* required — clear high-polygenicity/low-target diseases are enough.
**Stop/redesign if** usable N is too small.

---

## Phase 2 — Biological Proof of Concept

### Step 11. Freeze the primary polygenicity resource
One resource, one estimator, European ancestry, one inclusion rule. Document `source, method,
ancestry, GWAS cohort, GWAS N, SNP universe, polygenicity definition`. No mixing estimators in the
primary analysis; alternatives are sensitivity only.

### Step 12. Build a 10–20 disease pilot
Span low/intermediate/high polygenicity and multiple areas. Do **not** cherry-pick drug-rich diseases
— that makes GO/NO-GO too optimistic. Deliberately include schizophrenia and one sparse-development
disease.

### Step 13. Pilot crosswalk
`polygenicity_name, polygenicity_source_id, standard_disease_id, standard_disease_name,
open_targets_id, mapping_type, mapping_confidence, mapping_notes`. EFO/MONDO where possible; retain
source labels.

### Step 14. Extract pilot therapies
Apply Phase 1 rules: direct indication + indication-specific approval + known human target.

### Step 15. Pilot therapeutic measures
`n_approved_drugs, n_unique_approved_targets, n_unique_mechanisms`. Primary: `n_unique_approved_targets`.

### Step 16. First disease-level table

| Disease | EUR Polygenicity | GWAS N | Approved Drugs | Unique Targets | Distinct Mechanisms |
|---|--:|--:|--:|--:|--:|
| A | … | … | … | … | … |

### Step 17. First scatterplot
`x = polygenicity, y = unique approved targets`, one point per disease. Inspect only — spread in x,
spread in y, most diseases stuck at 1–2 targets?, oncology dominating?, off-diagonal cases?

### Step 18. Biological GO/NO-GO
Proceed if the relationship is measurable, the outcome varies, no single area dominates, and the full
build looks worthwhile. A visible association is not required; clear high-polygenicity/low-target
diseases suffice.

---

## Phase 3 — Full Disease Universe

### Step 19. Full polygenicity table
`disease_name_original, standard_disease_name, standard_disease_id, polygenicity, polygenicity_se,
gwas_n, heritability_if_available, source`.

### Step 20. Harmonize all disease identities
Permanent crosswalk; favour high-confidence mappings.

### Step 21. Fix disease granularity
Same biological grain on both axes. Don't auto-combine cancer subtypes or IBD/Crohn/UC. Exclude
incompatible mappings rather than forcing them.

---

## Phase 4 — Full Therapeutic Dataset

### Step 22. Indication-specific approved drugs
`disease_id, drug_id, drug_name, approval_status, clinical_stage, approval_source`. Direct indications
only.

### Step 23. Link drugs to targets
`disease_id, drug_id, target_id, target_gene, target_type, action_type, mechanism_of_action`.

### Step 24. Define eligible targets
Human gene/protein targets primary. Pre-specify handling of protein complexes, multi-subunit
receptors, pathogen/non-protein/unknown/nonspecific targets. Excluded records → audit table.

### Step 25. Deduplicate
Collapse duplicates from repeated sources, synonyms, duplicated indications, multiple mechanism
records, ontology propagation. Atomic record: `disease × drug × target`.

### Step 26. Disease-level outcomes
Primary `n_unique_approved_targets`; secondary `n_approved_drugs, n_unique_mechanisms`.

---

## Phase 5 — Therapeutic Diversity

### Step 27. Volume vs. diversity
Volume = number of approved drugs. Diversity = how broadly drugs spread across targets. Keep separate.

### Step 28. Target concentration — pre-committed metric
Primary: **unique target count**. Single concentration measure: **normalized entropy** (behaves better
than Herfindahl at the small target counts most diseases will have). Pre-commit now — do not leave five
options open to "choose before modeling."

### Step 29. Mechanism diversity
Roll targets up to mechanism-of-action classes. Primary grain: molecular target/gene/protein.
Secondary grain: mechanism class. Report both.

---

## Phase 6 — Confounders

### Step 30. GWAS sample size — with a stated collinearity rule
Record `gwas_n`; inspect `polygenicity ~ gwas_n` first. **If polygenicity and GWAS N are strongly
collinear** (likely — both track how studied a disease is), you *cannot* cleanly separate their
effects no matter the model. In that case report the raw correlation and state the partial confounding
honestly rather than claiming the covariate isolates polygenicity. Decide this before Step 40.

### Step 31. Research attention
Disease-level proxy: publication count (simplest), citations, or funding.

### Step 32. Development opportunity
`n drugs ever developed, years since first development, years since first approval`.

### Step 33. Disease burden (only if clean)
Prevalence/incidence/burden — skip if harmonization is poor; do not let it become its own project.

### Step 34. Therapeutic area
oncology / psychiatric / neurological / cardiovascular / metabolic / immune-inflammatory / respiratory
/ infectious / other.

### Step 35. Covariate budget from actual N
No kitchen-sink model. At tens of diseases: simple model first, one covariate at a time, or a single
composite "research/development opportunity" axis. Attention, drug count, development opportunity, and
GWAS N are mutually collinear — don't interpret them jointly.

---

## Phase 7 — Analysis Dataset

### Step 36. One row per disease
`disease_id, disease_name, polygenicity, polygenicity_se, gwas_n, n_approved_drugs,
n_unique_approved_targets, n_unique_mechanisms, target_diversity, mechanism_diversity,
research_attention, development_opportunity, disease_burden, therapeutic_area`.

### Step 37. Describe the universe
Total diseases; filtering flow (losses per step); polygenicity range; GWAS N; drug/target
distributions; area composition; mapping success/exclusions.

---

## Phase 8 — Primary Analysis

### Step 38. Unadjusted
`unique approved targets ~ polygenicity`, plus the scatterplot.

### Step 39. Count model
Check dispersion → Poisson if adequate, else negative-binomial. Zero-inflation not default (population
has approved therapies).

### Step 40. GWAS power
Compare with/without `+ GWAS N`; see Step 30's collinearity rule.

### Step 41. Development opportunity
Does the relationship survive reasonable adjustment? Avoid overfitting.

### Step 42. Research attention
Is it explained entirely by how studied the disease is? Keep sensitivity models simple.

---

## Phase 9 — Strongest Test: Diversity Conditional on Volume

### Step 43. The stronger question
Among diseases with comparable drug counts: are therapies for more polygenic diseases spread across
more targets? `target diversity ~ polygenicity + approved drug volume`. Separates "more drugs" from
"more distinct biology targeted." Likely the strongest analysis in the paper.

### Step 44. Mechanism level
Repeat at mechanism-of-action class; compare with target-level.

---

## Phase 10 — Bottlenecks

### Step 45. Off-diagonal diseases
High-poly/few-targets (bottleneck), high-poly/many-targets (distributed), low-poly/many-targets
(counterexample).

### Step 46. Nonlinearity
Test continuous rise / plateau / threshold / none. A plateau supports convergence onto a finite set of
actionable processes.

---

## Phase 11 — Robustness

### Step 47. Exclude oncology
Repeat primary analyses without oncology; if the association disappears, report it directly.

### Step 48. Leave-one-area-out
Remove each major area in turn; result must not hinge on one class.

### Step 49. Alternative polygenicity resource
Sensitivity only, kept out of the primary model; compare rankings/conclusions.

### Step 50. Alternative therapeutic definitions
unique targets / target diversity / mechanism diversity / concentration — does the conclusion depend on
outcome choice?

### Step 51. Mapping sensitivity
Exact/high-confidence mappings only vs. broader set.

---

## Phase 12 — Interpretation

### Step 52. Bottleneck diseases
High-poly/few-targets: which targets dominate, do many drugs hit one protein, same pathway, one
mechanism?

### Step 53. Distributed diseases
High-poly/many-targets: biologically diverse targets, distinct pathways, multiple mechanisms
independently druggable?

### Step 54. Compare
Central result: distributed genetic causation → distributed intervention, or convergence onto
therapeutic bottlenecks?

---

## Phase 13 — Figures

1. **Design** — polygenicity + indication-specific approved drug-target data → one row per disease →
   genetic ↔ therapeutic architecture.
2. **Universe** — distributions of polygenicity, approved drugs, unique targets, areas.
3. **Primary** — polygenicity vs unique approved targets.
4. **Diversity** — polygenicity vs target/mechanism diversity, conditional on volume.
5. **Extremes** — high-poly/many-targets vs high-poly/few-targets.

---

## Order of Work

```text
1  candidate polygenicity resources
2  count usable EUR disease phenotypes
3  validate RA (with pre-registered expected targets)
4  direct indications only
5  indication-specific approval (both rules)
6  target extraction
7  validate count vs expectation
8  awkward disease (schizophrenia)
9  grain compatibility
10 intersection count → GO/NO-GO
11 pilot (10–20 diseases)
12 first table → 13 first scatterplot
14 full universe → 15 crosswalk → 16 full therapeutic extraction
17 diversity metrics → 18 confounders
19 primary models → 20 diversity conditional on volume
21 area robustness → 22 bottleneck case studies → 23 write
```

## First Immediate Task

1. Find the best European polygenicity resource; count its disease phenotypes.
2. In parallel, prove on RA that the therapeutic data recovers direct, indication-specific approved
   drugs and their unique human targets **without ontology propagation** — checked against a
   pre-registered expected target list.

Those two determine whether the project scales.
