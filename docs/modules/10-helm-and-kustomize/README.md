# Module 10 – Helm & Kustomize

## Goal

After this module you can install Helm Charts, create your own charts, and understand Kustomize as an alternative approach. You know when to use which tool.

## Why does this matter?

Real applications consist of many YAML files. When you have 10 Deployments, 10 Services, and 10 ConfigMaps, you don't want to manually edit each file for every environment. Helm and Kustomize solve this problem in different ways.

## Key Concepts

### Helm

- **Chart:** A package containing all Kubernetes resources for an application — templates, default values, and metadata.
- **Release:** An installed instance of a Chart in a cluster.
- **Values:** Configuration that overrides template variables. Enables the same Chart to be used across multiple environments.
- **Repository:** A collection of Helm Charts (e.g., Artifact Hub).
- **Chart.yaml:** Metadata file for a Chart (name, version, description).

### Kustomize

- **Base:** Base YAML files that apply to all environments.
- **Overlay:** Environment-specific changes (patches) applied on top of the base.
- **kustomization.yaml:** Controls what is merged and which patches are applied.

## Hands-On Task: Helm

### Install Helm

```bash
# macOS
brew install helm
helm version
```

### Install a Chart from a repository

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm search repo nginx

helm install my-nginx bitnami/nginx --namespace demo --create-namespace
helm list -A
helm status my-nginx -n demo
```

### Working with values

```bash
# View default values
helm show values bitnami/nginx

# Override at install time
helm install my-nginx bitnami/nginx \
  --set replicaCount=2 \
  --set service.type=NodePort

# Use a values file
helm install my-nginx bitnami/nginx -f my-values.yaml
```

### Upgrade and rollback

```bash
helm upgrade my-nginx bitnami/nginx --set replicaCount=3
helm rollback my-nginx 1
helm uninstall my-nginx -n demo
```

## Hands-On Task: Kustomize

```yaml
# base/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- deployment.yaml
- service.yaml
```

```yaml
# overlays/prod/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
bases:
- ../../base
patches:
- patch: |-
    - op: replace
      path: /spec/replicas
      value: 5
  target:
    kind: Deployment
    name: my-app
```

```bash
kubectl apply -k overlays/prod/
kubectl kustomize overlays/prod/   # preview without applying
```

## Helm vs. Kustomize

| Feature | Helm | Kustomize |
|---------|------|-----------|
| Templating | Go templates with values | Patch-based, no templating |
| Packages | Charts from repositories | No packages |
| Complexity | Higher | Lower |
| Kubernetes-native | External tool | Built into kubectl |
| Best for | Deploying external software | Configuring your own apps |

## Common Mistakes

- **helm uninstall doesn't clean up everything:** Usually it does. Verify with `kubectl get all`. Some CRDs may remain.
- **Values not applied:** Wrong path in `--set` or syntax error in values file.
- **Kustomize patch target not matching:** The patch finds no resource. Check name/kind in the `target` field.

## Checkpoint

- [ ] What is a Helm Chart and what does it contain?
- [ ] How do you install a specific Chart version with custom values?
- [ ] What is the difference between `helm install` and `helm upgrade`?
- [ ] What does Kustomize do differently than Helm?
- [ ] When would you prefer Helm over Kustomize?

## Definition of Done

You are done with this module when you:

- [ ] Have installed a Chart from a Helm repository and configured it with `--set`
- [ ] Have created and installed your own Helm Chart
- [ ] Have performed a Helm upgrade and rollback
- [ ] Can explain the difference between Helm and Kustomize and when to use each
- [ ] Can answer all checkpoint questions

## Further Reading

- [Helm Documentation](https://helm.sh/docs/)
- [Artifact Hub](https://artifacthub.io/)
- [Kustomize Documentation](https://kustomize.io/)
- [Kustomize in kubectl](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
