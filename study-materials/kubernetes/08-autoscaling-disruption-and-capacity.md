# 08 — Autoscaling, Disruption Budgets and Capacity

## Why scaling needs more than replica count

A service may need more Pods when traffic increases, or fewer Pods when demand drops. Kubernetes provides scaling mechanisms, but autoscaling works only when metrics, resource requests, workload behaviour, and cluster capacity are understood.

## 1. Manual scaling

```bash
kubectl scale deployment/api --replicas=4 -n learning
```

Manual scaling changes the desired replica count. It is useful for learning and controlled interventions, but it is not an automatic response to changing traffic.

## 2. Horizontal Pod Autoscaler

HPA adjusts the replica count based on observed metrics such as CPU or memory, depending on configuration and metrics availability.

Before creating an HPA:
- Ensure resource requests are configured.
- Confirm the metrics pipeline is available.
- Define minimum and maximum replicas.
- Understand expected startup time.
- Ensure dependencies can handle increased load.

A high CPU percentage can be misleading when requests are absent or inappropriate.

## 3. Vertical Pod Autoscaler

VPA can provide recommendations or adjust resource requests/limits depending on its mode and configuration. It complements HPA in some cases, but combinations require careful planning, especially when both act on CPU/memory.

## 4. Cluster autoscaler

A cluster autoscaler can add or remove nodes based on unschedulable workloads and utilization rules, depending on the platform. Pod scaling does not help if the cluster cannot supply nodes or node quota is exhausted.

## 5. PodDisruptionBudget

A PodDisruptionBudget (PDB) can limit voluntary disruptions, such as maintenance-driven evictions. It does not guarantee availability during all failures and does not block every involuntary disruption.

## 6. Capacity planning

Measure:
- CPU and memory by workload.
- Pod startup time.
- Peak requests and concurrency.
- Queue depth and processing age.
- Downstream database and model quota.
- Node capacity and IP address space.
- Storage throughput and capacity.

For AI workloads, GPU availability, device scheduling, model memory, and provider rate limits may be key constraints.

## Practice exercise

Deploy a simple API with resource requests. Configure HPA in a local cluster only if metrics are available. Generate controlled load, observe replica count and resource use, then document why scaling may not improve latency if the database or external API is the bottleneck.

## Self-check

- What does HPA change?
- Why are resource requests important for CPU-based HPA?
- What does cluster autoscaler manage?
- What is a PDB intended to protect?
- Why can scaling API Pods overload a downstream database?

## Official references

- Horizontal Pod Autoscaling: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
- Vertical Pod Autoscaling: https://kubernetes.io/docs/concepts/workloads/autoscaling/vertical-pod-autoscale/
- Cluster autoscaler: https://github.com/kubernetes/autoscaler
- Pod disruptions: https://kubernetes.io/docs/concepts/workloads/pods/disruptions/
