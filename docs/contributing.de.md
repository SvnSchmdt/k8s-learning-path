# Beitragen

Beiträge sind herzlich willkommen! Dieser Lernpfad verbessert sich durch Community-Input.

---

## Was du beitragen kannst

- **Neues Modul** — Lerninhalt zu einem noch nicht behandelten Kubernetes-Thema
- **Neues Lab** — Praxisübung mit Schritt-für-Schritt-Anleitung und Manifesten
- **Übungsaufgabe** — Neue Troubleshooting-Szenarien oder Praxisaufgaben
- **Fehlerkorrektur** — Falsche Kommandos, veraltete Inhalte, kaputte Links
- **Verbesserungen** — Verständlichkeit, Vollständigkeit, bessere Erklärungen

---

## Qualitätsregeln

- Nur offizielle Quellen verwenden: kubernetes.io, CNCF, offizielle Tool-Dokumentation
- Keine erfundenen kubectl-Kommandos oder YAML-Felder
- Alle YAML-Manifeste müssen syntaktisch korrekt sein
- Keine Secrets, Passwörter oder Tokens – nur Dummy-Werte in `secret.example.yaml`
- Labs müssen mit einem lokalen kind-Cluster funktionieren
- Neue Seiten müssen in `mkdocs.yml` unter `nav:` eingetragen werden
- `mkdocs build --strict` muss fehlerfrei durchlaufen

---

## So trägst du bei

```bash
# 1. Fork und Clone
git clone https://github.com/DEIN-USERNAME/k8s-learning-path.git
cd k8s-learning-path

# 2. Branch erstellen
git checkout -b add/module-16-advanced-scheduling

# 3. Änderungen vornehmen
# Template in CLAUDE.md befolgen
# Bei zweisprachigem Inhalt: README.de.md (Deutsch) UND README.md (Englisch) erstellen

# 4. YAML validieren
kubectl apply --dry-run=client -f docs/labs/XX-neues-lab/manifests/

# 5. MkDocs Build testen
pip install -r requirements.txt
mkdocs build --strict

# 6. Neue Seiten in mkdocs.yml nav eintragen

# 7. Commit und Push
git add .
git commit -m "add: Module 16 - Advanced Scheduling"
```

---

## PR-Checkliste

- [ ] Kein Secret, Passwort oder Token committet
- [ ] Alle neuen YAML-Manifeste validiert (`--dry-run=client`)
- [ ] Keine erfundenen Kommandos oder API-Felder
- [ ] Neue Seiten in `mkdocs.yml` nav eingetragen
- [ ] `mkdocs build --strict` läuft fehlerfrei durch
- [ ] Modul/Lab-Template befolgt
- [ ] Cleanup-Schritte vorhanden (bei Labs)
- [ ] Zweisprachige Dateien erstellt (`.md` Englisch + `.de.md` Deutsch)

---

## Commit-Konventionen

| Präfix | Verwendung |
|--------|-----------|
| `add:` | Neuer Inhalt |
| `fix:` | Fehlerkorrektur |
| `update:` | Inhalt aktualisieren |
| `docs:` | Nur Dokumentation |
