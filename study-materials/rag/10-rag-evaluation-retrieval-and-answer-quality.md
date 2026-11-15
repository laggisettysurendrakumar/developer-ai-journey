# 10 — RAG Evaluation: Retrieval and Answer Quality

## Why RAG needs separate evaluation

A RAG answer can fail because the right document was not found, because the model misunderstood the evidence, or because the answer was not properly cited. Measuring only final answer quality makes it difficult to identify the cause.

## 1. Build an evaluation dataset

Each test case should include:
- question,
- expected relevant document/chunk IDs,
- reference answer or key facts,
- expected citations,
- whether evidence is intentionally missing,
- user/tenant context where access matters,
- difficulty and case category.

Use normal, ambiguous, multi-hop, exact-identifier, no-answer, stale-source, and adversarial cases.

## 2. Retrieval metrics

Common metrics:

- **Recall@k:** fraction of relevant items found within the top k.
- **Precision@k:** fraction of the top k retrieved items that are relevant.
- **MRR:** rewards a relevant result appearing near the top of the ranked list.
- **nDCG:** evaluates ranking quality when relevance has multiple grades.

Define relevance consistently before interpreting these metrics. A metric is only meaningful relative to the annotation rules and dataset.

## 3. Answer metrics

Evaluate:
- correctness against the reference,
- groundedness/faithfulness to retrieved evidence,
- citation correctness,
- relevance to the question,
- completeness,
- appropriate abstention,
- clarity and usefulness.

Automated judges can help scale review but may be biased or inconsistent. Use human review for representative samples and high-impact decisions.

## 4. Error analysis

When a case fails, classify it:
- ingestion/parsing failure,
- chunking problem,
- embedding/model mismatch,
- retrieval ranking failure,
- filter/permission issue,
- reranker failure,
- prompt/context construction issue,
- generation hallucination,
- citation mapping failure.

Fix the actual stage instead of automatically rewriting the prompt.

## 5. Example evaluation record

```json
{
  "case_id": "api-timeout-001",
  "question": "Which runbook covers API timeouts?",
  "expected_chunk_ids": ["runbook-api:timeouts:1"],
  "expected_answer_facts": ["The API timeout runbook covers gateway and dependency timeouts."],
  "should_abstain": false
}
```

Use this as a data shape, not as a complete evaluation framework.

## Practice exercise

Build at least 20 synthetic RAG questions. Annotate relevant chunks and expected answer facts. Calculate Recall@3 and MRR for retrieval, then manually score answer groundedness and citation correctness. Group failed cases by pipeline stage.

## Self-check

1. Why evaluate retrieval and generation separately?
2. What does Recall@k measure?
3. What does MRR reward?
4. Which answer-quality criteria matter for RAG?
5. Why classify failures by pipeline stage?

## Further reading

- [LLM Evaluation course](../llm-evaluation/README.md)
- [OpenAI evals guide](https://platform.openai.com/docs/guides/evals)
