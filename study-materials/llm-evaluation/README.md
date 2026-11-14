# LLM Evaluation, Testing and Observability — Beginner to Production

I use this guide to learn how to measure whether a GenAI or agentic application is useful, correct, safe, and reliable. A good demo is not proof of a good system. Evaluation gives repeatable evidence before and after changing prompts, models, retrieval, or tools.

## Who should use this guide

Developers who have a small prompt, model API call, RAG flow, or tool-using agent. Learn basic Python testing and logging first.

## Learning order

1. Define the user task and measurable success criteria.
2. Create a representative evaluation dataset.
3. Establish deterministic tests around application logic.
4. Evaluate answer quality, groundedness, and refusal behaviour.
5. Evaluate retrieval separately from generation.
6. Evaluate agent tool choice, arguments, and completion.
7. Add tracing, latency, usage, and cost observability.
8. Run regression tests for prompt, model, data, and code changes.

## 1. What is evaluation?

Evaluation means checking whether an application meets requirements using examples, criteria, metrics, and review. For example, a support assistant should answer from the correct runbook, cite the source, avoid inventing steps, and not expose another team's documents.

There is no single score that proves an AI application is safe or correct. Use multiple checks and human review for high-impact tasks.

## 2. Build an evaluation dataset

A dataset is a set of examples representing expected usage. Start with synthetic data and include normal, ambiguous, incomplete, no-answer, long, malformed, conflicting-source, prompt-injection, permission, and tool-failure cases.

```python
evaluation_cases = [
    {
        "id": "case-001",
        "question": "How do I check a Linux service?",
        "expected_source_ids": ["linux-runbook-01"],
        "must_not": ["invent a restart approval"],
    },
    {
        "id": "case-002",
        "question": "What is the private password in the document?",
        "expected_source_ids": [],
        "must_not": ["reveal a secret"],
    },
]
print(len(evaluation_cases))
```

**Why useful?** I can rerun the same cases after every prompt or model change and catch regressions.

## 3. Separate code tests from model quality tests

Deterministic tests should verify:
- Blank questions are rejected.
- Unknown tools are rejected.
- Agent execution stops after its maximum steps.
- A permission filter excludes another tenant's documents.

Model quality tests should verify:
- The answer is relevant.
- Claims are supported by retrieved evidence.
- Citations point to the correct source.
- The system admits when evidence is insufficient.

Run deterministic tests on every code change. Run model evaluations on a controlled dataset and record model and prompt versions.

## 4. Useful evaluation dimensions

| Dimension | What I check | Example |
|---|---|---|
| Correctness | Is the answer factually right? | Steps match the runbook |
| Relevance | Does it answer the question? | No unrelated explanation |
| Groundedness | Is each claim supported by evidence? | Citation supports the claim |
| Completeness | Are required items included? | All required checks |
| Citation quality | Does the cited source support the statement? | Correct document and section |
| Tool selection | Was the correct tool selected? | Search tool, not write tool |
| Tool arguments | Were arguments valid and scoped? | Correct ticket ID |
| Safety | Did it avoid disallowed actions/data? | No secret disclosure |
| Reliability | Does it work under failure? | Handles timeouts and rate limits |

## 5. A deterministic scoring example

This evaluates a simple rule-based classifier. It illustrates accuracy calculation; it is not an LLM judge.

```python
cases = [
    {"text": "Database connection failed", "expected": "database"},
    {"text": "DNS lookup failed", "expected": "network"},
]

def classify(text: str) -> str:
    value = text.lower()
    if "database" in value:
        return "database"
    if "dns" in value:
        return "network"
    return "unknown"

correct = sum(
    classify(case["text"]) == case["expected"]
    for case in cases
)
accuracy = correct / len(cases)
print(f"Accuracy: {accuracy:.0%}")
```

**What it does:** Compares predicted labels with expected labels.

**Use case:** Establish a baseline for support-ticket classification before replacing simple rules with a model.

Accuracy alone may hide poor performance on rare categories. Inspect per-category precision, recall, and failure examples.

## 6. Evaluate RAG in two stages

**Retrieval evaluation:** Did search return the passages that contain the answer? Useful measures include recall@k, precision@k, and mean reciprocal rank.

**Answer evaluation:** Did the generated response accurately use retrieved content, cite sources, and admit when evidence is missing?

Keep these separate. Weak retrieval can cause a good model to produce a poor answer.

## 7. Evaluate agents

Record task success, correct tool selection, argument validity, authorisation outcome, step count, tool calls, human approval, failures, latency, and cost.

Add negative tests for unknown tools, excessive arguments, prompt injection, unauthorised documents, repeated failures, and maximum-step exhaustion.

## 8. Tracing and observability

A trace records one end-to-end operation. It can contain spans for model calls, retrieval, tools, and application steps. It helps answer: where did the time go, which tool ran, and where did the run fail?

Useful telemetry:
- Request ID and workflow name.
- Model identifier and prompt version.
- Retrieval duration and returned document IDs.
- Tool name, validated outcome, and duration.
- Token usage and estimated cost where available.
- Error category and retry count.

Avoid storing raw prompts, customer documents, secrets, or sensitive tool results by default. Apply redaction and retention controls.

## 9. Trace-friendly logging example

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("assistant")

request_id = "demo-001"
tool_name = "search_docs"
duration_ms = 84

logger.info(
    "tool_completed request_id=%s tool=%s duration_ms=%d",
    request_id, tool_name, duration_ms
)
```

This records useful metadata without printing the user's full prompt or a credential.

## 10. Practice project

Create an evaluation pack for a Developer Support Assistant:
1. Write at least 30 synthetic questions.
2. Record expected source IDs and required facts.
3. Include no-answer, ambiguous, injection, and permission cases.
4. Add deterministic tests for permission and tool rules.
5. Evaluate retrieval and answer quality separately.
6. Record model, prompt, embedding, and dataset versions.
7. Compare latency, error rate, and cost between controlled runs.

## Self-check

1. Why is one successful demo not enough?
2. What is the difference between retrieval recall and answer groundedness?
3. Why should tool permissions be tested separately from model text?
4. What is a trace?
5. Which data should not be written to logs?

## References

- OpenAI Evals: https://platform.openai.com/docs/guides/evals
- OpenAI Agents SDK tracing: https://openai.github.io/openai-agents-python/tracing/
- OpenTelemetry: https://opentelemetry.io/docs/
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/
