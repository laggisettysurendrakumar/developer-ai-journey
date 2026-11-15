# 05 — Zero-Shot, Few-Shot, and Example Design

## What examples do

A model can often infer a pattern from a few examples. Few-shot prompting supplies representative input/output pairs that demonstrate the expected task and format.

## 1. Zero-shot prompting

Zero-shot means asking for a task without providing examples.

```text
Classify this support request as network, application, database, or unknown.
Return only the category and one-sentence reason.

Request:
[Paste request]
```

Use it when the task is straightforward and the model already understands the requested pattern.

## 2. Few-shot prompting

Few-shot means supplying a small number of examples.

```text
Classify support requests using one category:
network, application, database, unknown.

Example:
Request: "The API hostname does not resolve."
Output: {"category":"network"}

Example:
Request: "The API returns HTTP 500 after a new deployment."
Output: {"category":"application"}

Example:
Request: "SQL connection attempts are timing out."
Output: {"category":"database"}

Now classify:
Request: "[New request]"
Return the same JSON shape.
```

The examples show the boundary between categories and the desired output shape.

## 3. Choose examples carefully

Good examples should:
- represent common cases,
- include confusing boundary cases,
- show the exact output format,
- avoid leaking real customer or company data,
- be consistent with the task definition,
- include an unknown/insufficient-information case when useful.

A few contradictory examples can make behaviour less stable.

## 4. Balance and ordering

If one category dominates the examples, the model may overuse it. Include varied examples and evaluate each class. Try changing example order only when you have a test set to determine whether it makes a real difference.

## 5. When not to use few-shot

Few-shot examples add prompt length and maintenance. They are not always necessary. For simple tasks, a clear instruction and schema may be enough. For large, frequently updated knowledge, retrieval is usually more suitable than embedding lots of examples in every prompt.

## 6. Keep examples separate from live input

Use explicit boundaries and do not confuse an example with a real instruction. For sensitive workflows, validate output and enforce permissions outside the model.

## Practice exercise

Create 8 synthetic examples for categorising .NET production issues: network, authentication, database, configuration, application, capacity, and unknown. Include at least one ambiguous example. Write expected labels and test a zero-shot and few-shot version against the same inputs.

## Self-check

1. What is zero-shot prompting?
2. What is few-shot prompting?
3. Why should examples cover boundary cases?
4. When can examples make a prompt worse?
5. Why compare zero-shot and few-shot on the same dataset?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
