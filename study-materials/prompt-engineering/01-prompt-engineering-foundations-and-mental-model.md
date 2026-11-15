# 01 — Prompt Engineering Foundations and the Right Mental Model

## What this lesson teaches

Prompt engineering is the practice of designing instructions and context so a model is more likely to produce useful, relevant, and predictable results. It is not magic wording and it is not a substitute for good application design.

I begin with the basic mental model: a model predicts output from the input context it receives. The quality of that input matters, but so do the model, task, available context, settings, tools, and application validation.

## 1. What is a prompt?

A prompt is the input supplied to a model for a generation request. Depending on the application, it may contain:
- instructions about the task,
- relevant context or reference material,
- a user question,
- examples of expected behaviour,
- constraints,
- an output format or schema.

A short prompt can work very well for a simple task. A detailed prompt is useful when the task has several requirements or a strict output contract. Longer does not automatically mean better.

## 2. Prompt engineering versus model engineering

| Activity | Main responsibility |
|---|---|
| Prompt engineering | Express task, context, rules, examples, and output requirements |
| Model selection | Choose a model that fits quality, latency, cost, and modality needs |
| Context engineering | Select and organise information the model needs |
| Application engineering | Validate results, enforce permissions, handle failures |
| Evaluation | Measure whether behaviour meets requirements |

A prompt cannot guarantee truth, enforce access control, or prevent every attack. Trusted application code must enforce those controls.

## 3. A simple first prompt

Weak prompt:

```text
Explain Docker.
```

Improved prompt:

```text
Explain Docker to a developer who knows basic Linux but has never used containers.

Cover:
1. What problem Docker solves.
2. The difference between an image and a container.
3. One practical ASP.NET Core example.
4. Three common beginner mistakes.

Use plain English. Explain technical terms before using them.
Finish with five short self-check questions.
```

The second prompt tells the model the audience, scope, structure, and style. It is still important to review the answer for correctness.

## 4. When prompt engineering helps

- turning rough notes into a structured summary,
- extracting fields from text,
- generating a first draft of code or documentation,
- classifying a support issue,
- asking questions over approved reference material,
- converting a natural-language request into a constrained format.

For exact calculations, access control, data updates, or deterministic business rules, ordinary code should do the authoritative work.

## 5. The first prompt checklist

Before sending a prompt, ask:
- Is the task clear?
- Have I provided the necessary context?
- Have I identified the intended audience?
- Have I stated important constraints?
- Have I specified the result format?
- Have I explained what to do when information is missing?
- Can I check whether the result is correct?

## Practice exercise

Take five vague requests you might send to an AI assistant. Rewrite each one to include a clear task, intended audience, context, and expected result. Compare whether the rewritten prompt makes success easier to judge.

## Self-check

1. What does a prompt contain?
2. Why is a longer prompt not always better?
3. How does prompt engineering differ from evaluation?
4. Which responsibilities must remain in application code?
5. What makes a prompt's expected result testable?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
