Absolutely. Since you're documenting the **hands-on WSL + Docker container lab** we’ve been doing, I’d structure the README as a practical step-by-step guide you can keep in your GitHub repo and continue updating.

# WSL + Docker Container Hands-On Lab

A practical step-by-step guide for learning **WSL, Linux, Docker, and containers** on Windows.

The goal of this lab is to understand how Windows, WSL, Docker Desktop, and Linux containers work together by actually running and managing containers.

---

## 1. Lab Architecture

The environment used in this lab is:

```text
Windows
   │
   ├── WSL 2
   │     │
   │     └── Ubuntu
   │            │
   │            └── Docker CLI
   │
   └── Docker Desktop
          │
          └── Docker Engine
                 │
                 └── Containers
                       └── Nginx
```

### Main Technologies

* Windows 10/11
* WSL 2
* Ubuntu
* Docker Desktop
* Docker Engine
* Docker CLI
* Nginx
* Linux containers

---

# 2. Verify Windows

Open **PowerShell as Administrator**.

Check the Windows version:

```powershell
winver
```

Or:

```powershell
systeminfo
```

---

# 3. Install WSL

From PowerShell:

```powershell
wsl --install
```

Restart Windows if requested.

After restarting, verify WSL:

```powershell
wsl --version
```

Check installed distributions:

```powershell
wsl --list --verbose
```

Expected example:

```text
NAME      STATE           VERSION
Ubuntu    Running         2
```

The important part is:

```text
VERSION 2
```

This confirms that Ubuntu is running using **WSL 2**.

---

# 4. Start Ubuntu

From PowerShell:

```powershell
wsl
```

Or:

```powershell
ubuntu
```

You should now be inside the Linux environment.

Example:

```text
azeez@computer:~$
```

Check the current user:

```bash
whoami
```

Check the Linux distribution:

```bash
cat /etc/os-release
```

Check the kernel:

```bash
uname -a
```

---

# 5. Understand Windows and Linux File Systems

WSL allows Linux to access Windows files.

Windows drives are mounted under:

```text
/mnt/
```

For example:

```bash
cd /mnt/c
```

Go to the Windows Users directory:

```bash
cd /mnt/c/Users
```

List the contents:

```bash
ls
```

You can also access your Windows Desktop:

```bash
cd /mnt/c/Users/<USERNAME>/Desktop
```

---

# 6. Update Ubuntu

Before installing or working with packages, update Ubuntu:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade -y
```

---

# 7. Check Whether Docker Is Available

Inside Ubuntu:

```bash
docker --version
```

Also check:

```bash
docker info
```

If Docker is not available, install **Docker Desktop for Windows** and enable WSL integration.

---

# 8. Configure Docker Desktop

Open Docker Desktop on Windows.

Go to:

```text
Settings
   → Resources
      → WSL Integration
```

Enable:

```text
Enable integration with my default WSL distro
```

Then enable Ubuntu.

Apply the changes.

Return to Ubuntu and test:

```bash
docker --version
```

Then:

```bash
docker info
```

If Docker is working, the Docker Engine information should be displayed.

---

# 9. Test Docker

Run the Docker test container:

```bash
docker run hello-world
```

Docker will:

1. Check whether the image exists locally.
2. Download the image if necessary.
3. Create a container.
4. Start the container.
5. Display a confirmation message.
6. Stop the container.

This confirms that Docker is working correctly.

---

# 10. Understand Images vs Containers

This is one of the most important Docker concepts.

### Docker Image

An image is a packaged template used to create containers.

Examples:

```text
nginx
ubuntu
node
python
postgres
redis
```

### Docker Container

A container is a running instance created from an image.

Think of it like:

```text
IMAGE
   ↓
docker run
   ↓
CONTAINER
```

For example:

```bash
docker run nginx
```

The `nginx` image is used to create an Nginx container.

---

# 11. Download an Nginx Image

Pull the Nginx image:

```bash
docker pull nginx
```

Check downloaded images:

```bash
docker images
```

You should see something similar to:

```text
REPOSITORY   TAG       IMAGE ID
nginx        latest    xxxxxxxx
```

---

# 12. Run Your First Nginx Container

Run Nginx:

```bash
docker run -d --name nginx-lab nginx
```

Explanation:

```text
docker run       → create and start a container
-d               → run in detached/background mode
--name nginx-lab → give the container a name
nginx            → image to use
```

Check running containers:

```bash
docker ps
```

---

# 13. Expose the Container to Windows

Run Nginx with port mapping:

```bash
docker run -d --name nginx-lab -p 8081:80 nginx
```

Port mapping:

```text
Windows Port 8081
       │
       ▼
Container Port 80
       │
       ▼
     Nginx
```

Check the port:

```bash
docker ps
```

Or:

```bash
docker ps --format "table {{.Names}}\t{{.Ports}}\t{{.Status}}"
```

Expected:

```text
NAMES       PORTS                  STATUS
nginx-lab   0.0.0.0:8081->80/tcp  Up ...
```

---

# 14. Access Nginx From Windows

Open your Windows browser and visit:

```text
http://localhost:8081
```

You should see the default Nginx page.

This demonstrates:

```text
Browser
   ↓
localhost:8081
   ↓
Docker port mapping
   ↓
Container port 80
   ↓
Nginx
```

---

# 15. Enter the Running Container

Find the container:

```bash
docker ps
```

Then enter it:

```bash
docker exec -it nginx-lab /bin/bash
```

You are now inside the container.

Check the operating system:

```bash
cat /etc/os-release
```

Check the hostname:

```bash
hostname
```

Check the current directory:

```bash
pwd
```

---

# 16. Explore the Nginx Container

Go to the Nginx web directory:

```bash
cd /usr/share/nginx/html
```

List the files:

```bash
ls
```

You should see:

```text
index.html
```

Display the page:

```bash
cat index.html
```

---

# 17. Modify the Nginx Web Page

Inside the container:

```bash
echo '<h1>Hello from Azeez Docker Lab 🚀</h1>' > /usr/share/nginx/html/index.html
```

Exit the container:

```bash
exit
```

Refresh:

```text
http://localhost:8081
```

The new page should now appear.

This demonstrates that you can interact directly with the filesystem inside a running container.

---

# 18. View Container Logs

Run:

```bash
docker logs nginx-lab
```

Follow the logs in real time:

```bash
docker logs -f nginx-lab
```

Press:

```text
CTRL + C
```

to stop following the logs.

---

# 19. Inspect the Container

Run:

```bash
docker inspect nginx-lab
```

This displays detailed container information, including:

* Container ID
* Image
* Network
* IP address
* Port mappings
* Mounts
* Environment
* Runtime configuration

---

# 20. Stop the Container

```bash
docker stop nginx-lab
```

Check:

```bash
docker ps
```

The container should no longer appear among running containers.

To show stopped containers:

```bash
docker ps -a
```

---

# 21. Start the Container Again

```bash
docker start nginx-lab
```

Check:

```bash
docker ps
```

The container should be running again.

---

# 22. Restart a Container

```bash
docker restart nginx-lab
```

---

# 23. Remove a Container

First stop it:

```bash
docker stop nginx-lab
```

Then remove it:

```bash
docker rm nginx-lab
```

Verify:

```bash
docker ps -a
```

---

# 24. Remove a Docker Image

List images:

```bash
docker images
```

Remove the Nginx image:

```bash
docker rmi nginx
```

If a container still depends on the image, remove the container first.

---

# 25. Useful Docker Commands

### List running containers

```bash
docker ps
```

### List all containers

```bash
docker ps -a
```

### List images

```bash
docker images
```

### Pull an image

```bash
docker pull <image>
```

### Run a container

```bash
docker run <image>
```

### Run in background

```bash
docker run -d <image>
```

### Give a container a name

```bash
docker run --name <container-name> <image>
```

### Map a port

```bash
docker run -p <host-port>:<container-port> <image>
```

### Stop a container

```bash
docker stop <container>
```

### Start a container

```bash
docker start <container>
```

### Restart a container

```bash
docker restart <container>
```

### Remove a container

```bash
docker rm <container>
```

### View logs

```bash
docker logs <container>
```

### Enter a container

```bash
docker exec -it <container> /bin/bash
```

### Inspect a container

```bash
docker inspect <container>
```

---

# 26. Docker Container Lifecycle

The basic lifecycle is:

```text
Docker Image
     │
     ▼
docker run
     │
     ▼
Created
     │
     ▼
Running
     │
     ├── docker stop
     │       ↓
     │    Stopped
     │
     └── docker restart
             ↓
          Running
             
Stopped
   │
   ▼
docker rm
   │
   ▼
Removed
```

---

# 27. Important Docker Concepts to Learn Next

After understanding basic containers, continue with:

### Level 1 — Docker Fundamentals

* Images
* Containers
* Ports
* Logs
* Container lifecycle
* Docker commands

### Level 2 — Container Storage

Learn:

* Volumes
* Bind mounts
* Persistent data

Example:

```bash
docker volume create nginx-data
```

---

### Level 3 — Container Networking

Learn:

* Bridge networks
* Container-to-container communication
* DNS
* Port mapping

Example:

```bash
docker network ls
```

Create a network:

```bash
docker network create lab-network
```

---

### Level 4 — Dockerfile

Learn how to build your own image.

Example:

```dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html
```

Build:

```bash
docker build -t my-nginx .
```

Run:

```bash
docker run -d --name my-nginx -p 8082:80 my-nginx
```

---

### Level 5 — Docker Compose

Learn how to run multiple containers together.

Example architecture:

```text
                 Docker Compose
                      │
          ┌───────────┴───────────┐
          │                       │
       Frontend                Backend
          │                       │
          └───────────┬───────────┘
                      │
                   Database
```

This is important for understanding real-world application deployments.

---

# 28. DevOps Connection

The purpose of this lab is not just learning Docker commands.

The concepts connect directly to DevOps.

```text
Developer
    ↓
GitHub
    ↓
Dockerfile
    ↓
Docker Image
    ↓
Container
    ↓
CI/CD Pipeline
    ↓
Container Registry
    ↓
Azure / Kubernetes
```

For example:

```text
GitHub
   ↓
GitHub Actions
   ↓
docker build
   ↓
docker push
   ↓
Azure Container Registry
   ↓
Azure Container Apps / AKS
```

Understanding Docker locally makes it easier to understand **Kubernetes, Azure Container Apps, AKS, CI/CD and container registries**.

---

