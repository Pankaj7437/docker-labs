# 🚀 Deploy Nginx Container with Port Mapping

## 📌 Objective

On **Application Server 1 (stapp01)**:

1. Pull the `nginx:alpine` image.
2. Create a container named **demo**.
3. Map host port **8082** to container port **80**.
4. Keep the container running.

---

# 🖥️ Environment Details

| Component      | Value                          |
| -------------- | ------------------------------ |
| Server         | Application Server 1 (stapp01) |
| Image          | nginx:alpine                   |
| Container Name | demo                           |
| Host Port      | 8082                           |
| Container Port | 80                             |

---

# 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

---

# 🔹 Step 2: Pull Nginx Alpine Image

```bash
docker pull nginx:alpine
```

### Explanation

* Downloads the lightweight Alpine-based Nginx image.
* Stores it locally on the server.

Verify:

```bash
docker images
```

---

# 🔹 Step 3: Create and Start Container

Run:

```bash
docker run -d --name demo -p 8082:80 nginx:alpine
```

### Explanation

| Option         | Purpose                                 |
| -------------- | --------------------------------------- |
| `-d`           | Run container in background             |
| `--name demo`  | Assign container name `demo`            |
| `-p 8082:80`   | Map host port 8082 to container port 80 |
| `nginx:alpine` | Image used to create container          |

Port mapping:

```text
Host (stapp01):8082
        ↓
Container:80
```

---

# 🔹 Step 4: Verify Container

Check running containers:

```bash
docker ps
```

Expected output:

```text
CONTAINER ID   IMAGE          PORTS
xxxxxxx        nginx:alpine   0.0.0.0:8082->80/tcp
```

---

# 🔹 Step 5: Test Web Server

```bash
curl http://localhost:8082
```

Expected output contains:

```html
Welcome to nginx!
```

---

# 📖 Command Breakdown

### Pull Image

```bash
docker pull nginx:alpine
```

Downloads image from Docker Hub.

---

### Run Container

```bash
docker run -d --name demo -p 8082:80 nginx:alpine
```

Creates and starts a container while exposing Nginx through port 8082.

---

# 🔍 Validation Commands

```bash
docker images

docker ps

docker port demo

curl http://localhost:8082
```

---

# ⚡ Commands Used

```bash
ssh tony@stapp01

docker pull nginx:alpine

docker run -d --name demo -p 8082:80 nginx:alpine

docker ps

curl http://localhost:8082
```

---

# ✅ Result

Successfully pulled the **nginx:alpine** image, created a running container named **demo**, mapped host port **8082** to container port **80**, and verified Nginx accessibility through the mapped port.
