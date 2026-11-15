# 07 — Practical Prompt Patterns for Developer Tasks

## Goal

This lesson collects reusable templates for everyday engineering work. I should adapt the context and constraints rather than paste a generic prompt and trust the result.

## 1. Explain unfamiliar code

```text
Explain the following code to a developer who knows C# but is new to this project.

Context:
- Runtime/framework: [version]
- Component purpose: [purpose]
- Known dependencies: [dependencies]

Requirements:
- Explain execution order in plain English.
- Define unfamiliar terms.
- Refer only to code provided.
- Separate current behaviour from recommended improvements.
- Identify edge cases and tests.

Code:
<code>
[Paste relevant code]
</code>
```

## 2. Debug an exception

```text
Help me investigate this error. Do not guess a root cause.

Environment:
- Application: ASP.NET Core [version]
- Hosting: [IIS/App Service/container]
- Environment: [development/test/production]
- Recent changes: [known changes]

Evidence:
<error>
[Stack trace and redacted logs]
</error>

Return:
1. Confirmed facts
2. Possible causes ranked by evidence
3. Safe read-only checks
4. What evidence each check will provide
5. Remediation options only after diagnosis
6. Missing information

Do not recommend deleting data, restarting production systems, or changing configuration without discussing impact and rollback.
```

## 3. Review a pull request

```text
Review this diff as a senior engineer.

Focus on:
- correctness and edge cases,
- security and data exposure,
- concurrency and resource handling,
- performance,
- backward compatibility,
- test coverage.

For each finding provide:
- severity,
- file/line reference if available,
- concrete evidence,
- impact,
- suggested fix.

Do not report style preferences as defects. If no issue is found in an area, say that it was reviewed without a finding.

Diff:
[Paste diff]
```

## 4. Generate unit tests

```text
Generate unit tests for the supplied method.

Constraints:
- Use the test framework already shown in the project.
- Do not change the production method.
- Include happy path, boundary conditions, invalid input, and dependency failure.
- Mock only external dependencies.
- Avoid tests that merely repeat implementation details.
- Explain any assumptions.

Code:
[Paste method and relevant interfaces]
```

## 5. Explain a SQL query

```text
Explain this SQL query for an application developer.

Database engine/version: [SQL Server version if known]
Schema supplied: [tables and relationships]

Cover:
- output meaning,
- joins and filters,
- aggregation,
- possible NULL behaviour,
- indexes or performance considerations,
- assumptions that cannot be confirmed from the query.

Do not invent table relationships.

Query:
[Paste SQL]
```

## 6. Write technical documentation

```text
Create developer documentation for the component below.

Audience: developers maintaining this service.
Include: purpose, prerequisites, configuration, request flow, error handling, testing, troubleshooting, and security notes.
Use only supplied facts. Mark unknown values as "Not documented".
Use concise examples and keep code identifiers unchanged.

Source:
[Paste approved material]
```

## 7. Compare architecture options

```text
Compare these design options for the stated requirement.

Evaluate:
- operational complexity,
- reliability,
- security,
- maintainability,
- performance,
- cost,
- team skills,
- migration and rollback.

State assumptions. Use a comparison table, then recommend an option with reasons and trade-offs. Do not invent benchmark numbers.

Requirement and options:
[Describe]
```

## Best practice

A good engineering prompt supplies relevant versions, code, expected behaviour, error output, constraints, and desired result. It avoids secrets, asks for evidence, and leaves final engineering judgement with the developer.

## Practice exercise

Use at least four templates on synthetic examples: code explanation, debugging, PR review, and unit-test generation. Record where the output was wrong, vague, or unsupported, and improve one prompt based on those observations.

## Self-check

1. What context makes a debugging prompt useful?
2. Why ask for evidence in a code review?
3. Why should unit-test prompts mention existing test frameworks?
4. How can a SQL prompt prevent invented schema assumptions?
5. What must be reviewed before accepting generated code?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
