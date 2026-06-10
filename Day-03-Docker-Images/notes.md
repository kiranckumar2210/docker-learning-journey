# Day 03 — Notes: Docker Images

> Sourced from [docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

---

## Two Core Principles of Images (Official Docs — Must Know)

1. **Immutable** — once created, an image cannot be modified. You can only create a new image or add changes on top
2. **Composed of layers** — each layer is a set of filesystem changes (add, remove, or modify files)

---

## Layer Caching — Why Order Matters

Docker caches each layer. If a layer hasn't changed, Docker reuses the cache. This means:

```dockerfile
# ❌ BAD — code change invalidates the pip install cache every time
COPY . .
RUN pip install -r requirements.txt

# ✅ GOOD — requirements layer is cached; only invalidated when requirements.txt changes
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .                          ← This layer rebuilds on code change, but pip install is cached
```

**Rule:** Copy files that change *least* frequently first.

---

## Docker Hub Image Categories (Official)

| Category | Security Level | Best for |
|---|---|---|
| Docker Official Images | Highest trust, curated | Starting most projects |
| Docker Hardened Images | Near-zero CVEs, minimal | Production security requirements |
| Docker Verified Publishers | Verified by Docker | Commercial software (databases, etc.) |
| Docker-Sponsored Open Source | Community + Docker support | Open-source projects |

---

## Popular Base Images by Use Case

| Use Case | Image | Notes |
|---|---|---|
| Python apps | `python:3.11-slim` | ~130MB — good balance |
| Python apps (minimal) | `python:3.11-alpine` | ~57MB — harder to build some packages |
| Node.js | `node:20-alpine` | Lightweight Node |
| Web server | `nginx:alpine` | Minimal nginx |
| Generic OS | `ubuntu:22.04` | Full Ubuntu — use sparingly |
| Minimal scratch | `alpine:3.18` | ~7MB — bare minimum Linux |
| Debugging | `busybox` | Tiny utility image |

---

## Image ID vs Image Digest

| Identifier | What It Is | Example |
|---|---|---|
| Image ID | Short hash of image config | `eedaff45e3c7` |
| Digest | SHA256 of the full image manifest | `sha256:eedf...` |

The digest is **content-addressable** — if the image contents are unchanged, the digest is predictable. Use digests in production for pinned, reproducible deployments:
```bash
docker pull python@sha256:abc123...
```

---

## `docker image inspect` — Key Fields

```bash
docker image inspect python:3.11-slim
```

Key fields to look for:

| Field | Path in JSON | What It Tells You |
|---|---|---|
| Exposed ports | `.Config.ExposedPorts` | What ports the app uses |
| Default command | `.Config.Cmd` | What runs by default |
| Environment vars | `.Config.Env` | Built-in env vars |
| Working directory | `.Config.WorkingDir` | Default working dir |
| Architecture | `.Architecture` | `amd64`, `arm64`, etc. |
| Size (uncompressed) | `.Size` | Bytes on disk |

---

## Image Size Comparison (from official docs context)

```
REPOSITORY   TAG          SIZE
python       3.11         1.01GB    ← Full Python + build tools
python       3.11-slim    130MB     ← Stripped down
python       3.11-alpine  57.5MB    ← Alpine-based, smallest
```

Use `slim` or `alpine` variants in production. Use full image only if you need build tools.

---

## My Notes

> Record sizes you observed, layers you counted, and anything that surprised you.

- `docker/welcome-to-docker` size:
- `nginx:latest` number of layers:
- `python:3.11-slim` vs `python:3.11` size difference:
-
