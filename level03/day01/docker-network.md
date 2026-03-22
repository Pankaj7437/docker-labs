# 🐳 Docker Lab – Create Macvlan Network

## 📌 Task
Create a Docker network on **Application Server 3** with macvlan driver.

### Requirements
- Network name: `blog`
- Driver: `macvlan`
- Subnet: `192.168.30.0/24`
- IP Range: `192.168.30.0/24`

---

## 🛠 Solution

docker network create -d macvlan \
--subnet=192.168.30.0/24 \
--ip-range=192.168.30.0/24 \
-o parent=eth0 \
blog

---

## 🔍 Verification

docker network inspect blog

---

## 📖 Commands Used

- docker network create → Create network
- -d macvlan → Specify driver
- --subnet → Define network range
- --ip-range → Define IP allocation range
- -o parent=eth0 → Attach to host interface

---

## 🚀 Concepts Practiced

- Docker macvlan networking  
- Custom network configuration  
- Advanced Docker networking  

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
