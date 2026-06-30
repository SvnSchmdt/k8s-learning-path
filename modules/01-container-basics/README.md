# Modul 01 – Container Basics

## Ziel des Moduls

Nach diesem Modul verstehst du, was Container sind, wie sie sich von virtuellen Maschinen unterscheiden und wie der Lebenszyklus eines Containers aussieht. Du kannst Container-Images bauen und Container starten.

## Warum ist das wichtig?

Kubernetes orchestriert Container. Ohne ein solides Verständnis davon, was ein Container ist und wie er funktioniert, bleibt Kubernetes eine Blackbox. Container sind die kleinste Einheit, die Kubernetes verwaltet – alles andere baut darauf auf.

## Kernkonzepte

- **Container:** Ein Prozess, der isoliert vom Rest des Systems läuft – mit eigenem Dateisystem, Netzwerk-Namespace und begrenzten Ressourcen. Kein vollständiges Betriebssystem, kein Hypervisor.
- **Container-Image:** Eine unveränderliche, schichtweise aufgebaute Vorlage, aus der Container gestartet werden. Gebaut mit einem `Dockerfile`.
- **Container Registry:** Ein Speicherort für Images, z.B. Docker Hub, GitHub Container Registry oder ein privates Registry.
- **Dockerfile:** Eine Textdatei mit Anweisungen, wie ein Image Schicht für Schicht aufgebaut wird.
- **Container Runtime:** Die Software, die Container ausführt. In Kubernetes wird meist `containerd` oder `CRI-O` verwendet.

## Container vs. Virtuelle Maschine

| Merkmal | Container | VM |
|---------|-----------|-----|
| Startet in | Millisekunden | Sekunden bis Minuten |
| Betriebssystem | Teilt den Host-Kernel | Eigener Kernel |
| Größe | MB | GB |
| Isolation | Prozess-Level | Hardware-Level |
| Overhead | Minimal | Hoch |

## Praxisaufgabe

### Einfaches Image bauen und starten

Erstelle ein `Dockerfile`:

```dockerfile
FROM nginx:1.27-alpine
COPY index.html /usr/share/nginx/html/index.html
```

Erstelle `index.html`:

```html
<!DOCTYPE html>
<html><body><h1>Hallo Kubernetes!</h1></body></html>
```

Image bauen und starten:

```bash
docker build -t mein-nginx:v1 .
docker run -d -p 8080:80 --name mein-nginx mein-nginx:v1
curl http://localhost:8080
```

### Container-Lebenszyklus

```bash
# Container starten
docker run -d --name demo nginx:1.27-alpine

# Logs anzeigen
docker logs demo

# In Container exec'en
docker exec -it demo sh

# Container stoppen und löschen
docker stop demo && docker rm demo
```

## Beispiel-Kommandos

```bash
# Images auflisten
docker images

# Laufende Container anzeigen
docker ps

# Alle Container (auch gestoppte)
docker ps -a

# Image aus Registry pullen
docker pull nginx:1.27-alpine

# Container-Details anzeigen
docker inspect mein-nginx
```

## Typische Fehler

- **Port bereits belegt:** `docker run -p 8080:80` schlägt fehl, wenn Port 8080 schon benutzt wird. Lösung: anderen Port wählen.
- **`latest`-Tag nutzen:** `nginx:latest` ist eine schlechte Praxis in Produktion – die Version ändert sich unbemerkt. Immer spezifische Versionen nutzen.
- **Container startet und stoppt sofort:** Der Hauptprozess im Container ist beendet. `docker logs <name>` zeigt warum.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen einem Container-Image und einem laufenden Container?
- [ ] Warum startet ein Container schneller als eine VM?
- [ ] Was passiert mit einem Container, wenn sein Hauptprozess endet?
- [ ] Was ist eine Container Registry, und warum wird sie in Kubernetes wichtig?

## Weiterführende Links

- [Was sind Container?](https://kubernetes.io/docs/concepts/containers/)
- [Docker Übersicht](https://docs.docker.com/get-started/overview/)
- [OCI Image Spec](https://opencontainers.org/)
