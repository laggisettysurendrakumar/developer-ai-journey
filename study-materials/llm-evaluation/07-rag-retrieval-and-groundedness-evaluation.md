# 07 — RAG Evaluation: Retrieval and Groundedness

## Evaluate search separately from generation

A RAG system retrieves documents and then asks a model to generate an answer from the retrieved context. If retrieval returns the wrong evidence, the model may be unable to answer correctly.

Evaluate at least two distinct stages:
1. Did retrieval find the relevant evidence?
2. Did generation use that evidence correctly?

## 1. Retrieval metrics

- Recall@k: proportion of relevant documents found within the top k results.
- Precision@k: proportion of retrieved top-k documents that are relevant.
- Mean Reciprocal Rank (MRR): rewards systems that place the first relevant result near the top.
- nDCG: evaluates ranked relevance with graded labels.

Choose k and relevance labels based on the user experience. A system that retrieves many irrelevant passages may increase cost and distract the model.

## 2. Answer metrics

Evaluate:
- Correctness against authoritative evidence.
- Groundedness of material claims.
- Citation validity and relevance.
- Completeness of required facts.
- Appropriate no-answer behaviour.
- Access-control correctness.

A citation marker that points to a real document is not necessarily a good citation. Check that the cited passage supports the claim.

## 3. Build labelled retrieval cases

For each question, record the expected relevant document or passage IDs. Include multiple relevant sources, no-answer queries, similarly named documents, outdated revisions, and restricted documents.

## 4. Diagnose the retrieval pipeline

When retrieval scores are poor, inspect:
- Document extraction and parsing quality.
- Chunk boundaries and metadata.
- Embedding model and dimensions.
- Query transformation.
- Keyword versus vector retrieval.
- Metadata and permission filters.
- Re-ranking and top-k settings.
- Index freshness and deletion.

Change one variable at a time and record the experiment.

## 5. Evaluate permissions

Create test identities with different access. Confirm retrieval results are filtered before content is sent to the model. Prompt instructions are not an access-control mechanism.

## Practice exercise

Create 20 synthetic queries with expected source IDs. Measure recall@3 and MRR for a small retrieval dataset. Then test no-answer and restricted-document cases. Compare retrieval failures with answer-generation failures.

## Self-check

- Why split retrieval evaluation from answer evaluation?
- What does Recall@k measure?
- What does MRR reward?
- Why is a valid citation not automatically a supporting citation?
- When must access filtering happen?

## Official references

- OpenAI evaluation guide: https://platform.openai.com/docs/guides/evals
- RAG overview: https://learn.microsoft.com/azure/search/retrieval-augmented-generation-overview
- BEIR benchmark: https://github.com/beir-cellar/beir
