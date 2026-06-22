# 🚀 Docker Image Migration Across Servers

## 📌 Task Overview

Today I worked on a Docker image migration task where an existing custom image **official:datacenter** available on **Application Server 1** needed to be shared with another team working on **Application Server 3**.

The objective was not only to transfer the image but also to preserve its repository name and tag so that it could be used seamlessly on the destination server.

---

## 🛠️ What Was Done

### 1️⃣ Verified Existing Image

First, I verified that the required image was available on App Server 1.

```bash
docker images
```

Image identified:

```text
official:datacenter
```

---

### 2️⃣ Exported the Image

Docker images cannot be copied directly between servers, so the image was exported into a portable archive using:

```bash
docker save -o /tmp/official_datacenter.tar official:datacenter
```

This created a tar archive containing:

* Image layers
* Metadata
* Repository information
* Tag information

---

### 3️⃣ Transferred Archive to Another Server

The archive was securely transferred to App Server 3 using SCP.

```bash
scp /tmp/official_datacenter.tar banner@stapp03:/tmp/
```

This step simulated a real-world scenario where development or testing environments need the same container image.

---

### 4️⃣ Imported the Image

On App Server 3, the image was restored using:

```bash
docker load -i /tmp/official_datacenter.tar
```

Docker recreated the image exactly as it existed on the source server.

---

### 5️⃣ Validation

Finally, I verified that the image was available with the same repository name and tag.

```bash
docker images
```

Output confirmed:

```text
official    datacenter
```

---

# 📚 Key Learning

This task helped me understand the complete Docker image portability workflow:

### Docker Save

```bash
docker save
```

Used when we need to export an image and move it outside the current Docker host.

---

### SCP Transfer

```bash
scp
```

Provides a secure way to transfer image archives between Linux servers.

---

### Docker Load

```bash
docker load
```

Imports a previously exported image while preserving its repository and tag information.

---

# 💡 Real-World Use Cases

This workflow is commonly used when:

* Internet access is restricted
* Docker registries are unavailable
* Air-gapped environments are used
* Images need to be shared between staging and production servers
* Backup and disaster recovery procedures are required

---

# ✅ Outcome

Successfully exported, transferred, and imported the Docker image **official:datacenter** from **App Server 1** to **App Server 3** while preserving the original image name and tag.

### Skills Practiced

* Docker Image Management
* Docker Save & Load
* Secure File Transfer (SCP)
* Linux Server Administration
* Container Portability Concepts

---

