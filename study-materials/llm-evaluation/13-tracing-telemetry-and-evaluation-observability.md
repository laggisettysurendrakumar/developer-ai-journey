# 13 — Tracing, Telemetry and Evaluation Observability

## Why production evaluation continues after deployment

Offline evaluation tests a known dataset. Production traffic reveals new questions, changed user behaviour, unexpected documents, and failure patterns. Monitoring helps detect when real-world quality changes.

Production monitoring must respect privacy and data governance. Do not record every prompt or response by default.

## 1. Logs, metrics and traces

- Logs: discrete events such as provider timeout or tool validation failure.
- Metrics: rates and measurements over time.
- Traces: linked spans for a single request across model, retrieval, and tools.

Useful metrics:
- Request volume and success rate.
- Provider error and timeout rates.
- p50/p95/p99 latency.
- Token usage and estimated cost.
- Retrieval latency and empty-result rate.
- Tool selection and failure rates.
- Structured-output validation failures.
- Human feedback and escalations.
- Safety policy outcomes.

## 2. Use trace identifiers

Propagate a request or trace identifier across services. Record safe metadata such as model identifier, prompt version, duration, token usage, and tool names. Avoid sensitive text unless approved, redacted, and access-controlled.

## 3. Online quality signals

Possible signals include user thumbs-up/down, correction rate, escalation rate, repeated-question rate, citation clicks, task completion, and support tickets. Feedback is noisy and biased; combine it with reviewed examples and offline evaluation.

## 4. Drift and changing data

Quality can change when document collections, user questions, model versions, or product workflows change. Monitor:
- Input distribution.
- Language and topic mix.
- Retrieval result distribution.
- No-answer rate.
- Feedback trends.
- Performance by user cohort where lawful and appropriate.

Do not infer sensitive characteristics or collect unnecessary personal attributes.

## 5. Alerts and runbooks

Alert on meaningful shifts such as sustained provider failure, high latency, cost spikes, retrieval outage, or critical safety issue. Each alert should have an owner, threshold rationale, and response procedure.

## Practice exercise

Create a dashboard specification for a RAG support assistant. Define five operational metrics, three quality signals, one critical safety alert, privacy controls, and the runbook for investigating a sudden increase in no-answer responses.

## Self-check

- How do logs, metrics and traces differ?
- Why continue evaluation after production launch?
- What is a useful quality signal from users?
- Why might a rise in no-answer responses indicate retrieval drift?
- What should every alert include?

## Official references

- OpenTelemetry: https://opentelemetry.io/docs/
- OpenAI Agents tracing: https://openai.github.io/openai-agents-python/tracing/
- Prometheus: https://prometheus.io/docs/introduction/overview/
