# Kubernetes Environment Variables Challenge

## Overview

This challenge demonstrates how to define **environment variables in a Kubernetes Pod** and use them in a container command.

The Pod uses the `bash` image, prints a greeting assembled from three environment variables, and then exits.

## Requirements

- Pod: `print-envars-greeting`
- Container: `print-env-container`
- Image: `bash`
- `GREETING=Welcome to`
- `COMPANY=Nautilus`
- `GROUP=Datacenter`
- Restart policy: `Never`

## YAML Manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting

spec:
  containers:
    - name: print-env-container
      image: bash
      command:
        - /bin/sh
        - -c
        - 'echo "$(GREETING) $(COMPANY) $(GROUP)"'
      env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "Nautilus"
        - name: GROUP
          value: "Datacenter"

  restartPolicy: Never
```

## How It Works

Environment variables are defined under `spec.containers[].env`:

```yaml
env:
  - name: GREETING
    value: "Welcome to"
```

Kubernetes makes these values available to the container process. The command references the variables and produces:

```text
Welcome to Nautilus Datacenter
```

The basic flow is:

```text
GREETING + COMPANY + GROUP
           |
           v
      Container command
           |
           v
Welcome to Nautilus Datacenter
```

## Why `restartPolicy: Never`?

This is a one-shot workload:

```text
Container starts
      ↓
Prints greeting
      ↓
Process exits
      ↓
Pod completes
```

Since the process is expected to finish, the Pod should not continually restart it.

```yaml
restartPolicy: Never
```

## Apply and Verify

Create the Pod:

```bash
kubectl apply -f pod.yaml
```

Check its state:

```bash
kubectl get pod print-envars-greeting
```

After execution, it should normally reach `Completed`.

Read the output:

```bash
kubectl logs print-envars-greeting
```

Expected:

```text
Welcome to Nautilus Datacenter
```

To follow logs:

```bash
kubectl logs -f print-envars-greeting
```

## Troubleshooting

### Wrong greeting

Inspect the live Pod:

```bash
kubectl get pod print-envars-greeting -o yaml
```

Check the exact values of `GREETING`, `COMPANY`, and `GROUP`.

### No output

Use:

```bash
kubectl logs print-envars-greeting
kubectl describe pod print-envars-greeting
```

Then verify the image and command.

### Restart behavior is incorrect

Make sure the Pod-level setting is:

```yaml
restartPolicy: Never
```

## Environment Variables in Real Applications

Hard-coded values are useful for simple exercises, but Kubernetes also allows environment variables to come from:

- **ConfigMaps** for ordinary configuration
- **Secrets** for sensitive configuration
- **Downward API** for Pod and container metadata

This keeps configuration separate from application images.

## Key Takeaways

- Environment variables are configured under `spec.containers[].env`.
- Each direct variable uses `name` and `value`.
- Container commands can reference configured environment variables.
- `/bin/sh -c` executes a command through a shell.
- `restartPolicy: Never` is appropriate for one-shot workloads.
- `kubectl logs` retrieves output even after the container has completed.
- ConfigMaps and Secrets are preferable when configuration should be managed separately.

## Interview Questions

**Where are environment variables defined in a Pod?**  
Under the container's `env` field.

**How can configuration be injected without hard-coding values?**  
By referencing ConfigMaps, Secrets, or other Kubernetes-supported sources.

**What does `restartPolicy: Never` mean?**  
Kubernetes does not restart the Pod's containers after they terminate.

**How do you retrieve output from a completed container?**  
Use `kubectl logs <pod-name>`.

**What does `/bin/sh -c` do?**  
It launches a shell and executes the supplied command string.
