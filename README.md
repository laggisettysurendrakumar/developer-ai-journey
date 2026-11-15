# Developer AI Journey

A practical, beginner-first roadmap for learning how to build, evaluate, secure, and deploy Generative AI and Agentic AI applications. The repository starts with programming and operating-system foundations, then moves through LLMs, RAG, agents, integration protocols, and cloud delivery.

## Start here — recommended learning order

Do not try to learn every tool at once. Follow the dependency order below. The Python and Linux folders contain more detailed beginner lessons; other topic folders are learning tracks with a recommended sequence and practical examples.

### Phase 1 — Developer foundations

1. **Linux basics** — [Start the Linux course](study-materials/linux/README.md). Learn terminal, files, permissions, processes, networking, and safe troubleshooting. Useful for running and debugging AI services.
2. **Python for AI development** — [Start the 14-day Python course](study-materials/python/README.md). Learn Python from basics through APIs, async programming, data handling, and a first tool-using agent.
3. **Git and project hygiene** — use version control, branches, commits, `.gitignore`, code review, and secret scanning as part of every exercise.

### Phase 2 — Understand and call models

4. **GenAI fundamentals** — [Start the 14-lesson GenAI engineering course](study-materials/genai/README.md). Progress from AI/ML and transformer foundations through tokens, model APIs, prompting, structured outputs, embeddings, multimodal models, evaluation, security, and a GenAI application capstone.
5. **Prompt engineering** — [Study prompt engineering](study-materials/prompt-engineering/README.md). Create clear, testable prompts and learn prompt-injection boundaries.

### Phase 3 — Give models useful knowledge

6. **Vector databases and semantic search** — [Study vector databases](study-materials/vector-databases/README.md). Understand embeddings, similarity, indexes, metadata filters, and persistence.
7. **RAG fundamentals** — [Study RAG](study-materials/rag/README.md). Apply document loading, chunking, embeddings, retrieval, citations, and grounded answer evaluation.

### Phase 4 — Build controlled agents and integrations

8. **Agentic AI** — [Start the 14-lesson Agentic AI course](study-materials/agentic-ai/README.md). Progress from the manual agent loop and validated tools to memory, planning, multi-agent patterns, safety, evaluation, the OpenAI Agents SDK, and a developer-support capstone.
9. **MCP** — [Study Model Context Protocol](study-materials/mcp/README.md). Connect model hosts to external tools and resources through a defined protocol.
10. **LLM evaluation and observability** — [Start the 14-lesson LLM Evaluation Engineering course](study-materials/llm-evaluation/README.md). Learn evaluation datasets, human rubrics, deterministic tests, metrics, LLM judges, RAG and agent evaluation, red teaming, experiment tracking, CI quality gates, and production monitoring.

### Phase 5 — Package, deploy, and operate

11. **Docker** — [Start the 14-lesson Docker engineering course](study-materials/docker/README.md). Progress from container basics through Dockerfiles, .NET container builds, networking, volumes, Compose, registries, security, BuildKit, CI/CD, and production readiness.
12. **Kubernetes** — [Start the 14-lesson Kubernetes engineering and production-support course](study-materials/kubernetes/README.md). Progress from cluster fundamentals through workloads, networking, storage, security, Helm, observability, upgrades, incident triage, runbooks, and SRE practices.
13. **Terraform** — [Study Terraform](study-materials/terraform/README.md). Provision repeatable infrastructure with reviewed plans and secure state.
14. **Azure** — [Start the 14-lesson Azure engineering course](study-materials/azure/README.md). Progress from cloud and identity foundations through networking, compute, data, security, DevOps, Terraform, reliability, cost management, Azure AI, and a production-minded capstone.

## Repository map

```text
developer-ai-journey/
├── study-materials/
│   ├── linux/
│   ├── python/
│   ├── genai/
│   ├── prompt-engineering/
│   ├── rag/
│   ├── vector-databases/
│   ├── agentic-ai/
│   ├── mcp/
│   ├── llm-evaluation/
│   ├── docker/
│   ├── kubernetes/
│   ├── terraform/
│   └── azure/
├── projects/
└── resources/
```


## Suggested study pace

This is a practical estimate, not a deadline. Plan for roughly 1–2 focused hours per day. Repeat a topic when the examples are not yet comfortable; AI engineering requires building and debugging, not only reading.

| Topic | Suggested practice time |
|---|---:|
| Linux foundations | 7 days |
| Python for AI development | 14 days |
| GenAI fundamentals | 4 days |
| Prompt engineering | 3 days |
| Vector databases and semantic search | 4 days |
| RAG | 5 days |
| Agentic AI | 5 days |
| MCP | 3 days |
| LLM evaluation and observability | 4 days |
| Docker | 4 days |
| Kubernetes | 5 days |
| Terraform | 4 days |
| Azure for AI applications | 5 days |

The complete first pass is about 67 study days. Allow additional time for project work, revision, cloud provisioning, and troubleshooting. You can postpone Kubernetes or multi-agent patterns until a project has a real need for them.

## Recommended capstone

Build a **Developer Knowledge Assistant** in small increments:
1. Python CLI/API that accepts a question and validates it.
2. Model adapter that calls an approved LLM with secure configuration.
3. Document ingestion pipeline that keeps source IDs and access metadata.
4. RAG retrieval with citations and permission filters.
5. Read-only agent tools such as knowledge search and calculator.
6. MCP server exposing a limited subset of tools/resources.
7. Evaluation dataset, automated tests, tracing, and cost/latency tracking.
8. Docker packaging and a non-production Azure deployment.
9. Optional Kubernetes deployment when the operational need justifies it.
10. Terraform-managed infrastructure and CI/CD with reviewed plans.

## How to study each track

- Read the topic README before starting its examples.
- Learn the plain-English explanation before memorising terminology.
- Type every code example and understand the expected output.
- Use synthetic or public sample data. Do not commit company secrets, credentials, customer data, private prompts, or production logs.
- Complete the suggested exercise and mini-project before moving on.
- Record tool/library versions and check official documentation for current APIs.
- Build a single integrated project rather than several unrelated demos.

## Learning principles and safety

- Prefer deterministic code and explicit workflows when the task path is known; use agents only when model-driven decisions add value.
- Validate all external input and all model-generated structured data.
- A prompt is not an authorisation boundary. Enforce identity, access, and business rules in trusted code.
- Expose narrow allowlisted tools; never give an LLM unrestricted shell, SQL, cloud, or production credentials.
- Require human approval for consequential or irreversible actions.
- Bound agent steps, time, concurrency, retries, tool output size, tokens, and spend.
- Evaluate normal, failure, edge, and adversarial cases before deployment.
- Verify data retention, privacy, residency, and provider terms before sending data to a model service.

## Course status

| Topic | Entry point | Learning focus |
|---|---|---|
| Linux | [Linux course](study-materials/linux/README.md) | Host, shell, safe operations, troubleshooting |
| Python | [14-day Python course](study-materials/python/README.md) | Python fundamentals to agent prototype |
| GenAI | [GenAI](study-materials/genai/README.md) | LLM concepts, inference, APIs |
| Prompt engineering | [Prompts](study-materials/prompt-engineering/README.md) | Instructions, schemas, robustness |
| Vector databases | [Vector DB](study-materials/vector-databases/README.md) | Embedding storage and search |
| RAG | [RAG](study-materials/rag/README.md) | Document retrieval and grounded answers |
| Agentic AI | [Agents](study-materials/agentic-ai/README.md) | Tools, state, orchestration and safety |
| MCP | [MCP](study-materials/mcp/README.md) | Standard tool/resource integration |
| LLM evaluation | [Evaluation](study-materials/llm-evaluation/README.md) | Quality, traces, metrics, regression tests |
| Docker | [Docker](study-materials/docker/README.md) | Container packaging and runtime |
| Kubernetes | [Kubernetes](study-materials/kubernetes/README.md) | Cluster deployment and operations |
| Terraform | [Terraform](study-materials/terraform/README.md) | Infrastructure as Code |
| Azure | [Azure](study-materials/azure/README.md) | Cloud hosting, identity, AI services, monitoring |

## Curated resources

See [resources/README.md](resources/README.md) for official documentation, hands-on labs, books, and guidance on choosing current learning material.

This is a living learning repository. Prefer accurate, reproducible examples over a large collection of framework-specific snippets. Frameworks and provider APIs change; concepts, tests, safety boundaries, and good engineering practices remain the foundation.
