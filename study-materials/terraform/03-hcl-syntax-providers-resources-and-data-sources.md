# 03 — HCL Syntax, Providers, Resources, and Data Sources

## What is HCL?

HashiCorp Configuration Language (HCL) is the language commonly used to describe Terraform configuration. It is declarative: I describe the desired infrastructure, and Terraform determines the operations needed to move towards it.

## 1. Blocks and arguments

Terraform configuration is built from blocks and arguments.

```hcl
resource "local_file" "hello" {
  filename = "hello.txt"
  content  = "Hello from Terraform"
}
```

- `resource` is the block type.
- `local_file` is the resource type.
- `hello` is the local Terraform name.
- `filename` and `content` are arguments.

The full resource address is `local_file.hello`. This address is how Terraform identifies the resource in configuration and state.

## 2. Providers

A provider is a plugin that implements communication with a platform or service.

Example provider requirement:

```hcl
terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}
```

The source identifies the provider package and the version constraint describes acceptable versions. The lock file records the selected provider version and checksums.

For Azure, the commonly used provider source is `hashicorp/azurerm`. Check the current provider documentation and pin a reviewed version.

## 3. Resources

A resource represents something Terraform manages, such as a resource group, storage account, or virtual network.

The provider documentation defines valid resource types, arguments, computed attributes, and lifecycle behaviour. Never guess an argument name from its English description.

## 4. Data sources

A data source reads information about an existing object without creating it.

Conceptual example:

```hcl
data "azurerm_resource_group" "existing" {
  name = var.resource_group_name
}

output "resource_group_location" {
  value = data.azurerm_resource_group.existing.location
}
```

This example requires the AzureRM provider, authentication, and a declared variable. It reads an existing resource group. A data source is not a replacement for an authorisation check in an application.

## 5. Resource references and dependencies

When one resource argument references another resource's attribute, Terraform can infer a dependency.

For example, a subnet may reference the ID of a virtual network. Terraform understands that the network must exist before it can create the subnet.

Use explicit `depends_on` only when Terraform cannot infer a real dependency from references. Excessive explicit dependencies can reduce parallelism and hide unclear design.

## Practice exercise

Using the local provider, create two resources where the second uses an output attribute of the first. Draw the dependency relationship. Then inspect one Azure provider resource in the official Registry and identify its required and optional arguments without provisioning it.

## Self-check

1. What is a Terraform block?
2. What makes a resource address unique inside a module?
3. What is the purpose of a provider?
4. How does a data source differ from a managed resource?
5. How does Terraform infer dependencies?

## Further reading

- [Terraform language](https://developer.hashicorp.com/terraform/language)
- [Terraform providers](https://developer.hashicorp.com/terraform/language/providers)
- [AzureRM provider documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
