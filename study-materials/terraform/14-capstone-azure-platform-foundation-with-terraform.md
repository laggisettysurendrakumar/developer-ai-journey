# 14 — Capstone: Azure Platform Foundation with Terraform

## Capstone goal

Design and implement a small, secure, non-production Azure foundation for a Developer Knowledge Assistant. The project demonstrates Terraform workflow, reusable modules, remote state, identity, networking, monitoring, CI/CD, and safe operations.

This is a learning project. Use only an approved sandbox subscription with a budget. Do not deploy into a company or production subscription without explicit approval.

## 1. Target architecture

The platform may include:
- resource group and naming/tag standards,
- virtual network and application subnet,
- Azure App Service or another approved hosting resource,
- managed identity,
- storage for approved sample documents,
- Key Vault for application secrets,
- monitoring and diagnostic settings,
- remote Terraform state in a separately protected storage account.

The first pass can use only a subset of resources. Do not deploy every component simply to complete a checklist.

## 2. Suggested project layout

```text
terraform-azure-platform/
├── environments/
│   ├── dev/
│   └── prod/
├── modules/
│   ├── naming/
│   ├── network/
│   ├── app-hosting/
│   ├── identity/
│   ├── storage/
│   └── monitoring/
├── docs/
│   ├── architecture.md
│   ├── security.md
│   └── operations.md
└── README.md
```

This is one possible layout. Keep state boundaries and ownership clear; the directory structure alone does not create isolation.

## 3. Implementation milestones

**Milestone A — Design**
- Write requirements and non-goals.
- Draw trust boundaries and network paths.
- Define environment naming and tags.
- Estimate costs and set a budget alert.
- Identify required Azure roles.

**Milestone B — Terraform foundation**
- Pin Terraform and provider versions.
- Configure the AzureRM provider.
- Create resource-group and naming modules.
- Validate inputs and outputs.
- Run format, validate, and plan.

**Milestone C — Shared state and identity**
- Create or use an approved remote backend.
- Restrict state access.
- Verify locking and recovery.
- Configure approved workload identity for CI.
- Separate dev and prod state and permissions.

**Milestone D — Platform resources**
- Add only required networking and app hosting.
- Add managed identity and scoped role assignments.
- Configure storage and secret references.
- Enable diagnostic settings and monitoring.
- Review public exposure and private connectivity.

**Milestone E — CI/CD and operations**
- Add pull-request checks and plan review.
- Gate production apply behind approval.
- Add drift review and state recovery runbooks.
- Test resource deletion/replacement scenarios in an isolated lab.
- Document cost ownership and cleanup.

## 4. Acceptance criteria

The capstone is complete when:
1. Terraform and provider versions are pinned.
2. Configuration is formatted and validates.
3. Inputs have types and sensible validation.
4. Modules have documented inputs and outputs.
5. State is remote and protected for team environments.
6. CI uses a least-privilege identity.
7. Plans are reviewed before apply.
8. Public network exposure and role assignments are explicitly reviewed.
9. Monitoring and cost ownership are documented.
10. Drift, backup, recovery, and rollback procedures exist.

## 5. Production design review questions

- Who owns each state file and infrastructure module?
- Which identity can read state and which can apply changes?
- How are dev and production isolated?
- What happens if an apply fails halfway through?
- How are provider and module upgrades tested?
- How do we detect and resolve drift?
- How can a resource be recovered after accidental deletion?
- Which plan changes require explicit security approval?
- How will costs be attributed and monitored?

## 6. .NET and Azure integration challenge

Map the infrastructure to an ASP.NET Core 8 API:
- hosting and deployment identity,
- managed identity access to approved storage/Key Vault,
- network access to dependencies,
- App Insights or equivalent monitoring,
- environment-specific app settings,
- secrets retrieved through a managed secret mechanism.

Keep application deployment separate from infrastructure provisioning unless there is a deliberate pipeline design tying them together.

## Practice exercise

Build a dev-only version of the platform in a Git branch. Submit the plan and architecture for review before applying. Provide a demo showing the created resources, identity permissions, logs/metrics, cost tags, and a clean teardown of only the isolated lab resources.

## Self-check

1. What are the main modules in the capstone?
2. Why should state storage have tighter access than ordinary source files?
3. How is CI identity protected?
4. What should happen before a production apply?
5. What is required before calling an infrastructure design production-ready?

## Definition of done

The capstone is a learning implementation, not a production certification. Production use requires architecture and security review, subscription governance approval, tested recovery, operational ownership, and a reviewed cost model.
