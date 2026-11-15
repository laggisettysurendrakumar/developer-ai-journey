# 06 — Terraform State, Resource Addresses, and Lifecycle

## What state is

Terraform state is the record that maps the resources in a configuration to real objects managed by the provider. It stores resource addresses, identifiers, and attributes Terraform needs to plan future changes.

State is not just a disposable cache. Losing, corrupting, or exposing state can cause management and security problems.

## 1. Resource addresses

A resource address identifies a managed object in Terraform.

Examples:
- `azurerm_resource_group.main`
- `module.network.azurerm_virtual_network.main`
- `azurerm_subnet.app["private"]`

Addresses matter when moving resources between modules, using `for_each`, importing existing infrastructure, or interpreting a plan.

## 2. Local state and remote state

A local learning folder typically uses a local state file. This is acceptable for a disposable exercise.

Team environments should use an approved remote backend with access control, encryption, locking, versioning or recovery support as appropriate. Do not share a local state file through email or Git.

## 3. State security

State may contain sensitive resource attributes, including values that should not be exposed in source control or logs.

- Do not commit state or state backups.
- Limit read access to the backend.
- Use encryption at rest and TLS in transit.
- Enable backend recovery/versioning features where supported.
- Avoid printing sensitive state to build logs.
- Treat state access as privileged infrastructure access.

Marking an output or variable sensitive does not mean its underlying value is absent from state.

## 4. State locking

A lock helps prevent multiple Terraform operations from modifying the same state simultaneously. Use a backend with supported locking semantics for the selected Terraform version. Do not disable locking just to make a pipeline pass.

## 5. Lifecycle meta-arguments

The `lifecycle` block can influence resource handling. Examples include:
- `create_before_destroy`,
- `prevent_destroy`,
- `ignore_changes`.

These options change how Terraform handles updates and replacements. They do not replace backups or change approval. Be particularly careful with `ignore_changes`: it may hide meaningful drift.

## 6. Moving resource addresses

When refactoring configuration, a resource address can change even though the real infrastructure should remain the same. Use documented `moved` blocks or state-move workflows to preserve the association. Review the plan to ensure Terraform is not proposing an unnecessary destroy/create operation.

Do not edit state JSON by hand as a normal refactoring method.

## Practice exercise

In the local file lab, inspect the state list and resource address. Change the resource's local name and observe the plan. Explain why a rename can look like a delete/create. Review the use of a `moved` block in the official docs without using it on a shared state.

## Self-check

1. What relationship does Terraform state record?
2. Why should state be protected like sensitive data?
3. What problem does state locking solve?
4. What does `prevent_destroy` do, and what does it not guarantee?
5. How can a resource rename lead to an unexpected plan?

## Further reading

- [Terraform state](https://developer.hashicorp.com/terraform/language/state)
- [Terraform state locking](https://developer.hashicorp.com/terraform/language/state/locking)
- [Terraform moved blocks](https://developer.hashicorp.com/terraform/language/modules/develop/refactoring)
