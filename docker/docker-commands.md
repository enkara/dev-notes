# Docker Commands 🐳

A curated list of useful Docker commands and references for everyday development.

---

## 📚 References

- [Official Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)
- [Docker Hub – Public Image Registry](https://hub.docker.com/)

---

## 🧠 Basic Concepts

- **Images** are like *classes* (blueprints).
- **Containers** are like *objects* (runtime instances of images).
- Images are typically downloaded (pulled) from a registry (e.g. Docker Hub).
- Containers are local and can be created, stopped, removed, and recreated at any time.

General help:
```
bash
docker --help
docker image --help
docker container --help
docker network --help
```
Example for a specific topic:
```
bash
docker network --help
```
---

## 🧱 Image Management

### List Images
```
bash
# List all images on the local machine
docker images

# Same as:
docker image ls
```
### Pull Images
```
bash
# Pull an image with a specific tag
docker pull nginx:1.23

# Pull the latest version (tag "latest" is default)
docker pull nginx
docker pull nginx:latest
```
### Remove Images
```
bash
# Remove an image by ID
docker rmi 76461223f84a

# Remove an image by name:tag
docker rmi nginx:1.23

# Force-remove an image (even if used by stopped containers)
docker rmi -f nginx:1.23
```
---

## 📦 Container Management

### List Containers
```
bash
# List running containers (processes)
docker ps

# List all containers, including stopped ones
docker ps -a

# Equivalent using the "container" subcommand
docker container ls
docker container ls -a
```
### Run Containers
```
bash
# Run a container in detached mode (-d). If the image is not present
# locally, it will be pulled first.
docker run -d nginx:1.24

# Run a container with a custom name
docker run -d --name my-nginx nginx:1.24

# Map host port 8080 to container port 80
docker run -d --name my-nginx -p 8080:80 nginx:1.24
```
Useful `docker run` options:

- `-d` / `--detach` – run in the background  
- `--name` – assign a readable container name  
- `-p HOST:CONTAINER` – publish container port to the host  
- `-e KEY=VALUE` – set environment variables  
- `--rm` – automatically remove container when it exits  

### Stop, Start & Restart
```
bash
# Stop a running container
docker stop <CONTAINER_ID_OR_NAME>

# Start a stopped container
docker start <CONTAINER_ID_OR_NAME>

# Restart a container
docker restart <CONTAINER_ID_OR_NAME>
```
### Remove Containers
```
bash
# Remove a specific container (must be stopped)
docker rm <CONTAINER_ID_OR_NAME>

# Force-remove a running container
docker rm -f <CONTAINER_ID_OR_NAME>

# Show only container IDs
docker container ls -qa

# Remove all containers (running will be force-stopped)
docker container ls -qa | xargs docker container rm -f
```
---

## 🔍 Inspecting Containers & Logs

### Inspect Container Details
``` bash
# Show detailed information about a container
docker inspect <CONTAINER_ID_OR_NAME>
```
### Logs
```
bash
# Show logs of a container (first run: docker ps)
docker logs <CONTAINER_ID_OR_NAME>

# Follow logs (like tail -f)
docker logs -f <CONTAINER_ID_OR_NAME>

# Only show the last 100 lines
docker logs --tail 100 <CONTAINER_ID_OR_NAME>
```
---

## 🐚 Working Inside Containers

### Open a Shell in a Running Container

1. Get the container ID or name:

   ```bash
   docker ps
   ```

2. Start a shell:

   ```bash
   # Interactive bash shell
   docker exec -it <CONTAINER_ID_OR_NAME> /bin/bash

   # If bash is not available
   docker exec -it <CONTAINER_ID_OR_NAME> sh
   ```

- `-i` – interactive
- `-t` – allocate a pseudo-TTY

### Run a Single Command in a Container
```
bash
# Run 'ls -la' inside the container
docker exec <CONTAINER_ID_OR_NAME> ls -la

# Run as root (if supported by the image)
docker exec -u root -it <CONTAINER_ID_OR_NAME> /bin/bash
```
---

## 🌐 Network Management

### List Networks
```
bash
# List all Docker networks
docker network ls
```
### Remove Networks
```
bash
# Remove a specific network
docker network rm <NETWORK_NAME_OR_ID>

# List only network IDs
docker network ls -q

# Remove all networks (default networks may fail to delete – that’s OK)
docker network ls -q | xargs docker network rm
```
### Prune Unused Networks
```
bash
# Remove all unused networks
docker network prune
```
### Connect Containers to Networks
```
bash
# Attach a container to a network
docker network connect <NETWORK_NAME> <CONTAINER_ID_OR_NAME>
```
---

## 💾 Volumes & Data

### List & Inspect Volumes
```
bash
# List all volumes
docker volume ls

# Inspect a specific volume
docker volume inspect <VOLUME_NAME>
```
### Create & Remove Volumes
```
bash
# Create a named volume
docker volume create my-data

# Remove a volume
docker volume rm my-data

# Remove all unused volumes
docker volume prune
```
### Use Volumes When Running Containers
```
bash
# Mount a named volume into a container
docker run -d --name my-nginx -v my-data:/usr/share/nginx/html nginx:1.24

# Bind-mount the current directory into /app inside the container
docker run -d --name my-app -v "$(pwd)":/app my-image:latest
```
---

## 🛠️ Building Images
```
bash
# Build an image from a Dockerfile in the current directory
docker build -t my-image:1.0 .

# Build with a specific Dockerfile
docker build -f Dockerfile.dev -t my-image:dev .

# Show build history of an image
docker history my-image:1.0
```
---

## 📤 Copying Files To/From Containers
```
bash
# Copy from container to host
docker cp <CONTAINER_ID_OR_NAME>:/path/in/container /path/on/host

# Copy from host to container
docker cp /path/on/host <CONTAINER_ID_OR_NAME>:/path/in/container
```
---

## 🧹 Cleanup & Pruning
```
bash
# Remove all stopped containers
docker container prune

# Remove unused (dangling) images
docker image prune

# Remove unused images, including unreferenced ones
docker image prune -a

# Remove:
# - stopped containers
# - unused networks
# - dangling images
# - build cache
docker system prune

# Aggressive cleanup: also remove unused images and volumes
docker system prune -a --volumes
```
---

## 🚀 Quick Nginx Example
```
bash
# 1. Pull Nginx image
docker pull nginx:1.24

# 2. Run Nginx in the background, exposing port 8080 on the host
docker run -d --name my-nginx -p 8080:80 nginx:1.24

# 3. Check that the container is running
docker ps

# 4. View logs
docker logs my-nginx

# 5. Open a shell inside the container
docker exec -it my-nginx /bin/bash

# 6. Stop and remove the container
docker stop my-nginx
docker rm my-nginx
```
