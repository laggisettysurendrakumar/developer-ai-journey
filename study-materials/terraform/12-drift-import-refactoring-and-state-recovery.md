# 12 — Drift, Import, Refactoring, and State Recovery

## What is drift?

Drift occurs when real infrastructure differs from the configuration and state Terraform expects. It can happen when someone changes a resource in the portal, an external controller modifies it, or a provider changes how an attribute is represented.

## 1. Detect and understand drift

A plan refreshes information from the provider and compares it with the declared configuration. A detected change is evidence to investigate, not an instruction to apply immediately.

Ask:
- Who changed the resource?
- Was the change intentional?
- Is the configuration outdated?
- Is an external service expected to manage this setting?
- Could the proposed correction cause downtime or data loss?

## 2. Reconcile safely

Decide which system is authoritative. If Terraform should own the setting, update configuration and review the plan. If another controller legitimately owns it, define that ownership boundary explicitly and consider whether an ignore rule is appropriate.

Do not use `ignore_changes` simply to silence an unexplained difference.

## 3. Import existing infrastructure

Import associates an existing cloud resource with a Terraform resource address. It does not automatically write a complete configuration for the resource. After importing, inspect the state and write matching configuration before applying anything.

An incorrect import address or resource ID can create confusion or cause Terraform to propose unexpected changes.

## 4. Refactor resource addresses

Use documented `moved` blocks or state-move operations when moving resources between names or modules. Review the resulting plan to confirm Terraform understands the move rather than proposing replacement.

## 5. State recovery

Before recovery:
1. Stop concurrent Terraform operations.
2. Identify the correct backend and state key.
3. Check the state lock and active operations.
4. Locate a trusted backup/version.
5. Confirm which infrastructure resources currently exist.
6. Follow the backend's documented recovery process.
7. Run a plan and review all proposed changes.

Never casually overwrite a live state file or force-unlock a state while another operation may still be running.

## 6. Common dangerous commands

State commands can alter Terraform's management records without changing real resources. Commands such as `terraform state rm`, `terraform state mv`, and force-unlock should be treated as privileged operations. Understand their exact effect and use a backup and review process.

## Practice exercise

Create a local lab resource, then change its content outside Terraform. Run a plan and describe the drift. Next, review the official import and moved-block workflow for a sample resource. Do not experiment on shared or production state.

## Self-check

1. What creates Terraform drift?
2. Why should drift be investigated before apply?
3. Does import generate complete Terraform configuration automatically?
4. What is a moved block useful for?
5. What precautions should happen before state recovery?

## Further reading

- [Terraform import](https://developer.hashicorp.com/terraform/language/import)
- [Terraform refactoring](https://developer.hashicorp.com/terraform/language/modules/develop/refactoring)
- [Terraform state](https://developer.hashicorp.com/terraform/language/state)
