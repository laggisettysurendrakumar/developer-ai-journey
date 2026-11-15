# 01 — Cloud and Azure Foundations

## What this lesson is about

Before creating an Azure resource, understand what problem cloud platforms solve and how Azure organises what you create. These concepts appear in almost every design, deployment, billing, and troubleshooting conversation.

A traditional application might run on a server purchased by a company. Someone must maintain the hardware, operating system, storage, network, backups, and capacity. With cloud computing, a provider operates the underlying data-centre infrastructure and exposes services that teams can provision through a portal, command line, API, or infrastructure code.

Cloud does not mean that operations disappear. The work changes: instead of maintaining every physical server, teams make decisions about identity, configuration, network boundaries, availability, cost, monitoring, and application behaviour.

## 1. Cloud service models

| Model | What the provider manages | What the team still manages | Example |
|---|---|---|---|
| IaaS | Physical infrastructure and virtualisation | OS, patches, runtime, application, data | Azure Virtual Machines |
| PaaS | Infrastructure and much of the OS/runtime platform | Application, data, configuration, access | Azure App Service, Azure SQL |
| SaaS | Most of the application platform | Users, data, configuration and usage | Microsoft 365 |

The practical question is not which model is most advanced. It is which model reduces operational work while meeting the application's requirements.

## 2. Public, private and hybrid cloud

- **Public cloud:** services run on a provider's shared cloud infrastructure, with logical isolation between customers.
- **Private cloud:** cloud-like infrastructure dedicated to one organisation.
- **Hybrid:** systems in a private environment and a public cloud are connected.
- **Multicloud:** an organisation uses more than one cloud provider.

A hybrid design may be needed for legacy systems, data residency, latency, or existing investments. It also adds identity, routing, monitoring, and operational complexity.

## 3. Azure hierarchy

Understand these terms before deploying:

| Term | Meaning | Why it matters |
|---|---|---|
| Microsoft Entra tenant | Identity directory boundary | Users, groups, applications and identities |
| Management group | Groups subscriptions for governance | Apply policy and role assignments across subscriptions |
| Subscription | Resource and billing management boundary | Quotas, billing, access and isolation |
| Resource group | Logical lifecycle container for resources | Organise, deploy and remove related resources |
| Resource | An individual Azure service instance | The thing being configured and operated |
| Region | Geographic area containing Azure datacentres | Availability, latency, data location and service support |
| Availability zone | Physically separate location within a supported region | Resilience against a zone-level failure |

Do not assume every service or feature is available in every region or zone. Check the current service availability and your subscription's quota before selecting an architecture.

## 4. Naming, tags and environment separation

Agree on a naming convention before creating resources. Names should help identify the workload, environment, region, and resource type without exposing confidential data.

Useful tags include:
- `environment`: dev, test, staging, production
- `application`: application or product identifier
- `owner`: responsible team
- `costCenter`: approved cost allocation
- `dataClassification`: approved classification label

Use separate subscriptions or resource groups for isolation where required by your organisation's governance model. Tags help reporting, but tags are not a security boundary.

## 5. First Azure CLI session

Azure CLI is a command-line tool for managing Azure. It is useful for repeatable tasks, scripts, automation, and learning how resource operations are expressed.

Install Azure CLI using the official instructions for your operating system. Then:

```bash
az version
az login
az account show
az account list --output table
```

What each command does:
- `az version` confirms that the CLI is installed and shows its version.
- `az login` signs in and obtains an authenticated session.
- `az account show` displays the subscription currently selected.
- `az account list` lists subscriptions visible to the signed-in identity.

Select a subscription only when you are authorised to use it:

```bash
az account set --subscription "<subscription-id-or-name>"
az account show --output table
```

The second command is a verification step. Never assume the intended subscription is selected just because login succeeded.

## 6. First safe resource-group exercise

A resource group is a logical container for Azure resources. For a lab, create one only in a subscription where you have permission and where spending is allowed.

```bash
az group create \
  --name rg-azure-learning-dev \
  --location centralindia \
  --tags environment=dev application=azure-learning
```

Inspect it:

```bash
az group show --name rg-azure-learning-dev --output table
az group list --output table
```

Clean up only the resource group you created for this lab, after checking its contents:

```bash
az group delete --name rg-azure-learning-dev --yes --no-wait
```

Deletion is destructive. Do not run it against a shared, production, or unfamiliar resource group. The `--no-wait` option returns before deletion has completed.

## Practice exercise

1. Draw the hierarchy from tenant to resource.
2. Explain why a resource group is not the same as a subscription.
3. Log in to a permitted learning subscription and record the selected subscription name.
4. Create a lab resource group with tags, inspect it, and remove it only after confirming it contains no resources you need.
5. Find two Azure regions and verify whether your intended services are available in them.

## Self-check

- What is the difference between IaaS and PaaS?
- Where do users and managed identities live?
- What is the purpose of a management group?
- Why should you verify the active subscription before deploying?
- Why are tags useful but not a security boundary?

## Official references

- Azure fundamentals: https://learn.microsoft.com/azure/fundamentals/
- Azure regions: https://learn.microsoft.com/azure/reliability/regions-overview
- Azure CLI: https://learn.microsoft.com/cli/azure/
- Azure resource naming: https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules
