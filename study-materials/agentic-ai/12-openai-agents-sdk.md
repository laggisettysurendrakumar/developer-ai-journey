# Lesson 12 — Build an Agent with the OpenAI Agents SDK

## Learning goals

Now that I understand the control loop, tools, validation, state, safety, and evaluation, I can use an agent framework. This lesson focuses on the OpenAI Agents SDK for Python. The SDK manages much of the tool orchestration, but it does not remove application security responsibilities.

## 1. What the framework provides

The SDK provides building blocks for:
- Defining an agent with instructions and tools.
- Running the model/tool cycle.
- Delegating tasks through handoffs.
- Adding guardrails and context.
- Tracing agent execution.

The framework is not a substitute for secure API design, authorisation, validation, or tests.

## 2. Install in a virtual environment

Use the same Python environment created in the Python course.

```bash
python -m pip install openai-agents
```

Set the API key in your current shell session.

Windows PowerShell:

```powershell
$env:OPENAI_API_KEY = "YOUR_API_KEY"
```

macOS/Linux:

```bash
export OPENAI_API_KEY="YOUR_API_KEY"
```

Never commit the key or print it in logs. A real request can incur usage charges; check current model availability, pricing, and usage limits first.

## 3. Minimal agent with no tools

Create `first_agent.py`:

```python
import asyncio
from agents import Agent, Runner

agent = Agent(
    name="Python Tutor",
    instructions=(
        "Explain Python concepts to a beginner. "
        "Use short examples. If unsure, say so."
    ),
)

async def main():
    result = await Runner.run(
        agent,
        "Explain what a Python dictionary is."
    )
    print(result.final_output)

if __name__ == "__main__":
    asyncio.run(main())
```

Run:

```bash
python first_agent.py
```

**What happens?**
1. `Agent` defines the name and instructions.
2. `Runner.run` starts an agent run.
3. The SDK calls the configured model.
4. The result's final output is printed.

The installed SDK may require a configured default model or supported environment. Follow its current quickstart if defaults change.

## 4. Add a safe read-only tool

Create `agent_with_tool.py`:

```python
import asyncio
from agents import Agent, Runner, function_tool

@function_tool
def search_python_notes(query: str) -> str:
    """Search a tiny local sample of Python notes."""
    if not query.strip() or len(query) > 100:
        return "The search query must contain 1 to 100 characters."

    notes = {
        "dictionary": "A dictionary stores key-value pairs.",
        "list": "A list stores an ordered sequence of items.",
        "function": "A function is a reusable block of code."
    }

    matches = [
        note for keyword, note in notes.items()
        if keyword in query.lower()
    ]
    return "\n".join(matches) if matches else "No matching note was found."

agent = Agent(
    name="Python Notes Assistant",
    instructions=(
        "Answer beginner Python questions. "
        "Use search_python_notes when the local notes may contain the answer. "
        "Do not claim to search any external system."
    ),
    tools=[search_python_notes],
)

async def main():
    result = await Runner.run(
        agent,
        "Search my notes for dictionary information."
    )
    print(result.final_output)

if __name__ == "__main__":
    asyncio.run(main())
```

This uses a fixed local dictionary. It does not access private files or external services.

## 5. Understand the function tool

- The decorator registers a Python function as an agent tool.
- The function name and description help the model understand its purpose.
- The function's parameters form the tool input contract.
- The runner manages the model/tool cycle.
- The function executes in the Python application, so it must still enforce permissions and limits.

Do not assume the model will only call the tool in the intended way. Validate untrusted inputs inside the function and in the service layer.

## 6. Context and sessions

Applications often need state that survives multiple turns. The SDK provides session and context mechanisms; their exact APIs evolve. Read the current session documentation before choosing a persistence approach.

Before retaining conversation data, decide:
- Who owns the session?
- How is ownership checked?
- What is retained and for how long?
- How can the user request deletion?
- What is excluded from model context?
- How are sensitive fields redacted?

Do not use one shared conversation store without user/tenant isolation.

## 7. Tracing

The SDK includes tracing support for observing agent runs. Use it to understand model calls, tool calls, and handoffs. Configure appropriate privacy and data-retention settings before tracing real user data.

## 8. Troubleshooting checklist

- Missing API key: confirm the environment variable is present without printing it.
- Authentication failure: verify key, project, and access.
- Model error: verify model availability and request compatibility.
- Rate limit: reduce concurrency and respect retry guidance.
- Tool not called: inspect tool descriptions, instructions, and trace.
- Tool input rejected: add better validation/error handling.
- Long-running run: add timeouts and step limits appropriate to the SDK/runtime.

## 9. Practice exercise

Build a small Python tutor agent with two read-only tools: `search_python_notes` and `calculate_sum`. Test:
1. Question answered without a tool.
2. Question answered using a tool.
3. Blank tool input.
4. Invalid numeric input.
5. Unknown question with insufficient knowledge.

Record which tool was called and why. Use an evaluation dataset rather than checking only one response.

## Self-check

1. What does `Agent` configure?
2. What does `Runner.run` do?
3. Where does the Python tool function execute?
4. Does the framework automatically authorise access to private data?
5. Why should I learn the manual loop before using a framework?

## Official references

- OpenAI Agents SDK for Python: https://openai.github.io/openai-agents-python/
- SDK quickstart: https://openai.github.io/openai-agents-python/quickstart/
- OpenAI agent guide: https://platform.openai.com/docs/guides/agents
- Function tools: https://openai.github.io/openai-agents-python/tools/

SDK interfaces can change. Use official quickstart and API reference for the exact installed version. Do not copy old examples that use different decorators or runner methods.
