# Retrieval-Augmented Generation (RAG)

I created this learning track to understand the topic from the beginning and apply it to real GenAI and agentic AI applications. Each concept is explained in plain language, followed by code, why it is useful, a developer use case, and an exercise.

## Who should use this guide
Developers building assistants that must answer using a controlled set of documents, such as engineering documentation, support guides, or product manuals.

## Before starting
Complete Python Days 1–13, GenAI fundamentals, prompt engineering, and basic vector database concepts. Understand JSON, embeddings, and API calls.

## Learning sequence
1. Understand when RAG is useful and when a normal model prompt is enough.
2. Load documents and extract text while retaining source metadata.
3. Clean text and split it into meaningful chunks.
4. Generate embeddings and store vectors with metadata.
5. Retrieve candidate chunks using similarity search.
6. Add metadata filters, access-control checks, and citations.
7. Improve retrieval with keyword/hybrid search and reranking.
8. Build a RAG answer flow and evaluate retrieval and groundedness.

## Important concepts in plain language
### Retrieval
Retrieval means finding relevant information from a knowledge source for a particular question. Search quality has a major effect on RAG quality.

### Chunk
A chunk is a manageable section of a document. It should preserve meaning and keep source information such as document ID, heading, and page.

### Embedding
An embedding model converts text into a numeric vector. Similarity between vectors can help find conceptually related passages.

### RAG
Retrieval-Augmented Generation retrieves evidence first, then supplies that evidence to the LLM to help it produce a grounded answer.

### Reranking
Reranking takes an initial list of retrieved passages and scores them again to place the most useful evidence higher.

### Grounded answer
A grounded answer is supported by the supplied evidence. A citation should point to the source actually used, not simply to a related document.

## Practical examples
Example — chunk a document and keep its source
This simple character-based chunker teaches overlap and metadata. Production chunking should use document structure and may use token-aware boundaries.
```python
def make_chunks(text: str, document_id: str, size: int = 500, overlap: int = 80):
    if size <= 0 or not 0 <= overlap < size:
        raise ValueError("Require size > 0 and 0 <= overlap < size")

    chunks = []
    start = 0
    while start < len(text):
        end = min(start + size, len(text))
        chunks.append({
            "document_id": document_id,
            "start": start,
            "text": text[start:end]
        })
        if end == len(text):
            break
        start = end - overlap
    return chunks

print(make_chunks("RAG retrieves evidence. " * 20, "guide-01"))
```
Use case: index an internal user guide and later cite the right section. Do not discard metadata when creating vectors.
Example — construct a grounded prompt
The model receives the user's question and retrieved passages. Ask it to say when the passages do not contain the answer.
```python
def build_rag_prompt(question: str, passages: list[dict]) -> str:
    evidence = "\n\n".join(
        f"Source {item['document_id']}: {item['text']}"
        for item in passages
    )
    return (
        "Answer using only the evidence below. "
        "If it is insufficient, say so. Include source IDs.\n\n"
        f"Evidence:\n{evidence}\n\nQuestion: {question}"
    )
```
Use case: a developer assistant answers from a repository's documentation and points to the source document used.

## Mini-project
Build a Knowledge Assistant over public or synthetic documents. Ingest Markdown files, preserve headings and source IDs, chunk content, embed and index it, retrieve passages, enforce user access filters, and generate answers with citations. Add tests for missing evidence and stale documents.

## Safety, quality, and cost
- Filter by document permissions before returning chunks to a user; do not rely on the model to enforce access.
- Retrieved documents can contain prompt injection. Treat their text as untrusted evidence, not instructions.
- Measure retrieval recall/precision and answer groundedness separately.
- Plan for document updates, deletions, duplicate ingestion, and embedding-model migrations.
- Avoid sending confidential documents to unapproved embedding or model services.

## Suggested practice routine
1. Read one section and explain the idea in your own words.
2. Type the example instead of copying it blindly.
3. Change one input and predict the output before running it.
4. Test an invalid input and inspect the error.
5. Complete the exercise and record what you learned.

## References
- OpenAI file search: https://platform.openai.com/docs/guides/tools-file-search
- OpenAI embeddings: https://platform.openai.com/docs/guides/embeddings
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/

Documentation and APIs change. Check the linked official documentation for current commands, SDKs, model availability, versions, and pricing before using an example in a real project.

## Self-check

1. Why is RAG useful when an LLM does not know a private document?
2. Why should chunks retain source IDs, page/heading metadata, and version?
3. What is the difference between retrieval quality and answer quality?
4. Why must access filters run before private chunks are returned?
5. What should the assistant say when retrieved evidence is insufficient?
6. Why can prompt injection inside a document be dangerous?

## Practice checklist

- [ ] I can ingest a sample document and preserve its source metadata.
- [ ] I can explain chunk size and overlap trade-offs.
- [ ] I can retrieve relevant chunks and display their source IDs.
- [ ] I can build an answer prompt that requires evidence and admits missing information.
- [ ] I have tested no-answer, stale-document, and permission-filter cases.

**Next:** Learn agentic AI and expose retrieval as a read-only tool.

