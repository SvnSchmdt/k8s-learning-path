# Detaillierter Learning Path

## Phase 0: Voraussetzungen & Setup

**Lernziel:** Dein Rechner ist startklar. Du hast alle Tools installiert und einen funktionierenden lokalen Kubernetes-Cluster.

**Themen:**
- Linux/macOS Kommandozeilen-Grundlagen
- Docker oder eine Container-Runtime verstehen
- kind installieren und konfigurieren
- kubectl installieren und verstehen
- YAML-Syntax kennen

**Praktische Aufgaben:**
- [ ] kind installieren
- [ ] Ersten Cluster erstellen: `kind create cluster`
- [ ] `kubectl cluster-info` ausführen und Output verstehen
- [ ] `kubectl get nodes` ausführen

**Checkpoint:** Du siehst `STATUS: Ready` bei deinem Node.

**Module:** [00 – Voraussetzungen](../modules/00-prerequisites/README.md)  
**Lab:** [00 – Lokaler Cluster mit kind](../labs/00-local-cluster-kind/README.md)

---

## Phase 1: Container & Kubernetes Basics

**Lernziel:** Du verstehst, was Container sind, warum Kubernetes existiert und wie die Architektur aufgebaut ist.

**Themen:**
- Was ist ein Container? Was ist ein Image?
- Container-Lebenszyklus
- Was ist Kubernetes und welche Probleme löst es?
- Kubernetes-Architektur: Control Plane vs. Data Plane
- Kernkomponenten: API Server, etcd, Scheduler, Controller Manager, kubelet, kube-proxy
- Grundlegende Kubernetes-Objekte: Pod, Namespace

**Praktische Aufgaben:**
- [ ] Container-Image lokal bauen und starten (Docker)
- [ ] Ersten Pod in Kubernetes erstellen
- [ ] Pod-Logs ansehen
- [ ] In einen laufenden Pod "exec" ausführen

**Checkpoint:** Du kannst erklären, was ein Pod ist und was er von einem Container unterscheidet.

**Module:**
- [01 – Container Basics](../modules/01-container-basics/README.md)
- [02 – Kubernetes Fundamentals](../modules/02-kubernetes-fundamentals/README.md)
- [03 – kubectl & Cluster-Zugriff](../modules/03-kubectl-and-cluster-access/README.md)

---

## Phase 2: Workloads & Networking

**Lernziel:** Du kannst Anwendungen als Deployment deployen und über Services erreichbar machen.

**Themen:**
- Pod vs. Deployment vs. ReplicaSet
- DaemonSet und StatefulSet (Überblick)
- Labels und Selectors
- Services: ClusterIP, NodePort, LoadBalancer
- kube-proxy und das Kubernetes Netzwerkmodell
- Ingress und Ingress Controller

**Praktische Aufgaben:**
- [ ] nginx Deployment mit 3 Replicas erstellen
- [ ] Deployment skalieren (auf 5, zurück auf 1)
- [ ] Rolling Update durchführen
- [ ] Service (ClusterIP) erstellen und testen
- [ ] Service (NodePort) erstellen und lokal aufrufen
- [ ] Ingress Controller installieren und Route konfigurieren

**Checkpoint:** Eine nginx-App ist via Service erreichbar, Deployments laufen mit mehreren Replicas.

**Module:**
- [04 – Pods & Workloads](../modules/04-pods-and-workloads/README.md)
- [05 – Services & Networking](../modules/05-services-and-networking/README.md)
- [06 – Ingress & DNS](../modules/06-ingress-and-dns/README.md)

**Labs:**
- [01 – nginx Deployment](../labs/01-nginx-deployment/README.md)
- [02 – Service & Networking](../labs/02-service-networking/README.md)
- [04 – Ingress](../labs/04-ingress/README.md)

---

## Phase 3: Config, Secrets, Storage

**Lernziel:** Du kannst Konfigurationen und Secrets sicher in Pods einbinden und persistente Daten verwalten.

**Themen:**
- ConfigMaps: erstellen, mounten als Datei und Env-Variable
- Secrets: Base64, Types, sichere Handhabung
- Environment Variables in Pods
- PersistentVolumes (PV) und PersistentVolumeClaims (PVC)
- StorageClasses
- Volumes: emptyDir, hostPath, configMap

**Praktische Aufgaben:**
- [ ] ConfigMap erstellen und als Umgebungsvariable einbinden
- [ ] ConfigMap als Datei in Pod mounten
- [ ] Secret erstellen (Opaque Type)
- [ ] Secret als Env-Variable nutzen
- [ ] PVC erstellen und an Pod binden
- [ ] Daten in persistentem Volume speichern und Pod-Neustart überleben lassen

**Checkpoint:** Eine App liest Konfiguration aus einer ConfigMap und speichert Daten in einem PVC.

**Module:**
- [07 – ConfigMaps, Secrets & Env](../modules/07-configmaps-secrets-and-env/README.md)
- [08 – Storage](../modules/08-storage/README.md)

**Lab:** [03 – Config & Secrets](../labs/03-config-and-secrets/README.md)

---

## Phase 4: Security, RBAC & Troubleshooting

**Lernziel:** Du verstehst Kubernetes-Sicherheitskonzepte, kannst RBAC konfigurieren und Probleme systematisch debuggen.

**Themen:**
- RBAC: Roles, ClusterRoles, RoleBindings
- ServiceAccounts
- SecurityContext: runAsUser, readOnlyRootFilesystem, privileged
- Pod Security Standards (PSS)
- Netzwerkrichtlinien (Überblick)
- Troubleshooting-Workflow: describe, logs, events
- Häufige Fehlerzustände: CrashLoopBackOff, ImagePullBackOff, Pending, OOMKilled

**Praktische Aufgaben:**
- [ ] ServiceAccount erstellen und an Pod binden
- [ ] Role mit eingeschränkten Rechten erstellen
- [ ] RoleBinding erstellen
- [ ] Pod mit eingeschränktem SecurityContext starten
- [ ] Absichtlich kaputten Pod debuggen

**Checkpoint:** Du kannst einen CrashLoopBackOff-Pod ohne Anleitung diagnostizieren und beheben.

**Module:**
- [09 – RBAC & Security Basics](../modules/09-rbac-and-security-basics/README.md)
- [12 – Troubleshooting](../modules/12-troubleshooting/README.md)

---

## Phase 5: Helm, Kustomize & Observability

**Lernziel:** Du nutzt Helm und Kustomize für strukturierte Deployments und hast ein Grundverständnis von Observability.

**Themen:**
- Helm: Charts, Values, Releases, Repositories
- Helm install, upgrade, rollback
- Eigenes Helm Chart erstellen
- Kustomize: overlays, bases, patches
- Metrics: Prometheus & Grafana (Grundlagen)
- Logs: kubectl logs, stern
- Events und Monitoring

**Praktische Aufgaben:**
- [ ] Helm installieren und ersten Chart deployen
- [ ] Eigenes Helm Chart für eine Beispiel-App erstellen
- [ ] Kustomize overlay für dev/prod erstellen
- [ ] Prometheus mit Helm installieren
- [ ] Grundlegende Metriken in Grafana anzeigen

**Checkpoint:** Eine App wird via Helm Chart deployt, Metriken sind in Prometheus sichtbar.

**Module:**
- [10 – Helm & Kustomize](../modules/10-helm-and-kustomize/README.md)
- [11 – Observability](../modules/11-observability/README.md)

**Labs:**
- [05 – Helm Chart](../labs/05-helm-chart/README.md)
- [06 – Monitoring](../labs/06-monitoring/README.md)

---

## Phase 6: GitOps mit Argo CD

**Lernziel:** Du verstehst das GitOps-Prinzip und kannst Argo CD für automatisierte Deployments nutzen.

**Themen:**
- GitOps-Prinzipien (deklarativ, versioniert, automatisiert)
- Argo CD: Architektur, Application, AppProject
- App-of-Apps Pattern
- Sync-Strategien und Health-Checks
- Unterschied Push-based vs. Pull-based Deployment

**Praktische Aufgaben:**
- [ ] Argo CD in kind-Cluster installieren
- [ ] Erste Argo CD Application erstellen
- [ ] Deployment durch Git-Push triggern
- [ ] Sync-Status und Health in der UI verstehen

**Checkpoint:** Eine Änderung im Git-Repository wird automatisch in den Cluster deployed.

**Module:** [13 – GitOps mit Argo CD](../modules/13-gitops-with-argocd/README.md)  
**Lab:** [07 – GitOps mit Argo CD](../labs/07-gitops-argocd/README.md)

---

## Phase 7: Production Readiness

**Lernziel:** Du kennst die wichtigsten Konzepte für produktionsreife Kubernetes-Deployments.

**Themen:**
- Liveness und Readiness Probes
- Resource Requests und Limits
- Horizontal Pod Autoscaler (HPA)
- PodDisruptionBudgets (PDB)
- Pod Anti-Affinity
- Taints und Tolerations
- Node Affinity
- Kubernetes-Update-Strategie (RollingUpdate, Recreate)

**Praktische Aufgaben:**
- [ ] Liveness und Readiness Probes konfigurieren
- [ ] Resource Requests und Limits setzen
- [ ] HPA erstellen und testen
- [ ] PodDisruptionBudget definieren

**Checkpoint:** Ein Deployment hat Health Checks, Limits und ist gegen ungewollte Unterbrechungen geschützt.

**Modul:** [14 – Production Readiness](../modules/14-production-readiness/README.md)

---

## Phase 8: CKA, Homelab & Real Projects

**Lernziel:** Du wendest alles auf echten oder komplexeren Clustern an und bereitest dich ggf. auf die CKA-Prüfung vor.

**Themen:**
- CKA Prüfungsinhalte und Vorbereitung
- Bare-Metal-Cluster mit kubeadm
- k3s für Homelab
- Echte Projekte: eigene App von 0 auf Kubernetes
- Operator-Konzept (Überblick)
- Custom Resource Definitions (CRD)

**Praktische Aufgaben:**
- [ ] kubeadm-Cluster aufsetzen (oder k3s)
- [ ] Eigene containerisierte App komplett deployen
- [ ] CKA Killer.sh simulieren

**Checkpoint:** Du bist bereit für die CKA-Prüfung oder verwaltest einen echten Cluster.

**Modul:** [15 – Nächste Schritte: CKA & Homelab](../modules/15-next-steps-cka-and-homelab/README.md)
