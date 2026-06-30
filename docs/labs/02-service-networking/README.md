# Lab 02 – Service & Networking

## What you'll build

Two Services for the nginx Deployment from Lab 01: a ClusterIP for internal cluster access and a NodePort for external access. You will test both using `kubectl exec`, internal DNS, and `kubectl port-forward`.

**Kubernetes objects used:** Service (ClusterIP), Service (NodePort), Endpoints

```
Internal access:
  Pod → ClusterIP Service (nginx-service:80) → nginx Pods

External access:
  Browser → NodePort (localhost:30080) → nginx Pods
  OR
  kubectl port-forward → nginx-service:80 → nginx Pods
```

## Goal

A working ClusterIP Service accessible via DNS from inside the cluster, and a NodePort Service accessible from your workstation.

## Prerequisites

- [ ] kind cluster running
- [ ] nginx Deployment from Lab 01 running (or `kubectl apply -f manifests/` from Lab 01)

## Step-by-Step

### Step 1: Create the ClusterIP Service

```bash
kubectl apply -f manifests/service-clusterip.yaml
```

Expected output:
```text
service/nginx-service created
```

```bash
kubectl get service nginx-service
```

Expected output:
```text
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.96.xxx.xxx   <none>        80/TCP    10s
```

### Step 2: Verify Endpoints are populated

```bash
kubectl get endpoints nginx-service
```

Expected output (should list Pod IPs, not be empty):
```text
NAME            ENDPOINTS                                         AGE
nginx-service   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80       20s
```

> **Tip:** If Endpoints is empty, the Service selector labels don't match the Pod labels. Check `kubectl get pods --show-labels`.

### Step 3: Test internal DNS access

```bash
kubectl run curl-test --rm -it --image=curlimages/curl -- \
  curl http://nginx-service.default.svc.cluster.local
```

Expected output:
```text
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title>...
```

### Step 4: Test with port-forward

```bash
kubectl port-forward service/nginx-service 8080:80
```

In a second terminal:
```bash
curl http://localhost:8080
```

Expected output:
```text
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title>...
```

Press `Ctrl+C` to stop port-forward.

### Step 5: Create the NodePort Service

```bash
kubectl apply -f manifests/service-nodeport.yaml
kubectl get service nginx-nodeport
```

Expected output:
```text
NAME             TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx-nodeport   NodePort   10.96.xxx.xxx   <none>        80:30080/TCP   5s
```

```bash
# In kind, access via port-forward on the NodePort service
kubectl port-forward service/nginx-nodeport 30080:80
curl http://localhost:30080
```

## Validation

```bash
# Service exists
kubectl get svc nginx-service nginx-nodeport

# Endpoints are not empty
kubectl get endpoints nginx-service
kubectl get endpoints nginx-nodeport
```

## Cleanup

```bash
kubectl delete -f manifests/
```

## Extension Task

Create a Service with session affinity so that repeated requests from the same client always go to the same Pod:

```yaml
spec:
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800
```

Test whether consecutive requests go to the same Pod by checking the Pod's hostname in the response.
