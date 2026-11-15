# 11 — Experiment Tracking and Reproducibility

## Why results must be reproducible

An evaluation score is useful only when the team knows what produced it. If the dataset, model, prompt, parameters, or retrieval index changes without being recorded, comparing results becomes unreliable.

## 1. Track the complete configuration

Record:
- Application commit/version.
- Model provider and model/deployment identifier.
- Model version where available.
- Prompt template version or hash.
- Generation parameters.
- Retrieval configuration and index version.
- Embedding model and version.
- Dataset version.
- Evaluator version and rubric.
- Run timestamp and environment.
- Results, errors, latency, and usage.

Do not store credentials or restricted prompt content in experiment metadata.

## 2. Use a run manifest

```json
{
  "run_id": "eval-2026-001",
  "dataset_version": "support-cases-v3",
  "prompt_version": "support-prompt-v5",
  "model_id": "approved-model-deployment",
  "temperature": 0,
  "application_commit": "abc1234",
  "evaluator_version": "rubric-v2"
}
```

These are illustrative identifiers, not actual deployed model names.

## 3. Compare runs

A comparison should show overall and per-category scores, hard safety failures, example-level regressions, latency, token use, cost, and errors.

Do not promote based on one overall number.

## 4. Store artifacts safely

Evaluation outputs may contain sensitive user content. Store only what is needed, protect access, and apply retention/deletion rules. For public learning projects, use synthetic data.

## Practice exercise

Create a run manifest and a simple results file for two prompt versions. Compare case-level outcomes and identify the ten largest changes. Record the reasons for accepting or rejecting the new version.

## Self-check

- Why record the application commit?
- Why track the dataset and evaluator version?
- What is a prompt version/hash useful for?
- Why should evaluation output storage be protected?
- What information belongs in a run manifest?

## Official references

- MLflow tracking: https://mlflow.org/docs/latest/ml/tracking/
- Weights & Biases experiments: https://docs.wandb.ai/
