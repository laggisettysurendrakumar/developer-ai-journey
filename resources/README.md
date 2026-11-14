# Curated Resources for GenAI and Agentic AI Development

This folder is a directory of learning references that support the courses in `study-materials/`. Prefer official documentation for APIs and security-sensitive details. Community tutorials can help explain a topic, but verify commands and SDK calls against the official docs before using them.

## How to use this list

- Follow the main [Developer AI Journey roadmap](../README.md) in order.
- Start with one resource for the topic you are currently studying; do not collect links instead of practising.
- Check when a page was last updated, the library version, and whether an API has changed.
- Do not copy credentials or private data into public playgrounds.

## Programming foundations

| Resource | Why it is useful |
|---|---|
| [Python tutorial](https://docs.python.org/3/tutorial/) | Language concepts and examples from the official Python documentation |
| [Python standard library](https://docs.python.org/3/library/) | Reference for JSON, pathlib, logging, asyncio, testing, and other built-in tools |
| [Real Python](https://realpython.com/) | Explanations and tutorials for Python practice; verify version-sensitive examples |
| [Linux Journey](https://linuxjourney.com/) | Beginner explanations for Linux concepts |

## GenAI and LLM application development

| Resource | Why it is useful |
|---|---|
| [OpenAI API documentation](https://platform.openai.com/docs/) | Current API reference, authentication, model and response concepts |
| [OpenAI text generation](https://platform.openai.com/docs/guides/text) | Text generation concepts and API usage |
| [OpenAI embeddings](https://platform.openai.com/docs/guides/embeddings) | Embeddings for semantic search and retrieval |
| [OpenAI Agents SDK](https://developers.openai.com/api/docs/guides/agents/sdk) | Agent orchestration, tools, handoffs, and tracing in Python |
| [Hugging Face course](https://huggingface.co/learn) | Open model ecosystem, transformers, tokenizers, and ML concepts |

## RAG, retrieval, and vector search

| Resource | Why it is useful |
|---|---|
| [Qdrant documentation](https://qdrant.tech/documentation/) | Vector search concepts and hands-on database tutorials |
| [pgvector](https://github.com/pgvector/pgvector) | Vector similarity search inside PostgreSQL |
| [Pinecone learning centre](https://www.pinecone.io/learn/) | Practical explanations of vector search and RAG concepts |
| [OpenAI file search](https://platform.openai.com/docs/guides/tools-file-search) | Managed retrieval tool concepts and usage |

## Protocols, security, and evaluation

| Resource | Why it is useful |
|---|---|
| [Model Context Protocol documentation](https://modelcontextprotocol.io/docs/) | MCP architecture, concepts, and integration guidance |
| [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk) | Official Python SDK source and examples; check its current version |
| [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/) | Common LLM application security risks and mitigations |
| [OpenAI Evals guide](https://platform.openai.com/docs/guides/evals) | Testing and measuring model/application behaviour |
| [OpenTelemetry documentation](https://opentelemetry.io/docs/) | Traces, metrics, and logs across distributed applications |

## Containers, orchestration, and infrastructure

| Resource | Why it is useful |
|---|---|
| [Docker getting started](https://docs.docker.com/get-started/) | Images, containers, and basic workflows |
| [Kubernetes tutorials](https://kubernetes.io/docs/tutorials/) | Guided deployment and operations practice |
| [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials) | Infrastructure as Code from beginner level |
| [Azure architecture centre](https://learn.microsoft.com/azure/architecture/) | Cloud architecture patterns and decision guidance |
| [Azure Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/) | Reliability, security, cost, operations, and performance guidance |

## Free hands-on practice

| Platform | Useful for | Notes |
|---|---|---|
| [Google Colab](https://colab.research.google.com/) | Python notebooks, data experiments, introductory ML | Free access and hardware availability/limits can change |
| [Kaggle Learn](https://www.kaggle.com/learn) | Short courses and notebook-based data/ML practice | Account and competition rules apply |
| [Hugging Face Spaces](https://huggingface.co/spaces) | Explore community-hosted AI demos | Do not submit confidential data; availability varies |
| [Killercoda](https://killercoda.com/) | Browser-based Linux, Docker, and Kubernetes labs | Check current free scenario availability |
| [OverTheWire Bandit](https://overthewire.org/wargames/bandit/) | Linux shell command challenges | Start at Level 0 |

## Books and deeper study

- *Python Crash Course* by Eric Matthes — a project-based introduction to Python.
- *Fluent Python* by Luciano Ramalho — deeper Python language practices after the fundamentals.
- *Designing Machine Learning Systems* by Chip Huyen — system-level ML engineering concepts.
- *AI Engineering* by Chip Huyen — building applications with foundation models.

Check edition and publication details before purchasing. Use the Python course in this repository as the hands-on sequence.

## Resource maintenance

When adding a resource, record: topic, title, URL, what it teaches, whether it is official or community-authored, date checked, prerequisites, and any free-tier/usage limits. Do not describe a resource as permanently free unless its current terms confirm that.
