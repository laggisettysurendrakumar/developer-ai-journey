# 08 — Hybrid Search, Keyword Retrieval, and Reranking

## Why vector search is not enough for every question

Semantic search can find passages with related meaning even when they use different words. However, exact identifiers, error codes, method names, product versions, and configuration keys often require keyword matching too.

Hybrid retrieval combines complementary search methods.

## 1. Keyword search

Keyword retrieval is useful for:
- exact error strings,
- ticket IDs,
- class and method names,
- configuration keys,
- version strings,
- product identifiers.

Traditional search may use inverted indexes and ranking functions such as BM25. It can perform very well on exact terms but may miss paraphrases.

## 2. Vector search

Vector retrieval can find semantically related passages even when vocabulary differs. It may be weaker for exact identifiers and unusual strings. That is why it often complements keyword retrieval.

## 3. Hybrid retrieval

A hybrid system combines lexical and vector candidates. Common approaches include:
- weighted score fusion,
- reciprocal rank fusion,
- retrieve from both sources then deduplicate,
- rerank a merged candidate list.

Do not add raw scores from unrelated search systems without normalisation or a tested fusion method.

## 4. Reranking

A reranker receives a query and candidate passages and scores how relevant each passage is to that query. It can improve the ordering of a small candidate set, but it adds latency and possibly cost.

A common pipeline:

```text
Question
  |
  +--> Keyword search ----+
  |                       |
  +--> Vector search -----+--> Merge and deduplicate
                                  |
                                  v
                              Reranker
                                  |
                                  v
                           Select evidence
                                  |
                                  v
                           Generate answer
```

## 5. When to add complexity

Start with a simple vector baseline. Add keyword search when exact terms matter. Add reranking when measured retrieval quality justifies the extra cost and latency. Each extra component should solve an observed problem.

## Practice exercise

Build a test set with semantic questions and exact-identifier questions. Compare vector-only, keyword-only, and hybrid retrieval. If a reranker is available, measure the change in ranking and latency. Record cases where the hybrid method is worse.

## Self-check

1. What kind of query is keyword search especially good at?
2. What does vector retrieval contribute?
3. What does hybrid retrieval combine?
4. Why should unrelated raw scores not be added casually?
5. What trade-off does reranking introduce?

## Further reading

- [OpenAI file search](https://platform.openai.com/docs/guides/tools-file-search)
- [Information retrieval concepts](https://nlp.stanford.edu/IR-book/)
