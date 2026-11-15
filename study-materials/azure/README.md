# Azure Engineering — Beginner to Advanced

This track builds a practical understanding of Microsoft Azure, starting with cloud concepts and finishing with the design, deployment, security, and operation of a production-minded application.

The aim is to understand not just how to create an Azure resource, but why to choose it, how an application connects to it, how to secure it, what happens when it fails, and how to keep it observable and cost-effective.

## Who this learning path is for

This material starts from the basics and gradually moves to architecture and operations. Examples are especially relevant to developers building ASP.NET Core APIs, containerised services, and GenAI/Agentic AI applications.

Prior Azure experience is not required. Basic programming, HTTP, Git, and command-line familiarity will help. Azure fundamentals certification knowledge is useful but not mandatory.

## How to study each lesson

For every lesson:
1. Read the plain-English explanation before running commands.
2. Understand the problem the Azure service is intended to solve.
3. Draw the service or request flow in your own words.
4. Run only the lab steps in a subscription where you are authorised to create resources.
5. Check the cost and clean up disposable resources after the lab.
6. Answer the self-check questions without looking at the lesson.
7. Write down decisions, assumptions, and troubleshooting observations.

Do not deploy learning examples in a production subscription. Some labs create billable resources. Check pricing, permissions, quotas, regional availability, and organisational policies first.

## Learning roadmap

| Stage | Lessons | What I learn |
|---|---|---|
| Beginner | 01–02 | Cloud basics, Azure hierarchy, CLI, Entra ID, RBAC, managed identities, governance |
| Application developer | 03–06 | Compute, hosting, networking, storage, databases, messaging, Key Vault, .NET configuration |
| Operations and DevOps | 07–09 | Monitoring, KQL, alerts, pipelines, deployment strategies, Terraform |
| Advanced engineering | 10–12 | Security, threat modelling, reliability, backup/DR, cost optimisation |
| AI workloads and architecture | 13–14 | Azure AI, RAG, agents, Well-Architected design, production capstone |

## Lesson index

### Beginner — Understand Azure and identity

1. [Cloud and Azure Foundations](01-cloud-and-azure-foundations.md) — cloud models, tenants, subscriptions, resource groups, regions, tags, Azure CLI, and a safe first lab.
2. [Identity, RBAC and Governance](02-identity-rbac-and-governance.md) — Entra ID, users, groups, app identities, managed identities, role assignments, Azure Policy, and resource locks.

### Application developer — Build and connect workloads

3. [Compute and Application Hosting](03-compute-and-app-hosting.md) — Virtual Machines, App Service, Functions, Container Apps, AKS, and choosing a hosting model.
4. [Networking and Private Connectivity](04-networking-and-private-connectivity.md) — VNets, subnets, IP ranges, NSGs, DNS, private endpoints, routing, and troubleshooting.
5. [Storage, Databases and Messaging](05-storage-databases-and-messaging.md) — Blob Storage, Azure SQL, Cosmos DB, queues, Service Bus, Event Grid, Event Hubs, and reliable consumers.
6. [Key Vault, Secrets and Configuration](06-key-vault-and-configuration.md) — Key Vault, managed identity, secret rotation, environment settings, and ASP.NET Core configuration precedence.

### Operations and DevOps — Deploy and observe

7. [Monitoring, Logging and Alerts](07-monitoring-logging-and-alerts.md) — Azure Monitor, Application Insights, metrics, logs, traces, KQL, alerts, and incident investigation.
8. [Deployment, CI/CD and Azure DevOps](08-deployment-cicd-and-devops.md) — pipelines, secure deployment identity, artifacts, approvals, slots, rolling, blue/green, and canary releases.
9. [Infrastructure as Code with Terraform](09-infrastructure-as-code-terraform.md) — providers, resources, state, plans, modules, remote backend, and CI/CD integration.

### Advanced engineering — Protect, recover and optimise

10. [Azure Security and Defence in Depth](10-security-networking-and-defence.md) — shared responsibility, secure identity, network/data protection, Defender for Cloud, governance, and threat modelling.
11. [Reliability, Backup and Disaster Recovery](11-reliability-backup-and-disaster-recovery.md) — availability, RTO/RPO, retries, circuit breakers, backups, failover, runbooks, and recovery tests.
12. [Cost Management and Optimisation](12-cost-management-and-optimisation.md) — pricing models, estimates, budgets, right-sizing, cost allocation, and GenAI cost drivers.

### AI workloads and architecture — Apply the platform

13. [Azure AI Foundry and Generative AI Workloads](13-azure-ai-foundry-and-genai-platform.md) — model deployments, RAG, embeddings, search, agents, tool permissions, safety, and evaluation.
14. [Architecture, Well-Architected Design and Capstone](14-architecture-well-architected-and-capstone.md) — five architecture pillars, decision records, production readiness, and a Developer Knowledge Assistant.

## Recommended hands-on sequence

Build one small application throughout the course rather than creating unrelated demos:

- Start with an ASP.NET Core Web API running locally.
- Deploy it to an appropriate Azure hosting service.
- Add Microsoft Entra authentication and least-privilege service identity.
- Connect private or appropriately restricted storage and a database.
- Move required secrets to Key Vault.
- Add background processing through a queue when the workload needs it.
- Add Application Insights and useful alerts.
- Build the infrastructure with Terraform.
- Deploy through a CI/CD pipeline using secure authentication.
- Add recovery, security, and cost controls.
- Extend the application into a document-grounded AI assistant.

## Suggested pace

A steady schedule might take 6–10 weeks, depending on how much hands-on time is available and whether an Azure subscription is ready. Read the material first, then spend more time on the labs and capstone. The goal is understanding and evidence of working solutions, not rushing through service names.

## Skills checklist

- [ ] Explain tenants, subscriptions, resource groups, regions, and zones.
- [ ] Use Azure CLI and verify the active subscription.
- [ ] Explain Entra ID, authentication, authorisation, RBAC, and managed identity.
- [ ] Choose between App Service, Functions, Container Apps, VMs, and AKS.
- [ ] Draw a VNet, subnet, DNS, NSG, and private endpoint design.
- [ ] Select storage, relational data, NoSQL, and messaging services based on requirements.
- [ ] Retrieve secrets safely and understand .NET configuration precedence.
- [ ] Query telemetry and troubleshoot a failed request.
- [ ] Build a CI/CD pipeline with approvals and rollback.
- [ ] Provision infrastructure using Terraform and protect state.
- [ ] Describe shared responsibility and threat-model an application.
- [ ] Define and test RTO, RPO, backup, and recovery procedures.
- [ ] Estimate and review application cost.
- [ ] Design a secure RAG/agent application on Azure.
- [ ] Present architecture decisions with trade-offs and operational evidence.

## Important notes

Azure service capabilities, product names, SDKs, model availability, regional support, quotas, prices, and portal experiences change. Verify implementation details against current Microsoft documentation before deploying. Follow your organisation's identity, security, cost, and data-handling policies.

## Official reference hubs

- [Azure documentation](https://learn.microsoft.com/azure/)
- [Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
- [Azure Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)
- [Azure Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)
- [Azure CLI reference](https://learn.microsoft.com/cli/azure/)
- [Terraform on Azure](https://learn.microsoft.com/azure/developer/terraform/)
