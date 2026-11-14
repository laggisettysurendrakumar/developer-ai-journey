# Lesson 06 — Agent State, Conversation History, and Memory

## Learning goals

Understand what information an agent needs during a single run, what should be retained across runs, and why memory needs privacy and data-lifecycle rules.

## 1. State and memory are not the same

**Run state** is temporary information used while one task is executing:
- Current goal.
- Current step.
- Tool results.
- Remaining budget.
- Current approval status.

**Conversation history** is the sequence of user and assistant messages needed to understand the current conversation.

**Long-term memory** is information deliberately retained for future requests, such as a user-approved preference or a durable project fact.

Do not save everything automatically. Memory needs a clear purpose, permission, retention, and deletion strategy.

## 2. A simple state object

```python
from dataclasses import dataclass, field

@dataclass
class AgentState:
    user_id: str
    goal: str
    step_count: int = 0
    messages: list[dict[str, str]] = field(default_factory=list)
    tool_results: list[dict[str, str]] = field(default_factory=list)

state = AgentState(user_id="demo-user", goal="Find the Python setup guide")
state.messages.append({"role": "user", "content": state.goal})
state.step_count += 1

print(state.step_count)
```

`default_factory=list` creates a new list for each instance. It avoids sharing one mutable list across different objects.

## 3. Why user ID matters

Suppose two users use the same assistant. If the application loads conversation history without checking ownership, one user may receive another user's messages.

A secure persistence layer should:
- Authenticate the user.
- Associate each conversation with an owner/tenant.
- Check ownership on reads and writes.
- Limit retained information.
- Redact sensitive information where appropriate.
- Support deletion and retention policies.

A model must never decide which user's database records it is allowed to read.

## 4. Conversation history can become too large

Every retained message may consume context and cost when sent to a model. Strategies:
- Keep a bounded recent history.
- Summarise older turns with care.
- Store important references separately.
- Retrieve only relevant information.
- Keep source IDs and provenance for facts.

A summary can omit or distort details. Retain the original record where policy requires it and make summaries traceable.

## 5. Demonstration: bounded recent history

```python
def keep_recent_messages(
    messages: list[dict[str, str]],
    max_messages: int = 6
) -> list[dict[str, str]]:
    if max_messages < 1:
        raise ValueError("max_messages must be positive")
    return messages[-max_messages:]

history = [
    {"role": "user", "content": f"Message {number}"}
    for number in range(10)
]

print(keep_recent_messages(history, max_messages=3))
```

This example limits the number of messages, not the token count. Production systems should consider token length and preserve required system/developer instructions separately.

## 6. Long-term memory design

Before saving a memory, ask:
1. What task will this help with later?
2. Is it actually needed?
3. Did the user agree to retain it?
4. Is it sensitive?
5. How long should it be retained?
6. How can it be corrected or deleted?
7. Can it be retrieved only for the right user?

A safer pattern is to store explicit structured facts with source, timestamp, owner, and expiry where appropriate.

## 7. State transitions

A run can use explicit states such as:
- `created`
- `running`
- `waiting_for_approval`
- `completed`
- `failed`
- `cancelled`

Explicit states help recover after a worker restarts and prevent actions from being repeated accidentally.

## Practice exercise

Extend `AgentState` to record a run ID, status, and maximum steps. Write a function that refuses to increment the step count after the budget is exhausted. Explain which fields are temporary and which could be persisted.

## Self-check

1. What belongs in run state?
2. What is conversation history?
3. Why is long-term memory different from state?
4. Why should conversation data be tenant-scoped?
5. Why should older history be summarised or retrieved selectively?

**Completion check:** I can design a state object and explain how conversation data is owned, retained, and deleted.
