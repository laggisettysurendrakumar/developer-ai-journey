# 05 — Evaluation Metrics for Generated Answers

## Why one metric is not enough

A single score can hide important failures. A response may be relevant but wrong, complete but unsafe, or fluent but unsupported. Evaluate separate dimensions and decide which ones are critical for your task.

## 1. Common quality dimensions

| Dimension | Question |
|---|---|
| Correctness | Are the claims factually correct? |
| Relevance | Does the answer address the question? |
| Completeness | Does it include required information? |
| Groundedness | Are claims supported by supplied evidence? |
| Citation correctness | Do the cited sources support the statements? |
| Instruction adherence | Did the output follow the requested constraints? |
| Helpfulness | Can the user take the intended next step? |
| Safety | Did it avoid unsafe or disallowed behaviour? |

Define operational rules for each dimension and supply examples of high-, medium-, and low-quality outputs.

## 2. Classification metrics

For classification, common measures include:
- Accuracy: proportion of all predictions that are correct.
- Precision: of predictions for a class, how many are correct.
- Recall: of actual items in a class, how many were found.
- F1: harmonic mean of precision and recall.

For imbalanced classes, accuracy can be misleading. Inspect per-class results and confusion matrices.

## 3. Text overlap metrics

BLEU, ROUGE, and similar metrics compare output with reference text using overlap patterns. They can be useful for some constrained tasks, but semantically equivalent wording may score poorly and fluent wrong answers may score well.

Do not treat text overlap as a universal quality measure for open-ended answers.

## 4. Pairwise comparison

Reviewers or an evaluator compare two outputs for the same input and select the better one based on a rubric. Pairwise comparison can be easier than assigning absolute scores, but it still needs clear criteria, randomised ordering, and adjudication.

## 5. Example deterministic metric

```python
from sklearn.metrics import classification_report

expected = ["network", "database", "network", "other"]
predicted = ["network", "database", "other", "other"]

print(classification_report(expected, predicted, zero_division=0))
```

Install scikit-learn in your environment before running. The report provides per-class precision, recall, F1, and support.

## 6. Set thresholds based on risk

A support assistant may require high citation correctness. A ticket classifier may prioritise recall for a critical incident class. A customer-facing response may require strict privacy and safety gates. Thresholds must be tied to the use case and validated against representative data.

## Practice exercise

Create a small labelled dataset for classifying support incidents. Calculate precision, recall, and F1 per class. Then write a rubric for generated explanations and explain why classification metrics cannot fully evaluate explanation quality.

## Self-check

- Why can accuracy be misleading for imbalanced classes?
- What does precision measure?
- What does recall measure?
- Why can ROUGE be insufficient for open-ended answers?
- Why should thresholds vary by application risk?

## Official references

- scikit-learn metrics: https://scikit-learn.org/stable/modules/model_evaluation.html
- Hugging Face Evaluate: https://huggingface.co/docs/evaluate/
