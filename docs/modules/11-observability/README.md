# Module 11 – Observability

## Goal

After this module you can configure health probes, read resource metrics with `kubectl top`, interpret logs and events, and understand the role of Prometheus and Grafana in a production observability stack.

## Why does this matter?

A workload that runs is not the same as a workload that is healthy. Observability is what tells you the difference. Health probes protect your users from broken Pods receiving traffic. Metrics, logs, and events are the three pillars you use to diagnose every incident.

!!! important "Observability is mandatory in production"
    Probes, metrics, and log aggregation are not optional extras. A Pod without liveness and readiness probes is a reliability risk.

## Key Concepts

- **Liveness Probe:** Kubernetes checks if the container is alive. If it fails, the container is restarted.
- **Readiness Probe:** Kubernetes checks if the container is ready to receive traffic. If it fails, the Pod is removed from Service Endpoints — no traffic is sent to it.
- **Startup Probe:** Kubernetes waits until the startup probe succeeds before starting liveness checks. Prevents premature restarts of slow-starting applications.
- **Metrics Server:** Lightweight in-cluster metrics aggregator. Enables `kubectl top nodes` and `kubectl top pods`. Required for HPA.
- **Prometheus:** Time-series metrics database. Scrapes `/metrics` endpoints from Pods and stores them.
- **Grafana:** Visualization layer for Prometheus metrics. Provides dashboards.
- **Events:** Kubernetes objects that record what happened in the cluster (Pod scheduled, image pulled, probe failed, etc.).

## Probe Types

| Probe | Failure action | Use for |
|-------|---------------|---------|
| Liveness | Restart container | Detecting deadlocks, hangs |
| Readiness | Remove from Service | Slow startup, temporary overload |
| Startup | Restart container | Slow-starting applications |

## Probe Check Methods

```yaml
# HTTP GET
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10

# TCP Socket
readinessProbe:
  tcpSocket:
    port: 5432
  initialDelaySeconds: 5
  periodSeconds: 5

# Exec command
livenessProbe:
  exec:
    command: ["cat", "/tmp/healthy"]
  initialDelaySeconds: 5
  periodSeconds: 10
```

!!! warning "Set initialDelaySeconds carefully"
    If `initialDelaySeconds` is too low, the liveness probe kills the container before it has time to start — causing a CrashLoopBackOff even when the application is fine.

## The 5 Diagnostic Questions for Every Pod

When a Pod is not behaving as expected, answer these five questions in order:

```bash
# 1. What is the Pod status?
kubectl get pod <name>

# 2. What happened recently?
kubectl describe pod <name>   # check Events section at the bottom

# 3. What does the container log say?
kubectl logs <name>
kubectl logs <name> --previous   # if the container already restarted

# 4. Is the container actually using resources?
kubectl top pod <name>

# 5. Is the Service routing to this Pod?
kubectl get endpoints <service-name>
```

## Hands-On Task

### Install Metrics Server in kind

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Patch for kind (disable TLS verification for local cluster)
kubectl patch deployment metrics-server -n kube-system \
  --type json \
  -p '[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'

kubectl wait --namespace kube-system \
  --for=condition=available deployment/metrics-server \
  --timeout=90s
```

### Use kubectl top

```bash
kubectl top nodes
kubectl top pods
kubectl top pods -A
```

### Configure probes in a Deployment

```yaml
containers:
- name: app
  image: nginx:1.27-alpine
  livenessProbe:
    httpGet:
      path: /
      port: 80
    initialDelaySeconds: 10
    periodSeconds: 10
  readinessProbe:
    httpGet:
      path: /
      port: 80
    initialDelaySeconds: 5
    periodSeconds: 5
```

## Checkpoint

- [ ] What is the difference between a liveness probe and a readiness probe?
- [ ] What happens when a readiness probe fails?
- [ ] Why is `initialDelaySeconds` important for liveness probes?
- [ ] What command shows CPU and memory usage per Pod?
- [ ] What is the difference between Prometheus and Metrics Server?

## Definition of Done

You are done with this module when you:

- [ ] Have configured liveness and readiness probes and observed their behavior
- [ ] Have installed Metrics Server and used `kubectl top pods`
- [ ] Have read Pod logs including `--previous` for a restarted container
- [ ] Have located relevant Events using `kubectl describe pod`
- [ ] Can explain the difference between Prometheus and Metrics Server
- [ ] Can answer all checkpoint questions

## Further Reading

- [Configure Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server)
- [Prometheus](https://prometheus.io/docs/introduction/overview/)
- [kube-prometheus-stack (Helm)](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
