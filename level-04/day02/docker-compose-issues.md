# Docker Compose Configuration Troubleshooting – Nautilus

## 📌 Lab Overview

The Nautilus DevOps team was deploying an application on **App Server 1** in **Stratos DC**, but deployment was failing because of mistakes in the existing Docker Compose configuration.

The task was to inspect the existing `docker-compose.yml`, identify the configuration issues, fix them **without changing valid settings or container names**, and successfully run the application.

---

## 🎯 Objectives

* Locate the Docker Compose file under `/opt/docker`.
* Identify syntax/configuration errors.
* Correct the Compose file.
* Preserve the existing container names.
* Start the application successfully using Docker Compose.

---

## 🖥️ Server Details

| Item                  | Details              |
| --------------------- | -------------------- |
| Server                | App Server 1         |
| User                  | `tony`               |
| Working Directory     | `/opt/docker`        |
| Compose File          | `docker-compose.yml` |
| Application Container | `python`             |
| Redis Container       | `redis`              |

---

## 🔍 Initial Configuration

The existing file contained:

```yaml
name: myapp

service:
  web:
    build: /app
    container_name: python
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    depends_on:
      - redis

  redis:
    from: redis
    container_name: redis
```

---

## ❌ Issue 1 – Incorrect Compose Key

The file used:

```yaml
service:
```

Docker Compose expects:

```yaml
services:
```

Therefore, Docker Compose produced:

```text
Additional property service is not allowed
```

### Fix

Changed:

```yaml
service:
```

to:

```yaml
services:
```

---

## ❌ Issue 2 – Redis Service Configuration

The Redis service used:

```yaml
redis:
  from: redis
  container_name: redis
```

The service needs an image definition. The corrected configuration is:

```yaml
redis:
  image: redis
  container_name: redis
```

---

## ✅ Corrected `docker-compose.yml`

```yaml
name: myapp

services:
  web:
    build: /app
    container_name: python
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    depends_on:
      - redis

  redis:
    image: redis
    container_name: redis
```

> The existing container names `python` and `redis` were preserved as required.

---

## 🚀 Step-by-Step Solution

### 1. Connect to App Server 1

```bash
ssh tony@stapp01
```

---

### 2. Navigate to the Docker directory

```bash
cd /opt/docker
```

Check the files:

```bash
ls
```

Expected:

```text
app
docker-compose.yml
```

---

### 3. Inspect the Compose file

```bash
cat docker-compose.yml
```

The first error was identified from the Compose validation message:

```text
Additional property service is not allowed
```

This indicated that `service` was not a valid top-level Compose property.

---

### 4. Edit the file

```bash
sudo nano docker-compose.yml
```

Correct the configuration to:

```yaml
name: myapp

services:
  web:
    build: /app
    container_name: python
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    depends_on:
      - redis

  redis:
    image: redis
    container_name: redis
```

Save and exit.

---

## 🔎 Validate the Configuration

Before starting the application, validate the Compose file:

```bash
docker compose config
```

If the YAML is valid, Docker Compose displays the parsed configuration instead of reporting a validation error.

---

## ▶️ Start the Application

```bash
docker compose up -d
```

The `-d` option starts the containers in detached mode.

---

## 🔍 Verify Containers

```bash
docker ps
```

The containers should include:

```text
python
redis
```

You can also check the Compose services:

```bash
docker compose ps
```

---

## 🧹 Useful Commands

Stop the application:

```bash
docker compose down
```

View logs:

```bash
docker compose logs
```

View logs for the web container:

```bash
docker compose logs web
```

Restart the stack:

```bash
docker compose restart
```

---

## 🧠 What I Learned

### 1. Docker Compose uses `services`

The correct top-level structure is:

```yaml
services:
```

not:

```yaml
service:
```

---

### 2. `image` specifies the container image

For Redis:

```yaml
redis:
  image: redis
```

This tells Docker Compose to create the Redis container from the Redis image.

---

### 3. `depends_on`

```yaml
depends_on:
  - redis
```

defines the dependency between the `web` service and Redis.

It tells Compose to start the Redis service before the web service.

---

### 4. Port Mapping

```yaml
ports:
  - "5000:5000"
```

means:

```text
Host Port 5000
       │
       ▼
Container Port 5000
```

---

### 5. Bind Mount

```yaml
volumes:
  - .:/code
```

mounts the current host directory into `/code` inside the container.

---

## 📊 Architecture

```text
                 Docker Compose
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
     ┌─────────┐             ┌─────────┐
     │  web    │             │  redis  │
     │ python  │────────────▶│  redis  │
     └─────────┘             └─────────┘
          │
          │ Port 5000
          ▼
     Host :5000
```

---

## 💼 Key DevOps Takeaway

This lab was mainly about **troubleshooting an existing Docker Compose configuration rather than creating one from scratch**.

The important debugging approach was:

```text
Run Compose
    ↓
Read the validation error
    ↓
Inspect docker-compose.yml
    ↓
Identify invalid YAML/Compose properties
    ↓
Fix only the incorrect configuration
    ↓
Validate with docker compose config
    ↓
Start with docker compose up -d
    ↓
Verify with docker compose ps / docker ps
```
