# Module 12 – Troubleshooting

## Goal

After this module you have a systematic workflow for debugging broken workloads. You know the most common failure patterns and how to diagnose them quickly.

## Why does this matter?

Things break in Kubernetes. The question is not if, but when. Engineers who can diagnose problems systematically are vastly more effective than those who guess. This module gives you the mental model and the commands to debug any situation.

## Key Concepts

- **CrashLoopBackOff:** A container crashes repeatedly. Kubernetes backs off exponentially before retrying. Usually caused by application errors, misconfiguration, or a failing liveness probe.
- **ImagePullBackOff / ErrImagePull:** Container image cannot be pulled. Wrong tag, private registry without credentials, or network issue.
- **Pending:** Pod is waiting to be scheduled. Insufficient node resources, unmet tolerations/affinity, or missing PVC.
- **OOMKilled:** Container was killed because it exceeded its memory limit.
- **Terminating (stuck):** Pod is stuck in Terminating state. Often a finalizer issue.

## 10-Step Troubleshooting Workflow

```bash
# Step 1 — Where is the problem?
kubectl get pods -A | grep -v Running

# Step 2 — What is the Pod's status?
kubectl get pod <name> -o wide

# Step 3 — What happened?
kubectl describe pod <name>
# → Read the Events section at the bottom carefully

# Step 4 — What do the logs say?
kubectl logs <name>
kubectl logs <name> -c <container>    # multi-container Pod
kubectl logs <name> --previous        # logs from the crashed container

# Step 5 — Is the application reachable?
kubectl get endpoints <service-name>
# → Should not be empty

# Step 6 — Can the application reach other services?
kubectl run test --rm -it --image=curlimages/curl -- sh
# → curl http://<service>.<namespace>.svc.cluster.local

# Step 7 — Are there resource constraints?
kubectl top pod <name>
kubectl describe node <node-name> | grep -A5 "Allocated resources"

# Step 8 — Are there cluster-level events?
kubectl get events --sort-by='.lastTimestamp'

# Step 9 — Check probe configuration
kubectl get pod <name> -o yaml | grep -A10 "livenessProbe"

# Step 10 — Check RBAC (if service account issues)
kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa>
```

## Common Failure Patterns

### CrashLoopBackOff

```bash
# Get logs from the crashed container
kubectl logs <pod> --previous

# Check probe settings — initialDelaySeconds may be too low
kubectl describe pod <pod> | grep -A5 "Liveness"
```

### ImagePullBackOff

```bash
kubectl describe pod <pod>
# Look for: "Failed to pull image" or "not found"

# Wrong tag? Check available tags in registry
# Private registry? Create and reference an imagePullSecret
```

### Pending — insufficient resources

```bash
kubectl describe pod <pod>
# Look for: "0/1 nodes are available: 1 Insufficient cpu"

kubectl describe node <node>
# Check "Allocatable" vs "Allocated resources"
```

### Empty Service Endpoints

```bash
kubectl get endpoints <service>
# If empty: labels on Pods don't match the Service selector

kubectl get pods --show-labels
kubectl describe service <service> | grep Selector
```

## Checkpoint

- [ ] What does CrashLoopBackOff mean and what are three common causes?
- [ ] What is the first thing you check when a Pod is Pending?
- [ ] How do you get logs from a container that has already restarted?
- [ ] What does empty Endpoints output tell you?
- [ ] What causes OOMKilled and how do you prevent it?

## Definition of Done

You are done with this module when you:

- [ ] Have worked through the 10-step troubleshooting workflow on a broken workload
- [ ] Can explain CrashLoopBackOff, ImagePullBackOff, and OOMKilled
- [ ] Have diagnosed an empty Endpoints issue and fixed the label selector
- [ ] Have used `kubectl logs --previous` to diagnose a crashed container
- [ ] Can answer all checkpoint questions

## Further Reading

- [Debug Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/)
- [Debug Services](https://kubernetes.io/docs/tasks/debug/debug-application/debug-service/)
- [Application Introspection and Debugging](https://kubernetes.io/docs/tasks/debug/debug-application/)
- [Troubleshooting Exercises](../../exercises/troubleshooting.md)
