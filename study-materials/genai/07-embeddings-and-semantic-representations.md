# 07 — Embeddings and Semantic Representations

## What is an embedding?

An embedding is a numeric vector representing content in a way that captures aspects of its meaning or features. Text that is semantically related may have vectors that are close under a chosen similarity measure.

Embeddings are useful for semantic search, clustering, recommendation, duplicate detection, and retrieval-augmented generation.

## 1. Text to vector

Conceptually:

```text
"How do I restart an API?"
          |
          v
     Embedding model
          |
          v
[0.12, -0.08, 0.44, ...]
```

The actual vector dimension and values depend on the model. You should not invent vectors or compare embeddings from unrelated models.

## 2. Similarity

A vector database or search service can compare vectors using measures such as cosine similarity, dot product, or Euclidean distance, depending on the model and index configuration.

A similarity score indicates closeness according to that method. It is not a probability that the content is correct.

## 3. Model compatibility

Use compatible embedding models for documents and queries. Changing embedding model or dimensions often requires re-embedding documents and rebuilding the relevant index. Check model documentation for dimensions, input limits, and normalisation expectations.

## 4. Embeddings are not a complete search system

A useful search application also needs:
- Document IDs and metadata.
- Access filters.
- Updates and deletion.
- Keyword search where exact terms matter.
- Ranking and relevance evaluation.
- Source references.
- Handling for no-result queries.

Read the dedicated vector database and RAG tracks for indexing, retrieval, chunking, and hybrid search design.

## Practice exercise

Create a small set of synthetic support questions and answers. Use an approved embedding model to embed them, retrieve nearest neighbours for sample questions, and manually evaluate whether the top results are relevant. Do not interpret vector distance as a truth score.

## Self-check

- What is an embedding?
- Why can semantically related text have close vectors?
- Why is similarity not the same as correctness?
- Why must document and query embeddings be compatible?
- What additional metadata does a search application need?

## Official references

- OpenAI embeddings: https://platform.openai.com/docs/guides/embeddings
- Vector databases track: ../vector-databases/README.md
- RAG track: ../rag/README.md
