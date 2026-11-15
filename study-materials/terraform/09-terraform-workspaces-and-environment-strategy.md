# 09 — Terraform Workspaces and Environment Strategy

## The environment problem

Most applications have more than one environment: development, test, staging, and production. These environments may share the same architecture but need different names, sizes, network boundaries, and access permissions.

## 1. Separate configuration from environment values

Reusable Terraform configuration belongs in modules and root module files. Environment-specific values can be supplied through reviewed variable files or pipeline variables.

Example values file:

```hcl
environment     = "dev"
location        = "eastus"
instance_size   = "small"
```

The variables must be declared in the configuration. This example is only a value file.

## 2. Workspaces

Terraform workspaces associate separate state instances with the same configuration. They can be useful for simple parallel instances of the same configuration, but they are not automatically a full environment isolation or access-control strategy.

Workspaces share the same configuration and backend. If production requires different permissions, approval gates, or ownership, separate root configurations and state boundaries may be more appropriate.

## 3. Common environment strategies

| Strategy | Benefits | Trade-offs |
|---|---|---|
| Workspaces | Same configuration with separate state instances | Easy to misuse as security boundary |
| Separate directories/root modules | Clear environment-specific configuration and state | Some duplication |
| Shared modules + separate roots | Reuse with explicit isolation | Requires module discipline |
| Separate repositories | Strong ownership/release separation | More coordination and versioning |

## 4. Keep production distinct

Production should have:
- separately controlled state,
- least-privilege deployment identity,
- explicit approval,
- protected branch/release process,
- production-specific values and policies,
- stronger monitoring and rollback planning.

Never make production changes depend on an engineer's local workspace selection.

## 5. Naming, tags, and policy

Use consistent naming conventions and tags for application, environment, owner, cost centre, and managed-by information. Validate required tags and environment names. Avoid including secrets or personal information in tags.

## Practice exercise

Design dev, test, and prod configuration for an ASP.NET Core API. Choose a state separation approach and explain permissions, naming, size, network, and approval differences. Write a short decision record for why workspaces are or are not suitable.

## Self-check

1. What problem do Terraform workspaces solve?
2. Why are workspaces not an automatic security boundary?
3. What does a separate root configuration provide?
4. Which controls should differ for production?
5. Why are naming and tagging standards useful?

## Further reading

- [Terraform workspaces](https://developer.hashicorp.com/terraform/language/state/workspaces)
- [Terraform modules](https://developer.hashicorp.com/terraform/language/modules)
