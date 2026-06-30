# Modul 07 – ConfigMaps, Secrets & Umgebungsvariablen

## Ziel des Moduls

Nach diesem Modul kannst du Konfigurationsdaten und sensitive Informationen sicher in Pods einbinden – als Umgebungsvariablen oder als gemountete Dateien. Du verstehst die Unterschiede zwischen ConfigMaps und Secrets.

## Warum ist das wichtig?

Konfiguration gehört nicht ins Container-Image. Images sollen unveränderlich sein und in verschiedenen Umgebungen (dev, staging, prod) gleich sein – nur die Konfiguration ändert sich. ConfigMaps und Secrets entkoppeln Konfiguration vom Image.

## Kernkonzepte

- **ConfigMap:** Speichert nicht-sensitive Konfigurationsdaten als Key-Value-Paare oder ganze Dateien. Kann in Pods als Env-Variablen oder Dateien eingebunden werden.
- **Secret:** Speichert sensitive Daten (Passwörter, API-Schlüssel, TLS-Zertifikate). Werte werden Base64-kodiert gespeichert. **Base64 ist keine Verschlüsselung** – Secrets sind standardmäßig im etcd unverschlüsselt.
- **Environment Variables:** Einfachste Methode, Werte in Container einzubinden. Gut für einzelne Werte.
- **Volume-Mount:** ConfigMap oder Secret wird als Dateisystem in den Container eingehängt. Gut für Konfigurationsdateien.
- **envFrom:** Alle Keys einer ConfigMap oder eines Secrets als Env-Variablen einbinden.

## Secret-Typen

| Typ | Verwendung |
|-----|-----------|
| `Opaque` | Standard – beliebige Key-Value-Paare |
| `kubernetes.io/tls` | TLS-Zertifikate |
| `kubernetes.io/dockerconfigjson` | Registry-Zugangsdaten |
| `kubernetes.io/service-account-token` | ServiceAccount-Tokens |

## Praxisaufgabe

### ConfigMap erstellen

```bash
# Imperativ aus Literal
kubectl create configmap app-config \
  --from-literal=APP_ENV=produktion \
  --from-literal=LOG_LEVEL=info

# Aus Datei
kubectl create configmap nginx-config --from-file=nginx.conf
```

```yaml
# Deklarativ
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: produktion
  LOG_LEVEL: info
  app.properties: |
    server.port=8080
    feature.x=true
```

### ConfigMap als Env-Variable einbinden

```yaml
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    env:
    - name: APP_ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_ENV
    envFrom:
    - configMapRef:
        name: app-config
```

### ConfigMap als Datei mounten

```yaml
spec:
  volumes:
  - name: config-volume
    configMap:
      name: app-config
  containers:
  - name: app
    image: nginx:1.27-alpine
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
```

### Secret erstellen

```bash
# Imperativ
kubectl create secret generic db-credentials \
  --from-literal=username=dbuser \
  --from-literal=password=sicher123

# Base64-Wert anzeigen (zur Kontrolle)
kubectl get secret db-credentials -o jsonpath='{.data.password}' | base64 -d
```

### Secret als Env-Variable einbinden

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-credentials
      key: password
```

## Typische Fehler

- **Secrets im Git committen:** Niemals echte Secrets ins Repository. Nutze `.gitignore` oder Lösungen wie Sealed Secrets / Vault.
- **Base64 als Sicherheit verstehen:** Base64 schützt nicht. Secrets im etcd sind standardmäßig unverschlüsselt.
- **ConfigMap-Update wird nicht sofort sichtbar:** Bei Volume-Mounts werden Updates verzögert propagiert (~1 Minute). Env-Variablen werden erst beim Pod-Neustart aktualisiert.
- **Key nicht gefunden:** Der angegebene Key existiert nicht in der ConfigMap. `kubectl describe configmap <name>` prüfen.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen ConfigMap und Secret?
- [ ] Wann nimmst du Env-Variablen, wann Volume-Mounts?
- [ ] Warum ist Base64 kein Schutz?
- [ ] Was passiert, wenn du eine ConfigMap aktualisierst, die als Env-Variable eingebunden ist?
- [ ] Welche Alternativen zu nativen K8s-Secrets gibt es für produktive Cluster?

## Weiterführende Links

- [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Umgebungsvariablen in Pods](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets)
