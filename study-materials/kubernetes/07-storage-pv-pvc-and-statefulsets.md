# 07 — Kubernetes Storage, PV, PVC and StatefulSets

## Why storage is different for containers

Pods are replaceable. Data written only into a container's writable layer may disappear when that container or Pod is replaced. Stateful applications need a storage design independent of the Pod's temporary filesystem.

## 1. Volumes

A Pod can mount a volume into one or more containers. Volume types have different lifecycles and storage characteristics. Examples include ephemeral volumes, projected configuration, and persistent storage.

## 2. PersistentVolume and PersistentVolumeClaim

- PersistentVolume (PV): a storage resource available to the cluster.
- PersistentVolumeClaim (PVC): a workload's request for storage.
- StorageClass: describes dynamic provisioning behaviour for a class of storage.

A PVC asks for capacity and access modes. A provisioner may create a matching PV automatically, depending on the StorageClass and environment.

Example claim:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data
  namespace: learning
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

This is a learning example. Supported access modes, provisioner, volume binding, and storage class depend on the cluster.

## 3. StatefulSet

A StatefulSet manages Pods that need stable identities and storage associations. It is often used for stateful systems that require stable network identity or ordered operations.

StatefulSet does not automatically make a database highly available. Database replication, backups, failover, consistency, and recovery are separate responsibilities.

## 4. Storage troubleshooting

```bash
kubectl get pvc -n learning
kubectl describe pvc app-data -n learning
kubectl get pv
kubectl get storageclass
kubectl describe pod <pod-name> -n learning
```

Common causes of Pending PVC:
- No default or matching StorageClass.
- Unsupported access mode.
- Insufficient capacity.
- Provisioner or cloud storage failure.
- Node/zone topology constraints.

## 5. Backup and recovery

A PVC is not a backup. Define backup schedules, retention, encryption, access controls, restore testing, and recovery objectives. Check whether snapshots are supported and whether the application is consistent during backup.

For critical databases, evaluate managed database services when they reduce operational risk compared with running the database yourself inside a cluster.

## Practice exercise

In a local cluster that supports dynamic provisioning, create a PVC and mount it to a Pod. Write a file, replace the Pod, and verify the file remains. Delete only the lab PVC after understanding its reclaim policy and data impact.

## Self-check

- What is the difference between a PV and PVC?
- What does a StorageClass define?
- Why might a PVC remain Pending?
- Does a StatefulSet automatically make a database highly available?
- Why is persistent storage not the same as backup?

## Official references

- Volumes: https://kubernetes.io/docs/concepts/storage/volumes/
- Persistent Volumes: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
- StatefulSets: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
