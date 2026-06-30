# Lab 03 – ConfigMaps & Secrets verwenden

## Ziel

Eine Anwendung (nginx) liest ihre Konfiguration aus einer ConfigMap und hat Zugang zu einem Secret – beides wird als Umgebungsvariable und als Datei in den Container eingebunden.

## Voraussetzungen

- [ ] kind-Cluster läuft
- [ ] kubectl konfiguriert

## Schritt-für-Schritt

### Schritt 1: ConfigMap erstellen

```bash
kubectl apply -f manifests/configmap.yaml
kubectl get configmap app-config
kubectl describe configmap app-config
```

### Schritt 2: Secret erstellen

```bash
kubectl apply -f manifests/secret.yaml
kubectl get secret app-secret
kubectl describe secret app-secret

# Wert Base64-dekodiert anzeigen (zur Kontrolle, niemals in Logs!)
kubectl get secret app-secret -o jsonpath='{.data.api-key}' | base64 -d && echo
```

### Schritt 3: Pod mit ConfigMap und Secret starten

```bash
kubectl apply -f manifests/pod-with-config.yaml
kubectl get pod config-demo
```

### Schritt 4: Env-Variablen im Pod prüfen

```bash
kubectl exec -it config-demo -- env | grep -E "APP_|LOG_|API_"
# APP_ENV=produktion
# LOG_LEVEL=info
# API_KEY=mein-api-key-dummy
```

### Schritt 5: Gemountete Dateien prüfen

```bash
kubectl exec -it config-demo -- ls /etc/config/
# app.properties

kubectl exec -it config-demo -- cat /etc/config/app.properties
# server.port=8080
# feature.x=true
```

### Schritt 6: ConfigMap aktualisieren (beobachten)

```bash
# ConfigMap ändern
kubectl patch configmap app-config \
  --type merge \
  -p '{"data":{"LOG_LEVEL":"debug"}}'

# Env-Variablen ändern sich NICHT ohne Pod-Neustart
kubectl exec -it config-demo -- env | grep LOG_LEVEL
# LOG_LEVEL=info (alter Wert!)

# Volume-Mounts werden nach ~1 Minute aktualisiert
kubectl exec -it config-demo -- cat /etc/config/app.properties
```

## Validierung

```bash
# Pod läuft
kubectl get pod config-demo

# Alle Env-Variablen
kubectl exec -it config-demo -- env

# Dateien im Config-Volume
kubectl exec -it config-demo -- find /etc/config -type f
```

## Cleanup

```bash
kubectl delete -f manifests/
```

## Erweiterungsaufgabe

1. Erstelle ein Secret vom Typ `kubernetes.io/tls` mit einem selbst-signierten Zertifikat.
2. Ändere den Pod so, dass das Secret als Datei unter `/etc/secret/` eingebunden wird (statt als Env-Variable).
3. Was passiert, wenn du einen Key in der ConfigMap löschst, der als Env-Variable referenziert wird, und den Pod neu startest?
