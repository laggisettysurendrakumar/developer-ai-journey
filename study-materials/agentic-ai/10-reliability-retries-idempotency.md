# Lesson 10 — Reliability: Timeouts, Retries, Idempotency, and Recovery

## Learning goals

Learn how to make agents and their tools handle slow services, temporary failures, duplicate requests, and interrupted runs.

## 1. External tools fail

A model API, vector database, ticket API, or internal service may:
- Respond slowly.
- Return a timeout.
- Return a rate-limit response.
- Return a server error.
- Return invalid data.
- Succeed at the service but lose the response on the network.

A robust agent expects failure and has a safe recovery path.

## 2. Timeouts

A timeout limits how long the application waits. Without one, a request may hang and consume resources.

```python
import asyncio

async def slow_tool() -> str:
    await asyncio.sleep(5)
    return "done"

async def main():
    try:
        result = await asyncio.wait_for(slow_tool(), timeout=1)
        print(result)
    except TimeoutError:
        print("Tool call timed out")

asyncio.run(main())
```

A production HTTP client should also configure connection and read timeouts.

## 3. Retries

Retry only when a failure may be temporary. Do not retry invalid arguments or permission failures.

Use:
- A maximum retry count.
- Exponential backoff.
- Random jitter to avoid many clients retrying simultaneously.
- Respect for `Retry-After` and provider guidance.
- An overall time/cost budget.

Illustrative delay calculation:

```python
def retry_delay(attempt: int, base_seconds: float = 0.5) -> float:
    if attempt < 0:
        raise ValueError("attempt cannot be negative")
    return min(base_seconds * (2 ** attempt), 8.0)

for attempt in range(4):
    print(retry_delay(attempt))
```

This is a simple capped exponential delay without jitter. Production code should add jitter and use the retry guidance of the service being called.

## 4. Idempotency

Idempotency means repeating the same operation does not create duplicate effects.

Example: if an agent requests `create_ticket` twice because the first response was lost, the system should avoid creating two identical tickets when the same idempotency key is used.

The API or service needs to store and check idempotency keys. A local Python variable is not enough across multiple workers.

## 5. Checkpointing and recovery

For long-running agents, store progress at safe boundaries:
- Run ID.
- Current state.
- Completed tool calls.
- Tool request IDs.
- Approval status.
- Last safe checkpoint.

On restart, verify whether a previous external action succeeded before repeating it. Do not assume a timeout means the remote system did nothing.

## 6. Circuit breaker concept

A circuit breaker temporarily stops calls to a failing dependency so the application does not keep overwhelming it. It generally has closed, open, and half-open states.

Do not implement a complex circuit breaker from scratch for production unless you understand concurrent state, timing, and monitoring. Prefer a well-maintained library or platform feature.

## 7. Error categories

| Failure | Typical response |
|---|---|
| Invalid arguments | Return validation error; do not retry |
| Unauthorised | Deny and record safe audit event |
| Not found | Explain missing resource |
| Timeout | Retry only if safe and within budget |
| Rate limited | Respect backoff instructions |
| Temporary 5xx | Bounded retry if operation is safe |
| Unknown error | Stop safely, log a correlation ID, investigate |

## Practice exercise

Create a fake tool that fails twice with a temporary error, then succeeds. Add a maximum of three attempts and record attempt count. Then design how an email-send tool would avoid duplicate sends after a network timeout.

## Self-check

1. Why should every external call have a timeout?
2. Which errors should not be retried?
3. What is exponential backoff?
4. Why is idempotency important for write tools?
5. Why can a timeout leave the action's actual outcome uncertain?

**Completion check:** I can describe how an agent recovers safely from temporary failures and duplicate requests.
