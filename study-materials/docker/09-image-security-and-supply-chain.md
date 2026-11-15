# 09 — Image Security and Software Supply Chain

## Why image security matters

A container image contains an operating-system user space, application dependencies, and application code. Vulnerabilities or unnecessary privileges in any of these areas can affect the workload.

Security should be part of the build pipeline, not an action performed only after deployment.

## 1. Use trusted and maintained base images

Choose official or organisation-approved base images. Check support lifecycle, update frequency, provenance, architecture, and vulnerability status. Pin an intentional version rather than blindly using a moving tag.

Rebuild images regularly so that updated base-image and dependency fixes are incorporated.

## 2. Run as a non-root user

The container process should not run with more privilege than required. Use a non-root user, avoid privileged mode, and do not mount the Docker socket into application containers unless a carefully reviewed use case requires it.

A Dockerfile can define `USER` to select the runtime identity. Confirm the chosen user can read application files and write only to required directories.

## 3. Keep secrets out of images

Never place credentials in Dockerfile literals, source files copied into the build context, image environment defaults, build arguments used as a secret transport, or build logs.

Use BuildKit secret mounts for secrets genuinely required during a build, and runtime secret injection or workload identity for runtime access. Prefer designs that do not need secrets during image construction.

## 4. Scan and inventory

Use the image scanner approved by your organisation. Review findings rather than relying only on a severity count:
- Is the vulnerable package present in the final image?
- Is the vulnerable code path reachable?
- Is a fixed version available?
- Does the application depend on the affected behaviour?
- What compensating controls exist?

Generate or retain an SBOM where required. Use image signatures and provenance verification where your release process supports them.

## 5. Runtime controls

Apply controls such as read-only root filesystem where compatible, dropped Linux capabilities, seccomp and other platform restrictions, CPU and memory limits, restricted network access, non-root user, minimal mounted files and secrets, and centralised logs.

Exact control support varies between Docker, Kubernetes, and managed container platforms.

## 6. Container security is not only image security

Also protect the host/cluster, registry, build pipeline, identities, network, storage, and runtime configuration. A secure image can still be compromised by an over-permissioned identity or an exposed management endpoint.

## Practice exercise

Review a Dockerfile and image for a sample API. Identify the base image, user, exposed ports, packages, secrets, copied files, and write permissions. Produce a short remediation list and verify that the API still starts after applying reasonable hardening.

## Self-check

- Why is non-root execution helpful?
- Why can build arguments leak secrets?
- What is an SBOM?
- Why is a vulnerability scan not the same as a security review?
- Why does container security include the host and pipeline?

## Official references

- Docker security: https://docs.docker.com/engine/security/
- Build secrets: https://docs.docker.com/build/building/secrets/
- Docker Scout: https://docs.docker.com/scout/
- CIS Docker Benchmark: https://www.cisecurity.org/benchmark/docker
