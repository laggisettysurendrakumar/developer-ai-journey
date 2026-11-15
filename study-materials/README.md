# Study Materials — Preparation and Prerequisites

This folder contains the learning tracks for the Developer AI Journey. The courses are designed to take me from software and programming foundations to building, testing, securing, deploying, and operating AI-powered applications.

**Start with this README before opening a course.** It explains what background knowledge is useful, what tools to prepare, which course to read first, and how to study effectively.

You do not need to install every tool or complete every course before starting. Prepare the common foundations first, then install tools only when a lesson needs them.

---

## 1. What should I know before starting?

### Essential basics

These skills are enough to begin:
- Use a computer comfortably: create folders, manage files, install approved software, and use a code editor.
- Understand basic programming ideas such as variables, conditions, loops, functions, and errors. The Python course teaches these from the beginning.
- Know what a website or API is at a high level. HTTP, JSON, and client/server communication are introduced along the way.
- Be willing to use a terminal, read command output, and troubleshoot one problem at a time.
- Use Git and GitHub to save and track work. Basic Git knowledge is helpful and can be learned while following the courses.

Prior experience with AI, cloud computing, Docker, or Kubernetes is **not required** to start the foundation tracks.

### Helpful, but not mandatory

- Basic SQL and relational database concepts.
- Familiarity with REST APIs and JSON.
- Basic networking: IP addresses, DNS, ports, HTTP, and TLS.
- Basic Linux command-line usage.
- Basic object-oriented programming.
- Familiarity with Visual Studio, Visual Studio Code, or another IDE.

Do not delay starting because you do not know all of these. Follow the learning order and use the relevant prerequisite course when a topic is unfamiliar.

## 2. Recommended preparation order

The tracks are grouped by dependencies. Follow this sequence for a structured first pass.

### Stage 1 — Prepare the developer foundation

1. **[Linux](linux/README.md)** — start with terminal navigation, files and directories, permissions, processes, services, networking, and safe troubleshooting.
2. **[Python](python/README.md)** — learn syntax, data types, collections, conditions, loops, functions, modules, exceptions, files, JSON, APIs, packages, and async programming.
3. **Git and GitHub habits** — practise creating a repository, cloning it, creating a branch, committing changes, and writing a useful README.

**Ready to move on when:** I can navigate the terminal, run a Python script, install a package in a virtual environment, read an error message, and save my work with Git.

### Stage 2 — Understand generative AI and prompts

4. **[GenAI](genai/README.md)** — understand AI/ML foundations, LLMs, transformers, tokens, context windows, model APIs, embeddings, structured outputs, and multimodal concepts.
5. **[Prompt Engineering](prompt-engineering/README.md)** — learn prompt structure, instructions, context, examples, structured outputs, task decomposition, prompt-injection risks, and evaluation.

**Ready to move on when:** I can explain what an LLM does, call a model API safely in a small example, and validate the response rather than trusting it blindly.

### Stage 3 — Learn retrieval and knowledge grounding

6. **[Vector Databases](vector-databases/README.md)** — understand vectors and embeddings, similarity metrics, Qdrant, storing and searching vectors, metadata filters, ANN, hybrid search, pgvector, and production operations.
7. **[RAG](rag/README.md)** — build the retrieval-augmented generation flow: ingestion, parsing, chunking, embeddings, retrieval, reranking, grounded answers, citations, evaluation, and security.

**Ready to move on when:** I can explain how a document becomes chunks and embeddings, search for relevant content, apply metadata filters, and identify the sources used to support an answer.

### Stage 4 — Build agents and integrations responsibly

8. **[Agentic AI](agentic-ai/README.md)** — learn the agent loop, tool calling, validation, state, memory, planning, orchestration, safety, human approval, reliability, and evaluation.
9. **[MCP](mcp/README.md)** — learn the Model Context Protocol, tools, resources, prompts, servers, clients, transports, authentication, security, testing, and .NET integration.
10. **[LLM Evaluation](llm-evaluation/README.md)** — learn evaluation datasets, test cases, quality metrics, human review, LLM-as-judge patterns, red teaming, tracing, and production monitoring.

**Ready to move on when:** I can build a bounded tool-using workflow, validate tool inputs, enforce permissions in trusted code, test failure cases, and measure application quality.

### Stage 5 — Package, deploy, and operate

11. **[Docker](docker/README.md)** — learn images, containers, Dockerfiles, networking, volumes, Compose, registries, security, troubleshooting, and CI/CD.
12. **[Kubernetes](kubernetes/README.md)** — learn cluster fundamentals, workloads, services, configuration, storage, networking, security, Helm, monitoring, upgrades, and incident support.
13. **[Terraform](terraform/README.md)** — learn infrastructure-as-code, HCL, providers, resources, state, modules, remote backends, environment strategy, Azure resources, drift, and CI/CD.
14. **[Azure](azure/README.md)** — learn cloud foundations, identity, compute, networking, storage, databases, secrets, monitoring, deployment, security, reliability, cost, and AI workloads.

**Ready to complete the journey when:** I can package an application, explain how it is deployed, configure identity and secrets safely, inspect logs and metrics, and describe how infrastructure changes are reviewed and recovered.

---

## 3. Prerequisites by course

Each course README contains its detailed syllabus and course-specific lab requirements. This table is a quick preparation guide.

| Course | What I should know first | Required before starting? |
|---|---|---|
| Linux | Basic computer and file management | No prior Linux experience |
| Python | Basic computer use; programming experience is optional | No prior Python experience |
| GenAI | Basic programming concepts and willingness to use APIs | Linux/Python foundations recommended |
| Prompt Engineering | Basic understanding of text-based AI assistants | GenAI introduction recommended |
| Vector Databases | Python lists, dictionaries, functions, JSON; basic embedding idea | Python basics recommended |
| RAG | Python, files, APIs, embeddings, introductory vector search | GenAI and vector database foundations recommended |
| Agentic AI | Python functions, classes, exceptions, modules, JSON, REST, basic async/await | Python foundations recommended |
| MCP | Basic programming, JSON, HTTP, client/server concepts | Python or .NET API basics recommended |
| LLM Evaluation | Basic model/API usage, testing concepts, application workflows | GenAI and RAG/Agentic AI introductions recommended |
| Docker | Terminal use, basic application and HTTP concepts | Linux basics helpful |
| Kubernetes | Containers, Docker, YAML, networking, basic Linux | Docker fundamentals strongly recommended |
| Terraform | Command line, cloud basics, networking, identity, resource concepts | Azure foundations helpful; course introduces Terraform |
| Azure | Basic programming, HTTP, Git, command-line familiarity | No prior Azure experience |

### For experienced .NET developers

I can use my existing C# and ASP.NET Core knowledge while learning the Python examples used by many AI libraries. I do not need to replace .NET with Python.

1. Review Linux and learn enough Python to read and run the course examples.
2. Learn GenAI, prompt engineering, embeddings, vector databases, and RAG.
3. Build application boundaries in ASP.NET Core using interfaces for model, embedding, and retrieval providers.
4. Study Agentic AI and MCP, including the .NET integration material.
5. Add evaluation, authentication, authorisation, logging, and monitoring.
6. Learn Docker, Terraform, and Azure for repeatable deployment. Study Kubernetes when the deployment requirement justifies it.

## 4. Prepare your development environment

Do not install everything on the first day. Start with the basic tools and add others when a course requires them.

### Install first

| Tool | Purpose | When I need it |
|---|---|---|
| Git | Track code changes and work with repositories | From the beginning |
| Visual Studio Code or Visual Studio | Edit and debug code | From the beginning |
| Python 3 | Run Python lessons and many AI SDK examples | Python and AI tracks |
| .NET SDK | Build ASP.NET Core APIs and .NET integrations | .NET examples and capstone |
| A terminal | Run commands, scripts, and developer tools | From the beginning |
| Web browser | Read official docs and test web applications | From the beginning |

### Install later, when lessons require them

| Tool | Purpose | Course |
|---|---|---|
| Docker Desktop or Docker Engine | Build and run containers | Docker |
| Qdrant local service or Docker image | Store and search vectors | Vector Databases |
| PostgreSQL with pgvector | Practise relational vector search | Vector Databases |
| Postman or another HTTP client | Send and inspect API requests | GenAI, MCP, Azure |
| kubectl and a local Kubernetes environment | Manage and practise a Kubernetes cluster | Kubernetes |
| Terraform CLI | Plan and provision infrastructure | Terraform |
| Azure CLI | Inspect and manage authorised Azure resources | Azure |

Use official installation instructions for your operating system. Keep track of installed versions and check whether a lesson depends on a particular SDK or server version.

### Suggested local workspace

Create a dedicated folder for experiments instead of placing every exercise in one directory:

~~~text
learning-workspace/
├── python-practice/
├── genai-experiments/
├── vector-search-lab/
├── rag-project/
├── agentic-ai-lab/
├── mcp-lab/
├── docker-labs/
└── developer-knowledge-assistant/
~~~

Use a separate Python virtual environment for each Python project. This avoids mixing dependencies between lessons and projects.

Never commit virtual environment folders, API keys, .env files, certificates, private documents, or generated secrets to GitHub.

## 5. Accounts, subscriptions, and cost preparation

Most foundation lessons can be completed locally. Some later exercises may use external APIs or cloud services.

Before using a paid service:
- Check whether the lesson can be completed with a local or free alternative.
- Read the current pricing and usage limits.
- Set usage limits or budgets where available.
- Use a development project or subscription that you are authorised to access.
- Never use company or production credentials for personal exercises.
- Delete disposable cloud resources when the lab is complete.
- Keep API keys in environment configuration or a secret manager, never in source code.

An Azure subscription is not required to begin Linux, Python, GenAI fundamentals, prompt engineering, or local vector database lessons. It becomes useful when following Azure and cloud deployment labs.

## 6. How to prepare for each lesson

Use this routine whenever starting a new numbered lesson.

### Before reading
1. Read the course README.
2. Check the prerequisites and expected outcome.
3. Identify unfamiliar terms and tools.
4. Confirm the required software and version.
5. Understand whether the exercise is local, external-API based, or cloud based.

### While learning
1. Read the explanation before executing the command or code.
2. Understand what problem the example solves.
3. Type the example and inspect each important line or parameter.
4. Predict the output before running it.
5. Change one value and observe what changes.
6. Try an invalid input or failure case.
7. Note any errors and how they were resolved.

### After the lesson
1. Complete the practice exercise without copying the solution blindly.
2. Answer the self-check questions in your own words.
3. Summarise the key concept in a few sentences.
4. Commit working code and notes to Git.
5. Move forward when you can explain what you built and why it works.

### A simple learning-notes template

~~~text
Lesson:
What problem does this solve?
Important terms:
Commands or code I practised:
Expected result:
What happened when I changed an input?
Error or issue I faced:
How I fixed it:
What I can now explain:
What I still need to practise:
~~~

## 7. How much time should I plan?

Use this as a rough planning guide, not a deadline.

| Preparation or learning area | Suggested time |
|---|---:|
| Linux fundamentals | 1–2 weeks |
| Python foundations | 2–3 weeks |
| GenAI and prompt engineering | 1–2 weeks |
| Vector databases and RAG | 2–3 weeks |
| Agentic AI, MCP, and evaluation | 3–4 weeks |
| Docker, Kubernetes, Terraform, and Azure | 4–6 weeks |

An experienced developer can move faster through topics they already know. Spend more time on hands-on exercises, debugging, evaluation, and the integrated capstone than on memorising definitions.

## 8. Common mistakes to avoid

- **Installing everything at once:** install a tool only when the lesson needs it.
- **Skipping the course README:** the README explains lesson order and prerequisites.
- **Copying commands without understanding them:** know what a command changes before running it.
- **Skipping practice exercises:** reading alone does not build troubleshooting skills.
- **Using production data in experiments:** use synthetic, public, or explicitly approved data.
- **Committing secrets:** check Git changes before pushing to GitHub.
- **Trusting model output without validation:** model responses can be incorrect or incomplete.
- **Treating prompts as security controls:** permissions must be checked in trusted application code.
- **Ignoring cost and cleanup:** containers, volumes, model APIs, and cloud resources can consume resources or incur charges.
- **Moving to advanced tools too early:** learn the problem and fundamentals before adopting frameworks or orchestration.

## 9. Ready-to-start checklist

- [ ] I have read this preparation guide.
- [ ] I have selected the course that matches my current level.
- [ ] I have opened that course README and reviewed its prerequisites.
- [ ] I understand what I am expected to build or explain.
- [ ] I have a code editor and terminal ready.
- [ ] I have Git configured and know how to save my work.
- [ ] I have installed only the tools required for the first lessons.
- [ ] I know where to find official documentation.
- [ ] I will use local or approved sample data.
- [ ] I will check permissions and costs before using cloud or paid services.
- [ ] I will complete the exercises and self-checks rather than only reading.

## 10. Course entry points

Once prepared, open the relevant course README. Each README is the entry point for that track and contains its lesson sequence.

| Learning track | Course entry point |
|---|---|
| Linux | [Start Linux](linux/README.md) |
| Python | [Start Python](python/README.md) |
| GenAI | [Start GenAI](genai/README.md) |
| Prompt Engineering | [Start Prompt Engineering](prompt-engineering/README.md) |
| Vector Databases | [Start Vector Databases](vector-databases/README.md) |
| RAG | [Start RAG](rag/README.md) |
| Agentic AI | [Start Agentic AI](agentic-ai/README.md) |
| MCP | [Start MCP](mcp/README.md) |
| LLM Evaluation | [Start LLM Evaluation](llm-evaluation/README.md) |
| Docker | [Start Docker](docker/README.md) |
| Kubernetes | [Start Kubernetes](kubernetes/README.md) |
| Terraform | [Start Terraform](terraform/README.md) |
| Azure | [Start Azure](azure/README.md) |

**Recommended first step:** Start with [Linux](linux/README.md) if terminal usage is new, or [Python](python/README.md) if programming foundations need practice. If those skills are already comfortable, read the [GenAI course](genai/README.md) and continue through the recommended sequence.

The purpose of this folder is to help me prepare properly, learn each topic in the right order, and turn the knowledge into working projects—not simply finish a list of lessons.
