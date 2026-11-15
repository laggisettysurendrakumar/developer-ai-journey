# 09 — Errors, Timeouts, Retries, and Resilience

## Why reliability needs deliberate design

A tool call can fail because the client process stopped, the network disconnected, input validation failed, the user lacks permission, or a backend is unavailable. A reliable MCP server makes failures bounded, understandable, and safe.

## 1. Set timeouts at every boundary

A request may wait on:
- protocol connection,
- server queue,
- database,
- HTTP API,
- model/provider dependency,
- downstream service.

Set a total operation deadline and shorter dependency timeouts. Do not allow a tool call to wait indefinitely. Leave enough time for cleanup and a useful error response.

## 2. Retry only when safe

Retries can increase load and duplicate side effects.

| Operation | Retry approach |
|---|---|
| Read-only lookup | May retry transient failures within a deadline |
| Idempotent update | Retry only with a well-defined idempotency strategy |
| Send email or create payment | Do not blindly retry; verify outcome and deduplicate |
| Validation failure | Do not retry unchanged input |
| Permission denied | Do not retry as another identity |

Use exponential backoff with jitter for retryable transient failures. Bound attempts and total elapsed time.

## 3. Idempotency

An operation is idempotent when repeating it with the same intent does not create additional effects. For write operations, a server may accept an idempotency key tied to the authenticated principal and operation. Store the outcome safely and reject conflicting reuse.

Do not claim an action failed just because the client timed out. The server may have completed the operation while the response was lost. Query the operation state or use deduplication before attempting again.

## 4. Concurrency and backpressure

Limit concurrent requests and queue depth. Define behaviour when the server is overloaded: reject quickly, shed non-critical work, or return a bounded retryable error. Protect databases and downstream APIs from a burst of tool calls.

## 5. Error design

Useful errors identify the category and safe next step without revealing internal details.

Good:
- “The requested service is not available in this environment.”
- “The caller is not authorised to read this document.”
- “The dependency did not respond before the request deadline.”

Avoid stack traces, SQL text, tokens, private hostnames, and data from other tenants.

## 6. Observability fields

Useful fields include:
- trace/request ID,
- server and tool name,
- duration,
- outcome category,
- dependency name,
- retry count,
- redacted principal or tenant identifier where policy allows.

Never log raw credentials or entire sensitive resource content by default.

## Practice exercise

For a hypothetical `create_change_request` tool, define a 5-second backend timeout, total deadline, idempotency key, retryable errors, non-retryable errors, and a recovery query for an ambiguous timeout. Explain how you would prevent duplicate change requests.

## Self-check

1. Why is a timeout necessary?
2. Why can a retry create duplicate side effects?
3. What is an idempotency key?
4. What should happen after a permission failure?
5. Which telemetry fields are useful without exposing secrets?

## Further reading

- [MCP specification](https://modelcontextprotocol.io/specification/)
- [OWASP API Security](https://owasp.org/www-project-api-security/)
