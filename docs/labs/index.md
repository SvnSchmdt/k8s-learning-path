# Labs

8 hands-on labs that turn module knowledge into practice. Each lab runs locally with [kind](https://kind.sigs.k8s.io/) and walks you through a complete, verifiable scenario.

---

## Lab overview

| Lab | Title | Goal | Duration | Difficulty |
|-----|-------|------|----------|------------|
| [00](00-local-cluster-kind/README.md) | Local Cluster with kind | Set up a working local Kubernetes cluster | ~20 min | Beginner |
| [01](01-nginx-deployment/README.md) | nginx Deployment | Deploy, scale, and roll back a Deployment | ~30 min | Beginner |
| [02](02-service-networking/README.md) | Service & Networking | Expose Pods via ClusterIP and NodePort | ~30 min | Beginner |
| [03](03-config-and-secrets/README.md) | Config & Secrets | Inject configuration via ConfigMaps and Secrets | ~30 min | Beginner |
| [04](04-ingress/README.md) | Ingress | Route HTTP traffic with nginx Ingress Controller | ~45 min | Intermediate |
| [05](05-helm-chart/README.md) | Helm Chart | Create, install, upgrade, and roll back a Helm chart | ~45 min | Intermediate |
| [06](06-monitoring/README.md) | Monitoring & Probes | Configure health probes, use kubectl top, observe events | ~45 min | Intermediate |
| [07](07-gitops-argocd/README.md) | GitOps with Argo CD | Deploy with Argo CD, observe GitOps sync behavior | ~60 min | Advanced |

---

## Prerequisites for all labs

- [kind](https://kind.sigs.k8s.io/) installed and running
- `kubectl` installed and configured
- Docker or Podman running locally
- Helm v3 (for Lab 05+)

Start with **[Lab 00](00-local-cluster-kind/README.md)** if you haven't set up your cluster yet.

---

## Lab structure

Each lab includes:

- **Was du baust** — What you will build, with an ASCII architecture diagram
- **Schritt-für-Schritt** — Step-by-step instructions with expected command output
- **Validierung** — How to verify everything works correctly
- **Cleanup** — How to remove all resources after the lab
- **Weiterführende Schritte** — What to explore next
