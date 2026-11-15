# 10 — Reliability, Rate Limits and Error Handling

## A model API is an external dependency

An API request can fail because of a network issue, authentication problem, service outage, rate limit, context limit, invalid request, timeout, or safety filter. Production code must distinguish these cases.

## 1. Common failure categories

| Failure | Meaning | Typical response |
|---|---|---|
| Authentication error | Credential missing or invalid | Correct identity/secret; do not retry blindly |
| Permission error | Identity lacks access | Fix authorisation and scope |
| Rate limit | Request/token throughput limit reached | Respect retry guidance and reduce concurrency |
| Invalid request | Schema, parameter, or input is rejected | Correct the request |
| Timeout | Response was not received within limit | Apply finite timeout and decide safe retry |
| Service error | Provider temporarily failed | Bounded retry with backoff where safe |
| Content filter | Request or output triggered a policy control | Handle as a policy outcome, not a transient failure |

Exact status codes and exception types depend on the provider SDK.

## 2. Exponential backoff

When a transient failure can safely be retried, increase the delay between attempts. Add jitter so many clients do not all retry at the same moment.

```python
import random
import time

def wait_before_retry(attempt: int, base_seconds: float = 0.5) -> None:
    delay = min(8.0, base_seconds * (2 ** attempt))
    delay += random.uniform(0, 0.25)
    time.sleep(delay)
```

This helper only calculates a wait. A production retry policy must also define maximum attempts, total time budget, retryable errors, and whether the operation is safe to repeat.

## 3. Rate limits and concurrency

Providers may limit requests per minute, tokens per minute, or other workload dimensions. Use bounded concurrency and queueing to smooth bursts. Avoid launching unlimited parallel calls.

## 4. Idempotency and duplicate work

If a user retries a request after a timeout, the first request may still have completed. For tasks with side effects or cost, use request identifiers and idempotency controls where the API or application design supports them.

## 5. Fallbacks

A fallback may use a simpler model, cached answer, or graceful error message. Define when a fallback is safe; do not silently present lower-confidence output as equivalent to a fully successful result.

## Practice exercise

Use a fake model client to simulate timeout, rate-limit, invalid-request, and provider-failure responses. Implement bounded retry for only the transient cases and verify that invalid credentials do not trigger repeated calls.

## Self-check

- Why should authentication failures not be retried repeatedly?
- What is exponential backoff?
- Why add jitter?
- How can a client timeout create duplicate work?
- Why must fallback behaviour be explicit?

## Official references

- OpenAI rate limits: https://platform.openai.com/docs/guides/rate-limits
- OpenAI error codes: https://platform.openai.com/docs/guides/error-codes
