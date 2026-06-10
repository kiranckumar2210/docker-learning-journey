# Day 04 — Docker Containers

> **Source:** [docs.docker.com — What is a container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/) · [Running containers](https://docs.docker.com/engine/containers/run/) · [docker container CLI reference](https://docs.docker.com/reference/cli/docker/container/)

---

## 🎯 Learning Objectives

- Understand what a container is per the official definition and its four properties
- Master every `docker container` subcommand with all flags
- Run containers in foreground, detached, and interactive modes
- List, inspect, exec into, log, stop, remove, and clean up containers
- Understand port publishing, environment variables, volumes, restart policies

---

## 📖 What is a Container?

From the official Docker documentation:

> *"Simply put, containers are isolated processes for each of your app's components. Each component — the frontend React app, the Python API engine, and the database — runs in its own isolated environment, completely isolated from everything else on your machine."*

### The Four Properties of Containers (Official Docs)

| Property | Meaning |
|---|---|
| **Self-contained** | Each container has everything it needs — no reliance on pre-installed host dependencies |
| **Isolated** | Containers run in isolation with minimal influence on the host and other containers |
| **Independent** | Each container is independently managed. Deleting one won't affect any others |
| **Portable** | The container that runs on your development machine will work the same way in a data center or the cloud |

### What Happens When You Run `docker run`

Per the official docs — step by step:

1. Docker checks if the image exists in the local image cache
2. If missing, Docker pulls it from the configured registry
3. Docker creates a new container with a writable layer on top of the image
4. Docker creates a network interface and assigns an IP address
5. Docker starts the container and executes the specified command
6. When a container is removed, any changes to its state that aren't stored in persistent storage disappear

---

## 🛠 Complete `docker container` Command Reference

> Every `docker container` subcommand with all flags, examples, and expected outputs — sourced from the official CLI reference at [docs.docker.com/reference/cli/docker/container](https://docs.docker.com/reference/cli/docker/container/).

---

### 1. `docker container run` — Create and Start a Container

**Syntax:**
```
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
Shorthand: docker run
```

**Core Flags:**

| Flag | Short | Description |
|---|---|---|
| `--detach` | `-d` | Run container in background; print container ID |
| `--interactive` | `-i` | Keep STDIN open even if not attached |
| `--tty` | `-t` | Allocate a pseudo-TTY |
| `--name` | | Assign a name to the container |
| `--publish` | `-p` | Publish container port(s) to the host: `HOST:CONTAINER` |
| `--publish-all` | `-P` | Publish all exposed ports to random host ports |
| `--env` | `-e` | Set environment variable |
| `--env-file` | | Read environment variables from a file |
| `--volume` | `-v` | Bind mount a volume: `HOST_PATH:CONTAINER_PATH` |
| `--network` | | Connect to a network |
| `--rm` | | Automatically remove container when it exits |
| `--restart` | | Restart policy: `no`, `on-failure[:max]`, `always`, `unless-stopped` |
| `--pull` | | Pull policy: `missing` (default), `always`, `never` |
| `--init` | | Run an init process as PID 1 |
| `--platform` | | Set platform (e.g. `linux/amd64`) |
| `--workdir` | `-w` | Working directory inside the container |
| `--user` | `-u` | Username or UID to run as |
| `--hostname` | `-h` | Container hostname |
| `--memory` | `-m` | Memory limit (e.g. `512m`, `2g`) |
| `--cpus` | | Number of CPUs to use (e.g. `0.5`) |
| `--entrypoint` | | Override the image's ENTRYPOINT |
| `--read-only` | | Mount container root filesystem as read-only |
| `--cidfile` | | Write container ID to file |
| `--label` | `-l` | Set metadata on the container |

> **From official docs on `-d`:** *"The `--detach` (or `-d`) flag starts a container as a background process that doesn't occupy your terminal window. By design, containers started in detached mode exit when the root process exits, unless you also specify the `--rm` option."*

> **From official docs on `-i`:** *"The `--interactive` (or `-i`) flag keeps the container's STDIN open, and lets you send input to the container through standard input."*

> **From official docs on `-it` combined:** *"The `-i` flag is most often used together with the `--tty` flag to bind the I/O streams of the container to a pseudo terminal, creating an interactive terminal session."*

**Examples:**

```bash
# Run in foreground (blocks terminal)
docker run nginx

# Run in background — detached mode
docker run -d nginx

# Run interactively with a shell (most common for exploration)
docker run -it ubuntu bash
docker run -it debian
# root@10a3e71492b0:/# factor 90
# 90: 2 3 3 5

# Run with a custom name
docker run --name webserver -d nginx
# Container now reachable as "webserver" in docker commands and on custom networks

# Run with port mapping (host:container)
docker run -d -p 8080:80 nginx
docker run -d -p 127.0.0.1:8080:80 nginx     # Bind to localhost only
docker run -d -p 80                            # Random host port → container port 80
docker run -d -P nginx                         # Publish ALL EXPOSE'd ports randomly

# Run with environment variables
docker run -e MYSQL_ROOT_PASSWORD=secret mysql
docker run -e APP_ENV=production -e PORT=5000 myapp
docker run --env-file .env myapp

# Run with volume
docker run -v myvolume:/app/data nginx
docker run -v $(pwd):/app -w /app python:3.11-slim python app.py

# Auto-remove on exit (great for one-off tasks)
docker run --rm ubuntu echo "hello world"
docker run --rm -it alpine sh

# Restart policy
docker run -d --restart=always nginx
docker run -d --restart=on-failure:3 myapp
docker run -d --restart=unless-stopped redis

# Resource limits
docker run -d --memory=512m --cpus=0.5 myapp

# Override entrypoint
docker run --entrypoint bash nginx

# Run with an init process (good for apps that don't handle signals)
docker run --init -d myapp

# Write container ID to a file
docker run --cidfile /tmp/my_container.cid nginx

# Run as a specific user
docker run -u 1001 myapp
docker run -u nobody myapp
```

---

### 2. `docker container ls` — List Containers

**Syntax:**
```
docker container ls [OPTIONS]
Shorthand: docker ps
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all` | `-a` | Show all containers (default shows only running) |
| `--filter` | `-f` | Filter output by condition |
| `--format` | | Format output using a Go template |
| `--last` | `-n` | Show last N created containers (includes all states) |
| `--latest` | `-l` | Show the latest created container (includes all states) |
| `--no-trunc` | | Don't truncate output |
| `--quiet` | `-q` | Only show container IDs |
| `--size` | `-s` | Show total file sizes |

**Filter options for `--filter`:**

| Filter | Example | Description |
|---|---|---|
| `id` | `id=abc123` | Container ID (partial match) |
| `name` | `name=web` | Container name |
| `status` | `status=running` | Container status |
| `image` | `image=nginx` | Image name or ID |
| `label` | `label=env=prod` | Label key or key=value |
| `exited` | `exited=0` | Exit code (for stopped containers) |
| `before` | `before=mycontainer` | Created before a given container |
| `since` | `since=mycontainer` | Created after a given container |
| `ancestor` | `ancestor=nginx` | Containers from a specific image |
| `publish` | `publish=80` | Published port |
| `expose` | `expose=80` | Exposed port |
| `network` | `network=mynet` | Connected to a network |
| `volume` | `volume=mydata` | Mounted a volume |
| `health` | `health=healthy` | Health status |

**Format template fields:** `{{.ID}}`, `{{.Image}}`, `{{.Command}}`, `{{.CreatedAt}}`, `{{.Status}}`, `{{.Ports}}`, `{{.Names}}`, `{{.Size}}`

**Examples:**

```bash
# Running containers only (default)
docker ps

# All containers (running + stopped)
docker ps -a

# Last 3 created containers
docker ps -n 3

# Only show container IDs
docker ps -q

# All container IDs (for scripting)
docker ps -aq

# Show with file sizes
docker ps -s

# Filter: running only
docker ps --filter status=running

# Filter: exited containers only
docker ps --filter status=exited

# Filter: exited with code 0 (clean exit)
docker ps -a --filter exited=0

# Filter: by image name
docker ps --filter ancestor=nginx

# Filter: by container name
docker ps --filter name=web

# Filter: by network
docker ps --filter network=mynetwork

# Filter: created after a specific container
docker ps --filter since=mycontainer

# Custom table format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"

# One-line per container: name and status
docker ps --format "{{.Names}}: {{.Status}}"

# Full output without truncation
docker ps --no-trunc
```

**Official docs output:**
```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                    NAMES
4bed76d3ad42   nginx     "/docker-entrypoint.…"   1 second ago     Up 1 second     0.0.0.0:8080->80/tcp     webserver
```

> **From official docs:** *"`docker ps` groups exposed ports into a single range if possible. A container exposing TCP ports 100, 101, 102 displays `100-102/tcp` in the PORTS column."*

---

### 3. `docker container exec` — Run a Command in a Running Container

**Syntax:**
```
docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]
Shorthand: docker exec
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--detach` | `-d` | Run command in background |
| `--env` | `-e` | Set environment variable |
| `--env-file` | | Read environment variables from a file |
| `--interactive` | `-i` | Keep STDIN open |
| `--tty` | `-t` | Allocate a pseudo-TTY |
| `--user` | `-u` | Run as username or UID |
| `--workdir` | `-w` | Working directory inside the container |
| `--privileged` | | Give extended privileges to the command |

> **From official docs:** *"`docker exec` runs a new command in a running container."* This is fundamentally different from `docker run`, which creates a new container. `exec` requires the target container to already be running.

**Examples:**

```bash
# Open an interactive bash shell
docker exec -it webserver bash

# Open sh (for Alpine images that don't have bash)
docker exec -it mycontainer sh

# Run a single command (non-interactive)
docker exec webserver ls /etc/nginx/conf.d
docker exec webserver nginx -t              # Test nginx config
docker exec mydb psql -U postgres -c "\l"   # List postgres databases

# Run in background (detached)
docker exec -d mycontainer touch /tmp/execWorks

# Run as a different user
docker exec -u root mycontainer id
docker exec -u postgres mydb psql

# Set an environment variable for the exec session
docker exec -e DEBUG=true myapp python test.py

# Use a different working directory
docker exec -w /tmp mycontainer ls

# Official docs example:
docker run --name mycontainer -d -i -t alpine /bin/sh
docker exec -d mycontainer touch /tmp/execWorks   # Background command
docker exec -it mycontainer sh                     # Interactive shell
```

---

### 4. `docker container logs` — Fetch Container Logs

**Syntax:**
```
docker container logs [OPTIONS] CONTAINER
Shorthand: docker logs
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--details` | | Show extra details (env vars, labels from `--log-opt`) |
| `--follow` | `-f` | Stream log output continuously (Ctrl+C to stop) |
| `--since` | | Show logs since timestamp (e.g. `2024-01-01`, `1h`, `30m`) |
| `--tail` | `-n` | Number of lines from the end to show (default: `all`) |
| `--timestamps` | `-t` | Show RFC3339Nano timestamps on each line |
| `--until` | | Show logs before a timestamp |

> **From official docs:** *"The `docker logs --follow` command will continue streaming the new output from the container's STDOUT and STDERR."*
>
> *"The `docker logs --timestamps` command will add an RFC3339Nano timestamp, for example `2014-09-16T06:17:46.000000000Z`, to each log entry."*
>
> *"Passing a negative number or a non-integer to `--tail` is invalid and the value is set to `all` in that case."*

**Examples:**

```bash
# View all logs
docker logs webserver

# Stream logs live (follow)
docker logs -f webserver

# Last 50 lines only
docker logs --tail 50 webserver

# Last 50 lines and then follow
docker logs -f --tail 50 webserver

# Add timestamps to every line
docker logs -t webserver
# 2024-09-16T06:17:46.000000000Z 172.17.0.1 - - [16/Sep/2024] "GET / HTTP/1.1"

# Logs since 1 hour ago
docker logs --since 1h webserver

# Logs since 30 minutes ago
docker logs --since 30m webserver

# Logs since a specific date
docker logs --since 2024-01-15T10:00:00 webserver

# Logs before a specific time
docker logs --until 2024-01-15T12:00:00 webserver

# Logs between two timestamps
docker logs --since 2024-01-15T10:00:00 --until 2024-01-15T12:00:00 webserver

# Show extra details (env vars, labels)
docker logs --details webserver

# Full combination: last 20 lines with timestamps, live follow
docker logs -f --tail 20 -t webserver
```

---

### 5. `docker container inspect` — Show Detailed Container Metadata

**Syntax:**
```
docker container inspect [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker inspect CONTAINER
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--format` | `-f` | Format output using a Go template |
| `--size` | `-s` | Display total file sizes |
| `--type` | | Return JSON for specified type (container, image, network, volume) |

**Examples:**

```bash
# Full JSON metadata
docker inspect webserver

# Inspect multiple containers at once
docker inspect webserver mydb redis

# Get container IP address
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' webserver
# 172.17.0.2

# Get container state
docker inspect -f '{{.State.Status}}' webserver
# running

# Get exit code
docker inspect -f '{{.State.ExitCode}}' webserver
# 0

# Get restart count
docker inspect -f '{{.RestartCount}}' webserver

# Get the image it was created from
docker inspect -f '{{.Config.Image}}' webserver

# Get environment variables
docker inspect -f '{{.Config.Env}}' webserver

# Get port bindings
docker inspect -f '{{.HostConfig.PortBindings}}' webserver

# Get volume mounts
docker inspect -f '{{.Mounts}}' webserver

# Get restart policy
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' webserver

# Get all networks the container is on
docker inspect -f '{{json .NetworkSettings.Networks}}' webserver

# Check if container is running
docker inspect -f '{{.State.Running}}' webserver
# true

# Get health status (if HEALTHCHECK is defined)
docker inspect -f '{{.State.Health.Status}}' webserver
# healthy

# Get container's hostname
docker inspect -f '{{.Config.Hostname}}' webserver

# Get start time
docker inspect -f '{{.State.StartedAt}}' webserver

# Show with file sizes (-s)
docker inspect -s webserver
```

---

### 6. `docker container stop` — Gracefully Stop a Container

**Syntax:**
```
docker container stop [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker stop
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--signal` | `-s` | Signal to send to the container (default: `SIGTERM`) |
| `--time` | `-t` | Seconds to wait for stop before killing (default: `10`) |

> **How `docker stop` works:** It sends `SIGTERM` to the main process, waits up to `--time` seconds for a graceful shutdown, then sends `SIGKILL` if the container is still running.

**Examples:**

```bash
# Graceful stop (SIGTERM, 10s timeout)
docker stop webserver

# Stop multiple containers at once
docker stop webserver mydb redis

# Stop all running containers
docker stop $(docker ps -q)

# Custom timeout (30 seconds)
docker stop --time 30 webserver

# Send a different signal
docker stop --signal SIGHUP webserver

# Stop using the short ID (first 3 chars)
docker stop a1f
```

---

### 7. `docker container start` — Start a Stopped Container

**Syntax:**
```
docker container start [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker start
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--attach` | `-a` | Attach STDOUT/STDERR and forward signals |
| `--checkpoint` | | Restore from a checkpoint (experimental) |
| `--detach-keys` | | Override key sequence for detaching |
| `--interactive` | `-i` | Attach container's STDIN |

> **Note:** When you restart a stopped container, it starts with the **same flags and command** it was originally started with.

**Examples:**

```bash
# Start a stopped container
docker start webserver

# Start multiple containers
docker start webserver mydb redis

# Start and attach to output
docker start -a webserver

# Start with STDIN attached (for interactive containers)
docker start -i mycontainer
```

---

### 8. `docker container restart` — Restart a Container

**Syntax:**
```
docker container restart [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker restart
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--signal` | `-s` | Signal to send to the container |
| `--time` | `-t` | Seconds to wait before killing (default: `10`) |

**Examples:**

```bash
# Restart a container
docker restart webserver

# Restart multiple containers
docker restart webserver mydb

# Restart with custom stop timeout
docker restart --time 30 webserver
```

---

### 9. `docker container kill` — Kill a Container Immediately

**Syntax:**
```
docker container kill [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker kill
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--signal` | `-s` | Signal to send (default: `KILL` = SIGKILL) |

> **`docker stop` vs `docker kill`:** `stop` sends SIGTERM and waits. `kill` sends SIGKILL immediately with no grace period. Use `stop` for clean shutdowns; use `kill` only when a container is unresponsive.

**Examples:**

```bash
# Kill immediately (SIGKILL)
docker kill webserver

# Kill multiple containers
docker kill webserver mydb

# Send a specific signal
docker kill --signal SIGTERM webserver
docker kill --signal SIGHUP webserver      # Reload config (nginx, for example)
docker kill --signal SIGUSR1 myapp         # Custom signal handling
```

---

### 10. `docker container rm` — Remove Containers

**Syntax:**
```
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
Shorthand: docker rm
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--force` | `-f` | Force removal of a running container (sends SIGKILL first) |
| `--link` | `-l` | Remove the specified link (legacy) |
| `--volumes` | `-v` | Remove anonymous volumes attached to the container |

> **From official docs:** *"When a container is removed, it's no longer running nor in the stopped state. Instead, the process inside the container is terminated and the metadata for the container is removed."*

**Examples:**

```bash
# Remove a stopped container
docker rm webserver

# Remove multiple containers
docker rm webserver mydb redis

# Force-remove a running container (SIGKILL + rm)
docker rm -f webserver

# Remove and also delete anonymous volumes
docker rm -v webserver

# Remove all stopped containers (official docs approach)
docker rm $(docker ps --filter status=exited -q)

# Force-remove ALL containers (running + stopped)
docker rm -f $(docker ps -aq)
```

**Official docs note:**
> *"Use the `docker container prune` command to remove all stopped containers, or refer to `docker system prune` to remove unused containers in addition to other Docker resources such as images and networks."*

---

### 11. `docker container prune` — Remove All Stopped Containers

**Syntax:**
```
docker container prune [OPTIONS]
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--filter` | | Filter containers to prune (e.g. `until=24h`, `label=env=dev`) |
| `--force` | `-f` | Skip confirmation prompt |

> **From official docs:** *"When you stop a container, it isn't automatically removed unless you started it with the `--rm` flag. A stopped container's writable layers still take up disk space. To clean this up, you can use `docker container prune`."*

**Examples:**

```bash
# Remove all stopped containers (with confirmation)
docker container prune

# Skip confirmation
docker container prune --force

# Remove stopped containers older than 24 hours
docker container prune --filter "until=24h"

# Remove using an exact timestamp
docker container prune --force --filter "until=2017-01-04T13:10:00"

# Remove containers with a specific label
docker container prune --filter "label=env=dev"
```

**Official docs output:**
```
$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
4a7f7eebae0f63178aff7eb0aa39cd3f0627a203ab2df258c1a00b456cf20063
f98f9c2aa1eaf727e4ec9c0283bc7d4aa4762fbdba7f26191f26c97f64090360
Total reclaimed space: 212 B
```

---

### 12. `docker container cp` — Copy Files Between Host and Container

**Syntax:**
```
docker container cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH
docker container cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH
Shorthand: docker cp
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--archive` | `-a` | Archive mode: copy all uid/gid info |
| `--follow-link` | `-L` | Follow symbolic links in SRC_PATH |
| `--quiet` | `-q` | Suppress progress output |

**Examples:**

```bash
# Copy FROM container TO host
docker cp webserver:/etc/nginx/nginx.conf ./nginx.conf
docker cp webserver:/var/log/nginx/access.log ./logs/

# Copy FROM host TO container
docker cp ./myconfig.conf webserver:/etc/nginx/conf.d/myconfig.conf
docker cp ./index.html webserver:/usr/share/nginx/html/

# Copy a whole directory
docker cp webserver:/etc/nginx/ ./nginx-backup/

# Copy with archive mode (preserves uid/gid)
docker cp --archive mycontainer:/app/data ./data-backup/
```

---

### 13. `docker container stats` — Live Resource Usage Statistics

**Syntax:**
```
docker container stats [OPTIONS] [CONTAINER...]
Shorthand: docker stats
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all` | `-a` | Show all containers (default shows only running) |
| `--format` | | Format output using a Go template |
| `--no-stream` | | Disable streaming stats; show one snapshot |
| `--no-trunc` | | Don't truncate output |

**Output columns:** `CONTAINER ID`, `NAME`, `CPU %`, `MEM USAGE / LIMIT`, `MEM %`, `NET I/O`, `BLOCK I/O`, `PIDS`

**Examples:**

```bash
# Live stats for all running containers
docker stats

# One-shot snapshot (no live stream)
docker stats --no-stream

# Stats for specific containers
docker stats webserver mydb

# Show all containers (including stopped — they show 0 usage)
docker stats --all

# Custom format — name and CPU only
docker stats --format "{{.Container}}: {{.CPUPerc}}"
# 09d3bb5b1604: 6.61%
# 9db7aa4d986d: 9.19%

# Table format — name, CPU, memory
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
# CONTAINER    CPU %   MEM USAGE / LIMIT
# webserver    0.07%   796 KiB / 64 MiB
# mydb         1.23%   45 MiB / 512 MiB
```

**Official docs output:**
```
CONTAINER ID   NAME              CPU %   MEM USAGE / LIMIT   MEM %    NET I/O       BLOCK I/O   PIDS
b95a83497c91   awesome_brattain  0.28%   5.629MiB / 1.952GiB  0.28%  916B / 0B     147kB / 0B  9
67b2525d8ad1   foobar            0.00%   1.727MiB / 1.952GiB  0.09%  2.48kB / 0B   4.11MB / 0B 2
```

---

### 14. `docker container top` — Display Running Processes in a Container

**Syntax:**
```
docker container top CONTAINER [ps OPTIONS]
Shorthand: docker top
```

**No Docker-specific flags** — any additional arguments are passed to the `ps` command inside the container.

**Examples:**

```bash
# Show processes with default ps output
docker top webserver

# Show processes with custom ps options
docker top webserver aux
docker top webserver -eo pid,user,comm

# Output:
# UID    PID   PPID  C  STIME  TTY   TIME     CMD
# root   1234  1230  0  10:00  ?     0:00:00  nginx: master process
# 101    1235  1234  0  10:00  ?     0:00:00  nginx: worker process
```

---

### 15. `docker container diff` — Inspect Changes to Container Filesystem

**Syntax:**
```
docker container diff CONTAINER
Shorthand: docker diff
```

**No flags.** Returns a list of all files changed since the container was created from its image.

**Change types:**
- `A` — file/directory was **Added**
- `C` — file/directory was **Changed**
- `D` — file/directory was **Deleted**

**Examples:**

```bash
# See all filesystem changes
docker diff webserver
# C /etc/nginx
# C /etc/nginx/conf.d
# A /etc/nginx/conf.d/mysite.conf    ← New file added
# C /var/log/nginx
# A /var/log/nginx/access.log

# Useful workflow — make changes, then diff
docker exec webserver touch /tmp/testfile
docker diff webserver
# A /tmp/testfile
```

---

### 16. `docker container port` — List Port Mappings for a Container

**Syntax:**
```
docker container port CONTAINER [PRIVATE_PORT[/PROTO]]
Shorthand: docker port
```

**No flags.**

**Examples:**

```bash
# Show all port mappings
docker port webserver
# 80/tcp -> 0.0.0.0:8080

# Show mapping for a specific container port
docker port webserver 80
# 0.0.0.0:8080

# Show mapping for a specific port/protocol
docker port webserver 80/tcp
# 0.0.0.0:8080
```

---

### 17. `docker container rename` — Rename a Container

**Syntax:**
```
docker container rename CONTAINER NEW_NAME
Shorthand: docker rename
```

**No flags.**

**Examples:**

```bash
# Rename a container
docker rename webserver my-nginx

# Verify the rename
docker ps --filter name=my-nginx
```

---

### 18. `docker container pause` / `docker container unpause`

**Syntax:**
```
docker container pause CONTAINER [CONTAINER...]
docker container unpause CONTAINER [CONTAINER...]
Shorthand: docker pause / docker unpause
```

> Pausing uses Linux cgroups freezer, which suspends all processes in the container. The container remains running but CPU is not scheduled for it.

**Examples:**

```bash
# Pause a container (freezes all processes)
docker pause webserver

# Check status — shows "Paused"
docker ps
# STATUS: Up 5 minutes (Paused)

# Unpause
docker unpause webserver
```

---

### 19. `docker container commit` — Create Image from Container

**Syntax:**
```
docker container commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
Shorthand: docker commit
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--author` | `-a` | Author name |
| `--change` | `-c` | Apply a Dockerfile instruction to the new image |
| `--message` | `-m` | Commit message |
| `--pause` | `-p` | Pause container during commit (default: `true`) |

> **Note:** `docker commit` creates an image from a container's current state. This is useful for debugging but not recommended for production — write a Dockerfile instead for reproducibility.

**Examples:**

```bash
# Commit a container to a new image
docker commit webserver mynginx:customized

# With author and message
docker commit -a "Your Name" -m "Added custom config" webserver mynginx:v2

# Apply a new CMD when committing
docker commit --change='CMD ["nginx", "-g", "daemon off;"]' webserver mynginx:v2

# Apply ENV change
docker commit --change='ENV DEBUG=true' webserver myapp:debug
```

---

### 20. `docker container export` — Export Container Filesystem as Tar

**Syntax:**
```
docker container export [OPTIONS] CONTAINER
Shorthand: docker export
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--output` | `-o` | Write to a file instead of STDOUT |

> **`export` vs `docker save`:** `export` exports a **container's filesystem** as a flat tar — no image metadata, no layers, no history. `docker save` exports an **image** with all its layers and metadata intact. Use `save`/`load` for image transfer; use `export`/`import` for filesystem snapshots.

**Examples:**

```bash
# Export container filesystem to a file
docker export webserver --output webserver.tar

# Export to STDOUT and compress
docker export webserver | gzip > webserver.tar.gz

# Import it back as a new image
docker import webserver.tar mywebserver:v1
```

---

### 21. `docker container attach` — Attach to a Running Container

**Syntax:**
```
docker container attach [OPTIONS] CONTAINER
Shorthand: docker attach
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--detach-keys` | | Override key sequence for detaching (default: `ctrl-p,ctrl-q`) |
| `--no-stdin` | | Do not attach STDIN |
| `--sig-proxy` | | Proxy all received signals to the process (default: `true`) |

> **`attach` vs `exec`:** `attach` connects your terminal to the container's PID 1 process. Pressing Ctrl+C in an attached session sends SIGINT to PID 1, which may stop the container. Use `exec -it` for a new shell session instead — it's safer.

**Examples:**

```bash
# Attach to a running container
docker attach webserver

# Detach without stopping: Ctrl+P then Ctrl+Q

# Attach with custom detach keys
docker attach --detach-keys="ctrl-d" webserver

# Attach without STDIN
docker attach --no-stdin webserver
```

---

### 22. `docker container create` — Create Without Starting

**Syntax:**
```
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]
Shorthand: docker create
```

**Flags:** Same as `docker run` (minus the run-specific ones).

> **From official docs:** *"`docker create` shares most of its options with `docker run` (which performs a `docker create` before starting it). This is useful when you want to set up a container configuration ahead of time so that it's ready to start when you need it. The initial status of the new container is `created`."*

**Examples:**

```bash
# Create but don't start
docker create --name prebuilt nginx
# Returns container ID; status is "Created"

docker ps -a --filter name=prebuilt
# STATUS: Created

# Start it later
docker start prebuilt
```

---

### 23. `docker container wait` — Block Until Container Stops

**Syntax:**
```
docker container wait CONTAINER [CONTAINER...]
Shorthand: docker wait
```

**No flags.** Blocks until the container stops, then prints the exit code.

**Examples:**

```bash
# Wait for a container to exit, then capture its exit code
docker wait mycontainer
# 0

# Use in shell scripts
EXIT_CODE=$(docker wait mycontainer)
echo "Container exited with: $EXIT_CODE"
```

---

## 📖 All `docker container` Subcommands — Quick Reference Table

| Command | Shorthand | What It Does |
|---|---|---|
| `docker container run` | `docker run` | Create and start a container |
| `docker container ls` | `docker ps` | List containers |
| `docker container exec` | `docker exec` | Run a command in a running container |
| `docker container logs` | `docker logs` | Fetch container output logs |
| `docker container inspect` | `docker inspect` | Show detailed JSON metadata |
| `docker container stop` | `docker stop` | Gracefully stop (SIGTERM → SIGKILL) |
| `docker container start` | `docker start` | Start a stopped container |
| `docker container restart` | `docker restart` | Stop then start |
| `docker container kill` | `docker kill` | Immediately kill (SIGKILL) |
| `docker container rm` | `docker rm` | Remove stopped containers |
| `docker container prune` | — | Remove all stopped containers |
| `docker container cp` | `docker cp` | Copy files between host and container |
| `docker container stats` | `docker stats` | Live resource usage |
| `docker container top` | `docker top` | Show processes inside container |
| `docker container diff` | `docker diff` | Show filesystem changes since creation |
| `docker container port` | `docker port` | List port mappings |
| `docker container rename` | `docker rename` | Rename a container |
| `docker container pause` | `docker pause` | Freeze container processes |
| `docker container unpause` | `docker unpause` | Resume frozen processes |
| `docker container commit` | `docker commit` | Create image from container state |
| `docker container export` | `docker export` | Export container filesystem as tar |
| `docker container attach` | `docker attach` | Attach terminal to container PID 1 |
| `docker container create` | `docker create` | Create without starting |
| `docker container wait` | `docker wait` | Block until container stops; print exit code |

---

## 🧪 Practice Tasks

1. Run the welcome container from the official docs:
   ```bash
   docker run -d -p 8080:80 --name welcome docker/welcome-to-docker
   ```
2. Verify it's running: `docker ps` — note the PORTS column
3. Open `http://localhost:8080` in your browser
4. View logs: `docker logs -f welcome`
5. Exec in and explore: `docker exec -it welcome sh`
6. Check what files changed: `docker diff welcome`
7. Check port mapping: `docker port welcome`
8. View resource usage: `docker stats --no-stream welcome`
9. Stop using short ID: `docker stop wel` (first 3 chars)
10. Verify it stopped: `docker ps -a --filter name=welcome`
11. Remove it: `docker rm welcome`
12. Clean up all stopped containers: `docker container prune --force`

---

## 🏆 Challenge — Full Container Lifecycle

```bash
# 1. Run postgres with full options
docker run -d \
  --name mypostgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_USER=admin \
  -e POSTGRES_DB=mydb \
  -p 5432:5432 \
  --restart=unless-stopped \
  postgres:15-alpine

# 2. Verify and inspect
docker ps
docker inspect mypostgres -f '{{.State.Status}}'
docker inspect mypostgres -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
docker port mypostgres

# 3. Exec in and run SQL
docker exec -it mypostgres psql -U admin -d mydb -c "\l"

# 4. View resource usage
docker stats --no-stream mypostgres

# 5. Check processes
docker top mypostgres

# 6. Commit a snapshot
docker commit mypostgres mypostgres-snapshot:day04

# 7. Stop, remove, clean up
docker stop mypostgres
docker rm mypostgres
docker image rm mypostgres-snapshot:day04
```

---

## 💬 Interview Questions

1. What is the difference between `docker run` and `docker start`?
2. What does `docker exec` do, and how is it different from `docker run`?
3. What signals does `docker stop` send, and in what order?
4. What does `docker rm -f` do that `docker rm` alone cannot?
5. What is the difference between `docker export` and `docker save`?
6. What does `docker diff` show, and what do the `A`, `C`, `D` codes mean?
7. When would you use `docker attach` instead of `docker exec -it`?
8. What restart policies are available and what does `unless-stopped` mean?

---

## 🔗 Official Documentation References

- [What is a Container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)
- [Running containers](https://docs.docker.com/engine/containers/run/)
- [docker container run](https://docs.docker.com/reference/cli/docker/container/run/)
- [docker container ls](https://docs.docker.com/reference/cli/docker/container/ls/)
- [docker container exec](https://docs.docker.com/reference/cli/docker/container/exec/)
- [docker container logs](https://docs.docker.com/reference/cli/docker/container/logs/)
- [docker container inspect](https://docs.docker.com/reference/cli/docker/container/inspect/)
- [docker container stats](https://docs.docker.com/reference/cli/docker/container/stats/)
- [docker container rm](https://docs.docker.com/reference/cli/docker/container/rm/)
- [docker container prune](https://docs.docker.com/reference/cli/docker/container/prune/)
- [Prune unused Docker objects](https://docs.docker.com/engine/manage-resources/pruning/)

---

## 📝 Git Commit Message

```
Day 04: Docker Containers — Complete container command reference with all 24 subcommands, flags, examples per official docs
```
