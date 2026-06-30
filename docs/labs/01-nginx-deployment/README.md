# Lab 01 – nginx Deployment ausrollen

## Was du baust

Du rollst eine nginx-Webanwendung als Kubernetes-Deployment mit 3 Replicas aus. Du lernst, wie Deployments erstellt, skaliert, aktualisiert und zurückgerollt werden.

**Kubernetes-Objekte:** Deployment, ReplicaSet, Pods

```text
Deployment (nginx-deployment)
    │
    └── ReplicaSet
          ├── Pod (nginx:1.27-alpine)
          ├── Pod (nginx:1.27-alpine)
          └── Pod (nginx:1.27-alpine)
```

## Ziel

Ein nginx-Deployment mit 3 Replicas läuft. Du hast ein Rolling Update und einen Rollback erfolgreich durchgeführt.

## Voraussetzungen

- [ ] kind-Cluster läuft (`kubectl get nodes` zeigt Ready)
- [ ] kubectl konfiguriert (`kubectl config current-context` zeigt `kind-k8s-lernpfad`)

## Schritt-für-Schritt

### Schritt 1: Deployment anlegen

```bash
kubectl apply -f manifests/deployment.yaml
```

Erwartete Ausgabe:
```text
deployment.apps/nginx-deployment created
```

### Schritt 2: Deployment-Status prüfen

```bash
kubectl get deployments
```

Erwartete Ausgabe:
```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           30s
```

```bash
kubectl get pods
```

Erwartete Ausgabe:
```text
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6d4cf56db6-abcde   1/1     Running   0          30s
nginx-deployment-6d4cf56db6-fghij   1/1     Running   0          30s
nginx-deployment-6d4cf56db6-klmno   1/1     Running   0          30s
```

```bash
# Zugehöriges ReplicaSet anzeigen
kubectl get replicasets
```

### Schritt 3: Deployment skalieren

```bash
# Auf 5 Replicas skalieren
kubectl scale deployment nginx-deployment --replicas=5
kubectl get pods -w
```

Im Watch-Modus siehst du wie neue Pods von `Pending` → `ContainerCreating` → `Running` wechseln. Beende mit `Ctrl+C`.

```bash
# Wieder reduzieren
kubectl scale deployment nginx-deployment --replicas=3
kubectl get pods
```

Erwartete Ausgabe (nach Skalierung auf 3):
```text
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6d4cf56db6-abcde   1/1     Running   0          2m
nginx-deployment-6d4cf56db6-fghij   1/1     Running   0          2m
nginx-deployment-6d4cf56db6-klmno   1/1     Running   0          2m
```

### Schritt 4: In einen Pod exec'en

```bash
# Pod-Name automatisch ermitteln
POD=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')
echo "Pod: $POD"

# Shell im Pod öffnen
kubectl exec -it $POD -- sh
```

Im Pod:
```sh
# nginx-Version prüfen
nginx -v
# nginx version: nginx/1.27.x

# nginx-Konfiguration ansehen
cat /etc/nginx/nginx.conf

exit
```

### Schritt 5: Rolling Update durchführen

```bash
# Image-Version aktualisieren
kubectl set image deployment/nginx-deployment nginx=nginx:1.28-alpine

# Update-Fortschritt beobachten
kubectl rollout status deployment/nginx-deployment
```

Erwartete Ausgabe:
```text
Waiting for deployment "nginx-deployment" rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
deployment "nginx-deployment" successfully rolled out
```

```bash
# Update-Historie
kubectl rollout history deployment/nginx-deployment
```

Erwartete Ausgabe:
```text
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

### Schritt 6: Rollback durchführen

```bash
# Zur vorherigen Version zurück
kubectl rollout undo deployment/nginx-deployment
kubectl rollout status deployment/nginx-deployment
```

Erwartete Ausgabe:
```text
deployment "nginx-deployment" successfully rolled out
```

```bash
# Aktuelle Image-Version prüfen
kubectl get deployment nginx-deployment \
  -o jsonpath='{.spec.template.spec.containers[0].image}'
```

Erwartete Ausgabe:
```text
nginx:1.27-alpine
```

## Validierung

```bash
# Alle 3 Pods laufen
kubectl get pods -l app=nginx

# Deployment ist vollständig deployed
kubectl get deployment nginx-deployment

# Details des Deployments
kubectl describe deployment nginx-deployment | grep -E "Image:|Replicas:|RollingUpdate"
```

Erwartete Ausgabe (Auszug):
```text
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Image:      nginx:1.27-alpine
```

```bash
# Logs aller nginx-Pods (letzte 5 Zeilen)
kubectl logs -l app=nginx --tail=5
```

## Cleanup

```bash
kubectl delete -f manifests/
```

Erwartete Ausgabe:
```text
deployment.apps "nginx-deployment" deleted
```

> [!NOTE]
> Das Deployment wird in Lab 02 (Service & Networking) wiederverwendet. Falls du direkt weitermachst, kannst du den Cleanup überspringen und das Deployment laufen lassen.

## Erweiterungsaufgabe

1. Füge dem Deployment eine Liveness-Probe hinzu, die `GET /` auf Port 80 prüft.
2. Setze `resources.requests` und `resources.limits` (Richtwerte: `memory: "32Mi"`, `cpu: "50m"`).
3. Aktualisiere auf ein nicht-existierendes Image (`nginx:gibts-nicht`) und beobachte was passiert. Führe dann einen Rollback durch.
