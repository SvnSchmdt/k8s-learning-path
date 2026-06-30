# Beispiele

Fertige Kubernetes YAML-Manifeste. Kopiere, passe an und wende sie als Ausgangspunkt für eigene Workloads an.

---

> **Wichtig:** Die Datei `secret.example.yaml` enthält **ausschließlich Dummy-Werte**. Committe niemals echte Secrets, Passwörter oder Tokens in ein Repository. Ersetze Platzhalter-Werte vor der Verwendung und speichere echte Secrets in einem Secrets Manager.

---

## Verfügbare Manifeste

| Datei | Kind | Beschreibung |
|-------|------|-------------|
| [pod.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/pod.yaml) | Pod | Minimaler nginx Pod |
| [deployment.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/deployment.yaml) | Deployment | nginx Deployment mit 3 Replicas |
| [service-clusterip.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/service-clusterip.yaml) | Service | ClusterIP Service für internen Zugriff |
| [service-nodeport.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/service-nodeport.yaml) | Service | NodePort Service für externen Zugriff |
| [ingress.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/ingress.yaml) | Ingress | HTTP-Routing mit nginx Ingress |
| [configmap.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/configmap.yaml) | ConfigMap | Anwendungskonfiguration |
| [secret.example.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/secret.example.yaml) | Secret | **Nur Dummy-Werte** – Template für Secret-Struktur |
| [pvc.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/pvc.yaml) | PersistentVolumeClaim | 1Gi Storage-Anforderung |
| [serviceaccount.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/serviceaccount.yaml) | ServiceAccount | Benannter Service Account |
| [role.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/role.yaml) | Role | RBAC Role mit Pod-Leseberechtigungen |
| [rolebinding.yaml](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/docs/examples/rolebinding.yaml) | RoleBinding | Bindet die Role an einen Service Account |

---

## Verwendung

```bash
# Einzelnes Beispiel anwenden
kubectl apply -f docs/examples/deployment.yaml

# Syntax prüfen ohne anzuwenden
kubectl apply --dry-run=client -f docs/examples/deployment.yaml
```
