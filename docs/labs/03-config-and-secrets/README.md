# Lab 03 – Config & Secrets

## What you'll build

A ConfigMap and a Secret, injected into a Pod — both as environment variables and as mounted files. You will verify the values inside the running container and observe the update behavior.

**Kubernetes objects used:** ConfigMap, Secret, Pod

```
ConfigMap (app-config)  ──env──┐
                               ├──► Pod (app-pod)
Secret (db-credentials) ──env──┘
         │
         └──volume──► /etc/config/ (mounted files)
```

## Goal

A running Pod that receives configuration from a ConfigMap and a Secret, verifiable from inside the container.

## Prerequisites

- [ ] kind cluster running
- [ ] kubectl configured

## Step-by-Step

### Step 1: Create the ConfigMap

```bash
kubectl apply -f manifests/configmap.yaml
kubectl describe configmap app-config
```

Expected output:
```text
Name:         app-config
Namespace:    default
Data
====
APP_PORT:  8080
LOG_LEVEL: debug
```

### Step 2: Create the Secret

```bash
kubectl apply -f manifests/
```

> **Warning:** `manifests/secret.yaml` contains dummy values only and is safe to commit. Never commit real passwords, tokens, or API keys.

```bash
kubectl get secret db-credentials
```

Expected output:
```text
NAME             TYPE     DATA   AGE
db-credentials   Opaque   2      5s
```

Secret values are stored base64-encoded:
```bash
kubectl get secret db-credentials -o jsonpath='{.data.DB_PASSWORD}' | base64 -d
```

### Step 3: Start the Pod with injected config

```bash
kubectl apply -f manifests/pod-with-config.yaml
kubectl get pod app-pod
```

Expected output:
```text
NAME      READY   STATUS    RESTARTS   AGE
app-pod   1/1     Running   0          15s
```

### Step 4: Verify environment variables inside the container

```bash
kubectl exec app-pod -- env | grep -E "LOG_LEVEL|APP_PORT|DB_"
```

Expected output:
```text
LOG_LEVEL=debug
APP_PORT=8080
DB_USER=admin
DB_PASSWORD=supersecret
```

### Step 5: Verify volume-mounted files

```bash
kubectl exec app-pod -- ls /etc/config/
kubectl exec app-pod -- cat /etc/config/LOG_LEVEL
```

Expected output:
```text
APP_PORT
LOG_LEVEL
debug
```

## Validation

```bash
kubectl describe pod app-pod | grep -A5 "Environment"
# Should list LOG_LEVEL, APP_PORT from ConfigMap, DB_PASSWORD from Secret
```

## Cleanup

```bash
kubectl delete -f manifests/
```

## Extension Task

Update the ConfigMap while the Pod is running:

```bash
kubectl patch configmap app-config --patch '{"data":{"LOG_LEVEL":"info"}}'

# Check the volume-mounted file (updates automatically within ~60s)
kubectl exec app-pod -- cat /etc/config/LOG_LEVEL

# Check the environment variable (does NOT update — requires Pod restart)
kubectl exec app-pod -- env | grep LOG_LEVEL
```

This demonstrates the key difference between volume mounts (live updates) and environment variables (set at Pod start).
