# 🗺 Docker Learning Roadmap

## Overview

This roadmap breaks the 15-day journey into two phases, each building on the previous.

---

## Phase 1 — Foundations (Days 1–7)

```
Day 1  ──▶  Day 2  ──▶  Day 3  ──▶  Day 4
What is     Install     Images      Containers
Docker?     Docker      Lifecycle   Lifecycle

Day 5  ──▶  Day 6  ──▶  Day 7
Container   Docker Hub  Revision
Lifecycle   & Registry  + Mini Lab
(deep dive)
```

### Week 1 Goals
- [ ] Understand containers vs VMs
- [ ] Install and configure Docker
- [ ] Pull, inspect, and manage images
- [ ] Run, exec, stop, remove containers
- [ ] Push an image to Docker Hub
- [ ] Complete Week 1 mini-lab

---

## Phase 2 — Production Skills (Days 8–15)

```
Day 8  ──▶  Day 9  ──▶  Day 10 ──▶  Day 11
Dockerfile  Dockerfile  Custom      Env Vars
Basics      Advanced    Images      & Secrets

Day 12 ──▶  Day 13 ──▶  Day 14 ──▶  Day 15
Volumes     Networking  Docker      Final
& Mounts                Compose     Project
```

### Week 2 Goals
- [ ] Write Dockerfiles from scratch
- [ ] Use multi-stage builds
- [ ] Manage volumes and bind mounts
- [ ] Configure Docker networks
- [ ] Write Docker Compose files
- [ ] Deploy multi-container application

---

## Skill Progression Map

```
BEGINNER                    INTERMEDIATE               ADVANCED (beyond scope)
─────────────────────────────────────────────────────────────────────────────
docker run              │   Dockerfile               │   Kubernetes
docker ps               │   docker build             │   Docker Swarm
docker pull             │   Multi-stage builds       │   CI/CD Pipelines
docker images           │   Volumes                  │   Container Security
docker stop             │   Networks                 │   Orchestration
docker rm               │   Docker Compose           │   Monitoring
docker logs             │   Env variables            │   Secrets management
Docker Hub              │   Health checks            │   Production hardening
                        │   Multi-service apps       │
```

---

## Daily Time Commitment

| Activity | Estimated Time |
|---|---|
| Reading notes | 20–30 min |
| Practicing commands | 30–45 min |
| Mini lab / exercises | 30–45 min |
| Challenge tasks | 20–30 min |
| **Total per day** | **~2 hours** |

---

## Prerequisites

- Basic Linux command line comfort (cd, ls, cat, mkdir)
- A computer running Linux, macOS, or Windows (WSL2)
- An internet connection for pulling images
- A free Docker Hub account (for Day 6+)

---

## After This Journey

Once you complete Day 15, you're ready to:

1. **Kubernetes** — Container orchestration at scale
2. **CI/CD Integration** — GitHub Actions + Docker
3. **Docker Swarm** — Native Docker clustering
4. **Container Security** — Image scanning, rootless containers
5. **Monitoring** — Prometheus + Grafana with Docker

---

*Each day builds on the previous. Do not skip days — the labs reference earlier concepts.*
