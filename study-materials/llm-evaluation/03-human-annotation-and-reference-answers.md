# 03 — Human Annotation and Reference Answers

## Why human review is still important

Many GenAI tasks do not have one exact correct sentence. Two answers may use different wording and both be useful. Human annotation provides a way to judge meaning, evidence, completeness, and safety.

Automatic metrics and model-based judges can help scale the process, but humans are needed to define what good means and calibrate the automated process.

## 1. Create an annotation rubric

A rubric describes how reviewers should score output. Keep each dimension clear and independent.

Example 1–5 scale for groundedness:
- 1: major claims are unsupported or contradicted.
- 2: some support exists, but important claims are not grounded.
- 3: most important claims have partial support.
- 4: claims are supported, with only minor omissions.
- 5: all material claims are clearly supported by permitted sources.

Include a "not applicable" option where needed. Do not force a numeric score when the criterion cannot be judged.

## 2. Reference answers versus reference properties

For constrained tasks, an expected label or exact structured object may be suitable. For open-ended answers, a reference answer can be too restrictive.

Instead record:
- Facts that must be present.
- Claims that must not be made.
- Sources that support the answer.
- Important omissions.
- Acceptable alternative wording.
- Whether clarification or refusal is expected.

## 3. Reviewer instructions

Reviewers should:
1. Read the question and permitted context.
2. Read the model output.
3. Check claims against source material.
4. Score each rubric dimension independently.
5. Record the evidence for a low score.
6. Flag safety or access violations separately.
7. Mark uncertain cases for adjudication.

## 4. Inter-annotator agreement

When more than one reviewer labels cases, measure whether they agree. Disagreement may reveal ambiguous criteria, poor examples, or genuine uncertainty in the task. Discuss disagreements and revise the rubric before comparing model versions.

For categorical labels, Cohen's kappa or other agreement measures may be appropriate depending on the number of reviewers and label design. For ordinal or multiple-reviewer settings, use a measure suited to the data. Do not use one agreement score without understanding its assumptions.

## 5. Adjudication and reviewer bias

Create a process for resolving disagreements. Reviewers should not know which model version produced an answer when practical, because brand or version expectations can bias scores. Randomise answer order in pairwise comparisons where possible.

## Practice exercise

Take ten synthetic support questions and generate two answer variants. Write a rubric for correctness, completeness, groundedness, and safety. Ask two reviewers to score independently, compare disagreements, and refine the rubric.

## Self-check

- Why are exact reference strings unsuitable for many generative tasks?
- What is a rubric?
- Why should safety violations be separated from average quality scores?
- What does inter-annotator agreement help reveal?
- How can blind review reduce bias?

## Official references

- NIST AI RMF: https://www.nist.gov/itl/ai-risk-management-framework
- Cohen's kappa overview: https://scikit-learn.org/stable/modules/generated/sklearn.metrics.cohen_kappa_score.html
