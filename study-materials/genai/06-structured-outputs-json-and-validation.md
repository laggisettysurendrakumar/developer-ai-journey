# 06 — Structured Outputs, JSON and Validation

## Why structured output matters

A person can understand free-form text, but application code often needs predictable fields. For example, an incident API may need a title, severity, summary, and recommended next steps.

A model may generate invalid JSON, omit required fields, or return values with the wrong types. Structured output features can help constrain the response, but application-side validation is still necessary.

## 1. Define the expected schema

```python
from pydantic import BaseModel, Field

class IncidentSummary(BaseModel):
    title: str = Field(min_length=1, max_length=120)
    severity: str
    key_points: list[str]
    requires_human_review: bool
```

A schema makes the expected shape explicit. Add enums, length limits, and business constraints where appropriate.

## 2. Validate an example response

```python
raw_response = {
    "title": "API latency increased",
    "severity": "medium",
    "key_points": ["Database calls slowed", "Errors remained stable"],
    "requires_human_review": True
}

summary = IncidentSummary.model_validate(raw_response)
print(summary.model_dump())
```

This example uses a fixed dictionary, not a live model response. It demonstrates parsing and validation after the model adapter returns structured data.

## 3. Separate shape validation from truth validation

Pydantic can check required fields, data types, lengths, and custom rules. It cannot determine by itself whether the model's claims are factually supported by source evidence.

For factual applications, validate references, check required source IDs, compare values against trusted data, or route uncertain output for human review.

## 4. Handle invalid output

```python
from pydantic import ValidationError

def parse_summary(payload: dict) -> IncidentSummary:
    try:
        return IncidentSummary.model_validate(payload)
    except ValidationError as exc:
        # Log a safe error summary; avoid logging sensitive source content.
        raise ValueError("Model response did not match the required schema") from exc
```

Do not blindly ask the model to retry forever. A limited repair attempt may be appropriate for low-risk formatting errors, but always enforce retry and cost budgets.

## 5. Native structured-output features

Some model APIs support response formats or schema-constrained outputs. The exact syntax and supported schema subset varies by provider and model. Follow the selected provider's current documentation and still validate the returned payload in your application.

## Practice exercise

Build a classification endpoint that expects a category, confidence explanation, and list of evidence references. Validate the structure, reject unknown categories, and add a test for missing fields and malformed values.

## Self-check

- Why does an application need structured output?
- What does Pydantic validate?
- Why does a valid schema not prove factual correctness?
- How should malformed output be handled?
- Why should structured-output capabilities be checked per model/API?

## Official references

- Pydantic models: https://docs.pydantic.dev/latest/concepts/models/
- OpenAI structured outputs: https://platform.openai.com/docs/guides/structured-outputs
