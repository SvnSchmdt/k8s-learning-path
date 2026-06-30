# Command: generate-lab

Erstelle ein neues, praxisnahes Lab für den Kubernetes Learning Path.

## Verwendung

```
/generate-lab <nummer> <titel>
```

Beispiel: `/generate-lab 08 statefulset-database`

## Workflow

### 1. Verzeichnisstruktur anlegen

```bash
mkdir -p labs/<nummer>-<kebab-case>/manifests
```

### 2. README.md nach Template erstellen

```markdown
# Lab <XX> – <Titel>

## Ziel

Was nach diesem Lab funktioniert. Konkret und messbar.

## Voraussetzungen

- [ ] kind-Cluster läuft (`kind create cluster --name k8s-lernpfad`)
- [ ] kubectl konfiguriert (`kubectl cluster-info`)
- [ ] Weitere Tools, falls nötig

## Schritt-für-Schritt

### Schritt 1: [Beschreibung]

```bash
# Was dieser Schritt tut
kubectl apply -f manifests/...
```

### Schritt 2: [Beschreibung]

```yaml
# manifest.yaml
apiVersion: ...
```

...

## Validierung

So überprüfst du, ob alles korrekt läuft:

```bash
# Erwartete Ausgabe:
kubectl get pods
# NAME          READY   STATUS    RESTARTS   AGE
# mein-pod-xxx  1/1     Running   0          30s
```

## Cleanup

```bash
# Alle Ressourcen dieses Labs entfernen
kubectl delete -f manifests/
# oder selektiv:
kubectl delete deployment mein-deployment
kubectl delete service mein-service
```

## Erweiterungsaufgabe

Eine Aufgabe für Fortgeschrittene, die mehr wollen.
```

### 3. YAML-Manifeste erstellen

Für jede Ressource eine eigene Datei:
- `manifests/deployment.yaml`
- `manifests/service.yaml`
- etc.

**Qualitätsregeln für Manifeste:**
- Immer `namespace: default` oder explizit kommentieren
- Labels konsistent setzen: `app: <lab-name>`
- Keine `latest`-Tags für Images in Produktionsbeispielen (OK für Labs)
- Resource Limits zumindest als Kommentar erwähnen
- Keine echten Secrets

### 4. Manifeste validieren

```bash
kubectl apply --dry-run=client -f labs/<nummer>-<name>/manifests/
```

### 5. Integration prüfen

- [ ] In README.md (Hauptverzeichnis) in der Labs-Tabelle eintragen
- [ ] In `roadmap/01-learning-path.md` referenzieren, falls passend
- [ ] Voraussetzungen mit vorherigen Labs abgleichen

### 6. Checkliste vor dem Commit

- [ ] README folgt dem Template vollständig
- [ ] Alle Schritte mit kind lokal getestet (oder als "untested" markiert)
- [ ] Cleanup-Schritte vollständig
- [ ] YAML valide (dry-run)
- [ ] Keine Secrets oder private Daten
- [ ] In Haupt-README verlinkt
