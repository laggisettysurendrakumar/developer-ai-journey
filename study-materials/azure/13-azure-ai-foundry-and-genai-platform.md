# 13 — Azure AI Foundry and Generative AI Workloads

## Purpose of this lesson

This lesson connects Azure platform knowledge to AI applications. It introduces the Azure AI service landscape, but model names, deployment options, SDKs, API versions, quotas, and portal experiences change frequently. Treat official service documentation as the source of truth when implementing a real workload.

## 1. Separate the application from the model

A reliable AI application usually has several components:
- API or user interface.
- Authentication and authorisation.
- Model endpoint.
- Prompt and orchestration logic.
- Retrieval and data stores.
- Tool integrations.
- Content safety controls.
- Logging, evaluation, and monitoring.

The model is one dependency, not the entire application. Keep business rules, permissions, and critical validation outside model-generated text.

## 2. Azure AI Foundry concepts

Azure AI Foundry is Microsoft's platform for building and operating AI applications and agents. Learn the current concepts for projects/hubs or their current successors, model catalog, model deployments, endpoints, evaluation, tracing/observability, content safety, and agent capabilities. Microsoft product naming and UX may evolve; use the current documentation for exact terminology and steps.

When selecting a model:
- Confirm availability in the target region and subscription.
- Review throughput/quota and rate limits.
- Understand model/version lifecycle.
- Check supported API and SDK.
- Review data processing, privacy, and responsible AI requirements.
- Estimate cost using expected workload.
- Test quality on representative examples.

## 3. Retrieval-augmented generation (RAG)

A RAG system retrieves relevant content from approved sources and includes that context in a model request.

Typical flow:
1. Ingest approved files.
2. Extract and clean text.
3. Split into chunks with useful metadata.
4. Generate embeddings where needed.
5. Store searchable content and vectors.
6. Authenticate the user.
7. Apply document-level access filters.
8. Retrieve relevant chunks.
9. Generate an answer grounded in those chunks.
10. Return citations or source references.
11. Evaluate answer relevance and access correctness.

Retrieval must enforce the user's access rights before content reaches the model. A prompt instruction is not a substitute for a server-side permission check.

## 4. Embeddings and search

An embedding maps text into a numerical representation that can be compared for semantic similarity. Vector search retrieves items whose embeddings are close to a query embedding.

Learn:
- Chunk size and overlap trade-offs.
- Embedding model compatibility.
- Vector and keyword/hybrid search.
- Filters and metadata.
- Re-ranking.
- Index updates and deletion.
- Evaluation of retrieval recall and precision.

Do not assume that the nearest vector is necessarily a correct answer. Combine search quality measurement with source validation.

## 5. Agents and tool access

An agent may decide to call a tool such as a search service or ticket API. The application must validate the tool arguments, authenticate the user, authorise the requested action, and enforce limits before execution.

For side-effecting operations—creating tickets, changing configuration, sending messages—consider explicit human approval, idempotency, audit logs, and rollback.

## 6. Safety and governance

Plan for prompt injection, data leakage, sensitive information, harmful or unsupported output, over-permissioned tools, and cross-user data access. Use allowlisted tools, least privilege, input/output validation, safety filters, content provenance, and human review for high-impact actions.

## Practice exercise

Design an internal developer-support assistant for .NET and Azure documentation. Specify the model endpoint, identity, document storage, search index, permission filtering, API hosting, monitoring, evaluation dataset, and tool boundaries. Explain how the system prevents a user from retrieving documents they are not entitled to see.

## Self-check

- What is the difference between a model endpoint and the application around it?
- What are the main steps in a RAG ingestion pipeline?
- Why must access filtering occur before retrieved text is sent to a model?
- What does an embedding represent?
- Why must tool execution be authorised outside the prompt?

## Official references

- Azure AI Foundry: https://learn.microsoft.com/azure/ai-foundry/
- Azure OpenAI: https://learn.microsoft.com/azure/ai-services/openai/
- Azure AI Search: https://learn.microsoft.com/azure/search/
- Responsible AI: https://learn.microsoft.com/azure/ai-foundry/responsible-ai/
