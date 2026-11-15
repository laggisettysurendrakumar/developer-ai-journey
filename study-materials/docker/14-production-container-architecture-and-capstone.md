# 14 — Production Container Architecture and Capstone

## From a local container to a production service

Running an API on a laptop is a useful first milestone. Operating it reliably for users requires more: secure identity, resource limits, health checks, scaling, monitoring, data durability, deployment control, and incident procedures.

## 1. Production readiness areas

### Application
- Listen on the configured interface and port.
- Handle graceful shutdown.
- Use structured logs and correlation IDs.
- Configure request timeouts and dependency resilience.
- Expose readiness and liveness endpoints appropriately.

### Image
- Use a maintained and reviewed base image.
- Use multi-stage builds where appropriate.
- Run as non-root.
- Keep images minimal without sacrificing debugging or compatibility needs.
- Pin and record dependencies and image digests.
- Scan and inventory the final image.

### Runtime
- Set CPU and memory requests/limits through the platform.
- Restrict network access and filesystem writes.
- Inject configuration and secrets securely.
- Use health probes and restart policies deliberately.
- Avoid running privileged containers.

### Operations
- Centralise logs, metrics, and traces.
- Alert on user impact, resource pressure, and dependency failures.
- Keep deployment and rollback runbooks.
- Test backup and recovery processes.
- Define ownership and cost allocation.

## 2. Choosing the runtime platform

| Platform | Good starting point | Operational responsibility |
|---|---|---|
| Docker on a developer machine | Local development and learning | Developer manages local engine and data |
| Docker Compose | Repeatable local multi-service environments | Team maintains Compose project |
| Azure Container Apps | Managed container APIs, workers and event-driven workloads | Platform manages much of container infrastructure |
| Azure App Service (container) | Web applications with a managed app-hosting model | Platform-specific app and network configuration |
| Kubernetes / AKS | Workloads needing Kubernetes APIs and ecosystem | Team owns more cluster and platform operations |

A container image is portable, but the runtime platforms are not interchangeable in their networking, identity, scaling, storage, and operations. Choose based on requirements.

## 3. Capstone — Containerise a Developer Knowledge Assistant

Build a small API and a separate background worker. Use a local Compose environment for development, then publish a reviewed image to a registry and deploy it to a non-production managed container platform.

### Milestones

1. Create a minimal ASP.NET Core or FastAPI API with a health endpoint.
2. Add a Dockerfile and `.dockerignore`.
3. Build the image and run it as a non-root user.
4. Add runtime configuration without embedding secrets.
5. Add a worker and a local data dependency using Compose.
6. Add a named volume for local development data.
7. Verify service discovery using Compose service names.
8. Add health checks and application timeouts.
9. Add a CI workflow to restore, test, build, scan, and publish the image.
10. Record the image digest and deploy to a test environment.
11. Add logs, metrics, alerts, and a rollback procedure.
12. Document resource sizing, security controls, and cost assumptions.

### Definition of done

- The application builds from a documented command.
- A clean machine can run the Compose environment using documented setup.
- No secrets are committed or embedded in image layers.
- Application containers run with least privilege.
- Data required across container replacement is stored in a persistent service/volume.
- API and worker communicate through service discovery, not hardcoded IPs.
- Health and error behaviour can be diagnosed from logs and telemetry.
- The pipeline tests and scans the image before publishing.
- The deployed image digest is recorded and rollback is documented.
- Architecture choices and limitations are written down.

## Practice exercise

Create a production-readiness review for the capstone. Draw the request path, identify every container, registry, secret, data store, network, and identity, then list failure scenarios and recovery steps. Ask a peer to review the design before deploying it.

## Self-check

- Which responsibilities differ between local Compose and a managed container platform?
- Why is image immutability useful for releases?
- What does graceful shutdown protect?
- Which data needs persistence beyond a container lifecycle?
- What evidence demonstrates that the image is secure and deployable?

## Official references

- Docker documentation: https://docs.docker.com/
- Azure Container Apps: https://learn.microsoft.com/azure/container-apps/
- Kubernetes concepts: https://kubernetes.io/docs/concepts/
- Azure Well-Architected Framework: https://learn.microsoft.com/azure/well-architected/
