# Lab 04 – Ingress

## What you'll build

A kind cluster with port-mapping, two nginx Deployments with Services, a nginx Ingress Controller, and an Ingress resource that routes by hostname: `app-a.local` → Service A, `app-b.local` → Service B.

**Kubernetes objects used:** Deployment, Service, Ingress, Namespace (ingress-nginx)

```
Browser
  │
  └── localhost:8080 (kind port-mapping)
        └── ingress-nginx controller (port 80)
              ├── app-a.local → Service app-a → Pods
              └── app-b.local → Service app-b → Pods
```

## Goal

Two applications accessible at different hostnames via the same Ingress Controller port, verified with curl.

## Prerequisites

- [ ] Docker running
- [ ] kind and kubectl installed

> **Important:** This lab requires a fresh kind cluster with port-mapping configured. The standard cluster from Lab 00 does not have port 80/443 mapped.

## Step-by-Step

### Step 1: Create a kind cluster with port-mapping

```bash
cat <<EOF | kind create cluster --name ingress-lab --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 8080
    protocol: TCP
  - containerPort: 443
    hostPort: 8443
    protocol: TCP
EOF
```

Expected output:
```text
Creating cluster "ingress-lab" ...
 ✓ Ensuring node image ...
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
```

### Step 2: Install the nginx Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

Expected output:
```text
pod/ingress-nginx-controller-... condition met
```

### Step 3: Deploy two applications

```bash
kubectl apply -f manifests/deployments.yaml
kubectl get deployments
```

Expected output:
```text
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
app-a   1/1     1            1           15s
app-b   1/1     1            1           15s
```

### Step 4: Create the Ingress resource

```bash
kubectl apply -f manifests/ingress.yaml
kubectl get ingress
```

Expected output (ADDRESS may take 30s to appear):
```text
NAME          CLASS   HOSTS                      ADDRESS     PORTS   AGE
app-ingress   nginx   app-a.local,app-b.local    localhost   80      30s
```

### Step 5: Add local DNS entries

```bash
echo "127.0.0.1 app-a.local" | sudo tee -a /etc/hosts
echo "127.0.0.1 app-b.local" | sudo tee -a /etc/hosts
```

> **Note:** These entries remain in `/etc/hosts` after the lab. Remove them manually or during cleanup.

### Step 6: Test the routing

```bash
curl http://app-a.local:8080
curl http://app-b.local:8080
```

Expected output (each showing its app name in the response):
```text
# app-a.local → response from app-a
# app-b.local → response from app-b
```

## Validation

```bash
kubectl describe ingress app-ingress
# Should show both hosts and their backend services

kubectl logs -n ingress-nginx deployment/ingress-nginx-controller | tail -5
# Should show 200 OK responses for your curl requests
```

## Cleanup

```bash
# Remove /etc/hosts entries
sudo sed -i '' '/app-a.local/d' /etc/hosts
sudo sed -i '' '/app-b.local/d' /etc/hosts

# Delete the cluster
kind delete cluster --name ingress-lab
```

> **Caution:** `kind delete cluster` deletes all resources in the cluster permanently.

## Extension Task

Add TLS to the Ingress using a self-signed certificate:

```bash
# Generate a self-signed certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt \
  -subj "/CN=app-a.local"

# Create a TLS Secret
kubectl create secret tls app-a-tls --cert=tls.crt --key=tls.key
```

Then add a `tls:` section to your Ingress and test with `curl -k https://app-a.local:8443`.
