# Intermediate-Übungen

## Übung 1: Multi-Container Pod

**Aufgabe:** Erstelle einen Pod mit zwei Containern:
- Container 1: `nginx:1.27-alpine`, hört auf Port 80
- Container 2: `busybox:1.36`, der alle 10 Sekunden `wget -qO- localhost` ausführt und das Ergebnis in `/shared/log.txt` schreibt
- Beide Container teilen ein `emptyDir`-Volume unter `/shared`

Prüfe nach dem Start die Log-Datei im busybox-Container.

<details>
<summary>Lösung anzeigen</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container
spec:
  volumes:
  - name: shared
    emptyDir: {}
  containers:
  - name: nginx
    image: nginx:1.27-alpine
    ports:
    - containerPort: 80
    volumeMounts:
    - name: shared
      mountPath: /shared
  - name: logger
    image: busybox:1.36
    command:
    - sh
    - -c
    - |
      while true; do
        wget -qO- localhost >> /shared/log.txt 2>&1
        sleep 10
      done
    volumeMounts:
    - name: shared
      mountPath: /shared
```

```bash
kubectl apply -f multi-container.yaml
kubectl exec -it multi-container -c logger -- cat /shared/log.txt
```
</details>

---

## Übung 2: RBAC einrichten

**Aufgabe:** 
1. Erstelle einen ServiceAccount `leser` im Namespace `default`
2. Erstelle eine Role `pod-leser`, die nur `get`, `list` und `watch` auf Pods erlaubt
3. Binde die Role an den ServiceAccount
4. Teste, ob der ServiceAccount Pods listen kann, aber keine Deployments löschen kann

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl create serviceaccount leser

kubectl create role pod-leser \
  --verb=get,list,watch \
  --resource=pods

kubectl create rolebinding pod-leser-binding \
  --role=pod-leser \
  --serviceaccount=default:leser

kubectl auth can-i list pods --as=system:serviceaccount:default:leser
# -> yes

kubectl auth can-i delete deployments --as=system:serviceaccount:default:leser
# -> no
```
</details>

---

## Übung 3: PersistentVolumeClaim

**Aufgabe:** 
1. Erstelle einen PVC `daten-pvc` mit 500Mi und AccessMode `ReadWriteOnce`
2. Starte einen Pod, der diesen PVC unter `/daten` mountet
3. Schreibe eine Datei in `/daten/test.txt`
4. Lösche den Pod, starte ihn neu (neuer Pod, gleicher PVC)
5. Prüfe, ob die Datei noch vorhanden ist

<details>
<summary>Lösung anzeigen</summary>

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: daten-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
---
apiVersion: v1
kind: Pod
metadata:
  name: daten-pod
spec:
  volumes:
  - name: daten
    persistentVolumeClaim:
      claimName: daten-pvc
  containers:
  - name: app
    image: busybox:1.36
    command: ["sleep", "3600"]
    volumeMounts:
    - name: daten
      mountPath: /daten
```

```bash
kubectl apply -f pvc.yaml
kubectl exec -it daten-pod -- sh -c 'echo "test" > /daten/test.txt'
kubectl delete pod daten-pod
kubectl apply -f pvc.yaml  # Pod neu starten
kubectl exec -it daten-pod -- cat /daten/test.txt
# -> test
```
</details>

---

## Übung 4: Resource Limits und OOMKilled simulieren

**Aufgabe:** Erstelle einen Pod mit einem sehr niedrigen Memory-Limit (z.B. `4Mi`). Starte einen Prozess im Pod, der mehr Memory verbraucht. Beobachte den OOMKilled-Status.

**Hinweis:** Das ist intentional kaputt – du sollst beobachten, was passiert.

<details>
<summary>Lösung anzeigen</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: oom-test
spec:
  containers:
  - name: app
    image: polinux/stress
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "50M"]
    resources:
      limits:
        memory: "4Mi"
```

```bash
kubectl apply -f oom.yaml
kubectl get pod oom-test -w
# STATUS: OOMKilled
kubectl describe pod oom-test
# Last State: Terminated, Reason: OOMKilled
```
</details>

---

## Übung 5: Horizontal Pod Autoscaler

**Aufgabe:** 
1. Stelle sicher, dass der Metrics Server läuft
2. Erstelle ein Deployment `skalierbare-app` mit Resource Requests (`cpu: "100m"`)
3. Erstelle einen HPA, der zwischen 2 und 8 Replicas skaliert, wenn CPU-Auslastung > 50%
4. Beobachte den HPA-Status

<details>
<summary>Lösung anzeigen</summary>

```bash
kubectl create deployment skalierbare-app \
  --image=nginx:1.27-alpine \
  --replicas=2

kubectl set resources deployment skalierbare-app \
  --requests=cpu=100m,memory=32Mi

kubectl autoscale deployment skalierbare-app \
  --cpu-percent=50 \
  --min=2 \
  --max=8

kubectl get hpa
kubectl describe hpa skalierbare-app
```
</details>

---

## Aufräumen

```bash
kubectl delete pod multi-container daten-pod oom-test --ignore-not-found
kubectl delete pvc daten-pvc --ignore-not-found
kubectl delete deployment skalierbare-app --ignore-not-found
kubectl delete hpa skalierbare-app --ignore-not-found
kubectl delete serviceaccount leser --ignore-not-found
kubectl delete role pod-leser --ignore-not-found
kubectl delete rolebinding pod-leser-binding --ignore-not-found
```
