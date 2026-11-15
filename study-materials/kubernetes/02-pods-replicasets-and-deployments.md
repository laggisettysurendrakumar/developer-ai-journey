# 02 — Pods, ReplicaSets and Deployments

## Start with the Pod

A Pod is the smallest deployable unit in Kubernetes. It contains one or more closely related containers that share a network identity and can share configured storage.

Most application workloads should be created through controllers such as Deployments rather than as standalone Pods. A Deployment helps keep the desired number of replicas running and supports controlled updates.

## 1. A Pod manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-lab
  namespace: learning
  labels:
    app: web-lab
spec:
  containers:
    - name: web
      image: nginx:1.27
      ports:
        - containerPort: 80
```

Apply and inspect in a lab namespace:

```bash
kubectl apply -f pod.yaml
kubectl get pods -n learning -o wide
kubectl describe pod web-lab -n learning
kubectl delete pod web-lab -n learning
```

A Pod is replaceable. Its IP and local ephemeral state may change after recreation.

## 2. Why Deployments are preferred

A Deployment manages a ReplicaSet; the ReplicaSet maintains the requested number of matching Pods. This gives a declarative way to define replica count, selectors, Pod template, and rollout strategy.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: learning
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.27
          ports:
            - containerPort: 80
```

The selector must match the labels on the Pod template. Kubernetes uses that relationship to identify the Pods managed by the Deployment.

## 3. Apply and inspect

```bash
kubectl apply -f deployment.yaml
kubectl get deployments,replicasets,pods -n learning
kubectl describe deployment web -n learning
kubectl get pods -n learning --show-labels
```

## 4. Scale a Deployment

```bash
kubectl scale deployment/web --replicas=3 -n learning
kubectl get pods -n learning
```

Scaling changes the desired replica count. It does not guarantee that nodes have enough resources to schedule every Pod. Inspect Pending Pods and node capacity if the requested replicas do not become Ready.

## 5. Update an image

```bash
kubectl set image deployment/web nginx=nginx:1.28 -n learning
kubectl rollout status deployment/web -n learning
kubectl rollout history deployment/web -n learning
```

Use a reviewed version tag and test it in a controlled environment. For production, deploy a known image digest or immutable version according to release policy.

## 6. Roll back

```bash
kubectl rollout undo deployment/web -n learning
kubectl rollout status deployment/web -n learning
```

Rollback reverts the Deployment's Pod template to a prior revision where available. It does not reverse database migrations, external side effects, or changes to unrelated resources. Confirm that the previous application version is compatible with the current data.

## Practice exercise

Deploy two Nginx replicas. Change the image, watch the rollout, inspect ReplicaSets, and perform a lab rollback. Delete one Pod and observe how the ReplicaSet restores the desired count.

## Self-check

- Why is a standalone Pod not usually the right long-running application resource?
- What does a ReplicaSet maintain?
- Why must selector labels match the Pod template?
- What happens when you scale replicas beyond available capacity?
- What does a Deployment rollback not undo?

## Official references

- Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- ReplicaSets: https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
