# Module 15 – Next Steps: CKA & Homelab

## Goal

After this module you know how to deepen your Kubernetes knowledge — through certifications, your own homelab cluster, or real projects. You have a concrete next step.

## Why does this matter?

A local kind cluster is an excellent learning tool — but it is not a production cluster. The next step is applying what you've learned to real or more realistic clusters. The CKA certification validates your knowledge externally.

## Key Concepts

- **CKA (Certified Kubernetes Administrator):** The most important Kubernetes certification. A practical, hands-on exam from the CNCF — not multiple choice, but real cluster tasks.
- **CKAD (Certified Kubernetes Application Developer):** Focused on application development in Kubernetes, less on administration.
- **CKS (Certified Kubernetes Security Specialist):** Advanced security certification (CKA is a prerequisite).
- **kubeadm:** Official tool for setting up Kubernetes clusters on existing machines.
- **k3s:** Lightweight Kubernetes distribution, ideal for homelabs and edge computing.
- **Operator Pattern:** A Kubernetes extension pattern that encodes domain-specific knowledge in custom controllers.
- **CRD (Custom Resource Definition):** Allows defining custom Kubernetes resource types.

## CKA Exam Breakdown

| Domain | Weight |
|--------|--------|
| Storage | 10% |
| Troubleshooting | 30% |
| Workloads & Scheduling | 15% |
| Cluster Architecture, Installation & Configuration | 25% |
| Services & Networking | 20% |

## CKA Preparation Tips

```bash
# Set these aliases at the start of the exam — saves significant time
alias k=kubectl
export do="--dry-run=client -o yaml"

# Quickly generate YAML
kubectl create deployment nginx --image=nginx $do

# Enable autocomplete
source <(kubectl completion bash)
```

**Recommended resources:**
- **killer.sh** — Two exam simulations included with CKA purchase. The most realistic practice environment.
- **KodeKloud** — CKA course with integrated hands-on labs

## Homelab Setup

### Option 1: k3s (recommended for homelabs)

```bash
# On a VM or Raspberry Pi
curl -sfL https://get.k3s.io | sh -

# kubeconfig
cat /etc/rancher/k3s/k3s.yaml

# Verify
kubectl get nodes
```

### Option 2: kubeadm (real Kubernetes)

```bash
# Prerequisites: 2 VMs (Ubuntu), container runtime installed

# Initialize control plane
kubeadm init --pod-network-cidr=10.244.0.0/16

# Copy kubeconfig
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# Install network plugin (e.g., Flannel)
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

# Join a worker node
kubeadm join <control-plane-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## Real Projects as Next Steps

1. **Containerize your own application and deploy it to Kubernetes**
   - Write a Dockerfile, push the image, create Deployment + Service + Ingress + Helm Chart + GitOps

2. **Set up a monitoring stack**
   - Prometheus + Grafana via Helm, custom dashboards, alerting

3. **Multi-tenant cluster**
   - Namespaces per team, RBAC per team, ResourceQuotas

4. **Disaster recovery simulation**
   - etcd backup and restore, node failure simulation, PodDisruptionBudget testing

## Operators & CRDs (Introduction)

```bash
# List CRDs in your cluster
kubectl get crds

# Example: Argo CD uses CRDs
kubectl get crds | grep argoproj
```

## Checkpoint

- [ ] What is the difference between CKA, CKAD, and CKS?
- [ ] Why is k3s well-suited for a homelab?
- [ ] What is a Kubernetes Operator, and what problem does it solve?
- [ ] What is a CRD?
- [ ] What are your concrete next three steps after completing this learning path?

## Definition of Done

You are done with this module when you:

- [ ] Can explain the difference between CKA, CKAD, and CKS
- [ ] Have decided on a concrete next step (homelab, CKA, own project)
- [ ] Can explain the Operator concept at a high level
- [ ] Know what a CRD is and how to find existing CRDs in your cluster
- [ ] Can answer all checkpoint questions

## Further Reading

- [CKA Certification](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)
- [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)
- [k3s](https://k3s.io/)
- [Kubernetes Operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)
- [Custom Resource Definitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
