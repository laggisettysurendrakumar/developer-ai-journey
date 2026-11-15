# 01 — MCP Fundamentals and the Problem It Solves

## What this lesson is about

Model Context Protocol (MCP) is a standard way for an AI application to connect to external capabilities such as tools, data sources, and reusable prompt templates. I start with the problem MCP solves before learning an SDK. This keeps the protocol separate from any one model provider or framework.

## 1. The problem before MCP

Imagine I build an AI assistant that needs to search engineering documentation, query a ticketing system, and inspect a source repository. Without a shared integration approach, each host application may need a different custom adapter for every service. Those adapters often duplicate connection handling, capability descriptions, input schemas, and error handling.

MCP provides a common protocol boundary. A compatible host can connect to an MCP server and discover the capabilities it exposes. The host still needs to decide what the user is allowed to do, and the server must enforce its own security rules.

**Important:** MCP is not an LLM, an agent framework, a vector database, or an authorisation system. It does not automatically make an exposed tool safe.

## 2. Learn the vocabulary

| Term | Plain-English meaning | Example |
|---|---|---|
| Host | The application the user interacts with | An AI desktop app |
| Client | Protocol implementation inside the host | An MCP client connection |
| Server | Program exposing capabilities | Documentation MCP server |
| Tool | An operation the client can ask the server to run | Search approved docs |
| Resource | Addressable content the client can read | A runbook |
| Prompt | A reusable prompt template | Incident-summary template |
| Transport | How protocol messages travel | stdio or Streamable HTTP |

A host may connect to multiple servers. A server may expose multiple tools and resources. The server is responsible for validating each incoming request and checking the caller's permissions.

## 3. Where MCP fits in an AI application

A simple mental model:

1. The user asks a question in the host application.
2. The host's model decides whether an available capability may help.
3. The MCP client sends a protocol request to the server.
4. The server checks the request, authenticates/authorises as appropriate, and calls trusted application code.
5. The server returns content or a structured error.
6. The host decides how to present the result.

The model's decision is not permission. The model can suggest calling a tool, but trusted code must decide whether that operation is allowed.

## 4. MCP versus ordinary APIs and model tool calling

| Technology | Main responsibility |
|---|---|
| REST/HTTP API | Application-specific request/response contract |
| Model tool/function calling | Lets a model request a named function with structured arguments |
| MCP | Standardises discovery and interaction with tools, resources, and prompts |
| Agent framework | Helps orchestrate model calls, tools, state, and workflows |

MCP servers often call ordinary APIs internally. MCP does not eliminate the need to design those APIs carefully.

## 5. First design exercise

Before writing code, design a safe Documentation Server.

- Tool: `search_docs(query)`
- Resource: `knowledge://runbooks/linux-service`
- Prompt: `summarise_incident`
- Data: synthetic documentation titles only
- Permission: the user may see only documents they are allowed to read
- Explicit non-goal: no shell access and no production write operations

Draw the flow from user to host, client, server, and backend search function.

## 6. Common beginner misunderstandings

- **“MCP is the AI model.”** No. It is an integration protocol.
- **“A discovered tool is safe to call.”** No. Discovery describes capability; it does not grant authority.
- **“The model can enforce access control.”** No. Enforce access in trusted server/backend code.
- **“MCP replaces REST.”** No. An MCP server can adapt existing APIs.
- **“Every capability must be a tool.”** No. Resources and prompts have distinct purposes.

## Practice exercise

Write a one-page architecture note for a read-only knowledge server. Identify the host, client, server, tools, resources, transport, backend, identity source, and permission check. Explain what happens when the user asks for a document they cannot access.

## Self-check

1. What problem does MCP standardise?
2. How are host, client, and server different?
3. Why is MCP not an authorisation system?
4. When might a REST API still exist behind an MCP server?
5. What is the difference between a tool and a resource?

## Further reading

- [MCP architecture](https://modelcontextprotocol.io/docs/learn/architecture)
- [MCP specification](https://modelcontextprotocol.io/specification/)
