# CLAUDE.md – Projekt-Instructions für Claude Code

## Projektkontext

Dieses Repository ist ein **deutschsprachiger, praxisorientierter Kubernetes Learning Path** für Einsteiger bis Junior Cloud/DevOps Engineers. Es richtet sich an Menschen, die Kubernetes systematisch und hands-on lernen wollen.

## Sprache & Stil

- **Hauptsprache: Deutsch** – alle Inhalte, Erklärungen, Aufgaben und Lab-Anleitungen auf Deutsch
- Technische Begriffe (Pod, Deployment, kubectl, etc.) werden nicht übersetzt – sie werden als Fachbegriffe behandelt und erklärt
- Schreib verständlich, aber technisch korrekt
- Kein Marketing-Sprech, keine leeren Phrasen
- Zielgruppe: Einsteiger, die aber ernst genommen werden wollen

## Primärquellen

Alle fachlichen Aussagen basieren auf:
1. [Offizielle Kubernetes Dokumentation](https://kubernetes.io/docs/)
2. [CNCF Glossary](https://glossary.cncf.io/)
3. Offizielle Tool-Dokumentationen (Helm, Argo CD, Prometheus, etc.)

Keine erfundenen Kommandos. Kein Halbwissen. Im Zweifelsfall: nachschlagen und verlinken.

## Qualitätsregeln

- **YAML muss valide sein** – alle Manifeste vor dem Commit mit `kubectl apply --dry-run=client` oder einem YAML-Linter prüfbar
- **Keine echten Secrets committen** – `secret.example.yaml` nur mit Dummy-Werten und explizitem Hinweis
- **Labs sollen lokal mit kind nachvollziehbar sein** – keine Cloud-Voraussetzungen für Grundlagenlabs
- **Keine erfundenen kubectl-Flags oder API-Felder** – wenn unsicher, leer lassen und kommentieren
- **Keine Copy-Paste aus externen Quellen** – eigene Erklärungen, eigene Beispiele

## Modulstruktur (verpflichtend)

Jedes Modul-README folgt dieser Struktur:

```markdown
# Modul XX – Titel

## Ziel des Moduls
## Warum ist das wichtig?
## Kernkonzepte
## Praxisaufgabe
## Beispiel-Kommandos
## Typische Fehler
## Checkpoint
## Weiterführende Links
```

## Lab-Struktur (verpflichtend)

Jedes Lab-README folgt dieser Struktur:

```markdown
# Lab XX – Titel

## Ziel
## Voraussetzungen
## Schritt-für-Schritt
## Validierung
## Cleanup
## Erweiterungsaufgabe
```

## Arbeitsregeln für Claude Code

- Bei Änderungen an Modulen oder Labs: prüfen ob README.md und roadmap/01-learning-path.md noch aktuell sind
- Neue Module/Labs immer in der Übersichtstabelle in README.md eintragen
- Keine neuen Abhängigkeiten ohne Begründung einführen
- Wenn etwas unklar ist: nachfragen statt raten
- Bestehende Dateien erweitern/verbessern statt blind überschreiben

## Git-Regeln

- Keine Secrets, Tokens, Passwörter oder privaten Schlüssel committen
- `.gitignore` beachten
- Commit-Messages auf Englisch, beschreibend
- Keine `node_modules`, `__pycache__`, `.DS_Store` oder ähnliche Artefakte
