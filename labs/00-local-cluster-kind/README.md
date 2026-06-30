# Lab 00 – Lokalen Cluster mit kind aufsetzen

## Ziel

Am Ende dieses Labs läuft ein lokaler Kubernetes-Cluster auf deinem Rechner. Du hast kubectl konfiguriert und kannst mit dem Cluster interagieren.

## Voraussetzungen

- [ ] Docker oder Docker Desktop läuft (`docker version` gibt eine Versionsnummer aus)
- [ ] Internetverbindung für Downloads

## Schritt-für-Schritt

### Schritt 1: kind installieren

```bash
# macOS
brew install kind

# Linux (amd64)
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Windows (PowerShell)
winget install Kubernetes.kind
```

```bash
# Prüfen
kind version
```

### Schritt 2: kubectl installieren

```bash
# macOS
brew install kubectl

# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Prüfen
kubectl version --client
```

### Schritt 3: Cluster erstellen

```bash
kind create cluster --name k8s-lernpfad
```

Ausgabe (ca.):
```
Creating cluster "k8s-lernpfad" ...
 ✓ Ensuring node image (kindest/node:v1.30.x) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-k8s-lernpfad"
```

### Schritt 4: Cluster mit mehreren Nodes (optional)

Für realistischere Szenarien – erstelle `kind-config.yaml`:

```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

```bash
kind create cluster --name k8s-multi --config kind-config.yaml
```

### Schritt 5: Cluster-Kontext prüfen

```bash
kubectl config current-context
# -> kind-k8s-lernpfad

kubectl cluster-info
# -> Kubernetes control plane is running at https://127.0.0.1:XXXXX
```

## Validierung

```bash
# Nodes anzeigen
kubectl get nodes
# NAME                         STATUS   ROLES           AGE   VERSION
# k8s-lernpfad-control-plane   Ready    control-plane   2m    v1.30.x

# System-Pods prüfen
kubectl get pods -n kube-system
# Alle Pods sollten Running oder Completed sein

# Ersten Test-Pod starten
kubectl run hello --image=nginx:1.27-alpine --restart=Never
kubectl get pod hello
# NAME    READY   STATUS    RESTARTS   AGE
# hello   1/1     Running   0          10s

kubectl logs hello
# Nginx-Startlog erscheint
```

## Cleanup

```bash
# Test-Pod löschen
kubectl delete pod hello

# Cluster löschen
kind delete cluster --name k8s-lernpfad

# Alle kind-Cluster anzeigen
kind get clusters
```

## Erweiterungsaufgabe

Erstelle einen kind-Cluster mit einer spezifischen Kubernetes-Version (z.B. v1.29):

```bash
kind create cluster --name k8s-v129 \
  --image kindest/node:v1.29.4@sha256:3abb816a5b1061fb15c6e9e60856ec40d56b7b52bcea5f5f1350bc6e2320b6f8
```

Welche Kubernetes-Versionen sind verfügbar? Schau auf der [kind-Release-Seite](https://github.com/kubernetes-sigs/kind/releases).
