# 10 — Statistics, Confidence and Experiment Design

## Why a score needs context

Suppose a new prompt improves evaluation accuracy from 82% to 86% on 25 examples. That may look useful, but a small dataset can produce unstable estimates. The apparent improvement may not hold for new users or different input types.

Evaluation needs careful experiment design and interpretation.

## 1. Sample size and uncertainty

A measured score is an estimate based on sampled examples. More representative cases generally reduce sampling uncertainty, but dataset quality matters just as much as size.

Report:
- Number of examples.
- Dataset version and sampling method.
- Score and per-category breakdown.
- Confidence interval or uncertainty estimate where appropriate.
- Model and prompt versions.
- Known limitations.

## 2. Paired comparisons

When comparing prompt A and prompt B, run both on the same test cases. This makes it easier to see which cases improved or regressed. Do not compare one random set for A with a different set for B and assume the difference is caused by the prompt.

## 3. Confidence intervals

A confidence interval communicates uncertainty around a measured statistic. The method depends on the metric and sampling assumptions. For binary success rates, binomial intervals may be suitable. For paired differences, use a method that preserves pairing.

Do not claim statistical significance from a raw percentage change alone.

## 4. Segment the results

Overall averages can hide a regression for a particular language, task, tenant, or risk category. Break results down by meaningful cohorts, but avoid over-interpreting tiny groups.

## 5. Controlled experiments

Change one major factor at a time where possible:
- Model version.
- Prompt version.
- Retrieval top-k.
- Chunking.
- Generation settings.
- Tool schema or routing.

Record the hypothesis, experiment plan, acceptance criteria, and rollback threshold before running the experiment.

## Practice exercise

Compare two prompt versions on the same 50 synthetic cases. Report total scores, per-category results, paired wins/losses/ties, and uncertainty limitations. State what additional evidence would be needed before a production rollout.

## Self-check

- Why is an 86% score on 25 examples uncertain?
- Why use paired test cases when comparing prompts?
- What does a confidence interval describe?
- How can aggregate metrics hide a subgroup regression?
- Why write the hypothesis before an experiment?

## Official references

- NIST statistical guidance: https://www.itl.nist.gov/div898/handbook/
- scikit-learn model evaluation: https://scikit-learn.org/stable/modules/model_evaluation.html
