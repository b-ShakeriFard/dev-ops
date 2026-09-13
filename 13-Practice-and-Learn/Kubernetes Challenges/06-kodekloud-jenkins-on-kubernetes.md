# Deploying Jenkins on Kubernetes --- KodeKloud Challenge

## 🎯 Challenge Overview

In this challenge, the goal was to deploy a **Jenkins CI server on
Kubernetes** and expose its web interface outside the cluster.

We created:

-   A dedicated `jenkins` namespace
-   A Jenkins Deployment with one replica
-   A NodePort Service
-   A matching label/selector configuration
-   The required `JAVA_OPTS` environment variable
-   External access to the Jenkins UI through NodePort `30008`

------------------------------------------------------------------------

## 🧠 Architecture

``` text
Browser
   |
   | <Node-IP>:30008
   v
jenkins-service
NodePort Service
   |
   | targetPort: 8080
   v
Jenkins Pod
app=jenkins
   |
   v
jenkins-container
jenkins/jenkins
Port 8080
```

The important Kubernetes relationship is:

``` text
Service selector: app=jenkins
          |
          v
Pod label: app=jenkins
```

If these values do not match, the Service will have no Jenkins endpoint.

------------------------------------------------------------------------

## 1. Create the Namespace

``` bash
kubectl create ns jenkins
```

Instead of repeatedly adding `-n jenkins`, we changed the namespace of
the current context:

``` bash
kubectl config set-context --current --namespace=jenkins
```

Verify:

``` bash
kubectl config get-contexts
```

The current context should show:

``` text
NAMESPACE
jenkins
```

------------------------------------------------------------------------

## 2. Generate a NodePort Service YAML

A useful `kubectl` technique from this challenge was generating a YAML
skeleton without immediately creating the resource:

``` bash
kubectl create svc nodeport jenkins-service \
  --tcp=8080:8080 \
  --dry-run=client -o yaml
```

To save it directly:

``` bash
kubectl create svc nodeport jenkins-service \
  --tcp=8080:8080 \
  --dry-run=client -o yaml > svc.yaml
```

This provides a starting manifest that can then be edited.

> `--dry-run=client` tells kubectl to generate the resource locally
> without submitting it to the API server.

------------------------------------------------------------------------

## 3. Final Service Manifest

The challenge required NodePort `30008`.

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
  namespace: jenkins
spec:
  type: NodePort
  selector:
    app: jenkins
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
      nodePort: 30008
```

Apply it:

``` bash
kubectl apply -f svc.yaml
```

Verify:

``` bash
kubectl get svc
```

Expected mapping:

``` text
8080:30008/TCP
```

### Understanding the Ports

``` text
nodePort: 30008
       |
       v
port: 8080
       |
       v
targetPort: 8080
       |
       v
Jenkins container
```

`nodePort` exposes the Service through a Kubernetes node, while
`targetPort` identifies the port on which Jenkins is listening inside
the Pod.

------------------------------------------------------------------------

## 4. Generate a Deployment Skeleton

Another useful imperative/declarative combination:

``` bash
kubectl create deploy jenkins-deployment \
  --image=jenkins/jenkins \
  --dry-run=client -o yaml > deploy.yaml
```

We then edited the generated YAML to satisfy the exact challenge
requirements.

------------------------------------------------------------------------

## 5. Jenkins Deployment

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
  namespace: jenkins

spec:
  replicas: 1

  selector:
    matchLabels:
      app: jenkins

  template:
    metadata:
      labels:
        app: jenkins

    spec:
      containers:
        - name: jenkins-container
          image: jenkins/jenkins

          ports:
            - containerPort: 8080

          env:
            - name: JAVA_OPTS
              value: "-Djenkins.install.runSetupWizard=false"
```

Apply:

``` bash
kubectl apply -f deploy.yaml
```

------------------------------------------------------------------------

## 6. The `JAVA_OPTS` Variable

The challenge required:

``` yaml
env:
  - name: JAVA_OPTS
    value: "-Djenkins.install.runSetupWizard=false"
```

This passes a Java system property to Jenkins:

``` text
-Djenkins.install.runSetupWizard=false
```

Its purpose in this lab is to skip the normal Jenkins initial setup
wizard.

------------------------------------------------------------------------

## 7. Verify the Jenkins Pod

Immediately after Deployment creation, the Pod may temporarily show:

``` text
0/1   ContainerCreating
```

Check again:

``` bash
kubectl get pods
```

The desired state is:

``` text
1/1   Running
```

To watch continuously:

``` bash
kubectl get pods -w
```

------------------------------------------------------------------------

## 🔍 Troubleshooting: Service Had No Endpoints

An important troubleshooting moment occurred when we ran:

``` bash
kubectl get endpoints
```

and received:

``` text
No resources found in jenkins namespace.
```

The Pod itself was already:

``` text
1/1 Running
```

The problem was simple: `svc.yaml` had been created and edited, but the
Service had not yet been applied.

The fix:

``` bash
kubectl apply -f svc.yaml
```

Afterward:

``` bash
kubectl get endpoints
```

showed an endpoint similar to:

``` text
jenkins-service   10.22.0.9:8080
```

This confirmed that Kubernetes had successfully associated the Service
with the Jenkins Pod.

------------------------------------------------------------------------

## 🔗 Why Labels and Selectors Matter

We verified the Pod labels with:

``` bash
kubectl get pods --show-labels
```

The Pod contained:

``` text
app=jenkins
```

The Service contained:

``` yaml
selector:
  app: jenkins
```

Therefore Kubernetes could build the connection:

``` text
jenkins-service
selector: app=jenkins
        |
        v
Jenkins Pod
label: app=jenkins
        |
        v
10.x.x.x:8080
```

A running Pod alone does **not** guarantee that a Service can reach it.

------------------------------------------------------------------------

## ✅ Final Verification Commands

``` bash
kubectl get pods
```

``` bash
kubectl get svc
```

``` bash
kubectl get pods --show-labels
```

``` bash
kubectl get endpoints
```

On newer Kubernetes versions, `Endpoints` is deprecated in favor of
`EndpointSlice`, so this is also useful:

``` bash
kubectl get endpointslices
```

A healthy setup should show:

``` text
Pod
1/1 Running

Service
jenkins-service -> 8080:30008/TCP

Endpoint
Jenkins-Pod-IP:8080
```

Finally, access Jenkins through the lab-provided browser link or the
Kubernetes node address and NodePort:

``` text
<Node-IP>:30008
```

------------------------------------------------------------------------

## 🛠️ Useful Cheat Sheet

``` bash
# Create namespace
kubectl create ns jenkins

# Set current namespace
kubectl config set-context --current --namespace=jenkins

# Check current context
kubectl config get-contexts

# Generate Service YAML
kubectl create svc nodeport jenkins-service \
  --tcp=8080:8080 \
  --dry-run=client -o yaml > svc.yaml

# Generate Deployment YAML
kubectl create deploy jenkins-deployment \
  --image=jenkins/jenkins \
  --dry-run=client -o yaml > deploy.yaml

# Apply resources
kubectl apply -f deploy.yaml
kubectl apply -f svc.yaml

# Check Pods
kubectl get pods

# Check Services
kubectl get svc

# Check labels
kubectl get pods --show-labels

# Check Service endpoints
kubectl get endpoints

# Modern endpoint representation
kubectl get endpointslices
```

------------------------------------------------------------------------

## ⚠️ Common Problems

### Service has no endpoints

Check:

``` bash
kubectl get pods --show-labels
kubectl get svc jenkins-service -o yaml
```

Make sure:

``` text
Service selector == Pod label
```

### Jenkins Pod remains in `ContainerCreating`

Inspect it:

``` bash
kubectl describe pod <pod-name>
```

Look at the Events section for image-pull, networking, storage, or
runtime errors.

### Jenkins UI is inaccessible

Check the entire path:

``` text
Pod Running?
     ↓
Service exists?
     ↓
Selector matches?
     ↓
Endpoint exists?
     ↓
NodePort = 30008?
     ↓
Jenkins listening on 8080?
```

------------------------------------------------------------------------

## 💡 Key Lessons

This challenge demonstrated that deploying an application on Kubernetes
involves more than simply starting a container.

The complete path was:

``` text
Namespace
   ↓
Deployment
   ↓
ReplicaSet
   ↓
Jenkins Pod
   ↑
Service selector
   ↑
NodePort Service
   ↑
Browser
```

The most important practical lesson was the relationship between
**labels and selectors**. The Jenkins Pod was healthy, but external
traffic could only reach it after the Service existed and correctly
selected the Pod.

We also practiced a very useful workflow:

``` text
kubectl imperative command
        ↓
--dry-run=client -o yaml
        ↓
YAML file
        ↓
Edit exact requirements
        ↓
kubectl apply
```

This combines the speed of imperative commands with the reproducibility
of declarative Kubernetes manifests.

------------------------------------------------------------------------

## 🎤 Interview Questions

**What is a NodePort Service?**\
A Service type that exposes an application on a static port on each
Kubernetes node.

**What does `targetPort` represent?**\
The destination port on the selected Pods.

**How does a Service find its Pods?**\
Through label selectors.

**What happens if the Service selector does not match any Pod labels?**\
The Service exists but has no backend endpoints.

**What does `--dry-run=client -o yaml` do?**\
It generates the resource definition locally as YAML without creating
the resource in the cluster.

**Why use a Deployment for Jenkins in this challenge?**\
The Deployment manages the desired Jenkins Pod and recreates it if
necessary.

**What is the purpose of `containerPort: 8080`?**\
It documents the port exposed by the Jenkins container and can be
referenced by Kubernetes configuration.

**How can you verify that a Service has discovered its backend?**\
Use `kubectl get endpoints` or, on modern Kubernetes, inspect
`EndpointSlice` resources.

------------------------------------------------------------------------

## 🏁 Result

The final Jenkins deployment was successfully running and reachable
through the Kubernetes NodePort Service.

**Challenge completed successfully. ✌️**
