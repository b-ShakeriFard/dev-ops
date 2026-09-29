GitOps with Argo CD

Build the Continuous Delivery (CD) half of our CI/CD pipeline using **Gitea, Nexus, Argo CD, and Kubernetes**. This lab is independent of hardware and Kubernetes distribution.

## What We Will Build

1. Create a dedicated Git repository for Kubernetes deployment manifests.
2. Configure Kubernetes to pull application images from Nexus.
3. Connect Argo CD to the repository and target cluster.
4. Deploy our application using a Deployment and Service.
5. Extend CI to update the image tag in the CD repository after a successful build and push.
6. Enable automatic synchronization, verify updates, and practice rollback through Git.

**Workflow:** CI publishes an image to Nexus and updates its reference in Git. Argo CD synchronizes the manifests with Kubernetes, which pulls and runs the image.

## What You Will Learn

- How CI and CD work together and why their repositories can be separate.
- GitOps: using Git to define the desired application state.
- How Argo CD detects differences between Git and a running cluster.
- How image tags connect a source commit to a deployed version.
- Registry authentication and image-pull troubleshooting.
- Deployment health, synchronization, drift correction, and rollback.

## Final Outcome

A working pipeline that turns a code change into a versioned image and a running application, with deployment history tracked in Git.
