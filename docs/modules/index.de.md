# Module

16 strukturierte Lernmodule, die dich von null zu produktionsreifem Kubernetes-Wissen führen. Arbeite sie der Reihe nach durch – jedes Modul baut auf dem vorherigen auf.

---

## Modulübersicht

| Modul | Titel | Was du lernst |
|-------|-------|---------------|
| [00](00-prerequisites/README.de.md) | Voraussetzungen | Docker, kind, kubectl Setup; lokale Umgebung verifizieren |
| [01](01-container-basics/README.de.md) | Container-Grundlagen | Images, Container, Dockerfile, Registry-Workflow |
| [02](02-kubernetes-fundamentals/README.de.md) | Kubernetes-Grundlagen | Control Plane, Nodes, deklarativ vs. imperativ, Pod-Lifecycle |
| [03](03-kubectl-and-cluster-access/README.de.md) | kubectl & Cluster-Zugriff | kubeconfig, Contexts, Ressourcentypen, Output-Formate, jsonpath |
| [04](04-pods-and-workloads/README.de.md) | Pods & Workloads | Pod Spec, Deployments, ReplicaSets, Rolling Updates, Rollbacks |
| [05](05-services-and-networking/README.de.md) | Services & Networking | ClusterIP, NodePort, LoadBalancer, DNS, Endpoints |
| [06](06-ingress-and-dns/README.de.md) | Ingress & DNS | Ingress Resource, Ingress Controller, Host/Path-Routing, TLS Termination |
| [07](07-configmaps-secrets-and-env/README.de.md) | ConfigMaps, Secrets & Env | Konfigurationsmanagement, Umgebungsvariablen, Volume Mounts |
| [08](08-storage/README.de.md) | Storage | Volumes, PersistentVolumes, PersistentVolumeClaims, StorageClasses |
| [09](09-rbac-and-security-basics/README.de.md) | RBAC & Security | Roles, ClusterRoles, RoleBindings, ServiceAccounts, SecurityContext |
| [10](10-helm-and-kustomize/README.de.md) | Helm & Kustomize | Charts, Releases, Values, Upgrades, Rollbacks, Kustomize Overlays |
| [11](11-observability/README.de.md) | Observability | Health Probes, kubectl top, Logs, Events, Prometheus, Grafana |
| [12](12-troubleshooting/README.de.md) | Troubleshooting | Systematischer Debugging-Workflow, häufige Fehlermuster |
| [13](13-gitops-with-argocd/README.de.md) | GitOps mit Argo CD | GitOps-Prinzipien, Argo CD Setup, Application CRD, Sync-Workflow |
| [14](14-production-readiness/README.de.md) | Production Readiness | Resource Requests/Limits, HPA, PodDisruptionBudget, Probes |
| [15](15-next-steps-cka-and-homelab/README.de.md) | Nächste Schritte: CKA & Homelab | CKA/CKAD/CKS-Vorbereitung, Homelab mit k3s/kubeadm, Operators |

---

## Modulstruktur

Jedes Modul folgt dem gleichen Aufbau:

- **Ziel** — Lernziel
- **Warum** — Warum das Thema wichtig ist
- **Kernkonzepte** — Schlüsselbegriffe erklärt
- **Praxisaufgabe** — Hands-on-Aufgaben zum lokalen Ausprobieren
- **Typische Fehler** — Häufige Fehler und wie man sie behebt
- **Checkpoint** — Fragen zur Überprüfung des Verständnisses
- **Definition of Done** — Klare Abschlusskriterien
- **Weiterführende Links** — Weiterführende Links zu offiziellen Quellen
