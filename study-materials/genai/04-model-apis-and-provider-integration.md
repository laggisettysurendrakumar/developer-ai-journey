# 04 — Model APIs and Provider Integration

## What an API call does

A model API lets an application send content to a model service and receive a response. The API usually has an endpoint, authentication method, model or deployment identifier, request schema, response schema, and usage/error metadata.

Keep model calls behind an adapter so the rest of the application depends on your own interface rather than a provider-specific response object.

## 1. API request lifecycle

```text
Application input
  -> validate request
  -> authenticate to provider
  -> send model + instructions + input
  -> wait for response or timeout
  -> parse response
  -> validate content
  -> return application result
```

Handle missing credentials, permission failures, rate limits, network errors, provider outages, malformed output, and content filtering as distinct conditions where the SDK exposes them.

## 2. Protect credentials

Store API keys in environment variables for local development and in a secret manager or workload identity setup for deployed systems. Never commit credentials or expose server API keys in browser code, mobile code, public notebooks, screenshots, or logs.

## 3. Provider-neutral adapter

The following shows an application boundary without pretending that every provider has the same SDK:

```python
from typing import Protocol

class TextModel(Protocol):
    def generate(self, prompt: str) -> str:
        """Return generated text or raise a typed provider error."""

def summarise(model: TextModel, document: str) -> str:
    if not document.strip():
        raise ValueError("document must not be empty")

    return model.generate(
        "Summarise the following text using only its content:\n\n"
        + document
    )
```

A real provider adapter implements `TextModel` using the current SDK. Unit tests can pass a fake implementation instead of calling a paid external API.

## 4. Basic retry and timeout principles

Set a finite timeout. Retry only errors that are transient and safe to retry. Use bounded exponential backoff with jitter and respect provider retry instructions. Do not retry invalid requests or authentication failures indefinitely.

For requests that might be charged more than once or trigger tools, consider idempotency and duplicate processing.

## 5. Streaming versus non-streaming

A non-streaming request waits for the completed response. Streaming returns output chunks as they are generated, improving perceived responsiveness for interactive interfaces.

Streaming makes UI and error handling more involved: partial output may already have been shown when a later error occurs. Do not treat partial streamed content as a completed, validated result.

## Practice exercise

Create a provider-neutral model interface and a fake model for unit tests. Implement one provider adapter using current official SDK documentation. Add timeout/error handling and verify that unit tests do not require a real API key.

## Self-check

- What belongs in a model adapter?
- Why should provider credentials stay on a trusted backend?
- Why is a timeout required?
- What is different about handling streamed output?
- Which provider errors should not be retried automatically?

## Official references

- OpenAI API docs: https://platform.openai.com/docs/
- OpenAI text generation: https://platform.openai.com/docs/guides/text
- Azure OpenAI: https://learn.microsoft.com/azure/ai-services/openai/
