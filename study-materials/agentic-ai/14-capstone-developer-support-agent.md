# Lesson 14 — Capstone: Build a Developer Support Agent Step by Step

## Capstone goal

Build a small support assistant that answers developer questions from a controlled knowledge set and uses read-only tools. This capstone combines Python, LLMs, tool calling, RAG, state, evaluation, and safety.

Use synthetic or public documentation. Do not index company secrets, production logs, or customer information.

## User story

As a developer, I want to ask:
- “How do I check whether a Linux service is running?”
- “What does HTTP 502 mean?”
- “Find the Python setup instructions.”
- “Check the sample service status.”
- “Summarise what the runbook says and cite the source.”

The assistant should answer only from available evidence where the question requires documentation. It should admit when there is no relevant source.

## Phase 1 — Create the project structure

```text
developer-support-agent/
├── app/
│   ├── main.py
│   ├── models.py
│   ├── agent.py
│   ├── tools.py
│   ├── policy.py
│   └── retrieval.py
├── knowledge/
│   ├── linux-basics.md
│   └── python-setup.md
├── tests/
│   ├── test_tools.py
│   ├── test_policy.py
│   └── test_retrieval.py
├── evals/
│   └── cases.json
├── .env.example
├── .gitignore
└── requirements.txt
```

Start with a single Python script if the structure feels overwhelming. Split files only when each responsibility is understood.

## Phase 2 — Define request and response models

Use Pydantic to validate user input.

```python
from pydantic import BaseModel, Field

class AskRequest(BaseModel):
    question: str = Field(min_length=2, max_length=1000)

class AskResponse(BaseModel):
    answer: str
    sources: list[str]
    run_id: str
```

Add validation tests for blank and oversized inputs.

## Phase 3 — Add sample knowledge

Create two short Markdown files:
- Linux service status guide.
- Python virtual environment guide.

Each file should have a stable document ID and title. Do not remove source metadata when creating chunks.

For the first version, use a simple keyword search. Do not introduce a vector database until the basic flow works.

## Phase 4 — Build read-only tools

Example tool contract:

```python
from pydantic import BaseModel, Field

class SearchArgs(BaseModel):
    query: str = Field(min_length=2, max_length=200)

def search_docs(data: SearchArgs) -> dict:
    # Replace this fixture with local, permission-filtered document search.
    return {
        "matches": [
            {
                "document_id": "linux-basics",
                "title": "Linux service status",
                "text": "Use systemctl status service-name to inspect a service."
            }
        ]
    }
```

This sample returns one fixed result. The project implementation should actually search the sample files.

## Phase 5 — Add an agent

Choose either:
- Build the model/tool loop yourself using the earlier lessons, or
- Use the OpenAI Agents SDK after the plain-Python flow is understood.

Expose only `search_docs` and a harmless calculator/status demo at first. Do not expose shell, arbitrary SQL, ticket update, restart, email-send, or cloud deployment tools.

## Phase 6 — Ground answers in sources

For each retrieved passage, keep:
- Document ID.
- Title.
- Heading or page where possible.
- Text.
- Version or updated time.

The assistant should say when evidence is missing and return source IDs for supported claims. Do not claim a citation supports a statement unless that source was actually used.

## Phase 7 — Add bounded execution

Set a maximum tool-call/step budget, network timeouts, request size limits, and a total run deadline. Add a safe stop path for unknown tools, repeated errors, or missing evidence.

## Phase 8 — Add tests and evaluation

Minimum tests:
1. Blank question is rejected.
2. Unknown tool is rejected.
3. Search returns the expected document ID.
4. Missing evidence produces a no-answer result.
5. Unauthorised document is filtered.
6. Agent cannot exceed maximum steps.
7. Timeout is handled.
8. A write action is blocked without human approval.

Add at least 20 evaluation questions with expected source IDs and expected behaviour.

## Phase 9 — Add observability

Record run ID, duration, model identifier, prompt version, retrieved source IDs, tool name, outcome, and token usage when available. Do not log API keys or full sensitive content.

## Phase 10 — Package and document

Containerise with Docker. Document:
- Setup and Python version.
- How to configure the API key securely.
- How to run the API.
- How to run tests and evaluations.
- Known limitations.
- Cost and privacy assumptions.
- How to disable all tools quickly.


## Self-check

1. Why should the first project version use keyword search before adding a vector database?
2. Which responsibilities belong in the application rather than the LLM?
3. Why does the assistant need source IDs and a no-answer behaviour?
4. Why must access checks be applied before retrieved content reaches the model?
5. Which tests prove that the agent cannot exceed its limits?
6. What must be approved before a write tool is enabled?


## Definition of done

- [ ] I can explain the agent control loop.
- [ ] Inputs and tool arguments are validated.
- [ ] Only approved tools are available.
- [ ] User/data permissions are enforced in application code.
- [ ] The agent has step, time, and cost limits.
- [ ] The answer can cite its source documents.
- [ ] No-answer and failure cases are tested.
- [ ] Human approval is required for hypothetical write actions.
- [ ] Logs/traces do not expose secrets.
- [ ] The project can be run locally from a clean setup.

## Stretch goals

After the basic capstone works:
- Replace keyword search with embeddings and vector search.
- Add hybrid retrieval and reranking.
- Add an MCP server exposing the read-only search tool.
- Add a specialist agent only if a clear need exists.
- Add Docker Compose and an approved non-production Azure deployment.
- Add a human approval service for a simulated ticket update.

**Capstone outcome:** A bounded, tested, source-aware developer assistant—not an unrestricted autonomous system.
