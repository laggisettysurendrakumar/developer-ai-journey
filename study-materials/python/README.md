# Python for GenAI and Agentic AI Development — 14-Day Learning Path

I created this course to learn Python from the beginning and use it to build practical Generative AI (GenAI) and agentic applications. The aim is not to memorise Python syntax. I want to understand what the code is doing, why a feature is useful, how to debug it, and how the same idea appears in an AI application.

> **Who this is for:** A beginner who wants to write Python confidently enough to build API-backed GenAI applications, retrieval-augmented generation (RAG) systems, and tool-using AI agents. No previous Python experience is required.

## How to use this course
- Study one Markdown lesson each day, in order.
- Spend about 2–3 hours per day: read, type the examples, change the code, and complete the exercise.
- Use Python 3.11 or newer where possible. The course avoids relying on one specific AI framework.
- Create a small project folder and run the code locally. A notebook is useful for experiments, but learn to run normal `.py` files too.
- Keep API keys in environment variables or a local `.env` file that is excluded from Git. Never commit secrets.
- Do not copy code without understanding the inputs, outputs, errors, and side effects.

## Setup: prepare a Python workspace

Install Python from https://www.python.org/downloads/ or use the Python installation approved for your machine. Verify:

```bash
python --version
python -m pip --version
```

On some Windows installations use `py` instead of `python`:

```powershell
py --version
py -m pip --version
```

Create a course directory and virtual environment:

```bash
mkdir python-ai-course
cd python-ai-course
python -m venv .venv
```

Activate it:

```powershell
# Windows PowerShell
.venv\Scripts\Activate.ps1
```

```bash
# macOS / Linux
source .venv/bin/activate
```

A **virtual environment** is an isolated Python workspace for one project. It prevents packages installed for one project from interfering with another. When it is active, install packages with `python -m pip install package-name`. Use `deactivate` to leave it.

Create a `.gitignore` file containing:

```gitignore
.venv/
__pycache__/
*.py[cod]
.env
.ipynb_checkpoints/
.pytest_cache/
.ruff_cache/
```

## The 14-day course

| Day | Lesson | Main outcome |
|---|---|---|
| 01 | [Python setup, first program, variables and types](01-python-basics-and-setup.md) | Run scripts, store values and convert types |
| 02 | [Conditions, loops and problem solving](02-conditions-loops-and-logic.md) | Make decisions and repeat work safely |
| 03 | [Strings and data structures](03-strings-and-data-structures.md) | Work with text, lists, tuples, sets and dictionaries |
| 04 | [Functions, scope and reusable code](04-functions-and-reusable-code.md) | Break problems into reusable functions |
| 05 | [Modules, packages, environments and errors](05-modules-packages-and-errors.md) | Organise code and handle exceptions |
| 06 | [Files, JSON, CSV and configuration](06-files-json-and-configuration.md) | Read/write data and manage settings |
| 07 | [Object-oriented Python and type hints](07-oop-and-type-hints.md) | Model concepts and make code clearer |
| 08 | [Testing, debugging, logging and code quality](08-testing-debugging-and-logging.md) | Verify behaviour and investigate failures |
| 09 | [HTTP APIs, REST and Pydantic](09-http-apis-and-pydantic.md) | Build/call APIs and validate data |
| 10 | [Async Python and concurrency basics](10-async-python.md) | Understand asynchronous network work |
| 11 | [Data handling, NumPy and pandas basics](11-data-handling-for-ai.md) | Inspect and transform AI data |
| 12 | [LLM fundamentals, prompts and structured outputs](12-llm-foundations-and-prompts.md) | Call a model and validate outputs |
| 13 | [Embeddings, vector search, RAG and tool calling](13-embeddings-rag-and-tools.md) | Retrieve knowledge and expose controlled tools |
| 14 | [Build a tool-using AI agent](14-build-your-first-ai-agent.md) | Assemble a bounded agent prototype |

## The learning method I will follow

For every concept, ask: what is it, why does it exist, how does the code work, what output should I expect, what can fail, and where would I use it in an AI application?

## What I should know at the end
- Write, run, debug, test, and organise ordinary Python applications.
- Use dictionaries, lists, functions, classes, type hints, JSON, and environment variables.
- Build a small HTTP API and call external APIs safely.
- Understand async/await and why network-heavy AI applications use it.
- Explain an LLM, prompt, token, embedding, vector search, RAG, tool, and agent.
- Build a small AI application that calls tools through validated interfaces.
- Identify prompt injection, unsafe tool execution, secret leakage, unbounded loops, and excessive cost.

This is a focused foundation, not a promise of expert-level mastery in two weeks. Real proficiency comes from building, testing, operating, and improving several projects.

## Recommended tools and references
- Official Python tutorial: https://docs.python.org/3/tutorial/
- Python standard library: https://docs.python.org/3/library/
- Pydantic: https://docs.pydantic.dev/
- FastAPI: https://fastapi.tiangolo.com/tutorial/
- NumPy: https://numpy.org/doc/stable/user/quickstart.html
- pandas: https://pandas.pydata.org/docs/getting_started/index.html
- OpenAI API documentation: https://platform.openai.com/docs/
- OWASP Top 10 for LLM Applications: https://genai.owasp.org/llm-top-10/

Always check current provider documentation for available models, API methods, and pricing. SDKs and API interfaces can change.

## Suggested project structure

```text
python-ai-course/
├── .venv/
├── .env                 # local secrets; never commit
├── .gitignore
├── src/
│   └── app/
├── tests/
├── data/                # synthetic or public sample data only
├── notebooks/           # optional experiments
└── README.md
```

Start with simple single-file examples. Do not force every lesson into a large framework. Learn Python itself first, then apply it to AI.

## Course completion checklist
- [ ] I can explain variables, conditions, loops, and functions.
- [ ] I can choose between lists, tuples, sets, and dictionaries.
- [ ] I can organise code into modules and create a virtual environment.
- [ ] I can read JSON, manage configuration, and keep secrets out of Git.
- [ ] I can use classes, type hints, exceptions, logging, and automated tests.
- [ ] I can build and call a simple API.
- [ ] I understand async/await and can use it for network I/O.
- [ ] I can validate structured LLM output and handle API errors.
- [ ] I can explain embeddings, retrieval, RAG, and tool calling.
- [ ] I can build and test a small agent with bounded steps and safe tools.
