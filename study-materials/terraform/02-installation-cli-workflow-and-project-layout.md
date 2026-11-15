# 02 — Installation, CLI Workflow, and Project Layout

## Goal

I will set up Terraform locally, understand the command-line workflow, and organise a small project. Start with a local provider lab before creating any billable cloud infrastructure.

## 1. Install Terraform

Follow the official installation guide for the operating system in use. Use an approved package manager or signed installation package. Check the installed version:

```bash
terraform version
```

The version matters because Terraform CLI syntax, lock files, and provider compatibility can change. Pin the version used by a team and its CI pipeline.

## 2. Understand the project files

A simple project can contain:

```text
terraform-lab/
├── versions.tf
├── providers.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars.example
└── .gitignore
```

Terraform loads all `.tf` files in a working directory as one module. Splitting files by responsibility is for human readability; Terraform does not execute them in filename order.

## 3. Learn the essential commands

| Command | Plain-English purpose |
|---|---|
| `terraform init` | Prepares the working directory and downloads providers/modules |
| `terraform fmt` | Formats Terraform files consistently |
| `terraform validate` | Checks configuration syntax and internal consistency |
| `terraform plan` | Previews proposed changes |
| `terraform apply` | Applies changes after approval |
| `terraform show` | Displays a saved or current plan/state view |
| `terraform output` | Displays declared output values |
| `terraform providers` | Shows required providers |
| `terraform state list` | Lists objects tracked in state |

Some commands can reveal sensitive values or alter state. Understand their effect and access requirements before using them in a shared environment.

## 4. Safe local file lab

Create `main.tf`:

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

resource "local_file" "learning_note" {
  filename = "terraform-note.txt"
  content  = "This file is managed by Terraform."
}

output "note_path" {
  value = local_file.learning_note.filename
}
```

Run:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
```

Read the plan. It should propose creating one local file. Apply only in this disposable learning folder:

```bash
terraform apply
```

Confirm the file exists. This local provider example avoids creating cloud resources.

## 5. Inspect the result

```bash
terraform output
terraform state list
terraform show
```

The state records the resource managed by the current Terraform working directory. Do not delete or manually edit state files as a normal cleanup step.

## 6. Git hygiene

Terraform state and variable files can contain sensitive values. A starter `.gitignore`:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfplan
crash.log
*.auto.tfvars
*.auto.tfvars.json
terraform.tfvars
override.tf
override.tf.json
*_override.tf
*_override.tf.json
```

Commit `.terraform.lock.hcl` so provider selections and checksums are reviewed and repeatable. Do not commit secrets or real environment-specific credentials.

## Practice exercise

Create the local file lab in a new folder. Run each command and write one sentence explaining what it checks or changes. Inspect the generated files and explain why the lock file should normally be committed while state should not be committed.

## Self-check

1. What does `terraform init` prepare?
2. Does Terraform execute files in filename order?
3. What is the difference between `plan` and `apply`?
4. Why should state files be excluded from Git?
5. Why is the dependency lock file important?

## Further reading

- [Terraform CLI commands](https://developer.hashicorp.com/terraform/cli/commands)
- [Terraform installation](https://developer.hashicorp.com/terraform/install)
