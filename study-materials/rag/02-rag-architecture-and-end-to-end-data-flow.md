# 02 — RAG Architecture and End-to-End Data Flow

## Understand the system before choosing a framework

A RAG application is a pipeline, not a single vector database call. I first understand each component and its input/output contract. Frameworks can help connect components, but they do not remove the need to design and test them.

## 1. Main components

| Component | Responsibility | Example |
|---|---|---|
| Source connector | Reads approved source material | Markdown folder, object storage |
| Parser | Extracts text and structure | PDF/HTML/Markdown parser |
| Normaliser | Cleans and standardises content | Remove navigation boilerplate |
| Chunker | Splits documents into passages | Heading-aware chunks |
| Embedding model | Converts text into vectors | Text embedding API |
| Index/store | Stores vectors and metadata | Vector database |
| Retriever | Finds candidate passages | Similarity or hybrid search |
| Reranker | Reorders candidates | Cross-encoder/rerank API |
| Prompt builder | Combines evidence and question | Grounded prompt template |
| Generator | Writes answer from context | LLM |
| Evaluator/telemetry | Measures behaviour | Retrieval and answer tests |

## 2. Indexing pipeline

```text
Source systems
    |
    v
Extract + parse
    |
    v
Normalise + enrich metadata
    |
    v
Chunk with source references
    |
    v
Generate embeddings
    |
    v
Store chunks + vectors + metadata
```

Indexing should be repeatable. If the same source is ingested twice, the system should not accidentally create uncontrolled duplicate records.

## 3. Query pipeline

```text
Question + authenticated user
    |
    v
Validate and optionally rewrite query
    |
    v
Generate query embedding
    |
    v
Apply tenant / permission filters
    |
    v
Retrieve candidates
    |
    v
Rerank and select context
    |
    v
Generate answer with source IDs
    |
    v
Validate / cite / return
```

The exact ordering of filtering depends on the database and security model, but access constraints must be enforced before restricted content is returned or sent to a model.

## 4. Design stable data contracts

A chunk record should preserve more than raw text. A useful conceptual model:

```json
{
  "chunk_id": "doc-123:section-4:chunk-2",
  "document_id": "doc-123",
  "source_uri": "knowledge://runbooks/api-timeout",
  "title": "API Timeout Runbook",
  "heading_path": ["Operations", "API", "Timeouts"],
  "content": "Synthetic example text...",
  "document_version": "v3",
  "tenant_id": "tenant-a",
  "allowed_groups": ["engineering"],
  "updated_at": "2026-11-01T09:00:00Z"
}
```

The metadata fields must be designed around the source system, identity model, and retention policy. Do not assume every deployment has tenants or groups; include only the controls your application requires.

## 5. Keep components replaceable

Define interfaces around document loading, embedding generation, retrieval, and generation. This makes it possible to change a provider or vector store without rewriting the full application. Pin dependency versions and create contract tests for adapters.

## Practice exercise

Draw a RAG architecture for a developer documentation assistant. Define the input and output of every component and create a sample chunk schema with source ID, heading, version, and access metadata. Explain where duplicate detection and document deletion fit.

## Self-check

1. What are the main parts of the indexing pipeline?
2. What are the main parts of the query pipeline?
3. Why store source and version metadata with every chunk?
4. Why should component boundaries be explicit?
5. Where must access filtering be enforced?

## Further reading

- [OpenAI embeddings](https://platform.openai.com/docs/guides/embeddings)
- [MCP architecture](https://modelcontextprotocol.io/docs/learn/architecture)
