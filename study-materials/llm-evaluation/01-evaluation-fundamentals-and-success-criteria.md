# 01 — Evaluation Fundamentals and Success Criteria

## Why evaluation is a core engineering activity

A GenAI feature can produce an impressive answer in a demo and still fail in normal use. It may misunderstand real questions, invent details, expose restricted information, or become unreliable after a prompt or model change.

Evaluation is the process of collecting repeatable evidence about whether a system meets its requirements. It combines test cases, expected behaviour, metrics, human review, and analysis of failures.

## 1. Start with the user task

Before choosing a metric, describe the task in plain language:
- Who uses the system?
- What input do they provide?
- What output should they receive?
- What evidence or data may the application use?
- What must the system never do?
- What happens when the answer is unknown or evidence is missing?

Example: a developer support assistant should find the current approved runbook, answer the developer's question, cite the relevant source, and avoid inventing operational steps.

## 2. Turn requirements into measurable criteria

A requirement such as "the answer must be good" is not measurable.

Break it down:
- Relevance: does it address the user's question?
- Correctness: are important claims accurate?
- Groundedness: are claims supported by permitted evidence?
- Completeness: does it include the required points?
- Citation quality: do sources actually support the statements?
- Safety: does it protect secrets and access boundaries?
- Reliability: does it handle provider and dependency failures?
- Efficiency: does it meet latency and cost limits?

Not every task needs every metric. Select criteria based on user impact and risk.

## 3. Define acceptance criteria

Example acceptance criteria for a runbook assistant:
1. It uses the correct current runbook for supported questions.
2. It cites the source document for operational instructions.
3. It says when the approved source does not contain an answer.
4. It does not disclose another team's restricted material.
5. It does not execute a production operation without required approval.
6. It handles timeout and rate-limit failures with a clear response.

Make criteria testable. For example, "does not disclose restricted material" can be tested using identities with different document permissions.

## 4. Build a quality model

Use multiple dimensions rather than one overall score. An application can be factually correct but unsafe, or safe but unhelpful. Critical safety criteria should be pass/fail gates, not averaged away by a high relevance score.

Separate:
- Hard gates: security, authorisation, data leakage, forbidden actions.
- Quality measures: relevance, correctness, completeness, style.
- Operational measures: latency, availability, errors, cost.

## 5. Risk-based evaluation

A low-impact writing helper and a production incident assistant should not have the same release threshold. Identify failure impact, likelihood, detectability, and recovery method. Require more human review for high-impact workflows.

## Practice exercise

Write a one-page evaluation specification for a developer-support assistant. Include the target users, task, approved sources, five measurable quality criteria, three hard safety gates, and operational limits.

## Self-check

- Why is a demo not enough to prove a feature works?
- Why should criteria be defined before selecting metrics?
- What is the difference between a hard safety gate and a quality score?
- Why should evaluation be risk-based?
- How can a requirement be made testable?

## Official references

- OpenAI evaluation concepts: https://platform.openai.com/docs/guides/evals
- NIST AI Risk Management Framework: https://www.nist.gov/itl/ai-risk-management-framework
