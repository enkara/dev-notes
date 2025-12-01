# Docker Compose Commands 🐳

A curated list of useful Docker Compose commands and workflows for everyday development.

> For general Docker CLI commands (images, containers, networks, volumes, build…), see  
> [`docker-commands.md`](./docker-commands.md).

---

## 📚 References

- [Docker Compose CLI reference](https://docs.docker.com/engine/reference/commandline/compose/)
- [Compose file reference (v3+)](https://docs.docker.com/compose/compose-file/)
- [Docker Desktop & Compose](https://docs.docker.com/desktop/)

---

## 🧠 Basic Concepts

- **docker** CLI manages:
  - containers, images, networks, volumes, etc.
- **docker compose** manages:
  - **multi-container applications** defined in `docker-compose.yml` (and friends).

Common defaults:

- Default file: `docker-compose.yml` in the current directory.
- Project name: derived from the directory name (affects container, network and volume names).
- Names typically look like:  
  `<project>_<service>_1` (containers),  
  `<project>_<volume>` (volumes),  
  `<project>_<network>` (networks).

---

## 🚀 Basic Compose Commands

### Start Services
``` bash
# Start all services (foreground with logs)
docker compose up

# Start in detached mode (recommended for dev)
docker compose up -d
```
### Stop & Remove
``` bash
# Stop and remove containers, default network, and anonymous volumes
docker compose down
```
Common options:

- `--volumes` – also remove **named** volumes declared in the file
- `--rmi local` – remove images built by `docker compose build`
``` bash
# More aggressive: also drop named volumes
docker compose down --volumes
```
### Status & Processes
``` bash
# Show status of all services
docker compose ps

# Show processes per service
docker compose top
```
### Logs
``` bash
# Logs of all services
docker compose logs

# Follow logs of all services
docker compose logs -f

# Logs of a specific service
docker compose logs <service>

# Tail + follow a specific service
docker compose logs --tail 100 -f <service>
```
### Restart Services
``` bash
# Restart all services
docker compose restart

# Restart a single service
docker compose restart <service>
```
---

## 🧱 Build, Pull & Rebuild

### Build Images
``` bash
# Build images for all services
docker compose build

# Build only one service
docker compose build <service>

# Build without using cache
docker compose build --no-cache
```
### Pull Images
``` bash
# Pull all images referenced in the compose file
docker compose pull

# Pull only one service
docker compose pull <service>
```
### Rebuild and (Re)Start a Service
``` bash
# Rebuild and restart a single service
docker compose up -d --build <service>

# Rebuild everything, recreate containers
docker compose up -d --build
```
---

## 📁 Compose Files & Overrides

By default, Compose uses `docker-compose.yml`.  
You can combine multiple files to create **overrides**, e.g. for dev/prod.

### Multiple Compose Files
``` bash
# Base + dev overrides
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d

# Base + prod overrides
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```
Rules:

- Later files **override** and **extend** earlier ones.
- Useful for:
  - different environment variables
  - different volumes (e.g. bind mounts for dev)
  - different resource limits (prod vs. dev)

### Validate & Inspect Final Config
``` bash
# Validate and print the fully merged configuration
docker compose config

# Only validate (non-zero exit code if invalid)
docker compose config --quiet
```
---

## 🌐 Networking & Ports in Compose

### Port Mapping

In `docker-compose.yml`:
``` yaml
services:
web:
image: nginx:1.24
ports:
- "8080:80"  # HOST:CONTAINER
```
Notes:

- If the port is **already in use** on the host, `docker compose up` will fail.
- You can change the host port (e.g. `8081:80`) without touching the container’s internal port.

---

## 💾 Volumes & Data in Compose

Typical pattern for databases and stateful services:
``` yaml
services:
db:
image: postgres:17
volumes:
- my-project-db-data:/var/lib/postgresql/data

volumes:
my-project-db-data:
```
Resulting volume name (by default):

- `<project>_my-project-db-data`, e.g. `myapp_my-project-db-data`

### Inspect Project Volumes
``` bash
# List all volumes
docker volume ls

# Inspect a specific volume
docker volume inspect <volume-name>
```
### Remove a Project Volume (Destructive!)
``` bash
# Stop stack
docker compose down

# Remove the named volume
docker volume rm <project>_my-project-db-data

# Start stack again (fresh data directory)
docker compose up -d
```
---

## 🔧 Typical Development Workflows

### 1️⃣ Full Stack Recreate (e.g. after major config change)
``` bash
docker compose down          # stop everything
docker compose pull          # optional: get latest images
docker compose up -d --build # rebuild & recreate
docker compose ps            # verify status
``` 
### 2️⃣ Update & Restart One Service
``` bash
# Change code / Dockerfile for one service, then:
docker compose up -d --build <service>
docker compose logs -f <service>
```
### 3️⃣ Restart After Config Change (no rebuild)
``` bash
docker compose restart <service>
```
---

## 🩺 Troubleshooting with Compose

### 🔁 Containers in a Restart Loop

**Symptom:**
``` bash
docker compose ps
# STATUS: Restarting (1) ...
```
**Steps:**

1. Check logs:

   ```bash
   docker compose logs --tail 100 -f <service>
   ```

2. Typical causes:
    - Wrong environment variables (missing passwords, wrong URLs, ports).
    - Incompatible or corrupted volume data.
    - Dependent services not available (DB, message broker, etc.).
    - App crashes due to misconfiguration.

3. Try:
    - Fix config / env.
    - If data is not important (dev only), drop the volume and restart.

---

### 🔒 Port Already in Use

**Errors:**

- `port is already allocated`
- `bind: address already in use`

**Steps:**

1. Check which process uses the port (example: port 5432):

   ```bash
   # Linux / WSL
   sudo lsof -i :5432

   # Windows PowerShell
   netstat -ano | findstr :5432
   ```

2. Either:
    - Stop the conflicting process, or
    - Change the host port in `docker-compose.yml`, e.g.:

      ```yaml
      services:
        db:
          ports:
            - "5433:5432"  # HOST:CONTAINER
      ```

---

### 📂 Volume & Permission Issues

Typical errors:

- `No space left on device`
- `could not create directory ...`
- `permission denied`

**Checks:**
```
bash
# Inspect the volume
docker volume inspect <volume-name>

# Check disk space on host
df -h
```
On Windows / WSL / Docker Desktop:

- Prefer **named volumes** instead of complex host bind mounts for databases.
- Avoid deep Windows paths with strange characters.
- If this is only dev data: remove the volume and start fresh.

---

## 🐘 PostgreSQL / TimescaleDB with Compose

### 1️⃣ Incompatible Data Directory (Major Version Mismatch)

**Typical log output:**
```
text
FATAL:  database files are incompatible with server
DETAIL: The data directory was initialized by PostgreSQL version 16,
which is not compatible with this version 17.x.
```
**Cause:**  
The Docker volume was initialized with a different PostgreSQL **major** version  
(e.g. old container used `postgres:16`, new one uses `postgres:17`).

#### Option A – Data Not Important (Dev / Test): Drop Volume

1. Stop stack:

   ```bash
   docker compose down
   ```

2. Find the volume name:

   ```bash
   docker volume ls
   # e.g. myapp_postgres-data
   ```

3. Remove the volume:

   ```bash
   docker volume rm myapp_postgres-data
   ```

4. Start stack again:

   ```bash
   docker compose up -d
   docker compose logs -f db
   ```

PostgreSQL will initialize a **new** empty data directory with the current version.

#### Option B – Data Important: Use Compatible Image

In `docker-compose.yml` use a compatible image, e.g.:
```
yaml
services:
  db:
    image: timescale/timescaledb:2.23.1-pg16
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```
Then:
``` bash
docker compose down
docker compose pull
docker compose up -d
```
#### Option C – Proper Upgrade with Migration

For production-like data:

1. Start container with the **old** major version (e.g. `pg16`) using the existing volume.
2. Create backups (`pg_dumpall` / `pg_dump`).
3. Create a **new volume** and start a container with the **new** version (e.g. `pg17`).
4. Restore backups into the new database.

> Never just change the Postgres image tag on an existing data volume across major versions without a migration plan.

---

### 2️⃣ Password / User Issues

Typical messages:

- `Error: POSTGRES_PASSWORD is not set`
- `FATAL:  password authentication failed for user "..."`

**Check in `docker-compose.yml`:**
``` yaml
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: "MySecureDevPassword!"
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
```
Notes:

- Remember: changing `POSTGRES_PASSWORD` **does not** change the password on an existing initialized data directory – it only applies on first initialization.
- If you need to reset everything in dev: drop the volume and recreate (see Option A above).

---

## 🧹 Cleaning Up Compose Projects

### Remove Containers but Keep Volumes
``` bash
docker compose down
```
### Remove Containers **and** Named Volumes (Destructive)
``` bash
docker compose down --volumes
```
### Global Cleanup (All Projects)

For global Docker cleanup, use the regular Docker commands:
``` bash
docker system prune
docker system prune -a --volumes
```
> Be careful: this affects **all** projects, not just the current compose stack.

---

## 📌 Quick Reference – Common Compose Commands
``` bash
# Start / Stop
docker compose up -d
docker compose down
docker compose restart <service>

# Status & Logs
docker compose ps
docker compose logs -f
docker compose logs -f <service>

# Build & Rebuild
docker compose build
docker compose build <service>
docker compose up -d --build <service>

# Validate config
docker compose config
```

