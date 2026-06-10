# Day 03 — Docker Images

> **Source:** [docs.docker.com — What is an image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/) · [Understanding image layers](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/) · [docker image CLI reference](https://docs.docker.com/reference/cli/docker/image/)

---

## 🎯 Learning Objectives

- Understand what a Docker image is per the official definition
- Know the two important principles of images (immutable + composed of layers)
- Search, pull, list, inspect, tag, push, save, load, and remove images
- Understand every flag for every `docker image` subcommand
- Understand Docker Hub's image categories (Official, Hardened, Verified, Open Source)

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

## 🛠 Complete `docker image` Command Reference

> The `docker image` command group manages images. Each subcommand below includes its syntax, all flags, examples, and expected output — sourced from the official CLI reference at [docs.docker.com/reference/cli/docker/image](https://docs.docker.com/reference/cli/docker/image/).

---

### 1. `docker image pull` — Download an Image

**Syntax:**
```
docker image pull [OPTIONS] NAME[:TAG|@DIGEST]
Shorthand: docker pull
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all-tags` | `-a` | Download all tagged images in the repository |
| `--disable-content-trust` | | Skip image verification (default: `true`) |
| `--platform` | | Set platform if server is multi-platform capable (e.g. `linux/amd64`) |
| `--quiet` | `-q` | Suppress verbose output |

**Examples:**

```bash
# Pull latest tag (default)
docker image pull nginx
# Output: Using default tag: latest
#         latest: Pulling from library/nginx ...

# Pull a specific tag
docker image pull ubuntu:22.04

# Pull by digest (pinned, reproducible)
docker image pull ubuntu@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d

# Pull all tags from a repository
docker image pull --all-tags ubuntu

# Pull for a specific platform
docker image pull --platform linux/arm64 python:3.11-slim

# Quiet pull (suppresses layer output)
docker image pull --quiet nginx
# Output: docker.io/library/nginx:latest
```

> **From official docs:** *"If no tag is provided, Docker Engine uses the `:latest` tag as a default. Docker uses a content-addressable image store, and the image ID is a SHA256 digest covering the image's configuration and layers."*

---

### 2. `docker image ls` — List Images

**Syntax:**
```
docker image ls [OPTIONS] [REPOSITORY[:TAG]]
Shorthand: docker images
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all` | `-a` | Show all images (including intermediate layers) |
| `--digests` | | Show image digests (SHA256 content hashes) |
| `--filter` | `-f` | Filter output based on conditions |
| `--format` | | Format output using a Go template |
| `--no-trunc` | | Don't truncate output (show full image IDs) |
| `--quiet` | `-q` | Only show image IDs |

**Filter options for `--filter`:**

| Filter | Example | Description |
|---|---|---|
| `dangling` | `dangling=true` | Images with no tag (`<none>:<none>`) |
| `label` | `label=com.example.version` | Images with a specific label |
| `before` | `before=image1` | Images created before a given image |
| `since` | `since=image3` | Images created after a given image |
| `reference` | `reference=ubuntu` | Images whose name matches a pattern |

**Format template fields:** `{{.Repository}}`, `{{.Tag}}`, `{{.ID}}`, `{{.CreatedAt}}`, `{{.Size}}`

**Examples:**

```bash
# List all local images (standard output)
docker image ls
# REPOSITORY    TAG         IMAGE ID       CREATED        SIZE
# nginx         latest      a8758716bb6a   2 weeks ago    187MB
# ubuntu        22.04       3b418d7b466a   5 weeks ago    77.8MB

# List all images including intermediate layers
docker image ls --all

# Show only image IDs
docker image ls --quiet
# a8758716bb6a
# 3b418d7b466a

# Show image digests
docker image ls --digests
# REPOSITORY   TAG      DIGEST                          IMAGE ID       SIZE
# ubuntu       22.04    sha256:26c686...                3b418d7b466a   77.8MB

# Don't truncate IDs
docker image ls --no-trunc

# Filter: dangling images only
docker image ls --filter "dangling=true"

# Filter: images matching a reference pattern
docker image ls --filter reference=ubuntu
# REPOSITORY   TAG     IMAGE ID       CREATED        SIZE
# ubuntu       22.04   8a3cdc4d1ad3   3 weeks ago    77.9MB
# ubuntu       jammy   8a3cdc4d1ad3   3 weeks ago    77.9MB

# Filter: images with a specific label
docker image ls --filter "label=com.example.version"

# Filter: images created before another image
docker image ls --filter "before=nginx:latest"

# Filter: images created after another image
docker image ls --filter "since=ubuntu:22.04"

# Custom table format
docker image ls --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# JSON-style format (one image per line)
docker image ls --format "{{.Repository}}:{{.Tag}} → {{.Size}}"

# List all images for a specific repository
docker image ls ubuntu
```

---

### 3. `docker image inspect` — Show Detailed Image Metadata

**Syntax:**
```
docker image inspect [OPTIONS] IMAGE [IMAGE...]
Shorthand: docker inspect IMAGE
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--format` | `-f` | Format output using a Go template |

**Examples:**

```bash
# Full JSON metadata
docker image inspect nginx

# Inspect multiple images at once
docker image inspect nginx ubuntu python:3.11-slim

# Get the OS and architecture
docker image inspect --format '{{.Os}}/{{.Architecture}}' nginx
# linux/amd64

# Get all exposed ports
docker image inspect --format '{{.Config.ExposedPorts}}' nginx
# map[80/tcp:{}]

# Get the default command
docker image inspect --format '{{.Config.Cmd}}' nginx
# [nginx -g daemon off;]

# Get environment variables
docker image inspect --format '{{.Config.Env}}' python:3.11-slim

# Get the working directory
docker image inspect --format '{{.Config.WorkingDir}}' python:3.11-slim

# Get image labels
docker image inspect --format '{{json .Config.Labels}}' nginx

# Get uncompressed image size in bytes
docker image inspect --format '{{.Size}}' nginx

# Get the image creation date
docker image inspect --format '{{.Created}}' nginx

# Get the number of layers
docker image inspect --format '{{len .RootFS.Layers}}' nginx
```

> **Key JSON fields to explore:** `.Os`, `.Architecture`, `.Config.ExposedPorts`, `.Config.Cmd`, `.Config.Entrypoint`, `.Config.Env`, `.Config.WorkingDir`, `.Config.Labels`, `.RootFS.Layers`, `.Size`, `.Created`

---

### 4. `docker image history` — Show Image Layer History

**Syntax:**
```
docker image history [OPTIONS] IMAGE
Shorthand: docker history
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--format` | | Format using a Go template |
| `--human` | `-H` | Print sizes and dates in human-readable format (default: `true`) |
| `--no-trunc` | | Don't truncate output — shows full commands |
| `--quiet` | `-q` | Only show image IDs of each layer |

**Examples:**

```bash
# View all layers and the commands that created them
docker image history nginx
# IMAGE         CREATED       CREATED BY                     SIZE
# a8758716bb6a  2 weeks ago   CMD ["nginx" "-g" "daemon…"]   0B
# <missing>     2 weeks ago   EXPOSE map[80/tcp:{}]           0B
# <missing>     2 weeks ago   RUN /bin/sh -c set -x ...      61.5MB

# Show full commands without truncation
docker image history --no-trunc nginx

# Show only layer image IDs
docker image history --quiet nginx

# Custom format output
docker image history --format "{{.CreatedBy}}" nginx
```

> **From official docs:** *"`<missing>` in the IMAGE column means the layer comes from the base image and its ID isn't stored locally."*

---

### 5. `docker image tag` — Tag an Image

**Syntax:**
```
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
Shorthand: docker tag
```

**No flags** — this command takes only two arguments.

**Examples:**

```bash
# Tag an image with your Docker Hub username (required before push)
docker image tag ubuntu:22.04 yourusername/myubuntu:latest

# Tag with a version number
docker image tag myapp:latest yourusername/myapp:v1.0.0

# Tag to a different registry
docker image tag nginx:latest ghcr.io/yourorg/nginx:prod

# Apply multiple tags to the same image
docker image tag myapp:latest yourusername/myapp:latest
docker image tag myapp:latest yourusername/myapp:v2
docker image tag myapp:latest yourusername/myapp:v2.1
docker image tag myapp:latest yourusername/myapp:v2.1.0

# Verify tags — all share the same IMAGE ID
docker image ls yourusername/myapp
# REPOSITORY            TAG      IMAGE ID       SIZE
# yourusername/myapp    latest   fd484f19954f   77MB
# yourusername/myapp    v2       fd484f19954f   77MB
# yourusername/myapp    v2.1     fd484f19954f   77MB
```

> **From official docs on `docker image rm`:** *"If an image has multiple tags, using this command with the tag only removes the tag. If the tag is the only one for the image, both the image and the tag are removed."*

---

### 6. `docker image push` — Push an Image to a Registry

**Syntax:**
```
docker image push [OPTIONS] NAME[:TAG]
Shorthand: docker push
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all-tags` | `-a` | Push all tags of the image to the registry |
| `--disable-content-trust` | | Skip image signing (default: `true`) |
| `--quiet` | `-q` | Suppress verbose output |

**Examples:**

```bash
# Login first (required for push)
docker login

# Push a single tag
docker image push yourusername/myapp:v1.0

# Push all tags for an image at once
docker image push --all-tags yourusername/myapp

# Push to a private registry
docker image push registry-host:5000/myname/myimage

# Quiet push
docker image push --quiet yourusername/myapp:latest
```

**Official docs example output:**
```
Using default tag: latest
The push refers to repository [docker.io/yourusername/myapp]
d0e011850342: Pushed
e4e9e9ad93c2: Mounted from library/nginx   ← Layer already in registry
6ac729401225: Mounted from library/nginx
latest: digest: sha256:7f5223ae... size: 1985
```

> **From official docs:** *"The command pushes the image to Docker Hub and automatically creates the repository if it doesn't exist."* Also: *"By default the Docker daemon will push five layers of an image at a time."*

---

### 7. `docker image rm` — Remove Images

**Syntax:**
```
docker image rm [OPTIONS] IMAGE [IMAGE...]
Shorthand: docker rmi
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--force` | `-f` | Force removal of the image (even if tagged in multiple repos) |
| `--no-prune` | | Do not delete untagged parents |

**Examples:**

```bash
# Remove a single image by name
docker image rm nginx

# Remove by tag
docker image rm nginx:latest

# Remove by image ID
docker image rm a8758716bb6a

# Remove multiple images at once
docker image rm nginx ubuntu:22.04 python:3.11-slim

# Force remove (removes all tags on that image ID)
docker image rm --force fd484f19954f

# Remove all images (use with caution)
docker image rm $(docker image ls --quiet)
```

**From official docs — multi-tag behaviour:**
```bash
# If an image has 3 tags pointing to the same ID:
docker image ls
# REPOSITORY   TAG      IMAGE ID
# test1        latest   fd484f19954f
# test         latest   fd484f19954f
# test2        latest   fd484f19954f

# Removing by ID with 3 tags fails without --force
docker rmi fd484f19954f
# Error: Conflict, cannot delete image fd484f19954f because it is tagged in
# multiple repositories, use -f to force

# Remove tags one by one — last one also deletes the image
docker rmi test1:latest    # Untagged: test1:latest
docker rmi test2:latest    # Untagged: test2:latest
docker rmi test:latest     # Untagged: test:latest + Deleted: fd484f19954f...

# OR force-remove all tags at once
docker rmi --force fd484f19954f
# Untagged: test1:latest
# Untagged: test:latest
# Untagged: test2:latest
# Deleted: fd484f19954f...
```

---

### 8. `docker image prune` — Remove Unused Images

**Syntax:**
```
docker image prune [OPTIONS]
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--all` | `-a` | Remove **all** unused images, not just dangling ones |
| `--filter` | | Filter images to remove (e.g. `until=24h`, `label=env=dev`) |
| `--force` | `-f` | Skip confirmation prompt |

**What is a dangling image?** An image with no tag (`<none>:<none>`). This happens when a new build takes the `repo:tag` away from an image ID, leaving it untagged.

**Examples:**

```bash
# Remove only dangling (untagged) images — asks for confirmation
docker image prune

# Remove ALL unused images (not referenced by any container)
docker image prune --all

# Skip confirmation
docker image prune --force

# Remove images older than 24 hours
docker image prune --all --filter "until=24h"

# Remove images older than 240 hours (10 days)
docker image prune --all --force --filter "until=240h"

# Remove images with a specific label
docker image prune --filter "label=env=dev"

# Remove dangling images via batch command (manual approach)
docker image rm $(docker image ls --filter "dangling=true" --quiet)
```

**Official docs example output:**
```
$ docker image prune -a --force --filter "until=240h"
Deleted Images:
untagged: golang:1.7.0
untagged: golang@sha256:67650...
deleted: sha256:138c2e655421...
deleted: sha256:ec353c2e1a67...
```

---

### 9. `docker image save` — Export Image to a Tar Archive

**Syntax:**
```
docker image save [OPTIONS] IMAGE [IMAGE...]
Shorthand: docker save
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--output` | `-o` | Write to a file instead of STDOUT |

**Examples:**

```bash
# Save to a file
docker image save --output nginx.tar nginx

# Shorthand
docker save -o nginx.tar nginx

# Save multiple images into one archive
docker save -o myimages.tar nginx ubuntu:22.04 python:3.11-slim

# Save to STDOUT and pipe to gzip for compression
docker save nginx | gzip > nginx.tar.gz

# Save to STDOUT and pipe to a remote host
docker save nginx | ssh user@remote "docker load"
```

> **Use case:** Transfer images in air-gapped environments, backup images before removal, or share images without a registry.

---

### 10. `docker image load` — Import Image from a Tar Archive

**Syntax:**
```
docker image load [OPTIONS]
Shorthand: docker load
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--input` | `-i` | Read from a tar archive file (instead of STDIN) |
| `--platform` | | Load a specific platform variant (if archive is multi-platform) |
| `--quiet` | `-q` | Suppress load output |

**Examples:**

```bash
# Load from a file
docker image load --input nginx.tar
# Loaded image: nginx:latest

# Shorthand
docker load -i nginx.tar

# Load from STDIN (pipe from gzip)
docker load < nginx.tar.gz
# Loaded image: nginx:latest

# Load a multi-image archive
docker load --input myimages.tar
# Loaded image: nginx:latest
# Loaded image: ubuntu:22.04
# Loaded image: python:3.11-slim

# Load a specific platform variant
docker load --platform linux/arm64 --input multiarch.tar
```

**Official docs example output:**
```
$ docker load --input fedora.tar
Loaded image: fedora:rawhide
Loaded image: fedora:20
$ docker images
REPOSITORY   TAG        IMAGE ID       CREATED        SIZE
fedora       rawhide    0d20aec6529d   7 weeks ago    387 MB
fedora       20         58394af37342   7 weeks ago    385.5 MB
```

> **From official docs:** *"Load an image or repository from a tar archive (even if compressed with gzip, bzip2, xz or zstd) from a file or STDIN. It restores both images and tags."*

---

### 11. `docker image import` — Import a Tarball as an Image

**Syntax:**
```
docker image import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
Shorthand: docker import
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--change` | `-c` | Apply a Dockerfile instruction to the created image |
| `--message` | `-m` | Set a commit message for the imported image |
| `--platform` | | Set platform for the imported image (e.g. `linux/amd64`) |

> **`import` vs `load`:** `docker load` restores a full image (with all layers and metadata) from a `docker save` archive. `docker import` creates a **new single-layer image** from a raw filesystem tarball (e.g. exported with `docker export`). Use `load` for Docker images; use `import` for custom root filesystems.

**Examples:**

```bash
# Import a filesystem tarball as a new image
docker image import mycontainer.tar myimage:v1

# Import from URL
docker image import https://example.com/rootfs.tar.gz myimage:latest

# Import with Dockerfile instructions applied
docker image import --change "ENV DEBUG=false" myfs.tar myimage:prod
docker image import --change "CMD [\"python3\", \"app.py\"]" myfs.tar myapp:v1

# Set a commit message
docker image import --message "Imported from legacy server" myfs.tar myimage:legacy

# Import for a different platform
docker image import --platform linux/arm64 armfs.tar armimage:v1

# Import and view history (the comment field appears)
docker image history exampleimagelocal:new
# IMAGE         CREATED        CREATED BY   SIZE    COMMENT
# 25e54c0df7dc  2 minutes ago               53.6MB  New image imported from tarball
```

---

### 12. `docker search` — Search Docker Hub

**Syntax:**
```
docker search [OPTIONS] TERM
```

**Flags:**

| Flag | Short | Description |
|---|---|---|
| `--filter` | `-f` | Filter results (`is-official=true`, `stars=100`) |
| `--format` | | Format output using a Go template |
| `--limit` | | Maximum number of results (default: 25) |
| `--no-trunc` | | Don't truncate descriptions |

**Examples:**

```bash
# Basic search
docker search nginx
# NAME      DESCRIPTION                STARS   OFFICIAL
# nginx     Official build of Nginx.   19000   [OK]

# Filter to official images only
docker search --filter is-official=true python

# Filter by minimum stars
docker search --filter stars=1000 ubuntu

# Limit to 5 results
docker search --limit 5 redis

# Don't truncate descriptions
docker search --no-trunc nginx

# Custom format
docker search --format "{{.Name}}\t{{.StarCount}}\t{{.IsOfficial}}" nginx
```

---

## 📖 `docker image ls` — Official Output Format

```
REPOSITORY                 TAG       IMAGE ID       CREATED        SIZE
docker/welcome-to-docker   latest    eedaff45e3c7   4 months ago   29.7MB
ubuntu                     22.04     3b418d7b466a   5 weeks ago    77.8MB
python                     3.11-slim f2faed6e1741   3 weeks ago    130MB
```

| Column | Description |
|---|---|
| `REPOSITORY` | Image name (namespace/name or official name) |
| `TAG` | Version label; `<none>` for dangling images |
| `IMAGE ID` | Short form of the SHA256 content hash (12 chars) |
| `CREATED` | When the image was built |
| `SIZE` | Uncompressed size on disk |

> **From official docs:** *"Docker uses a content-addressable image store, and the image ID is a SHA256 digest. In the example above, `debian:bookworm` and `debian:latest` have the same image ID because they are the same image tagged with different names. Because they are the same image, their layers are stored only once."*

---

## 📖 All `docker image` Subcommands — Quick Reference Table

| Command | Shorthand | What It Does |
|---|---|---|
| `docker image pull` | `docker pull` | Download an image from a registry |
| `docker image ls` | `docker images` | List local images |
| `docker image inspect` | `docker inspect` | Show full JSON metadata |
| `docker image history` | `docker history` | Show layer-by-layer build history |
| `docker image tag` | `docker tag` | Add a new name/tag to an image |
| `docker image push` | `docker push` | Upload an image to a registry |
| `docker image rm` | `docker rmi` | Remove one or more images |
| `docker image prune` | — | Remove dangling or all unused images |
| `docker image save` | `docker save` | Export image to a `.tar` archive |
| `docker image load` | `docker load` | Import image from a `.tar` archive |
| `docker image import` | `docker import` | Create image from a filesystem tarball |
| `docker search` | — | Search Docker Hub for images |
| `docker image build` | `docker build` | Build image from a Dockerfile (Day 8) |

---

## 🧪 Practice Tasks

1. Pull `docker/welcome-to-docker` — observe layer-by-layer download output
2. Pull `python:3.11`, `python:3.11-slim`, `python:3.11-alpine` — compare sizes with `docker image ls`
3. Run `docker image history nginx --no-trunc` — read what each layer does
4. Use `docker image inspect` with `--format` to extract: exposed port, default cmd, OS/arch
5. Tag `ubuntu:22.04` as `myubuntu:day03` — verify same IMAGE ID in `docker image ls`
6. Save `nginx` to a tar file, remove the local image, then load it back
7. Run `docker image prune --all --dry-run` to see what would be removed

---

## 🏆 Challenge — Full Image Lifecycle

```bash
# 1. Pull
docker image pull ubuntu:22.04

# 2. Inspect — get architecture and exposed ports
docker image inspect --format '{{.Os}}/{{.Architecture}}' ubuntu:22.04
docker image inspect --format '{{.Config.ExposedPorts}}' ubuntu:22.04

# 3. View layers
docker image history ubuntu:22.04

# 4. Tag for "push" (don't need to actually push)
docker image tag ubuntu:22.04 yourusername/myubuntu:v1

# 5. Verify same IMAGE ID for both tags
docker image ls | grep ubuntu

# 6. Save to disk
docker image save --output myubuntu.tar ubuntu:22.04

# 7. Remove local image
docker image rm ubuntu:22.04

# 8. Verify it's gone
docker image ls | grep ubuntu

# 9. Load it back
docker image load --input myubuntu.tar

# 10. Verify it's restored
docker image ls | grep ubuntu

# 11. Cleanup
docker image rm ubuntu:22.04 yourusername/myubuntu:v1
rm myubuntu.tar
```

---

## 💬 Interview Questions

1. What are the two important principles of Docker images per the official docs?
2. What is the difference between `docker image save`/`load` and `docker image import`?
3. What is a dangling image, and how is it created?
4. When you remove one tag from an image with multiple tags, is the image deleted?
5. Why should you use `--no-trunc` with `docker image history`?
6. What does `--filter reference=ubuntu` do in `docker image ls`?
7. What is the difference between `--all-tags` in `docker pull` vs `docker push`?

---

## 🔗 Official Documentation References

- [What is an image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)
- [Understanding image layers](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/)
- [docker image pull](https://docs.docker.com/reference/cli/docker/image/pull/)
- [docker image ls](https://docs.docker.com/reference/cli/docker/image/ls/)
- [docker image inspect](https://docs.docker.com/reference/cli/docker/image/inspect/)
- [docker image history](https://docs.docker.com/reference/cli/docker/image/history/)
- [docker image tag](https://docs.docker.com/reference/cli/docker/image/tag/)
- [docker image push](https://docs.docker.com/reference/cli/docker/image/push/)
- [docker image rm](https://docs.docker.com/reference/cli/docker/image/rm/)
- [docker image prune](https://docs.docker.com/reference/cli/docker/image/prune/)
- [docker image save](https://docs.docker.com/reference/cli/docker/image/save/)
- [docker image load](https://docs.docker.com/reference/cli/docker/image/load/)
- [docker image import](https://docs.docker.com/reference/cli/docker/image/import/)

---

## 📝 Git Commit Message

```
Day 03: Docker Images — Complete image command reference with all flags, examples, and official docs output
```
