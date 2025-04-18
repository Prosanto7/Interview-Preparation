## 🐳 What is Docker?

**Docker** is a platform that allows you to **develop**, **ship**, and **run applications** inside **containers**. A **container** is a lightweight, standalone, and executable unit that contains everything needed to run an application: code, libraries, dependencies, and environment.

---

## ⚙️ Key Concepts in Docker

| Concept           | Description |
|-------------------|-------------|
| **Image**         | A read-only template with instructions to create a container (e.g., OS + app). |
| **Container**     | A running instance of an image. |
| **Dockerfile**    | A text file with instructions to build a Docker image. |
| **Docker Hub**    | A public registry where Docker images are stored. |
| **Docker Engine** | The client-server application that runs Docker containers. |
| **Docker Compose**| Tool to define and run multi-container apps using YAML. |

---

## 📦 Docker Image Commands

| Command | Description |
|--------|-------------|
| `docker build -t <name> .` | Build a Docker image from a Dockerfile in current directory. |
| `docker images` | List all locally available images. |
| `docker rmi <image_id>` | Remove an image. |
| `docker pull <image>` | Download an image from Docker Hub. |
| `docker tag <image_id> <newname>` | Tag an image with a new name or version. |

---

## 🧱 Docker Container Commands

| Command | Description |
|--------|-------------|
| `docker run <image>` | Run a container from an image. |
| `docker run -it <image>` | Run a container in interactive terminal mode. |
| `docker run -d <image>` | Run a container in detached mode (in the background). |
| `docker run -p 8080:80 <image>` | Map host port 8080 to container port 80. |
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
