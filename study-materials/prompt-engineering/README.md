# Prompt Engineering — Beginner to Expert Learning Path

I use this course to learn how to create clear, useful, safe, and testable prompts—from simple ChatGPT requests to prompts embedded in .NET/Python applications, RAG systems, and AI agents.

## Who this course is for

Developers who use AI assistants or build applications powered by language models. No advanced AI background is required. The lessons begin with plain-English fundamentals and gradually move into evaluation, security, and production operations.

## What I should be able to do by the end

- Explain how prompts, context, model instructions, and output contracts work.
- Create prompts using clear tasks, relevant context, constraints, and examples.
- Choose between zero-shot, one-shot, and few-shot prompting.
- Create reusable templates for debugging, code review, testing, SQL, documentation, and architecture.
- Request structured outputs and validate them in Python/.NET code.
- Handle missing information, conflicts, and uncertainty.
- Recognise prompt injection and understand why prompt-only security is insufficient.
- Evaluate prompts with representative datasets and regression tests.
- Version, optimise, monitor, and govern production prompts.
- Build a reusable prompt library for a Developer Support Assistant.

## Learning path

| Lesson | Topic | Outcome |
|---:|---|---|
| 01 | [Prompt engineering fundamentals](01-prompt-engineering-fundamentals.md) | Understand prompts and the model interaction |
| 02 | [Instructions, roles, and message hierarchy](02-understanding-instructions-roles-and-message-hierarchy.md) | Separate application instructions from user input |
| 03 | [Context engineering and input selection](03-context-engineering-and-input-selection.md) | Supply relevant, safe, current context |
| 04 | [Zero-shot, one-shot, and few-shot prompting](04-zero-shot-one-shot-and-few-shot-prompting.md) | Use examples effectively |
| 05 | [Prompt templates, variables, and reuse](05-prompt-templates-variables-and-reuse.md) | Build maintainable prompt templates |
| 06 | [Output formatting, JSON, and schema validation](06-output-formatting-structured-json-and-schema-validation.md) | Produce and validate structured results |
| 07 | [Task-specific patterns: classification and extraction](07-task-specific-prompt-patterns-classification-extraction.md) | Design prompts for structured tasks |
| 08 | [Task-specific patterns: summarisation and Q&A](08-task-specific-prompt-patterns-summarisation-and-question-answering.md) | Summarise and answer from evidence |
| 09 | [Reasoning-friendly workflows and decomposition](09-reasoning-friendly-workflows-and-decomposition.md) | Break complex work into reviewable stages |
| 10 | [Prompt injection and defensive design](10-prompt-injection-threats-and-defensive-prompt-design.md) | Identify attacks and apply layered controls |
| 11 | [Prompt evaluation, testing, and regression](11-prompt-evaluation-testing-and-regression.md) | Measure prompt quality with repeatable tests |
| 12 | [Optimisation, versioning, and experiment design](12-prompt-optimization-versioning-and-experiment-design.md) | Improve prompts based on evidence |
| 13 | [Production prompt architecture and observability](13-production-prompt-architecture-and-observability.md) | Operate prompts as production assets |
| 14 | [Capstone: Developer Support Assistant prompt library](14-capstone-prompt-library-for-developer-support-assistant.md) | Build a reusable, evaluated prompt library |

## Essential best-practices guide

Start with **[Prompt Engineering Best Practices — Practical Prompt-Creation Playbook](prompt-engineering-best-practices.md)**. It includes:
- a reusable prompt formula,
- weak versus strong prompt examples,
- a prompt review checklist,
- common mistakes and how to fix them,
- ready-to-use templates for learning, .NET debugging, unit-test generation, code review, documentation, and architecture comparisons,
- a repeatable prompt improvement loop.

## Suggested learning pace

Plan for 2–3 weeks with 1–2 focused hours a day. Spend time adapting the templates to real but non-sensitive examples. The important skill is not memorising one prompt format; it is learning to define requirements, supply the right context, and evaluate results.

## Recommended practice environment

- ChatGPT or another approved model interface for manual exercises.
- Python for template and validation examples.
- .NET / ASP.NET Core for developer use cases.
- Git for storing and reviewing prompt versions.
- Synthetic or redacted data for tests.
- A small evaluation dataset for recurring or high-impact prompts.

## How to study each lesson

1. Read the plain-English explanation first.
2. Rewrite the example prompt in your own words.
3. Run it on more than one input, including an edge case.
4. Review the answer for factual accuracy and unsupported assumptions.
5. Complete the practice exercise.
6. Save useful prompts with a version and intended use.
7. Re-test after changing the prompt or model.

## Core prompt design principles

- Be specific about the task and expected outcome.
- Give relevant context, but do not overload the model.
- State constraints and out-of-scope requirements.
- Use examples when they clarify a pattern.
- Specify an output format and validate it in code.
- Ask the model to identify missing facts and assumptions.
- Prefer concise, focused templates over one universal prompt.
- Test prompts with normal, edge, ambiguous, and adversarial inputs.
- Track prompt versions separately from model versions.

## Security and reliability principles

- Never place passwords, API keys, tokens, or connection strings in prompts.
- Treat user input, retrieved documents, and tool outputs as untrusted data.
- Delimiters help readability but do not enforce security.
- Prompts do not grant permissions. Enforce identity and access in trusted code.
- Validate structured output before application use.
- Require real approval controls for consequential actions.
- Record prompt/model versions and monitor quality, latency, and cost.
- Do not accept generated code without review and appropriate tests.

## How this course connects to other tracks

- [GenAI](../genai/README.md) — model behaviour, APIs, and structured outputs.
- [RAG](../rag/README.md) — grounding answers in retrieved evidence.
- [Agentic AI](../agentic-ai/README.md) — using prompts with tools and workflows.
- [MCP](../mcp/README.md) — connecting hosts to external capabilities.
- [LLM Evaluation](../llm-evaluation/README.md) — datasets, metrics, judges, regression tests, and monitoring.

## Official references

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI structured outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [OpenAI evals](https://platform.openai.com/docs/guides/evals)
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
- [Pydantic documentation](https://docs.pydantic.dev/)

This is a living course. Verify provider-specific features and model capabilities against current official documentation. The examples teach prompt design concepts and should be adapted and tested with the actual model and application.
