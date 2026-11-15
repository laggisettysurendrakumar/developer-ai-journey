# 05 — Build a Local Read-Only MCP Server with Python

## Goal

I will build a small local server that exposes a read-only search tool and a resource. I keep the business logic simple and use synthetic data. This lets me focus on the protocol boundary without exposing company systems.

## 1. Prepare the environment

Use Python 3.11 or newer for the current official Python SDK v2 line, and check the SDK's current installation guide before choosing a version.

```bash
mkdir mcp-learning
cd mcp-learning
python -m venv .venv
```

Activate the environment:

```bash
# Windows PowerShell
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

Install the official SDK CLI extra:

```bash
python -m pip install "mcp[cli]"
python -m pip freeze
```

For a repeatable project, pin the reviewed dependency version in a requirements or lock file. Do not silently upgrade production dependencies.

## 2. Write business logic first

Create `knowledge.py`:

```python
from pydantic import BaseModel, ConfigDict, Field

class SearchInput(BaseModel):
    model_config = ConfigDict(extra="forbid")
    query: str = Field(min_length=2, max_length=100)

DOCUMENTS = [
    {"id": "linux-01", "title": "Linux service troubleshooting"},
    {"id": "python-01", "title": "Python virtual environments"},
    {"id": "docker-01", "title": "Docker container basics"},
]

def search_documents(query: str) -> list[dict[str, str]]:
    parsed = SearchInput(query=query)
    term = parsed.query.casefold()
    return [
        {"id": item["id"], "title": item["title"]}
        for item in DOCUMENTS
        if term in item["title"].casefold()
    ]
```

Try importing and testing the function before creating the server. This separates search bugs from protocol bugs.

## 3. Create the MCP server

Create `server.py` using the high-level API documented by the installed official SDK version. The SDK evolves; compare this pattern with the current v2 quickstart before running it.

```python
from mcp.server.fastmcp import FastMCP
from knowledge import search_documents

mcp = FastMCP("developer-knowledge")

@mcp.tool()
def search_docs(query: str) -> list[dict[str, str]]:
    """Search synthetic developer knowledge titles."""
    return search_documents(query)

@mcp.resource("knowledge://welcome")
def welcome() -> str:
    """Describe the sample knowledge collection."""
    return (
        "Sample knowledge collection: Linux, Python, and Docker guide titles. "
        "The data is synthetic and contains no private company documents."
    )

if __name__ == "__main__":
    mcp.run()
```

The server exposes one tool and one static resource. The SDK manages protocol framing and lifecycle details.

## 4. Run and inspect locally

The official SDK CLI provides a development workflow. From the project directory, consult the installed CLI help and current SDK quickstart; a common documented workflow is:

```bash
mcp dev server.py
```

The Inspector workflow may require Node.js and `npx`. With uv-managed projects, the documented pattern may use `uv run mcp dev server.py`. Confirm commands against the installed SDK version.

Do not print debug messages to standard output when using stdio transport. Keep protocol output clean; send diagnostics to standard error or a configured logger.

## 5. What to test

- Can the client initialise?
- Does tool discovery list `search_docs`?
- Does resource discovery/read return the welcome text?
- Does a search return expected sample titles?
- Does an empty or oversized query fail safely?
- Does the server stop cleanly when the client disconnects?

## 6. Add automated tests

Test `search_documents` directly with pytest before testing the protocol layer:

```python
import pytest
from knowledge import search_documents

def test_search_returns_matching_title():
    results = search_documents("Linux")
    assert results
    assert all("Linux" in item["title"] for item in results)

def test_search_rejects_too_short_query():
    with pytest.raises(Exception):
        search_documents("")
```

In a mature test suite, assert the specific Pydantic validation exception rather than the broad `Exception` used in this introductory example.

## Security boundary

This demo is intentionally local and read-only. It has no authentication, real identity, database access, tenant isolation, or production logging. Do not expose it to a network or connect it to private documents without adding those controls.

## Practice exercise

Add a `get_document_title(document_id)` tool with an allowlisted set of synthetic IDs. Add tests for a valid ID, an unknown ID, malformed input, and a deliberately oversized input. Keep the result to the title only.

## Self-check

1. Why write and test business logic separately from MCP?
2. What does the SDK manage for me?
3. Why should stdio debug output not be printed to protocol stdout?
4. What does the Inspector help me verify?
5. Which production controls are missing from this local demo?

## Further reading

- [Official Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [Python SDK documentation](https://py.sdk.modelcontextprotocol.io/)
- [MCP quickstart](https://modelcontextprotocol.io/docs/develop/build-server)
