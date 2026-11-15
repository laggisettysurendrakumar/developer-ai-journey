# 14 — Capstone: Developer Knowledge Assistant MCP Server

## Capstone outcome

I will design a Developer Knowledge Assistant that exposes a carefully limited set of MCP capabilities. It will start with synthetic documentation and a local server. The architecture can later be adapted to approved company documentation, but only after identity, permissions, privacy, and security reviews.

## 1. Functional requirements

The first release must:
- search approved documentation,
- return bounded snippets with source IDs,
- expose a read-only runbook resource,
- provide an incident-summary prompt,
- reject malformed and oversized requests,
- enforce user/document permissions,
- return safe errors,
- emit redacted audit and performance telemetry.

Explicit non-goals:
- arbitrary shell execution,
- arbitrary SQL,
- production deployments,
- secret retrieval,
- ticket updates or other write actions.

## 2. Architecture

```text
User
 |
AI Host
 |
MCP Client
 |
Local stdio Server (first milestone)
 |
MCP Tool / Resource / Prompt adapters
 |
Application services
 |
Permission-filtered documentation repository
```

Later, a reviewed remote version may use Streamable HTTP behind an authenticated HTTPS gateway. The backend permission checks remain mandatory.

## 3. Capability design

| Name | Type | Purpose | Risk |
|---|---|---|---|
| `search_docs` | Tool | Search allowed documents | Read-only |
| `get_doc_summary` | Tool | Fetch a bounded summary by ID | Read-only |
| `knowledge://runbooks/{name}` | Resource/template | Read a runbook the user may access | Read-only |
| `incident_summary` | Prompt | Structure a summary from supplied evidence | No direct side effect |

The server must verify permission for every returned document. Tool arguments cannot supply trusted identity.

## 4. Implementation phases

**Phase A — pure business logic**
- Create a synthetic document collection.
- Implement query validation and permission filtering.
- Add deterministic unit tests.
- Keep IDs, titles, snippets, and permissions explicit.

**Phase B — MCP adapter**
- Expose the search and summary operations as tools.
- Add the runbook resource.
- Add the reusable prompt.
- Verify discovery and invocation with the Inspector.

**Phase C — security and resilience**
- Add a trusted identity source for the intended host.
- Test cross-user and cross-tenant denial.
- Bound query length, result count, output bytes, and latency.
- Add cancellation, safe errors, audit, and prompt-injection test cases.

**Phase D — operations**
- Package dependencies with pinned versions.
- Add CI unit, contract, integration, and security tests.
- Document host configuration and support procedure.
- Add telemetry and an incident runbook.

## 5. Acceptance criteria

The capstone is complete only when:
- the intended host can initialise and discover the server,
- tools, resource, and prompt appear as designed,
- a permitted user can retrieve approved synthetic content,
- an unauthorised user receives no restricted content,
- malformed and oversized input is rejected,
- resource prompt-injection text is treated as data,
- dependency timeout produces a bounded safe error,
- logs contain correlation details but no credentials,
- tests run in CI,
- protocol and SDK versions are documented.

## 6. Final architecture review

Answer these questions before calling the project production-ready:
1. Who owns this server and its data?
2. Which identities can connect?
3. How is identity propagated to the backend?
4. Where are object-level and tenant-level permissions enforced?
5. What data is returned to the model and retained in logs?
6. What is the failure and rollback plan?
7. How will protocol, SDK, and schema changes be tested?
8. Who responds to an incident?

## Practice exercise

Complete the capstone in a Git branch. Include a README with setup, version pins, host configuration, capability inventory, security assumptions, test commands, and known limitations. Demonstrate one allowed search, one denied access case, one invalid input, and one dependency failure using synthetic fixtures.

## Self-check

1. What are the first three capabilities in the capstone?
2. Why is the first milestone local and read-only?
3. Where must access checks be enforced?
4. Which acceptance criteria prove safe failure behaviour?
5. What evidence would you present in a design review?

## Definition of done

The project is a learning implementation, not a production certification. It becomes a candidate for production only after security review, operational ownership, host compatibility testing, data governance approval, and deployment validation.

## Further reading

- [MCP documentation](https://modelcontextprotocol.io/docs/)
- [MCP specification](https://modelcontextprotocol.io/specification/)
- [Official Python SDK](https://github.com/modelcontextprotocol/python-sdk)
