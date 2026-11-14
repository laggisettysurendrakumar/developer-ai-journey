# Generative AI Fundamentals

I created this learning track to understand the topic from the beginning and apply it to real GenAI and agentic AI applications. Each concept is explained in plain language, followed by code, why it is useful, a developer use case, and an exercise.

## Who should use this guide
Developers who know basic Python and want to understand how applications use generative models. Start after the Python course, especially Days 1–12.

## Before starting
Complete the Python fundamentals course through Day 12. Understand HTTP requests, JSON, environment variables, and Pydantic validation.

## Learning sequence
1. Understand AI, machine learning, deep learning, generative AI, and LLMs.
2. Learn tokens, tokenisation, context windows, model limits, and why text length matters.
3. Understand model inputs, roles, responses, and the request/response lifecycle.
4. Call a model API from Python using environment variables and timeouts.
5. Work with structured outputs and validate responses.
6. Learn embeddings and multimodal inputs at a conceptual level.
7. Build a small summarisation/classification API and test its failure cases.

## Important concepts in plain language
### Artificial intelligence (AI)
AI is the broad field of building systems that perform tasks associated with human intelligence. Machine learning is one approach within AI: instead of writing every rule manually, we train a model using data.

### Generative AI
Generative AI creates new content such as text, code, images, audio, or structured data based on learned patterns and the input it receives.

### Large language model (LLM)
An LLM is a model designed to process and generate language. It can draft or summarise, but it can also produce incorrect information. The application must validate important outputs.

### Token
A token is a unit used by a model to represent text. Tokens are not the same as words; punctuation and word pieces may be separate tokens. Token counts affect context limits, latency, and usage costs.

### Context window
The context window is the amount of input and generated output a model can handle in one request. Long conversation history and large documents use this capacity.

### Inference
Inference is the act of using a trained model to produce an output for a particular input.

### Embedding
An embedding is a numeric vector representing text or other content in a way that can support similarity search. See the RAG and vector database tracks.

## Practical examples
Example — validate a model-like response before using it
The example uses a fixed dictionary rather than a paid model call. It shows why an application should validate the shape of data returned by an external model.
```python
from pydantic import BaseModel, Field

class Summary(BaseModel):
    title: str = Field(min_length=1)
    key_points: list[str]

raw_response = {
    "title": "Linux service investigation",
    "key_points": ["Check service state", "Read recent logs"]
}

summary = Summary.model_validate(raw_response)
print(summary.title)
for point in summary.key_points:
    print("-", point)
```
Use case: an incident summariser returns a predictable object that a web UI can render. Validation checks the structure, not whether the model's facts are true.
Example — model API boundary (provider-neutral pseudocode)
The exact SDK differs by provider. Keep the model call behind a small function so the rest of the application does not depend on a specific vendor's response format.
```python
def generate_answer(client, model_name: str, prompt: str) -> str:
    # Pseudocode: replace with the selected provider's current SDK.
    response = client.generate(model=model_name, input=prompt)
    return response.text
```
Use case: switch model providers or mock the model in unit tests without rewriting your business logic.

## Mini-project
Build a small Text Processing API: accept a question and a task (`summarise`, `classify`, or `rewrite`), validate it, call a selected model through one adapter, validate structured output, and return a clear error for timeout, rate limit, or malformed responses. Use synthetic sample data.

## Safety, quality, and cost
- Never treat generated content as guaranteed truth.
- Never place API keys in source code, notebooks, logs, browser code, or Git.
- Set request timeouts and handle rate limits; do not retry indefinitely.
- Check data handling, retention, residency, and provider terms before sending business or personal data.
- Track token usage, latency, and estimated cost.

## Suggested practice routine
1. Read one section and explain the idea in your own words.
2. Type the example instead of copying it blindly.
3. Change one input and predict the output before running it.
4. Test an invalid input and inspect the error.
5. Complete the exercise and record what you learned.

## References
- Python tutorial: https://docs.python.org/3/tutorial/
- OpenAI API documentation: https://platform.openai.com/docs/
- OpenAI text generation guide: https://platform.openai.com/docs/guides/text
- OpenAI embeddings guide: https://platform.openai.com/docs/guides/embeddings

Documentation and APIs change. Check the linked official documentation for current commands, SDKs, model availability, versions, and pricing before using an example in a real project.

## Self-check

1. How is generative AI different from traditional rule-based software?
2. What is an LLM, and what does inference mean?
3. Why are tokens not the same as words?
4. What happens when the request exceeds a model's context window?
5. Why should the application validate a model's structured response?
6. Which information should never be placed in prompts or logs?

## Practice checklist

- [ ] I can explain tokens, context windows, inference, and embeddings in my own words.
- [ ] I can call a model through a small adapter function.
- [ ] I can validate an expected response shape.
- [ ] I can handle missing credentials, timeouts, malformed data, and rate limits.
- [ ] I can estimate usage and check provider pricing before running a real request.

**Next:** Study prompt engineering, then vector search and RAG.

