# Gitea, Gitea Actions and Nexus

This folder documents our work on the Continuous Integration (CI) side of the HomeLab: hosting application code in **Gitea**, executing workflows through a **runner**, and preparing container images for **Nexus**.

## What We Will Do

- Establish the Gitea Actions environment and checked that Git pushes target our local Gitea repository.
- Enable repository Actions and verified the registered `rocky-runner`, running in the `gitea-runner` container with the `ubuntu-latest` label.
- Create a sample workflow `.gitea/workflows/hello.yaml`, commit and push it, and successfully run our first greeting job.
- Configure Nexus image storage and verify manual image pushes.
- Create the dedicated **Nexus user** `gitea-ci` and role `gitea-ci-push`, granting repository permissions without administrator access.
- Prepare the workflow to use `NEXUS_USERNAME` and `NEXUS_PASSWORD` through Actions secrets.
- Build and test a small nginx application locally.
- Create `.gitea/workflows/build-image.yaml` to check out code, build an image, tag it with the Git commit SHA, and push it to Nexus.
- Troubleshoot file permissions, workflow placement, Docker-container DNS, firewalld, and checkout download failures.

## What We Will Learn

- Gitea stores code and coordinates workflows; the runner executes jobs.
- Workflow files belong inside the application repository.
- Runner labels match jobs to execution environments.
- Nexus users hold credentials; roles define permissions.
- Host connectivity does not guarantee container connectivity.
- Commit-based image tags link artifacts to source revisions.

## Progress and Next Step

The greeting job, local application test, and manual registry pushes succeeded. Our saved troubleshooting notes do not yet confirm the final automated build-and-push result.

Next: connect the published image to Kubernetes deployment manifests and Argo CD for Continuous Delivery.
