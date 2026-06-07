# Day 01 — Notes: Docker Introduction

> Sourced from [docs.docker.com/get-started/docker-overview](https://docs.docker.com/get-started/docker-overview/) and [docs.docker.com/get-started/docker-concepts/the-basics](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

---

## Official Definitions to Memorise

| Term | Official Definition |
|---|---|
| **Docker** | An open platform for developing, shipping, and running applications |
| **Container** | A runnable instance of an image; an isolated process with all the files it needs to run |
| **Image** | A read-only template with instructions for creating a Docker container |
| **Docker Daemon (`dockerd`)** | Listens for Docker API requests; manages images, containers, networks, volumes |
| **Docker Client (`docker`)** | The primary CLI tool; sends commands to `dockerd` via REST API |
| **Docker Registry** | Stores Docker images; Docker Hub is the default public registry |
| **Dockerfile** | A text file with a simple syntax defining steps to create an image |
| **Layer** | Each instruction in a Dockerfile creates a layer in the image |

---

## The Container Lifecycle (Official)

From the docs, a container exists in these states:

```
docker create ──▶ [Created]
docker start  ──▶ [Running]
docker pause  ──▶ [Paused]
docker stop   ──▶ [Exited]
docker rm     ──▶ [Deleted]
```

**Key quote from official docs:**
> "When a container is removed, any changes to its state that aren't stored in persistent storage disappear."

---

## Containers vs VMs — Official Comparison

| Aspect | Container | Virtual Machine |
|---|---|---|
| OS | Shares host OS kernel | Full OS with own kernel |
| Size | Lightweight (MBs) | Heavy (GBs) |
| Startup | Seconds | Minutes |
| Isolation | Process-level (namespaces) | Hardware-level (hypervisor) |
| Use case | Isolate individual apps | Run entire OS environments |

**Official note:** VMs and containers are often used *together* — cloud VMs run multiple containerized apps, maximising resource use.

---

## Docker Architecture — Key Points

- **Client-Server model** — client sends REST API requests, daemon acts on them
- **Communication** — over UNIX sockets or a network interface
- **Docker Compose** is also a Docker client — it lets you work with multi-container applications
- Docker Desktop bundles: daemon, client, Compose, Kubernetes, Credential Helper

---

## Image Layers — How They Work

Per official docs:
- Images are **immutable** — once created, they cannot be modified
- Images are **composed of layers** — each layer = a set of filesystem changes (add, remove, modify files)
- When you rebuild, **only changed layers are rebuilt** — this is what makes Docker fast
- Layers are **shared** between images that use the same base

```
┌──────────────────────────────┐  ← Thin writable container layer (runtime)
├──────────────────────────────┤
│  COPY . .                    │  ← Layer 4 — your app code
├──────────────────────────────┤
│  RUN pip install flask       │  ← Layer 3 — dependencies
├──────────────────────────────┤
│  COPY requirements.txt .     │  ← Layer 2 — dep file
├──────────────────────────────┤
│  FROM python:3.11-slim       │  ← Layer 1 — base image
└──────────────────────────────┘
```

---

## Docker Registries — Types

| Registry | URL | Notes |
|---|---|---|
| Docker Hub | hub.docker.com | Default; public; 100,000+ images |
| Docker Official Images | hub.docker.com/search?badges=official | Curated, most secure |
| Docker Hardened Images | hub.docker.com/hardened-images | Minimal CVEs, production-ready |
| Docker Verified Publishers | hub.docker.com/search?badges=verified_publisher | Commercial, verified |
| AWS ECR | *.dkr.ecr.region.amazonaws.com | AWS deployments |
| GitHub GHCR | ghcr.io | GitHub Actions |

---

## Linux Kernel Features Behind Docker

| Feature | Purpose |
|---|---|
| **Namespaces** | Isolate each container's view of the system (process tree, network, filesystem, users) |
| **cgroups** | Limit and monitor resource usage (CPU, memory, I/O) per container |
| **Union Filesystem** | Layer images efficiently on top of each other (overlay2 driver) |

---

## Common Misconceptions

| Misconception | Reality (per docs) |
|---|---|
| Docker is a VM | Containers share the host kernel; VMs have their own |
| Containers are always secure | They provide isolation, but require proper configuration |
| `latest` tag is always current | It's just a tag; not guaranteed to be the most recent build |
| Removing a container removes its image | Images and containers are separate objects |

---

## My Notes

> Add your personal observations, "aha moments", and questions here.

-
-
-
