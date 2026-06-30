# Module 14 – Production Readiness

## Goal

After this module you know the patterns that make a Kubernetes workload production-ready: resource requests and limits, HPA, PodDisruptionBudgets, and properly configured probes.

## Why does this matter?

A Pod that works in a dev cluster is not automatically production-ready. Production means: zero downtime during updates, graceful handling of node failures, protection against resource starvation, and automatic scaling under load.

## Key Concepts

- **Resource Requests:** The amount of CPU/memory Kubernetes reserves for a container on a node. Used for scheduling.
- **Resource Limits:** The maximum CPU/memory a container is allowed to use. Enforced by the kernel.
- **OOMKilled:** A container exceeded its memory limit. The kernel kills it. Set limits based on realistic profiling, not guesses.
- **HPA (HorizontalPodAutoscaler):** Automatically scales the number of Pod replicas based on CPU/memory metrics. Requires Metrics Server.
- **PodDisruptionBudget (PDB):** Guarantees a minimum number of healthy replicas are available during voluntary disruptions (node drain, rolling update).
- **Graceful shutdown:** A Pod receiving SIGTERM has a configurable `terminationGracePeriodSeconds` (default: 30s) to finish in-flight requests.
- **topologySpreadConstraints:** Distribute Pods evenly across nodes, zones, or regions for high availability.

## Resource Requests and Limits

```yaml
containers:
- name: app
  image: my-app:v1
  resources:
    requests:
      memory: "128Mi"
      cpu: "100m"
    limits:
      memory: "256Mi"
      cpu: "500m"
```

- `100m` CPU = 0.1 CPU cores
- Requests: what the scheduler uses to find a node
- Limits: what the kernel enforces at runtime

## HorizontalPodAutoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

```bash
kubectl get hpa
kubectl describe hpa my-app-hpa
```

## PodDisruptionBudget

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

## Production Readiness Checklist

- [ ] Resource requests and limits set for all containers
- [ ] Liveness probe configured
- [ ] Readiness probe configured
- [ ] `minReplicas >= 2` (no single point of failure)
- [ ] HPA configured for CPU/memory scaling
- [ ] PodDisruptionBudget defined
- [ ] Graceful shutdown handled in application code
- [ ] Image tag is specific (not `latest`)
- [ ] `runAsNonRoot: true` in SecurityContext

## Common Mistakes

- **No resource limits:** A single Pod can starve all other workloads on the node.
- **Limits set too low:** Legitimate traffic causes OOMKilled. Profile your application first.
- **HPA without requests:** HPA uses CPU utilization relative to requests. Without requests, HPA cannot calculate utilization.
- **Only 1 replica:** A single Pod has no redundancy. Node drain during updates causes downtime.

## Checkpoint

- [ ] What is the difference between resource requests and resource limits?
- [ ] What causes OOMKilled and how do you prevent it?
- [ ] What does HPA need in order to function?
- [ ] What does a PodDisruptionBudget guarantee?
- [ ] What is `terminationGracePeriodSeconds` and why does it matter?

## Definition of Done

You are done with this module when you:

- [ ] Have configured resource requests and limits for a Deployment
- [ ] Have created an HPA and observed it scaling the Deployment
- [ ] Have created a PodDisruptionBudget and can explain what it guarantees
- [ ] Can work through the production readiness checklist for a deployment
- [ ] Can answer all checkpoint questions

## Further Reading

- [Resource Management for Pods and Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Horizontal Pod Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [PodDisruptionBudget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
- [Termination of Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination)
