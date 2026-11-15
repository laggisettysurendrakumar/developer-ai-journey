# 06 — LLM-as-a-Judge: Design and Calibration

## What is an LLM judge?

An LLM judge is a model used to assess another model's output against a rubric. It can help review large sets of outputs for properties such as relevance, completeness, or groundedness.

A judge is itself a model and can make mistakes. It is not an independent source of truth.

## 1. Define the judging task narrowly

A good judge prompt should specify:
- The evaluation question.
- The rubric and score meaning.
- The source evidence or reference facts.
- The exact output structure.
- What to do when evidence is insufficient.
- Which aspects must be scored separately.

Avoid asking a judge simply whether an answer is "good."

## 2. Example judge prompt structure

```text
You are evaluating a support answer.

Question:
{{question}}

Approved source excerpts:
{{evidence}}

Candidate answer:
{{answer}}

Score groundedness from 1 to 5 using the rubric.
A score of 5 means all material claims are supported.
A score of 1 means material claims are unsupported or contradicted.

Return a score and short evidence-based rationale.
Do not reward claims that are absent from the approved evidence.
```

The judge output must also be parsed and validated. Keep its score and reasoning separate from the original answer.

## 3. Common judge biases

- Position bias: preferring the first or second answer in a comparison.
- Verbosity bias: rewarding longer answers.
- Style bias: preferring polished language over correctness.
- Self-preference: favouring outputs from the same model family.
- Prompt sensitivity: scores change when judge instructions change.
- Evidence blindness: judge accepts a claim without checking source support.

Mitigate with clear rubrics, randomised candidate order, blind model identity, and human review.

## 4. Calibrate against human labels

Select a sample that humans score carefully. Compare judge decisions with the human results. Inspect disagreement cases, not only overall correlation. Change the rubric or judge prompt when it systematically rewards the wrong behaviour.

Recalibrate when the judge model, prompt, dataset, or target task changes.

## 5. Use judges for suitable tasks

LLM judges can help assess open-ended answer qualities. Do not use a judge as the final authority for authentication, access control, exact calculations, destructive actions, or compliance decisions.

## Practice exercise

Create 20 synthetic question/evidence/answer examples. Ask a model to score groundedness using a defined rubric. Have a human reviewer score the same cases. Compare agreement and analyse cases where the judge missed unsupported claims.

## Self-check

- What does an LLM judge do?
- Why is a judge not ground truth?
- What is position bias?
- How can human labels calibrate a judge?
- Which decisions should never depend only on an LLM judge?

## Official references

- OpenAI Evals: https://platform.openai.com/docs/guides/evals
- G-Eval paper: https://arxiv.org/abs/2303.16634
