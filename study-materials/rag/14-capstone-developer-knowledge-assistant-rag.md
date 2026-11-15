# 14 — Capstone: Developer Knowledge Assistant with RAG

## Capstone outcome

Build a RAG-powered Developer Knowledge Assistant over approved, synthetic documentation. The objective is to demonstrate the complete lifecycle: ingestion, chunking, embeddings, indexing, retrieval, grounded generation, evaluation, security, and operations.

The capstone is a learning implementation. Do not connect company-private data or production systems until security, privacy, and operational reviews are complete.

## 1. Functional requirements

The first version should ingest Markdown documents, preserve document ID/source URI/heading/version, split documents into meaningful chunks, generate embeddings using one selected model, store vectors with metadata, retrieve relevant chunks, return answers with citations, abstain when evidence is insufficient, enforce document permissions, and record safe telemetry.

## 2. Suggested architecture

```text
                 INDEXING
Markdown Sources -> Parse -> Normalise -> Chunk
                                      |
                                      v
                                  Embeddings
                                      |
                                      v
                                Vector Index

                  QUERY
User -> Auth -> Question -> Permission-aware Retrieval
                                  |
                                  v
                          Hybrid Search / Rerank
                                  |
                                  v
                            Context Selection
                                  |
                                  v
                              Grounded LLM
                                  |
                                  v
                         Citation Validation
                                  |
                                  v
                           Answer + Sources
```

## 3. Implementation milestones

**Milestone A — Document pipeline**
- Load five synthetic Markdown documents.
- Extract headings and stable source identifiers.
- Add content hashes and a repeatable ingestion manifest.
- Test changed and deleted sources.

**Milestone B — Chunking and embeddings**
- Implement heading-aware chunking.
- Compare at least two chunk-size settings.
- Generate embeddings using a compatible model.
- Store chunk records with version and source metadata.

**Milestone C — Retrieval**
- Create at least ten questions with expected relevant chunks.
- Implement top-k similarity retrieval.
- Add metadata filtering.
- Measure Recall@k and inspect incorrect rankings.

**Milestone D — Grounded generation**
- Build a prompt with labelled evidence and source IDs.
- Ask the model to abstain when evidence is insufficient.
- Validate source identifiers in the returned citations.
- Test contradictory and malicious documents.

**Milestone E — Quality and operations**
- Compare vector-only and hybrid retrieval.
- Add reranking only if evaluation demonstrates value.
- Measure retrieval and generation quality separately.
- Record latency, failure rate, ingestion lag, and cost.
- Add a runbook for ingestion or vector-store failure.

## 4. Suggested project structure

```text
developer-knowledge-rag/
├── app/
│   ├── ingestion/
│   ├── parsing/
│   ├── chunking/
│   ├── embeddings/
│   ├── retrieval/
│   ├── generation/
│   ├── security/
│   └── api/
├── data/
│   └── synthetic-docs/
├── evaluations/
│   ├── retrieval-cases.jsonl
│   └── answer-cases.jsonl
├── tests/
├── config/
└── README.md
```

This is a conceptual layout. Use the modules and file formats that fit your chosen framework.

## 5. Acceptance criteria

The capstone is complete when:
1. Every returned chunk contains a valid source ID and document version.
2. Re-running ingestion does not create duplicate chunks.
3. A changed source updates its indexed representation.
4. A deleted/revoked source is no longer retrievable.
5. Permission filters prevent cross-user/tenant retrieval.
6. Retrieval metrics are measured on a labelled dataset.
7. Answers cite retrieved evidence and do not fabricate source IDs.
8. Missing evidence produces a clear abstention.
9. Prompt injection inside a document does not grant new authority.
10. Latency, failures, index version, and cost are observable.

## 6. .NET integration challenge

After completing the Python prototype, design an ASP.NET Core API that calls the same logical RAG components through interfaces:
- `IDocumentIngestionService`
- `IEmbeddingService`
- `IRetrievalService`
- `IAnswerGenerationService`

Pass authenticated identity to retrieval and enforce permission filters in the backend. Keep provider SDK details in adapters and verify current package APIs before implementation.

## Practice exercise

Build the capstone in a Git branch. Include setup instructions, chosen model/store versions, sample synthetic documents, evaluation dataset, architecture diagram, security assumptions, and troubleshooting notes. Demonstrate one normal answer, one no-answer case, one stale-source case, and one denied-access case.

## Self-check

1. What are the indexing and query pipelines?
2. Why preserve source IDs and version metadata?
3. How do you prove ingestion is idempotent?
4. Which metrics assess retrieval separately from answer quality?
5. How do you prove restricted content never reaches the model?

## Definition of done

A RAG prototype becomes a production candidate only after permission testing, privacy review, quality evaluation, operational ownership, backup/restore planning, and deployment validation.
