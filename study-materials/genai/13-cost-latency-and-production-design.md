# 13 — Cost, Latency and Production Design

## A prototype and a production service have different constraints

A prototype may make one model call for one user. A production workflow may call a model several times, retrieve data, invoke tools, retry failures, and handle concurrent users.

Measure the complete request, not only the model's response.

## 1. Cost drivers

Common cost contributors include:
- Input and output tokens.
- Model and deployment selection.
- Number of model calls per task.
- Cached input where supported.
- Embedding generation.
- Retrieval infrastructure.
- Multimodal processing.
- Retries and failed requests.
- Logging and observability ingestion.

Use current provider pricing and actual usage metadata. Do not assume a model's cost from its name alone.

## 2. Reduce unnecessary work

- Keep prompts focused.
- Limit irrelevant conversation history.
- Retrieve only useful context.
- Cache safe, repeatable results where appropriate.
- Use a smaller model for simple tasks if evaluation supports it.
- Set output limits.
- Bound agent/tool steps in systems that use them.
- Avoid retry loops.

## 3. Latency

Measure:
- Queue wait time.
- Retrieval time.
- Provider request time.
- Tool execution time.
- Post-processing time.
- Total time to first output and completion.

Streaming can improve perceived responsiveness, but does not necessarily reduce total inference time.

## 4. Architecture boundaries

A production GenAI service commonly separates:
- API layer: authentication, validation, rate limiting.
- Model adapter: provider-specific requests and response normalisation.
- Prompt management: versioned instructions and templates.
- Domain logic: trusted business rules.
- Data access: retrieval and persistence.
- Evaluation/telemetry: quality and operational signals.

The boundaries make it easier to mock providers, change models, test logic, and control permissions.

## 5. Capacity planning

Estimate expected requests per minute, average and peak token counts, concurrency, timeout budget, and downstream throughput. Check provider quota and regional availability. Load test within an approved budget and respect provider policies.

## Practice exercise

Estimate the monthly usage of a sample summarisation API using three workloads: light, expected, and peak. Record assumptions for request volume, token counts, retries, and model choice. Compare cost per successful request and set practical limits.

## Self-check

- Why is cost per task more useful than only cost per model call?
- What parts of an application contribute to end-to-end latency?
- How can irrelevant history increase cost?
- Why should concurrency be bounded?
- Why must pricing and quota be checked before launch?

## Official references

- OpenAI API pricing: https://openai.com/api/pricing/
- OpenAI production best practices: https://platform.openai.com/docs/guides/production-best-practices
- Azure OpenAI quota guidance: https://learn.microsoft.com/azure/ai-services/openai/quotas-limits
