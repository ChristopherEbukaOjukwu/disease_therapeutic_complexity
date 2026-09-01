# Project 3: Analysis Plan

## Research Question

**Do diseases with greater polygenicity have successful therapies targeting a broader set of genes/proteins and biological mechanisms?**

Alternatively, do many distributed genetic effects converge on a small number of **therapeutic bottlenecks**?

---

# Phase 1 — Feasibility / Proof of Concept

The goal is to determine whether the two sides of the study can be measured cleanly before building the full dataset.

## Step 1. Choose a polygenicity resource

Identify a published resource with:

* European-ancestry polygenicity estimates
* multiple diseases
* one consistent estimator
* GWAS sample size
* ideally uncertainty/SE

Keep disease traits only.

**Output:** one table with:

```text
disease
polygenicity
gwas_n
polygenicity_se
```

## Step 2. Count the usable diseases

Determine how many diseases have European polygenicity estimates.

This gives the maximum possible sample size.

## Step 3. Validate the therapeutic data on rheumatoid arthritis

For RA:

1. Retrieve **direct disease indications only**.
2. Confirm that approval is specific to RA, not merely that the drug is approved somewhere.
3. Link approved drugs to their human gene/protein targets.
4. Manually inspect whether the resulting drug and target list makes biological sense.

This establishes that:

```text
disease → approved drugs → unique targets
```

can be measured reliably.

## Step 4. Repeat with schizophrenia

Repeat the same extraction for a more difficult disease.

This tests whether the method works outside diseases with clean biologic therapies.

## Step 5. Check disease matching

The GWAS phenotype and therapeutic indication must describe the same disease.

Classify mappings as:

```text
exact
close synonym
mismatch
ambiguous
```

Do not force broad and narrow phenotypes together.

## Step 6. Run the kill-test

Intersect:

```text
EUR polygenicity
      ∩
clean disease mapping
      ∩
indication-specific approved drugs
      ∩
known human targets
```

Count the surviving diseases.

### GO / NO-GO

Proceed if:

* disease mappings are reliable;
* indication-specific approval can be determined;
* therapeutic target counts vary;
* enough diseases remain for analysis.

---

# Phase 2 — Build the Study Dataset

## Step 7. Freeze the polygenicity dataset

Choose one primary:

* resource
* estimator
* ancestry: European
* disease inclusion rule

Do not mix polygenicity estimators in the primary analysis.

## Step 8. Harmonize diseases

Create a permanent disease crosswalk:

```text
polygenicity disease
standard disease ID/name
Open Targets disease ID
mapping quality
```

Use a consistent disease ontology such as EFO/MONDO.

## Step 9. Build the therapeutic dataset

For every eligible disease, retrieve:

```text
disease
approved drug
target gene/protein
mechanism of action
```

Rules:

* direct indication only;
* indication-specific approval;
* human molecular targets;
* remove duplicate drug–target records.

## Step 10. Create one row per disease

Final core table:

| Disease   | Polygenicity | GWAS N | Approved Drugs | Unique Targets | Distinct Mechanisms |
| --------- | -----------: | -----: | -------------: | -------------: | ------------------: |
| Disease A |            … |      … |              … |              … |                   … |
| Disease B |            … |      … |              … |              … |                   … |

Primary therapeutic outcome:

**Number of unique approved molecular targets.**

Secondary outcomes:

* number of approved drugs;
* number of mechanisms;
* target/mechanism diversity.

---

# Phase 3 — Main Analysis

## Step 11. Look at the raw relationship

Plot:

```text
x = disease polygenicity
y = number of unique approved targets
```

One point = one disease.

Ask:

> Do more polygenic diseases tend to have more therapeutic targets?

## Step 12. Fit the primary model

Test:

```text
unique approved targets ~ polygenicity
```

Use Poisson or negative-binomial regression depending on the count distribution.

## Step 13. Test the stronger diversity question

Drug count and target count must be separated.

For example:

```text
Disease A:
20 drugs → 3 targets

Disease B:
20 drugs → 12 targets
```

The stronger question is:

> Among diseases with similar numbers of approved drugs, are therapies for more polygenic diseases distributed across more targets?

Conceptually:

```text
target diversity ~ polygenicity + approved drug volume
```

Repeat at the mechanism-of-action level.

## Step 14. Check major alternative explanations

Important variables include:

* GWAS sample size;
* research attention;
* therapeutic development opportunity;
* therapeutic area.

Keep models simple. The number of diseases determines how many covariates can reasonably be included.

Important robustness checks:

* exclude oncology;
* leave one therapeutic area out at a time;
* use only high-confidence disease mappings.

---

# Phase 4 — Biological Interpretation

## Step 15. Identify the interesting diseases

Look especially for:

### High polygenicity + many targets

Suggests:

**distributed genetic causation → distributed therapeutic intervention**

### High polygenicity + few targets

Suggests:

**therapeutic bottlenecks**

Many genetic effects may converge on a small number of biological processes that can be successfully drugged.

These diseases can then be examined as biological case studies.

---

# Main Outputs

### Figure 1

Study design and disease filtering.

### Figure 2

Polygenicity vs. number of unique approved targets.

### Figure 3

Polygenicity vs. therapeutic target diversity after accounting for drug volume.

### Figure 4

Results by therapeutic area / robustness analyses.

### Figure 5

Examples of distributed treatment versus therapeutic bottlenecks.

---

# Immediate Next Steps

Do only these first:

1. **Find the best European polygenicity dataset.**
2. **Count how many actual diseases it contains.**
3. **Use rheumatoid arthritis to validate direct, indication-specific approved drugs and their human targets.**
4. **Repeat the validation with schizophrenia.**
5. **Intersect the polygenicity and therapeutic disease sets and determine the real sample size.**

Only after those five steps pass do we build the full project.
