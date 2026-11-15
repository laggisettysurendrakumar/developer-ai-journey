# 08 — Remote Backends, Locking, and Team Workflows

## Why teams need remote state

When several developers or pipelines manage the same infrastructure, a state file on one person's laptop is not a reliable shared source. A remote backend stores state in a central location and supports a coordinated workflow.

## 1. Backend responsibilities

A backend determines where Terraform stores state and how operations interact with it. Backend configuration is separate from an ordinary provider resource configuration.

Important backend requirements:
- authentication,
- encryption,
- access control,
- state consistency,
- locking support where available,
- recovery/versioning,
- availability,
- clear ownership.

## 2. Azure Storage backend

For an Azure team, the `azurerm` backend commonly stores state in an Azure Storage blob. Verify current backend configuration and authentication guidance for the Terraform version in use.

Backend identity should use an approved workload identity or managed identity pattern where supported. Avoid embedding storage account keys, client secrets, or SAS tokens in committed configuration.

Conceptual backend block:

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "sttfstateexample"
    container_name       = "tfstate"
    key                  = "knowledge-assistant/dev.tfstate"
  }
}
```

This is an illustrative configuration. The backend resources must already exist, and authentication must be configured securely according to the current AzureRM backend documentation.

## 3. Locking and concurrent changes

A state lock prevents concurrent operations from writing the same state at the same time. It does not prevent someone from changing the cloud resource manually. That is a drift problem.

Never force-unlock a state unless you have confirmed that the lock is stale and no Terraform operation is still active. Force-unlock used incorrectly can allow concurrent writes and damage state.

## 4. Team workflow

A safe team process:
1. Developer creates a branch and changes configuration.
2. CI runs formatting, validation, security checks, and a plan.
3. Reviewers inspect the exact plan.
4. An authorised approver approves the environment.
5. The deployment pipeline applies the reviewed change.
6. The team verifies the real resources and records the deployment.

The exact plan applied should be the one reviewed, not a newly generated plan with unknown changes.

## 5. Separate state boundaries

Separate state by environment and ownership boundary where that reduces blast radius. Avoid one enormous state for unrelated workloads. Also avoid excessive fragmentation that makes dependencies and operations impossible to understand.

## Practice exercise

Design a remote state arrangement for dev, test, and prod. Specify storage account, container/key naming, identity, permissions, locking, recovery, and who can approve a production apply. Do not create cloud resources without an approved lab subscription and budget.

## Self-check

1. Why use a remote backend for team Terraform?
2. What does locking protect against?
3. Does locking prevent portal changes?
4. Why is force-unlock dangerous?
5. Why should the applied plan match the reviewed plan?

## Further reading

- [Terraform backends](https://developer.hashicorp.com/terraform/language/backend)
- [AzureRM backend](https://developer.hashicorp.com/terraform/language/backend/azurerm)
