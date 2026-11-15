# 11 — Prompt Evaluation, Testing, and Regression

## Why prompts need tests

A prompt is part of application behaviour. A small wording change may improve one example and break another. I treat prompt changes like code changes: version them, test them, compare results, and review regressions.

## 1. Build a representative dataset

Include:
- common valid inputs,
- edge cases,
- missing information,
- ambiguous questions,
- conflicting evidence,
- long inputs,
- out-of-scope requests,
- adversarial/prompt-injection content.

Keep a separate holdout set for final comparison. Do not tune only on examples already visible in the prompt.

## 2. Define measurable criteria

For a support classification prompt:
- correct category,
- valid schema,
- evidence-supported explanation,
- appropriate unknown response,
- no sensitive-data leakage,
- acceptable latency and cost.

For a summary prompt:
- coverage of required facts,
- no invented actions,
- preserved chronology,
- concise output,
- correct source attribution.

## 3. Deterministic checks

Use normal tests for:
- schema validity,
- required fields,
- allowed enum values,
- citation IDs exist,
- output size,
- prohibited field presence,
- permission behaviour.

These tests should not depend on another model judging the result.

## 4. Human review

People should review criteria that require judgement, such as clarity, usefulness, tone, and whether a summary omits an important nuance. Use a consistent rubric and multiple reviewers for high-impact evaluation.

## 5. LLM-as-judge carefully

A model can assist with rubric scoring, but judge outputs can be biased, inconsistent, and sensitive to phrasing or answer order. Calibrate against human-labelled examples. Never use a judge score as the only release gate for a consequential system.

## 6. Compare versions fairly

When comparing prompt A and prompt B:
- run both on the same dataset,
- keep model and generation settings constant,
- record the date and model version,
- repeat runs where output variability matters,
- inspect per-category regressions,
- record latency and cost,
- retain raw outputs according to data policy.

Change one major prompt element at a time where possible.

## Practice exercise

Create a 20-case test dataset for a code explanation prompt. Write five deterministic checks and a human review rubric with correctness, clarity, completeness, and unsupported-claim dimensions. Compare two prompt versions and document at least one regression.

## Self-check

1. Why create a fixed evaluation set?
2. What belongs in an edge-case dataset?
3. Which checks can be deterministic?
4. Why calibrate an LLM judge with human examples?
5. How can a prompt change be compared fairly?

## Further reading

- [LLM Evaluation course](../llm-evaluation/README.md)
- [OpenAI evals guide](https://platform.openai.com/docs/guides/evals)
