# Day 04 — Notes: Docker Containers

> Sourced from [docs.docker.com/engine/containers/run](https://docs.docker.com/engine/containers/run/) and [docs.docker.com/reference/cli/docker/container/run](https://docs.docker.com/reference/cli/docker/container/run/)

---

## `docker run` — What Happens Internally (Official Docs)

When `docker run` is called, the daemon:

1. Checks if the image is in the local image cache
2. If missing: pulls from registry (default `--pull missing` behaviour)
3. Creates a new container (`docker container create` equivalent)
4. Allocates a writable filesystem layer on top of the image
5. Creates a network interface and assigns IP
6. Starts the container and runs the specified command

---

## Key `docker run` Flags (Official Reference)

| Flag | Full Name | Effect |
|---|---|---|
| `-d` | `--detach` | Run in background; print container ID |
| `-i` | `--interactive` | Keep STDIN open even if not attached |
| `-t` | `--tty` | Allocate a pseudo-TTY |
| `--name` | — | Assign a name (enables DNS in custom networks) |
| `-p` | `--publish` | Publish container port to host: `HOST:CONTAINER` |
| `-e` | `--env` | Set environment variable |
| `--env-file` | — | Read env vars from a file |
| `--rm` | — | Auto-remove container when it exits |
| `-v` | `--volume` | Bind mount or named volume |
| `--network` | — | Connect to a network |
| `--restart` | — | Restart policy: `no`, `always`, `on-failure`, `unless-stopped` |
| `--pull` | — | Pull policy: `missing` (default), `always`, `never` |

---

## `docker ps` — Understanding the Output

```
CONTAINER ID   IMAGE         COMMAND     CREATED    STATUS     PORTS                  NAMES
4bed76d3ad42   nginx:alpine  "/docker…"  1 sec ago  Up 1 sec   0.0.0.0:8080->80/tcp   webserver
```

| Column | Meaning |
|---|---|
| CONTAINER ID | Short form of the 64-char container ID |
| IMAGE | Image the container was created from |
| COMMAND | The entrypoint/command running inside |
| CREATED | When the container was created |
| STATUS | `Up Xm` (running) or `Exited (0) Xm ago` (stopped) |
| PORTS | Port mappings: `HOST_PORT->CONTAINER_PORT/protocol` |
| NAMES | Human-readable name (random if not set with `--name`) |

---

## Container States (Official)

Per the official docs, containers move through these states:

```
Created ──▶ Running ──▶ Paused
    ▲           │          │
    │      docker stop  docker unpause
    │           │
    └───── Stopped/Exited ──▶ Removed (docker rm)
```

**Key quote from docs:**
> *"When you stop a container, it's not removed but the status is changed to stopped and the process inside of the container is stopped."*

---

## `docker exec` vs `docker run`

| Aspect | `docker exec` | `docker run` |
|---|---|---|
| What it does | Runs a command in an **existing** running container | Creates and starts a **new** container |
| Requires | Container must already be running | Only needs an image |
| Use case | Debugging, one-off commands, opening a shell | Starting new containers |
| Creates container? | No | Yes |

---

## Exit Codes

| Code | Meaning |
|---|---|
| 0 | Container exited normally (success) |
| 1 | Application error |
| 125 | Docker daemon error |
| 126 | Command found but not executable |
| 127 | Command not found |
| 137 | Killed (SIGKILL — OOM or `docker kill`) |
| 143 | Terminated (SIGTERM — `docker stop`) |

---

## Port Mapping — `-p` Flag Variants

```bash
-p 8080:80                  # Host 8080 → Container 80 (all interfaces)
-p 127.0.0.1:8080:80        # Host 8080 (localhost only) → Container 80
-p 80                       # Random host port → Container 80
-P                          # Publish ALL EXPOSE'd ports to random host ports
```

---

## My Notes

> Record container IDs you worked with, commands you ran, observations.

- Container ID format: 64 hex chars (you only need the first 3–4 to be unique)
- Random names Docker assigned today:
- Something that surprised me:
-
