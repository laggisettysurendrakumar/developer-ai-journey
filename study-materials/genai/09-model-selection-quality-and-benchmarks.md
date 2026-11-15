# 09 — Model Selection, Quality and Benchmarks

## There is no best model for every workload

Different models have different strengths, latencies, costs, context limits, modalities, and availability. Select a model using actual application requirements and representative tests.

## 1. Define the task before comparing models

Write down:
- What input the model receives.
- What output the application requires.
- What counts as a correct result.
- How much latency users can tolerate.
- The volume of requests.
- Data sensitivity and residency constraints.
- Cost limits.
- Required tools, modalities, context length, and structured output support.

## 2. Build an evaluation dataset

Create representative examples containing:
- Common cases.
- Difficult cases.
- Ambiguous questions.
- Missing information.
- Out-of-scope requests.
- Adversarial or prompt-injection attempts.

Label the expected behaviour. For generative tasks, evaluation may require rubrics or human review rather than exact text matching.

## 3. Useful quality dimensions

- Task correctness.
- Factuality and grounding.
- Completeness.
- Instruction adherence.
- Structured-output validity.
- Robustness to ambiguous input.
- Safety and policy compliance.
- Latency and cost.

A model may score well on a public benchmark but perform poorly on your application's domain data.

## 4. Compare consistently

Use the same dataset, prompt version, parameters, and evaluation rubric for each candidate. Repeat tests where output variability matters. Record model version and evaluation date because provider models can change.

## 5. Understand trade-offs

A smaller/cheaper model may be suitable for simple classification. A more capable model may be justified for complex reasoning or multimodal tasks. A pipeline can use different models for different steps, provided the design is tested and not made unnecessarily complex.

## Practice exercise

Choose three model candidates that are available to your project. Define an evaluation set of 20 synthetic tasks, score output quality, measure latency and usage cost, and document the final selection and trade-offs.

## Self-check

- Why should model selection begin with requirements?
- Why can public benchmark scores be insufficient?
- Which quality dimensions matter beyond fluent text?
- Why record the model version and prompt version?
- How can a multi-model design increase complexity?

## Official references

- OpenAI model documentation: https://platform.openai.com/docs/models
- HELM evaluation project: https://crfm.stanford.edu/helm/
- MLCommons benchmarks: https://mlcommons.org/
