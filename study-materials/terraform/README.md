# Terraform — Beginner to Expert Infrastructure as Code Learning Path

I use this course to learn Terraform from first principles and apply it to real cloud infrastructure, especially Azure. The goal is to understand not only HCL syntax, but also safe state management, reusable modules, identity, networking, CI/CD, drift, and production operations.

## Who this course is for

Developers and cloud engineers who know basic command-line usage and want to move from manual portal-based provisioning to repeatable, reviewed infrastructure changes. Basic cloud and networking knowledge helps, but the course starts with plain-English foundations.

## What I should be able to do by the end

- Explain Infrastructure as Code and the Terraform workflow.
- Install Terraform, use its CLI, and organise a project.
- Write HCL using providers, resources, data sources, variables, locals, and outputs.
- Use expressions, loops, and lifecycle behaviour safely.
- Understand resource addresses and state.
- Build reusable modules with clear interfaces.
- Configure remote state, locking, and team workflows.
- Design dev/test/prod state and environment boundaries.
- Provision basic Azure resources with approved identity.
- Apply networking, identity, and security principles.
- Detect drift, import existing resources, refactor addresses, and recover state.
- Build a controlled CI/CD pipeline with plan review and production approval.
- Complete an Azure platform foundation capstone.

## Learning path

| Lesson | Topic | Outcome |
|---:|---|---|
| 01 | [Terraform and Infrastructure as Code fundamentals](01-terraform-and-infrastructure-as-code-fundamentals.md) | Understand IaC and the Terraform workflow |
| 02 | [Installation, CLI workflow, and project layout](02-installation-cli-workflow-and-project-layout.md) | Set up a safe local Terraform lab |
| 03 | [HCL syntax, providers, resources, and data sources](03-hcl-syntax-providers-resources-and-data-sources.md) | Write and understand basic configuration |
| 04 | [Variables, locals, outputs, and type constraints](04-variables-locals-outputs-and-type-constraints.md) | Make configuration reusable and validated |
| 05 | [Expressions, functions, loops, and dynamic blocks](05-expressions-functions-loops-and-dynamic-blocks.md) | Build data-driven configuration |
| 06 | [Terraform state, resource addresses, and lifecycle](06-terraform-state-resource-addresses-and-lifecycle.md) | Understand how Terraform tracks infrastructure |
| 07 | [Modules, reuse, and module composition](07-modules-reuse-and-module-composition.md) | Create reusable infrastructure components |
| 08 | [Remote backends, locking, and team workflows](08-remote-backends-locking-and-team-workflows.md) | Collaborate safely with shared state |
| 09 | [Workspaces and environment strategy](09-terraform-workspaces-and-environment-strategy.md) | Separate environment configuration and state |
| 10 | [Azure infrastructure with Terraform foundations](10-azure-infrastructure-with-terraform-foundations.md) | Configure the AzureRM provider and basic resources |
| 11 | [Azure networking, identity, and security](11-azure-networking-identity-and-security-with-terraform.md) | Design secure cloud foundations |
| 12 | [Drift, import, refactoring, and state recovery](12-drift-import-refactoring-and-state-recovery.md) | Maintain existing infrastructure safely |
| 13 | [Terraform testing, policy, and CI/CD workflows](13-terraform-testing-policy-and-cicd-workflows.md) | Automate validation, review, and controlled apply |
| 14 | [Capstone: Azure platform foundation with Terraform](14-capstone-azure-platform-foundation-with-terraform.md) | Design and implement a complete learning environment |

## Suggested learning pace

A first pass can take 4–6 weeks at 1–2 focused hours per day. Spend extra time on state, modules, Azure identity, and plan review. Terraform is an operational tool: understanding the consequences of a plan is more important than memorising every command.

## Prerequisites and environment

- Basic command-line and Git knowledge.
- Basic cloud concepts: subscription, resource group, identity, networking, storage.
- Terraform CLI installed from the official source.
- A text editor such as VS Code.
- Start with the local provider lab before provisioning cloud resources.
- An approved Azure sandbox subscription and budget for cloud exercises.
- A dedicated learning directory and Git repository.

Do not use production subscriptions or company resources for practice without explicit approval. Cloud resources may incur costs.

## Recommended study method

1. Read each lesson before typing commands.
2. Use the local provider lab to learn the lifecycle without cloud cost.
3. Run `terraform fmt` and `terraform validate` regularly.
4. Always inspect `terraform plan` before an apply.
5. Keep configuration in Git but exclude state, plan files, and secret values.
6. Record Terraform, provider, and module versions.
7. Use a separate sandbox state and identity for practice.
8. Complete the exercise and self-check before moving forward.

## Essential safety rules

- Never commit Terraform state, state backups, plan files, secrets, or real tfvars containing credentials.
- Protect remote state with encryption, access control, locking, and recovery procedures.
- Use least-privilege identities and prefer approved workload identity/managed identity patterns.
- Never blindly apply a plan that contains unexpected deletion, replacement, public exposure, or role changes.
- Do not disable state locking to resolve a pipeline issue.
- Do not force-unlock or manually edit state without confirming the cause and following a recovery procedure.
- Keep production apply behind a protected approval workflow.
- Use explicit budgets, tags, and isolated resource groups for learning.
- Verify what a destroy operation will remove before confirming it.

## Azure focus

The advanced lessons use Azure examples to make Terraform useful in a practical cloud engineering path. The course covers:
- AzureRM provider and resource groups,
- subscription and tenant context,
- identity and least privilege,
- VNet/subnet and private connectivity design,
- storage and Key Vault integration,
- monitoring and diagnostic settings,
- remote state in Azure Storage,
- CI identity and approval gates.

Provider versions and resource arguments evolve. Pin reviewed versions and use the official provider documentation for the version in your project.

## How this course connects to other tracks

- [Linux](../linux/README.md) — CLI and operating-system foundations.
- [Docker](../docker/README.md) — package and run applications.
- [Kubernetes](../kubernetes/README.md) — deploy and operate workloads.
- [Azure](../azure/README.md) — Azure identity, networking, hosting, and monitoring.
- [MCP](../mcp/README.md) and [RAG](../rag/README.md) — examples of workloads that can use Terraform-managed infrastructure.

## Official references

- [Terraform documentation](https://developer.hashicorp.com/terraform/docs)
- [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials)
- [Terraform language](https://developer.hashicorp.com/terraform/language)
- [Terraform CLI](https://developer.hashicorp.com/terraform/cli)
- [Terraform state](https://developer.hashicorp.com/terraform/language/state)
- [Azure Terraform documentation](https://learn.microsoft.com/azure/developer/terraform/)
- [AzureRM provider documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)

Terraform CLI, providers, and cloud APIs change. Verify the current documentation and pin versions before implementation. This learning track is not a production certification; production use requires security review, tested recovery, subscription governance approval, and operational ownership.
