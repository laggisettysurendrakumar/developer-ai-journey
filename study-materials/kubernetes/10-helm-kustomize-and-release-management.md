# 10 — Helm, Kustomize and Release Management

## Why manifests need organisation

As an application grows, raw YAML files can become repetitive across environments. Helm and Kustomize help manage related Kubernetes resources and environment differences.

## 1. Helm

Helm is a package manager for Kubernetes. A Helm chart bundles templates, default values, and metadata. A release is an installed instance of a chart.

Common commands:

```bash
helm version
helm repo list
helm list -n learning
helm show values <chart-reference>
helm template learning-release <chart-reference>
```

The template command renders manifests locally for inspection. Always review rendered resources before installing or upgrading.

Typical release operations include install, upgrade, history, rollback, and uninstall. These change cluster state and should follow an approved deployment process.

## 2. Values and templates

A chart can define defaults, while a values file supplies environment-specific values. Avoid storing plaintext secrets in committed values files. Use the organisation's secret-management integration.

## 3. Kustomize

Kustomize composes and patches Kubernetes YAML without requiring a template language. It supports a base plus overlays for environments such as development and production.

```bash
kubectl kustomize ./overlays/dev
kubectl diff -k ./overlays/dev
```

Inspect the rendered output and diff before applying.

## 4. Choose deliberately

Helm is useful for packaged applications with configurable values and release lifecycle. Kustomize is useful for overlaying declarative manifests and patches. Teams may use both, but keep the deployment strategy understandable.

## 5. GitOps introduction

GitOps uses Git as the declared source of desired cluster state. Controllers such as Flux or Argo CD observe repositories and reconcile cluster resources. Learn repository structure, sync policies, drift, reconciliation status, and safe rollback practices.

Do not assume GitOps means every change is automatically safe. Protect the repository, require reviews, and ensure emergency procedures are documented.

## Practice exercise

Package a simple API Deployment and Service with Helm or a Kustomize base/overlay. Render manifests and review the diff for dev and production-like settings. Deploy only to a lab cluster, then practise a controlled upgrade and rollback.

## Self-check

- What is a Helm chart?
- What is a Helm release?
- How does Kustomize manage environment differences?
- Why inspect rendered manifests before applying?
- What does a GitOps controller reconcile?

## Official references

- Helm documentation: https://helm.sh/docs/
- Kustomize: https://kustomize.io/
- Kubernetes Kustomize: https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/
- Flux: https://fluxcd.io/docs/
- Argo CD: https://argo-cd.readthedocs.io/
