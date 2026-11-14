# Day 12 — LLM foundations, prompts and structured outputs

## Goal for today

I will connect Python skills to Generative AI. I will understand what an LLM does, how an API call fits into a Python application, and why model output must be treated as data that needs validation.

## 1. What is an LLM?

A large language model (LLM) is a model trained to process and generate language. It can summarise, explain, classify, draft, and transform text. It does not automatically know private company information or have permission to perform actions.

The Python application is responsible for authentication, input validation, sending the request, checking the response, managing errors, and deciding what happens next.

## 2. Important terms

| Term | Meaning |
|---|---|
| Prompt | Instructions and information sent to a model |
| Token | A unit of text used by a model; not exactly the same as a word |
| Context window | Amount of input and output the model can handle in a request |
| System/developer instruction | Higher-priority behaviour guidance, depending on the API |
| User message | The question or content supplied by the user |
| Response | The model's generated output |
| Temperature | A generation setting that can influence variation where supported |
| Structured output | Output constrained to a defined data shape, where supported |
| Tool call | A model request for the application to invoke a named tool with arguments |

Exact names and capabilities vary by model and provider. Read current documentation before building against an API.

## 3. Write a clear prompt

A useful prompt often states the task, relevant context, output format, constraints, and what to do when information is missing.

```python
prompt = """
Task: Summarise the supplied support note.
Rules:
- Use only the supplied note.
- Separate confirmed facts from assumptions.
- If a detail is missing, say it is not provided.
Output:
- Summary
- Risks
- Next action
"""
```

This is a Python string. A real application sends it to a model API. Clear instructions improve consistency but do not guarantee correctness.

## 4. Understand message roles

Many chat interfaces use messages with roles:

```python
messages = [
    {"role": "system", "content": "You are a careful technical assistant."},
    {"role": "user", "content": "Explain virtual environments."}
]
```

The provider's exact role names and formats may differ. Treat user input and external documents as untrusted data. A prompt is not a security boundary.

## 5. Make a model request with the OpenAI Python SDK

Install the current SDK in the virtual environment:

```bash
python -m pip install openai
```

Set an API key as an environment variable. Do not paste it into the Python file or commit it to Git.

```powershell
# Windows PowerShell — current terminal session
$env:OPENAI_API_KEY = "YOUR_API_KEY"
```

```bash
# macOS / Linux — current terminal session
export OPENAI_API_KEY="YOUR_API_KEY"
```

A minimal Responses API example:

```python
from openai import OpenAI

client = OpenAI()  # Reads OPENAI_API_KEY from the environment.

response = client.responses.create(
    model="MODEL_AVAILABLE_TO_YOUR_ACCOUNT",
    input="Explain what a Python virtual environment is in simple terms."
)

print(response.output_text)
```

Replace the model placeholder with a model currently available to your account. This example incurs API usage and may cost money. Check current pricing and usage limits before running it. The API response may include more structured items than plain text; the SDK's `output_text` helper returns the text output for this simple example.

## 6. Structured output and validation

Plain text is easy for a person to read but harder for a program to depend on. Structured output can make responses easier to validate and consume.

```python
from pydantic import BaseModel

class TicketSummary(BaseModel):
    summary: str
    severity: str
    next_action: str

raw_output = {
    "summary": "API requests are timing out.",
    "severity": "high",
    "next_action": "Check dependency connectivity and recent changes."
}

summary = TicketSummary.model_validate(raw_output)
print(summary.severity)
```

This code validates a Python dictionary; it does not call an LLM. Use the same kind of validation after parsing a model response. A valid data shape does not prove the content is true.

## 7. Hallucination and verification

A hallucination is generated information that sounds plausible but is false or unsupported. Reduce risk by supplying trustworthy context, asking the model to identify missing information, using retrieval for private/changing knowledge, validating important facts, and showing sources where possible.

## 8. Evaluate a prompt

Test whether the response follows the required format, avoids unsupported claims, handles missing data, protects private information, and remains useful with short, long, and malformed input.

## Practice exercise

Create a function that accepts a ticket dictionary and returns a Pydantic-validated summary. Start with hard-coded sample output. Then describe how an LLM could generate the fields and why validation is still needed.

## Self-check

1. What does an LLM do?
2. What is a token?
3. Why is a prompt not a security boundary?
4. Why does valid JSON not guarantee a correct answer?
5. Why should API keys come from secure configuration?

**Done for today:** I can explain a model request, validate structured output, and identify common LLM risks.

Further reading: https://platform.openai.com/docs/