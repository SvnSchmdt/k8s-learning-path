# Modul 12 – Troubleshooting

## Ziel des Moduls

Nach diesem Modul kannst du Kubernetes-Probleme systematisch debuggen. Du kennst die häufigsten Fehlerzustände und weißt, wie du von Symptom zur Ursache kommst.

## Warum ist das wichtig?

Dinge gehen kaputt. Das ist normal. Der Unterschied zwischen Anfängern und Profis liegt nicht darin, ob Fehler passieren – sondern wie schnell man sie findet und behebt. Systematisches Troubleshooting ist eine der wichtigsten Kubernetes-Skills.

## Kernkonzepte

- **Pod-Status:** Der Status eines Pods gibt erste Hinweise: `Pending`, `Running`, `Succeeded`, `Failed`, `Unknown`, `CrashLoopBackOff`, `ImagePullBackOff`, `OOMKilled`.
- **Events:** Kubernetes protokolliert alle wichtigen Ereignisse. Events sind oft die schnellste Diagnosemöglichkeit.
- **describe:** `kubectl describe` zeigt alle Details einer Ressource inkl. Events.
- **logs:** Pod-Logs zeigen, was der Container geschrieben hat (stdout/stderr).
- **exec:** Direkter Zugriff auf einen laufenden Container zur Diagnose.

## Troubleshooting-Workflow

```
1. Status prüfen     → kubectl get pods
2. Events lesen      → kubectl describe pod <name>
3. Logs analysieren  → kubectl logs <name>
4. In Pod exec'en    → kubectl exec -it <name> -- sh
5. Networking prüfen → kubectl run test-pod --image=busybox --rm -it -- sh
6. Ressourcen prüfen → kubectl top pods / kubectl describe node
```

## Häufige Fehlerzustände

### ImagePullBackOff / ErrImagePull

```bash
kubectl describe pod <name>
# Events zeigen: Failed to pull image ...
```

**Ursachen:**
- Image-Name falsch geschrieben
- Image-Tag existiert nicht
- Registry nicht erreichbar
- Fehlende Pull-Secrets für private Registry

**Lösung:**
```bash
# Image lokal prüfen
docker pull <image-name>

# ImagePullSecret für private Registry
kubectl create secret docker-registry registry-secret \
  --docker-server=registry.example.com \
  --docker-username=user \
  --docker-password=geheim
```

### CrashLoopBackOff

Pod startet, crasht, wird neu gestartet, crasht wieder...

```bash
# Logs des aktuellen Containers
kubectl logs <pod-name>

# Logs des vorherigen Containers (vor dem letzten Crash)
kubectl logs <pod-name> --previous

# Details zum Absturz
kubectl describe pod <pod-name>
# Suche nach: Exit Code, Last State
```

**Ursachen:**
- Anwendungsfehler (Konfiguration falsch, fehlende Env-Variable)
- Falsche Startkommandos im Container
- Liveness Probe zu aggressiv konfiguriert
- Berechtigungsproblem

### Pending

Pod läuft nicht, bleibt im Status `Pending`.

```bash
kubectl describe pod <name>
# Events: Insufficient cpu / Insufficient memory / No nodes available
```

**Ursachen:**
- Nicht genug Ressourcen (CPU/Memory) auf Nodes verfügbar
- Node Affinity oder Taints verhindern Scheduling
- PVC kann nicht gebunden werden

### OOMKilled (Out of Memory)

```bash
kubectl describe pod <name>
# Last State: Terminated, Reason: OOMKilled
```

**Ursache:** Container hat das Memory-Limit überschritten.  
**Lösung:** Memory-Limit erhöhen oder Memory-Leak in der Anwendung finden.

### Service antwortet nicht

```bash
# 1. Läuft der Pod überhaupt?
kubectl get pods -l app=<label>

# 2. Sind Endpoints korrekt?
kubectl get endpoints <service-name>

# 3. Selector stimmt?
kubectl describe service <name>
kubectl describe pod <pod-name> | grep Labels

# 4. Von innen testen
kubectl run test --image=busybox:1.36 --rm -it --restart=Never -- \
  wget -qO- http://<service-name>
```

## Nützliche Debugging-Kommandos

```bash
# Pod-Details mit Events
kubectl describe pod <name>

# Ressourcenverbrauch
kubectl top pods
kubectl top nodes

# Node-Auslastung
kubectl describe node <node-name>

# Events des gesamten Clusters, nach Zeit sortiert
kubectl get events --sort-by=.lastTimestamp -A

# Temporären Debug-Container starten
kubectl debug -it <pod-name> --image=busybox --target=<container-name>

# Netzwerktest von innerhalb des Clusters
kubectl run nettest --image=busybox:1.36 --rm -it --restart=Never -- \
  nslookup kubernetes.default.svc.cluster.local
```

## Typische Fehler beim Troubleshooting

- **Nur Status schauen, nicht describe:** `kubectl get pods` zeigt nur den Status. `describe` zeigt die Ursache.
- **Falsche Logs:** Bei CrashLoopBackOff `--previous` vergessen. Aktuelle Logs sind leer, weil der Container gerade neu gestartet hat.
- **Namespace vergessen:** Ressource in falschem Namespace gesucht.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Wie findest du heraus, warum ein Pod im Status `CrashLoopBackOff` ist?
- [ ] Was zeigt `kubectl describe pod` was `kubectl get pod` nicht zeigt?
- [ ] Wie testest du, ob ein Service korrekt auf Pods verweist?
- [ ] Was bedeutet `OOMKilled`, und wie behebst du es?
- [ ] Wie zeigst du die Logs des zuletzt abgestürzten Container-Instances an?

## Weiterführende Links

- [Troubleshooting Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/)
- [Troubleshooting Nodes](https://kubernetes.io/docs/tasks/debug/debug-cluster/debug-cluster/)
- [kubectl Troubleshooting](https://kubernetes.io/docs/tasks/debug/)
