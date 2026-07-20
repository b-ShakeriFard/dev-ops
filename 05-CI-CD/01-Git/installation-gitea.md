July 20th - 2026
Behrouz ShakeriFard

bshakeri@torontomu.ca

In this document we will look at the steps required for installing gite, local, running on containers.

Check your docker

```bash
docker --version

docker compose --version

sudo systemctl is-active docker

getenforce

hostname -I
```

Cool - feel free to check your user ID and Group ID as well.

```bash

id -u

id -g
```

If it returns 1000, you're good ;-)

<hr>

Here is a macro level overview of what we're about to do:

- Prepare your Linux

- Confirm networking, hostname, storage capacity, time synchronization, and firewall status.

- Choose the deployment method (docker container in this case, which keeps it isolated and makes upgrades or removal easier)

- Create persistent storage (we are here)

- Gitea’s configuration, repositories, user data, and SQLite database must live on the laptop—not temporarily inside the container.

- Deploy Gitea

- Start the container with two network entry points:

<ul>
 <li> Web interface for administration and repository browsing </li>
 <li> SSH service for cloning and pushing repositories </li>
</ul>

- Complete initial configuration

- Through the browser, we’ll configure the database, administrator account, server address, -    repository directory, and basic security settings.

- Configure network access

- Allow the required ports through Rocky Linux’s firewall so your other home-lab machines can reach Gitea.

- Add local DNS and HTTPS

 - Give it a friendly address such as gitea.lab.local and secure the web interface with a TLS certificate. We can initially postpone this and use the IP address.

- Create and test a repository

- Create a repository, clone it to another machine, commit a file, and push it back to Gitea.

- Integrate the home lab

- Connect Gitea to Argo CD so Gitea stores your Kubernetes manifests and Argo CD continuously deploys them.

<hr>

Make this folder 

```bash
sudo mkdir /opt/gitea/data
```

sanity check:
```bash
ls -ld /opt/gitea/data

```

Now we create the docker compose file for our Gitea container, with the following content:

/opt/gitea/compose.yaml

```bash

services:
  gitea:
    image: docker.gitea.com/gitea:1.27.0
    container_name: gitea
    restart: unless-stopped

    environment:
      USER_UID: 1000
      USER_GID: 1000

    volumes:
      - /opt/gitea/data:/data:Z

    ports:
      - "3000:3000"
      - "2222:22"
```