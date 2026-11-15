# 02 — Evaluation Datasets and Test Case Design

## Why a dataset matters

An evaluation dataset is a collection of examples used to check whether an AI system behaves as expected. It gives the team a consistent set of cases to rerun after changing the model, prompt, retrieval settings, or application code.

A small, carefully designed dataset is more useful than a large collection of vague questions with no expected behaviour.

## 1. Design each test case

A useful case can include:
- Stable case ID.
- User input and relevant conversation context.
- User identity or permission profile.
- Expected source IDs or reference answer.
- Required output properties.
- Forbidden behaviour.
- Risk category.
- Expected tool behaviour, if tools are involved.
- Annotation notes and evaluator version.

Example:

```python
case = {
    "id": "support-001",
    "input": "How do I inspect a failed Kubernetes Pod?",
    "user_role": "developer",
    "expected_source_ids": ["k8s-debugging-guide"],
    "must_include": ["describe pod", "logs"],
    "must_not": ["delete the namespace"],
    "risk": "medium",
}
```

## 2. Include diverse cases

Build test groups:
- Common happy-path questions.
- Rare but valid questions.
- Ambiguous or underspecified requests.
- Missing-evidence and no-answer cases.
- Contradictory or outdated sources.
- Long inputs and context-boundary cases.
- Malformed inputs and unsupported tasks.
- Prompt injection and adversarial instructions.
- Unauthorised document access.
- Tool timeouts and provider failures.
- Multilingual and formatting variation where relevant.

## 3. Avoid dataset leakage

Do not tune a prompt repeatedly against the same small test set and then treat its score as independent evidence. Create separate groups:
- Development set for prompt iteration.
- Validation set for comparing candidates.
- Held-out test set for final assessment.

Keep sensitive or company-specific data out of public repositories. Use synthetic or approved, redacted examples.

## 4. Version the dataset

Record dataset version, creation date, source, labels, and changes. A changed expected answer can alter scores even when the model has not changed. Keep a reviewable history of test-case modifications.

## 5. Balance coverage and practicality

Start small enough that a human can review every case. Expand using production failure patterns, support tickets, feedback, and newly discovered risks. Deduplicate near-identical cases so one pattern does not dominate the results.

## Practice exercise

Create 25 synthetic cases for a technical support assistant. Include common questions, no-answer cases, ambiguous questions, restricted documents, and prompt injection. Split them into development, validation, and held-out groups. Explain why the split matters.

## Self-check

- What information belongs in an evaluation case?
- Why include no-answer and permission cases?
- What is dataset leakage?
- Why keep a held-out test set?
- How can production failures improve the dataset safely?

## Official references

- OpenAI Evals: https://platform.openai.com/docs/guides/evals
- Hugging Face Evaluate: https://huggingface.co/docs/evaluate/
