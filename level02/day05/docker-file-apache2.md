# 🐳 Docker Lab – Build Custom Apache Image

## 📌 Task
Create a Docker image using a Dockerfile on **Application Server 3**.

### Requirements
- Base image: `ubuntu:24.04`
- Install `apache2`
- Configure Apache to run on port `6200`
- Do not modify other Apache configurations

---

## 🛠 Solution

### Create Dockerfile

FROM ubuntu:24.04

RUN apt update && apt install -y apache2

RUN sed -i 's/80/6200/g' /etc/apache2/ports.conf && \
    sed -i 's/*:80/*:6200/g' /etc/apache2/sites-available/000-default.conf

CMD ["apachectl", "-D", "FOREGROUND"]

---

### Build Image

docker build -t apache_6200 /opt/docker

---

### Run Container (Optional)

docker run -d -p 6200:6200 apache_6200

---

## 🔍 Verification

curl http://localhost:6200

---

## 📖 Commands Used

- docker build → Build image from Dockerfile
- sed → Modify configuration files
- CMD → Run apache in foreground

---

## 🚀 Concepts Practiced

- Writing Dockerfile
- Image creation
- Apache configuration
- Port customization
- Container lifecycle

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
