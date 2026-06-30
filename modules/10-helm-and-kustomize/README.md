# Modul 10 – Helm & Kustomize

## Ziel des Moduls

Nach diesem Modul kannst du Helm-Charts installieren, aktualisieren und eigene Charts erstellen. Du verstehst Kustomize als Alternative und weißt, wann du welches Tool verwendest.

## Warum ist das wichtig?

Reale Anwendungen bestehen aus vielen YAML-Dateien. Wenn du 10 Deployments, 10 Services und 10 ConfigMaps hast, willst du nicht jede Datei manuell anpassen. Helm und Kustomize lösen dieses Problem auf unterschiedliche Weisen.

## Kernkonzepte

### Helm

- **Chart:** Ein Paket mit allen Kubernetes-Ressourcen für eine Anwendung. Enthält Templates, Default-Values und Metadaten.
- **Release:** Eine installierte Instanz eines Charts in einem Cluster.
- **Values:** Konfiguration, die Template-Variablen überschreibt. Ermöglicht, denselben Chart für verschiedene Umgebungen zu nutzen.
- **Repository:** Sammlung von Helm Charts (z.B. Artifact Hub).
- **Chart.yaml:** Metadaten-Datei eines Charts (Name, Version, Beschreibung).

### Kustomize

- **Base:** Basis-YAML-Dateien, die für alle Umgebungen gelten.
- **Overlay:** Umgebungsspezifische Änderungen (patches), die auf die Base angewendet werden.
- **kustomization.yaml:** Steuert, was zusammengefügt wird und welche Patches angewendet werden.

## Praxisaufgabe: Helm

### Helm installieren

```bash
# macOS
brew install helm

# Prüfen
helm version
```

### Chart aus Repository installieren

```bash
# Repository hinzufügen
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Verfügbare Charts suchen
helm search repo nginx

# Chart installieren
helm install mein-nginx bitnami/nginx --namespace demo --create-namespace

# Alle Releases anzeigen
helm list -A

# Release-Details
helm status mein-nginx -n demo
```

### Mit Values arbeiten

```bash
# Values einsehen
helm show values bitnami/nginx

# Beim Installieren überschreiben
helm install mein-nginx bitnami/nginx \
  --set replicaCount=2 \
  --set service.type=NodePort

# Mit values.yaml
helm install mein-nginx bitnami/nginx -f eigene-values.yaml
```

### Upgrade und Rollback

```bash
helm upgrade mein-nginx bitnami/nginx --set replicaCount=3
helm rollback mein-nginx 1
helm uninstall mein-nginx -n demo
```

## Praxisaufgabe: Eigenes Chart

```bash
# Chart-Skelett erstellen
helm create meine-app

# Struktur:
# meine-app/
# ├── Chart.yaml
# ├── values.yaml
# └── templates/
#     ├── deployment.yaml
#     ├── service.yaml
#     └── _helpers.tpl

# Chart lokal rendern (ohne Installation)
helm template meine-app ./meine-app

# Chart installieren
helm install meine-app ./meine-app
```

## Praxisaufgabe: Kustomize

```
k8s/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
```

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
    name: meine-app
```

```bash
# Kustomize anwenden (kubectl integriert)
kubectl apply -k overlays/prod/

# Nur ausgeben, nicht anwenden
kubectl kustomize overlays/prod/
```

## Helm vs. Kustomize

| Merkmal | Helm | Kustomize |
|---------|------|-----------|
| Templating | Go-Templates mit Values | Patch-basiert, kein Templating |
| Packages | Charts aus Repositories | Keine Packages |
| Komplexität | Höher | Niedriger |
| Kubernetes-nativ | Externes Tool | In kubectl integriert |
| Ideal für | Externe Software deployen | Eigene Apps konfigurieren |

## Typische Fehler

- **Helm-Release löscht Ressourcen nicht vollständig:** `helm uninstall` löscht normalerweise alles. Prüfen mit `kubectl get all`.
- **Values werden nicht übernommen:** Pfad in `--set` falsch oder Values-Datei enthält Syntaxfehler.
- **Kustomize Patch-Target stimmt nicht:** Der Patch findet keine Ressource. Name/Kind im target prüfen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist ein Helm Chart und was enthält er?
- [ ] Wie installierst du eine spezifische Chart-Version mit bestimmten Values?
- [ ] Was ist der Unterschied zwischen `helm install` und `helm upgrade`?
- [ ] Was macht Kustomize anders als Helm?
- [ ] Wann würdest du Helm bevorzugen, wann Kustomize?

## Weiterführende Links

- [Helm Dokumentation](https://helm.sh/docs/)
- [Artifact Hub (Chart Repository)](https://artifacthub.io/)
- [Kustomize Dokumentation](https://kustomize.io/)
- [Kustomize in kubectl](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
