# 05 — Generate Embeddings and Prepare Records for Upsert

## Connect embedding concepts to storage

The vector database stores vectors, but it does not usually create the text embeddings itself. A separate embedding model or service converts content into vectors. The application then stores the vector with its source text and metadata.

## 1. Indexing workflow

1. Load an approved document.
2. Split it into meaningful chunks.
3. Send each chunk to the selected embedding model.
4. Receive one vector per chunk.
5. Validate dimensions and response status.
6. Build stable point IDs and metadata.
7. Upsert records into the collection.

Upsert usually means insert a record or replace/update the record with the same identifier. Check the database-specific semantics.

## 2. Use compatible embedding models

Use the same compatible embedding model and preprocessing strategy for document chunks and user queries. Store the model identifier and index version. If the embedding model changes, plan a migration rather than mixing incompatible vectors.

## 3. Example record preparation

```python
def build_vector_record(
    chunk_id: str,
    document_id: str,
    text: str,
    vector: list[float],
    model_id: str,
) -> dict:
    if not chunk_id or not document_id:
        raise ValueError("Stable identifiers are required")
    if not vector or not all(isinstance(value, (int, float)) for value in vector):
        raise ValueError("A non-empty numeric vector is required")

    return {
        "id": chunk_id,
        "vector": vector,
        "payload": {
            "document_id": document_id,
            "text": text,
            "embedding_model": model_id,
        },
    }

sample = build_vector_record(
    chunk_id="doc-01:chunk-01",
    document_id="doc-01",
    text="Synthetic runbook section",
    vector=[0.1, 0.2, 0.3],
    model_id="sample-embedding-model",
)
print(sample)
```

This example prepares a record using a synthetic vector. It does not call an embedding provider or database.

## 4. Stable IDs and repeatable ingestion

A stable ID helps avoid duplicate records when indexing runs again. It may be derived from document ID, section ID, chunk index, and content version/hash. Decide whether a content change should update an existing record or create a new version.

## 5. Batch sizes and rate limits

Embedding APIs and databases have request limits. Process records in bounded batches. Handle transient failures with limited retries and preserve a checkpoint so a failed batch can resume safely.

## Practice exercise

Create five synthetic chunks, use an approved embedding service or local model to generate vectors, and prepare upsert records. Validate that all vectors have the expected dimension and that every point contains source ID, chunk ID, model ID, and version.

## Self-check

1. Which component generates embeddings?
2. Why embed documents and queries compatibly?
3. What does upsert usually mean?
4. Why use stable point IDs?
5. Why should batch size and retry policy be bounded?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- [Qdrant points](https://qdrant.tech/documentation/concepts/points/)
