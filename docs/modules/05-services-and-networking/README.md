# Module 05 – Services & Networking

## Goal

After this module you understand why Services are necessary, know the three main Service types, and can expose Pods for both internal and external access. You understand how Kubernetes DNS enables service discovery.

## Why does this matter?

Pods are ephemeral — they come and go, and their IP addresses change. Services provide a stable endpoint that always points to the current healthy Pods. Without Services, you can't reliably communicate between applications inside a cluster or reach them from outside.

## Key Concepts

- **Service:** A stable network endpoint that load-balances traffic to a set of Pods selected by labels.
- **ClusterIP:** Default type. The Service is reachable only from inside the cluster. Assigned a stable virtual IP.
- **NodePort:** Exposes the Service on a port on every Node. Reachable from outside the cluster via `<NodeIP>:<NodePort>`.
- **LoadBalancer:** Provisions an external load balancer (cloud providers). In kind, use port-forward or NodePort instead.
- **Endpoints:** The actual Pod IPs that back a Service. Updated automatically as Pods come and go.
- **DNS:** Kubernetes runs a built-in DNS server (CoreDNS). Services are reachable by name: `<service>.<namespace>.svc.cluster.local`.

## Service Types Comparison

| Type | Reachable from | Use case |
|------|----------------|----------|
| ClusterIP | Inside cluster only | Service-to-service communication |
| NodePort | Outside cluster via Node IP | Dev/testing, no cloud load balancer |
| LoadBalancer | External load balancer IP | Production on cloud providers |

## Hands-On Task

### Create a ClusterIP Service

```yaml
# service-clusterip.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

```bash
kubectl apply -f service-clusterip.yaml
kubectl get service nginx-service
kubectl get endpoints nginx-service
```

### Test with port-forward

```bash
kubectl port-forward service/nginx-service 8080:80
curl http://localhost:8080
```

### Test internal DNS

```bash
# From inside a Pod
kubectl run test --rm -it --image=curlimages/curl -- sh
curl http://nginx-service.default.svc.cluster.local
```

### Create a NodePort Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
  type: NodePort
```

## Common Mistakes

- **Empty Endpoints:** The Service exists but has no Endpoints. The selector labels don't match any Pod labels. Check `kubectl get endpoints <name>`.
- **port vs targetPort confusion:** `port` is the Service port clients use. `targetPort` is the port on the Pod. They don't have to match.
- **Expecting LoadBalancer to work in kind:** kind doesn't provision external load balancers. Use NodePort or port-forward instead.

## Checkpoint

- [ ] Why do Pods need Services instead of being accessed directly by IP?
- [ ] What is the difference between ClusterIP and NodePort?
- [ ] How does Kubernetes DNS allow Pods to find Services by name?
- [ ] What does it mean when a Service has empty Endpoints?

## Definition of Done

You are done with this module when you:

- [ ] Have created a ClusterIP Service and accessed it via port-forward
- [ ] Have verified Endpoints are populated and match Pod IPs
- [ ] Can explain the DNS name format for a Service
- [ ] Can explain the difference between `port` and `targetPort`
- [ ] Can answer all checkpoint questions

## Further Reading

- [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
- [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
- [Connecting Applications with Services](https://kubernetes.io/docs/tutorials/services/connect-applications-service/)
