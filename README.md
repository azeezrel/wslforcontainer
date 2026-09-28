# WSL Container Lab — WSL2 + Docker (Container-focused README)

This lab covers using WSL2 (Ubuntu) as your development environment for Docker container experiments. It teaches: running containers, port mapping, inspecting containers, exec'ing into containers, modifying container files, and understanding persistence vs. volumes. Include the provided screenshots in the `images/` folder (see the "Images" section).

## Objective

- Run an NGINX container alongside an existing WordPress container (avoid host port conflicts).
- Inspect and enter the container, modify the default webpage, and test persistence across restarts.

## Prerequisites

- Windows 10/11 with WSL2 enabled
- Ubuntu (or other Linux) distribution in WSL2
- Docker Desktop with WSL2 integration enabled
- VS Code (optional) with Remote - WSL

Verify WSL and Docker:

```powershell
wsl --version
wsl --status
wsl --list --verbose
```

```bash
docker version
docker info
```

## Quick Notes / Best Practices

- Keep your project files inside the WSL filesystem (`~/...`) rather than under `/mnt/c/...` for better I/O performance.
- Containers are ephemeral: changes to the container's writable layer may be lost unless persisted with volumes or bind mounts.

## Lab: Run NGINX alongside WordPress (host ports 8081 and 8080)

This section follows the exact flow used in the lab screenshots. Replace or add screenshots into `images/` and the README will show them inline.

### 1) Check current working directory and running containers

Recommended working directory: `~/container-lab` (but in the screenshots the user was in `/mnt/c/Users/Administrator`).

Commands:

```bash
pwd
docker ps --format "table {{.Names}}\t{{.Ports}}\t{{.Status}}"
```

![pwd + docker ps output](images/01-pwd-docker-ps.png)

### 2) Remove an old container (if it exists) and run NGINX on host port 8081

```bash
docker rm nginx-lab    # remove previous container if present
docker run -d --name nginx-lab -p 8081:80 nginx
docker ps --format "table {{.Names}}\t{{.Ports}}\t{{.Status}}"
```

![run nginx and docker ps](images/01-pwd-docker-ps.png)

Explanation: The container exposes port `80` internally. `-p 8081:80` maps container port 80 to host port 8081.

### 3) Test HTTP response from the host

```bash
curl -I http://localhost:8081
```

Expected: `HTTP/1.1 200 OK` and NGINX headers.

![curl headers and start of inspect](images/02-curl-docker-inspect.png)

### 4) Inspect the container

```bash
docker inspect nginx-lab

# or to get the container IP only:
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx-lab
```

This shows the container JSON (networking, ports, mounts, state). Example bridge IP: `172.17.0.2`.

![full docker inspect output](images/03-docker-inspect-full.png)
![extracted container IP](images/04-container-ip.png)

### 5) Exec into the running container and verify files

```bash
docker exec -it nginx-lab /bin/bash
# inside the container
cat /usr/share/nginx/html/index.html
cat /etc/os-release
exit
```

You should see the NGINX default page content and the container's OS info (Debian in the screenshots).

![inside container, cat index.html and os-release](images/05-inside-container.png)

### 6) Modify the default webpage (inside container or via bind mount)

From inside the container (or via a bind-mounted file), replace the index.html:

```bash
docker exec -it nginx-lab /bin/bash
echo '<h1>Hello from Azeez Docker Lab 🚀</h1>' > /usr/share/nginx/html/index.html
cat /usr/share/nginx/html/index.html
exit
```

Refresh `http://localhost:8081` to verify the change.

### 7) Stop, start and test persistence

```bash
docker stop nginx-lab
docker start nginx-lab
curl -I http://localhost:8081
docker exec -it nginx-lab cat /usr/share/nginx/html/index.html
```

If the change is gone after restart, this proves the container's writable layer was ephemeral — use volumes to persist files.

### 8) Quick note: Docker images on your host

You can list images present on the host:

```bash
docker images
```

![docker images list](images/06-docker-images-1.png)
![docker images (continued) & example run](images/07-docker-images-2.png)

## Where to put the lab screenshots

- Copy the screenshots you provided into a new `images/` folder next to this README. Use the filenames used above (or update the Markdown links to match your filenames).

Suggested filenames (matching the embedded references):

- `images/01-pwd-docker-ps.png`
- `images/02-curl-docker-inspect.png`
- `images/03-docker-inspect-full.png`
- `images/04-container-ip.png`
- `images/05-inside-container.png`
- `images/06-docker-images-1.png`
- `images/07-docker-images-2.png`

Place them with:

```bash
mkdir -p images
# copy your screenshots into images/ with the names above (or change the links in this README)
```

## Short Glossary

- Container: A running instance of an image (a set of Linux namespaces and cgroups around a process).
- Image: Read-only template built from a `Dockerfile`.
- Bind mount: Map a host directory/file into a container (good for development).
- Volume: Managed Docker storage for persisting container data.

## Next steps (suggested)

- Convert the edited web content into a bind mount or Docker volume so changes survive container restarts.
- Create a Dockerfile and build a custom image that embeds your HTML permanently.
- Learn `docker-compose` to manage WordPress + NGINX stacks together.

---

If you want, I can also:

- Add the actual screenshots into the repo (you can upload them here), or
- Replace the placeholders with your filenames and regenerate the README.
