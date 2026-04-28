## Day 9: Best Practices

### Learning Objectives
- ✅ Dockerfile optimization
- ✅ Health checks
- ✅ Security basics
- ✅ Resource limits

### Optimized Dockerfile

```dockerfile
# Multi-stage build
FROM python:3.11-slim as builder

WORKDIR /build

COPY requirements.txt .
RUN pip install --target /packages -r requirements.txt

# Final stage
FROM python:3.11-slim

WORKDIR /app

# Copy only what's needed
COPY --from=builder /packages /usr/local/lib/python3.11/site-packages
COPY app.py .

# Create non-root user
RUN useradd -m appuser
USER appuser

EXPOSE 5000

CMD ["python", "app.py"]
```

### Health Checks

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y curl

HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1

CMD ["python", "app.py"]
```


# ✅ Use read-only root filesystem (in compose)
read_only: true
tmpfs:
  - /tmp
```

### Resource Limits

```yaml
services:
  app:
    image: myapp:latest
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 512M
        reservations:
          cpus: '0.5'
          memory: 256M
```

### Exercises

1. **Write multi-stage Dockerfile** - See size reduction
2. **Add health checks** - To docker-compose.yml
3. **Add resource limits** - Set CPU and memory


---
