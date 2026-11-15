# 10 — Azure Infrastructure with Terraform Foundations

## Applying Terraform to Azure

This lesson connects Terraform concepts to Azure resources. Start by understanding the resource types and identity model. Use a sandbox subscription, set a budget, and do not deploy billable resources to a personal or company subscription without approval.

## 1. Azure provider

The AzureRM provider manages many Azure Resource Manager resources. A versioned provider requirement belongs in the Terraform configuration. Check the provider's current compatibility notes before selecting a version.

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

This is an illustrative provider setup. Version constraints should be selected and tested for your environment; the example is not a claim that a specific version is currently latest.

## 2. Azure resource hierarchy

A simple mental model:

```text
Microsoft Entra ID tenant
        |
Subscription
        |
Resource Group
        |
Azure resources
```

A resource group is a management container for resources. A subscription is a billing and governance boundary. A tenant is an identity boundary. Understand which subscription and tenant are selected before any apply.

## 3. Example resource group

```hcl
variable "resource_group_name" {
  type = string
}

variable "location" {
  type = string
}

resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location
}
```

This is a small example. It creates an Azure resource group when applied with valid provider authentication and an approved subscription. It may incur costs indirectly when other billable resources are added.

## 4. Authentication

Use an approved authentication mechanism such as Azure CLI authentication for a developer lab or workload identity/managed identity for automation when supported by the provider and environment.

Do not hardcode client secrets, tenant secrets, or subscription credentials in source files. Restrict the identity to the required scope and roles.

## 5. Plan before provisioning

Before running an apply:
- confirm the active tenant and subscription,
- check the target resource group and location,
- review every create, update, replace, and delete,
- verify tags and network exposure,
- confirm budget and ownership,
- ensure there is a recovery plan.

## Practice exercise

In an approved sandbox, configure the AzureRM provider and plan creation of one resource group with naming and tags. Review the plan and identify the subscription context. Apply only after confirming that the subscription is intended for the exercise. Destroy only the isolated lab resource group after verifying its contents and following the lab cleanup process.

## Self-check

1. What is the difference between a tenant and a subscription?
2. What does a resource group do?
3. Why should provider versions be constrained?
4. What is a safer authentication pattern for CI/CD than a hardcoded secret?
5. Which checks should happen before an Azure apply?

## Further reading

- [AzureRM provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure Terraform documentation](https://learn.microsoft.com/azure/developer/terraform/)
