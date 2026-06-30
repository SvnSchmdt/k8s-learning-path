# Kubernetes Glossary

Wichtige Begriffe und Konzepte im Kubernetes-Ökosystem.

---

## A

**API Server (`kube-apiserver`)**  
Die zentrale Komponente des Kubernetes Control Plane. Alle Kommunikation im Cluster läuft über den API Server – kubectl, andere Kubernetes-Komponenten und externe Tools sprechen ausschließlich mit ihm.

**Annotation**  
Key-Value-Metadaten an Kubernetes-Ressourcen. Im Gegensatz zu Labels nicht für Selektion gedacht, sondern für Metainformationen (Autor, Timestamp, Tool-Konfiguration).

---

## C

**ClusterIP**  
Standard Service-Typ. Erstellt eine clusterinterne virtuelle IP, über die der Service erreichbar ist. Von außen nicht direkt erreichbar.

**ConfigMap**  
Kubernetes-Ressource zum Speichern nicht-sensitiver Konfigurationsdaten als Key-Value-Paare oder Dateien.

**Container Runtime**  
Die Software, die Container tatsächlich ausführt. In Kubernetes meist `containerd` oder `CRI-O`. Docker ist seit Kubernetes 1.24 keine unterstützte Runtime mehr.

**Control Plane**  
Die Verwaltungsebene eines Kubernetes-Clusters. Besteht aus API Server, etcd, Scheduler und Controller Manager. Verwaltet den Cluster-Zustand.

**CRD (Custom Resource Definition)**  
Erweiterungsmechanismus von Kubernetes. Ermöglicht es, eigene Ressourcentypen in den Kubernetes-API einzufügen.

---

## D

**DaemonSet**  
Workload-Ressource, die sicherstellt, dass auf jedem (oder bestimmten) Nodes genau ein Pod läuft. Typisch für Monitoring-Agents oder Log-Collectoren.

**Deployment**  
Kubernetes-Ressource für zustandslose Anwendungen. Verwaltet ReplicaSets und ermöglicht Rolling Updates und Rollbacks.

**Desired State**  
Der gewünschte Zustand, der in Kubernetes-Manifesten beschrieben wird. Kubernetes versucht kontinuierlich, den aktuellen Zustand (`current state`) dem gewünschten Zustand anzugleichen.

---

## E

**etcd**  
Verteilter Key-Value-Store, in dem Kubernetes den gesamten Cluster-Zustand persistiert. Ausfallsicher und konsistent. Bei etcd-Verlust verliert man die Cluster-Konfiguration.

**Endpoint**  
Die tatsächliche IP-Adresse und Port eines Pods, auf den ein Service Traffic weiterleitet. Kubernetes pflegt Endpoints automatisch.

---

## H

**Helm**  
Paketmanager für Kubernetes. Charts sind Pakete mit allen benötigten Kubernetes-Ressourcen für eine Anwendung.

**HPA (Horizontal Pod Autoscaler)**  
Kubernetes-Ressource, die automatisch die Anzahl der Pod-Replicas basierend auf Metriken (CPU, Memory, Custom Metrics) skaliert.

---

## I

**Ingress**  
Kubernetes-Ressource, die HTTP(S)-Routing-Regeln für externen Traffic definiert. Benötigt einen Ingress Controller zum Funktionieren.

**Ingress Controller**  
Eine Komponente (oft ein Reverse Proxy wie nginx oder Traefik), die Ingress Resources auswertet und Traffic tatsächlich weiterleitet.

---

## J

**Job**  
Kubernetes-Ressource für einmalige Aufgaben. Ein Job läuft bis zum Abschluss (Exit Code 0) und erstellt einen oder mehrere Pods.

---

## K

**kubectl**  
Kubernetes Command Line Interface. Das primäre Tool zur Interaktion mit einem Kubernetes-Cluster.

**kubeconfig**  
Konfigurationsdatei (Standard: `~/.kube/config`), die Cluster, User und Contexts für kubectl definiert.

**kubelet**  
Agent, der auf jedem Node läuft. Empfängt Anweisungen vom API Server und verwaltet Pods auf dem Node.

**kube-proxy**  
Netzwerk-Proxy auf jedem Node. Implementiert die Kubernetes Service-Abstraktion über iptables oder IPVS.

---

## L

**Label**  
Key-Value-Paar an Kubernetes-Ressourcen. Wird für Selektion und Gruppierung verwendet (z.B. Services finden Pods über Labels).

**LimitRange**  
Namespace-Ressource, die Standard-Limits und Request-Werte für Pods/Container setzt.

**LoadBalancer**  
Service-Typ, der einen externen Load Balancer (in Cloud-Umgebungen) anfordert. Gibt dem Service eine externe IP.

---

## N

**Namespace**  
Logische Trennung von Ressourcen innerhalb eines Clusters. Ideal für Team- oder Umgebungstrennung.

**Node**  
Physische oder virtuelle Maschine, die als Worker im Kubernetes-Cluster fungiert. Führt Pods aus.

**NodePort**  
Service-Typ, der einen Port auf jedem Node öffnet. Erreichbar von außen über `<NodeIP>:<NodePort>`.

---

## O

**Operator**  
Kubernetes-Erweiterungsmuster: ein Controller + CRD, der domänenspezifisches Wissen kapselt. Ermöglicht komplexe zustandsbehaftete Anwendungen (z.B. Datenbanken) automatisiert zu verwalten.

---

## P

**PDB (PodDisruptionBudget)**  
Definiert, wie viele Pods gleichzeitig nicht verfügbar sein dürfen. Schützt vor zu vielen gleichzeitigen Unterbrechungen bei Wartungsarbeiten.

**Pod**  
Die kleinste deploybare Einheit in Kubernetes. Enthält einen oder mehrere Container, die sich Netzwerk und Storage teilen.

**PersistentVolume (PV)**  
Storage-Ressource im Cluster, unabhängig vom Pod-Lebenszyklus.

**PersistentVolumeClaim (PVC)**  
Anforderung für Storage durch eine Anwendung. Wird an ein PV gebunden.

**Probe**  
Health-Check für Container. Liveness Probe (Container lebendig?), Readiness Probe (Container bereit?), Startup Probe (Container gestartet?).

---

## R

**RBAC (Role-Based Access Control)**  
Autorisierungsmechanismus in Kubernetes. Kontrolliert über Roles und RoleBindings, wer was im Cluster machen darf.

**ReplicaSet**  
Stellt sicher, dass eine bestimmte Anzahl identischer Pods läuft. Wird üblicherweise von einem Deployment verwaltet.

**Role**  
RBAC-Ressource, die Berechtigungen für einen Namespace definiert.

**RoleBinding**  
Verknüpft eine Role mit einem Subject (User, Group, ServiceAccount).

---

## S

**Scheduler (`kube-scheduler`)**  
Control-Plane-Komponente, die entscheidet, auf welchem Node ein neuer Pod läuft.

**Secret**  
Kubernetes-Ressource für sensitive Daten. Werte werden Base64-kodiert gespeichert (kein Schutz!).

**Selector**  
Filtermechanismus über Labels. Services und Deployments verwenden Selectors, um zugehörige Pods zu finden.

**ServiceAccount**  
Kubernetes-Identität für Pods. Ermöglicht Pods, auf die Kubernetes-API zuzugreifen.

**StatefulSet**  
Workload für zustandsbehaftete Anwendungen (z.B. Datenbanken). Pods haben stabile Identitäten und geordnete Starts.

**StorageClass**  
Definiert, wie Storage dynamisch bereitgestellt wird. Ermöglicht automatische PV-Erstellung beim PVC-Request.

---

## T

**Taint**  
Eigenschaft eines Nodes, die verhindert, dass Pods ohne passende Tolerations auf diesem Node geplant werden.

**Toleration**  
Pod-Eigenschaft, die dem Pod erlaubt, auf Nodes mit bestimmten Taints geplant zu werden.

---

## V

**Volume**  
Speicher, der in einen Container gemountet wird. Kann temporär (emptyDir) oder persistent (PVC) sein.

---

## W

**Worker Node**  
Node im Cluster, der Workloads (Pods) ausführt. Enthält kubelet, kube-proxy und die Container Runtime.
