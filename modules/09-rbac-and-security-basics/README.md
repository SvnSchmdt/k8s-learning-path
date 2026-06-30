# Modul 09 – RBAC & Security Basics

## Ziel des Moduls

Nach diesem Modul verstehst du das Kubernetes RBAC-System und kannst Rollen, Bindings und ServiceAccounts konfigurieren. Du kennst grundlegende Security-Konzepte wie SecurityContext und Pod Security Standards.

## Warum ist das wichtig?

In einem Kubernetes-Cluster haben standardmäßig alle Pods weitreichende Rechte. Produktionscluster brauchen eine durchdachte Zugriffskontrolle. RBAC ist der Standard-Mechanismus, um zu kontrollieren, wer was im Cluster darf.

## Kernkonzepte

- **RBAC (Role-Based Access Control):** Steuert, wer (Subject) was (Verbs) mit welchen Ressourcen (Resources) machen darf.
- **Role:** Definiert Rechte innerhalb eines **Namespace**.
- **ClusterRole:** Definiert Rechte **cluster-weit** (über Namespace-Grenzen hinweg oder für nicht-namespaced Ressourcen).
- **RoleBinding:** Verknüpft eine Role mit einem Subject (User, Group, ServiceAccount) innerhalb eines Namespace.
- **ClusterRoleBinding:** Verknüpft eine ClusterRole mit einem Subject cluster-weit.
- **ServiceAccount:** Ein Pod-Identität innerhalb von Kubernetes. Pods können über ServiceAccounts auf die Kubernetes-API zugreifen.
- **SecurityContext:** Konfiguration auf Pod- oder Container-Ebene, die definiert, mit welchen Berechtigungen ein Container läuft (User, Capabilities, read-only Filesystem).

## RBAC-Modell

```
Subject (User/Group/ServiceAccount)
    ↕ (RoleBinding oder ClusterRoleBinding)
Role oder ClusterRole
    → Verbs (get, list, create, delete, ...)
    → Resources (pods, services, deployments, ...)
    → API Groups (core, apps, batch, ...)
```

## Praxisaufgabe

### ServiceAccount erstellen

```yaml
# serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-serviceaccount
  namespace: default
```

### Role erstellen (namespace-scoped)

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

### RoleBinding erstellen

```yaml
# rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: app-serviceaccount
  namespace: default
roleRef:
  kind: Role
  apiName: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### ServiceAccount an Pod binden

```yaml
spec:
  serviceAccountName: app-serviceaccount
  containers:
  - name: app
    image: nginx:1.27-alpine
```

### Rechte prüfen

```bash
# Kann der ServiceAccount Pods listen?
kubectl auth can-i list pods --as=system:serviceaccount:default:app-serviceaccount

# Eigene Rechte prüfen
kubectl auth can-i create deployments
kubectl auth can-i "*" "*"  # Admin-Prüfung
```

### SecurityContext

```yaml
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: app
    image: nginx:1.27-alpine
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
```

## Beispiel-Kommandos

```bash
# Roles anzeigen
kubectl get roles
kubectl get clusterroles

# RoleBindings anzeigen
kubectl get rolebindings
kubectl get clusterrolebindings

# Wer hat welche Rechte?
kubectl describe rolebinding pod-reader-binding

# RBAC-Prüfung
kubectl auth can-i get pods --as=system:serviceaccount:default:app-serviceaccount -n default
```

## Typische Fehler

- **Zu großzügige Rechte:** `cluster-admin` für alle ServiceAccounts ist ein häufiger Fehler. Least-Privilege-Prinzip anwenden.
- **Falsches `apiGroups`-Feld:** Core-Ressourcen (pods, services, configmaps) haben `apiGroups: [""]`. Deployments haben `apiGroups: ["apps"]`.
- **Namespace vergessen:** Eine Role in Namespace A gilt nicht in Namespace B. ClusterRole + RoleBinding für namespace-übergreifende Rechte nutzen.
- **ServiceAccount-Token:** Pods haben automatisch einen gemounteten ServiceAccount-Token. In sicheren Umgebungen abschalten: `automountServiceAccountToken: false`.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Role und ClusterRole?
- [ ] Was ist der Unterschied zwischen RoleBinding und ClusterRoleBinding?
- [ ] Was ist ein ServiceAccount, und warum brauchen Pods einen?
- [ ] Wie überprüfst du, ob ein ServiceAccount bestimmte Rechte hat?
- [ ] Was macht `readOnlyRootFilesystem: true` in einem SecurityContext?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] den Unterschied zwischen Role und ClusterRole erklären kannst
- [ ] eine Role, ein ServiceAccount und ein RoleBinding erstellt hast
- [ ] mit `kubectl auth can-i` die Rechte eines ServiceAccounts geprüft hast
- [ ] weißt was `readOnlyRootFilesystem: true` in einem SecurityContext bewirkt
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [RBAC Autorisierung](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [ServiceAccounts](https://kubernetes.io/docs/concepts/security/service-accounts/)
- [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [SecurityContext](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
