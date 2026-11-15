# 13 — Advanced Patterns, Composition, and Enterprise Governance

## Moving beyond a single-server demo

When several teams publish MCP servers, the challenge is no longer just writing a tool. It becomes capability ownership, identity propagation, governance, version management, policy, and operational consistency.

## 1. Server boundaries

Split servers around clear trust and ownership boundaries rather than creating one universal server.

Examples:
- engineering documentation,
- source repository read access,
- deployment status,
- ticket search.

Each server should expose a small capability set and own its permission checks.

## 2. Composing capabilities

A host may connect to multiple MCP servers and combine their results. A workflow might search a runbook, inspect deployment status, and retrieve an incident summary.

Composition creates risks:
- one server's output may influence another tool call,
- errors or slow services can propagate,
- identities may be lost or confused,
- the host may send more context than needed.

Keep each call bounded, preserve source attribution, and never let one server's output grant access to another server.

## 3. Identity propagation

Define whether the server acts:
- as itself with narrowly scoped service permissions,
- on behalf of the authenticated user,
- through a delegated token or approved enterprise identity pattern.

Document the trust assumptions. Do not forward arbitrary tokens to downstream services. Validate audience and scopes at each relevant boundary.

## 4. Versioning and compatibility

Track:
- MCP protocol version,
- server implementation version,
- SDK version,
- tool schema version,
- backend API version.

Changing a tool's argument name or meaning can break clients even if the server still starts. Prefer additive changes, publish migration notes, and test representative clients.

## 5. Enterprise governance

A central registry or catalogue can record:
- owner and support team,
- server purpose and data classification,
- approved hosts and transports,
- tool risk classification,
- authentication model,
- permission model,
- dependency and vulnerability status,
- operational SLO and incident contact,
- lifecycle and deprecation date.

Governance should make ownership clear without becoming a reason to expose every capability globally.

## 6. Risk classification

A useful internal classification:
- **Read-only low risk:** public or approved metadata search.
- **Sensitive read:** customer, employee, or restricted engineering data.
- **Reversible write:** update a draft or create a non-production record.
- **High-impact write:** deploy, delete, grant access, send external communication.

Higher classes require stronger approvals, logging, testing, and rollback. The exact controls must follow company policy and regulatory requirements.

## 7. Cost and context control

Multiple servers can return duplicate or large results. Bound the number of calls, result count, snippet size, and total context. Cache only where freshness and access isolation are understood. Never share cached private data across users or tenants.

## Practice exercise

Design an enterprise MCP catalogue with three servers. For each, specify owner, data classification, transport, authentication, tools, risk level, SLO, support contact, and deprecation policy. Draw one composed workflow and mark all trust boundaries.

## Self-check

1. Why split servers around ownership and trust boundaries?
2. What can go wrong when a host composes multiple servers?
3. How should identity be propagated?
4. Which version numbers should be tracked?
5. What information belongs in an enterprise server catalogue?

## Further reading

- [MCP architecture](https://modelcontextprotocol.io/docs/learn/architecture)
- [MCP specification](https://modelcontextprotocol.io/specification/)
