# 03 — Compute and Application Hosting

## What compute means

Compute is the capacity that runs application code. Choosing a hosting service affects how much infrastructure the team manages, how an application scales, how it connects to other services, and what the monthly bill looks like.

The aim is not to select the most complex service. It is to select the simplest service that meets the application's runtime, network, scale, resilience, and operational requirements.

## 1. Azure Virtual Machines

A virtual machine (VM) is a software-defined computer. You choose an operating system image, VM size, disks, network, and extensions.

A VM offers control over the OS and installed software, but the team is responsible for more operations:
- OS updates and hardening.
- Runtime and agent installation.
- Backup and recovery design.
- Monitoring and capacity management.
- Network and identity configuration.

Use VMs when OS-level control or software compatibility requires them. A VM is not automatically the best home for a simple ASP.NET Core API.

## 2. Azure App Service

App Service is a managed hosting platform for web applications and APIs. It reduces the need to manage the underlying VM and web-server infrastructure.

Learn these concepts:
- App Service plan: compute resources and pricing tier shared by apps in the plan.
- Web app: the application resource.
- Deployment slot: a separate live environment used for deployment and swap workflows where supported.
- Configuration settings: environment-specific settings exposed to the app.
- Scale up: move to a different plan tier or size.
- Scale out: add instances.

For an ASP.NET Core API, understand runtime support, deployment package/container options, health checks, logging, TLS, access restrictions, VNet integration, and deployment slots. Feature availability varies by plan and platform.

## 3. Azure Functions

Azure Functions runs event-triggered code such as HTTP handlers, queue consumers, timers, and scheduled jobs.

Important concepts:
- Trigger: event that starts the function.
- Binding: declarative connection to an input or output resource where supported.
- Hosting plan: determines scaling, execution characteristics, and pricing.
- Durable Functions: extensions for stateful orchestration patterns.

Functions fit event-driven tasks, scheduled maintenance, and small independent handlers. They are not automatically suitable for every long-running process or API. Check execution limits, scaling behaviour, networking, and hosting-plan details.

## 4. Azure Container Apps

Container Apps runs containerised applications without requiring the team to operate a Kubernetes cluster directly.

Learn:
- Container Apps environment.
- Revisions and traffic splitting.
- Ingress and internal services.
- Scaling rules, including event-driven scaling.
- Secrets and environment configuration.
- Jobs for finite background tasks.
- Dapr integration where appropriate.

A typical architecture can host an API and a separate worker as different apps, with a queue connecting them. This separates request handling from background processing.

## 5. Azure Kubernetes Service (AKS)

AKS is managed Kubernetes. Kubernetes orchestrates containers across a cluster and offers control over deployments, services, scheduling, networking, and extensibility.

Learn Kubernetes fundamentals before AKS:
- Pods, Deployments, Services and Namespaces.
- ConfigMaps and Secrets.
- Readiness and liveness probes.
- Requests and limits.
- Ingress and network policies.
- Persistent volumes.
- Horizontal scaling and disruption budgets.

AKS brings cluster-level responsibilities and a broader security and operations surface. Use it when its control and ecosystem are justified by requirements, not simply because the application runs in a container.

## 6. Decision guide

| Requirement | Potential starting point | Questions to validate |
|---|---|---|
| Standard ASP.NET Core web API | App Service | Runtime, deployment, networking, scaling |
| Containerised API and worker with simple operations | Container Apps | Ingress, event scale, revisions, environment |
| Event-driven short tasks | Azure Functions | Trigger, execution model, timeout, plan |
| Custom OS or legacy runtime requirement | Virtual Machines | Patching, backup, security ownership |
| Complex Kubernetes platform requirements | AKS | Cluster operations, skill, governance, cost |

## 7. Container deployment flow

For a containerised API:
1. Create a Dockerfile.
2. Build and test locally.
3. Scan the image and dependencies.
4. Push the image to a registry such as Azure Container Registry (ACR).
5. Configure the hosting service to pull the approved image.
6. Set environment configuration and managed identity.
7. Add health checks, logs, metrics, and rollback.
8. Promote the same immutable image through environments.

Do not bake secrets into the image. Keep configuration external and use managed identity or an approved secret-management approach.

## Practice exercise

Take a simple ASP.NET Core 8 Web API with one background job. Compare App Service, Container Apps, Functions, and AKS. For each, list operational responsibilities, deployment approach, scaling model, network requirements, and potential cost drivers. Recommend one and explain what new requirement would change your decision.

## Self-check

- What does an App Service plan represent?
- What is the difference between scale up and scale out?
- How is a Function different from a continuously running web API?
- Why might Container Apps be a better fit than AKS for a small team?
- What additional responsibilities come with Kubernetes?

## Official references

- App Service: https://learn.microsoft.com/azure/app-service/
- Azure Functions: https://learn.microsoft.com/azure/azure-functions/
- Container Apps: https://learn.microsoft.com/azure/container-apps/
- AKS: https://learn.microsoft.com/azure/aks/
- Virtual Machines: https://learn.microsoft.com/azure/virtual-machines/
- Azure Container Registry: https://learn.microsoft.com/azure/container-registry/
