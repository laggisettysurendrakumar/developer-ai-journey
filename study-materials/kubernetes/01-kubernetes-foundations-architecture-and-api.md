# 01 — Kubernetes Foundations, Architecture and the API

## What Kubernetes solves

Running a single container with Docker is straightforward. Operating many services across several machines introduces more questions: where should each container run, how should failed containers recover, how should new versions be released, and how should services find each other?

Kubernetes is a container orchestration platform. You describe the desired state of your workloads, and Kubernetes controllers work to move the cluster toward that state.

Kubernetes does not make an application automatically reliable. Applications still need good health endpoints, sensible resource usage, secure configuration, observability, and resilient dependency handling.

## 1. Cluster architecture

A cluster has a control plane and worker nodes.

**Control plane** components manage the cluster:
- API server: the front door for Kubernetes API requests.
- Scheduler: selects a suitable node for an unscheduled Pod.
- Controller manager: runs controllers that reconcile desired and actual state.
- etcd: key-value store used for Kubernetes cluster data.

**Worker node** components run workloads:
- kubelet: ensures assigned Pods are running on the node.
- Container runtime: starts and manages containers.
- kube-proxy or an equivalent networking implementation: helps provide Service networking, depending on the cluster networking design.

Managed Kubernetes services may operate some or all control-plane components for you. The exact architecture depends on the provider and cluster configuration.

## 2. Kubernetes objects and declarative state

A Kubernetes object describes a desired resource. For example, a Deployment can declare that two replicas of an API should run. If one Pod disappears, the controller works to create a replacement.

This is called reconciliation: controllers repeatedly compare desired state with observed state and take actions to reduce the difference.

## 3. Kubernetes API and kubectl

`kubectl` is the command-line tool used to communicate with the Kubernetes API server.

First inspect your configuration and target:

```bash
kubectl version --client
kubectl config get-contexts
kubectl config current-context
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
```

Why these commands matter:
- `version --client` confirms the CLI version.
- `config get-contexts` lists known clusters and identities.
- `current-context` shows which context would receive commands.
- `cluster-info` confirms API endpoint reachability.
- `get nodes` shows worker node readiness.
- `get namespaces` lists logical resource groups.

Always check the context before a state-changing operation. A context may point to production even when your terminal is on a development laptop.

## 4. Namespaces and labels

Namespaces organise namespaced resources. Labels are key-value metadata used to select and group objects. Selectors are fundamental to Deployments, Services, NetworkPolicies, and many controllers.

Namespaces are not complete security boundaries by themselves. Use RBAC, network controls, quotas, and admission policies for actual isolation requirements.

## 5. YAML manifests

Most Kubernetes resources can be described in YAML. Common fields:
- `apiVersion`: API group/version for the object.
- `kind`: resource type.
- `metadata`: name, namespace, labels, annotations.
- `spec`: desired configuration.
- `status`: observed state written by Kubernetes.

The example is a namespace definition:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: learning
  labels:
    environment: lab
```

Apply it only to a cluster you are authorised to use:

```bash
kubectl apply -f namespace.yaml
kubectl get namespace learning
```

## 6. API resources and discovery

```bash
kubectl api-resources
kubectl explain deployment
kubectl explain deployment.spec.template.spec
kubectl get --raw /readyz
```

- `api-resources` lists resource kinds known to the API.
- `explain` describes fields and schema information.
- `/readyz` is an API server health endpoint; access and output depend on cluster permissions and configuration.

## Practice exercise

Create a local cluster using a tool such as kind or minikube. Inspect the context, nodes, namespaces, API resources, and a Deployment schema. Create a learning namespace and confirm it exists. Delete only that namespace after verifying the active context and its contents.

## Self-check

- What is the purpose of the API server?
- What does the scheduler do?
- What is reconciliation?
- What is the difference between a namespace and a label?
- Why is checking the current context an operational safety step?

## Official references

- Kubernetes architecture: https://kubernetes.io/docs/concepts/architecture/
- Kubernetes API: https://kubernetes.io/docs/concepts/overview/kubernetes-api/
- kubectl: https://kubernetes.io/docs/reference/kubectl/
- Namespaces: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
