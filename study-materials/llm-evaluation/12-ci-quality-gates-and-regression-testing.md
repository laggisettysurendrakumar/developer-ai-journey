# 12 — CI Quality Gates and Regression Testing

## Evaluation should be part of delivery

Manual evaluation is useful during discovery. Mature GenAI teams automate the repeatable parts so prompt, model, retrieval, and code changes are checked before release.

## 1. What runs in CI?

A pipeline may run:
1. Static checks and unit tests.
2. Provider adapter contract tests using fixtures.
3. Permission and tool-policy tests.
4. A small, deterministic evaluation suite.
5. A controlled model evaluation suite where cost and latency are acceptable.
6. Safety/adversarial test cases.
7. A comparison against the accepted baseline.

Not all model evaluations need to run on every commit. Use a fast PR suite and a broader scheduled or pre-release suite where appropriate.

## 2. Quality gates

Possible release gates:
- Zero critical authorisation or data-leakage failures.
- Required schema validity threshold.
- No unacceptable regression in task success.
- Citation or groundedness above a task-specific threshold.
- Latency and cost within budget.
- No known critical vulnerability in the release artifact.

Choose thresholds using evidence and risk. Avoid arbitrary thresholds copied from another project.

## 3. Regression analysis

A regression is a deterioration in behaviour after a change. Compare each case and identify:
- Newly failing cases.
- Previously failing cases that now pass.
- Score changes by category.
- Safety violations.
- Latency and cost changes.

A release may improve the average while breaking a high-value workflow. Review individual regressions.

## 4. Baselines and approval

Keep an accepted baseline with its exact configuration. Require review for changes to evaluation data, rubric, judge prompt, or pass/fail thresholds. Otherwise teams can accidentally make a failing system appear successful by weakening the test.

## Practice exercise

Design a CI pipeline for a model-backed support API. Specify fast tests for pull requests, broader evaluations before release, hard safety gates, approval rules, and what artifacts should be retained.

## Self-check

- Why have a fast test suite and a broader evaluation suite?
- Which failures should block release regardless of average score?
- Why keep a baseline?
- How can changing the rubric hide a regression?
- Why inspect per-case failures?

## Official references

- GitHub Actions: https://docs.github.com/actions
- OpenAI Evals: https://platform.openai.com/docs/guides/evals
