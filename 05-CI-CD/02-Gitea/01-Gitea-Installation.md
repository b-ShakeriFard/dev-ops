# Gitea Installation – HomeLab Run #2

## Goal

This document records the second-run installation of **Gitea** in the DevOps HomeLab, with emphasis on understanding *why* each step is required, including the mistakes encountered and the troubleshooting lessons learned.

The goal of this second run is not merely to get Gitea working. The goal is to understand the infrastructure around it well enough to rebuild and troubleshoot it independently.

---

## Environment

### Host VM

- **Operating System:** Zorin Linux
- **Role:** Gitea server
- **IP address:** `192.168.1.16`
- **Container engine:** Podman
- **Compose tool:** `podman-compose`
- **Gitea image:** `docker.gitea.com/gitea:1.27.0`

### Planned HomeLab architecture

```text
Developer
   |
   | git push
   v
Gitea VM
192.168.1.16
   |
   | triggers CI
   v
Gitea Runner
   |
   | docker build / push
   v
Nexus VM
   |
   | image
   v
GitOps Repository
   |
   v
ArgoCD
   |
   v
Kubernetes
   |
   v
Running Application
```

Important conceptual point:

- **Gitea stores Git repositories**.
- Git repositories can contain application source code, Dockerfiles, CI workflow files, Kubernetes manifests, Helm charts, Kustomize overlays, documentation, and more.
- **CI and CD are processes**, not repositories.
- ArgoCD is not a Git repository. ArgoCD reads desired-state files from Git and reconciles Kubernetes toward that state.

---

# 1. Compose file

The initial Compose file was:

```yaml
services:
  gitea:
    image: docker.gitea.com/gitea:1.27.0
    container_name: gitea
    restart: unless-stopped

    environment:
      USER_UID: 1000
      USER_GID: 1000

    volumes:
      - /opt/gitea/data:/data

    ports:
      - "3000:3000"
      - "2222:22"
```

## What each part means

### Image

```yaml
image: docker.gitea.com/gitea:1.27.0
```

This tells Podman to run Gitea version `1.27.0`.

### Container name

```yaml
container_name: gitea
```

This gives the container the predictable name `gitea`.

Useful commands later include:

```bash
podman ps
podman logs gitea
podman restart gitea
podman inspect gitea
```

### Restart policy

```yaml
restart: unless-stopped
```

The container should restart automatically unless it was deliberately stopped.

### UID and GID

```yaml
environment:
  USER_UID: 1000
  USER_GID: 1000
```

These values tell the Gitea container which UID/GID to use for relevant internal file ownership.

### Persistent volume

```yaml
volumes:
  - /opt/gitea/data:/data
```

This maps:

```text
HOST                           CONTAINER
/opt/gitea/data  ----------->  /data
```

Gitea stores persistent information under `/data`, including repositories, configuration, SSH host keys, logs, and other application state.

Without persistence, deleting and recreating the container could destroy the application state.

### Ports

```yaml
ports:
  - "3000:3000"
  - "2222:22"
```

This means:

```text
Host port 3000  ---> Container port 3000  ---> Gitea Web UI
Host port 2222  ---> Container port 22    ---> Gitea SSH
```

Therefore:

```text
Web UI:
http://192.168.1.16:3000

Git over SSH later:
ssh://git@192.168.1.16:2222/...
```

---

# 2. SELinux `:Z` discussion

The original volume line considered was:

```yaml
- /opt/gitea/data:/data:Z
```

The `:Z` suffix is used on systems with **SELinux**.

It does **not** disable SELinux.

Instead, it asks Docker/Podman to relabel the host directory so that the container is allowed to access it under SELinux policy.

### `:Z` vs `:z`

```text
:Z  -> private/exclusive SELinux relabel
:z  -> shared SELinux relabel
```

For example, on Rocky Linux with SELinux enforcing, `:Z` can be important.

However, Zorin Linux is Ubuntu-based and normally uses **AppArmor**, not SELinux.

Therefore the second-run Zorin setup uses:

```yaml
- /opt/gitea/data:/data
```

with no `:Z` suffix.

### Lesson learned

Container configuration is not always portable line-for-line between distributions.

A setting that is necessary on Rocky Linux may be irrelevant on Zorin/Ubuntu.

---

# 3. First startup attempt

The command used was:

```bash
podman-compose up
```

Podman successfully parsed the Compose file and created the Compose network, but container creation failed with:

```text
Error: statfs /opt/gitea/data: no such file or directory
```

## Root cause

The host-side volume directory did not exist.

The Compose configuration said:

```text
/opt/gitea/data  --->  /data
```

but `/opt/gitea/data` had not been created yet.

## Fix

Create it explicitly:

```bash
sudo mkdir -p /opt/gitea/data
```

Then verify:

```bash
ls -ld /opt/gitea/data
```

### Troubleshooting lesson

The later error:

```text
Error: no container with name or ID "gitea" found
```

was **not the root cause**.

The container had never been successfully created because the volume mount had already failed.

This teaches an important troubleshooting habit:

> Find the first meaningful error. Later errors may only be consequences.

---

# 4. Second startup attempt: permission failure

After creating `/opt/gitea/data`, Gitea started far enough to attempt initialization, but logs showed errors such as:

```text
mkdir: can't create directory '/data/ssh': Permission denied
```

followed by messages such as:

```text
Unable to load host key: /data/ssh/ssh_host_ed25519_key
Unable to load host key: /data/ssh/ssh_host_rsa_key
sshd: no hostkeys available -- exiting
```

## What was happening

Inside the container, Gitea was trying to create:

```text
/data/ssh
```

But `/data` was actually the bind mount:

```text
/opt/gitea/data
```

on the Zorin VM.

The host directory had been created with `sudo`, so its ownership was:

```text
root:root
```

The container was running through **rootless Podman** as user `behroox`.

Therefore the container could see the directory but could not write to it.

---

# 5. Why `sudo podman-compose up` was not the preferred fix

A tempting idea was to rerun the stack with:

```bash
sudo podman-compose up
```

We deliberately avoided this.

Why?

Because rootless Podman and rootful Podman are effectively separate environments.

For example:

```bash
podman ps
```

and:

```bash
sudo podman ps
```

may show different containers.

Using `sudo` would change the execution model from:

```text
behroox
   |
   v
rootless Podman
   |
   v
Gitea
```

into something closer to:

```text
root
 |
 v
rootful Podman
 |
 v
Gitea
```

For this HomeLab, rootless Podman is preferable because it reduces privilege and helps us learn ownership correctly.

---

# 6. `chown` vs `chmod`

A mistake was made here as well.

The proposed command was:

```bash
sudo chown 766 /opt/gitea/data
```

This mixes two different concepts.

## `chown`

`chown` means:

> change owner

Example:

```bash
sudo chown -R behroox:behroox /opt/gitea/data
```

or:

```bash
sudo chown -R $USER:$USER /opt/gitea/data
```

## `chmod`

`chmod` means:

> change permission bits

For example:

```bash
chmod 755 directory
```

or:

```bash
chmod 644 file
```

### Memory trick

```text
chown -> WHO owns it?
chmod -> WHAT can they do?
```

## Why `766` was not appropriate

If used with `chmod`:

```bash
chmod 766 /opt/gitea/data
```

it would mean approximately:

```text
Owner:  rwx
Group:  rw-
Others: rw-
```

That would unnecessarily allow group members and other users to write into the directory.

The real problem was not missing write bits. The problem was **ownership**.

Therefore the correct fix was:

```bash
sudo chown -R $USER:$USER /opt/gitea/data
```

Then verify:

```bash
ls -ld /opt/gitea/data
```

Expected ownership:

```text
behroox behroox
```

---

# 7. Successful startup

After fixing the ownership, the container successfully started.

The service became reachable locally at:

```text
http://localhost:3000
```

This confirmed that:

```text
Compose parsing        OK
Podman networking      OK
Container creation     OK
Volume mount           OK
Host permissions       OK
Gitea startup          OK
HTTP exposure          OK
```

---

# 8. Why `localhost` is not enough

Although `localhost:3000` worked on the Zorin VM itself, `localhost` means:

> this machine

Other components in the HomeLab cannot use that address to reach Gitea.

The VM's LAN address is:

```text
192.168.1.16
```

Therefore the useful service address is:

```text
http://192.168.1.16:3000
```

This matters because later systems such as:

- developer machines
- Gitea Runner
- ArgoCD
- Kubernetes
- Nexus integrations

must reach Gitea over the network.

---

# 9. Preliminary Gitea installation settings

The initial Gitea web installer was configured using the VM's LAN address.

Recommended values:

| Setting | Value |
|---|---|
| Database Type | SQLite3 |
| Site Title | Behroox DevOps HomeLab |
| Repository Root Path | `/data/git/repositories` |
| Git LFS Root Path | `/data/git/lfs` |
| Run As Username | `git` |
| Server Domain | `192.168.1.16` |
| SSH Server Port | `2222` |
| HTTP Listen Port | `3000` |
| Base URL | `http://192.168.1.16:3000/` |
| Log Path | `/data/gitea/log` |

SQLite3 is intentionally used for this HomeLab run because it reduces unrelated complexity while still giving us a fully functional Gitea environment.

---

# 10. Network design lesson

Using the VM's actual LAN address is much better than building the configuration around `localhost`.

The design is now:

```text
Other LAN systems
       |
       v
192.168.1.16:3000
       |
       v
     Gitea
```

Later we may add DNS through Pi-hole:

```text
gitea.lab
    |
    v
192.168.1.16
```

Then users and services can refer to Gitea by name rather than by IP address.

---

# 11. Important reliability consideration

The Gitea IP should remain stable.

If `192.168.1.16` changes later, it may break:

- Git clone URLs
- Gitea Runner registration
- ArgoCD repository references
- API integrations
- SSH clone URLs

Therefore the address should eventually be one of:

- a static IP configured on the VM, or
- a DHCP reservation on the router.

---

# 12. Troubleshooting timeline

The second-run troubleshooting sequence was:

```text
podman-compose up
        |
        v
ERROR:
/opt/gitea/data does not exist
        |
        v
sudo mkdir -p /opt/gitea/data
        |
        v
Retry
        |
        v
ERROR:
/data/ssh: Permission denied
        |
        v
Check host directory ownership
        |
        v
root:root
        |
        v
sudo chown -R $USER:$USER /opt/gitea/data
        |
        v
Retry
        |
        v
Gitea starts successfully
        |
        v
Access localhost:3000
        |
        v
Configure LAN address
        |
        v
http://192.168.1.16:3000
```

This sequence is valuable because it demonstrates layered troubleshooting rather than randomly changing configuration.

---

# 13. What we learned

## Lesson 1 — Bind mounts depend on the host filesystem

A line such as:

```yaml
- /opt/gitea/data:/data
```

is not merely a container setting.

The host-side path must exist and have suitable permissions.

---

## Lesson 2 — Container errors may actually be host errors

The Gitea log complained about:

```text
/data/ssh
```

but the real problem existed at:

```text
/opt/gitea/data
```

because `/data` was a bind mount.

Always mentally translate container paths through their volume mappings.

---

## Lesson 3 — Avoid solving everything with `sudo`

Using `sudo` can hide the original ownership problem and may create a second Podman environment.

Prefer understanding why rootless containers cannot access a path.

---

## Lesson 4 — `chown` and `chmod` solve different problems

```text
chown -> ownership
chmod -> permission bits
```

Do not use broad permissions such as `777` or `766` when the real issue is simply the wrong owner.

---

## Lesson 5 — Follow the first meaningful error

Messages after a failure may simply be cascading consequences.

Example:

```text
statfs /opt/gitea/data: no such file or directory
```

was the real issue.

The later message saying the Gitea container could not be started was merely a consequence.

---

## Lesson 6 — `localhost` is local only

`localhost:3000` proves the web service works from inside the VM.

It does not prove the service is usable by the rest of the HomeLab.

For infrastructure integration, use a LAN-reachable address such as:

```text
192.168.1.16:3000
```

and later a DNS name.

---

## Lesson 7 — Distribution differences matter

Rocky Linux and Zorin Linux do not use the same security model by default.

For example:

```text
Rocky Linux -> SELinux commonly enabled
Zorin Linux -> AppArmor commonly used
```

Therefore SELinux-specific mount flags such as `:Z` should not be copied blindly between systems.

---

# 14. Useful verification commands

## Check container status

```bash
podman ps
```

## Show all containers

```bash
podman ps -a
```

## Follow Gitea logs

```bash
podman logs -f gitea
```

## Check the persistent directory

```bash
ls -ld /opt/gitea/data
```

## Check the VM IP

```bash
ip -br addr
```

or:

```bash
hostname -I
```

## Test Gitea locally

```bash
curl http://localhost:3000
```

## Test using the LAN address

```bash
curl http://192.168.1.16:3000
```

## Stop the Compose stack

```bash
podman-compose down
```

## Start detached

```bash
podman-compose up -d
```

---

# 15. Quick cheat sheet

```bash
# Create persistent Gitea directory
sudo mkdir -p /opt/gitea/data

# Give rootless Podman user ownership
sudo chown -R $USER:$USER /opt/gitea/data

# Verify
ls -ld /opt/gitea/data

# Start Gitea
podman-compose up -d

# Check container
podman ps

# Follow logs
podman logs -f gitea

# Test locally
curl http://localhost:3000

# Test via LAN
curl http://192.168.1.16:3000
```

---

# 16. Current status

At the end of this stage:

```text
Zorin VM:       192.168.1.16
Podman:         Working
Gitea container: Running
Persistent data: /opt/gitea/data
Web UI:         http://192.168.1.16:3000
SSH mapping:    Host 2222 -> Container 22
Database:       SQLite3
```

The next logical steps are:

1. verify access to Gitea from another LAN machine;
2. create the first repository;
3. test Git clone/push;
4. configure the Gitea Runner;
5. create a first simple CI workflow;
6. later integrate Nexus.

---

# 17. The key mindset from Run #2

The most important difference from the first HomeLab run is that the installation is being treated as a system rather than a recipe.

Instead of asking only:

```text
What command comes next?
```

we are asking:

```text
What failed?
Where did it fail?
Which layer owns the problem?
What evidence supports the hypothesis?
What is the smallest correct fix?
How do we verify it afterward?
```

That is the troubleshooting mindset we want to carry through the rest of the CI/CD rebuild.
