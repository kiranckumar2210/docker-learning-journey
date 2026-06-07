# Day 01 — Commands

> No hands-on commands on Day 1 (installation is Day 2). Study these — you will run them on Day 2.

---

## Your First Container (Day 2 Preview)

From the official Docker docs — the canonical first command:

```bash
docker run -i -t ubuntu /bin/bash
```

**What the official docs say happens:**

1. Docker checks if `ubuntu` image exists locally — if not, pulls it from Docker Hub
2. Docker creates a new container
3. Docker allocates a read-write filesystem layer to the container
4. Docker creates a network interface and assigns an IP address
5. Docker starts the container and executes `/bin/bash`
6. Because `-i` and `-t` are set, you can interact via keyboard
7. When you `exit`, the container stops but is **not removed**

---

## Verify Installation (Day 2)

```bash
# Show Docker client and server versions
docker version

# Show complete system information
docker info

# Run the classic hello-world test
docker run hello-world
```

---

## The "Welcome to Docker" Container (from official docs)

The official Docker documentation uses this command to introduce containers:

```bash
# Pull and run — maps host port 8080 to container port 80
docker run -d -p 8080:80 docker/welcome-to-docker
```

**Verify it's running:**
```bash
docker ps
```

**Expected output from official docs:**
```
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                      NAMES
a1f7a4bb3a27   docker/welcome-to-docker   "/docker-entrypoint.…"   11 seconds ago   Up 11 seconds   0.0.0.0:8080->80/tcp       gracious_keldysh
```

**Stop the container** (official docs: you only need the first few characters of the ID):
```bash
docker stop a1f
```

> Tip from official docs: *"When referencing containers by ID, you don't need to provide the full ID. You only need to provide enough of the ID to make it unique."*

---

## Commands Reference Table (Day 1 Study)

| Command | What It Does | Official Docs |
|---|---|---|
| `docker run` | Create and start a container | [CLI reference](https://docs.docker.com/reference/cli/docker/container/run/) |
| `docker ps` | List running containers | [CLI reference](https://docs.docker.com/reference/cli/docker/container/ls/) |
| `docker ps -a` | List all containers (including stopped) | [CLI reference](https://docs.docker.com/reference/cli/docker/container/ls/) |
| `docker stop` | Stop a running container | [CLI reference](https://docs.docker.com/reference/cli/docker/container/stop/) |
| `docker pull` | Download an image from a registry | [CLI reference](https://docs.docker.com/reference/cli/docker/image/pull/) |
| `docker images` | List local images | [CLI reference](https://docs.docker.com/reference/cli/docker/image/ls/) |
| `docker version` | Show client and server versions | — |
| `docker info` | Show system-wide information | — |

---

## Screenshot Placeholder

> `[Screenshot: docker run hello-world — full output showing the 4 steps Docker took]`
> `[Screenshot: docker ps showing welcome-to-docker container running on port 8080]`
