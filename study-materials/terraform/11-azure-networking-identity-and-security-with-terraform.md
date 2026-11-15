# 11 — Azure Networking, Identity, and Security with Terraform

## Why infrastructure security belongs in the design

Terraform can create networks and identities, but a valid plan does not prove the design is secure. I need to understand exposure, identity scope, secret handling, and policy before provisioning resources.

## 1. Network fundamentals

An Azure network design may include:
- virtual networks,
- subnets,
- route tables,
- network security groups,
- private endpoints,
- DNS configuration,
- public IPs and load balancers.

Start with the traffic flow. Which clients need to reach the API? Which dependencies must the API call? Which paths must be denied?

## 2. Least privilege

Terraform automation identities should receive only the permissions required for the target scope. Prefer managed identity or workload identity federation where supported over long-lived client secrets.

Separate the identity used for a developer's local plan from the identity used to apply production changes.

## 3. Secrets

Do not put secrets in:
- committed `.tf` files,
- variable defaults,
- source-controlled tfvars,
- outputs,
- plan artifacts,
- logs.

A sensitive variable can be redacted in CLI output but may still be stored in state. Prefer a secret manager for application secrets and carefully limit access to Terraform state.

## 4. Private connectivity

Private endpoints can reduce public exposure for supported Azure services. They also require correct subnet, DNS, routing, and client configuration. Creating a private endpoint alone does not prove that public network access is disabled or that name resolution is correct.

## 5. Security controls in Terraform

Use:
- explicit network exposure settings,
- least-privilege role assignments,
- required resource tags,
- approved regions and SKUs,
- diagnostic settings,
- secure transport,
- policy checks,
- code review for public endpoints and broad roles.

## 6. Example: security review of a plan

Before applying a plan, search it for:
- `public_network_access_enabled`,
- `network_rules`,
- `role_assignment`,
- `administrator_login`,
- `password`,
- `secret`,
- `force_destroy`,
- resource replacement/deletion.

These keywords are review prompts, not a complete security scanner.

## Practice exercise

Design a network and identity boundary for an ASP.NET Core API that reads from a storage account and Key Vault. Draw allowed traffic, required identities, and denied paths. Write a security review checklist for the Terraform plan before provisioning.

## Self-check

1. What is least privilege?
2. Why is a private endpoint not sufficient by itself?
3. Why can secrets still be exposed through Terraform state?
4. Why separate plan and apply identities?
5. Which network and identity changes deserve additional review?

## Further reading

- [Azure security documentation](https://learn.microsoft.com/azure/security/)
- [AzureRM provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Terraform sensitive data](https://developer.hashicorp.com/terraform/language/state/sensitive-data)
