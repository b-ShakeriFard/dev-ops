# Gitea and Gitea Runner on Rocky Linux: homelab guide

**Lab snapshot:** 26 September 2026  
**Host:** Rocky Linux desktop, `192.168.1.23`  
**Gitea:** `docker.gitea.com/gitea:1.27.0`, reachable at `http://localhost:3000` on the desktop and `http://192.168.1.23:3000` from the LAN/containers  
**Runner:** Docker container `gitea-runner`, `docker.io/gitea/runner:2`, runner version 2.3.0, named `rocky-runner`  
**Repository:** `behroox/pipeline-lab`

> **Record versus recipe.** The screenshots establish the host, versions, paths, commands listed under “Commands we observed,” and the green result. Gitea itself was already deployed when this exercise began. Its original `docker run`/Compose invocation and the runner container's complete original flags were not captured. The “Fresh setup” commands below are a reusable example, not a verbatim transcript of those earlier invocations. Check existing volumes before recreating containers.

## What we achieved

1. Confirmed the local Gitea login and created the `pipeline-lab` repository under `behroox`.
2. Enabled repository Actions and registered a repository-scoped runner, `rocky-runner`; the Gitea UI showed it online (green).
3. Placed `.gitea/workflows/hello.yaml` **inside the cloned Git repository**, committed it, and pushed to local Gitea.
4. Diagnosed the initially yellow job: it was pulling the `ubuntu-latest` runner image.
5. Waited for the image pull to finish. The Actions run became **green** and printed **“Hello from Gitea runner!”**

The result proves that a local Git push triggered a workflow, that Gitea assigned the job to the runner, and that the runner executed its shell step.

## Quick reference

| Item | Value or command |
| --- | --- |
| Rocky desktop | `192.168.1.23` |
| Gitea browser URL on desktop | `http://localhost:3000` |
| Gitea URL for runner | `http://192.168.1.23:3000` |
| Repository | `behroox/pipeline-lab` |
| Local clone | `/home/behroox/Behrouz_ghastly_projects/pipeline-lab` |
| Workflow location | `.gitea/workflows/hello.yaml` relative to repository root |
| Runner container/name | `gitea-runner` / `rocky-runner` |
| View Git destination | `git remote -v` |
| View runner logs | `docker logs --since 15m --tail 80 gitea-runner` |

## Prerequisites and verification

On the Rocky desktop, Docker must be running and the Gitea container reachable. These are **reference checks**:

```bash
docker version
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}'
curl -I http://127.0.0.1:3000/
```

`localhost` on the desktop refers to the desktop; `localhost` **inside the runner container** refers to that container. We therefore supplied the desktop's LAN IP to the runner. If a future host or IP changes, update the runner instance address and Gitea's advertised URLs as needed.

### Fresh Gitea setup example (only for a new installation)

We did not record the original Gitea container creation command. The following is an **illustrative** rootful Docker Compose deployment; it is not a command to run over an existing Gitea data directory. Choose UID/GID using `id -u` and `id -g`, and ensure the data directory is writable by them. Pin the version you intend to run. For our snapshot, that was `1.27.0`.

```yaml
# compose.yaml — illustrative fresh install
services:
  gitea:
    image: docker.gitea.com/gitea:1.27.0
    container_name: gitea
    restart: unless-stopped
    environment:
      USER_UID: "1000"
      USER_GID: "1000"
      GITEA__server__ROOT_URL: "http://192.168.1.23:3000/"
    ports:
      - "3000:3000"
      - "2222:22"
    volumes:
      - ./data:/data:Z
```

```bash
# Fresh installation only, in a dedicated directory:
mkdir -p "$HOME/gitea/data"
cd "$HOME/gitea"
# Save the compose.yaml above, confirm UID/GID and storage permissions, then:
docker compose up -d
docker logs --tail 80 gitea
```

For an existing installation, inspect its mounts first instead of replacing the container:

```bash
docker inspect gitea --format '{{json .Mounts}}'
docker inspect gitea --format '{{json .NetworkSettings.Ports}}'
```

Gitea's browser installer/configuration must be completed for a new instance. Persist the `/data` volume, and back it up before changing or upgrading the deployment.

## Create the repository and enable Actions

In Gitea, log in as `behroox`, create the repository **`pipeline-lab`**, then open:

**Repository → Settings → Actions → General → Enable Repository Actions → Update Settings.**

The Actions section appearing in Settings confirms that Actions can be configured, but the main **Actions** tab may not show on an empty repository until a workflow has been pushed. After our push, the tab appeared and showed the run.

In **Repository → Settings → Actions → Runners**, obtain a **repository registration token**. Treat the token as a secret; do not put it in a workflow or Git commit. Runner scope matters: a repository-level runner serves this repository. Gitea also supports broader scopes, but we used repository scope here.

## Runner on the Rocky Docker host

Our runner was named `gitea-runner`, registered as `rocky-runner`, retained runner state under `$HOME/gitea-runner/data`, and had access to the host Docker socket so it could start job containers. The registration token was kept in a separate file under `$HOME/gitea-runner/token`. A file-backed registration-token variable is supported by the runner image.

**Reproducible example for a fresh runner** (the exact original `docker run` flags were not captured):

```bash
mkdir -p "$HOME/gitea-runner/data"
umask 077
# Paste the repository-scoped token in the prompted editor; do not print it.
${EDITOR:-vi} "$HOME/gitea-runner/token"
chmod 600 "$HOME/gitea-runner/token"
umask 022

docker run -d \
  --name gitea-runner \
  --restart unless-stopped \
  -e GITEA_INSTANCE_URL=http://192.168.1.23:3000 \
  -e GITEA_RUNNER_REGISTRATION_TOKEN_FILE=/run/secrets/gitea-runner-token \
  -e GITEA_RUNNER_NAME=rocky-runner \
  -v "$HOME/gitea-runner/data:/data:Z" \
  -v "$HOME/gitea-runner/token:/run/secrets/gitea-runner-token:ro,Z" \
  -v /var/run/docker.sock:/var/run/docker.sock \
  docker.io/gitea/runner:2
```

If your host's SELinux policy or mount labeling differs, inspect `docker logs gitea-runner` and the mounted file permissions; do not relabel `/var/run/docker.sock` casually. The `/data` mount holds runner registration state, so keep it when restarting/recreating the container. The host Docker socket gives jobs broad control of the host Docker daemon; use this runner for trusted repositories and workflows. A narrower Docker-in-Docker setup is an option for a future hardening pass.

Check its status and registered labels:

```bash
docker ps --filter name=gitea-runner
docker logs --tail 100 gitea-runner
```

The Gitea Runners page showed `rocky-runner` **Online**, version **v2.3.0**, repository scope. Its `ubuntu-latest` label matched `runs-on: ubuntu-latest` in the workflow.

> `umask 077` makes newly created files private by default (typically mode `600`) and directories mode `700`. We used it while handling the token and restored the usual `022` afterward. `chmod 600` further enforces private permissions on the token file.

## Clone the right repository

An early attempt placed `hello.yaml` under `$HOME/gitea-runner/.gitea/workflows`. That directory is **runner configuration**, not the Git repository; `git status` there correctly reported “not a git repository.” We switched to the `pipeline-lab` clone.

From the Rocky desktop:

```bash
mkdir -p "$HOME/Behrouz_ghastly_projects"
cd "$HOME/Behrouz_ghastly_projects"
git clone http://localhost:3000/behroox/pipeline-lab.git
cd pipeline-lab
git status
git remote -v
```

If the repository is already cloned, simply `cd` into its root. `git remote -v` is the decisive check for where `git push` goes. The Gitea page displayed `http://localhost:3000/behroox/pipeline-lab.git`, so a push to that `origin` goes to **local Gitea**, not GitHub.com. A URL such as `https://github.com/...` elsewhere in runner logs can describe the runner's default **action source**, not the Git push target. Verify the actual remote every time it matters.

## Add the first workflow

From the **repository root**:

```bash
mkdir -p .gitea/workflows
${EDITOR:-vi} .gitea/workflows/hello.yaml
```

Use this workflow, matching the test we performed:

```yaml
name: First CI job

on: [push]

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Say hello
        run: |
          echo "Hello from our Gitea runner!"
          uname -a
```

The actual successful run printed the hello message. The exact capitalization of the saved echo text can vary with the file you wrote; confirm it with `cat .gitea/workflows/hello.yaml` if you need an exact transcript. The file must live under `.gitea/workflows/` and be committed in the repository. No checkout action is needed for this simple workflow because it does not read source files.

Before committing, check the directory and remote:

```bash
pwd
git status
git remote -v
cat .gitea/workflows/hello.yaml
```

Then commit and push:

```bash
git add .gitea/workflows/hello.yaml
git commit -m "add first Gitea Action workflow"
git push -u origin main
```

Our screenshot showed the workflow commit `f509bc4` as the first/root commit. Git printed a reminder that the author name and email had been inferred from the username and `localhost.localdomain`. For future commits, set your intended identity (replace the sample email with your own):

```bash
git config --global user.name "Behrouz ShakeriFard"
git config --global user.email "your-email@example.com"
```

If you need to correct the already-created commit's author, review the pending change first, then use `git commit --amend --reset-author` and push according to whether the old commit has already been shared. There was no need to amend the commit to make this CI job run.

## Watch the first run and understand the yellow delay

Open **`behroox/pipeline-lab` → Actions → First CI job → the run → `hello` → Set up job**. At first, the yellow run stayed in setup for around 13 minutes. Expanding the step showed it was pulling the job image:

```text
docker.gitea.com/runner-images:ubuntu-latest
```

The runner logs showed that it had been assigned the repository job and had created one worker:

```bash
docker logs --since 15m --tail 80 gitea-runner
docker logs --tail 100 gitea-runner
docker ps -a --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
```

Those messages alone do not show a stuck runner. The Actions **Set up job** detail identified the image pull as the wait. A manual pull can expose progress or a specific network error and pre-cache the image:

```bash
docker pull docker.gitea.com/runner-images:ubuntu-latest
docker image ls docker.gitea.com/runner-images
```

Because this runner uses the **host Docker socket**, the host daemon's image cache is available to jobs. If a runner instead has its own Docker-in-Docker daemon, pulling on the host would not fill that separate cache. In this lab the image pull eventually completed, the `Say hello` step ran, and the workflow turned green. The `ubuntu-latest` runner label is a label mapping to the job image; it does not mean the Rocky host OS changed to Ubuntu.

## Commands we observed during the exercise

These commands or command forms appeared in the terminal screenshots/logs; paths are shown in their final, correct context:

```bash
# Initial mistaken location, kept as a diagnostic lesson:
pwd                     # /home/behroox/gitea-runner/.gitea/workflows
git status              # fatal: not a git repository
git remote -v           # same error there

# Correct repository workflow creation and inspection:
mkdir -p .gitea
cd .gitea
mkdir -p workflows
cd workflows
touch hello.yaml
sudo vim hello.yaml     # observed; normal user editor is preferable for repo files
pwd                     # .../Behrouz_ghastly_projects/pipeline-lab/.gitea/workflows
cat hello.yaml
git add hello.yaml
git commit -m "add first Gitea Action workflow"

# Runner/job diagnosis:
docker logs --since 15m --tail 80 gitea-runner
docker ps -a --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
docker logs --tail 100 gitea-runner
```

Using `sudo vim` can leave repository files owned by root. If that happened, inspect ownership with `ls -l .gitea/workflows/hello.yaml` and fix only the affected file with `sudo chown "$(id -u):$(id -g)" .gitea/workflows/hello.yaml`.

## Troubleshooting checklist

| Symptom | Check | Likely action |
| --- | --- | --- |
| No Actions tab | Is Actions enabled in repository Settings? Is a workflow committed? | Enable Actions, push `.gitea/workflows/hello.yaml`, refresh. |
| Runner offline | `docker ps`, `docker logs --tail 100 gitea-runner`; can it reach `192.168.1.23:3000`? | Correct URL/token, check container and host networking. |
| Job queued with no runner | Compare job `runs-on` with registered runner labels. | Use a matching label, such as `ubuntu-latest`. |
| Job yellow at Set up job | Expand the setup log; check image name/pull progress. | Wait for first pull or try the exact `docker pull` on the daemon the runner uses. |
| `fatal: not a git repository` | `pwd`, `git rev-parse --show-toplevel` | Change to the `pipeline-lab` clone. |
| Concern about GitHub push | `git remote -v` | Verify `origin` points to `localhost:3000/behroox/pipeline-lab.git`. |
| Runner token exposed | Check files, shell history and logs; never paste token in a ticket | Rotate token in Gitea, replace the protected token file, restart the runner if needed. |

## Final verification and next milestone

What we verified in Gitea: runner online; workflow detected after local push; `hello` job green; hello text in job output. This is a working **CI execution path**. It has not yet built, tested, or published an application image. The next milestone is to check the existing Nexus container and whether port `5043` serves a Docker registry, then build a small app and push its image from a Gitea workflow using a repository secret for registry credentials.

On Rocky, begin that inventory with:

```bash
docker ps --filter name=nexus --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
curl -i --max-time 5 http://127.0.0.1:5043/v2/
```

Do not assume `5043` is a registry until its Nexus repository connector and `/v2/` response confirm it.

## Official references

- [Gitea installation with Docker](https://docs.gitea.com/installation/install-with-docker/)
- [Gitea Actions quick start](https://docs.gitea.com/usage/actions/quickstart/)
- [Gitea Runner 2 Docker installation](https://docs.gitea.com/runner/2/installation/docker/)
- [Gitea Runner 2 registration](https://docs.gitea.com/runner/2/registration/)
- [Gitea Actions secrets](https://docs.gitea.com/usage/actions/secrets/)
