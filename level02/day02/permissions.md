# 🐳 Docker Lab – Allow User to Run Docker Without Sudo

## 📌 Task
A user named `kareem` was unable to execute Docker commands on **Application Server 2** without using `sudo`.

The task is to configure the system so that the user can run Docker commands directly.

---

## 🛠 Solution

### Step 1 – Add user to docker group
sudo usermod -aG docker kareem

### Step 2 – Apply group changes
newgrp docker

OR re-login:

exit
su - kareem

---

## 🔍 Verification

Run Docker command without sudo:

docker ps

Expected:
- Command should execute successfully
- No permission denied error

---

## 📖 Commands Used

- usermod -aG docker → Add user to docker group
- newgrp docker → Apply group changes without logout
- docker ps → Verify Docker access

---

## 🚀 Concepts Practiced

- Linux user and group management  
- Docker permission handling  
- Group-based access control  
- Applying permission changes  

---

## 🧠 Key Learning

Adding a user to a group does not apply immediately.  
The session must be refreshed using `newgrp` or by logging out and logging back in.

---

## 👨‍💻 Author

Pankaj Sharma  
B.Tech CSE | DevOps Learner
