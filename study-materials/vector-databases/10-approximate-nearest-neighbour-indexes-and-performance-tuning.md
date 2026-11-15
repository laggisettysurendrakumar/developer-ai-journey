# Approximate Nearest Neighbour Indexes and Performance Tuning

## 1. Why do vector databases need indexes?

Imagine a collection with 100 vectors. A database could compare the query vector with every stored vector and sort the results. This is an exact or brute-force search approach.

Now imagine a collection with 100 million vectors. Comparing every stored vector for every query can become too slow and expensive.

A vector index helps the database find promising nearby candidates without examining every vector. This is commonly called Approximate Nearest Neighbour search, or ANN.

Approximate search trades some search accuracy for better performance. The goal is to find a configuration that meets the application's latency and relevance requirements.

## 2. Exact search and approximate search

**Exact search** calculates distances against every candidate in the search space. It can provide an exact nearest-neighbour result, but the work grows with the number of vectors.

**Approximate search** uses an index to narrow down the candidates. It is usually faster at scale, but it may miss a true nearest neighbour.

I should not assume ANN is always better. For a tiny dataset, exact search may be simpler and sufficiently fast. For a large production dataset, ANN is often necessary.

## 3. HNSW in plain language

HNSW means Hierarchical Navigable Small World. It is a graph-based ANN approach.

Think of a city road network. Instead of checking every address, I use connected roads that take me closer to the destination. HNSW builds layers of links between vectors. Search starts at a higher, more sparse layer and navigates toward promising regions before exploring more detailed connections.

Important HNSW controls include:

- **M:** Controls the number of connections created for each node. Higher values can improve recall but use more memory and build time.
- **ef_construct:** Controls the search effort while building the graph. Higher values may improve index quality but make indexing slower.
- **ef:** Controls search effort at query time. Higher values often improve recall at the cost of latency.

The exact configuration names and defaults depend on the database and version.

## 4. Understand the main performance factors

| Factor | What it affects | What to measure |
|---|---|---|
| Vector dimensions | Memory and distance calculation work | Memory, latency |
| Number of vectors | Index size and search workload | Index size, latency |
| HNSW settings | Recall, build time, memory | Recall and p95 latency |
| Payload filters | Candidate set and filter cost | Filtered latency |
| Top-K | Result count and downstream work | Latency and relevance |
| Batch size | Ingestion throughput and memory | Documents per second |
| Replicas | Availability and read capacity | Availability, cost |
| Network distance | Request overhead | End-to-end latency |

## 5. A tuning method that avoids guesswork

I should tune the system using a repeatable benchmark:

1. Create a representative dataset, not only a few toy vectors.
2. Prepare a query set with known relevant documents.
3. Run an exact baseline where the database supports it.
4. Measure approximate search recall against the baseline.
5. Measure p50, p95, and p99 latency.
6. Repeat with different index parameters and Top-K values.
7. Include real metadata filters in the benchmark.
8. Test under expected concurrent load.
9. Record memory, CPU, disk, and index build time.
10. Select the configuration that meets the application's service objectives.

P50 is the median latency. P95 means 95 percent of requests completed within that time. P99 is useful for understanding slow tail requests.

## 6. Qdrant HNSW configuration example

Qdrant supports collection and vector index configuration. The exact API and accepted parameters can change between client versions, so I should verify against the installed Qdrant server and Python client documentation.

A configuration example:

~~~python
from qdrant_client import QdrantClient, models

client = QdrantClient(path="./qdrant_data")

client.create_collection(
    collection_name="articles_hnsw",
    vectors_config=models.VectorParams(
        size=384,
        distance=models.Distance.COSINE,
    ),
    hnsw_config=models.HnswConfigDiff(
        m=16,
        ef_construct=100,
    ),
)
~~~

The dimensions above are an example only. I must use the actual dimension count produced by the embedding model. I should not copy index settings into production without benchmarking.

Some databases support exact search options, quantization, on-disk storage, and other index settings. These are separate design choices and should be measured.

## 7. Memory and quantization

A vector with many floating-point dimensions consumes memory. A large collection can require substantial memory for raw vectors plus index structures and metadata.

Quantization stores a compressed representation of vectors to reduce memory use and sometimes improve search speed. Compression can affect recall, so the database may use original vectors to refine candidate results.

Before enabling quantization, measure:

- Memory savings.
- Recall change.
- Query latency.
- Index build time.
- Disk use.
- Whether exact reranking is available and needed.

## 8. Filter-aware performance

A search without filters may behave differently from a search that includes tenant, status, date, and category filters.

I should benchmark the actual request patterns. A filter that matches nearly every record is different from a filter that matches a tiny fraction of the collection. Indexing metadata may improve filter execution, but it also consumes resources.

Security filters must remain enabled during performance tests. Never remove tenant isolation just to produce a faster benchmark.

## 9. Common tuning mistakes

- Increasing HNSW parameters without measuring recall and latency.
- Testing with a tiny dataset and assuming results will scale.
- Benchmarking only average latency while ignoring p95 and p99.
- Ignoring filters and concurrency.
- Forgetting that vector dimensions affect memory.
- Optimising query speed while making ingestion too slow.
- Comparing scores from different embedding models as though they were directly equivalent.

## Practice exercise

1. Create a collection with a representative sample size.
2. Write a repeatable query benchmark.
3. Record latency for several Top-K values.
4. Compare exact and approximate search if your selected database supports both.
5. Change one HNSW parameter at a time.
6. Record recall, p50, p95, memory, and build time.
7. Explain which configuration you would choose for a support knowledge base and why.

## Self-check

- What does ANN stand for?
- Why can ANN return a different result from exact search?
- What is HNSW at a high level?
- What do M, ef_construct, and ef influence?
- Why is p95 latency useful?
- Why should I benchmark filtered queries?
- What trade-off can quantization introduce?

## Key takeaway

Index tuning is an evidence-based engineering task. I should define relevance and latency targets, benchmark representative workloads, and change one variable at a time.
