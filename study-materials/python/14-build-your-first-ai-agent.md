# Day 14 — Build a tool-using AI agent

## Goal for today

I will combine Python, type validation, API concepts, and tool execution into a small agent. First I will understand the control flow. Then I will run a real agent with a narrow calculator tool using the OpenAI Agents SDK.

## 1. What is an AI agent?

An AI agent is an application pattern where a model helps decide what step to take, and the surrounding program executes permitted actions and feeds results back into the process.

A typical tool-using cycle:
1. Receive a goal.
2. Provide instructions and available tool descriptions to the model.
3. The model proposes a tool call or a final response.
4. The application validates the request and checks permissions.
5. The approved function runs and returns a result.
6. The result is supplied for the next decision, within limits.
7. The workflow stops when complete or when a safe stopping condition is met.

An agent is not simply a clever prompt. Python code and the agent runtime control tools, state, permissions, limits, and error handling.

## 2. Understand the parts before using a framework

- **Model:** interprets the task and proposes the next step.
- **Instructions:** describe the agent's role and boundaries.
- **Tool:** a function the agent may request.
- **Runner/orchestrator:** manages model turns and tool execution.
- **Context/state:** information needed during a run.
- **Guardrail:** a check that blocks unsafe or invalid input/output/actions.
- **Trace:** a record of the steps taken, useful for debugging and evaluation.

Frameworks provide conveniences, but knowing Python functions, classes, validation, and async makes the framework easier to understand.

## 3. Install the OpenAI Agents SDK

Create and activate a virtual environment first. Then install:

```bash
python -m pip install openai-agents
```

Set `OPENAI_API_KEY` as an environment variable using the instructions in Day 12. Use a secure key and never commit it. A real model call can incur API charges; check current pricing and usage controls.

## 4. Create a narrow calculator tool

Create `agent_demo.py`:

```python
import asyncio

from agents import Agent, Runner
from agents.decorators import tool

@tool
def add_numbers(left: float, right: float) -> str:
    """Add two numbers and return the result."""
    return str(left + right)

agent = Agent(
    name="Python Learning Assistant",
    instructions=(
        "Help with basic Python questions. "
        "Use add_numbers only when a calculation is useful. "
        "Do not claim to have performed actions that you did not perform."
    ),
    tools=[add_numbers],
)

async def main():
    result = await Runner.run(
        agent,
        "What is 125 plus 275? Explain the calculation briefly."
    )
    print(result.final_output)

if __name__ == "__main__":
    asyncio.run(main())
```

Run:

```bash
python agent_demo.py
```

The decorator exposes a Python function as a tool to the agent SDK. The docstring describes what the tool does. The agent can request the tool; the SDK manages the model/tool cycle. The runner is asynchronous, so I call it using `await` inside `main()`.

The example is intentionally narrow and read-only apart from calculating a value. It demonstrates tool wiring, not a production-ready application. SDK interfaces can evolve; follow the current official quickstart if an import or option changes.

## 5. What happens when this runs?

1. Python defines the function and agent configuration.
2. `Runner.run()` starts an agent run with the user's request.
3. The model decides whether a tool is useful.
4. If it requests `add_numbers`, the runtime invokes that function with arguments.
5. The result is returned to the model.
6. The agent produces its final response.
7. Python prints the final output.

The model does not get unrestricted access to my computer. It can only use the tools I deliberately expose through the application and SDK.

## 6. Add conversation context

A conversation often needs prior messages. Agent SDKs provide ways to carry input history or use session/state features. Do not store all conversation history forever by default. Decide how to handle retention, privacy, summarisation, and user separation. Check the current SDK documentation for the supported session approach.

## 7. Production safeguards

A demo is not production-ready. Before connecting real systems, add:

- **Authentication:** identify the user who made the request.
- **Authorisation:** check that the user may perform the requested action.
- **Tool allowlist:** expose only deliberately approved functions.
- **Input validation:** enforce ranges, lengths, and allowed values.
- **Step and time limits:** prevent loops and runaway cost.
- **Rate/concurrency limits:** protect downstream services.
- **Audit logs:** record tool name, user identity, outcome, and request ID without storing secrets.
- **Human approval:** require confirmation before sending messages, changing records, deploying software, or taking consequential actions.
- **Prompt-injection defence:** treat retrieved documents and tool outputs as untrusted data.
- **Evaluation:** test normal, invalid, adversarial, and edge-case requests.

Do not give an LLM unrestricted shell, database, cloud, or production credentials. Put permission checks in trusted code, not only in the prompt.

## 8. Final project extension: developer knowledge assistant

Extend the demo over the next few weeks:
1. Load three synthetic Markdown documents from a local folder.
2. Split them into chunks and keep document IDs and titles.
3. Retrieve relevant chunks.
4. Add a read-only `search_knowledge` tool to the allowlist.
5. Keep the calculator as a second tool.
6. Return answers with source document IDs.
7. Add unit tests for validation, retrieval, and tool behaviour.
8. Add evaluation examples that check whether answers are grounded in the retrieved documents.

Do not index confidential work material unless storage and processing services are approved.

## 9. What to learn after the 14 days

To move from a learning prototype toward professional agent development, continue with:
- FastAPI application architecture, authentication, dependency injection, background jobs, and deployment.
- Database fundamentals and persistent conversation state.
- RAG quality: chunking, embedding model choice, vector stores, hybrid search, reranking, citations, and access filtering.
- Agent orchestration: handoffs, specialist agents, tool schemas, guardrails, tracing, and human approval.
- Evaluation: fixed test sets, task success, retrieval quality, groundedness, latency, and cost.
- Operations: Docker, CI/CD, monitoring, rate limiting, secret management, and incident response.

## Practice exercise

Change the request to ask for 50 plus 15. Then add a second safe tool that converts a temperature from Celsius to Fahrenheit. Explain how the agent knows which tool is available and what controls the application should enforce.

## Self-check

1. What makes a tool-using agent different from one model prompt and response?
2. What happens when an agent requests a tool I did not expose?
3. Why is a step limit necessary?
4. What controls are needed before a tool changes business data?
5. Why does finishing a 14-day course not automatically make me a production agent engineer?

**Course completion:** I now have a Python foundation and a first tool-using agent. My next step is to build a complete application, add tests and evaluation, and learn deeper agent patterns through projects.

Official reference: https://openai.github.io/openai-agents-python/quickstart/