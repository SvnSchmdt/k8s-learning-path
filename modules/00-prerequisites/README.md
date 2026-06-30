# Modul 00 – Voraussetzungen

## Ziel des Moduls

Nach diesem Modul hast du alle notwendigen Tools installiert und einen laufenden lokalen Kubernetes-Cluster. Du bist bereit, mit den folgenden Modulen zu starten.

## Warum ist das wichtig?

Kubernetes wird praktisch gelernt. Ohne eine funktionierende lokale Umgebung bleiben alle Konzepte abstrakt. kind (Kubernetes in Docker) ermöglicht dir, einen vollständigen Cluster auf deinem Rechner zu betreiben – kostenlos, schnell und ohne Cloud-Zugang.

## Kernkonzepte

- **kind (Kubernetes in Docker):** Startet einen vollständigen Kubernetes-Cluster als Docker-Container. Ideal für lokales Lernen und Testen.
- **kubectl:** Das Kommandozeilen-Tool zur Interaktion mit Kubernetes-Clustern. Fast alles, was du in Kubernetes tust, geht über kubectl.
- **kubeconfig:** Eine Konfigurationsdatei (standardmäßig `~/.kube/config`), die definiert, welche Cluster du kennst und mit welchem du gerade arbeitest.

## Voraussetzungen für dieses Modul

- macOS, Linux oder Windows (WSL2)
- Docker oder Docker Desktop läuft
- Internetverbindung für den Download der Tools

## Praxisaufgabe

### Schritt 1: Docker prüfen

```bash
docker version
```

Du solltest eine Versionsnummer sehen. Falls nicht: [Docker installieren](https://docs.docker.com/get-docker/).

### Schritt 2: kubectl installieren

```bash
# macOS mit Homebrew
brew install kubectl

# Linux (Debian/Ubuntu)
sudo apt-get update && sudo apt-get install -y kubectl

# Variante: Direkter Download
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/
```

```bash
# Prüfen
kubectl version --client
```

### Schritt 3: kind installieren

```bash
# macOS
brew install kind

# Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind
```

```bash
# Prüfen
kind version
```

### Schritt 4: Cluster erstellen

```bash
kind create cluster --name k8s-lernpfad
```

Das kann 1–2 Minuten dauern. Kind lädt das Kubernetes-Node-Image herunter und startet den Cluster.

### Schritt 5: Cluster prüfen

```bash
kubectl cluster-info --context kind-k8s-lernpfad
kubectl get nodes
```

Erwartete Ausgabe:
```
NAME                         STATUS   ROLES           AGE   VERSION
k8s-lernpfad-control-plane   Ready    control-plane   1m    v1.30.x
```

## Beispiel-Kommandos

```bash
# Alle Cluster auflisten (kind)
kind get clusters

# Cluster löschen
kind delete cluster --name k8s-lernpfad

# Aktuellen Kontext prüfen
kubectl config current-context

# Alle Kontexte anzeigen
kubectl config get-contexts
```

## Typische Fehler

- **Docker läuft nicht:** `kind create cluster` schlägt fehl. Lösung: Docker starten.
- **kubectl findet keinen Cluster:** `kubectl get nodes` gibt Connection-Fehler. Lösung: `kubectl config use-context kind-k8s-lernpfad`
- **Port-Konflikte:** Selten bei kind. Falls doch: bestehende Cluster löschen und neu erstellen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was macht kind, und warum nutzen wir es statt einem echten Cluster?
- [ ] Was ist kubectl, und was ist kubeconfig?
- [ ] Wie wechselst du zwischen mehreren Cluster-Kontexten?

## Weiterführende Links

- [kind Dokumentation](https://kind.sigs.k8s.io/)
- [kubectl installieren](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [kubeconfig verstehen](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)
