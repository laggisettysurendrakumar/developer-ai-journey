# 14 — Production Support Scenarios, Runbooks and SRE Practices

## Move from ad-hoc support to repeatable operations

A good support engineer does more than fix one incident. They make the next incident easier to detect, diagnose, mitigate, and prevent.

This lesson builds practical runbooks and service reliability practices for Kubernetes workloads.

## 1. Standard support workflow

Use this repeatable sequence:

1. Acknowledge the incident and confirm severity.
2. Establish scope: cluster, namespace, workload, users, region.
3. Check current health and recent changes.
4. Gather logs, events, metrics, and traces.
5. Form a hypothesis and validate it with evidence.
6. Select the smallest safe mitigation.
7. Obtain approval for disruptive changes.
8. Apply the change and observe.
9. Confirm user-visible recovery.
10. Document the timeline and follow-up work.

## 2. Runbook: API is down

**Symptoms:** HTTP 5xx, connection timeout, readiness failures, or no Service endpoints.

**Diagnostics:**

```bash
kubectl get deploy,pods,svc,endpointslices -n <namespace>
kubectl describe deployment <name> -n <namespace>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
kubectl logs deployment/<name> -n <namespace> --since=30m
```

Check recent rollout, Pod readiness, selectors, endpoint count, application listener, ingress/gateway, DNS, policy, and dependency health.

**Mitigation:** Follow incident authority. Depending on evidence, mitigation may include stopping a rollout, rolling back a known bad release, or routing traffic to a healthy region. Do not apply a blanket restart without evidence.

**Verification:** Confirm healthy endpoints, successful synthetic requests, falling error rate, and acceptable latency.

## 3. Runbook: Pod restart spike

**Symptoms:** Restart count increasing or CrashLoopBackOff.

```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> --previous
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

Review exit code, OOMKilled, failed probes, application stack trace, dependency errors, and image/config change. Compare across replicas and nodes.

Do not assume all restarts are application bugs; node maintenance, eviction, and probe misconfiguration can also restart Pods.

## 4. Runbook: Node NotReady

**Symptoms:** Node condition becomes NotReady; Pods may become unavailable or unschedulable.

Inspect node conditions, events, resource pressure, kubelet/runtime health through approved node monitoring, provider maintenance events, and network/storage health.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
kubectl get pods -A -o wide
```

Do not SSH to a production node or restart services unless authorised and following platform procedures. Managed Kubernetes nodes may be replaced through provider workflows.

## 5. Runbook: DNS or connectivity issue

Check Service name, namespace, Service endpoints, CoreDNS health, NetworkPolicy, CNI health, ingress/gateway controller, and upstream DNS/routing.

Test name resolution from an approved diagnostic Pod. Do not install random debugging packages in production containers.

## 6. Runbook: storage incident

Check PVC state, Pod mount events, PV and StorageClass, CSI components, node attachment limits, storage capacity, and backend service health.

Do not delete PVC/PV or detach storage manually to clear an error. Verify backup and recovery status before any storage operation.

## 7. Runbook: capacity pressure

Review node CPU/memory/disk pressure, Pod requests/limits, Pending Pods, HPA state, quota, IP capacity, and downstream dependency limits. Scale only after identifying the constraint and confirming budget and platform quota.

## 8. SLOs, SLIs and error budgets

- SLI: a measured indicator of service quality, such as successful request ratio or latency.
- SLO: the target for that indicator over a defined period.
- SLA: an external commitment that may have contractual consequences.
- Error budget: the allowed unreliability implied by an SLO over its measurement window.

Define SLIs from user experience rather than only Pod health. A cluster may be green while customers are unable to complete an important workflow.

## 9. Change and maintenance operations

Before node drain, cluster upgrade, certificate rotation, or ingress controller change:
- Confirm change window and owner.
- Review replicas and PDBs.
- Check capacity in remaining nodes/zones.
- Validate backup and rollback.
- Confirm monitoring and communication.
- Test in non-production.
- Execute one controlled step at a time.
- Verify workload health after each stage.

## 10. Production support skills matrix

| Skill | Evidence of competence |
|---|---|
| Cluster navigation | Confirms context and target before changes |
| Pod troubleshooting | Uses describe, events and current/previous logs |
| Deployment support | Explains rollout, ReplicaSet and rollback limitations |
| Service networking | Diagnoses labels, endpoints, DNS, ingress and policy |
| Resource support | Understands requests, limits, OOM and scheduling |
| Storage support | Diagnoses PVC/PV/CSI without unsafe deletion |
| Security operations | Uses RBAC, least privilege and safe secret handling |
| Release operations | Reviews image digest, health, change approval and rollback |
| Incident management | Captures impact, timeline, evidence and mitigation |
| Reliability engineering | Defines SLI/SLO and turns incidents into corrective actions |

## Final capstone — Kubernetes production support simulation

Deploy a sample API and worker into a local or authorised non-production cluster. Add Service, configuration, probes, resource requests/limits, HPA where supported, and monitoring.

Run these controlled failure scenarios:
1. Invalid image reference.
2. Application exits at startup.
3. Readiness endpoint returns failure.
4. Service selector does not match Pods.
5. Insufficient resource request/capacity causes Pending.
6. PVC cannot bind.
7. A release introduces an application error.
8. A dependency becomes unreachable.

For each incident, document symptoms, commands, evidence, root cause, safe mitigation, verification, and preventive action.

### Definition of done

- I can safely confirm cluster context and permissions.
- I can triage without exposing secrets or changing state prematurely.
- I can identify common Pod, deployment, service, DNS, storage, and resource failures.
- I can explain the blast radius and approval needs of corrective operations.
- I can verify recovery using user-facing signals.
- I can write a useful incident timeline and runbook.
- I can propose monitoring and engineering changes that reduce repeat incidents.

## Self-check

- Why should production support begin with impact and read-only evidence?
- What could make a node NotReady?
- Why is deleting a PVC not an acceptable routine troubleshooting action?
- How do SLOs differ from Pod health checks?
- What turns an incident fix into a lasting reliability improvement?

## Official references

- Kubernetes troubleshooting: https://kubernetes.io/docs/tasks/debug/
- Kubernetes operations: https://kubernetes.io/docs/concepts/cluster-administration/
- Site Reliability Engineering workbook: https://sre.google/workbook/table-of-contents/
- Google SRE book: https://sre.google/sre-book/table-of-contents/
