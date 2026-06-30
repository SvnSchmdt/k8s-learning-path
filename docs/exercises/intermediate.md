# Intermediate Exercises

Work through these exercises after completing Modules 05–10 (Phases 4–6). They assume a running kind cluster and familiarity with Deployments and Services.

---

## Exercise 1 — RBAC: Restrict a ServiceAccount

**Task:** Create a namespace `restricted`. Create a ServiceAccount `reader` in that namespace. Grant it permission to `list` and `get` Pods — but not Deployments or Secrets. Verify with `kubectl auth can-i`.

<details>
<summary>Solution</summary>

```bash
kubectl create namespace restricted
kubectl create serviceaccount reader -n restricted

kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: restricted
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
EOF

kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: reader-binding
  namespace: restricted
subjects:
- kind: ServiceAccount
  name: reader
  namespace: restricted
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EOF

# Verify
kubectl auth can-i list pods -n restricted \
  --as=system:serviceaccount:restricted:reader
# yes

kubectl auth can-i list deployments -n restricted \
  --as=system:serviceaccount:restricted:reader
# no
```

</details>

---

## Exercise 2 — Storage: Persist Data Across Pod Restarts

**Task:** Create a PVC named `data-pvc` (1Gi, ReadWriteOnce). Deploy a Pod that mounts it at `/data`. Write a file to `/data/hello.txt`. Delete the Pod. Create a new Pod with the same PVC and verify the file still exists.

<details>
<summary>Solution</summary>

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data-pvc
spec:
  accessModes: [ReadWriteOnce]
  resources:
    requests:
      storage: 1Gi
EOF

kubectl run writer --image=busybox --restart=Never \
  --overrides='{"spec":{"volumes":[{"name":"data","persistentVolumeClaim":{"claimName":"data-pvc"}}],"containers":[{"name":"writer","image":"busybox","command":["sh","-c","echo hello > /data/hello.txt && sleep 3600"],"volumeMounts":[{"name":"data","mountPath":"/data"}]}]}}'

kubectl exec writer -- cat /data/hello.txt
# hello

kubectl delete pod writer

kubectl run reader --image=busybox --restart=Never \
  --overrides='{"spec":{"volumes":[{"name":"data","persistentVolumeClaim":{"claimName":"data-pvc"}}],"containers":[{"name":"reader","image":"busybox","command":["sleep","3600"],"volumeMounts":[{"name":"data","mountPath":"/data"}]}]}}'

kubectl exec reader -- cat /data/hello.txt
# hello  ← data persisted!
```

</details>

---

## Exercise 3 — Ingress: Multi-Service Routing

**Task:** Deploy two applications (`app-a` and `app-b`) with Services. Create a single Ingress that routes `app-a.local/` to `app-a` and `app-b.local/` to `app-b`. Test both routes.

*(Requires a kind cluster with port-mapping — see Lab 04)*

<details>
<summary>Hint</summary>

You need `ingressClassName: nginx` and two separate `rules` entries in the Ingress spec — one per host.

</details>

<details>
<summary>Solution</summary>

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-app
spec:
  ingressClassName: nginx
  rules:
  - host: app-a.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-a
            port:
              number: 80
  - host: app-b.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-b
            port:
              number: 80
```

</details>

---

## Exercise 4 — Helm: Custom Values File

**Task:** Install the `bitnami/nginx` chart with a custom values file that sets `replicaCount: 3` and `service.type: ClusterIP`. Upgrade it to `replicaCount: 5`. Roll back to the previous version.

<details>
<summary>Solution</summary>

```yaml
# values.yaml
replicaCount: 3
service:
  type: ClusterIP
```

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install my-nginx bitnami/nginx -f values.yaml

helm upgrade my-nginx bitnami/nginx -f values.yaml --set replicaCount=5
helm list
helm history my-nginx

helm rollback my-nginx 1
helm list
# replica count should be back to 3
```

</details>

---

## Exercise 5 — HPA: Autoscaling

**Task:** Create a Deployment with CPU `requests: 100m`. Create an HPA targeting 50% CPU utilization, min 2, max 8 replicas. Describe what would happen if CPU rises above 50% of the request.

<details>
<summary>Solution</summary>

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: app
  minReplicas: 2
  maxReplicas: 8
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

```bash
kubectl apply -f hpa.yaml
kubectl get hpa
kubectl describe hpa app-hpa
```

**Answer:** If average CPU across all Pods exceeds 50% of `100m` (i.e., > 50m per Pod), HPA adds replicas until average drops below 50%, up to a maximum of 8 replicas.

</details>
