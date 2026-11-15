# 08 — Deployment, CI/CD and Azure DevOps

## What CI/CD means

Continuous Integration (CI) builds and validates code changes frequently. Continuous Delivery or Deployment (CD) automates getting validated software into environments.

A good pipeline makes a release repeatable and traceable. It should not become a collection of hidden manual steps that only one developer understands.

## 1. Typical pipeline stages

1. Checkout source code.
2. Restore dependencies.
3. Run formatting, static analysis, and security checks.
4. Build the application.
5. Run unit and integration tests.
6. Build and scan a container image if applicable.
7. Publish a versioned artifact.
8. Deploy to a non-production environment.
9. Run smoke tests and health checks.
10. Require appropriate approval and policy checks for production.
11. Deploy the same artifact to production.
12. Verify health and retain a rollback path.

Do not rebuild a different binary for production after testing a different one. Promote the same immutable artifact where practical.

## 2. Azure DevOps concepts

Azure DevOps includes:
- Repos: Git repositories.
- Pipelines: CI/CD definitions.
- Artifacts: package feeds and build outputs.
- Boards: work item tracking.
- Test Plans: test management capabilities.

Learn YAML pipelines, triggers, stages, jobs, steps, variables, templates, environments, service connections, approvals, and pipeline permissions.

GitHub Actions can also deploy to Azure. The pipeline platform is a choice; secure identity, reproducibility, and release controls matter more than loyalty to one tool.

## 3. Authenticate pipelines securely

Prefer workload identity federation or other approved short-lived authentication methods where supported. Avoid long-lived service-principal secrets stored as plain pipeline variables.

Limit which repositories, branches, workflows, and environments can use a deployment identity. Production deployments should have explicit permissions and auditability.

## 4. Deployment strategies

- Rolling deployment: gradually replace old instances.
- Blue/green: deploy a separate environment and switch traffic.
- Canary: send a small portion of traffic to a new version before increasing it.
- Slot swap: use App Service deployment slots when the plan and configuration support the workflow.

Each strategy needs health checks, observability, compatibility planning, and rollback criteria. Database schema changes require special care because application versions may overlap during a rollout.

## 5. Environment configuration and release safety

Keep configuration separate from build artifacts. Validate required settings before starting the application. Avoid using production secrets in test environments.

Use feature flags to release functionality independently from deployment when a controlled flag system is available. Feature flags also need owners, expiry/review dates, and safe defaults.

## Practice exercise

Create a pipeline for an ASP.NET Core API that restores, builds, tests, publishes, and deploys to a non-production App Service. Add a smoke test. Document how the production stage is protected and how you would roll back a faulty release. Use a disposable or authorised environment.

## Self-check

- What is the difference between CI and CD?
- Why should the same artifact be promoted between environments?
- Why are long-lived pipeline secrets risky?
- What makes a deployment rollback safe?
- Why can a database migration make application rollback harder?

## Official references

- Azure Pipelines: https://learn.microsoft.com/azure/devops/pipelines/
- Deploy to App Service: https://learn.microsoft.com/azure/app-service/deploy-best-practices
- Workload identity federation in Azure DevOps: https://learn.microsoft.com/azure/devops/pipelines/library/connect-to-azure
- GitHub Actions for Azure: https://learn.microsoft.com/azure/developer/github/
