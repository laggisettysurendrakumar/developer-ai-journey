# 12 — Cluster Installation, Upgrades and Maintenance

## Cluster lifecycle is an operational responsibility

A Kubernetes cluster needs maintenance: control-plane and node upgrades, security patches, certificates, add-on updates, capacity planning, and backup of cluster configuration and application data.

Managed services reduce some infrastructure work but do not remove the need for version, add-on, workload, and compatibility planning.

## 1. Learning clusters

For local practice, use kind, minikube, or Docker Desktop Kubernetes. They are useful for learning objects and kubectl, but do not reproduce every production networking, storage, or cloud integration.

For production, learn the platform your organisation operates, such as AKS, EKS, or GKE. Follow provider upgrade and support policies.

## 2. Version compatibility

Kubernetes components and add-ons have version compatibility requirements. Check the supported version skew and provider-specific version lifecycle before upgrading.

Inventory:
- Control plane and node versions.
- CNI and CSI plugins.
- DNS and ingress/gateway controllers.
- Metrics and monitoring agents.
- Admission controllers and policy engines.
- Helm charts and CRDs.
- Application API versions.

## 3. Upgrade planning

A safe upgrade plan includes:
1. Read provider release notes and known issues.
2. Check API deprecations and removed versions.
3. Test in non-production.
4. Check PodDisruptionBudgets and available spare capacity.
5. Confirm backups and recovery plans.
6. Upgrade control plane and nodes according to provider procedure.
7. Verify node readiness, workloads, DNS, storage, ingress, and telemetry.
8. Watch error rates and rollouts.
9. Keep a documented escalation and recovery plan.

Not every cluster upgrade can be rolled back. Treat upgrades as controlled changes, not a casual command.

## 4. Node maintenance

Cordoning a node prevents new Pods from being scheduled there. Draining attempts to evict eligible Pods so maintenance can proceed. Draining can be blocked by PDBs, local storage, or workload constraints.

```bash
kubectl cordon <node-name>
kubectl drain <node-name> --ignore-daemonsets
```

These commands can disrupt workloads. Use them only in an approved maintenance window after checking the target node, running Pods, PDBs, local storage, and capacity on other nodes. They are not lab cleanup commands.

After maintenance, an authorised operator may uncordon the node:

```bash
kubectl uncordon <node-name>
```

## 5. Cluster state and backup

Back up application data using service-appropriate mechanisms. Back up cluster resource configuration using a reviewed GitOps/IaC workflow. For self-managed clusters, protect control-plane data such as etcd according to the Kubernetes backup guidance.

A copy of YAML files alone may not recover application data stored in persistent volumes.

## Practice exercise

Write an upgrade runbook for a non-production cluster. Include version checks, deprecated APIs, add-on compatibility, PDB review, node capacity, verification commands, and post-upgrade smoke tests. Do not upgrade a production cluster as part of this exercise.

## Self-check

- Why do managed clusters still require upgrade planning?
- What should be checked before a Kubernetes version upgrade?
- What does cordon do?
- Why can drain be blocked by a PDB?
- Why is Git backup of YAML not enough for every recovery scenario?

## Official references

- Kubernetes releases: https://kubernetes.io/releases/
- Version skew policy: https://kubernetes.io/releases/version-skew-policy/
- Cluster upgrades: https://kubernetes.io/docs/tasks/administer-cluster/cluster-upgrade/
- kubectl drain: https://kubernetes.io/docs/reference/kubectl/generated/kubectl_drain/
