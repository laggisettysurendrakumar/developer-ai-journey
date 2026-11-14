# Day 3 — Strings and data structures

## Goal for today

AI applications work with text and structured information. I will learn how to prepare text and choose the right Python collection for storing data.

## 1. Strings: working with text

```python
question = "  How do Python agents use tools?  "

clean_question = question.strip()
lower_question = clean_question.lower()

print(clean_question)
print(lower_question)
print(len(clean_question))
```

A string (`str`) is text. Useful operations:
- `strip()` removes whitespace at the beginning and end.
- `lower()` and `upper()` change letter case.
- `replace(old, new)` replaces matching text.
- `split()` splits text into a list.
- `len(value)` counts characters.

Strings are immutable: these operations create new strings rather than changing the original in place.

## 2. Indexing and slicing

```python
text = "AGENT"

print(text[0])    # A
print(text[-1])   # T
print(text[0:3])  # AGE
```

Indexes start at zero. A slice includes its starting position but excludes its ending position. Accessing an index that does not exist raises `IndexError`.

## 3. Lists: ordered and changeable

```python
messages = ["system", "user"]
messages.append("assistant")
messages[1] = "user question"

print(messages)
```

A list keeps items in order and can be changed. It is useful for conversation messages, search results, and a sequence of tasks. Common methods include `append()`, `extend()`, `remove()`, `pop()` and `sort()`.

## 4. Tuples: fixed ordered groups

```python
coordinates = (17.45, 78.38)
latitude, longitude = coordinates

print(latitude, longitude)
```

A tuple is ordered but cannot be changed after creation. It can represent a fixed pair or group of related values.

## 5. Sets: unique values

```python
requested_tools = {"search", "calculator", "search"}
print(requested_tools)
```

A set keeps unique values and supports quick membership checks. Do not rely on its display order.

**Use case:** Remove duplicate document IDs or check whether a requested tool belongs to an allowed set.

## 6. Dictionaries: key/value information

A dictionary stores values under labels (keys). It is extremely useful for API payloads and chat messages.

```python
message = {
    "role": "user",
    "content": "Explain embeddings",
    "metadata": {"source": "web-app"}
}

print(message["role"])
print(message.get("content"))
print(message.get("missing_key", "not provided"))
```

Square-bracket access to a missing key raises `KeyError`. `get()` can return a default instead.

## 7. Loop through a dictionary

```python
settings = {"model": "demo-model", "temperature": 0.2}

for key, value in settings.items():
    print(f"{key}: {value}")
```

Use `keys()`, `values()` and `items()` to inspect dictionary content.

## 8. List and dictionary comprehensions

A comprehension is a compact way to build a new collection from an existing one.

```python
questions = ["  Explain Python ", "What is RAG?  "]
cleaned = [question.strip() for question in questions]

word_counts = {
    question: len(question.split())
    for question in cleaned
}

print(cleaned)
print(word_counts)
```

This is useful for cleaning document lists or extracting simple metadata. Keep it readable; a normal loop is better when the logic becomes complicated.

## 9. Choose the right structure

| Need | Useful type | Example |
|---|---|---|
| Ordered, changeable sequence | List | Conversation turns |
| Fixed ordered group | Tuple | Coordinate pair |
| Unique values | Set | Allowed tool names |
| Named values | Dictionary | API message payload |
| Text | String | User prompt |

## 10. A small conversation history

```python
messages = [
    {"role": "user", "content": "What is RAG?"},
    {
        "role": "assistant",
        "content": "RAG retrieves relevant information before generating an answer."
    }
]

for item in messages:
    print(f"{item['role']}: {item['content']}")
```

This is an in-memory example. A real chat system needs persistence, privacy controls, retention limits, and a defined message schema.

## Practice exercise

Create a dictionary for a support ticket containing `ticket_id`, `question`, `priority` and `tags`. Use a list for tags and a set to remove duplicates. Print a readable summary.

## Self-check

1. Why does a list suit conversation turns?
2. What happens when I access a missing dictionary key with square brackets?
3. When is a set useful?
4. Why do AI APIs often use dictionaries and lists?

**Done for today:** I can represent a simple conversation and configuration using Python collections.