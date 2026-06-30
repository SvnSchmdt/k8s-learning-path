# Lab 00 – Local Cluster with kind

## What you'll build

In this lab you set up a fully functional local Kubernetes cluster using kind (Kubernetes in Docker). This cluster is the foundation for all other labs. You will verify the installation and run your first workload.

**Kubernetes objects used:** none (cluster setup only)

```
Docker Engine
  └── kind container (k8s-learning-control-plane)
        └── Kubernetes API Server
              └── kubectl (your workstation)
```

## Goal

A running kind cluster with `kubectl` configured, verified with `kubectl get nodes` showing `Ready`.

## Prerequisites

- [ ] Docker Desktop running (or Docker Engine on Linux)
- [ ] Internet connection (for image download)
- [ ] Terminal access

## Step-by-Step

### Step 1: Install tools

```bash
# macOS (Homebrew)
brew install kind kubectl helm

# Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind

curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/kubectl
```

Verify:

```bash
kind version
kubectl version --client
```

Expected output:
```text
kind v0.23.0 go1.21.x ...
Client Version: v1.30.x
```

### Step 2: Create the cluster

```bash
kind create cluster --name k8s-learning
```

Expected output:
```text
Creating cluster "k8s-learning" ...
 ✓ Ensuring node image (kindest/node:v1.30.x) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-k8s-learning"
You can now use your cluster with:

kubectl cluster-info --context kind-k8s-learning
```

> **Note:** First run downloads the kind node image (~700 MB). This takes a few minutes depending on your connection speed.

### Step 3: Verify the cluster

```bash
kubectl get nodes
```

Expected output:
```text
NAME                         STATUS   ROLES           AGE   VERSION
k8s-learning-control-plane   Ready    control-plane   60s   v1.30.x
```

```bash
kubectl get pods -n kube-system
```

Expected output (all pods should be Running):
```text
NAME                                                 READY   STATUS    RESTARTS   AGE
coredns-...                                          1/1     Running   0          90s
etcd-k8s-learning-control-plane                      1/1     Running   0          90s
kube-apiserver-k8s-learning-control-plane            1/1     Running   0          90s
kube-controller-manager-k8s-learning-control-plane   1/1     Running   0          90s
kube-proxy-...                                       1/1     Running   0          90s
kube-scheduler-k8s-learning-control-plane            1/1     Running   0          90s
```

### Step 4: Run your first workload

```bash
kubectl run hello --image=nginx:1.27-alpine --restart=Never
kubectl get pod hello
```

Expected output:
```text
NAME    READY   STATUS    RESTARTS   AGE
hello   1/1     Running   0          10s
```

```bash
kubectl delete pod hello
```

## Validation

```bash
kubectl cluster-info
# Kubernetes control plane is running at https://127.0.0.1:...
kubectl config current-context
# kind-k8s-learning
```

## Cleanup

> **Caution:** This deletes the entire cluster and all resources in it.

```bash
kind delete cluster --name k8s-learning
```

To recreate it: `kind create cluster --name k8s-learning`

## Extension Task

Create a cluster with a custom configuration — 1 control-plane node and 2 worker nodes:

```yaml
# kind-multinode.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

```bash
kind create cluster --name k8s-multinode --config kind-multinode.yaml
kubectl get nodes
```
