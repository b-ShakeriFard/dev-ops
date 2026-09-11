# Kubernetes Rolling Update and Rollback Challenge

## Overview

This challenge demonstrates how Kubernetes **Deployments** perform rolling updates and how to restore a previous application version with a **rollback**.

The scenario uses an Apache HTTP Server Deployment with three replicas in the `devops` namespace. We:

1. Deploy `httpd:2.4.28`.
2. Expose it with a NodePort Service.
3. Upgrade to `httpd:2.4.43` using a rolling update.
4. Verify the rollout.
5. Roll back to the original `httpd:2.4.28` version.

---

## Architecture

```text
                 NodePort :30008
                       |
                       v
                httpd-service
                    :80
                       |
          +------------+------------+
          |            |            |
          v            v            v
       httpd Pod    httpd Pod    httpd Pod
          :80          :80          :80

        Deployment: httpd-deploy
             replicas: 3
```

---

## Requirements

- Namespace: `devops`
- Deployment: `httpd-deploy`
- Container: `httpd`
- Initial image: `httpd:2.4.28`
- Replicas: `3`
- Strategy: `RollingUpdate`
- `maxSurge: 1`
- `maxUnavailable: 2`
- Service: `httpd-service`
- Service type: `NodePort`
- NodePort: `30008`
- Upgrade image: `httpd:2.4.43`
- Finally roll back to the original version

---

## 1. Create the Namespace

```bash
kubectl create namespace devops
```

Verify:

```bash
kubectl get namespace devops
```

---

## 2. Deployment Manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  namespace: devops

spec:
  replicas: 3

  selector:
    matchLabels:
      app: httpd-deploy

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 2
      maxSurge: 1

  template:
    metadata:
      labels:
        app: httpd-deploy

    spec:
      containers:
        - name: httpd
          image: httpd:2.4.28
          ports:
            - containerPort: 80
```

Apply it:

```bash
kubectl apply -f deploy.yaml
```

Verify:

```bash
kubectl get deployment -n devops
kubectl get pods -n devops
```

---

## 3. NodePort Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: devops

spec:
  type: NodePort

  selector:
    app: httpd-deploy

  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
```

Apply:

```bash
kubectl apply -f svc.yaml
```

Verify:

```bash
kubectl get svc -n devops
```

Expected port mapping:

```text
80:30008/TCP
```

Traffic follows:

```text
<NodeIP>:30008
       |
       v
httpd-service:80
       |
       v
httpd Pods:80
```

---

## 4. Understanding the Rolling Update Strategy

The Deployment specifies:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 2
    maxSurge: 1
```

### `maxSurge: 1`

Kubernetes may temporarily create **one Pod above the desired replica count**.

With three replicas, Kubernetes may temporarily have:

```text
3 desired + 1 surge = 4 Pods
```

### `maxUnavailable: 2`

Up to two desired replicas may be unavailable while the update is taking place.

These settings control the balance between rollout speed and application availability.

---

## 5. Upgrade the Application

The required upgrade is:

```text
httpd:2.4.28
       |
       v
httpd:2.4.43
```

Update the image:

```bash
kubectl set image deployment/httpd-deploy \
  httpd=httpd:2.4.43 \
  -n devops
```

Here:

```text
httpd-deploy      -> Deployment name
httpd             -> Container name
httpd:2.4.43      -> New image
```

Kubernetes modifies the Pod template, creates a new ReplicaSet, and performs the rolling update.

---

## 6. Watch the Rollout

```bash
kubectl rollout status deployment/httpd-deploy -n devops
```

You can also watch Pods:

```bash
kubectl get pods -n devops -w
```

Conceptually:

```text
Old ReplicaSet
httpd:2.4.28
     |
     | rolling update
     v
New ReplicaSet
httpd:2.4.43
```

Kubernetes gradually scales the new ReplicaSet up while scaling the old ReplicaSet down according to the configured rolling-update strategy.

---

## 7. Verify the New Image

```bash
kubectl get deployment httpd-deploy \
  -n devops \
  -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
```

Expected:

```text
httpd:2.4.43
```

---

## 8. View Rollout History

```bash
kubectl rollout history deployment/httpd-deploy -n devops
```

The Deployment controller retains old ReplicaSets, which makes rollback possible.

A simplified history at this stage is:

```text
Revision 1 -> httpd:2.4.28
Revision 2 -> httpd:2.4.43
```

---

## 9. Roll Back

To undo the most recent rollout:

```bash
kubectl rollout undo deployment/httpd-deploy -n devops
```

Expected response:

```text
deployment.apps/httpd-deploy rolled back
```

Then monitor it:

```bash
kubectl rollout status deployment/httpd-deploy -n devops
```

---

## 10. Verify the Rollback

Check the image again:

```bash
kubectl get deployment httpd-deploy \
  -n devops \
  -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
```

Expected:

```text
httpd:2.4.28
```

The application has returned to its original version.

---

## Why Does Rollback Create Another Revision?

An important observation from the lab was that after rollback, the history showed later revision numbers rather than simply returning the Deployment's revision counter to `1`.

Conceptually:

```text
Revision 1
httpd:2.4.28
      |
      | update
      v
Revision 2
httpd:2.4.43
      |
      | rollback
      v
Revision 3
httpd:2.4.28
```

A rollback is itself a change to the Deployment's Pod template. Kubernetes therefore records the restored configuration as the latest revision rather than moving the revision counter backward.

---

## Roll Back to a Specific Revision

When several revisions exist, inspect them:

```bash
kubectl rollout history deployment/httpd-deploy -n devops
```

Then restore a particular revision:

```bash
kubectl rollout undo deployment/httpd-deploy \
  --to-revision=2 \
  -n devops
```

Use this carefully: revision numbers refer to rollout history, not directly to application version numbers.

---

## Important Lab Lesson: Do Not Make Unnecessary Image Changes

This challenge specifically warned against experimenting with other image versions.

Every change to the Deployment's Pod template can affect rollout history.

For example, accidentally deploying:

```text
httpd:latest
```

and then correcting it to:

```text
httpd:2.4.43
```

may create additional revisions and make an automated rollback validation fail.

For revision-sensitive tasks, follow the requested sequence exactly.

---

## Troubleshooting

### Invalid RollingUpdate field

Use the exact camelCase field names:

```yaml
rollingUpdate:
  maxUnavailable: 2
  maxSurge: 1
```

Kubernetes performs strict schema validation, so capitalization and spelling matter.

### Service cannot reach Pods

Check labels and selectors:

```bash
kubectl get pods -n devops --show-labels
kubectl describe svc httpd-service -n devops
```

The Service selector must match:

```yaml
app: httpd-deploy
```

### Check rollout problems

```bash
kubectl rollout status deployment/httpd-deploy -n devops
kubectl describe deployment httpd-deploy -n devops
kubectl get rs -n devops
kubectl get pods -n devops
```

### Verify the currently configured image

```bash
kubectl get deployment httpd-deploy \
  -n devops \
  -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
```

---

## Useful Cheat Sheet

```bash
# Update an image
kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 -n devops

# Watch rollout
kubectl rollout status deployment/httpd-deploy -n devops

# View history
kubectl rollout history deployment/httpd-deploy -n devops

# Undo latest rollout
kubectl rollout undo deployment/httpd-deploy -n devops

# Undo to a particular revision
kubectl rollout undo deployment/httpd-deploy --to-revision=2 -n devops

# View ReplicaSets
kubectl get rs -n devops

# Verify image
kubectl get deployment httpd-deploy -n devops \
  -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
```

---

## Key Takeaways

- Deployments provide declarative application updates.
- `RollingUpdate` replaces Pods gradually rather than replacing all replicas simultaneously.
- `maxSurge` controls how many extra Pods may temporarily exist.
- `maxUnavailable` controls how many desired Pods may be unavailable during an update.
- Changing a Deployment's Pod template creates a new rollout revision.
- Kubernetes uses ReplicaSets to manage old and new Pod generations.
- `kubectl rollout status` monitors rollout progress.
- `kubectl rollout history` displays Deployment revision history.
- `kubectl rollout undo` restores a previous Deployment configuration.
- A rollback becomes a new current revision; revision numbering does not simply move backward.
- Exact image sequencing matters when automated validation checks rollout history.

---

## Interview Questions

**What is a rolling update?**  
A Deployment strategy that gradually replaces old Pods with Pods using a new configuration while maintaining application availability.

**What does `maxSurge` control?**  
The maximum number of Pods that may temporarily exist above the desired replica count during a rolling update.

**What does `maxUnavailable` control?**  
The maximum number of desired replicas that may be unavailable during an update.

**What command monitors a Deployment rollout?**  
`kubectl rollout status deployment/<name>`.

**How do you view Deployment revision history?**  
Use `kubectl rollout history deployment/<name>`.

**How do you undo the latest Deployment update?**  
Use `kubectl rollout undo deployment/<name>`.

**What Kubernetes object allows a Deployment to preserve old versions?**  
ReplicaSets associated with previous Deployment revisions.

**Does rollback make the revision number decrease?**  
No. The restored Pod template becomes the latest Deployment revision.
