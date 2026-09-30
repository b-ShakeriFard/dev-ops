# 🚀 Behroox's HomeLab - Argo CD Continuous Delivery Progress

## 🎯 Goal

Build the CD (Continuous Delivery) part of our HomeLab CI/CD pipeline:

    Developer
       |
       v
    Gitea (Source Code)
       |
       v
    CI Pipeline
       |
       v
    Nexus Registry
       |
       v
    CD Repository (Kubernetes YAML)
       |
       v
    Argo CD
       |
       v
    Kubernetes Cluster
       |
       v
    Running Application

The objective is to move from manually deploying applications to a
GitOps workflow.

------------------------------------------------------------------------

# ✅ Completed Steps

## 1. Installed Argo CD

Argo CD was installed inside Kubernetes and verified:

``` bash
kubectl get pods -n argocd
```

All Argo CD components became:

    Running

Components include:

-   Application Controller
-   Repo Server
-   API Server
-   Redis
-   ApplicationSet Controller

------------------------------------------------------------------------

# 2. Created CD Repository

Created:

    pipeline-lab-deploy

This repository stores Kubernetes desired state.

Structure:

    pipeline-lab-deploy
    |
    └── manifest
        |
        ├── deployment.yaml
        └── service.yaml

Important idea:

-   CI builds the application.
-   CD repository describes how Kubernetes should run it.

------------------------------------------------------------------------

# 3. Kubernetes Deployment Manifest

The Deployment defines:

-   Application image
-   Replica count
-   Container port
-   Resource limits
-   Readiness checks
-   Image pull credentials

Example:

``` yaml
imagePullSecrets:
  - name: nexus-pull
```

This tells Kubernetes:

"Use this Secret when downloading private images."

------------------------------------------------------------------------

# 4. Nexus Registry Connectivity

The Kubernetes node was tested against Nexus:

``` bash
curl http://192.168.1.23:5043/v2/
```

Result:

    HTTP/1.1 401 Unauthorized

This was GOOD.

It proved:

✅ Kubernetes can reach Nexus\
✅ Nexus Docker Registry API is working\
✅ Authentication is required

------------------------------------------------------------------------

# 5. Configured containerd for Nexus HTTP Registry

Because Nexus was running on HTTP instead of HTTPS, containerd needed
registry configuration.

Created:

    /etc/containerd/certs.d/192.168.1.23:5043/hosts.toml

Containing:

``` toml
server = "http://192.168.1.23:5043"

[host."http://192.168.1.23:5043"]
  capabilities = ["pull", "resolve"]
```

Important concept:

-   Registry configuration tells containerd HOW to connect.
-   Pull Secret tells Kubernetes WHO is allowed to connect.

------------------------------------------------------------------------

# 6. Created Kubernetes Registry Secret

Created namespace:

``` bash
kubectl create namespace pipeline-lab
```

Created:

    nexus-pull

Secret type:

    kubernetes.io/dockerconfigjson

Purpose:

Allow Kubernetes to authenticate against Nexus and download private
images.

------------------------------------------------------------------------

# 7. Connected Argo CD to Gitea

Argo CD successfully connected to:

    pipeline-lab-deploy.git

Using:

    Repository
          |
          v
    Argo CD Repo Server
          |
          v
    Kubernetes YAML

------------------------------------------------------------------------

# 8. First GitOps Deployment 🎉

Created Argo CD Application:

Application:

    pipeline-lab

Repository:

    pipeline-lab-deploy

Path:

    manifest

Destination:

    pipeline-lab namespace

Result:

    Synced ✅
    Healthy ✅

The application pod started successfully.

------------------------------------------------------------------------

# 🧠 What We Have Now

Current state:

    Code
     |
     v
    Gitea
     |
     v
    CI builds image
     |
     v
    Nexus stores image
     |
     v
    Git contains Kubernetes manifests
     |
     v
    Argo CD deploys application

This is GitOps-based Continuous Delivery.

------------------------------------------------------------------------

# 🚧 Remaining Steps

To achieve fully automated CI/CD:

## 1. Enable Argo CD Automatic Sync

Argo CD should automatically deploy Git changes.

Settings:

``` yaml
automated:
  enabled: true
  selfHeal: true
```

------------------------------------------------------------------------

## 2. Update CD Repository Automatically

After CI pushes a new image:

Example:

    pipeline-lab:abc123

CI should update:

``` yaml
image:
  192.168.1.23:5043/lab/pipeline-lab:abc123
```

inside:

    manifest/deployment.yaml

Then:

    Git Commit
         |
         v
    Argo CD detects change
         |
         v
    Automatic Deployment

------------------------------------------------------------------------

# 🎓 Concepts Learned

-   GitOps
-   Continuous Delivery vs Continuous Deployment
-   Argo CD Applications
-   Kubernetes Secrets
-   Private Registry Authentication
-   containerd registry configuration
-   Desired State Management
-   Separation of CI and CD repositories

------------------------------------------------------------------------

# 🏆 Current Achievement

Behroox's HomeLab now has:

✅ Gitea SCM\
✅ Gitea Runner CI\
✅ Nexus Registry\
✅ Kubernetes Cluster\
✅ Argo CD GitOps Delivery\
✅ Private Image Pulling\
✅ First Successful Deployment

Next milestone:

🚀 Fully automated CI → CD pipeline
