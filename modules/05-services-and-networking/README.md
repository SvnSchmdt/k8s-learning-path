# Modul 05 – Services & Networking

## Ziel des Moduls

Nach diesem Modul verstehst du das Kubernetes-Netzwerkmodell und kannst Pods über Services erreichbar machen. Du kennst die verschiedenen Service-Typen und weißt, wann du welchen verwendest.

## Warum ist das wichtig?

Pods sind kurzlebig und haben wechselnde IP-Adressen. Services geben dir einen stabilen Endpunkt, über den du Pods erreichst – unabhängig davon, wie viele Replicas laufen oder welche IP sie haben. Ohne Services kein funktionierendes Networking.

## Kernkonzepte

- **Service:** Ein stabiler, virtueller Netzwerkendpunkt, der Traffic zu Pods weiterleitet, die seinen Selector-Labels entsprechen.
- **ClusterIP (Standard):** Nur innerhalb des Clusters erreichbar. Ideal für interne Kommunikation zwischen Services.
- **NodePort:** Öffnet einen Port auf jedem Node des Clusters. Erreichbar von außerhalb über `<NodeIP>:<NodePort>`.
- **LoadBalancer:** Fordert einen externen Load Balancer an (funktioniert in Cloud-Umgebungen). Lokal meist emuliert.
- **Endpoints:** Kubernetes pflegt automatisch eine Endpoints-Liste für jeden Service – die aktuellen Pod-IPs.
- **kube-proxy:** Läuft auf jedem Node und setzt iptables/IPVS-Regeln, die den Service-Traffic zu den richtigen Pods routen.
- **DNS:** Kubernetes hat einen internen DNS-Server (CoreDNS). Jeder Service bekommt einen DNS-Namen: `<service-name>.<namespace>.svc.cluster.local`

## Das Kubernetes Netzwerkmodell

Kubernetes folgt drei Grundregeln:
1. Jeder Pod bekommt eine eigene IP-Adresse
2. Alle Pods können direkt miteinander kommunizieren (ohne NAT)
3. Services vermitteln stabilen Zugang zu Pods

## Praxisaufgabe

### ClusterIP Service erstellen

```yaml
# service-clusterip.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

```bash
kubectl apply -f service-clusterip.yaml
kubectl get service nginx-service
kubectl describe service nginx-service

# Endpoints prüfen (welche Pods werden angesprochen?)
kubectl get endpoints nginx-service
```

### Service intern testen

```bash
# Temporären Pod starten und Service per DNS ansprechen
kubectl run test-pod --image=busybox:1.36 --rm -it --restart=Never -- \
  wget -qO- http://nginx-service.default.svc.cluster.local
```

### NodePort Service

```bash
# NodePort: macht den Service auf jedem Node auf einem bestimmten Port erreichbar
kubectl expose deployment nginx-deployment --type=NodePort --port=80

# Port ermitteln
kubectl get service nginx-deployment
```

## Beispiel-Kommandos

```bash
# Alle Services anzeigen
kubectl get services

# Service-Details anzeigen
kubectl describe service nginx-service

# Service-YAML generieren (ohne zu erstellen)
kubectl expose deployment nginx-deployment --port=80 --dry-run=client -o yaml

# Endpoints anzeigen
kubectl get endpoints
```

## Typische Fehler

- **Selector stimmt nicht:** Service findet keine Pods. `kubectl get endpoints <service-name>` zeigt leere Liste. Labels im Service-Selector und Pods vergleichen.
- **Falscher targetPort:** Der Service leitet auf Port 80, der Container hört aber auf 8080. `targetPort` muss dem Container-Port entsprechen.
- **LoadBalancer bleibt `Pending`:** In kind gibt es keinen echten Load Balancer. Für lokales Testen NodePort oder Port-Forward nutzen.
- **DNS-Auflösung funktioniert nicht:** CoreDNS läuft möglicherweise nicht. `kubectl get pods -n kube-system | grep coredns` prüfen.

## Port-Forward für schnelle Tests

```bash
# Lokalen Port auf Service weiterleiten (kein Service-Typ erforderlich)
kubectl port-forward service/nginx-service 8080:80
# -> http://localhost:8080 erreichbar
```

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Warum brauchen wir Services, wenn Pods doch IP-Adressen haben?
- [ ] Was ist der Unterschied zwischen `port` und `targetPort` in einem Service?
- [ ] Wie heißt der DNS-Name eines Services `my-app` im Namespace `production`?
- [ ] Wann verwendest du ClusterIP, wann NodePort, wann LoadBalancer?
- [ ] Was zeigt dir `kubectl get endpoints`?

## Weiterführende Links

- [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
- [DNS für Services und Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
- [Kubernetes Netzwerkmodell](https://kubernetes.io/docs/concepts/cluster-administration/networking/)
