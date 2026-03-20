# 🐳 Docker Lab – Pull and Re-tag Image

## 📌 Task
Pull a Docker image and create a new tag on **Application Server 3**.

### Requirements
- Pull image: `busybox:musl`
- Create new tag: `busybox:blog`

---

## 🛠 Solution

docker pull busybox:musl
docker tag busybox:musl busybox:blog

---

## 🔍 Verification

docker images

Ensure both tags are present:
- busybox:musl  
- busybox:blog  

---

## 📖 Commands Used

- docker pull → Download image from Docker Hub  
- docker tag → Create a new tag for an existing image  

---

## 🚀 Concepts Practiced

- Image pulling  
- Image tagging  
- Managing multiple tags for same image  

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
