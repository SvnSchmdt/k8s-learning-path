# kubectl Cheat Sheet

Wichtige kubectl-Kommandos für den täglichen Einsatz.

---

## Cluster-Info & Kontext

```bash
kubectl cluster-info                          # Cluster-Informationen
kubectl config current-context               # Aktueller Kontext
kubectl config get-contexts                  # Alle Kontexte
kubectl config use-context <name>            # Kontext wechseln
kubectl config view                          # kubeconfig anzeigen
kubectl version                              # Client + Server Version
kubectl api-resources                        # Alle Ressourcentypen
```

---

## Namespaces

```bash
kubectl get namespaces                        # Namespaces auflisten
kubectl create namespace <name>              # Namespace erstellen
kubectl delete namespace <name>              # Namespace löschen (alle Ressourcen!)
kubectl config set-context --current --namespace=<ns>  # Standard-Namespace setzen
```

---

## Pods

```bash
kubectl get pods                              # Pods im aktuellen Namespace
kubectl get pods -A                          # Pods in allen Namespaces
kubectl get pods -n <namespace>              # Pods in spezifischem Namespace
kubectl get pods -o wide                     # Mit Node und IP
kubectl get pods --show-labels               # Mit Labels
kubectl get pods -l app=nginx                # Nach Label filtern
kubectl get pods -w                          # Watch-Modus

kubectl describe pod <name>                  # Pod-Details und Events
kubectl logs <name>                          # Pod-Logs
kubectl logs <name> -f                       # Logs folgen
kubectl logs <name> --previous               # Logs vor letztem Crash
kubectl logs <name> -c <container>           # Spezifischer Container
kubectl logs -l app=nginx --tail=50          # Logs von Pods mit Label

kubectl exec -it <name> -- sh               # Shell im Pod
kubectl exec <name> -- <befehl>             # Befehl im Pod ausführen
kubectl exec -it <name> -c <container> -- sh  # Container auswählen

kubectl run <name> --image=<image>           # Pod starten (imperativ)
kubectl run test --image=busybox --rm -it --restart=Never -- sh  # Temp-Pod

kubectl delete pod <name>                    # Pod löschen
kubectl delete pod <name> --force            # Pod sofort löschen
```

---

## Deployments

```bash
kubectl get deployments                       # Deployments anzeigen
kubectl describe deployment <name>           # Details
kubectl create deployment <name> --image=<image>  # Deployment erstellen
kubectl apply -f deployment.yaml             # Aus Datei erstellen/aktualisieren

kubectl scale deployment <name> --replicas=3 # Skalieren
kubectl set image deployment/<name> <container>=<image>  # Image aktualisieren

kubectl rollout status deployment/<name>     # Update-Status
kubectl rollout history deployment/<name>    # Update-Historie
kubectl rollout undo deployment/<name>       # Rollback
kubectl rollout undo deployment/<name> --to-revision=2  # Zu bestimmter Version

kubectl delete deployment <name>             # Deployment löschen
```

---

## Services

```bash
kubectl get services                          # Services anzeigen
kubectl get svc                              # Kurzform
kubectl describe service <name>              # Details
kubectl get endpoints <name>                 # Endpoints anzeigen

kubectl expose deployment <name> --port=80 --type=ClusterIP  # Service erstellen
kubectl port-forward service/<name> 8080:80  # Lokaler Port-Forward
kubectl port-forward pod/<name> 8080:80      # Port-Forward zu Pod

kubectl delete service <name>                # Service löschen
```

---

## ConfigMaps & Secrets

```bash
kubectl get configmaps                        # ConfigMaps anzeigen
kubectl get cm                               # Kurzform
kubectl describe configmap <name>            # Details
kubectl create configmap <name> --from-literal=key=value  # Erstellen
kubectl create configmap <name> --from-file=<datei>       # Aus Datei

kubectl get secrets                           # Secrets anzeigen
kubectl describe secret <name>               # Details (ohne Werte!)
kubectl create secret generic <name> --from-literal=key=value  # Erstellen
kubectl get secret <name> -o jsonpath='{.data.key}' | base64 -d  # Wert lesen

kubectl delete configmap <name>              # Löschen
kubectl delete secret <name>                 # Löschen
```

---

## Storage

```bash
kubectl get pvc                               # PersistentVolumeClaims
kubectl get pv                               # PersistentVolumes
kubectl get storageclass                     # StorageClasses
kubectl describe pvc <name>                  # PVC-Details (inkl. Bound-Status)
kubectl delete pvc <name>                    # PVC löschen
```

---

## RBAC

```bash
kubectl get roles                             # Roles (namespace-scoped)
kubectl get clusterroles                     # ClusterRoles
kubectl get rolebindings                     # RoleBindings
kubectl get clusterrolebindings              # ClusterRoleBindings
kubectl describe rolebinding <name>          # Details

kubectl auth can-i <verb> <resource>         # Eigene Rechte prüfen
kubectl auth can-i get pods --as=system:serviceaccount:default:my-sa  # Als SA prüfen
```

---

## Nodes

```bash
kubectl get nodes                             # Nodes anzeigen
kubectl get nodes -o wide                    # Mit IPs und OS
kubectl describe node <name>                 # Details (Ressourcen, Pods, etc.)
kubectl top nodes                            # Ressourcenverbrauch (Metrics Server)
kubectl top pods                             # Pod-Ressourcenverbrauch
kubectl cordon <node>                        # Node für neue Pods sperren
kubectl drain <node> --ignore-daemonsets     # Node für Wartung leeren
kubectl uncordon <node>                      # Node wieder freigeben
```

---

## Standard-Troubleshooting-Workflow

Wenn etwas nicht funktioniert, diese Schritte **der Reihe nach** ausführen:

```bash
# Schritt 1: Status prüfen – was ist der aktuelle Zustand?
kubectl get pods
kubectl get pods -A  # alle Namespaces

# Schritt 2: Events prüfen – was hat Kubernetes zuletzt getan?
kubectl get events --sort-by=.lastTimestamp
kubectl get events -A --sort-by=.lastTimestamp

# Schritt 3: describe nutzen – Details und Events zu einer Ressource
kubectl describe pod <name>
kubectl describe deployment <name>
kubectl describe service <name>

# Schritt 4: Logs lesen – was sagt die Anwendung selbst?
kubectl logs <pod-name>
kubectl logs <pod-name> --previous    # bei CrashLoopBackOff!
kubectl logs <pod-name> --all-containers

# Schritt 5: In Pod exec'en – direkt im Container nachschauen
kubectl exec -it <pod-name> -- sh
kubectl exec -it <pod-name> -- curl localhost:8080/healthz

# Schritt 6: Service Selector prüfen – zeigt der Service auf die richtigen Pods?
kubectl describe service <name>       # Selector anzeigen
kubectl get pods --show-labels        # Labels aller Pods

# Schritt 7: Endpoints prüfen – hat der Service Pods gefunden?
kubectl get endpoints <service-name>
# Leer = Selector findet keine Pods

# Schritt 8: Rollout-Status prüfen – steckt ein Update fest?
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>

# Schritt 9: Ressourcen prüfen – ist Node/Pod überlastet?
kubectl top nodes
kubectl top pods
kubectl describe node <node-name>     # Ressourcen-Druck prüfen

# Schritt 10: Änderung rückgängig machen – zurück zur funktionierenden Version
kubectl rollout undo deployment/<name>
kubectl rollout undo deployment/<name> --to-revision=<n>
```

---

## Debugging

```bash
kubectl describe <resource> <name>           # Details & Events
kubectl get events --sort-by=.lastTimestamp  # Events nach Zeit
kubectl get events -A --sort-by=.lastTimestamp  # Alle Namespaces

kubectl debug -it <pod> --image=busybox      # Ephemerer Debug-Container
kubectl cp <pod>:/pfad/datei ./lokal         # Datei aus Pod kopieren

# Ressourcen-YAML anzeigen
kubectl get deployment <name> -o yaml
kubectl get pod <name> -o json
kubectl get pod <name> -o jsonpath='{.status.podIP}'
```

---

## Dry-Run & Output-Generierung

```bash
# YAML ohne Anwenden generieren
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml

# Apply ohne Ausführen
kubectl apply -f manifest.yaml --dry-run=client

# Diff gegen Cluster
kubectl diff -f manifest.yaml
```

---

## Nützliche Aliase (in ~/.bashrc oder ~/.zshrc)

```bash
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
alias kaf='kubectl apply -f'

# Autovervollständigung
source <(kubectl completion bash)   # bash
source <(kubectl completion zsh)    # zsh

complete -F __start_kubectl k       # Alias nutzt auch Completion
```

---

## Kurzformen (Resource-Abkürzungen)

| Lang | Kurz |
|------|------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| namespaces | ns |
| configmaps | cm |
| persistentvolumeclaims | pvc |
| persistentvolumes | pv |
| serviceaccounts | sa |
| horizontalpodautoscalers | hpa |
| ingresses | ing |
| storageclasses | sc |
