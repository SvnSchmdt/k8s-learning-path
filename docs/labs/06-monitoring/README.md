# Lab 06 – Monitoring & Probes

## What you'll build

An observability setup with Metrics Server, a Pod with configured liveness and readiness probes, and a simulation of a failing probe. You will observe how Kubernetes responds to probe failures and learn to read Events.

**Kubernetes objects used:** Pod, Service, Deployment (for Metrics Server)

```
kubectl top ──► Metrics Server ──► kubelet (on each node)
                                        │
                                        └──► Pod metrics (CPU/Memory)

Kubernetes ──► livenessProbe ──► Container (HTTP GET /)
           ──► readinessProbe ──► Container (HTTP GET /ready)
```

## Goal

Metrics Server running, Pod with probes deployed, and a broken probe scenario diagnosed using `kubectl describe` and Events.

## Prerequisites

- [ ] kind cluster running
- [ ] kubectl configured

## Step-by-Step

### Step 1: Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Patch for kind (disables TLS verification — only for local clusters)
kubectl patch deployment metrics-server -n kube-system \
  --type json \
  -p '[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'

kubectl wait --namespace kube-system \
  --for=condition=available deployment/metrics-server \
  --timeout=90s
```

Wait ~60 seconds after the Metrics Server is available before `kubectl top` returns data.

### Step 2: Deploy a test workload

```bash
kubectl apply -f manifests/test-deployment.yaml
kubectl get pods -w
```

Expected output:
```text
NAME                    READY   STATUS    RESTARTS   AGE
nginx-test-xxx-yyy      1/1     Running   0          30s
```

### Step 3: Check resource metrics

```bash
kubectl top nodes
```

Expected output:
```text
NAME                         CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
k8s-learning-control-plane   150m         3%     512Mi           12%
```

```bash
kubectl top pods
```

Expected output:
```text
NAME                    CPU(cores)   MEMORY(bytes)
nginx-test-xxx-yyy      1m           4Mi
```

### Step 4: Deploy a Pod with health probes

```bash
kubectl apply -f manifests/pod-with-probe.yaml
kubectl apply -f manifests/probe-service.yaml
kubectl get pod probe-demo
```

Expected output:
```text
NAME         READY   STATUS    RESTARTS   AGE
probe-demo   1/1     Running   0          15s
```

Inspect the probe status:
```bash
kubectl describe pod probe-demo | grep -A10 "Liveness\|Readiness"
```

### Step 5: Simulate a broken probe

Create a Pod with a liveness probe pointing to a non-existent endpoint:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: broken-probe
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    livenessProbe:
      httpGet:
        path: /this-does-not-exist
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
      failureThreshold: 2
EOF
```

Wait ~30 seconds, then observe:
```bash
kubectl get pod broken-probe
```

Expected output (RESTARTS counter increasing):
```text
NAME           READY   STATUS    RESTARTS   AGE
broken-probe   1/1     Running   2          60s
```

```bash
kubectl describe pod broken-probe
```

Expected output (in Events section):
```text
Warning  Unhealthy  Liveness probe failed: HTTP probe failed with statuscode: 404
Warning  Killing    Container broken-probe failed liveness probe, will be restarted
```

> **Note:** READY shows `1/1` because the container is running — but the liveness probe fails, causing restarts. This is different from readiness failure (which would show `0/1`).

### Step 6: Check Events cluster-wide

```bash
kubectl get events --sort-by='.lastTimestamp' | tail -10
```

## Validation

```bash
# Metrics Server provides data
kubectl top nodes
kubectl top pods

# Probe-demo is healthy
kubectl get pod probe-demo
# READY: 1/1, RESTARTS: 0

# Broken probe is restarting
kubectl get pod broken-probe
# RESTARTS: > 0
```

## Cleanup

```bash
kubectl delete -f manifests/
kubectl delete pod broken-probe
```

## Extension Task

Install the full Prometheus + Grafana monitoring stack using Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring --create-namespace \
  --set grafana.adminPassword=admin

kubectl port-forward -n monitoring service/monitoring-grafana 3000:80
# Open http://localhost:3000 (admin/admin)
```

Explore the built-in Kubernetes dashboards in Grafana.
