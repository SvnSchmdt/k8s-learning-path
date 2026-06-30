# Module 03 – kubectl & Cluster Access

## Goal

After this module you master kubectl as your primary tool for working with Kubernetes. You understand kubeconfig, contexts, and can work efficiently with cluster resources.

## Why does this matter?

kubectl is your main tool for everything in Kubernetes. Knowing it well means working fast and safely — even when graphical UIs are available. In the CKA exam, kubectl is the only tool that matters.

## Key Concepts

- **kubectl:** Kubernetes command-line tool. Communicates with the API server over HTTPS.
- **kubeconfig:** Configuration file (default: `~/.kube/config`) defining clusters, users, and contexts.
- **Context:** A combination of cluster + user + namespace. Switching contexts = switching between clusters or environments.
- **Resource types:** Kubernetes has many resource types (Pods, Deployments, Services…). `kubectl api-resources` lists all of them.
- **Output formats:** kubectl can output as table, JSON, YAML, or custom (jsonpath, go-template).

## Hands-On Task

### Understand kubeconfig

```bash
# View full kubeconfig
kubectl config view

# Show current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context kind-k8s-learning
```

### Work with resources

```bash
# List all resource types
kubectl api-resources

# Get resources
kubectl get pods
kubectl get pods -A              # all namespaces
kubectl get pods -n kube-system  # specific namespace

# Describe a resource
kubectl describe pod <name>

# YAML output
kubectl get pod <name> -o yaml

# Extract a field with jsonpath
kubectl get pod <name> -o jsonpath='{.status.podIP}'
```

### Create and delete resources

```bash
# Apply from file (declarative, preferred)
kubectl apply -f manifest.yaml

# Delete
kubectl delete -f manifest.yaml
kubectl delete pod <name>

# Set labels and annotations
kubectl label pod <name> env=test
kubectl annotate pod <name> info="test-pod"
```

### Useful shortcuts

```bash
kubectl get po      # pods
kubectl get svc     # services
kubectl get deploy  # deployments
kubectl get ns      # namespaces
kubectl get cm      # configmaps

# Multiple resource types at once
kubectl get pods,services

# Watch mode (real-time updates)
kubectl get pods -w
```

## kubeconfig Structure

```yaml
apiVersion: v1
clusters:
- cluster:
    server: https://127.0.0.1:6443
  name: kind-k8s-learning
contexts:
- context:
    cluster: kind-k8s-learning
    user: kind-k8s-learning
  name: kind-k8s-learning
current-context: kind-k8s-learning
```

## Common Mistakes

- **Wrong context:** You are working on the wrong cluster. Always check `kubectl config current-context` first.
- **Wrong namespace:** Resource not found because you're in the wrong namespace. Use `-n <namespace>` or `-A`.
- **`kubectl create` vs. `kubectl apply`:** `create` fails if the resource already exists. `apply` is idempotent — always prefer `apply`.

## Checkpoint

- [ ] What is kubeconfig and where does it live by default?
- [ ] What is a context and how do you switch between contexts?
- [ ] How do you show all pods across all namespaces?
- [ ] What is the difference between `kubectl create` and `kubectl apply`?
- [ ] How do you extract a specific field value using jsonpath?

## Definition of Done

You are done with this module when you:

- [ ] Have switched between cluster contexts using `kubectl config use-context`
- [ ] Have listed pods in all namespaces with `kubectl get pods -A`
- [ ] Have extracted a specific field value using `-o jsonpath`
- [ ] Can explain the difference between `kubectl create` and `kubectl apply`
- [ ] Can answer all checkpoint questions

## Further Reading

- [kubectl Overview](https://kubernetes.io/docs/reference/kubectl/)
- [kubectl Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/)
- [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
- [Local Cheat Sheet](../../resources/cheat-sheet.md)
