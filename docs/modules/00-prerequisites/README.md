# Module 00 – Prerequisites

## Goal

After this module you have a working local environment with Docker, kind, and kubectl. You can create a local Kubernetes cluster and interact with it using kubectl.

## Why does this matter?

Every lab in this learning path runs locally. You need a working local cluster before you can do anything else. Getting this right once means you won't be blocked later by environment issues.

## Key Concepts

- **Docker / Podman:** Container runtime. kind uses Docker to run Kubernetes nodes as containers.
- **kind (Kubernetes in Docker):** Runs a complete Kubernetes cluster inside Docker containers. Perfect for local development and learning.
- **kubectl:** The Kubernetes command-line tool. Communicates with the API server to create, inspect, and delete resources.
- **kubeconfig:** Configuration file (default: `~/.kube/config`) that tells kubectl which cluster to connect to and with which credentials.

## Hands-On Task

### Install tools (macOS)

```bash
brew install docker kind kubectl helm
```

### Verify installations

```bash
docker version
kind version
kubectl version --client
helm version
```

### Create a local cluster

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
You can now use your cluster with: kubectl cluster-info --context kind-k8s-learning
```

### Verify the cluster

```bash
kubectl get nodes
```

Expected output:
```text
NAME                         STATUS   ROLES           AGE   VERSION
k8s-learning-control-plane   Ready    control-plane   60s   v1.30.x
```

## Common Mistakes

- **Docker not running:** kind cannot create a cluster if Docker is not started. Start Docker Desktop first.
- **kubectl connects to wrong cluster:** Check `kubectl config current-context`. Switch with `kubectl config use-context kind-k8s-learning`.
- **kind command not found:** kind is installed but not in PATH. Restart your terminal or check PATH.

## Checkpoint

- [ ] What is kind and why is it useful for learning Kubernetes?
- [ ] Where does kubectl read its cluster connection configuration from?
- [ ] What does `kubectl get nodes` show you?

## Definition of Done

You are done with this module when you:

- [ ] Have Docker, kind, kubectl, and Helm installed and can verify their versions
- [ ] Have created a local kind cluster and see the node in `Ready` state
- [ ] Can explain what kubeconfig is and where it lives
- [ ] Can answer all checkpoint questions

## Further Reading

- [kind Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/)
- [kubectl Installation](https://kubernetes.io/docs/tasks/tools/)
- [kubeconfig Overview](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)
