# Command: review-module

Prüfe ein bestehendes Modul auf Verständlichkeit, technische Korrektheit und Praxisnähe.

## Verwendung

```
/review-module <modulpfad>
```

Beispiel: `/review-module modules/04-pods-and-workloads/README.md`

## Workflow

### 1. Strukturprüfung

Prüfe ob alle Pflichtabschnitte vorhanden sind:
- [ ] Ziel des Moduls
- [ ] Warum ist das wichtig?
- [ ] Kernkonzepte
- [ ] Praxisaufgabe
- [ ] Beispiel-Kommandos
- [ ] Typische Fehler
- [ ] Checkpoint
- [ ] Weiterführende Links

### 2. Fachliche Korrektheit

- Sind alle kubectl-Kommandos real und dokumentiert?
- Sind alle YAML-Felder korrekte Kubernetes-API-Felder?
- Stimmen die API-Versionen? (z.B. `apps/v1` für Deployments, nicht `extensions/v1beta1`)
- Sind veraltete Features markiert?
- Verweisen die Links auf aktuelle offizielle Dokumentation?

Prüfe kritische Kommandos:
```bash
kubectl api-resources | grep <ressource>
```

### 3. Verständlichkeit

Beantworte folgende Fragen:
- Ist die Zielgruppe klar? (Einsteiger bis Junior)
- Sind Fachbegriffe erklärt, bevor sie verwendet werden?
- Ist der rote Faden nachvollziehbar?
- Gibt es unnötigen Jargon?
- Sind die Praxisaufgaben konkret genug?

### 4. Praxisnähe

- Kann ein Einsteiger die Aufgaben mit einem kind-Cluster durchführen?
- Sind alle Voraussetzungen genannt?
- Gibt es Cleanup-Schritte?
- Sind die "Typischen Fehler" realistisch?

### 5. YAML-Validierung (falls vorhanden)

```bash
# Dry-run auf existierendem Cluster
kubectl apply --dry-run=client -f <manifest.yaml>

# Oder mit kubeval (offline)
kubeval <manifest.yaml>
```

### 6. Ausgabe des Reviews

Gib ein strukturiertes Review aus:

```
## Review: modules/XX-name/README.md

### Struktur: ✓/✗
- [x] Alle Abschnitte vorhanden
- [ ] Checkpoint fehlt

### Fachliche Korrektheit: ✓/✗
- Gefundene Probleme: ...

### Verständlichkeit: ✓/✗
- Verbesserungsvorschläge: ...

### Praxisnähe: ✓/✗
- ...

### Empfohlene Änderungen:
1. ...
2. ...
```

### 7. Korrekturen anwenden

Nach dem Review: Korrekturen direkt in die Datei einarbeiten und als Fix-Commit kennzeichnen.
