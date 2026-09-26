# Nexus Docker registry on Rocky Linux: setup and troubleshooting

**Lab record:** 26 September 2026  
**Host:** Rocky Linux desktop  
**Nexus:** Community 3.88.0-08, container `nexus`  
**Nexus UI:** `http://localhost:8081`  
**Docker registry:** `192.168.1.23:5043`, hosted repository `Behr00z-repo`  
**Outcome:** Docker login succeeded and `alpine:latest` was tagged and pushed to Nexus.

> This documents the commands and observations from our session. Some Nexus UI actions have no shell equivalent in the transcript. The dedicated `gitea-ci` account and Gitea Actions integration are **planned next steps**, not completed steps in this record.

## What each piece does

| Component | Address | Purpose |
| --- | --- | --- |
| Nexus UI | `http://localhost:8081` | Create repositories, realms, users and roles; browse images |
| Docker hosted repository connector | `192.168.1.23:5043` | Docker Registry API for login, push and pull |
| Gitea | `http://192.168.1.23:3000` | Source repository and CI workflows (configured earlier) |

Port **5043** has no built-in meaning. We selected it as the HTTP connector port for the Nexus Docker repository. The same port is published by the `nexus` container. A different Docker repository needs its own connector port, unless routing is arranged another way.

## Quick command sheet

```bash
# Container and port inventory
docker ps --filter name=nexus --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'

# Query the local registry without using the workstation's HTTP proxy
curl --noproxy '*' -i --max-time 5 http://127.0.0.1:5043/v2/

# Check the Docker daemon's current insecure registries
docker info | grep -A8 'Insecure Registries'

# Authenticate to the repository connector, with no URL scheme or path
docker login -u admin 192.168.1.23:5043

# Manual image smoke test
docker tag alpine:latest 192.168.1.23:5043/lab/alpine:smoke-test
docker push 192.168.1.23:5043/lab/alpine:smoke-test
```

## 1. Verify the container and ports

On Rocky, we ran:

```bash
docker ps --filter name=nexus --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
```

The container was running and showed both `8081:8081` for the UI and `5043:5043` for the registry connector. A published port alone does not show which repository listens there, so we tested the Registry API:

```bash
curl --noproxy '*' -i --max-time 5 http://127.0.0.1:5043/v2/
```

The response was `HTTP/1.1 401 Unauthorized` with `Docker-Distribution-Api-Version: registry/2.0`. That was useful: the API was reachable and was asking for authentication. It was **not** a successful login.

### Why the first curl failed on port 2081

The unmodified `curl` command targeting `5043` failed with `Failed to connect to 127.0.0.1 port 2081`. A proxy configured in the shell intercepted the request. `--noproxy '*'` forced this test to connect directly to local Nexus. This did not mean the Nexus connector had moved to `2081`.

## 2. Create a Docker hosted repository

In the Nexus UI, **Administration → Repository → Repositories**, we removed the prior repository using connector `5043` and created a new **Docker (hosted)** repository named **`Behr00z-repo`**, with an **HTTP connector on port `5043`**.

Repository types have different jobs:

| Type | Intended use |
| --- | --- |
| Docker hosted | Store images we build and push ourselves |
| Docker proxy | Cache images from an upstream registry |
| Docker group | Present hosted/proxy repositories as one pull endpoint |

For our Gitea CI image publishing goal, **hosted** is the appropriate destination. Do not assign the same connector port to two repositories. Verify that the port is published by the Nexus container as well as configured inside Nexus.

## 3. Activate the correct authentication realms

In **Administration → Security → Realms**, the active list needs **Local Authenticating Realm** for the built-in Nexus users and **Docker Bearer Token Realm** for Docker client authentication. Move the Docker realm into the active list and **Save**. Keep the local realm active.

Our first attempt accidentally activated **Conan Bearer Token Realm** instead of **Docker Bearer Token Realm**. This was the actual cause of the stubborn Docker login failure. After correcting the realm, Docker accepted the credentials.

Before the correction, a direct check had at one point returned a Bearer challenge, and a direct token request returned HTTP 200. Those observations alone did not prove the Docker login would complete. The Docker client still got a 401 after presenting the issued token. Checking the **actual active realm names** resolved the problem.

Safe challenge-header check (does not send credentials):

```bash
curl --noproxy '*' -sS -D - -o /dev/null http://192.168.1.23:5043/v2/ |
  grep -Ei '^(HTTP/|WWW-Authenticate:|Docker-Distribution-Api-Version:)'
```

For our registry, the challenge advertised a `Bearer` token endpoint at `/v2/token`. The realm list in the Nexus UI remained the authoritative check for which realm was active.

## 4. Allow this HTTP registry in Docker Engine

Our first `docker login` tried `https://192.168.1.23:5043/v2/` and failed with **“http: server gave HTTP response to HTTPS client.”** Docker normally expects TLS for a nonlocal registry. For this lab's HTTP connector, we added the specific host and port to `/etc/docker/daemon.json`.

Back up the current config before editing:

```bash
sudo mkdir -p /etc/docker
sudo cp -a /etc/docker/daemon.json /etc/docker/daemon.json.bak 2>/dev/null || true
sudoedit /etc/docker/daemon.json
```

For a previously empty file, the minimal config is:

```json
{
  "insecure-registries": ["192.168.1.23:5043"]
}
```

If the file already contains settings, **merge** `insecure-registries` into the existing JSON object. The actual workstation config later showed other registry addresses too; do not replace or delete unrelated entries. The screenshot showed entries for `192.168.43.176:5043`, `192.168.252.95:5043`, `192.168.1.23:5043`, and `192.168.1.11:5043`. Those are specific to this host and may no longer all be needed.

Validate before applying:

```bash
sudo dockerd --validate --config-file /etc/docker/daemon.json
```

It returned `configuration OK`. Then the Docker daemon configuration was reloaded using `HUP` (without a container restart):

```bash
sudo systemctl kill -s HUP --kill-who=main docker.service
docker info | grep -A8 'Insecure Registries'
```

**Scope:** This exception allows unencrypted HTTP to those registries. Passwords and image traffic can be observed on the network. It was used for a home lab; replace it with a trusted HTTPS endpoint when the lab matures.

## 5. Diagnose the misleading 401

Once Docker could speak HTTP, login still failed with `401 Unauthorized`, despite the admin password working in the Nexus web UI. We tried the documented hostname and port form:

```bash
docker login -u admin 192.168.1.23:5043
```

We also tried without shell proxy variables and with `--password-stdin`. Both still gave 401:

```bash
env -u HTTP_PROXY -u HTTPS_PROXY -u ALL_PROXY \
    -u http_proxy -u https_proxy -u all_proxy \
    docker login -u admin 192.168.1.23:5043
```

```bash
read -r -s -p 'Nexus password: ' NEXUS_TEST_PASSWORD
printf '\n'
printf '%s' "$NEXUS_TEST_PASSWORD" |
  docker login --username admin --password-stdin 192.168.1.23:5043
unset NEXUS_TEST_PASSWORD
```

We checked whether the **Docker daemon** itself had proxy settings without printing proxy URLs:

```bash
docker info --format 'HTTP proxy set: {{if .HTTPProxy}}yes{{else}}no{{end}}
HTTPS proxy set: {{if .HTTPSProxy}}yes{{else}}no{{end}}
NO_PROXY: {{.NoProxy}}'
```

Both proxy fields were `no`, and `NO_PROXY` was blank. The daemon proxy was not the source of this 401.

### Direct token request

This command prompted for the password and printed only the HTTP status; it did **not** print the returned token:

```bash
curl --noproxy '*' --user admin --get \
  --data-urlencode 'service=http://192.168.1.23:5043/v2/token' \
  --silent --show-error --output /dev/null \
  --write-out 'Nexus token endpoint: HTTP %{http_code}\n' \
  'http://192.168.1.23:5043/v2/token'
```

It returned **HTTP 200**. This proved the supplied admin credentials worked for the direct token request. It did not prove that a Docker client could use the resulting token for the subsequent Registry API request.

### Read the Nexus request log

We inspected recent registry requests:

```bash
docker exec nexus sh -c \
  "tail -n 120 /nexus-data/log/request.log | grep -E '/v2/|/v2/token' | tail -n 30"
```

The log sequence was:

1. Docker `GET .../v2/` → `401`: normal challenge.
2. Docker `GET .../v2/token?...account=admin...` → `200`: credentials accepted and token issued.
3. Docker retried `GET .../v2/` → `401`: token was not accepted for the registry request.

This proved Nexus **was receiving** Docker's login requests. The wrong active realm was found in the Nexus Security UI and corrected.

### The desktop had two relevant LAN addresses

The Nexus request log showed `192.168.1.11` as the source while the registry URL used `192.168.1.23`. We checked routing:

```bash
ip -4 route get 192.168.1.23
```

The output showed `local 192.168.1.23 ... src 192.168.1.11`: `.23` was local to this desktop, with `.11` selected as the source address. The 401 persisted when using `.11` as the registry destination. The address difference was not the fix; the realm correction was.

## 6. Log in and push the first image

After activating **Docker Bearer Token Realm** alongside **Local Authenticating Realm** and saving the realm list, the admin login succeeded:

```bash
docker login -u admin 192.168.1.23:5043
```

The local Docker host already had `alpine:latest` (`8.44 MB`), which made a small smoke test. We tagged and pushed it:

```bash
docker image ls --format 'table {{.Repository}}\t{{.Tag}}\t{{.Size}}'
docker tag alpine:latest 192.168.1.23:5043/lab/alpine:smoke-test
docker push 192.168.1.23:5043/lab/alpine:smoke-test
```

The user confirmed **the push worked**. The image path `lab/alpine` is a name within the hosted repository reached through port `5043`; it is not a Nexus UI path such as `/repository/Behr00z-repo/`. You can browse its component/tag in **Nexus → Browse → Behr00z-repo**.

## Next: connect Gitea CI

The successful manual push proves registry login and upload work from the Rocky desktop. The Gitea runner has **not yet pushed an image**. The next steps are:

1. Create a dedicated Nexus role for this hosted repository, granting `browse`, `read`, `add`, and `edit`, without admin or delete privileges.
2. Create a local Nexus user such as `gitea-ci` with that role and verify its Docker login.
3. Save its username and password as **repository Actions secrets** in Gitea `behroox/pipeline-lab`.
4. Add a small app, Dockerfile and test to the Git repository; update `.gitea/workflows/` to test, build, log in and push an image tagged with the commit ID.
5. Confirm the CI run is green and the new image appears in Nexus; then use that image from Kubernetes deployment manifests.

Do not put passwords in Git, shell command arguments, screenshots, or workflow YAML. Docker login credentials stored locally may reside in `~/.docker/config.json`; use a dedicated low-privilege account for automation.

## Official references

- [Sonatype: Docker Authentication](https://help.sonatype.com/en/docker-authentication.html)
- [Sonatype: Docker Registry](https://help.sonatype.com/en/docker-registry.html)
- [Sonatype: Hosted Repository for Docker](https://help.sonatype.com/en/hosted-repository-for-docker.html)
- [Sonatype: Realms](https://help.sonatype.com/en/realms.html)
- [Docker: insecure registries and daemon configuration](https://docs.docker.com/reference/cli/dockerd/)
- [Docker: login](https://docs.docker.com/reference/cli/docker/login/)
- [Gitea: Actions secrets](https://docs.gitea.com/usage/actions/secrets/)
