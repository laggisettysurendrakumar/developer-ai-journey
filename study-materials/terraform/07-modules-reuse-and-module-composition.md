# 07 — Modules, Reuse, and Module Composition

## What is a module?

A Terraform module is a collection of Terraform configuration files in one directory. A root module is the working directory where I run Terraform. Child modules are called by the root module or by other modules.

Modules allow teams to reuse reviewed infrastructure patterns instead of copying resource blocks everywhere.

## 1. A simple module structure

```text
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    └── naming/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Keep module purpose narrow. A module should have a clear contract: inputs, outputs, assumptions, and lifecycle expectations.

## 2. Call a child module

```hcl
module "naming" {
  source = "./modules/naming"

  application_name = var.application_name
  environment      = var.environment
}

output "resource_prefix" {
  value = module.naming.resource_prefix
}
```

The example assumes the child module declares the input variables and output shown. A module call creates a boundary for reuse, not a separate Terraform state by default.

## 3. Module interface design

Good module inputs:
- use clear names and types,
- validate allowed values,
- provide sensible non-production defaults only when safe,
- avoid dozens of unrelated toggles,
- avoid accepting secrets unnecessarily.

Good outputs:
- expose only values consumers need,
- document whether the value is sensitive,
- avoid returning credentials just for convenience.

## 4. Version external modules

Pin module sources to a reviewed version or commit where appropriate. Inspect the module code, providers, permissions, and resource behaviour before adopting a public module. A popular registry module is not automatically safe for your organisation.

## 5. Avoid over-modularising

A module that wraps one trivial resource and has no reuse or abstraction value can make Terraform harder to navigate. Start with clear root configuration and extract modules when a repeated pattern or ownership boundary exists.

## 6. Test modules

Test:
- input validation,
- expected plan output,
- required outputs,
- security defaults,
- behaviour across supported environments,
- upgrades between module versions.

## Practice exercise

Create a local child module that accepts an application name and environment and outputs a standardised prefix. Call it from the root module. Then write a module README describing inputs, outputs, assumptions, and an example call.

## Self-check

1. What is the root module?
2. Does a child module automatically have a separate state file?
3. What makes a good module interface?
4. Why pin and review external modules?
5. When does a module add unnecessary complexity?

## Further reading

- [Terraform modules](https://developer.hashicorp.com/terraform/language/modules)
- [Terraform module development](https://developer.hashicorp.com/terraform/language/modules/develop)
