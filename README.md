# WSL Container Lab — WSL2 + Docker (Container-focused README)

This lab is a concise, reproducible guide for doing container experiments inside WSL2 (Ubuntu). It covers running containers, port mapping, container inspection, exec-ing into containers, simple file edits inside containers, and demonstrating ephemeral storage vs. volumes.

Important: To make images display reliably on GitHub, add your screenshot files into an `images/` folder at the repository root. Filenames must exactly match the links used in this README (case-sensitive on some platforms).

---

## Objective

- Run an NGINX container on host port `8081` while WordPress runs on `8080`.
- Inspect the NGINX container, change its default webpage, and test persistence behavior.

## Prerequisites

- Windows 10/11 with WSL2 enabled
- Ubuntu (or another WSL2 distribution)
- Docker Desktop with WSL2 integration enabled
- (Optional) VS Code with Remote - WSL

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

## Best practices

- Keep your project files inside WSL (`~/...`) not under `/mnt/c/...` to avoid slow I/O.
- Use Docker volumes or bind mounts to persist files across container restarts.

---

## Lab steps (with images)

Place the images into `images/` with the exact filenames below. Then push them to GitHub; they will render inline.

### 1) Check working directory and running containers

Commands:

```bash
pwd
docker ps --format "table {{.Names}}\t{{.Ports}}\t{{.Status}}"
```

Image: `images/01-pwd-docker-ps.png`

![pwd + docker ps output](images/01-pwd-docker-ps.png)

### 2) Remove any previous container and run NGINX on host port 8081

```bash
docker rm nginx-lab  # remove if present
docker run -d --name nginx-lab -p 8081:80 nginx
docker ps --format "table {{.Names}}\t{{.Ports}}\t{{.Status}}"
```

Image: `images/01-pwd-docker-ps.png` (same screenshot shows run and ps)

![run nginx and docker ps](images/01-pwd-docker-ps.png)

### 3) Test NGINX from host

```bash
curl -I http://localhost:8081
```

Image: `images/02-curl-docker-inspect.png`

![curl headers and start of inspect](images/02-curl-docker-inspect.png)

### 4) Inspect the container

```bash
docker inspect nginx-lab

# or to get the container IP only:
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx-lab
```

Images: `images/03-docker-inspect-full.png`, `images/04-container-ip.png`

![full docker inspect output](images/03-docker-inspect-full.png)
![extracted container IP](images/04-container-ip.png)

### 5) Enter the container and view files

```bash
docker exec -it nginx-lab /bin/bash
# inside container
cat /usr/share/nginx/html/index.html
cat /etc/os-release
exit
```

Image: `images/05-inside-container.png`

![inside container, cat index.html and os-release](images/05-inside-container.png)

### 6) Modify the default webpage

```bash
docker exec -it nginx-lab /bin/bash
echo '<h1>Hello from Azeez Docker Lab 🚀</h1>' > /usr/share/nginx/html/index.html
cat /usr/share/nginx/html/index.html
exit
```

Refresh `http://localhost:8081` to confirm the change.

### 7) Stop/start and check persistence

```bash
docker stop nginx-lab
docker start nginx-lab
curl -I http://localhost:8081
docker exec -it nginx-lab cat /usr/share/nginx/html/index.html
```

If your change is lost after restart, persistent storage (volumes) is required.

### 8) List Docker images on host

```bash
docker images
```

Images: `images/06-docker-images-1.png`, `images/07-docker-images-2.png`

![docker images list](images/06-docker-images-1.png)
![docker images (continued) & example run](images/07-docker-images-2.png)

---

## How to add images and verify they will render on GitHub

1. Create the `images/` folder at the repository root:

```powershell
mkdir images
```

2. Copy your screenshots into `images/` and ensure filenames match these exactly (or edit the filenames below to match your images):

- `01-pwd-docker-ps.png`
- `02-curl-docker-inspect.png`
- `03-docker-inspect-full.png`
- `04-container-ip.png`
- `05-inside-container.png`
- `06-docker-images-1.png`
- `07-docker-images-2.png`

3. Stage and commit the images and README, then push to the branch GitHub displays (`main` by default):

```bash
git add images/*.png README.md
git commit -m "Add lab README and screenshots"
git push origin main
```

4. Visit GitHub and open your README — images should render inline. If an image is missing, check the exact filename and case, and ensure it was pushed to the same branch.

---

## Glossary

- Container: Running instance of an image.
- Image: Read-only template created from a Dockerfile.
- Bind mount: Host file/directory mounted inside container (good for development).
- Volume: Docker-managed persistent storage.

## Next steps

- Persist edits using bind mounts or volumes.
- Build a custom image with the modified content.
- Use `docker-compose` for multi-container setups.

If you'd like, I can add an `images/.gitkeep` placeholder now so the `images/` folder exists in the repo. I can also add the actual screenshots if you upload them here.

