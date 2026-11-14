# Terraform for Reproducible AI Infrastructure — Beginner Course

I use Terraform to describe infrastructure in code. Instead of clicking through a cloud portal and trying to remember every setting, I can review a configuration, preview a plan, and apply a controlled change.

## Prerequisites and learning order

Understand basic cloud concepts, Linux/CLI, identity, networking, and Docker. Start with a local lab before creating chargeable cloud resources.

1. Understand Infrastructure as Code (IaC) and Terraform workflow.
2. Install Terraform and learn providers, resources, and data sources.
3. Use variables, locals, and outputs.
4. Understand state and resource addresses.
5. Use modules and environment-specific values.
6. Learn remote backend, locking, and state protection.
7. Read plans, understand drift, and import resources.
8. Add formatting, validation, policy checks, and CI review.
9. Design a small non-production AI application environment.

## 1. Infrastructure as Code in plain English

Infrastructure includes cloud resources an application depends on: networks, app hosting, storage, identities, databases, monitoring, and more.

Infrastructure as Code means describing those resources in files. Terraform compares declared configuration with its state and the real infrastructure, then proposes changes.

## 2. Core Terraform terms

| Term | Plain-English meaning |
|---|---|
| Provider | Plugin that talks to a platform such as Azure or AWS |
| Resource | Infrastructure object Terraform manages |
| Data source | Reads information about an existing object |
| Variable | Input value that makes configuration reusable |
| Local | Named expression used inside configuration |
| Output | Value Terraform displays or makes available after operations |
| State | Records Terraform's mapping between configuration and real objects |
| Module | Reusable package of Terraform configuration |
| Backend | Where Terraform state is stored |

## 3. Safe local lab — create a local file

This example uses the local provider. It does not provision cloud infrastructure and is a safe way to learn the Terraform lifecycle in a disposable folder.

Create main.tf:

```hcl
terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}

provider "local" {}

variable "note_content" {
  type        = string
  description = "Text to put in the sample file."
  default     = "Created by a Terraform learning lab."
}

resource "local_file" "course_note" {
  filename = "generated-note.txt"
  content  = var.note_content
}

output "created_path" {
  value = local_file.course_note.filename
}
```

**What it does:** Declares a local provider, an input variable, a file resource, and an output.

**Why useful?** The same concepts apply to cloud resources, but this lab avoids creating billable infrastructure.

Run from the folder containing main.tf:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
```

Review the plan carefully before applying:

```bash
terraform apply
```

Terraform asks for confirmation unless an approval option is supplied. For learning, read the proposed change before confirming.

## 4. Variables and environment separation

Variables make values configurable rather than hardcoding them repeatedly.

```hcl
variable "environment" {
  type        = string
  description = "Deployment environment"
  default     = "dev"
}

locals {
  app_name = format("knowledge-assistant-%s", var.environment)
}

output "app_name" {
  value = local.app_name
}
```

A deployment pipeline can supply approved values for dev, test, and prod. Avoid copying a production configuration into a developer laptop.

## 5. Understand Terraform state

State maps Terraform resource addresses to real objects. It helps Terraform understand what it already manages.

Important rules:
- State may contain sensitive values.
- Do not commit state files or backups.
- Protect state with encryption, access control, and locking.
- Use a remote backend for team environments.
- Back up state and test recovery.
- Do not edit state JSON manually as a normal repair technique.

State is not simply a cache; careless state changes can lead Terraform to propose destructive replacements or lose track of resources.

## 6. Understand plan, apply, and destroy

- terraform plan previews what Terraform intends to do.
- terraform apply executes planned changes after approval.
- terraform destroy proposes removing managed resources.

Read the plan before every apply. A plan can replace a resource and cause downtime or data loss even when the configuration change looks small. Never destroy shared or production resources without an approved change and recovery plan.

## 7. Modules

A module groups resources and inputs into a reusable unit. For example, a reviewed networking module might be reused by several app environments. Keep modules small and document their inputs, outputs, assumptions, and ownership.

## 8. Remote state and team workflow

For a team, use an approved backend such as Azure Storage remote state. Configure authentication through an approved identity, not a hardcoded account key. Ensure locking and consistency are supported by the chosen backend and Terraform version.

A safe workflow:
1. Format and validate configuration.
2. Run security and policy checks.
3. Generate a plan in CI.
4. Publish the plan for review.
5. Approve the exact environment and change.
6. Apply using a least-privilege deployment identity.
7. Record the result and verify the application.

## 9. Drift and import

Drift means the real infrastructure no longer matches the declared configuration/state. It may happen because someone changed a setting outside Terraform or an external system changed it.

Do not blindly apply a plan when drift appears. Understand why it happened, decide which source should be authoritative, and reconcile configuration and state through the documented Terraform workflow. Import brings an existing resource under Terraform management; it does not automatically create a complete configuration for that resource.

## 10. Practice project — AI application environment

Design a non-production Terraform environment for a Developer Knowledge Assistant:
1. Resource group and naming conventions.
2. Application hosting resource.
3. Managed identity.
4. Document storage.
5. Monitoring.
6. Network boundaries.
7. Secret manager integration.
8. Tags, outputs, and cost ownership.
9. Remote state design.
10. CI plan/review/apply workflow.

Start with terraform plan and a design review. Provision cloud resources only in an approved subscription with a budget.

## Self-check

1. What is the difference between a provider and a resource?
2. Why does Terraform need state?
3. What should I review in a plan?
4. Why should state not be committed to Git?
5. Why is remote state access a security boundary?

## References

- Terraform tutorials: https://developer.hashicorp.com/terraform/tutorials
- Terraform language: https://developer.hashicorp.com/terraform/language
- Terraform state: https://developer.hashicorp.com/terraform/language/state
- AzureRM provider: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs

Terraform and provider versions evolve. Pin reviewed versions and verify resource arguments against the provider documentation for your version.
