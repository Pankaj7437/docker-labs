# 🐳 Docker Lab – Fix Static Website Container

## 📌 Task
A static website running in a container named `nautilus` on **App Server 1** was not accessible.

### Requirements
- Verify volume mapping:
  - Host: `/var/www/html`
  - Container: `/usr/local/apache2/htdocs`
- Ensure website is accessible on:
  - `http://localhost:8085`

---

## 🛠 Solution

Start the container:

docker start nautilus

---

## 🔍 Verification

Check running containers:

docker ps

Test website:

curl http://localhost:8085

---

## 📖 Commands Used

- docker inspect nautilus → Check container configuration
- docker start nautilus → Start stopped container
- docker ps → Verify running containers
- curl → Test website accessibility

---

## 🚀 Concepts Practiced

- Container troubleshooting
- Volume mapping verification
- Port mapping verification
- Fixing stopped containers

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
