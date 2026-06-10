# Day 04 — Commands: Docker Containers

> All commands sourced and cross-referenced with [docs.docker.com/reference/cli/docker/container](https://docs.docker.com/reference/cli/docker/container/)

---

## docker run — Core Usage

```bash
# Run in foreground (blocks terminal)
docker run nginx

# Run in background/detached (-d)
docker run -d nginx

# Run with port mapping (host:container)
docker run -d -p 8080:80 nginx

# Run interactively with TTY (-it)
docker run -it ubuntu /bin/bash

# Run with a custom name (--name)
docker run --name test -d nginx:alpine

# Auto-remove on exit (--rm)
docker run --rm ubuntu echo "hello"

# With environment variable
docker run -e MYSQL_ROOT_PASSWORD=secret mysql

# Specific tag
docker run ubuntu:22.04

# Pull always before running
docker run --pull always nginx
```

---

## docker ps — List Containers

```bash
# Running containers only (default)
docker ps

# All containers (running + stopped)
docker ps -a

# Only container IDs
docker ps -q

# All container IDs
docker ps -aq

# Filter by status
docker ps --filter status=running
docker ps --filter status=exited

# Custom format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
```

**Official docs output:**
```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                    NAMES
715ebfcee040   busybox   "top"                    16 minutes ago   Up 16 minutes                            i_am_nostalgic
```

---

## docker exec — Run Commands in Running Container

```bash
# Open an interactive shell (bash)
docker exec -it mycontainer bash

# Open sh (for Alpine — no bash)
docker exec -it mycontainer sh

# Run a single command
docker exec mycontainer ls /etc/nginx

# Run as different user
docker exec -u root mycontainer id

# With environment variable
docker exec -e DEBUG=1 mycontainer python test.py
```

**Official docs example:**
```bash
# Create container with persistent shell
docker run --name mycontainer -d -i -t alpine /bin/sh

# Execute a background command in it
docker exec -d mycontainer touch /tmp/execWorks

# Open interactive shell in it
docker exec -it mycontainer sh
```

---

## docker stop / start / restart / kill

```bash
# Graceful stop (SIGTERM → 10s grace → SIGKILL)
docker stop mycontainer

# Stop with custom timeout (30 seconds)
docker stop --time 30 mycontainer

# Stop using short ID (first 3 chars)
docker stop a1f

# Start a stopped container
docker start mycontainer

# Restart
docker restart mycontainer

# Kill immediately (SIGKILL — no grace period)
docker kill mycontainer
```

---

## docker logs — View Container Output

```bash
# View all logs
docker logs mycontainer

# Follow log output (live stream)
docker logs -f mycontainer

# Last N lines
docker logs --tail 50 mycontainer

# With timestamps
docker logs -t mycontainer

# Since a specific time
docker logs --since 1h mycontainer

# Combine: last 20 lines, with timestamps, live
docker logs -f --tail 20 -t mycontainer
```

---

## docker rm — Remove Containers

```bash
# Remove a stopped container
docker rm mycontainer

# Force-remove a running container
docker rm -f mycontainer

# Remove all stopped containers
docker container prune

# Remove all containers (caution!)
docker rm -f $(docker ps -aq)
```

---

## docker inspect — Get Container Metadata

```bash
# Full JSON inspection
docker inspect mycontainer

# Get IP address (Go template)
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mycontainer

# Get container state
docker inspect -f '{{.State.Status}}' mycontainer

# Get exit code
docker inspect -f '{{.State.ExitCode}}' mycontainer

# Get port bindings
docker inspect -f '{{.HostConfig.PortBindings}}' mycontainer
```

---

## docker stats — Live Resource Usage

```bash
# Live stats for all running containers
docker stats

# One-shot (no streaming)
docker stats --no-stream

# Specific container
docker stats mycontainer

# Custom format
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

---

## docker cp — Copy Files

```bash
# Copy FROM container TO host
docker cp mycontainer:/etc/nginx/nginx.conf ./nginx.conf

# Copy FROM host TO container
docker cp ./myfile.conf mycontainer:/etc/nginx/conf.d/
```

---

## Full Workflow — Official Docs Tutorial Sequence

```bash
# 1. Run the welcome container (from official docs)
docker run -d -p 8080:80 docker/welcome-to-docker

# 2. View it's running
docker ps
# Note: PORTS column shows 0.0.0.0:8080->80/tcp

# 3. Visit http://localhost:8080 in browser

# 4. Stop it using the short ID
docker stop a1f   # Replace with your first 3 chars

# 5. Verify stopped (not removed)
docker ps         # Empty (no running containers)
docker ps -a      # Shows container with status "Exited (0)"

# 6. Remove it
docker rm a1f
```

---

## Screenshot Placeholders

> `[Screenshot: docker ps showing welcome-to-docker container with port 0.0.0.0:8080->80/tcp]`
> `[Screenshot: http://localhost:8080 in browser — the welcome page]`
> `[Screenshot: docker exec -it mycontainer sh — inside the container shell]`
> `[Screenshot: docker stats showing CPU and memory usage]`
