# Vector Databases and Semantic Search

I created this learning track to move from the basic idea of a vector to designing, building, evaluating, and operating a vector-search system. The course starts with plain-English explanations and small examples, then moves into Qdrant, PostgreSQL with pgvector, production engineering, and a practical developer knowledge assistant.

## Who this course is for

This course is for developers who want to understand how vector databases work and how they fit into GenAI, semantic search, and Retrieval-Augmented Generation (RAG) applications. It is suitable for beginners who are comfortable with basic programming and are willing to practise Python examples.

The examples use Python for learning and Qdrant as the main hands-on vector database. A later lesson compares PostgreSQL with pgvector. The architecture and engineering principles also apply when integrating vector search into an ASP.NET Core application.

## What I will be able to do

By completing the course and capstone, I should be able to:

- Explain vectors, embeddings, dimensions, distance metrics, and semantic search.
- Choose a compatible embedding model and configure a vector collection correctly.
- Store vectors with stable IDs and useful metadata.
- Search with query vectors and interpret ranked results.
- Apply metadata filters and design tenant-aware retrieval.
- Update, delete, and paginate through vector records safely.
- Explain ANN, HNSW, recall, latency, and index tuning.
- Combine keyword and vector retrieval.
- Compare a dedicated vector database with PostgreSQL and pgvector.
- Plan security, backups, monitoring, scaling, and migrations.
- Expose vector search through a clean application boundary.

## Before starting

Helpful background:

- Basic Python: lists, dictionaries, functions, classes, and exceptions.
- Basic API concepts: HTTP requests, JSON, and environment configuration.
- Basic SQL is useful for the PostgreSQL lesson.
- Read the [RAG learning track](../rag/README.md) to understand where retrieval fits into a larger application.

You do not need to know every vector database product before starting. The early lessons teach the concepts first.

## Course roadmap — 14 lessons

### Part 1 — Understand the fundamentals

1. [Vectors, embeddings, and semantic search fundamentals](01-vectors-embeddings-and-semantic-search-fundamentals.md) — What vectors represent, how embeddings are produced, and why semantic search is useful.
2. [Vector dimensions, distance metrics, and normalisation](02-vector-dimensions-distance-metrics-and-normalisation.md) — Dimensions, cosine similarity, dot product, Euclidean distance, and common mistakes.
3. [Vector database concepts: collections, points, payloads, and indexes](03-vector-database-concepts-collections-points-payloads-and-indexes.md) — Understand the vocabulary and how a vector record is organised.

### Part 2 — Build a working local database

4. [Install a local vector database and understand the development workflow](04-install-a-local-vector-database-and-understand-the-dev-workflow.md) — Run Qdrant locally, persist data, and inspect the service safely.
5. [Generate embeddings and prepare records for upsert](05-generate-embeddings-and-prepare-records-for-upsert.md) — Select a model, prepare text, validate dimensions, and structure records.
6. [Create collections and store vectors with Qdrant](06-create-collections-and-store-vectors-with-qdrant.md) — Create a collection and insert points using the Python client.

### Part 3 — Retrieve and manage data

7. [Search with query vectors, Top-K results, and scores](07-search-query-vectors-top-k-and-score-interpretation.md) — Search a collection, understand ranking, and measure relevance.
8. [Metadata filtering, payload indexes, and access control](08-metadata-filtering-payload-indexes-and-access-control.md) — Filter by business fields and protect tenant boundaries.
9. [Upsert, update, delete, scroll, and pagination](09-upsert-update-delete-scroll-and-pagination.md) — Keep indexed data in sync with changing source documents.
10. [Approximate nearest neighbour indexes and performance tuning](10-approximate-nearest-neighbour-indexes-and-performance-tuning.md) — Learn ANN, HNSW, recall, latency, and measured tuning.

### Part 4 — Advanced retrieval and production

11. [Keyword search, hybrid search, and reranking](11-keyword-and-hybrid-search-with-vector-databases.md) — Combine exact term matching with semantic retrieval and rank candidates.
12. [PostgreSQL, pgvector, and relational vector search](12-postgresql-pgvector-and-relational-vector-search.md) — Understand vector search inside a relational database and compare it with Qdrant.
13. [Production vector databases: security, backups, monitoring, and scaling](13-production-vector-database-security-backups-monitoring-and-scaling.md) — Plan operations, recovery, security boundaries, observability, and growth.

### Part 5 — Apply the concepts

14. [Capstone: build a developer knowledge assistant with vector search](14-capstone-developer-knowledge-assistant-vector-search.md) — Bring ingestion, embeddings, Qdrant, metadata filtering, evaluation, and an ASP.NET Core API boundary together.

## Recommended way to study

I recommend one lesson at a time:

1. Read the plain-English explanation and write down the key idea in your own words.
2. Type the example and understand each important parameter.
3. Predict what will happen when an input or setting changes.
4. Run the example in a local environment where possible.
5. Complete the exercise and answer the self-check questions.
6. Record any library or server version used, because client APIs and product features can change.

A reasonable first pass is 1–2 focused hours per lesson, with extra time for the capstone and troubleshooting. Understanding the trade-offs is more important than finishing quickly.

## Important design and safety principles

- Use compatible embedding models, preprocessing, and dimensions for document vectors and query vectors.
- Treat similarity scores as ranking signals, not as probabilities of correctness.
- Derive tenant identity and permissions from authenticated server-side context.
- Apply access filters to every retrieval path, including keyword and hybrid search.
- Keep credentials out of source control and avoid exposing database administration ports publicly.
- Keep the source system as the source of truth; vectors are derived data that may need to be rebuilt.
- Version embedding models, chunking settings, and ingestion logic.
- Test backups by restoring them, not only by checking that a backup file exists.
- Measure retrieval quality, latency, memory, throughput, and cost with representative workloads.
- Do not use synthetic example vectors as real semantic embeddings.

## How this connects to the rest of the repository

- [GenAI engineering](../genai/README.md): understand model APIs and embeddings.
- [Prompt engineering](../prompt-engineering/README.md): design clear instructions for the later RAG answer-generation step.
- [RAG engineering](../rag/README.md): combine retrieval with grounded answers and citations.
- [MCP](../mcp/README.md): expose carefully scoped knowledge-search capabilities to compatible clients.
- [Docker](../docker/README.md): package local services and repeatable development environments.
- [Terraform](../terraform/README.md): understand infrastructure-as-code for cloud deployment.
- [Azure](../azure/README.md): apply cloud identity, networking, monitoring, and operational practices.

## Official references

- [Qdrant documentation](https://qdrant.tech/documentation/)
- [Qdrant Python client](https://github.com/qdrant/qdrant-client)
- [pgvector](https://github.com/pgvector/pgvector)
- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)

APIs, supported metrics, configuration options, model availability, and pricing can change. I should check the official documentation for the versions selected by a project before copying examples into production.

## Completion checklist

- [ ] I can explain vectors, embeddings, dimensions, and semantic search.
- [ ] I understand distance metrics and how to choose a compatible configuration.
- [ ] I can run a local vector database and create a collection.
- [ ] I can prepare embeddings and upsert points with stable IDs.
- [ ] I can query the collection and inspect scores and payloads.
- [ ] I can apply metadata filters and test tenant isolation.
- [ ] I can update, delete, and paginate records safely.
- [ ] I understand ANN and can explain how I would benchmark HNSW settings.
- [ ] I can explain keyword, vector, hybrid, and reranking approaches.
- [ ] I understand the trade-offs between Qdrant and pgvector.
- [ ] I have a basic production security, backup, monitoring, and scaling plan.
- [ ] I have completed the developer knowledge assistant capstone.

**Next step:** use vector retrieval as one component of the [RAG course](../rag/README.md), and measure whether the retrieved sources actually answer the questions.
