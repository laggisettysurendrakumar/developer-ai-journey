# Day 9 — HTTP APIs, REST and Pydantic

## Goal for today

GenAI applications communicate with model providers, internal services, and tools over APIs. I will learn the HTTP request/response model, call an API, and validate structured data with Pydantic.

## 1. What is an API?

An API is a defined way for one program to request something from another program. A web API commonly uses HTTP.

A request can contain:
- A method such as GET or POST.
- A URL identifying the resource.
- Headers such as content type and authentication.
- A body containing data, often JSON.

A response contains a status code, headers, and sometimes a body.

## 2. Common HTTP methods and status codes

| Method | Typical purpose |
|---|---|
| GET | Read information |
| POST | Submit data or request an operation |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Remove a resource |

Status code groups:
- 2xx: request succeeded.
- 3xx: redirect.
- 4xx: client, authentication, permission, or request issue.
- 5xx: server or upstream failure.

A 429 commonly indicates rate limiting. Respect retry guidance and headers rather than retrying aggressively.

## 3. Call a sample API with requests

Install:

```bash
python -m pip install requests
```

```python
import requests

response = requests.get(
    "https://jsonplaceholder.typicode.com/todos/1",
    timeout=10
)

response.raise_for_status()
todo = response.json()
print(todo["title"])
```

- `requests.get()` sends an HTTP GET.
- `timeout=10` prevents waiting forever.
- `raise_for_status()` raises an exception for unsuccessful status codes.
- `json()` parses the response body as JSON.

This uses a public demonstration endpoint. Do not send company or personal data to public test services.

## 4. Send a JSON request

```python
import requests

payload = {"question": "What is an AI agent?"}
response = requests.post(
    "https://httpbin.org/post",
    json=payload,
    timeout=10
)
response.raise_for_status()
print(response.json()["json"])
```

The example service echoes submitted data. It is for learning only.

## 5. Pydantic: validate incoming data

Install:

```bash
python -m pip install pydantic
```

```python
from pydantic import BaseModel, Field

class QuestionRequest(BaseModel):
    question: str = Field(min_length=1, max_length=2_000)
    language: str = "English"

request = QuestionRequest(
    question="Explain retrieval augmented generation",
    language="English"
)

print(request.question)
```

Pydantic validates data against a model. Invalid input raises a validation error. This is useful for tool arguments and API request bodies. A valid shape does not prove the information is true or the user is authorised.

## 6. Create a small FastAPI endpoint

Install:

```bash
python -m pip install fastapi uvicorn
```

Create `api.py`:

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()

class AskRequest(BaseModel):
    question: str = Field(min_length=1, max_length=2_000)

@app.post("/ask")
def ask(request: AskRequest):
    return {"message": f"You asked: {request.question}"}
```

Run locally:

```bash
uvicorn api:app --reload
```

The endpoint is a demonstration; it does not call an LLM yet. FastAPI generates interactive API documentation while the local server is running.

## 7. API design habits

- Validate data at the API boundary.
- Set connection and read timeouts.
- Return clear error codes and messages.
- Keep secrets out of responses and logs.
- Do not expose stack traces to users.
- Apply authentication, authorisation, and rate limits before exposing an endpoint publicly.

## Practice exercise

Create `POST /questions` that accepts a question and language using a Pydantic model. Reject blank questions and return a response object. Test it through the local API documentation.

## Self-check

1. What are the main parts of an HTTP request?
2. Why should network calls have timeouts?
3. What does HTTP 429 usually indicate?
4. Why validate tool arguments before execution?

**Done for today:** I can build and call a basic API and validate its input.