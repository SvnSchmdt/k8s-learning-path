# Lab 05 – Eigenes Helm Chart erstellen

## Ziel

Du erstellst ein eigenes Helm Chart für eine einfache Web-Anwendung, installierst es in verschiedenen Konfigurationen und lernst den Upgrade/Rollback-Workflow.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] Helm installiert (`helm version`)

## Schritt-für-Schritt

### Schritt 1: Helm installieren (falls nötig)

```bash
# macOS
brew install helm

# Linux
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

helm version
```

### Schritt 2: Chart-Skelett in diesem Lab-Verzeichnis prüfen

Das Chart liegt bereits unter `chart/meine-app/`:

```
chart/meine-app/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    └── service.yaml
```

### Schritt 3: Chart-Templates rendern (ohne Installation)

```bash
# Zeigt das gerenderte YAML ohne es anzuwenden
helm template meine-app chart/meine-app/

# Mit überschriebenen Values
helm template meine-app chart/meine-app/ --set replicaCount=5
```

### Schritt 4: Chart installieren

```bash
helm install meine-app chart/meine-app/ \
  --namespace lab05 \
  --create-namespace

helm list -n lab05
kubectl get all -n lab05
```

### Schritt 5: Chart mit eigenen Values upgraden

```bash
helm upgrade meine-app chart/meine-app/ \
  --namespace lab05 \
  --set replicaCount=3 \
  --set image.tag=1.28-alpine

helm list -n lab05
kubectl get pods -n lab05
```

### Schritt 6: Upgrade-Historie und Rollback

```bash
# Release-Historie
helm history meine-app -n lab05

# Rollback auf Revision 1
helm rollback meine-app 1 -n lab05

helm history meine-app -n lab05
```

### Schritt 7: Chart aus Repository installieren

```bash
# Bitnami-Repository hinzufügen
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Chart-Werte anzeigen
helm show values bitnami/nginx | head -40

# Installieren mit eigenen Values
helm install demo-nginx bitnami/nginx \
  --namespace lab05 \
  --set replicaCount=1

helm list -n lab05
```

## Validierung

```bash
# Chart-Status
helm status meine-app -n lab05

# Gerenderte Values
helm get values meine-app -n lab05

# Alle Ressourcen des Releases
helm get manifest meine-app -n lab05
```

## Cleanup

```bash
helm uninstall meine-app -n lab05
helm uninstall demo-nginx -n lab05
kubectl delete namespace lab05
```

## Erweiterungsaufgabe

1. Füge dem Chart eine `ConfigMap`-Template hinzu, die über Values konfigurierbar ist.
2. Erstelle eine `values-prod.yaml` und eine `values-dev.yaml` mit unterschiedlichen Konfigurationen.
3. Füge dem Deployment eine Liveness-Probe hinzu, die über Values ein- und ausgeschaltet werden kann.
