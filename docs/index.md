# Kubernetes Learning Path

A practical, hands-on Kubernetes learning path — from your very first Pod to GitOps and production readiness.

---

## Who this is for

This learning path is designed for:

- **Developers** who want to understand how their applications run in Kubernetes
- **System administrators** moving into cloud-native infrastructure
- **DevOps engineers** who need solid Kubernetes fundamentals
- **Students** preparing for CKA, CKAD, or other Kubernetes certifications

No prior Kubernetes experience required. Basic Linux command line skills and some familiarity with containers (Docker) will help you get started faster.

---

## What you will be able to do

By the end of this learning path, you will be able to:

- Set up a local Kubernetes cluster with kind
- Deploy applications using Pods, Deployments, and ReplicaSets
- Expose workloads with Services and Ingress
- Manage configuration with ConfigMaps and Secrets
- Implement persistent storage with PersistentVolumeClaims
- Secure workloads with RBAC and SecurityContexts
- Package and deploy applications with Helm and Kustomize
- Observe and debug running workloads with probes, logs, and metrics
- Implement GitOps workflows with Argo CD
- Apply production readiness patterns: probes, resource limits, HPA

---

## Start here

**Recommended order:**

1. Work through **[Modules](modules/index.md)** in order (00 → 15)
2. Complete the corresponding **[Labs](labs/index.md)** as you go
3. Challenge yourself with **[Exercises](exercises/index.md)** after each phase
4. Use the **[Cheat Sheet](resources/cheat-sheet.md)** as a reference while working
5. Check your progress with the **[Roadmap Checkpoints](roadmap/02-checkpoints.md)**

---

## Learning philosophy

!!! tip "How to get the most out of this learning path"
    - **Do not just read.** Run every command. Every. Single. One.
    - **Break things intentionally.** Change a label, delete a service, misconfigure a probe — then fix it.
    - **Answer the checkpoints.** If you cannot explain something in your own words, re-read the module.
    - **Clean up after each lab.** `kubectl delete namespace <name>` keeps your cluster tidy and your learning intentional.
    - **Slow down at the hard parts.** Confusion is a signal, not a failure.

---

## Quick overview

| Area | Description |
|------|-------------|
| [Roadmap](roadmap/index.md) | Phase-by-phase learning plan and checkpoints |
| [Modules](modules/index.md) | 16 structured learning modules (00–15) |
| [Labs](labs/index.md) | 8 hands-on labs with kind, kubectl, and Helm |
| [Exercises](exercises/index.md) | Beginner, intermediate, and troubleshooting challenges |
| [Resources](resources/index.md) | Glossary, cheat sheet, and official documentation links |
| [Examples](examples/index.md) | Ready-to-use YAML manifests |

---

## Local setup

```bash
# Prerequisites
brew install kind kubectl helm   # macOS

# Start your first cluster
kind create cluster --name k8s-learning

# Verify
kubectl get nodes
```

See [Lab 00](labs/00-local-cluster-kind/README.md) for the full cluster setup walkthrough.
