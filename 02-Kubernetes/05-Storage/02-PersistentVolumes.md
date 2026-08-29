# PersistentVolume 💽

## 1. Overview

A **PersistentVolume (PV)** is a cluster-level storage resource that represents persistent capacity available to Kubernetes workloads.

Unlike `emptyDir`, a PersistentVolume exists independently of any single Pod.

A PV may represent storage provided by:

* Local disks
* NFS
* Ceph
* SAN
* Cloud block storage
* CSI-based storage systems

Applications usually do not use a PV directly. Instead, they request storage through a **PersistentVolumeClaim (PVC)**.

---

## 2. Storage Relationship

```mermaid
flowchart LR
    POD[🚀 Pod] --> PVC[📄 PersistentVolumeClaim]
    PVC --> PV[💽 PersistentVolume]
    PV --> STORAGE[🗄️ Physical / External Storage]
```

The PVC acts as the request, while the PV represents the actual storage resource.

---

## 3. Key Concepts

| Concept                         | Purpose                               |
| ------------------------------- | ------------------------------------- |
| `capacity`                      | Amount of storage provided            |
| `accessModes`                   | Defines how the volume may be mounted |
| `storageClassName`              | Associates the PV with a StorageClass |
| `persistentVolumeReclaimPolicy` | Controls what happens after release   |
| `volumeMode`                    | Filesystem or raw block device        |
| `claimRef`                      | Shows which PVC is bound to the PV    |

Common PV states include:

* `Available`
* `Bound`
* `Released`
* `Failed`

---

## 4. Cheat Sheet

List PersistentVolumes:

```bash
kubectl get pv
```

Inspect a PV:

```bash
kubectl describe pv <pv-name>
```

View YAML:

```bash
kubectl get pv <pv-name> -o yaml
```

Check capacity and status:

```bash
kubectl get pv -o wide
```

Delete a PV:

```bash
kubectl delete pv <pv-name>
```

---

## 5. Practical Example

Suppose an administrator has a 10 GiB storage volume available for a database.

A PersistentVolume can advertise:

```text
capacity: 10Gi
accessMode: ReadWriteOnce
```

A PVC requesting compatible storage can then bind to that PV.

The database Pod mounts the PVC rather than referencing the physical storage directly.

This keeps the application separated from the underlying storage implementation.

---

## 6. YAML Example

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: database-pv
spec:
  capacity:
    storage: 10Gi

  accessModes:
    - ReadWriteOnce

  persistentVolumeReclaimPolicy: Retain

  storageClassName: manual

  hostPath:
    path: /data/database
```

This PV provides:

```text
10Gi
ReadWriteOnce
StorageClass: manual
```

> `hostPath` is useful for labs but is generally unsuitable for production persistent storage because it ties data to one node.

---

## 7. Common Problems 🚨

* PV capacity is smaller than the PVC request
* Access modes do not match
* StorageClass does not match
* PV remains `Available`
* PV becomes stuck in `Released`
* Underlying storage is unavailable
* Node-local storage becomes inaccessible after rescheduling

---

## 8. Interview Questions 🎯

1. What is a PersistentVolume?
2. Is a PV namespaced?
3. What is the relationship between a PV and PVC?
4. What does `Bound` mean?
5. What does `Released` mean?
6. What does the reclaim policy control?
7. Why is `hostPath` usually unsuitable for production?

---

## 9. Related Topics 🔗

* PersistentVolumeClaim
* StorageClass
* Dynamic Provisioning
* Access Modes
* Reclaim Policies
* CSI
* StatefulSet
