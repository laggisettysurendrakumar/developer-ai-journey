# 11 — Kubernetes Observability: Logs, Metrics, Events and Tracing

## Why observability is essential

A cluster can report that a Pod is Running while requests fail. To support production, combine Kubernetes state with application-level telemetry.

The main signals are:
- Metrics: numerical measurements over time.
- Logs: timestamped events from applications and system components.
- Traces: connected operations across services.
- Events: Kubernetes reports about scheduling, image pulls, probes, and resource changes.

## 1. First-line kubectl diagnostics

```bash
kubectl get nodes
kubectl get pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
kubectl top nodes
kubectl top pods -A
```

Metrics commands require a working metrics pipeline such as metrics-server or a provider-specific solution. Do not assume they are available in every cluster.

## 2. Logs

```bash
kubectl logs <pod-name> -n <namespace>
kubectl logs <pod-name> -c <container-name> -n <namespace>
kubectl logs <pod-name> --previous -n <namespace>
kubectl logs -f <pod-name> -n <namespace>
```

- Use `-c` when a Pod has multiple containers.
- Use `--previous` to retrieve logs from the previous crashed container instance when available.
- Use `-f` to follow logs.

Collect only the time range and data needed. Redact secrets, tokens, customer details, and sensitive prompts before sharing diagnostic output.

## 3. Events

Events often explain why a Pod is Pending, a volume did not mount, or a container failed to start.

```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

Events are useful but may be short-lived and are not a complete audit history. Use centralised monitoring and audit logs for longer-term investigations.

## 4. Metrics and dashboards

Track:
- Node CPU, memory, filesystem, and network pressure.
- Pod/container CPU and memory.
- Restarts and OOM kills.
- Deployment replica availability.
- Request rate, error rate, and latency.
- Queue depth and age.
- Service dependency failures.
- Persistent volume capacity and latency.

Prometheus and Grafana are common ecosystem tools, while managed Kubernetes offerings may provide integrated monitoring.

## 5. Tracing

Distributed tracing follows a request through API, worker, database, and downstream services. Propagate trace context and correlation IDs. Avoid adding sensitive user content to span attributes.

## Practice exercise

Deploy a simple API and create a controlled failure. Use Pods, events, describe, logs, and metrics to identify the cause. Write a short incident timeline with symptom, evidence, root cause, mitigation, and prevention.

## Self-check

- What is the difference between logs, metrics, traces, and events?
- Why can Pod Running coexist with failed requests?
- What does `kubectl logs --previous` show?
- Why are events not a substitute for long-term audit logs?
- Which signals help identify memory pressure?

## Official references

- Kubernetes monitoring: https://kubernetes.io/docs/tasks/debug/
- Metrics pipeline: https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/
- Prometheus: https://prometheus.io/docs/introduction/overview/
- OpenTelemetry: https://opentelemetry.io/docs/
