# 13 — Production Support Playbook: Triage and Incident Management

## Purpose

Production support is not about running random commands until a service becomes healthy. It is a controlled process: confirm impact, gather evidence, identify the failing layer, apply the smallest safe mitigation, verify recovery, and document what happened.

This lesson provides a reusable operational workflow. Adapt it to your organisation's incident process, access controls, and change policy.

## 1. Before running a command

Start with these questions:
1. Which environment and cluster are affected?
2. Which namespace and application are affected?
3. What is the incident start time and user impact?
4. Was there a recent deployment, configuration change, node event, or dependency outage?
5. Is the incident still active, and who is coordinating it?

Confirm context and permissions:

```bash
kubectl config current-context
kubectl config get-contexts
kubectl auth can-i get pods -n <namespace>
kubectl get nodes
```

Do not change context casually during an incident. Use the approved production access process and record the target.

## 2. First five minutes: read-only triage

```bash
kubectl get pods -n <namespace> -o wide
kubectl get deployments,replicasets,services -n <namespace>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
kubectl get endpointslices -n <namespace>
kubectl top pods -n <namespace>
```

These commands provide a fast overview of workload state, placement, rollout, endpoint readiness, recent events, and resource usage. Metrics may be unavailable if the cluster has no metrics pipeline.

Capture timestamps and resource names. Avoid dumping Secrets or full environment values into incident channels.

## 3. Common incident patterns

### A. Pods in CrashLoopBackOff

Likely reasons include an application exception, missing configuration, failed dependency connection, invalid startup command, failed probe, or memory limit.

```bash
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> --previous
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

Check exit code, termination reason, previous logs, probe failures, mounts, and recent configuration/deployment changes. If multiple containers exist, specify `-c`.

### B. Pods in Pending

Likely reasons include insufficient node capacity, taints without tolerations, affinity constraints, quota, unbound PVC, or unavailable nodes.

```bash
kubectl describe pod <pod> -n <namespace>
kubectl get nodes
kubectl describe nodes
kubectl get pvc -n <namespace>
kubectl get resourcequota -n <namespace>
```

Read scheduler events before changing requests, node selectors, or quotas. Do not simply increase cluster size without identifying the constraint and checking cost/approval.

### C. ImagePullBackOff / ErrImagePull

Check image name/tag/digest, registry reachability, registry pull permissions, imagePullSecrets if used, and architecture compatibility.

```bash
kubectl describe pod <pod> -n <namespace>
kubectl get serviceaccount -n <namespace>
```

Avoid printing registry credentials. Validate the image reference against the release record.

### D. Service is unavailable

Check:
- Service selector matches Pod labels.
- Pods are Ready.
- EndpointSlices contain expected addresses.
- Service port and targetPort are correct.
- NetworkPolicy, DNS, ingress/gateway, and cloud firewall allow traffic.
- Application listens on the expected interface and port.

```bash
kubectl get svc,endpointslices -n <namespace>
kubectl describe svc <service> -n <namespace>
kubectl get pods -n <namespace> --show-labels
```

### E. OOMKilled or memory pressure

Check previous termination reason, container limits, working set metrics, node memory pressure, and recent release changes.

```bash
kubectl describe pod <pod> -n <namespace>
kubectl top pod <pod> -n <namespace>
kubectl describe node <node>
```

Do not raise memory limits blindly. Check node capacity and whether the process has a memory leak or workload spike.

### F. High CPU / latency

Check CPU throttling, requests/limits, replica count, application latency, downstream dependencies, queue depth, and node saturation. CPU usage alone does not establish the root cause.

### G. PVC Pending / mount failure

Inspect PVC/PV/StorageClass and Pod events. Check provisioner, access mode, zone, capacity, attachment limits, and CSI controller/node plugin health. Never delete a production PVC as a troubleshooting shortcut.

## 4. Deployment incident

When a new release causes errors:
1. Confirm the affected Deployment and release version.
2. Check rollout status and history.
3. Compare error rate and latency before/after deployment.
4. Inspect ReplicaSets and Pod events.
5. Check configuration and secret changes.
6. Decide with the incident/change owner whether rollback is appropriate.
7. Verify service recovery and data compatibility.

```bash
kubectl rollout status deployment/<deployment> -n <namespace>
kubectl rollout history deployment/<deployment> -n <namespace>
kubectl get replicasets -n <namespace>
```

A rollback is not always safe. Consider schema migrations, queued messages, external side effects, configuration compatibility, and whether the previous image is still available.

## 5. Change safety levels

**Read-only diagnostics** usually include get, describe, logs, top, and auth can-i.

**State-changing operations** include apply, delete, scale, patch, rollout restart/undo, cordon, drain, and exec (which can alter a container). Use change approvals and least-privilege access.

Before any corrective command:
- Confirm cluster context and namespace.
- Identify exact object name and owner.
- Understand blast radius.
- Check redundancy and capacity.
- Check PDB, HPA, and rollout state where relevant.
- Record current state and the planned change.
- Obtain required approval.
- Define verification and rollback/mitigation.

Avoid broad commands such as deleting all Pods in a namespace, deleting PVCs, or restarting every Deployment.

## 6. Incident communications

Record:
- Start time and detection source.
- User/business impact.
- Affected services and regions.
- Evidence and timeline.
- Mitigation applied and approval.
- Recovery verification.
- Follow-up owner and due date.

Send short updates at the cadence required by the incident process. Separate confirmed facts from hypotheses.

## 7. After recovery

A post-incident review should be blameless and evidence-based. Capture:
- What happened?
- How was it detected?
- What was the impact?
- What made diagnosis or recovery slow?
- Which controls worked?
- What should change in monitoring, automation, design, or documentation?
- Who owns each corrective action?

## Practice exercise

Use a local cluster to simulate a failed image, crash-looping container, readiness failure, and Service selector mismatch. Follow the read-only triage flow for each. Write an incident ticket with impact, evidence, root cause, mitigation, and prevention. Do not practise destructive actions on a production cluster.

## Self-check

- What are the first checks before using kubectl in production?
- Which commands help diagnose CrashLoopBackOff?
- How would you distinguish Pending caused by capacity from Pending caused by storage?
- What risks should be considered before rolling back?
- What information belongs in a post-incident review?

## Official references

- Debugging Pods: https://kubernetes.io/docs/tasks/debug/debug-application/
- Debugging Services: https://kubernetes.io/docs/tasks/debug/debug-application/debug-service/
- Troubleshooting clusters: https://kubernetes.io/docs/tasks/debug/debug-cluster/
- kubectl reference: https://kubernetes.io/docs/reference/kubectl/
