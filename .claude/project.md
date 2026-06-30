# Project Scope – Kubernetes Learning Path

## Projektziel

Ein öffentlich zugänglicher, strukturierter Lernpfad für Kubernetes – von den Grundlagen bis zu produktionsreifen Konzepten. Kein Link-Aggregator, sondern eigener Content mit Modulen, Labs, Übungen und Checkpoints.

## Zielgruppe

**Primär:**
- Einsteiger ohne Kubernetes-Kenntnisse
- Junior Cloud/DevOps/Platform Engineers
- Entwickler, die ihre Container-Workloads in K8s verstehen wollen

**Sekundär:**
- CKA-Prüfungskandidaten als Begleitmaterial
- Teams, die neue Mitglieder einarbeiten wollen

## Content-Stil

- Deutsch als Hauptsprache, technische Fachbegriffe auf Englisch
- Hands-on: jedes Konzept wird sofort angewendet
- Progressive Komplexität: Modul 00 ist einfach, Modul 15 ist anspruchsvoll
- Realistisch: echte Fehlermeldungen, echte Debugging-Szenarien
- Kein Buzzword-Bingo

## Qualitätsregeln

| Regel | Details |
|-------|---------|
| YAML valide | Alle Manifeste müssen syntaktisch korrekt sein |
| kubectl korrekt | Nur dokumentierte Flags und Commands verwenden |
| Keine Secrets | Nur Beispiel-Secrets mit Dummy-Werten und Warnung |
| kind-kompatibel | Grundlagenlabs ohne Cloud-Zugang nutzbar |
| Vollständig | Kein halbfertiges Modul ohne "Work in Progress"-Hinweis |
| Verlinkt | Jedes Modul verlinkt auf offizielle Docs |

## Modul-Template

```markdown
# Modul XX – [Titel]

## Ziel des Moduls
Was du nach diesem Modul verstehen und können wirst.

## Warum ist das wichtig?
Einordnung in den Kubernetes-Kontext, Motivation.

## Kernkonzepte
- **Begriff 1**: Erklärung
- **Begriff 2**: Erklärung

## Praxisaufgabe
Konkrete Aufgabe mit kubectl-Kommandos und YAML.

## Beispiel-Kommandos
```bash
kubectl ...
```

## Typische Fehler
- Fehler 1: Ursache und Lösung
- Fehler 2: Ursache und Lösung

## Checkpoint
Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Frage 1
- [ ] Frage 2

## Weiterführende Links
- [Offizielle Docs](https://kubernetes.io/docs/...)
```

## Lab-Template

```markdown
# Lab XX – [Titel]

## Ziel
Was in diesem Lab erreicht wird.

## Voraussetzungen
- kind-Cluster läuft
- kubectl konfiguriert
- ggf. weitere Tools

## Schritt-für-Schritt

### Schritt 1: ...
### Schritt 2: ...

## Validierung
```bash
kubectl get ...
```

## Cleanup
```bash
kubectl delete ...
```

## Erweiterungsaufgabe
Eine weiterführende Aufgabe für Schnelle.
```

## Definition of Done für neue Inhalte

Ein Modul oder Lab gilt als fertig, wenn:
- [ ] Die Datei existiert und folgt dem Template
- [ ] Alle YAML-Manifeste syntaktisch valide sind
- [ ] Alle kubectl-Kommandos dokumentiert sind
- [ ] Ein Checkpoint/Validierungs-Schritt vorhanden ist
- [ ] Cleanup-Schritte enthalten sind
- [ ] Mindestens ein Link auf offizielle Docs vorhanden ist
- [ ] In README.md und der Roadmap verlinkt
- [ ] Kein Hard-coded Secret oder Token

## Git-Regeln

- Commits auf Englisch
- Feature-Branches für größere Änderungen: `add/module-XX-topic`
- Bugfix-Branches: `fix/description`
- Kein `--force` auf `main`
- Kein Commit von `.DS_Store`, `*.swp`, `node_modules`, etc.

## Public-Repo-Regeln

- Keine personenbezogenen Daten
- Keine internen URLs, IP-Adressen oder Firmennamen
- Keine Credentials, Tokens, Passwörter – auch nicht in Commit-History
- README.md ist der erste Anlaufpunkt für Außenstehende – immer aktuell halten
- LICENSE und CONTRIBUTING.md müssen vorhanden sein
