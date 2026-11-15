# 06 — MCP Client, Discovery, and Host Integration

## The client role

The MCP client lives inside a host application. It connects to a server, negotiates the protocol session, discovers capabilities, and sends requests. The host is the user-facing application; the client is the protocol component that communicates with servers.

## 1. Client responsibilities

A well-designed client should:
- connect using a supported transport,
- negotiate protocol version and capabilities,
- discover tools, resources, and prompts,
- present tool descriptions and schemas to the host/model,
- forward approved requests,
- correlate responses with requests,
- handle timeouts, errors, and disconnects,
- close sessions cleanly.

The client should not assume that a server is trustworthy merely because it connected successfully.

## 2. Discovery workflow

For tools, the conceptual flow is:
1. List available tools.
2. Inspect each tool name, description, and input schema.
3. Make the relevant tool definitions available to the host.
4. When a tool is selected, send the call through the MCP client.
5. Return bounded results to the host.

Resources and prompts have their own discovery/read/get flows in the protocol. Check the current specification for exact request methods and optional capabilities.

## 3. Host versus model

A model may recommend a tool call, but the host mediates whether that call is allowed. The host can apply user consent, configured server allowlists, UI confirmation, and policy. The server independently enforces its own authorisation.

This creates a defence-in-depth model:
- host: user experience and client-side policy,
- client: protocol handling,
- server: request validation and capability boundary,
- backend: data and business permission enforcement.

## 4. Build an integration checklist

Before registering a server in a host:
- confirm the server executable and working directory,
- pin the runtime and dependencies,
- identify environment variables and required secrets,
- check the transport the host supports,
- limit which servers are enabled,
- understand which tools can change state,
- review the server's source and dependencies,
- test disconnect and error behaviour.

Never paste an untrusted server configuration into a work machine without reviewing what process will start and what files or credentials it can access.

## 5. Common connection problems

| Symptom | Possible cause | First check |
|---|---|---|
| Server never starts | Wrong executable or working directory | Process configuration and stderr |
| Connection closes immediately | Startup exception or protocol mismatch | Server logs and versions |
| No tools appear | Capability not registered or wrong server | Tool list and server registration |
| Tool appears but fails | Invalid args or backend error | Request schema and server logs |
| Works in Inspector only | Host configuration/runtime differs | Compare exact launch environment |
| Intermittent timeout | Slow dependency or short deadline | Timing and dependency health |

## Practice exercise

Connect the local server from Lesson 05 to a compatible MCP Inspector or host. Record server process command, transport, discovered tool names, resource URI, sample request, sample result, and one intentionally triggered error. Do not connect private systems.

## Self-check

1. What does an MCP client do?
2. Why should the host mediate model-proposed tool calls?
3. Why does server-side authorisation remain necessary?
4. What configuration should be reviewed before launching a local server?
5. How would you investigate a server that starts but advertises no tools?

## Further reading

- [MCP architecture](https://modelcontextprotocol.io/docs/learn/architecture)
- [Build an MCP client](https://modelcontextprotocol.io/docs/develop/build-client)
