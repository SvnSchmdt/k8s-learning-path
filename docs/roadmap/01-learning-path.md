# Learning Path

## Phase 0 — Prerequisites & Local Setup

**Goal:** A running local Kubernetes cluster. Every lab in this path runs locally.

**Topics:**
- Docker / Podman basics
- kind (Kubernetes in Docker)
- kubectl installation and configuration
- kubeconfig and cluster contexts

**Hands-On:**
- Install Docker, kind, kubectl, Helm
- Create a local kind cluster: `kind create cluster --name k8s-learning`
- Verify: `kubectl get nodes` shows Ready

**Checkpoint:**
- [ ] `kind create cluster` runs without errors
- [ ] `kubectl get nodes` shows the control-plane node as Ready
- [ ] You can explain what kubeconfig is and where it lives

**→ Lab 00: Local Cluster with kind**

---

## Phase 1 — Container Basics

**Goal:** Understand what containers are and how images work before touching Kubernetes.

**Topics:**
- Container images vs. containers
- Dockerfile basics
- Docker Hub and container registries
- Layer caching and image optimization

**Hands-On:**
- Run containers with `docker run`
- Build a custom image from a Dockerfile
- Push to Docker Hub or GitHub Container Registry

**Checkpoint:**
- [ ] You can explain the difference between an image and a container
- [ ] You can build an image, run it, exec into it, and read its logs
- [ ] You know what `latest` means and why it is problematic

---

## Phase 2 — Kubernetes Fundamentals

**Goal:** Understand the Kubernetes architecture and the declarative model.

**Topics:**
- Control plane components (API Server, etcd, Scheduler, Controller Manager)
- Worker node components (kubelet, kube-proxy, container runtime)
- Pods as the smallest deployable unit
- Namespaces
- Declarative vs. imperative configuration

**Hands-On:**
- Explore control plane: `kubectl get pods -n kube-system`
- Create your first Pod declaratively with YAML
- Understand Pod lifecycle states

**Checkpoint:**
- [ ] You can name all control plane components and explain their roles
- [ ] You can explain why declarative configuration is preferred
- [ ] You know what Pending, Running, and CrashLoopBackOff mean

---

## Phase 3 — Workloads: Pods, Deployments, ReplicaSets

**Goal:** Deploy applications and manage their lifecycle.

**Topics:**
- Deployment, ReplicaSet, Pod hierarchy
- Rolling updates and rollback
- Scaling
- Resource requests and limits (introduction)

**Hands-On:**
- Create a Deployment, scale it, perform a rolling update, roll back

**Checkpoint:**
- [ ] You can explain why you use Deployments instead of Pods directly
- [ ] You can perform a rolling update and rollback without looking at the docs
- [ ] You know what `kubectl rollout history` shows

**→ Lab 01: nginx Deployment**

---

## Phase 4 — Networking: Services & Ingress

**Goal:** Make applications reachable inside and outside the cluster.

**Topics:**
- ClusterIP, NodePort, LoadBalancer Service types
- Kubernetes DNS and service discovery
- Endpoints
- Ingress resources and Ingress Controllers
- Host-based and path-based routing

**Hands-On:**
- Create ClusterIP and NodePort Services
- Test with `kubectl port-forward` and internal DNS
- Install nginx Ingress Controller and configure host-based routing

**Checkpoint:**
- [ ] You can explain what happens when a Service has empty Endpoints
- [ ] You know the DNS name format for a Service
- [ ] You can explain the difference between Ingress Resource and Ingress Controller

**→ Lab 02: Service & Networking**
**→ Lab 04: Ingress**

---

## Phase 5 — Configuration & Storage

**Goal:** Decouple configuration from container images and persist data.

**Topics:**
- ConfigMaps and Secrets
- Environment variables vs. volume mounts
- PersistentVolumes, PersistentVolumeClaims
- StorageClasses

**Hands-On:**
- Inject ConfigMap and Secret into a Pod
- Mount a ConfigMap as files, observe live update behavior
- Create a PVC and verify data persists across Pod restarts

**Checkpoint:**
- [ ] You can explain when to use env vars vs. volume mounts for ConfigMaps
- [ ] You know the security considerations for Secrets
- [ ] You can explain what PVC Pending status means

**→ Lab 03: Config & Secrets**

---

## Phase 6 — Security, Packaging & Observability

**Goal:** Secure workloads, package them with Helm, and observe their health.

**Topics:**
- RBAC: Roles, ClusterRoles, RoleBindings, ServiceAccounts
- SecurityContext
- Helm: Charts, releases, values, upgrades, rollbacks
- Kustomize: bases, overlays
- Health probes: liveness, readiness, startup
- Metrics Server, kubectl top
- Logs and Events

**Hands-On:**
- Create a Role, RoleBinding, and verify with `kubectl auth can-i`
- Install a Helm chart, customize values, upgrade, and rollback
- Configure probes, simulate probe failure, observe Events

**Checkpoint:**
- [ ] You can explain the difference between liveness and readiness probes
- [ ] You can diagnose a CrashLoopBackOff from logs and events
- [ ] You can install, upgrade, and roll back a Helm release

**→ Lab 05: Helm Chart**
**→ Lab 06: Monitoring & Probes**

---

## Phase 7 — GitOps, Production Readiness & Next Steps

**Goal:** Deploy via GitOps, make workloads production-ready, and plan your next steps.

**Topics:**
- GitOps principles and pull-based deployments
- Argo CD: Application CRD, sync, self-heal
- Resource requests and limits
- HorizontalPodAutoscaler
- PodDisruptionBudget
- CKA/CKAD/CKS certification paths
- Homelab with k3s or kubeadm
- Operators and CRDs

**Hands-On:**
- Install Argo CD, connect to a Git repository, observe sync
- Configure HPA and trigger autoscaling
- Create a PodDisruptionBudget

**Checkpoint:**
- [ ] You can explain the GitOps pull model vs. push-based CI/CD
- [ ] You can create a production-ready Deployment checklist from memory
- [ ] You know your concrete next step after completing this path

**→ Lab 07: GitOps with Argo CD**
