# 06 — Output Formatting, Structured JSON, and Schema Validation

## Why output contracts matter

Free-form text is useful for conversation, but application code needs predictable data. A structured output contract describes the fields and types expected from a model.

A prompt that says “return JSON” does not guarantee valid JSON. Use provider-supported structured output features when available and validate the result in application code.

## 1. Describe the contract

Example task:

```text
Classify the supplied support issue.
Return:
- category: one of network, application, database, unknown
- summary: short explanation
- next_check: a safe diagnostic step
Do not include fields outside this contract.
```

## 2. Validate with Pydantic

```python
from typing import Literal
from pydantic import BaseModel, ConfigDict, Field

class TriageResult(BaseModel):
    model_config = ConfigDict(extra="forbid")
    category: Literal["network", "application", "database", "unknown"]
    summary: str = Field(min_length=1, max_length=500)
    next_check: str = Field(min_length=1, max_length=300)

# Demonstration of validating a model-produced object:
candidate = {
    "category": "network",
    "summary": "The service cannot resolve the dependency hostname.",
    "next_check": "Check DNS resolution from the application environment."
}
validated = TriageResult.model_validate(candidate)
print(validated.model_dump())
```

**What it demonstrates:** validation of allowed categories, required fields, field lengths, and unknown-field rejection.

## 3. Validation is not truth checking

A structurally valid response can still be factually wrong, unsafe, incomplete, or unsupported by evidence.

Use additional checks:
- evidence/source validation,
- business-rule validation,
- range and enum constraints,
- permission checks,
- human review for high-impact output.

## 4. Handle parse and validation failures

When output fails validation:
1. Capture a safe diagnostic event.
2. Do not use the invalid object.
3. Decide whether one bounded correction attempt is appropriate.
4. Validate again.
5. Return a safe failure if validation still fails.

Avoid endless “please fix your JSON” retries.

## 5. Provider structured-output features

Some model APIs support constrained JSON or schema-based output modes. Their exact schemas and limitations vary by model and API. Check current official documentation. Even when the provider guarantees structural conformance, the application must still validate business rules and permissions.

## Practice exercise

Design a structured output for a deployment-risk review with fields for risk level, evidence, missing information, and next safe check. Add enum and length constraints. Test missing fields, invalid enum values, extra fields, and a valid but factually unsupported claim.

## Self-check

1. Why is “return JSON” insufficient for production?
2. What does schema validation verify?
3. What does schema validation not verify?
4. Why use bounded correction attempts?
5. What further checks are needed before acting on model output?

## Further reading

- [OpenAI Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [Pydantic documentation](https://docs.pydantic.dev/)
