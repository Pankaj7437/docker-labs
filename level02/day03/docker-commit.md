# 🐳 Docker Lab – Create Image from Running Container

## 📌 Task
Create a Docker image from an existing running container.

### Requirements
- Container name: `ubuntu_latest`
- New image name: `news:datacenter`

---

## 🛠 Solution

docker commit ubuntu_latest news:datacenter

---

## 🔍 Verification

docker images

Ensure image `news` with tag `datacenter` is present.

---

## 📖 Commands Used

- docker commit → Create image from container
- ubuntu_latest → Source container
- news:datacenter → New image name and tag

---

## 🚀 Concepts Practiced

- Creating images from running containers  
- Image tagging  
- Container state snapshot  

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
