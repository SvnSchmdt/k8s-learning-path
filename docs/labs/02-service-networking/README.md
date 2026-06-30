# Lab 02 – Service & Networking

## Was du baust

Du machst das nginx-Deployment aus Lab 01 über einen Service erreichbar. Ein ClusterIP-Service routet Traffic intern zu den Pods. Ein NodePort-Service öffnet einen Port auf dem Node selbst. Du testest die Erreichbarkeit und verstehst, wie Kubernetes DNS für Services funktioniert.

**Kubernetes-Objekte:** Service (ClusterIP), Service (NodePort), Endpoints

```text
Anfrage intern (curl-test Pod)
      │
      ▼
Service: nginx-clusterip (ClusterIP 10.96.x.x:80)
      │
      ├── Pod nginx (10.244.x.5:80)
      ├── Pod nginx (10.244.x.6:80)
      └── Pod nginx (10.244.x.7:80)

Anfrage von außen (Port-Forward)
      │
localhost:8080 ──► Service: nginx-nodeport ──► Pods
```

## Ziel

Ein ClusterIP-Service leitet Traffic zu den nginx-Pods. Du hast den Service per DNS von innerhalb des Clusters angesprochen und per Port-Forward von außen.

## Voraussetzungen

- [ ] Lab 01 abgeschlossen **oder** nginx-Deployment läuft (`kubectl get deployment nginx-deployment`)
- [ ] kubectl konfiguriert

## Schritt-für-Schritt

### Schritt 1: nginx-Deployment sicherstellen

```bash
kubectl get deployment nginx-deployment
```

Falls nicht vorhanden:
```bash
kubectl apply -f ../01-nginx-deployment/manifests/deployment.yaml
```

### Schritt 2: ClusterIP Service erstellen

```bash
kubectl apply -f manifests/service-clusterip.yaml
kubectl get service nginx-clusterip
```

Erwartete Ausgabe:
```text
NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx-clusterip   ClusterIP   10.96.42.100   <none>        80/TCP    10s
```

### Schritt 3: Endpoints prüfen

```bash
kubectl get endpoints nginx-clusterip
```

Erwartete Ausgabe:
```text
NAME              ENDPOINTS                                   AGE
nginx-clusterip   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80   30s
```

Die IPs entsprechen den Pod-IPs:
```bash
kubectl get pods -l app=nginx -o wide
```

> [!TIP]
> Wenn `ENDPOINTS` leer ist, stimmt der Selector nicht mit den Pod-Labels überein. Prüfe: `kubectl describe service nginx-clusterip` und `kubectl get pods --show-labels`.

### Schritt 4: Service intern per DNS ansprechen

```bash
kubectl run curl-test \
  --image=curlimages/curl:8.8.0 \
  --rm -it --restart=Never \
  -- curl -s http://nginx-clusterip.default.svc.cluster.local
```

Erwartete Ausgabe (Auszug):
```text
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

Der DNS-Name `nginx-clusterip.default.svc.cluster.local` setzt sich zusammen aus:
`<service-name>.<namespace>.svc.cluster.local`

### Schritt 5: Port-Forward für lokalen Zugriff

```bash
# Port-Forward im Hintergrund starten
kubectl port-forward service/nginx-clusterip 8080:80 &

# Von deinem Rechner aus aufrufen
curl http://localhost:8080 | head -5
```

Erwartete Ausgabe (Auszug):
```text
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
```

```bash
# Port-Forward beenden
kill %1
```

### Schritt 6: NodePort Service erstellen

```bash
kubectl apply -f manifests/service-nodeport.yaml
kubectl get service nginx-nodeport
```

Erwartete Ausgabe:
```text
NAME             TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
nginx-nodeport   NodePort   10.96.42.200   <none>        80:30080/TCP   10s
```

```bash
# Port-Forward auf den NodePort-Service
kubectl port-forward service/nginx-nodeport 9090:80
```

In einem anderen Terminal:
```bash
curl http://localhost:9090 | head -3
```

## Validierung

```bash
# Beide Services laufen
kubectl get services
```

Erwartete Ausgabe:
```text
NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes        ClusterIP   10.96.0.1      <none>        443/TCP        10m
nginx-clusterip   ClusterIP   10.96.42.100   <none>        80/TCP         5m
nginx-nodeport    NodePort    10.96.42.200   <none>        80:30080/TCP   3m
```

```bash
# DNS-Auflösung testen
kubectl run dns-test \
  --image=busybox:1.36 \
  --rm -it --restart=Never \
  -- nslookup nginx-clusterip.default.svc.cluster.local
```

Erwartete Ausgabe:
```text
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-clusterip.default.svc.cluster.local
Address 1: 10.96.42.100 nginx-clusterip.default.svc.cluster.local
```

## Cleanup

```bash
kubectl delete -f manifests/
kubectl delete deployment nginx-deployment --ignore-not-found
```

## Erweiterungsaufgabe

1. Ändere den Selector in `service-clusterip.yaml` auf ein nicht-existierendes Label (`app: gibts-nicht`). Was zeigt `kubectl get endpoints nginx-clusterip`?
2. Erstelle ein zweites Deployment mit Label `app: nginx-v2` und dirigiere den Service auf die neue Version – ohne Ausfallzeit.
3. Öffne einen Port-Forward und lösche dann einen der nginx-Pods. Funktioniert `curl localhost:8080` noch? Warum?
