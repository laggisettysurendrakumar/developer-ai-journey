# 10 — Health Checks, Logs and Troubleshooting

## What does it mean for a container to be healthy?

A container may be running while its application is unable to serve requests. For example, a process may be alive but unable to connect to its database. Health checks help distinguish process status from application health.

## 1. Process state versus application readiness

- Running: the container's main process is active.
- Exited: the main process has stopped.
- Healthy: a configured health check is currently succeeding.
- Unhealthy: a configured health check has repeatedly failed.

A health check is only useful if it tests something meaningful. Do not make liveness depend on every remote service, because a temporary dependency problem can cause unnecessary restarts.

## 2. Example Dockerfile health check

If the image includes a suitable HTTP client and the app exposes `/health`, a health check can be configured. Minimal images may not have `curl` installed, so do not copy this snippet without checking the tool exists.

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1
```

For production, use the runtime platform's supported health probes and distinguish readiness from liveness.

## 3. Troubleshooting commands

```bash
docker ps -a
docker logs --tail 100 <container>
docker inspect <container>
docker stats <container>
docker port <container>
docker exec -it <container> sh
```

Use these in order:
1. Check container state and exit code.
2. Read application logs.
3. Inspect environment, port bindings, mounts, and network.
4. Check CPU and memory pressure.
5. Enter the container if an interactive shell is available.
6. Reproduce with the same image and non-sensitive configuration.

## 4. Common failure patterns

| Symptom | Possible cause | What to check |
|---|---|---|
| Container exits immediately | Main process completed or crashed | Exit code and logs |
| Connection refused | Wrong port or app listening only on loopback | App listen address and port mapping |
| Cannot resolve service name | Containers not on same network | Network membership and Compose service name |
| Data disappears | Data written to container layer | Volume mount and data path |
| Permission denied | Runtime UID cannot access path | File ownership and writable directories |
| Image pull denied | Registry auth or repository permission | Registry login and pull identity |
| Works locally, fails in deployment | Different configuration, architecture, network, or secret access | Compare effective settings and platform logs |

## 5. Logs and observability

Applications should write operational logs to stdout/stderr so the container runtime can collect them. Include timestamps and correlation IDs where appropriate. Avoid logging passwords, tokens, personal data, or sensitive prompts.

For production, centralise logs, metrics, and traces in the approved monitoring platform. Define retention, access controls, alert thresholds, and incident runbooks.

## Practice exercise

Break a local API intentionally by changing its port or environment configuration. Diagnose the failure with container status, logs, inspect, and network commands. Fix the configuration and write down the evidence that identified the cause.

## Self-check

- Why does a running container not guarantee a healthy API?
- What is the difference between readiness and liveness?
- Where should application logs be written?
- What could cause a host port to return connection refused?
- Why should troubleshooting start with logs and inspection rather than random changes?

## Official references

- Docker health checks: https://docs.docker.com/reference/dockerfile/#healthcheck
- Docker logs: https://docs.docker.com/reference/cli/docker/container/logs/
- Docker Desktop troubleshooting: https://docs.docker.com/desktop/troubleshoot-and-support/
