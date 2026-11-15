# 12 — Prompt Optimization, Versioning, and Experiment Design

## Improvement should be evidence-led

Prompt optimization is the process of improving a prompt against defined quality criteria. The best prompt is not necessarily the longest, cleverest, or most elaborate. It is the simplest prompt that reliably meets the requirements.

## 1. A disciplined optimisation loop

1. Define the task and success criteria.
2. Record the baseline prompt.
3. Run the baseline evaluation dataset.
4. Inspect failure clusters.
5. Form one hypothesis about the cause.
6. Change one important element.
7. Re-run the same tests.
8. Compare improvements and regressions.
9. Record the decision.

## 2. Avoid random prompt tweaking

Do not change role, format, examples, temperature, model, and context all at once. If the output changes, you will not know what caused it.

Keep model and settings fixed while testing prompt text. Then separately test configuration changes.

## 3. Version prompts

Store:
- prompt ID,
- version,
- owner,
- purpose,
- template and variable contract,
- model/provider expectation,
- output schema version,
- evaluation dataset version,
- change summary,
- approval and release date.

A Git repository is suitable for text-based prompt assets, provided no secrets or sensitive production data are committed.

## 4. Experiment design

A/B evaluation compares two prompt variants on the same inputs. Blind reviewers where possible to avoid preference bias. For stochastic outputs, repeat runs and analyse variability.

For online experiments, define:
- eligible traffic,
- user-impact guardrails,
- rollout percentage,
- success metric,
- stop condition,
- rollback path.

Do not test risky prompt changes on all users without a staged release.

## 5. Token, latency, and cost optimisation

Remove redundant instructions and irrelevant examples. Use a smaller context only when quality is preserved. Measure actual token use, latency, and cost using provider telemetry; do not estimate production cost from character count alone.

## Practice exercise

Create version 1 and version 2 of an issue-summary prompt. Version 2 should address a specific observed failure. Run both across the same synthetic dataset and record quality, latency, and token usage. Write a short release note explaining the evidence.

## Self-check

1. What makes a prompt optimisation evidence-led?
2. Why change one variable at a time?
3. What metadata should accompany a prompt version?
4. Why stage an online rollout?
5. How can prompt length affect cost and latency?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [LLM Evaluation course](../llm-evaluation/README.md)
