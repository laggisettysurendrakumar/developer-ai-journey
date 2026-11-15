# 04 — Variables, Locals, Outputs, and Type Constraints

## Why configuration needs inputs

A Terraform configuration should not hardcode every value. Variables allow the same module to be reused for development, test, and production while supplying environment-specific values.

## 1. Input variables

```hcl
variable "environment" {
  description = "Environment name"
  type        = string

  validation {
    condition     = contains(["dev", "test", "prod"], var.environment)
    error_message = "Environment must be dev, test, or prod."
  }
}
```

The variable has a description, type constraint, and validation rule. Validation catches invalid values before Terraform applies changes.

## 2. Common types

- `string`: text value.
- `number`: numeric value.
- `bool`: true or false.
- `list(string)`: ordered list of strings.
- `set(string)`: unique unordered strings.
- `map(string)`: key/value mapping.
- `object({...})`: structured object with declared attributes.

Use types to make module contracts clear.

## 3. Locals

Locals name expressions and reduce repeated logic.

```hcl
variable "application_name" {
  type = string
}

variable "environment" {
  type = string
}

locals {
  name_prefix = "${var.application_name}-${var.environment}"
  common_tags = {
    application = var.application_name
    environment = var.environment
    managed_by  = "terraform"
  }
}
```

A local is not an input from the user. It is a named expression inside the module.

## 4. Outputs

Outputs expose selected values after Terraform operations.

```hcl
output "name_prefix" {
  description = "Name prefix for resources"
  value       = local.name_prefix
}
```

Avoid outputting credentials, connection strings, or private values unless there is a well-justified need and the sensitivity is handled correctly.

## 5. Supplying values

Values can come from defaults, command-line arguments, environment variables, variable files, or workspace settings. Understand precedence and avoid relying on undocumented local defaults.

Example `dev.tfvars`:

```hcl
application_name = "knowledge-assistant"
environment      = "dev"
```

Do not put secrets in a committed tfvars file.

## 6. Sensitive variables

Marking a variable as `sensitive = true` helps redact it from normal CLI output. It does not encrypt a value by itself or guarantee it is absent from state. Protect state and use approved secret-management mechanisms.

## Practice exercise

Create variables for application name, environment, location, and tags. Add validation for environment and output a naming prefix. Test valid and invalid values using local-only resources.

## Self-check

1. Why are variables useful?
2. When should you use a local instead of a variable?
3. What is the purpose of an output?
4. What is the difference between a list and a set?
5. Does `sensitive = true` remove a value from state?

## Further reading

- [Terraform input variables](https://developer.hashicorp.com/terraform/language/values/variables)
- [Terraform locals](https://developer.hashicorp.com/terraform/language/values/locals)
- [Terraform outputs](https://developer.hashicorp.com/terraform/language/values/outputs)
