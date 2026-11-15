# Developer AI Journey

A practical, beginner-first learning path for becoming a developer who can build, evaluate, secure, deploy, and operate modern AI-powered applications.

I created this repository as a structured learning journey rather than a collection of disconnected notes. It starts with programming and operating-system foundations, explains how modern AI models work, then moves into retrieval, agents, integration protocols, evaluation, containers, infrastructure, and cloud operations.

The goal is to understand **why a technology is used, how it works, when to choose it, how to build with it, how to troubleshoot it, and how to operate it safely**.

> **Who this is for:** Beginners who want a guided route into AI engineering, software developers moving into GenAI/Agentic AI, and experienced developers who want to strengthen their Linux, Python, cloud, DevOps, and production engineering skills.

---

## Start here: how to use this repository

When opening this repository for the first time, follow this sequence:

1. Read this root README completely enough to understand the learning path.
2. Choose your current level: beginner, application developer, or experienced developer transitioning into AI engineering.
3. Start with the **Phase 1 foundations** below if Linux, Python, or command-line basics are new to you.
4. Read the README inside the selected course folder before opening its numbered lessons.
5. Follow the lesson order. Each course README explains its prerequisites, lesson sequence, exercises, and expected outcomes.
6. Build the recommended mini-project or capstone instead of only reading the material.
7. Keep notes about commands, errors, design decisions, and what you learned.
8. Move to the next course when you can explain the key concepts and complete the practical work without blindly copying instructions.

**Important:** You do not need to finish all 13 tracks before building something. The roadmap is a recommended sequence, not a rigid gate. Use the shortest path that supports the application you want to build.

## What this repository covers

The learning material is organised into five phases. Each phase builds on the ideas from the previous one.

### Phase 1 — Developer foundations

Before building AI applications, I need to be comfortable working with code, files, processes, APIs, and development tools.

| Course | What it covers | Start here |
|---|---|---|
| Linux | Terminal basics, files and directories, permissions, processes, networking, package management, services, logs, shell scripting, troubleshooting, and safe system operations. | [Linux course](study-materials/linux/README.md) |
| Python | Python syntax, data types, collections, conditions, loops, functions, modules, exceptions, object-oriented programming, files, JSON, APIs, async programming, testing, and a first AI/tool-using project. | [14-day Python course](study-materials/python/README.md) |
| Git and project hygiene | Branches, commits, readable history, .gitignore, dependency files, documentation, code review, and secret-safe repository practices. These practices should be used throughout every course. | Practise with every lesson and project. |

**Why start here?** Linux helps me understand the environment where applications run. Python is used for many AI examples and SDKs in this repository. Git helps me preserve and review my work.

### Phase 2 — Understand models and communicate with them

This phase explains what modern generative AI systems do and how to interact with them reliably.

| Course | What it covers | Start here |
|---|---|---|
| GenAI fundamentals | AI and machine-learning foundations, neural networks, transformers, tokens, context windows, inference, model APIs, structured outputs, embeddings, multimodal capabilities, evaluation, safety, and an application capstone. | [GenAI engineering course](study-materials/genai/README.md) |
| Prompt engineering | Prompt structure, instructions and context, examples, reusable templates, structured outputs, developer workflows, task decomposition, prompt injection, evaluation, optimisation, and a practical prompt-library capstone. | [Prompt engineering course](study-materials/prompt-engineering/README.md) |

**Why this phase matters:** A developer should understand the model's role and limitations before relying on it in an application. Prompting is useful, but application code must still validate outputs and enforce business rules.

### Phase 3 — Give models knowledge and retrieve reliable information

A model's built-in knowledge is not a substitute for an application's private, current, or source-specific information. This phase teaches how to prepare data and retrieve relevant evidence.

| Course | What it covers | Start here |
|---|---|---|
| Vector databases | Vectors and embeddings, dimensions and distance metrics, collections, points, payloads, Qdrant, upsert, vector search, metadata filtering, tenant isolation, ANN/HNSW, hybrid search, pgvector, production operations, and a developer knowledge assistant capstone. | [14-lesson vector database course](study-materials/vector-databases/README.md) |
| RAG engineering | RAG architecture, document ingestion, parsing, chunking, embedding generation, vector storage, retrieval, hybrid search, reranking, grounded generation, citations, evaluation, security, incremental indexing, production operations, and a knowledge assistant capstone. | [14-lesson RAG course](study-materials/rag/README.md) |

**Recommended order:** Understand embeddings and vector search before going deep into RAG. RAG uses retrieval as one part of a larger answer-generation pipeline. A useful RAG system needs both relevant search results and a disciplined way to use them.

### Phase 4 — Build controlled agents, integrations, and evaluation

This phase moves from a model that responds to a prompt toward applications that can select tools, perform bounded tasks, integrate with other systems, and be tested.

| Course | What it covers | Start here |
|---|---|---|
| Agentic AI | Agent concepts, manual agent loop, validated tools, model tool calling, state and memory, planning, workflows, routing, multi-agent patterns, guardrails, human approval, reliability, evaluation, tracing, Agents SDK, production architecture, and a support-agent capstone. | [14-lesson Agentic AI course](study-materials/agentic-ai/README.md) |
| MCP | Model Context Protocol fundamentals, lifecycle, tools, resources, prompts, Python servers, client discovery, stdio and Streamable HTTP, authentication, security, errors, retries, testing, .NET integration, production deployment, governance, and a developer knowledge assistant capstone. | [14-lesson MCP course](study-materials/mcp/README.md) |
| LLM evaluation and observability | Evaluation datasets, human rubrics, deterministic checks, quality metrics, LLM judges, RAG and agent evaluation, red teaming, experiment tracking, CI quality gates, tracing, and production monitoring. | [LLM evaluation course](study-materials/llm-evaluation/README.md) |

**Recommended order:** Learn the manual agent loop and tool boundaries before depending on an agent framework. Learn MCP when an application needs a standard way to expose tools or context to compatible clients. Evaluation should be introduced early and expanded as the application grows.

### Phase 5 — Package, deploy, and operate applications

A working local application is only the start. This phase teaches repeatable packaging, orchestration, infrastructure, security, monitoring, and cloud delivery.

| Course | What it covers | Start here |
|---|---|---|
| Docker | Images and containers, Docker Engine, lifecycle commands, Dockerfiles, build context and layers, multi-stage .NET builds, networking, volumes, Compose, registries, image security, troubleshooting, build optimisation, CI/CD, and a production-minded capstone. | [14-lesson Docker course](study-materials/docker/README.md) |
| Kubernetes | Cluster architecture, Pods, Deployments, Services, configuration, probes, resources, networking, storage, security, Helm, observability, upgrades, incident triage, runbooks, SRE practices, and a production-support capstone. | [14-lesson Kubernetes course](study-materials/kubernetes/README.md) |
| Terraform | Infrastructure-as-code, HCL, providers, resources, variables, expressions, state, modules, remote backends, workspaces, Azure networking and identity, drift, import, state recovery, testing, CI/CD, security, and an Azure foundation capstone. | [14-lesson Terraform course](study-materials/terraform/README.md) |
| Azure | Cloud foundations, subscriptions, Entra ID, RBAC, compute, App Service, Functions, networking, storage, databases, messaging, Key Vault, .NET configuration, monitoring, CI/CD, Terraform, security, reliability, disaster recovery, cost, Azure AI, and an architecture capstone. | [14-lesson Azure course](study-materials/azure/README.md) |

**Recommended order:** Learn Docker before Kubernetes. Understand basic cloud and identity concepts before provisioning infrastructure with Terraform. Use Azure labs only in an authorised subscription and check costs before creating resources.

---

## Which section should I read first?

Choose the route that best matches your current experience.

### Route A — I am new to programming and AI

Follow this full foundation route:

1. [Linux](study-materials/linux/README.md) — start with terminal navigation, files, directories, and safe commands.
2. [Python](study-materials/python/README.md) — learn basic syntax, collections, functions, and exceptions before APIs or async code.
3. [GenAI](study-materials/genai/README.md) — understand AI, LLMs, tokens, and model APIs.
4. [Prompt engineering](study-materials/prompt-engineering/README.md) — learn to write clear instructions and validate structured outputs.
5. [Vector databases](study-materials/vector-databases/README.md) — understand embeddings and semantic retrieval.
6. [RAG](study-materials/rag/README.md) — combine retrieval with grounded answers.
7. [Agentic AI](study-materials/agentic-ai/README.md) — build a bounded tool-using agent.
8. [LLM evaluation](study-materials/llm-evaluation/README.md) — test quality, safety, and reliability.
9. [MCP](study-materials/mcp/README.md) — expose tools and context through a standard protocol.
10. Continue with Docker, Kubernetes, Terraform, and Azure when preparing to deploy and operate the application.

### Route B — I am already a software developer

I can move faster through programming basics, but I should not skip unfamiliar concepts.

1. Review [Linux](study-materials/linux/README.md) and complete the command-line and troubleshooting sections I have not used before.
2. Review the Python course for syntax, virtual environments, packages, APIs, JSON, and async programming.
3. Read the GenAI and prompt engineering courses to understand model behaviour, token limits, structured outputs, and prompt risks.
4. Continue with Vector Databases and RAG.
5. Build the Agentic AI capstone and study MCP.
6. Add LLM evaluation before considering the application production-ready.
7. Use Docker, Kubernetes, Terraform, and Azure based on the deployment requirements.

### Route C — I am a .NET developer moving into AI engineering

The examples and capstones can be adapted to a .NET application architecture:

1. Use the Python track to become comfortable reading and running Python SDK examples. I do not need to stop using .NET for application development.
2. Study GenAI concepts, embeddings, and prompt engineering.
3. Learn vector search and RAG, then expose retrieval through an ASP.NET Core API.
4. Use interfaces such as IEmbeddingService and IVectorSearchRepository to keep provider-specific code out of controllers.
5. Study Agentic AI and MCP, including the .NET integration lessons.
6. Add structured logging, tests, evaluation, authentication, and authorisation.
7. Package the API and workers with Docker, then learn Kubernetes only when the deployment model needs it.
8. Use Terraform and Azure to provision and operate the environment.

The key idea is transferable engineering: model providers and SDKs may change, but clear interfaces, validation, access control, testing, observability, and reliable operations remain important.

---

## Prerequisites

You do not need to know every tool before starting. The prerequisites depend on the course.

### Minimum prerequisites to begin

- A computer that can run a modern code editor and terminal.
- Ability to install approved developer tools.
- Basic file and folder navigation.
- Willingness to read command output and troubleshoot errors.
- Git and GitHub account for saving and tracking exercises (recommended).
- Internet access for official documentation and downloading packages.

### Helpful foundations by topic

| Topic | Useful prerequisite |
|---|---|
| Linux | None beyond basic computer use; terminal skills are taught from the beginning. |
| Python | No advanced programming required. Start with variables, conditions, loops, and functions in the course itself. |
| GenAI | Basic programming and ability to send an HTTP request are helpful. |
| Prompt engineering | Basic understanding of what an LLM does is useful, but the course introduces the concepts. |
| Vector databases | Basic Python lists, dictionaries, functions, JSON, and an understanding of embeddings. The first lessons explain the vector concepts. |
| RAG | Basic Python, files, APIs, embeddings, and introductory vector search. |
| Agentic AI | Python functions, classes, exceptions, modules, JSON, REST APIs, and basic async/await. |
| MCP | Basic programming, JSON, HTTP, and familiarity with client/server concepts are useful. See the MCP README for its exact prerequisites. |
| LLM evaluation | Basic GenAI and application testing concepts are helpful. |
| Docker | Terminal usage and basic application/HTTP knowledge. Linux familiarity is helpful. |
| Kubernetes | Docker/container fundamentals, YAML, networking, and basic Linux concepts. |
| Terraform | Command-line basics, cloud fundamentals, and basic networking and identity concepts. |
| Azure | Basic programming, HTTP, Git, and command-line familiarity. Prior Azure experience is not required. |

Each course README is the source of truth for its detailed prerequisites and lab setup. When a course depends on another track, follow the link in that course's README.

---

## How to study each lesson

Use the same learning routine across the repository. The material is intended to be practised, not just read.

### Step 1 — Understand the problem first

Before running a command or copying code, identify:

- What problem does this tool or concept solve?
- Why would a developer need it?
- What happens if I do not use it?
- In what situation should I choose another approach?

### Step 2 — Learn the terminology in plain language

Understand each new term before memorising it. For example, when learning Docker, understand the difference between an image and a container. When learning RAG, understand the difference between a document, a chunk, an embedding, and a retrieved result.

### Step 3 — Read the example line by line

Do not copy a long command or code block without understanding it. Identify its inputs, outputs, side effects, permissions, and assumptions.

### Step 4 — Run a small, safe example

Start with local or synthetic data. Run one command at a time. Inspect the output. Use disposable resources with clear names for labs.

### Step 5 — Change something and predict the result

Change one input, setting, or parameter. Predict what should happen before running the example. This is how I learn the relationship between the code and its behaviour.

### Step 6 — Test failure cases

Try an invalid input, missing configuration, unavailable service, or unexpected response. Learn how the error appears and how to troubleshoot it.

### Step 7 — Complete the exercise and self-check

Every detailed lesson should include practice activities and self-check questions. Try to answer the questions without looking at the explanation. Revisit the lesson when I cannot explain the result.

### Step 8 — Build and document

Complete the mini-project or capstone. Record setup steps, versions, assumptions, commands, test results, screenshots where useful, and known limitations.

---

## Recommended environment and tools

I do not need to install every tool on day one. Install a tool when a lesson requires it.

| Tool | Why it is useful |
|---|---|
| Visual Studio Code or Visual Studio | Edit and debug source code. |
| Git and GitHub | Version control, branches, commits, and collaboration. |
| Python and virtual environments | Run Python lessons and AI SDK examples in isolated environments. |
| .NET SDK | Build ASP.NET Core APIs and .NET integrations. |
| PowerShell, Bash, or a Linux terminal | Practise commands and developer workflows. |
| Docker Desktop or Docker Engine | Run containers and local supporting services. |
| kubectl and a local Kubernetes option | Practise Kubernetes when that phase begins. |
| Terraform CLI | Validate and plan infrastructure changes. |
| Azure CLI | Inspect and manage authorised Azure resources. |
| Postman or an equivalent HTTP client | Test API requests and responses. |

Use supported versions from the official documentation and record the versions used in each project. Tool availability and installation steps can vary by operating system.

---

## Recommended study schedule

The following is a flexible first-pass estimate, assuming around 1–2 focused hours per day. It is not a deadline and does not include all project, revision, or cloud troubleshooting time.

| Phase | Topics | Suggested time |
|---|---|---:|
| 1 | Linux, Python, and Git habits | 3–4 weeks |
| 2 | GenAI fundamentals and prompt engineering | 1–2 weeks |
| 3 | Vector databases and RAG | 2–3 weeks |
| 4 | Agentic AI, MCP, and LLM evaluation | 3–4 weeks |
| 5 | Docker, Kubernetes, Terraform, and Azure | 4–6 weeks |

A learner with software development experience can shorten the foundation phase and spend more time on AI retrieval, evaluation, security, and deployment. Do not rush past a topic that is needed by the next project.

---

## The recommended end-to-end project

The repository is designed to lead toward one integrated project: a **Developer Knowledge Assistant**.

The project can grow step by step:

1. **Local foundation:** create a small Python CLI or API that accepts a question and validates the input.
2. **Model integration:** add a model adapter with secure configuration and clear request/response contracts.
3. **Knowledge ingestion:** read permitted Markdown or sample documents, extract content, and split it into chunks.
4. **Embeddings and storage:** generate embeddings and store vectors with source metadata.
5. **Retrieval:** search for relevant chunks and return source IDs and citations.
6. **RAG:** generate answers grounded in retrieved content and abstain when evidence is insufficient.
7. **Agent tools:** expose a small set of read-only tools with input validation and permission checks.
8. **MCP integration:** expose selected tools/resources to compatible clients through an MCP server.
9. **Evaluation:** create test questions, expected sources, quality metrics, regression tests, and adversarial tests.
10. **Application API:** expose the capability through an ASP.NET Core API if that fits the application architecture.
11. **Containerisation:** package the API, worker, and required dependencies using Docker.
12. **Cloud and operations:** provision approved infrastructure with Terraform and deploy to Azure with monitoring, security, backup, and cost controls.
13. **Optional orchestration:** use Kubernetes when the workload and operational needs justify it.

Build the smallest useful version first. Add agents, orchestration, and Kubernetes only when they solve a real problem.

---

## Quality, security, and responsible engineering principles

These rules apply across every course and project:

- **Validate input:** never assume user input, external API responses, or model-generated output is correct.
- **Enforce access in trusted code:** prompts are not an authorisation boundary. Check identity and permissions on the backend.
- **Use least privilege:** tools and service identities should have only the permissions they need.
- **Treat retrieved content as untrusted:** documents and tool outputs can contain misleading or malicious instructions.
- **Protect secrets:** do not commit API keys, passwords, tokens, certificates, company configuration, customer data, or production logs.
- **Control side effects:** require approval for consequential or irreversible actions.
- **Bound resource use:** set limits for retries, timeouts, concurrency, output size, model tokens, and spend.
- **Test failure paths:** include invalid input, unavailable services, timeouts, partial failures, and permission denial.
- **Measure quality:** evaluate whether retrieval and generated answers are useful, grounded, and safe.
- **Observe the application:** use structured logs, metrics, traces, alerts, and correlation IDs.
- **Plan recovery:** understand backups, restore procedures, data retention, and incident response.
- **Clean up labs:** cloud resources, containers, volumes, and databases can persist or incur cost. Verify before deleting anything.

Use synthetic, public, or explicitly approved data in learning exercises.

---

## Course progress checklist

Use this checklist as a personal progress tracker. The course README inside each folder contains the lesson-by-lesson roadmap.

- [ ] I can navigate a terminal and explain basic Linux files, permissions, processes, and networking.
- [ ] I can write Python functions, work with collections, handle exceptions, use modules, and call APIs.
- [ ] I can use Git branches and commits and keep secrets out of repositories.
- [ ] I can explain the basic role and limitations of an LLM.
- [ ] I can write structured prompts and validate model outputs.
- [ ] I understand embeddings, vector search, metadata filters, and retrieval quality.
- [ ] I can explain and build a basic RAG pipeline with source references.
- [ ] I understand an agent loop, tool calling, validation, state, and human approval.
- [ ] I can expose a permitted tool or resource through MCP.
- [ ] I can create evaluation cases and measure application quality.
- [ ] I can build and troubleshoot Docker images and containers.
- [ ] I understand Kubernetes workloads, services, configuration, and basic troubleshooting.
- [ ] I can create and review Terraform plans and understand state.
- [ ] I can deploy and operate an application on Azure with identity, monitoring, security, and cost controls.
- [ ] I have built and documented an integrated Developer Knowledge Assistant.

---

## Repository structure

```text
developer-ai-journey/
├── study-materials/
│   ├── linux/
│   ├── python/
│   ├── genai/
│   ├── prompt-engineering/
│   ├── vector-databases/
│   ├── rag/
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

### Course directory convention

- The course `README.md` is the entry point. Read it first.
- Numbered Markdown files provide the recommended lesson order.
- Practical exercises and self-check questions help reinforce each topic.
- Projects combine the knowledge from multiple lessons.
- Official references are included where useful; check them for current APIs and product behaviour.

---

## Official documentation and learning references

Use the official documentation when installing tools, selecting versions, configuring cloud services, or implementing provider-specific code.

- [Python documentation](https://docs.python.org/3/)
- [Microsoft .NET documentation](https://learn.microsoft.com/dotnet/)
- [Microsoft Learn — Azure](https://learn.microsoft.com/azure/)
- [Docker documentation](https://docs.docker.com/)
- [Kubernetes documentation](https://kubernetes.io/docs/)
- [Terraform documentation](https://developer.hashicorp.com/terraform/docs)
- [OpenAI platform documentation](https://platform.openai.com/docs/)
- [Model Context Protocol documentation](https://modelcontextprotocol.io/docs/)
- [Qdrant documentation](https://qdrant.tech/documentation/)
- [pgvector](https://github.com/pgvector/pgvector)

Provider APIs, product names, model availability, SDK versions, pricing, quotas, and cloud capabilities change. Verify current official documentation before using an example in a real project.

## Final note

This is a living learning repository. It is designed to help me move from understanding individual technologies to building systems that work as a whole. Reading the lessons is the beginning; the real learning comes from running the examples, debugging failures, making design decisions, testing assumptions, and completing projects.

**My learning principle:** understand the problem first, learn the concept, practise with a small example, test failure cases, and then apply it in a real project.
