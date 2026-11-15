# 03 — Tokens, Context Windows and Generation Settings

## Why prompt length matters

A model request includes more than the latest user message. It may contain system instructions, conversation history, examples, retrieved documents, tool descriptions, and the requested output. All of this consumes context capacity.

A context window is the amount of token content a model can consider and generate in a request, subject to the provider's exact counting and limits.

## 1. Context budget

A simplified budget is:

```text
System instructions
+ conversation history
+ user input
+ retrieved context
+ tool definitions/results
+ generated output
<= model request limits
```

The exact limits depend on the selected model and API. Do not assume every model has the same context window or output limit.

## 2. Token usage and cost

Many providers report input and output token usage separately. Some pricing models also distinguish cached input, reasoning tokens, image/audio processing, or other usage categories.

Track actual API usage rather than estimating from character count alone. Keep a budget for:
- Prompt and system instructions.
- Conversation history.
- Retrieved content.
- Tool definitions and tool results.
- Model output.

## 3. Generation settings

Common controls can include:
- Maximum output tokens: limit the length of generated content.
- Temperature: affects sampling variability where supported.
- Top-p: sampling cutoff based on cumulative probability where supported.
- Stop sequences: configured strings or conditions that stop generation.
- Structured-output constraints: constrain output format for supported models/APIs.

These parameters are model/API-specific. Not every parameter applies to every model. Avoid changing settings without testing how output quality changes for your task.

## 4. Temperature is not a truth setting

A lower temperature can make output more consistent for some sampling setups. It does not make an answer factually correct. A higher temperature can increase variety, but may reduce consistency for tasks that need stable output.

Use task-specific evaluation rather than assuming one universal setting.

## 5. Managing long conversations

Long histories may waste tokens or exceed a model limit. Strategies include:
- Keep only recent relevant turns.
- Summarise older conversation state carefully.
- Retrieve facts from a controlled store when needed.
- Separate durable application state from conversational wording.
- Preserve source and timestamp metadata for remembered facts.
- Avoid carrying forward untrusted instructions from previous inputs.

## Practice exercise

Design a prompt budget for a support assistant. Allocate a hypothetical context among system instructions, recent history, retrieved documents, and output. Explain what the application should do when the request exceeds the budget.

## Self-check

- What content consumes context capacity?
- Why should input and output tokens be measured separately?
- Does lower temperature guarantee correctness?
- How can an application handle a long conversation?
- Why should a summary of older history be treated as potentially lossy?

## Official references

- OpenAI models: https://platform.openai.com/docs/models
- OpenAI API pricing: https://openai.com/api/pricing/
- Tokenizer tool and guidance: https://platform.openai.com/tokenizer
