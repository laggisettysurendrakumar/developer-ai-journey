# 09 — Reasoning-Friendly Workflows and Task Decomposition

## Why decomposition helps

A complex task may be easier to solve as smaller steps with clear outputs. Instead of asking a model to diagnose, change, test, and deploy an application in one request, I can separate evidence collection, hypothesis generation, validation, and recommendations.

Decomposition does not guarantee correctness. Each stage needs a contract and evaluation.

## 1. Break the task into stages

For incident triage:
1. Extract observed symptoms.
2. Separate confirmed facts from assumptions.
3. Identify plausible hypotheses.
4. Select safe read-only checks.
5. Summarise evidence and remaining unknowns.

The application can execute deterministic checks between stages and decide whether a further model call is needed.

## 2. Ask for concise rationale, not hidden internal reasoning

A useful response can provide:
- conclusion,
- relevant evidence,
- assumptions,
- uncertainty,
- next steps.

Do not require or depend on private internal reasoning traces. Ask for a concise, verifiable explanation suitable for the user.

## 3. Use deterministic code where possible

Use normal code for:
- arithmetic,
- access decisions,
- schema validation,
- routing by fixed rules,
- date parsing,
- permissions,
- irreversible business actions.

Use a model when interpreting natural language, generating explanations, or handling ambiguity provides real value.

## 4. Keep intermediate outputs bounded

Each stage should have:
- a named task,
- required input,
- output schema,
- maximum size,
- timeout,
- failure path.

Do not pass every intermediate token or full transcript to every later stage.

## 5. Stop conditions

Define when the workflow should stop:
- evidence is insufficient,
- a required permission is missing,
- validation fails,
- a high-impact decision needs human review,
- the maximum step count is reached.

## Practice exercise

Design a five-step workflow for investigating an HTTP 500 issue. Mark which steps use deterministic code and which use a model. Define an output schema and a stop condition for each step.

## Self-check

1. What is task decomposition?
2. Why are deterministic checks preferable for fixed rules?
3. What explanation should be returned to users?
4. Why should intermediate outputs be bounded?
5. What stop conditions should prevent the workflow from continuing?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
