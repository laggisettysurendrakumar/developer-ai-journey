# 01 — Vectors, Embeddings, and Semantic Search Fundamentals

## What this lesson is about

A vector database stores and searches numeric vectors. In AI applications, those vectors often come from an embedding model that converts text, images, or other content into a numeric representation.

I start with the idea of a vector before using a database. That makes similarity search easier to understand.

## 1. What is a vector?

A vector is an ordered list of numbers. For example:

```python
document_vector = [0.12, -0.45, 0.88, 0.21]
query_vector = [0.10, -0.40, 0.82, 0.25]
```

These four-number vectors are made up examples. Real embedding vectors usually have many more dimensions and their values are produced by a model.

## 2. What is an embedding?

An embedding model transforms input content into a vector. It is trained so that useful relationships in the input are reflected in the vector space. Texts with related meanings may be close according to a selected distance or similarity metric.

An embedding is not:
- a text summary,
- a database ID,
- a keyword list,
- a guarantee that two passages mean exactly the same thing.

It is a numeric representation that supports similarity-based retrieval.

## 3. What is semantic search?

Keyword search looks for matching words or terms. Semantic search looks for related meaning using vector representations.

Example:
- Stored document: “How to restart an ASP.NET Core service safely.”
- User query: “What should I do when my web API is stuck?”

The words differ, but the meaning may be related. Semantic search can help find the relevant passage even without an exact phrase match.

## 4. Why use a vector database?

A vector database or vector-capable search engine can store vectors and metadata, find nearest vectors, filter records, and manage large search collections more efficiently than manually comparing every vector in application code.

It is commonly used for:
- RAG knowledge assistants,
- semantic document search,
- recommendations,
- duplicate or similarity detection,
- multimodal retrieval.

## 5. Where it fits in RAG

```text
Documents -> Chunks -> Embedding model -> Vector database
                                            ^
                                            |
User question -> Query embedding -> Similarity search
                                            |
                                            v
                              Relevant passages -> LLM
```

The vector database retrieves candidate passages. It does not generate the final answer. A separate language model usually does that in a RAG application.

## Practice exercise

Write five synthetic technical document titles and five questions that could refer to them using different wording. Explain why keyword search may miss some pairs and why semantic search may find them.

## Self-check

1. What is a vector?
2. What does an embedding model produce?
3. How does semantic search differ from keyword search?
4. Does a vector database generate natural-language answers?
5. Why is semantic similarity not proof that two documents are equivalent?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- [Qdrant documentation](https://qdrant.tech/documentation/)
