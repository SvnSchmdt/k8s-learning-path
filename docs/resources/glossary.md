# Glossary

Definitions for the most important Kubernetes concepts. Technical terms are kept in English — that is how they appear in `kubectl` output, documentation, and job descriptions.

---

## A

**API Server (`kube-apiserver`)**
The central management component of the Kubernetes control plane. All interactions with the cluster — from `kubectl` to controllers to kubelets — go through the API Server.

**Application (Argo CD)**
An Argo CD custom resource that defines a source (Git repository + path) and a destination (cluster + namespace) for a GitOps deployment.

## C

**ClusterIP**
The default Service type in Kubernetes. Assigns the Service a virtual IP that is only reachable from within the cluster.

**ConfigMap**
A Kubernetes object for storing non-sensitive configuration data as key-value pairs. Can be injected into Pods as environment variables or mounted as files.

**Container**
A running instance of a container image. Containers are isolated processes that share the host OS kernel.

**Container Image**
An immutable, layered package containing application code, runtime, dependencies, and configuration. Built from a Dockerfile.

**Container Runtime**
Software that runs containers on a node. Examples: containerd, CRI-O.

**Control Plane**
The set of components that manage the Kubernetes cluster: API Server, etcd, Scheduler, and Controller Manager.

**CrashLoopBackOff**
A Pod status indicating a container crashes repeatedly. Kubernetes backs off exponentially before retrying.

**CRD (Custom Resource Definition)**
An extension of the Kubernetes API that allows defining custom resource types (e.g., Argo CD's `Application`).

## D

**Deployment**
A higher-level workload controller that manages ReplicaSets and provides rolling updates, rollbacks, and declarative scaling.

**DNS (CoreDNS)**
Kubernetes runs CoreDNS as the cluster-internal DNS server. Services are reachable by name: `<service>.<namespace>.svc.cluster.local`.

## E

**Endpoint**
An object that stores the IP addresses and ports of the Pods backing a Service. Updated automatically as Pods are created or deleted.

**etcd**
A distributed key-value store that holds the entire cluster state. All control plane components read from and write to etcd via the API Server.

## G

**GitOps**
A set of practices where Git is the single source of truth for the desired state of infrastructure. Changes are made through pull requests; a controller (like Argo CD) applies them automatically.

## H

**Helm**
A package manager for Kubernetes. Helm Charts bundle Kubernetes resources as templates with configurable values.

**HPA (HorizontalPodAutoscaler)**
A Kubernetes controller that automatically scales the number of Pod replicas based on observed CPU or memory utilization.

## I

**Image Pull Policy**
Controls when Kubernetes pulls a container image: `Always`, `IfNotPresent`, or `Never`.

**Ingress**
A Kubernetes resource that defines HTTP(S) routing rules. Requires an Ingress Controller to take effect.

**Ingress Controller**
A component that reads Ingress resources and routes traffic accordingly. Examples: nginx-ingress, Traefik.

## K

**kind (Kubernetes in Docker)**
A tool for running Kubernetes clusters inside Docker containers. Used for local development and testing.

**kubeconfig**
A configuration file (default: `~/.kube/config`) that defines clusters, users, and contexts for `kubectl`.

**kubelet**
An agent running on every worker node. Ensures that containers described in PodSpecs are running and healthy.

**kubectl**
The Kubernetes command-line tool. Communicates with the API Server over HTTPS.

**Kustomize**
A tool for customizing Kubernetes YAML configurations using a patch-based overlay system. Built into `kubectl`.

## L

**Label**
A key-value pair attached to a Kubernetes object. Used by selectors to identify sets of objects (e.g., Service → Pods).

**Liveness Probe**
A health check that determines whether a container is alive. Failure causes the container to be restarted.

**LoadBalancer**
A Service type that provisions an external load balancer (typically on cloud providers).

## N

**Namespace**
A virtual cluster within a cluster. Used to separate environments, teams, or applications within the same physical cluster.

**Node**
A physical or virtual machine that runs workloads. Each node runs a kubelet, kube-proxy, and a container runtime.

**NodePort**
A Service type that exposes the Service on a port on every Node, making it accessible from outside the cluster.

## O

**OOMKilled**
A container was killed because it exceeded its memory limit. OOM = Out Of Memory.

**Operator**
A Kubernetes pattern that uses a custom controller and CRDs to encode domain-specific operational knowledge (e.g., managing a database cluster).

## P

**PersistentVolume (PV)**
A storage resource in the cluster, provisioned by an admin or dynamically via a StorageClass.

**PersistentVolumeClaim (PVC)**
A request for storage by a Pod. The cluster binds the PVC to a matching PV.

**Pod**
The smallest deployable unit in Kubernetes. One or more containers sharing network namespace and storage volumes.

**PodDisruptionBudget (PDB)**
A policy that guarantees a minimum number of healthy replicas during voluntary disruptions (node drain, rolling update).

## R

**RBAC (Role-Based Access Control)**
Kubernetes' access control system. Permissions are defined in Roles and ClusterRoles, then granted via RoleBindings.

**Readiness Probe**
A health check that determines whether a container is ready to receive traffic. Failure removes the Pod from Service Endpoints.

**ReplicaSet**
A controller that ensures a specified number of Pod replicas are running at all times.

**Role**
An RBAC object that defines permissions within a specific namespace.

**RoleBinding**
Binds a Role (or ClusterRole) to a user, group, or ServiceAccount within a namespace.

## S

**Secret**
A Kubernetes object for storing sensitive data (passwords, tokens, certificates). Data is base64-encoded.

**SecurityContext**
Pod- or container-level security settings: run as non-root, read-only filesystem, drop Linux capabilities.

**Selector**
A label query that identifies a set of objects. Services use selectors to find their backing Pods.

**Service**
A stable network endpoint that load-balances traffic to a set of Pods identified by label selectors.

**ServiceAccount**
An identity for processes running inside a Pod. Used for authentication against the Kubernetes API.

**Startup Probe**
A health check that delays liveness checks until the startup probe passes. Prevents premature restarts of slow-starting applications.

**StorageClass**
Defines how Persistent Volumes are dynamically provisioned (provisioner, parameters, reclaim policy).

## V

**Volume**
A directory accessible to containers in a Pod. Types range from ephemeral (`emptyDir`) to persistent (`PersistentVolumeClaim`).

## W

**Worker Node**
A node that runs application workloads (Pods). Managed by the control plane.
