# Day 3: Dockerfile

## Learning Objectives

By the end of this day, you will:
- ✅ Write efficient Dockerfiles
- ✅ Understand all Dockerfile instructions
- ✅ Build custom Docker images
- ✅ Best practices for Dockerfiles
- ✅ Multi-stage builds

---

## What is a Dockerfile?

A **Dockerfile** is a text file containing instructions to build a Docker image.

Think of it as a recipe:
- Each instruction creates a layer
- Layers are stacked to create final image
- Read-only, can be versioned
- Reproducible across environments

---

## Dockerfile Instructions

### FROM - Base Image

```dockerfile
FROM ubuntu:22.04
FROM python:3.11
FROM node:18-alpine
```

**Explanation:**
- Must be first instruction
- Specifies base image
- Use specific versions, not `latest`

---

### RUN - Execute Commands

```dockerfile
RUN apt-get update
RUN apt-get install -y python3
RUN pip install flask
```

**Multiple commands in single RUN:**
```dockerfile
RUN apt-get update && \
    apt-get install -y python3 && \
    pip install flask
```

**Why combine?**
- Reduces layers
- Smaller image size
- Better performance

---

### COPY - Copy Files from Host

```dockerfile
COPY requirements.txt /app/
COPY app.py /app/
COPY src/ /app/src/
```

**Syntax:** `COPY <source-on-host> <destination-in-container>`

---

### ADD - Copy with Archive Support

```dockerfile
ADD requirements.txt /app/
ADD app.tar.gz /app/
```

**Difference from COPY:**
- Can copy from URLs
- Can extract archives automatically
- Usually, use COPY instead (simpler)

---

### WORKDIR - Set Working Directory

```dockerfile
WORKDIR /app
RUN pip install -r requirements.txt
COPY . .
```

**Equivalent to:**
```bash
cd /app
```

---

### ENV - Environment Variables

```dockerfile
ENV PYTHONUNBUFFERED=1
ENV DEBUG=true
ENV APP_VERSION=1.0
```

**Usage in container:**
```bash
echo $PYTHONUNBUFFERED  # Output: 1
echo $DEBUG            # Output: true
```

---

### EXPOSE - Document Ports

```dockerfile
EXPOSE 8080
EXPOSE 3000
EXPOSE 5432
```

**Note:** Doesn't actually publish port
- Use `-p` flag with `docker run` to publish

---

### CMD - Default Command

```dockerfile
CMD ["python", "app.py"]
CMD ["npm", "start"]
CMD ["/bin/bash"]
```

**Only last CMD is executed**
- Can be overridden with `docker run`

---

### ENTRYPOINT - Set Entry Command

```dockerfile
ENTRYPOINT ["python"]
```

**Difference from CMD:**
- ENTRYPOINT: Main command (not easily overridden)
- CMD: Arguments to ENTRYPOINT

**Example:**
```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]

# Results in: python app.py
```

---

## Complete Dockerfile Examples

### Example 1: Python Flask App

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

**Build:**
```bash
docker build -t myflask:1.0 .
```

**Run:**
```bash
docker run -p 5000:5000 myflask:1.0
```

---

### Example 2: Node.js App

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

### Example 3: Multi-Stage Build (Smaller Image!)

```dockerfile
# Stage 1: Builder
FROM python:3.11 as builder

WORKDIR /build
COPY requirements.txt .
RUN pip install --target /packages -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app
COPY --from=builder /packages /usr/local/lib/python3.11/site-packages
COPY app.py .

CMD ["python", "app.py"]
```

**Why multi-stage?**
- Build tools not in final image
- Smaller final size
- Faster deployment

---

## Building Images

### Basic Build

```bash
docker build -t myapp:1.0 .
```

**Flags:**
- `-t` = Tag (name and version)
- `.` = Build context (current directory)

### Build with Different Name

```bash
docker build -t myapp:latest .
docker build -t myapp:v1.0 .
docker build -t username/myapp:1.0 .
```

### View Build Progress

```bash
docker build --progress=plain -t myapp:1.0 .
```

### Build from Specific Dockerfile

```bash
docker build -f Dockerfile.prod -t myapp:prod .
```

---

## Best Practices

### ✅ DO:

✅ **Use specific base image versions**
```dockerfile
FROM python:3.11-slim      # Good
FROM python:latest         # Bad
```


✅ **Keep image small**
```dockerfile
# Use Alpine or slim variants
FROM python:3.11-alpine    # ~50MB
FROM python:3.11-slim      # ~150MB
FROM python:3.11           # ~1GB
```

### ❌ DON'T:

❌ **Use latest tag**
```dockerfile
FROM ubuntu:latest         # Can break unexpectedly
```

❌ **Run as root**
```dockerfile
# Default is root - create user instead
```

❌ **Too many layers**
```dockerfile
RUN apt-get update
RUN apt-get install -y python
RUN apt-get install -y pip
```

---

## Practical Exercises

### Exercise 1: Create Simple Python Dockerfile

```bash
# Create Dockerfile
cat > Dockerfile << 'END'
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

CMD ["python", "app.py"]
END

# Create app.py
cat > app.py << 'END'
print("Hello from Docker!")
for i in range(3):
    print(f"Count: {i}")
END

# Create requirements.txt
echo "flask==2.3.0" > requirements.txt

# Build image
docker build -t myapp:1.0 .

# Run container
docker run myapp:1.0
```

**Expected output:**
```
Hello from Docker!
Count: 0
Count: 1
Count: 2
```

---

### Exercise 2: Multi-Stage Build

```dockerfile
# Create optimized Dockerfile
FROM python:3.11 as builder

WORKDIR /build
RUN pip install --target /packages flask

FROM python:3.11-slim

WORKDIR /app
COPY --from=builder /packages /usr/local/lib/python3.11/site-packages
COPY app.py .

CMD ["python", "app.py"]
```

**Build and compare:**
```bash
docker build -t app-full:1.0 .    # Multi-stage

# Check size
docker images app-full
```

---

### Exercise 3: Dockerfile with Multiple Stages

```dockerfile
FROM python:3.11 as dev
RUN pip install pytest
RUN pip install black

FROM python:3.11-slim as prod
COPY --from=dev /usr/local/bin /usr/local/bin
COPY app.py .
CMD ["python", "app.py"]
```

---

## Dockerfile Optimization

### Image Size Comparison

```
Ubuntu base:          500 MB
+ Dependencies:       700 MB
Total (full):         1.2 GB

Alpine base:          10 MB
+ Dependencies:       40 MB
Total (alpine):       50 MB

Size reduction: 96%! 🚀
```

### Layer Caching

```dockerfile
# Bad: Changes invalidate cache
COPY . .
RUN pip install -r requirements.txt

# Good: Stable first, changing last
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

---

## Troubleshooting

### Problem: Build fails with "file not found"

```bash
# Make sure file exists and working directory is correct
docker build -t myapp:1.0 .

# Check if Dockerfile is in current directory
ls -la Dockerfile
```

### Problem: Image too large

```bash
# Use smaller base image
FROM python:3.11-alpine    # Instead of python:3.11

# Use multi-stage builds
# See examples above
```

### Problem: Build takes too long

```bash
# Create .dockerignore to exclude files
cat > .dockerignore << 'END'
.git
node_modules
__pycache__
.env
END

# Use layer caching (copy requirements first)
```

---

## Quick Reference

```dockerfile
# Template
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

USER appuser

EXPOSE 8000

CMD ["python", "app.py"]
```

---

## Summary

In Day 3 you learned:
✅ What Dockerfiles are
✅ All Dockerfile instructions
✅ How to build images
✅ Multi-stage builds
✅ Best practices
✅ Optimization techniques

---

**Congratulations on completing Day 3! 🎉**

You can now:
- Write Dockerfiles
- Build custom images
- Optimize image size
- Use best practices

Ready for **[Day 4 - Containers](../Day04/)**

