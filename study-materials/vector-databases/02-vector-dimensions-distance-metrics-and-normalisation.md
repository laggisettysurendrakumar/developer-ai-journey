# 02 — Vector Dimensions, Distance Metrics, and Normalisation

## Why dimensions matter

A vector's dimension is the number of numeric values it contains. If one embedding model returns vectors of a particular size, the collection/index must be configured to store that size. A vector with the wrong number of values cannot be searched against an incompatible index.

## 1. Understand vector dimensions

```python
vector = [0.1, 0.2, 0.3, 0.4]
print(len(vector))  # 4 dimensions
```

The dimension is determined by the embedding model or configuration. Do not truncate or pad an embedding just to make it fit another index. Use a compatible model and collection configuration.

## 2. Similarity and distance

Common comparison measures include:

- **Cosine similarity:** compares the direction of two vectors.
- **Dot product:** multiplies corresponding elements and sums them.
- **Euclidean distance:** measures straight-line distance between points.

A database may expose a similarity score or a distance score. Higher may mean better for one metric, while lower may mean better for another. Always check the database's documented semantics.

## 3. Cosine similarity example

```python
import math

def cosine_similarity(a: list[float], b: list[float]) -> float:
    if len(a) != len(b) or not a:
        raise ValueError("Vectors must have the same non-zero dimension")

    dot = sum(x * y for x, y in zip(a, b))
    norm_a = math.sqrt(sum(x * x for x in a))
    norm_b = math.sqrt(sum(y * y for y in b))

    if norm_a == 0 or norm_b == 0:
        raise ValueError("Cosine similarity is undefined for a zero vector")

    return dot / (norm_a * norm_b)

print(cosine_similarity([1.0, 0.0], [0.9, 0.1]))
```

This example compares two illustrative vectors. It does not call an embedding model or database.

## 4. Normalisation

Some models or search systems expect vectors to be normalised to unit length. Others handle normalisation internally or use a different metric. Follow the embedding model and vector database guidance. Normalising incorrectly can change ranking behaviour.

## 5. Do not treat scores as confidence

A similarity score says how close two vectors are under a particular measure. It does not mean the answer has that probability of being correct. Do not compare raw scores from different models or metrics as though they shared one scale.

## Practice exercise

Create a small collection of illustrative vectors and calculate cosine similarity and Euclidean distance. Compare how the scores change when vector magnitude changes but direction stays similar. Write down why a score threshold needs evaluation on real query/document pairs.

## Self-check

1. What does vector dimension mean?
2. Why must index dimensions match inserted vectors?
3. What does cosine similarity compare?
4. Why does a high similarity score not prove correctness?
5. When should vectors be normalised?

## Further reading

- [Qdrant concepts](https://qdrant.tech/documentation/concepts/)
- [pgvector](https://github.com/pgvector/pgvector)
