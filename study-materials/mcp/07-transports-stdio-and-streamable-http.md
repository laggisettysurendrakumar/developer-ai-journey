# 07 — Transports: stdio and Streamable HTTP

## What a transport means

The transport is the communication path between MCP client and server. The protocol defines the messages; the transport carries them. A server's capabilities do not become different just because the transport changes.

## 1. stdio for local processes

With stdio, the host starts a local server process and exchanges protocol messages through standard input and standard output.

Useful for:
- local developer tools,
- desktop applications that launch a child process,
- trusted per-user utilities,
- development and testing.

Important operational rules:
- do not write ordinary log lines to stdout,
- log diagnostics to stderr or a file,
- use a controlled executable and working directory,
- restrict process environment and filesystem access,
- handle termination signals and child-process cleanup.

A stdio process can often access the same operating-system permissions as the user who launched it. That makes least-privilege execution important.

## 2. Streamable HTTP for remote servers

Streamable HTTP is the current standard HTTP transport for remote MCP communication. It supports HTTP-based request/response and streaming behaviour defined by the specification.

Useful for:
- centrally hosted MCP services,
- controlled enterprise integrations,
- remote clients,
- deployments that need central monitoring and scaling.

A production deployment should consider:
- TLS,
- authentication and token validation,
- origin validation and DNS rebinding protections,
- per-user and per-tenant authorisation,
- request size and rate limits,
- timeouts and concurrency,
- reverse proxy and load balancer behaviour,
- session and reconnect behaviour as defined by the SDK/specification.

## 3. SSE and legacy compatibility

Older MCP integrations may use the HTTP+SSE transport. It remains relevant when maintaining systems that depend on it. For new implementations, prefer the current Streamable HTTP transport unless a host or compatibility requirement says otherwise.

Do not mix endpoint, header, session, and message assumptions from older transport examples with the current transport.

## 4. Choosing a transport

| Question | stdio | Streamable HTTP |
|---|---|---|
| Server location | Local process | Remote service |
| Typical use | Desktop/local integration | Shared service |
| Network exposure | Not inherently required | Yes |
| Main operational concern | Process and OS permissions | Identity, network, scale |
| Deployment | Host launches process | Operated service infrastructure |

## 5. Transport troubleshooting

For stdio, inspect process startup, command path, working directory, environment, stderr, and whether stdout contains non-protocol text.

For HTTP, inspect DNS, TLS, proxy routes, authentication, origin, status code, response headers, session handling, and server health. Redact access tokens and sensitive payloads before sharing logs.

## Practice exercise

Write two deployment diagrams for the same read-only search server: one using stdio and one using Streamable HTTP. Compare identity propagation, network boundaries, monitoring, upgrades, and failure modes. Explain why changing transport does not remove backend authorisation.

## Self-check

1. What does a transport do?
2. Why must stdio stdout remain free of ordinary log messages?
3. When is Streamable HTTP appropriate?
4. Why should new examples not casually copy legacy SSE instructions?
5. What security concerns become important for a remote server?

## Further reading

- [MCP transports](https://modelcontextprotocol.io/specification/)
- [MCP security best practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices)
