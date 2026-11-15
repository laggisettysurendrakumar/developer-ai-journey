# LLM Evaluation Engineering — Beginner to Production

This course teaches how to evaluate, test, and monitor applications built with large language models (LLMs), retrieval-augmented generation (RAG), and agents.

A successful demo is not evidence that an AI system is correct, safe, or reliable. Evaluation provides repeatable evidence about quality, grounding, permissions, tool behaviour, latency, and cost. The goal is to make model and prompt changes measurable, reviewable, and safe to release.

## Who should use this course

Developers who have built or are building a model API, chatbot, RAG application, or tool-using agent. Basic Python, application testing, and model API concepts are helpful.

You can study the ideas before building a complete AI application. The examples use synthetic data and Python-like code. Adapt them to your framework and provider.

## How to study

1. Define the user task and what success means before selecting a metric.
2. Create a small, representative dataset and label expected behaviour.
3. Separate deterministic application tests from model-quality evaluations.
4. Review failures, not just the average score.
5. Record model, prompt, dataset, evaluator, and application versions.
6. Treat data leakage and unauthorised actions as hard failures.
7. Use synthetic or approved redacted data; protect evaluation artifacts and logs.

## Learning roadmap

| Stage | Lessons | Outcome |
|---|---|---|
| Foundations | 01–03 | Define evaluation, design datasets, and create human rubrics |
| Core engineering | 04–06 | Test application contracts, score outputs, and calibrate LLM judges |
| AI-specific evaluation | 07–09 | Evaluate RAG, agents, safety and adversarial behaviour |
| Measurement science | 10–11 | Interpret uncertainty and make experiments reproducible |
| Release and operations | 12–13 | Add CI gates, regression tests, tracing, and live quality monitoring |
| Capstone | 14 | Build a repeatable evaluation framework for a developer assistant |

## Lesson index

### Foundations — Define what good means

1. [Evaluation Fundamentals and Success Criteria](01-evaluation-fundamentals-and-success-criteria.md) — turn user needs into measurable quality, safety, and operational criteria.
2. [Evaluation Datasets and Test Case Design](02-evaluation-datasets-and-test-case-design.md) — happy paths, edge cases, no-answer cases, dataset splits, leakage, and versioning.
3. [Human Annotation and Reference Answers](03-human-annotation-and-reference-answers.md) — rubrics, human review, inter-annotator agreement, adjudication, and reference properties.

### Core engineering — Test and measure outputs

4. [Deterministic Tests and Application Contracts](04-deterministic-tests-and-application-contracts.md) — fake models, unit tests, contract tests, permission checks, and failure paths.
5. [Evaluation Metrics for Generated Answers](05-evaluation-metrics-for-generation.md) — correctness, relevance, groundedness, classification metrics, text overlap, and pairwise comparisons.
6. [LLM-as-a-Judge: Design and Calibration](06-llm-as-judge-design-and-calibration.md) — judge prompts, rubric design, bias, agreement, calibration, and limitations.

### AI-specific evaluation — Retrieval, agents, safety

7. [RAG Evaluation: Retrieval and Groundedness](07-rag-retrieval-and-groundedness-evaluation.md) — recall@k, precision@k, MRR, citation validity, no-answer behaviour, and permission filtering.
8. [Agent, Tool and Workflow Evaluation](08-agent-tool-and-workflow-evaluation.md) — task success, tool selection, argument validation, authorisation, step budgets, and side effects.
9. [Safety, Red Teaming and Adversarial Evaluation](09-safety-red-teaming-and-adversarial-evaluation.md) — prompt injection, data leakage, misuse, unsafe tool actions, and hard safety gates.

### Measurement science — Trust the experiment

10. [Statistics, Confidence and Experiment Design](10-statistics-confidence-and-experiment-design.md) — sample size, uncertainty, paired comparisons, confidence intervals, and segmentation.
11. [Experiment Tracking and Reproducibility](11-experiment-tracking-and-reproducibility.md) — run manifests, model/prompt/dataset versions, baselines, and result comparison.

### Release and production operations

12. [CI Quality Gates and Regression Testing](12-ci-quality-gates-and-regression-testing.md) — fast PR suites, pre-release evaluations, pass/fail gates, and regression review.
13. [Tracing, Telemetry and Evaluation Observability](13-tracing-telemetry-and-evaluation-observability.md) — logs, metrics, traces, online quality signals, drift, alerts, and privacy controls.

### Capstone

14. [Evaluation Framework for a Developer Assistant](14-capstone-evaluation-framework-for-developer-assistant.md) — build a test dataset, deterministic checks, retrieval metrics, answer judging, agent evaluation, experiment tracking, and release gates.

## Important distinction: tests, evaluations and monitoring

| Activity | Main question | Example |
|---|---|---|
| Unit/contract test | Does application code behave correctly? | Reject an unauthorised document request |
| Offline evaluation | Is output quality acceptable on a controlled dataset? | Groundedness on 100 labelled questions |
| Red-team test | Does the system resist deliberate misuse? | Prompt injection cannot reveal restricted data |
| Regression test | Did a change make behaviour worse? | New prompt breaks citation quality |
| Production monitoring | Is the live service healthy and useful? | Latency, errors, feedback, token cost |

These activities support one another but are not interchangeable. A model judge cannot replace a deterministic permission test. Production metrics cannot replace a curated evaluation dataset.

## Recommended capstone

Build an evaluation framework for a Developer Support Assistant using synthetic runbooks and questions.

The framework should:
- Maintain versioned development, validation, and held-out datasets.
- Record expected source IDs, required facts, and forbidden behaviour.
- Run deterministic input, permission, schema, and tool-policy tests.
- Evaluate retrieval separately from answer generation.
- Use a human rubric for correctness, completeness, groundedness, and citations.
- Optionally use an LLM judge calibrated against human review.
- Evaluate agent tool choice and argument safety.
- Record model, prompt, dataset, evaluator, and application versions.
- Produce a report covering quality, safety, latency, and cost.
- Apply hard release gates for data leakage and unauthorised actions.

## Definition of done

- Test cases cover normal, ambiguous, no-answer, adversarial, and permission scenarios.
- Human annotation instructions are documented.
- Deterministic security tests run without a live model credential.
- Retrieval and generation scores are reported separately.
- Judge quality is compared with human labels.
- Results can be reproduced from a run manifest.
- CI has explicit quality and safety gates.
- Production telemetry avoids unnecessary sensitive content.
- Failures are reported with example IDs and root-cause categories—not only a single score.

## Skills checklist

- [ ] Turn product requirements into measurable evaluation criteria.
- [ ] Design and version a representative evaluation dataset.
- [ ] Write human annotation rubrics and review guidelines.
- [ ] Create deterministic tests using fake model adapters.
- [ ] Choose metrics that match the task.
- [ ] Calibrate and challenge an LLM judge.
- [ ] Measure retrieval quality and answer groundedness separately.
- [ ] Evaluate agent tool calls, arguments, permissions, and termination.
- [ ] Red-team prompt injection and data-access boundaries.
- [ ] Interpret score uncertainty and paired experiments.
- [ ] Track experiments so results are reproducible.
- [ ] Add regression and safety gates to CI.
- [ ] Monitor quality, latency, errors, and cost in production.

## Suggested pace

A first pass may take 3–5 weeks with regular practice. The capstone is more important than completing the lessons quickly. Build the evaluation set early and keep extending it when new failure patterns are discovered.

## Official references

- [OpenAI Evals](https://platform.openai.com/docs/guides/evals)
- [OpenAI evaluation guide](https://platform.openai.com/docs/guides/evals)
- [OpenTelemetry](https://opentelemetry.io/docs/)
- [MLflow Tracking](https://mlflow.org/docs/latest/ml/tracking/)
- [scikit-learn model evaluation](https://scikit-learn.org/stable/modules/model_evaluation.html)
- [Hugging Face Evaluate](https://huggingface.co/docs/evaluate/)
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)

Evaluation methods, model APIs, and framework capabilities change. Verify current documentation and record exact versions for reproducible experiments.
