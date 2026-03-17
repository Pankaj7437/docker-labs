# Docker Container Deployment – Run Nginx Container on Application Server 2

## Objective

The Nautilus DevOps team is conducting application deployment testing on selected application servers. The goal is to deploy an **NGINX container** on **Application Server 2**.

The container must:

- Use the **nginx image**
- Use the **alpine tag**
- Be named **nginx2**
- Be in **running state**

---

# Environment Details

| Parameter | Value |
|---|---|
| Infrastructure | Nautilus |
| Datacenter | Stratos |
| Server | Application Server 2 |
| Hostname | stapp02 |
| Container Name | nginx2 |
| Docker Image | nginx |
| Image Tag | alpine |

---

# Step 1 – Connect to Application Server 2

Login from the jump host.

```bash
ssh steve@stapp02
```

---

# Step 2 – Deploy Nginx Container

Run the Docker container using the nginx alpine image.

```bash
docker run -d --name nginx2 nginx:alpine
```

Explanation:

| Option | Description |
|---|---|
| docker run | Create and start container |
| -d | Run container in background |
| --name nginx2 | Assign container name |
| nginx:alpine | Use nginx image with alpine tag |

---

# Step 3 – Verify Container Status

Check running containers.

```bash
docker ps
```

Expected output should show:

```
CONTAINER ID   IMAGE          NAME
xxxxxxxxxxxx   nginx:alpine   nginx2
```

Status should be:

```
Up
```

---

# Commands Cheat Sheet

```bash
ssh steve@stapp02

docker run -d --name nginx2 nginx:alpine

docker ps
```

---

# Common Mistakes

| Mistake | Problem |
|---|---|
| Wrong container name | Task validation fails |
| Missing alpine tag | Wrong image version |
| Forgetting `-d` flag | Container runs in foreground |
| Running on wrong server | Task fails |

---

# Key Concepts Learned

- Docker container deployment
- Using image tags
- Running containers in detached mode
- Container verification using docker ps
- Basic container lifecycle management

---

# Conclusion

An **NGINX container named `nginx2`** was successfully deployed on **Application Server 2** using the **nginx:alpine image**, and the container is running successfully.
