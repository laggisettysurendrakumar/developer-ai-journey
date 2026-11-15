# 06 — Vector Indexes and Storage Backends

## Why store vectors?

A vector index stores embeddings and supports similarity retrieval. In a RAG system, it also needs to store or reference the original chunk text and metadata so the application can retrieve useful evidence after finding a similar vector.

A vector database is one possible storage option. Depending on scale and requirements, the application may use a dedicated vector database, a relational database with vector support, a search engine, or a managed retrieval service.

## 1. What belongs in a vector record?

Typical fields:
- unique chunk ID,
- vector,
- chunk text or content reference,
- document ID,
- source URI,
- title and heading,
- document version,
- access-control metadata,
- embedding model and index version.

Avoid placing secrets or unnecessary personal information in metadata.

## 2. Index concepts

Vector databases may support:
- exact or approximate nearest-neighbour search,
- configurable similarity metrics,
- metadata filters,
- namespaces or collections,
- update and delete operations,
- index parameters that trade recall for speed.

Approximate nearest-neighbour indexes are designed to search large vector collections efficiently, but they may trade exactness for performance. Evaluate recall and latency with your own workload.

## 3. Metadata filters

A query may need to filter by product, version, environment, tenant, department, or document classification. Where access restrictions apply, the backend should apply them as part of the trusted retrieval operation—not after restricted text is sent to the model.

Do not assume that metadata filtering is automatically secure. Test cross-user and cross-tenant access explicitly.

## 4. Storage options

| Option | When it may fit | Considerations |
|---|---|---|
| Local in-memory index | Learning and small tests | Data disappears on restart |
| Local persistent vector store | Prototypes and local apps | File durability, backups, access control |
| Relational DB with vector support | Existing data platform | Extension/version and query capabilities |
| Dedicated vector database | Large or specialised search workloads | Operations, cost, tenancy, backups |
| Managed retrieval/file search | Faster provider-managed implementation | Provider feature limits, data handling, portability |

## 5. Operational questions

Before choosing storage, answer:
- How many chunks are expected?
- How frequently do documents change?
- What is the query rate and latency target?
- Are there multiple tenants?
- Which filters must be enforced?
- How are backups, deletion, and restore handled?
- How will an embedding migration be completed?

## Practice exercise

Compare a local prototype store with one persistent backend for a synthetic documentation collection. Define the record schema, metadata filters, update/delete process, backup needs, and expected query workload. Do not choose a database based only on popularity.

## Self-check

1. Why store metadata with vectors?
2. What is approximate nearest-neighbour search?
3. How can metadata filters affect retrieval?
4. Why must permission filters be tested?
5. Which operational factors influence vector-store selection?

## Further reading

- [Vector database course](../vector-databases/README.md)
- [OpenAI embeddings](https://platform.openai.com/docs/guides/embeddings)
