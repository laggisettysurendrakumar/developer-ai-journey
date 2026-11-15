# 04 — Chunking Strategies and Metadata Design

## What is a chunk?

A chunk is a piece of a larger document that can be retrieved independently. Chunking controls what the retriever can find and what context the model receives. Poor chunking can split definitions from explanations or combine unrelated topics.

## 1. Common chunking strategies

| Strategy | How it works | Useful for |
|---|---|---|
| Fixed character/token window | Split at a configured size | Quick baseline |
| Sentence-based | Split near sentence boundaries | Prose |
| Paragraph-based | Keep paragraphs together | Articles and guides |
| Heading-aware | Split by document sections | Technical docs |
| Recursive | Try larger separators before smaller ones | Mixed text |
| Semantic | Split when topic meaning changes | Long narrative documents |
| Structure-aware | Respect code, tables, pages, or records | Source code and manuals |

There is no universal best chunk size. Measure with your own documents and questions.

## 2. Chunk size trade-offs

Small chunks improve precision for a precise detail but may lose surrounding explanation. Large chunks preserve context but may include irrelevant content, use more tokens, and reduce precision.

## 3. Overlap

Overlap repeats some text between adjacent chunks so a boundary does not cut a useful sentence or concept in half. Too much overlap creates duplicate results, wastes storage, and makes the same fact appear multiple times.

Start with a simple baseline, then tune chunk size and overlap using a retrieval evaluation dataset.

## 4. Preserve parent and heading relationships

A useful chunk can contain chunk ID, document ID, heading path, page/section, source URI, content hash, version, and access metadata. Consider storing a parent document or section reference so the application can fetch more context when a small chunk is retrieved.

## 5. Example: a basic overlap chunker

```python
def split_with_overlap(text: str, size: int = 500, overlap: int = 80) -> list[str]:
    if size <= 0 or not 0 <= overlap < size:
        raise ValueError("Require size > 0 and 0 <= overlap < size")

    chunks = []
    start = 0
    while start < len(text):
        end = min(start + size, len(text))
        chunks.append(text[start:end])
        if end == len(text):
            break
        start = end - overlap
    return chunks
```

This is a teaching example based on Python character positions, not tokens. It can split words and code blocks. A production chunker should prefer document structure and use a tokenizer when model token limits matter.

## 6. Chunking for code and tables

For source code, preserve class/function boundaries and enough imports or surrounding context to explain dependencies. For tables, repeat column headers when splitting rows. A chunk containing values without their column names may be meaningless.

## Practice exercise

Take a synthetic ASP.NET Core deployment guide. Split it with fixed windows and with headings. Create ten questions and compare whether the retrieved chunks contain enough context to answer each question. Record the effect of changing size and overlap.

## Self-check

1. What is chunking and why is it needed?
2. What can go wrong with chunks that are too small?
3. What can go wrong with chunks that are too large?
4. Why does overlap have a storage and duplication cost?
5. Why should code and tables be chunked differently from prose?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
