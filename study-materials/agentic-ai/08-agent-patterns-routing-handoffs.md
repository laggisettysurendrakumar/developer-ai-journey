# Lesson 08 — Agent Patterns: Routing, Handoffs, and Multi-Agent Systems

## Learning goals

Understand the main ways to organise agent work, why multiple agents are not automatically better, and how to keep specialist components controlled.

## 1. Start with one agent

A single agent with a few well-designed tools is usually the best starting point. It is easier to understand, test, trace, secure, and estimate cost.

Add another agent only when there is a real benefit such as separate expertise, separate instructions, independent context, or a clear ownership boundary.

## 2. Common patterns

### Router

A router classifies a request and sends it to the appropriate handler.

Example: route a deployment question to a DevOps helper, and a Python question to a Python helper.

### Specialist handoff

A coordinator sends a task to a specialist. The specialist returns a result to the coordinator or user.

### Manager pattern

A manager agent keeps control of the user task and calls specialist agents as tools. The manager remains responsible for combining results.

### Sequential pipeline

Several components run in a predefined order. This is often a workflow rather than a fully autonomous multi-agent system.

### Parallel specialists

Independent read-only tasks run concurrently, and the results are combined. Concurrency must be limited.

## 3. Simple deterministic router

This is a classification demo, not an LLM router.

```python
def route_question(question: str) -> str:
    value = question.lower()

    if "python" in value:
        return "python_handler"
    if "deployment" in value or "docker" in value:
        return "devops_handler"
    return "general_handler"

print(route_question("How do Docker deployments work?"))
```

**Why useful?** The developer can understand routing before introducing model uncertainty.

## 4. Handoff diagram

```text
User task
   |
   v
Coordinator
   |
   +----> Python specialist
   |
   +----> DevOps specialist
   |
   +----> Documentation specialist
   |
   v
Coordinator validates and combines results
   |
   v
Final answer with sources and limitations
```

Specialists should have distinct responsibilities and only the tools they need.

## 5. Parallel work

Parallel calls can reduce waiting if tasks are independent. They also consume more resources and may hit rate limits.

```python
import asyncio

async def specialist(name: str) -> str:
    await asyncio.sleep(0.2)  # Simulated I/O wait
    return f"{name} completed a read-only check"

async def main():
    results = await asyncio.gather(
        specialist("Python"),
        specialist("DevOps"),
    )
    print(results)

asyncio.run(main())
```

The example simulates waiting only. Real calls should use bounded concurrency, timeouts, cancellation, and clear partial-failure handling.

## 6. Multi-agent risks

- One agent's unsupported result can mislead another.
- Agents can repeat work and increase cost.
- Parallel calls can overload services.
- Tool permissions can accidentally become broader.
- Tracing and debugging become harder.
- Agents may disagree or fail to terminate.

Do not treat one agent's output as trusted policy for another agent. Validate cross-agent messages and tool results.

## 7. When to introduce multiple agents

Good reasons:
- Different specialists need different tools and permissions.
- Independent subtasks can be evaluated separately.
- A coordinator needs to combine independently produced evidence.

Weak reasons:
- It looks more advanced.
- One prompt became too long.
- The developer has not yet tested a single agent.

## Practice exercise

Create a deterministic support router with three handlers:
1. Python knowledge.
2. Docker/deployment knowledge.
3. General documentation search.

Record which handler receives each example. Then write down a clear reason why a second agent might be justified and what additional risks it creates.

## Self-check

1. What problem does a router solve?
2. How is a manager pattern different from a simple sequential workflow?
3. When is parallel work useful?
4. Why can more agents make an application less reliable?
5. Which permissions should a specialist receive?

**Completion check:** I can justify a multi-agent design and describe its trade-offs.
