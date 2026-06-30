# Modul 11 – Observability

## Ziel des Moduls

Nach diesem Modul verstehst du die drei Säulen der Observability in Kubernetes: Metrics, Logs und Traces. Du kannst den Gesundheitszustand eines Pods gezielt prüfen, Probleme eingrenzen und weißt, welche Tools du wann einsetzt.

## Warum ist das wichtig?

In einem laufenden Kubernetes-Cluster passieren ständig Dinge: Pods crashen, Probes schlagen fehl, Ressourcen erschöpfen sich. Ohne Observability erkennst du diese Probleme erst, wenn Nutzer sich beschweren. Mit den richtigen Werkzeugen und Health Checks erkennst du Probleme frühzeitig – oder vermeidest sie ganz.

> [!IMPORTANT]
> Observability ist kein "Nice-to-have". In produktiven Clustern ist es eine Grundvoraussetzung. Wer keine Probes, keine Metrics und kein Logging hat, betreibt Kubernetes blind.

## Kernkonzepte

- **Metrics:** Numerische Messwerte über Zeit. Beispiele: CPU-Auslastung in %, Memory-Verbrauch in MB, Anzahl HTTP-Requests pro Sekunde. Standard-Tool: Prometheus.
- **Logs:** Textueller Output von Anwendungen und Kubernetes-Komponenten (stdout/stderr). kubectl-basiert für einfache Fälle, Log-Aggregatoren (Loki, EFK) für Produktion.
- **Traces:** Verteilte Ablaufverfolgung von Requests über mehrere Services. Zeigt, wo Zeit verloren geht. Standard-Tool: OpenTelemetry / Jaeger.
- **Events:** Kubernetes-interne Ereignisse (Pod gestartet, Probe fehlgeschlagen, OOMKilled). Kurzlebig, sehr nützlich für Debugging.
- **Probes:** Health Checks, die Kubernetes direkt ausführt – ohne externe Monitoring-Lösung.
- **Metrics Server:** Leichtgewichtiges Kubernetes-Add-on, das kurzfristige CPU/Memory-Metriken bereitstellt. Basis für `kubectl top` und HPA.

## Die drei Säulen der Observability

```
Observability
├── Metrics     → "Was passiert gerade?" (Zahlen, Graphen, Alerts)
│                  Tool: Prometheus + Grafana
│
├── Logs        → "Was genau ist passiert?" (Texte, Zeitstempel)
│                  Tool: kubectl logs, Loki, EFK Stack
│
└── Traces      → "Wie verhält sich das System als Ganzes?" (Request-Flow)
                   Tool: OpenTelemetry, Jaeger
```

## Kubernetes Health Probes im Detail

Probes sind der eingebaute Health-Check-Mechanismus von Kubernetes. Sie laufen in jedem Pod, ohne externe Tools.

### Liveness Probe

**Frage:** Lebt der Container noch?  
**Fehlschlag:** Container wird neu gestartet.

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 15   # Warte 15s nach Start
  periodSeconds: 10          # Prüfe alle 10s
  failureThreshold: 3        # Starte nach 3 Fehlschlägen neu
```

> [!WARNING]
> `initialDelaySeconds` zu kurz setzen ist ein häufiger Fehler. Wenn die App länger zum Starten braucht als der Delay, schlägt die Probe fehl → Container-Neustart → CrashLoopBackOff.

### Readiness Probe

**Frage:** Ist der Container bereit, Traffic anzunehmen?  
**Fehlschlag:** Pod wird aus den Service-Endpoints entfernt (kein Traffic mehr).

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
  failureThreshold: 3
```

### Startup Probe

**Frage:** Hat der Container erfolgreich gestartet?  
**Zweck:** Gibt langsam startenden Apps (z.B. JVM) Zeit, bevor Liveness eingreift.

```yaml
startupProbe:
  httpGet:
    path: /healthz
    port: 8080
  failureThreshold: 30     # Gibt der App bis zu 300s (30 × 10s) zum Starten
  periodSeconds: 10
```

### Unterschied Liveness vs. Readiness

| Merkmal | Liveness | Readiness |
|---------|----------|-----------|
| Fehlschlag führt zu | Container-Neustart | Aus Endpoints entfernen |
| Wann nutzen | Deadlocks, eingefrorene Prozesse | App noch nicht bereit (z.B. warmup) |
| Traffic-Auswirkung | Keine sofortige | Sofort kein Traffic mehr |

### Probe-Typen

```yaml
# HTTP GET (häufigste Variante)
httpGet:
  path: /healthz
  port: 8080

# TCP Socket (für nicht-HTTP-Services)
tcpSocket:
  port: 5432

# Befehl im Container ausführen (Exit Code 0 = gesund)
exec:
  command:
  - sh
  - -c
  - "redis-cli ping | grep PONG"
```

## kubectl-basiertes Monitoring

### kubectl top

Voraussetzung: Metrics Server muss installiert sein.

```bash
# Node-Ressourcen
kubectl top nodes
```

Ausgabe:
```text
NAME                         CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
k8s-lernpfad-control-plane   182m         2%     891Mi           23%
```

```bash
# Pod-Ressourcen im aktuellen Namespace
kubectl top pods

# Alle Namespaces, nach Memory sortiert
kubectl top pods -A --sort-by=memory
```

### Logs

```bash
# Pod-Logs anzeigen
kubectl logs <pod-name>

# Logs in Echtzeit folgen
kubectl logs <pod-name> -f

# Logs des vorherigen Container-Runs (bei CrashLoopBackOff!)
kubectl logs <pod-name> --previous

# Logs mehrerer Pods über Label
kubectl logs -l app=nginx --tail=50

# Spezifischen Container in Multi-Container-Pod
kubectl logs <pod-name> -c <container-name>
```

### Events

Events zeigen, was Kubernetes intern gemacht hat – oft die schnellste Diagnose.

```bash
# Events nach Zeit sortiert
kubectl get events --sort-by=.lastTimestamp

# Events in allen Namespaces
kubectl get events -A --sort-by=.lastTimestamp

# Events für einen bestimmten Pod (am Ende von describe)
kubectl describe pod <pod-name>
```

## Die 5 Diagnose-Fragen für jeden Pod

Wenn etwas nicht stimmt, beantworte diese Fragen der Reihe nach:

### 1. Ist der Pod gesund?
```bash
kubectl get pod <name>
# STATUS: Running, Pending, CrashLoopBackOff, OOMKilled, ...
```

### 2. Wird Traffic angenommen?
```bash
# Ist der Pod in den Service-Endpoints?
kubectl get endpoints <service-name>

# Ist die Readiness Probe aktiv?
kubectl describe pod <name> | grep -A5 "Readiness:"
```

### 3. Hat der Pod genug CPU und RAM?
```bash
kubectl top pod <name>
kubectl describe pod <name> | grep -A5 "Limits:"
```

Wenn Memory-Verbrauch nahe am Limit: OOMKilled droht.

### 4. Was sagen die Events?
```bash
kubectl describe pod <name>
# Scrolle zum Ende – Events zeigen Fehler und Warnungen
```

### 5. Was sagen die Logs?
```bash
kubectl logs <name>
kubectl logs <name> --previous  # bei CrashLoopBackOff
```

## Prometheus & Grafana (Ausblick)

Für produktive Observability braucht man Langzeit-Metriken und Dashboards. Der Standard im CNCF-Ökosystem:

**Prometheus:** Scrapet Metriken von Endpoints (`/metrics`) und speichert sie als Zeitreihen. Ermöglicht Alerting.

**Grafana:** Visualisiert Metriken aus Prometheus (und anderen Quellen) als Dashboards.

```bash
# Installation mit Helm (kube-prometheus-stack = Prometheus + Grafana + Alertmanager)
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=lernpfad123

# Grafana öffnen
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
# URL: http://localhost:3000 (admin / lernpfad123)
```

> [!TIP]
> Im Grafana-Dashboard: "Kubernetes / Compute Resources / Namespace (Pods)" zeigt CPU und Memory aller Pods auf einen Blick.

## Typische Fehler

- **Liveness Probe zu aggressiv:** `initialDelaySeconds` zu klein → App startet noch → Probe schlägt fehl → CrashLoopBackOff.
- **Readiness und Liveness verwechseln:** Liveness startet Container neu. Readiness entfernt ihn aus dem Load Balancer. Beide gleichzeitig falsch zu konfigurieren kann zu kaskadierten Ausfällen führen.
- **kubectl top zeigt Fehler:** Metrics Server nicht installiert oder noch nicht bereit. `kubectl get pods -n kube-system | grep metrics` prüfen.
- **Logs sind leer:** Der Container schreibt in stderr/stdout, aber du fragst den falschen Container oder Pod ab. `-c <containername>` und `--previous` prüfen.

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] den Unterschied zwischen Liveness, Readiness und Startup Probe erklären kannst
- [ ] `kubectl top pods` und `kubectl get events` für die Diagnose nutzt
- [ ] `kubectl logs --previous` bei einem CrashLoopBackOff-Pod angewendet hast
- [ ] eine Readiness Probe konfiguriert hast und beobachtet hast wie der Service-Endpoint reagiert
- [ ] die Checkpoint-Fragen beantworten kannst

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Liveness und Readiness Probe?
- [ ] Was passiert, wenn eine Liveness Probe 3× hintereinander fehlschlägt?
- [ ] Was passiert, wenn eine Readiness Probe fehlschlägt?
- [ ] Wie zeigst du Logs des Container-Runs *vor* dem letzten Crash an?
- [ ] Was ist Prometheus, und was unterscheidet es vom Metrics Server?
- [ ] Wie findest du heraus, warum ein Pod im Status `Pending` ist?

## Weiterführende Links

- [Liveness, Readiness und Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server)
- [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator)
- [Grafana](https://grafana.com/docs/)
- [OpenTelemetry](https://opentelemetry.io/docs/)
- [Kubernetes Events](https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/event-v1/)
