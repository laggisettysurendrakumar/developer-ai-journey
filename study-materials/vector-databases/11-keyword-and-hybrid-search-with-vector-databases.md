# Keyword Search, Hybrid Search, and Reranking

## 1. Why semantic search is not enough by itself

Vector search is good at finding text with a similar meaning, even when the words are different. For example, a user may ask “How can I get my money back?” while a policy document says “refund procedure.”

However, semantic similarity can be weaker for exact identifiers and specialised terms:

- Error code ERR-2048.
- Product SKU AB-100.
- A person's exact name.
- A configuration key such as EnableFeatureX.
- A version number or legal clause reference.

A keyword search can be better for those exact terms. A production search system often combines both approaches.

## 2. Understand three retrieval approaches

**Keyword or lexical search** looks for matching words, tokens, phrases, or terms. Traditional inverted indexes and BM25-style ranking are common approaches.

**Vector or semantic search** compares the query embedding with stored document embeddings to find meaning-related candidates.

**Hybrid search** combines lexical and semantic results. The goal is to use the strengths of both.

| Query | Keyword search strength | Vector search strength |
|---|---|---|
| “ERR-2048” | Exact code match | May miss exact code |
| “I forgot my password” | Matches the same terms | Can find “reset credentials” |
| “SKU AB-100” | Exact product identifier | May return related products |
| “How do I change billing details?” | Matches billing words | Can find differently worded guidance |

## 3. Build two candidate lists

A hybrid retrieval flow can execute two searches:

1. A keyword search returns its ranked candidate list.
2. A vector search returns its ranked candidate list.
3. The application combines the two lists.
4. Duplicate document IDs are merged.
5. The combined list may be reranked.
6. The final Top-K records are passed to the application or RAG model.

The keyword and vector indexes may live in one database or in separate systems. The best choice depends on scale, capabilities, and operational requirements.

## 4. Reciprocal Rank Fusion

Reciprocal Rank Fusion (RRF) is a simple way to combine ranked lists without requiring the scores from different search systems to have the same scale.

For each document, RRF adds a contribution based on its position in each result list:

~~~text
RRF score = sum over result lists of 1 / (k + rank)
~~~

Here, k is a smoothing constant and rank is the document's one-based position in a result list. The constant is commonly selected through evaluation.

A small Python illustration:

~~~python
def reciprocal_rank_fusion(result_lists, k=60):
    scores = {}

    for results in result_lists:
        for rank, document_id in enumerate(results, start=1):
            scores[document_id] = (
                scores.get(document_id, 0.0)
                + 1.0 / (k + rank)
            )

    return sorted(
        scores.items(),
        key=lambda item: item[1],
        reverse=True,
    )


keyword_results = ["doc-10", "doc-20", "doc-30"]
vector_results = ["doc-20", "doc-40", "doc-10"]

combined = reciprocal_rank_fusion(
    [keyword_results, vector_results]
)

for document_id, score in combined:
    print(document_id, score)
~~~

This example combines IDs only. A real implementation should map IDs to canonical document records, apply authorization rules, and keep the original retrieval ranks for debugging.

## 5. Why raw scores are difficult to combine

A keyword engine may produce BM25 scores. A vector database may return cosine similarity or a distance value. Those values do not necessarily share the same scale or direction.

Simply adding the two raw scores can give misleading results.

Possible strategies include:

- Reciprocal Rank Fusion, which combines ranks.
- Score normalisation based on measured distributions.
- A trained ranking model.
- A cross-encoder reranker that evaluates the query and each candidate together.

Start with a simple and explainable approach such as RRF, then measure whether a more advanced method improves the result.

## 6. Reranking

Initial retrieval is designed to find a reasonably broad candidate set quickly. A reranker can then examine the query and candidate text more carefully and reorder the candidates.

A practical pipeline may look like this:

1. Retrieve the top 30 candidates from vector search.
2. Retrieve the top 30 candidates from keyword search.
3. Merge duplicates using stable document IDs.
4. Apply trusted access-control filters.
5. Rerank the remaining candidates.
6. Send the best 5 to the answer-generation step.

The counts are examples, not universal defaults. Reranking adds compute and latency, so I should test whether the quality improvement is worth its cost.

## 7. Query routing

Some systems use the query type to decide how much to rely on each retrieval method.

Examples:

- A query containing a known error-code pattern may emphasise keyword search.
- A natural-language question may benefit from vector search.
- A mixed query may use both.

This routing should be based on evaluated patterns rather than a collection of untested assumptions. Even when routing changes ranking, mandatory security filters must remain active.

## 8. Measure hybrid search

Create a test set containing exact identifiers, natural-language questions, short queries, long queries, multilingual queries if relevant, and ambiguous questions.

Measure:

- Recall@K: did the system retrieve the relevant document within the first K results?
- Precision@K: how many of the first K results were relevant?
- MRR: how early the first relevant result appears.
- nDCG: whether relevant results are ranked near the top with graded relevance.
- Latency and cost: including keyword, vector, merge, and rerank steps.

Compare keyword-only, vector-only, and hybrid search using the same query set.

## Practice exercise

1. Prepare ten search questions, including exact IDs and natural-language questions.
2. Create a keyword candidate list and a vector candidate list for each question.
3. Combine the lists using the RRF function.
4. Compare the combined order with each individual order.
5. Identify queries where hybrid search improves the result.
6. Design a reranking stage and measure its additional latency.
7. Document how access-control filters are enforced before results are returned.

## Self-check

- When is keyword search useful?
- When is vector search useful?
- Why can raw keyword and vector scores be difficult to add?
- What does RRF combine?
- What is reranking?
- Why should hybrid search be evaluated against keyword-only and vector-only baselines?

## Key takeaway

Hybrid search helps cover both exact terminology and semantic meaning. It should be measured against real queries, and security filters must apply to every retrieval path.
