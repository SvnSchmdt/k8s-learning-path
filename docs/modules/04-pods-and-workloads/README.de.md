# Modul 04 – Pods & Workloads

## Ziel des Moduls

Nach diesem Modul verstehst du Pods, Deployments, ReplicaSets und weitere Workload-Typen. Du kannst Deployments erstellen, skalieren, aktualisieren und Rollbacks durchführen.

## Warum ist das wichtig?

Pods und Deployments sind das Herzstück von Kubernetes. Fast jede Anwendung läuft als Deployment. Wer Deployments, ReplicaSets und Rolling Updates versteht, kann Anwendungen zuverlässig betreiben.

## Kernkonzepte

- **Pod:** Die kleinste deploybare Einheit in Kubernetes. Enthält einen oder mehrere eng gekoppelte Container, die sich Netzwerk und Storage teilen. Pods sind kurzlebig – sie werden ersetzt, nicht repariert.
- **ReplicaSet:** Stellt sicher, dass immer eine bestimmte Anzahl identischer Pod-Kopien läuft. Wird meistens nicht direkt erstellt, sondern vom Deployment verwaltet.
- **Deployment:** Die bevorzugte Art, zustandslose Anwendungen zu betreiben. Verwaltet ReplicaSets und ermöglicht Rolling Updates und Rollbacks.
- **DaemonSet:** Sorgt dafür, dass auf jedem (oder bestimmten) Nodes genau ein Pod läuft. Typisch für Log-Collector, Monitoring-Agents.
- **StatefulSet:** Für zustandsbehaftete Anwendungen (z.B. Datenbanken). Pods haben stabile Identitäten und persistenten Storage.
- **Job / CronJob:** Für einmalige oder regelmäßige Aufgaben, die einmal laufen und dann beendet sind.

## Labels und Selectors

Labels sind Key-Value-Paare an Kubernetes-Ressourcen. Selectors filtern Ressourcen nach Labels. Services und Deployments verwenden Selectors, um Pods zu finden.

```yaml
# Labels an einem Pod
metadata:
  labels:
    app: nginx
    version: v1
    umgebung: produktion
```

## Praxisaufgabe

### Deployment erstellen und verwalten

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27-alpine
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

### Deployment skalieren

```bash
# Imperativ
kubectl scale deployment nginx-deployment --replicas=5

# Deklarativ: replicas in YAML ändern und erneut apply
kubectl apply -f deployment.yaml
```

### Rolling Update

```bash
# Image-Version aktualisieren
kubectl set image deployment/nginx-deployment nginx=nginx:1.28-alpine

# Update-Status beobachten
kubectl rollout status deployment/nginx-deployment

# Update-Historie anzeigen
kubectl rollout history deployment/nginx-deployment
```

### Rollback

```bash
# Zur vorherigen Version zurück
kubectl rollout undo deployment/nginx-deployment

# Zu einer bestimmten Revision zurück
kubectl rollout undo deployment/nginx-deployment --to-revision=1
```

## Beispiel-Kommandos

```bash
# Deployment-Details anzeigen
kubectl describe deployment nginx-deployment

# ReplicaSet anzeigen
kubectl get rs

# Pods mit Labels anzeigen
kubectl get pods --show-labels

# Pods nach Label filtern
kubectl get pods -l app=nginx
```

## Typische Fehler

- **Pod direkt erstellen:** Pods ohne Deployment zu erstellen bedeutet, dass sie bei Ausfall nicht neu gestartet werden.
- **Selector stimmt nicht mit Template-Labels überein:** Deployment schlägt fehl. Labels in `spec.selector.matchLabels` müssen exakt mit `spec.template.metadata.labels` übereinstimmen.
- **`latest`-Tag nutzen:** Kubernetes cached Images. `latest` kann zu inkonsistenten Deployments führen. Spezifische Versionen verwenden.
- **Deployment pausiert aus Versehen:** `kubectl rollout pause deployment/<name>` hält das Update an. Mit `kubectl rollout resume` fortsetzen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Pod, ReplicaSet und Deployment?
- [ ] Was passiert, wenn du einen Pod löscht, der Teil eines Deployments ist?
- [ ] Wie führst du ein Rolling Update durch, ohne Downtime zu erzeugen?
- [ ] Wann würdest du ein DaemonSet statt einem Deployment verwenden?
- [ ] Was ist der Unterschied zwischen `kubectl scale` und dem Ändern von `replicas` in der YAML-Datei?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] den Unterschied zwischen Pod, ReplicaSet und Deployment erklären kannst
- [ ] ein Deployment mit 3 Replicas erstellt und auf 5 skaliert hast
- [ ] ein Rolling Update durchgeführt und den Status mit `kubectl rollout status` beobachtet hast
- [ ] einen Rollback erfolgreich durchgeführt hast
- [ ] weißt, wann DaemonSet oder StatefulSet sinnvoller als Deployment ist
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)
- [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
- [Labels und Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
