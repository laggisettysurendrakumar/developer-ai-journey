# 01 — RAG Fundamentals and When to Use RAG

## What this lesson is about

Retrieval-Augmented Generation (RAG) is an application pattern that retrieves relevant information from an external knowledge source and gives that evidence to a language model when it generates an answer. It is useful when an application needs to answer from documents, manuals, policies, product data, or frequently changing knowledge that may not be present in a model's training data.

The important idea is simple: **search first, answer using the retrieved evidence second**.

## 1. Why do we need RAG?

A language model can explain general concepts, but it may not know:
- the latest version of an internal runbook,
- a company's private engineering documentation,
- a newly updated product manual,
- the contents of a specific repository,
- which policy applies to a particular customer or employee.

Fine-tuning is not automatically the right solution for adding changing facts. RAG makes relevant source material available at query time and can provide references to that material.

## 2. The basic RAG flow

```text
User question
    |
    v
Question processing
    |
    v
Retrieve relevant passages
    |
    v
Filter / rank evidence
    |
    v
Build grounded prompt
    |
    v
Language model
    |
    v
Answer + source references
```

There are two broad workflows:

**Indexing time:** load documents, extract text and structure, clean and split content, create embeddings, and store searchable records with metadata.

**Query time:** accept a question, create a query representation, retrieve relevant records, optionally rerank them, and provide selected evidence to the model.

## 3. RAG, normal prompting, and fine-tuning

| Approach | Best suited for | Main limitation |
|---|---|---|
| Normal prompting | General tasks with enough context already supplied | Context must fit and be supplied |
| RAG | Answers grounded in external or changing knowledge | Search can return poor or incomplete evidence |
| Fine-tuning | Consistent task behaviour, style, or specialised patterns | Not a convenient live knowledge store |

These approaches can be combined. RAG does not guarantee correctness; the retrieval step and answer step both need evaluation.

## 4. When RAG is useful

- Internal documentation assistant.
- Product-support knowledge search.
- Policy and procedure Q&A.
- Developer assistant over approved source code and Markdown.
- Customer support with permission-filtered articles.

RAG may be unnecessary for a simple calculator, deterministic business rule, or small fixed reference that can safely be supplied directly in a prompt.

## 5. The two quality problems

A RAG system has at least two separate questions:

1. **Did retrieval find the right evidence?**
2. **Did the model produce an answer supported by that evidence?**

A correct answer can be produced from the model's prior knowledge even when retrieval failed. That is why retrieval quality and answer groundedness must be measured separately.

## Practice exercise

Choose a small knowledge domain—for example, five synthetic developer runbooks. Draw the indexing flow and query flow. Label which components are deterministic code, which call an embedding model, which call a language model, and where permission checks occur.

## Self-check

1. What does RAG add to a normal model request?
2. Why can RAG be useful for frequently updated documents?
3. What is the difference between indexing time and query time?
4. Why is retrieval quality different from answer quality?
5. When might normal prompting be simpler than RAG?

## Further reading

- [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- [OpenAI file search guide](https://platform.openai.com/docs/guides/tools-file-search)
