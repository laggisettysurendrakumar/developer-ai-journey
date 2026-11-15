# 06 — Output Contracts, JSON, and Structured Prompts

## Why output format matters

Free-form text is easy for a person to read but difficult for an application to consume reliably. An output contract defines the fields, types, and constraints the application expects.

## 1. Describe the exact shape

Example contract:

```json
{
  "category": "database",
  "severity": "high",
  "summary": "Database connection failures are affecting API requests.",
  "next_checks": [
    "Review database dependency health",
    "Check connection pool saturation"
  ],
  "unknowns": [
    "Affected environment was not provided"
  ]
}
```

Tell the model what each field means, allowed enum values, whether a field can be empty, and what to do when information is missing.

## 2. Validate output in code

```python
from typing import Literal
from pydantic import BaseModel, Field

class TriageResult(BaseModel):
    category: Literal["network", "application", "database", "unknown"]
    severity: Literal["low", "medium", "high", "critical"]
    summary: str = Field(min_length=1, max_length=500)
    next_checks: list[str] = Field(max_length=5)
    unknowns: list[str] = Field(max_length=10)

sample = {
    "category": "database",
    "severity": "high",
    "summary": "Database connections are failing.",
    "next_checks": ["Check dependency health"],
    "unknowns": ["Environment was not supplied"]
}

validated = TriageResult.model_validate(sample)
print(validated.model_dump())
```

**What it does:** Validates types, enum values, and field limits before application code uses the result.

**Important:** This sample validates a Python dictionary, not a real model response. In an application, parse the actual response and handle validation errors. Use provider-supported structured output modes where appropriate, but still validate at the application boundary.

## 3. JSON-only instructions

If using plain text generation, be explicit:

```text
Return one JSON object only.
Do not add Markdown fences or commentary.
Use exactly these keys: category, severity, summary, next_checks, unknowns.
Use [] when no list items are known.
Use "unknown" when category cannot be determined.
Do not invent facts.
```

JSON-only wording alone does not guarantee valid JSON. A structured output API/schema mode is preferable when supported.

## 4. Handle invalid or incomplete output

The application should:
1. parse response,
2. validate schema,
3. check semantic business rules,
4. reject or safely handle invalid results,
5. record the failure without logging sensitive content,
6. optionally retry with a bounded correction prompt if it is safe and useful.

Do not silently coerce invalid values into dangerous defaults.

## Practice exercise

Define a Pydantic model for extracting an API error summary with fields `http_status`, `endpoint`, `probable_area`, `evidence`, and `unknowns`. Add constraints and test missing fields, invalid status, and oversized strings.

## Self-check

1. What is an output contract?
2. Why validate a model-generated JSON response?
3. What is the difference between syntax and semantic validation?
4. Why is “return valid JSON” not a guarantee?
5. What should happen when the result fails validation?

## Further reading

- [OpenAI structured outputs guide](https://platform.openai.com/docs/guides/structured-outputs)
- [Pydantic documentation](https://docs.pydantic.dev/)
