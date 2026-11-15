# 03 — Clear Instructions and Task Decomposition

## The problem with overloaded prompts

A prompt that asks for architecture, implementation, testing, deployment, security review, and documentation in one unstructured paragraph is difficult to evaluate. I break complex work into stages and make each stage's output visible.

## 1. Use precise verbs

Prefer instructions such as:
- explain,
- compare,
- classify,
- extract,
- summarise,
- review,
- generate,
- transform,
- identify risks,
- propose tests.

Avoid broad requests like “make this better” unless I also define what better means.

## 2. Decompose complex tasks

For a code-change task:

1. Understand the requirement.
2. Inspect the supplied code and constraints.
3. Identify the minimal change.
4. Propose the implementation.
5. Generate or edit code.
6. Add tests.
7. Review security and compatibility.
8. Summarise changes and unresolved assumptions.

A staged workflow gives me checkpoints where I can correct misunderstandings.

## 3. Ask for an explicit plan before implementation

Template:

```text
I need to implement the requirement below.

First, do not write code.
1. Restate the requirement.
2. List the affected components.
3. Identify assumptions and missing information.
4. Suggest a minimal implementation plan.
5. Identify risks and tests.

Wait for confirmation before proposing a code change.

Requirement:
[Paste requirement]
```

This is especially useful when a task may affect data, production behaviour, or multiple services. The confirmation instruction is a workflow control only when the host/application actually enforces the pause.

## 4. Define what is out of scope

Examples:
- Do not change public API contracts.
- Do not update database schema.
- Do not introduce a new package.
- Do not modify unrelated files.
- Do not replace existing logging conventions.
- Do not assume access to production.

Out-of-scope constraints reduce accidental expansion, but the generated result still needs review.

## 5. Separate facts, assumptions, and recommendations

Ask the model to label:
- **Provided facts:** directly present in the supplied input.
- **Assumptions:** needed to proceed but not confirmed.
- **Recommendations:** proposed improvements.
- **Unknowns:** values that cannot be determined.

This helps prevent a polished explanation from hiding gaps in the source information.

## Practice exercise

Write a prompt for diagnosing an intermittent ASP.NET Core API timeout. Ask for a triage plan first, require facts/assumptions/unknowns to be separate, prohibit destructive troubleshooting steps, and request safe read-only checks before remediation.

## Self-check

1. Why decompose a multi-part task?
2. Which verbs make a prompt clearer?
3. How do out-of-scope constraints help?
4. Why separate facts from assumptions?
5. When should a plan be reviewed before code is generated?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
