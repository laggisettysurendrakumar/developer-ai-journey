# 02 — Protocol Lifecycle, Messages, and Capabilities

## What I need to understand

MCP is more than sending JSON to a function. It defines a lifecycle, capability negotiation, request/response patterns, and notifications. Understanding that lifecycle helps me diagnose failures that otherwise look like “the tool did not work.”

## 1. Connection lifecycle in plain English

A typical client/server session follows this broad sequence:

1. **Initialise:** client and server exchange protocol version and capability information.
2. **Initialised notification:** client indicates that initialisation is complete.
3. **Operate:** client lists capabilities and sends requests.
4. **Notify:** either side may send notifications where the protocol defines them.
5. **Shutdown:** the connection is closed cleanly or terminated after a failure.

The exact method names and required fields belong to the protocol version. I check the current specification rather than copying message examples from an old blog post.

## 2. Request, response, and notification

- A **request** asks the other side to do something and expects a response.
- A **response** contains a result or an error for a request.
- A **notification** communicates an event and does not expect a response.

This is useful when reading protocol logs: a notification should not be diagnosed as a request that is missing its response.

MCP uses JSON-RPC 2.0 message conventions. JSON-RPC request IDs correlate requests with their responses. Do not invent IDs or hand-roll protocol messages in production when a maintained SDK can handle the details.

## 3. Capability negotiation

During initialisation, each side communicates the protocol capabilities it supports. A client should not assume a server supports every optional feature. A server should advertise only capabilities it actually implements.

Examples of capability areas include tools, resources, prompts, and optional notifications or other protocol extensions. The exact list can evolve with the specification.

## 4. Discovery versus invocation

Discovery answers “what can this server expose?” Invocation answers “please perform this operation with these arguments.”

For a tool, the conceptual sequence is:

1. Client requests the available tools.
2. Server returns tool definitions, including names, descriptions, and input schemas.
3. Host/model selects a tool when appropriate.
4. Client sends the tool call with arguments.
5. Server validates and executes.
6. Client receives the result or error.

The host should present tool descriptions accurately. The server must never treat the model-provided arguments as trusted.

## 5. Read structured data carefully

JSON values have types: strings, numbers, booleans, arrays, objects, and null. A schema is a contract, not proof that data is true or authorised.

When a server receives a request, it should validate:
- required fields,
- types and allowed values,
- maximum string and collection sizes,
- cross-field business rules,
- identity and resource permissions,
- operation-specific approval requirements.

## 6. Error categories

Keep protocol failures distinct from application failures.

| Failure | Example | Handling idea |
|---|---|---|
| Connection | Process exited | Reconnect within a bounded policy |
| Protocol | Unsupported version or malformed message | Fix compatibility/implementation |
| Validation | Missing required field | Return useful validation error |
| Authentication | No valid identity | Reject and authenticate |
| Authorisation | User cannot access document | Deny without leaking data |
| Dependency | Search backend unavailable | Timeout and return safe error |
| Business rule | Ticket is already closed | Explain conflict; do not retry blindly |

Avoid returning stack traces, credentials, internal hostnames, or private data in error messages.

## Practice exercise

Draw the initialise → discover → invoke → response → shutdown lifecycle. For each stage, write one possible failure and the evidence you would inspect. Then define three validation rules for a document-search tool.

## Self-check

1. What is capability negotiation for?
2. How does a request differ from a notification?
3. Why should a client check advertised capabilities?
4. Why are valid JSON arguments not necessarily authorised?
5. How do protocol errors differ from backend errors?

## Further reading

- [MCP specification](https://modelcontextprotocol.io/specification/)
- [MCP lifecycle](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle)
