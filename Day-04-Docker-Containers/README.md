# Day 04 — Docker Containers

> **Source:** [docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/) · [docs.docker.com/engine/containers/run](https://docs.docker.com/engine/containers/run/) · [docs.docker.com/reference/cli/docker/container/run](https://docs.docker.com/reference/cli/docker/container/run/) · [docs.docker.com/reference/cli/docker/container/exec](https://docs.docker.com/reference/cli/docker/container/exec/)

---

## 🎯 Learning Objectives

- Run containers in multiple modes (foreground, detached, interactive)
- Understand how `docker run` creates a container from an image
- Use `docker ps` to list containers (running and all)
- Execute commands in running containers with `docker exec`
- Stop, start, and remove containers
- Map ports from host to container

---

## 📖 Running a Container — `docker run`

Per the official docs:

> *"The docker run command must specify an image reference to create the container from. The image reference is the name and version of the image."*

When you run `docker run nginx`:

1. Docker checks if the image exists in the local image cache
2. If not present, it pulls from the configured registry
3. Docker creates a new container with a writable layer on top of the image
4. Docker starts the container and runs the default command

### Detached Mode (`-d`)

From the official docs:

> *"When you start a container, the container runs in the foreground by default. If you want to run the container in the background instead, you can use the `--detach` (or `-d`) flag. This starts the container without occupying your terminal window."*

```bash
docker run -d -p 8080:80 docker/welcome-to-docker
```

### Named Containers (`--name`)

From the official CLI docs:

> *"If you don't specify a custom name using the `--name` flag, the daemon assigns a randomly generated name, such as `vibrant_cannon`, to the container. Using a custom-defined name provides the benefit of having an easy-to-remember ID."*

> *"Moreover, if you connect the container to a user-defined bridge network, other containers on the same network can refer to the container by name via DNS."*

```bash
docker run --name test -d nginx:alpine
```

### Image Tags

Per the official docs:

> *"An image tag is the image version, which defaults to `latest` when omitted. Use the tag to run a container from a specific version of an image."*

```bash
docker run ubuntu:24.04     # Specific tag
docker run ubuntu           # Implicit :latest
```

### Pull Policy

The official docs document three pull behaviours:

| Flag | Behaviour |
|---|---|
| `--pull missing` (default) | Only pull if not present in local cache |
| `--pull always` | Always pull before creating (ensures up-to-date) |
| `--pull never` | Never pull; fail if image not in local cache |

---

## 📖 Listing Containers — `docker ps`

From the official CLI docs:

> *"The `docker ps` command only shows running containers by default. To see all containers, use the `--all` (or `-a`) flag."*

```bash
docker ps         # Running containers only
docker ps -a      # All containers (running + stopped)
```

**Official docs example output:**
```
CONTAINER ID   IMAGE         COMMAND                  CREATED       STATUS       PORTS                  NAMES
4bed76d3ad42   nginx:alpine  "/docker-entrypoint.…"   1 second ago  Up 1 second  80/tcp                 test
```

> **Tip from official docs:** `docker ps` groups exposed ports into a single range if possible. A container exposing TCP ports 100, 101, 102 displays `100-102/tcp` in the PORTS column.

---

## 📖 Port Publishing

From the official docs:

> *"When you launched the container, you exposed one of the container's ports onto your machine. Think of this as creating configuration to let you connect through the isolated environment of the container."*

```bash
# Map host port 8080 to container port 80
docker run -d -p 8080:80 nginx
```

**Syntax:** `-p HOST_PORT:CONTAINER_PORT`

**Access the app:** Open `http://localhost:8080` in your browser.

---

## 📖 Executing Commands — `docker exec`

From the official CLI docs, `docker exec` runs a new command in a **running** container. Example from official docs:

```bash
# Create a container with a persistent shell process
docker run --name mycontainer -d -i -t alpine /bin/sh

# Execute a new command in the running container
docker exec -d mycontainer touch /tmp/execWorks

# Open an interactive shell session
docker exec -it mycontainer sh
```

Per the official docs:

> *"The `-d` option sets the container to run in the background, in detached mode. The `-i` option keeps STDIN attached, which prevents the sh process from exiting immediately."*

---

## 📖 Container Lifecycle — Stop, Start, Remove

From the official docs:

> *"Docker containers can be started, stopped and restarted. When you stop a container, it's not removed but the status is changed to stopped and the process inside of the container is stopped."*

```bash
# Stop a running container (sends SIGTERM, then SIGKILL after grace period)
docker stop <container-id>

# Start a stopped container
docker start <container-id>

# Remove a stopped container
docker rm <container-id>
```

> **Tip from official docs:** *"When referencing containers by ID, you don't need to provide the full ID. You only need to provide enough of the ID to make it unique."*

For example, if the container ID is `a1f7a4bb3a27`, you can stop it with:
```bash
docker stop a1f
```

---

## 📖 Environment Variables (`-e`)

From the official CLI docs:

> *"Use the `-e`, `--env`, and `--env-file` flags to set simple environment variables in the container you're running, or overwrite variables defined in the Dockerfile."*

```bash
docker run -e MYSQL_ROOT_PASSWORD=my-secret-pw mysql
docker run --env-file .env myapp
```

---

## 🧪 Practice Tasks (Based on Official Docs Tutorial)

1. Run the official welcome container:
   ```bash
   docker run -d -p 8080:80 docker/welcome-to-docker
   ```
2. Verify it's running: `docker ps`
3. Open `http://localhost:8080` in your browser
4. Stop it: `docker stop <id>` (use first 3 chars of ID)
5. Verify it stopped: `docker ps` (should be empty), then `docker ps -a` (shows stopped)
6. Run Ubuntu interactively:
   ```bash
   docker run -it --name myubuntu ubuntu /bin/bash
   ```
7. Run a command inside it: `cat /etc/os-release`, then `exit`
8. Exec into a running container:
   ```bash
   docker run --name mycontainer -d -i -t alpine /bin/sh
   docker exec -it mycontainer sh
   ```

---

## 🏆 Challenge — Full Container Workflow

```bash
# 1. Run nginx in background, named, with port mapping
docker run --name test -d nginx:alpine

# 2. Check it's running
docker ps

# 3. Stop it (use 3-char ID shorthand)
docker stop test

# 4. Verify status changed (not removed)
docker ps -a   # STATUS: Exited

# 5. Start it again
docker start test

# 6. Exec in and check nginx version
docker exec -it test nginx -v

# 7. Clean up
docker stop test && docker rm test
```

---

## 💬 Interview Questions

1. What does `docker run -d` do and why is it used?
2. How does `docker exec` differ from `docker run`?
3. When you `docker stop` a container, what happens to its data?
4. Why doesn't `docker ps` show a container you just stopped?
5. What does the `--name` flag provide beyond convenience, per the official docs?

---

## 🔗 Official Documentation References

- [Running containers](https://docs.docker.com/engine/containers/run/)
- [docker container run](https://docs.docker.com/reference/cli/docker/container/run/)
- [docker container exec](https://docs.docker.com/reference/cli/docker/container/exec/)
- [docker container ls](https://docs.docker.com/reference/cli/docker/container/ls/)
- [docker container stop](https://docs.docker.com/reference/cli/docker/container/stop/)

---

## 📝 Git Commit Message

```
Day 04: Docker Containers — Documented docker run, ps, exec, stop, start, rm per official CLI reference docs
```
