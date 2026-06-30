# Labs

8 praktische Labs, die Modulwissen in die Praxis umsetzen. Jedes Lab läuft lokal mit [kind](https://kind.sigs.k8s.io/) und führt dich durch ein vollständiges, überprüfbares Szenario.

---

## Lab-Übersicht

| Lab | Titel | Ziel | Dauer | Schwierigkeit |
|-----|-------|------|-------|---------------|
| [00](00-local-cluster-kind/README.de.md) | Lokaler Cluster mit kind | Lokalen Kubernetes-Cluster aufsetzen | ~20 Min | Einsteiger |
| [01](01-nginx-deployment/README.de.md) | nginx Deployment | Deployment deployen, skalieren und zurückrollen | ~30 Min | Einsteiger |
| [02](02-service-networking/README.de.md) | Service & Networking | Pods via ClusterIP und NodePort exponieren | ~30 Min | Einsteiger |
| [03](03-config-and-secrets/README.de.md) | Config & Secrets | Konfiguration via ConfigMaps und Secrets injizieren | ~30 Min | Einsteiger |
| [04](04-ingress/README.de.md) | Ingress | HTTP-Traffic mit nginx Ingress Controller routen | ~45 Min | Fortgeschritten |
| [05](05-helm-chart/README.de.md) | Helm Chart | Helm Chart erstellen, installieren, upgraden und zurückrollen | ~45 Min | Fortgeschritten |
| [06](06-monitoring/README.de.md) | Monitoring & Probes | Health Probes konfigurieren, kubectl top nutzen, Events beobachten | ~45 Min | Fortgeschritten |
| [07](07-gitops-argocd/README.de.md) | GitOps mit Argo CD | Mit Argo CD deployen, GitOps-Sync-Verhalten beobachten | ~60 Min | Fortgeschritten |

---

## Voraussetzungen für alle Labs

- [kind](https://kind.sigs.k8s.io/) installiert und lauffähig
- `kubectl` installiert und konfiguriert
- Docker oder Podman läuft lokal
- Helm v3 (für Lab 05+)

Starte mit **[Lab 00](00-local-cluster-kind/README.de.md)**, wenn du noch keinen Cluster aufgesetzt hast.

---

## Lab-Struktur

Jedes Lab enthält:

- **Was du baust** — Was du aufbaust, mit ASCII-Architekturdiagramm
- **Schritt-für-Schritt** — Schritt-für-Schritt-Anleitungen mit erwarteter Befehlsausgabe
- **Validierung** — Wie du überprüfst, dass alles korrekt funktioniert
- **Cleanup** — Wie du alle Ressourcen nach dem Lab entfernst
- **Erweiterungsaufgabe** — Was du als nächstes erkunden kannst
