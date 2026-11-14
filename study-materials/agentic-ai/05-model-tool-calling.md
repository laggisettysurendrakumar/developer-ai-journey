# Lesson 05 — Connect a Real LLM to Python Tools

## Learning goals

Understand the difference between the mock agent loop and real model-driven tool calling. Learn the request/decision/result cycle without giving the model direct access to local resources.

## 1. What happens in model tool calling?

A model does not normally execute the Python function itself. The application:
1. Sends the conversation and tool descriptions to the model.
2. Receives a response that may request a tool.
3. Validates the tool name and arguments.
4. Executes the function in trusted application code.
5. Sends the tool result back to the model.
6. Receives the next response, which may be another tool call or a final answer.

The exact API fields differ by provider and SDK. The control principle is the same.

## 2. A provider-neutral flow

```text
Python application
   |
   | messages + tool schemas
   v
LLM API
   |
   | tool call request
   v
Python validates arguments
   |
   | permitted?
   +---- no ----> reject safely
   |
   v
Execute allowlisted function
   |
   | tool result
   v
LLM API
   |
   v
Final response
```

## 3. Start with a local tool

```python
from pydantic import BaseModel

class CalculatorInput(BaseModel):
    left: float
    right: float

def add_numbers(data: CalculatorInput) -> dict:
    return {"result": data.left + data.right}

raw_arguments = {"left": 125, "right": 275}
validated = CalculatorInput.model_validate(raw_arguments)
print(add_numbers(validated))
```

This isolates the part your application owns before introducing a model API.

## 4. OpenAI function tool calling — conceptual example

The Responses API supports tools, but provider SDK response shapes and model availability can evolve. Follow the current official tool-calling guide for the exact request and response fields.

A tool definition describes a function and its JSON Schema. A model response can contain a function-call item with a name and JSON arguments. Your code must parse those arguments, validate them, and execute only an allowlisted tool.

Illustrative tool schema:

```python
tool_schema = {
    "type": "function",
    "name": "add_numbers",
    "description": "Add two numbers. Use only for arithmetic addition.",
    "parameters": {
        "type": "object",
        "properties": {
            "left": {"type": "number"},
            "right": {"type": "number"}
        },
        "required": ["left", "right"],
        "additionalProperties": False
    },
    "strict": True
}
```

This is a data structure describing the tool. It is not the tool implementation and it does not authorise the caller.

## 5. Parse arguments as untrusted data

```python
import json
from pydantic import BaseModel, Field

class AddInput(BaseModel):
    left: float = Field(ge=-1_000_000, le=1_000_000)
    right: float = Field(ge=-1_000_000, le=1_000_000)

def handle_add_call(arguments_json: str) -> dict:
    raw = json.loads(arguments_json)
    args = AddInput.model_validate(raw)
    return {"result": args.left + args.right}

print(handle_add_call('{"left": 12, "right": 30}'))
```

`json.loads` parses a JSON string. Pydantic validates its shape and ranges. In production, also catch JSON parsing and validation errors at the tool boundary and return a safe error.

## 6. Use the official SDK after understanding the cycle

Install the OpenAI Python SDK in a virtual environment:

```bash
python -m pip install openai
```

Set `OPENAI_API_KEY` through your shell or approved secret manager. Do not hardcode it.

Official documentation: https://platform.openai.com/docs/guides/function-calling

Before running a real model request, check:
- The model is available to your account/project.
- API usage and pricing.
- Tool schema requirements.
- Rate limits and timeout behaviour.
- Data processing requirements.

## 7. Common errors

- The model returns text instead of a tool call.
- Tool arguments are not valid JSON.
- Required properties are missing.
- The model requests a tool not registered by the application.
- Tool execution times out.
- The tool result is too large.
- The model repeats a tool call.

A robust application handles all of these and enforces a maximum tool-call/step budget.

## Practice exercise

Create a local tool dispatcher with `add_numbers` and `search_docs`. Feed it fixed mock tool-call JSON first. Reject malformed JSON, invalid numeric ranges, and unknown tool names. Only then connect a model API using the official guide.

## Self-check

1. Does the model execute the function itself?
2. What does the tool schema tell the model?
3. Why must the function call be validated again in Python?
4. What should happen for an unknown tool name?
5. Why should the result be sent back to the model?

**Completion check:** I understand the model/tool round-trip and can implement its trusted execution boundary.
