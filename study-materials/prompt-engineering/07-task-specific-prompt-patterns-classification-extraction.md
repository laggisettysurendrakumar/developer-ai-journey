# 07 — Task-Specific Prompt Patterns: Classification and Extraction

## One task, one contract

Classification and extraction are common application tasks. They benefit from limited categories, explicit definitions, clear treatment of missing data, and predictable output.

## 1. Classification

Classification assigns an input to one of a known set of labels.

Example prompt:

```text
Task: Classify this support issue.
Allowed labels:
- network
- application
- database
- identity
- unknown

Rules:
- Choose exactly one label.
- Use unknown when evidence is insufficient or the issue is outside these categories.
- Do not infer an infrastructure cause from a symptom alone.
- Include one sentence of supporting evidence.

Input:
<issue>
[Issue description]
</issue>
```

The allowed labels and definitions should be documented. If two categories overlap, specify a precedence rule or allow a multi-label result.

## 2. Information extraction

Extraction pulls specified information from supplied text.

Example:

```text
Extract incident ID, service name, environment, first observed time, and stated symptom.
Rules:
- Copy values only when present.
- Use null when a value is missing.
- Do not calculate or guess timestamps.
- Preserve the source wording for the symptom.
Return the defined JSON schema.
```

## 3. Summarisation

Tell the model:
- target audience,
- length or section limits,
- which facts to preserve,
- whether to include citations,
- how to represent uncertainty,
- whether recommendations are allowed.

## 4. Normalisation

Normalisation converts variations into a standard representation. For example, “prod”, “production”, and “live environment” may map to `production` only if the application has an agreed mapping.

Do not silently normalise ambiguous terms.

## 5. Ask for missing fields explicitly

For extraction tasks, distinguish:
- not present,
- unclear,
- contradictory,
- not applicable.

Do not make an empty string mean all four unless the application intentionally defines that behaviour.

## Practice exercise

Build a prompt that extracts fields from synthetic incident reports. Include missing timestamps, contradictory service names, and malformed incident IDs. Validate the output with a schema and record whether the model preserves unknown values correctly.

## Self-check

1. What is the difference between classification and extraction?
2. Why define an unknown label?
3. What should happen when a field is missing?
4. Why should normalisation rules be explicit?
5. How can ambiguity be represented?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
