# 11 — Evaluation, Testing and Observability for GenAI

## Why ordinary unit tests are not enough

Traditional software usually has deterministic expected outputs for a given input. Generative outputs can vary. Testing must therefore check important properties and task quality, not only exact wording.

## 1. Test layers

- Unit tests: validate prompt construction, parsing, business rules, and adapter logic.
- Contract tests: verify the provider response is handled correctly using mocks or recorded safe fixtures.
- Evaluation tests: score outputs against a rubric and reference examples.
- Integration tests: verify the complete application flow.
- Safety tests: try prompt injection, sensitive-data requests, and out-of-scope actions.
- Regression tests: compare a new prompt/model/configuration with the previous baseline.

## 2. Define metrics

Depending on the task:
- Task success or label accuracy.
- Citation/source correctness.
- Groundedness and unsupported-claim rate.
- Structured-output parse success.
- Refusal and safety behaviour.
- Latency percentiles.
- Input/output token use and cost.
- Timeout, retry, and rate-limit frequency.

Do not treat one automatic evaluator as ground truth. Calibrate evaluation against human review.

## 3. Trace a model request

A useful trace may include:
- Application request ID.
- Model/provider and deployment identifier.
- Prompt version.
- Duration and token usage.
- Retrieval duration and source IDs where relevant.
- Tool calls and outcomes.
- Error category.
- Evaluation result.

Do not log raw sensitive prompts or retrieved documents by default. Apply redaction, access control, and retention policy.

## 4. Compare versions

Run the same evaluation set before and after changing a prompt, model, retrieval settings, or generation parameters. Investigate regressions before promoting the change.

## Practice exercise

Create a 20-case evaluation set for a support summariser. Add a deterministic test for schema parsing and a rubric for factuality and completeness. Record output quality, latency, and token use for two prompt versions.

## Self-check

- Why is exact string comparison often a weak test for generated text?
- What is the difference between a unit test and an evaluation test?
- Why should traces contain prompt versions and model identifiers?
- Why is automatic evaluation not always sufficient?
- What sensitive information can observability accidentally expose?

## Official references

- OpenAI evaluation: https://platform.openai.com/docs/guides/evals
- OpenTelemetry: https://opentelemetry.io/docs/
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/
