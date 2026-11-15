# Docker Engineering — Beginner to Advanced

This track builds practical Docker skills from the first container command to secure image builds, multi-container development, registry publishing, automated delivery, and production readiness.

The goal is to understand what Docker is doing at each step. I explain the purpose of commands, the problems they solve, and when I would use them in a real application. Examples include Python APIs and ASP.NET Core services.

## Prerequisites

No previous Docker experience is required. Basic terminal usage, HTTP, and familiarity with one programming language are enough to begin. Basic Linux commands are helpful; the [Linux learning track](../linux/README.md) is a useful companion.

## How to study

1. Read the plain-English explanation before copying a command.
2. Understand the expected result and why the command is needed.
3. Run one command at a time and inspect its output.
4. Use names ending in `-lab` for disposable resources so they are easy to identify.
5. Complete each practice exercise and answer the self-check.
6. Keep secrets, company code, and customer data out of images, build contexts, and public registries.
7. Revisit a lesson when the commands work but the underlying reason is still unclear.

Docker commands can remove containers, networks, images, and volumes. Read cleanup commands carefully and never run them against shared or production resources.

## Learning roadmap

| Stage | Lessons | Outcome |
|---|---|---|
| Foundations | 01–02 | Understand Docker Engine, images, containers, and lifecycle commands |
| Build application images | 03–04 | Create Dockerfiles, understand layers, and containerise Python/.NET APIs |
| Connect and persist | 05–07 | Configure ports, networks, volumes, and Compose |
| Distribute and secure | 08–09 | Publish images and improve supply-chain/runtime security |
| Operate and optimise | 10–12 | Troubleshoot, build faster, and support repeatable testing |
| Deliver and design | 13–14 | Automate releases and complete a production-minded capstone |

## Lesson index

### Beginner — Understand Docker

1. [Containers, Images and Docker Engine](01-containers-images-and-docker-engine.md) — what Docker solves, the engine, images, containers, and the first safe commands.
2. [Docker Commands and Container Lifecycle](02-docker-commands-and-container-lifecycle.md) — run, inspect, logs, exec, stop, start, and remove.

### Build images — Package real applications

3. [Dockerfiles, Build Context and Image Layers](03-dockerfile-build-context-and-layers.md) — Dockerfile instructions, .dockerignore, build context, layers, and caching.
4. [Multi-Stage Builds and ASP.NET Core Containers](04-multistage-builds-and-dotnet-containers.md) — SDK versus runtime images, .NET publish, non-root execution, and configuration.

### Connect services — Networking and persistence

5. [Ports, Networks and Service Discovery](05-ports-networks-and-service-discovery.md) — host/container ports, bridge networks, service names, and connectivity debugging.
6. [Volumes, Bind Mounts and Persistent Data](06-volumes-bind-mounts-and-persistence.md) — container writable layers, named volumes, bind mounts, and data lifecycle.
7. [Docker Compose and Multi-Container Applications](07-docker-compose-multi-container-apps.md) — describe and run an API with supporting services.

### Share and secure images

8. [Registries, Tags and Image Distribution](08-registries-tags-and-image-distribution.md) — Docker Hub, GHCR, ACR, image tags, digests, and pull permissions.
9. [Image Security and Software Supply Chain](09-image-security-and-supply-chain.md) — trusted images, non-root users, secret handling, SBOMs, scanning, and runtime controls.

### Operate and optimise

10. [Health Checks, Logs and Troubleshooting](10-healthchecks-logs-and-troubleshooting.md) — readiness, liveness, logs, inspect, stats, and common failure patterns.
11. [Compose Workflows for Development and Testing](11-docker-compose-for-development-and-testing.md) — local databases, profiles, integration tests, environment files, and persistence.
12. [BuildKit, Cache and Build Performance](12-buildkit-cache-and-build-performance.md) — Buildx, multi-platform builds, cache optimisation, and build secrets.

### Advanced — Release and production readiness

13. [CI/CD and Container Release Workflows](13-ci-cd-and-container-release-workflows.md) — build, test, scan, publish, promote by digest, deploy, and roll back.
14. [Production Container Architecture and Capstone](14-production-container-architecture-and-capstone.md) — production readiness, platform choices, and a Developer Knowledge Assistant deployment project.

## Recommended hands-on project

Build one Developer Knowledge Assistant throughout the course:

- Start with a minimal ASP.NET Core or FastAPI API.
- Create a Dockerfile and .dockerignore.
- Run the API as a non-root user.
- Add environment-based configuration without embedding credentials.
- Add a worker and a supporting data service with Compose.
- Use networks for service discovery and named volumes for local persistence.
- Publish an image to a registry you are authorised to use.
- Add security scanning and CI/CD.
- Deploy the same image digest to a non-production container platform.
- Add health checks, logs, metrics, alerting, and rollback guidance.

## Skills checklist

- [ ] Explain how images, containers, layers, registries, and Docker Engine relate.
- [ ] Run, inspect, debug, stop, and remove a container safely.
- [ ] Write a Dockerfile and understand build context and .dockerignore.
- [ ] Use layer caching and multi-stage builds.
- [ ] Containerise an ASP.NET Core API and a Python API.
- [ ] Configure ports, networks, DNS/service discovery, and volumes.
- [ ] Run a multi-service development environment with Compose.
- [ ] Push and pull images from a registry.
- [ ] Use image digests and understand tag mutability.
- [ ] Apply non-root execution, secret handling, scanning, and supply-chain controls.
- [ ] Diagnose startup, connectivity, data, and permission failures.
- [ ] Build and release images with CI/CD.
- [ ] Explain production container operations and platform trade-offs.

## Suggested pace

A first pass may take 3–5 weeks with regular practice, depending on the time available and how much of the capstone is implemented. Spend more time debugging and rebuilding than memorising command options. Container skills become useful when you can explain why a deployment works and diagnose why it does not.

## Official documentation

- [Docker documentation](https://docs.docker.com/)
- [Docker Get Started](https://docs.docker.com/get-started/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Docker Build](https://docs.docker.com/build/)
- [Docker security](https://docs.docker.com/engine/security/)
- [Docker Scout](https://docs.docker.com/scout/)

Docker features, image tags, base-image support, and platform behaviour evolve. Check current official documentation before using a command or image tag in a production environment.
