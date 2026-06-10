# Day 02 — Commands: Docker Installation

> All commands sourced from [docs.docker.com/engine/install/ubuntu](https://docs.docker.com/engine/install/ubuntu/)

---

## Full Installation Sequence (Official Method)

```bash
# 1. Remove any conflicting unofficial packages
sudo apt remove $(dpkg --get-selections \
  docker.io docker-compose docker-compose-v2 \
  docker-doc podman-docker containerd runc | cut -f1)

# 2. Install prerequisites
sudo apt-get update
sudo apt-get install -y ca-certificates curl

# 3. Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 4. Add Docker apt repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# 5. Install Docker Engine
sudo apt-get install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin

# 6. Verify with hello-world
sudo docker run hello-world
```

---

## Post-Installation

```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Apply group change without logout
newgrp docker

# Enable Docker to start on system boot
sudo systemctl enable docker.service
sudo systemctl enable containerd.service

# Verify docker works without sudo
docker run hello-world
```

---

## Verification Commands

```bash
docker version
docker info
docker run hello-world
```

**Expected output of `docker run hello-world`:**
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

---

## Service Management

```bash
sudo systemctl start docker       # Start daemon
sudo systemctl stop docker        # Stop daemon
sudo systemctl restart docker     # Restart daemon
sudo systemctl status docker      # Check status
sudo systemctl is-enabled docker  # Check if starts on boot
sudo journalctl -u docker.service # View daemon logs
```

---

## Uninstall Docker (If Needed)

```bash
# Remove Docker packages
sudo apt-get purge docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

# Remove all images, containers, and volumes
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

## Screenshot Placeholders

> `[Screenshot: docker version output showing both Client and Server versions]`
> `[Screenshot: docker run hello-world full output showing the 4 steps]`
> `[Screenshot: docker info output showing 0 containers, 0 images, overlay2 storage driver]`
