# 04 — ConfigMaps, Secrets and Application Configuration

## Why configuration should be separate from images

The same container image may run in development, test, and production. Environment-specific values such as API URLs, log levels, and feature switches should be supplied at runtime rather than baked into the image.

## 1. ConfigMap for non-secret settings

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-config
  namespace: learning
data:
  ASPNETCORE_ENVIRONMENT: "Development"
  Logging__LogLevel__Default: "Information"
```

A ConfigMap can be consumed as environment variables or mounted files. It is intended for non-confidential data.

## 2. Secret for sensitive values

Kubernetes Secret objects represent sensitive data, but base64 encoding is not encryption. Configure encryption at rest, RBAC restrictions, and safe access patterns. Prefer an external secret manager integration where organisational policy requires it.

Create lab secrets interactively or via a protected secret manager. Avoid placing real secret values in Git, command history, shell traces, or terminal screenshots.

Example Deployment reference:

```yaml
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-credentials
        key: password
```

The Secret must exist in the same namespace as the Pod.

## 3. Environment variable versus mounted file

Environment variables are convenient for many applications. Mounted files can be useful for certificates or configuration formats that expect files. Consider how applications refresh configuration: environment variables do not automatically update in an already running process.

Mounted ConfigMap/Secret files may update eventually, but application reload behaviour still depends on the app and mount type. Do not assume a changed value is active without verification.

## 4. Configuration precedence in .NET

ASP.NET Core uses a configuration provider pipeline. Environment variables can override values from JSON files when added later in the configuration order. Double underscores represent hierarchy separators for environment-variable configuration.

For example, `Logging__LogLevel__Default` maps to `Logging:LogLevel:Default`.

Validate required configuration at startup. Fail clearly if a mandatory endpoint or credential is missing; do not silently run with an empty value.

## Practice exercise

Create a ConfigMap for a non-secret setting and attach it to a sample API Deployment. For secret handling, use a disposable local cluster and a dummy value. Demonstrate how the value reaches the process without printing the secret. Update configuration and document whether a restart is needed.

## Self-check

- When should a ConfigMap be used?
- Why is base64 not encryption?
- Why must a Secret and Pod be in the same namespace for a secretKeyRef?
- How does .NET environment-variable hierarchy work?
- Why should a production app validate configuration at startup?

## Official references

- ConfigMaps: https://kubernetes.io/docs/concepts/configuration/configmap/
- Secrets: https://kubernetes.io/docs/concepts/configuration/secret/
- ASP.NET Core configuration: https://learn.microsoft.com/aspnet/core/fundamentals/configuration/
