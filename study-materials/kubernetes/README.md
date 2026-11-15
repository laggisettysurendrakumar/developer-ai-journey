# Kubernetes Engineering — Beginner to Advanced, with Production Support

This course starts from the basics and builds toward the skills needed to deploy, operate, troubleshoot, and support Kubernetes workloads in real environments.

I use Kubernetes to manage containerised applications across a cluster. Kubernetes can help with scheduling, service discovery, scaling, rollout management, and workload recovery. It also introduces operational complexity, so learn Docker and container fundamentals first and choose Kubernetes when the workload and platform requirements justify it.

A major part of this course is production support. It covers how to investigate incidents using evidence, how to identify the failing layer, how to make safe changes, and how to verify recovery without making an incident worse.

## Who this course is for

This track is suitable for someone starting with Kubernetes as well as developers and support engineers who want to become confident operating workloads.

Helpful prerequisites:
- Basic Linux commands and file permissions.
- Docker images, containers, ports, networks, and volumes.
- HTTP APIs and health endpoints.
- YAML, environment variables, and Git.
- Basic networking concepts such as DNS, IP addresses, and ports.

## How to study

1. Read each concept in plain language before copying commands.
2. Understand what a Kubernetes object or command changes.
3. Practise on kind, minikube, or another disposable local cluster.
4. Check the current context and namespace before every state-changing operation.
5. Use read-only commands first during troubleshooting.
6. Record symptoms, events, logs, metrics, root cause, and recovery evidence.
7. Never experiment on a production cluster without permission, change approval, and an agreed rollback or mitigation plan.

Some exercises create resources or incur cloud costs. Use an authorised learning environment and remove only resources you created.

## Learning roadmap

| Stage | Lessons | What I learn |
|---|---|---|
| Beginner foundations | 01–02 | Cluster architecture, API, kubectl, Pods, ReplicaSets, Deployments |
| Application networking/configuration | 03–05 | Services, DNS, ingress/gateway, ConfigMaps, Secrets, probes, lifecycle |
| Workload operations | 06–08 | Scheduling, storage, StatefulSets, autoscaling, disruption and capacity |
| Security and release engineering | 09–10 | RBAC, ServiceAccounts, NetworkPolicies, Helm, Kustomize, GitOps |
| Monitoring and platform operations | 11–12 | Logs, events, metrics, traces, cluster maintenance, upgrades |
| Production support and SRE | 13–14 | Incident triage, failure diagnosis, safe remediation, runbooks, SLOs, support simulation |

## Lesson index

### Beginner — Understand the platform

1. [Kubernetes Foundations, Architecture and the API](01-kubernetes-foundations-architecture-and-api.md) — control plane, nodes, API server, reconciliation, namespaces, labels, contexts, and kubectl.
2. [Pods, ReplicaSets and Deployments](02-pods-replicasets-and-deployments.md) — Pod lifecycle, selectors, replicas, scaling, image updates, rollouts, and rollback limitations.

### Application networking and configuration

3. [Services, Ingress, Gateway and DNS](03-services-ingress-gateway-and-dns.md) — Service types, selectors, EndpointSlices, DNS, Ingress controllers, Gateway API, and connectivity.
4. [ConfigMaps, Secrets and Application Configuration](04-configmaps-secrets-and-application-configuration.md) — configuration delivery, Secret security, external secret management, and ASP.NET Core configuration.
5. [Probes, Container Lifecycle and Graceful Shutdown](05-probes-lifecycle-hooks-and-graceful-shutdown.md) — startup, readiness, liveness, termination, and worker shutdown.

### Intermediate — Scheduling, storage and scaling

6. [Scheduling, Labels, Affinity, Taints and Quotas](06-scheduling-labels-affinity-taints-and-quotas.md) — scheduler decisions, resource requests, node placement, tolerations, and namespace limits.
7. [Kubernetes Storage, PV, PVC and StatefulSets](07-storage-pv-pvc-and-statefulsets.md) — volumes, dynamic provisioning, storage classes, stateful workloads, and backup considerations.
8. [Autoscaling, Disruption and Capacity](08-autoscaling-disruption-and-capacity.md) — HPA, VPA, cluster autoscaler, PDB, capacity planning, and AI workload constraints.

### Advanced — Security and release management

9. [RBAC, ServiceAccounts and NetworkPolicies](09-rbac-serviceaccounts-and-networkpolicies.md) — authentication, authorisation, least privilege, workload identity, and network segmentation.
10. [Helm, Kustomize and Release Management](10-helm-kustomize-and-release-management.md) — charts, releases, overlays, manifest rendering, GitOps, and controlled deployment.

### Monitoring and platform lifecycle

11. [Kubernetes Observability: Logs, Metrics, Events and Tracing](11-observability-logs-metrics-events-and-tracing.md) — kubectl diagnostics, metrics pipeline, Prometheus/Grafana, events, and distributed tracing.
12. [Cluster Installation, Upgrades and Maintenance](12-cluster-installation-upgrades-and-maintenance.md) — local clusters, managed Kubernetes, compatibility, upgrades, node drain, and recovery planning.

### Production support — Real operational activities

13. [Production Support Playbook: Triage and Incident Management](13-production-support-playbook-triage-and-incident-management.md) — first five-minute checks, common incidents, diagnostic workflows, rollout support, safe remediation, and post-incident reviews.
14. [Production Support Scenarios, Runbooks and SRE Practices](14-production-support-scenarios-runbooks-and-sre.md) — API outages, restart spikes, NotReady nodes, DNS, storage, capacity, SLOs, and support simulations.

## Production support quick reference

These are examples of read-only first-response commands. Confirm the active context, namespace, and access policy before running them.

```bash
# Which cluster will kubectl use?
kubectl config current-context

# What is happening across the cluster?
kubectl get nodes
kubectl get pods -A -o wide

# What happened recently in the affected namespace?
kubectl get events -n <namespace> --sort-by=.lastTimestamp

# Why is a particular Pod failing?
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace> --since=30m
kubectl logs <pod-name> -n <namespace> --previous

# Are Services routing to ready Pods?
kubectl get svc,endpointslices -n <namespace>

# Is resource usage available?
kubectl top nodes
kubectl top pods -n <namespace>
```

The metrics commands require a configured metrics pipeline. Some clusters have provider-specific monitoring instead.

### Production command safety

**Read-only diagnostics:** `get`, `describe`, `logs`, `top`, `api-resources`, `explain`, and `auth can-i` are generally used for inspection.

**Potentially disruptive or state-changing operations:** `apply`, `delete`, `scale`, `patch`, `rollout restart`, `rollout undo`, `cordon`, `drain`, and interactive `exec` can affect running workloads or cluster state.

Before remediation, verify the target, assess blast radius, check capacity and redundancy, review PodDisruptionBudgets and rollout status, follow approval policy, record the current state, and define how recovery will be verified.

Never delete a production PVC or namespace, restart every Pod, or drain a node as a generic first troubleshooting step.

## Recommended hands-on journey

Build and operate a small ASP.NET Core API and a background worker:

1. Containerise both services with Docker.
2. Create a local Kubernetes cluster using kind or minikube.
3. Deploy the API using a Deployment.
4. Expose it through a ClusterIP Service.
5. Add ConfigMaps, approved Secret handling, and health probes.
6. Set CPU and memory requests/limits.
7. Add a worker and queue dependency.
8. Configure persistent storage only where the workload needs it.
9. Add RBAC and NetworkPolicy in a supported local environment.
10. Package resources with Helm or Kustomize.
11. Add metrics, logs, and tracing.
12. Simulate incidents and complete the support runbooks.
13. Deploy to an authorised non-production managed cluster.
14. Document upgrade, rollback, backup, and recovery procedures.

## Production support scenarios to practise

- CrashLoopBackOff and repeated restarts.
- Pending Pods caused by capacity, affinity, quota, or storage.
- ImagePullBackOff and registry permission failures.
- Service selectors with no endpoints.
- DNS, Ingress, Gateway, TLS, and NetworkPolicy problems.
- Readiness/liveness probe failures.
- OOMKilled containers and CPU throttling.
- PVC binding, mount, and CSI problems.
- HPA not scaling or scaling too aggressively.
- Deployment rollout failures and rollback decisions.
- Node NotReady, maintenance, cordon, and drain.
- Cluster version and add-on upgrade issues.

For each scenario, practise documenting symptoms, impact, evidence, timeline, root cause, mitigation, validation, and preventive actions.

## Skills checklist

- [ ] Explain the control plane, node components, API server, scheduler, and controllers.
- [ ] Verify cluster context and namespace before making changes.
- [ ] Deploy, inspect, scale, update, and roll back a Deployment.
- [ ] Explain Pods, ReplicaSets, labels, selectors, and namespaces.
- [ ] Configure Services, DNS, Ingress, Gateway, and TLS.
- [ ] Manage ConfigMaps and Secrets safely.
- [ ] Design startup, readiness, and liveness probes.
- [ ] Explain requests, limits, scheduling, taints, affinity, and quotas.
- [ ] Configure PVs, PVCs, StorageClasses, and StatefulSets.
- [ ] Understand HPA, VPA, cluster autoscaler, and PDBs.
- [ ] Apply least-privilege RBAC and NetworkPolicies.
- [ ] Use Helm and Kustomize to manage releases.
- [ ] Diagnose using events, logs, metrics, and traces.
- [ ] Plan cluster maintenance and upgrades safely.
- [ ] Triage common production incidents using read-only evidence first.
- [ ] Assess blast radius and approvals before remediation.
- [ ] Write runbooks, incident timelines, and post-incident actions.
- [ ] Define user-focused SLIs and SLOs.

## Suggested pace

A first pass may take 6–10 weeks with regular hands-on practice. Becoming proficient in production support requires repeated troubleshooting in realistic non-production environments and learning the policies and architecture of the actual platform you support. The goal is not to memorise commands; it is to understand the evidence and impact behind each action.

## Official references

- [Kubernetes documentation](https://kubernetes.io/docs/home/)
- [Kubernetes concepts](https://kubernetes.io/docs/concepts/)
- [kubectl reference](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes tasks](https://kubernetes.io/docs/tasks/)
- [Kubernetes troubleshooting](https://kubernetes.io/docs/tasks/debug/)
- [Kubernetes security](https://kubernetes.io/docs/concepts/security/)
- [Helm](https://helm.sh/docs/)
- [Kustomize](https://kustomize.io/)
- [Google SRE book](https://sre.google/sre-book/table-of-contents/)

Kubernetes releases, API versions, provider features, controllers, CNI/CSI behaviour, and command options change. Confirm compatibility with the cluster version and provider documentation before production use.
