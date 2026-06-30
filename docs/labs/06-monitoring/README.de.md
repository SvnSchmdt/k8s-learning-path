# Lab 06 – Observability & Health Checks

## Was du baust

Du baust ein Szenario, das zeigt wie Kubernetes Health Checks (Probes) im Alltag funktionieren. Ein Deployment hat Readiness- und Liveness-Proben. Du beobachtest wie Kubernetes auf einen fehlschlagenden Health Check reagiert – und wie du Probleme mit kubectl diagnostizierst.

**Kubernetes-Objekte:** Deployment, Service, Pod (mit Probes), Metrics Server

```text
kubectl top ──► Metrics Server ──► Node/Pod Metriken
                                           │
kubectl describe ──► Events ──────────────┤
                                           │
kubectl logs ──► Container Logs ──────────┤
                                           ▼
                                   Pod: probe-demo
                                       ├── livenessProbe (GET /)
                                       └── readinessProbe (GET /)
                                           │
Service: probe-svc ──► Endpoints (nur Ready-Pods)
```

## Ziel

Du hast den Metrics Server installiert, `kubectl top` genutzt, eine Readiness Probe absichtlich zum Fehlschlagen gebracht und beobachtet wie Kubernetes den Pod aus den Service-Endpoints entfernt. Diagnose mit `kubectl describe`, `kubectl logs` und `kubectl get events` verstanden.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert
- [ ] Helm installiert (für optionalen Prometheus-Teil)

## Schritt-für-Schritt

### Schritt 1: Metrics Server installieren

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Für kind ist ein zusätzlicher Patch nötig (kubelet-TLS umgehen):

```bash
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'

# Auf bereit warten
kubectl rollout status deployment/metrics-server -n kube-system --timeout=90s
```

Erwartete Ausgabe:
```text
deployment "metrics-server" successfully rolled out
```

```bash
# Test – dauert ca. 60s bis erste Metriken sichtbar sind
kubectl top nodes
```

Erwartete Ausgabe:
```text
NAME                         CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
k8s-lernpfad-control-plane   150m         2%     700Mi           18%
```

### Schritt 2: Test-Workload mit Probes starten

```bash
kubectl apply -f manifests/test-deployment.yaml
kubectl apply -f manifests/pod-with-probe.yaml
```

```bash
kubectl get pods
```

Erwartete Ausgabe:
```text
NAME                         READY   STATUS    RESTARTS   AGE
demo-app-xxxxxxxxx-aaaaa     1/1     Running   0          30s
demo-app-xxxxxxxxx-bbbbb     1/1     Running   0          30s
probe-demo                   1/1     Running   0          30s
```

### Schritt 3: Probe-Status beobachten

```bash
kubectl describe pod probe-demo
```

Suche im Output nach diesem Abschnitt:

```text
Containers:
  nginx:
    ...
    Liveness:     http-get http://:80/ delay=5s timeout=1s period=10s #success=1 #failure=3
    Readiness:    http-get http://:80/ delay=3s timeout=1s period=5s  #success=1 #failure=3
```

### Schritt 4: kubectl top für Pods nutzen

```bash
# Pod-Ressourcenverbrauch (nach ~60s nach Metrics Server Start)
kubectl top pods
```

Erwartete Ausgabe:
```text
NAME                         CPU(cores)   MEMORY(bytes)
demo-app-xxxxxxxxx-aaaaa     1m           3Mi
demo-app-xxxxxxxxx-bbbbb     1m           3Mi
probe-demo                   1m           2Mi
```

```bash
# Sortiert nach Memory
kubectl top pods --sort-by=memory
```

### Schritt 5: Service erstellen und Endpoints beobachten

```bash
kubectl apply -f manifests/probe-service.yaml
kubectl get service probe-svc
kubectl get endpoints probe-svc
```

Erwartete Ausgabe (Pod ist Ready):
```text
NAME        ENDPOINTS          AGE
probe-svc   10.244.0.x:80      30s
```

### Schritt 6: Readiness Probe absichtlich zum Fehlschlagen bringen

Wir simulieren eine kaputte Readiness Probe, indem wir den nginx-Pod so konfigurieren, dass die Probe auf einen Pfad zeigt, der 404 zurückgibt:

```bash
# Direktes Patch des laufenden Pods (Simulation)
kubectl run broken-pod \
  --image=nginx:1.27-alpine \
  --restart=Never \
  --dry-run=client -o yaml | \
  kubectl apply -f -
```

Erstelle alternativ dieses Manifest und wende es an:

```yaml
# Temporäre Datei: /tmp/broken-probe.yaml
apiVersion: v1
kind: Pod
metadata:
  name: broken-probe
spec:
  containers:
  - name: nginx
    image: nginx:1.27-alpine
    readinessProbe:
      httpGet:
        path: /dieser-pfad-gibt-404
        port: 80
      initialDelaySeconds: 3
      periodSeconds: 5
      failureThreshold: 3
```

```bash
cat > /tmp/broken-probe.yaml <<'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: broken-probe
spec:
  containers:
  - name: nginx
    image: nginx:1.27-alpine
    readinessProbe:
      httpGet:
        path: /dieser-pfad-gibt-404
        port: 80
      initialDelaySeconds: 3
      periodSeconds: 5
      failureThreshold: 3
EOF

kubectl apply -f /tmp/broken-probe.yaml
```

```bash
# Beobachten (READY bleibt 0/1!)
kubectl get pod broken-probe
```

Erwartete Ausgabe:
```text
NAME           READY   STATUS    RESTARTS   AGE
broken-probe   0/1     Running   0          30s
```

> [!NOTE]
> Der Pod hat Status `Running` (Container läuft), aber READY zeigt `0/1`. Das bedeutet: der Container lebt, ist aber laut Readiness Probe noch nicht bereit für Traffic.

```bash
# Events des Pods prüfen
kubectl describe pod broken-probe
```

Im Events-Abschnitt (Ende der Ausgabe):

```text
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  40s                default-scheduler  Successfully assigned default/broken-probe to k8s-lernpfad-control-plane
  Normal   Pulled     38s                kubelet            Container image "nginx:1.27-alpine" already present on machine
  Normal   Created    38s                kubelet            Created container nginx
  Normal   Started    38s                kubelet            Started container nginx
  Warning  Unhealthy  8s (x4 over 23s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 404
```

### Schritt 7: Events des gesamten Clusters beobachten

```bash
kubectl get events --sort-by=.lastTimestamp | tail -20
```

Erwartete Ausgabe (Auszug):
```text
LAST SEEN   TYPE      REASON      OBJECT                MESSAGE
15s         Warning   Unhealthy   pod/broken-probe      Readiness probe failed: HTTP probe failed with statuscode: 404
```

### Schritt 8: Log-Befehle üben

```bash
# Aktuelle Logs eines Pods
kubectl logs probe-demo

# Logs verfolgen
kubectl logs probe-demo -f

# Logs der Demo-App (über Label)
kubectl logs -l app=demo-app --tail=10

# Logs aller Container in allen Namespaces (nur letzten 5 Zeilen)
kubectl logs -n kube-system -l component=kube-apiserver --tail=5
```

### Schritt 9 (Optional): Prometheus & Grafana mit Helm

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=lernpfad123 \
  --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false

kubectl wait --for=condition=available \
  deployment/monitoring-grafana \
  -n monitoring \
  --timeout=120s
```

```bash
# Grafana öffnen
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
# URL: http://localhost:3000 (admin / lernpfad123)
```

Dashboard "Kubernetes / Compute Resources / Namespace (Pods)" zeigt CPU und Memory aller Pods.

## Validierung

```bash
# Metrics Server läuft
kubectl get deployment metrics-server -n kube-system
```

Erwartete Ausgabe:
```text
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
metrics-server   1/1     1            1           5m
```

```bash
# kubectl top funktioniert
kubectl top nodes
kubectl top pods

# probe-demo ist Ready (Probes laufen korrekt)
kubectl get pod probe-demo

# broken-probe ist NICHT Ready (Probe fehlerhaft)
kubectl get pod broken-probe
```

Erwartete Ausgabe (beide Pods):
```text
NAME           READY   STATUS    RESTARTS   AGE
broken-probe   0/1     Running   0          3m
probe-demo     1/1     Running   0          5m
```

```bash
# Events zeigen Probe-Fehler
kubectl get events --sort-by=.lastTimestamp | grep Unhealthy
```

## Cleanup

```bash
# Lab-Ressourcen löschen
kubectl delete -f manifests/
kubectl delete pod broken-probe --ignore-not-found

# Falls Prometheus installiert
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

> [!NOTE]
> Der Metrics Server kann im Cluster bleiben – er schadet nicht und ist für spätere Labs (HPA in Modul 14) nützlich.

## Erweiterungsaufgabe

1. **OOMKill simulieren:** Starte einen Pod mit sehr niedrigem Memory-Limit (`4Mi`) und einem Prozess, der mehr Memory braucht. Beobachte `kubectl describe pod` und suche nach `OOMKilled`.
2. **Liveness Probe testen:** Konfiguriere eine Liveness Probe auf `/healthz` (gibt 404 zurück) mit `failureThreshold: 2`. Was passiert nach 2 Fehlschlägen?
3. **Grafana Dashboard:** Falls Prometheus installiert: Finde das Dashboard "Kubernetes / Compute Resources / Namespace (Pods)" und lese CPU- und Memory-Verbrauch für den `default` Namespace ab.
