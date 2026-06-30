# Learning Path Overview

## Vision

This learning path takes you from zero Kubernetes knowledge to a solid understanding of production-ready Kubernetes operations. By the end, you can independently deploy, configure, observe, secure, and troubleshoot workloads in a Kubernetes cluster.

## Learning Philosophy

**Hands-on over theory.** Every concept is introduced through something you can run immediately. Reading without doing produces shallow knowledge that fades quickly.

**Progressive complexity.** Each module and lab builds on the previous one. Don't skip ahead — the order is deliberate.

**Learn by breaking things.** The exercises and extension tasks are designed to make you intentionally misconfigure things and then fix them. That is how real debugging instincts develop.

**Checkpoints are mandatory.** If you can't answer the checkpoint questions without looking at the module, read it again. The checkpoints test understanding, not recall.

## Phase Overview

| Phase | Topic | Modules | Labs |
|-------|-------|---------|------|
| 0 | Prerequisites & Setup | 00 | Lab 00 |
| 1 | Container Basics | 01 | — |
| 2 | Kubernetes Fundamentals | 02, 03 | — |
| 3 | Workloads | 04 | Lab 01 |
| 4 | Networking | 05, 06 | Lab 02, 03, 04 |
| 5 | Configuration & Storage | 07, 08 | Lab 03 |
| 6 | Security, Packaging & Observability | 09, 10, 11, 12 | Lab 05, 06 |
| 7 | GitOps & Production | 13, 14, 15 | Lab 07 |

## Estimated Time

- **Total:** 40–60 hours depending on prior experience
- **Per module:** 1–2 hours
- **Per lab:** 30–90 minutes

Work at your own pace. Speed is not the goal — depth is.

## Tools You Will Use

| Tool | Purpose |
|------|---------|
| [kind](https://kind.sigs.k8s.io/) | Local Kubernetes cluster |
| [kubectl](https://kubernetes.io/docs/reference/kubectl/) | Cluster interaction |
| [Helm](https://helm.sh/) | Application packaging |
| [Argo CD](https://argo-cd.readthedocs.io/) | GitOps deployments |
| [Prometheus + Grafana](https://prometheus.io/) | Metrics and dashboards |
