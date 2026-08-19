# Dockerize and Deploy Node.js Application – Nautilus

## 📌 Lab Overview

The Nautilus DevOps team has provided a Node.js application on **App Server 2**. The application files are already available under:

```bash
/node_app
```

The objective was to containerize the application using Docker, build the required image, run it as a container, and expose the application on the specified host port.

---

# 🎯 Requirements

The task required:

### Dockerfile

Create:

```text
/node_app/Dockerfile
```

The Dockerfile must:

* Use a `node` image as the base image.
* Install dependencies from `package.json`.
* Use `server.js` as the application startup command.
* Expose port `6400`.

### Docker Image

The image must be named:

```text
nautilus/node-web-app
```

### Docker Container

The container must be named:

```text
nodeapp_nautilus
```

Port mapping:

```text
Host:      8098
Container: 6400
```

The application can then be tested with:

```bash
curl http://localhost:8098
```

---

# 1. Connect to App Server 2

From the Jump Host:

```bash
ssh steve@stapp02
```

---

# 2. Navigate to the Application Directory

```bash
cd /node_app
```

Check the existing application files:

```bash
ls
```

The directory should contain:

```text
package.json
server.js
```

These files were already provided by the development team.

---

# 3. Create the Dockerfile

Create the Dockerfile:

```bash
vi Dockerfile
```

The filename is case-sensitive and must be exactly:

```text
Dockerfile
```

---

# 4. Dockerfile

Use the following configuration:

```dockerfile
FROM node:latest

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY server.js ./

EXPOSE 6400

CMD ["node", "server.js"]
```

---

# 🧠 Dockerfile Explanation

## `FROM`

```dockerfile
FROM node:latest
```

Uses the official Node.js image as the base image.

This provides Node.js and npm inside the container.

---

## `WORKDIR`

```dockerfile
WORKDIR /app
```

Sets `/app` as the working directory inside the container.

Subsequent commands such as `COPY`, `RUN`, and `CMD` operate relative to this directory unless an absolute path is specified.

---

## Copy package files

```dockerfile
COPY package*.json ./
```

Copies the application's package files into the image.

The `*` allows both:

```text
package.json
package-lock.json
```

to be copied if a lock file exists.

---

## Install dependencies

```dockerfile
RUN npm install
```

Reads `package.json` and installs the Node.js application's dependencies inside the image.

The resulting dependencies are generally placed under:

```text
/app/node_modules
```

---

## Copy application code

```dockerfile
COPY server.js ./
```

Copies the application's `server.js` into the image.

---

## Expose application port

```dockerfile
EXPOSE 6400
```

Documents that the Node.js application listens on port `6400` inside the container.

It does **not** publish the port to the host by itself.

Host publishing is done when the container is started.

---

## Start the application

```dockerfile
CMD ["node", "server.js"]
```

This starts the Node.js application when the container starts.

Equivalent command:

```bash
node server.js
```

---

# 5. Build the Docker Image

From `/node_app`:

```bash
docker build -t nautilus/node-web-app .
```

Explanation:

```text
docker build
    │
    ├── -t nautilus/node-web-app
    │       └── Image name
    │
    └── .
        └── Current directory as build context
```

The `.` is important because `Dockerfile`, `package.json`, and `server.js` are located in `/node_app`.

---

# 6. Verify the Image

Run:

```bash
docker images
```

The image should appear as:

```text
nautilus/node-web-app
```

You can also check specifically:

```bash
docker images nautilus/node-web-app
```

---

# 7. Run the Container

Create the required container:

```bash
docker run -d \
  --name nodeapp_nautilus \
  -p 8098:6400 \
  nautilus/node-web-app
```

### Port mapping

```text
Host                         Container
  │                              │
  │       8098 : 6400            │
  └──────────────────────────────┘
          │
          ▼
       Node.js
```

The syntax:

```bash
-p 8098:6400
```

means:

```text
Host port 8098 → Container port 6400
```

---

# 8. Verify the Container

Check running containers:

```bash
docker ps
```

You should see:

```text
nodeapp_nautilus
```

Check the container details:

```bash
docker inspect nodeapp_nautilus
```

Check application logs:

```bash
docker logs nodeapp_nautilus
```

If the Node.js application starts successfully, its startup output should appear in the logs.

---

# 9. Test the Application

The lab provides the following test:

```bash
curl http://localhost:8098
```

The request follows this path:

```text
curl
  │
  ▼
localhost:8098
  │
  │ Docker port mapping
  ▼
Container:6400
  │
  ▼
Node.js server
  │
  ▼
server.js
```

If the application is running correctly, the application's response should be returned.

---

# 📊 Architecture

```text
                         App Server 2
                              │
                       /node_app/
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
        Dockerfile       package.json      server.js
             │
             │ docker build
             ▼
   ┌─────────────────────────────┐
   │   nautilus/node-web-app     │
   │                             │
   │       Node.js              │
   │          │                  │
   │          ▼                  │
   │      server.js             │
   │                             │
   │      Container :6400       │
   └──────────────┬──────────────┘
                  │
             Docker -p
                  │
                  ▼
             Host :8098
                  │
                  ▼
        curl localhost:8098
```

---

# 🔧 Useful Troubleshooting Commands

### Check all containers

```bash
docker ps -a
```

### View application logs

```bash
docker logs nodeapp_nautilus
```

### Follow logs

```bash
docker logs -f nodeapp_nautilus
```

### Check port mapping

```bash
docker port nodeapp_nautilus
```

Expected:

```text
6400/tcp -> 0.0.0.0:8098
```

### Restart container

```bash
docker restart nodeapp_nautilus
```

### Remove the container

```bash
docker rm -f nodeapp_nautilus
```

### Rebuild the image

```bash
cd /node_app
docker build -t nautilus/node-web-app .
```

---

# ⚠️ Common Mistakes

### Incorrect image name

The required image name is:

```text
nautilus/node-web-app
```

So build with:

```bash
docker build -t nautilus/node-web-app .
```

---

### Incorrect container name

The required container name is:

```text
nodeapp_nautilus
```

Therefore:

```bash
--name nodeapp_nautilus
```

must be used.

---

### Reversing port mapping

Correct:

```bash
-p 8098:6400
```

Incorrect:

```bash
-p 6400:8098
```

Remember:

```text
-p HOST_PORT:CONTAINER_PORT
```

---

### Forgetting the build context

Correct:

```bash
docker build -t nautilus/node-web-app .
```

The `.` tells Docker to use `/node_app` as the build context.

---

# ✅ Final Verification Checklist

Run:

```bash
cd /node_app
```

Check:

```bash
ls
```

Expected:

```text
Dockerfile
package.json
server.js
```

Build:

```bash
docker build -t nautilus/node-web-app .
```

Verify:

```bash
docker images
```

Run:

```bash
docker run -d \
  --name nodeapp_nautilus \
  -p 8098:6400 \
  nautilus/node-web-app
```

Verify:

```bash
docker ps
```

Test:

```bash
curl http://localhost:8098
```

---

# 📚 What I Learned

This lab provided practical experience with **Dockerizing a Node.js application**.

### Key concepts practiced:

* Creating a Dockerfile
* Selecting a Node.js base image
* Using `WORKDIR`
* Copying `package.json`
* Installing npm dependencies
* Copying application source code
* Using `CMD`
* Exposing container ports
* Building a custom Docker image
* Running a named container
* Docker port mapping
* Testing a containerized application with `curl`
* Troubleshooting containers using logs and port inspection

## 💡 Key Takeaway

> **Dockerizing an application means packaging the application's runtime, dependencies, and source code into a reproducible image so that it can run consistently as a container.**

The important flow from this lab was:

```text
Node.js Application
       ↓
   Dockerfile
       ↓
 docker build
       ↓
nautilus/node-web-app
       ↓
docker run
       ↓
nodeapp_nautilus
       ↓
Host :8098 → Container :6400
       ↓
     curl
```

---

