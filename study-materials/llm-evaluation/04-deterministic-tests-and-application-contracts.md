# 04 — Deterministic Tests and Application Contracts

## Do not use a model to test everything

Much of a GenAI application is ordinary software. Request validation, permission checks, API contracts, tool allowlists, retry budgets, output parsing, and database operations should be tested deterministically.

If a requirement can be expressed as a normal code assertion, prefer a normal test.

## 1. Examples of deterministic checks

- Blank input is rejected.
- Unsupported task names return a validation error.
- Only allowlisted tools can be called.
- A user's identity is passed to the retrieval permission filter.
- The agent stops at its configured maximum number of steps.
- Invalid JSON is rejected.
- A failed provider call maps to a safe application error.
- A request timeout is enforced.

## 2. Use a fake model

A fake model makes unit tests quick, stable, and independent of external API availability or cost.

```python
class FakeModel:
    def __init__(self, response: str):
        self.response = response
        self.calls = 0

    def generate(self, prompt: str) -> str:
        self.calls += 1
        return self.response


def test_summary_uses_model():
    model = FakeModel("Database calls became slower.")
    result = model.generate("Summarise the incident")
    assert result == "Database calls became slower."
    assert model.calls == 1
```

This tests the fake and application wiring, not the quality of a real model. Keep those test purposes separate.

## 3. Contract tests

A contract test checks assumptions about the boundaries between components. For a provider adapter, use sanitised fixtures to test parsing of valid response, missing content, refusal/filtered output, usage metadata, and known error types.

For a retrieval service, check that the expected fields and document identifiers are returned. For tools, check input and output schemas.

## 4. Security tests are deterministic gates

Test that:
- A user cannot retrieve a restricted tenant's documents.
- A model-generated tool request cannot bypass authorisation.
- A tool name outside the allowlist is rejected.
- A malformed identifier cannot target another resource.
- A forbidden action requires human approval or is rejected.

Never rely solely on a model judge to validate access control.

## 5. Test boundaries and failure paths

Cover empty content, oversized input, malformed response, dependency timeout, provider rate limit, duplicate event, and interrupted workflow. Verify that retries are bounded and failures do not leave partially updated state.

## Practice exercise

Build a small test suite for a model-backed ticket summariser. Use a fake model. Test input validation, output schema, unknown categories, timeout handling, and access control. Demonstrate that tests pass with network access disabled.

## Self-check

- Which application behaviours should be tested without a model?
- Why are fake models useful?
- What is a contract test?
- Why are permission checks hard safety gates?
- How do tests for provider fixtures differ from model quality evaluation?

## Official references

- pytest: https://docs.pytest.org/
- Pydantic validation: https://docs.pydantic.dev/latest/concepts/models/
