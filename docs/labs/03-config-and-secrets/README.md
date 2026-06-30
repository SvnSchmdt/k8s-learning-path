# Lab 03 – ConfigMaps & Secrets verwenden

## Was du baust

Du bindest externe Konfiguration sicher in einen Pod ein: einmal als Umgebungsvariable (für einzelne Werte) und einmal als gemountetes Volume (für Konfigurationsdateien). Ein Secret wird ebenfalls als Umgebungsvariable bereitgestellt.

**Kubernetes-Objekte:** ConfigMap, Secret (Opaque), Pod

```text
ConfigMap: app-config
    ├── Env-Variable APP_ENV  ──────┐
    ├── Env-Variable LOG_LEVEL ─────┤──► Pod: config-demo
    └── Volume: /etc/config/ ───────┘         │
                                               └── env | grep APP_
Secret: app-secret
    └── Env-Variable API_KEY ──────────────────────► Pod: config-demo
```

## Ziel

Ein Pod läuft mit Konfiguration aus einer ConfigMap (als Env-Variable und als Datei) sowie einem Secret (als Env-Variable). Du hast die Werte im Pod verifiziert.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert

## Schritt-für-Schritt

### Schritt 1: ConfigMap erstellen

```bash
kubectl apply -f manifests/configmap.yaml
kubectl get configmap app-config
```

Erwartete Ausgabe:
```text
NAME         DATA   AGE
app-config   3      5s
```

```bash
kubectl describe configmap app-config
```

Erwartete Ausgabe (Auszug):
```text
Name:         app-config
Namespace:    default
Data
====
APP_ENV:     8 bytes
LOG_LEVEL:   4 bytes
app.properties:
----
server.port=8080
feature.x=true
```

### Schritt 2: Secret erstellen

> [!WARNING]
> Die Datei `manifests/secret.yaml` enthält ausschließlich Dummy-Werte. Committen von echten Secrets in Git-Repositories ist ein kritisches Sicherheitsrisiko.

```bash
kubectl apply -f manifests/secret.yaml
kubectl get secret app-secret
```

Erwartete Ausgabe:
```text
NAME         TYPE     DATA   AGE
app-secret   Opaque   2      5s
```

```bash
# Secret-Wert (Base64-dekodiert) prüfen – nur zur Verifikation
kubectl get secret app-secret -o jsonpath='{.data.api-key}' | base64 -d && echo
```

Erwartete Ausgabe:
```text
mein-api-key-dummy
```

### Schritt 3: Pod starten

```bash
kubectl apply -f manifests/pod-with-config.yaml
kubectl get pod config-demo
```

Erwartete Ausgabe:
```text
NAME          READY   STATUS    RESTARTS   AGE
config-demo   1/1     Running   0          15s
```

### Schritt 4: Env-Variablen im Pod prüfen

```bash
kubectl exec -it config-demo -- env | grep -E "APP_|LOG_|API_"
```

Erwartete Ausgabe:
```text
APP_ENV=produktion
LOG_LEVEL=info
API_KEY=mein-api-key-dummy
```

### Schritt 5: Gemountete Dateien prüfen

```bash
kubectl exec -it config-demo -- ls /etc/config/
```

Erwartete Ausgabe:
```text
app.properties
```

```bash
kubectl exec -it config-demo -- cat /etc/config/app.properties
```

Erwartete Ausgabe:
```text
server.port=8080
feature.x=true
```

### Schritt 6: ConfigMap-Update beobachten

```bash
# ConfigMap ändern
kubectl patch configmap app-config \
  --type merge \
  -p '{"data":{"LOG_LEVEL":"debug"}}'
```

Erwartete Ausgabe:
```text
configmap/app-config patched
```

```bash
# Env-Variable: ändert sich NICHT ohne Pod-Neustart
kubectl exec -it config-demo -- env | grep LOG_LEVEL
```

Erwartete Ausgabe:
```text
LOG_LEVEL=info
```

```bash
# Volume-Mount: wird nach ~60 Sekunden aktualisiert (warte kurz)
sleep 65
kubectl exec -it config-demo -- cat /etc/config/app.properties
```

> [!NOTE]
> Env-Variablen aus ConfigMaps werden nur beim Pod-Start gesetzt. Änderungen an der ConfigMap erfordern einen Pod-Neustart. Volume-Mounts hingegen werden vom kubelet periodisch aktualisiert (Standard: ~1 Minute).

## Validierung

```bash
# Pod läuft
kubectl get pod config-demo

# Alle Env-Variablen anzeigen
kubectl exec -it config-demo -- env | sort

# Gemountete Dateien anzeigen
kubectl exec -it config-demo -- find /etc/config -type f
```

Erwartete Ausgabe:
```text
/etc/config/app.properties
```

## Cleanup

```bash
kubectl delete -f manifests/
```

Erwartete Ausgabe:
```text
configmap "app-config" deleted
secret "app-secret" deleted
pod "config-demo" deleted
```

## Erweiterungsaufgabe

1. Erstelle ein zweites Secret vom Typ `kubernetes.io/tls`. Du kannst ein selbst-signiertes Zertifikat mit `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=test"` erstellen und als Secret speichern.
2. Ändere den Pod so, dass das Secret als Datei unter `/etc/secrets/` eingebunden wird (statt als Env-Variable). Verifiziere den Mount.
3. Was passiert, wenn du einen Key aus der ConfigMap entfernst, der als Env-Variable referenziert wird, und den Pod neu startest?
