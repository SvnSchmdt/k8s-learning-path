# Checkpoints – Lernfortschritt prüfen

Diese Checkliste hilft dir, deinen Fortschritt zu verfolgen. Hake ab, was du wirklich kannst – nicht nur, was du gelesen hast.

---

## Phase 0: Setup

- [ ] kind ist installiert (`kind version` gibt eine Versionsnummer aus)
- [ ] `kind create cluster` erstellt erfolgreich einen Cluster
- [ ] `kubectl cluster-info` zeigt den laufenden API-Server
- [ ] `kubectl get nodes` zeigt einen Node mit Status `Ready`
- [ ] Du weißt, was eine kubeconfig ist und wo sie liegt

---

## Phase 1: Container & K8s Basics

- [ ] Du kannst erklären, was ein Container-Image ist
- [ ] Du kannst erklären, was ein Container ist und wie er sich von einer VM unterscheidet
- [ ] Du weißt, was Kubernetes ist und welche Probleme es löst
- [ ] Du kannst die Hauptkomponenten des Control Plane nennen (API Server, etcd, Scheduler, Controller Manager)
- [ ] Du kannst die Node-Komponenten nennen (kubelet, kube-proxy, Container Runtime)
- [ ] Du hast einen Pod manuell gestartet: `kubectl run`
- [ ] Du kannst Pod-Logs abrufen: `kubectl logs`
- [ ] Du kannst in einen Pod exec'en: `kubectl exec -it`
- [ ] Du weißt, was ein Namespace ist und hast einen erstellt

---

## Phase 2: Workloads & Networking

- [ ] Du kannst den Unterschied zwischen Pod, ReplicaSet und Deployment erklären
- [ ] Du hast ein Deployment mit mehreren Replicas erstellt
- [ ] Du hast ein Deployment skaliert: `kubectl scale`
- [ ] Du weißt, was Labels und Selectors sind und wozu sie dienen
- [ ] Du hast einen ClusterIP-Service erstellt und verstehst, wozu er dient
- [ ] Du hast einen NodePort-Service erstellt und von außen aufgerufen
- [ ] Du weißt, was ein Ingress Controller ist
- [ ] Du hast einen Ingress erstellt und getestet
- [ ] Du hast ein Rolling Update durchgeführt und einen Rollback gemacht

---

## Phase 3: Config, Secrets & Storage

- [ ] Du hast eine ConfigMap erstellt
- [ ] Du hast eine ConfigMap als Umgebungsvariable in einen Pod eingebunden
- [ ] Du hast eine ConfigMap als Datei in einen Pod gemountet
- [ ] Du hast ein Secret erstellt (Base64-kodiert)
- [ ] Du weißt, warum Base64 kein Schutz ist und was Alternativen sind (Vault, Sealed Secrets)
- [ ] Du hast ein PersistentVolumeClaim erstellt
- [ ] Du hast ein PVC an einen Pod gebunden und Daten persistiert
- [ ] Du hast einen Pod-Neustart überlebt und Daten noch gefunden

---

## Phase 4: RBAC & Troubleshooting

- [ ] Du kannst erklären, was RBAC ist
- [ ] Du hast eine Role mit eingeschränkten Rechten erstellt
- [ ] Du hast ein RoleBinding erstellt
- [ ] Du hast einen ServiceAccount erstellt und an einen Pod gebunden
- [ ] Du weißt, was ein SecurityContext ist
- [ ] Du kannst `kubectl describe pod` lesen und Events verstehen
- [ ] Du kannst einen Pod mit `ImagePullBackOff` diagnostizieren und beheben
- [ ] Du kannst einen Pod mit `CrashLoopBackOff` diagnostizieren
- [ ] Du weißt, was `Pending` bedeutet und wie du die Ursache findest
- [ ] Du hast `kubectl get events --sort-by=.lastTimestamp` benutzt

---

## Phase 5: Helm, Kustomize & Observability

- [ ] Du hast Helm installiert (`helm version`)
- [ ] Du hast einen Chart aus einem Repository installiert
- [ ] Du hast einen Chart mit `helm upgrade` aktualisiert
- [ ] Du hast einen Chart mit `helm rollback` zurückgerollt
- [ ] Du hast ein eigenes Helm Chart erstellt
- [ ] Du weißt, was Kustomize ist und hast einen overlay erstellt
- [ ] Du weißt, was Prometheus ist und was es sammelt
- [ ] Du kannst `kubectl top pods` ausführen (Metrics Server muss laufen)
- [ ] Du hast `kubectl logs` mit `-f` (follow) und `--previous` verwendet

---

## Phase 6: GitOps

- [ ] Du kannst erklären, was GitOps ist
- [ ] Du kennst den Unterschied zwischen Push-based und Pull-based Deployment
- [ ] Du hast Argo CD installiert
- [ ] Du hast eine Argo CD Application erstellt
- [ ] Du hast einen Sync-Zyklus beobachtet
- [ ] Du hast eine Änderung im Git-Repository gemacht und sie automatisch deployt gesehen

---

## Phase 7: Production Readiness

- [ ] Du weißt, was eine Liveness Probe ist und wann sie feuert
- [ ] Du weißt, was eine Readiness Probe ist und wie sie sich von Liveness unterscheidet
- [ ] Du hast Resource Requests und Limits gesetzt
- [ ] Du weißt, was passiert, wenn ein Pod sein Memory Limit überschreitet (OOMKilled)
- [ ] Du hast einen Horizontal Pod Autoscaler konfiguriert
- [ ] Du weißt, was ein PodDisruptionBudget ist
- [ ] Du kannst eine `RollingUpdate`-Strategie konfigurieren (maxSurge, maxUnavailable)

---

## Phase 8: CKA & Real World

- [ ] Du hast Killer.sh oder eine CKA-Übungsumgebung genutzt
- [ ] Du hast eine eigene App vollständig in Kubernetes deployt (von der Containerisierung bis zum Ingress)
- [ ] Du hast einen Cluster mit kubeadm oder k3s aufgesetzt
- [ ] Du weißt, was eine CRD ist
- [ ] Du hast das Operator-Konzept verstanden

---

## Gesamt-Checkpoint

Du bist bereit für einen echten Kubernetes-Job oder die CKA-Prüfung, wenn:

- [ ] Alle Phasen-Checkpoints abgehakt sind
- [ ] Du Troubleshooting-Aufgaben lösen kannst, ohne nachzuschauen
- [ ] Du ein eigenes Helm Chart deployen und Upgrades durchführen kannst
- [ ] Du Argo CD für eine App konfiguriert hast
- [ ] Du die RBAC-Konzepte sicher anwenden kannst
