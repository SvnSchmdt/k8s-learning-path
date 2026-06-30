# Beginner Exercises

Work through these exercises after completing Modules 00–04 (Phases 0–3). Solve each task before expanding the solution.

---

## Exercise 1 — Create a Pod

**Task:** Create a Pod named `web` running `nginx:1.27-alpine` in the `default` namespace. Verify it is Running. Then delete it.

<details>
<summary>Solution</summary>

```bash
kubectl run web --image=nginx:1.27-alpine --restart=Never
kubectl get pod web
kubectl delete pod web
```

Or declaratively:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  containers:
  - name: web
    image: nginx:1.27-alpine
```

```bash
kubectl apply -f pod.yaml
kubectl get pod web
kubectl delete pod web
```

</details>

---

## Exercise 2 — Deploy and Scale

**Task:** Create a Deployment named `app` with image `nginx:1.27-alpine` and 2 replicas. Then scale it to 4 replicas. Verify all 4 Pods are Running.

<details>
<summary>Solution</summary>

```bash
kubectl create deployment app --image=nginx:1.27-alpine --replicas=2
kubectl get pods -l app=app

kubectl scale deployment app --replicas=4
kubectl get pods -l app=app
# All 4 should be Running
```

</details>

---

## Exercise 3 — Rolling Update and Rollback

**Task:** Update the `app` Deployment image to `nginx:1.25-alpine`. Then roll back to the previous version. Verify the image is reverted.

<details>
<summary>Solution</summary>

```bash
kubectl set image deployment/app nginx=nginx:1.25-alpine
kubectl rollout status deployment/app
kubectl rollout history deployment/app

kubectl rollout undo deployment/app
kubectl get deployment app -o jsonpath='{.spec.template.spec.containers[0].image}'
# Should show nginx:1.27-alpine
```

</details>

---

## Exercise 4 — Create a Service

**Task:** Create a ClusterIP Service named `app-svc` that selects Pods with label `app=app` and exposes port 80. Verify the Endpoints are populated.

<details>
<summary>Solution</summary>

```bash
kubectl expose deployment app --name=app-svc --port=80 --target-port=80
kubectl get service app-svc
kubectl get endpoints app-svc
# Endpoints should list Pod IPs — not be empty
```

</details>

---

## Exercise 5 — Inject Configuration

**Task:** Create a ConfigMap named `app-config` with key `LOG_LEVEL=info`. Run a Pod that uses this ConfigMap as an environment variable. Verify the value inside the container.

<details>
<summary>Solution</summary>

```bash
kubectl create configmap app-config --from-literal=LOG_LEVEL=info

kubectl run test-pod \
  --image=nginx:1.27-alpine \
  --env="LOG_LEVEL_VALUE=$(kubectl get configmap app-config -o jsonpath='{.data.LOG_LEVEL}')" \
  --restart=Never
```

Or with a YAML that references the ConfigMap:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    envFrom:
    - configMapRef:
        name: app-config
```

```bash
kubectl exec test-pod -- env | grep LOG_LEVEL
# LOG_LEVEL=info
```

</details>

---

## Exercise 6 — Debug a failing Pod

**Task:** Apply this manifest and diagnose why the Pod is not starting:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: debug-me
spec:
  containers:
  - name: app
    image: nginx:does-not-exist
```

What is the status? What command gives you the most useful information?

<details>
<summary>Solution</summary>

```bash
kubectl apply -f debug-pod.yaml
kubectl get pod debug-me
# STATUS: ErrImagePull or ImagePullBackOff

kubectl describe pod debug-me
# Events section: Failed to pull image "nginx:does-not-exist": ... not found
```

**Fix:** Change the image tag to a valid one, e.g., `nginx:1.27-alpine`.

```bash
kubectl delete pod debug-me
# Edit the manifest and apply again
```

</details>

---

## Exercise 7 — Explore with kubectl

**Task:** Without looking at the module, answer these using kubectl:

1. How many Pods are running in the `kube-system` namespace?
2. What is the cluster's Kubernetes version?
3. What node is your `app` Deployment's Pods scheduled on?

<details>
<summary>Solution</summary>

```bash
# 1. Pods in kube-system
kubectl get pods -n kube-system | grep Running | wc -l

# 2. Cluster version
kubectl version

# 3. Which node
kubectl get pods -l app=app -o wide
# NODE column shows the node name
```

</details>
