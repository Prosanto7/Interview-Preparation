## 🐳 What is Docker?

**Docker** is a platform that allows you to **develop**, **ship**, and **run applications** inside **containers**. A **container** is a lightweight, standalone, and executable unit that contains everything needed to run an application: code, libraries, dependencies, and environment.

---

## ⚙️ Key Concepts in Docker

| Concept           | Description |
|-------------------|-------------|
| **Image**         | A read-only, immutable blueprint with instructions to create a container (e.g., OS + app). |
| **Container**     | A running instance of an image. |
| **Dockerfile**    | A text file with instructions to build a Docker image. |
| **Docker Hub**    | A public registry where Docker images are stored. |
| **Docker Engine** | The client-server application that runs Docker containers. |
| **Docker Compose**| Tool to define and run multi-container apps using YAML. |

---

## 📦 Docker Image Commands

| Command | Description |
|--------|-------------|
| `docker build -t <name>:<version> . ` | Build a Docker image from a Dockerfile in current directory. (version is optional) |
| `docker images` | List all locally available images. |
| `docker rmi <image_id>` | Remove an image. |
| `docker image prune` | Remove unused images. |
| `docker pull <image>` | Download an image from Docker Hub. |
| `docker tag <image_id> <newname>` | Tag an image with a new name or version. |

---

## 🧱 Docker Container Commands

| Command | Description |
|--------|-------------|
| `docker run <image>` | Run a container from an image. |
| `docker run -it <image>` | Run a container in interactive terminal mode. |
| `docker run -d <image>` | Run a container in detached mode (in the background). |
| `docker run -p <host_port>:<container_port> <image>` | Map host port 8080 to container port 80. |
| `docker run -e <var_name>=<var_value> <container>` | Set environment variables in a container |
| `docker run --name <container_name> <image>` | Run container with custom name |
| `docker ps` | List running containers. |
| `docker ps -a` | List all containers (including stopped). |
| `docker stop <container_id>` | Stop a running container. |
| `docker start <container_id>` | Start a stopped container. |
| `docker restart <container_id>` | Restart a container. |
| `docker rm <container_id>` | Remove a stopped container. |
| `docker exec -it <container_id> bash` | Open a bash shell inside a running container. |

---

## 🗃️ Docker Volume Commands

| Command | Description |
|--------|-------------|
| `docker volume create <volume_name>` | Create a named volume. |
| `docker volume ls` | List all volumes. |
| `docker volume rm <volume_name>` | Remove a volume. |
| `docker run -v <volume_name>:/data <image>` | Mount a volume to a container. |

---

## 🛠️ Docker Network Commands

| Command | Description |
|--------|-------------|
| `docker network ls` | List all Docker networks. |
| `docker network create <name>` | Create a new network. |
| `docker network rm <network_name>` | Remove a network |
| `docker network inspect <name>` | View details of a network. |
| `docker run --network <name> <image>` | Attach a container to a network. |

---

## 📁 Dockerfile Essentials

Example `Dockerfile`:
```Dockerfile
FROM node:16
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
```

---

## 🧩 Docker Compose

Define multiple containers in a `docker-compose.yml` file:
```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "3000:3000"
  db:
    image: mongo
    ports:
      - "27017:27017"
```

### Commands:
| Command | Description |
|--------|-------------|
| `docker-compose up` | Start all services. |
| `docker-compose up --build` | Build images and start services. |
| `docker-compose down` | Stop and remove containers, networks, etc. |

---

## 🔍 Useful Docker Commands

| Command | Description |
|--------|-------------|
| `docker logs <container_id>` | View logs of a container. |
| `docker inspect <container_id>` | Get low-level info on containers. |
| `docker stats` | Show live resource usage of running containers. |
| `docker system prune` | Remove all unused containers, networks, images, and build cache. |

---

## 🧼 Cleanup Commands

| Command | Description |
|--------|-------------|
| `docker container prune` | Remove all stopped containers. |
| `docker image prune` | Remove unused images. |
| `docker volume prune` | Remove unused volumes. |
| `docker network prune` | Remove unused networks. |

---

## 🤖 Docker Best Practices

- Use `.dockerignore` like `.gitignore`
- Keep images small (multi-stage builds)
- Use official base images
- Use health checks in `docker-compose`
- Store secrets securely (not in Dockerfile)

---


## 🆚 Docker vs Virtual Machine (VM)

<img width="700" src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*KtazvJZ-IX6aoq3jCjD5tA.png">

| Feature                      | Docker (Containers)                            | Virtual Machines (VMs)                          |
|-----------------------------|------------------------------------------------|-------------------------------------------------|
| **Architecture**            | Shares host OS kernel and virtualizes the application layer. Shares resources with the underlying host kernel.                       | Virtualizes both kernel and application layer. Runs its own kernel and operating system. Emulates the hardware components of a physical machine, such as the CPU, memory, network interface card, USB controllers, and sound cards. You can run a guest operating system and multiple applications in the virtual environment.           |
| **Startup Time**            | Very fast (seconds)                            | Slow (minutes)                                 |
| **Resource Usage**          | Lightweight (less CPU/RAM overhead)            | Heavy (requires full OS resources)             |
| **Isolation**               | Process-level isolation                        | Full machine-level isolation                   |
| **Portability**             | Highly portable (runs anywhere Docker runs)    | Less portable (depends on hypervisor, OS)      |
| **Performance**             | Near-native (no hypervisor overhead)           | Slightly slower (due to full OS virtualization)|
| **Size**                    | MBs (small image sizes)                        | GBs (entire OS image)                          |
| **Security**                | Less isolated than VMs                         | Strong isolation due to separate OS            |
| **Use Case**                | Microservices, CI/CD, Dev & Testing            | Running multiple OSs, Legacy Apps              |
| **Networking**              | Shared or custom Docker networks               | Full virtualized networking                    |

---

## ⚙️ Visual Architecture Comparison

### 🔹 Docker:
```
Host OS
 └── Docker Engine
      ├── Container: App + Libs
      ├── Container: App + Libs
```

### 🔸 VM:
```
Host OS
 └── Hypervisor
      ├── Guest OS + App
      ├── Guest OS + App
```

---

## 📌 When to Use Docker

✅ Choose **Docker** when:
- You want **fast deployments** and startup.
- You’re developing **microservices** or **REST APIs**.
- You need **lightweight**, reproducible environments.
- You need to **scale** easily in orchestration platforms like Kubernetes.
- You want to **develop once and deploy anywhere**.

---

## 📌 When to Use Virtual Machines

✅ Choose **VMs** when:
- You need to run **multiple OS environments** (e.g., Linux + Windows).
- You require **stronger security and isolation** between apps.
- You need to **emulate hardware** or use apps that require a **full OS**.
- You're hosting **monolithic legacy applications** that can’t be containerized.
- Your company already has **VM infrastructure (e.g., VMware, Hyper-V)**.

---

## 🧠 Real-World Use Cases

| Scenario                              | Recommended |
|---------------------------------------|-------------|
| Local dev/test environment            | Docker      |
| CI/CD pipelines                       | Docker      |
| Microservices architecture            | Docker      |
| Running Windows app on Linux          | VM          |
| Hosting multiple OSs on one machine   | VM          |
| Legacy software requiring full OS     | VM          |
| Isolated and secure workloads         | VM          |
| Lightweight testing or scripting      | Docker      |

---

## 🔄 Can They Be Used Together?

Yes! A **Docker container** can run **inside a VM**. This is common in cloud environments (e.g., AWS EC2 + Docker), where the VM gives a secure, isolated environment, and Docker adds flexible app packaging.

---

## 🏁 Conclusion

| Want...                              | Use         |
|--------------------------------------|-------------|
| Speed, portability, scalability      | Docker      |
| Isolation, full OS features          | VM          |

If you're building a modern web app, REST API, or microservices system, **Docker is usually the better choice**. For running older apps, complex environments, or mixed-OS stacks, **VMs are more appropriate**.

---

## 📚 References

- Docker Documentation: https://docs.docker.com/
- Dockerfile reference: https://docs.docker.com/reference/dockerfile/
- Docker Compose: https://docs.docker.com/compose/
- Best practices for writing Dockerfiles: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

---

## 🎯 Docker Interview Questions — Critical Scenarios

---

### 🏗️ Images & Builds

**Q1. What is a multi-stage build and why should you use it in production?**

**Answer:**
Multi-stage builds use multiple `FROM` statements in one Dockerfile, allowing you to build in one stage and copy only artifacts to a lean final image.

```dockerfile
# ❌ Single-stage — final image includes compiler, dev deps, source
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install    # Includes devDependencies
COPY . .
RUN npm run build
CMD ["node", "dist/server.js"]
# Image size: ~900MB

# ✅ Multi-stage — final image only contains runtime artifacts
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine AS runtime
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
# Only production dependencies copied
CMD ["node", "dist/server.js"]
# Image size: ~150MB
```

**Benefits:**
- Smaller image = faster pulls, less attack surface, lower storage costs
- Build tools (compilers, test frameworks) don't end up in production image
- Separate caching per stage — source changes don't invalidate dependency layer

**PHP example:**
```dockerfile
FROM composer:2 AS vendor
COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader

FROM php:8.3-fpm-alpine AS production
COPY --from=vendor /app/vendor /var/www/vendor
COPY . /var/www
```

---

**Q2. How do Docker layer caching work and how do you optimize Dockerfiles for it?**

**Answer:**
Each instruction in a Dockerfile creates an immutable layer. Docker caches layers and only rebuilds from the point where something changed.

```dockerfile
# ❌ Bad — COPY . . invalidates cache before npm install
FROM node:18-alpine
WORKDIR /app
COPY . .               # Changes every time any file changes
RUN npm install        # Reinstalls every time — slow!
CMD ["node", "index.js"]

# ✅ Good — copy package files first (rarely change)
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./  # Only reinstalls when deps change
RUN npm install        # Cached unless package.json changed
COPY . .               # Application code changes don't affect install
CMD ["node", "index.js"]
```

**Key rules:**
1. Put instructions that change least at the top
2. Copy dependency manifests before copying source
3. `RUN apt-get update && apt-get install` in one layer (avoids stale cache)
4. Use `--no-cache` in CI to ensure clean builds

---

### 🔐 Security

**Q3. What are the most important Docker security best practices?**

**Answer:**

| Practice | Why | How |
|---------|-----|-----|
| **Don't run as root** | Root in container = near-root on host | `USER appuser` in Dockerfile |
| **Use minimal base images** | Fewer packages = smaller attack surface | Alpine, distroless, scratch |
| **Don't embed secrets** | Image layers are inspectable | Use env vars, Docker secrets, Vault |
| **Scan images** | Known CVEs in base images | `docker scout`, Trivy, Snyk |
| **Read-only filesystem** | Prevents runtime modification | `--read-only` flag |
| **Limit capabilities** | Drop unnecessary Linux capabilities | `--cap-drop ALL --cap-add NET_BIND_SERVICE` |
| **Don't use `latest` tag** | Unpredictable builds | Pin to specific versions `node:18.19.0-alpine3.19` |

```dockerfile
# Security-hardened Dockerfile
FROM php:8.3-fpm-alpine

# Create non-root user
RUN addgroup -g 1001 appgroup && adduser -u 1001 -G appgroup -s /bin/sh -D appuser

WORKDIR /var/www

# Install deps as root (needed)
RUN apk add --no-cache libpng-dev && docker-php-ext-install pdo_mysql

COPY --chown=appuser:appgroup . .

# Drop to non-root
USER appuser

EXPOSE 9000
CMD ["php-fpm"]
```

**Secrets — never do this:**
```dockerfile
# ❌ Secret baked into image layer (visible with docker history)
ENV DB_PASSWORD=secret123
RUN curl -H "Auth: $DB_PASSWORD" https://api.example.com
```

**Use runtime secrets instead:**
```bash
docker run -e DB_PASSWORD=$(cat /run/secrets/db_pass) myapp
# Or Docker Swarm secrets
docker secret create db_password ./password.txt
```

---

**Q4. What is the difference between Docker Volumes, Bind Mounts, and tmpfs?**

**Answer:**

| Type | Storage Location | Use Case | Managed by |
|------|-----------------|---------|-----------|
| **Volume** | Docker-managed directory (`/var/lib/docker/volumes/`) | DB data, persistent app data | Docker |
| **Bind Mount** | Any host path | Development (hot reload), config files | Host OS |
| **tmpfs** | Host RAM (not persisted) | Secrets, temp sensitive data | Docker + RAM |

```bash
# Named Volume — Docker manages location, survives container deletion
docker run -v myapp-data:/var/www/storage myapp

# Bind Mount — direct host path (development)
docker run -v $(pwd)/src:/var/www/src myapp
# Code changes immediately reflected in container

# tmpfs — in-memory, never written to disk
docker run --tmpfs /run/secrets:rw,noexec,nosuid,size=64m myapp
```

**Best practices:**
- Use **volumes** for production data persistence
- Use **bind mounts** for local development only
- Use **tmpfs** for sensitive data that must not touch disk (session tokens, temp credentials)
- Never use bind mounts in production (host path dependency)

---

### ⚡ Networking & Performance

**Q5. Explain Docker networking modes and when to use each.**

**Answer:**

| Network Mode | Description | Use Case |
|-------------|-------------|---------|
| **bridge** (default) | Private network, containers communicate via container name | Multi-container apps on single host |
| **host** | Container uses host's network stack directly | Maximum performance, no NAT overhead |
| **none** | No network access | Security-isolated batch jobs |
| **overlay** | Multi-host networking (Docker Swarm/Kubernetes) | Distributed services across hosts |
| **macvlan** | Container gets its own MAC/IP on physical network | Legacy apps needing direct network access |

```yaml
# docker-compose.yml — custom bridge network
services:
  app:
    networks:
      - backend
      - frontend
  db:
    networks:
      - backend    # Only app can reach DB, not public internet

networks:
  frontend:
  backend:
    internal: true  # No external access
```

**Container DNS:** On custom bridge networks, containers communicate by service name (`db`, `redis`) — Docker's embedded DNS resolves them.

---

**Q6. Docker vs Docker Compose vs Docker Swarm vs Kubernetes — when to use each?**

**Answer:**

| Tool | Use Case | Scale | Complexity |
|------|---------|-------|-----------|
| **Docker** | Single container, local dev | 1 container | Low |
| **Docker Compose** | Multi-container local dev, simple production | Multiple containers, 1 host | Low |
| **Docker Swarm** | Simple container orchestration, multi-host | Multiple hosts | Medium |
| **Kubernetes** | Complex orchestration, auto-scaling, self-healing | Enterprise, 10+ services | High |

**Decision guide:**
- **Local development:** Docker Compose
- **Small team, simple deployment (2-3 services):** Docker Compose on a single server
- **Medium complexity, prefer simplicity:** Docker Swarm
- **Large scale, complex requirements, cloud-native:** Kubernetes

**Kubernetes advantages over Swarm:**
- Auto-scaling (HPA, VPA, Cluster Autoscaler)
- Rich ecosystem (Helm, Prometheus, Istio)
- More granular networking policies
- Better support by all major clouds (EKS, GKE, AKS)

---

**Q7. What are the key differences between `CMD` and `ENTRYPOINT` in Dockerfile?**

**Answer:**

| Feature | CMD | ENTRYPOINT |
|---------|-----|-----------|
| **Purpose** | Default command (can be overridden) | Fixed executable (harder to override) |
| **Override** | `docker run myimage custom-command` replaces CMD | `docker run --entrypoint newcmd myimage` |
| **Combined** | ENTRYPOINT sets binary, CMD provides default args | — |

```dockerfile
# CMD — easily overridden
CMD ["php", "artisan", "serve"]
# docker run myapp php artisan migrate  → runs migrate, not serve

# ENTRYPOINT — fixed binary
ENTRYPOINT ["php"]
CMD ["artisan", "serve"]
# docker run myapp artisan migrate  → runs php artisan migrate
# docker run myapp -v  → runs php -v

# Best practice for production: ENTRYPOINT + CMD
ENTRYPOINT ["/docker-entrypoint.sh"]  # Setup script
CMD ["php-fpm"]                        # Default process
```

**Shell vs Exec form:**
```dockerfile
# Shell form — runs as /bin/sh -c (PID is NOT 1, signals not forwarded)
CMD php artisan serve

# Exec form — process IS PID 1, receives signals properly
CMD ["php", "artisan", "serve"]
```

Always use exec form (`["executable", "arg1"]`) in production — it ensures your process receives SIGTERM for graceful shutdown.
