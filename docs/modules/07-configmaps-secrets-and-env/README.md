# Module 07 – ConfigMaps, Secrets & Environment Variables

## Goal

After this module you can inject configuration into Pods using ConfigMaps and Secrets — both as environment variables and as mounted files. You understand the security implications of Secrets and know what happens when a ConfigMap is updated.

## Why does this matter?

Hardcoding configuration into container images is an anti-pattern. Images should be environment-agnostic. ConfigMaps and Secrets decouple configuration from the image, allowing the same image to run in dev, staging, and production with different configuration.

## Key Concepts

- **ConfigMap:** Stores non-sensitive key-value configuration data. Can be injected as environment variables or mounted as files.
- **Secret:** Stores sensitive data (passwords, tokens, certificates). Data is base64-encoded (not encrypted by default at rest — use encryption at rest for production).
- **Environment variables:** The simplest way to inject configuration. Available in the container as `$VAR_NAME`.
- **Volume mount:** Mounts ConfigMap or Secret content as files. Useful for config files, certificates, and `.env` files.
- **`envFrom`:** Inject all keys from a ConfigMap or Secret as environment variables at once.

## ConfigMap vs. Secret

| | ConfigMap | Secret |
|-|-----------|--------|
| Use for | Non-sensitive config | Passwords, tokens, certs |
| Stored as | Plain text | base64-encoded |
| Max size | 1 MiB | 1 MiB |
| Access control | RBAC | RBAC (stricter by default) |

## Hands-On Task

### Create a ConfigMap

```bash
# From literal values
kubectl create configmap app-config \
  --from-literal=LOG_LEVEL=debug \
  --from-literal=APP_PORT=8080

kubectl describe configmap app-config
```

### Create a Secret

```bash
# From literal values (base64-encoded automatically)
kubectl create secret generic db-credentials \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASSWORD=supersecret

# View (base64-encoded)
kubectl get secret db-credentials -o yaml
```

### Inject into a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: nginx:1.27-alpine
    envFrom:
    - configMapRef:
        name: app-config
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: DB_PASSWORD
```

```bash
kubectl apply -f pod.yaml
kubectl exec app-pod -- env | grep -E "LOG_LEVEL|DB_PASSWORD"
```

### Mount as files

```yaml
volumes:
- name: config-vol
  configMap:
    name: app-config
containers:
- name: app
  volumeMounts:
  - name: config-vol
    mountPath: /etc/config
```

## Common Mistakes

- **Storing real secrets in Git:** Never commit actual credentials. Use `secret.example.yaml` with dummy values only.
- **ConfigMap update not reflected in env vars:** Environment variables are resolved at Pod start. To pick up updates, restart the Pod. Volume-mounted files update automatically.
- **Forgetting base64 encoding in YAML Secrets:** In a Secret YAML manifest, `data` values must be base64-encoded. Use `stringData` to provide plain text and let Kubernetes encode it.

## Checkpoint

- [ ] What is the difference between a ConfigMap and a Secret?
- [ ] What are the two ways to inject ConfigMap data into a Pod?
- [ ] What happens to environment variable values when a ConfigMap is updated?
- [ ] Why should Secrets not be committed to Git?

## Definition of Done

You are done with this module when you:

- [ ] Have created a ConfigMap and a Secret and injected both into a Pod
- [ ] Have verified the environment variables are visible inside the container
- [ ] Have mounted a ConfigMap as a file and read it from inside the Pod
- [ ] Can explain the update behavior difference between env vars and volume mounts
- [ ] Can answer all checkpoint questions

## Further Reading

- [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)
