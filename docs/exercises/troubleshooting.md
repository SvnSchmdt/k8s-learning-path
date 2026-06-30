# Troubleshooting Exercises

These exercises simulate real-world failure scenarios. For each one: diagnose the problem, explain what caused it, and fix it. Do not look at the solution until you have at least identified the root cause.

---

## Scenario 1 — CrashLoopBackOff

Apply this manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: crasher
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "echo start && exit 1"]
```

**Question:** Why is the Pod in CrashLoopBackOff? What command shows the crash reason?

<details>
<summary>Solution</summary>

The container exits with code `1` immediately. Kubernetes restarts it, waits, and retries — hence CrashLoopBackOff.

```bash
kubectl get pod crasher
# STATUS: CrashLoopBackOff

kubectl logs crasher
# start

kubectl logs crasher --previous
# start  (from previous crash)

kubectl describe pod crasher
# Events: Back-off restarting failed container
```

**Fix:** Change the command so the container stays running:
```bash
command: ["sh", "-c", "echo start && sleep 3600"]
```

</details>

---

## Scenario 2 — ImagePullBackOff

Apply this manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bad-image
spec:
  containers:
  - name: app
    image: nginx:this-tag-does-not-exist
```

**Question:** What is the Pod status? Which command gives you the root cause?

<details>
<summary>Solution</summary>

```bash
kubectl get pod bad-image
# STATUS: ErrImagePull or ImagePullBackOff

kubectl describe pod bad-image
# Events:
#   Warning  Failed  Failed to pull image "nginx:this-tag-does-not-exist":
#             rpc error: code = NotFound

kubectl get pod bad-image -o jsonpath='{.status.containerStatuses[0].state}'
```

**Fix:** Use a valid image tag: `nginx:1.27-alpine`

</details>

---

## Scenario 3 — Empty Service Endpoints

Create this Deployment and Service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp        # ← label is "myapp"
    spec:
      containers:
      - name: app
        image: nginx:1.27-alpine
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-svc
spec:
  selector:
    app: my-app           # ← selector is "my-app" (different!)
  ports:
  - port: 80
    targetPort: 80
```

**Question:** Why does `kubectl get endpoints myapp-svc` show no addresses?

<details>
<summary>Solution</summary>

```bash
kubectl get endpoints myapp-svc
# NAME         ENDPOINTS   AGE
# myapp-svc    <none>      10s

kubectl get pods --show-labels
# app=myapp

kubectl describe service myapp-svc | grep Selector
# Selector: app=my-app
```

The Service selector (`app=my-app`) does not match the Pod labels (`app=myapp`). Kubernetes cannot find any matching Pods.

**Fix:** Align the labels:
```yaml
# In Service:
selector:
  app: myapp   # ← must match Pod label
```

</details>

---

## Scenario 4 — Pending Pod (Insufficient Resources)

Apply this manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: too-greedy
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    resources:
      requests:
        memory: "999Gi"
        cpu: "500"
```

**Question:** Why is the Pod stuck in Pending? Which command shows the scheduler's reasoning?

<details>
<summary>Solution</summary>

```bash
kubectl get pod too-greedy
# STATUS: Pending

kubectl describe pod too-greedy
# Events:
#   Warning  FailedScheduling  0/1 nodes are available:
#            1 Insufficient memory, 1 Insufficient cpu

kubectl describe node <node-name> | grep -A5 "Allocatable"
# Shows actual available resources — much less than 999Gi/500 CPU
```

**Fix:** Set realistic resource requests based on actual application needs.

</details>

---

## Scenario 5 — Liveness Probe Killing a Healthy Container

Apply this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: premature-kill
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 0    # ← probe starts immediately
      periodSeconds: 2
      failureThreshold: 1
```

**Question:** Why might this Pod restart even though nginx starts successfully?

<details>
<summary>Solution</summary>

`initialDelaySeconds: 0` means the liveness probe starts immediately. nginx takes 1–3 seconds to start and begin serving HTTP. If the probe fires during that window, it gets a connection refused (not a 200 OK), counts as a failure, and kills the container before it is ready.

```bash
kubectl get pod premature-kill -w
# Watch RESTARTS increment despite nginx being fine

kubectl describe pod premature-kill
# Events: Liveness probe failed: dial tcp ... connect: connection refused
#         Container premature-kill failed liveness probe, will be restarted
```

**Fix:** Set a reasonable `initialDelaySeconds`:
```yaml
initialDelaySeconds: 10
failureThreshold: 3
```

Or use a startup probe to protect the liveness check during startup.

</details>

---

## Scenario 6 — Service Not Routing to Pods

A Service exists with correct labels and Endpoints. But `curl` inside the cluster gets `connection refused`.

**Question:** List the steps you would follow to diagnose this.

<details>
<summary>Systematic diagnosis</summary>

```bash
# 1. Are the Pods Running and Ready?
kubectl get pods -l app=<your-app>
# READY must be 1/1, not 0/1

# 2. Is the Service port correct?
kubectl describe service <svc-name>
# Check: Port, TargetPort, Endpoints

# 3. Are Endpoints populated?
kubectl get endpoints <svc-name>
# Must NOT be empty

# 4. Does the Pod actually listen on the targetPort?
kubectl exec <pod> -- ss -tlnp
# Or: netstat -tlnp

# 5. Test directly to the Pod (bypassing the Service)
kubectl exec -it <another-pod> -- curl http://<pod-ip>:<targetPort>

# 6. Check the readiness probe
kubectl describe pod <pod> | grep -A5 "Readiness"
# A failing readiness probe removes the Pod from Endpoints
```

Most common causes: wrong `targetPort`, readiness probe failing, or the app listening on a different port than specified.

</details>
