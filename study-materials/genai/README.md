# Generative AI Engineering — Beginner to Advanced

This learning track explains how generative AI systems work and how to integrate them into reliable applications. It starts with AI and machine-learning foundations, then moves through LLM behaviour, tokens, model APIs, prompts, structured outputs, embeddings, multimodal models, evaluation, safety, and production design.

The focus is practical software engineering: understand what the model does, what the application must do around it, and how to build a feature that can be tested, monitored, secured, and maintained.

## Who this course is for

This course is designed for developers who know basic programming and want to build GenAI features. Python examples are used for clarity, but the concepts apply to ASP.NET Core, Java, Node.js, and other backends.

Helpful prerequisites:
- Basic Python syntax and functions.
- HTTP, REST APIs, JSON, and environment variables.
- Basic understanding of Git and testing.
- Pydantic or DTO/model validation is useful but can be learned along the way.

## How to use the lessons

1. Read the concept explanation and make sure you can explain it in plain English.
2. Study the example and understand what each line does.
3. Identify what is deterministic application logic and what is delegated to the model.
4. Run the practice exercise with synthetic or public sample data.
5. Complete the self-check without looking back at the lesson.
6. Record model, SDK, and prompt versions when testing a live provider.
7. Check current official documentation before using a provider API, SDK parameter, model, or pricing detail.

Never commit API keys or company/customer data. Live model calls may incur charges; use an approved account and a small usage budget.

## Learning roadmap

| Stage | Lessons | Outcome |
|---|---|---|
| Foundations | 01–03 | Understand AI, ML, LLMs, transformers, tokens, context, and generation |
| Application integration | 04–06 | Call model APIs, structure prompts, parse and validate output |
| Model capabilities | 07–09 | Understand embeddings, multimodal models, and model selection |
| Quality and safety | 10–12 | Handle failures, evaluate output, monitor, and protect applications |
| Production design | 13–14 | Manage cost/latency and build an end-to-end GenAI API |

## Lesson index

### Beginner — Understand the technology

1. [AI, Machine Learning, Deep Learning and Generative AI](01-ai-ml-deep-learning-and-generative-ai.md) — explain the AI landscape and know when ordinary code is a better fit.
2. [How LLMs Work: Training, Inference and Transformers](02-how-llms-work-training-inference-and-transformers.md) — tokens, neural representations, attention, next-token generation, and model limitations.
3. [Tokens, Context Windows and Generation Settings](03-tokens-context-windows-and-generation-settings.md) — context budgets, token usage, model settings, and conversation length.

### Intermediate — Integrate models into applications

4. [Model APIs and Provider Integration](04-model-apis-and-provider-integration.md) — request lifecycle, adapters, credentials, streaming, timeouts, and provider errors.
5. [Prompt Structure, Messages and Instructions](05-prompt-structure-messages-and-instructions.md) — task definition, trusted instructions, examples, prompt versioning, and injection boundaries.
6. [Structured Outputs, JSON and Validation](06-structured-outputs-json-and-validation.md) — schemas, Pydantic, structured response features, and application validation.

### Model capabilities — Work with different kinds of information

7. [Embeddings and Semantic Representations](07-embeddings-and-semantic-representations.md) — vectors, similarity, compatibility, and search foundations.
8. [Multimodal Models: Images, Audio and Video](08-multimodal-models-images-audio-and-video.md) — media input/output, validation, privacy, and application considerations.
9. [Model Selection, Quality and Benchmarks](09-model-selection-quality-and-benchmarks.md) — requirements, evaluation datasets, quality dimensions, and trade-offs.

### Advanced — Build reliable and responsible features

10. [Reliability, Rate Limits and Error Handling](10-reliability-rate-limits-and-error-handling.md) — provider failures, backoff, timeouts, concurrency, and idempotency.
11. [Evaluation, Testing and Observability for GenAI](11-evaluation-testing-and-observability.md) — unit tests, evaluations, regression tests, metrics, and traces.
12. [Security, Privacy and Responsible AI](12-security-privacy-and-responsible-ai.md) — prompt injection, data protection, output validation, and human oversight.

### Production — Design and deliver a GenAI application

13. [Cost, Latency and Production Design](13-cost-latency-and-production-design.md) — cost drivers, latency budgets, architecture boundaries, and capacity planning.
14. [GenAI Application Architecture and Capstone](14-genai-application-capstone.md) — build a Text Processing API with model adapter, validation, evaluation, security, and deployment foundations.

## What this course covers — and what is taught separately

This track teaches the core foundations of generative models and application integration. It introduces embeddings and multimodal capabilities, but does not attempt to replace the deeper tracks:

- [Prompt Engineering](../prompt-engineering/README.md) — prompt patterns and advanced techniques.
- [Vector Databases](../vector-databases/README.md) — indexing, metadata, similarity search, and vector storage.
- [RAG](../rag/README.md) — ingestion, chunking, retrieval, grounding, and citations.
- [Agentic AI](../agentic-ai/README.md) — tools, agent loops, planning, memory, handoffs, and production agents.
- [LLM Evaluation](../llm-evaluation/README.md) — additional evaluation and observability practice.

## Suggested study pace

A first pass may take 3–5 weeks with regular practice. Spend more time implementing and evaluating than memorising definitions. Use a fake model in unit tests so the test suite does not need a live provider call.

## Capstone — Text Processing API

Build an API that accepts text and a task such as summarise, classify, or rewrite.

Expected components:
- FastAPI or ASP.NET Core API.
- Request validation and clear response models.
- Provider-neutral model adapter.
- Versioned prompt templates.
- Structured output parsing and validation.
- Fake model for unit tests.
- Error handling for timeout, rate limit, invalid credentials, and malformed response.
- Evaluation dataset and quality rubric.
- Safe logging, usage and latency measurement.
- Docker packaging and documented non-production deployment.

### Definition of done

- The API runs locally from documented steps.
- Provider-specific calls are isolated behind an adapter.
- Tests run without a real model credential.
- Inputs and outputs are validated.
- Model errors map to predictable application behaviour.
- Prompt/model versions and token usage can be inspected.
- Sensitive prompts and credentials are not logged by default.
- Evaluation includes normal, ambiguous, and adversarial examples.
- Cost and latency assumptions are documented.

## Learning checklist

- [ ] Explain AI, machine learning, deep learning, and generative AI.
- [ ] Describe training versus inference and the basic transformer generation process.
- [ ] Explain tokens, context windows, and token-based usage.
- [ ] Call a model using a secure backend integration.
- [ ] Design prompts with clear tasks and content boundaries.
- [ ] Validate structured outputs and distinguish schema validity from factual truth.
- [ ] Explain embeddings and similarity search.
- [ ] Identify multimodal input requirements and risks.
- [ ] Compare models using a repeatable evaluation dataset.
- [ ] Handle rate limits, timeouts, and provider errors.
- [ ] Evaluate output quality, cost, and latency.
- [ ] Apply privacy, security, and responsible AI controls.
- [ ] Build and explain a complete GenAI application architecture.

## Official references

- [OpenAI API documentation](https://platform.openai.com/docs/)
- [OpenAI text generation](https://platform.openai.com/docs/guides/text)
- [OpenAI prompt engineering](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI structured outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [OpenAI embeddings](https://platform.openai.com/docs/guides/embeddings)
- [Hugging Face NLP Course](https://huggingface.co/learn/nlp-course/)
- [Pydantic documentation](https://docs.pydantic.dev/latest/)
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)

Model capabilities, SDKs, APIs, model identifiers, limits, and pricing change. Verify current provider documentation before using examples in a real application.
