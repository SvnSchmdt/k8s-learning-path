# Troubleshooting-Übungen

Diese Übungen simulieren reale Probleme. Deine Aufgabe: Fehler finden und beheben – ohne sofort in die Lösung zu schauen.

**Arbeitsweise:**
1. Wende das kaputte Manifest an
2. Beobachte den Fehler
3. Analysiere mit `kubectl describe`, `kubectl logs`, `kubectl get events`
4. Behebe den Fehler
5. Validiere die Lösung

---

## Szenario 1: ImagePullBackOff

**Ausgangslage:** Ein Pod lässt sich nicht starten.

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fehler-1
spec:
  containers:
  - name: app
    image: nginx:diese-version-gibt-es-nicht
EOF
```

**Deine Aufgabe:** Finde heraus warum der Pod nicht startet und behebe das Problem.

**Diagnose-Hinweise:**
```bash
kubectl get pod fehler-1
kubectl describe pod fehler-1
# Suche nach Events und "Failed to pull image"
```

<details>
<summary>Lösung anzeigen</summary>

Das Image `nginx:diese-version-gibt-es-nicht` existiert nicht.

```bash
kubectl set image pod/fehler-1 app=nginx:1.27-alpine
# Alternativ: Pod löschen und mit korrektem Image neu erstellen
kubectl delete pod fehler-1
kubectl run fehler-1 --image=nginx:1.27-alpine
```
</details>

---

## Szenario 2: CrashLoopBackOff

**Ausgangslage:** Ein Pod startet, crasht aber sofort.

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fehler-2
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "exit 1"]
EOF
```

**Diagnose-Hinweise:**
```bash
kubectl get pod fehler-2
kubectl logs fehler-2
kubectl logs fehler-2 --previous
kubectl describe pod fehler-2
# Suche nach "Exit Code"
```

<details>
<summary>Lösung anzeigen</summary>

Der Container-Befehl `exit 1` beendet den Container sofort mit Exit Code 1. Kubernetes startet ihn immer wieder neu → CrashLoopBackOff.

```bash
kubectl delete pod fehler-2
kubectl run fehler-2 --image=busybox:1.36 --command -- sleep 3600
```
</details>

---

## Szenario 3: Service findet keine Pods (falscher Selector)

**Ausgangslage:** Der Service antwortet nicht, obwohl Pods laufen.

```bash
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fehler-3-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: meine-app
  template:
    metadata:
      labels:
        app: meine-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.27-alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: fehler-3-svc
spec:
  selector:
    app: falsche-app
  ports:
  - port: 80
    targetPort: 80
EOF
```

**Diagnose-Hinweise:**
```bash
kubectl get endpoints fehler-3-svc
# Erwartung: leer (keine Endpoints)
kubectl describe service fehler-3-svc
kubectl get pods --show-labels
```

<details>
<summary>Lösung anzeigen</summary>

Der Service-Selector `app: falsche-app` stimmt nicht mit dem Pod-Label `app: meine-app` überein.

```bash
kubectl patch service fehler-3-svc \
  -p '{"spec":{"selector":{"app":"meine-app"}}}'

kubectl get endpoints fehler-3-svc
# Jetzt sollten Endpoints sichtbar sein
```
</details>

---

## Szenario 4: ConfigMap nicht gefunden

**Ausgangslage:** Ein Pod startet nicht, weil eine ConfigMap fehlt.

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fehler-4
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    env:
    - name: MEIN_WERT
      valueFrom:
        configMapKeyRef:
          name: nicht-existierende-config
          key: wert
EOF
```

**Diagnose-Hinweise:**
```bash
kubectl get pod fehler-4
kubectl describe pod fehler-4
# Suche nach "configmap ... not found"
```

<details>
<summary>Lösung anzeigen</summary>

Die ConfigMap `nicht-existierende-config` existiert nicht.

```bash
kubectl create configmap nicht-existierende-config --from-literal=wert=hallo

# Pod neu starten (ConfigMap-Fix wird nicht auto-retried)
kubectl delete pod fehler-4
kubectl apply -f fehler-4.yaml  # oder erneut applyen
```
</details>

---

## Szenario 5: Pod im Status Pending (Ressourcenmangel)

**Ausgangslage:** Ein Pod bleibt in `Pending`.

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fehler-5
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    resources:
      requests:
        memory: "999Gi"
        cpu: "999"
EOF
```

**Diagnose-Hinweise:**
```bash
kubectl get pod fehler-5
kubectl describe pod fehler-5
# Suche nach: "Insufficient memory", "0/1 nodes are available"
```

<details>
<summary>Lösung anzeigen</summary>

Die Ressourcen-Requests (999Gi Memory, 999 CPU) übersteigen bei weitem was im Cluster verfügbar ist. Kein Node kann den Pod hosten.

```bash
kubectl delete pod fehler-5
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fehler-5
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"
EOF
```
</details>

---

## Szenario 6: Readiness Probe schlägt fehl

**Ausgangslage:** Pod läuft, aber Traffic kommt nicht an.

```bash
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fehler-6
spec:
  replicas: 2
  selector:
    matchLabels:
      app: fehler-6
  template:
    metadata:
      labels:
        app: fehler-6
    spec:
      containers:
      - name: app
        image: nginx:1.27-alpine
        readinessProbe:
          httpGet:
            path: /dieser-pfad-existiert-nicht
            port: 80
          initialDelaySeconds: 2
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: fehler-6-svc
spec:
  selector:
    app: fehler-6
  ports:
  - port: 80
    targetPort: 80
EOF
```

**Diagnose-Hinweise:**
```bash
kubectl get pods -l app=fehler-6
# READY: 0/1
kubectl describe pod <pod-name>
# Suche nach: Readiness probe failed
kubectl get endpoints fehler-6-svc
# Endpoints: leer!
```

<details>
<summary>Lösung anzeigen</summary>

Die Readiness Probe prüft `/dieser-pfad-existiert-nicht`, was nginx mit 404 beantwortet. Der Pod gilt als "nicht ready" und wird aus den Service-Endpoints entfernt.

```bash
kubectl patch deployment fehler-6 \
  --type='json' \
  -p='[{"op":"replace","path":"/spec/template/spec/containers/0/readinessProbe/httpGet/path","value":"/"}]'

kubectl get pods -l app=fehler-6
# READY: 1/1
kubectl get endpoints fehler-6-svc
# Endpoints: sichtbar
```
</details>

---

## Aufräumen

```bash
kubectl delete pod fehler-1 fehler-2 fehler-4 fehler-5 --ignore-not-found
kubectl delete deployment fehler-3-app fehler-6 --ignore-not-found
kubectl delete service fehler-3-svc fehler-6-svc --ignore-not-found
kubectl delete configmap nicht-existierende-config --ignore-not-found
```
