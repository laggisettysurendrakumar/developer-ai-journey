# 01 — Prompt Engineering Fundamentals

## Why I am learning prompt engineering

A prompt is the instruction and context sent to a model for a particular task. Prompt engineering is the practice of designing, testing, and improving those inputs so the model produces useful, consistent, and appropriately constrained results.

A prompt is not magic wording. Good results usually come from a clear task, relevant context, explicit constraints, suitable examples, a defined output contract, and a way to evaluate the answer.

## 1. First understand how a model receives a prompt

A model does not automatically know the application, the user, or the intended business outcome. It receives the information included in its input and generates a response based on learned patterns and that context.

For example, this is vague:

```text
Explain this error.
```

The model does not know whether I need a beginner explanation, a root-cause analysis, or a production remediation plan.

A better version is:

```text
Explain the following ASP.NET Core error to a developer with basic .NET experience.
Describe:
1. What the error means in plain English.
2. The most likely causes.
3. Safe checks I can run before changing anything.
4. A small example fix.
5. What evidence would confirm the fix.

Error:
<error>
[Paste a redacted error message]
</error>
```

The second prompt tells the model what task to perform and how to shape the answer.

## 2. The core prompt ingredients

| Ingredient | What it means | Example |
|---|---|---|
| Task | The work to perform | Explain an exception |
| Context | Relevant background | ASP.NET Core 8 API |
| Input | Material to analyse | Redacted stack trace |
| Constraints | Boundaries and rules | Do not invent missing facts |
| Output format | Shape of the answer | Numbered sections |
| Quality criteria | What a good answer must contain | Include safe diagnostics |
| Examples | Demonstrations of expected behaviour | Example input/output pair |

Not every prompt needs every ingredient. A simple factual question may need one sentence. A prompt used in a business workflow needs a much clearer contract.

## 3. Prompt quality starts with task clarity

Weak instruction: “Review this code.”

Improved instruction: “Review this C# method for correctness, null handling, security, and performance. Report only issues that can be supported by the code. For each issue, include severity, affected line or method, why it matters, and a minimal fix. Do not rewrite unrelated code.”

The improved version defines the scope and output.

## 4. Separate facts, assumptions, and unknowns

Useful instructions include:
- distinguish evidence from assumptions,
- ask a question when required information is missing,
- state when the available material is insufficient,
- do not invent logs, citations, file paths, or test results,
- give a confidence explanation only when the application can interpret it appropriately.

Do not treat a model's self-reported confidence score as proof of correctness.

## 5. Prompt engineering is iterative

A sensible improvement loop is:

1. Write the smallest prompt that could work.
2. Run it against representative inputs.
3. Inspect where it fails.
4. Change one prompt element at a time.
5. Re-run the same test cases.
6. Keep the change only when the results improve without unacceptable regressions.

Do not judge quality from one impressive example.

## Practice exercise

Take three vague prompts you commonly use: “explain this code,” “fix this bug,” and “summarise this document.” Rewrite each with an explicit task, context, constraints, and output format. Compare the results on at least three synthetic inputs.

## Self-check

1. What is a prompt?
2. Why is “explain this” often too vague?
3. What is the difference between context and instruction?
4. Why should a prompt mention missing information?
5. Why is a single successful output not enough to prove prompt quality?

## Key takeaway

A good prompt makes the task and expected result clear. A reliable AI feature also needs validation, testing, and trusted application logic.
