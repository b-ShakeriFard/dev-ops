# What Is Kubernetes?

## 1. Overview

Kubernetes is an open-source platform used to deploy, manage, scale, and operate containerized applications. It provides a consistent way to run applications across physical servers, virtual machines, private data centers, and public cloud environments.

Containers make it possible to package an application together with its dependencies. However, running a few containers manually is very different from operating hundreds or thousands of containers in a production environment. Administrators must handle scheduling, networking, scaling, failures, updates, storage, and security.

Kubernetes solves many of these operational challenges through automation.

Instead of manually starting containers on specific servers, administrators describe the desired state of the application. Kubernetes then works continuously to maintain that state.

For example, an administrator may request:

* Three instances of a web application
* A stable network endpoint
* Automatic restart of failed containers
* Controlled rolling updates
* Persistent storage for a database

Kubernetes uses its control plane and worker nodes to make these requirements a reality.

A Kubernetes environment is called a **cluster**. A cluster normally contains:

* A **control plane**, which manages the cluster
* One or more **worker nodes**, which run applications
* A container runtime, such as containerd
* Networking and storage components

Applications are not deployed directly as individual containers. Kubernetes runs containers inside objects called **Pods**. Pods are the smallest deployable units in Kubernetes.

Kubernetes is based on a declarative model. The user defines what the final system should look like, usually through YAML manifests. Kubernetes compares the desired state with the current state and makes changes when the two are different.

This continuous comparison is called **reconciliation**.

For example, when a Deployment requests three replicas but only two Pods are running, Kubernetes attempts to create another Pod automatically.

Important Kubernetes capabilities include:

* Automated container scheduling
* Self-healing
* Horizontal scaling
* Service discovery
* Load balancing
* Rolling updates
* Rollbacks
* Configuration management
* Secret management
* Persistent storage integration
* Resource control
* Declarative infrastructure management

Kubernetes does not normally build application source code or create container images. Those tasks are usually handled by development and CI/CD tools. Kubernetes is primarily responsible for running and managing the resulting containerized workloads.

---

## 2. Syntax and Cheat Sheet

Check the cluster:

```bash
kubectl cluster-info
```

List cluster nodes:

```bash
kubectl get nodes
```

List Pods:

```bash
kubectl get pods
```

List common resources:

```bash
kubectl get all
```

Display detailed information:

```bash
kubectl describe pod <pod-name>
```

Create a resource from YAML:

```bash
kubectl apply -f application.yaml
```

Delete a resource:

```bash
kubectl delete -f application.yaml
```

General command structure:

```text
kubectl <action> <resource> <resource-name>
```

Example:

```bash
kubectl get pod nginx
```

---

## 3. Practical Example

Suppose an online store runs three instances of its frontend application.

If one container crashes, Kubernetes can replace it. If one worker node fails, Kubernetes can recreate the affected workload on another available node. If traffic increases, the number of application replicas can be increased.

When a new application version is released, Kubernetes can gradually replace the old Pods with new ones. This allows the application to remain available during the update.

The administrator describes the desired state, while Kubernetes performs the continuous operational work required to maintain it.

---

## 4. YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-application
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:latest
          ports:
            - containerPort: 80
```

This Deployment requests three Pods running the NGINX container image.

---

## 5. Common Problems

* Insufficient CPU or memory prevents Pods from being scheduled.
* Incorrect YAML indentation causes manifest errors.
* Container images cannot be downloaded.
* Applications start but are not exposed through a Service.
* Pods repeatedly restart because of application failures.
* Missing networking or storage components affect workloads.

---

## 6. Interview Questions

1. What problem does Kubernetes solve?
2. What is a Kubernetes cluster?
3. What is the difference between a container and a Pod?
4. What does desired state mean in Kubernetes?
5. What is reconciliation?
6. What is the role of the control plane?
7. Does Kubernetes build container images?

---

## 7. Related Topics

* Kubernetes Cluster
* Control Plane
* Worker Nodes
* Pods
* Deployments
* Services
* Declarative Configuration
* `kubectl`
