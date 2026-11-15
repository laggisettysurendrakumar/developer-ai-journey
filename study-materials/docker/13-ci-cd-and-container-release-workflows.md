# 13 — CI/CD and Container Release Workflows

## What a container release pipeline should achieve

A release pipeline should build a known image, test it, scan it, publish it, and deploy that same immutable image to the target environment. Rebuilding a different image later weakens confidence that the tested artifact is the one being deployed.

## 1. Typical pipeline

1. Checkout source code.
2. Restore dependencies.
3. Run formatters, linters, and tests.
4. Build the container image.
5. Scan the image and dependencies.
6. Generate an SBOM and provenance where required.
7. Tag and push the image to a registry.
8. Deploy to a non-production environment.
9. Run smoke and integration tests.
10. Promote the same digest through approval-controlled environments.
11. Monitor health and retain rollback details.

## 2. Tag strategy

Useful tags can include semantic application version, CI build number, and source commit SHA. For deployment traceability, record the image digest as well as the human-readable tag.

## 3. GitHub Actions / Azure Pipelines concepts

Learn how your selected CI platform handles build agents/runners, workflow triggers, secrets and permissions, container registry authentication, build caching, artifact retention, environments and approvals, deployment identities, rollback, and release audit.

Use short-lived identity federation where supported. Avoid exposing long-lived registry credentials to untrusted pull-request jobs.

## 4. Release and rollback

Keep the previously deployed image digest available. Define which health signals trigger rollback and who can authorise it. Database migrations need backward-compatible rollout planning; rolling back application binaries does not automatically roll back data changes.

## Practice exercise

Create a pipeline that builds and tests a container image, scans it using an approved tool, pushes it to a test registry, and deploys it to a disposable test environment. Record the commit SHA and image digest, then perform a controlled rollback to the previous image.

## Self-check

- Why promote the same image through environments?
- What is the value of recording the digest?
- Why must untrusted pull requests not receive production credentials?
- What can make an application rollback unsafe after a database migration?
- Which checks should happen before publishing an image?

## Official references

- Docker CI/CD: https://docs.docker.com/build/ci/
- GitHub Actions: https://docs.github.com/actions
- Azure Pipelines: https://learn.microsoft.com/azure/devops/pipelines/
