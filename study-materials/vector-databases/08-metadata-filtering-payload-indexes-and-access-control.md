# Metadata Filtering, Payload Indexes, and Access Control

## What problem does metadata solve?

A vector tells the database something about the meaning of a record. It does not tell the application everything it needs to know about that record.

A support article may also have:

- A tenant or customer ID.
- A product name.
- A language.
- A publication status.
- A document category.
- A creation or update date.
- An access classification.

This information is metadata. In Qdrant, metadata stored alongside a point is commonly called its payload.

Filtering lets me combine semantic similarity with structured conditions. For example: find articles similar to the question, but only from the current customer's tenant, only in English, and only when published.

## 1. A simple business example

Suppose a company hosts a support platform for multiple customers. Customer A and Customer B both upload documents about invoices.

A search for “How do I download an invoice?” may find semantically similar documents from both customers. Returning another customer's document would be a serious data leak.

The search must include a tenant filter derived from the authenticated server-side identity. The application must never trust a tenant ID supplied only by the browser.

## 2. Store useful metadata

A point payload might look like this:

~~~json
{
  "tenant_id": "tenant-a",
  "document_id": "billing-guide-001",
  "title": "Download an invoice",
  "category": "billing",
  "language": "en",
  "status": "published",
  "source": "knowledge-base"
}
~~~

I should design metadata deliberately. Store fields that the application needs for filtering, display, auditing, or lifecycle management. Avoid copying large document bodies into every payload when a separate document store is more appropriate.

## 3. Create payload indexes

A payload index helps the database execute filters efficiently. The best fields to index depend on real query patterns and the selected database.

Qdrant example:

~~~python
from qdrant_client import QdrantClient, models

client = QdrantClient(path="./qdrant_data")

client.create_payload_index(
    collection_name="support_articles",
    field_name="tenant_id",
    field_schema=models.PayloadSchemaType.KEYWORD,
)

client.create_payload_index(
    collection_name="support_articles",
    field_name="status",
    field_schema=models.PayloadSchemaType.KEYWORD,
)
~~~

An index consumes resources, so I should not create indexes for every field without understanding how the application searches.

## 4. Filter a vector search

~~~python
from qdrant_client import models

tenant_filter = models.Filter(
    must=[
        models.FieldCondition(
            key="tenant_id",
            match=models.MatchValue(value="tenant-a"),
        ),
        models.FieldCondition(
            key="status",
            match=models.MatchValue(value="published"),
        ),
        models.FieldCondition(
            key="language",
            match=models.MatchValue(value="en"),
        ),
    ]
)

response = client.query_points(
    collection_name="support_articles",
    query=[0.12, 0.85, 0.20],
    query_filter=tenant_filter,
    limit=5,
    with_payload=True,
)
~~~

The must conditions mean that all listed conditions must match. Other filter structures can express alternatives or exclusions. I should learn the filter model supported by the selected database before translating complicated business rules.

## 5. Authorization is more than a filter

Filtering is an important part of data isolation, but it is not a complete security system.

A safe request path should look like this:

1. Authenticate the caller.
2. Resolve the caller's tenant and permissions on the server.
3. Build the database filter from trusted authorization context.
4. Execute the search.
5. Apply any additional document-level permission checks required by the application.
6. Return only permitted fields.

Do not accept a tenant filter directly from the client and assume that it is safe. A malicious user could change the tenant value in a request.

For high-sensitivity systems, use defense in depth: separate collections or databases where appropriate, strict service credentials, network controls, audit logs, and tests that attempt cross-tenant access.

## 6. Metadata design decisions

| Field | Example | Why it may be useful |
|---|---|---|
| tenant_id | tenant-a | Tenant isolation |
| document_id | guide-001 | Link to source record |
| status | published | Exclude drafts or deleted data |
| language | en | Language-aware retrieval |
| category | billing | Narrow the subject area |
| updated_at | ISO timestamp | Freshness or lifecycle filtering |
| access_group | finance | Permission-aware retrieval |

Use stable field names and consistent data types. A date should not sometimes be stored as a string and sometimes as a number unless the database explicitly supports the intended behavior.

## 7. Avoid over-filtering

A filter that is too restrictive can return no useful results. For example, a language filter may exclude a relevant document if language metadata is missing or incorrect.

I should distinguish between:

- Mandatory security filters, which must never be relaxed.
- User-selected filters, which may be optional.
- Ranking preferences, which can influence ordering but should not accidentally remove all relevant records.

Never remove a mandatory tenant or authorization filter just to make search return more results.

## 8. Production checks

- Confirm that tenant identity comes from authenticated server context.
- Add automated tests that try to retrieve another tenant's records.
- Monitor filter selectivity and search latency.
- Index fields used frequently in filters.
- Define how missing or malformed metadata is handled.
- Ensure update and delete workflows maintain metadata correctly.
- Review logs so sensitive payload data is not exposed unnecessarily.

## Practice exercise

1. Add tenant_id, status, language, and category to sample points.
2. Create payload indexes for fields used frequently in filters.
3. Search with a tenant filter and a status filter.
4. Try to search for a different tenant by changing a client-supplied value.
5. Design an API that derives tenant_id from the authenticated user instead.
6. Write a test that proves Tenant A cannot retrieve Tenant B's document.

## Self-check

- What is a payload?
- Why use a payload index?
- Why must tenant identity be derived on the server?
- What is the difference between a security filter and an optional user filter?
- Why should I avoid removing mandatory filters when no results are found?
- Why is filtering only one part of access control?

## Key takeaway

Metadata makes vector retrieval useful in real applications. Secure filtering must be based on trusted identity and tested as an authorization boundary, not treated as a convenience feature.
