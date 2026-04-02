# Day 1: What is Docker? Architecture & Installation

## 📋 Learning Objectives

By the end of Day 1, you will understand:

- What is Docker and why it's important for DevOps
- Docker architecture and core components
- Virtual Machines vs Containers
- How to install Docker on different operating systems
- Running your first container
- Understanding Docker daemon and client

## 🎯 Key Concepts

### What is Docker?

Docker is an open-source containerization platform that packages your application and its dependencies into a standardized unit called a **container**. These containers can run consistently across different environments.

**Key Problem It Solves:**
- "It works on my machine!" problem
- Dependency hell
- Environment inconsistency
- Complex deployment processes

### Docker vs Virtual Machines

| Aspect | Virtual Machine | Docker Container |
|--------|-----------------|------------------|
| **Size** | Gigabytes (includes OS) | Megabytes to few GB |
| **Boot Time** | Minutes | Seconds |
| **Resource Usage** | High (dedicated resources) | Low (shared OS kernel) |
| **Isolation** | Full OS isolation | Process-level isolation |
| **Portability** | Good | Excellent |
| **Performance** | Lower | Near-native |

### Docker Architecture

```
┌─────────────────────────────────────────────┐
│         Docker Client (CLI/API)            │
│        (docker run, docker build, etc)     │
└────────────────┬────────────────────────────┘
                 │
              Socket/REST API
                 │
┌────────────────▼────────────────────────────┐
│         Docker Daemon (dockerd)             │
│  - Manages containers                       │
│  - Manages images                           │
│  - Manages networks                         │
│  - Manages volumes                          │
└────────────────┬────────────────────────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
┌───▼────┐  ┌───▼────┐  ┌───▼────┐
│Container│ │ Container│ │Container│
│Manager  │ │ Manager  │ │ Manager │
│(containerd)          │
└────────┘  └────────┘  └────────┘
```

### Core Docker Components

1. **Docker Image**
   - Read-only template for creating containers
   - Contains application code, runtime, dependencies
   - Built from Dockerfile or pulled from registry

2. **Docker Container**
   - Running instance of a Docker image
   - Isolated from other containers
   - Can be started, stopped, paused, deleted

3. **Docker Registry**
   - Centralized repository for Docker images
   - Default: Docker Hub
   - Can be private (ECR, GCR, self-hosted)

4. **Docker Daemon**
   - Runs on the host machine
   - Manages containers, images, networks, storage
   - Listens for Docker API requests

---

## 🔧 Installation Guide

### Prerequisites

- A computer (Windows, macOS, or Linux)
- At least 4GB RAM
- Administrator/sudo access
- Internet connection

### Windows 10/11

**Requirements:**
- Windows 10 Professional/Enterprise or Windows 11
- Hyper-V enabled
- WSL 2 (Windows Subsystem for Linux 2)

**Steps:**

1. **Download Docker Desktop**
   ```
   Visit: https://www.docker.com/products/docker-desktop
   Download Docker Desktop for Windows
   ```

2. **Install**
   - Run the installer
   - Follow the installation wizard
   - Restart your computer

3. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

### macOS

**Requirements:**
- macOS 11 (Big Sur) or newer
- Apple Silicon (M1/M2) or Intel processor
- At least 4GB RAM

**Steps:**

1. **Using Homebrew (Recommended)**
   ```bash
   brew install docker
   brew install docker-desktop
   ```

2. **Download from Website**
   ```
   Visit: https://www.docker.com/products/docker-desktop
   Download Docker Desktop for Mac
   ```

3. **Install and Verify**
   ```bash
   docker --version
   docker run hello-world
   ```

### Linux (Ubuntu/Debian)

**Steps:**

1. **Install Dependencies**
   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl gnupg lsb-release
   ```

2. **Add Docker Repository**
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   
   echo \
     "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

3. **Install Docker**
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

4. **Add User to Docker Group**
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

5. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

---

## 📝 Practical Tasks

### Task 1: Verify Docker Installation
```bash
# Check Docker version
docker --version

# Check Docker info
docker info

# Check Docker CLI help
docker --help
```

### Task 2: Run Your First Container
```bash
# Run a simple container
docker run hello-world

# Explanation:
# docker run     - Command to create and run a new container
# hello-world    - Image name (Docker will pull from Docker Hub if not local)
```

### Task 3: Interactive Container
```bash
# Run Ubuntu container interactively
docker run -it ubuntu /bin/bash

# Inside the container:
apt-get update
apt-get install curl -y
curl https://www.google.com
exit
```

### Task 4: View Running Containers
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Get detailed information
docker inspect <container-id>
```

### Task 5: Container Lifecycle
```bash
# Run a container in background (detached mode)
docker run -d --name my-container alpine sleep 100

# Check if it's running
docker ps

# Pause the container
docker pause my-container

# Check status
docker ps

# Unpause the container
docker unpause my-container

# Stop the container
docker stop my-container

# Start it again
docker start my-container

# Remove the container
docker rm my-container
```

### Task 6: Container Output
```bash
# Run a container and capture logs
docker run --name logger alpine echo "Hello from Docker!"

# View logs
docker logs logger

# Follow logs in real-time
docker logs -f logger

# Clean up
docker rm logger
```

---

## 💼 Real-World Scenario

### Scenario: Developer Environment Setup

**Problem:**
You have a team developing a web application. Each developer has:
- Different operating systems (Windows, macOS, Linux)
- Different Node.js versions installed
- Database setup issues
- "Works on my machine" problems

**Solution with Docker:**

1. **Create a Dockerfile** (we'll learn in Day 3)
   ```dockerfile
   FROM node:16-alpine
   WORKDIR /app
   COPY . .
   RUN npm install
   EXPOSE 3000
   CMD ["npm", "start"]
   ```

2. **Benefits:**
   - Consistent environment across all developers
   - No need to install Node.js locally
   - Easy onboarding for new team members
   - Same environment for development, testing, and production

3. **Usage:**
   ```bash
   # Developer A (Windows)
   docker run -p 3000:3000 myapp
   
   # Developer B (macOS)
   docker run -p 3000:3000 myapp
   
   # Developer C (Linux)
   docker run -p 3000:3000 myapp
   
   # All have the exact same environment!
   ```

---

## ✅ Checkpoint

Before moving to Day 2, verify you can:

- [ ] Docker is installed and running on your system
- [ ] `docker --version` shows version info
- [ ] `docker run hello-world` completes successfully
- [ ] You can list containers with `docker ps -a`
- [ ] You understand Docker architecture basics
- [ ] You can run an interactive container
- [ ] You can view container logs

---

## 📚 Resources

### Official Documentation
- [Docker Official Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Daemon Documentation](https://docs.docker.com/config/daemon/)
- [Docker Overview](https://docs.docker.com/get-started/)

### Articles & Tutorials
- [Docker Architecture Explained](https://docs.docker.com/get-started/overview/)
- [Containers vs VMs](https://www.docker.com/blog/containers-replacing-virtual-machines/)

### Video Resources
- [Docker in 100 Seconds](https://www.youtube.com/watch?v=Gjnup-PuquQ)
- [Docker Fundamentals Course](https://www.youtube.com/playlist?list=PLBmvwwWQmNG0CqNhtycd-cHRIUQL9x34V)

### Interactive Learning
- [Docker Interactive Tutorial](https://docs.docker.com/get-started/hands-on-overview/)
- [Play with Docker](https://labs.play-with-docker.com/)

---

## 🎓 Next Steps

✅ Complete all practical tasks above  
✅ Run the hello-world container multiple times  
✅ Try running different images (ubuntu, alpine, nginx)  
✅ Review Docker architecture concepts  

**Tomorrow on Day 2:** We'll explore images and containers in depth, learning how to find, pull, and manage Docker images!

---

<div align="center">
  
**← Back to [Main README](../README.md)** | **[Day 2 →](../Day02/README.md)**

</div>
