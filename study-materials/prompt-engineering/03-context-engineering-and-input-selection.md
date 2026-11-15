# 03 — Context Engineering and Input Selection

## Prompt versus context

The prompt is the complete input/instruction package for a model request. Context is the information the model can use while answering: user details, conversation history, retrieved documents, code, API results, or other data.

Prompt engineering focuses on instructions and examples. Context engineering focuses on selecting, structuring, updating, and limiting the information supplied to the model. Both matter.

## 1. More context is not always better

Imagine asking for a summary of one API exception while sending:
- the complete repository,
- hundreds of old chat messages,
- unrelated documentation,
- duplicated logs.

The useful evidence becomes harder to identify, the request costs more, and conflicting information may appear.

A better approach is to select the smallest relevant evidence set.

## 2. Context selection checklist

Before adding information, ask:
1. Is it relevant to the current task?
2. Is it current?
3. Is it authoritative?
4. Is it permitted for this user?
5. Is it duplicated elsewhere?
6. Is it too large or sensitive?
7. Can I preserve source attribution?

## 3. Structure context in labelled sections

```text
TASK
Summarise the incident using the evidence below.

SERVICE
Name: Orders API
Environment: Test
Version: 2.4.1

OBSERVED SYMPTOMS
- Requests return HTTP 503.
- The issue began after a deployment.

EVIDENCE
- Timestamp: 10:15 UTC
- Redacted health-check log: [sample]

KNOWN LIMITATIONS
- Database telemetry is not available.
- No production evidence is supplied.

OUTPUT
Impact, confirmed facts, hypotheses, next safe checks.
```

This structure is easier to inspect than an unlabelled block of pasted text.

## 4. Preserve metadata

When using retrieved documents, include useful metadata such as source ID, title, version, timestamp, and access classification. Do not remove provenance if the answer needs citations or auditability.

## 5. Context budgets

Models have context limits. The practical usable context is smaller than the theoretical maximum because the request also needs instructions, user input, response space, and sometimes tool definitions.

Use:
- relevant retrieval,
- bounded history,
- concise summaries,
- deduplication,
- truncation rules,
- priority ordering.

Never silently truncate critical evidence. Tell the model or user when relevant content is incomplete.

## 6. Context and privacy

Only include data required for the task. Apply permissions before retrieval and before sending content to a model provider. Redact credentials, tokens, customer identifiers, and unrelated personal data.

A prompt cannot repair an application that retrieved data the user was not allowed to see.

## Practice exercise

Create two versions of a support prompt: one with a large noisy context and one with only three relevant evidence items. Use the same question. Compare correctness, unsupported claims, answer length, and whether the model notices missing evidence.

## Self-check

1. What is context engineering?
2. Why can irrelevant context reduce answer quality?
3. Which metadata helps preserve source provenance?
4. Why should permissions be applied before model context is built?
5. How should an application handle context truncation?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI retrieval guide](https://platform.openai.com/docs/guides/retrieval)
