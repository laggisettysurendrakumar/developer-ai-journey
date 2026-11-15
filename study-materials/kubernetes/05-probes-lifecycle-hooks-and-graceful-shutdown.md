# 05 — Probes, Container Lifecycle and Graceful Shutdown

## Why Kubernetes needs health information

Kubernetes can see whether a container process is running, but the process being alive does not prove that the application is ready to receive traffic. Probes let Kubernetes ask the application about its state.

## 1. Three probe types

- Startup probe: gives a slow-starting application time to initialise. Other probes are held until startup succeeds.
- Readiness probe: decides whether the Pod should receive normal Service traffic.
- Liveness probe: detects a container that is no longer functioning and should be restarted.

## 2. HTTP probe example

```yaml
startupProbe:
  httpGet:
    path: /health/startup
    port: 8080
  periodSeconds: 5
  failureThreshold: 30

readinessProbe:
  httpGet:
    path: /health/ready
    port: 8080
  periodSeconds: 10
  timeoutSeconds: 2

livenessProbe:
  httpGet:
    path: /health/live
    port: 8080
  periodSeconds: 20
  timeoutSeconds: 2
```

The application must implement these routes. Set timings based on measured startup and response behaviour, not copied defaults.

## 3. Probe design mistakes

If readiness checks every downstream dependency and a database briefly slows down, every Pod may become NotReady. If liveness checks dependencies and fails, Kubernetes may repeatedly restart healthy application processes during a dependency outage.

Separate the meaning:
- Liveness: is this process able to continue?
- Readiness: should it receive traffic now?
- Startup: has initialisation completed?

## 4. Container lifecycle and termination

When Kubernetes terminates a Pod, it typically sends a termination signal to the container process and allows a grace period before forceful termination. Applications should handle shutdown signals, stop accepting new work, finish or safely abandon in-flight work, and close connections.

For background workers, define what happens to a message currently being processed when the Pod terminates. A message broker and idempotent consumer design help prevent lost or duplicated work.

## 5. preStop and termination grace

Lifecycle hooks can run actions around container start/stop. They should not be used as a substitute for application-level graceful shutdown. Keep hook actions short and predictable.

## Practice exercise

Implement startup, readiness, and liveness endpoints in a sample API. Add probes to its Pod template. Make readiness fail temporarily and observe that the Pod is removed from Service endpoints without an automatic restart. Then test graceful shutdown during a request.

## Self-check

- When is a startup probe useful?
- What happens to a Pod that fails readiness?
- Why should liveness usually avoid dependency health checks?
- What should an application do after receiving a termination signal?
- Why is graceful shutdown important for message consumers?

## Official references

- Liveness, readiness and startup probes: https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/
- Pod lifecycle: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
