# 06 — Key Vault, Secrets and Application Configuration

## Configuration is not the same as a secret

Applications need settings such as a feature name, environment, endpoint, timeout, and credentials. Some settings are ordinary configuration; others are sensitive secrets.

A value does not become safe merely because it is stored in an environment variable. Avoid committing secrets to source control, placing them in container images, or printing them in logs.

## 1. Azure Key Vault

Key Vault stores and controls access to secrets, cryptographic keys, and certificates. It centralises access management and can support auditing and lifecycle controls.

Typical use cases:
- Third-party API credentials where managed identity is not supported.
- Certificates and private keys.
- Cryptographic key operations.
- Application secrets that must be centrally managed.

For Azure-hosted applications, prefer managed identity to authenticate to Key Vault. Then grant only the required data-plane role. The exact role depends on whether the app needs to read secrets, keys, or certificates.

## 2. Managed identity plus Key Vault

A recommended flow looks like:

1. The application is deployed to an Azure compute resource.
2. The resource has a managed identity enabled.
3. The identity is granted narrowly scoped permission to the required vault data.
4. The application uses an Azure SDK credential chain to authenticate.
5. The SDK requests the required secret from Key Vault.
6. The application uses the value without writing it into logs.

If Key Vault is unavailable, decide whether the request should fail, use a controlled cached value, or fall back to another approved configuration source. Do not silently substitute an empty credential.

## 3. ASP.NET Core configuration

ASP.NET Core configuration can be assembled from multiple providers. Typical sources include JSON files, environment variables, command-line arguments, and secret/configuration providers.

For example:

```json
{
  "Features": {
    "NewSearch": true
  },
  "Services": {
    "SearchTimeoutSeconds": 15
  }
}
```

Environment-variable names can represent nested keys using double underscores:

```bash
Features__NewSearch=false
Services__SearchTimeoutSeconds=30
```

The double underscore maps to the configuration hierarchy separator on supported .NET configuration providers. Provider precedence matters: a later provider can override an earlier provider. Check how your application builds its configuration pipeline rather than assuming a file edit changes the effective value.

## 4. Separate configuration by environment

Use different values for development, test, and production. Avoid copying production secrets into local settings. Keep environment-specific configuration explicit and reviewable.

A useful design:
- Non-secret defaults in source-controlled configuration.
- Deployment-specific values in an approved configuration mechanism.
- Secrets in Key Vault or an approved secret store.
- Managed identity for Azure-to-Azure access.
- Configuration validation at application startup.

## 5. Rotation and access

Plan how a secret is replaced without unnecessary downtime. Applications may need to refresh cached values or reload configuration. Do not assume every library automatically reloads a secret when the underlying value changes.

Track:
- Who can create, read, update, and delete secrets.
- Which application uses each secret.
- Expiry/rotation expectations.
- Whether old credentials can be revoked safely.
- Audit and alerting requirements.

## Practice exercise

Build a small ASP.NET Core API that reads a non-secret feature setting from configuration and retrieves a lab secret from Key Vault using DefaultAzureCredential. Run locally with an approved developer identity, then describe the changes required to run with a managed identity in Azure. Do not paste a real secret into source control or a shared chat.

## Self-check

- Why is a secret different from an ordinary configuration value?
- What is the role of managed identity in Key Vault access?
- What does the double underscore mean in .NET configuration keys?
- Why can editing appsettings.json fail to change the effective setting?
- What must be considered when rotating credentials?

## Official references

- Key Vault overview: https://learn.microsoft.com/azure/key-vault/general/overview
- Key Vault authentication: https://learn.microsoft.com/azure/key-vault/general/authentication
- ASP.NET Core configuration: https://learn.microsoft.com/aspnet/core/fundamentals/configuration/
- Azure Identity for .NET: https://learn.microsoft.com/dotnet/azure/sdk/authentication/
