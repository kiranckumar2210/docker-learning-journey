# Day 02 — Docker Installation & Setup

> **Source:** [docs.docker.com/engine/install/ubuntu](https://docs.docker.com/engine/install/ubuntu/) · [docs.docker.com/engine/install/linux-postinstall](https://docs.docker.com/engine/install/linux-postinstall/)

---

## 🎯 Learning Objectives

- Install Docker Engine on Ubuntu using the official `apt` repository method
- Run post-installation steps to allow non-root access
- Configure Docker to start on boot
- Verify the installation with `docker version`, `docker info`, and `hello-world`

---

## 📖 Prerequisites (from official docs)

Per the official Docker Engine installation documentation:

- **OS:** 64-bit version of one of: Ubuntu Noble 24.04, Mantic 23.10, Jammy 22.04, or Focal 20.04
- **Architecture:** x86_64 (amd64), armhf, arm64, s390x, or ppc64le
- **Firewall note:** Docker is only compatible with `iptables-nft` and `iptables-legacy`. Firewall rules created with `nft` are not supported on a system with Docker installed.

---

## 📖 Installation Method 1: Using the apt Repository (Recommended)

This is the official recommended method from docs.docker.com. After this, you can update Docker from the repository.

### Step 1 — Remove Conflicting Packages

From the official docs:

> *"Before you can install Docker Engine, you need to uninstall any conflicting packages. Your Linux distribution may provide unofficial Docker packages which may conflict with the official packages."*

```bash
sudo apt remove $(dpkg --get-selections \
  docker.io \
  docker-compose \
  docker-compose-v2 \
  docker-doc \
  podman-docker \
  containerd \
  runc | cut -f1)
```

> Note from docs: `apt` might report that you have none of these packages installed — that's fine.

### Step 2 — Set Up Docker's apt Repository

```bash
# Add Docker's official GPG key
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the Docker repository to apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

### Step 3 — Install Docker Engine

```bash
sudo apt-get install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

> The above installs: Docker Engine (`docker-ce`), CLI (`docker-ce-cli`), container runtime (`containerd.io`), BuildKit plugin, and Compose plugin.

### Step 4 — Verify

```bash
sudo docker run hello-world
```

Per the official docs: *"You have now successfully installed and started Docker Engine."*

---

## 📖 Installation Method 2: Convenience Script (Dev Only)

The official docs provide a convenience script but warn:

> *"The convenience script isn't recommended for production environments, but it's useful for creating a provisioning script tailored to your needs."*

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
```

---

## 📖 Installation Method 3: Docker Desktop

For **macOS**, **Windows**, and **Linux (GUI)**: Docker Desktop is the recommended option. It includes the daemon, client, Compose, and Kubernetes.

Download from: https://docs.docker.com/desktop/

---

## 📖 Post-Installation Steps (Linux)

### Allow Non-Root Users to Run Docker

From the official docs:

> *"The docker user group exists but contains no users, which is why you're required to use sudo to run Docker commands. Continue to Linux postinstall to allow non-privileged users to run Docker commands."*

```bash
# Create the docker group (usually already exists)
sudo groupadd docker

# Add your user to the docker group
sudo usermod -aG docker $USER

# Activate the group change (or log out and log back in)
newgrp docker

# Verify — this should work WITHOUT sudo
docker run hello-world
```

### Configure Docker to Start on Boot

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

To disable autostart:
```bash
sudo systemctl disable docker.service
sudo systemctl disable containerd.service
```

---

## 📖 Verify the Full Installation

```bash
# Client + server versions
docker version

# Full system info (storage driver, cgroups, containers count, etc.)
docker info

# End-to-end functional test
docker run hello-world
```

---

## 🔧 Troubleshooting (from official docs)

### "Cannot connect to the Docker daemon"

```bash
# Check if daemon is running
sudo systemctl status docker

# Start the daemon manually
sudo systemctl start docker
```

### "Permission denied while trying to connect to Docker daemon socket"

```bash
# Add user to docker group, then apply without logout
sudo usermod -aG docker $USER
newgrp docker
```

### "Port already in use" on subsequent hello-world runs

The `hello-world` container exits immediately — this is expected. It produces output then stops. Check with `docker ps -a`.

---

## ✅ Day 2 Checklist

- [ ] Docker Engine installed from the official apt repository
- [ ] `docker version` shows both Client and Server sections
- [ ] `docker info` runs without errors
- [ ] `docker run hello-world` runs successfully **without sudo**
- [ ] `sudo systemctl is-enabled docker` returns `enabled`

---

## 💬 Interview Questions

1. What is `containerd` and how does it relate to Docker Engine?
2. Why does Docker bypass firewall rules created with `nft`?
3. What security risk is there in adding a user to the `docker` group?
4. What is the difference between `docker-ce` and `docker-ce-cli`?

> **Answer to Q3:** The `docker` group grants root-equivalent privileges. A user in the `docker` group can mount arbitrary host paths, access the Docker socket, and escalate to root. In production environments, consider using rootless Docker instead.

---

## 🔗 Official Documentation References

- [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
- [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)
- [Install Docker Desktop](https://docs.docker.com/desktop/)

---

## 📝 Git Commit Message

```
Day 02: Docker Installation — Installed Docker Engine using official apt repository method, configured non-root access and boot startup
```
