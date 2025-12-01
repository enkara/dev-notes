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
- Containers are local and can be created, stopped, removed and recreated at any time.

For help on any command:
```
bash
docker --help
docker image --help
docker container --help
docker network --help
```
Example for a specific command:
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

# Same as: docker image ls
docker image ls
```
### Pull Images
```
bash
# Pull image with specific tag
docker pull nginx:1.23

# Pull the latest version (tag "latest" is the default)
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

# Equivalent commands using the "container" subcommand
docker container ls
docker container ls -a
```
### Run Containers
```
bash
# Run a container in detached mode (-d) from an image
# If the image is not present locally, it will be pulled first
docker run -d nginx:1.24

# Run a container with a custom name
docker run -d --name my-nginx nginx:1.24

# Map host port 8080 to container port 80
docker run -d --name my-nginx -p 8080:80 nginx:1.24
```
Useful options:

- `-d` / `--detach` – run in background
- `--name` – assign a readable container name
- `-p HOST:CONTAINER` – publish container ports to the host
- `-e KEY=VALUE` – set environment variables
- `--rm` – automatically remove container when it exits

### Stop & Start Containers
``` bash
# Stop a running container
docker stop <CONTAINER_ID_OR_NAME>

# Start a stopped container
docker start <CONTAINER_ID_OR_NAME>

# Restart a container
docker restart <CONTAINER_ID_OR_NAME>
```
### Remove Containers
``` bash
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

## 🧹 Removing Containers and Images Safely

### 1. Stop and Remove a Single Container
``` bash
# Stop container (replace <container_id> with the real ID or name)
docker stop <container_id>

# Remove container
docker rm <container_id>
```
Example:
``` bash
docker stop 76461223f84a
docker rm   76461223f84a
```
### 2. Remove the Corresponding Image

After the container is removed, you can delete the image:
``` bash
# Remove image by ID
docker rmi <image_id>
```
If container and image share the same ID (typical in your examples):
``` bash
docker rmi 76461223f84a
```
If the image is still used by **other containers**, Docker will refuse to delete it.  
In that case, stop/remove those containers first or use `docker rmi -f` (with care).

---

## 🧹 Cleaning Up Unused Images and Resources

### Remove Unused Images Only
``` bash
# Remove dangling images (untagged, <none>)
docker image prune

# Remove all unused images (not referenced by any container)
docker image prune -a
```
### Remove Unused Containers, Networks, Images, Build Cache
``` bash
# Dry clean: remove stopped containers, unused networks, dangling images, build cache
docker system prune
```
### Aggressive Cleanup
``` bash
# More aggressive: also remove all unused images and volumes
docker system prune -a --volumes
```
Use the aggressive version only if you are sure you no longer need old containers/images/volumes.

---

## 🔍 Finding Image Repositories and Tags

### List Local Images with Repository and Tag
``` bash
# Show images with ID, repository, tag
docker images --format "{{.ID}}: {{.Repository}}:{{.Tag}}"
```
Output example:
``` text
76461223f84a: nginx:1.24
a1b2c3d4e5f6: myregistry.local/myteam/myapp:2.0.1
```
- **Repository** column usually contains:
  - the registry host (optional, e.g. `myregistry.local`), and
  - the repository path (`nginx`, `myteam/myapp`, …).
- **Tag** shows the version or tag (e.g. `1.24`, `latest`).

### Inspect an Image
``` bash
# Show detailed information about an image
docker inspect <image_id_or_repo:tag>
```
Useful fields:

- `RepoTags` – list of `repository:tag` entries the image currently has.
- `Config.Image` / `Config.Labels` – sometimes contain additional metadata.

**Important:** Docker does **not** store “where” (which registry URL) an image originally came from in a reliable way.  
You usually infer it from the **repository name**:

- `nginx:1.24` → from Docker Hub (official library)
- `myregistry.local/myteam/myapp:2.0.1` → from your private registry `myregistry.local`

If an image was **built locally** from a `Dockerfile`, it may not have any external registry at all.

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
``` bash
# Run 'ls -la' inside the container
docker exec <CONTAINER_ID_OR_NAME> ls -la

# Run as root (if supported by the image)
docker exec -u root -it <CONTAINER_ID_OR_NAME> /bin/bash
```
---

## 🌐 Network Management

### List Networks
``` bash
# List all Docker networks
docker network ls
```
### Remove Networks
``` bash
# Remove a specific network
docker network rm <NETWORK_NAME_OR_ID>

# List only network IDs
docker network ls -q

# Remove all networks (default networks may fail to delete – that’s OK)
docker network ls -q | xargs docker network rm
```
### Prune Unused Networks
``` bash
# Remove all unused networks
docker network prune
```
### Connect Containers to Networks
``` bash
# Attach a container to a network
docker network connect <NETWORK_NAME> <CONTAINER_ID_OR_NAME>
```
---

## 💾 Volumes & Data

### List & Inspect Volumes
``` bash
# List all volumes
docker volume ls

# Inspect a specific volume
docker volume inspect <VOLUME_NAME>
```
### Create & Remove Volumes
``` bash
# Create a named volume
docker volume create my-data

# Remove a volume
docker volume rm my-data

# Remove all unused volumes
docker volume prune
```
### Use Volumes When Running Containers
``` bash
# Mount a named volume into a container
docker run -d --name my-nginx -v my-data:/usr/share/nginx/html nginx:1.24

# Bind-mount the current directory into /app inside the container
docker run -d --name my-app -v "$(pwd)":/app my-image:latest
```
---

## 🛠️ Building Images
``` bash
# Build an image from a Dockerfile in the current directory
docker build -t my-image:1.0 .

# Build with a specific Dockerfile
docker build -f Dockerfile.dev -t my-image:dev .

# Show build history of an image
docker history my-image:1.0
```
---

## 📤 Copying Files To/From Containers
``` bash
# Copy from container to host
docker cp <CONTAINER_ID_OR_NAME>:/path/in/container /path/on/host

# Copy from host to container
docker cp /path/on/host <CONTAINER_ID_OR_NAME>:/path/in/container
```
---

## 🧹 Cleanup & Pruning
``` bash
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
``` bash
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
