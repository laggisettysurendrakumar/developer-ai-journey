# 02 — How LLMs Work: Training, Inference and Transformers

## Two different stages: training and inference

An LLM is created through a training process and then used through inference.

**Training** is the process of adjusting a model's parameters using large datasets and an optimisation objective. Training requires substantial data and compute and is usually performed by the model developer.

**Inference** is what happens when an application sends input to an already trained model and receives generated output.

When a developer calls a model API, they are usually performing inference—not training the foundation model.

## 1. Tokens and tokenisation

Models do not normally read text exactly as humans see it. Tokenisation converts text into smaller units called tokens. A token may be a whole word, a piece of a word, punctuation, or another unit depending on the tokenizer.

This matters because:
- Model context limits are measured in tokens.
- Input and output usage may be billed by tokens.
- Long prompts can increase latency and cost.
- Different languages and content types can have different tokenisation patterns.

Do not assume one word equals one token. Use the selected provider's tokenizer tools or usage information when estimating.

## 2. Embeddings inside neural models

The model maps tokens into numeric vectors that can be processed by neural network layers. These vectors are not simple dictionary definitions; they are learned representations used by the model.

As information passes through the network, the model calculates contextual representations that help it determine which next tokens are likely under its learned parameters.

## 3. Transformer architecture

Transformers are neural network architectures that use attention mechanisms to process relationships between tokens. Self-attention allows a token's representation to incorporate information from other tokens in the input sequence.

At a high level:
1. Text is tokenised.
2. Tokens are mapped to numeric representations.
3. Transformer layers process the representations.
4. The model calculates a distribution over possible next tokens.
5. A decoding strategy selects tokens to continue the output.

This is a simplified conceptual view; model architectures and inference strategies differ.

## 4. Next-token generation

Many language models generate text one token at a time. Each generated token becomes part of the context for producing the next token, until the model reaches a stopping condition or output limit.

That helps explain why the same prompt can produce different outputs when sampling settings are changed, or when a model is nondeterministic.

## 5. Pretraining, fine-tuning and alignment

- **Pretraining:** learning broad patterns from a large training corpus.
- **Fine-tuning:** further training on a more focused dataset or behaviour.
- **Instruction tuning:** training to follow instructions more effectively.
- **Preference/alignment methods:** techniques intended to shape model behaviour toward desired outputs.

These terms describe different stages or approaches. They are not interchangeable, and a provider may not disclose the exact training recipe for a model.

## 6. Why LLMs can make mistakes

An LLM generates content based on learned patterns and context. It may:
- Produce facts that sound plausible but are false.
- Misread ambiguous instructions.
- Miss information absent from its context.
- Generate invalid code or JSON.
- Reflect patterns or bias in its training data.
- Be influenced by malicious instructions embedded in input.

Use sources, retrieval, tests, validation, and human review according to the risk of the task.

## Practice exercise

Explain the journey of a short user prompt from text to tokens, model processing, next-token generation, and final output. Then write three examples of mistakes an application should detect after inference.

## Self-check

- What is the difference between training and inference?
- Why are tokens important for usage and cost?
- What does self-attention help a transformer model do?
- Why can an LLM produce plausible false statements?
- What work remains the application's responsibility after inference?

## Official references

- OpenAI text generation guide: https://platform.openai.com/docs/guides/text
- Attention Is All You Need (paper): https://arxiv.org/abs/1706.03762
- Hugging Face NLP course: https://huggingface.co/learn/nlp-course/
