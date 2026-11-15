# Prompt Engineering Best Practices — A Practical Prompt-Creation Playbook

I use this playbook before writing, reviewing, or improving a prompt. It is designed for day-to-day work with ChatGPT and for prompts embedded in Python, .NET, RAG, and agentic applications.

## 1. Start with the outcome, not fancy wording

Before writing a prompt, answer these questions:

1. What task must the model perform?
2. Who will use the answer?
3. What information does the model need?
4. What must the model not assume or change?
5. What should the result look like?
6. How will I check whether the result is correct?

A prompt is successful when its output meets the real task requirements, not merely when the response sounds confident.

## 2. The reusable prompt formula

Use this structure as a checklist. Remove sections that do not help the task.

```text
ROLE / PERSPECTIVE
[Which viewpoint or level of expertise is useful?]

TASK
[One clear action. State the desired outcome.]

CONTEXT
[Project, audience, framework/version, relevant background.]

INPUT
[Paste the code, question, notes, or evidence.]
Label user-provided or retrieved content as untrusted data.

CONSTRAINTS
- [What must be preserved?]
- [What must not be changed or assumed?]
- [What sources or evidence may be used?]
- [What is out of scope?]

OUTPUT FORMAT
[Sections, table, checklist, JSON schema, code diff, etc.]

UNCERTAINTY HANDLING
[What should happen when information is missing, conflicting, or unclear?]

QUALITY CHECK
[Ask for assumptions, risks, tests, or validation steps.]
```

This is a flexible prompt framework, not a magic template. A simple question may need only one or two sentences.

## 3. A weak prompt versus a strong prompt

**Weak**

```text
Fix my API.
```

**Stronger**

```text
Act as a senior ASP.NET Core developer helping me investigate a bug.

Task:
Analyse the controller method and error details below. Identify the most likely cause and recommend the smallest safe fix.

Context:
- Framework: ASP.NET Core 8
- Data access: Entity Framework Core
- Database: SQL Server
- Environment: test

Constraints:
- Do not invent classes, configuration, or logs that are not supplied.
- Do not change public API behaviour unless the evidence requires it.
- Separate confirmed facts, assumptions, and unknowns.
- Suggest read-only diagnostic checks before remediation.

Output:
1. What the code currently does
2. Evidence from the supplied code/logs
3. Likely causes, ranked with reasons
4. Minimal fix
5. Unit/integration tests
6. Risks and rollback considerations

Code:
<code>
[Paste relevant method]
</code>

Error:
<error>
[Paste redacted error]
</error>
```

The stronger version includes the task, environment, boundaries, evidence, and a result structure. It still needs a developer to verify any proposed change.

## 4. Best practices while creating prompts

### Be specific

Use precise verbs such as explain, classify, extract, compare, review, summarise, or generate. Avoid vague requests such as “make it better” unless you define the quality criteria.

### Give only relevant context

Include framework versions, expected behaviour, relevant code, schema, error messages, or business rules. Do not paste an entire repository when a few focused files will answer the question.

### State constraints explicitly

Examples:
- Keep existing public method signatures.
- Do not add packages.
- Use the test framework already present.
- Do not modify unrelated files.
- Do not invent missing data.
- Use only the supplied sources.

### Define the output

Tell the model whether you need a short explanation, comparison table, code patch, JSON object, test cases, or step-by-step implementation guide. For machine consumption, use a supported structured-output feature and validate the result in code.

### Explain how to handle uncertainty

Useful instructions:
- State when information is missing.
- Mark assumptions separately.
- Identify conflicts between sources.
- Ask a focused clarification when a missing fact changes the answer.
- Do not present guesses as confirmed facts.

### Supply examples only when they add value

Use zero-shot for straightforward tasks. Use one-shot or few-shot examples when the desired pattern or classification boundary is difficult to explain with rules alone. Use representative, consistent, privacy-safe examples.

### Keep prompts maintainable

Use named templates, explicit placeholders, version numbers, owners, and tests. Avoid one huge universal prompt with unrelated rules for every task.

### Review the answer

Check facts, code, API names, citations, schema validity, edge cases, security, and compatibility. A model output is a proposal, not an automatic source of truth.

## 5. Ready-to-use prompt templates

### A. Learn a new technical topic

```text
Teach me [TOPIC] from beginner level.

My background:
[What I already know]

Explain:
1. The problem this technology solves.
2. Important concepts in plain English.
3. How it works internally at a high level.
4. A small practical example.
5. Common mistakes and troubleshooting.
6. When I should and should not use it.

Define technical terms before using them. Use examples relevant to [MY STACK].
Finish with a practice exercise and self-check questions.
```

### B. Debug a .NET issue

```text
Help me diagnose this issue in an ASP.NET Core application.

Environment:
- .NET version: [VERSION]
- Hosting: [IIS / Azure App Service / container]
- Database: [DATABASE]
- Environment: [DEV / TEST / PROD]
- Recent changes: [CHANGES OR UNKNOWN]

Evidence:
<logs>
[REDACTED LOGS]
</logs>
<code>
[RELEVANT CODE]
</code>

Return:
- Confirmed facts
- Possible causes ranked by evidence
- Safe read-only checks and expected findings
- Smallest likely fix
- Tests to prevent recurrence
- Risks and rollback

Do not invent evidence. Do not recommend disruptive production actions before diagnosis and impact review.
```

### C. Generate unit tests

```text
Create unit tests for the supplied C# method.

Use:
- Existing test framework: [xUnit / NUnit / MSTest]
- Mocking library: [if already used]
- Target behaviour: [EXPECTED BEHAVIOUR]

Cover happy path, boundaries, invalid input, nulls where relevant, and dependency failures.
Keep production code unchanged. Do not invent dependencies.
Explain each test's purpose and identify any missing requirements.
```

### D. Review code

```text
Review this code/diff for correctness and maintainability.

Prioritise:
- bugs and edge cases,
- security and data exposure,
- concurrency and resource handling,
- performance,
- backward compatibility,
- test gaps.

For every finding include severity, evidence, impact, and a suggested fix.
Do not report style preferences as defects. Do not invent line references.
If no issue is evident, state the limits of the review.
```

### E. Create technical documentation

```text
Write developer documentation for [COMPONENT].

Audience: developers maintaining the service.
Use only the supplied source material.
Cover purpose, prerequisites, architecture, request flow, configuration,
errors, testing, troubleshooting, and security.
Keep identifiers exact. Mark unknown information as "Not documented".
Include a concise example where the source supports one.
```

### F. Compare architecture options

```text
Compare [OPTION A] and [OPTION B] for this requirement: [REQUIREMENT].

Evaluate reliability, security, performance, cost, operational complexity,
maintainability, team skills, and migration risk.
List assumptions. Do not invent benchmark results.
Provide a comparison table and a recommendation with trade-offs.
List what evidence would change the recommendation.
```

## 6. A prompt review checklist

Before using a prompt in a real workflow, check:

- [ ] The task asks for one clear outcome.
- [ ] Audience and background are relevant.
- [ ] Required context is present and current.
- [ ] User/retrieved content is clearly separated from application instructions.
- [ ] Constraints and out-of-scope items are explicit.
- [ ] Output structure is clear.
- [ ] Missing or conflicting information has a defined handling rule.
- [ ] No password, token, connection string, or unnecessary personal data is included.
- [ ] Any structured output is validated in trusted code.
- [ ] A representative test set exists for recurring or high-impact use.

## 7. Common prompt mistakes

| Mistake | Why it causes problems | Better practice |
|---|---|---|
| “Be perfect” | Not measurable | Define acceptance criteria |
| No task context | Model must guess | Provide relevant background |
| Too many unrelated tasks | Output becomes unfocused | Split into stages |
| Conflicting requirements | Model cannot satisfy both | Define priority |
| Excessive examples | Adds noise and token cost | Use representative examples |
| Asking for JSON only | Does not guarantee valid JSON | Use schema mode and validate |
| Trusting generated code | It may compile poorly or be unsafe | Review, run tests, inspect diffs |
| Relying on prompt for security | Prompt injection can bypass wording | Enforce controls in trusted code |
| Changing prompt and model together | Hard to identify what caused a result | Track and compare versions |

## 8. Improve prompts through a repeatable loop

1. Write the simplest prompt that could work.
2. Save it as version 1.
3. Create representative normal, edge, ambiguous, and adversarial inputs.
4. Define expected behaviour and a scoring rubric.
5. Run the same test set against the prompt.
6. Identify the largest recurring failure.
7. Change one meaningful thing.
8. Run the tests again and review regressions.
9. Record prompt version, model version, settings, cost, and latency.
10. Keep the change only when evidence supports it.

For production systems, use the [LLM Evaluation course](../llm-evaluation/README.md) to build stronger datasets, metrics, and release gates.

## 9. Important security reminder

Prompt instructions are not access control. Do not put secrets in prompts. Treat user messages, retrieved pages, files, and tool outputs as untrusted data. Enforce identity, permissions, approval, and business rules in application code. Validate model output before using it to query data or take an action.

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI structured outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [OpenAI evals guide](https://platform.openai.com/docs/guides/evals)
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
