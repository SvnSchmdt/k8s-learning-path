# Lab 00 – Lokalen Cluster mit kind aufsetzen

## Was du baust

Du richtest eine vollständige lokale Kubernetes-Entwicklungsumgebung ein. Am Ende läuft ein Kubernetes-Cluster als Docker-Container auf deinem Rechner, und du kannst ihn über kubectl steuern.

**Kubernetes-Objekte in diesem Lab:** noch keine – dieses Lab legt die Grundlage für alle weiteren.

```text
Dein Rechner
    │
    ├── Docker
    │     └── kind-Container (= Kubernetes Node)
    │               └── Kubernetes Control Plane
    │
    └── kubectl ──────────────────────────> API Server
```

## Ziel

Ein lokaler kind-Cluster läuft. kubectl ist konfiguriert und zeigt den Node im Status `Ready`.

## Voraussetzungen

- [ ] Docker oder Docker Desktop läuft – prüfen mit `docker version`
- [ ] Internetverbindung für Downloads

> [!IMPORTANT]
> Docker muss laufen, bevor du kind verwendest. kind startet Kubernetes-Nodes als Docker-Container.

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
# Version prüfen
kind version
```

Erwartete Ausgabe:
```text
kind v0.23.0 go1.21.x linux/amd64
```

### Schritt 2: kubectl installieren

```bash
# macOS
brew install kubectl

# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

```bash
# Version prüfen
kubectl version --client
```

Erwartete Ausgabe:
```text
Client Version: v1.30.x
Kustomize Version: v5.x.x
```

### Schritt 3: Cluster erstellen

```bash
kind create cluster --name k8s-lernpfad
```

Erwartete Ausgabe:
```text
Creating cluster "k8s-lernpfad" ...
 ✓ Ensuring node image (kindest/node:v1.30.x) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-k8s-lernpfad"
You can now use your cluster with:
kubectl cluster-info --context kind-k8s-lernpfad
```

> [!TIP]
> Der erste Start dauert länger, weil das Node-Image heruntergeladen wird (ca. 800 MB). Folgestarts sind schnell.

### Schritt 4: Kontext prüfen

```bash
kubectl config current-context
```

Erwartete Ausgabe:
```text
kind-k8s-lernpfad
```

```bash
kubectl cluster-info
```

Erwartete Ausgabe:
```text
Kubernetes control plane is running at https://127.0.0.1:XXXXX
CoreDNS is running at https://127.0.0.1:XXXXX/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

### Schritt 5: Cluster mit mehreren Nodes (optional)

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

## Validierung

```bash
# Nodes anzeigen – alle müssen Ready sein
kubectl get nodes
```

Erwartete Ausgabe:
```text
NAME                         STATUS   ROLES           AGE   VERSION
k8s-lernpfad-control-plane   Ready    control-plane   2m    v1.30.x
```

```bash
# System-Pods prüfen – alle sollten Running oder Completed sein
kubectl get pods -n kube-system
```

Erwartete Ausgabe (ca.):
```text
NAME                                                 READY   STATUS    RESTARTS   AGE
coredns-xxxxxxxxx-xxxxx                              1/1     Running   0          2m
coredns-xxxxxxxxx-yyyyy                              1/1     Running   0          2m
etcd-k8s-lernpfad-control-plane                      1/1     Running   0          2m
kube-apiserver-k8s-lernpfad-control-plane            1/1     Running   0          2m
kube-controller-manager-k8s-lernpfad-control-plane   1/1     Running   0          2m
kube-scheduler-k8s-lernpfad-control-plane            1/1     Running   0          2m
```

```bash
# Ersten Test-Pod starten
kubectl run hello --image=nginx:1.27-alpine --restart=Never
kubectl get pod hello
```

Erwartete Ausgabe:
```text
NAME    READY   STATUS    RESTARTS   AGE
hello   1/1     Running   0          15s
```

```bash
# Nginx-Startlog ansehen
kubectl logs hello
```

## Cleanup

```bash
# Test-Pod löschen
kubectl delete pod hello

# Alle kind-Cluster anzeigen
kind get clusters
```

> [!CAUTION]
> Der folgende Befehl löscht den gesamten Cluster und alle darin befindlichen Ressourcen:
>
> ```bash
> kind delete cluster --name k8s-lernpfad
> ```
>
> Für die nächsten Labs muss der Cluster laufen – lösche ihn erst am Ende des Lernpfads.

## Erweiterungsaufgabe

Erstelle einen kind-Cluster mit einer spezifischen Kubernetes-Version. Verfügbare Versionen findest du auf der [kind-Release-Seite](https://github.com/kubernetes-sigs/kind/releases):

```bash
kind create cluster --name k8s-v129 \
  --image kindest/node:v1.29.4
```

Vergleiche die kubectl-Ausgabe beider Cluster und beobachte den Versionsunterschied.
