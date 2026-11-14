# Day 6 — Files, JSON, CSV and configuration

## Goal for today

AI applications read documents, load settings, save results, and exchange JSON with APIs. I will learn safe file handling and the JSON format.

## 1. Read a text file

```python
from pathlib import Path

file_path = Path("notes.txt")
content = file_path.read_text(encoding="utf-8")
print(content)
```

`Path` represents a filesystem path across operating systems. `read_text()` reads text using the chosen encoding. This works well for small files. Very large files should be processed in chunks or line by line.

## 2. Write a text file

```python
from pathlib import Path

output_path = Path("summary.txt")
output_path.write_text("This is a sample summary.\\n", encoding="utf-8")
```

This creates or replaces the file. Be careful: write mode overwrites existing content. Practise in a disposable folder.

## 3. JSON: structured text for exchanging data

JSON stands for JavaScript Object Notation. APIs and configuration files commonly use it. JSON supports objects, arrays, strings, numbers, Booleans, and null.

```python
import json

message = {
    "role": "user",
    "content": "Explain tool calling",
    "metadata": {"channel": "web"}
}

json_text = json.dumps(message, indent=2)
print(json_text)
```

- `json.dumps()` converts a Python object into a JSON string.
- `json.dump()` writes JSON to a file.
- `json.loads()` parses a JSON string into Python values.
- `json.load()` reads JSON from a file.

## 4. Save and load JSON

```python
import json
from pathlib import Path

settings = {"model": "demo-model", "max_steps": 5}
path = Path("settings.json")

path.write_text(json.dumps(settings, indent=2), encoding="utf-8")
loaded = json.loads(path.read_text(encoding="utf-8"))

print(loaded["max_steps"])
```

For real configuration, validate values and use secure secret storage. Do not put API keys in checked-in JSON files.

## 5. Handle invalid JSON

```python
import json

bad_json = '{"role": "user",}'
try:
    data = json.loads(bad_json)
except json.JSONDecodeError as error:
    print(f"Invalid JSON on line {error.lineno}: {error.msg}")
```

API responses may be malformed or different from what I expect. Catch parsing errors at the boundary and report a useful failure.

## 6. CSV files: simple tables

```python
import csv

rows = [
    {"id": "1", "question": "What is Python?"},
    {"id": "2", "question": "What is an embedding?"}
]

with open("questions.csv", "w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=["id", "question"])
    writer.writeheader()
    writer.writerows(rows)
```

`DictWriter` writes dictionary values into named columns. The `with` statement closes the file even if something fails.

## 7. Environment variables

```python
import os

app_environment = os.getenv("APP_ENV", "development")
api_key = os.getenv("AI_API_KEY")

if not api_key:
    print("AI_API_KEY is not configured.")
else:
    print(f"Configuration is present for {app_environment}.")
```

Do not print the key itself. In deployment, inject secrets using the platform's secret-management features.

## 8. A document-ingestion use case

A basic knowledge ingestion pipeline can:
1. Read a text document.
2. Clean extra whitespace.
3. Split content into manageable sections.
4. Store metadata such as filename and page number.
5. Send sections to an embedding and retrieval stage.

Real ingestion also needs file-type validation, size limits, readable text extraction, access controls, and protection against malicious instructions hidden in documents.

## Practice exercise

Create a JSON file with three synthetic knowledge entries. Load it in Python, print each entry's title, and handle a missing `title` key safely using `.get()`.

## Self-check

1. What is the difference between `json.dumps()` and `json.loads()`?
2. Why should I use `with open(...)`?
3. Why can writing a file be destructive?
4. Why should a secret not be stored in a public repository?

**Done for today:** I can read and write text, JSON, and CSV files and load non-secret configuration.