# Kubernetes Cluster Setup

Hands-on notes for creating Kubernetes clusters, from local labs to multi-node deployments. Each guide records prerequisites, setup commands, verification, and troubleshooting from practical exercises.

## Methods

| Method | Approach | Learning focus | Progress |
|---|---|---|---|
| kind | Runs Kubernetes nodes in containers | Local development and testing | Lab completed; guide in progress |
| kubeadm | Bootstraps Kubernetes nodes | Node preparation, networking, and cluster assembly | Planned |
| Kubespray | Automates deployment with Ansible | Repeatable multi-node provisioning | Planned |
| RKE2 | Kubernetes distribution | Cluster deployment and operations | Planned |
| minikube | Creates local Kubernetes clusters | Local experimentation | Future exploration |
| K3s | Lightweight Kubernetes distribution | Small-footprint deployments | Future exploration |
| MicroK8s | Kubernetes distribution packaged as a snap | Local and multi-node deployments | Future exploration |

## Guides

- [kind setup](kind/README.md)
- [kind troubleshooting](kind/troubleshooting.md)

Guide links become available as their files are added. Other method folders will be added as the labs progress.

## First Lab — kind

Ubuntu with rootless Podman, kind, and kubectl.

1. Install Go and Podman.
2. Install kind and persist the Go binary directory in PATH.
3. Create a cluster using a systemd scope with `Delegate=yes`.
4. Install kubectl and configure cluster access.
5. Verify node readiness and system pods.

Troubleshooting notes cover PATH, rootless delegation, and inotify limits.

## Guide Format

Each method will include prerequisites and tested versions, installation steps, validation commands, troubleshooting, and cleanup instructions.

These exercises document learning environments. Production deployments require environment-specific planning and validation.
