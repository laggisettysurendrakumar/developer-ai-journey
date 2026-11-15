# 06 — Create Collections and Store Vectors with Qdrant

## Goal

I will map the provider-neutral concepts to a real vector database. Qdrant uses collections to hold points with vectors and payloads. This lesson uses the Python client; verify the installed client version against the current official documentation.

## 1. Prepare a Python environment

```bash
python -m venv .venv
```

Activate it:

```bash
# Windows PowerShell
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

Install the client:

```bash
python -m pip install qdrant-client
python -m pip freeze
```

Pin the reviewed package version in a lock or requirements file for repeatable projects.

## 2. Create a collection

For a local embedded learning example:

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(path="./qdrant-local")
collection_name = "developer-docs"

if not client.collection_exists(collection_name):
    client.create_collection(
        collection_name=collection_name,
        vectors_config=models.VectorParams(
            size=3,
            distance=models.Distance.COSINE,
        ),
    )

print(client.get_collection(collection_name))
```

The vector size of three is deliberately tiny for this sample. In a real application, set the dimension to the exact value returned by the selected embedding model.

## 3. Insert points

```python
from qdrant_client import models

client.upsert(
    collection_name="developer-docs",
    points=[
        models.PointStruct(
            id="doc-1",
            vector=[0.1, 0.2, 0.3],
            payload={
                "title": "ASP.NET Core API",
                "category": "dotnet",
                "text": "Synthetic sample document text",
            },
        ),
        models.PointStruct(
            id="doc-2",
            vector=[0.2, 0.1, 0.4],
            payload={
                "title": "Docker Basics",
                "category": "containers",
                "text": "Synthetic sample document text",
            },
        ),
    ],
)

print("Points inserted")
```

These are synthetic numeric vectors, not model embeddings. They demonstrate collection and point operations only.

## 4. Inspect collection contents

Use the client's scroll/query methods documented for the installed version to inspect points. Do not rely on a vector score when checking that records were inserted; inspect identifiers and payload fields too.

## 5. Local storage

The embedded client stores data in the local path. Close the client cleanly before moving or copying local database files. Production durability and backup requirements are different from a local learning folder.

## Practice exercise

Create a collection whose vector dimension matches a chosen embedding model. Insert synthetic points, inspect the collection configuration, and verify that an attempted vector with the wrong dimension is rejected. Do not use fake vectors for application relevance tests.

## Self-check

1. What is a Qdrant collection?
2. What is a point?
3. Why is the sample vector dimension intentionally small?
4. What does upsert do?
5. What needs to change before using real embeddings?

## Further reading

- [Qdrant Python client](https://qdrant.tech/documentation/quickstart/)
- [Qdrant collections](https://qdrant.tech/documentation/concepts/collections/)
