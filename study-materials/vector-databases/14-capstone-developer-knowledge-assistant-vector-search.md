# Capstone: Build a Developer Knowledge Assistant with Vector Search

## What I am building

In this capstone I will bring the course concepts together into a small developer knowledge assistant. It will index technical notes and project documentation, then retrieve relevant passages when a developer asks a question.

The project is intentionally small enough to build locally, but its architecture follows patterns that can grow into an ASP.NET Core application.

## 1. Target capabilities

The first version should support:

- Ingesting Markdown documents.
- Splitting documents into searchable chunks.
- Generating embeddings with one selected model.
- Storing vectors and metadata in Qdrant.
- Searching with a query vector.
- Filtering by project, topic, or access scope.
- Returning source file names and chunk references.
- Updating and deleting indexed documents.
- Evaluating retrieval quality with a known question set.

The assistant should return retrieved source material. Answer generation can be added after retrieval works correctly. A language model should not be expected to fix poor search results.

## 2. Architecture

~~~text
Markdown files
     |
     v
Ingestion worker
     |
     +--> Parse and normalise
     |
     +--> Split into chunks
     |
     +--> Generate embeddings
     |
     v
Qdrant collection
     ^
     |
Search API
     |
     +--> Authenticate user
     |
     +--> Apply trusted filters
     |
     +--> Embed question
     |
     +--> Vector search
     |
     v
Ranked source chunks
     |
     v
Application response or RAG generation
~~~

For the first iteration I can implement ingestion and search in Python to learn the database workflow. Then I can expose retrieval through an ASP.NET Core 8 API.

## 3. Suggested project structure

~~~text
developer-knowledge-assistant/
├── data/
│   └── sample-docs/
├── ingestion/
│   ├── load_documents.py
│   ├── chunk_documents.py
│   ├── embed_documents.py
│   └── index_documents.py
├── search-api/
│   └── DeveloperKnowledgeApi/
│       ├── Controllers/
│       ├── Contracts/
│       ├── Services/
│       └── Program.cs
├── evaluation/
│   ├── questions.json
│   └── evaluate_retrieval.py
├── docker-compose.yml
└── README.md
~~~

I should keep secrets out of this directory and add local environment files to .gitignore.

## 4. Define a document and chunk model

Each indexed chunk needs enough information to identify and display its source.

Example metadata:

~~~json
{
  "tenant_id": "personal",
  "project": "developer-ai-journey",
  "source_path": "docs/vector-search.md",
  "document_id": "vector-search",
  "chunk_id": "vector-search-0003",
  "chunk_number": 3,
  "title": "Vector search fundamentals",
  "heading": "Similarity metrics",
  "content_version": "v1",
  "language": "en"
}
~~~

The vector is stored separately from this payload. The content may also be stored in an appropriate document store or included in the payload when the size and security requirements allow it.

Use stable chunk IDs so that re-indexing a document updates the correct records.

## 5. Create an embedding interface

The embedding provider should be replaceable. I should not spread provider-specific API calls across every ingestion module.

Python interface example:

~~~python
from typing import Protocol

class EmbeddingService(Protocol):
    def embed(self, texts: list[str]) -> list[list[float]]:
        ...

def validate_embeddings(
    texts: list[str],
    vectors: list[list[float]],
) -> None:
    if len(texts) != len(vectors):
        raise ValueError("Text and vector counts do not match")

    if not vectors:
        raise ValueError("No vectors were generated")

    dimension = len(vectors[0])
    if dimension == 0:
        raise ValueError("Embedding dimension cannot be zero")

    for vector in vectors:
        if len(vector) != dimension:
            raise ValueError("Inconsistent vector dimensions")
~~~

The actual implementation calls the chosen embedding model. I should record its model name, version, and dimensions so that later migrations are possible.

## 6. Ingestion workflow

Implement the ingestion process in small steps:

1. Discover files under the permitted input directory.
2. Read Markdown content and normalise line endings.
3. Extract title and heading information.
4. Split content using a consistent chunking strategy.
5. Keep source path and heading metadata for each chunk.
6. Generate embeddings in batches.
7. Validate vector count and dimensions.
8. Upsert points using stable IDs.
9. Record source version and ingestion status.
10. Log success and failure counts.

Do not silently ignore parse or embedding failures. Record them so that a document can be retried.

## 7. Search workflow

When a user asks a question:

1. Authenticate the caller.
2. Resolve the user's permitted project or tenant scope.
3. Generate the query embedding with the compatible model.
4. Search Qdrant with the required metadata filter.
5. Request a measured Top-K candidate count.
6. Return source path, heading, chunk ID, and relevant content.
7. Log latency and retrieval identifiers without leaking secrets or unnecessary personal content.

Start with vector search. Add keyword or hybrid search only after the baseline is working and measured.

## 8. ASP.NET Core integration boundary

In the .NET API, keep storage and embedding details behind service interfaces.

~~~csharp
public sealed record KnowledgeSearchRequest(
    string Query,
    string Project,
    int TopK = 5);

public sealed record KnowledgeSearchResult(
    string ChunkId,
    string SourcePath,
    string Heading,
    string Content,
    double Score);

public interface IKnowledgeSearchService
{
    Task<IReadOnlyList<KnowledgeSearchResult>> SearchAsync(
        KnowledgeSearchRequest request,
        CancellationToken cancellationToken);
}
~~~

The controller should validate the request and call the service. The service should derive the caller's allowed project scope from authenticated server context rather than trusting the Project field as an authorization decision.

A provider-specific repository can call Qdrant or pgvector. This separation keeps the API contract stable if the storage engine changes.

## 9. Configuration and dependency injection

Use configuration for non-secret settings such as collection name, endpoint, and default Top-K. Store credentials in a secret manager or protected environment configuration.

A simplified registration pattern:

~~~csharp
builder.Services.AddHttpClient<IKnowledgeSearchService, KnowledgeSearchService>(
    client =>
    {
        client.BaseAddress = new Uri(
            builder.Configuration["VectorDatabase:Endpoint"]
            ?? throw new InvalidOperationException(
                "Vector database endpoint is missing."));
    });
~~~

This shows the dependency-injection boundary. The KnowledgeSearchService implementation still needs to create the request, apply authentication, handle non-success responses, validate the database response, and map results. Do not treat an endpoint URL or a successful HTTP response as proof that the search is authorised.

## 10. Evaluation plan

Create a small file containing questions and expected source chunks.

Example evaluation record:

~~~json
{
  "question": "How do I keep another tenant's documents out of search?",
  "expected_chunk_ids": [
    "vector-security-0002"
  ]
}
~~~

Evaluate:

- Recall@K: whether an expected chunk appears in the first K results.
- Precision@K: how many returned chunks are relevant.
- MRR: how early the first relevant chunk appears.
- nDCG: whether highly relevant chunks are ranked near the top.
- p50 and p95 latency.
- Empty-result rate.
- Cross-tenant access test results.

Start with a small hand-labelled test set. Expand it as real questions and failure cases appear.

## 11. Security and reliability requirements

- Do not expose the vector database directly to browsers.
- Authenticate every search request.
- Derive tenant and project permissions from trusted server-side identity.
- Apply filters to every retrieval method.
- Keep API keys in a secret store.
- Validate input length and Top-K range.
- Add request timeouts and cancellation.
- Handle database unavailability without returning fabricated results.
- Log correlation IDs and useful metrics.
- Provide retry-safe ingestion and deletion workflows.
- Keep source documents and vectors in sync.

For a RAG extension, treat retrieved text as untrusted input. A document may contain instructions intended to manipulate a language model. Retrieved content should be treated as evidence, not as higher-priority system instructions.

## 12. Suggested implementation milestones

| Milestone | Deliverable |
|---|---|
| 1 | Local Qdrant starts and persists data |
| 2 | Markdown parser and chunker produce stable IDs |
| 3 | Embedding adapter generates validated vectors |
| 4 | Ingestion job upserts chunks and metadata |
| 5 | Search returns relevant source chunks |
| 6 | Metadata filters enforce project boundaries |
| 7 | Update and delete workflows remove stale chunks |
| 8 | ASP.NET Core API exposes search through an interface |
| 9 | Evaluation set measures retrieval relevance |
| 10 | Monitoring, configuration, and operational notes are documented |

## 13. Stretch goals

After the baseline works, extend the project with:

- Hybrid keyword and vector retrieval.
- Reranking.
- Multiple languages.
- Incremental indexing based on source version or file hash.
- PostgreSQL with pgvector as an alternative backend.
- Docker Compose for repeatable local development.
- CI checks for formatting, tests, and configuration validation.
- Azure deployment with private networking and managed identity where supported.
- A RAG response step that cites source files and abstains when evidence is insufficient.

## Practice exercise

Complete the milestones in order. Keep a README with setup instructions, environment variables, architecture, sample requests, evaluation results, and known limitations.

Before calling the project complete, demonstrate these scenarios:

1. A relevant question retrieves the expected document.
2. An unrelated question does not return misleading results.
3. A project filter excludes other projects.
4. Editing a Markdown file updates its indexed chunks.
5. Deleting a Markdown file removes its chunks.
6. Re-running ingestion does not create duplicate logical records.
7. A database outage produces a clear error rather than fabricated search results.
8. Evaluation metrics improve or remain stable after a retrieval change.

## Self-check

- Can I explain the complete ingestion and query flow?
- Can I explain why query and document embeddings must be compatible?
- Can I explain Top-K, score interpretation, and recall?
- Can I show that metadata filters are derived from trusted identity?
- Can I update and delete a document without leaving stale chunks?
- Can I measure retrieval quality with expected results?
- Can I switch between Qdrant and pgvector without changing the API contract?
- Can I explain how the application behaves when the database is unavailable?

## Final outcome

When this capstone is complete, I will have built a small semantic search system and understood the engineering work behind it: data preparation, embeddings, vector storage, filtering, retrieval, evaluation, application integration, and production readiness.
