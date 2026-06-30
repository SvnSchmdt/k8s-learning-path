# Checkpoints

Use this checklist to verify your progress. Do not move to the next phase until you can answer all questions in the current phase from memory — without looking at the module.

---

## Phase 0 — Prerequisites

- [ ] Docker is installed and `docker version` works
- [ ] kind is installed and `kind version` works
- [ ] kubectl is installed and `kubectl version --client` works
- [ ] Helm is installed and `helm version` works
- [ ] You have created a kind cluster and verified it with `kubectl get nodes`
- [ ] You can explain what kubeconfig is and where `~/.kube/config` lives
- [ ] You can switch between cluster contexts with `kubectl config use-context`

---

## Phase 1 — Container Basics

- [ ] You can explain the difference between an image and a running container
- [ ] You can build an image from a Dockerfile
- [ ] You can run a container, exec into it, read its logs, and stop it
- [ ] You know what a container registry is and have pushed an image to one
- [ ] You can explain why `latest` is a problematic tag in production
- [ ] You understand what layer caching is and how it affects build speed

---

## Phase 2 — Kubernetes Fundamentals

- [ ] You can name all four control plane components and explain what each one does
- [ ] You can explain what etcd stores and why it matters
- [ ] You can explain the difference between declarative (`kubectl apply`) and imperative (`kubectl create`) configuration
- [ ] You know the Pod lifecycle states: Pending, Running, Succeeded, Failed, CrashLoopBackOff
- [ ] You can create a Pod from a YAML manifest and verify it is Running
- [ ] You can explain what a Namespace is and list all namespaces

---

## Phase 3 — Workloads

- [ ] You can explain the Deployment → ReplicaSet → Pod hierarchy
- [ ] You can scale a Deployment up and down
- [ ] You can perform a rolling update and verify with `kubectl rollout status`
- [ ] You can roll back a Deployment to a previous version
- [ ] You understand why you should never edit Pods directly when managed by a Deployment
- [ ] You can explain what resource requests and limits are (not yet how to tune them)

---

## Phase 4 — Networking

- [ ] You can explain the difference between ClusterIP, NodePort, and LoadBalancer
- [ ] You know the Kubernetes DNS format: `<service>.<namespace>.svc.cluster.local`
- [ ] You can check if a Service has Endpoints and know what empty Endpoints means
- [ ] You can access a Service from inside the cluster using its DNS name
- [ ] You can explain the difference between an Ingress Resource and an Ingress Controller
- [ ] You can configure host-based routing for two Services in one Ingress

---

## Phase 5 — Configuration & Storage

- [ ] You can create a ConfigMap and inject it as both environment variables and mounted files
- [ ] You know the update behavior difference: env vars are static, volume mounts update live
- [ ] You can create a Secret and access its values from inside a Pod
- [ ] You understand why Secrets must not be committed to Git
- [ ] You can create a PVC and bind it to a Pod
- [ ] You can explain what `Pending` PVC status means and what causes it
- [ ] You know the difference between `emptyDir` and a PVC

---

## Phase 6 — Security, Packaging & Observability

- [ ] You can create a Role, RoleBinding, and ServiceAccount
- [ ] You can verify permissions with `kubectl auth can-i`
- [ ] You can explain the difference between Role and ClusterRole
- [ ] You know what `runAsNonRoot: true` does in a SecurityContext
- [ ] You can install a Helm Chart from a repository with custom values
- [ ] You can upgrade and roll back a Helm release
- [ ] You can explain the difference between Helm and Kustomize
- [ ] You can configure liveness and readiness probes
- [ ] You know what happens when a liveness probe fails vs. a readiness probe fails
- [ ] You can diagnose a CrashLoopBackOff using `kubectl logs --previous` and `kubectl describe pod`
- [ ] You can use `kubectl top pods` and know what Metrics Server does

---

## Phase 7 — GitOps & Production Readiness

- [ ] You can explain the four GitOps principles
- [ ] You can explain the difference between push-based CI/CD and pull-based GitOps
- [ ] You have installed Argo CD and deployed an Application that stays Synced/Healthy
- [ ] You understand what Argo CD self-heal does
- [ ] You can configure resource requests and limits for a container
- [ ] You can create an HPA and explain what `averageUtilization: 70` means
- [ ] You can create a PodDisruptionBudget and explain what it protects against
- [ ] You know your concrete next step: CKA exam, homelab, or a personal project

---

## Full Production Readiness Checklist

When you can review a Deployment spec and answer all of these from memory, you are ready for a junior cloud/DevOps role:

- [ ] Image tag is specific (not `latest`)
- [ ] `replicas >= 2`
- [ ] Resource requests and limits set
- [ ] Liveness probe configured
- [ ] Readiness probe configured
- [ ] `runAsNonRoot: true`
- [ ] Secrets come from a Secret object (not hardcoded)
- [ ] HPA configured for CPU/memory
- [ ] PodDisruptionBudget defined
- [ ] Application handles SIGTERM gracefully
