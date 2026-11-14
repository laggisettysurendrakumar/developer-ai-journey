# Agentic AI Engineering — Beginner to Advanced Course

I created this course to understand how AI agents work from the fundamentals through designing, securing, evaluating, and deploying an agentic application. The goal is not to memorise framework APIs. I want to understand the control loop, write and validate tools, understand what the SDK does, and build an agent that behaves safely.

> **Target learner:** A Python developer who is new to Agentic AI. The course explains concepts in plain language, then builds from normal Python functions to a working agent and a production-minded capstone.

## Why this course has multiple lessons

The earlier overview was not enough to learn the subject. Agentic AI combines normal software engineering, model behaviour, tool contracts, state, orchestration, security, evaluation, and operations. Each of those needs examples and practice. This course separates them so I can understand one concept before combining it with the next.

## Prerequisites

Before starting, I should be comfortable with Python variables, conditions, loops, functions, classes, exceptions, modules, virtual environments, dictionaries, lists, JSON, basic type hints, REST APIs, and basic async/await.

Use the [14-day Python course](../python/README.md) as the foundation. Complete at least Days 1–10 before this course. Read the GenAI and prompt engineering introductions as well.

## How to study

- Study one lesson at a time. Expect 1.5–3 hours per lesson depending on how much code I write.
- Type the examples manually and run them in a virtual environment.
- Explain what each function, parameter, return value, and validation rule does.
- Change an input and predict the output before running it.
- Try invalid input and inspect the error.
- Finish the exercise and self-check before moving forward.
- Use synthetic data and local sample tools. Do not connect practice code to company systems or production credentials.
- Keep API usage optional until the model tool-calling and SDK lessons. Real model requests may cost money.

## Course roadmap — 14 lessons

| Lesson | Topic | Outcome |
|---|---|---|
| 01 | [What is Agentic AI?](01-agentic-ai-from-zero.md) | Explain agents, workflows, and agent use cases |
| 02 | [Python building blocks](02-python-foundations-for-agents.md) | Understand the Python features agents use |
| 03 | [Agent loop without a framework](03-agent-loop-without-framework.md) | Build a bounded mock loop |
| 04 | [Tools, schemas, and permissions](04-tools-schemas-validation.md) | Design safe, validated tools |
| 05 | [Model tool calling](05-model-tool-calling.md) | Understand the LLM/tool round-trip |
| 06 | [State, history, and memory](06-state-conversation-memory.md) | Manage run state and data retention |
| 07 | [Planning and workflows](07-planning-reasoning-workflows.md) | Create and validate bounded plans |
| 08 | [Routing and multi-agent patterns](08-agent-patterns-routing-handoffs.md) | Understand routers, handoffs, and parallelism |
| 09 | [Guardrails and human approval](09-human-approval-guardrails-security.md) | Protect tools and side effects |
| 10 | [Reliability and recovery](10-reliability-retries-idempotency.md) | Handle timeouts, retries, and duplicate work |
| 11 | [Evaluation and tracing](11-evaluation-tracing-observability.md) | Measure quality, tool use, latency, and cost |
| 12 | [OpenAI Agents SDK](12-openai-agents-sdk.md) | Build a real model-driven agent |
| 13 | [Production architecture](13-production-agent-architecture.md) | Design a deployable agent service |
| 14 | [Developer Support Agent capstone](14-capstone-developer-support-agent.md) | Combine the course into a working project |

## Understand the learning progression

```text
Python foundations
       |
       v
What is an agent?
       |
       v
Manual agent loop (mock model)
       |
       v
Validated tools and model tool calling
       |
       v
State, memory, planning, routing
       |
       v
Security, approvals, reliability
       |
       v
Evaluation and tracing
       |
       v
Agent framework (OpenAI Agents SDK)
       |
       v
Production architecture
       |
       v
Developer Support Agent capstone
```

The mock loop comes before the framework intentionally. If I understand the manual loop, I can reason about what a framework is doing and debug it more effectively.

## Important concepts in plain language

### An agent is an application pattern, not a magic model

An LLM can interpret a goal and propose a next step. The application decides which tools are available, checks arguments and permissions, executes functions, tracks state, and stops the run safely.

### Tool calling does not mean the model executes code

The model returns a request for a named tool and arguments. The application runtime validates the request and calls a normal Python function or service. The model receives the result in a later turn.

### Workflow versus agent

A workflow follows steps chosen by the developer. An agent can choose among permitted next steps at runtime. Use workflows when the path is known and predictable; use agents when model-driven decisions add real value. A hybrid design is common.

### State versus memory

Run state helps the current task progress. Conversation history provides context from earlier turns. Long-term memory persists selected information across runs and needs explicit ownership, consent, retention, and deletion rules.

### More agents do not automatically mean a better system

A single agent with a few clear tools is easier to understand and evaluate. Add specialist agents only when there is a clear separation of responsibilities or permissions that justifies the extra latency, cost, and debugging effort.

## Practical capstone

The final project is a **Developer Support Agent**. It accepts a question, validates it, searches sample Linux/Python documentation, keeps source IDs, uses bounded read-only tools, admits when evidence is missing, records trace-friendly metadata, runs tests/evaluations, and is packaged with Docker as a later exercise.

## What advanced means in this course

By the end I should be able to design and explain manual agent orchestration, model-driven tool calls, typed tool contracts, validation, authorisation, state, conversation history, memory trade-offs, planning, workflows, routing, handoffs, parallel tools, prompt injection risks, least privilege, approvals, audit, timeouts, retries, idempotency, evaluation, tracing, latency, cost, and maintainable deployment architecture.

This is a strong foundation, not a claim of mastery. Professional proficiency comes from building more than one application, operating it, and learning from failures.

## Security rules to remember throughout

- A system prompt is not an authorisation system.
- Never give a model unrestricted shell, SQL, cloud, or production credentials.
- Validate model-generated arguments in trusted code.
- Check user identity and resource permissions on every operation.
- Treat user prompts, retrieved documents, and tool outputs as untrusted.
- Require a human to approve consequential or irreversible actions.
- Bound steps, time, retries, concurrency, output size, tokens, and spend.
- Avoid logging secrets and sensitive content.
- Use tests and evaluation before enabling additional tools.

## Official references

- OpenAI Agents SDK for Python: https://openai.github.io/openai-agents-python/
- OpenAI SDK quickstart: https://openai.github.io/openai-agents-python/quickstart/
- OpenAI agent guide: https://platform.openai.com/docs/guides/agents
- Function calling: https://platform.openai.com/docs/guides/function-calling
- Model Context Protocol: https://modelcontextprotocol.io/docs/
- OWASP Top 10 for LLM Applications: https://genai.owasp.org/llm-top-10/

Framework and provider APIs change. Before running a real model call, check the installed SDK's official documentation, supported models, pricing, data handling, and usage limits.
