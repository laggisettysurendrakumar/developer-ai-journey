# Vector Databases and Semantic Search

I created this learning track to understand the topic from the beginning and apply it to real GenAI and agentic AI applications. Each concept is explained in plain language, followed by code, why it is useful, a developer use case, and an exercise.

## Who should use this guide
Developers who understand embeddings and want to store, search, filter, and maintain vector data for RAG or semantic search.

## Before starting
Learn Python data structures, embedding concepts, metadata, and the RAG overview first.

## Learning sequence
1. Understand vectors, dimensions, similarity, and distance measures.
2. Generate embeddings using the same compatible model for documents and queries.
3. Learn index, collection, point/record, payload/metadata, and namespace concepts.
4. Insert, retrieve, update, and delete sample vectors.
5. Apply metadata filters and access-control boundaries.
6. Compare exact search, approximate nearest neighbour search, keyword search, and hybrid search.
7. Understand persistence, backups, index tuning, and embedding-version migrations.
8. Measure retrieval quality, latency, and operational cost.

## Important concepts in plain language
### Vector
A vector is an ordered list of numbers. An embedding model produces a vector from content so a search system can compare it with other vectors.

### Dimension
The dimension is the number of values in a vector. A database collection generally expects vectors of the configured size; mismatched dimensions cause errors.

### Similarity metric
A metric such as cosine similarity or dot product scores how close two vectors are. The correct metric and normalisation depend on the embedding model and database.

### Metadata/payload
Metadata is information stored beside a vector, such as document ID, title, tenant, source, or access group. It supports filtering and citation.

### ANN index
Approximate nearest neighbour indexes trade a controlled amount of exactness for faster search at scale. Their parameters affect recall, latency, and memory.

## Practical examples
Example — understand a vector using a local cosine similarity function
This small example calculates cosine similarity without a database or embedding model. The vectors are illustrative numbers, not real embeddings.
```python
import math

def cosine_similarity(left: list[float], right: list[float]) -> float:
    if len(left) != len(right) or not left:
        raise ValueError("Vectors must have the same non-zero length")
    dot = sum(a * b for a, b in zip(left, right))
    left_norm = math.sqrt(sum(a * a for a in left))
    right_norm = math.sqrt(sum(b * b for b in right))
    if left_norm == 0 or right_norm == 0:
        raise ValueError("Zero vector has no cosine direction")
    return dot / (left_norm * right_norm)

print(cosine_similarity([1, 0], [0.9, 0.1]))
```
Use case: understand why a query embedding is compared with document embeddings before implementing a vector search service.
Example — metadata filter concept
A vector search result should be filtered by the user's access scope. This local example illustrates the idea, not database-level security.
```python
records = [
    {"id": "d1", "tenant": "team-a", "text": "Deployment guide"},
    {"id": "d2", "tenant": "team-b", "text": "Incident report"},
]

current_tenant = "team-a"
visible = [row for row in records if row["tenant"] == current_tenant]
print([row["id"] for row in visible])
```
Use case: a multi-tenant knowledge assistant must not return another customer's documents. Enforce this in the trusted service and database query.

## Mini-project
Create a small vector-search proof of concept using a local or approved vector database. Include collection creation, embedding version metadata, upsert, filtered query, update, delete, and a test that proves one tenant cannot see another tenant's rows.

## Safety, quality, and cost
- Use the same embedding model and compatible dimensions for indexed documents and query vectors.
- Do not rely on a model prompt to enforce tenant isolation; apply filters in trusted backend queries.
- Store embedding model/version metadata so records can be re-embedded deliberately.
- Back up and test restore procedures for persistent indexes.
- Compare retrieval quality and cost with a simple keyword baseline.

## Suggested practice routine
1. Read one section and explain the idea in your own words.
2. Type the example instead of copying it blindly.
3. Change one input and predict the output before running it.
4. Test an invalid input and inspect the error.
5. Complete the exercise and record what you learned.

## References
- Qdrant documentation: https://qdrant.tech/documentation/
- pgvector: https://github.com/pgvector/pgvector
- OpenAI embeddings: https://platform.openai.com/docs/guides/embeddings

Documentation and APIs change. Check the linked official documentation for current commands, SDKs, model availability, versions, and pricing before using an example in a real project.

## Self-check

1. What does an embedding vector represent?
2. What does vector dimension mean?
3. Why should the query and document use compatible embedding models?
4. What is the role of metadata filtering?
5. How does approximate nearest-neighbour search trade exactness for speed?
6. Why must tenant permissions be enforced by the backend query?

## Practice checklist

- [ ] I can explain vectors, dimensions, similarity, and metadata in plain language.
- [ ] I can create a sample collection/index and insert vectors.
- [ ] I can search and inspect returned metadata.
- [ ] I can apply an access filter and test tenant separation.
- [ ] I understand update, deletion, backup, and embedding-version migration.

**Next:** Apply vector retrieval in the RAG course.

