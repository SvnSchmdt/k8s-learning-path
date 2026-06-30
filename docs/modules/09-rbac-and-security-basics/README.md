# Module 09 – RBAC & Security Basics

## Goal

After this module you understand Kubernetes RBAC and can configure Roles, ClusterRoles, RoleBindings, and ServiceAccounts. You know how to restrict what a workload or user is allowed to do in the cluster.

## Why does this matter?

By default, every Pod runs with a ServiceAccount that has access to the API server. In a shared cluster, this is a significant security risk. RBAC (Role-Based Access Control) is the mechanism to enforce least-privilege access — only allow what is explicitly needed.

## Key Concepts

- **ServiceAccount:** An identity for processes running in a Pod. Pods use ServiceAccounts to authenticate against the API server.
- **Role:** Grants permissions to resources within a single namespace. Example: allow reading Pods in the `default` namespace.
- **ClusterRole:** Like a Role, but applies cluster-wide or can be reused across namespaces.
- **RoleBinding:** Binds a Role to a user, group, or ServiceAccount within a namespace.
- **ClusterRoleBinding:** Binds a ClusterRole cluster-wide.
- **SecurityContext:** Pod/container-level security settings: run as non-root, read-only filesystem, drop capabilities.

## RBAC Hierarchy

```
Who?              What?          Where?
ServiceAccount  + Role         + RoleBinding         = namespace-scoped
ServiceAccount  + ClusterRole  + ClusterRoleBinding  = cluster-wide
```

## Hands-On Task

### Create a ServiceAccount

```bash
kubectl create serviceaccount my-app-sa
kubectl get serviceaccount my-app-sa
```

### Create a Role and RoleBinding

```yaml
# role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

```yaml
# rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-app-sa
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f role.yaml -f rolebinding.yaml

# Verify with auth can-i
kubectl auth can-i list pods --as=system:serviceaccount:default:my-app-sa
kubectl auth can-i delete pods --as=system:serviceaccount:default:my-app-sa
```

### SecurityContext

```yaml
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
  containers:
  - name: app
    image: nginx:1.27-alpine
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
```

## Common Mistakes

- **Using ClusterRoleBinding when RoleBinding is sufficient:** Prefer namespace-scoped bindings. Cluster-wide permissions are a significant attack surface.
- **Forgetting `apiGroups`:** Core resources (Pods, Services, ConfigMaps) use `apiGroups: [""]`. Other resources need their API group (e.g., `apps` for Deployments).
- **Running containers as root:** Always set `runAsNonRoot: true` in production.

## Checkpoint

- [ ] What is the difference between a Role and a ClusterRole?
- [ ] What does a RoleBinding do?
- [ ] How do you verify what a ServiceAccount is allowed to do?
- [ ] What does `runAsNonRoot: true` in a SecurityContext enforce?

## Definition of Done

You are done with this module when you:

- [ ] Have created a ServiceAccount, Role, and RoleBinding
- [ ] Have verified permissions with `kubectl auth can-i`
- [ ] Can explain the difference between Role and ClusterRole
- [ ] Can explain what SecurityContext settings are and why they matter
- [ ] Can answer all checkpoint questions

## Further Reading

- [RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [Configure Service Accounts](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)
- [Security Contexts](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
