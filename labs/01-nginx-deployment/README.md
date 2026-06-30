# Lab 01 – nginx Deployment ausrollen

## Ziel

Eine nginx-Instanz wird als Kubernetes-Deployment mit 3 Replicas betrieben. Du lernst, wie man ein Deployment erstellt, skaliert und aktualisiert.

## Voraussetzungen

- [ ] kind-Cluster läuft (`kubectl get nodes` zeigt Ready)
- [ ] kubectl konfiguriert

## Schritt-für-Schritt

### Schritt 1: Deployment-Manifest erstellen

```bash
# manifests/deployment.yaml erstellen
kubectl apply -f manifests/deployment.yaml
```

### Schritt 2: Deployment prüfen

```bash
kubectl get deployments
# NAME               READY   UP-TO-DATE   AVAILABLE   AGE
# nginx-deployment   3/3     3            3           30s

kubectl get pods
# NAME                                READY   STATUS    RESTARTS   AGE
# nginx-deployment-xxxx-yyyy          1/1     Running   0          30s
# nginx-deployment-xxxx-zzzz          1/1     Running   0          30s
# nginx-deployment-xxxx-aaaa          1/1     Running   0          30s

kubectl get replicasets
```

### Schritt 3: Deployment skalieren

```bash
# Auf 5 Replicas skalieren
kubectl scale deployment nginx-deployment --replicas=5
kubectl get pods -w  # Watch-Modus: sieh wie neue Pods starten

# Auf 2 Replicas reduzieren
kubectl scale deployment nginx-deployment --replicas=2
```

### Schritt 4: In einen Pod exec'en

```bash
# Pod-Name ermitteln
POD=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')

# In Pod exec'en
kubectl exec -it $POD -- sh

# Im Pod: nginx-Konfiguration anschauen
cat /etc/nginx/nginx.conf
exit
```

### Schritt 5: Rolling Update

```bash
# Image-Version aktualisieren
kubectl set image deployment/nginx-deployment nginx=nginx:1.28-alpine

# Update beobachten
kubectl rollout status deployment/nginx-deployment
# Waiting for deployment "nginx-deployment" rollout to finish: 1 out of 3 new replicas...
# deployment "nginx-deployment" successfully rolled out

# Update-Historie
kubectl rollout history deployment/nginx-deployment
```

### Schritt 6: Rollback

```bash
# Zur vorherigen Version zurück
kubectl rollout undo deployment/nginx-deployment

# Prüfen
kubectl rollout status deployment/nginx-deployment
kubectl describe deployment nginx-deployment | grep Image
```

## Validierung

```bash
# Deployment-Status
kubectl describe deployment nginx-deployment

# Aktuelle Image-Version prüfen
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}'

# Pod-Logs
kubectl logs -l app=nginx --tail=5
```

## Cleanup

```bash
kubectl delete -f manifests/
# oder:
kubectl delete deployment nginx-deployment
```

## Erweiterungsaufgabe

1. Füge dem Deployment eine Liveness-Probe hinzu, die `http://localhost:80/` prüft.
2. Setze Resource-Requests und Limits (`memory: "32Mi"`, `cpu: "50m"`).
3. Versuche, das Deployment auf ein nicht-existierendes Image zu aktualisieren (z.B. `nginx:does-not-exist`) und beobachte, was passiert. Führe dann einen Rollback durch.
