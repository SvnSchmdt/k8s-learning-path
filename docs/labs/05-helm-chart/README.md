# Lab 05 – Eigenes Helm Chart erstellen

## Was du baust

Du erstellst ein eigenes Helm Chart für eine nginx-Webanwendung, installierst es im Cluster, führst ein Upgrade mit geänderten Values durch und lernst den Rollback-Workflow kennen. Das Chart liegt bereits vorbereitet unter `chart/`.

**Kubernetes-Objekte (durch Helm verwaltet):** Deployment, Service

```text
Helm Chart: meine-app/
    ├── Chart.yaml        (Metadaten)
    ├── values.yaml       (Default-Konfiguration)
    └── templates/
          ├── deployment.yaml  ──► Deployment im Cluster
          └── service.yaml     ──► Service im Cluster

helm install ──► Release "meine-app" (Namespace: lab05)
```

## Ziel

Ein Helm Release `meine-app` ist im Namespace `lab05` installiert. Du hast ein Upgrade und einen Rollback erfolgreich durchgeführt.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] Helm v3 installiert (`helm version`)

## Schritt-für-Schritt

### Schritt 1: Helm installieren (falls nötig)

```bash
# macOS
brew install helm

# Linux
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

```bash
helm version
```

Erwartete Ausgabe:
```text
version.BuildInfo{Version:"v3.x.x", ...}
```

### Schritt 2: Chart-Struktur inspizieren

Das Chart liegt bereits unter `chart/`:

```bash
ls chart/
ls chart/templates/
```

Erwartete Ausgabe:
```text
Chart.yaml  values.yaml  templates/

deployment.yaml  service.yaml
```

### Schritt 3: Chart-Templates rendern (ohne Installation)

```bash
# Zeigt das gerenderte YAML ohne es anzuwenden
helm template meine-app chart/
```

Erwartete Ausgabe (Auszug):
```text
---
# Source: meine-app/templates/service.yaml
apiVersion: v1
kind: Service
...
---
# Source: meine-app/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
...
```

```bash
# Mit überschriebenen Values rendern
helm template meine-app chart/ --set replicaCount=5 | grep "replicas:"
```

Erwartete Ausgabe:
```text
  replicas: 5
```

### Schritt 4: Chart installieren

```bash
helm install meine-app chart/ \
  --namespace lab05 \
  --create-namespace
```

Erwartete Ausgabe:
```text
NAME: meine-app
LAST DEPLOYED: ...
NAMESPACE: lab05
STATUS: deployed
REVISION: 1
```

```bash
helm list -n lab05
```

Erwartete Ausgabe:
```text
NAME        NAMESPACE  REVISION  UPDATED  STATUS    CHART            APP VERSION
meine-app   lab05      1         ...      deployed  meine-app-0.1.0  1.27
```

```bash
kubectl get all -n lab05
```

Erwartete Ausgabe:
```text
NAME                                      READY   STATUS    RESTARTS   AGE
pod/meine-app-deployment-xxxxxxxxx-xxxxx  1/1     Running   0          30s
pod/meine-app-deployment-xxxxxxxxx-yyyyy  1/1     Running   0          30s

NAME                       TYPE        CLUSTER-IP    PORT(S)   AGE
service/meine-app-service  ClusterIP   10.96.x.y     80/TCP    30s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/meine-app-deployment 2/2     2            2           30s
```

### Schritt 5: Upgrade mit geänderten Values

```bash
helm upgrade meine-app chart/ \
  --namespace lab05 \
  --set replicaCount=3 \
  --set image.tag=1.28-alpine
```

Erwartete Ausgabe:
```text
Release "meine-app" has been upgraded. Happy Helming!
REVISION: 2
```

```bash
kubectl get pods -n lab05
```

Erwartete Ausgabe:
```text
NAME                                      READY   STATUS    RESTARTS   AGE
meine-app-deployment-xxxxxxxxx-aaaaa      1/1     Running   0          20s
meine-app-deployment-xxxxxxxxx-bbbbb      1/1     Running   0          20s
meine-app-deployment-xxxxxxxxx-ccccc      1/1     Running   0          20s
```

### Schritt 6: Release-Historie und Rollback

```bash
helm history meine-app -n lab05
```

Erwartete Ausgabe:
```text
REVISION  UPDATED   STATUS      CHART            APP VERSION  DESCRIPTION
1         ...       superseded  meine-app-0.1.0  1.27         Install complete
2         ...       deployed    meine-app-0.1.0  1.27         Upgrade complete
```

```bash
# Rollback auf Revision 1
helm rollback meine-app 1 -n lab05
helm history meine-app -n lab05
```

Erwartete Ausgabe:
```text
REVISION  STATUS      DESCRIPTION
1         superseded  Install complete
2         superseded  Upgrade complete
3         deployed    Rollback to 1
```

### Schritt 7: Chart aus öffentlichem Repository

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Installieren
helm install demo-nginx bitnami/nginx \
  --namespace lab05 \
  --set replicaCount=1

helm list -n lab05
```

## Validierung

```bash
# Release-Status
helm status meine-app -n lab05

# Aktuell gesetzte Values
helm get values meine-app -n lab05

# Alle verwalteten Ressourcen
helm get manifest meine-app -n lab05 | grep "kind:"
```

Erwartete Ausgabe:
```text
kind: Service
kind: Deployment
```

## Cleanup

```bash
helm uninstall meine-app -n lab05
helm uninstall demo-nginx -n lab05
kubectl delete namespace lab05
```

Erwartete Ausgabe:
```text
release "meine-app" uninstalled
release "demo-nginx" uninstalled
namespace "lab05" deleted
```

## Erweiterungsaufgabe

1. Füge dem Chart ein `ConfigMap`-Template hinzu (`templates/configmap.yaml`). Übergib den ConfigMap-Inhalt als Value in `values.yaml`.
2. Erstelle eine `values-prod.yaml` mit `replicaCount: 5` und eine `values-dev.yaml` mit `replicaCount: 1`. Installiere einmal pro Umgebung.
3. Füge dem Deployment eine Liveness-Probe hinzu, die per Value ein- und ausgeschaltet werden kann (`livenessProbe.enabled: true/false`).
