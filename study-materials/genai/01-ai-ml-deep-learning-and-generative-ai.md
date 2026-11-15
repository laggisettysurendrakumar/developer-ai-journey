# 01 — AI, Machine Learning, Deep Learning and Generative AI

## Start with the big picture

Artificial Intelligence (AI) is the broad area of building software that performs tasks commonly associated with human intelligence, such as recognising patterns, understanding language, planning, or making predictions.

There are different ways to build an AI system. Some use explicit rules written by developers. Others learn patterns from data. Generative AI is a category of systems that can create new content based on patterns learned during training and the input they receive.

## 1. Rule-based software

A rule-based program follows conditions explicitly written by a developer.

```python
def classify_temperature(celsius: float) -> str:
    if celsius < 0:
        return "Freezing"
    if celsius < 20:
        return "Cool"
    return "Warm"
```

The behaviour is predictable because the rules are visible. This is often the right approach for business rules, validation, permissions, and calculations.

## 2. Machine learning

Machine learning uses data to train a model to recognise patterns or make predictions. Instead of writing every decision rule manually, developers provide training data and a learning algorithm.

Examples:
- Predicting whether a transaction is unusual.
- Classifying an email as spam or not spam.
- Forecasting demand from past observations.

The model's performance depends on training data, features, learning method, and how it is evaluated.

## 3. Deep learning

Deep learning is a part of machine learning that uses neural networks with multiple layers. Deep learning has been important in image recognition, speech processing, and modern language models.

A neural network processes numeric representations through learned parameters. Training adjusts those parameters to reduce a defined error objective.

## 4. Generative AI

Generative AI systems create outputs such as:
- Text and code.
- Images and audio.
- Video and other media.
- Structured content such as JSON-like objects.

A generative system does not simply look up a prewritten answer. It produces an output conditioned on the input, the model's learned parameters, and any context supplied by the application.

## 5. Large language models

A Large Language Model (LLM) is a model trained to process and generate language. Many modern LLMs use transformer architectures and predict likely next tokens or otherwise generate token sequences under a model-specific objective.

An LLM can help with drafting, summarisation, classification, translation, code assistance, and question answering. It can also produce plausible but incorrect claims. Treat it as a probabilistic component, not an authoritative database or a replacement for business logic.

## 6. Where GenAI fits in an application

A useful mental model is:

```text
User request
    |
    v
Application validation and policy
    |
    v
Prompt + allowed context
    |
    v
Model inference
    |
    v
Output parsing and validation
    |
    v
Business rules + response to user
```

The model is one component. Authentication, authorisation, input validation, output validation, persistence, monitoring, and error handling remain responsibilities of the application.

## 7. When not to use generative AI

Use ordinary code for exact arithmetic, access control, deterministic decisions, and workflows with known steps. Generative AI is useful when language or flexible interpretation adds value, but introduces latency, cost, variability, and quality risk.

## Practice exercise

Choose five tasks from a normal business application. For each, decide whether deterministic code, machine learning prediction, or generative AI is the best starting point. Explain the trade-off.

## Self-check

- How is AI broader than machine learning?
- What is the relationship between machine learning and deep learning?
- What makes generative AI different from a fixed rule-based function?
- Why should an LLM not be the authority for application permissions?
- Give an example where ordinary code is better than a model.

## Official references

- AI concepts: https://learn.microsoft.com/en-us/training/paths/get-started-with-artificial-intelligence/
- Deep learning overview: https://www.ibm.com/think/topics/deep-learning
- OpenAI platform docs: https://platform.openai.com/docs/
