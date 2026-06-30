# Modul 06 – Ingress & DNS

## Ziel des Moduls

Nach diesem Modul kannst du HTTP/HTTPS-Traffic über einen Ingress Controller ins Cluster routen. Du verstehst, was ein Ingress Controller ist, wie Routing-Regeln funktionieren und wie du lokalen DNS für Tests konfigurierst.

## Warum ist das wichtig?

Mit Services allein kann man Anwendungen erreichbar machen, aber für produktiven HTTP-Betrieb mit mehreren Services, Path-basiertem Routing und TLS brauchst du Ingress. Es ist der Standard-Weg, externe HTTP-Anfragen zu Kubernetes-Services zu routen.

## Kernkonzepte

- **Ingress Resource:** Ein Kubernetes-Objekt, das HTTP(S)-Routing-Regeln definiert. Es legt fest, welche URL zu welchem Service weitergeleitet wird.
- **Ingress Controller:** Eine Komponente, die Ingress Resources liest und Traffic tatsächlich weiterleitet. Kubernetes bringt keinen Ingress Controller mit – du musst einen installieren (z.B. nginx-ingress, Traefik, Contour).
- **Host-basiertes Routing:** Verschiedene Domains (z.B. `app.example.com`, `api.example.com`) leiten zu verschiedenen Services.
- **Path-basiertes Routing:** Verschiedene Pfade (`/app`, `/api`) leiten zu verschiedenen Services.
- **TLS Termination:** HTTPS-Verbindungen werden am Ingress terminiert, intern läuft HTTP.

## Ingress Controller vs. Ingress Resource

```
Extern → [Ingress Controller] → [Ingress Resource] → [Service] → [Pods]
```

Der Ingress Controller ist ein laufender Pod/Deployment. Die Ingress Resource ist nur eine Konfiguration. Ohne Controller macht eine Ingress Resource nichts.

## Praxisaufgabe

### Nginx Ingress Controller in kind installieren

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

# Warten bis der Controller läuft
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

### Ingress Resource erstellen

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: nginx.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
kubectl describe ingress nginx-ingress
```

### Lokales DNS für Tests

Da `nginx.local` nicht existiert, musst du es lokal auflösen:

```bash
# /etc/hosts Eintrag (macOS/Linux)
# Erst die kind-Node-IP ermitteln
kubectl get nodes -o wide
# INTERNAL-IP notieren (z.B. 172.18.0.2)

# Eintrag hinzufügen
echo "127.0.0.1 nginx.local" | sudo tee -a /etc/hosts
```

Bei kind mit Port-Forwarding:

```bash
kubectl port-forward -n ingress-nginx service/ingress-nginx-controller 8080:80
# -> http://nginx.local:8080 (mit /etc/hosts Eintrag für 127.0.0.1)
```

## Beispiel-Kommandos

```bash
# Alle Ingresses anzeigen
kubectl get ingress

# Ingress-Details
kubectl describe ingress nginx-ingress

# Ingress Controller Logs prüfen
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller
```

## Typische Fehler

- **Kein Ingress Controller installiert:** Ingress Resource wird erstellt, aber nichts passiert. Es fehlt der Controller.
- **`ingressClassName` falsch oder fehlend:** Der Ingress wird ignoriert. Die Class muss mit dem installierten Controller übereinstimmen.
- **Host im Browser falsch:** `nginx.local` in /etc/hosts eingetragen, aber der Port stimmt nicht.
- **Backend-Service nicht gefunden:** Ingress kann Service nicht erreichen. Service-Name und Port im Ingress prüfen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen Ingress Resource und Ingress Controller?
- [ ] Wie routest du `/app` zu Service A und `/api` zu Service B?
- [ ] Was ist der Vorteil von Ingress gegenüber NodePort für produktive HTTP-Services?
- [ ] Was ist TLS Termination am Ingress?

## Definition of Done

Du bist mit diesem Modul fertig, wenn du:

- [ ] den Unterschied zwischen Ingress Resource und Ingress Controller erklären kannst
- [ ] einen Ingress Controller installiert hast
- [ ] Host-basiertes Routing für zwei Services konfiguriert und getestet hast
- [ ] erklären kannst was TLS Termination am Ingress bedeutet
- [ ] alle Checkpoint-Fragen beantworten kannst

## Weiterführende Links

- [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)
- [nginx Ingress Controller](https://kubernetes.github.io/ingress-nginx/)
