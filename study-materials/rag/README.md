# Retrieval-Augmented Generation (RAG) — Beginner to Expert Learning Path

I use this course to learn how to build reliable AI applications that answer from an approved knowledge base. It begins with the basic RAG idea and progresses through document ingestion, chunking, embeddings, vector retrieval, hybrid search, grounded generation, evaluation, security, operations, and a complete developer-assistant capstone.

The objective is to understand the whole system rather than simply learn one vector database or framework.

## Who this course is for

Developers who know basic programming, JSON, APIs, and the fundamentals of LLMs. Python is used for small teaching examples. The capstone also includes an ASP.NET Core integration challenge.

## What I should be able to do by the end

- Explain the indexing and query pipelines and when RAG is appropriate.
- Parse documents while preserving source structure and metadata.
- Design and compare chunking strategies.
- Understand embeddings, similarity, vector indexes, and storage options.
- Build retrieval with top-k, filters, and query processing.
- Compare keyword, vector, hybrid, and reranked retrieval.
- Generate evidence-based answers with citations and safe abstention.
- Evaluate retrieval and answer quality separately.
- Protect private data with permission-aware retrieval and tenant isolation.
- Operate incremental ingestion, re-indexing, deletion, and recovery.
- Design production latency, caching, cost, monitoring, and reliability controls.
- Build a Developer Knowledge Assistant and understand its .NET service boundaries.

## Learning path

| Lesson | Topic | Outcome |
|---:|---|---|
| 01 | [RAG fundamentals and when to use RAG](01-rag-fundamentals-and-when-to-use-rag.md) | Understand the problem and the RAG pattern |
| 02 | [RAG architecture and end-to-end data flow](02-rag-architecture-and-end-to-end-data-flow.md) | Understand indexing and query stages |
| 03 | [Document ingestion, parsing, and normalisation](03-document-ingestion-parsing-and-normalisation.md) | Prepare reliable source content |
| 04 | [Chunking strategies and metadata design](04-chunking-strategies-and-metadata-design.md) | Split content without losing meaning |
| 05 | [Embeddings, models, and vector representations](05-embeddings-models-and-vector-representations.md) | Understand semantic representations |
| 06 | [Vector indexes and storage backends](06-vector-indexes-and-storage-backends.md) | Choose and operate a vector store |
| 07 | [Retrieval, similarity search, and query design](07-retrieval-similarity-search-and-query-design.md) | Retrieve useful evidence |
| 08 | [Hybrid search, keyword retrieval, and reranking](08-hybrid-search-keyword-retrieval-and-reranking.md) | Improve ranking for exact and semantic queries |
| 09 | [Grounded generation, citations, and abstention](09-grounded-generation-citations-and-abstention.md) | Answer from evidence and admit missing information |
| 10 | [RAG evaluation: retrieval and answer quality](10-rag-evaluation-retrieval-and-answer-quality.md) | Measure each part of the pipeline |
| 11 | [RAG security, access control, and prompt injection](11-rag-security-access-control-and-prompt-injection.md) | Protect documents and users |
| 12 | [RAG pipeline engineering and incremental indexing](12-rag-pipeline-engineering-incremental-indexing-and-operations.md) | Handle updates, deletes, and failures |
| 13 | [Production RAG architecture, caching, and performance](13-production-rag-architecture-caching-and-performance.md) | Operate a reliable production service |
| 14 | [Capstone: Developer Knowledge Assistant with RAG](14-capstone-developer-knowledge-assistant-rag.md) | Build an end-to-end solution |

## Suggested learning pace

A first pass can take 3–4 weeks at 1–2 focused hours per day. Spend additional time on chunking, retrieval evaluation, permission filtering, and the capstone. Reading about RAG is not enough: run the pipeline against a labelled dataset and inspect failed retrievals.

## Recommended prerequisites and environment

- Basic Python, JSON, HTTP APIs, and Git.
- GenAI fundamentals: [GenAI course](../genai/README.md).
- Prompt structure and safety: [Prompt Engineering course](../prompt-engineering/README.md).
- Embedding and vector database concepts: [Vector Database course](../vector-databases/README.md).
- A Python virtual environment and pinned dependencies.
- An approved embedding/generation provider or local model for experimentation.
- A local or hosted vector store.
- Synthetic or public documents for initial work.

Never commit provider keys, private company documents, customer data, or production logs.

## How to study each lesson

1. Read the plain-English explanation before using a framework.
2. Draw the indexing or query flow in your own words.
3. Type and run the example, checking the expected output.
4. Use a small, known document set and labelled questions.
5. Complete the exercise and self-check.
6. Record model, embedding, parser, chunker, and database versions.
7. Inspect failed cases before changing parameters or prompts.

## Core engineering principles

- Preserve source IDs, version, heading/page, and access metadata through every stage.
- Start with a simple baseline before adding hybrid search or reranking.
- Tune chunking, top-k, and filters using representative questions.
- Evaluate retrieval separately from answer groundedness and citation quality.
- Permit a clear abstention when evidence is insufficient.
- Enforce user and tenant permissions before restricted chunks reach the model.
- Treat retrieved content and tool outputs as untrusted data.
- Make ingestion repeatable, idempotent, incremental, and deletion-aware.
- Track index and embedding model versions; plan safe migrations.
- Measure quality, latency, cost, and freshness continuously.

## How this course connects to other tracks

- [GenAI](../genai/README.md) — model APIs, inference, and embeddings.
- [Prompt Engineering](../prompt-engineering/README.md) — grounded prompt design and input boundaries.
- [Vector Databases](../vector-databases/README.md) — indexes, metadata filters, and similarity search.
- [Agentic AI](../agentic-ai/README.md) — use retrieval as a controlled agent tool.
- [MCP](../mcp/README.md) — expose approved retrieval capabilities through a standard protocol.
- [LLM Evaluation](../llm-evaluation/README.md) — build retrieval and answer evaluation datasets.
- [Docker](../docker/README.md) and [Azure](../azure/README.md) — package, deploy, and operate the service.

## Official references

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- [OpenAI file search guide](https://platform.openai.com/docs/guides/tools-file-search)
- [OpenAI retrieval guide](https://platform.openai.com/docs/guides/retrieval)
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
- [OpenTelemetry documentation](https://opentelemetry.io/docs/)

Provider APIs, model names, pricing, and vector database capabilities change. Confirm current documentation and pin versions before using a sample in a real project.

This is a learning track, not a production certification. Production use requires security review, data governance approval, representative evaluation, operational ownership, and deployment validation.
