# 04 — Zero-Shot, One-Shot, and Few-Shot Prompting

## What examples do for a prompt

Examples show a model what a desired input/output pattern looks like. They are useful when an instruction is difficult to describe precisely or when output consistency matters.

## 1. Zero-shot

Zero-shot means I provide the task without examples.

```text
Classify this support issue as network, application, database, or unknown.
Issue: The API cannot resolve the database hostname.
Return only the category.
```

Use it for straightforward tasks with clear instructions.

## 2. One-shot

One-shot includes one demonstration.

```text
Classify each issue as network, application, database, or unknown.

Example:
Issue: The API returns a null reference exception.
Category: application

Now classify:
Issue: The service cannot resolve the database hostname.
Category:
```

## 3. Few-shot

Few-shot includes several representative examples, ideally covering meaningful variations and edge cases.

```text
Classify each issue as network, application, database, or unknown.

Example 1:
Issue: DNS lookup for the API dependency fails.
Category: network

Example 2:
Issue: SQL connection pool is exhausted.
Category: database

Example 3:
Issue: NullReferenceException occurs in an endpoint handler.
Category: application

Example 4:
Issue: The report has no technical evidence.
Category: unknown

Now classify:
Issue: [New issue]
Category:
```

## 4. Choose examples carefully

Good examples are:
- representative of real inputs,
- correct and reviewed,
- diverse enough to cover expected variations,
- consistent in format,
- short enough to leave room for the actual task.

Bad examples can teach the wrong pattern. Avoid examples that encode stereotypes, outdated policies, private data, or accidental contradictions.

## 5. Example order and similarity

The most relevant examples are often the most useful. Group examples by task type and consider whether the first/last examples dominate the pattern. Test ordering rather than assuming one ordering is best.

## 6. Example selection at runtime

A production application can choose examples based on the current task category. Keep the selector deterministic and test it. Do not pass unreviewed user-generated examples into a trusted prompt library.

## Practice exercise

Create a zero-shot and few-shot prompt for classifying .NET exceptions into validation, authentication, authorisation, dependency, or unknown. Build at least five examples, including ambiguous cases. Compare performance on a separate test set not used to write the prompt.

## Self-check

1. What is zero-shot prompting?
2. When can examples improve consistency?
3. Why should examples be reviewed?
4. What is the risk of testing only the examples included in the prompt?
5. How can dynamic example selection be made safer?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
