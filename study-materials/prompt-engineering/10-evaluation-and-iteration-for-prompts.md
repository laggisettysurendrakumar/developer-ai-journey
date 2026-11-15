# 10 — Evaluate and Improve Prompts Systematically

## Why “it looks good” is not enough

A prompt can produce an excellent answer for one input and fail on a slightly different request. I need a repeatable test set to know whether a change improves the task.

## 1. Build a baseline dataset

For each test case, record:
- input,
- expected behaviour,
- required fields or facts,
- forbidden outcomes,
- source evidence where relevant,
- test category.

Include normal, edge, missing-context, ambiguous, adversarial, long-input, and out-of-scope cases.

## 2. Define evaluation criteria

For a support-ticket classifier:
- category correctness,
- schema validity,
- correct unknown handling,
- consistency on similar cases,
- no extra sensitive data.

For a code-review assistant:
- valid finding,
- severity calibration,
- evidence from diff,
- actionable fix,
- false-positive rate.

## 3. Compare versions fairly

1. Save the baseline prompt.
2. Change one meaningful aspect.
3. Run the same dataset against both versions.
4. Compare quality, failures, cost, latency, and output length.
5. Inspect regressions, not just average scores.
6. Keep the change only if it improves the agreed criteria.

Use the same model and settings for prompt comparisons where possible. If the model version changes, record it separately.

## 4. Example simple test harness

```python
test_cases = [
    {
        "name": "database_timeout",
        "input": "SQL connections time out from the API.",
        "expected_category": "database",
    },
    {
        "name": "unknown_signal",
        "input": "The application is not behaving as expected.",
        "expected_category": "unknown",
    },
]

def evaluate_category(output: dict, expected: str) -> bool:
    return output.get("category") == expected

# The model call is intentionally omitted.
# Feed real, validated model outputs into this evaluator.
for case in test_cases:
    print(case["name"], case["expected_category"])
```

This example defines a deterministic evaluator. It does not call a model or prove the prompt is good.

## 5. Human evaluation

For subjective tasks, use a rubric with observable criteria. Ask reviewers to score independently when possible, then discuss disagreements. Do not use a model judge as the only authority for safety or high-impact decisions.

## 6. Regression management

Save prompt versions and test results. A prompt update should have:
- reason for change,
- old and new template,
- model and parameter details,
- dataset version,
- result summary,
- known regressions,
- reviewer/approval where required.

## Practice exercise

Create a 20-case evaluation set for a .NET support summariser. Include missing context, duplicate logs, contradictory timestamps, injection text, oversized input, and unknown causes. Compare two prompt versions and write a short report with examples of improvements and regressions.

## Self-check

1. Why is one successful example not enough?
2. What belongs in a prompt evaluation dataset?
3. Why change one variable at a time?
4. Which metrics might differ between classification and code review?
5. Why retain prompt versions and evaluation results?

## Further reading

- [LLM Evaluation course](../llm-evaluation/README.md)
- [OpenAI evals guide](https://platform.openai.com/docs/guides/evals)
