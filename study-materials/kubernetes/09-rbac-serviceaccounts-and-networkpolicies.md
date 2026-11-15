# 09 — RBAC, ServiceAccounts and NetworkPolicies

## Why cluster access must be controlled

Kubernetes exposes powerful APIs. A user or workload with excessive permissions can read secrets, change Deployments, create privileged Pods, or disrupt cluster resources.

Use least privilege for both human operators and applications.

## 1. Authentication and authorisation

Authentication identifies the caller. Authorisation determines whether the caller may perform an API action on a resource.

Kubernetes commonly uses external identity integrations for human access and ServiceAccounts for workloads. Managed clusters may integrate with a cloud identity provider.

## 2. RBAC objects

- Role: permissions within one namespace.
- ClusterRole: reusable permissions that can apply cluster-wide or be bound within a namespace.
- RoleBinding: grants a Role or ClusterRole to subjects in one namespace.
- ClusterRoleBinding: grants a ClusterRole at cluster scope.

Permissions are additive. Avoid wildcards and broad cluster-admin assignments for ordinary applications.

## 3. ServiceAccounts

A Pod can use a ServiceAccount identity to access the Kubernetes API. Disable automatic token mounting for workloads that do not need Kubernetes API access. Where access is needed, grant narrow permissions and use short-lived projected tokens as supported.

## 4. Test permissions safely

```bash
kubectl auth can-i get pods -n learning
kubectl auth can-i create deployments -n learning
kubectl get serviceaccounts -n learning
kubectl get role,rolebinding -n learning
```

These commands inspect the permissions of the current identity. They do not grant permissions.

## 5. NetworkPolicy

A NetworkPolicy can restrict Pod ingress and egress traffic when the cluster network plugin enforces it. Policies use Pod selectors, namespaces, and IP blocks.

Important: a NetworkPolicy object may have no effect if the CNI plugin does not implement NetworkPolicy. Check your cluster's network implementation.

## 6. Security controls beyond RBAC

- Pod Security Standards/admission controls.
- Image provenance and vulnerability scanning.
- Non-root containers and capability reduction.
- Read-only root filesystem when practical.
- Secret encryption and external secret management.
- Audit logging.
- API server access control.
- Network segmentation.

## Practice exercise

Create a namespace and a limited ServiceAccount for a lab workload. Grant read-only access to Pods in that namespace using a Role and RoleBinding. Verify permitted and forbidden operations with `kubectl auth can-i`. Delete only lab resources.

## Self-check

- What is the difference between Role and ClusterRole?
- Why should application Pods use dedicated ServiceAccounts?
- What does `kubectl auth can-i` show?
- Why might a NetworkPolicy fail to enforce traffic restrictions?
- Why is cluster-admin not suitable for a routine application identity?

## Official references

- RBAC: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
- ServiceAccounts: https://kubernetes.io/docs/concepts/security/service-accounts/
- NetworkPolicy: https://kubernetes.io/docs/concepts/services-networking/network-policies/
- Pod Security Standards: https://kubernetes.io/docs/concepts/security/pod-security-standards/
