# Module 13 – GitOps with Argo CD

## Goal

After this module you understand the GitOps model and can deploy applications to Kubernetes using Argo CD. You know the difference between push-based and pull-based deployments.

## Why does this matter?

Traditional CI/CD pipelines push changes to a cluster. GitOps inverts this: the cluster pulls its desired state from Git. This makes deployments auditable, reproducible, and self-healing. Argo CD is the most widely adopted GitOps tool in the Kubernetes ecosystem.

## Key Concepts

- **GitOps:** A set of practices where Git is the single source of truth for the desired state of infrastructure and applications. Every change goes through a pull request.
- **Push-based deployment:** A CI pipeline runs `kubectl apply` to push changes to the cluster. The pipeline needs cluster credentials.
- **Pull-based deployment:** A controller inside the cluster watches a Git repository and applies changes automatically. No outbound credentials needed.
- **Argo CD:** A declarative GitOps controller for Kubernetes. It continuously compares the desired state in Git with the actual state in the cluster and synchronizes them.
- **Application (CRD):** The Argo CD custom resource that defines a source (Git repo + path) and destination (cluster + namespace).
- **Sync:** The process of making the cluster state match the Git state.
- **Self-heal:** Argo CD detects manual changes to the cluster and automatically reverts them to match Git.

## Push vs. Pull

```
Push-based:  Git → CI Pipeline → kubectl apply → Cluster
Pull-based:  Git ← Argo CD watches ← Cluster (Argo CD pulls and applies)
```

## GitOps Principles

1. **Declarative:** All system configuration is expressed declaratively
2. **Versioned:** The desired state is stored in Git (version-controlled, auditable)
3. **Automatic:** Approved changes are applied automatically
4. **Continuous:** Argo CD continuously ensures actual state matches desired state

## Hands-On Task

### Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl wait --namespace argocd \
  --for=condition=available deployment/argocd-server \
  --timeout=120s

# Access the UI
kubectl port-forward -n argocd service/argocd-server 8080:443
```

### Get the initial admin password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath='{.data.password}' | base64 -d
```

### Create an Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/your-org/your-repo.git
    targetRevision: HEAD
    path: manifests/
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

```bash
kubectl apply -f argocd-application.yaml

# Check sync status
kubectl get application -n argocd
```

## Common Mistakes

- **Forgetting to enable `selfHeal`:** Without self-heal, manual `kubectl apply` changes persist and diverge from Git.
- **Using `prune: false`:** Resources removed from Git will remain in the cluster and accumulate as dead weight.
- **Deploying Argo CD itself via Argo CD:** Be careful with bootstrapping. Managing the Argo CD installation itself via GitOps requires careful ordering.

## Checkpoint

- [ ] What are the four GitOps principles?
- [ ] What is the difference between push-based and pull-based deployments?
- [ ] What does Argo CD do when `selfHeal: true` is set and someone runs `kubectl delete`?
- [ ] What is an Argo CD Application resource?

## Definition of Done

You are done with this module when you:

- [ ] Can explain the four GitOps principles
- [ ] Can explain the difference between push-based and pull-based deployments
- [ ] Have installed Argo CD and created an Application resource
- [ ] Have observed Argo CD sync a change from Git to the cluster
- [ ] Can answer all checkpoint questions

## Further Reading

- [Argo CD Documentation](https://argo-cd.readthedocs.io/)
- [GitOps Principles (OpenGitOps)](https://opengitops.dev/)
- [Argo CD Application CRD](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)
