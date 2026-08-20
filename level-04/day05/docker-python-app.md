## 📌 Lab Overview & Requirements

The Nautilus DevOps team requires a Python application to be containerized and deployed on App Server 1. This guide covers packaging the app with its dependencies, building a custom Docker image, and running it with the correct port mappings.

| Requirement | Configuration |
| --- | --- |
| **Server** | App Server 1 (`stapp01`) |
| **App Directory** | `/python_app` |
| **Requirements File** | `/python_app/src/requirements.txt` |
| **Base Image** | `python:3` |
| **App Script** | `server.py` |
| **Ports (Host:Container)** | `8093:6300` |
| **Image & Container Name** | `nautilus/python-app` & `pythonapp_nautilus` |

---

## 🚀 Deployment Steps

Execute the following commands to build and deploy the containerized application.

**1. Connect and Setup**

```bash
ssh <user>@stapp01
cd /python_app

```

**2. Create the Dockerfile**
Create a `Dockerfile` in `/python_app` to define the environment, install dependencies, and start the application.

```dockerfile
FROM python:3
WORKDIR /app
COPY src/requirements.txt .
RUN pip install -r requirements.txt
COPY src/server.py .
EXPOSE 6300
CMD ["python", "server.py"]

```

**3. Build and Run**

```bash
# Build the Docker image
docker build -t nautilus/python-app .

# Run the container in detached mode with port mapping
docker run -d --name pythonapp_nautilus -p 8093:6300 nautilus/python-app

```

---

## 🔧 Troubleshooting & Verification

Use this checklist to verify your deployment or debug common issues if the application fails to start.

* **Verify Image & Container:** Use `docker images | grep python-app` and `docker ps` to ensure the container is actively running.
* **Check Logs:** If the container exits immediately, inspect it using `docker logs pythonapp_nautilus`.
* **Confirm Port Mapping:** Run `docker port pythonapp_nautilus` to verify `6300/tcp -> 0.0.0.0:8093`.
* **Build Context Errors:** If `requirements.txt` is not found, ensure you are running `docker build` directly inside `/python_app` so the `src/` directory is in the build context.
* **Test the Application:** Finally, execute `curl http://localhost:8093/` to confirm the app returns a successful response.

---

## 📚 Architecture & Learnings

This deployment maps a host port to a containerized Python environment, ensuring isolated and reproducible execution.

```text
 App Server 1 (Host: 8093)
       │
       ▼
 ┌─────────────────────────┐
 │ pythonapp_nautilus      │
 │ Container Port: 6300    │
 │   │                     │
 │   ▼                     │
 │ server.py (Python 3)    │
 └─────────────────────────┘

```

> **💡 Key Takeaway:** A Dockerfile efficiently packages an application's runtime, dependencies, and source code into a reproducible image, allowing seamless deployment via simple port mapping between the host and the container.

---

Would you like to add a section for environment variables or a specific GitHub Actions workflow to automate this build?
