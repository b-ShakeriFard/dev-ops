# Init Containers 🧰

## 1. Overview

**Init containers** are special containers that run **before** the main application containers in a Pod.

They are commonly used for preparation tasks such as:

* Waiting for a dependency
* Creating configuration files
* Setting permissions
* Downloading required data
* Preparing a shared volume
* Performing startup checks

Init containers run **sequentially**. Each one must complete successfully before the next starts. The main application containers start only after all init containers have finished.

---

## 2. How Init Containers Work

```mermaid
flowchart LR
    POD[🚀 Pod Starts] --> INIT1[🧰 Init Container 1]
    INIT1 --> INIT2[🧰 Init Container 2]
    INIT2 --> APP[📦 Application Container]

    INIT1 --> VOL[💾 Shared Volume]
    APP --> VOL
```

Init containers can use different images and tools from the main application, which helps keep the application image smaller and simpler.

---

## 3. Cheat Sheet

View Pod status:

```bash
kubectl get pods
```

Inspect init container state:

```bash
kubectl describe pod <pod-name>
```

View logs from an init container:

```bash
kubectl logs <pod-name> -c <init-container-name>
```

View all containers in a Pod:

```bash
kubectl get pod <pod-name> \
  -o jsonpath='{.spec.initContainers[*].name}'
```

---

## 4. Practical Example

Suppose a web application requires a configuration file before it starts.

Instead of building that configuration into the application image, an init container can generate the file and place it inside a shared volume.

The main container then mounts the same volume and reads the prepared configuration.

This separates **startup preparation** from the actual application process.

---

## 5. YAML Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
spec:
  volumes:
    - name: config-volume
      emptyDir: {}

  initContainers:
    - name: prepare-config
      image: busybox
      command:
        - sh
        - -c
        - echo "APP_MODE=production" > /config/app.conf
      volumeMounts:
        - name: config-volume
          mountPath: /config

  containers:
    - name: nginx
      image: nginx:1.27
      volumeMounts:
        - name: config-volume
          mountPath: /usr/share/nginx/html
```

The init container writes data into the shared volume before NGINX starts.

---

## 6. Common Problems 🚨

* Init container repeatedly fails
* Dependency is never reachable
* Incorrect command or script
* Shared volume is not mounted correctly
* Permission errors
* Pod remains in `Init` state
* Init image cannot be pulled

---

## 7. Interview Questions 🎯

1. What is an init container?
2. When do init containers run?
3. Can multiple init containers be used?
4. Do init containers run in parallel?
5. Can init containers share volumes with application containers?
6. What happens when an init container fails?
7. Why use an init container instead of adding tools to the main image?

---

## 8. Related Topics 🔗

* Pods
* Multi-Container Pods
* Sidecar Containers
* Volumes
* ConfigMaps
* Secrets
* Container Lifecycle
