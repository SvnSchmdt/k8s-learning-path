# Lab 04 – Ingress lokal testen

## Was du baust

Du installierst einen nginx Ingress Controller und routest HTTP-Traffic zu zwei verschiedenen Services über Host-basiertes Routing. Beide Apps sind über separate Domains (`app-a.local`, `app-b.local`) erreichbar.

**Kubernetes-Objekte:** Deployment (×2), Service (×2), Ingress, Ingress Controller

```text
Browser / curl
      │
      ▼
localhost:80 (Port-Mapping in kind)
      │
      ▼
Ingress Controller (nginx)
      │
      ├── app-a.local ──► Service: service-a ──► Pod app-a (nginx)
      │
      └── app-b.local ──► Service: service-b ──► Pod app-b (nginx)
```

## Ziel

Zwei verschiedene nginx-Apps sind über unterschiedliche Hostnames via Ingress erreichbar. Du hast Host-basiertes Routing erfolgreich konfiguriert.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert
- [ ] Für Port 80 Zugang: kind-Cluster **muss** mit Port-Mapping erstellt werden (siehe Schritt 1)

> [!IMPORTANT]
> Dieses Lab erfordert einen kind-Cluster mit explizitem Port-Mapping auf Port 80. Falls dein Cluster ohne Port-Mapping läuft, musst du ihn neu erstellen.

## Schritt-für-Schritt

### Schritt 1: kind-Cluster mit Port-Mapping erstellen

```bash
# Bestehenden Cluster löschen (falls ohne Port-Mapping erstellt)
kind delete cluster --name k8s-lernpfad

# kind-ingress-config.yaml erstellen
cat > /tmp/kind-ingress-config.yaml <<'EOF'
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF

kind create cluster --name k8s-lernpfad --config /tmp/kind-ingress-config.yaml
```

Erwartete Ausgabe:
```text
Creating cluster "k8s-lernpfad" ...
 ✓ Ensuring node image ...
 ✓ Preparing nodes 📦
Set kubectl context to "kind-k8s-lernpfad"
```

### Schritt 2: Nginx Ingress Controller installieren

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

Auf den Controller warten:
```bash
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

Erwartete Ausgabe:
```text
pod/ingress-nginx-controller-xxxxxxxxx-xxxxx condition met
```

```bash
kubectl get pods -n ingress-nginx
```

Erwartete Ausgabe:
```text
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-xxxxx        0/1     Completed   0          2m
ingress-nginx-controller-xxxxxxxxx-xxxxx    1/1     Running     0          2m
```

### Schritt 3: Test-Deployments und Services erstellen

```bash
kubectl apply -f manifests/deployments.yaml
```

Erwartete Ausgabe:
```text
deployment.apps/app-a created
deployment.apps/app-b created
service/service-a created
service/service-b created
```

```bash
kubectl get deployments,services
```

Erwartete Ausgabe:
```text
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/app-a   2/2     2            2           30s
deployment.apps/app-b   2/2     2            2           30s

NAME                TYPE        CLUSTER-IP     PORT(S)   AGE
service/kubernetes  ClusterIP   10.96.0.1      443/TCP   5m
service/service-a   ClusterIP   10.96.x.y      80/TCP    30s
service/service-b   ClusterIP   10.96.x.z      80/TCP    30s
```

### Schritt 4: Ingress erstellen

```bash
kubectl apply -f manifests/ingress.yaml
kubectl get ingress
```

Erwartete Ausgabe:
```text
NAME            CLASS   HOSTS                     ADDRESS     PORTS   AGE
lab04-ingress   nginx   app-a.local,app-b.local   localhost   80      15s
```

```bash
kubectl describe ingress lab04-ingress
```

### Schritt 5: /etc/hosts konfigurieren

```bash
# Einträge hinzufügen (nur für lokale Tests!)
echo "127.0.0.1 app-a.local app-b.local" | sudo tee -a /etc/hosts
```

> [!NOTE]
> Diese Einträge sind nur für lokales Testen gedacht. Entferne sie nach dem Lab (Cleanup-Abschnitt).

### Schritt 6: Routing testen

```bash
# App A über Host-basiertes Routing
curl -s http://app-a.local | grep -o "<title>.*</title>"
```

Erwartete Ausgabe:
```text
<title>Welcome to nginx!</title>
```

```bash
# App B über Host-basiertes Routing
curl -s http://app-b.local | grep -o "<title>.*</title>"
```

Erwartete Ausgabe:
```text
<title>Welcome to nginx!</title>
```

## Validierung

```bash
# Ingress Controller läuft
kubectl get pods -n ingress-nginx

# Ingress-Objekt ist konfiguriert und hat ADDRESS
kubectl get ingress lab04-ingress

# Ingress Controller Logs (letzte Requests)
kubectl logs -n ingress-nginx \
  deployment/ingress-nginx-controller \
  --tail=10
```

Erwartete Log-Einträge nach dem curl-Test (Auszug):
```text
... 127.0.0.1 - - "GET / HTTP/1.1" 200 615 ...
```

```bash
# Services erreichbar
kubectl get endpoints service-a service-b
```

## Cleanup

```bash
# Kubernetes-Ressourcen löschen
kubectl delete -f manifests/

# /etc/hosts Einträge entfernen
# macOS/Linux:
sudo sed -i '' '/app-a.local/d' /etc/hosts 2>/dev/null || \
  sudo sed -i '/app-a.local/d' /etc/hosts

# Ingress Controller entfernen (optional)
kubectl delete namespace ingress-nginx
```

> [!CAUTION]
> Das Löschen des Namespaces `ingress-nginx` entfernt den Ingress Controller vollständig. Andere Labs brauchen keinen Ingress Controller, daher ist das hier in Ordnung.

## Erweiterungsaufgabe

1. Füge TLS hinzu: Erstelle ein selbst-signiertes Zertifikat mit `openssl`, speichere es als `kubernetes.io/tls` Secret und füge `spec.tls` zum Ingress hinzu.
2. Konfiguriere Rate-Limiting: Füge die Annotation `nginx.ingress.kubernetes.io/limit-rps: "5"` hinzu und teste mit einer Schleife (`for i in {1..20}; do curl -s -o /dev/null -w "%{http_code}\n" http://app-a.local; done`).
3. Füge ein drittes Deployment hinzu und route `app-a.local/v2` zu ihm (Path-basiertes Routing).
