# Lab 07 – GitOps mit Argo CD

## Was du baust

Du installierst Argo CD in deinem kind-Cluster und verbindest es mit einem Git-Repository. Argo CD beobachtet das Repository und hält den Cluster automatisch synchron. Änderungen im Git werden ohne manuellen `kubectl apply` in den Cluster deployed.

**Kubernetes-Objekte:** Argo CD Application, Argo CD Namespace, Deployment (durch Argo CD), Pods

```text
Git Repository
      │  (Argo CD pollt alle 3 Minuten)
      ▼
Argo CD Application (argocd Namespace)
      │  (sync: kubectl apply im Hintergrund)
      ▼
Deployment: gitops-nginx (default Namespace)
      └── Pod (nginx)
          Pod (nginx)
```

## Ziel

Argo CD läuft im Cluster. Eine Application synchronisiert ein Git-Repository. Eine Änderung im Git (z.B. `replicas: 3`) wird automatisch in den Cluster deployed.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert
- [ ] Eigenes Git-Repository vorhanden (GitHub, GitLab, etc.)
- [ ] Git-Zugang für Argo CD (öffentliches Repo: kein Token nötig)

## Schritt-für-Schritt

### Schritt 1: Argo CD installieren

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Warten bis Argo CD bereit ist:

```bash
kubectl wait --for=condition=available \
  deployment/argocd-server \
  -n argocd \
  --timeout=180s
```

Erwartete Ausgabe:
```text
deployment.apps/argocd-server condition met
```

```bash
kubectl get pods -n argocd
```

Erwartete Ausgabe:
```text
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          2m
argocd-applicationset-controller-xxxxxxxxx-xxxxx    1/1     Running   0          2m
argocd-dex-server-xxxxxxxxx-xxxxx                   1/1     Running   0          2m
argocd-notifications-controller-xxxxxxxxx-xxxxx     1/1     Running   0          2m
argocd-redis-xxxxxxxxx-xxxxx                        1/1     Running   0          2m
argocd-repo-server-xxxxxxxxx-xxxxx                  1/1     Running   0          2m
argocd-server-xxxxxxxxx-xxxxx                       1/1     Running   0          2m
```

### Schritt 2: Argo CD UI aufrufen

```bash
# Admin-Passwort abrufen
ARGOCD_PW=$(kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d)
echo "Passwort: $ARGOCD_PW"

# UI-Port weiterleiten
kubectl port-forward svc/argocd-server -n argocd 8080:443 &
```

Öffne im Browser: **https://localhost:8080**
Zertifikats-Warnung ignorieren (lokaler Test). Login: `admin` / Passwort von oben.

### Schritt 3: Argo CD CLI installieren (optional, empfohlen)

```bash
# macOS
brew install argocd

# Login
argocd login localhost:8080 --insecure \
  --username admin \
  --password "$ARGOCD_PW"
```

Erwartete Ausgabe:
```text
'admin:login' logged in successfully
```

### Schritt 4: Git-Repository mit Manifest vorbereiten

Lege in deinem eigenen Git-Repository diese Struktur an:

```
k8s/
└── nginx-deployment.yaml
```

Inhalt von `k8s/nginx-deployment.yaml`:

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

Pushe die Datei:
```bash
git add k8s/nginx-deployment.yaml
git commit -m "add: initial nginx deployment for gitops demo"
git push
```

### Schritt 5: Argo CD Application erstellen

Passe in `manifests/argocd-application.yaml` die `repoURL` auf dein Repository an, dann:

```bash
kubectl apply -f manifests/argocd-application.yaml
```

Erwartete Ausgabe:
```text
application.argoproj.io/gitops-demo created
```

```bash
# Status prüfen
kubectl get application -n argocd
```

Erwartete Ausgabe:
```text
NAME          SYNC STATUS   HEALTH STATUS
gitops-demo   Synced        Healthy
```

### Schritt 6: GitOps-Workflow erleben

Ändere in deinem Git-Repository `replicas: 2` auf `replicas: 3` und pushe:

```bash
# Im eigenen Repo:
# replicas: 3 setzen, dann:
git commit -am "update: scale nginx to 3 replicas"
git push
```

Argo CD synchronisiert nach ca. 3 Minuten automatisch. Oder sofort manuell:

```bash
argocd app sync gitops-demo
```

Erwartete Ausgabe:
```text
TIMESTAMP  GROUP  KIND        NAMESPACE  NAME          STATUS  HEALTH   HOOK  MESSAGE
...        apps   Deployment  default    gitops-nginx  Synced  Healthy        deployment.apps/gitops-nginx configured
```

```bash
# Pods prüfen – jetzt 3 statt 2
kubectl get pods -l app=gitops-nginx
```

Erwartete Ausgabe:
```text
NAME                            READY   STATUS    RESTARTS   AGE
gitops-nginx-xxxxxxxxx-aaaaa    1/1     Running   0          2m
gitops-nginx-xxxxxxxxx-bbbbb    1/1     Running   0          2m
gitops-nginx-xxxxxxxxx-ccccc    1/1     Running   0          20s
```

> [!TIP]
> Teste Self-Heal: Lösche manuell das Deployment (`kubectl delete deployment gitops-nginx`). Argo CD erkennt die Abweichung und stellt es automatisch wieder her.

## Validierung

```bash
# Application-Übersicht
kubectl get application -n argocd

# Sync-Historie
argocd app history gitops-demo

# Deployed Pods
kubectl get pods -l app=gitops-nginx
kubectl get deployment gitops-nginx
```

```bash
# Argo CD Server-Logs (für Debugging)
kubectl logs -n argocd deployment/argocd-server --tail=20
```

## Cleanup

```bash
# Argo CD Application löschen
kubectl delete -f manifests/argocd-application.yaml

# Durch Argo CD erstelltes Deployment löschen
kubectl delete deployment gitops-nginx --ignore-not-found

# Argo CD Namespace löschen
kubectl delete namespace argocd

# Port-Forward beenden
kill %1 2>/dev/null
```

> [!CAUTION]
> `kubectl delete namespace argocd` löscht Argo CD vollständig inklusive aller konfigurierten Applications und Credentials. Nur ausführen, wenn das beabsichtigt ist.

## Erweiterungsaufgabe

1. **Self-Heal testen:** Lösche das Deployment manuell und beobachte, wie Argo CD es innerhalb von Sekunden wiederherstellt (Auto-Sync und selfHeal müssen aktiv sein).
2. **Zweite Application:** Erstelle eine Application, die auf einen anderen Pfad im selben Repo zeigt (z.B. `k8s-v2/`).
3. **App-of-Apps Pattern:** Erstelle eine Root-Application, die im Repo andere Application-Manifeste enthält und verwaltet.
