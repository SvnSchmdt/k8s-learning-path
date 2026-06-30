# Kubernetes Learning Path

> Vom ersten Pod bis GitOps und Production Readiness – ein praxisorientierter Lernpfad für Einsteiger und Junior Cloud/DevOps Engineers.

---

## Worum geht es?

Dieses Repository ist kein Link-Sammler. Es ist ein strukturierter, hands-on Lernpfad, der dich Schritt für Schritt durch Kubernetes führt – von den Grundlagen bis hin zu echten Produktionsthemen wie Helm, Observability, RBAC und GitOps.

Du lernst nicht nur die Theorie. Du wendest sie direkt an: mit lokalen Clustern, echten YAML-Manifesten, kubectl-Kommandos und Mini-Projekten.

---

## Zielgruppe

- Entwickler, die verstehen wollen, wie ihre Container in Kubernetes laufen
- Systemadministratoren, die in Cloud Native einsteigen wollen
- Junior DevOps / Platform Engineers am Anfang ihrer Karriere
- Alle, die sich auf die CKA-Prüfung vorbereiten wollen

**Vorwissen:** Grundlegende Linux-Kenntnisse, Umgang mit der Kommandozeile, Basisverständnis von Containern (Docker ist von Vorteil, aber kein Muss).

---

## Was du am Ende kannst

- Kubernetes-Cluster lokal aufsetzen und verstehen
- Pods, Deployments, Services und Ingress erstellen und verwalten
- Konfigurationen und Secrets sicher handhaben
- Persistenten Storage konfigurieren
- RBAC und Sicherheitsgrundlagen anwenden
- Mit Helm Charts arbeiten und eigene Charts erstellen
- Grundlegendes Monitoring und Logging einrichten
- GitOps-Workflows mit Argo CD verstehen und umsetzen
- Produktionsreife Deployments planen

---

## Empfohlene Lernreihenfolge

| Phase | Inhalt | Aufwand |
|-------|--------|---------|
| 0 | [Voraussetzungen](modules/00-prerequisites/README.md) | 0,5 Woche |
| 1 | [Container Basics](modules/01-container-basics/README.md) + [K8s Fundamentals](modules/02-kubernetes-fundamentals/README.md) | 1 Woche |
| 2 | [kubectl](modules/03-kubectl-and-cluster-access/README.md) + [Pods & Workloads](modules/04-pods-and-workloads/README.md) | 1 Woche |
| 3 | [Services & Networking](modules/05-services-and-networking/README.md) + [Ingress](modules/06-ingress-and-dns/README.md) | 1 Woche |
| 4 | [ConfigMaps & Secrets](modules/07-configmaps-secrets-and-env/README.md) + [Storage](modules/08-storage/README.md) | 1 Woche |
| 5 | [RBAC](modules/09-rbac-and-security-basics/README.md) + [Troubleshooting](modules/12-troubleshooting/README.md) | 1 Woche |
| 6 | [Helm & Kustomize](modules/10-helm-and-kustomize/README.md) + [Observability](modules/11-observability/README.md) | 1,5 Wochen |
| 7 | [GitOps mit Argo CD](modules/13-gitops-with-argocd/README.md) | 1 Woche |
| 8 | [Production Readiness](modules/14-production-readiness/README.md) + [Nächste Schritte](modules/15-next-steps-cka-and-homelab/README.md) | 1 Woche |

**Gesamtaufwand:** ca. 8–10 Wochen bei 1–2 Stunden täglich

---

## Setup – Lokaler Cluster

Kubernetes wird praktisch gelernt. Du brauchst von Anfang an einen lokalen Cluster.

**Empfehlung: [kind](https://kind.sigs.k8s.io/)** (Kubernetes in Docker)

```bash
# kind installieren (macOS)
brew install kind

# Cluster erstellen
kind create cluster --name k8s-lernpfad

# kubectl konfigurieren (wird automatisch gesetzt)
kubectl cluster-info --context kind-k8s-lernpfad
```

**Alternativen:**
- [minikube](https://minikube.sigs.k8s.io/) – einfacher Einstieg, GUI-freundlich
- [k3d](https://k3d.io/) – k3s in Docker, sehr leichtgewichtig

Starte mit [Lab 00: Lokalen Cluster mit kind](labs/00-local-cluster-kind/README.md).

---

## Inhaltsübersicht

### Roadmap
- [Übersicht](roadmap/00-overview.md)
- [Learning Path](roadmap/01-learning-path.md)
- [Checkpoints](roadmap/02-checkpoints.md)

### Module
| Nr | Modul | Beschreibung |
|----|-------|-------------|
| 00 | [Voraussetzungen](modules/00-prerequisites/README.md) | Tools, Umgebung, Grundwissen |
| 01 | [Container Basics](modules/01-container-basics/README.md) | Docker, Images, Container-Grundlagen |
| 02 | [Kubernetes Fundamentals](modules/02-kubernetes-fundamentals/README.md) | Architektur, Komponenten, Konzepte |
| 03 | [kubectl & Cluster-Zugriff](modules/03-kubectl-and-cluster-access/README.md) | CLI, Kubeconfig, Contexts |
| 04 | [Pods & Workloads](modules/04-pods-and-workloads/README.md) | Pod, Deployment, ReplicaSet, DaemonSet |
| 05 | [Services & Networking](modules/05-services-and-networking/README.md) | ClusterIP, NodePort, LoadBalancer |
| 06 | [Ingress & DNS](modules/06-ingress-and-dns/README.md) | Ingress Controller, Routing, TLS |
| 07 | [ConfigMaps, Secrets & Env](modules/07-configmaps-secrets-and-env/README.md) | Konfiguration sicher verwalten |
| 08 | [Storage](modules/08-storage/README.md) | PV, PVC, StorageClass |
| 09 | [RBAC & Security](modules/09-rbac-and-security-basics/README.md) | Rollen, ServiceAccounts, Policies |
| 10 | [Helm & Kustomize](modules/10-helm-and-kustomize/README.md) | Paketmanagement, Templating |
| 11 | [Observability](modules/11-observability/README.md) | Metrics, Logs, Tracing |
| 12 | [Troubleshooting](modules/12-troubleshooting/README.md) | Debugging, häufige Fehler |
| 13 | [GitOps mit Argo CD](modules/13-gitops-with-argocd/README.md) | Deklarative Deployments |
| 14 | [Production Readiness](modules/14-production-readiness/README.md) | Probes, Limits, PodDisruptionBudgets |
| 15 | [Nächste Schritte: CKA & Homelab](modules/15-next-steps-cka-and-homelab/README.md) | Zertifizierung, echte Cluster |

### Labs
| Nr | Lab | Beschreibung |
|----|-----|-------------|
| 00 | [Lokaler Cluster mit kind](labs/00-local-cluster-kind/README.md) | Ersten Cluster aufsetzen |
| 01 | [nginx Deployment](labs/01-nginx-deployment/README.md) | Erste Workload ausrollen |
| 02 | [Service & Networking](labs/02-service-networking/README.md) | Pods erreichbar machen |
| 03 | [Config & Secrets](labs/03-config-and-secrets/README.md) | Konfiguration in Pods |
| 04 | [Ingress](labs/04-ingress/README.md) | HTTP-Routing lokal testen |
| 05 | [Helm Chart](labs/05-helm-chart/README.md) | Eigenes Chart erstellen |
| 06 | [Monitoring](labs/06-monitoring/README.md) | Metrics & Logs verstehen |
| 07 | [GitOps mit Argo CD](labs/07-gitops-argocd/README.md) | Deklarativer Deployment-Workflow |

### Übungen & Ressourcen
- [Übungen – Beginner](exercises/beginner.md)
- [Übungen – Intermediate](exercises/intermediate.md)
- [Troubleshooting-Aufgaben](exercises/troubleshooting.md)
- [Glossary](resources/glossary.md)
- [kubectl Cheat Sheet](resources/cheat-sheet.md)
- [Offizielle Dokumentation](resources/official-docs.md)

---

## Wie du mit diesem Repo lernst

Kubernetes lernst du nicht durch Lesen – sondern durch Machen. Halte dich an diese Regeln:

1. **Führe jedes Lab aus.** Nicht überspringen, nicht nur drüberlesen.
2. **Mach nach jedem Lab Cleanup.** Residuale Ressourcen stören spätere Labs.
3. **Provoziere Fehler absichtlich.** `kubectl delete deployment` beobachten. Falsche Image-Tags ausprobieren. Probes kaputt konfigurieren.
4. **Erkläre Befehle bevor du weitergehst.** Wenn du nicht weißt, was `kubectl apply -f` tut: erkläre es, bevor du zum nächsten Schritt gehst.
5. **Beantworte Checkpoints ehrlich.** Nicht abhaken, weil du die Frage kennst – sondern weil du sie beantworten *kannst*.
6. **Führe ein Lernjournal.** Ein Markdown-File, eine Notiz-App, ein Notizbuch – egal. Notiere, was dich überrascht hat.

> "The best way to learn Kubernetes is to break it and fix it." – Community-Weisheit

---

## Getestete Versionen

Die Inhalte dieses Repos sind bewusst versionsarm gehalten – Kernkonzepte ändern sich selten. Labs sollten regelmäßig gegen aktuelle Versionen getestet werden.

| Tool | Empfehlung |
|------|-----------|
| Kubernetes | 1.30 oder neuer |
| kubectl | passend zur Cluster-Version (±1 Minor) |
| kind | aktuelle stabile Version |
| Docker / Podman | aktuelle stabile Version |
| Helm | v3.x |
| Argo CD | aktuelle stabile Version |

> [!NOTE]
> Kubernetes garantiert API-Kompatibilität über mehrere Minor-Versionen. Die meisten Beispiele hier laufen auch auf älteren 1.28+ Clustern.

---

## Beitragen

Beiträge sind willkommen! Lies [CONTRIBUTING.md](CONTRIBUTING.md) für Details.

---

## Lizenz

[MIT License](LICENSE)
