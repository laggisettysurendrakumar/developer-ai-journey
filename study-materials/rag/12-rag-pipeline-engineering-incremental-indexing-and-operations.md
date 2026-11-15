# 12 — RAG Pipeline Engineering: Incremental Indexing and Operations

## RAG is a living data pipeline

Documents change. Permissions change. Embedding models change. A production RAG system needs a reliable process for initial indexing, incremental updates, deletion, and recovery.

## 1. Full versus incremental ingestion

**Full ingestion** rebuilds the index from all approved sources. It is simple to reason about but may be expensive and slow.

**Incremental ingestion** processes only added or changed documents and removes deleted sources. It needs reliable change detection and reconciliation.

A content hash can identify whether document bytes or canonical text have changed. Store parser, chunker, and embedding model versions so an ingestion run is reproducible.

## 2. Make ingestion idempotent

Running the same source through ingestion repeatedly should not create duplicate chunks. Use stable document and chunk IDs, source hashes, and safe upsert/delete operations.

Keep an ingestion state such as discovered, parsed, chunked, embedded, indexed, failed, and deleted. Do not mark a document as fully indexed until all required chunks are committed successfully.

## 3. Re-indexing and embedding migrations

A new embedding model may change dimensions and similarity behaviour. Build a new index version, ingest and evaluate it, then switch traffic deliberately. Keep the old index available until rollback is no longer needed.

## 4. Deletion and revocation

When a source is deleted or access is revoked:
- remove affected vectors and content,
- invalidate caches,
- update search filters,
- ensure backups follow retention/deletion policy,
- confirm that old content is no longer retrievable.

Deletion should be auditable and testable.

## 5. Operational observability

Track source count and indexed document count, ingestion success/failure rate, indexing lag, chunks per document, duplicate rate, embedding latency and errors, retrieval latency, empty/low-quality retrieval rate, answer abstention and feedback, index version, and model version.

## 6. Failure recovery

A robust pipeline should support bounded retries, dead-letter handling or failed-item queues, checkpoints, idempotent reprocessing, and alerts when ingestion falls behind. Avoid retry loops that overload a source system.

## Practice exercise

Design an ingestion pipeline for a folder of Markdown documents. Specify document identity, content hashes, chunk IDs, version fields, retry rules, deletion handling, and a recovery process after the embedding provider becomes unavailable halfway through a batch.

## Self-check

1. What is the difference between full and incremental ingestion?
2. Why should ingestion be idempotent?
3. Why record parser and embedding model versions?
4. What happens when a user loses access to a source document?
5. Which metrics show that the index is falling behind?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- [OpenTelemetry](https://opentelemetry.io/docs/)
