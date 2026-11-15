# 10 — Azure Security and Defence in Depth

## Security is a system property

Cloud security is not one firewall or one identity role. It is a set of controls across identity, network, compute, application, data, monitoring, and operations.

Use defence in depth: if one control fails, other controls should reduce the chance or impact of compromise.

## 1. Shared responsibility

The cloud provider and customer share security responsibilities. The boundary changes by service model. With a VM, the customer owns much more of the guest OS and application operations than with a managed PaaS service.

For every service, identify who owns:
- Physical infrastructure.
- Host and guest OS.
- Runtime and patching.
- Application code and dependencies.
- Identity and permissions.
- Data classification, access, retention, and backup.
- Network exposure and monitoring.

Do not assume that a managed service means the application is secure by default.

## 2. Secure identity

- Use managed identities for supported Azure workloads.
- Enforce least privilege and narrow RBAC scopes.
- Separate production and non-production identities.
- Use privileged identity controls and periodic access reviews where available.
- Protect administrator accounts with strong authentication.
- Remove unused credentials and role assignments.

## 3. Protect the network

- Minimise public exposure.
- Use private connectivity where required.
- Restrict inbound and outbound paths.
- Use TLS for traffic in transit.
- Review DNS, firewall, NSG, and routing configuration.
- Monitor changes to network security rules.

## 4. Protect data and secrets

- Classify data and define retention.
- Apply encryption at rest and in transit.
- Use Key Vault and managed identity where supported.
- Restrict data-plane permissions.
- Secure backup access.
- Test deletion and recovery processes.
- Avoid secrets and personal data in logs.

## 5. Security posture and threat protection

Learn Microsoft Defender for Cloud concepts:
- Secure score and recommendations.
- Regulatory compliance dashboards.
- Workload protection plans.
- Security alerts and incident investigation.
- Continuous assessment and remediation.

A score is a prioritisation aid, not proof that a system is secure. Validate alerts and recommendations against actual architecture and business risk.

## 6. Governance controls

Use Azure Policy to audit or restrict selected configurations, for example allowed regions, required tags, and approved resource types. Use resource locks cautiously to reduce accidental deletion or modification.

Security controls should be tested in a non-production environment first. A deny policy or restrictive network rule can block legitimate deployments and break applications.

## 7. Threat modelling

For a new workload, list:
- Assets: customer data, secrets, model endpoints, infrastructure.
- Trust boundaries: public ingress, service-to-service calls, admin paths.
- Threat actors and abuse cases.
- Attack surfaces: APIs, containers, dependencies, CI/CD, identities.
- Mitigations: validation, authorisation, rate limits, isolation, monitoring.
- Residual risk and owner.

For AI applications, add prompt injection, data exfiltration, unsafe tool execution, over-permissioned retrieval, and sensitive content exposure.

## Practice exercise

Threat-model a document question-answering API. Include a malicious uploaded document, a user who should not see another department's documents, a leaked deployment credential, and an exposed model endpoint. For each threat, identify preventive, detective, and recovery controls.

## Self-check

- What does shared responsibility mean?
- Why is least privilege important for an application identity?
- Why is a secure score not a security guarantee?
- What is a trust boundary?
- Which additional threats appear when an AI agent can call tools?

## Official references

- Azure security documentation: https://learn.microsoft.com/azure/security/
- Shared responsibility: https://learn.microsoft.com/azure/security/fundamentals/shared-responsibility
- Defender for Cloud: https://learn.microsoft.com/azure/defender-for-cloud/
- Azure Well-Architected security: https://learn.microsoft.com/azure/well-architected/security/
