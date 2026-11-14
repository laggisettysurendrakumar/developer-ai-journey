# Model Context Protocol (MCP) — Beginner to Practical Integration

I use this guide to understand how AI applications can discover and call tools and read resources through a shared protocol. MCP is not an LLM and it does not make a tool safe automatically. It standardises how a host and server exchange capability information and requests.

## Who should use this guide

Developers who understand Python functions, JSON, APIs, tool calling, and the basic agent loop. Learn agent tool design before adding MCP.

## Learning order

1. Understand the problem MCP solves and its host/client/server architecture.
2. Learn tools, resources, and prompts.
3. Understand capability discovery and structured requests/responses.
4. Learn transports: stdio and Streamable HTTP. Understand SSE when maintaining an existing integration.
5. Build a local read-only server.
6. Connect a client/host and inspect discovery and results.
7. Secure remote access and authorise every operation.
8. Deploy and test failure, timeout, and malicious-input cases.

## 1. MCP in plain English

Imagine every AI client needed a different custom integration for every database, repository, ticket system, or file store. MCP defines a common protocol so compatible hosts can discover and interact with servers that expose capabilities.

MCP separates two concerns:
- The host/client asks for context or requests an operation.
- The MCP server exposes the capability and performs its own validation and access checks.

MCP does not decide which LLM should be used and does not replace the model API.

## 2. Host, client, and server

| Part | Plain-English meaning | Example |
|---|---|---|
| Host | The AI application the user operates | Desktop assistant or developer tool |
| Client | Protocol component inside the host | MCP client connection |
| Server | Program that exposes capabilities | Internal documentation MCP server |

A host may connect to several servers. Each server should expose only capabilities its security boundary permits.

## 3. Tools, resources, and prompts

- **Tool:** An action such as searching approved documentation or creating a ticket.
- **Resource:** Data a client can read, such as a runbook or document.
- **Prompt:** A reusable prompt template supplied by a server.

A tool can have side effects. A resource is primarily a content/context surface. A prompt is a template, not an access-control rule.

## 4. Build the business function first

Before wrapping code in a protocol, build and test a normal Python function.

```python
from pydantic import BaseModel, Field

class SearchInput(BaseModel):
    query: str = Field(min_length=2, max_length=200)

def search_sample_docs(args: SearchInput) -> dict:
    # Synthetic data only. Replace with an approved, permission-filtered service.
    return {
        "query": args.query,
        "matches": [
            {"document_id": "guide-001", "title": "Linux service guide"}
        ],
    }

validated = SearchInput(query="Linux service")
print(search_sample_docs(validated))
```

**What it does:** Pydantic validates input, and the function returns a predictable object.

**Why useful:** The business logic can be unit-tested without starting an MCP server. The MCP layer should be a thin adapter around already-tested functions.

**Use case:** Reuse the same documentation search logic from a web API, command-line tool, and MCP server.

## 5. Official Python SDK — setup

The official Python SDK documentation currently describes the v2 stable line and requires Python 3.11 or newer. Check the current installation and migration guide before adding it to a real project.

```bash
python -m venv .venv
# Activate the environment, then:
python -m pip install "mcp[cli]"
```

Do not mix examples written for MCP SDK v1 with v2. Pin a reviewed version in your dependency file and follow the current SDK examples.

## 6. Minimal local server example

The following shows the current high-level Python SDK style. Keep the sample tool read-only and use synthetic data.

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("developer-knowledge")

@mcp.tool()
def search_docs(query: str) -> list[dict[str, str]]:
    """Search a small sample knowledge collection."""
    if not query.strip() or len(query) > 200:
        raise ValueError("Provide a query between 1 and 200 characters.")

    documents = [
        {"id": "linux-01", "title": "Linux service troubleshooting"},
        {"id": "python-01", "title": "Python virtual environments"},
    ]
    return [
        item for item in documents
        if query.lower() in item["title"].lower()
    ]

if __name__ == "__main__":
    mcp.run()
```

**What it does:** Creates a server and exposes a function as a tool. The SDK handles protocol details.

**Why useful:** Compatible MCP hosts can discover the tool and request it without knowing the server's internal implementation.

**Use case:** Make an approved support-document search available to a desktop AI host.

SDK imports and server startup details may change between versions. Use the official v2 quickstart as the source of truth.

## 7. Discovery and execution

1. Client establishes a connection and negotiates protocol capabilities.
2. Client asks which capabilities are available.
3. Server lists permitted tools, resources, and prompts.
4. Host/model decides whether a tool is relevant.
5. Client sends a tool request.
6. Server validates input, applies access controls, executes the operation, and returns content or a structured error.

The model's selection is not authorisation. The server must enforce permissions for every request.

## 8. Transport choices

- **stdio:** Common for a local server started as a child process. Standard input/output carries protocol messages; do not print ordinary debug text to the protocol output stream.
- **Streamable HTTP:** Standard transport for remote server communication. It needs suitable authentication, TLS, origin validation, and deployment controls.
- **SSE:** Older transport still relevant to some existing integrations. Prefer the current standard transport for new work unless compatibility requires otherwise.

Choose transport based on host compatibility and deployment needs. Do not expose a local stdio server as a network service without a deliberate architecture.

## 9. Security checklist

- Expose the smallest possible set of tools.
- Validate every argument at the server boundary.
- Authenticate remote clients and authorise every operation.
- Enforce tenant/document permissions in backend queries.
- Treat tool descriptions, user input, and resource contents as untrusted.
- Never expose unrestricted shell, arbitrary SQL, private keys, or production credentials.
- Require user approval for sending messages or changing business records.
- Set timeouts, response-size limits, rate limits, and audit logs.
- Avoid logging secrets or sensitive returned content.

## 10. Practice project

Build a local MCP server for a synthetic support knowledge base:
1. Add one read-only search tool.
2. Add one resource containing a sample runbook.
3. Connect using a compatible inspector or host.
4. Confirm the tool and resource are discoverable.
5. Test blank and oversized input.
6. Add tests proving that the server returns only documents the test user may read.
7. Document how remote authentication would work.

## Self-check

1. What is the difference between an MCP host, client, and server?
2. How is a tool different from a resource?
3. Why does tool discovery not grant permission?
4. When is stdio suitable?
5. Why must retrieved resource content be treated as untrusted?

## Official references

- MCP documentation: https://modelcontextprotocol.io/docs/
- MCP specification: https://modelcontextprotocol.io/specification/
- Official Python SDK v2 documentation: https://py.sdk.modelcontextprotocol.io/
- Official Python SDK repository: https://github.com/modelcontextprotocol/python-sdk

The protocol and SDK evolve. Confirm version, transport, authentication, and API details against current official docs before implementation.
