# Azure for GenAI and Agentic Applications — Beginner to Deployment

I use Azure to host, secure, monitor, and operate AI-enabled applications. This track connects Python APIs, models, storage, identity, networking, and infrastructure into a production-minded design. It is not a guide to creating every Azure service; choose services based on requirements, cost, and organisational standards.

## Prerequisites and learning order

Understand Python APIs, HTTP, environment variables, Linux, Docker, and basic cloud concepts. Familiarity with Azure fundamentals and developer services is useful.

1. Understand subscriptions, resource groups, regions, and resource hierarchy.
2. Learn Microsoft Entra ID, managed identities, and Azure RBAC.
3. Choose an application hosting model: App Service, Container Apps, or AKS.
4. Understand Azure AI model/service deployments, quotas, and data governance.
5. Store documents and application data securely.
6. Use Key Vault and managed identity for secrets and service access.
7. Add logs, traces, metrics, and alerts.
8. Design networking and private access.
9. Automate infrastructure and deployments.
10. Deploy and operate a non-production AI assistant with cost and recovery controls.

## 1. Azure in plain English

Azure is Microsoft's cloud platform. An organisation can provision compute, network, storage, identity, databases, and AI services on demand instead of buying and maintaining every server.

| Term | Plain-English meaning |
|---|---|
| Tenant | Identity boundary associated with Microsoft Entra ID |
| Subscription | Billing and management boundary for Azure resources |
| Resource group | Logical container for related resources |
| Region | Geographic Azure location where a resource is deployed |
| Resource | An individual Azure service instance |
| RBAC | Role-based access control: which identity can do what at a scope |
| Managed identity | Azure-managed identity an application can use without keeping a client secret in code |

A resource may be unavailable in a particular region or subscription. Verify service availability, quotas, and policy before designing a solution.

## 2. Identity: managed identity and least privilege

Applications need identities to call storage, Key Vault, or AI services. Avoid storing client secrets in code or configuration when a supported managed identity can be used.

Conceptual Python example:

```python
import os

app_environment = os.getenv("APP_ENV", "development")
service_name = os.getenv("SERVICE_NAME", "knowledge-assistant")

print(f"Starting {service_name} in {app_environment}")
```

The app reads non-secret configuration from the environment. In Azure, grant a managed identity only the roles and scopes it needs. Managed identity removes the need to manage an app secret for supported authentication flows, but it does not remove the need for authorisation.

## 3. Choose an application hosting model

| Service | When it can fit | What to consider |
|---|---|---|
| Azure App Service | Web apps and APIs with a managed platform experience | Runtime support, networking, scale, platform constraints |
| Azure Container Apps | Containerised APIs, background workers, event-driven workloads | Scale rules, revisions, environment, networking |
| Azure Kubernetes Service (AKS) | More control over Kubernetes workloads and cluster ecosystem | Cluster operations, upgrades, networking, security, cost |

Do not choose AKS just because the application uses AI. Use the simplest hosting model that meets scaling, networking, compliance, and operational requirements.

## 4. Model services and Azure AI

Azure provides managed AI services, including Azure OpenAI and Azure AI Foundry capabilities. Before choosing a deployment, check:
- Which model is available to the subscription and target region.
- Deployment name/model version and quota.
- Authentication method and endpoint.
- Data handling, retention, and compliance requirements.
- Price, token usage, rate limits, and throughput.
- Content safety and responsible AI requirements.

Service names, portal experiences, API versions, model availability, and regional quota can change. Use current Microsoft documentation for the exact deployment and SDK path.

## 5. Store knowledge documents

A RAG assistant usually needs a source for documents and metadata. Azure Storage can hold files, while a search or vector service indexes content for retrieval.

Think about file layout, private access, identity-based access, document IDs, versions, access groups, retention, deletion, backups, and data classification.

The retrieval service must filter documents by the current user's permissions before passages are returned to the model.

## 6. Key Vault and secrets

Key Vault is designed to store and control access to secrets, keys, and certificates. Prefer managed identity to access it from an Azure-hosted app where supported.

Do not put API keys in Git, bake credentials into container images, print tokens into application logs, or grant every application identity broad secret-read permissions.

Use the smallest required access scope and rotate credentials according to organisational policy.

## 7. Monitoring with Azure Monitor and Application Insights

A production AI app needs more than a server-is-up check. Observe request count, error rate, latency, dependency calls, timeouts, model request duration, token usage, retrieval duration, tool outcomes, CPU, memory, queue depth, restarts, cost, and quota alerts.

Use correlation IDs to connect a web request with model calls, retrieval, and tools. Avoid collecting sensitive prompts or documents unless there is a reviewed need and appropriate redaction and retention controls.

## 8. Networking

Decide whether an endpoint must be public or private. Consider inbound application access, outbound model/storage/search access, private endpoints and DNS, firewall rules, TLS certificates, workload identity, and egress controls.

A private endpoint alone does not configure every DNS, routing, and identity requirement. Test the full connectivity path.

## 9. Example architecture for a RAG assistant

```text
User
  |
  v
Web/API hosting (App Service or Container Apps)
  |                 |
  |                 +--> Managed identity --> Key Vault / Azure services
  |
  +--> Model service (approved deployment)
  |
  +--> Document storage --> ingestion/embedding pipeline
  |
  +--> Search/vector service --> retrieved chunks + source metadata
  |
  +--> Application Insights / Azure Monitor
```

This is a conceptual design. Exact services, network paths, identity roles, and model APIs depend on the Azure environment and security requirements.

## 10. Practice project

Deploy the Dockerised Developer Knowledge Assistant to a non-production Azure environment:
1. Choose App Service or Container Apps and document why.
2. Create an isolated resource group and naming/tagging standard.
3. Configure managed identity.
4. Deploy the model service in an approved region with suitable quota.
5. Store sample documents in private storage.
6. Configure search/retrieval and document permission filters.
7. Store secrets in Key Vault or use identity-based authentication.
8. Add Application Insights and alerts.
9. Configure network access and TLS.
10. Define a cost budget, token limits, rollback, and incident process.
11. Manage resources with reviewed Terraform plans.

Provision resources only in an authorised subscription and check pricing before deployment.

## Self-check

1. What is the difference between a tenant, subscription, and resource group?
2. Why is managed identity preferable to a hardcoded service secret?
3. When might Container Apps be simpler than AKS?
4. Why should a RAG service filter documents before model generation?
5. Which latency, reliability, and cost signals should an AI application monitor?

## Official references

- Azure Well-Architected Framework: https://learn.microsoft.com/azure/well-architected/
- Managed identities: https://learn.microsoft.com/entra/identity/managed-identities-azure-resources/overview
- Azure OpenAI documentation: https://learn.microsoft.com/azure/ai-services/openai/
- Azure AI Foundry: https://learn.microsoft.com/azure/ai-foundry/
- Application Insights: https://learn.microsoft.com/azure/azure-monitor/app/app-insights-overview
- Azure Container Apps: https://learn.microsoft.com/azure/container-apps/
- Azure App Service: https://learn.microsoft.com/azure/app-service/

Azure services, model availability, regions, quotas, SDKs, pricing, and portal experiences change. Confirm current details before deployment.
