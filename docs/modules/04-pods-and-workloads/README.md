# Module 04 – Pods & Workloads

## Goal

After this module you understand the relationship between Pods, ReplicaSets, and Deployments. You can deploy applications, scale them, perform rolling updates, and roll back to previous versions.

## Why does this matter?

In real workloads, you almost never create Pods directly. Deployments manage Pods for you — they handle redundancy, rolling updates, and rollbacks. Understanding how Deployments, ReplicaSets, and Pods relate is fundamental to everything that follows.

## Key Concepts

- **Pod:** The smallest deployable unit. One or more containers sharing network and storage. Pods are ephemeral — when they die, they are not recreated automatically.
- **ReplicaSet:** Ensures a specified number of identical Pod replicas are running at all times. Automatically replaces failed Pods.
- **Deployment:** Manages ReplicaSets. Provides declarative updates, rolling update strategy, and rollback capability. This is what you use in practice.
- **Rolling Update:** The default update strategy. New Pods are created before old ones are removed, ensuring zero downtime.
- **Rollback:** Revert a Deployment to a previous version using `kubectl rollout undo`.

## Hierarchy

```
Deployment
  └── ReplicaSet (current)
        ├── Pod
        ├── Pod
        └── Pod
  └── ReplicaSet (previous, scaled to 0)
```

## Hands-On Task

### Create a Deployment

```bash
kubectl create deployment nginx --image=nginx:1.27-alpine --replicas=3
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

### Scale

```bash
kubectl scale deployment nginx --replicas=5
kubectl get pods -w   # watch pods appear
```

### Perform a rolling update

```bash
kubectl set image deployment/nginx nginx=nginx:1.27-alpine
kubectl rollout status deployment/nginx
```

### Inspect rollout history

```bash
kubectl rollout history deployment/nginx
```

### Roll back

```bash
kubectl rollout undo deployment/nginx
kubectl rollout status deployment/nginx
```

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27-alpine
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
```

## Common Mistakes

- **Editing Pods directly:** Pods managed by a Deployment cannot be edited directly (most fields are immutable). Edit the Deployment instead.
- **Label mismatch:** The `selector.matchLabels` must match `template.metadata.labels`. If they don't match, the Deployment can't find its own Pods.
- **Forgetting resource requests/limits:** Always set them. Without them, a Pod can consume all node resources and starve other workloads.

## Checkpoint

- [ ] What is the difference between a Pod, a ReplicaSet, and a Deployment?
- [ ] What happens to Pods when you scale a Deployment down to 0?
- [ ] What does a rolling update strategy guarantee?
- [ ] How do you roll back a Deployment to the previous version?

## Definition of Done

You are done with this module when you:

- [ ] Have created a Deployment and verified it manages a ReplicaSet and Pods
- [ ] Have scaled a Deployment and watched Pods appear and disappear
- [ ] Have performed a rolling update and checked the rollout status
- [ ] Have rolled back a Deployment using `kubectl rollout undo`
- [ ] Can answer all checkpoint questions

## Further Reading

- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
