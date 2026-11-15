# 03 — Document Ingestion, Parsing, and Normalisation

## Why ingestion quality matters

The model cannot retrieve information that was never extracted or indexed correctly. Document ingestion converts source material into text and structured metadata that can be searched.

## 1. Identify source types

Common sources include:
- Markdown and text files,
- HTML documentation,
- PDF manuals,
- Word documents,
- source-code repositories,
- database records,
- knowledge-base APIs.

Each source format needs an appropriate parser. A PDF may contain selectable text, scanned pages, tables, or images; plain text extraction may not preserve all of its meaning.

## 2. Preserve structure

Useful document structure includes:
- title,
- heading hierarchy,
- paragraph boundaries,
- code blocks,
- tables,
- page numbers,
- source URI,
- section anchors,
- document version and last-modified time.

A passage saying “This setting must be enabled” is much more useful when the source heading and product version are retained.

## 3. Normalise without destroying meaning

Typical cleanup:
- remove duplicate headers and footers,
- normalise line endings,
- remove irrelevant navigation text,
- collapse excessive whitespace,
- standardise encoding,
- preserve code indentation and table relationships,
- retain meaningful headings and metadata.

Do not remove words such as “not”, version numbers, units, or command-line flags just because they look unusual.

## 4. Use a document manifest

Maintain a manifest for each source with:
- stable source ID,
- canonical URI,
- source version or content hash,
- ingestion timestamp,
- source modified timestamp,
- parser version,
- chunking version,
- embedding model/version,
- processing status.

This makes re-indexing and debugging possible.

## 5. Handle duplicates, updates, and deletions

A reliable ingestion system should:
- detect unchanged documents, often with a content hash,
- update affected chunks when a document changes,
- remove stale chunks when a document is deleted,
- avoid partial indexing being mistaken for a complete source,
- record failures for retry and investigation.

Avoid blindly appending every run to the vector store.

## 6. Example: read Markdown with metadata

```python
from pathlib import Path
import hashlib

def load_markdown(path: Path) -> dict:
    raw = path.read_bytes()
    text = raw.decode("utf-8")
    return {
        "source_id": str(path.as_posix()),
        "text": text,
        "content_sha256": hashlib.sha256(raw).hexdigest(),
        "file_name": path.name,
    }

document = load_markdown(Path("docs/runbook.md"))
print(document["source_id"], document["content_sha256"])
```

This small example reads a UTF-8 Markdown file and computes a content hash. A production loader also needs file-size limits, error handling, access checks, and a controlled source allowlist.

## Practice exercise

Create a loader for five synthetic Markdown files. Preserve title, headings, source path, version, and content hash. Modify one file and prove that the ingestion manifest detects the change. Simulate a deleted document and define how its chunks should be removed.

## Self-check

1. Why is parsing a PDF sometimes more difficult than reading Markdown?
2. Which structure should be preserved during extraction?
3. Why can aggressive cleanup reduce retrieval accuracy?
4. How can a content hash support incremental ingestion?
5. What should happen when a source document is deleted?

## Further reading

- [OpenAI file search](https://platform.openai.com/docs/guides/tools-file-search)
