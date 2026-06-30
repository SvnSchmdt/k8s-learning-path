# Modul 13 – GitOps mit Argo CD

## Ziel des Moduls

Nach diesem Modul verstehst du das GitOps-Prinzip und kannst Argo CD als GitOps-Controller für automatisierte Kubernetes-Deployments einsetzen.

## Warum ist das wichtig?

Manuelles Deployen mit `kubectl apply` ist fehleranfällig und nicht auditierbar. GitOps macht Git zur "Single Source of Truth" für deinen Cluster-Zustand. Jede Änderung ist versioniert, nachvollziehbar und kann rückgängig gemacht werden.

## Kernkonzepte

- **GitOps:** Ein Betriebsmodell, bei dem der gewünschte Zustand eines Systems in einem Git-Repository deklariert wird. Automatisierte Prozesse gleichen den aktuellen Zustand mit dem deklarativen Soll-Zustand ab.
- **Pull-based Deployment:** Argo CD läuft im Cluster und *pullt* Änderungen aus Git – statt dass ein externes System *pusht*. Das minimiert externe Zugriffe auf den Cluster.
- **Application (Argo CD):** Ein Argo CD-Objekt, das definiert: Welches Git-Repo? Welcher Pfad? Welches Cluster? Welcher Namespace?
- **Sync:** Argo CD erkennt Abweichungen zwischen Git und Cluster und "synct" den Cluster auf den Git-Zustand.
- **Health Status:** Argo CD prüft, ob Ressourcen nicht nur deployed, sondern auch gesund sind.
- **App-of-Apps Pattern:** Eine Argo CD Application, die andere Applications verwaltet. Skaliert GitOps für viele Services.

## Push-based vs. Pull-based Deployment

```
Push-based (traditionell):
CI/CD System → kubectl apply → Cluster

Pull-based (GitOps):
Developer → git push → Git Repository
                           ↑ Argo CD pollt
                      Argo CD → sync → Cluster
```

## Praxisaufgabe

### Argo CD installieren

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Warten bis Argo CD läuft
kubectl wait --for=condition=available deployment/argocd-server -n argocd --timeout=120s

# Admin-Passwort abrufen
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo

# UI aufrufen
kubectl port-forward svc/argocd-server -n argocd 8080:443
# -> https://localhost:8080 (Zertifikat-Warnung ignorieren, admin / Passwort oben)
```

### Erste Application erstellen (deklarativ)

```yaml
# argocd-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<dein-user>/<dein-repo>.git
    targetRevision: HEAD
    path: labs/01-nginx-deployment/manifests
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

```bash
kubectl apply -f argocd-application.yaml
```

### argocd CLI

```bash
# CLI installieren (macOS)
brew install argocd

# Login
argocd login localhost:8080 --insecure --username admin --password <passwort>

# Apps anzeigen
argocd app list

# App sync
argocd app sync nginx-app

# App-Status
argocd app get nginx-app
```

## GitOps-Workflow

```
1. YAML-Manifest ändern
2. git commit & git push
3. Argo CD erkennt Änderung (Polling alle 3 Minuten oder via Webhook)
4. Argo CD synct den Cluster auf den neuen Zustand
5. Health-Status prüfen
```

## Typische Fehler

- **App ist "OutOfSync":** Git und Cluster unterscheiden sich. `argocd app sync` oder `kubectl apply` manuell auslösen.
- **Self-Heal loop:** Wenn manuell `kubectl apply` gemacht wird, korrigiert Argo CD es zurück. Das ist Absicht – GitOps bedeutet, dass Git die Wahrheit ist.
- **Repo-Zugangsdaten:** Privates Repo braucht Credentials in Argo CD. In der UI oder per CLI konfigurieren.
- **Namespace existiert nicht:** Das Ziel-Namespace muss existieren oder `CreateNamespace=true` muss gesetzt sein.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Push-based und Pull-based Deployment?
- [ ] Was ist die "Single Source of Truth" in GitOps?
- [ ] Was passiert, wenn du manuell `kubectl delete deployment` ausführst, und Argo CD ist aktiv?
- [ ] Was bedeutet "Sync" in Argo CD?
- [ ] Was ist der Vorteil von GitOps gegenüber manuellem `kubectl apply`?

## Weiterführende Links

- [Argo CD Dokumentation](https://argo-cd.readthedocs.io/)
- [GitOps Prinzipien (OpenGitOps)](https://opengitops.dev/)
- [CNCF GitOps Whitepaper](https://github.com/open-gitops/documents)
- [App-of-Apps Pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
