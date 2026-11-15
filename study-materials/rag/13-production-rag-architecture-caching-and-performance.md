# 13 — Production RAG Architecture, Caching, and Performance

## From prototype to production

A production RAG application needs more than a vector database and a model API. It needs secure identity, resilient dependencies, predictable latency, capacity planning, monitoring, cost control, and a clear support model.

## 1. Reference architecture

```text
Web / API client
      |
Authentication + authorisation
      |
RAG application service
      |
Query preprocessing
      |
Permission-aware hybrid retrieval
      |
Reranking + context selection
      |
Grounded prompt builder
      |
LLM provider
      |
Citation / schema validation
      |
Response + telemetry
```

The ingestion pipeline runs separately and publishes versioned indexed content.

## 2. Latency budget

Measure each stage separately: authentication, query embedding, keyword search, vector search, reranking, prompt construction, generation, validation, and citation mapping.

Do not optimise only the model call if retrieval or ingestion is the real bottleneck.

## 3. Caching

Potential caches include query embedding cache, retrieval result cache, and generated answer cache.

Caching private data is risky. Include identity, permission scope, source version, and freshness in cache design. A cache hit must never bypass a permission change or return one user's restricted data to another.

## 4. Resilience

- set per-dependency timeouts,
- bound retries and use backoff for transient failures,
- enforce rate and concurrency limits,
- degrade gracefully when optional reranking is unavailable,
- return a safe message when generation is unavailable,
- track partial results and cancelled requests.

Do not silently answer from stale cached evidence without exposing its age or status.

## 5. Cost management

Monitor embedding cost during ingestion and query time, vector-store operations, reranking, LLM input/output tokens, and repeated queries. Reduce cost by deduplicating documents, selecting relevant context, limiting output, and choosing models based on evaluation—not by blindly removing evidence.

## 6. Production readiness checklist

- access-control and tenant-isolation tests pass,
- source ingestion and deletion are reliable,
- prompt-injection tests exist,
- retrieval and answer quality have baselines,
- latency/cost objectives are defined,
- logs are redacted,
- alerts and operational ownership exist,
- backup, restore, and index rollback are documented,
- model and index version changes are tested.

## Practice exercise

Create an operational dashboard specification for your RAG capstone. Define stage-level latency metrics, retrieval quality indicators, ingestion freshness, permission-denial events, model errors, cost, and a runbook for vector-store unavailability.

## Self-check

1. Why measure latency for each pipeline stage?
2. How can caching create a data leak?
3. What should happen if the reranker is unavailable?
4. Which activities contribute to RAG cost?
5. What makes a prototype different from a production service?

## Further reading

- [OpenTelemetry](https://opentelemetry.io/docs/)
- [LLM Evaluation course](../llm-evaluation/README.md)
