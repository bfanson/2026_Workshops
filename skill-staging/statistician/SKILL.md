---
name: statistician
description: >-
  Fit and compare GAM, GLMM, and HMM models for ecological movement data
  (e.g., acoustic telemetry of fish), evaluate model assumptions, and
  identify the best-supported model using AIC. Use when asked to analyse
  animal movement or telemetry data, build a candidate model set, run
  model diagnostics, or perform AIC-based model selection.
metadata:
  author: Arthur Rylah Institute
  version: "1.0"
compatibility: Requires R with mgcv, glmmTMB, moveHMM (or momentuHMM), DHARMa, and AICcmodavg.
---

# Statistical modelling of animal movement

Fit and compare models in three families — GAM, GLMM, and HMM — and identify
the best-supported model with AIC. Each family answers a different question
about the same data. The goal is a defensible comparison, not a
model-fitting contest.

## Step 1: Clarify the question and the data

Before writing any model code:

- Confirm the ecological question with the user: what is the response, and
  what is the unit of analysis (detection, step, individual, day)?
- Identify the data structure: detection histories, time step, covariates,
  number of tagged individuals, missingness, and deployment gaps.
- Run EDA first and show it to the user: detection timelines per individual,
  step-length and turn-angle distributions, covariate ranges, zero inflation.
- If the question, response definition, or candidate set is ambiguous, ask
  before fitting anything.

## Step 2: Assemble the candidate model set

Build a small, justified set — rarely more than about six models. Record a
one-line rationale for each candidate before fitting it.

| Family | Package | Question it answers |
|---|---|---|
| GAM | mgcv | How does the response vary smoothly over time, space, or environmental gradients? |
| GLMM | glmmTMB | Which fixed and random effects (individual, site, year) explain the response, accounting for correlation structure? |
| HMM | moveHMM / momentuHMM | What behavioural states (e.g. resident vs transient) structure the movement, and what drives transitions between them? |

Rules:

- Every candidate must be fit to the same response and the same data.
- Don't include a family that doesn't speak to the question.
- Don't fit every combination of covariates (no dredging).

## Step 3: Fit and check assumptions

- Fit each candidate and record convergence warnings and singularity issues.
- GAM/GLMM: check simulated residuals with DHARMa — uniformity, dispersion,
  zero inflation, and residual autocorrelation. For temporal data, inspect
  the ACF of residuals; add correlation structure or smoother terms if
  needed.
- HMM: check that the step-length and turn-angle distributions suit the
  data, that the number of states is justified, and that state assignments
  are not degenerate (nearly all one state).
- Assumptions come before AIC: a model with violated assumptions does not
  get to win on AIC alone — report the failure.

## Step 4: Compare with AIC

- Use AICc whenever the number of observations is not large relative to the
  number of parameters (a common case in telemetry).
- Only compare models fit to identical data and response.
- Produce a comparison table: family, formula, K, logLik, AICc, delta-AICc,
  Akaike weight.
- Interpret with care: models within delta-2 of the top model are effectively
  tied; weights are relative support, not the probability a model is true;
  cross-family comparisons (e.g. HMM vs GLMM) are descriptive, since the
  likelihoods are built from different data representations.

## Step 5: Report

- The model comparison table, plus a diagnostics summary for each family.
- Effect estimates with confidence intervals for the top model(s) — not just
  p-values.
- Key figures: raw data, fitted fits, residual checks, and (for HMM) state
  assignments over time.
- Reproducibility: report the random seed, R version, and sessionInfo().

## Guardrails

- Never fit all possible models; justify every candidate first.
- Ask the user when the question or data structure is unclear.
- Describe results with language proportional to the evidence.
