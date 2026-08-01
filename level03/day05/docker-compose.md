# Deploying Apache (httpd) Container Using Docker Compose

## Objective

The goal of this task is to deploy an **Apache (httpd)** web server inside a Docker container using **Docker Compose**. The deployment must satisfy the following requirements:

- Create a Docker Compose file named **docker-compose.yml** in **/opt/docker/**
- Use the **httpd:latest** image
- Create a container named **httpd**
- Expose **host port 5003** to **container port 80**
- Mount the host directory **/opt/data** to the container directory **/usr/local/apache2/htdocs**
- Deploy the container using Docker Compose

---

# Prerequisites

- Docker Engine installed
- Docker Compose installed
- SSH access to **Application Server 2**
- Existing host directory:

```
/opt/data
```

---

# Step 1: Connect to Application Server 2

SSH into the target server.

```bash
ssh steve@stapp02
```

Move to the directory where the compose file needs to be created.

```bash
cd /opt/docker
```

---

# Step 2: Create Docker Compose File

Create the compose file.

```bash
vi docker-compose.yml
```

Add the following configuration:

```yaml
version: "3.8"

services:
  web:
    image: httpd:latest
    container_name: httpd

    ports:
      - "5003:80"

    volumes:
      - /opt/data:/usr/local/apache2/htdocs
```

Save and exit.

---

# Docker Compose File Explanation

## Version

```yaml
version: "3.8"
```

Specifies the Docker Compose file format version.

---

## Services

```yaml
services:
```

Defines all containers that Docker Compose will manage.

---

## Service Name

```yaml
web:
```

The internal name of the service.

This can be any valid name.

---

## Image

```yaml
image: httpd:latest
```

Downloads (if necessary) and uses the latest official Apache HTTP Server image from Docker Hub.

---

## Container Name

```yaml
container_name: httpd
```

Creates the container with the exact name:

```
httpd
```

instead of Docker generating a random name.

---

## Port Mapping

```yaml
ports:
  - "5003:80"
```

Maps

```
Host Port      → 5003
Container Port → 80
```

This means users access Apache using

```
http://Server-IP:5003
```

---

## Volume Mapping

```yaml
volumes:
  - /opt/data:/usr/local/apache2/htdocs
```

Maps the host directory

```
/opt/data
```

to Apache's default web root inside the container

```
/usr/local/apache2/htdocs
```

Benefits:

- Persistent storage
- Website files remain even if the container is deleted
- Changes on the host instantly reflect inside the container

---

# Step 3: Deploy the Container

Start the container using Docker Compose.

```bash
docker compose up -d
```

If using the older Compose plugin:

```bash
docker-compose up -d
```

The **-d** option runs the container in detached mode (background).

---

# Step 4: Verify Running Container

Check if the container is running.

```bash
docker ps
```

Example Output

```
CONTAINER ID   IMAGE          PORTS
xxxxx          httpd:latest   0.0.0.0:5003->80/tcp
```

---

# Step 5: Verify Port Mapping

```bash
docker port httpd
```

Output

```
80/tcp -> 0.0.0.0:5003
```

This confirms that host port **5003** forwards traffic to Apache running on port **80** inside the container.

---

# Step 6: Verify Volume Mount

Inspect the container.

```bash
docker inspect httpd
```

Look under:

```
Mounts
```

You should find

```
Source:
/opt/data

Destination:
/usr/local/apache2/htdocs
```

This confirms the bind mount is configured correctly.

---

# Architecture Diagram

```
                    Client Browser
                           │
                           │
                  http://Server-IP:5003
                           │
                           ▼
          +----------------------------------+
          |      Docker Host (stapp02)       |
          |                                  |
          |          Port 5003               |
          +----------------+-----------------+
                           │
                           ▼
                +----------------------+
                | Apache Container     |
                |      httpd           |
                |      Port 80         |
                |                      |
                | /usr/local/apache2   |
                |       /htdocs        |
                +----------+-----------+
                           │
                   Bind Mount Volume
                           │
                           ▼
                    /opt/data (Host)
```

---

# Complete Command Summary

```bash
ssh steve@stapp02

cd /opt/docker

vi docker-compose.yml

docker compose up -d

docker ps

docker port httpd

docker inspect httpd
```

---

# Key Concepts Learned

### Docker Compose

Allows defining container deployments using a YAML file instead of long Docker commands.

---

### Service

Represents one application/container managed by Docker Compose.

---

### Image

Blueprint used to create containers.

Example:

```
httpd:latest
```

---

### Container

A running instance of an image.

---

### Port Mapping

```
Host Port → Container Port
```

Example

```
5003 → 80
```

Allows external users to access applications running inside containers.

---

### Volume Mapping

Connects a host directory to a container directory.

Example

```
Host

/opt/data

↓

Container

/usr/local/apache2/htdocs
```

Provides persistent storage and easy file sharing.

---

### Detached Mode

```
docker compose up -d
```

Runs containers in the background without occupying the terminal.

---

### Docker Inspect

Displays detailed metadata about containers including:

- Networks
- Mounts
- Environment Variables
- Port Bindings

---

# Real-World Use Cases

- Deploying production web servers
- Hosting static websites
- Managing multi-container applications
- Persistent web content using bind mounts
- Infrastructure as Code using Docker Compose

---

# Learning Outcome

After completing this lab, I learned how to:

- Create a Docker Compose configuration file
- Deploy containers using Docker Compose
- Use the official Apache (httpd) Docker image
- Configure custom container names
- Perform host-to-container port mapping
- Mount host directories as Docker volumes
- Verify deployments using Docker commands
- Understand how Docker Compose simplifies application deployment

---
