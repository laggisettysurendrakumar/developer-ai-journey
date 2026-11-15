# PostgreSQL, pgvector, and Relational Vector Search

## 1. Why store vectors in PostgreSQL?

A dedicated vector database is not the only way to perform vector search. PostgreSQL can store application records and vector embeddings together by using the pgvector extension.

This can be useful when:

- The application already uses PostgreSQL as its primary database.
- The vector collection is closely connected to relational business data.
- The team prefers fewer database technologies to operate.
- SQL joins, transactions, and existing PostgreSQL controls are important.

A dedicated vector database may be a better choice when the workload needs specialised vector features, very large scale, independent scaling, or operational capabilities that fit that product. I should compare real requirements rather than assume one approach is always best.

## 2. What pgvector adds

PostgreSQL stores the relational data. The pgvector extension adds a vector data type, distance operations, and vector indexing options.

At a high level:

- A row represents a document or a document chunk.
- A vector column stores its embedding.
- Ordinary columns store the title, tenant, status, and source.
- A vector index accelerates nearest-neighbour retrieval.
- SQL filters and joins can combine vector retrieval with business data.

## 3. Enable the extension

The database administrator must install a compatible pgvector extension package on the PostgreSQL server. Once installed, a database administrator or suitably privileged role can enable it in a database.

~~~sql
CREATE EXTENSION IF NOT EXISTS vector;
~~~

The SQL above enables the extension in the selected database. It does not install the server package itself.

## 4. Create a table

The vector dimension in this example is 384. That number is only an example. It must match the output dimension of the embedding model used by the application.

~~~sql
CREATE TABLE knowledge_chunks (
    id BIGSERIAL PRIMARY KEY,
    tenant_id TEXT NOT NULL,
    document_id TEXT NOT NULL,
    chunk_number INTEGER NOT NULL,
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    embedding vector(384),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (tenant_id, document_id, chunk_number)
);
~~~

The uniqueness constraint helps prevent duplicate chunk numbers for one document within a tenant. The exact key design should reflect the source system and update workflow.

## 5. Insert a vector

The application generates the embedding before inserting the row. The placeholder below represents a parameter supplied by a database driver. It is not a vector value to paste literally.

~~~sql
INSERT INTO knowledge_chunks (
    tenant_id,
    document_id,
    chunk_number,
    title,
    content,
    embedding
)
VALUES (
    :tenant_id,
    :document_id,
    :chunk_number,
    :title,
    :content,
    :embedding
);
~~~

Use parameterised queries. Do not build SQL by concatenating user text or vector values into the query string.

## 6. Search with a query vector

The application generates a query embedding using the same compatible model and dimensions used for the stored embeddings.

A pgvector query orders candidate rows by a distance expression supported by pgvector and the selected distance metric. The metric-specific operator is documented by pgvector. I should select the correct operator for cosine distance, Euclidean distance, or inner product, and ensure that the index operator class matches that metric.

A query pattern looks like this:

~~~sql
SELECT
    id,
    document_id,
    title,
    content
FROM knowledge_chunks
WHERE tenant_id = :tenant_id
ORDER BY /* pgvector distance expression for the selected metric */
LIMIT :top_k;
~~~

The ORDER BY comment is an intentional placeholder, not executable SQL. Replace it with the documented pgvector distance expression for the metric and index chosen in the project. This avoids hiding an important metric choice inside a copied example.

The tenant condition is part of the server-side query. It must be derived from authenticated application context, not trusted from an arbitrary browser parameter.

## 7. Indexes and query planning

pgvector supports exact search and approximate index approaches. Common approximate index families include HNSW and IVFFlat. They have different build, memory, and recall characteristics.

The right index depends on:

- Number of vectors.
- Vector dimensions.
- Dataset update frequency.
- Required recall.
- Query latency target.
- Available memory and storage.
- Filter selectivity.
- PostgreSQL and pgvector versions.

After creating an index, inspect query plans and benchmark representative searches. A query that includes a tenant filter may behave differently from a query without the filter. Do not assume an index is being used just because it exists.

## 8. Combine vectors with relational data

One benefit of PostgreSQL is the ability to join search records with normal application tables.

For example, an application can keep document ownership, subscription status, and permissions in relational tables. The query service can combine the vector candidate set with those business rules.

However, the application must still design the query carefully. A join that happens after a broad vector search may waste work or expose results if permission checks are incomplete. Security should be enforced within the trusted data access path.

## 9. Qdrant or pgvector?

| Question | Dedicated vector database | PostgreSQL with pgvector |
|---|---|---|
| Existing operational platform | Adds a database technology | Reuses PostgreSQL |
| Relational joins | Usually handled in application or another store | Native SQL joins |
| Vector-focused features | Often a primary design focus | Extension capabilities |
| Independent scaling | Can scale separately | Shares PostgreSQL resources unless separately designed |
| Operations | New service to secure and monitor | Extend existing PostgreSQL operations |
| Best decision | Based on vector workload and product features | Based on relational integration and existing platform |

Neither option is automatically the winner. Build a small representative proof of concept and compare result quality, latency, cost, operational burden, and security.

## 10. Practical integration from a .NET application

For an ASP.NET Core service, I can keep the design provider-neutral:

- IEmbeddingService converts text into a vector.
- IVectorSearchRepository stores and searches vectors.
- The repository owns database-specific SQL.
- The application service handles authentication context and business rules.
- The API returns a response DTO rather than a database row.

This structure allows the storage technology to change without spreading database-specific code across controllers.

## Practice exercise

1. Enable pgvector on a local PostgreSQL environment where the extension package is installed.
2. Create the knowledge_chunks table with the correct dimension for a chosen embedding model.
3. Insert a few sample records using parameterised queries.
4. Implement a search query using the correct pgvector distance expression.
5. Add a tenant condition and test that another tenant's rows cannot be returned.
6. Compare exact search with an approximate index.
7. Compare the same dataset and query set against Qdrant.
8. Record quality, latency, operational complexity, and cost.

## Self-check

- What does pgvector add to PostgreSQL?
- Why must the vector column dimension match the embedding model?
- Why is the ORDER BY expression a metric-specific choice?
- What is the difference between exact and approximate search?
- Why can relational joins be useful for vector retrieval?
- When might a dedicated vector database be preferable?

## Key takeaway

pgvector makes PostgreSQL a practical vector-search option for many applications. The choice between pgvector and a dedicated vector database should come from measured workload needs and operational trade-offs.
