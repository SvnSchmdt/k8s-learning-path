# Lab 05 – Helm Chart

## What you'll build

A custom Helm chart for an nginx application. You will install it, customize values, upgrade the release, inspect history, and perform a rollback — the complete Helm lifecycle.

**Kubernetes objects used:** Deployment, Service (managed by Helm)

```
chart/
  ├── Chart.yaml          ← metadata
  ├── values.yaml         ← default configuration
  └── templates/
        ├── deployment.yaml   ← Go template
        └── service.yaml      ← Go template

helm install my-nginx ./chart
  └── Release: my-nginx
        ├── Deployment (my-nginx)
        └── Service (my-nginx)
```

## Goal

Install a Helm release from a local chart, upgrade it with custom values, roll back to a previous revision — all verifiable with `helm list` and `kubectl get`.

## Prerequisites

- [ ] kind cluster running
- [ ] Helm v3 installed (`helm version`)

## Step-by-Step

### Step 1: Verify Helm

```bash
helm version
```

Expected output:
```text
version.BuildInfo{Version:"v3.x.x", ...}
```

### Step 2: Preview the chart before installing

```bash
helm template my-nginx ./chart
```

This renders the templates without installing — useful for reviewing the output.

Expected output: two YAML documents (Deployment and Service) with values filled in.

### Step 3: Install the chart

```bash
helm install my-nginx ./chart
```

Expected output:
```text
NAME: my-nginx
LAST DEPLOYED: ...
NAMESPACE: default
STATUS: deployed
REVISION: 1
```

```bash
helm list
```

Expected output:
```text
NAME      NAMESPACE  REVISION  STATUS    CHART         APP VERSION
my-nginx  default    1         deployed  my-app-0.1.0  1.0.0
```

### Step 4: Inspect the deployed resources

```bash
helm get all my-nginx
kubectl get deployment,service -l app.kubernetes.io/instance=my-nginx
```

### Step 5: Upgrade with custom values

```bash
helm upgrade my-nginx ./chart --set replicaCount=3
kubectl get pods -w
```

Expected output (after upgrade):
```text
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-xxx-1              1/1     Running   0          30s
my-nginx-xxx-2              1/1     Running   0          15s
my-nginx-xxx-3              1/1     Running   0          5s
```

### Step 6: Inspect release history

```bash
helm history my-nginx
```

Expected output:
```text
REVISION  STATUS      DESCRIPTION
1         superseded  Install complete
2         deployed    Upgrade complete
```

### Step 7: Roll back to revision 1

```bash
helm rollback my-nginx 1
helm list
```

Expected output:
```text
REVISION: 3   (rollback creates a new revision)
STATUS: deployed
```

## Validation

```bash
helm status my-nginx
# STATUS: deployed

kubectl get deployment my-nginx
# Check replica count matches the rolled-back revision
```

## Cleanup

```bash
helm uninstall my-nginx
kubectl get all   # verify everything is gone
```

## Extension Task

Create a values file for a "production" configuration:

```yaml
# values-prod.yaml
replicaCount: 5
image:
  tag: "1.27-alpine"
service:
  type: ClusterIP
```

Install with:
```bash
helm install my-nginx-prod ./chart -f values-prod.yaml
```

Compare the output with the default installation.
