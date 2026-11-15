# 14 — Architecture, Well-Architected Design and Capstone

## Move from service knowledge to system design

Knowing how to create an App Service or database is only one part of Azure engineering. An experienced engineer must explain why a service was selected, how it is protected, what happens when it fails, how it is monitored, and what it costs.

## 1. Azure Well-Architected Framework

Use the Well-Architected Framework to review five pillars:
- Reliability: recover and continue operating through failures.
- Security: protect identities, systems, and data.
- Cost optimisation: meet requirements without unnecessary spend.
- Operational excellence: deploy, observe, and improve safely.
- Performance efficiency: use resources efficiently as demand changes.

A design often involves trade-offs. For example, higher redundancy may improve availability but increase cost and operational complexity. Document the decision and the reason.

## 2. Requirements before services

Write down:
- Users and workload shape.
- Functional and non-functional requirements.
- Data classification and access model.
- Availability target, RTO and RPO.
- Latency and throughput targets.
- Compliance and region constraints.
- Operational ownership and support model.
- Budget and expected growth.

A service choice without requirements is a guess.

## 3. Reference architecture: developer knowledge assistant

A possible architecture:

```text
Developer / Browser
       |
       v
Web frontend
       |
       v
API hosting (App Service or Container Apps)
       |------ Microsoft Entra authentication
       |------ Managed identity
       |          |------ Key Vault
       |          |------ Blob Storage
       |          |------ Search service
       |          |------ Approved model endpoint
       |
       |------ Queue ----> Background ingestion worker
       |                       |
       |                       +--> Extract / chunk / embed / index
       |
       +------ Application Insights / Azure Monitor

Terraform + CI/CD manage reviewed infrastructure and releases.
```

This is a starting point, not a universal architecture. Choose exact services, network paths, and availability options from requirements and verified service capabilities.

## 4. Architecture decision records (ADRs)

For a significant decision, write:
- Context: what problem needs solving?
- Options: what alternatives were considered?
- Decision: what was selected?
- Consequences: benefits, costs, limitations, risks.
- Revisit trigger: what future condition would make the decision worth reviewing?

Example decision: use Container Apps instead of AKS for an API and worker because the team does not need direct Kubernetes control. Revisit if platform requirements require Kubernetes APIs, custom operators, or cluster-level capabilities.

## 5. Production readiness checklist

Before production, verify:
- Identity and least-privilege role assignments.
- Public/private network exposure and TLS.
- Secrets and configuration handling.
- Data access filtering and retention.
- Scaling and quota limits.
- Health checks and dependency timeouts.
- Retries, idempotency, and dead-letter procedures.
- Backups and tested restore.
- Monitoring, alerts, and runbooks.
- Cost estimates, budgets, and ownership tags.
- CI/CD controls, approvals, and rollback.
- Security review and threat model.
- Load, failure, and recovery tests.

## 6. Capstone project

Build and deploy a small Developer Knowledge Assistant with:
- ASP.NET Core API.
- A simple frontend or API client.
- Azure hosting selected with documented reasoning.
- Microsoft Entra authentication.
- Managed identity for service-to-service access.
- Key Vault for any unavoidable secrets.
- Blob Storage for approved documents.
- Azure AI Search or an approved retrieval service.
- An approved model endpoint.
- Background ingestion using a queue and worker.
- Application Insights / Azure Monitor.
- Terraform for infrastructure.
- GitHub Actions or Azure Pipelines for CI/CD.

### Suggested implementation milestones

1. Run the API locally and write architecture requirements.
2. Add authentication and a simple authorisation model.
3. Create a document ingestion pipeline with tests.
4. Add retrieval with source metadata and access filters.
5. Connect the model through a server-side service.
6. Add tool calls only where useful and permission-checked.
7. Containerise or package the app.
8. Provision non-production infrastructure with Terraform.
9. Deploy through CI/CD using a secure identity.
10. Add monitoring, cost limits, and alerting.
11. Run security, load, and failure tests.
12. Document rollback and recovery.

### Definition of done

- A user can ask a question and receive an answer with source references.
- A user cannot retrieve another user's or team's restricted content.
- Secrets are not stored in Git or container images.
- The app uses identity-based access where supported.
- Errors and latency can be diagnosed using telemetry.
- Model, retrieval, and tool usage have explicit limits.
- Infrastructure is reproducible from reviewed code.
- The deployment process is documented and repeatable.
- Cost assumptions and recovery procedures are documented.
- A reviewer can understand key architecture decisions.

## Practice exercise

Create a one-page architecture proposal for the capstone. Include a component diagram, data flow, identity and permission matrix, network boundaries, RTO/RPO assumptions, estimated cost drivers, deployment workflow, and three important trade-offs. Ask a peer to review it before provisioning resources.

## Self-check

- What are the five Well-Architected pillars?
- Why should a design begin with requirements rather than Azure product names?
- What belongs in an ADR?
- Which tests prove that a backup or deployment rollback works?
- What evidence would you provide to show that the capstone is production-ready?

## Official references

- Azure Well-Architected Framework: https://learn.microsoft.com/azure/well-architected/
- Azure Architecture Center: https://learn.microsoft.com/azure/architecture/
- Cloud Adoption Framework: https://learn.microsoft.com/azure/cloud-adoption-framework/
- Azure landing zones: https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
