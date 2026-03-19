# 🐳 Docker Labs – Complete DevOps Practice (9 Tasks)

This repository contains solutions to multiple Docker-based DevOps tasks performed on **Application Server 2 (Stratos Datacenter)**.  
Each task focuses on real-world container operations such as deployment, debugging, networking, and file management.

---

# 📌 Task 1 – Deploy Nginx Container

## Objective
Deploy an Nginx container using the alpine image.

## Requirements
- Container name: `nginx_2`
- Image: `nginx:alpine`
- Container must be running

## Solution
docker run -d --name nginx_2 nginx:alpine

## Verification
docker ps

---

# 📌 Task 2 – Debug Container with Custom CMD

## Objective
Create a debug container with a custom command.

## Requirements
- Container name: `debug_2`
- Image: `ubuntu/apache2:latest`
- Override CMD: `sleep 1000`

## Solution
docker run -d --name debug_2 ubuntu/apache2:latest sleep 1000

## Verification
docker ps

---

# 📌 Task 3 – Copy File from Container to Host

## Objective
Copy an encrypted file from container to host.

## Requirements
- Container: `development_3`
- Source: `/tmp/test.txt.gpg`
- Destination: `/tmp/`

## Solution
docker cp development_3:/tmp/test.txt.gpg /tmp/

## Verification
ls /tmp/

---

# 📌 Task 4 – Copy File from Host to Container

## Objective
Copy an encrypted file from host to container.

## Requirements
- Container: `ubuntu_latest`
- Source: `/tmp/nautilus.txt.gpg`
- Destination: `/opt/`

## Solution
docker exec ubuntu_latest mkdir -p /opt/
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/

## Verification
docker exec ubuntu_latest ls /opt/

---

# 📌 Task 5 – Create Image from Container

## Objective
Create a Docker image from an existing container.

## Requirements
- Container: `alpine_nautilus`
- Image: `alpine:nautilus`

## Solution
docker commit alpine_nautilus alpine:nautilus

## Verification
docker images

---

# 📌 Task 6 – Pull Required Images

## Objective
Pull required Docker images.

## Requirements
- redis:alpine
- memcached:alpine

## Solution
docker pull redis:alpine
docker pull memcached:alpine

## Verification
docker images

---

# 📌 Task 7 – Delete Docker Network

## Objective
Remove an unused Docker network.

## Requirements
- Network: `php-network`

## Solution
docker network rm php-network

## Verification
docker network ls

---

# 📌 Task 8 – Create Custom Docker Network

## Objective
Create a custom bridge network with subnet and gateway.

## Requirements
- Network: `mysql-network`
- Driver: bridge
- Subnet: 182.18.0.0/24
- Gateway: 182.18.0.1

## Solution
docker network create \
--driver bridge \
--subnet 182.18.0.0/24 \
--gateway 182.18.0.1 \
mysql-network

## Verification
docker network inspect mysql-network

---

# 📌 Task 9 – Fix Crashing Container

## Objective
Fix a crashing container and ensure it runs properly.

## Issue
Container was failing due to incorrect execution command (`bash not found`).

## Solution
docker rm -f apple_alpine
docker run -d --name apple_alpine httpd

**

## Verification
docker ps

---

# 🚀 Concepts Covered

- Docker container deployment
- Overriding container commands (CMD)
- Copying files between host and container
- Creating images from containers
- Pulling images from Docker Hub
- Docker network management
- Custom bridge network configuration
- Container debugging and troubleshooting
- Container lifecycle management

---

# 🧠 Key Learnings

- Containers run only while their main process is active  
- Always inspect logs before fixing a container  
- Avoid unnecessary changes in image or configuration  
- Use proper Docker commands based on task requirements  
- Debugging is a critical DevOps skill  

---

# 👨‍💻 Author

**Pankaj Sharma**  
B.Tech CSE | DevOps Learner  

---

# ⭐ Notes

This project demonstrates practical Docker skills commonly used in real-world DevOps environments and technical interviews.
