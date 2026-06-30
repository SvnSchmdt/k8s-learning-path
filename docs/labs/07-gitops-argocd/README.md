# Lab 07 – GitOps with Argo CD

## What you'll build

A GitOps deployment pipeline: Argo CD installed in your cluster, connected to a Git repository, deploying an Application automatically and keeping it in sync. You will observe Argo CD reconcile a manually deleted resource.

**Kubernetes objects used:** Namespace (argocd), Deployment, Service, Application (Argo CD CRD)

```
Git Repository (your manifests)
        │
        │  Argo CD watches and pulls
        ▼
Argo CD Controller (in cluster)
        │
        │  applies manifests
        ▼
Namespace: default
  ├── Deployment (my-app)
  └── Service (my-app)
```

## Goal

Argo CD running, an Application deployed and showing `Synced/Healthy`, and self-heal observed when a resource is manually deleted.

## Prerequisites

- [ ] kind cluster running
- [ ] kubectl configured
- [ ] A public Git repository with Kubernetes manifests (or use the examples in this repo)

## Step-by-Step

### Step 1: Install Argo CD

```bash
kubectl create namespace argocd

kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl wait --namespace argocd \
  --for=condition=available deployment/argocd-server \
  --timeout=120s
```

Expected output:
```text
deployment.apps/argocd-server condition met
```

```bash
kubectl get pods -n argocd
```

Expected output (all Running):
```text
NAME                                  READY   STATUS    RESTARTS   AGE
argocd-application-controller-...    1/1     Running   0          90s
argocd-dex-server-...                1/1     Running   0          90s
argocd-redis-...                     1/1     Running   0          90s
argocd-repo-server-...               1/1     Running   0          90s
argocd-server-...                    1/1     Running   0          90s
```

### Step 2: Access the Argo CD UI

```bash
kubectl port-forward -n argocd service/argocd-server 8080:443
```

Open [https://localhost:8080](https://localhost:8080) (accept the self-signed certificate warning).

Get the initial admin password:
```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath='{.data.password}' | base64 -d && echo
```

Log in: username `admin`, password from above.

### Step 3: Create an Application

Edit `manifests/argocd-application.yaml` and replace `repoURL` with your Git repository URL.

```bash
kubectl apply -f manifests/argocd-application.yaml
```

Check the Application status:
```bash
kubectl get application -n argocd
```

Expected output:
```text
NAME     SYNC STATUS   HEALTH STATUS
my-app   Synced        Healthy
```

### Step 4: Trigger a sync manually

```bash
# Install argocd CLI (optional)
brew install argocd

argocd login localhost:8080 --username admin --password <password> --insecure
argocd app sync my-app
```

Expected output:
```text
TIMESTAMP   GROUP  KIND        NAMESPACE  NAME    STATUS   HEALTH    HOOK  MESSAGE
...         apps   Deployment  default    my-app  Synced   Healthy         deployment.apps/my-app configured
```

### Step 5: Observe self-healing

With `selfHeal: true` configured, delete a resource manually:

```bash
kubectl delete deployment my-app

# Watch Argo CD recreate it within seconds
kubectl get deployment my-app -w
```

Expected output (deployment reappears quickly):
```text
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
my-app   0/3     0            0           2s
my-app   3/3     3            3           15s
```

> **Tip:** Try changing the replica count directly with `kubectl scale` and observe Argo CD reset it back to the value in Git.

### Step 6: Change the replica count via Git

Update the replica count in your Git repository (edit the Deployment manifest and push). Watch Argo CD detect the change and apply it:

```bash
kubectl get deployment my-app -w
# replica count changes to match the new value in Git
```

## Validation

```bash
kubectl get application -n argocd
# SYNC STATUS: Synced, HEALTH STATUS: Healthy

kubectl get pods -n argocd
# All Running
```

## Cleanup

```bash
kubectl delete -f manifests/argocd-application.yaml
kubectl delete namespace argocd
```

> **Caution:** `kubectl delete namespace argocd` deletes all Argo CD resources and configuration permanently.

## Extension Task

Configure Argo CD notifications to send a Slack message when an Application sync fails. See [Argo CD Notifications](https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/) for setup instructions.
