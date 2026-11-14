# Day 8 — Testing, debugging, logging and code quality

## Goal for today

Code that works once is not automatically reliable. I will learn to test expected behaviour, debug failures, and record useful events. This matters in AI systems, where model responses and network calls can be unpredictable.

## 1. Assertions: check an expectation

```python
def clean_question(value: str) -> str:
    return value.strip()

assert clean_question("  hello  ") == "hello"
assert clean_question("") == ""
```

An assertion checks a condition. It is useful for quick learning checks, but production input validation should use explicit checks and exceptions. Do not rely on assertions for security controls.

## 2. Unit tests with unittest

Create `test_text_utils.py`:

```python
import unittest
from text_utils import clean_question

class CleanQuestionTests(unittest.TestCase):
    def test_removes_surrounding_spaces(self):
        self.assertEqual(clean_question("  hello  "), "hello")

if __name__ == "__main__":
    unittest.main()
```

Run `python -m unittest`. A unit test checks one small piece of behaviour in isolation.

## 3. pytest

Install it in the virtual environment:

```bash
python -m pip install pytest
python -m pytest
```

A test can be very small:

```python
def test_word_count():
    assert len("AI agents use tools".split()) == 4
```

Use test filenames such as `test_*.py`. Keep unit tests deterministic; do not call a paid model API from every ordinary test.

## 4. Debug step by step

When code behaves unexpectedly:
1. Reproduce the issue with the smallest input.
2. Inspect the type and value of important variables.
3. Read the traceback and identify the line that raised the exception.
4. Verify assumptions about external data.
5. Add a test for the corrected behaviour.

A debugger lets me pause execution and inspect variables. Avoid logging sensitive values while debugging.

## 5. Logging: a record of what happened

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s %(message)s"
)
logger = logging.getLogger(__name__)

logger.info("Starting document processing")
logger.warning("Retrying a temporary operation")
logger.error("Document processing failed")
```

Logging levels commonly include DEBUG, INFO, WARNING, ERROR, and CRITICAL.

**AI use case:** Record that a request started, a tool was selected, retrieval found three chunks, or a model call failed. Do not log API keys, private prompts, or full customer documents by default.

## 6. Catch errors at the right boundary

```python
import logging

logger = logging.getLogger(__name__)

def run_task() -> None:
    try:
        perform_task()
    except TimeoutError:
        logger.warning("Task timed out; a safe retry may be possible")
        raise
```

The example re-raises the error after recording it. The caller can decide whether to retry or stop. Retry only temporary failures, and set a maximum number of attempts.

## 7. Formatting and static checks

Useful development tools:
- `ruff`: linting and formatting.
- `mypy` or `pyright`: static type checking.
- `pytest`: testing.

```bash
python -m pip install ruff
ruff check .
ruff format .
```

Use tools consistently in a project. Clean style makes code review easier.

## 8. How to test AI behaviour

Model output can vary, so avoid expecting exactly the same sentence every time. Test whether:
- Required fields are present.
- JSON can be parsed and validated.
- A tool call uses an allowed name and valid arguments.
- An error or refusal path is handled.
- Retrieval returns expected document IDs for fixed sample data.
- The agent stops at its maximum step count.

This is the beginning of AI evaluation: measuring whether the system meets defined requirements.

## Practice exercise

Write tests for a function that rejects blank prompts and limits prompt length. Add logging for success and validation failure. Ensure the tests do not need an API key or internet access.

## Self-check

1. What is a unit test?
2. Why should unit tests avoid real paid API calls?
3. What belongs in a useful log?
4. Why is it dangerous to log secrets or full user prompts?

**Done for today:** I can test ordinary logic and diagnose failures without relying only on print statements.