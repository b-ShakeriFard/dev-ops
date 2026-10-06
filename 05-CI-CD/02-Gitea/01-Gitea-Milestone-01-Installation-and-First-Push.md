# Gitea Installation & First Repository — DevOps HomeLab Run #2

> **Milestone:** Gitea installed on a dedicated Zorin Linux VM, given a static IP, validated over the LAN, and used successfully for the first `git push`.

---

## 1. Goal of This Milestone

The purpose of this second HomeLab run is not just to reproduce a working CI/CD pipeline.

This time, the goal is to understand **why each component exists, how it communicates, and how to troubleshoot each layer independently**.

For this milestone, we focused only on the **SCM layer**:

```text
Developer Workstation
        |
        | git push
        v
+----------------------+
|      Gitea VM        |
|   192.168.1.16       |
|                      |
|   Gitea :3000        |
|   SSH   :2222        |
+----------+-----------+
           |
           v
   Git repositories
   persisted on disk
```

At the end of this milestone:

- the VM has a stable IP address
- Gitea runs successfully in Podman
- Gitea data survives container recreation
- the web UI is available over the LAN
- a repository named `pipeline-lab` exists
- the first Git push succeeded

This gives us a clean foundation before introducing the Gitea Runner.

---

# 2. Environment

## Host / VM Layout

The Gitea service is running inside a dedicated Zorin Linux VM.

```text
Physical HomeLab Host
        |
        +-- Zorin Linux VM
             |
             +-- IP: 192.168.1.16
             |
             +-- Podman
                  |
                  +-- Gitea container
```

### Important Network Identity

| Item | Value |
|---|---|
| VM OS | Zorin Linux |
| VM IPv4 | `192.168.1.16` |
| Subnet | `/24` |
| Gitea Web Port | `3000` |
| Gitea SSH Host Port | `2222` |
| Container SSH Port | `22` |

The interface was identified with:

```bash
ip -br addr
```

Example output:

```text
lo       UNKNOWN   127.0.0.1/8 ::1/128
enp0s3   UP        192.168.1.16/24 fe80::d72:a42f:ec75:9fe0/64
```

### IPv6 vs MAC Address

The address:

```text
fe80::d72:a42f:ec75:9fe0/64
```

is an **IPv6 link-local address**.

It is not the MAC address.

To see the MAC address:

```bash
ip -br link
```

or:

```bash
ip link show enp0s3
```

A MAC address looks more like:

```text
08:00:27:aa:bb:cc
```

A stable MAC address is useful when configuring a DHCP reservation on the router.

---

# 3. Static IP

Because Gitea will later be referenced by developers, the Gitea Runner, ArgoCD, scripts, and Git remote URLs, the VM must keep a stable address.

For this lab:

```text
Gitea VM = 192.168.1.16
```

Why this matters:

```text
WITHOUT STATIC IP

Today:
Gitea -> 192.168.1.16

Tomorrow after DHCP lease change:
Gitea -> 192.168.1.27

Result:
git remote URLs break
runner registration breaks
ArgoCD repo references may break


WITH STATIC IP

Gitea -> 192.168.1.16

Result:
stable infrastructure
```

---

# 4. Podman Compose File

The service was defined with this Compose configuration:

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

---

# 5. Compose File Explained

## Image

```yaml
image: docker.gitea.com/gitea:1.27.0
```

This tells Podman to use Gitea version `1.27.0`.

Using an explicit version is preferable to a floating tag such as `latest` because the lab remains reproducible.

## Container Name

```yaml
container_name: gitea
```

This gives the container a predictable name.

Useful commands therefore become simple:

```bash
podman ps
podman logs gitea
podman restart gitea
podman stop gitea
```

## Restart Policy

```yaml
restart: unless-stopped
```

This means Gitea should restart automatically after failures or host reboots unless it was intentionally stopped.

## UID / GID

```yaml
environment:
  USER_UID: 1000
  USER_GID: 1000
```

These values help align Gitea's expected file ownership with the Linux user identity used by the VM.

---

# 6. Persistent Storage

The container uses:

```yaml
volumes:
  - /opt/gitea/data:/data
```

The mapping is:

```text
Zorin VM                         Gitea Container

/opt/gitea/data    ----------->  /data

persistent host storage          Gitea data directory
```

This is extremely important.

Without persistent storage, deleting the container could delete repositories, configuration, SSH keys, users, application data, and logs.

With the bind mount, the container becomes replaceable while the data remains on the VM.

---

# 7. SELinux `:Z` — Why We Removed It

In a Rocky Linux environment, a bind mount may appear as:

```yaml
- /opt/gitea/data:/data:Z
```

The `:Z` suffix is related to **SELinux relabeling**.

It tells the container engine to relabel the mounted directory for private container access.

```text
host path
   |
   v
/opt/gitea/data
   |
   +-- normal Linux ownership
   |
   +-- SELinux label
          |
          v
   allow container access
```

Zorin Linux is Ubuntu-based and normally uses **AppArmor**, not SELinux.

Therefore our second-run compose file uses:

```yaml
- /opt/gitea/data:/data
```

without `:Z`.

---

# 8. First Failure — Missing Host Directory

The first attempt was:

```bash
podman-compose up
```

Podman failed with:

```text
Error: statfs /opt/gitea/data: no such file or directory
```

The compose file expected `/opt/gitea/data`, but it did not exist.

## Fix

```bash
sudo mkdir -p /opt/gitea/data
```

The `-p` option creates missing parent directories if necessary.

---

# 9. Second Failure — Permission Denied

After creating the directory, Gitea failed again.

The important error was:

```text
mkdir: can't create directory '/data/ssh': Permission denied
```

At first glance, `/data/ssh` looks like a container path.

But remember:

```text
/data inside container
        |
        v
/opt/gitea/data on host
```

The host directory had been created by `sudo`, therefore ownership was:

```text
root:root
```

Verification:

```bash
ls -ld /opt/gitea/data
```

The problem was therefore not Podman networking or Gitea itself.

It was a **host filesystem ownership issue**.

---

# 10. `chown` vs `chmod`

This became an important Linux lesson.

The incorrect instinct was something like:

```bash
sudo chown 766 /opt/gitea/data
```

This mixes two different commands.

## `chown`

`chown` answers:

> Who owns this file or directory?

Example:

```bash
sudo chown -R behroox:behroox /opt/gitea/data
```

or:

```bash
sudo chown -R $USER:$USER /opt/gitea/data
```

## `chmod`

`chmod` answers:

> What is the owner/group/others allowed to do?

Example:

```bash
chmod 755 directory
```

But:

```bash
chmod 766 /opt/gitea/data
```

would make the directory writable by group and others.

That is unnecessary and too permissive.

### Memory Trick

```text
chown = WHO owns it?
chmod = WHAT may they do?
```

---

# 11. Why We Did NOT Use `sudo podman-compose up`

Another tempting workaround would have been:

```bash
sudo podman-compose up
```

We intentionally avoided that.

Rootless and rootful Podman environments are distinct:

```text
behroox
   |
   v
rootless Podman
   |
   +-- containers owned by user


root
   |
   v
rootful Podman
   |
   +-- completely separate container environment
```

This means:

```bash
podman ps
```

and:

```bash
sudo podman ps
```

can show different containers.

For this lab, using rootless Podman is cleaner and safer.

The correct solution was to fix host directory ownership.

---

# 12. Correct Ownership Fix

We changed ownership with:

```bash
sudo chown -R $USER:$USER /opt/gitea/data
```

Then verified:

```bash
ls -ld /opt/gitea/data
```

Expected ownership:

```text
behroox behroox
```

After that:

```bash
podman-compose up
```

successfully launched the container.

---

# 13. Gitea Web Access

After startup, Gitea became available locally at:

```text
http://localhost:3000
```

However, for infrastructure usage, `localhost` is not sufficient.

`localhost` always means:

```text
this machine
```

So another machine cannot use `http://localhost:3000` to reach this Gitea VM.

The correct LAN address is:

```text
http://192.168.1.16:3000
```

```text
Rocky Desktop
     |
     | HTTP :3000
     v
192.168.1.16
     |
     v
Gitea container
```

---

# 14. Gitea Initial Installation Settings

During the Gitea installation wizard, the following values were selected.

## Database

```text
Database Type: SQLite3
```

Why SQLite?

- lightweight
- simple
- no separate DB server
- fewer moving parts
- sufficient for learning and small-scale usage

This keeps the focus on CI/CD rather than database administration.

## Repository Storage

Typical Gitea defaults were retained under `/data`.

Because `/data` is bind-mounted to the host, repository data persists under:

```text
/opt/gitea/data
```

## Run As Username

```text
git
```

This is the conventional internal service account used by Gitea.

## Server Domain

```text
192.168.1.16
```

This matters because clone URLs and other Gitea-generated links depend on how Gitea identifies itself.

## SSH Port

The container listens internally on:

```text
22
```

but the VM exposes:

```text
2222
```

because the compose file contains:

```yaml
- "2222:22"
```

Therefore:

```text
HOST PORT 2222
       |
       v
CONTAINER PORT 22
```

This avoids conflicting with the VM's own SSH daemon, which normally already uses host port `22`.

## HTTP Port

Gitea listens on:

```text
3000
```

and we map:

```yaml
- "3000:3000"
```

So the web UI is reachable at:

```text
http://192.168.1.16:3000
```

## Gitea Base URL

The correct Base URL for this stage of the lab is:

```text
http://192.168.1.16:3000/
```

This is much better than `http://localhost:3000/` because other machines can use the actual VM address.

---

# 15. Installation Settings Summary

| Setting | Value |
|---|---|
| Database Type | `SQLite3` |
| Run As Username | `git` |
| Server Domain | `192.168.1.16` |
| Gitea HTTP Port | `3000` |
| SSH Host Port | `2222` |
| SSH Container Port | `22` |
| Base URL | `http://192.168.1.16:3000/` |
| Host Data Directory | `/opt/gitea/data` |
| Container Data Directory | `/data` |

---

# 16. First Repository

A repository was created in Gitea:

```text
pipeline-lab
```

This is the repository we will use for our CI/CD learning path.

---

# 17. Local Repository Creation

On the client machine:

```bash
mkdir pipeline-lab
cd pipeline-lab
git init
```

Then a simple README can be created:

```bash
echo "# Pipeline Lab - CI/CD Run 2" > README.md
```

Stage and commit:

```bash
git add README.md
git commit -m "Initial commit"
```

---

# 18. Connecting Local Git to Gitea

The remote repository was added using the Gitea server IP.

Conceptually:

```bash
git remote add origin http://192.168.1.16:3000/<username>/pipeline-lab.git
```

Verification:

```bash
git remote -v
```

Expected pattern:

```text
origin  http://192.168.1.16:3000/.../pipeline-lab.git (fetch)
origin  http://192.168.1.16:3000/.../pipeline-lab.git (push)
```

---

# 19. First Successful Push

The first push succeeded.

Example:

```bash
git push -u origin main
```

or:

```bash
git push -u origin master
```

This proves the full SCM path works:

```text
Developer Machine
       |
       | git push
       v
192.168.1.16:3000
       |
       v
Gitea
       |
       v
Repository Storage
       |
       v
/opt/gitea/data
```

---

# 20. What This Successful Push Proves

The push is more important than simply loading the web UI.

It proves several layers at once:

```text
+-------------------------------+
| LAN connectivity             |  OK
+-------------------------------+
| Port mapping                 |  OK
+-------------------------------+
| Gitea HTTP service           |  OK
+-------------------------------+
| Git authentication path      |  OK
+-------------------------------+
| Repository creation          |  OK
+-------------------------------+
| Persistent repository write  |  OK
+-------------------------------+
```

---

# 21. Troubleshooting Timeline

```text
podman-compose up
        |
        v
ERROR:
host directory missing
        |
        v
sudo mkdir -p /opt/gitea/data
        |
        v
ERROR:
permission denied
        |
        v
inspect ownership
        |
        v
root:root
        |
        v
sudo chown -R $USER:$USER /opt/gitea/data
        |
        v
podman-compose up
        |
        v
SUCCESS
```

---

# 22. Lessons Learned

## Lesson 1 — Follow the first meaningful error

The important error was:

```text
statfs /opt/gitea/data: no such file or directory
```

Later errors were consequences.

Always search for the **first root-cause failure**.

## Lesson 2 — Understand bind mounts

```text
host path       container path

/opt/gitea/data  ->  /data
```

If the container gets a filesystem permission error, the root cause may be on the host.

## Lesson 3 — Rootless Podman matters

Avoid using `sudo` as a reflex.

Rootless Podman gives us better isolation, less privilege, clearer ownership, and a more security-conscious setup.

## Lesson 4 — Ownership and permissions are different

```text
chown -> ownership
chmod -> permissions
```

Do not use `chmod 777` or similarly permissive fixes simply to make an error disappear.

## Lesson 5 — `localhost` is contextual

`localhost` on the Gitea VM means the Gitea VM itself.

It is not usable as the canonical service address for the rest of the HomeLab.

Use:

```text
192.168.1.16
```

until DNS is introduced.

## Lesson 6 — Infrastructure needs stable addressing

A CI/CD system cannot rely on random DHCP changes.

Eventually we may evolve from:

```text
192.168.1.16
```

to something like:

```text
gitea.home.arpa
```

using local DNS.

---

# 23. Current Architecture

```text
                    HOME LAB
                       |
                       |
              +--------+--------+
              |                 |
              |                 |
        Developer PC       Zorin Linux VM
                              |
                        192.168.1.16
                              |
                    +---------+---------+
                    |                   |
                    |      Podman       |
                    |                   |
                    |   +-----------+   |
                    |   |   Gitea   |   |
                    |   |           |   |
                    |   | Web :3000 |   |
                    |   | SSH :22   |   |
                    |   +-----+-----+   |
                    |         |         |
                    +---------|---------+
                              |
                              v
                     /opt/gitea/data
```

---

# 24. Where We Are in the Full CI/CD Journey

```text
[ DONE ] VM
[ DONE ] Static IP
[ DONE ] Podman
[ DONE ] Gitea
[ DONE ] Persistent storage
[ DONE ] First repository
[ DONE ] First push
         |
         v
[ NEXT ] Gitea Runner
         |
         v
[ NEXT ] First CI workflow
         |
         v
[ NEXT ] Nexus
         |
         v
[ NEXT ] Docker image build
         |
         v
[ NEXT ] Push image to Nexus
         |
         v
[ NEXT ] GitOps repository
         |
         v
[ NEXT ] ArgoCD
         |
         v
[ NEXT ] Kubernetes deployment
```

At this point, roughly **one quarter of the second-run CI/CD rebuild** is complete.

---

# 25. Useful Commands

Check container:

```bash
podman ps
```

Check logs:

```bash
podman logs gitea --tail 50
```

Check host data directory:

```bash
ls -ld /opt/gitea/data
```

Check VM IP:

```bash
ip -br addr
```

Check MAC address:

```bash
ip -br link
```

Verify HTTP access:

```bash
curl -I http://192.168.1.16:3000
```

Check Git remote:

```bash
git remote -v
```

---

# 26. Final Milestone Checklist

- [x] Zorin VM created
- [x] VM assigned static IP `192.168.1.16`
- [x] Podman installed and working
- [x] Compose file created
- [x] Persistent directory created
- [x] Ownership fixed
- [x] Gitea container started successfully
- [x] Gitea web UI reachable
- [x] SQLite3 selected
- [x] HTTP port configured as `3000`
- [x] SSH port mapped as `2222 -> 22`
- [x] Base URL set to `http://192.168.1.16:3000/`
- [x] `pipeline-lab` repository created
- [x] Local repository initialized
- [x] Gitea remote configured
- [x] First push successful

---

# 27. Next Milestone

The next step is:

```text
Gitea
  |
  v
Gitea Runner
  |
  v
CI Workflow
```

The key conceptual question for the next milestone is:

> Gitea now stores our code — but what component actually executes the CI jobs?

That answer is the **Gitea Actions Runner**.

And that is where Run #2 continues.
