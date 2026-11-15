# 05 — Expressions, Functions, Loops, and Dynamic Blocks

## Why expressions matter

Terraform expressions allow configuration to be reusable and data-driven. I use them to avoid repeating similar resource blocks and to derive names and values consistently.

## 1. Common expression types

- Reference: `var.environment`
- Conditional: `var.environment == "prod" ? "Premium" : "Basic"`
- For expression: transform a collection into another collection.
- Function: `lower("APP")`, `join("-", ["api", "dev"])`
- Splat: access an attribute across a list of objects.

Read the expression carefully; a short expression can still create many cloud resources.

## 2. for_each

`for_each` creates one resource instance for each map or set entry. It is useful when each item needs a stable key.

```hcl
variable "service_names" {
  type = set(string)
  default = ["api", "worker"]
}

resource "local_file" "service_notes" {
  for_each = var.service_names

  filename = "${each.key}.txt"
  content  = "Terraform learning resource for ${each.key}"
}
```

Each resource has a stable address such as `local_file.service_notes["api"]`.

## 3. count

`count` creates a number of indexed instances. It is convenient for simple optional or repeated resources, but inserting or removing elements in a list can shift indexes. For named objects, `for_each` is often easier to manage.

## 4. for expressions

```hcl
variable "names" {
  type    = list(string)
  default = ["API", "Worker", "Scheduler"]
}

locals {
  normalised_names = [for name in var.names : lower(name)]
}
```

The expression converts the list to lowercase values.

## 5. Dynamic blocks

A dynamic block generates nested configuration blocks from a collection. It can reduce repetition but can make modules harder to read. Use it only when a provider schema genuinely requires repeated nested blocks.

## 6. Use functions carefully

Terraform has built-in functions for strings, collections, files, encoding, and numeric operations. Use only documented functions and validate inputs. Do not treat Terraform as a general-purpose programming language.

## Practice exercise

Use a map of two synthetic service names and ports to create local text files or output objects. Compare `count` and `for_each` resource addresses. Explain which one is easier to maintain when a new service is inserted.

## Self-check

1. What does `for_each` create?
2. When is `count` appropriate?
3. Why are stable resource keys useful?
4. What does a for expression do?
5. Why should dynamic blocks be used sparingly?

## Further reading

- [Terraform expressions](https://developer.hashicorp.com/terraform/language/expressions)
- [Terraform functions](https://developer.hashicorp.com/terraform/language/functions)
