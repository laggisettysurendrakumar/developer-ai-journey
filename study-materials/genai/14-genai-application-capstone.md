# 14 — GenAI Application Architecture and Capstone

## What the capstone demonstrates

Build a small Text Processing API that uses a model to summarise or classify developer-provided text. The purpose is to connect model fundamentals, API integration, validation, evaluation, security, and operations in one project.

## 1. Reference architecture

```text
Client
  |
  v
ASP.NET Core or FastAPI API
  |---- authentication and input validation
  |---- task/domain rules
  |---- prompt template + version
  |---- model adapter
  |          |
  |          v
  |      Model provider
  |
  |---- output parsing and validation
  |---- evaluation/telemetry
  |
  v
Validated application response
```

Keep provider credentials on the server. Do not let the model decide user permissions or bypass application policy.

## 2. Project structure

A simple Python example:

```text
genai-text-api/
├── app/
│   ├── api.py
│   ├── models.py
│   ├── prompts.py
│   ├── services/
│   │   └── model_adapter.py
│   └── core/
│       └── settings.py
├── tests/
│   ├── test_models.py
│   └── test_service.py
├── requirements.txt
├── Dockerfile
└── README.md
```

Keep provider-specific code in `model_adapter.py`. The API and domain logic should depend on a small interface that can be tested with a fake model.

## 3. API behaviour

Support a small set of tasks:
- Summarise a text input.
- Classify a text input into known categories.
- Rewrite a text while preserving the original meaning.

Validate request size, task name, and required fields before calling the model. Return a clear error for unsupported task, authentication failure, timeout, rate limit, provider outage, or invalid output.

## 4. Validation and evaluation

- Define Pydantic or .NET DTO schemas.
- Validate all model output.
- Create a synthetic evaluation dataset.
- Test unsupported inputs and malformed outputs.
- Record prompt and model versions.
- Measure latency and token usage.
- Review factuality and usefulness with human scoring.

## 5. Security and operations

- Store credentials outside source control.
- Redact sensitive input from logs.
- Apply authentication and rate limits.
- Set timeouts and bounded retries.
- Restrict provider permissions.
- Record request IDs and safe diagnostic metadata.
- Set usage and cost limits.
- Deploy a container only after tests and security review.

## 6. Definition of done

- A developer can run the service using documented steps.
- A fake model enables unit tests without external calls.
- Real model integration is isolated behind an adapter.
- Inputs and model outputs are validated.
- Errors have predictable API behaviour.
- Prompt/model versions and usage metrics are recorded.
- Sensitive information is not logged by default.
- The evaluation dataset includes normal, ambiguous, and adversarial cases.
- A container build and non-production deployment are documented.

## Practice exercise

Build the API, run it locally with a fake model, then connect one approved provider using its current official SDK. Add tests for malformed output and timeouts. Write an architecture note describing what the model is allowed to do and what remains enforced by application code.

## Self-check

- Why use a model adapter?
- Why validate both input and generated output?
- Why use a fake model for unit tests?
- Which data should observability avoid capturing?
- What evidence demonstrates that a GenAI feature is ready for a controlled pilot?

## Official references

- OpenAI API documentation: https://platform.openai.com/docs/
- FastAPI: https://fastapi.tiangolo.com/
- Pydantic: https://docs.pydantic.dev/latest/
- Docker: https://docs.docker.com/
- Azure Well-Architected Framework: https://learn.microsoft.com/azure/well-architected/
