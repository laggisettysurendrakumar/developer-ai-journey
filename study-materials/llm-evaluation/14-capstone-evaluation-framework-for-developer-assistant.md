# 14 — Capstone: Evaluation Framework for a Developer Assistant

## Project goal

Build a small evaluation framework that can test a Developer Support Assistant before and after changes to prompts, models, retrieval, and tools.

Use synthetic support documents and test cases. Do not upload confidential company runbooks or customer information to an unapproved service.

## 1. Suggested project structure

```text
developer-assistant-evaluation/
├── datasets/
│   ├── dev.jsonl
│   ├── validation.jsonl
│   └── heldout.jsonl
├── app/
│   ├── assistant.py
│   ├── retrieval.py
│   └── model_adapter.py
├── evaluation/
│   ├── metrics.py
│   ├── judge.py
│   ├── rubric.md
│   └── run.py
├── tests/
│   ├── test_permissions.py
│   ├── test_tool_policy.py
│   └── test_output_schema.py
├── results/
├── requirements.txt
└── README.md
```

## 2. Create dataset cases

Each case should capture question, user role, expected source IDs, required facts, forbidden behaviour, expected tool policy, and risk category.

Include:
- Normal support questions.
- Ambiguous questions.
- No-answer cases.
- Outdated or conflicting content.
- Restricted documents.
- Prompt injection.
- Tool timeout and invalid arguments.

## 3. Implement deterministic gates

Before calling a model, test authentication, access filters, and input limits. After the model returns, validate schema and enforce tool allowlists. Create tests that prove restricted documents are never returned to an unauthorised user.

These tests should run without a model API key.

## 4. Evaluate retrieval

Label relevant document IDs and measure recall@k and ranking quality. Examine failures caused by extraction, chunking, filters, stale indexes, or embedding mismatch.

## 5. Evaluate generated answers

Use a human rubric for correctness, completeness, groundedness, citations, and no-answer behaviour. A model judge can help triage large sets, but calibrate it against human labels and retain human review for critical cases.

## 6. Evaluate agents

If the assistant calls tools, measure task completion, tool choice, argument validity, permission outcomes, step count, failures, human approval, and cost.

## 7. Track each experiment

Create a run manifest containing commit, model identifier/version, prompt version, dataset version, retrieval configuration, evaluator/rubric version, and run date. Store results securely.

## 8. Create release gates

Example gates to adapt:
- No cross-user or cross-tenant data disclosure.
- No unauthorised tool action.
- No critical safety-policy violation.
- Structured response validity above the agreed threshold.
- Groundedness and task quality do not regress beyond an approved margin.
- Latency and cost remain within the defined budget.

Hard safety gates must not be averaged into a quality score.

## 9. Production feedback loop

Collect privacy-safe metrics and feedback, triage incidents, add approved synthetic or redacted cases to the dataset, review the rubric, and rerun the suite. Do not automatically add unreviewed user content to a training or evaluation dataset.

## Definition of done

- Dataset has versioned development, validation, and held-out cases.
- Human rubric and annotation instructions exist.
- Deterministic security and contract tests run in CI.
- Retrieval and answer quality are reported separately.
- Judge results are calibrated against human review.
- Agent tool behaviour is evaluated when applicable.
- Run manifests make experiments reproducible.
- Regression and safety gates are documented.
- Production telemetry avoids unnecessary sensitive content.
- Evaluation results identify examples and failure causes, not just one score.

## Practice exercise

Implement a first end-to-end run with at least 30 synthetic cases. Produce a report showing dataset version, model/prompt versions, per-dimension results, retrieval metrics, safety failures, latency, and token usage. Recommend whether the candidate should be promoted, held for review, or rejected.

## Self-check

- Why split held-out examples from prompt-development examples?
- Why are deterministic permissions tests required?
- How should an LLM judge be calibrated?
- Which failures must block release?
- What makes an evaluation run reproducible?

## Official references

- OpenAI Evals: https://platform.openai.com/docs/guides/evals
- OpenTelemetry: https://opentelemetry.io/docs/
- MLflow: https://mlflow.org/docs/latest/ml/tracking/
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/
