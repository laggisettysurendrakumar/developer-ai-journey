# Kubernetes for AI Application Workloads — Beginner Course

I use Kubernetes when an application needs a managed way to run containers across a cluster of machines. It can help with rollouts, scaling, service discovery, and recovery, but it adds operational complexity. A small application does not automatically need Kubernetes; start with Docker and choose a cluster only when requirements justify it.

## Prerequisites and learning order

Learn Linux, Docker, HTTP health endpoints, environment variables, YAML, and container networking first.

1. Understand cluster, control plane, node, namespace, and kubectl.
2. Deploy a basic Pod and inspect it.
3. Use a Deployment to manage replicated Pods.
4. Expose an application using a Service; learn Ingress/Gateway concepts.
5. Configure applications with ConfigMaps and Secrets.
6. Add startup, readiness, and liveness probes.
7. Understand resource requests, limits, and autoscaling.
8. Troubleshoot events, logs, rollouts, and failed Pods.
9. Learn RBAC, service accounts, and network boundaries.
10. Deploy a small AI API and worker to a local cluster.

## 1. Kubernetes in plain English

Kubernetes is a platform for managing containerised applications. Instead of manually starting each container, I describe the desired state, such as running two copies of an API image. Kubernetes controllers work to keep the actual cluster close to that desired state.

| Term | Plain-English meaning |
|---|---|
| Cluster | Group of machines managed by Kubernetes |
| Control plane | Components that make cluster-wide decisions and maintain state |
| Node | Machine that runs workloads |
| Pod | Smallest deployable unit; contains one or more related containers |
| Deployment | Declares and manages a desired number of replicated Pods |
| Service | Stable network endpoint for a set of Pods |
| Namespace | Logical grouping of cluster resources |
| kubectl | Command-line tool to communicate with the Kubernetes API |

## 2. Check the cluster before doing anything

```bash
kubectl config current-context
kubectl get nodes
kubectl get namespaces
kubectl get pods -A
```

**What it does:** Shows the current cluster context, nodes, namespaces, and Pods across namespaces.

**Why useful?** The same kubectl command can affect different clusters depending on the current context. Always confirm the target first.

**Use case:** Prevent an accidental deployment to the wrong cluster.

## 3. Pod and Deployment

A Pod wraps one or more containers that share a network identity and can share storage. Most application workloads should be managed by a Deployment rather than by creating standalone Pods.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: assistant-api
  namespace: learning
spec:
  replicas: 2
  selector:
    matchLabels:
      app: assistant-api
  template:
    metadata:
      labels:
        app: assistant-api
    spec:
      containers:
        - name: api
          image: nginx:1.27
          ports:
            - containerPort: 80
```

This is a learning example using an official sample image, not a GenAI API. Replace the image with a reviewed application image when practising your own service.

- replicas asks Kubernetes to keep two Pod copies.
- selector identifies the Pods belonging to this Deployment.
- template describes each Pod.
- containerPort documents the container's listening port.

Apply only in a disposable/local cluster:

```bash
kubectl create namespace learning
kubectl apply -f deployment.yaml
kubectl get deployments,pods -n learning
```

Remove the learning resources when finished:

```bash
kubectl delete namespace learning
```

Deletion removes all namespaced resources in that lab namespace. Confirm the current context and namespace before running it.

## 4. Service: stable access to Pods

Pod IP addresses can change when Pods are replaced. A Service gives a stable virtual endpoint and selects Pods using labels.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: assistant-api
  namespace: learning
spec:
  selector:
    app: assistant-api
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
```

ClusterIP exposes the Service inside the cluster. Other options such as LoadBalancer expose it differently and may create cloud resources and costs.

## 5. ConfigMap and Secret

A ConfigMap stores non-secret configuration. A Secret is intended for sensitive values, but it is not automatically safe merely because it is a Secret object. Configure encryption at rest, RBAC, access restrictions, and secure delivery.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: assistant-settings
  namespace: learning
data:
  APP_ENV: "development"
  LOG_LEVEL: "INFO"
```

Do not put API keys into a committed YAML file. Use a secret manager or the cluster's approved secret-injection integration. Avoid displaying secret values in terminal output or logs.

## 6. Probes: started, ready, and healthy

- Startup probe gives a slow-starting application time to initialise.
- Readiness probe decides whether the Pod should receive traffic.
- Liveness probe detects a process that is stuck and may need restart.

```yaml
startupProbe:
  httpGet:
    path: /health/startup
    port: 8000
  failureThreshold: 30
  periodSeconds: 5
readinessProbe:
  httpGet:
    path: /health/ready
    port: 8000
  periodSeconds: 10
livenessProbe:
  httpGet:
    path: /health/live
    port: 8000
  periodSeconds: 20
```

These paths must exist and should be designed carefully. A readiness failure removes a Pod from service endpoints; a liveness failure can cause restarts. Do not make liveness depend on every remote service, or a temporary dependency failure can restart all application Pods.

## 7. Requests, limits, and autoscaling

Resource requests help the scheduler place workloads. Limits cap resource use where supported. CPU limits can throttle; memory limits can cause the container to be terminated when it exceeds the limit.

```yaml
resources:
  requests:
    cpu: 250m
    memory: 512Mi
  limits:
    cpu: "1"
    memory: 1Gi
```

Values above are examples only. Measure real application usage. AI workloads can have large memory needs, model downloads, GPU requirements, and bursts in network calls. Autoscaling needs meaningful metrics and enough downstream capacity.

## 8. Troubleshoot a workload

```bash
kubectl get pods -n learning -o wide
kubectl describe pod <pod-name> -n learning
kubectl logs <pod-name> -n learning --since=15m
kubectl get events -n learning --sort-by=.lastTimestamp
kubectl rollout status deployment/assistant-api -n learning
```

Common symptoms:
- Pending: scheduling, resource, node, or volume issue.
- ImagePullBackOff: image name, registry access, or pull credentials.
- CrashLoopBackOff: container repeatedly exits; inspect current and previous logs.
- Not Ready: readiness probe, dependencies, or app initialisation issue.

Use the previous-container log option when a container has crashed. Redact secrets from diagnostic output.

## 9. Identity and security basics

- Use a dedicated ServiceAccount for each workload where appropriate.
- Apply least-privilege RBAC.
- Avoid cluster-admin for application Pods.
- Restrict network paths with appropriate NetworkPolicies and cloud controls.
- Scan images and run as non-root.
- Keep secrets outside source control.
- Check context and namespace before every state-changing command.

Commands such as apply, delete, exec, scale, patch, and rollout restart can affect running workloads. Use them only in your lab or through an approved operational process.

## 10. Practice project

Deploy the containerised Developer Knowledge Assistant to a local cluster:
1. Create a dedicated learning namespace.
2. Deploy the API image with two replicas.
3. Expose it internally with a ClusterIP Service.
4. Supply non-secret configuration with a ConfigMap.
5. Add secret integration without committing secret values.
6. Implement startup, readiness, and liveness endpoints.
7. Set measured resource requests and limits.
8. Test rollout, Pod failure, and logs.
9. Document how persistent vector data would be stored and backed up.

## Self-check

1. What is the difference between a Pod and a Deployment?
2. Why does a Service exist when Pods already have IP addresses?
3. How are readiness and liveness probes different?
4. Why is a Secret not automatically a complete secret-management solution?
5. Why should an AI API have resource limits and concurrency controls?

## References

- Kubernetes concepts: https://kubernetes.io/docs/concepts/
- kubectl reference: https://kubernetes.io/docs/reference/kubectl/
- Probes: https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
