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
| `docker run --name <container_name> <image> | Run container with custom name |
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
