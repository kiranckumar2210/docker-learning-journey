# Day 01 — Docker Introduction

> **Source:** [docs.docker.com/get-started/docker-overview](https://docs.docker.com/get-started/docker-overview/) · [docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

---

## 🎯 Learning Objectives

- Understand what Docker is and the problem it solves
- Know the four properties of containers (self-contained, isolated, independent, portable)
- Understand Docker's client-server architecture
- Distinguish between images and containers
- Know all Docker objects: images, containers, networks, volumes

---

## 📖 What is Docker?

As per the official Docker documentation:

> *"Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly."*

Docker packages and runs applications in a loosely isolated environment called a **container**. The isolation and security lets you run many containers simultaneously on a single host. Containers are **lightweight** and contain everything needed to run the application — you don't need to rely on what's installed on the host.

**The key result:** You can significantly reduce the delay between writing code and running it in production.

---

## 📖 What is a Container?

Per docs.docker.com:

> *"Simply put, containers are isolated processes for each of your app's components."*

Imagine a web app with three components — a React frontend, a Python API, and a PostgreSQL database. Each component runs in its own **isolated environment**, completely separate from everything else on your machine.

### The Four Properties of Containers (Official Docs)

| Property | Meaning |
|---|---|
| **Self-contained** | Each container has everything it needs to function — no reliance on pre-installed host dependencies |
| **Isolated** | Containers run in isolation, with minimal influence on the host and other containers |
| **Independent** | Each container is independently managed. Deleting one doesn't affect others |
| **Portable** | A container that runs on your development machine will work the same in a data center or the cloud |

### Containers vs Virtual Machines

Per the official Docker docs:

> *"A VM is an entire operating system with its own kernel, hardware drivers, programs, and applications. Spinning up a VM only to isolate a single application is a lot of overhead. A container is simply an isolated process with all of the files it needs to run. If you run multiple containers, they all share the same kernel, allowing you to run more applications on less infrastructure."*

```
VIRTUAL MACHINE                        CONTAINER
────────────────────                   ────────────────────
┌──────────────────┐                   ┌──────────────────┐
│   Application    │                   │   Application    │
├──────────────────┤                   ├──────────────────┤
│   Guest OS       │                   │  Container Libs  │
│   (Full Kernel)  │                   │  (Minimal)       │
├──────────────────┤                   ├──────────────────┤
│   Hypervisor     │                   │  Docker Engine   │
├──────────────────┤                   ├──────────────────┤
│   Host OS        │                   │  Host OS Kernel  │
└──────────────────┘                   └──────────────────┘
Size: GBs, Startup: Minutes            Size: MBs, Startup: Seconds
```

> **Note from docs:** VMs and containers are often used *together*. In a cloud environment, the provisioned machines are typically VMs — but instead of one VM per application, a VM with a container runtime runs multiple containerized apps, increasing utilization and reducing costs.

---

## 📖 Docker Architecture

Per the official docs:

> *"Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface."*

```
┌──────────────────────────────────────────────────────┐
│                    Docker Host                        │
│                                                       │
│  ┌─────────────────────────────────────────────────┐ │
│  │            Docker Daemon (dockerd)               │ │
│  │  Listens for API requests                        │ │
│  │  Manages: images, containers, networks, volumes  │ │
│  └─────────────────────┬───────────────────────────┘ │
│                         │  REST API (UNIX socket)     │
│  ┌──────────────────────▼──────────────────────────┐ │
│  │             Docker Client (docker)               │ │
│  │  Primary way users interact with Docker          │ │
│  │  Sends commands (e.g. docker run) to dockerd     │ │
│  └─────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
                         │
              ┌──────────▼──────────┐
              │   Docker Registry   │
              │   (Docker Hub)      │
              │   Stores images     │
              └─────────────────────┘
```

### Key Components (from official docs)

**Docker Daemon (`dockerd`)**
Listens for Docker API requests and manages Docker objects — images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

**Docker Client (`docker`)**
The primary way users interact with Docker. When you run `docker run`, the client sends this command to `dockerd`, which carries it out. The client can communicate with more than one daemon.

**Docker Desktop**
An application for Mac, Windows, or Linux that includes: the Docker daemon, the Docker client, Docker Compose, Kubernetes, and Credential Helper.

**Docker Registries**
A Docker registry stores Docker images. Docker Hub is the default public registry. When you use `docker pull` or `docker run`, Docker pulls images from your configured registry. When you use `docker push`, it pushes your image to the registry.

---

## 📖 Docker Objects

When you use Docker, you create and use the following objects:

### Images

Per the official docs:

> *"An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization."*

To build your own image, you create a **Dockerfile**. Each instruction in a Dockerfile creates a **layer** in the image. When you change the Dockerfile and rebuild, only changed layers are rebuilt — making images lightweight and fast.

### Containers

Per the official docs:

> *"A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI."*

> *"When a container is removed, any changes to its state that aren't stored in persistent storage disappear."*

A container is defined by:
1. Its image
2. Any configuration options you provide when you create or start it

### What Happens When You Run `docker run -i -t ubuntu /bin/bash`

The official docs describe the exact sequence:

1. If `ubuntu` image isn't local, Docker pulls it from the configured registry (as if you ran `docker pull ubuntu`)
2. Docker creates a new container (as if you ran `docker container create`)
3. Docker allocates a read-write filesystem to the container as its final layer
4. Docker creates a network interface and assigns an IP address
5. Docker starts the container and executes `/bin/bash`
6. Because the container is running interactively (`-i` `-t`), you can type commands and see output
7. When you `exit`, the container stops but is not removed

---

## 📖 The Underlying Technology

Per the official docs:

> *"Docker is written in the Go programming language and takes advantage of several features of the Linux kernel. Docker uses a technology called `namespaces` to provide the isolated workspace called the container. When you run a container, Docker creates a set of namespaces for that container. These namespaces provide a layer of isolation."*

**Linux namespaces used by Docker:**

| Namespace | Isolates |
|---|---|
| `pid` | Process IDs |
| `net` | Network interfaces |
| `mnt` | Filesystem mount points |
| `uts` | Hostname and domain name |
| `ipc` | Interprocess communication |
| `user` | User and group IDs |

---

## 📖 What Can You Use Docker For?

From the official docs, three primary use cases:

**1. Fast, consistent delivery**
Developers write code locally and share work via Docker containers. They push to test environments and run CI/CD pipelines. Getting a fix to customers is as simple as pushing the updated image to production.

**2. Responsive deployment and scaling**
Docker containers can run on a developer's laptop, in a data center, on cloud providers, or in a hybrid of environments. Docker's portability makes it easy to dynamically scale workloads in near real time.

**3. Running more workloads on the same hardware**
Docker is lightweight and fast — a cost-effective alternative to hypervisor-based VMs. Perfect for high-density environments where you need to do more with fewer resources.

---

## ✅ Day 1 Checklist

- [ ] I can explain what Docker is in my own words
- [ ] I can list the four properties of containers
- [ ] I can explain how a container differs from a VM
- [ ] I can describe the Docker daemon, client, and registry
- [ ] I understand what happens step-by-step when `docker run` is executed
- [ ] I know what Linux namespaces are used for

---

## 💬 Interview Questions

1. What is Docker and what problem does it solve? *(Official answer: open platform for developing, shipping, running apps; separates apps from infrastructure)*
2. What are the four properties of containers per Docker's documentation?
3. How does Docker's client-server architecture work?
4. What happens under the hood when you run `docker run -it ubuntu bash`?
5. What Linux kernel feature provides container isolation?

---

## 🔗 Official Documentation References

- [What is Docker?](https://docs.docker.com/get-started/docker-overview/)
- [What is a Container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)
- [What is an Image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

---

## 📝 Git Commit Message

```
Day 01: Docker Introduction — Documented Docker overview, container vs VM, architecture, and objects per official docs
```
