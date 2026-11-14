# Lesson 02 — Python Building Blocks Used Inside Every Agent

## Learning goals

Understand the Python concepts that an agent framework uses behind the scenes: functions, dictionaries, lists, classes, exceptions, type hints, and async functions.

If these ideas are unfamiliar, revisit the matching lessons in the 14-day Python course before continuing.

## 1. Functions become tools

A tool is often just a normal function that the application has deliberately made available.

```python
def calculate_total(price: float, quantity: int) -> float:
    return price * quantity

total = calculate_total(125.50, 3)
print(total)
```

- `def` creates a function.
- `price` and `quantity` are inputs.
- `return` sends the result to the caller.
- The function does not run until it is called.

**Agent use case:** A calculator tool, date formatter, or knowledge search function.

## 2. Dictionaries represent structured data

Model APIs and tool calls often exchange JSON-shaped data. Python dictionaries are a natural way to represent it.

```python
tool_request = {
    "name": "calculate_total",
    "arguments": {
        "price": 125.50,
        "quantity": 3
    }
}

print(tool_request["name"])
print(tool_request["arguments"]["quantity"])
```

Think of a dictionary as labelled fields. Accessing a missing key raises `KeyError`, so validate external payloads before using them.

## 3. Lists represent sequences

An agent may need to keep a sequence of messages or tool results.

```python
messages = [
    {"role": "user", "content": "Calculate 10 times 5"},
    {"role": "assistant", "content": "I will use the calculator."}
]

messages.append({"role": "tool", "content": "50"})

for message in messages:
    print(message["role"], message["content"])
```

A list keeps items in order and can be modified. A real application may store messages in a database rather than keeping all history in memory.

## 4. Type hints make tool contracts readable

```python
def search_docs(query: str, limit: int = 5) -> list[str]:
    """Search approved documentation and return titles."""
    return ["Python guide", "Agent design guide"][:limit]
```

`str`, `int`, and `list[str]` describe expected types. Type hints help developers and editors, but Python does not automatically validate all external values from annotations.

## 5. Pydantic validates external arguments

Install Pydantic in your virtual environment:

```bash
python -m pip install pydantic
```

```python
from pydantic import BaseModel, Field

class SearchArgs(BaseModel):
    query: str = Field(min_length=2, max_length=200)
    limit: int = Field(default=5, ge=1, le=10)

incoming = {"query": "Python agent", "limit": 3}
args = SearchArgs.model_validate(incoming)

print(args.query, args.limit)
```

**Why useful?** A model may propose malformed arguments. Validation stops bad data before a function runs.

This validates shape and value ranges. It does not prove a user is authorised to search a particular document.

## 6. Exceptions represent failure

```python
def divide(left: float, right: float) -> float:
    if right == 0:
        raise ValueError("The denominator cannot be zero")
    return left / right

try:
    print(divide(10, 0))
except ValueError as error:
    print(f"Tool input error: {error}")
```

An agent tool can fail because of invalid input, access denial, timeout, or upstream service failure. Return safe error information and do not silently hide failures.

## 7. Classes hold related state and behaviour

```python
class AgentSession:
    def __init__(self, user_id: str):
        self.user_id = user_id
        self.steps = 0
        self.messages: list[dict[str, str]] = []

    def add_message(self, role: str, content: str) -> None:
        self.messages.append({"role": role, "content": content})

    def increment_step(self) -> None:
        self.steps += 1

session = AgentSession("demo-user")
session.add_message("user", "Find the Python guide")
session.increment_step()
print(session.steps)
```

A class keeps related information and operations together. This is an in-memory teaching example, not persistent storage.

## 8. Async: why agent APIs commonly use await

Model and tool APIs spend time waiting for network responses.

```python
import asyncio

async def search_tool() -> str:
    await asyncio.sleep(1)  # Simulates network waiting.
    return "Search completed"

async def main():
    result = await search_tool()
    print(result)

asyncio.run(main())
```

`async def` defines a coroutine. `await` waits for it without blocking the entire event loop. It does not magically make CPU-heavy work faster.

## 9. Put the pieces together

An agent runner typically needs:
- A list of messages.
- Tool definitions and schemas.
- A model client.
- A loop that decides whether to call a tool or stop.
- A step counter.
- Error handling and logging.
- A final output.

We build this loop in the next lesson without using an AI framework.

## Practice exercise

Create a Python file with:
1. A Pydantic input model for a calculator.
2. A function that validates and calculates.
3. A class that counts tool calls.
4. A test for divide-by-zero and invalid input.
5. Logging that records tool name and outcome without logging secrets.

## Self-check

1. Why are Python functions useful as tools?
2. Why should tool arguments be validated?
3. What is the difference between a list and a dictionary?
4. Why use a class for session state?
5. Why do model calls often use async Python?

**Completion check:** I can implement and test a normal Python function before turning it into an agent tool.
