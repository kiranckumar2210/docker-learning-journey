# Day 03 — Commands: Docker Images

> Commands sourced and cross-referenced with [docs.docker.com/reference/cli/docker/image](https://docs.docker.com/reference/cli/docker/image/)

---

## Search Docker Hub

```bash
# Search Docker Hub from CLI
docker search nginx

# Filter to Official Images only
docker search --filter is-official=true python

# Limit results
docker search --limit 5 redis
```

**Official docs example output:**
```
NAME      DESCRIPTION                STARS   OFFICIAL
nginx     Official build of Nginx.   19000   [OK]
```

---

## Pull Images

```bash
# Pull latest (implicit tag)
docker pull nginx

# Pull specific tag
docker pull ubuntu:22.04

# Pull the welcome-to-docker image (from official docs tutorial)
docker pull docker/welcome-to-docker
```

**Official docs pull output:**
```
Using default tag: latest
latest: Pulling from docker/welcome-to-docker
579b34f0a95b: Download complete
d11a451e6399: Download complete
1c2214f9937c: Download complete
...
Digest: sha256:eedaff45e3c78538...
Status: Downloaded newer image for docker/welcome-to-docker:latest
docker.io/docker/welcome-to-docker:latest
```

---

## List Images

```bash
# List all local images (official syntax)
docker image ls

# Equivalent shorthand
docker images

# Show only image IDs
docker images -q

# Filter dangling images (no tag)
docker images --filter "dangling=true"

# Custom format
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

**Official docs output example:**
```
REPOSITORY                 TAG       IMAGE ID       CREATED        SIZE
docker/welcome-to-docker   latest    eedaff45e3c7   4 months ago   29.7MB
```

---

## Inspect Images

```bash
# Full JSON metadata
docker image inspect nginx

# Get specific fields with Go template
docker inspect --format='{{.Config.ExposedPorts}}' nginx
# Output: map[80/tcp:{}]

docker inspect --format='{{.Config.Cmd}}' nginx
# Output: [nginx -g daemon off;]

docker inspect --format='{{.Architecture}}' python:3.11-slim
# Output: amd64
```

---

## View Image History (Layers)

```bash
docker history nginx
```

**Output:**
```
IMAGE         CREATED       CREATED BY                                  SIZE
a8758716bb6a  2 weeks ago   CMD ["nginx" "-g" "daemon off;"]            0B
<missing>     2 weeks ago   EXPOSE map[80/tcp:{}]                       0B
<missing>     2 weeks ago   ENV NGINX_VERSION=1.25.2                    0B
<missing>     2 weeks ago   RUN /bin/sh -c set -x && groupadd...        61.5MB
```

Each row is a layer. `<missing>` means the layer comes from the base image and its ID isn't stored locally.

---

## Tag Images

```bash
# Tag an existing image with a new name:tag
docker tag ubuntu:22.04 myubuntu:day03

# Tag for Docker Hub push (must include your username)
docker tag nginx:latest yourusername/nginx:v1.0

# Verify the tag was created
docker images | grep myubuntu
```

---

## Remove Images

```bash
# Remove by name
docker rmi nginx
docker image rm nginx:latest

# Force remove (even if a stopped container uses it)
docker rmi -f nginx

# Remove multiple
docker rmi nginx ubuntu python

# Remove all dangling (untagged) images
docker image prune

# Remove ALL unused images (not just dangling)
docker image prune -a

# Remove all images (caution!)
docker rmi $(docker images -q)
```

**Official docs filter example:**
```bash
# Remove dangling images using filter
docker rmi $(docker images -f "dangling=true" -q)
```

---

## Compare Image Sizes

```bash
# Pull variants and compare
docker pull python:3.11
docker pull python:3.11-slim
docker pull python:3.11-alpine

docker images python
```

**Expected output:**
```
REPOSITORY   TAG          IMAGE ID       CREATED        SIZE
python       3.11         ccc7a11d65b1   3 weeks ago    1.01GB
python       3.11-slim    f2faed6e1741   3 weeks ago    130MB
python       3.11-alpine  b0bf2e1ce43e   3 weeks ago    57.5MB
```

---

## Screenshot Placeholders

> `[Screenshot: docker image ls showing multiple images with sizes]`
> `[Screenshot: docker pull docker/welcome-to-docker showing each layer downloading]`
> `[Screenshot: docker history nginx showing all layers]`
> `[Screenshot: docker images python showing size comparison across variants]`
