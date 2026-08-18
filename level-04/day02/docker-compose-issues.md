# Docker Compose Deployment Troubleshooting – Nautilus App Server 1

## 📌 Task Overview

The Nautilus DevOps team had an application deployment failure on **App Server 1** due to a misconfigured Docker Compose file.

The existing Compose file was located at:

```bash
/opt/docker/docker-compose.yml
```

The objective was to identify the invalid configuration, correct it **without changing valid settings or container names**, and successfully deploy the application with its Redis dependency.

---

## 🔍 Initial Issue

The original Compose file contained:

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

Running:

```bash
docker compose up
```

resulted in a validation error:

```text
additional property 'service' is not allowed
```

### Problems identified

There were two incorrect Compose keys:

1. `service:` should be **`services:`**
2. `from:` should be **`image:`**

---

## 🛠️ Corrected Configuration

The final `docker-compose.yml` was:

```yaml
name: myapp

services:
  web:
    build: ./app
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

### Important corrections

| Incorrect     | Correct        |
| ------------- | -------------- |
| `service:`    | `services:`    |
| `from: redis` | `image: redis` |
| `build: /app` | `build: ./app` |

The required container names were preserved:

```text
python
redis
```

---

## 🚀 Deployment

Navigate to the Compose directory:

```bash
cd /opt/docker
```

Validate the Compose configuration:

```bash
docker compose config
```

Then start the application:

```bash
docker compose up -d
```

Check running containers:

```bash
docker ps
```

Expected containers:

```text
python
redis
```

---

## 🔎 Troubleshooting

If the `python` container is reported as **down or missing**, check:

```bash
docker ps -a
```

Then inspect its logs:

```bash
docker logs python
```

Also check the Compose status:

```bash
docker compose ps
```

If necessary, recreate the services:

```bash
docker compose down
docker compose up -d --build
```

Then verify:

```bash
docker ps
```

---

## 🧠 What I Learned

### 1. `services` is the root key

Docker Compose expects services to be declared under:

```yaml
services:
```

Using:

```yaml
service:
```

causes Compose validation to fail.

### 2. `image` specifies an existing Docker image

For Redis:

```yaml
redis:
  image: redis
```

`from:` is not a valid Compose service property.

### 3. `build` defines the build context

```yaml
build: ./app
```

means Docker uses the `app` directory relative to the Compose file as the build context.

### 4. `depends_on`

```yaml
depends_on:
  - redis
```

ensures the Redis service is started as a dependency of the web service.

---

## 📊 Architecture

```text
                 App Server 1
              /opt/docker/
                    │
                    │ docker compose
                    ▼
          ┌─────────────────────┐
          │       myapp         │
          │   Docker Compose    │
          └──────────┬──────────┘
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   ┌─────────────┐       ┌─────────────┐
   │   python    │       │    redis    │
   │  Web App    │──────▶│   Database  │
   │             │       │   Service   │
   │ Port 5000   │       │             │
   └──────┬──────┘       └─────────────┘
          │
          │ Port 5000
          ▼
       Host :5000
```

---

## 💡 Key Takeaway

This lab reinforced an important DevOps troubleshooting approach:

> **Don't immediately rewrite a broken configuration. First validate it, identify the exact invalid directive, change only what is necessary, and then verify the resulting containers and logs.**

This is especially important in production environments where unnecessarily modifying valid configuration can introduce additional failures.
