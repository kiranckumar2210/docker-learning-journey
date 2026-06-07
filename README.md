# 🐳 Docker Learning Journey — 15 Days from Zero to Job-Ready

> **A structured, portfolio-grade Docker learning repository** documenting a complete 15-day journey from absolute beginner to intermediate Docker practitioner — built to impress recruiters, hiring managers, and engineering teams.

---

## 📌 Repository at a Glance

| Attribute | Details |
|---|---|
| 📅 Duration | 15 Days |
| 🎯 Level | Beginner → Intermediate |
| 🛠 Tech Stack | Docker, Docker Compose, Python, Flask, Redis, Nginx |
| 📂 Content | Notes, Commands, Dockerfiles, Compose files, Labs, Projects |
| 🎓 Goal | Portfolio-ready DevOps skills |

---

## 🎯 Learning Goals

- Understand containerization and why Docker matters in modern software delivery
- Master Docker CLI — images, containers, volumes, networks
- Write production-quality Dockerfiles and Docker Compose configurations
- Deploy a multi-service application (Flask + Redis + Nginx)
- Prepare confidently for Docker interview questions at beginner/intermediate level

---

## 📊 Progress Tracker

| Day | Topic | Status |
|-----|-------|--------|
| Day 01 | Docker Introduction & Concepts | ⬜ |
| Day 02 | Docker Installation & Setup | ⬜ |
| Day 03 | Docker Images | ⬜ |
| Day 04 | Docker Containers | ⬜ |
| Day 05 | Container Lifecycle | ⬜ |
| Day 06 | Docker Hub & Registry | ⬜ |
| Day 07 | Revision & Mini Lab | ⬜ |
| Day 08 | Dockerfile Basics | ⬜ |
| Day 09 | Dockerfile Advanced | ⬜ |
| Day 10 | Building Custom Images | ⬜ |
| Day 11 | Environment Variables & Secrets | ⬜ |
| Day 12 | Volumes & Bind Mounts | ⬜ |
| Day 13 | Docker Networking | ⬜ |
| Day 14 | Docker Compose | ⬜ |
| Day 15 | Final Project | ⬜ |

> **Update status:** Replace ⬜ with ✅ as you complete each day.

---

## 🧠 Skills Learned

### Week 1 — Fundamentals
- Containerization concepts vs. virtual machines
- Docker architecture (Engine, Daemon, CLI, Registry)
- Image management: pull, inspect, tag, remove
- Container operations: run, exec, stop, rm, logs
- Docker Hub: search, push, pull

### Week 2 — Production Skills
- Writing optimized Dockerfiles
- Multi-stage builds
- Volume management and data persistence
- Docker networking (bridge, host, overlay)
- Docker Compose for multi-service apps
- Environment variables and secrets management

---

## 🗂 Repository Structure

```
docker-learning-journey/
│
├── README.md                        # This file
├── ROADMAP.md                       # Full learning roadmap
├── RESOURCES.md                     # Curated learning resources
├── INTERVIEW_PREP.md                # Interview Q&A (beginner + intermediate)
├── CHEATSHEET.md                    # Quick reference cheatsheet
│
├── Day-01-Docker-Introduction/
│   ├── README.md                    # Learning objectives & theory
│   ├── notes.md                     # Detailed notes
│   └── commands.md                  # Commands practiced
│
├── Day-02-Docker-Installation/ ...
├── Day-03-Docker-Images/ ...
│   └── (same structure each day)
│
├── Day-14-Docker-Compose/
│   ├── README.md
│   ├── notes.md
│   ├── commands.md
│   ├── docker-compose.yml           # Single service example
│   ├── docker-compose.multi.yml     # Multi-service example
│   └── docker-compose.flask.yml     # Flask + Redis example
│
├── Day-15-Final-Project/
│   ├── README.md
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── app/
│
├── Projects/
│   └── flask-redis-nginx/           # Complete final project
│       ├── README.md
│       ├── docker-compose.yml
│       ├── app/
│       │   ├── Dockerfile
│       │   ├── app.py
│       │   └── requirements.txt
│       └── nginx/
│           ├── Dockerfile
│           └── nginx.conf
│
├── Cheatsheets/
│   └── docker-cheatsheet.md
│
├── Interview-Questions/
│   └── docker-interview-qa.md
│
└── Resources/
    └── learning-resources.md
```

---

## 🚀 Final Project — Flask + Redis + Nginx

A production-style multi-container application demonstrating real-world Docker Compose usage.

### Architecture

```
                    ┌─────────────┐
         HTTP       │             │
User ──────────────▶│    Nginx    │  Port 80
                    │  (Reverse   │
                    │   Proxy)    │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │             │
                    │  Flask App  │  Port 5000
                    │  (Python)   │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │             │
                    │    Redis    │  Port 6379
                    │  (Cache/DB) │
                    └─────────────┘
```

### Quick Start

```bash
git clone https://github.com/YOUR_USERNAME/docker-learning-journey.git
cd docker-learning-journey/Projects/flask-redis-nginx
docker compose up -d
# Visit http://localhost
```

---

## 📸 Screenshots

> _Screenshots to be added as each day is completed._

| Day | Screenshot |
|-----|------------|
| Day 01 | `[Add screenshot of Docker version output]` |
| Day 03 | `[Add screenshot of docker images list]` |
| Day 04 | `[Add screenshot of running container]` |
| Day 15 | `[Add screenshot of final project running]` |

---

## 🛠 Technologies Used

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=for-the-badge&logo=flask&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

---

## 🗺 Learning Roadmap

See [ROADMAP.md](./ROADMAP.md) for the complete structured learning path.

---

## 🎤 Interview Preparation

See [INTERVIEW_PREP.md](./INTERVIEW_PREP.md) for:
- 20+ Beginner Questions with Answers
- 20+ Intermediate Questions with Answers
- Scenario-Based Questions
- Troubleshooting Questions

---

## 📚 Resources

See [RESOURCES.md](./RESOURCES.md) for a curated list of:
- Official documentation
- Free courses
- Recommended books
- YouTube channels
- Practice platforms

---

## 🤝 Connect

> If this repository helped you, please ⭐ star it and share it with others on their Docker learning journey.

---



## 📄 License

This repository is for educational purposes. Feel free to fork, clone, and adapt for your own learning journey.

---

*Built with 💙 during 15 days of focused Docker learning.*
# docker-learning-journey
