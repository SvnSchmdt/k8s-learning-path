# Lab 01 – nginx Deployment

## What you'll build

A production-style nginx Deployment with 3 replicas, managed by a ReplicaSet. You will scale it, perform a rolling update, inspect rollout history, and roll back to the previous version.

**Kubernetes objects used:** Deployment, ReplicaSet, Pod

```
Deployment (nginx)
  └── ReplicaSet (current)
        ├── Pod (nginx-xxx-1)
        ├── Pod (nginx-xxx-2)
        └── Pod (nginx-xxx-3)
  └── ReplicaSet (previous, replicas=0)
```

## Goal

Deploy nginx with 3 replicas, scale to 5, perform a rolling update, and roll back — verifying the state after each step.

## Prerequisites

- [ ] kind cluster running (`kubectl get nodes` shows Ready)
- [ ] kubectl configured

## Step-by-Step

### Step 1: Apply the Deployment manifest

```bash
kubectl apply -f manifests/deployment.yaml
```

Expected output:
```text
deployment.apps/nginx created
```

```bash
kubectl get deployments
```

Expected output:
```text
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   3/3     3            3           20s
```

### Step 2: Inspect the Pod hierarchy

```bash
kubectl get pods -l app=nginx
```

Expected output:
```text
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6d4cf56db6-2kxvp   1/1     Running   0          30s
nginx-6d4cf56db6-8lmnz   1/1     Running   0          30s
nginx-6d4cf56db6-qtxfp   1/1     Running   0          30s
```

```bash
kubectl get replicasets
```

### Step 3: Scale to 5 replicas

```bash
kubectl scale deployment nginx --replicas=5
kubectl get pods -w
```

Expected output (watch mode):
```text
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6d4cf56db6-2kxvp   1/1     Running   0          2m
nginx-6d4cf56db6-8lmnz   1/1     Running   0          2m
nginx-6d4cf56db6-qtxfp   1/1     Running   0          2m
nginx-6d4cf56db6-abcde   0/1     Pending   0          1s
nginx-6d4cf56db6-fghij   0/1     Pending   0          1s
```

Press `Ctrl+C` to exit watch mode.

### Step 4: Perform a rolling update

```bash
kubectl set image deployment/nginx nginx=nginx:1.27-alpine
kubectl rollout status deployment/nginx
```

Expected output:
```text
Waiting for deployment "nginx" rollout to finish: 1 out of 5 new replicas have been updated...
...
deployment "nginx" successfully rolled out
```

### Step 5: Inspect rollout history

```bash
kubectl rollout history deployment/nginx
```

Expected output:
```text
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

### Step 6: Roll back to the previous version

```bash
kubectl rollout undo deployment/nginx
kubectl rollout status deployment/nginx
```

Verify the image was reverted:

```bash
kubectl get deployment nginx -o jsonpath='{.spec.template.spec.containers[0].image}'
```

## Validation

```bash
kubectl get deployment nginx
# READY should show 5/5

kubectl describe deployment nginx | grep Image
# Should show the original image
```

## Cleanup

```bash
kubectl delete -f manifests/deployment.yaml
```

## Extension Task

Configure a rolling update strategy with controlled rollout speed:

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # max extra pods during update
      maxUnavailable: 0  # never have fewer than desired replicas
```

Apply it and observe the difference in how the update progresses.
