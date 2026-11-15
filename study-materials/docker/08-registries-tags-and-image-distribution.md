# 08 — Registries, Tags and Image Distribution

## Why an image registry is needed

Building an image on a developer laptop does not make it available to a test server or cloud platform. An image registry stores versioned container images so pipelines and runtime platforms can pull them.

Common registries include Docker Hub, GitHub Container Registry, and Azure Container Registry (ACR).

## 1. Image names and tags

An image reference commonly has this structure:

```text
registry-host/namespace/image-name:tag
```

Examples:
- `nginx:1.27`
- `ghcr.io/example/developer-api:1.2.0`
- `myregistry.azurecr.io/developer-api:build-1042`

A tag is a human-readable pointer to an image. Tags can be moved; the same tag name is not a guarantee that the underlying contents never change. For stronger deployment reproducibility, record the image digest.

## 2. Tag and push a local image

Assuming you are authenticated to a registry and have permission to push:

```bash
docker build -t developer-api:1.0.0 .
docker tag developer-api:1.0.0 <registry-host>/<namespace>/developer-api:1.0.0
docker push <registry-host>/<namespace>/developer-api:1.0.0
```

Replace placeholders with the real registry host and namespace. Do not paste passwords into command history.

## 3. Pull and run on another machine

```bash
docker pull <registry-host>/<namespace>/developer-api:1.0.0
docker run --rm -p 8080:8080 <registry-host>/<namespace>/developer-api:1.0.0
```

The registry makes the image portable between machines, subject to CPU architecture, runtime compatibility, and access permissions.

## 4. Authentication

Use the registry's supported secure login mechanism. CI/CD should authenticate through short-lived credentials or workload identity where supported. Avoid personal access tokens with broad, permanent permissions.

For private images, the deployment platform's identity must have permission to pull the image. Application permissions and registry pull permissions are separate concerns.

## 5. Image digests and provenance

An image digest identifies immutable image content. Record the digest used by a deployment when reliable traceability matters.

A mature pipeline can also attach build metadata, source commit, software bill of materials (SBOM), vulnerability scan results, and image signatures or provenance attestations.

## Practice exercise

Build an API image, tag it for a registry you are authorised to use, push it, then pull it from a clean environment. Record the tag and digest. Confirm that the deployment uses the exact image you intended.

## Self-check

- What does a registry store?
- Why can a mutable tag such as `latest` be ambiguous?
- What does an image digest identify?
- Why is registry pull access different from application access to a database?
- Why should CI avoid long-lived broad registry credentials?

## Official references

- Docker image push: https://docs.docker.com/reference/cli/docker/image/push/
- Docker Hub: https://docs.docker.com/docker-hub/
- Azure Container Registry: https://learn.microsoft.com/azure/container-registry/
- GitHub Container Registry: https://docs.github.com/packages/working-with-a-github-packages-registry/working-with-the-container-registry
