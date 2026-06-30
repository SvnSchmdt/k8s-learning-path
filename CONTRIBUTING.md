# Contributing – Beitragen

Vielen Dank für dein Interesse, diesen Lernpfad zu verbessern! Beiträge sind herzlich willkommen.

---

## Was du beitragen kannst

- **Neues Modul:** Lerninhalt zu einem Kubernetes-Thema, das noch fehlt
- **Neues Lab:** Praxisübung mit Schritt-für-Schritt-Anleitung und Manifesten
- **Übungsaufgabe:** Neue Troubleshooting-Szenarien oder Aufgaben
- **Fehlerkorrektur:** Technische Fehler, veraltete Kommandos, kaputte Links
- **Verbesserungen:** Verständlichkeit, Vollständigkeit, bessere Erklärungen
- **Übersetzungen:** Aktuell nur Deutsch – aber Englisch wäre eine Erweiterung

---

## Qualitätsregeln

Bitte halte dich an diese Regeln für neue oder geänderte Inhalte:

### Allgemein
- Schreibe auf Deutsch (technische Begriffe bleiben englisch)
- Schreibe einsteigerfreundlich, aber technisch korrekt
- Nutze nur offizielle Quellen: kubernetes.io, CNCF, offizielle Tool-Docs
- Keine erfundenen kubectl-Kommandos oder YAML-Felder

### YAML-Manifeste
- Alle YAML-Dateien müssen syntaktisch korrekt sein
- Teste Manifeste mit `kubectl apply --dry-run=client -f <datei>.yaml`
- Keine echten Secrets, Passwörter oder Tokens committen
- Verwende spezifische Image-Tags (nicht `latest` in Lernbeispielen)

### Module
- Folge der Modul-Struktur aus [CLAUDE.md](CLAUDE.md)
- Mindestens: Ziel, Kernkonzepte, Praxisaufgabe, Checkpoint, Links
- Checkpoint mit mindestens 3 überprüfbaren Fragen

### Labs
- Folge der Lab-Struktur aus [CLAUDE.md](CLAUDE.md)
- Muss mit einem lokalen kind-Cluster funktionieren
- Cleanup-Schritte sind Pflicht
- Validierungs-Schritt ist Pflicht

---

## So trägst du bei

### 1. Fork und Branch erstellen

```bash
git clone https://github.com/DEIN-USERNAME/k8s-learning-path.git
cd k8s-learning-path
git checkout -b add/module-16-advanced-scheduling
```

### 2. Änderungen vornehmen

Halte dich an das Template für Module oder Labs (siehe [CLAUDE.md](CLAUDE.md)).

### 3. Testen

```bash
# YAML-Manifeste prüfen
kubectl apply --dry-run=client -f labs/XX-neues-lab/manifests/

# Links prüfen (falls du neue Links hinzufügst)
# Öffne jeden Link manuell in einem Browser
```

### 4. README.md aktualisieren

Falls du ein neues Modul oder Lab hinzufügst:
- Trage es in die Tabelle in [README.md](README.md) ein
- Aktualisiere [roadmap/01-learning-path.md](roadmap/01-learning-path.md)

### 5. Commit erstellen

```bash
git add .
git commit -m "add: Modul 16 - Advanced Scheduling"
```

**Commit-Message-Konventionen:**
- `add: Beschreibung` – Neuer Inhalt
- `fix: Beschreibung` – Fehlerkorrektur
- `update: Beschreibung` – Inhalt aktualisieren
- `docs: Beschreibung` – Reine Dokumentation

### 6. Pull Request erstellen

- Titel: kurz und beschreibend
- Beschreibung: Was wurde geändert und warum?
- Referenziere Issues, falls vorhanden

---

## PR-Checkliste

Bevor du den PR öffnest:

- [ ] Kein Secret, Passwort oder Token committet
- [ ] Alle neuen YAML-Manifeste validiert (`--dry-run=client`)
- [ ] Keine erfundenen Kommandos oder API-Felder
- [ ] README.md aktualisiert (falls neues Modul/Lab)
- [ ] Modul/Lab-Template befolgt
- [ ] Cleanup-Schritte vorhanden (bei Labs)

---

## Probleme melden

Öffne ein GitHub Issue für:
- Technische Fehler in Manifesten oder Kommandos
- Veraltete Informationen
- Kaputte Links
- Unklare Erklärungen

---

## Verhaltenskodex

Respektvoller Umgang miteinander ist selbstverständlich. Dieser Lernpfad richtet sich an Einsteiger – konstruktives, geduldiges Feedback ist ausdrücklich erwünscht.

---

Danke für deinen Beitrag!
