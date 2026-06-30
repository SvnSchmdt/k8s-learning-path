# Modul 14 – Production Readiness

## Ziel des Moduls

Nach diesem Modul kennst du die wichtigsten Konzepte, die ein Kubernetes-Deployment produktionsreif machen: Health Checks, Resource Management, Autoscaling und Disruption Budgets.

## Warum ist das wichtig?

Ein funktionierendes Deployment und ein produktionsreifes Deployment sind zwei verschiedene Dinge. Im Produktionsbetrieb muss deine App mit Node-Ausfällen, plötzlichem Traffic-Anstieg, Memory-Leaks und Rolling Updates umgehen können – ohne Downtime.

## Kernkonzepte

- **Liveness Probe:** Prüft, ob ein Container lebt. Schlägt fehl → Container-Neustart.
- **Readiness Probe:** Prüft, ob ein Container bereit für Traffic ist. Schlägt fehl → Pod aus Service-Endpoints entfernt.
- **Startup Probe:** Gibt langsam startenden Containern Zeit, bevor Liveness eingreift.
- **Resource Requests:** Minimale Ressourcen, die Kubernetes für den Pod reserviert. Beeinflusst Scheduling.
- **Resource Limits:** Maximale Ressourcen, die ein Container nutzen darf. Bei Überschreitung: CPU gedrosselt, Memory → OOMKilled.
- **Horizontal Pod Autoscaler (HPA):** Skaliert die Anzahl der Pods basierend auf Metriken (CPU, Memory, Custom Metrics).
- **PodDisruptionBudget (PDB):** Definiert, wie viele Pods eines Deployments gleichzeitig nicht verfügbar sein dürfen.
- **Pod Anti-Affinity:** Verhindert, dass alle Pods desselben Deployments auf demselben Node laufen.

## Praxisaufgabe

### Health Probes

```yaml
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    startupProbe:
      httpGet:
        path: /healthz
        port: 80
      failureThreshold: 30
      periodSeconds: 10
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
      initialDelaySeconds: 0
      periodSeconds: 10
      failureThreshold: 3
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
      failureThreshold: 3
```

### Resource Requests & Limits

```yaml
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"    # 100 Millicores = 0.1 CPU
      limits:
        memory: "128Mi"
        cpu: "500m"
```

**Faustregel:** Limits sollten 2–4x höher sein als Requests. Ohne Requests kein garantierter Scheduling-Platz. Ohne Limits können einzelne Pods den ganzen Node belasten.

### Horizontal Pod Autoscaler

```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: meine-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

```bash
kubectl apply -f hpa.yaml
kubectl get hpa
kubectl describe hpa app-hpa
```

### PodDisruptionBudget

```yaml
# pdb.yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: app-pdb
spec:
  minAvailable: 2    # mindestens 2 Pods müssen immer laufen
  selector:
    matchLabels:
      app: meine-app
```

### Rolling Update Strategie

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # max. 1 Pod zusätzlich während Update
      maxUnavailable: 0  # kein Pod darf während Update fehlen
```

### Pod Anti-Affinity (Pods auf verschiedene Nodes verteilen)

```yaml
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: app
              operator: In
              values:
              - meine-app
          topologyKey: kubernetes.io/hostname
```

## Typische Fehler

- **Keine Resource Limits:** Ein Pod kann den ganzen Node-Memory belegen und andere Pods verdrängen.
- **Requests = Limits:** Das ist restriktiv. CPU-Throttling tritt auf, wenn der Pod kurz mehr CPU braucht.
- **Liveness schlägt während Startup fehl:** `startupProbe` verwenden, damit der Container Zeit zum Starten hat.
- **PDB blockiert Node-Drain:** Wenn PDB `minAvailable` nicht erfüllt werden kann, blockiert `kubectl drain`. Das ist Absicht – schützt vor Downtime.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Liveness und Readiness Probe?
- [ ] Was passiert, wenn ein Container sein Memory-Limit überschreitet?
- [ ] Was macht ein HPA, wenn die CPU-Auslastung sinkt?
- [ ] Wozu dient ein PodDisruptionBudget?
- [ ] Was bewirkt `maxUnavailable: 0` in einer Rolling-Update-Strategie?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] Liveness- und Readiness-Probes in einem Deployment konfiguriert hast
- [ ] Resource Requests und Limits gesetzt hast und den Unterschied erklären kannst
- [ ] einen HPA erstellt hast und verstehst wie er skaliert
- [ ] erklären kannst was OOMKilled bedeutet und wie du es vermeidest
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [Liveness & Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Resource Management](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [PodDisruptionBudget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
- [Production Best Practices (Learnk8s)](https://learnk8s.io/production-best-practices)
