# Agentic AI Engineering

I created this learning track to understand the topic from the beginning and apply it to real GenAI and agentic AI applications. Each concept is explained in plain language, followed by code, why it is useful, a developer use case, and an exercise.

## Who should use this guide
Developers who have built a basic model API call and understand functions, validation, async code, and tool calling.

## Before starting
Complete the Python 14-day course, GenAI fundamentals, prompt engineering, and basic RAG. Build a tool-calling demo before using a framework.

## Learning sequence
1. Understand the difference between a chat completion, workflow, and agent.
2. Learn the agent loop: observe, decide, call a tool, inspect result, stop.
3. Define tools with narrow schemas and validate arguments.
4. Manage run state, conversation history, memory, and persistence.
5. Use planning only when the task needs decomposition; prefer deterministic workflows for fixed processes.
6. Learn handoffs, specialist agents, routing, and multi-agent trade-offs.
7. Add human approvals for side effects and high-impact actions.
8. Trace agent runs and evaluate tool selection, step count, correctness, latency, and cost.
9. Deploy with identity, secrets, rate limits, and failure recovery.

## Important concepts in plain language
### Workflow
A workflow is a predefined sequence or graph of steps. It is often better than an agent when the required path is known and predictable.

### Agent
An agent uses a model to decide among permitted next steps. The application still owns execution, permissions, limits, and safety.

### Tool
A tool is a function or service the agent can request. It should have a narrow purpose, validated input, and controlled output.

### State and memory
State is information needed during a run. Memory is information retained across runs. Persistent memory requires ownership, retention, privacy, and deletion policies.

### Handoff
A handoff routes work to another specialised agent or component. It introduces coordination and debugging complexity.

### Human-in-the-loop
A person reviews or approves an action before it happens. Use it when an action is consequential or difficult to reverse.

## Practical examples
Example — a bounded agent loop without a model
This demo shows application-controlled steps. It is deterministic and intentionally does not pretend that a normal Python function is an intelligent model.
```python
def run_demo_agent(goal: str, max_steps: int = 3) -> str:
    if not goal.strip():
        raise ValueError("Goal is required")
    if not 1 <= max_steps <= 5:
        raise ValueError("Step limit must be between 1 and 5")

    for step in range(max_steps):
        print(f"Working on step {step + 1}")
        if step == 1:
            return f"Completed the demo task: {goal}"

    raise RuntimeError("Step limit reached")

print(run_demo_agent("Explain a deployment error"))
```
Use case: make the maximum-step rule explicit before adding a real model that may request multiple tools.
Example — require approval before a write action
The model may suggest an action, but trusted code determines whether it is allowed and whether a human must approve it.
```python
def approve_action(action: str, approved_by_user: bool) -> None:
    if action in {"send_email", "update_record", "deploy"}:
        if not approved_by_user:
            raise PermissionError("Human approval is required")

approve_action("send_email", approved_by_user=False)
```
Use case: an assistant can draft an email automatically but cannot send it until the user approves.

## Mini-project
Build a Developer Support Agent with read-only tools: search a local knowledge base, calculate simple values, and retrieve a synthetic service status. Include explicit tool schemas, authorisation stubs, approval gates for write tools, bounded steps, tracing, unit tests, and an evaluation set.

## Safety, quality, and cost
- Never expose unrestricted shell, SQL, cloud, or production credentials to a model.
- Treat prompts, retrieved documents, web content, and tool results as untrusted input.
- Enforce authentication and authorisation in trusted code, not just system prompts.
- Bound steps, time, tokens, concurrency, retries, and tool output size.
- Require human approval for consequential or irreversible actions.
- Log traces without secrets and evaluate failures before expanding tool access.

## Suggested practice routine
1. Read one section and explain the idea in your own words.
2. Type the example instead of copying it blindly.
3. Change one input and predict the output before running it.
4. Test an invalid input and inspect the error.
5. Complete the exercise and record what you learned.

## References
- OpenAI Agents SDK: https://openai.github.io/openai-agents-python/
- OpenAI agent guide: https://platform.openai.com/docs/guides/agents
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/

Documentation and APIs change. Check the linked official documentation for current commands, SDKs, model availability, versions, and pricing before using an example in a real project.
