# Command: new-module

Erstelle ein neues Lernmodul im Kubernetes Learning Path.

## Verwendung

```
/new-module <nummer> <titel>
```

Beispiel: `/new-module 16 advanced-scheduling`

## Workflow

1. **Prüfe ob das Modul bereits existiert:**
   ```bash
   ls modules/<nummer>-*/
   ```

2. **Erstelle das Verzeichnis:**
   ```bash
   mkdir -p modules/<nummer>-<kebab-case-titel>/
   ```

3. **Erstelle die README.md mit dem Pflicht-Template:**

```markdown
# Modul <XX> – <Titel>

## Ziel des Moduls

Was du nach diesem Modul verstehen und können wirst (2-4 Sätze).

## Warum ist das wichtig?

Einordnung in den Kubernetes-Kontext. Warum braucht man das in der Praxis?

## Kernkonzepte

- **Begriff 1**: Präzise Erklärung in 1-2 Sätzen
- **Begriff 2**: Präzise Erklärung in 1-2 Sätzen

## Praxisaufgabe

Schritt-für-Schritt-Aufgabe mit konkreten kubectl-Kommandos.

## Beispiel-Kommandos

```bash
# Kommentar was dieser Befehl macht
kubectl ...
```

## Typische Fehler

- **Fehler**: Fehlermeldung oder Symptom
  **Ursache**: Warum passiert das?
  **Lösung**: Wie behebt man es?

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Frage 1
- [ ] Frage 2
- [ ] Frage 3

## Weiterführende Links

- [Offizielle Kubernetes Docs – Thema](https://kubernetes.io/docs/...)
```

4. **Trage das Modul in README.md ein** (Modultabelle).

5. **Aktualisiere die Roadmap** in `roadmap/01-learning-path.md`.

## Qualitätsprüfung vor dem Commit

- [ ] Alle YAML-Beispiele syntaktisch korrekt
- [ ] Kein erfundener kubectl-Befehl
- [ ] Checkpoint mit mindestens 3 Fragen
- [ ] Link auf offizielle Docs vorhanden
- [ ] Modul in README.md-Tabelle eingetragen
