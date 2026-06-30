# Modul 03 – kubectl & Cluster-Zugriff

## Ziel des Moduls

Nach diesem Modul beherrschst du kubectl als zentrales Werkzeug für die Arbeit mit Kubernetes. Du verstehst kubeconfig, Contexts und kannst effizient mit Ressourcen im Cluster arbeiten.

## Warum ist das wichtig?

kubectl ist dein Hauptwerkzeug für alles in Kubernetes. Wer kubectl gut kennt, arbeitet schnell und sicher – auch wenn grafische UIs vorhanden sind. Im CKA-Examen ist kubectl das einzige Werkzeug, das zählt.

## Kernkonzepte

- **kubectl:** Kubernetes Command Line Interface. Kommuniziert mit dem API Server über HTTPS.
- **kubeconfig:** Konfigurationsdatei (Standard: `~/.kube/config`), die Cluster, User und Contexts definiert.
- **Context:** Kombination aus Cluster + User + Namespace. Wechseln zwischen Contexts = wechseln zwischen Clustern/Umgebungen.
- **Ressource-Typen:** Kubernetes kennt viele Ressourcentypen (Pods, Deployments, Services...). `kubectl api-resources` listet alle auf.
- **Output-Formate:** kubectl kann Ausgaben als Table, JSON, YAML oder benutzerdefiniert (jsonpath, go-template) ausgeben.

## Praxisaufgabe

### kubeconfig verstehen

```bash
# kubeconfig anzeigen
kubectl config view

# Aktuellen Context anzeigen
kubectl config current-context

# Alle Contexts auflisten
kubectl config get-contexts

# Zu einem anderen Context wechseln
kubectl config use-context kind-k8s-lernpfad
```

### Mit Ressourcen arbeiten

```bash
# Alle Ressourcentypen anzeigen
kubectl api-resources

# Ressourcen im Cluster anzeigen
kubectl get pods
kubectl get pods -A          # alle Namespaces
kubectl get pods -n kube-system  # spezifischer Namespace

# Details einer Ressource
kubectl describe pod <name>

# YAML-Ausgabe
kubectl get pod <name> -o yaml

# JSON-Ausgabe, Feld extrahieren
kubectl get pod <name> -o jsonpath='{.status.podIP}'
```

### Ressourcen erstellen und löschen

```bash
# Aus YAML-Datei erstellen
kubectl apply -f manifest.yaml

# Löschen
kubectl delete -f manifest.yaml
kubectl delete pod <name>

# Label setzen
kubectl label pod <name> umgebung=test

# Annotation setzen
kubectl annotate pod <name> info="test-pod"
```

### Hilfreiche Shortcuts

```bash
# Ressourcentypen haben Kurzformen
kubectl get po      # pods
kubectl get svc     # services
kubectl get deploy  # deployments
kubectl get ns      # namespaces
kubectl get cm      # configmaps

# Mehrere Ressourcen auf einmal
kubectl get pods,services

# Watch-Modus
kubectl get pods -w
```

## Beispiel: kubeconfig manuell verstehen

```yaml
# Ausschnitt aus ~/.kube/config
apiVersion: v1
clusters:
- cluster:
    server: https://127.0.0.1:6443
  name: kind-k8s-lernpfad
contexts:
- context:
    cluster: kind-k8s-lernpfad
    user: kind-k8s-lernpfad
  name: kind-k8s-lernpfad
current-context: kind-k8s-lernpfad
```

## Typische Fehler

- **Falscher Context:** Du arbeitest am falschen Cluster. Immer `kubectl config current-context` prüfen.
- **Falscher Namespace:** Ressource nicht gefunden, weil im falschen Namespace gesucht. `-n <namespace>` oder `-A` verwenden.
- **`kubectl create` vs. `kubectl apply`:** `create` schlägt fehl wenn Ressource existiert. `apply` ist idempotent – der bevorzugte Weg.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist eine kubeconfig und wo liegt sie standardmäßig?
- [ ] Was ist ein Context und wie wechselst du zwischen Contexts?
- [ ] Wie zeigst du alle Pods in allen Namespaces an?
- [ ] Was ist der Unterschied zwischen `kubectl create` und `kubectl apply`?
- [ ] Wie extrahierst du mit jsonpath die IP-Adresse eines Pods?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] zwischen Cluster-Kontexten gewechselt hast (`kubectl config use-context`)
- [ ] Pods in allen Namespaces angezeigt hast (`kubectl get pods -A`)
- [ ] mit `-o jsonpath` einen spezifischen Feldwert extrahiert hast
- [ ] den Unterschied zwischen `kubectl create` und `kubectl apply` erklären kannst
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [kubectl Übersicht](https://kubernetes.io/docs/reference/kubectl/)
- [kubectl Cheat Sheet (offiziell)](https://kubernetes.io/docs/reference/kubectl/quick-reference/)
- [kubeconfig verwalten](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
- [Lokales Cheat Sheet](../../resources/cheat-sheet.md)
