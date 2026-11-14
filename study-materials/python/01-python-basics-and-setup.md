# Day 1 — Python basics, setup, variables and data types

## Goal for today

I will install or locate Python, run a program, and learn how to store information. These are the smallest building blocks of every Python application, including an AI agent.

## 1. What is Python?

Python is a programming language. I write instructions in a readable form, save them in a file, and the Python interpreter executes them. Python is popular for AI because its ecosystem includes tools for data processing, machine learning, web APIs, and automation.

An LLM does not replace Python. Python is still the application layer that receives a user's request, validates it, calls a model, controls tools, and returns a response.

## 2. Check Python and create a file

Check the installed version:

```bash
python --version
python -m pip --version
```

On some Windows systems, use `py` instead of `python`.

Create `hello.py`:

```python
print("Hello, Python!")
print("I am learning Python to build AI applications.")
```

Run it:

```bash
python hello.py
```

**What is happening?** `print()` displays text in the terminal. Python executes statements from top to bottom. A `.py` file contains Python source code.

**Why useful?** I will use output to check a value while learning. Later, an application should use logging instead of relying on print statements for operational diagnostics.

## 3. Variables: labelled boxes for values

A variable is a name that refers to a value. Think of a labelled box: the label is the variable name and the contents are the value.

```python
developer_name = "Surendra"
years_of_experience = 8
is_learning_ai = True

print(developer_name)
print(years_of_experience)
print(is_learning_ai)
```

Python figures out the type from the value. Names are case-sensitive: `name` and `Name` are different.

**AI use case:** Store the user's question, selected model, retry count, and whether a feature is enabled.

## 4. Basic data types

```python
question = "Explain embeddings"  # str: text
attempts = 3                      # int: whole number
temperature = 0.2                 # float: decimal number
is_enabled = True                 # bool: True or False
response = None                   # NoneType: no value yet

print(type(question))
print(type(attempts))
print(type(temperature))
print(type(is_enabled))
print(type(response))
```

- `str` stores text.
- `int` stores whole numbers.
- `float` stores decimal numbers.
- `bool` stores `True` or `False`.
- `None` means no value has been assigned or there is no result.

## 5. Convert values between types

Input typed by a person usually arrives as text. I may need to convert it before using it as a number.

```python
attempts_text = "3"
attempts = int(attempts_text)

print(attempts + 1)  # 4
```

Other conversions include `str(value)`, `int(value)` and `float(value)`. Conversion can fail: `int("three")` raises a `ValueError`. Never assume external input has the format I expect.

## 6. Arithmetic and operators

```python
input_tokens = 800
output_tokens = 200
total_tokens = input_tokens + output_tokens

print(total_tokens)
print(total_tokens / 1000)
```

Common operators: `+` addition, `-` subtraction, `*` multiplication, `/` division, `//` floor division, `%` remainder, and `**` exponentiation.

**AI use case:** Calculate a simple illustrative usage estimate or count attempts. Actual provider billing must use current provider usage and pricing data.

## 7. f-strings: insert values into text

```python
model_name = "demo-model"
request_count = 12

message = f"Model {model_name} handled {request_count} requests."
print(message)
```

The `f` makes it possible to place variable values inside braces. This is useful for readable messages and logs.

## 8. Comments and readable names

```python
# This comment explains why the retry limit exists.
max_retries = 3
```

Use names such as `user_question` or `max_retries` rather than unclear names such as `x`. Comments should explain why a decision exists, not repeat every line.

## Common beginner mistakes

- Writing `true` instead of `True`.
- Forgetting quotation marks around text.
- Adding a number to text without converting it.
- Using a variable before creating it.
- Naming a file `json.py`, `random.py` or `typing.py`, which can interfere with imports.

## Practice exercise

Create `usage_estimate.py`. Store a model label, input token count, output token count, and an example price per 1,000 tokens. Calculate and print an illustrative estimate. Clearly state that the price is made up for practice, not a current provider price.

## Self-check

1. What is the Python interpreter?
2. What is the difference between `str` and `int`?
3. Why does `int("3")` work but `int("three")` fail?
4. Why might an AI application store a retry count as an integer?

**Done for today:** I can run a Python file and explain the values and types it uses.