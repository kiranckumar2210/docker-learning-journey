# Day 03 — Docker Images

> **Source:** [docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/) · [docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/)

---

## 🎯 Learning Objectives

- Understand what a Docker image is per the official definition
- Know the two important principles of images (immutable + composed of layers)
- Search, pull, list, and inspect images
- Understand Docker Hub's image categories (Official, Hardened, Verified, Open Source)
- Tag and remove images

---

## 📖 What is a Docker Image?

From the official Docker documentation:

> *"A container image is a standardized package that includes all of the files, binaries, libraries, and configurations to run a container."*

Two important principles per the official docs:

> *"1. Images are immutable. Once an image is created, it can't be modified. You can only make a new image or add changes on top of it."*
>
> *"2. Container images are composed of layers. Each layer represents a set of file system changes that add, remove, or modify files."*

These two principles let you **extend or add to existing images**. For example, if you're building a Python app, you start from the Python image and add layers for your app's dependencies and code — letting you focus on your app, not Python itself.

---

## 📖 Image Layers — How They Work

Per the official docs, when you run `docker pull docker/welcome-to-docker`:

```
Using default tag: latest
latest: Pulling from docker/welcome-to-docker
579b34f0a95b: Download complete       ← Each line = one layer
d11a451e6399: Download complete
1c2214f9937c: Download complete
b42a2f288f4d: Download complete
...
Status: Downloaded newer image for docker/welcome-to-docker:latest
```

> *"Each line represents a different downloaded layer of the image. Remember that each layer is a set of filesystem changes and provides functionality of the image."*

### Layer Stack Example

```
┌──────────────────────────────┐  ← Thin writable container layer (created on docker run)
├──────────────────────────────┤
│  COPY . .                    │  ← App code layer
├──────────────────────────────┤
│  RUN pip install -r req.txt  │  ← Dependencies layer
├──────────────────────────────┤
│  COPY requirements.txt .     │  ← Requirements file layer
├──────────────────────────────┤
│  FROM python:3.11-slim       │  ← Base image layer(s)
└──────────────────────────────┘
```

Layers are **cached and shared**. If two images use the same base, Docker stores only one copy of those base layers.

---

## 📖 Finding Images — Docker Hub

Per the official docs, Docker Hub has over **100,000 images** and provides four categories of trusted content:

| Category | Description |
|---|---|
| **Docker Official Images** | Curated set of repositories; starting point for most users; most secure on Docker Hub |
| **Docker Hardened Images** | Minimal, secure, production-ready with near-zero CVEs; free under Apache 2.0 |
| **Docker Verified Publishers** | High-quality images from commercial publishers verified by Docker |
| **Docker-Sponsored Open Source** | Published by open-source projects sponsored by Docker |

**Examples from official docs:**
- `redis` and `memcached` — ready-to-run Docker Official Images
- `node`, `python` — base images to start from and add your own files
- Docker Hardened Images — minimal variants of Node.js, Python, Go for production

---

## 📖 Image Naming Convention

```
[registry/][namespace/]image-name[:tag]

Examples:
ubuntu                          → Official image, implicit :latest tag
ubuntu:22.04                    → Official image, specific tag
python:3.11-slim                → Slim variant (reduced size)
docker/welcome-to-docker        → Namespace/image, implicit :latest
myusername/myapp:v1.0           → User image with version tag
ghcr.io/owner/repo:latest       → GitHub Container Registry
```

**About tags:** Tags default to `latest` when omitted. Per best practice, always specify a version tag in production — `latest` is a moving target.

---

## 📖 docker image ls — Official Output Format

From the official CLI docs:

```bash
docker image ls
```

```
REPOSITORY                 TAG       IMAGE ID       CREATED        SIZE
docker/welcome-to-docker   latest    eedaff45e3c7   4 months ago   29.7MB
ubuntu                     22.04     3b418d7b466a   5 weeks ago    77.8MB
python                     3.11-slim f2faed6e1741   3 weeks ago    130MB
```

Fields: `REPOSITORY` (image name), `TAG` (version), `IMAGE ID` (content hash, first 12 chars), `CREATED`, `SIZE`.

---

## 🛠 Key Image Commands

See [commands.md](./commands.md) for full syntax and expected outputs.

| Command | What It Does |
|---|---|
| `docker pull nginx` | Download image from registry |
| `docker search nginx` | Search Docker Hub |
| `docker image ls` | List local images |
| `docker image inspect nginx` | Full JSON metadata |
| `docker history nginx` | Show image layers |
| `docker tag nginx:latest myrepo/nginx:v1` | Tag an image |
| `docker rmi nginx` | Remove image |
| `docker image prune` | Remove dangling images |

---

## 🧪 Practice Tasks

1. Pull the official `docker/welcome-to-docker` image (from official docs)
2. Pull `python:3.11-slim` and `python:3.11` — compare sizes with `docker image ls`
3. Run `docker pull ubuntu:22.04` — observe each layer downloading
4. Inspect the nginx image: `docker image inspect nginx` — find the exposed port
5. View `docker history nginx` — count the layers
6. Tag `ubuntu:22.04` as `myubuntu:day03` and verify with `docker image ls`
7. Remove `myubuntu:day03` with `docker rmi`

---

## 🏆 Challenge — From Official Docs Tutorial

Complete the official docs hands-on:

```bash
# Step 1: Search for the image
docker search docker/welcome-to-docker

# Step 2: Pull it
docker pull docker/welcome-to-docker

# Step 3: List images and note the SIZE
docker image ls

# Step 4: Inspect the image
docker image inspect docker/welcome-to-docker

# Step 5: View layers
docker history docker/welcome-to-docker
```

Answer these:
1. How many layers does `docker/welcome-to-docker` have?
2. What is its size?
3. What is the image's `Cmd` (default command)?

---

## 💬 Interview Questions

1. What are the two important principles of Docker images per the official docs?
2. What is a Docker image layer?
3. What is the difference between Docker Official Images and Docker Hardened Images?
4. Why should you avoid using the `latest` tag in production?
5. What happens to image layers when you change your Dockerfile and rebuild?

---

## 🔗 Official Documentation References

- [What is an image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)
- [Understanding image layers](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/)
- [Build, tag, and publish an image](https://docs.docker.com/get-started/docker-concepts/building-images/build-tag-and-publish-an-image/)
- [docker image ls reference](https://docs.docker.com/reference/cli/docker/image/ls/)

---

## 📝 Git Commit Message

```
Day 03: Docker Images — Documented image layers, Docker Hub categories, pull/inspect/tag/remove per official docs
```
