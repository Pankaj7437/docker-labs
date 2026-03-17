# 🐳 Docker Lab – Delete Container

## 📌 Task
A container named `kke-container` was created on **App Server 2** for testing purposes.
Delete the container.

---

## 🛠 Solution

### Stop the container
docker stop kke-container

### Remove the container
docker rm kke-container

---

## ⚡ Alternative (Single Command)

docker rm -f kke-container

---

## 🔍 Verification

docker ps

Ensure `kke-container` is not present in the list.

---

## 📖 Commands Used

- docker stop kke-container → Stop running container
- docker rm kke-container → Remove container
- docker rm -f kke-container → Force remove running container
- docker ps → List running containers

---

## 🚀 Concepts

- Container lifecycle (start / stop / remove)
- Safe vs force deletion
- Basic Docker operations

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
