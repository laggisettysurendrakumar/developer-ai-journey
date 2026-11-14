# Day 4 — Functions, scope and reusable code

## Goal for today

A function is a named, reusable block of code. I will use functions to break a large task into understandable steps. Agent applications are easier to test when validation, API calls, tool logic, and formatting are separate.

## 1. Define and call a function

```python
def greet(name):
    return f"Hello, {name}!"

message = greet("Surendra")
print(message)
```

`def` starts the definition. `name` is a parameter (an input). `return` sends a result back to the caller. Calling `greet("Surendra")` runs the function. A function does not run just because it is defined.

## 2. Parameters, return values and type hints

```python
def estimate_total(
    input_tokens: int,
    output_tokens: int,
    rate_per_1k: float
) -> float:
    total_tokens = input_tokens + output_tokens
    return total_tokens / 1_000 * rate_per_1k

estimate = estimate_total(800, 200, 0.01)
print(estimate)
```

The annotations describe the intended input and output types. Python does not automatically enforce all type hints at runtime; validate untrusted data separately.

**Why useful?** I can calculate usage, prepare API payloads, validate prompts, or format agent results in isolated functions.

## 3. Default and keyword arguments

```python
def build_prompt(question: str, language: str = "English") -> str:
    return f"Answer in {language}. Question: {question}"

print(build_prompt("What is an API?"))
print(build_prompt(question="What is RAG?", language="Telugu"))
```

A default is used when the caller does not supply an argument. Keyword arguments make calls easier to read. Avoid mutable defaults such as `[]` because the same object can be reused between calls.

## 4. Return multiple values

```python
def split_name(full_name: str) -> tuple[str, str]:
    parts = full_name.split(maxsplit=1)
    first = parts[0] if parts else ""
    last = parts[1] if len(parts) > 1 else ""
    return first, last

first_name, last_name = split_name("Ada Lovelace")
```

Python can return a tuple of values, which the caller can unpack.

## 5. Scope: where a variable is visible

```python
model_name = "demo-model"

def show_model():
    request_count = 1
    print(model_name, request_count)

show_model()
```

`request_count` is local to the function call. `model_name` is defined at module level. Prefer passing values as parameters rather than modifying global state; explicit inputs make code easier to test.

## 6. A function can become a tool

```python
def search_knowledge_base(query: str) -> dict:
    """Demo tool: return a fixed result for a supplied query."""
    if not query.strip():
        raise ValueError("Query cannot be empty")

    return {
        "query": query,
        "documents": ["Sample document about Python"]
    }

result = search_knowledge_base("Python functions")
print(result)
```

This is not a real search engine. It demonstrates a clear input, validation, and structured output. Later an agent can choose to call a search function, but the application controls what it is allowed to do.

## 7. Docstrings

A docstring is a short explanation immediately inside a function or class.

```python
def normalise_question(question: str) -> str:
    """Remove surrounding whitespace from a question."""
    return question.strip()
```

## 8. Main entry point

```python
def main():
    question = "Explain Python functions"
    print(normalise_question(question))

if __name__ == "__main__":
    main()
```

This pattern lets a file be run directly while still allowing its functions to be imported by tests or other modules. `__name__` is set to `"__main__"` when the file is run as the main program.

## Practice exercise

Write three functions: `validate_question()`, `count_words()`, and `format_summary()`. Call them from a `main()` function. Try an empty input and decide how the program should respond.

## Self-check

1. What is the difference between a parameter and an argument?
2. Why is `return` different from `print`?
3. Why are small functions easier to test?
4. Why should a tool have a clear input and output?

**Done for today:** I can break a task into small functions and explain their inputs and outputs.