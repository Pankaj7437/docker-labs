# 🐳 Docker Lab – Configure Apache Inside Container

## 📌 Task
Configure Apache inside a running container `kkloud` on **Application Server 2**.

### Requirements
- Install apache2
- Change port from 80 → 8087
- Do not bind to specific IP
- Ensure Apache is running

---

## 🛠 Solution

### Enter container
docker exec -it kkloud bash

### Install Apache
apt update
apt install -y apache2

### Update port
vi /etc/apache2/ports.conf
# Change: Listen 80 → Listen 8087

vi /etc/apache2/sites-available/000-default.conf
# Change: <VirtualHost *:80> → <VirtualHost *:8087>

### Start Apache
service apache2 start

---

## 🔍 Verification

curl http://localhost:8087

---

## 📖 Commands Used

- docker exec → Access container
- apt → Install packages
- vi → Edit config files
- service → Manage apache
- curl → Test service

---

## 🚀 Concepts Practiced

- Running commands inside container
- Installing software in container
- Service configuration
- Port configuration
- Container-based debugging

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
