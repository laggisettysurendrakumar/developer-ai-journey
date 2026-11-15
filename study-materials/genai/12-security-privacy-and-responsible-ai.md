# 12 — Security, Privacy and Responsible AI

## Treat the model as an untrusted component

A model can return inaccurate content, reveal information present in its context, or be manipulated by hostile input. Security must be enforced in trusted application code and infrastructure.

## 1. Protect inputs and outputs

- Validate size, type, and format of user input.
- Apply content and business policy.
- Treat retrieved documents and tool results as untrusted data.
- Validate generated structures before use.
- Escape content before rendering in a browser.
- Keep secrets out of prompts unless an approved design explicitly requires a protected secret operation.

## 2. Prompt injection

Prompt injection is an attempt to manipulate a model through malicious instructions in a user prompt, document, or tool output. A model may follow hostile text despite the application's intended instruction hierarchy.

Mitigations include:
- Clearly separate trusted instructions from untrusted content.
- Restrict tools and their arguments.
- Enforce permissions in backend services.
- Filter retrieved content by the user's identity.
- Require approval for sensitive actions.
- Log security-relevant outcomes without exposing sensitive content.
- Test adversarial examples.

Prompt wording alone is not a reliable mitigation.

## 3. Privacy and data governance

Before sending information to an external model, confirm:
- Whether the data may leave the organisation.
- Provider retention and training terms.
- Data residency and regional requirements.
- Encryption and access controls.
- Contractual and regulatory obligations.
- Retention and deletion process.

Use synthetic or redacted data for learning examples.

## 4. Hallucination and user trust

A generated answer may contain unsupported statements. For factual workflows, ground answers in trusted evidence, show source references, express uncertainty, and provide escalation to a human where necessary.

Do not allow model-generated text alone to make irreversible or high-impact decisions.

## 5. Responsible AI checks

Assess fairness, reliability, transparency, privacy, safety, and accountability in the context of the use case. Document limitations and define human oversight where needed.

## Practice exercise

Threat-model a document summarisation service. Include a prompt-injection document, a secret in a source file, an unauthorised user, and a hallucinated conclusion. List preventive and detective controls for each.

## Self-check

- Why should model output be considered untrusted?
- What is prompt injection?
- Why is application-side authorisation essential?
- What provider data-handling terms should be checked?
- When should a human review be required?

## Official references

- OWASP Top 10 for LLM Applications: https://genai.owasp.org/llm-top-10/
- NIST AI Risk Management Framework: https://www.nist.gov/itl/ai-risk-management-framework
- Microsoft Responsible AI: https://www.microsoft.com/ai/principles-and-approach
