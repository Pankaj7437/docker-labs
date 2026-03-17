# 🐳 Docker Lab – Copy File to Container

## 📌 Task
The Nautilus DevOps team needs to copy an encrypted file from the host system to a running container.

- Host file: `/tmp/nautilus.txt.gpg`
- Container name: `ubuntu_latest`
- Destination path inside container: `/usr/src/`
- Ensure the file remains unchanged

---

## 🛠 Solution

Run the following command:

docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/usr/src/

---

## 🔍 Verification

To verify inside the container:

docker exec -it ubuntu_latest ls /usr/src/

Ensure `nautilus.txt.gpg` is present.

---

## 📖 Command Explanation

- docker cp → Copy files between host and container
- /tmp/nautilus.txt.gpg → Source file (host)
- ubuntu_latest:/usr/src/ → Destination inside container

---

## 🚀 Concepts Practiced

- File transfer between host and container
- Working with running containers
- Docker cp command usage

---

## 👨‍💻 Author
Pankaj Sharma  
B.Tech CSE | DevOps
