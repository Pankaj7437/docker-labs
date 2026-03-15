# Docker Installation Lab – Install Docker CE and Docker Compose on App Server 1

## Objective
The Nautilus DevOps team plans to containerize applications for testing purposes.  
To support containerization, **Docker CE** and **Docker Compose** must be installed on **App Server 1**, and the **Docker service must be started**.

---

# Environment Details

| Parameter | Value |
|----------|------|
| Infrastructure | Nautilus |
| Server | App Server 1 |
| Hostname | stapp01 |
| Container Platform | Docker |
| Required Packages | docker-ce, docker-compose |
| Service | docker |

---

# Initial Situation

The application server initially did not have Docker installed.  
Attempting to install Docker directly resulted in package errors because the **Docker repository was not configured in the system**.

Example error:

```
No match for argument: docker-ce
No match for argument: compose
```

This indicates that the required Docker packages are not available in the default system repositories.

---

# Step 1 – Connect to App Server 1

Login from the jump host.

```bash
ssh tony@stapp01
```

Explanation:

| Command | Purpose |
|-------|---------|
| ssh | Secure shell login |
| tony@stapp01 | Connect to App Server 1 |

---

# Step 2 – Add Docker Repository

Add the official Docker repository to the system.

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Explanation:

| Command | Purpose |
|-------|---------|
| yum-config-manager | Manage yum repositories |
| --add-repo | Add new repository |
| docker-ce.repo | Official Docker package repository |

This allows the system to download Docker packages.

---

# Step 3 – Install Docker CE and Docker Compose

Install Docker engine and related components.

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Explanation:

| Package | Purpose |
|-------|---------|
| docker-ce | Docker engine |
| docker-ce-cli | Docker command line interface |
| containerd.io | Container runtime |
| docker-buildx-plugin | Docker build plugin |
| docker-compose-plugin | Docker Compose support |

---

# Step 4 – Start Docker Service

Start the Docker daemon.

```bash
sudo systemctl start docker
```

---

# Step 5 – Enable Docker Service

Enable Docker to start automatically on system boot.

```bash
sudo systemctl enable docker --now
```

Explanation:

| Command | Purpose |
|-------|---------|
| systemctl enable | Enable service at boot |
| --now | Start service immediately |

---

# Step 6 – Verify Docker Service

Check Docker service status.

```bash
sudo systemctl status docker
```

Expected output:

```
Active: active (running)
```

---

# Step 7 – Verify Docker Installation

Check Docker version.

```bash
docker --version
```

Check Docker Compose plugin.

```bash
docker compose version
```

---

# Commands Cheat Sheet

```bash
ssh tony@stapp01

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker --now

sudo systemctl status docker

docker --version
docker compose version
```

---

# Common Mistakes

| Mistake | Problem |
|-------|--------|
| Installing docker without repository | Package not found |
| Typing `docker compose` instead of `docker-compose` package | Installation fails |
| Forgetting to start docker service | Docker commands fail |
| Installing on wrong server | Task requires App Server 1 only |

---

# Key Concepts Learned

- Docker installation on Linux
- Adding external repositories
- Package installation using dnf
- Docker service management
- Container runtime components

---

# Conclusion

Docker CE and Docker Compose were successfully installed on **App Server 1**, and the Docker service was started and enabled. This prepares the server for containerized application deployment within the Nautilus infrastructure.
