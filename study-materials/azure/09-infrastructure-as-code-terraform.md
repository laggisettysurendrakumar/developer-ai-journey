# 09 — Infrastructure as Code with Terraform

## Why use Infrastructure as Code?

Creating resources manually in the portal is useful for exploration. It is difficult to repeat consistently across development, test, and production.

Infrastructure as Code (IaC) describes infrastructure in files that can be reviewed, versioned, tested, and applied repeatedly. Terraform is one tool for this purpose.

## 1. Terraform concepts

- **Provider:** plugin that talks to an API such as Azure Resource Manager.
- **Resource:** infrastructure object Terraform manages.
- **Data source:** reads information about an existing object.
- **Variable:** input to a configuration.
- **Output:** value exposed after Terraform evaluates/applies configuration.
- **State:** mapping between Terraform configuration and real resources.
- **Plan:** proposed changes before applying.
- **Module:** reusable group of Terraform configuration.

Terraform is declarative: describe the desired state and Terraform calculates changes to move towards it.

## 2. Basic Azure example

The following is a small learning example. Use a permitted subscription and validate the provider version and resource requirements before applying.

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}

provider "azurerm" {
  features {}
  subscription_id = var.subscription_id
}

variable "subscription_id" {
  type        = string
  description = "Subscription ID for the authorised lab environment."
}

variable "location" {
  type    = string
  default = "Central India"
}

resource "azurerm_resource_group" "lab" {
  name     = "rg-terraform-learning-dev"
  location = var.location

  tags = {
    environment = "dev"
    managed_by  = "terraform"
  }
}

output "resource_group_name" {
  value = azurerm_resource_group.lab.name
}
```

The resource block declares one resource group. The provider connects Terraform to Azure. The variable avoids hardcoding a subscription identifier into the shared configuration. The output makes the created name easy to retrieve.

## 3. Terraform workflow

```bash
terraform fmt -check
terraform init
terraform validate
terraform plan -out=tfplan
terraform apply tfplan
```

What each command does:
- `fmt -check` checks formatting without changing the file.
- `init` downloads providers and initialises the working directory.
- `validate` checks configuration structure and types.
- `plan` calculates proposed changes and writes a saved plan.
- `apply` executes the saved plan.

Inspect the plan before applying. A plan can create, update, or destroy resources. A saved plan can contain sensitive values; protect it like other sensitive deployment artifacts.

After the lab, inspect resources and use a reviewed destroy plan only for resources created by this exercise.

## 4. State and remote backend

Terraform state can contain sensitive resource attributes. Do not commit state files or plan files into Git.

For team environments, use an approved remote backend with access control, encryption, and state locking capabilities. Azure Storage is a common backend option. Configure backend authentication securely; do not store storage account keys in source files.

State is important operational data. Protect it, back it up according to policy, and avoid manual edits unless following a reviewed recovery procedure.

## 5. Modules and environments

Create modules for reusable building blocks such as:
- Resource group and tags.
- Network foundation.
- Storage and private endpoint.
- Application hosting.
- Monitoring.

Keep environment inputs separate and review differences explicitly. Avoid one giant module with dozens of unrelated switches.

## 6. Terraform in CI/CD

A secure workflow commonly:
1. Runs formatting, validation, linting, and security checks.
2. Produces a plan for review.
3. Publishes the plan securely.
4. Requires approval for sensitive or production changes.
5. Applies the reviewed plan with a narrowly scoped identity.
6. Records the deployment and state changes.

Do not automatically apply every pull-request plan to production. Make destructive changes visible to reviewers.

## Practice exercise

Use Terraform to create a resource group and a storage account in a disposable subscription. Store state using the approved backend for your environment. Review the plan, apply it, inspect the resources in Azure, then generate and review a destroy plan. Never run a destroy against shared or production infrastructure.

## Self-check

- What does Terraform state represent?
- Why is a plan reviewed before apply?
- Why should state not be committed to Git?
- What is the difference between a resource and a data source?
- Why should production applies require stronger controls?

## Official references

- Terraform AzureRM provider: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs
- Terraform Azure guides: https://developer.hashicorp.com/terraform/tutorials/azure-get-started
- Terraform state: https://developer.hashicorp.com/terraform/language/state
- Azure Storage backend: https://developer.hashicorp.com/terraform/language/backend/azurerm
