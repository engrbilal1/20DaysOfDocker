# Day 2: Docker Images

## Learning Objectives

By the end of this day, you will:
- ✅ Understand Docker image structure
- ✅ Learn how image layers work
- ✅ Master image commands
- ✅ Pull and manage images
- ✅ Understand image naming and versions

---

## What Are Docker Images?

Docker images are:
- **Read-only** templates/blueprints
- Made of **layered filesystems**
- Stored in **registries** (Docker Hub, private registries)
- Can be **versioned** with tags
- Contain application code + dependencies + runtime

### Image vs Container

```
Image       → Blueprint, static, stored
Container   → Running instance, dynamic, temporary

Image       → Like a class definition
Container   → Like an object instance

Image       → Like a recipe
Container   → Like a cooked dish
```

---

## Image Structure

Docker images are built in layers, each layer is read-only:

```
Layer 5: Application Code (app.py)
Layer 4: Dependencies (installed packages)
Layer 3: Runtime (Python runtime)
Layer 2: Base OS (Ubuntu or Alpine)
Layer 1: Base Filesystem

When you run: docker run
↓
A writable layer is added on top
↓
Container = Image + Writable Layer
```

### Why Layers Matter?

✅ **Efficiency** - Layers can be shared between images
✅ **Caching** - Docker caches layers during build
✅ **Size optimization** - Don't duplicate common layers
✅ **Fast startup** - Reuse existing layers

---

## Docker Image Naming Convention

```
image_name:tag

Examples:
ubuntu:22.04
python:3.11-slim
nginx:latest
node:18-alpine
myapp:v1.0

Format breakdown:
registry/repository:tag

Full example:
docker.io/library/ubuntu:22.04

- registry: docker.io (Docker Hub)
- repository: library (official images)
- image: ubuntu
- tag: 22.04
```

### Image Tags

```
ubuntu:latest       → Latest version
ubuntu:22.04        → Specific version
ubuntu:jammy        → Codename version
python:3.11-slim    → Specific version + variant
python:3.11-alpine  → Lightweight variant
```

---

## Image Management Commands

### Search for Images

```bash
# Search on Docker Hub
docker search ubuntu

# Output:
# NAME                          DESCRIPTION                    STARS
# ubuntu                        Ubuntu is a Debian-based...    14500
# ubuntu-debootstrap            DEPRECATED; see "ubuntu"...    90
# ubuntu/ubuntu-dev-environments Development environment...    50

docker search python

docker search nginx
```

### Pull (Download) Images

```bash
# Pull default latest version
docker pull ubuntu
docker pull python
docker pull nginx

# Pull specific version
docker pull ubuntu:22.04
docker pull python:3.11
docker pull nginx:1.25

# Pull lightweight variant
docker pull python:3.11-alpine
docker pull node:18-slim

# Pull from specific registry
docker pull gcr.io/google-samples/hello-app:1.0
```

### List Local Images

```bash
# List all images
docker images

# Output:
# REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
# ubuntu       22.04     3565a89d9e81   2 weeks ago    77MB
# python       3.11      abc123def456   3 days ago     1GB
# nginx        latest    def456ghi789   1 week ago     188MB

# Show with more info
docker images --all

# Show in quiet mode (only IDs)
docker images -q

# Show images for specific repo
docker images ubuntu

# Sort by size
docker images --format "table {{.Repository}}\t{{.Size}}"
```

### Inspect Image Details

```bash
# View image details
docker inspect ubuntu:22.04

# Output includes:
# - Image ID
# - Created date
# - Architecture
# - OS
# - Environment variables
# - Exposed ports
# - Volumes
# - Entry point
# - Command
# - Layers

# Get specific info
docker inspect --format='{{.Architecture}}' ubuntu:22.04
docker inspect --format='{{.Os}}' ubuntu:22.04
docker inspect --format='{{.Config.ExposedPorts}}' nginx:latest
```

### View Image History (Layers)

```bash
# View image layers
docker history ubuntu:22.04

# Output:
# IMAGE          CREATED       CREATED BY                                      SIZE
# 3565a89d9e81   2 weeks ago   /bin/sh -c #(nop) ADD file:xxxxx...            77MB
# <missing>      2 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]          0B
# <missing>      2 weeks ago   /bin/sh -c #(nop)  ENV ...                    0B

docker history python:3.11

docker history nginx:latest
```

### Tag Images

```bash
# Tag existing image with new name
docker tag ubuntu:22.04 myubuntu:v1.0

# Output: Now you have two references to same image
docker images
# ubuntu         22.04     3565a89d9e81
# myubuntu       v1.0      3565a89d9e81  (same ID!)

# Tag for pushing to registry
docker tag myapp:latest username/myapp:v1.0
docker tag myapp:latest docker.io/username/myapp:v1.0

# Tag with multiple versions
docker tag myapp:latest myapp:stable
docker tag myapp:latest myapp:production
```

### Remove Images

```bash
# Remove image by name
docker rmi ubuntu:22.04

# Remove image by ID
docker rmi 3565a89d9e81

# Remove multiple images
docker rmi ubuntu:22.04 python:3.11 nginx:latest

# Force remove (even if used)
docker rmi -f ubuntu:22.04

# Remove all unused images
docker image prune

# Remove all images (careful!)
docker rmi $(docker images -q)
```

---

## Image Repositories and Registries

### Docker Hub (Public)

```bash
# Docker Hub is default registry
docker pull ubuntu            # Pulls from docker.io/library/ubuntu
docker pull python:3.11       # Pulls from docker.io/library/python:3.11

# Official images (trusted)
docker pull ubuntu
docker pull nginx
docker pull postgres
docker pull redis

# User images
docker pull username/imagename:tag
```

### Push Image to Docker Hub

```bash
# 1. Login
docker login
# Enter username and password

# 2. Tag image
docker tag myapp:latest username/myapp:v1.0

# 3. Push
docker push username/myapp:v1.0

# 4. Pull from anywhere
docker pull username/myapp:v1.0

# 5. Logout
docker logout
```

### Private Registries

```bash
# Pull from private registry
docker pull myregistry.com/myapp:v1.0

# Push to private registry
docker tag myapp:latest myregistry.com/myapp:v1.0
docker push myregistry.com/myapp:v1.0
```

---

## Practical Examples

### Example 1: Pull and Run Different Versions

```bash
# Pull different Python versions
docker pull python:3.11
docker pull python:3.10
docker pull python:3.9

# Run different versions
docker run -it python:3.11 python --version
# Python 3.11.x

docker run -it python:3.10 python --version
# Python 3.10.x

docker run -it python:3.9 python --version
# Python 3.9.x
```

### Example 2: Compare Image Sizes

```bash
# Full vs Slim vs Alpine
docker pull python:3.11           # ~1GB
docker pull python:3.11-slim      # ~150MB
docker pull python:3.11-alpine    # ~50MB

# View sizes
docker images python

# Output:
# REPOSITORY   TAG            SIZE
# python       3.11           1GB
# python       3.11-slim      150MB
# python       3.11-alpine    50MB
```

### Example 3: Work with Official Images

```bash
# Ubuntu
docker run -it ubuntu:22.04 bash

# Alpine Linux (minimal)
docker run -it alpine:latest sh

# Debian
docker run -it debian:bookworm bash

# CentOS
docker run -it centos:7 bash
```

### Example 4: Check Image Compatibility

```bash
# Check architecture
docker inspect ubuntu:22.04 | grep -i architecture
# "Architecture": "amd64"

# Check OS
docker inspect ubuntu:22.04 | grep -i "os"
# "Os": "linux"

# Check created date
docker inspect ubuntu:22.04 | grep -i created
# "Created": "2024-01-10T10:00:00Z"
```

---

## Hands-On Exercises

### Exercise 1: Explore Multiple Python Versions

```bash
# Pull 3 different Python versions
docker pull python:3.11
docker pull python:3.10
docker pull python:3.8

# List images
docker images python

# Run each version
docker run python:3.11 python --version
docker run python:3.10 python --version
docker run python:3.8 python --version

# Compare sizes
docker images python

# Clean up
docker rmi python:3.11 python:3.10 python:3.8
```

### Exercise 2: Inspect Different Databases

```bash
# Pull different database images
docker pull postgres:15
docker pull mysql:8.0
docker pull mongodb:latest

# Inspect each
docker inspect postgres:15 | head -50
docker inspect mysql:8.0 | head -50
docker inspect mongodb:latest | head -50

# Check sizes
docker images
```

### Exercise 3: Work with Variants

```bash
# Pull different Node.js variants
docker pull node:18          # Full image (~1GB)
docker pull node:18-slim     # Slim variant (~200MB)
docker pull node:18-alpine   # Alpine variant (~50MB)

# Check sizes
docker images node

# Output:
# REPOSITORY   TAG         SIZE
# node         18          ~1GB
# node         18-slim     ~200MB
# node         18-alpine   ~50MB

# Run each
docker run node:18 node --version
docker run node:18-slim node --version
docker run node:18-alpine node --version

# Clean up
docker rmi node:18 node:18-slim node:18-alpine
```

### Exercise 4: Create Docker Hub Account (Optional)

```bash
# 1. Go to hub.docker.com
# 2. Sign up
# 3. Create repository
# 4. Tag local image
docker tag myapp:latest yourusername/myapp:v1.0

# 5. Login
docker login

# 6. Push
docker push yourusername/myapp:v1.0

# 7. Verify on hub.docker.com
```

---

## Image Best Practices

### ✅ DO:

✅ Use specific versions, not latest
```bash
docker pull python:3.11    # Good
docker pull python:latest  # Avoid
```

✅ Use lightweight variants when possible
```bash
docker pull python:3.11-alpine    # Smaller, faster
docker pull python:3.11-slim      # Medium
docker pull python:3.11           # Full (largest)
```

✅ Use official images
```bash
docker pull ubuntu          # Official
docker pull node            # Official
docker pull random/ubuntu   # Unknown source - risky!
```

✅ Inspect images before using
```bash
docker inspect ubuntu:22.04
docker history ubuntu:22.04
```

### ❌ DON'T:

❌ Use 'latest' tag in production
```bash
docker pull ubuntu:latest   # Latest changes, unpredictable
```

❌ Pull from unknown sources
```bash
docker pull randomuser/randomimage  # Unknown, security risk
```

❌ Keep unused images
```bash
# Clean up periodically
docker image prune
```

---

## Quick Reference

### Most Used Commands

```bash
# Search
docker search ubuntu

# Pull
docker pull ubuntu:22.04

# List
docker images

# Inspect
docker inspect ubuntu:22.04

# View history
docker history ubuntu:22.04

# Tag
docker tag ubuntu:22.04 myubuntu:v1

# Remove
docker rmi myubuntu:v1

# Clean up
docker image prune
```

---

## Summary

In Day 2 you learned:
✅ What Docker images are
✅ How image layers work
✅ Image naming and tagging
✅ How to search and pull images
✅ How to inspect and manage images
✅ Image repositories and registries
✅ Best practices for image selection

---

## Key Points to Remember

🔑 **Images** are read-only, **containers** are writable instances
🔑 **Layers** enable efficient caching and sharing
🔑 **Tags** allow multiple versions of same image
🔑 **Alpine** variants are smallest, **full** are largest
🔑 **Official** images are most reliable
🔑 **Registries** store images (Docker Hub, private, etc.)

---

## Troubleshooting

### Problem: Image not found
```bash
docker pull ubuntu:99.99
# Error: manifest not found

# Solution: Use valid version
docker pull ubuntu:22.04
```

### Problem: Not enough disk space
```bash
# Clean up unused images
docker image prune

# Remove specific image
docker rmi <image-id>
```

### Problem: Slow downloads
```bash
# Use lightweight variants
docker pull python:3.11-alpine  # Faster than full

# Use local cached images
# (Docker caches layers, second pull is instant)
```

---

## Resources

- [Docker Hub Official Images](https://hub.docker.com/search?image_filter=official)
- [Docker Documentation - Images](https://docs.docker.com/engine/images/)
- [Best Practices for Writing Dockerfiles](https://docs.docker.com/develop/dockerfile_best-practices/)

---

**Congratulations on completing Day 2! 🎉**

You now understand:
- How Docker images work
- How to find and pull images
- How to manage images locally
- Image best practices

Ready for **[Day 3 - Dockerfile](../Day03/)**

