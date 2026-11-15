# 11 — Prompt Templates, Versioning, and Reuse

## Treat prompts as maintained application assets

A production prompt is part of the software behaviour. It should be reviewed, versioned, tested, and released like code.

## 1. Use named templates

Instead of duplicating a long prompt in several files, define a named template such as:
- `support_triage_v1`
- `pr_review_v2`
- `sql_explanation_v1`

Record purpose, expected inputs, output contract, owner, model compatibility, and tests.

## 2. Keep variable inputs explicit

Example:

```python
from string import Template

SUPPORT_TEMPLATE = Template("""
Task: Summarise the incident evidence.

Environment: $environment
Service: $service

Evidence:
<evidence>
$evidence
</evidence>

Return:
- Confirmed facts
- Unknowns
- Safe next checks
""")

prompt = SUPPORT_TEMPLATE.substitute(
    environment="test",
    service="catalog-api",
    evidence="Synthetic example: API requests return HTTP 503."
)
print(prompt)
```

This example demonstrates template substitution. In production, validate placeholder values and avoid placing sensitive data into prompts unnecessarily.

## 3. Avoid accidental template injection

Do not let untrusted user text become template syntax or trusted instructions. Use a template engine safely, validate variables, and keep template definitions controlled by application code.

## 4. Version and review

Track:
- prompt ID and version,
- change description,
- owner,
- model/provider and relevant settings,
- evaluation dataset version,
- evaluation results,
- deployment date,
- rollback version.

A prompt change can alter application behaviour even when no source code changes.

## 5. Reuse without over-generalising

A single universal prompt often becomes full of unrelated conditional rules. Prefer a small set of focused templates for distinct tasks. Share common guidance only when it truly applies.

## 6. Rollback

Keep the previous known-good version available. If quality or safety regresses, route traffic back to the previous version while investigating. Make sure the serving application can identify which prompt version produced an output.

## Practice exercise

Create a prompt catalogue with three templates: code explanation, support triage, and documentation generation. Add metadata and a small test set for each. Simulate a prompt regression and document the rollback steps.

## Self-check

1. Why version prompts?
2. Which metadata should accompany a prompt?
3. Why are focused templates often better than one universal template?
4. What is template injection?
5. How should a prompt regression be rolled back?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
