# Modul 02 – Kubernetes Fundamentals

## Ziel des Moduls

Nach diesem Modul verstehst du die Kubernetes-Architektur, die wichtigsten Komponenten und grundlegende Konzepte wie Deklarativität und den Control Loop. Du kannst erklären, was Kubernetes tut und warum es so aufgebaut ist.

## Warum ist das wichtig?

Kubernetes wirkt komplex, weil es viele Komponenten hat – aber das Grundprinzip ist elegant einfach: Du beschreibst den gewünschten Zustand, Kubernetes sorgt dafür, dass dieser Zustand erreicht und aufrechterhalten wird. Wer dieses Prinzip versteht, versteht Kubernetes.

## Kernkonzepte

- **Deklarativ vs. Imperativ:** Du sagst Kubernetes "ich will 3 Replicas laufen haben" – nicht "starte jetzt 3 Container". Kubernetes kümmert sich um das Wie.
- **Control Loop / Reconciliation Loop:** Kubernetes vergleicht kontinuierlich den *desired state* (was du willst) mit dem *current state* (was gerade läuft) und bringt beides in Übereinstimmung.
- **API Server:** Zentraler Einstiegspunkt für alle Kubernetes-Kommunikation. Alle Komponenten reden mit dem API Server, nicht direkt miteinander.
- **etcd:** Verteilter Key-Value-Store. Hier speichert Kubernetes den gesamten Cluster-Zustand. Ausfallsicher und konsistent.
- **Scheduler:** Entscheidet, auf welchem Node ein neuer Pod läuft, basierend auf Ressourcen, Affinität und anderen Regeln.
- **Controller Manager:** Führt verschiedene Controller aus, z.B. den Deployment Controller oder ReplicaSet Controller. Sie beobachten den Cluster und reagieren auf Änderungen.
- **kubelet:** Läuft auf jedem Node. Empfängt Anweisungen vom API Server und startet/stoppt Container über die Container Runtime.
- **kube-proxy:** Verwaltet Netzwerkregeln auf jedem Node, damit Pods über Services erreichbar sind.

## Architektur im Überblick

```
┌─────────────────────────────────────────────┐
│              Control Plane                  │
│  ┌──────────┐  ┌──────┐  ┌───────────────┐ │
│  │API Server│  │ etcd │  │  Scheduler    │ │
│  └──────────┘  └──────┘  └───────────────┘ │
│  ┌─────────────────────────────────────────┐│
│  │        Controller Manager               ││
│  └─────────────────────────────────────────┘│
└─────────────────────────────────────────────┘
           │ API calls
┌──────────┴──────────────────────────────────┐
│                Worker Nodes                 │
│  ┌────────┐  ┌────────────┐  ┌───────────┐ │
│  │kubelet │  │ kube-proxy │  │  Runtime  │ │
│  └────────┘  └────────────┘  └───────────┘ │
│  ┌─────────────────────────────────────────┐│
│  │    Pod   Pod   Pod   Pod                ││
│  └─────────────────────────────────────────┘│
└─────────────────────────────────────────────┘
```

## Wichtige Kubernetes-Objekte (Überblick)

| Objekt | Zweck |
|--------|-------|
| **Pod** | Kleinste deploybare Einheit; eine oder mehrere Container |
| **Deployment** | Verwaltet Pods deklarativ mit Rolling Updates |
| **Service** | Stabiler Netzwerkendpunkt für eine Gruppe von Pods |
| **ConfigMap** | Konfigurationsdaten für Pods |
| **Secret** | Sensitive Daten (Passwörter, Tokens) |
| **Namespace** | Logische Trennung von Ressourcen im Cluster |
| **Node** | Physische oder virtuelle Maschine im Cluster |

## Praxisaufgabe

### Cluster-Komponenten erkunden

```bash
# Alle System-Pods anzeigen (hier laufen die K8s-Komponenten)
kubectl get pods -n kube-system

# Nodes anzeigen
kubectl get nodes -o wide

# Cluster-Info
kubectl cluster-info
```

### Ersten Pod manuell starten

```bash
# Imperativ (gut zum Lernen, nicht für Produktion)
kubectl run mein-erster-pod --image=nginx:1.27-alpine

# Pod anzeigen
kubectl get pods
kubectl describe pod mein-erster-pod
kubectl logs mein-erster-pod

# Pod löschen
kubectl delete pod mein-erster-pod
```

## Typische Fehler

- **Imperativ vs. Deklarativ verwechseln:** In Produktion sollte man immer deklarativ mit YAML-Manifesten arbeiten, nicht mit `kubectl run`. `kubectl run` ist ein Lern- und Debugging-Tool.
- **etcd als Datenbank nutzen:** etcd ist für den Kubernetes-Cluster-Zustand, nicht für App-Daten.
- **Alle Ressourcen im default-Namespace:** In realen Clustern nutzt man Namespaces zur Trennung von Teams/Umgebungen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen deklarativem und imperativem Ansatz?
- [ ] Was passiert, wenn du einen Pod löscht, der von einem Deployment verwaltet wird?
- [ ] Welche Komponente entscheidet, auf welchem Node ein Pod gestartet wird?
- [ ] Warum gibt es etcd, und was passiert, wenn es ausfällt?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] die Hauptkomponenten des Control Plane (API Server, etcd, Scheduler, Controller Manager) benennen und ihre Aufgabe erklären kannst
- [ ] den Unterschied zwischen deklarativem und imperativem Ansatz erklären kannst
- [ ] einen Pod manuell gestartet, seine Logs gelesen und in ihn exec'ed hast
- [ ] weißt, was passiert wenn du einen Pod löscht, der von einem Deployment verwaltet wird
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [Kubernetes Architektur](https://kubernetes.io/docs/concepts/architecture/)
- [Kubernetes Komponenten](https://kubernetes.io/docs/concepts/overview/components/)
- [Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/)
