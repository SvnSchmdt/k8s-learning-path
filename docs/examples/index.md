# Examples

Ready-to-use Kubernetes YAML manifests. Copy, modify, and apply these as a starting point for your own workloads.

---

> **Warning:** The file `secret.example.yaml` contains **dummy values only**. Never commit real secrets, passwords, or tokens to a repository. Replace placeholder values before use, and store real secrets in a secrets manager.

---

## Available manifests

| File | Kind | Description |
|------|------|-------------|
| [pod.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/pod.yaml) | Pod | Minimal nginx Pod |
| [deployment.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/deployment.yaml) | Deployment | nginx Deployment with 3 replicas |
| [service-clusterip.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/service-clusterip.yaml) | Service | ClusterIP service for internal access |
| [service-nodeport.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/service-nodeport.yaml) | Service | NodePort service for external access |
| [ingress.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/ingress.yaml) | Ingress | HTTP routing with nginx Ingress |
| [configmap.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/configmap.yaml) | ConfigMap | Application configuration |
| [secret.example.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/secret.example.yaml) | Secret | **Dummy values only** — template for secret structure |
| [pvc.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/pvc.yaml) | PersistentVolumeClaim | 1Gi storage claim |
| [serviceaccount.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/serviceaccount.yaml) | ServiceAccount | Named service account |
| [role.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/role.yaml) | Role | RBAC role with pod read permissions |
| [rolebinding.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/rolebinding.yaml) | RoleBinding | Binds the role to a service account |

---

## Usage

```bash
# Apply a single example
kubectl apply -f docs/examples/deployment.yaml

# Dry-run to check syntax
kubectl apply --dry-run=client -f docs/examples/deployment.yaml

# View the manifest
cat docs/examples/deployment.yaml
```
