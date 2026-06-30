# Lab 06 – Grundlegendes Monitoring

## Ziel

Du installierst den Metrics Server, lernst `kubectl top` kennen und installierst optional Prometheus & Grafana für echte Metrics-Visualisierung.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert
- [ ] Helm installiert (für optionalen Prometheus-Teil)

## Schritt-für-Schritt

### Schritt 1: Metrics Server installieren

```bash
kubectl apply -f manifests/metrics-server.yaml
```

Alternativ direkt aus dem Release:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Für kind: kubelet-insecure-tls Patch ist notwendig:

```bash
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'

# Warten
kubectl rollout status deployment/metrics-server -n kube-system
```

### Schritt 2: Test-Workload erstellen

```bash
kubectl apply -f manifests/test-deployment.yaml
kubectl get pods
```

### Schritt 3: kubectl top verwenden

```bash
# Node-Ressourcen anzeigen
kubectl top nodes

# Pod-Ressourcen anzeigen
kubectl top pods
kubectl top pods --sort-by=memory
kubectl top pods -n kube-system
```

### Schritt 4: Events und Logs überwachen

```bash
# Events nach Zeit sortiert
kubectl get events --sort-by=.lastTimestamp

# Logs folgen
kubectl logs -l app=demo-app -f

# Logs des vorherigen Containers (bei Crash)
kubectl logs <pod-name> --previous
```

### Schritt 5 (Optional): Prometheus & Grafana mit Helm

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=lernpfad123 \
  --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false

kubectl get pods -n monitoring
```

```bash
# Grafana öffnen
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
# URL: http://localhost:3000
# Login: admin / lernpfad123
```

### Schritt 6: Liveness und Readiness Probes beobachten

```bash
# Pod mit Probe starten
kubectl apply -f manifests/pod-with-probe.yaml

# Probe-Status beobachten
kubectl describe pod probe-demo
# Suche nach: Liveness, Readiness
```

## Validierung

```bash
# Metrics Server läuft
kubectl get deployment metrics-server -n kube-system

# kubectl top funktioniert
kubectl top nodes
kubectl top pods

# Prometheus (falls installiert)
kubectl get pods -n monitoring
```

## Cleanup

```bash
kubectl delete -f manifests/

# Falls Prometheus installiert
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

## Erweiterungsaufgabe

1. Erstelle einen Pod, der absichtlich viel CPU verbraucht (`stress-ng` oder `dd`), und beobachte `kubectl top pods`.
2. Konfiguriere eine Readiness Probe, die absichtlich fehlschlägt, und beobachte was mit den Service-Endpoints passiert.
3. Suche in Grafana nach dem Dashboard "Kubernetes / Compute Resources / Namespace (Pods)".
