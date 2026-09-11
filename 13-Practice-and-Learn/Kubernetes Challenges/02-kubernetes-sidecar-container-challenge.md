# Kubernetes Sidecar Container with Shared `emptyDir` Volume

## Overview

This challenge demonstrates the **Kubernetes native sidecar container pattern**.

The Pod runs:

- **Nginx** as the main application container.
- **Ubuntu** as a sidecar container that continuously reads Nginx access and error logs.
- A shared **`emptyDir` volume** mounted into both containers at `/var/log/nginx`.

The key detail is that the sidecar is declared under `initContainers` with:

```yaml
restartPolicy: Always
```

Unlike a traditional init container, it continues running for the lifetime of the Pod alongside the main application container.

---

## Architecture

```text
                  Pod: webserver
                       |
              shared-logs (emptyDir)
                 /var/log/nginx
                  /           \
                 /             \
        nginx-container     sidecar-container
         nginx:latest        ubuntu:latest
              |                    |
        writes log files      reads log files
                              every 30 seconds
```

---

## Requirements

The challenge required us to:

1. Create a Pod named `webserver`.
2. Create an `emptyDir` volume named `shared-logs`.
3. Run the main container as:
   - Name: `nginx-container`
   - Image: `nginx:latest`
4. Create the sidecar as an init container:
   - Name: `sidecar-container`
   - Image: `ubuntu:latest`
   - `restartPolicy: Always`
5. Continuously read:
   - `/var/log/nginx/access.log`
   - `/var/log/nginx/error.log`
6. Mount `shared-logs` at `/var/log/nginx` in both containers.
7. Ensure all containers remain running.

---

## YAML Manifest

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: webserver

spec:
  initContainers:
    - name: sidecar-container
      image: ubuntu:latest
      restartPolicy: Always
      command:
        - sh
        - -c
        - "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx

  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx

  volumes:
    - name: shared-logs
      emptyDir: {}

  restartPolicy: Always
```

---

## Apply and Verify

Validate the manifest before creating the Pod:

```bash
kubectl apply --dry-run=client -f pod.yaml
```

Create it:

```bash
kubectl apply -f pod.yaml
```

Check its status:

```bash
kubectl get pod webserver
```

Inspect the Pod:

```bash
kubectl describe pod webserver
```

Inspect the actual specification:

```bash
kubectl get pod webserver -o yaml
```

The main application container should appear under `containers`, while the sidecar should appear under `initContainers`.

---

## Why `emptyDir`?

`emptyDir` is temporary storage whose lifetime is tied to the Pod.

When the Pod starts, Kubernetes creates the volume. Both containers can mount the same volume, allowing Nginx to write log files while the sidecar reads them.

```text
nginx-container
      |
      | writes
      v
 shared-logs
      ^
      | reads
      |
sidecar-container
```

If the individual containers restart, the volume remains available while the Pod still exists. When the Pod itself is removed, the `emptyDir` data is removed as well.

This makes `emptyDir` appropriate here because the challenge does not require persistent log storage.

---

## Native Sidecars vs Regular Init Containers

A normal init container runs **before** the application containers and is expected to terminate successfully.

A native sidecar is represented as an init container with:

```yaml
restartPolicy: Always
```

This changes its lifecycle behavior: Kubernetes starts it during Pod initialization but keeps it running alongside the regular application containers.

This is why the following is important:

```yaml
initContainers:
  - name: sidecar-container
    restartPolicy: Always
```

Simply placing both containers under `containers:` creates a normal multi-container Pod, but it does **not** satisfy a task specifically requiring the native sidecar mechanism.

---

## Troubleshooting Lessons

### Sidecar placed under `containers`

Incorrect for this challenge:

```yaml
containers:
  - name: nginx-container
  - name: sidecar-container
```

Required structure:

```yaml
initContainers:
  - name: sidecar-container
    restartPolicy: Always

containers:
  - name: nginx-container
```

### Incorrect container names

Automated lab validation often checks exact names.

Required:

```text
nginx-container
sidecar-container
```

A Pod can be perfectly healthy and still fail the challenge if the required object or container names do not match.

### Verify the live object, not only the YAML file

A local manifest may differ from the object currently running in Kubernetes.

Useful commands:

```bash
kubectl get pod webserver
kubectl describe pod webserver
kubectl get pod webserver -o yaml
```

---

## Key Takeaways

- Sidecars provide supporting functionality to a main application.
- Containers within a Pod can share volumes.
- `emptyDir` provides Pod-scoped temporary storage.
- Native Kubernetes sidecars use `initContainers` with `restartPolicy: Always`.
- A normal init container terminates before regular containers start; a native sidecar remains running.
- Exact resource and container names matter in automated Kubernetes labs.
- Always verify the **live Kubernetes object** before submitting a challenge.

---

## Interview Questions

**What is a sidecar container?**  
A helper container that runs alongside a primary application container in the same Pod and provides supporting functionality.

**How can two containers in a Pod share files?**  
They can mount the same Kubernetes volume into their filesystems.

**What is an `emptyDir` volume?**  
Temporary Pod-level storage created when a Pod is assigned to a node and removed when the Pod is removed.

**What distinguishes a native sidecar from a normal init container?**  
A native sidecar is defined under `initContainers` with `restartPolicy: Always`, allowing it to continue running alongside regular containers.

**Why use a sidecar for log processing?**  
It separates log-handling responsibilities from the main application while allowing both containers to access the same log files.
