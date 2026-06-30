# kubectl Cheat Sheet

---

## Standard Troubleshooting Workflow

When something is broken, follow these 10 steps in order:

```bash
# 1. Find the broken resource
kubectl get pods -A | grep -v Running

# 2. Get detailed status
kubectl get pod <name> -o wide

# 3. Read the events
kubectl describe pod <name>

# 4. Read the logs
kubectl logs <name>
kubectl logs <name> --previous         # logs from crashed container

# 5. Check endpoints
kubectl get endpoints <service-name>   # must not be empty

# 6. Test network connectivity
kubectl run test --rm -it --image=curlimages/curl -- sh
# curl http://<service>.<namespace>.svc.cluster.local

# 7. Check resource usage
kubectl top pod <name>

# 8. Check cluster-wide events
kubectl get events --sort-by='.lastTimestamp'

# 9. Check probe configuration
kubectl get pod <name> -o yaml | grep -A10 "livenessProbe"

# 10. Check RBAC
kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa>
```

---

## Context & Cluster

```bash
kubectl config get-contexts           # list all contexts
kubectl config current-context        # show active context
kubectl config use-context <name>     # switch context
kubectl config view                   # show full kubeconfig
kubectl cluster-info                  # cluster API URL
```

---

## Pods

```bash
kubectl get pods                      # default namespace
kubectl get pods -A                   # all namespaces
kubectl get pods -n <ns>              # specific namespace
kubectl get pods -o wide              # with node and IP
kubectl get pods -w                   # watch mode
kubectl get pods --show-labels        # show labels

kubectl describe pod <name>           # full detail + events
kubectl logs <name>                   # container logs
kubectl logs <name> -c <container>    # specific container
kubectl logs <name> --previous        # previous container
kubectl logs <name> -f                # follow live

kubectl exec -it <name> -- sh         # interactive shell
kubectl exec <name> -- env            # print env vars
kubectl exec <name> -- cat /file      # read file

kubectl delete pod <name>             # delete pod
kubectl delete pod <name> --force     # force delete (use carefully)
```

---

## Deployments

```bash
kubectl create deployment <name> --image=<img> --replicas=<n>
kubectl get deployments
kubectl describe deployment <name>

kubectl scale deployment <name> --replicas=<n>
kubectl set image deployment/<name> <container>=<image>

kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
kubectl rollout undo deployment/<name> --to-revision=<n>
```

---

## Services

```bash
kubectl get services
kubectl get svc                       # shorthand
kubectl describe service <name>
kubectl get endpoints <name>

kubectl expose deployment <name> --port=80 --target-port=8080
kubectl port-forward service/<name> <local>:<remote>
```

---

## ConfigMaps & Secrets

```bash
kubectl create configmap <name> --from-literal=KEY=value
kubectl create configmap <name> --from-file=<file>
kubectl get configmap <name> -o yaml
kubectl describe configmap <name>

kubectl create secret generic <name> --from-literal=KEY=value
kubectl get secret <name> -o yaml
kubectl get secret <name> -o jsonpath='{.data.KEY}' | base64 -d
```

---

## RBAC

```bash
kubectl get roles,rolebindings -n <ns>
kubectl get clusterroles,clusterrolebindings
kubectl describe role <name> -n <ns>

kubectl auth can-i <verb> <resource>
kubectl auth can-i list pods --as=system:serviceaccount:<ns>:<sa>
kubectl auth can-i list pods --as=<user>
```

---

## Storage

```bash
kubectl get pvc                        # PersistentVolumeClaims
kubectl get pv                         # PersistentVolumes
kubectl get storageclass               # StorageClasses
kubectl describe pvc <name>            # why is it Pending?
```

---

## Ingress

```bash
kubectl get ingress
kubectl describe ingress <name>
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller
```

---

## Helm

```bash
helm repo add <name> <url>
helm repo update
helm search repo <keyword>
helm install <release> <chart> --namespace <ns> --create-namespace
helm list -A
helm status <release> -n <ns>
helm upgrade <release> <chart> --set key=value
helm history <release>
helm rollback <release> <revision>
helm uninstall <release>
helm template <release> <chart>        # render without installing
helm get values <release>              # show applied values
```

---

## Debugging

```bash
# Generate YAML without applying
kubectl create deployment test --image=nginx --dry-run=client -o yaml

# Apply from stdin
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
...
EOF

# Diff between live and local
kubectl diff -f manifest.yaml

# Force recreate (delete and re-apply)
kubectl replace --force -f manifest.yaml

# Run a temporary debug container
kubectl run debug --rm -it --image=busybox -- sh
kubectl run curl --rm -it --image=curlimages/curl -- sh

# Copy files to/from Pod
kubectl cp <pod>:/remote/path ./local/path
kubectl cp ./local/path <pod>:/remote/path
```

---

## Output Formats

```bash
kubectl get pod <name> -o yaml          # full YAML
kubectl get pod <name> -o json          # full JSON
kubectl get pod <name> -o wide          # extra columns
kubectl get pods -o name                # just names
kubectl get pod <name> -o jsonpath='{.status.podIP}'
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get nodes -o custom-columns=NAME:.metadata.name,STATUS:.status.conditions[-1].type
```

---

## Useful Aliases (CKA exam ready)

```bash
alias k=kubectl
export do="--dry-run=client -o yaml"
source <(kubectl completion bash)

# Usage:
k create deployment nginx --image=nginx $do
k get po -A
```
