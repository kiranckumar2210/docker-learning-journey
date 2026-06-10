# Day 02 — Notes: Docker Installation

> Sourced from [docs.docker.com/engine/install/ubuntu](https://docs.docker.com/engine/install/ubuntu/) and [docs.docker.com/engine/install/linux-postinstall](https://docs.docker.com/engine/install/linux-postinstall/)

---

## What Gets Installed

| Package | Role |
|---|---|
| `docker-ce` | Docker Engine (the daemon) |
| `docker-ce-cli` | Docker CLI (`docker` command) |
| `containerd.io` | Container runtime (containerd + runc bundled) |
| `docker-buildx-plugin` | BuildKit — next-gen image builder |
| `docker-compose-plugin` | Docker Compose v2 (integrated as `docker compose`) |

> **Key note from official docs:** Docker Engine depends on `containerd` and `runc`. The `containerd.io` package bundles both. If you had them installed separately before, remove them first to avoid conflicts.

---

## Two Update Channels (Official Docs)

| Channel | Description |
|---|---|
| **stable** | Latest versions released for general availability |
| **test** | Pre-release versions ready for testing before GA |

The installation above uses the **stable** channel.

---

## The docker Group — Security Note

From the official post-installation docs:

> *"The docker user group exists but contains no users, which is why you're required to use sudo to run Docker commands."*

Adding yourself to the `docker` group is equivalent to granting root access. This is acceptable on a personal dev machine but **not recommended in production** — use rootless Docker instead.

---

## Rootless Docker (Advanced — Optional)

Per the official docs, rootless mode lets you run the Docker daemon and containers as a non-root user — mitigating potential vulnerabilities in the daemon and container runtime.

Reference: https://docs.docker.com/engine/security/rootless/

---

## Checking Service Status

```bash
# Check if Docker daemon is running
sudo systemctl status docker

# View daemon logs
sudo journalctl -u docker.service

# Check if Docker starts on boot
sudo systemctl is-enabled docker
```

---

## `docker version` Output — What Each Field Means

```
Client: Docker Engine - Community
 Version:           26.x.x        ← Docker Engine version
 API version:       1.45          ← REST API version
 Go version:        go1.21.x      ← Go compiler version
 OS/Arch:           linux/amd64   ← Client OS and architecture

Server: Docker Engine - Community
 Engine:
  Version:          26.x.x
  API version:      1.45 (minimum version 1.12)
  OS/Arch:          linux/amd64
 containerd:
  Version:          1.7.x         ← containerd runtime version
 runc:
  Version:          1.1.x         ← runc (OCI runtime) version
```

---

## `docker info` — Key Fields to Note

```
Containers: 0          ← Total containers (running + stopped)
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0              ← Total local images
Server Version: 26.x
Storage Driver: overlay2  ← Filesystem driver (overlay2 is default and recommended)
Cgroup Driver: systemd    ← Control group driver
```

---

## My Notes

> Add your installation observations here.

- Docker version installed:
- Ubuntu version:
- Any issues encountered:
-
