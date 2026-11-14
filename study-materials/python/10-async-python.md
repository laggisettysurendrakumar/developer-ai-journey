# Day 10 — Async Python and concurrency basics

## Goal for today

AI applications spend time waiting for network responses: model calls, search, databases, and external tools. Async programming lets one program make progress on other work while an operation waits. I will understand `async`, `await`, and when they help.

## 1. Waiting versus doing other work

Imagine ordering food. A synchronous approach waits at the counter until the order is ready. An asynchronous approach places the order and does another useful task while waiting.

Network I/O is often a good fit for async code. CPU-heavy calculations do not automatically become faster just because I add `async`.

## 2. Write a coroutine

```python
import asyncio

async def get_message():
    await asyncio.sleep(1)
    return "Response received"

async def main():
    result = await get_message()
    print(result)

asyncio.run(main())
```

- `async def` defines a coroutine function.
- Calling it creates a coroutine object.
- `await` pauses the current coroutine until the awaited operation completes.
- `asyncio.run()` starts an event loop for a top-level async program.

The sleep is a demonstration of waiting, not a real network call.

## 3. Run independent waits concurrently

```python
import asyncio

async def simulated_tool(name: str, seconds: float) -> str:
    await asyncio.sleep(seconds)
    return f"{name} finished"

async def main():
    results = await asyncio.gather(
        simulated_tool("search", 2),
        simulated_tool("metadata", 1),
    )
    print(results)

asyncio.run(main())
```

Both waits can overlap. This can reduce waiting time when operations are independent and the libraries support async I/O.

## 4. Async HTTP with httpx

Install:

```bash
python -m pip install httpx
```

```python
import asyncio
import httpx

async def fetch_title(client: httpx.AsyncClient, item_id: int) -> str:
    response = await client.get(
        f"https://jsonplaceholder.typicode.com/todos/{item_id}"
    )
    response.raise_for_status()
    data = response.json()
    return data["title"]

async def main():
    timeout = httpx.Timeout(10.0)
    async with httpx.AsyncClient(timeout=timeout) as client:
        titles = await asyncio.gather(
            fetch_title(client, 1),
            fetch_title(client, 2),
        )
        print(titles)

asyncio.run(main())
```

A shared async client manages connections and is closed by the context manager. Use approved endpoints for work.

## 5. Add a timeout

```python
import asyncio

async def slow_operation():
    await asyncio.sleep(5)

async def main():
    try:
        await asyncio.wait_for(slow_operation(), timeout=1)
    except TimeoutError:
        print("The operation took too long.")

asyncio.run(main())
```

Timeouts prevent waiting indefinitely. Real clients should also have connection and read timeouts. Cancellation and cleanup matter when an async operation is stopped.

## 6. When not to use async

- A tiny script with one quick operation may be simpler synchronously.
- CPU-heavy work needs other approaches, such as multiprocessing or specialised numerical libraries.
- Do not call blocking libraries directly inside async functions if they block the event loop.
- Do not start unlimited concurrent calls. Use limits, queues, rate control, and backpressure.

## 7. Why async matters for agents

An agent may need to search documents, call a business API, and fetch metadata. Independent operations can sometimes overlap. Operations that depend on earlier results must run in order. Concurrency must respect access controls and service rate limits.

## Practice exercise

Write three simulated async tool functions with different wait durations. Run them with `asyncio.gather()`. Add a timeout and report which operation timed out. Keep the number of tasks small.

## Self-check

1. What does `await` do?
2. Why can async help API-heavy applications?
3. Does async automatically speed up CPU-heavy calculations?
4. Why must concurrent tool calls be limited?

**Done for today:** I can explain how Python handles multiple waiting operations and when that is useful.