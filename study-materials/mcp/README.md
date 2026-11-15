# Model Context Protocol (MCP) — Beginner to Expert Learning Path

I use this course to understand MCP from its basic purpose through server and client development, secure enterprise integration, and production operations. The goal is not to memorise an SDK. The goal is to understand the protocol, design safe capabilities, and build integrations that can be operated and maintained.

## Who this course is for

Developers who know basic programming, JSON, APIs, and Git. Python is used for the first hands-on server because it keeps the protocol concepts visible. A dedicated lesson also maps the same architecture to .NET and ASP.NET Core.

## What I should be able to do by the end

- Explain the host, client, server, lifecycle, capabilities, and transport.
- Choose correctly between tools, resources, and prompts.
- Build and inspect a local read-only MCP server.
- Connect a compatible client/host and diagnose discovery and invocation.
- Understand stdio and Streamable HTTP, including legacy SSE compatibility.
- Apply authentication, authorisation, tenant isolation, and prompt-injection defences.
- Design bounded timeouts, retries, idempotency, testing, and telemetry.
- Integrate MCP into a .NET application architecture.
- Plan deployment, governance, and production support.
- Complete a Developer Knowledge Assistant capstone.

## Learning path

| Lesson | Topic | Outcome |
|---:|---|---|
| 01 | [MCP fundamentals and the problem it solves](01-mcp-fundamentals-and-the-problem-it-solves.md) | Understand what MCP is and where it fits |
| 02 | [Protocol lifecycle, messages, and capabilities](02-protocol-lifecycle-messages-and-capabilities.md) | Understand initialise, discovery, requests, responses, and notifications |
| 03 | [Tools, input schemas, and safe execution](03-tools-input-schemas-and-safe-execution.md) | Design narrow, validated operations |
| 04 | [Resources, prompts, and context design](04-resources-prompts-and-context-design.md) | Expose useful context without over-sharing |
| 05 | [Build a local read-only MCP server with Python](05-python-sdk-build-a-local-read-only-server.md) | Implement the first server |
| 06 | [MCP client, discovery, and host integration](06-mcp-client-discovery-and-host-integration.md) | Connect a host and inspect capabilities |
| 07 | [Transports: stdio and Streamable HTTP](07-transports-stdio-and-streamable-http.md) | Choose and troubleshoot transports |
| 08 | [Authentication, authorisation, and enterprise security](08-authentication-authorisation-and-enterprise-security.md) | Protect users, data, and operations |
| 09 | [Errors, timeouts, retries, and resilience](09-errors-timeouts-retries-and-resilience.md) | Design predictable failure behaviour |
| 10 | [Testing MCP servers: Inspector, contracts, and security](10-testing-inspector-contracts-and-security.md) | Validate behaviour and security |
| 11 | [.NET MCP integration and ASP.NET Core patterns](11-dotnet-mcp-integration-and-aspnet-core-patterns.md) | Apply MCP concepts to a .NET service |
| 12 | [Production deployment, observability, and operations](12-production-deployment-observability-and-operations.md) | Operate a remote server |
| 13 | [Advanced patterns, composition, and enterprise governance](13-advanced-patterns-composition-and-enterprise-governance.md) | Design multiple-server enterprise environments |
| 14 | [Capstone: Developer Knowledge Assistant MCP Server](14-capstone-developer-knowledge-assistant-mcp-server.md) | Build and review an end-to-end implementation |

## Suggested pace

A first pass can take 3–4 weeks at 1–2 focused hours per day. Spend more time on the server, security, test, and capstone lessons. Reading alone is not enough: run the local server, connect an Inspector or host, and test failure cases.

## Recommended environment

- Python 3.11+ for the current official Python SDK v2 line (confirm the latest SDK requirement).
- A virtual environment and pinned dependency versions.
- Node.js and `npx` if required by the Inspector workflow.
- Git for versioning.
- An MCP-compatible host or the official Inspector.
- .NET 8+ for the architecture exercise in Lesson 11 (verify the current C# SDK requirements separately).

Use synthetic data until the full security design is reviewed. Never place company credentials, private documents, customer records, or production logs in this learning repository.

## How to study each lesson

1. Read the plain-English explanation first.
2. Draw the architecture or message flow in your own words.
3. Type and run the examples using the current official SDK docs.
4. Complete the exercise and self-check.
5. Record the exact protocol and SDK versions you used.
6. Add tests before adding more tools or connecting private data.

## Important implementation note

MCP and its SDKs evolve. Code samples are teaching patterns, not a substitute for checking the current protocol specification and SDK quickstart. In particular, verify package names, imports, server startup, transport APIs, authentication, and Inspector commands for the versions installed. Do not combine examples from different SDK major versions.

## Safety principles

- Treat model-proposed tool calls as requests, not permission.
- Enforce identity and object-level permissions in trusted server/backend code.
- Treat resource contents, tool results, and tool descriptions as untrusted input.
- Start read-only and expose narrow allowlisted capabilities.
- Require approval for consequential or irreversible actions.
- Set timeouts, request/response limits, concurrency limits, and audit events.
- Keep credentials out of prompts, logs, source files, and resource URIs.
- Test denial cases, prompt injection, dependency failures, and ambiguous write outcomes.

## How this course connects to the rest of the repository

- [GenAI](../genai/README.md) — understand model APIs and structured outputs.
- [Prompt engineering](../prompt-engineering/README.md) — design reliable instructions and understand prompt injection.
- [RAG](../rag/README.md) — retrieve permission-filtered knowledge.
- [Agentic AI](../agentic-ai/README.md) — understand tool-using agents and orchestration.
- [LLM evaluation](../llm-evaluation/README.md) — evaluate quality, safety, and tool behaviour.
- [Docker](../docker/README.md) — package the server.
- [Kubernetes](../kubernetes/README.md) — deploy and operate when justified.
- [Azure](../azure/README.md) — apply cloud identity, hosting, monitoring, and governance.

## Official references

- [MCP documentation](https://modelcontextprotocol.io/docs/)
- [MCP specification](https://modelcontextprotocol.io/specification/)
- [MCP architecture](https://modelcontextprotocol.io/docs/learn/architecture)
- [MCP security best practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices)
- [Official Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [Official Python SDK documentation](https://py.sdk.modelcontextprotocol.io/)
- [Official C# SDK](https://github.com/modelcontextprotocol/csharp-sdk)
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector)

This is a learning track. Production use still requires environment-specific security review, version validation, testing, data governance approval, and named operational ownership.
