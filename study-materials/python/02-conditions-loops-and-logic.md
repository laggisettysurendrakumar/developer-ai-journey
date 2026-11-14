# Day 2 — Conditions, loops and problem solving

## Goal for today

I will teach my program to make decisions and repeat work. An AI application needs normal program logic around the model: check input, limit retries, decide whether a tool is allowed, and stop when a task is complete.

## 1. Comparisons produce True or False

```python
score = 82

print(score >= 70)  # True
print(score == 100) # False
print(score != 0)   # True
```

Comparison operators include `==`, `!=`, `>`, `<`, `>=` and `<=`. One equals sign (`=`) assigns a value; two (`==`) compare values.

## 2. if, elif and else

A condition lets the program choose which block to run.

```python
question = "What is Python?"

if question.strip():
    print("A question was provided.")
else:
    print("Please enter a question.")
```

- `if` checks the first condition.
- `elif` checks another condition when earlier conditions are false.
- `else` handles the remaining case.
- Indentation (usually four spaces) defines which lines belong to a block.

**Use case:** Check that the user's prompt is not blank before sending it to a model.

## 3. Combine conditions

```python
is_authenticated = True
has_permission = False

if is_authenticated and has_permission:
    print("The example action is permitted.")
else:
    print("Do not run the action.")
```

`and` requires both conditions to be true; `or` requires at least one; `not` reverses a Boolean. This is only a learning example. Real authorisation must be checked by a trusted server-side system.

## 4. for loops: repeat for each item

```python
questions = [
    "What is Python?",
    "What is an API?",
    "What is an AI agent?"
]

for question in questions:
    print(f"Reviewing: {question}")
```

A `for` loop takes each item in a collection, one at a time.

```python
for attempt in range(1, 4):
    print(f"Attempt {attempt}")
```

`range(1, 4)` produces 1, 2 and 3. The ending number is excluded.

## 5. while loops: repeat while a condition stays true

```python
remaining_attempts = 3

while remaining_attempts > 0:
    print("Trying the operation...")
    remaining_attempts -= 1

print("No attempts remain.")
```

A `while` loop is useful when I do not know the exact number of repetitions in advance. Something must eventually change the condition; otherwise the program can loop forever.

**Agentic AI connection:** An agent may continue a tool-use cycle until it has completed a task. It must also have a maximum step count and timeout so it cannot run forever or spend without limit.

## 6. break and continue

```python
for status in ["queued", "running", "completed", "ignored"]:
    if status == "ignored":
        continue
    if status == "completed":
        print("Work is complete.")
        break
    print(f"Current status: {status}")
```

`continue` skips to the next iteration. `break` exits the loop.

## 7. Validate a prompt

```python
def validate_question(question: str) -> bool:
    if not question.strip():
        return False
    if len(question) > 2_000:
        return False
    return True

for item in ["Explain RAG", "   ", "Explain Python"]:
    print(validate_question(item))
```

The length limit is an example only. Real limits depend on the application's requirements and model context window.

## 8. Understand truthy and falsy values

Python treats some values as false in conditions: `False`, `None`, numeric zero, and empty strings/collections. Most other values are true.

```python
documents = []

if not documents:
    print("No documents were found.")
```

This is a common way to check whether a list or dictionary is empty.

## Practice exercise

Write a small command-line menu with options: 1) show a sample question, 2) show help, 3) exit. Use a loop to keep showing the menu until the user chooses exit. Handle invalid menu choices without crashing.

## Self-check

1. What is the difference between `=` and `==`?
2. When is a `for` loop a better choice than a `while` loop?
3. What can cause an infinite loop?
4. Why should an AI agent have a maximum number of steps?

**Done for today:** I can use conditions and loops to validate input and repeat work safely.