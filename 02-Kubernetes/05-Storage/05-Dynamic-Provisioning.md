# Dynamic Provisioning ⚡

## 1. Overview

**Dynamic provisioning** allows Kubernetes to create PersistentVolumes automatically when a PersistentVolumeClaim requests storage.

Without dynamic provisioning, administrators must create PVs manually in advance.

With dynamic provisioning, the flow becomes:

```text
PVC → StorageClass → Provisioner / CSI Driver → New PV
```

This makes storage management faster, more scalable, and easier to automate.

---

## 2. Provisioning Flow

```mermaid
flowchart LR
    POD[🚀 Pod] --> PVC[📄 PVC]
    PVC --> SC[🏷️ StorageClass]
    SC --> CSI[🔌 CSI Driver]
    CSI --> STORAGE[🗄️ Storage Backend]
    STORAGE --> PV[💽 New PV]
    PV --> PVC
```

---

## 3. Key Concepts

| Concept      | Purpose                                |
| ------------ | -------------------------------------- |
| PVC          | Requests storage                       |
| StorageClass | Defines provisioning policy            |
| Provisioner  | Creates the volume                     |
| CSI Driver   | Connects Kubernetes to storage backend |
| PV           | Automatically created storage resource |
| Binding      | Connects the new PV to the PVC         |

Dynamic provisioning is especially useful in large clusters where manual PV creation would be inefficient.

---

## 4. Cheat Sheet

List PVCs:

```bash
kubectl get pvc
```

Watch provisioning:

```bash
kubectl get pvc -w
```

List PVs:

```bash
kubectl get pv
```

Inspect a claim:

```bash
kubectl describe pvc <pvc-name>
```

Check StorageClasses:

```bash
kubectl get sc
```

Inspect CSI components:

```bash
kubectl get pods -A | grep csi
```

---

## 5. Practical Example

Suppose a database requests:

```text
20Gi
ReadWriteOnce
StorageClass: fast
```

No matching PV exists.

Kubernetes reads the `fast` StorageClass and asks the configured CSI provisioner to create a new 20 GiB volume on the storage backend.

A new PV is created automatically and bound to the PVC.

The Pod can then mount the claim without the administrator manually creating any PersistentVolume.

---

## 6. YAML Example

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: example.csi.storage.io
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-storage
spec:
  storageClassName: fast

  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 20Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
    - name: app
      image: nginx:1.27
      volumeMounts:
        - name: data
          mountPath: /data

  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: app-storage
```

When the PVC is created, Kubernetes dynamically provisions a matching PV through the `fast` StorageClass.

---

## 7. Common Problems 🚨

* PVC remains `Pending`
* StorageClass is missing
* CSI provisioner is unavailable
* Storage backend has insufficient capacity
* Provisioner configuration is incorrect
* Volume creation times out
* Binding mode delays provisioning unexpectedly

---

## 8. Interview Questions 🎯

1. What is dynamic provisioning?
2. What triggers dynamic provisioning?
3. What role does a StorageClass play?
4. What component actually creates the storage volume?
5. What happens if no suitable StorageClass exists?
6. Why is dynamic provisioning better than manual PV creation at scale?
7. What does `WaitForFirstConsumer` change?

---

## 9. Related Topics 🔗

* StorageClass
* PersistentVolumeClaim
* PersistentVolume
* CSI
* Access Modes
* Reclaim Policies
