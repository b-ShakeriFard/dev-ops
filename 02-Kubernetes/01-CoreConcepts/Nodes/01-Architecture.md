# Kubernetes Nodes 🖥️

## Overview

A **Node** is a machine that participates in a Kubernetes cluster.

Nodes generally fall into two categories:

* **Control Plane Nodes** — manage the cluster
* **Worker Nodes** — run application workloads

Together, they form the basic execution architecture of Kubernetes.

---

## Cluster Architecture

```mermaid
flowchart TD
    CLUSTER[☸️ Kubernetes Cluster]

    CLUSTER --> CP[🧠 Control Plane Nodes]
    CLUSTER --> WK[🖥️ Worker Nodes]

    CP --> API[API Server]
    CP --> ETCD[etcd]
    CP --> SCH[Scheduler]
    CP --> CM[Controller Manager]

    WK --> KUBELET[kubelet]
    WK --> PROXY[kube-proxy]
    WK --> CRI[Container Runtime]
    WK --> PODS[🚀 Pods]
```

The control plane makes decisions and maintains cluster state.

Worker nodes execute workloads.

---

## Folder Structure

```text
Nodes/
├── README.md
├── Control-Plane.md
├── Worker-Nodes.md
├── Working-with-Nodes.md
└── Node-Lifecycle.md
```

---

## Main Topics

### `Control-Plane.md`

Explains the components responsible for managing the Kubernetes cluster:

* API Server
* etcd
* Scheduler
* Controller Manager
* High Availability basics

### `Worker-Nodes.md`

Covers the components responsible for running workloads:

* kubelet
* kube-proxy
* Container Runtime
* Pods
* Node resources

### `Working-with-Nodes.md`

Focuses on day-to-day node administration:

```bash
kubectl get nodes
kubectl describe node
kubectl top nodes
kubectl cordon
kubectl drain
kubectl uncordon
```

It also covers labels, taints, and resource inspection.

### `Node-Lifecycle.md`

Explains how nodes:

* Join the cluster
* Report health
* Become `Ready` or `NotReady`
* Enter maintenance
* Recover from failures
* Leave the cluster

---


