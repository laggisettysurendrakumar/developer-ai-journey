# Day 11 — Data handling, NumPy and pandas basics

## Goal for today

AI applications work with documents, tables, measurements, and model outputs. I will learn enough data handling to inspect and transform information before sending it into an AI workflow.

## 1. Why data preparation matters

A model can produce poor answers when input data is incomplete, duplicated, inconsistent, or irrelevant. Data preparation means inspecting, cleaning, transforming, and validating information before using it.

Practise with synthetic or public sample data. Do not upload confidential company or customer information into an unapproved notebook or AI service.

## 2. NumPy: arrays for numerical data

Install:

```bash
python -m pip install numpy
```

```python
import numpy as np

scores = np.array([0.8, 0.6, 0.95])
print(scores.mean())
print(scores.max())
```

NumPy provides efficient arrays and numerical operations. It is widely used in scientific computing and machine learning. A NumPy array is designed for numerical processing and is not the same as a normal Python list.

## 3. pandas: table-shaped data

Install:

```bash
python -m pip install pandas
```

```python
import pandas as pd

records = [
    {"document": "guide-a", "category": "python", "pages": 12},
    {"document": "guide-b", "category": "ai", "pages": 20},
    {"document": "guide-c", "category": "python", "pages": 8},
]

df = pd.DataFrame(records)
print(df.head())
print(df["pages"].mean())
print(df[df["category"] == "python"])
```

A DataFrame is like a spreadsheet in Python. It has rows and columns and supports filtering, grouping, and summaries.

## 4. Missing values and duplicates

```python
import pandas as pd

df = pd.DataFrame([
    {"id": "a", "title": "Python", "pages": 10},
    {"id": "b", "title": None, "pages": 7},
    {"id": "a", "title": "Python", "pages": 10},
])

print(df.isna().sum())
clean_df = df.drop_duplicates(subset=["id"])
print(clean_df)
```

Missing data is not automatically safe to fill with an empty value. Decide what missing means for the application. Remove duplicates using a meaningful key, not a guess.

## 5. Split text into chunks

Retrieval systems split documents into smaller sections so relevant passages can be found. This simple character-based example is only a teaching illustration:

```python
def chunk_text(text: str, chunk_size: int = 100, overlap: int = 20) -> list[str]:
    if chunk_size <= 0:
        raise ValueError("chunk_size must be positive")
    if overlap < 0 or overlap >= chunk_size:
        raise ValueError("overlap must be between 0 and chunk_size - 1")

    chunks = []
    start = 0
    while start < len(text):
        end = min(start + chunk_size, len(text))
        chunks.append(text[start:end])
        if end == len(text):
            break
        start = end - overlap
    return chunks

sample = "Python can prepare documents for retrieval. " * 8
for index, chunk in enumerate(chunk_text(sample), start=1):
    print(index, chunk)
```

Overlap preserves context between adjacent chunks. Production chunking should consider tokens, document structure, headings, tables, code blocks, and model context limits. Character count is not token count.

## 6. Keep metadata with chunks

```python
chunk = {
    "chunk_id": "guide-1-section-2",
    "document_id": "guide-1",
    "title": "Python Setup Guide",
    "text": "A virtual environment isolates project packages.",
    "page": 3,
    "access_group": "engineering"
}
```

Metadata supports citations, versioning, filtering, and access checks. Permissions must be enforced before a user sees retrieved content.

## 7. Data quality checklist

Before indexing a document, ask:
- Is the file type expected?
- Is the document empty or extremely large?
- Is extracted text readable?
- Are duplicates present?
- Can this user access the document?
- Is the information allowed to be processed by the selected service?

## Practice exercise

Create a small DataFrame of five synthetic knowledge documents. Count records by category, identify missing titles, remove duplicate IDs, and chunk one sample text. Record why source metadata should travel with each chunk.

## Self-check

1. What is a NumPy array good at?
2. What does a pandas DataFrame represent?
3. Why should missing values be investigated?
4. Why does a RAG chunk need a source ID or page number?

**Done for today:** I can inspect and prepare simple datasets and explain why data quality affects AI answers.