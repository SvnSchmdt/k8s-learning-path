# Module 08 – Storage

## Goal

After this module you understand how Kubernetes manages persistent storage. You can create PersistentVolumes, PersistentVolumeClaims, and StorageClasses, and use them in Pods.

## Why does this matter?

Pods are ephemeral — when a Pod dies, all local data is gone. For applications that need to persist data (databases, file uploads, etc.), you need persistent storage that exists independently of the Pod lifecycle.

## Key Concepts

- **Volume:** Temporary or persistent storage mounted into a Pod. Lifetime depends on the volume type.
- **PersistentVolume (PV):** A storage resource in the cluster, provisioned by an admin or dynamically by a StorageClass. Exists independently of Pods.
- **PersistentVolumeClaim (PVC):** A request for storage by a Pod. The PVC is bound to a matching PV.
- **StorageClass:** Defines how storage is dynamically provisioned (provisioner, parameters). With StorageClasses, you don't create PVs manually.
- **AccessModes:** How storage can be used — ReadWriteOnce (RWO), ReadOnlyMany (ROX), ReadWriteMany (RWX).

## Storage Types

| Type | Lifetime | Use case |
|------|----------|----------|
| `emptyDir` | Pod lifetime | Temporary shared storage between containers |
| `hostPath` | Node lifetime | Testing/dev only, not for production |
| `configMap`/`secret` | Config-driven | Configuration as files |
| `PersistentVolumeClaim` | Independent of Pod | Production persistent data |

## Hands-On Task

### emptyDir (temporary, for testing)

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
      mountPath: /tmp/data
```

### Create and use a PVC

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-data
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  # storageClassName: standard  # available in kind
```

```bash
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl describe pvc my-data
```

Use the PVC in a Pod:

```yaml
spec:
  volumes:
  - name: data-volume
    persistentVolumeClaim:
      claimName: my-data
  containers:
  - name: app
    image: nginx:1.27-alpine
    volumeMounts:
    - name: data-volume
      mountPath: /var/data
```

### View StorageClasses

```bash
kubectl get storageclass
kubectl describe storageclass standard   # kind's default StorageClass
```

## Common Mistakes

- **PVC stays Pending:** No matching PV or StorageClass not configured. `kubectl describe pvc` shows the reason.
- **ReadWriteMany not supported:** Most simple StorageClasses (hostPath, local) only support ReadWriteOnce. For RWX you need NFS or cloud storage.
- **Data lost after Pod restart:** The Pod uses `emptyDir` instead of a PVC. emptyDir only lives as long as the Pod.
- **PV not released:** ReclaimPolicy `Retain` means the PV remains after PVC deletion and must be manually cleaned up.

## Checkpoint

- [ ] What is the difference between `emptyDir` and a PersistentVolumeClaim?
- [ ] What is the difference between a PV and a PVC?
- [ ] What does a StorageClass do?
- [ ] What happens to the data in a PVC when the Pod is restarted?
- [ ] What does `accessMode: ReadWriteOnce` mean?

## Definition of Done

You are done with this module when you:

- [ ] Can explain the difference between emptyDir and a PersistentVolumeClaim
- [ ] Have created a PVC and bound it to a Pod
- [ ] Have written data to the PVC, deleted the Pod, and found the data after restart
- [ ] Can explain what a StorageClass does
- [ ] Can answer all checkpoint questions

## Further Reading

- [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)
