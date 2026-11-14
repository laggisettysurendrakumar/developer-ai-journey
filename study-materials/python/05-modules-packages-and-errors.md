# Day 5 — Modules, packages, virtual environments and errors

## Goal for today

I will organise code into files, install libraries safely, and handle expected failures. Real AI projects call external systems, so network failures, invalid input, and missing configuration are normal situations to plan for.

## 1. Modules: split code into files

A module is a Python file that can be imported by another Python file.

Create `text_utils.py`:

```python
def clean_text(value: str) -> str:
    return value.strip()
```

Create `main.py` in the same folder:

```python
from text_utils import clean_text

question = "  Explain Python modules  "
print(clean_text(question))
```

Run `python main.py`. Python loads the function from the other file. This keeps related logic together.

Avoid naming your files after standard library modules such as `json.py`, `typing.py` or `asyncio.py`.

## 2. Packages and pip

A package distributes reusable code. `pip` installs packages from configured package indexes.

```bash
python -m pip install requests
python -m pip show requests
python -m pip freeze
```

Using `python -m pip` helps ensure I install into the Python interpreter I intend to use.

## 3. Virtual environments

Different projects may need different versions of a library. A virtual environment isolates the packages used by one project.

```bash
python -m venv .venv

# Windows PowerShell
.venv\\Scripts\\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

After activation:

```bash
python -m pip install requests
python -m pip freeze > requirements.txt
```

A teammate can install the listed packages with `python -m pip install -r requirements.txt`. For production, review and pin dependencies according to the project's policy.

## 4. Exceptions: errors Python can raise

An exception signals that an operation could not complete normally.

```python
try:
    number = int("not-a-number")
except ValueError:
    print("Please provide a whole number.")
```

The conversion raises `ValueError`; the `except` block handles that known failure.

## 5. Use finally or a context manager for cleanup

```python
file_handle = None

try:
    file_handle = open("example.txt", encoding="utf-8")
    print(file_handle.read())
finally:
    if file_handle is not None:
        file_handle.close()
```

The `finally` block runs whether the operation succeeds or fails. For files, prefer a `with` statement, which closes the file automatically (Day 6).

## 6. Raise a useful exception

```python
def require_question(question: str) -> str:
    cleaned = question.strip()
    if not cleaned:
        raise ValueError("A question is required.")
    return cleaned

try:
    print(require_question("   "))
except ValueError as error:
    print(f"Input validation failed: {error}")
```

A clear error tells the caller what needs correction.

## 7. Do not hide every error

Avoid silently swallowing errors:

```python
try:
    call_service()
except Exception:
    pass
```

This hides the reason for failure. Handle specific errors I can recover from. Unexpected errors should be logged and reported to the appropriate boundary.

## 8. Read an environment variable

```python
import os

environment = os.getenv("APP_ENV", "development")
print(environment)
```

An environment variable lets deployment provide configuration without hardcoding it in source. For secrets, use a secret manager or secure environment injection. A local `.env` file must be excluded from Git.

## Practice exercise

Create `validators.py` with a function that rejects blank questions, then import it from `main.py`. Add a virtual environment and a requirements file. Try a valid and an invalid question.

## Self-check

1. What is a module?
2. Why should each project have its own virtual environment?
3. What is an exception?
4. Why is swallowing all exceptions dangerous?

**Done for today:** I can organise a small Python project and handle common input errors.