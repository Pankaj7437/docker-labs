# 🚀 Docker Container + Volume Mapping Lab (KodeKloud)

---

## 📌 Objective

On **App Server 1**:

### Tasks:

1. Pull `nginx` Docker image
2. Create container named:

```bash
cluster
```

3. Map host directory:

```bash
/opt/security
```

to container directory:

```bash
/tmp
```

4. Ensure `sample.txt` is available inside container
5. Keep container running

---

# 🧠 Concepts Used

| Concept          | Meaning                                     |
| ---------------- | ------------------------------------------- |
| Docker Image     | Template to create containers               |
| Docker Container | Running instance of image                   |
| Volume Mapping   | Share files between host and container      |
| Bind Mount       | Direct host folder mounted inside container |

---

# 🖥️ Step-by-Step Solution

---

## 🔹 1. SSH into App Server 1

```bash
ssh tony@stapp01
```

---

## 🔹 2. Verify Docker

```bash
docker --version
```

---

# 📥 Pull Nginx Image

---

## 🔹 3. Pull nginx image

```bash
docker pull nginx:latest
```

---

## 🧠 Explanation

| Part        | Meaning              |
| ----------- | -------------------- |
| docker pull | Download image       |
| nginx       | Official nginx image |
| latest      | Latest version tag   |

---

## 🔹 4. Verify image

```bash
docker images
```

Expected:

```bash
nginx   latest
```

---

# 📦 Create Container

---

## 🔹 5. Create container with volume mapping

```bash
docker run -d \
--name cluster \
-v /opt/security:/tmp \
nginx
```

---

# 🧠 Command Breakdown

| Option            | Meaning           |
| ----------------- | ----------------- |
| -d                | Run in background |
| --name cluster    | Container name    |
| -v host:container | Volume mapping    |
| nginx             | Image name        |

---

# 📂 Volume Mapping Explained

---

## Host Side

```bash
/opt/security
```

Contains:

```bash
sample.txt
```

---

## Container Side

Mapped to:

```bash
/tmp
```

So inside container:

```bash
/tmp/sample.txt
```

becomes accessible automatically.

---

# 🧪 Verify Inside Container

---

## 🔹 6. Open shell inside container

```bash
docker exec -it cluster /bin/bash
```

---

## 🧠 Explanation

| Part        | Meaning                      |
| ----------- | ---------------------------- |
| docker exec | Run command inside container |
| -it         | Interactive terminal         |
| cluster     | Container name               |
| /bin/bash   | Open shell                   |

---

## 🔹 7. Verify file

```bash
ls /tmp
```

Expected:

```bash
sample.txt
```

---

## 🔹 8. Exit container

```bash
exit
```

---

# 🔍 Verify Container Running

---

## 🔹 9. Check running containers

```bash
docker ps
```

Expected:

```bash
cluster   nginx   Up
```

---

# ⚠️ Common Mistakes

| Mistake                    | Fix                                 |
| -------------------------- | ----------------------------------- |
| Wrong volume path          | Use `/opt/security:/tmp`            |
| Container exits            | Use nginx image properly            |
| Wrong exec syntax          | `docker exec -it cluster /bin/bash` |
| Typing `/bin/bash cluster` | Wrong order ❌                       |

---

# 🧠 Why Volume Mapping?

Without mapping:

* Files inside container disappear after container deletion

With mapping:

* Data persists on host machine

---

# 📌 Important Docker Commands

| Command       | Purpose            |
| ------------- | ------------------ |
| docker images | List images        |
| docker ps     | Running containers |
| docker exec   | Access container   |
| docker stop   | Stop container     |
| docker rm     | Remove container   |

---

# ⚡ Quick Commands

```bash
docker pull nginx:latest

docker run -d \
--name cluster \
-v /opt/security:/tmp \
nginx

docker exec -it cluster /bin/bash

ls /tmp

docker ps
```

---

# ✅ Final Result

✔ nginx image pulled
✔ Container `cluster` created
✔ Host volume mapped successfully
✔ `sample.txt` accessible inside container
✔ Container running successfully

---

🔥 Task Completed Successfully
