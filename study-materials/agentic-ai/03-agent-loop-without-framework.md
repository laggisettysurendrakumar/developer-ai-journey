# Lesson 03 — Build an Agent Loop Yourself Before Using a Framework

## Learning goals

Understand the control loop at the centre of many agents. This lesson uses a mock decision-maker first. It does not call an LLM; do not confuse the simulation with a real agent.

## 1. What is an agent loop?

A simple tool-using agent follows a repeated process:

1. Receive the user's goal.
2. Give the model current context and available tools.
3. Receive either a final answer or a proposed tool call.
4. Validate the proposed tool and arguments.
5. Execute only an allowed tool.
6. Add the tool result to the context.
7. Continue until the model finishes or a safety limit is reached.

The application owns steps 4–7. The model proposes a decision.

```text
Goal
 |
 v
Ask model for next action
 |
 +------> final response? ------> return answer
 |
 v
Tool request
 |
 v
Validate tool name and arguments
 |
 v
Check permissions and limits
 |
 v
Execute approved function
 |
 v
Add result to context
 |
 +----------> next model turn
```

## 2. Create two simple tools

```python
def calculator(expression: str) -> str:
    # Deliberately support one narrow operation, not arbitrary Python eval.
    if expression == "10 * 5":
        return "50"
    raise ValueError("This demo supports only the example 10 * 5")

def search_docs(query: str) -> str:
    if "python" in query.lower():
        return "Python setup guide: use a virtual environment."
    return "No matching sample document."
```

Never use Python `eval()` to execute model-generated expressions. A real calculator should parse and allow only an explicitly supported grammar or use safe arithmetic functions.

## 3. Create a strict tool registry

```python
TOOLS = {
    "calculator": calculator,
    "search_docs": search_docs,
}

def execute_tool(name: str, argument: str) -> str:
    tool = TOOLS.get(name)

    if tool is None:
        raise ValueError(f"Tool is not allowed: {name}")

    return tool(argument)
```

A registry is an allowlist. The model cannot call a function that the application did not register.

## 4. Simulate a model decision

In this lesson, write a mock decision function that returns predetermined decisions.

```python
def mock_model_decision(step: int) -> dict:
    if step == 1:
        return {
            "type": "tool_call",
            "tool": "search_docs",
            "argument": "Python setup"
        }

    return {
        "type": "final",
        "answer": "The Python guide recommends using a virtual environment."
    }
```

**Important:** This function is not AI. It simply returns a fixed decision so I can understand orchestration without API costs.

## 5. Implement a bounded loop

```python
def run_mock_agent(goal: str, max_steps: int = 3) -> str:
    if not goal.strip():
        raise ValueError("Goal cannot be blank")
    if not 1 <= max_steps <= 5:
        raise ValueError("max_steps must be between 1 and 5")

    for step in range(1, max_steps + 1):
        decision = mock_model_decision(step)

        if decision["type"] == "final":
            return decision["answer"]

        if decision["type"] != "tool_call":
            raise ValueError("Unknown decision type")

        result = execute_tool(
            decision["tool"],
            decision["argument"]
        )
        print(f"Tool result: {result}")

    raise RuntimeError("Agent stopped because the step limit was reached")

print(run_mock_agent("Find the Python setup guidance"))
```

### Understand the safety boundaries

- The loop stops after `max_steps`.
- Unknown decision types fail closed.
- Unknown tools are rejected.
- Only functions in `TOOLS` can run.
- Tool arguments are still basic strings in this lesson; we add Pydantic schemas later.
- A real implementation also needs timeout, cancellation, authorisation, and cost controls.

## 6. Why not let the model run Python code?

A model might propose `import os; ...` or a shell command. Executing arbitrary generated code can expose secrets, delete files, or access systems.

Instead, provide specific functions with validated arguments. A calculator tool should calculate; a ticket lookup tool should retrieve a permitted ticket. Do not provide general-purpose execution as a shortcut.

## 7. What frameworks add

Agent frameworks can manage:
- Model request formatting.
- Tool schema generation.
- Tool-call parsing.
- Tool execution cycles.
- Conversation context.
- Handoffs and tracing.

Frameworks do not remove the need to understand permissions, validation, timeouts, side effects, and evaluation.

## Practice exercise

Extend the mock agent:
1. Add a `get_service_status(service_name)` read-only tool backed by fixed sample data.
2. Reject unknown service names.
3. Add a per-run maximum of two tool calls.
4. Record tool names and durations.
5. Add tests for an unknown tool, blank argument, and step-limit exhaustion.

## Self-check

1. Which part proposes a tool call?
2. Which part validates and executes it?
3. Why does the tool registry act as an allowlist?
4. Why is a maximum step count necessary?
5. Why is the mock decision-maker not a real agent?

**Completion check:** I can explain the loop and implement the control logic without a framework.
