# Project 3: Disease Polygenicity and Therapeutic Diversity

## Research Question

**Is greater disease polygenicity associated with a broader set of successful therapeutic targets and mechanisms?**

> If a disease is influenced by many genetic variants, does treatment also require many distinct molecular targets, or do those genetic effects converge on a smaller number of druggable biological processes?

## Motivation

Complex diseases differ substantially in their genetic architecture. Some are influenced by relatively few loci, whereas others are highly polygenic, with genetic effects distributed across many variants and biological processes.

Successful treatments also differ in their biological breadth. Some diseases are treated through a small number of molecular targets, while others have approved therapies acting through many distinct targets and mechanisms.

This project asks whether the distribution of genetic causation is reflected in the structure of successful therapeutic intervention.

## Hypothesis

**More polygenic diseases will have approved therapies acting through a larger number of distinct molecular targets and mechanisms.**

An alternative outcome is equally informative: highly polygenic diseases may still be treatable through a small number of targets, suggesting that distributed genetic effects converge on **therapeutic bottlenecks**.

## Unit of Analysis

The unit of analysis is the **disease/indication**.

For each disease:

| Disease   | EUR Polygenicity | GWAS N | Approved Drugs | Unique Targets | Distinct Mechanisms |
| --------- | ---------------: | -----: | -------------: | -------------: | ------------------: |
| Disease A |                … |      … |              … |              … |                   … |
| Disease B |                … |      … |              … |              … |                   … |

## Genetic Exposure

### Disease Polygenicity

Use a continuous published estimate of polygenicity derived from European-ancestry GWAS.

Use one primary polygenicity resource and estimator across diseases to maintain comparability.

Record GWAS sample size and other relevant characteristics of the source GWAS because polygenicity estimates may be sensitive to statistical power.

## Therapeutic Outcomes

### Primary Outcome

**Number of unique approved molecular targets per disease.**

The focus is on distinct genes/proteins targeted by approved drugs rather than simply the number of drugs.

For example:

```text
Drug A → TNF
Drug B → TNF
Drug C → IL6R
Drug D → JAK1

4 drugs
3 unique therapeutic targets
```

### Secondary Outcomes

* Number of approved drugs.
* Number of distinct mechanisms of action.
* Diversity or concentration of therapeutic mechanisms.

The primary analysis will focus on approved therapies rather than experimental programs.

## Data Sources

### Polygenicity

A published cross-disease resource containing:

* disease/trait;
* European-ancestry polygenicity estimate;
* GWAS sample size;
* ideally uncertainty or standard error.

### Therapeutic Data

Open Targets / ChEMBL resources containing:

```text
disease → approved drug → molecular target → mechanism of action
```

Disease names and identifiers will be harmonized to a common ontology such as EFO or MONDO.

## Primary Analysis

Test whether diseases with greater polygenicity have more unique approved therapeutic targets.

```text
Unique approved targets ~ polygenicity + covariates
```

Because the outcome is a count, Poisson or negative binomial regression will be evaluated based on the dispersion.

The relationship should also be visualized directly, with each disease represented as one observation.

## Secondary Analysis

Ask whether polygenicity is associated with **therapeutic diversity**, rather than simply therapeutic volume.

For example, two diseases may each have 20 approved drugs:

```text
Disease A
20 drugs → 3 targets

Disease B
20 drugs → 12 targets
```

Disease B has a more distributed therapeutic architecture even though both diseases have the same number of drugs.

Mechanism-of-action diversity can provide an additional level of biological interpretation.

## Key Considerations

### GWAS Power

Polygenicity estimates can depend on the size and power of the underlying GWAS. GWAS sample size should therefore be recorded and evaluated as a potential confounder.

### Therapeutic Opportunity

Some diseases have had much more time, funding, and research devoted to drug development.

Potential controls include:

* number of drugs developed;
* research/publication attention;
* years since first approved therapy;
* disease prevalence or burden;
* therapeutic area.

### Disease Definition

Disease mappings must be consistent between the polygenicity and therapeutic datasets.

Broad ontology parents should not be allowed to double-count therapies belonging to their more specific disease subtypes.

### Therapeutic Area

The relationship should be tested with oncology excluded and through leave-one-therapeutic-area-out analyses to ensure that one disease class is not driving the overall result.

## Feasibility Test

Before building the full analysis:

1. Select one published European polygenicity resource.
2. Extract the diseases with usable estimates.
3. Map those diseases to Open Targets / ChEMBL.
4. Determine how many diseases have both:

   * a reliable polygenicity estimate; and
   * at least one approved therapy with a known molecular target.

This intersection is the effective sample size of the study.

## Interpretation

### Positive Association

If more polygenic diseases have therapies acting through more targets and mechanisms:

> Distributed genetic causation may translate into distributed therapeutic intervention.

### Little or No Association

If highly polygenic diseases are successfully treated through relatively few targets:

> Complex genetic causation may converge on a smaller set of biologically actionable therapeutic bottlenecks.

The project therefore addresses a broader question:

> **How does the architecture of genetic causation translate into the biology that medicine can successfully perturb?**

## Initial Scope

1. Identify and freeze one European polygenicity dataset.
2. Harmonize diseases with therapeutic indications.
3. Count approved drugs and unique molecular targets per disease.
4. Test the polygenicity–target relationship.
5. Adjust for major confounders.
6. Evaluate mechanism diversity as a secondary analysis.
7. Perform therapeutic-area robustness analyses.

### Out of Scope

* Running new GWAS.
* Estimating polygenicity from raw GWAS data.
* PPIN analysis.
* Ancestry comparisons.
* LLM or MCP analysis.
* Predicting individual drug approval.

## One-Sentence Summary

**Test whether diseases with more distributed genetic causation are treated through a broader set of molecular targets, or whether many genetic effects converge on a small number of druggable biological bottlenecks.**
