# Docker Compose – PHP Apache + MariaDB Stack

## 📌 Lab Overview

In this KodeKloud lab, the Nautilus Application development team required a **containerized PHP and MariaDB stack** on **App Server 2** in the Stratos Datacenter.

The deployment had to be created using Docker Compose with two services:

* **Web:** PHP with Apache
* **Database:** MariaDB

The Compose file had to be created at the exact location:

```bash
/opt/security/docker-compose.yml
```

---

# 🎯 Requirements

### Web Service

| Requirement      | Configuration   |
| ---------------- | --------------- |
| Service          | `web`           |
| Image            | `php:apache`    |
| Container        | `php_apache`    |
| Host Port        | `3002`          |
| Container Port   | `80`            |
| Host Volume      | `/var/www/html` |
| Container Volume | `/var/www/html` |

### Database Service

| Requirement      | Configuration        |
| ---------------- | -------------------- |
| Service          | `DB`                 |
| Image            | `mariadb:latest`     |
| Container        | `mysql_apache`       |
| Host Port        | `3306`               |
| Container Port   | `3306`               |
| Host Volume      | `/var/lib/mysql`     |
| Container Volume | `/var/lib/mysql`     |
| Database         | `database_apache`    |
| User             | Custom non-root user |
| Password         | Custom password      |

---

# 1. Connect to App Server 2

From the Jump Host:

```bash
ssh steve@stapp02
```

The first connection may ask for host verification:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Enter:

```text
yes
```

---

# 2. Create the Required Directory

Initially, attempting:

```bash
cd opt/security
```

failed because the path was missing the leading `/`.

The correct absolute path is:

```bash
cd /opt/security
```

Since the directory did not exist, it was created using:

```bash
mkdir -p /opt/security
```

Then:

```bash
cd /opt/security
```

---

# 3. Create the Docker Compose File

The required file was:

```text
/opt/security/docker-compose.yml
```

It was created using:

```bash
sudo nano docker-compose.yml
```

`vim` was not installed on the server:

```text
sudo: vim: command not found
```

Therefore, `vi` was used:

```bash
sudo vi docker-compose.yml
```

---

# 4. Final Docker Compose Configuration

The final working configuration was:

```yaml
services:
  web:
    image: php:apache
    container_name: php_apache
    ports:
      - "3002:80"
    volumes:
      - /var/www/html:/var/www/html

  DB:
    image: mariadb:latest
    container_name: mysql_apache
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_apache
      MYSQL_USER: apacheuser
      MYSQL_PASSWORD: Apache@12345
      MYSQL_ROOT_PASSWORD: Root@12345
```

> The exact credentials used in this lab were custom values. In a real production environment, passwords should not be stored directly in a Compose file committed to source control.

---

# 5. YAML Troubleshooting

During the first attempts, Docker Compose reported:

```text
yaml: line 4: found character that cannot start any token
```

After another correction:

```text
yaml: line 3: found character that cannot start any token
```

Later:

```text
yaml: line 19: mapping values are not allowed in this context
```

These errors indicated that the YAML syntax/formatting was incorrect.

### Common YAML issues to check

YAML is sensitive to:

* Indentation
* Spaces
* Colons
* Tabs
* Quotation marks
* Key/value formatting

For example:

```yaml
MYSQL_DATABASE: database_apache
```

is valid.

While incorrectly formatted YAML can result in parsing errors.

A good practice is to use **spaces instead of tabs** for indentation.

---

# 6. Start the Docker Compose Stack

After correcting the YAML configuration:

```bash
sudo docker compose up -d
```

Docker successfully processed the Compose file.

The output showed:

```text
[+] up 27/27
```

Docker then pulled the required images:

```text
Image mariadb:latest   Pulled
Image php:apache       Pulled
```

---

# 7. Docker Network Creation

Compose automatically created a network:

```text
Network app_default Created
```

Docker Compose creates a dedicated network for the services so that the containers can communicate with each other.

Conceptually:

```text
             app_default network
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
   ┌─────────────┐     ┌─────────────┐
   │ php_apache  │────▶│ mysql_apache│
   │ PHP + Apache│     │   MariaDB   │
   └─────────────┘     └─────────────┘
```

---

# 8. Containers Created

The final output confirmed:

```text
Container mysql_apache Created
Container php_apache   Created
```

Therefore, both required containers were successfully created.

---

# 9. Verify the Containers

After deployment, verify the running containers:

```bash
sudo docker ps
```

Expected containers:

```text
php_apache
mysql_apache
```

You can also use:

```bash
sudo docker compose ps
```

to see the Compose-managed services.

---

# 10. Application Access

The lab specifies that the application can be accessed through port `3002`.

The port mapping is:

```text
Host :3002
      │
      ▼
Container :80
      │
      ▼
PHP + Apache
```

Test locally on the server:

```bash
curl localhost:3002/
```

Or from another machine:

```bash
curl <server-ip>:3002/
```

---

# 📊 Architecture Diagram

```text
                         App Server 2
                       Stratos Datacenter
                              │
                              │
                     Docker Compose
                              │
                 /opt/security/docker-compose.yml
                              │
                ┌─────────────┴─────────────┐
                │                           │
                ▼                           ▼
       ┌─────────────────┐         ┌─────────────────┐
       │   php_apache    │         │   mysql_apache  │
       │                 │         │                 │
       │   php:apache    │         │ mariadb:latest  │
       │                 │         │                 │
       │ Container :80   │         │ Container :3306 │
       └────────┬────────┘         └────────┬────────┘
                │                           │
          Host :3002                  Host :3306
                │                           │
                ▼                           │
           Web Browser                      │
                │                           │
                └───────────┐               │
                            ▼               │
                       PHP Application ─────┘
```

---

# 💾 Volume Mapping

## PHP/Apache

```yaml
volumes:
  - /var/www/html:/var/www/html
```

This maps:

```text
Host:
/var/www/html

        │
        ▼

Container:
/var/www/html
```

The same application directory is therefore available inside the PHP/Apache container.

---

## MariaDB

```yaml
volumes:
  - /var/lib/mysql:/var/lib/mysql
```

This maps:

```text
Host:
/var/lib/mysql

        │
        ▼

Container:
/var/lib/mysql
```

This provides persistent storage for MariaDB's database files.

---

# 🔌 Port Mapping

### PHP Apache

```yaml
ports:
  - "3002:80"
```

means:

```text
Host Port 3002
      │
      ▼
Container Port 80
```

So the application is accessible through:

```bash
curl <server-ip>:3002/
```

### MariaDB

```yaml
ports:
  - "3306:3306"
```

means:

```text
Host Port 3306
      │
      ▼
Container Port 3306
```

---

# 🧠 Key Docker Compose Concepts Learned

### `services`

Defines the containers/services that Compose manages:

```yaml
services:
```

---

### `image`

Specifies the image used to create a container:

```yaml
image: php:apache
```

and:

```yaml
image: mariadb:latest
```

---

### `container_name`

Explicitly defines the container name:

```yaml
container_name: php_apache
```

and:

```yaml
container_name: mysql_apache
```

---

### `ports`

Maps host ports to container ports:

```yaml
ports:
  - "3002:80"
```

---

### `volumes`

Creates a host-to-container bind mount:

```yaml
volumes:
  - /var/www/html:/var/www/html
```

---

### `environment`

Provides environment variables to the container:

```yaml
environment:
  MYSQL_DATABASE: database_apache
  MYSQL_USER: apacheuser
  MYSQL_PASSWORD: Apache@12345
  MYSQL_ROOT_PASSWORD: Root@12345
```

These variables are used by the MariaDB image during initialization.

---

# 🔧 Useful Troubleshooting Commands

Check Compose services:

```bash
sudo docker compose ps
```

Check all containers:

```bash
sudo docker ps -a
```

Check PHP container logs:

```bash
sudo docker logs php_apache
```

Check MariaDB logs:

```bash
sudo docker logs mysql_apache
```

View Compose logs:

```bash
sudo docker compose logs
```

Stop the stack:

```bash
sudo docker compose down
```

Start again:

```bash
sudo docker compose up -d
```

Rebuild/recreate:

```bash
sudo docker compose up -d --force-recreate
```

---

# 🧪 Final Verification Checklist

Before considering the lab complete:

```bash
cd /opt/security
```

Verify the file:

```bash
ls -l docker-compose.yml
```

Check Compose configuration:

```bash
sudo docker compose config
```

Start the stack:

```bash
sudo docker compose up -d
```

Verify:

```bash
sudo docker compose ps
```

Check:

```bash
sudo docker ps
```

Expected:

```text
php_apache
mysql_apache
```

Finally:

```bash
curl localhost:3002/
```

---

# 📚 What I Learned

This lab provided practical experience with deploying a **two-tier application stack using Docker Compose**.

### Main concepts practiced:

* Creating a Docker Compose file from requirements
* PHP + Apache container deployment
* MariaDB container deployment
* Docker image selection
* Container naming
* Port mapping
* Host-to-container bind mounts
* MariaDB environment variables
* Docker Compose networking
* YAML indentation and syntax troubleshooting
* Reading and resolving Docker Compose errors
* Verifying containers after deployment
* Testing an application using `curl`

## 💡 Key Takeaway

> **Docker Compose allows multiple application components such as a web server and database to be defined, configured, networked, and deployed together from a single YAML file.**

This lab was particularly useful for understanding how **application containers, database containers, networking, ports, environment variables, and persistent storage** fit together in a containerized deployment.

---
