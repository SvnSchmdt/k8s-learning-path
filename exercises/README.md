# Übungen

Dieser Bereich enthält praktische Übungsaufgaben in drei Schwierigkeitsgraden. Die Aufgaben sind absichtlich praxisnah – genau wie Probleme, die in echten Kubernetes-Umgebungen auftreten.

## Übersicht

| Datei | Niveau | Beschreibung |
|-------|--------|-------------|
| [beginner.md](beginner.md) | Einsteiger | Grundlegende YAML-Erstellung, Pod-Management, erste Deployments |
| [intermediate.md](intermediate.md) | Fortgeschritten | Multi-Ressourcen, Helm, RBAC, Storage |
| [troubleshooting.md](troubleshooting.md) | Alle Niveaus | Kaputte Szenarien debuggen und reparieren |

## Wie du die Übungen verwendest

1. Lies die Aufgabe komplett durch, bevor du anfängst
2. Versuche es erst ohne Hilfe
3. Schau nur in die Lösung (Lösung am Ende jeder Aufgabe), wenn du wirklich nicht weiterkommst
4. Lass den Cluster laufen und beobachte, was passiert

## Vorbereitung

Stelle sicher, dass ein kind-Cluster läuft:

```bash
kind create cluster --name lernpfad-uebungen
kubectl config use-context kind-lernpfad-uebungen
```

## Grundsatz

Es gibt keine "falschen" Versuche. Wenn etwas nicht funktioniert: `kubectl describe`, `kubectl logs`, `kubectl get events` – das sind deine besten Freunde.
