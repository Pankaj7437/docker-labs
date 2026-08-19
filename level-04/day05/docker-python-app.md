Dockerize and Deploy Python Application – Nautilus

📌 Lab Overview

The Nautilus DevOps team needs to containerize a Python application and deploy it on App Server 1.

The application dependencies have already been provided in:

/python_app/src/requirements.txt

The task is to create a Dockerfile, build the required Docker image, run the application as a container, and verify it using curl.


---

🎯 Requirements

Requirement	Configuration

Server	App Server 1
Application Directory	/python_app
Requirements File	/python_app/src/requirements.txt
Dockerfile	/python_app/Dockerfile
Base Image	Any Python image
Application Script	server.py
Container Port	6300
Host Port	8093
Image Name	nautilus/python-app
Container Name	pythonapp_nautilus



---

1. Connect to App Server 1

From the Jump Host:

ssh <user>@stapp01


---

2. Navigate to the Application Directory

cd /python_app

Check the available files:

ls -la

The provided dependency file is located at:

/python_app/src/requirements.txt

The application is expected to contain:

/python_app
└── src
    ├── requirements.txt
    └── server.py


---

3. Create the Dockerfile

Create the Dockerfile directly under /python_app:

vi /python_app/Dockerfile

Use:

FROM python:3

WORKDIR /app

COPY src/requirements.txt .

RUN pip install -r requirements.txt

COPY src/server.py .

EXPOSE 6300

CMD ["python", "server.py"]


---

🧠 Dockerfile Explanation

FROM

FROM python:3

Uses a Python image as the base image.

This provides Python and pip inside the container.


---

WORKDIR

WORKDIR /app

Creates/sets /app as the working directory inside the container.

Commands that follow operate from this directory.


---

Copy requirements.txt

COPY src/requirements.txt .

The source path is relative to the Docker build context.

If the image is built from:

cd /python_app
docker build -t nautilus/python-app .

then:

src/requirements.txt

refers to:

/python_app/src/requirements.txt

It is copied into:

/app/requirements.txt

inside the image.


---

Install Dependencies

RUN pip install -r requirements.txt

This reads requirements.txt and installs all required Python packages into the image.


---

Copy server.py

COPY src/server.py .

Copies:

/python_app/src/server.py

into:

/app/server.py

inside the image.


---

Expose Port

EXPOSE 6300

Documents that the application listens on port 6300 inside the container.

EXPOSE itself does not publish the port to the host.


---

Start the Application

CMD ["python", "server.py"]

When the container starts, Docker executes:

python server.py


---

4. Build the Docker Image

Move into the application directory:

cd /python_app

Build the image:

docker build -t nautilus/python-app .

Breakdown

docker build
     │
     ├── -t nautilus/python-app
     │       └── Required image name
     │
     └── .
         └── /python_app as build context


---

5. Verify the Image

docker images

The image should appear as:

nautilus/python-app

You can also check specifically:

docker images nautilus/python-app


---

6. Create and Run the Container

Run:

docker run -d \
  --name pythonapp_nautilus \
  -p 8093:6300 \
  nautilus/python-app

Port Mapping

Host Port 8093
      │
      │ Docker port mapping
      ▼
Container Port 6300
      │
      ▼
Python Application

The syntax is:

-p HOST_PORT:CONTAINER_PORT

Therefore:

-p 8093:6300

means:

8093 → 6300


---

7. Verify the Container

Check running containers:

docker ps

The container should be:

pythonapp_nautilus

You can also check all containers:

docker ps -a


---

8. Check Application Logs

If the container is not running:

docker logs pythonapp_nautilus

For continuously watching logs:

docker logs -f pythonapp_nautilus

This is particularly useful if server.py exits because of a missing dependency or configuration problem.


---

9. Verify Port Mapping

Run:

docker port pythonapp_nautilus

You should see a mapping similar to:

6300/tcp -> 0.0.0.0:8093


---

10. Test the Application

The lab requires:

curl http://localhost:8093/

The request follows:

curl
 │
 ▼
localhost:8093
 │
 │ Docker port mapping
 ▼
Container:6300
 │
 ▼
server.py
 │
 ▼
Python Application

If the application is working, curl should return the application's response.


---

📊 Architecture

App Server 1
                            │
                     /python_app
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
        Dockerfile              src/requirements.txt
              │                           │
              │                           │
              └─────────────┬─────────────┘
                            │
                      docker build
                            │
                            ▼
               ┌─────────────────────────┐
               │  nautilus/python-app    │
               │                         │
               │       Python            │
               │          │              │
               │          ▼              │
               │      server.py          │
               │                         │
               │      Port 6300          │
               └────────────┬────────────┘
                            │
                       docker run
                            │
                            ▼
               ┌─────────────────────────┐
               │ pythonapp_nautilus      │
               │                         │
               │ Container :6300         │
               └────────────┬────────────┘
                            │
                     Host :8093
                            │
                            ▼
                  curl localhost:8093


---

🔧 Troubleshooting

Container exits immediately

Check:

docker ps -a

Then:

docker logs pythonapp_nautilus


---

requirements.txt not found during build

Make sure you are building from:

cd /python_app

and use:

docker build -t nautilus/python-app .

Because:

COPY src/requirements.txt .

expects src to be inside the build context.


---

server.py not found

Verify:

ls -la /python_app/src/

You should have:

requirements.txt
server.py


---

Port already in use

Check:

docker ps

or:

ss -lntp | grep 8093

The required mapping is:

8093:6300


---

🧪 Final Verification Checklist

Dockerfile exists

ls -l /python_app/Dockerfile

Build image

cd /python_app
docker build -t nautilus/python-app .

Verify image

docker images | grep nautilus/python-app

Run container

docker run -d \
  --name pythonapp_nautilus \
  -p 8093:6300 \
  nautilus/python-app

Verify container

docker ps

Verify port

docker port pythonapp_nautilus

Test application

curl http://localhost:8093/


---

📚 What I Learned

This lab provided practical experience with Dockerizing a Python application.

Key concepts practiced:

Creating a Dockerfile

Using a Python base image

Setting a container working directory

Copying application dependency files

Installing dependencies using pip

Copying Python application code

Using CMD to start an application

Exposing container ports

Building a custom Docker image

Running a named container

Mapping host and container ports

Checking container logs

Testing a containerized application using curl


💡 Key Takeaway

> A Dockerfile packages the application's runtime, dependencies, and source code into a reproducible image. The resulting image can then be deployed as a container with the required port mapping.



The overall workflow was:

Python Application
       ↓
requirements.txt + server.py
       ↓
    Dockerfile
       ↓
   docker build
       ↓
nautilus/python-app
       ↓
   docker run
       ↓
pythonapp_nautilus
       ↓
8093 → 6300
       ↓
curl localhost:8093

