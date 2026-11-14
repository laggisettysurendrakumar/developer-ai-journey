# Lesson 01 — What Is Agentic AI? Understand It from Zero

## Learning goals

By the end of this lesson I should be able to explain, in simple terms, what an AI agent is, why people use agents, and how an agent is different from an ordinary chatbot or a fixed workflow.

## 1. Start with a familiar example

Imagine asking an assistant:

> Find the likely cause of this API outage, check our runbook, and prepare a short incident update.

A basic chatbot can explain how outages are investigated. It does not automatically inspect your runbook, check service data, or prepare an update using current evidence.

An AI-enabled application can call a model, but the application developer must write the exact sequence of operations.

An agentic application gives a model a goal and a limited set of tools. The model can decide which permitted step is useful next. The application executes the selected tool, provides the result, and lets the model decide what to do next.

**The model suggests a next step; trusted application code controls what actually happens.**

## 2. Important terms

| Term | Beginner meaning | Example |
|---|---|---|
| AI | Software that performs tasks associated with human intelligence | Classifying support tickets |
| Generative AI | AI that generates content | Drafting a response |
| LLM | A model that processes and generates language | A model answering a question |
| Agent | An application in which a model chooses among permitted next steps | A support assistant selecting search or calculator |
| Tool | A function/service the agent can request | Search approved docs |
| Orchestration | Code that coordinates model calls and tools | Run loop and tool registry |
| State | Information needed while one task is running | Current task, steps, results |
| Memory | Information deliberately retained beyond the current run | User-approved preferences |
| Guardrail | A check that restricts unsafe or invalid behaviour | Reject unknown tool |
| Trace | A record of the steps taken | Model call → search tool → answer |

## 3. Chatbot, workflow, and agent

### Chatbot

A chatbot usually receives a message and produces a response. It may use conversation history, but it does not necessarily use external tools.

### Workflow

A workflow follows a path designed by the developer.

Example:
1. Validate a support ticket.
2. Look up the service owner.
3. Retrieve the runbook.
4. Generate a suggested summary.
5. Return the summary.

The steps are predictable. A normal Python function or workflow engine is usually easier to test than an agent.

### Agent

An agent chooses from available actions based on the current goal and observations. It may search a runbook, ask for missing information, check a read-only status tool, or stop and explain that evidence is insufficient.

The choice is dynamic, but the available tools and permissions must remain controlled by the application.

## 4. Decision guide — should this task use an agent?

Use a deterministic workflow when:
- The steps are always known.
- The outcome must be repeatable.
- The task is simple validation or CRUD.
- The action is high impact and does not need model judgement.

Consider an agent when:
- The next step depends on information discovered during execution.
- The request can be achieved in several valid ways.
- Natural-language understanding helps select a tool.
- A human can review consequential actions.

Do not use an agent just because the word “agent” is popular.

## 5. Understand the architecture

```text
User request
    |
    v
Application/API
    |
    v
Agent runner / orchestrator
    |
    +----> Model: decide next step
    |          |
    |          +----> Final answer
    |          |
    |          +----> Tool request
    |                    |
    v                    v
Trusted policy checks   Tool registry
                         |
                         v
                  Approved function/API
                         |
                         v
                    Tool result
                         |
                         +----> back to model
```

The runner controls the cycle. The model does not receive unrestricted access to the host machine.

## 6. First non-AI example: a controlled workflow

Create `workflow_demo.py`:

```python
def validate_request(question: str) -> str:
    cleaned = question.strip()
    if not cleaned:
        raise ValueError("A question is required")
    if len(cleaned) > 200:
        raise ValueError("Question is too long")
    return cleaned

def search_sample_docs(question: str) -> list[str]:
    # Fixed sample data. No network or model is involved.
    if "python" in question.lower():
        return ["Python setup guide", "Virtual environment guide"]
    return []

def run_workflow(question: str) -> dict:
    cleaned = validate_request(question)
    documents = search_sample_docs(cleaned)
    return {
        "question": cleaned,
        "documents": documents,
        "found_evidence": bool(documents),
    }

print(run_workflow("How do I set up Python?"))
```

### Understand every part

- `validate_request` checks input before work starts.
- `search_sample_docs` represents a fixed lookup. It is not semantic search.
- `run_workflow` calls the steps in a fixed order.
- `bool(documents)` becomes `True` when at least one document is returned.

**Why useful?** An agent still needs ordinary programming for validation, tool execution, permissions, errors, and result formatting.

## 7. Agent versus workflow in a developer use case

Suppose I want to investigate a deployment failure.

A workflow may always retrieve the deployment runbook and recent deployment record.

An agent may choose to search the deployment runbook, ask for the environment if it is missing, check a read-only deployment status tool, or search for an error identifier if one appears.

The agent is useful only if its ability to choose the next step adds value. If the task is always the same, use a workflow.

## 8. Beginner mistakes

- Thinking an agent is simply a long system prompt.
- Giving an agent too many tools before understanding each one.
- Believing model-generated answers are always correct.
- Allowing a model to directly run shell commands or SQL.
- Starting with multiple agents before one agent works.
- Ignoring cost, timeouts, permissions, and loops.

## Practice exercise

1. Write down a task from software development, such as investigating a failed API request.
2. List the steps that are always required.
3. List the decisions that depend on information discovered.
4. Decide which parts should be deterministic and which might benefit from an agent.
5. Explain why an agent is or is not justified.

## Self-check

1. What is the difference between GenAI and Agentic AI?
2. What is a tool?
3. Who executes the tool: the model or the application runtime?
4. When is a workflow better than an agent?
5. Why should the model not be given unrestricted computer access?

**Completion check:** I can explain agentic AI without using framework-specific words.
