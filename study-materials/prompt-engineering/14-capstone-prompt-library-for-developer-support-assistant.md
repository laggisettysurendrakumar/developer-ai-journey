# 14 — Capstone: A Reusable Prompt Library for a Developer Support Assistant

## Capstone goal

I will create a small prompt library for a Developer Support Assistant. The library should explain code, summarise errors, extract incident fields, and answer questions using approved evidence. Every prompt should have an explicit contract and tests.

## 1. Scope

Initial tasks:
- explain a C# code snippet,
- classify a redacted application error,
- summarise a synthetic incident report,
- answer a question from supplied runbook passages.

Non-goals:
- execute shell commands,
- make production changes,
- expose credentials,
- authorise access to company documents,
- claim that tests or commands were executed when they were not.

## 2. Prompt asset format

A simple repository structure:

```text
prompt-library/
├── README.md
├── code-explainer/
│   ├── prompt-v1.md
│   └── test-cases.json
├── error-classifier/
│   ├── prompt-v1.md
│   └── test-cases.json
├── incident-summary/
│   ├── prompt-v1.md
│   └── test-cases.json
└── grounded-qa/
    ├── prompt-v1.md
    └── test-cases.json
```

## 3. Example: code explainer prompt

```text
Task: Explain the supplied C# code to a developer with basic .NET experience.

Include:
1. Purpose of the code.
2. Step-by-step execution flow.
3. Important .NET or C# concepts.
4. Potential null, error-handling, or security concerns.
5. One practical example of how the code is used.

Rules:
- Refer only to the supplied code for code-specific claims.
- Mark assumptions clearly.
- Do not claim that the code was compiled or executed.
- If the snippet is incomplete, list the missing context.

<code>
[Untrusted source code]
</code>
```

## 4. Example: error classifier contract

Allowed categories:
- configuration,
- network,
- database,
- authentication,
- authorisation,
- application,
- unknown.

Required output fields:
- category,
- evidence,
- missing_information,
- safe_next_check.

Use `unknown` when available evidence does not support a category. Validate this response with an application schema.

## 5. Test dataset

Create synthetic cases for:
- common valid input,
- missing stack trace,
- conflicting timestamps,
- credentials accidentally present in sample text,
- prompt injection in a code comment,
- oversized input,
- unsupported technology,
- ambiguous root cause,
- empty retrieval,
- outdated runbook.

Do not include real company logs or customer data in public repository examples.

## 6. Evaluation rubric

Score each prompt output from 1 to 5 for:
- correctness,
- evidence grounding,
- completeness,
- clarity,
- format compliance,
- safe handling of uncertainty.

Track deterministic checks separately. A high average must not hide a critical security failure.

## 7. Capstone acceptance criteria

- All prompts have a clear purpose, owner, and version.
- Inputs have defined variables and limits.
- Outputs have a documented structure.
- Structured results are validated in code.
- Missing and contradictory information is handled explicitly.
- Injection cases are included in tests.
- No secrets are present in prompt assets or telemetry.
- Baseline and updated versions are compared using the same dataset.
- Known limitations and rollback versions are documented.

## 8. What I should be able to explain after completion

1. Why is this prompt designed this way?
2. Which input fields can vary?
3. Which content is untrusted?
4. What does the application validate after generation?
5. Which tests prove it behaves as expected?
6. What changed between prompt versions?
7. What would make me roll back a prompt release?

## Practice exercise

Implement the four prompt assets in the project. Write at least five synthetic test cases per prompt. Run a baseline evaluation, improve one prompt based on a measured failure, and document the before/after evidence. Keep any provider API integration optional and use approved credentials outside source control.

## Self-check

1. What is the purpose of a prompt library?
2. Why version prompts and test cases together?
3. Why should critical security failures not be hidden by an average score?
4. Which application checks run after generation?
5. What evidence is required before releasing an improved prompt?

## Definition of done

The capstone is a learning project, not a production certification. Production deployment still requires security, privacy, data governance, model/provider, operational, and user-impact review.
