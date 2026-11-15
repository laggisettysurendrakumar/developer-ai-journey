# 03 — Vector Database Concepts: Collections, Points, Payloads, and Indexes

## Learn the vocabulary before using a vendor

Vector databases use different names, but many share the same basic ideas. I learn the conceptual model first, then map it to Qdrant, pgvector, or another selected technology.

## 1. Common concepts

| Concept | Plain-English meaning |
|---|---|
| Collection / table / index | Logical container for searchable records |
| Point / record / row | One stored item |
| Vector | Numeric embedding |
| Payload / metadata | Fields stored alongside a vector |
| Distance metric | Rule used to compare vectors |
| Filter | Condition that restricts candidate records |
| ANN index | Data structure for fast approximate nearest-neighbour search |
| Namespace / partition | Logical separation of stored data, depending on product |

The exact terminology and capabilities differ between databases.

## 2. What should a point contain?

A document chunk record may contain:
- stable chunk ID,
- embedding vector,
- chunk text or a content reference,
- source document ID,
- source URI,
- heading or page number,
- document version,
- access metadata,
- embedding model version.

Keep source metadata so a retrieval result can be traced back to its original document.

## 3. Collection configuration

A collection/index may define:
- vector dimension,
- distance metric,
- vector name for multi-vector configurations,
- index parameters,
- replication or storage options.

Choose these based on the embedding model and workload. Some choices are difficult to change later and may require re-creating or migrating the index.

## 4. Metadata/payload

Metadata allows filtering by attributes such as product, language, version, category, tenant, or access group. For private data, access-control metadata must come from a trusted source and be applied by a trusted query service.

Do not treat an opaque or random document ID as authorisation.

## 5. Example record shape

```json
{
  "id": "runbook-api-timeout-02",
  "vector": [0.12, -0.45, 0.88],
  "payload": {
    "document_id": "runbook-api-timeout",
    "title": "API Timeout Runbook",
    "heading": "Dependency timeouts",
    "tenant": "engineering",
    "version": "v2"
  }
}
```

The vector shown is illustrative and has only three dimensions. Real vectors must match the collection's configured size.

## Practice exercise

Design a metadata schema for a developer knowledge base. Include fields needed for citations, filtering by document version, and permissions. Mark which fields can be supplied by the user and which must be populated by trusted ingestion or identity services.

## Self-check

1. What is a collection?
2. What is the difference between a vector and payload?
3. Why is a stable chunk ID useful?
4. What does a filter do?
5. Why must access metadata come from a trusted source?

## Further reading

- [Qdrant collections](https://qdrant.tech/documentation/concepts/collections/)
- [Qdrant points](https://qdrant.tech/documentation/concepts/points/)
- [pgvector](https://github.com/pgvector/pgvector)
