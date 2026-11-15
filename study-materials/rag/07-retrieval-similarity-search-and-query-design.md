# 07 — Retrieval, Similarity Search, and Query Design

## Retrieval is the search stage

Retrieval finds candidate chunks that may contain evidence for a question. The quality of this stage strongly influences the answer. A language model cannot reliably answer from evidence that the retriever never found.

## 1. Top-k retrieval

The application typically requests the top k candidates according to a similarity or search score. A very small k may omit evidence; a very large k may add irrelevant context and increase cost.

Choose k using an evaluation set. It is a tunable parameter, not a universal constant.

## 2. Similarity threshold

A threshold can exclude weak matches, but scores depend on the model, metric, and database. Do not copy a threshold from another project without testing. A similarity score is not a calibrated probability that the answer is correct.

## 3. Query formulation

Users may ask vague questions, use abbreviations, or describe symptoms rather than the exact terms in a document. Query processing may include:
- normalisation,
- spelling correction,
- acronym expansion,
- query rewriting,
- decomposition into multiple sub-questions.

Keep the original question available and evaluate whether rewriting improves retrieval without changing its meaning.

## 4. Retrieval example — conceptual pseudocode

```python
def retrieve(query: str, user_context, vector_store, limit: int = 5):
    if not query.strip():
        raise ValueError("Question cannot be empty")
    if not 1 <= limit <= 10:
        raise ValueError("Limit must be between 1 and 10")

    # The store adapter must apply permission filters as part
    # of the trusted query, not after exposing restricted chunks.
    return vector_store.search(
        query=query,
        filters=user_context.allowed_filters,
        limit=limit,
    )
```

This is interface-oriented pseudocode; `vector_store.search` and `user_context` must be implemented by the chosen stack. The example highlights validation and permission-filter placement, not a specific vendor API.

## 5. Retrieval failure cases

- relevant document was not ingested,
- parser lost the important content,
- chunk boundary separated the fact from its context,
- query embedding is incompatible,
- similarity metric or index is unsuitable,
- metadata filter excluded the right document,
- top-k or threshold is poorly tuned,
- user terminology differs from document terminology.

Diagnose which stage failed before changing the prompt.

## Practice exercise

Create ten synthetic questions for your document set. For each question, record the expected source chunks, top-k results, ranking, and whether the evidence is sufficient. Identify at least three retrieval failures and their likely upstream cause.

## Self-check

1. What does top-k mean?
2. Why is a similarity score not answer confidence?
3. When might query rewriting help?
4. What are common causes of missing evidence?
5. Why should filters be applied inside a trusted search operation?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
