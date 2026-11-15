# 01 — Terraform and Infrastructure as Code Fundamentals

## What this lesson is about

Terraform is an Infrastructure as Code (IaC) tool. Instead of creating cloud resources only by clicking through a portal, I describe the desired infrastructure in configuration files. Terraform compares that configuration with its state and the real platform, then proposes changes.

The main benefit is repeatability and review. The configuration can be stored in Git, reviewed like application code, and applied in a controlled workflow.

## 1. What counts as infrastructure?

An application depends on more than source code. Its infrastructure may include:
- virtual networks and subnets,
- virtual machines or app hosting,
- storage accounts and databases,
- identities and permissions,
- DNS and load balancing,
- monitoring and alerting,
- secrets infrastructure,
- backup and recovery.

Terraform can manage resources through providers for supported platforms and services.

## 2. Why use IaC?

Without IaC, teams can end up with undocumented portal changes, inconsistent environments, forgotten settings, and difficult disaster recovery.

With IaC, teams can review desired configuration, reproduce environments, identify changes before applying, and track infrastructure changes in version control.

IaC does not automatically make infrastructure secure or correct. Poor configuration can create insecure or expensive resources very consistently.

## 3. Terraform workflow in plain English

```text
Write configuration
       |
       v
Initialise providers and modules
       |
       v
Format and validate
       |
       v
Create a plan
       |
       v
Review the proposed changes
       |
       v
Apply the approved changes
       |
       v
Verify the real environment
```

The plan is an important review point. Applying without understanding replacements, deletions, or access changes can cause outages or data loss.

## 4. Terraform and alternatives

| Tool | Main role |
|---|---|
| Terraform | Declarative infrastructure provisioning across providers |
| Azure Bicep | Declarative Azure resource deployment |
| ARM templates | Azure Resource Manager deployment format |
| Ansible | Configuration management and automation |
| Kubernetes manifests | Declare desired state of Kubernetes resources |

Tools can complement each other. Terraform does not replace application configuration management or every server-configuration tool.

## 5. Key terms

- **Provider:** Plugin that communicates with a platform or service.
- **Resource:** Object Terraform manages.
- **Data source:** Reads information about an existing object.
- **State:** Mapping between Terraform resource addresses and real objects.
- **Module:** Reusable group of Terraform configuration.
- **Backend:** Where Terraform stores its state.
- **Plan:** Proposed set of infrastructure changes.

## Practice exercise

List the infrastructure components required for a small ASP.NET Core API on Azure. Group them into networking, compute, identity, data, secrets, and monitoring. For each item, explain why the application needs it.

## Self-check

1. What is Infrastructure as Code?
2. Why does a team use Terraform instead of relying only on portal changes?
3. What is the difference between a provider and a resource?
4. Why is a plan review important?
5. Does using Terraform automatically make a deployment secure?

## Further reading

- [Terraform overview](https://developer.hashicorp.com/terraform/intro)
- [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials)
