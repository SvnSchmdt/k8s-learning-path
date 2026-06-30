# Modul 08 – Storage

## Ziel des Moduls

Nach diesem Modul verstehst du, wie Kubernetes persistenten Storage verwaltet. Du kannst PersistentVolumes, PersistentVolumeClaims und StorageClasses erstellen und in Pods verwenden.

## Warum ist das wichtig?

Pods sind kurzlebig – wenn ein Pod stirbt, sind alle lokalen Daten weg. Für Anwendungen, die Daten persistieren müssen (Datenbanken, File-Uploads, etc.), braucht man persistenten Storage, der unabhängig vom Pod-Lebenszyklus existiert.

## Kernkonzepte

- **Volume:** Temporärer oder persistenter Speicher, der in einen Pod eingehängt wird. Lebt so lange wie der Pod (bei emptyDir) oder länger (bei PV).
- **PersistentVolume (PV):** Eine Storage-Ressource im Cluster, die von einem Administrator (oder dynamisch durch eine StorageClass) bereitgestellt wird. Existiert unabhängig von Pods.
- **PersistentVolumeClaim (PVC):** Eine Anforderung für Storage durch einen Pod. Der PVC wird an ein passendes PV gebunden.
- **StorageClass:** Definiert, wie Storage dynamisch bereitgestellt wird (provisioner, Parameter). Mit StorageClasses musst du keine PVs manuell erstellen.
- **AccessModes:** Wie der Storage verwendet werden kann – ReadWriteOnce (RWO), ReadOnlyMany (ROX), ReadWriteMany (RWX).

## Storage-Typen im Überblick

| Typ | Lebensdauer | Verwendung |
|-----|-------------|-----------|
| `emptyDir` | Pod-Lebensdauer | Temporärer Shared-Storage zwischen Containern |
| `hostPath` | Node-Lebensdauer | Test/Dev, nicht für Produktion |
| `configMap`/`secret` | Konfigurationsdaten als Dateien | Konfiguration |
| `PersistentVolumeClaim` | Unabhängig vom Pod | Produktive persistente Daten |

## Praxisaufgabe

### emptyDir (temporär, für Tests)

```yaml
spec:
  volumes:
  - name: tmp-storage
    emptyDir: {}
  containers:
  - name: app
    image: nginx:1.27-alpine
    volumeMounts:
    - name: tmp-storage
      mountPath: /tmp/daten
```

### PVC erstellen und verwenden

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meine-daten
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  # storageClassName: standard  # In kind vorhanden
```

```bash
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl describe pvc meine-daten
```

PVC in einem Pod verwenden:

```yaml
spec:
  volumes:
  - name: daten-volume
    persistentVolumeClaim:
      claimName: meine-daten
  containers:
  - name: app
    image: nginx:1.27-alpine
    volumeMounts:
    - name: daten-volume
      mountPath: /var/daten
```

### StorageClasses anzeigen

```bash
kubectl get storageclass
kubectl describe storageclass standard  # kind's Default-StorageClass
```

## Beispiel-Kommandos

```bash
# PVCs anzeigen
kubectl get pvc

# PVs anzeigen
kubectl get pv

# StorageClasses anzeigen
kubectl get sc

# PVC löschen (PV bleibt erhalten, abhängig von ReclaimPolicy)
kubectl delete pvc meine-daten
```

## Typische Fehler

- **PVC bleibt `Pending`:** Kein passendes PV vorhanden oder StorageClass nicht konfiguriert. `kubectl describe pvc` zeigt den Grund.
- **ReadWriteMany nicht unterstützt:** Die meisten einfachen StorageClasses (hostPath, local) unterstützen nur ReadWriteOnce. Für RWX braucht man NFS oder Cloud-Storage.
- **Daten verloren nach Pod-Neustart:** Der Pod nutzt `emptyDir` statt PVC. emptyDir lebt nur so lange wie der Pod.
- **PV nicht freigegeben:** ReclaimPolicy `Retain` bedeutet, das PV bleibt nach PVC-Löschung erhalten und muss manuell bereinigt werden.

## Checkpoint

Du hast das Modul verstanden, wenn du folgende Fragen beantworten kannst:
- [ ] Was ist der Unterschied zwischen `emptyDir` und einem PersistentVolumeClaim?
- [ ] Was ist der Unterschied zwischen PV und PVC?
- [ ] Was macht eine StorageClass?
- [ ] Was passiert mit den Daten in einem PVC, wenn der Pod neu gestartet wird?
- [ ] Was bedeutet `accessMode: ReadWriteOnce`?

## Weiterführende Links

- [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)
