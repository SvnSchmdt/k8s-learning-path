# Module 06 – Ingress & DNS

## Goal

After this module you can route HTTP/HTTPS traffic via an Ingress Controller into a cluster. You understand what an Ingress Controller is, how routing rules work, and how to configure local DNS for testing.

## Why does this matter?

With Services alone you can make applications reachable, but for production HTTP traffic with multiple services, path-based routing, and TLS, you need Ingress. It is the standard way to route external HTTP requests to Kubernetes Services.

## Key Concepts

- **Ingress Resource:** A Kubernetes object that defines HTTP(S) routing rules. It specifies which URL maps to which Service.
- **Ingress Controller:** A component that reads Ingress resources and actually routes the traffic. Kubernetes does not ship with one — you must install one (e.g., nginx-ingress, Traefik, Contour).
- **Host-based routing:** Different domains (e.g., `app.example.com`, `api.example.com`) route to different Services.
- **Path-based routing:** Different paths (`/app`, `/api`) route to different Services.
- **TLS Termination:** HTTPS connections are terminated at the Ingress; traffic runs as HTTP internally.

## Ingress Controller vs. Ingress Resource

```
External → [Ingress Controller] → [Ingress Resource] → [Service] → [Pods]
```

The Ingress Controller is a running Deployment. The Ingress Resource is just configuration. Without a Controller, an Ingress Resource does nothing.

## Hands-On Task

### Install nginx Ingress Controller in kind

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

### Create an Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: nginx.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
kubectl describe ingress nginx-ingress
```

### Local DNS for testing

```bash
# Add to /etc/hosts
echo "127.0.0.1 nginx.local" | sudo tee -a /etc/hosts

# Port-forward the Ingress Controller
kubectl port-forward -n ingress-nginx service/ingress-nginx-controller 8080:80

# Test
curl http://nginx.local:8080
```

## Common Mistakes

- **No Ingress Controller installed:** The Ingress Resource is created but nothing happens. The controller is missing.
- **Wrong or missing `ingressClassName`:** The Ingress is ignored. The class must match the installed controller.
- **Backend Service not found:** The Ingress cannot reach the Service. Check Service name and port in the Ingress spec.

## Checkpoint

- [ ] What is the difference between an Ingress Resource and an Ingress Controller?
- [ ] How do you route `/app` to Service A and `/api` to Service B?
- [ ] What is the advantage of Ingress over NodePort for production HTTP services?
- [ ] What is TLS termination at the Ingress?

## Definition of Done

You are done with this module when you:

- [ ] Can explain the difference between Ingress Resource and Ingress Controller
- [ ] Have installed an Ingress Controller and verified it is running
- [ ] Have configured host-based routing for at least one Service and tested it
- [ ] Can explain what TLS termination at the Ingress means
- [ ] Can answer all checkpoint questions

## Further Reading

- [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)
- [nginx Ingress Controller](https://kubernetes.github.io/ingress-nginx/)
