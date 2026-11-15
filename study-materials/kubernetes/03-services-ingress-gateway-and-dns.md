# 03 — Services, Ingress, Gateway and DNS

## Why Services exist

Pods can be replaced and their IP addresses can change. A Service provides a stable virtual endpoint for a group of Pods selected by labels. Clients connect to the Service rather than tracking individual Pod IPs.

## 1. ClusterIP Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
  namespace: learning
spec:
  selector:
    app: web
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
```

- `selector` finds matching Pods.
- `port` is the Service port.
- `targetPort` is the port on the selected Pod.
- ClusterIP exposes the Service inside the cluster.

Apply and inspect:

```bash
kubectl apply -f service.yaml
kubectl get service,endpointslices -n learning
kubectl describe service web -n learning
```

If no endpoints are present, check whether the selector matches Pod labels and whether the Pods are ready.

## 2. Service types

- ClusterIP: internal cluster access.
- NodePort: exposes a port on nodes, subject to cluster networking and firewall configuration.
- LoadBalancer: requests an external or internal load balancer from a supported environment.
- ExternalName: maps a Service name to an external DNS name.

A LoadBalancer may create billable cloud infrastructure. Do not create one in a shared subscription without approval.

## 3. DNS and service discovery

Kubernetes DNS provides service-name resolution. A Pod in the same namespace can usually reach a Service by its short name; cross-namespace access can use a namespace-qualified DNS name.

Troubleshoot with a temporary diagnostic container approved for your environment. Do not assume every application image contains DNS tools.

## 4. Ingress

Ingress defines HTTP/HTTPS routing rules from outside the cluster to Services. An Ingress resource requires an Ingress controller to implement those rules. Creating only the YAML resource does not install the controller.

Typical capabilities include host-based routing, path routing, and TLS termination, depending on the implementation.

## 5. Gateway API

Gateway API provides a more expressive role-oriented model for traffic management, including GatewayClass, Gateway, and HTTPRoute resources. Support and maturity of features depend on the selected controller and version.

Understand both Ingress and Gateway API because organisations may use either depending on platform standards and controller support.

## 6. Network path

Draw the request path:

```text
Client
  -> DNS
  -> Load balancer / Gateway / Ingress
  -> Service
  -> Ready Pod
  -> Application listener
```

Failures can occur at every hop. Test DNS, TLS, routing, Service selectors, endpoint readiness, container port, and application listener separately.

## Practice exercise

Expose a two-replica web Deployment internally with a ClusterIP Service. Test it from a temporary Pod. Then change the Service selector so it matches no Pods and observe how endpoints change. Restore the selector and document the troubleshooting sequence.

## Self-check

- Why does a Service provide stability for changing Pods?
- What is the difference between port and targetPort?
- Why does creating an Ingress object alone not guarantee external access?
- What does Kubernetes DNS resolve?
- How would you narrow down a request that reaches the load balancer but not the Pod?

## Official references

- Services: https://kubernetes.io/docs/concepts/services-networking/service/
- DNS for Services and Pods: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- Ingress: https://kubernetes.io/docs/concepts/services-networking/ingress/
- Gateway API: https://gateway-api.sigs.k8s.io/
