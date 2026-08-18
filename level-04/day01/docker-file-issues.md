# Dockerfile Troubleshooting – Apache SSL Image Build

## 📌 Lab Overview

This KodeKloud lab involved troubleshooting a failing **Docker image build** on **App Server 2** in the Stratos Datacenter.

An existing `Dockerfile` was provided under:

```bash
/opt/docker
```

The Dockerfile was designed to create an Apache HTTP Server image with:

* Apache `httpd:2.4.43`
* Apache listening on port `8080`
* SSL enabled
* `server.crt` and `server.key`
* An existing `index.html`

The Docker build was failing because the Dockerfile was attempting to use `RUN cp` to copy files that existed only in the **Docker build context**.

The task was to troubleshoot and fix the Dockerfile without changing the base image or the existing application data.

---

# 🎯 Objective

The objective was to:

* Investigate the Docker build failure.
* Understand why the existing `RUN cp` commands failed.
* Correct the Dockerfile.
* Preserve the existing Apache and SSL configuration.
* Use the existing certificate and HTML files.
* Successfully build the Docker image.

---

# 1. Connect to App Server 2

From the Jump Host:

```bash
ssh steve@stapp02
```

Navigate to the Docker directory:

```bash
cd /opt/docker
```

Check the files:

```bash
ls
```

Output:

```text
Dockerfile
certs
html
```

---

# 2. Inspect the Dockerfile

The original Dockerfile contained:

```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

RUN cp certs/server.crt /usr/local/apache2/conf/server.crt

RUN cp certs/server.key /usr/local/apache2/conf/server.key

RUN cp html/index.html /usr/local/apache2/htdocs/
```

The Apache configuration itself was valid and did not need to be changed.

---

# 3. Attempt to Build the Image

The image was built using:

```bash
docker build -t myapp .
```

The build failed with:

```text
cp: cannot stat 'certs/server.crt': No such file or directory
```

The failure occurred at:

```dockerfile
RUN cp certs/server.crt /usr/local/apache2/conf/server.crt
```

---

# 4. Investigate the File Locations

The host directory was checked:

```bash
ls
```

The structure was:

```text
/opt/docker
│
├── Dockerfile
├── certs
│   ├── server.crt
│   └── server.key
│
└── html
    └── index.html
```

So the files **did exist on the server**.

The problem was not that the files were missing.

The problem was **where the command was being executed**.

---

# 🧠 Understanding the Problem

The important Docker concept here is the difference between:

```dockerfile
RUN cp ...
```

and:

```dockerfile
COPY ...
```

### `RUN`

A `RUN` instruction executes a command **inside the image being built**.

For example:

```dockerfile
RUN cp certs/server.crt /usr/local/apache2/conf/server.crt
```

The command looks for:

```text
certs/server.crt
```

**inside the image filesystem**.

But the `certs` directory existed on the host/build context:

```text
/opt/docker/certs
```

It had not yet been copied into the image.

Therefore `cp` could not find it.

---

# 5. `COPY` Is the Correct Instruction

Docker provides `COPY` specifically for transferring files from the build context into the image.

For example:

```dockerfile
COPY ./certs/server.crt /usr/local/apache2/conf/server.crt
```

means:

```text
Docker Build Context
        │
        │ COPY
        ▼
Docker Image
```

Similarly:

```dockerfile
COPY ./certs/server.key /usr/local/apache2/conf/server.key
```

and:

```dockerfile
COPY ./html/index.html /usr/local/apache2/htdocs/
```

---

# 6. Corrected Dockerfile

The final corrected Dockerfile was:

```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY ./certs/server.crt /usr/local/apache2/conf/server.crt

COPY ./certs/server.key /usr/local/apache2/conf/server.key

COPY ./html/index.html /usr/local/apache2/htdocs/
```

### What was changed?

Only the three file-copy instructions were changed:

```dockerfile
RUN cp certs/server.crt ...
RUN cp certs/server.key ...
RUN cp html/index.html ...
```

became:

```dockerfile
COPY ./certs/server.crt ...
COPY ./certs/server.key ...
COPY ./html/index.html ...
```

The following were **not changed**:

* Base image
* Apache configuration
* SSL configuration
* Certificate files
* Private key
* `index.html`
* Port configuration

---

# 7. Understanding `./`

The corrected Dockerfile uses:

```dockerfile
COPY ./certs/server.crt ...
```

The `./` means the path is relative to the **Docker build context**.

Because the build was executed from:

```bash
cd /opt/docker
docker build -t myapp .
```

the build context was:

```text
/opt/docker
```

Therefore:

```text
./certs/server.crt
```

refers to:

```text
/opt/docker/certs/server.crt
```

and:

```text
./html/index.html
```

refers to:

```text
/opt/docker/html/index.html
```

The `./` itself was **not the main issue**. The important issue was using `RUN cp` instead of `COPY`.

---

# 8. Build the Image Again

After correcting the Dockerfile:

```bash
docker build -t myapp .
```

The build completed successfully.

The important output was:

```text
[+] Building ... FINISHED
```

and Docker created:

```text
myapp:latest
```

---

# 9. Verify the Image

Run:

```bash
docker images
```

The image should appear as:

```text
REPOSITORY   TAG       IMAGE ID       SIZE
myapp        latest    ...            ...
```

This confirms that the Dockerfile was successfully corrected and the image was built.

---

# 🏗️ Build Context Diagram

```text
                 App Server 2
                 /opt/docker
                      │
                      │
              docker build .
                      │
                      ▼
             ┌─────────────────┐
             │  Build Context  │
             ├─────────────────┤
             │ Dockerfile      │
             │                 │
             │ certs/          │
             │ ├─ server.crt   │
             │ └─ server.key   │
             │                 │
             │ html/           │
             │ └─ index.html   │
             └────────┬────────┘
                      │
                    COPY
                      │
                      ▼
             ┌─────────────────────┐
             │    Docker Image     │
             │    myapp:latest     │
             ├─────────────────────┤
             │ /usr/local/apache2/ │
             │                     │
             │ server.crt          │
             │ server.key          │
             │ htdocs/index.html   │
             └─────────────────────┘
```

---

# 🔑 Important Docker Concepts

## `RUN`

Used to execute commands **inside the image during the build**.

Example:

```dockerfile
RUN sed -i "s/Listen 80/Listen 8080/g" ...
```

---

## `COPY`

Used to copy files from the **Docker build context into the image**.

Example:

```dockerfile
COPY ./html/index.html /usr/local/apache2/htdocs/
```

---

## Docker Build Context

When running:

```bash
docker build -t myapp .
```

the `.` tells Docker that the current directory is the build context.

In this lab:

```text
/opt/docker
```

was the build context.

---

# 🔄 `RUN cp` vs `COPY`

```text
RUN cp
   │
   ▼
Command runs INSIDE image
   │
   └── Source must already exist inside image


COPY
   │
   ▼
Host/Build Context
       │
       ▼
     Image
```

This distinction was the key to solving the lab.

---

# Commands Used

```bash
ssh steve@stapp02

cd /opt/docker

ls

cat Dockerfile

docker build -t myapp .

cd certs
ls

cd ..

sudo nano Dockerfile

docker build -t myapp .

docker images
```

---

# 📚 What I Learned

This lab strengthened my understanding of:

* Dockerfile troubleshooting
* Docker build context
* `RUN` vs `COPY`
* Relative paths in Dockerfiles
* Apache Docker images
* SSL configuration inside containers
* Reading Docker build errors
* Debugging missing-file errors
* Building and verifying Docker images

## Key Takeaway

> **`RUN` executes commands inside the image, while `COPY` transfers files from the Docker build context into the image.**

When a Docker build reports that a file cannot be found, first check:

```bash
pwd
ls -la
find . -maxdepth 2 -type f
```

Then verify whether the file needs to be brought into the image using `COPY`.

---
