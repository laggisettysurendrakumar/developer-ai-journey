# 03 — Tools, Input Schemas, and Safe Execution

## Why tools deserve special attention

An MCP tool is an operation exposed by a server. A tool may only read data, or it may change something important. I design a tool as a public API endpoint: clear name, narrow purpose, validated input, permission checks, controlled output, and reliable errors.

## 1. Choose a narrow tool boundary

Good tool names communicate one action:
- `search_approved_docs`
- `get_ticket_summary`
- `list_deployment_statuses`

Avoid vague or excessively powerful tools:
- `do_anything`
- `run_command`
- `execute_sql`

A broad tool makes it difficult to describe its behaviour, reason about permissions, limit impact, and audit use.

## 2. Define a schema before implementation

Example input contract:

```json
{
  "type": "object",
  "properties": {
    "query": {
      "type": "string",
      "minLength": 2,
      "maxLength": 200
    },
    "limit": {
      "type": "integer",
      "minimum": 1,
      "maximum": 10
    }
  },
  "required": ["query"],
  "additionalProperties": false
}
```

This schema communicates structure to clients. The server must still validate input at runtime and apply business rules. A malicious or buggy client may not follow the advertised schema.

## 3. Business logic first

I keep the business operation independent of MCP. That makes it testable and reusable.

```python
from pydantic import BaseModel, ConfigDict, Field

class SearchDocsInput(BaseModel):
    model_config = ConfigDict(extra="forbid")
    query: str = Field(min_length=2, max_length=200)
    limit: int = Field(default=5, ge=1, le=10)

def search_approved_docs(args: SearchDocsInput, *, user_id: str) -> list[dict]:
    # Example only: replace with a real permission-filtered repository.
    sample_docs = [
        {"id": "doc-01", "title": "Linux service runbook", "allowed_users": {"user-1"}},
        {"id": "doc-02", "title": "Python deployment guide", "allowed_users": {"user-1", "user-2"}},
    ]

    matches = [
        {"id": item["id"], "title": item["title"]}
        for item in sample_docs
        if args.query.casefold() in item["title"].casefold()
        and user_id in item["allowed_users"]
    ]
    return matches[:args.limit]

request = SearchDocsInput(query="Linux", limit=5)
print(search_approved_docs(request, user_id="user-1"))
```

**What this demonstrates:** schema validation, rejection of unexpected fields, a bounded result limit, and permission filtering before returning documents.

**What this does not demonstrate:** real identity verification, database isolation, or production-grade document security. The sample user ID is supplied by the caller for illustration only. In a real server, derive identity from a trusted authenticated session.

## 4. Validate at multiple layers

1. Protocol/schema validation catches malformed shape.
2. Application validation checks meaning and business rules.
3. Authorisation checks whether this principal can perform this operation on this object.
4. Backend query filters data before it is loaded or returned.
5. Output controls remove unnecessary sensitive fields.

Do not rely on a model prompt to validate or authorise a request.

## 5. Design useful results

Return the minimum information the host needs. For a search tool, that may be document IDs, titles, short snippets, and source references—not entire database rows or secret metadata.

Set limits on:
- number of results,
- snippet length,
- total response size,
- backend execution time,
- pagination size.

## 6. Side effects and approval

A read-only search has different risks from closing a ticket or deploying an application. For consequential operations, use explicit policy checks and human approval where required. Make the operation idempotent when possible and record an audit event.

## Practice exercise

Design a `get_service_health(service_name, environment)` tool. Write its input schema, allowed environment list, permission rule, response fields, timeout, and three error outcomes. Then create unit tests for empty service name, unknown environment, and unauthorised service.

## Self-check

1. Why is a narrow tool safer than a universal command tool?
2. Why validate on the server even when a schema exists?
3. What is the difference between validation and authorisation?
4. Why should outputs be size-limited?
5. When should a tool require human approval?

## Further reading

- [MCP tools concepts](https://modelcontextprotocol.io/docs/learn/server-concepts)
- [MCP specification](https://modelcontextprotocol.io/specification/)
