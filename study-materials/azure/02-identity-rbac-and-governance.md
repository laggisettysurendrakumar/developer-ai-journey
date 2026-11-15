# 02 — Identity, RBAC and Governance

## Why identity comes before application deployment

Every application and administrator needs an identity. That identity must prove who or what it is, and Azure must decide what it is allowed to do.

Two questions must stay separate:

1. **Authentication:** who are you?
2. **Authorisation:** what are you allowed to do?

A successful sign-in does not mean that the caller can read a storage account, deploy a resource, or retrieve a secret.

## 1. Microsoft Entra ID concepts

Microsoft Entra ID is Microsoft's cloud identity and access-management service. Common objects include:
- Users: people who sign in.
- Groups: collections of users and other principals for access management.
- App registrations: application identity definitions and configuration.
- Service principals: application identities represented in a tenant.
- Managed identities: identities managed by Azure for supported workloads.

Use groups for team access where possible. Assigning an individual user directly to many resources can become difficult to review and maintain.

## 2. Azure role-based access control (RBAC)

Azure RBAC controls access to Azure resources. A role assignment combines:
- **Security principal:** user, group, service principal, or managed identity.
- **Role definition:** allowed actions, such as read or manage.
- **Scope:** where the permissions apply.

Scopes form a hierarchy: management group, subscription, resource group, and individual resource. A role assigned at a broader scope can flow down to child resources.

Prefer least privilege: give an identity only the permissions it needs, at the narrowest practical scope. Avoid giving Owner or Contributor simply because a deployment failed. First identify the missing action and required scope.

## 3. Built-in role examples

- Reader: view resources without changing them.
- Contributor: manage resources, but does not grant permission to assign roles.
- Owner: manage resources and assign access.
- User Access Administrator: manage user access at a scope.

These are broad examples, not a recommendation to assign them indiscriminately. Many services have narrower data-plane roles. For example, permission to manage a storage account resource is not automatically the same as permission to read blobs inside it.

## 4. Managed identities

A managed identity gives an Azure workload an identity that can be used to authenticate to supported services. Azure manages the credential lifecycle; the application does not need a long-lived client secret embedded in source code.

Common types:
- System-assigned: lifecycle is tied to one Azure resource.
- User-assigned: a separate identity can be attached to one or more supported resources.

Choose based on lifecycle and reuse. A system-assigned identity is simple for a single workload. A user-assigned identity can be useful when identity lifecycle should be independent of a compute resource or shared across a controlled set of workloads.

Conceptual .NET example using Azure Identity:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

var vaultUri = new Uri(
    Environment.GetEnvironmentVariable("KEY_VAULT_URI")
    ?? throw new InvalidOperationException("KEY_VAULT_URI is required."));

var client = new SecretClient(vaultUri, new DefaultAzureCredential());
KeyVaultSecret secret = await client.GetSecretAsync("service-setting");
Console.WriteLine("Secret retrieved successfully.");
// Do not print secret.Value to logs.
```

The example uses a credential chain suitable for local development and Azure-hosted workloads when configured correctly. Local developer sign-in and the deployed managed identity are different identities; grant the right identity access in each environment. Install the `Azure.Identity` and `Azure.Security.KeyVault.Secrets` NuGet packages and configure the vault URI and role assignment before running it.

## 5. Governance with Azure Policy and locks

Azure Policy helps enforce or audit organisational rules, such as allowed regions, required tags, or approved resource types. Policies can audit, deny, append, or deploy related configuration depending on the policy effect.

A resource lock helps prevent accidental deletion or modification:
- CanNotDelete: resource can be changed but not deleted.
- ReadOnly: resource cannot be modified or deleted through operations blocked by the lock.

Policy and locks solve different problems. Neither replaces RBAC. A user may have permission to perform an operation that policy blocks, or a lock may prevent deletion even when the user has broad RBAC permissions.

## 6. Governance lab

In an approved lab subscription:
1. Inspect your existing role assignments; do not change shared access.
2. Create a resource group with standard tags.
3. Explore Azure Policy definitions and assignments in read-only mode.
4. Review the difference between Reader, Contributor, and Owner.
5. If permitted, add a CanNotDelete lock to your lab resource group and test the expected effect.
6. Remove the lock before cleaning up your lab.

## Practice exercise

Design access for a web API that reads blobs and a secret from Key Vault. Name the identity, list the minimum permissions, and state the scope of each assignment. Explain why an application should not use the developer's personal identity in production.

## Self-check

- What is the difference between authentication and authorisation?
- What are the three parts of an RBAC role assignment?
- Why is Contributor not always sufficient for assigning access?
- How does a managed identity differ from a client secret?
- What does Azure Policy do that RBAC does not?

## Official references

- Microsoft Entra ID: https://learn.microsoft.com/entra/fundamentals/whatis
- Azure RBAC: https://learn.microsoft.com/azure/role-based-access-control/overview
- Managed identities: https://learn.microsoft.com/entra/identity/managed-identities-azure-resources/overview
- Azure Policy: https://learn.microsoft.com/azure/governance/policy/overview
- Resource locks: https://learn.microsoft.com/azure/azure-resource-manager/management/lock-resources
