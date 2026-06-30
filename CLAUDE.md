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
- **Expected Output nach wichtigen Befehlen** – Lernende müssen wissen, ob ihr Befehl korrekt war
- **Definition of Done am Ende jedes Moduls** – klar formulierte Abschlusskriterien

## Callout-System

Verwende in Modulen und Labs folgende GitHub-kompatiblen Callouts (sparsam einsetzen):

```markdown
> [!NOTE]
> Zusätzliche hilfreiche Information, die das Verständnis vertieft.

> [!TIP]
> Praktischer Tipp, der die Arbeit mit Kubernetes erleichtert.

> [!IMPORTANT]
> Wichtiger Punkt für das Verständnis oder die Funktion – nicht überspringen.

> [!WARNING]
> Häufige Fehlerquelle oder bekanntes Risiko – bitte lesen.

> [!CAUTION]
> Potenziell gefährliche Aktion, z. B. Löschen von Ressourcen oder Cluster-Änderungen.
```

**Regeln für Callouts:**
- Maximal 1–2 pro Modul/Lab
- Nicht als Ersatz für normalen Text verwenden
- `[!CAUTION]` nur bei echtem Datenverlust-Risiko

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

## Was du baust
Kurze Beschreibung (2–4 Sätze) + verwendete K8s-Objekte + optionales ASCII-Diagramm.

## Ziel
## Voraussetzungen
## Schritt-für-Schritt

Nach wichtigen Befehlen: "Erwartete Ausgabe:" mit realistischem Beispiel.

## Validierung
Konkrete kubectl-Checks, die Erfolg bestätigen.

## Cleanup
Bevorzugt: kubectl delete -f manifests/ oder kubectl delete namespace <name>.

## Erweiterungsaufgabe
```

## Dokumentations-Website (MkDocs)

- Die Website basiert auf **MkDocs** mit **Material for MkDocs**
- Alle Lerninhalte liegen unter `docs/` (Module, Labs, Exercises, Resources, Examples, Roadmap)
- Neue Seiten müssen in `mkdocs.yml` unter `nav:` eingetragen werden
- Nach Dokumentationsänderungen: `mkdocs build --strict` ausführen
- Das Root-`README.md` ist nur die kurze GitHub-Landingpage – nicht die vollständige Dokumentation
- Website-Deployment läuft automatisch per GitHub Actions bei jedem Push auf `main`
- Lokale Vorschau: `pip install -r requirements.txt && mkdocs serve`

## Arbeitsregeln für Claude Code

- Bei Änderungen an Modulen oder Labs: prüfen ob README.md und docs/roadmap/01-learning-path.md noch aktuell sind
- Neue Module/Labs immer in `mkdocs.yml` nav und `docs/modules/index.md` bzw. `docs/labs/index.md` eintragen
- Keine neuen Abhängigkeiten ohne Begründung einführen
- Wenn etwas unklar ist: nachfragen statt raten
- Bestehende Dateien erweitern/verbessern statt blind überschreiben

## Git-Regeln

- Keine Secrets, Tokens, Passwörter oder privaten Schlüssel committen
- `secret.example.yaml` nur mit Dummy-Werten und explizitem Hinweis
- `.gitignore` beachten – `.venv/` und `site/` werden nicht committed
- Commit-Messages auf Englisch, beschreibend
- Kein Claude/Anthropic/AI-Attribution in Commit-Messages oder Dokumentation
- Keine `node_modules`, `__pycache__`, `.DS_Store` oder ähnliche Artefakte
