# Day 7 — Object-oriented Python and type hints

## Goal for today

I will learn how classes group data and behaviour, and how type hints make code easier to understand. I do not need to turn every small function into a class. A class is useful when it represents an object with related state and behaviour.

## 1. What is a class?

A class is a blueprint for creating objects. An object is one instance of that blueprint.

```python
class UserQuestion:
    def __init__(self, text: str):
        self.text = text

    def cleaned_text(self) -> str:
        return self.text.strip()

question = UserQuestion("  Explain an AI agent  ")
print(question.cleaned_text())
```

- `class` defines the blueprint.
- `__init__` runs when an object is created.
- `self` refers to the current object.
- `self.text` stores data on that object.
- A method is a function defined inside a class.

## 2. Why use a class?

A class is useful when several functions need the same state. For example, a conversation object can store messages and provide methods to add and count them.

```python
class Conversation:
    def __init__(self):
        self.messages: list[dict[str, str]] = []

    def add_message(self, role: str, content: str) -> None:
        self.messages.append({"role": role, "content": content})

    def count(self) -> int:
        return len(self.messages)

chat = Conversation()
chat.add_message("user", "What is RAG?")
print(chat.count())
```

This is in-memory only. A real application needs persistence, privacy, and retention rules.

## 3. Dataclasses for simple data containers

```python
from dataclasses import dataclass

@dataclass
class ToolResult:
    tool_name: str
    success: bool
    output: str

result = ToolResult("calculator", True, "42")
print(result.tool_name)
```

A dataclass creates useful methods such as an initializer and readable representation. It is handy when I mostly need to store structured values.

## 4. Type hints

Type hints describe the kind of values a function expects and returns.

```python
def word_count(text: str) -> int:
    return len(text.split())

count: int = word_count("Python supports AI development")
print(count)
```

Type hints help editors, static-analysis tools, and teammates understand intended data shapes. Python does not enforce all annotations automatically at runtime.

## 5. Optional values

```python
def find_user(user_id: int) -> str | None:
    if user_id == 1:
        return "Ada"
    return None

result = find_user(2)
if result is not None:
    print(result.upper())
```

`str | None` means the function may return text or no value. Check for `None` before using the result as text.

## 6. Composition: objects can use other objects

```python
class PromptBuilder:
    def build(self, question: str) -> str:
        return f"Answer clearly: {question}"

class AssistantService:
    def __init__(self, prompt_builder: PromptBuilder):
        self.prompt_builder = prompt_builder

    def prepare(self, question: str) -> str:
        return self.prompt_builder.build(question)
```

The assistant service receives a prompt builder rather than creating one internally. This simple form of dependency injection makes components easier to replace and test.

## 7. Keep responsibilities small

A class called `AgentApplication` should not necessarily read files, authenticate users, call a model, execute tools, write databases, and send email itself. Separate components are easier to understand and test.

A possible design later:
- `Settings`: configuration.
- `LLMClient`: model API communication.
- `ToolRegistry`: approved tools.
- `AgentRunner`: bounded execution.
- `ConversationStore`: message persistence.

## Practice exercise

Create a `Document` dataclass with `title`, `text`, and `source` fields. Add a function that returns a cleaned title and word count. Create two sample objects.

## Self-check

1. What is the difference between a class and an object?
2. What does `self` refer to?
3. When is a dataclass useful?
4. Why do type hints help when several components exchange data?

**Done for today:** I can model simple application data and describe function inputs and outputs with type hints.