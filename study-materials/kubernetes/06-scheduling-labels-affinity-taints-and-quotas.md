# 06 — Scheduling, Labels, Affinity, Taints and Quotas

## How Kubernetes chooses a node

The scheduler selects a node for each Pod that has not yet been assigned. It considers resource requests, node constraints, affinity rules, taints/tolerations, and other scheduling requirements.

A Pod that remains Pending may be waiting because no node can satisfy these requirements.

## 1. Resource requests

A container request tells the scheduler how much CPU and memory to reserve for placement decisions. It is not the same as observed usage.

```yaml
resources:
  requests:
    cpu: 250m
    memory: 256Mi
  limits:
    cpu: "1"
    memory: 1Gi
```

CPU is commonly expressed in millicores. Memory uses binary or decimal quantity suffixes. Choose values based on measurements and workload tests.

## 2. Node selectors and node affinity

Node labels allow workloads to target nodes with particular characteristics. Node affinity provides more expressive placement rules.

Do not hardcode provider-specific labels without checking the cluster standard. Overly strict placement can leave Pods Pending.

## 3. Taints and tolerations

A taint repels Pods unless they have a matching toleration. This can reserve nodes for particular workloads or keep general workloads away from special-purpose nodes.

A toleration allows scheduling with a matching taint; it does not force the Pod onto that node.

## 4. Pod affinity and anti-affinity

Affinity can place related Pods together; anti-affinity can spread replicas apart. Topology spread constraints can distribute Pods across zones or nodes.

These settings help resilience but can make workloads unschedulable if constraints are too strict or the cluster lacks capacity.

## 5. Namespace quotas and limits

ResourceQuota can limit aggregate resource consumption in a namespace. LimitRange can define defaults and constraints for individual containers or Pods.

These controls help teams share clusters responsibly. They must be planned with application requests and operational capacity.

## Practice exercise

Create a lab Deployment with CPU and memory requests/limits. Inspect node capacity and Pod placement. In a disposable namespace, create a quota and observe what happens when a workload exceeds it. Remove only lab resources.

## Self-check

- What does a resource request tell the scheduler?
- What can cause a Pod to remain Pending?
- What is the difference between a taint and a toleration?
- Why can strict anti-affinity reduce availability?
- What problem does a namespace quota help solve?

## Official references

- Scheduling: https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/
- Assigning Pods to Nodes: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
- Taints and tolerations: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
- Resource quotas: https://kubernetes.io/docs/concepts/policy/resource-quotas/
