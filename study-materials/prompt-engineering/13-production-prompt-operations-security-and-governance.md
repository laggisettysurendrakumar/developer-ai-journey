# 13 — Production Prompt Operations, Security, and Governance

## Prompts are part of production behaviour

Once a prompt influences a customer-facing or internal workflow, prompt changes need operational discipline. A prompt can affect quality, cost, latency, privacy, and safety.

## 1. Production prompt lifecycle

1. Author a template.
2. Review task scope, data boundaries, and output contract.
3. Run unit and evaluation tests.
4. Conduct security and privacy review.
5. Approve a version.
6. Release to a controlled environment.
7. Monitor quality, cost, latency, and failures.
8. Roll back or revise when results regress.

## 2. Monitor useful signals

Depending on the application, track:
- prompt version,
- model/provider version,
- request count,
- latency percentiles,
- token/input-output usage,
- schema validation failure rate,
- refusal and fallback rates,
- human correction rate,
- task-specific quality metrics,
- safety incidents.

Do not log raw prompts and outputs indiscriminately. Apply data minimisation, redaction, access control, and retention rules.

## 3. Security and privacy review

Ask:
- Does the prompt include personal, customer, or confidential material?
- Is every included document authorised for this user?
- Could retrieved content contain prompt injection?
- Does the prompt request secrets or unnecessary personal data?
- Can model output trigger a write or external action?
- Are approvals and audit events enforced by trusted code?

## 4. Model and prompt changes

A model change can alter output even when the prompt is unchanged. Record prompt and model versions separately. Re-run evaluations when either changes.

## 5. Cost and latency controls

- remove redundant instructions,
- retrieve only relevant context,
- cap input and output sizes,
- use a smaller model for simple tasks when evaluation supports it,
- cache only when freshness and permission isolation are safe,
- use bounded retries,
- measure cost per successful task rather than only cost per request.

## 6. Ownership and governance

Every production prompt should have an owner, purpose, data classification, approved model, test pack, release history, and rollback path. Define who can edit it and how emergency changes are reviewed.

## Practice exercise

Write a production-readiness checklist for a support summarisation prompt. Include version control, model details, privacy review, evaluation results, monitoring, owner, alert conditions, and rollback procedure.

## Self-check

1. Why record prompt and model versions separately?
2. Which operational signals help identify prompt regressions?
3. Why is unrestricted logging risky?
4. What does a production prompt owner need to maintain?
5. How can cost be optimised without silently reducing quality?

## Further reading

- [LLM Evaluation course](../llm-evaluation/README.md)
- [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)
