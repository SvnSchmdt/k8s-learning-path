# Kubernetes Learning Path

Ein praxisorientierter, schrittweiser Kubernetes-Lernpfad – vom ersten Pod bis zu GitOps und Production Readiness.

---

## Für wen ist dieser Lernpfad?

Dieser Lernpfad richtet sich an:

- **Entwickler**, die verstehen wollen, wie ihre Anwendungen in Kubernetes laufen
- **Systemadministratoren**, die in cloud-native Infrastruktur einsteigen
- **DevOps Engineers**, die solide Kubernetes-Grundlagen aufbauen wollen
- **Lernende**, die sich auf CKA, CKAD oder andere Kubernetes-Zertifizierungen vorbereiten

Keine Kubernetes-Vorkenntnisse erforderlich. Grundlegende Linux-Kenntnisse und erste Erfahrungen mit Containern (Docker) helfen beim Einstieg.

---

## Was du am Ende können wirst

- Einen lokalen Kubernetes-Cluster mit kind aufsetzen
- Anwendungen mit Pods, Deployments und ReplicaSets deployen
- Workloads mit Services und Ingress exponieren
- Konfiguration mit ConfigMaps und Secrets verwalten
- Persistenten Storage mit PersistentVolumeClaims einrichten
- Workloads mit RBAC und SecurityContexts absichern
- Anwendungen mit Helm und Kustomize paketieren und deployen
- Laufende Workloads mit Probes, Logs und Metriken beobachten und debuggen
- GitOps-Workflows mit Argo CD implementieren
- Production-Readiness-Muster anwenden: Probes, Resource Limits, HPA

---

## Hier starten

**Empfohlene Reihenfolge:**

1. **[Module](modules/index.md)** der Reihe nach durcharbeiten (00 → 15)
2. Die zugehörigen **[Labs](labs/index.md)** direkt dabei machen
3. Mit **[Übungsaufgaben](exercises/index.md)** nach jeder Phase vertiefen
4. Das **[Cheat Sheet](resources/cheat-sheet.md)** beim Arbeiten als Referenz nutzen
5. Fortschritt mit den **[Roadmap-Checkpoints](roadmap/02-checkpoints.md)** prüfen

---

## Lernprinzipien

> **Wie du das meiste aus diesem Lernpfad herausholst:**
>
> - **Nicht nur lesen.** Jeden Befehl ausführen. Jeden. Einzelnen.
> - **Dinge absichtlich kaputt machen.** Ein Label ändern, einen Service löschen, eine Probe falsch konfigurieren – und dann reparieren.
> - **Checkpoints beantworten.** Wenn du etwas nicht in eigenen Worten erklären kannst, Modul nochmal lesen.
> - **Nach jedem Lab aufräumen.** `kubectl delete namespace <name>` hält den Cluster sauber.
> - **An schwierigen Stellen verlangsamen.** Verwirrung ist ein Signal, kein Versagen.

---

## Überblick

| Bereich | Beschreibung |
|---------|-------------|
| [Roadmap](roadmap/index.md) | Phasenweiser Lernplan und Checkpoints |
| [Module](modules/index.md) | 16 strukturierte Lernmodule (00–15) |
| [Labs](labs/index.md) | 8 praktische Labs mit kind, kubectl und Helm |
| [Übungsaufgaben](exercises/index.md) | Anfänger-, Fortgeschrittenen- und Troubleshooting-Aufgaben |
| [Ressourcen](resources/index.md) | Glossar, Cheat Sheet und offizielle Dokumentationslinks |
| [Beispiele](examples/index.md) | Fertige YAML-Manifeste |

---

## Lokales Setup

```bash
# Voraussetzungen (macOS)
brew install kind kubectl helm

# Cluster starten
kind create cluster --name k8s-learning

# Prüfen
kubectl get nodes
```

Vollständige Anleitung: [Lab 00](labs/00-local-cluster-kind/README.de.md)
