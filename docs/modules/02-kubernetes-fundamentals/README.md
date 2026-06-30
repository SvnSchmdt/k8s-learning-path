# Module 02 – Kubernetes Fundamentals

## Goal

After this module you understand the Kubernetes architecture, can explain the role of each control plane component, and know the difference between declarative and imperative configuration.

## Why does this matter?

Kubernetes is complex. Knowing what's happening under the hood — which component does what and why — helps you debug problems instead of guessing. Understanding declarative configuration is the mental shift that makes Kubernetes click.

## Key Concepts

- **Cluster:** A set of machines (nodes) running Kubernetes. Consists of a control plane and worker nodes.
- **Control Plane:** The brain of the cluster. Manages scheduling, state, and API access.
  - **API Server (`kube-apiserver`):** The only entry point for all cluster operations. kubectl talks to this.
  - **etcd:** Distributed key-value store. Holds the entire cluster state.
  - **Scheduler (`kube-scheduler`):** Decides which node a new Pod runs on.
  - **Controller Manager (`kube-controller-manager`):** Runs controllers that watch cluster state and reconcile toward the desired state.
- **Worker Node:** Runs the actual application workloads.
  - **kubelet:** Agent on every node. Ensures containers defined in PodSpecs are running and healthy.
  - **kube-proxy:** Handles network rules and Service routing on each node.
  - **Container Runtime:** Runs containers (e.g., containerd, CRI-O).
- **Pod:** The smallest deployable unit in Kubernetes. One or more containers that share network and storage.
- **Namespace:** Virtual cluster within a cluster. Used to separate environments or teams.

## Control Plane Architecture

```
User → kubectl → API Server → etcd (state)
                     ↓
              Scheduler / Controller Manager
                     ↓
              kubelet (on each node) → Container Runtime → Pod
```

## Declarative vs. Imperative

| Approach | How | When to use |
|----------|-----|-------------|
| Imperative | `kubectl run nginx --image=nginx` | Quick experiments only |
| Declarative | `kubectl apply -f deployment.yaml` | Everything in production |

Declarative means: describe the desired state in a YAML file, and Kubernetes figures out how to reach it. The same `apply` command is idempotent — run it 10 times, same result.

## Pod Lifecycle

```
Pending → Running → Succeeded/Failed
```

- **Pending:** Pod is accepted, waiting for scheduling or image pull
- **Running:** All containers started, at least one is running
- **Succeeded:** All containers exited with code 0 (Jobs)
- **Failed:** At least one container exited with non-zero code
- **CrashLoopBackOff:** Container crashes repeatedly — Kubernetes backs off and retries

## Hands-On Task

```bash
# Explore cluster components
kubectl get nodes
kubectl get pods -n kube-system
kubectl describe node <node-name>

# Create your first Pod declaratively
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
spec:
  containers:
  - name: hello
    image: nginx:1.27-alpine
EOF

kubectl get pod hello-pod
kubectl describe pod hello-pod
kubectl delete pod hello-pod
```

## Common Mistakes

- **Mixing imperative and declarative:** Pick declarative. Imperative commands leave no record and can't be version-controlled.
- **Working in the default namespace for everything:** Create dedicated namespaces to keep things organized.
- **Confusing nodes and pods:** Nodes are machines; Pods are workloads running on those machines.

## Checkpoint

- [ ] What are the four main control plane components and what does each do?
- [ ] What is etcd used for?
- [ ] What is the difference between declarative and imperative configuration?
- [ ] What does `kubectl apply` do that `kubectl create` does not?

## Definition of Done

You are done with this module when you:

- [ ] Can name and explain all control plane components
- [ ] Can explain the Pod lifecycle states
- [ ] Have created a Pod declaratively and verified it is Running
- [ ] Can explain why declarative configuration is preferred
- [ ] Can answer all checkpoint questions

## Further Reading

- [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)
- [Understanding Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/)
- [Pod Lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/)
