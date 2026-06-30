# Lab 07 – GitOps mit Argo CD

## Ziel

Du installierst Argo CD in deinem kind-Cluster und erstellst eine Application, die ein Git-Repository als Quelle für Kubernetes-Manifeste nutzt. Du erlebst den GitOps-Workflow: Änderung im Git → automatisches Update im Cluster.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert
- [ ] Ein eigenes Git-Repository (GitHub, GitLab, etc.) mit Kubernetes-Manifesten

## Schritt-für-Schritt

### Schritt 1: Argo CD installieren

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Warten bis alle Pods laufen
kubectl wait --for=condition=available \
  deployment/argocd-server \
  -n argocd \
  --timeout=120s

kubectl get pods -n argocd
```

### Schritt 2: Argo CD UI aufrufen

```bash
# Admin-Passwort abrufen
ARGOCD_PW=$(kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d)
echo "Argo CD Passwort: $ARGOCD_PW"

# UI-Port weiterleiten
kubectl port-forward svc/argocd-server -n argocd 8080:443
# -> https://localhost:8080
# Login: admin / <Passwort oben>
```

### Schritt 3: Argo CD CLI installieren (optional)

```bash
# macOS
brew install argocd

# Login
argocd login localhost:8080 --insecure \
  --username admin \
  --password "$ARGOCD_PW"
```

### Schritt 4: Erstes eigenes Repository vorbereiten

Lege in deinem GitHub-Repository folgende Datei an:

```
mein-repo/
└── k8s/
    └── nginx-deployment.yaml
```

Inhalt von `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitops-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: gitops-nginx
  template:
    metadata:
      labels:
        app: gitops-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27-alpine
        ports:
        - containerPort: 80
```

### Schritt 5: Argo CD Application erstellen

Passe die Repository-URL in `manifests/argocd-application.yaml` an und wende sie an:

```bash
# Datei editieren: repoURL auf dein Repository setzen
kubectl apply -f manifests/argocd-application.yaml
```

Alternativ über die Argo CD UI:
1. "New App" klicken
2. Application Name: `gitops-demo`
3. Project: `default`
4. Repository URL: dein Git-Repo
5. Path: `k8s/`
6. Destination: `https://kubernetes.default.svc`
7. Namespace: `default`
8. "Create" klicken

### Schritt 6: GitOps-Workflow erleben

```bash
# Status prüfen
kubectl get application -n argocd

# Über CLI
argocd app list
argocd app get gitops-demo
```

Jetzt: Ändere `replicas: 2` auf `replicas: 3` in deinem Git-Repository und pushe.

```bash
# Nach 3 Minuten (oder manuell sync):
argocd app sync gitops-demo

# Pods beobachten
kubectl get pods -w
```

## Validierung

```bash
# Application-Status
kubectl get application -n argocd
# gitops-demo   Synced   Healthy

# Deployed Pods
kubectl get pods -l app=gitops-nginx

# Letzter Sync
argocd app history gitops-demo
```

## Cleanup

```bash
kubectl delete -f manifests/argocd-application.yaml
kubectl delete namespace argocd
kubectl delete deployment gitops-nginx
```

## Erweiterungsaufgabe

1. Aktiviere Auto-Sync und Self-Heal: Lösche manuell einen Pod und beobachte, wie Argo CD ihn sofort wiederherstellt.
2. Füge eine zweite Application hinzu, die auf einen anderen Pfad im selben Repo zeigt.
3. Probiere das App-of-Apps Pattern: Erstelle eine "root" Application, die andere Applications verwaltet.
