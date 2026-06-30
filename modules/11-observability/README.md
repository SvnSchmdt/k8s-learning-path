# Modul 11 – Observability

## Ziel des Moduls

Nach diesem Modul verstehst du die drei Säulen der Observability (Metrics, Logs, Traces) und kannst grundlegende Monitoring- und Logging-Lösungen in Kubernetes einrichten.

## Warum ist das wichtig?

Ohne Observability bist du bei Problemen blind. Du merkst erst, dass ein Service ausgefallen ist, wenn Nutzer sich beschweren. Mit Metrics, Logs und Health Checks erkennst du Probleme frühzeitig und kannst sie systematisch debuggen.

## Kernkonzepte

- **Metrics:** Numerische Messwerte über Zeit (CPU, Memory, Request-Rate, Error-Rate). Prometheus ist der Standard für Kubernetes-Metrics.
- **Logs:** Textuelle Aufzeichnung von Ereignissen in Pods und Kubernetes-Komponenten. Zentralisiert mit einem Log-Aggregator (EFK, Loki).
- **Traces:** Verteilte Ablaufverfolgung über mehrere Services hinweg (OpenTelemetry, Jaeger).
- **Prometheus:** Open-Source-Monitoring-System, das Metrics von Targets scrapet. Standard im CNCF-Ökosystem.
- **Grafana:** Visualisierungstool für Metrics, Logs und Traces. Zeigt Dashboards.
- **Metrics Server:** Kubernetes-Add-on, das CPU/Memory-Metriken für `kubectl top` bereitstellt.
- **Probes:** Kubernetes-eingebaute Health Checks (liveness, readiness, startup).

## Die drei Säulen der Observability

```
Observability
├── Metrics     → "Was passiert gerade?" (Zahlen, Graphen)
├── Logs        → "Was genau ist passiert?" (Texte, Events)
└── Traces      → "Wie verhält sich das System als Ganzes?" (Request-Flows)
```

## Praxisaufgabe: kubectl-basiertes Monitoring

### Metrics Server installieren

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# In kind braucht man den --kubelet-insecure-tls Flag
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'

# Prüfen
kubectl top nodes
kubectl top pods
```

### Logs

```bash
# Pod-Logs
kubectl logs <pod-name>

# Logs folgen
kubectl logs <pod-name> -f

# Logs des vorherigen Containers (bei CrashLoopBackOff)
kubectl logs <pod-name> --previous

# Alle Container in einem Pod
kubectl logs <pod-name> --all-containers

# Mehrere Pods mit Label (mit stern-Tool)
# brew install stern
stern -l app=nginx
```

### Events überwachen

```bash
# Events nach Zeit sortiert
kubectl get events --sort-by=.lastTimestamp

# Events eines bestimmten Pods
kubectl describe pod <name>  # Events am Ende der Ausgabe
```

## Praxisaufgabe: Prometheus & Grafana mit Helm

```bash
# Prometheus Community Chart Repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Kube-Prometheus-Stack installieren (Prometheus + Grafana + Alertmanager)
helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=admin123

# Grafana UI aufrufen
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
# -> http://localhost:3000 (admin/admin123)
```

## Health Probes in Pods

```yaml
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

**Liveness Probe:** Ist der Container lebendig? Schlägt fehl → Container wird neu gestartet.  
**Readiness Probe:** Ist der Container bereit für Traffic? Schlägt fehl → Pod wird aus Service-Endpoints entfernt.

## Typische Fehler

- **Liveness Probe zu aggressiv:** Zu kurze `initialDelaySeconds` → App startet langsam, Probe schlägt fehl → Container wird neu gestartet → CrashLoopBackOff.
- **Readiness und Liveness verwechseln:** Readiness entfernt den Pod aus dem Load Balancer. Liveness startet den Container neu. Falscher Einsatz kann zu Ausfällen führen.
- **Keine Metrics installiert:** `kubectl top pods` gibt Fehler zurück. Metrics Server muss installiert sein.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Liveness und Readiness Probe?
- [ ] Was passiert, wenn eine Liveness Probe dauerhaft fehlschlägt?
- [ ] Wie zeigst du die Logs des letzten abgestürzten Containers an?
- [ ] Was ist Prometheus, und was macht es mit Metrics?
- [ ] Was ist der Unterschied zwischen Metrics, Logs und Traces?

## Weiterführende Links

- [Liveness und Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server)
- [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator)
- [Grafana](https://grafana.com/docs/)
- [OpenTelemetry](https://opentelemetry.io/docs/)
