# Beginner-Übungen

## Übung 1: Ersten Pod erstellen

**Aufgabe:** Erstelle einen Pod namens `mein-erster-pod` mit dem Image `nginx:1.27-alpine` im Namespace `default`. Warte bis er läuft, zeige seine IP-Adresse an.

**Hinweise:**
- `kubectl run` oder YAML-Manifest
- `kubectl get pod -o wide` zeigt die IP-Adresse

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl run mein-erster-pod --image=nginx:1.27-alpine
kubectl get pod mein-erster-pod -o wide
# Alternativ mit YAML:
# kubectl apply -f - <<EOF
# apiVersion: v1
# kind: Pod
# metadata:
#   name: mein-erster-pod
# spec:
#   containers:
#   - name: nginx
#     image: nginx:1.27-alpine
# EOF
```
</details>

---

## Übung 2: Deployment mit 3 Replicas

**Aufgabe:** Erstelle ein Deployment namens `web-app` mit dem Image `nginx:1.27-alpine` und 3 Replicas. Skaliere es dann auf 5 Replicas.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl create deployment web-app --image=nginx:1.27-alpine --replicas=3
kubectl get pods -l app=web-app
kubectl scale deployment web-app --replicas=5
kubectl get pods -l app=web-app
```
</details>

---

## Übung 3: Service erstellen

**Aufgabe:** Erstelle einen ClusterIP-Service namens `web-service`, der auf das Deployment `web-app` (Label `app: web-app`) zeigt und Port 80 freigibt.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl expose deployment web-app --name=web-service --port=80 --type=ClusterIP
kubectl get service web-service
kubectl get endpoints web-service
```
</details>

---

## Übung 4: ConfigMap erstellen und nutzen

**Aufgabe:** Erstelle eine ConfigMap `app-konfiguration` mit dem Key `FARBE` und Wert `blau`. Starte einen Pod, der diese ConfigMap als Umgebungsvariable nutzt, und überprüfe, ob der Wert korrekt gesetzt ist.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl create configmap app-konfiguration --from-literal=FARBE=blau

kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: config-test
spec:
  containers:
  - name: test
    image: busybox:1.36
    command: ["env"]
    env:
    - name: FARBE
      valueFrom:
        configMapKeyRef:
          name: app-konfiguration
          key: FARBE
  restartPolicy: Never
EOF

kubectl logs config-test | grep FARBE
```
</details>

---

## Übung 5: Namespace erstellen und nutzen

**Aufgabe:** Erstelle einen Namespace `mein-namespace`. Erstelle darin einen Pod `test-pod` mit dem Image `busybox:1.36`, der den Befehl `sleep 3600` ausführt. Lösche dann alles im Namespace mit einem einzigen Befehl.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl create namespace mein-namespace
kubectl run test-pod --image=busybox:1.36 --command -- sleep 3600 -n mein-namespace
kubectl get pods -n mein-namespace
kubectl delete namespace mein-namespace
```
</details>

---

## Übung 6: Pod-Logs und exec

**Aufgabe:** Starte einen nginx-Pod, exec in ihn hinein und erstelle eine Datei `/tmp/test.txt` mit dem Inhalt "Hallo Kubernetes". Verifiziere die Datei von außen mit `kubectl exec`.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl run nginx-test --image=nginx:1.27-alpine
kubectl wait pod nginx-test --for=condition=Ready
kubectl exec -it nginx-test -- sh -c 'echo "Hallo Kubernetes" > /tmp/test.txt'
kubectl exec nginx-test -- cat /tmp/test.txt
```
</details>

---

## Übung 7: Rolling Update und Rollback

**Aufgabe:** Das Deployment `web-app` (aus Übung 2) soll auf das Image `nginx:1.28-alpine` aktualisiert werden. Führe ein Rolling Update durch und beobachte den Status. Führe anschließend einen Rollback durch.

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl set image deployment/web-app nginx=nginx:1.28-alpine
kubectl rollout status deployment/web-app
kubectl rollout history deployment/web-app
kubectl rollout undo deployment/web-app
kubectl rollout status deployment/web-app
```
</details>

---

## Aufräumen

```bash
kubectl delete deployment web-app
kubectl delete service web-service
kubectl delete configmap app-konfiguration
kubectl delete pod mein-erster-pod nginx-test config-test --ignore-not-found
```
