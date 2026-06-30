# Lab 02 – Service & Networking

## Ziel

Das nginx-Deployment aus Lab 01 wird über einen Service erreichbar gemacht. Du lernst ClusterIP, NodePort und Port-Forward kennen.

## Voraussetzungen

- [ ] Lab 01 abgeschlossen oder nginx-Deployment läuft
- [ ] kubectl konfiguriert

## Schritt-für-Schritt

### Schritt 1: nginx-Deployment sicherstellen

```bash
kubectl get deployment nginx-deployment
# Falls nicht vorhanden:
kubectl apply -f ../01-nginx-deployment/manifests/deployment.yaml
```

### Schritt 2: ClusterIP Service erstellen

```bash
kubectl apply -f manifests/service-clusterip.yaml
kubectl get service nginx-clusterip
```

### Schritt 3: Service intern testen

```bash
# Temporären Pod starten und Service testen
kubectl run curl-test \
  --image=curlimages/curl:8.8.0 \
  --rm -it --restart=Never \
  -- curl -s http://nginx-clusterip.default.svc.cluster.local

# Erwartete Ausgabe: nginx Willkommensseite HTML
```

### Schritt 4: Endpoints prüfen

```bash
# Welche Pods werden angesprochen?
kubectl get endpoints nginx-clusterip
# NAME              ENDPOINTS                                            AGE
# nginx-clusterip   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80          1m

# Stimmt mit Pod-IPs überein?
kubectl get pods -l app=nginx -o wide
```

### Schritt 5: Port-Forward für lokalen Zugriff

```bash
# Lokaler Port 8080 → Service Port 80
kubectl port-forward service/nginx-clusterip 8080:80 &

# Vom lokalen Rechner aus aufrufen
curl http://localhost:8080

# Port-Forward beenden
kill %1
```

### Schritt 6: NodePort Service erstellen

```bash
kubectl apply -f manifests/service-nodeport.yaml
kubectl get service nginx-nodeport

# NodePort-Nummer ermitteln
NODE_PORT=$(kubectl get svc nginx-nodeport -o jsonpath='{.spec.ports[0].nodePort}')
echo "NodePort: $NODE_PORT"
```

### Schritt 7: NodePort mit kind testen

```bash
# In kind muss der NodePort über Port-Forward getestet werden
kubectl port-forward service/nginx-nodeport 9090:80

curl http://localhost:9090
```

## Validierung

```bash
# Alle Services anzeigen
kubectl get services

# Service-Details
kubectl describe service nginx-clusterip
kubectl describe service nginx-nodeport

# DNS-Auflösung testen
kubectl run dns-test \
  --image=busybox:1.36 \
  --rm -it --restart=Never \
  -- nslookup nginx-clusterip.default.svc.cluster.local
```

## Cleanup

```bash
kubectl delete -f manifests/
kubectl delete deployment nginx-deployment
```

## Erweiterungsaufgabe

1. Ändere den Selector des Services so, dass er auf keine Pods mehr passt. Was passiert mit `kubectl get endpoints`?
2. Erstelle ein zweites Deployment mit einem anderen Label und ändere den Service-Selector so, dass er auf das neue Deployment zeigt.
3. Was passiert, wenn du einen Pod löschst, der Teil des Services ist? Lädt der Browser sofort auf den nächsten Pod um?
