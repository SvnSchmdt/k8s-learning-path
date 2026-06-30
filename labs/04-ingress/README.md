# Lab 04 – Ingress lokal testen

## Ziel

HTTP-Traffic wird über einen Ingress Controller zu verschiedenen Services geroutet. Du lernst Host- und Path-basiertes Routing.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] nginx-Deployment und Service aus Lab 01/02 (oder neu erstellen)
- [ ] Nginx Ingress Controller installiert

## Schritt-für-Schritt

### Schritt 1: kind-Cluster mit Port-Mapping erstellen

Für Ingress in kind empfiehlt sich ein Cluster mit explizitem Port-Mapping:

```yaml
# kind-ingress-config.yaml
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
```

```bash
# Bestehenden Cluster löschen und neu erstellen (falls nötig)
kind delete cluster --name k8s-lernpfad
kind create cluster --name k8s-lernpfad --config kind-ingress-config.yaml
```

### Schritt 2: Nginx Ingress Controller installieren

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

# Warten bis Controller bereit ist
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

kubectl get pods -n ingress-nginx
```

### Schritt 3: Test-Deployments und Services erstellen

```bash
kubectl apply -f manifests/
kubectl get deployments
kubectl get services
```

### Schritt 4: Ingress erstellen

```bash
kubectl apply -f manifests/ingress.yaml
kubectl get ingress
kubectl describe ingress lab04-ingress
```

### Schritt 5: /etc/hosts konfigurieren

```bash
# Diese Einträge hinzufügen (nur für lokale Tests!)
echo "127.0.0.1 app-a.local app-b.local" | sudo tee -a /etc/hosts
```

### Schritt 6: Routing testen

```bash
# App A über Host-basiertes Routing
curl http://app-a.local

# App B über Host-basiertes Routing
curl http://app-b.local

# Path-basiertes Routing (falls konfiguriert)
curl http://app-a.local/api
```

## Validierung

```bash
# Ingress anzeigen
kubectl get ingress

# Ingress Controller Logs
kubectl logs -n ingress-nginx \
  deployment/ingress-nginx-controller \
  --tail=20

# Endpoint des Ingress Controllers
kubectl get svc -n ingress-nginx ingress-nginx-controller
```

## Cleanup

```bash
kubectl delete -f manifests/

# /etc/hosts Einträge entfernen (manuell)
sudo sed -i '' '/app-a.local/d' /etc/hosts
sudo sed -i '' '/app-b.local/d' /etc/hosts
```

## Erweiterungsaufgabe

1. Füge TLS-Unterstützung hinzu: Erstelle ein selbst-signiertes Zertifikat, speichere es als Secret und konfiguriere den Ingress für HTTPS.
2. Konfiguriere Rate-Limiting mit Ingress-Annotationen (`nginx.ingress.kubernetes.io/limit-rps`).
3. Erstelle ein drittes Deployment und route `/v2` zu ihm.
