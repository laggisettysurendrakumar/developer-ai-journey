# 08 — Task-Specific Prompt Patterns: Summarisation and Question Answering

## Summarisation

Summarisation reduces source content while preserving information important to a particular audience and purpose. A summary is not a licence to add facts.

A good summarisation prompt states:
- who will read the summary,
- why the summary is needed,
- maximum length or sections,
- facts that must be preserved,
- whether recommendations are in scope,
- how to flag missing or conflicting evidence.

Example:

```text
Summarise the supplied deployment incident for an engineering manager.
Include:
1. User impact
2. Timeline
3. Confirmed facts
4. Current hypotheses
5. Actions taken, only when documented
6. Open questions

Do not add causes or actions that are not in the source.
Use "Not stated" when information is missing.
Keep the summary under 250 words.
```

## Question answering from evidence

When the answer should be grounded in supplied material, define the evidence boundary.

```text
Answer the question using only the supplied reference passages.
For every factual claim, identify the supporting source ID.
If the passages do not answer the question, say that the evidence is insufficient.
Do not use outside knowledge to fill missing details.

Question:
[Question]

Reference passages:
[Permission-filtered passages with source IDs]
```

The application must retrieve only content the user can access. The model should not be asked to enforce document permissions.

## Citations and traceability

Preserve source IDs and versions through retrieval and generation. Do not let the model invent URLs, page numbers, or source IDs. Verify citations against the retrieved evidence in application code where feasible.

## Conflicting evidence

Tell the model to report conflicts rather than silently choosing one. Include timestamps and source authority when available. The application can apply deterministic precedence rules for authoritative systems.

## Practice exercise

Write a summarisation prompt for a .NET outage report and a grounded Q&A prompt for an internal runbook. Create cases with missing evidence, outdated documents, and contradictory logs. Check for unsupported claims and fabricated references.

## Self-check

1. Why should summarisation preserve source facts?
2. What is a grounded answer?
3. How can the application verify a citation?
4. What should the model do when evidence conflicts?
5. Why must access filtering happen before generation?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI retrieval guide](https://platform.openai.com/docs/guides/retrieval)
