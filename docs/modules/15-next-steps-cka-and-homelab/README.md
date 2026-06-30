# Modul 15 – Nächste Schritte: CKA & Homelab

## Ziel des Moduls

Nach diesem Modul weißt du, wie du dein Kubernetes-Wissen vertiefst – durch Zertifizierungen, eigene Homelab-Cluster oder echte Projekte. Du kennst die nächsten konkreten Schritte.

## Warum ist das wichtig?

Ein lokaler kind-Cluster ist ein hervorragendes Lernwerkzeug – aber kein Produktionscluster. Der nächste Schritt ist, das Gelernte auf echte oder realistischere Cluster anzuwenden. Die CKA-Zertifizierung bestätigt dein Wissen extern.

## Kernkonzepte

- **CKA (Certified Kubernetes Administrator):** Die wichtigste Kubernetes-Zertifizierung. Praktische, hands-on Prüfung bei der CNCF. Kein Multiple-Choice, sondern echte Cluster-Aufgaben.
- **CKAD (Certified Kubernetes Application Developer):** Fokus auf Anwendungsentwicklung in Kubernetes, weniger Administration.
- **CKS (Certified Kubernetes Security Specialist):** Fortgeschrittene Sicherheitszertifizierung (CKA ist Voraussetzung).
- **kubeadm:** Offizielle Tool zum Aufsetzen von Kubernetes-Clustern auf bestehenden Maschinen.
- **k3s:** Leichtgewichtige Kubernetes-Distribution, ideal für Homelab und Edge-Computing.
- **Operator Pattern:** Kubernetes-Extension-Pattern, das domänenspezifisches Wissen in Custom Controllers kapselt.
- **CRD (Custom Resource Definition):** Ermöglicht, eigene Kubernetes-Ressourcentypen zu definieren.

## CKA-Vorbereitung

### Was wird geprüft?

| Bereich | Gewichtung |
|---------|-----------|
| Storage | 10% |
| Troubleshooting | 30% |
| Workloads & Scheduling | 15% |
| Cluster Architecture, Installation & Configuration | 25% |
| Services & Networking | 20% |

### Empfohlene Lernressourcen

- **Killer.sh:** Zwei Prüfungssimulationen inklusive in der CKA-Prüfung
- **Kodekloud:** CKA-Kurs mit eingebetteten Labs
- **killer.sh Simulator:** Realistischste Übungsumgebung

### Tipps für die Prüfung

```bash
# Alias immer setzen (spart Zeit)
alias k=kubectl
export do="--dry-run=client -o yaml"

# Beispiel: Schnell YAML generieren
kubectl create deployment nginx --image=nginx $do

# Autovervollständigung aktivieren
source <(kubectl completion bash)
```

## Homelab-Setup

### Option 1: k3s (empfohlen für Homelab)

```bash
# Auf einer VM oder einem Raspberry Pi
curl -sfL https://get.k3s.io | sh -

# kubeconfig
cat /etc/rancher/k3s/k3s.yaml

# Node-Status prüfen
kubectl get nodes
```

### Option 2: kubeadm (echtes Kubernetes)

```bash
# Voraussetzungen: 2 VMs (Ubuntu), Container Runtime installiert

# Control Plane initialisieren
kubeadm init --pod-network-cidr=10.244.0.0/16

# kubeconfig kopieren
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# Netzwerk-Plugin installieren (z.B. Flannel)
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

# Worker-Node joinen
kubeadm join <control-plane-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## Echte Projekte als nächster Schritt

Nichts festigt Kubernetes besser als echte Projekte:

1. **Eigene App containerisieren und in Kubernetes deployen**
   - Dockerfile schreiben
   - Image in Registry pushen
   - Deployment, Service, Ingress erstellen
   - Helm Chart erstellen
   - GitOps mit Argo CD

2. **Monitoring-Stack aufsetzen**
   - Prometheus + Grafana mit Helm installieren
   - Eigene Dashboards erstellen
   - Alerting konfigurieren

3. **Multi-Tenant-Cluster**
   - Namespaces für Teams
   - RBAC je Team
   - ResourceQuotas und LimitRanges

4. **Disaster Recovery simulieren**
   - etcd-Backup und Restore
   - Node-Ausfall simulieren
   - PodDisruptionBudgets testen

## Operators & CRDs (Einstieg)

```bash
# CRDs im Cluster anzeigen
kubectl get crds

# Beispiel: Argo CD nutzt CRDs
kubectl get crds | grep argoproj

# Eigene CRD (einfaches Beispiel)
# Nicht in diesem Modul implementiert, aber zu kennen wichtig
```

## Empfohlene Communities & Ressourcen

- [CNCF Community](https://www.cncf.io/community/)
- [Kubernetes Slack](https://slack.k8s.io/)
- [KubeCon Talks (YouTube)](https://www.youtube.com/@cncf)
- [learnk8s.io](https://learnk8s.io/) – hochwertige Artikel
- [iximiuz.com](https://iximiuz.com/en/) – tiefgehende Labs

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen CKA, CKAD und CKS?
- [ ] Warum ist k3s gut für ein Homelab geeignet?
- [ ] Was ist ein Kubernetes Operator, und wozu dient er?
- [ ] Was ist eine CRD?
- [ ] Was sind die nächsten drei konkreten Schritte, die du nach diesem Lernpfad machen willst?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] den Unterschied zwischen CKA, CKAD und CKS erklären kannst
- [ ] einen konkreten nächsten Schritt für dein Lernen festgelegt hast (Homelab, CKA, eigenes Projekt)
- [ ] das Operator-Konzept auf hohem Niveau erklären kannst
- [ ] weißt was eine CRD ist und wie du vorhandene CRDs in deinem Cluster findest
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [CKA Zertifizierung](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)
- [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)
- [k3s](https://k3s.io/)
- [Kubernetes Operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)
- [CRDs](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
