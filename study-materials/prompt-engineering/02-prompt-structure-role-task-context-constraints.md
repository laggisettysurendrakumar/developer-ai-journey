# 02 — Prompt Structure: Role, Task, Context, and Constraints

## Why structure matters

A prompt is easier to understand and maintain when I separate its parts. This also makes it easier to review what changed when a model starts behaving differently.

## 1. A reusable prompt structure

I use this as a starting checklist—not a mandatory formula for every request:

1. **Role or perspective:** What expertise or viewpoint is useful?
2. **Task:** What exactly should be done?
3. **Context:** What background information is relevant?
4. **Input boundaries:** Which parts are user-provided or retrieved data?
5. **Constraints:** What must be included, avoided, or preserved?
6. **Output contract:** What structure should the answer follow?
7. **Uncertainty rule:** What should happen when required information is absent?

## 2. Example: ASP.NET Core code explanation

```text
Role:
Act as a senior .NET developer explaining code to a teammate.

Task:
Explain the supplied ASP.NET Core controller method.

Context:
The application uses ASP.NET Core 8, Entity Framework Core, and SQL Server.
The reader understands C# basics but is learning dependency injection.

Constraints:
- Explain the execution flow in order.
- Do not invent classes or configuration that are not shown.
- Identify potential null-handling and error-handling issues.
- Distinguish confirmed behaviour from recommendations.

Output:
1. Plain-English summary
2. Step-by-step flow
3. Important framework concepts
4. Potential issues
5. Suggested tests

Input code:
<code>
[Paste code here]
</code>
```

## 3. Role instructions: useful but not magical

A role such as “act as a senior engineer” can encourage a relevant style and level of detail. It does not give the model real professional experience, guarantee correctness, or override system and application policy.

The task and constraints usually matter more than an elaborate persona.

## 4. Make constraints observable

Vague constraint: “Be accurate.”

More testable constraints:
- “Do not invent API names.”
- “If a required value is absent, write ‘Not provided’.”
- “Keep existing public method signatures unchanged.”
- “List assumptions separately.”
- “Return exactly the fields in the schema.”

Avoid conflicting instructions, such as “be exhaustive” and “answer in one sentence,” unless you specify priority.

## 5. Build a reusable template

Keep templates in source control and use placeholders for variable input. Document which placeholders are trusted system data and which are untrusted user content. Do not interpolate secrets or private data unnecessarily.

## Practice exercise

Create a reusable prompt for explaining a SQL query to a junior developer. Include database context, task, constraints, output sections, and a rule that prevents the model from assuming table relationships that were not supplied.

## Self-check

1. What are the main parts of a structured prompt?
2. When is a role instruction useful?
3. Why are measurable constraints better than vague adjectives?
4. What should I do when prompt instructions conflict?
5. Why should templates identify untrusted input?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
