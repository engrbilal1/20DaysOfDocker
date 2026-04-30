## Day 10: Performance Optimization

### Learning Objectives
- ✅ Reduce image size
- ✅ Speed up builds
- ✅ Optimize layers
- ✅ Caching strategies

### Image Size Reduction

**Before (Large):**
```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y \
    python3 python3-pip gcc build-essential
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
CMD ["python3", "app.py"]
```

Size: 500+ MB

**After (Optimized):**
```dockerfile
FROM python:3.11-slim
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
CMD ["python", "app.py"]
```

Size: 150 MB ✅


### Build Caching

```dockerfile
# Bad: Changes invalidate cache
FROM ubuntu:latest
RUN apt-get update
COPY . /app              # Frequently changes
RUN pip install -r requirements.txt

# Good: Stable first, changing last
FROM python:3.11-slim
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app              # Changes often, placed last
```

### Layer Optimization

```dockerfile
# Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y python3
RUN apt-get install -y pip

# Good: Single layer
RUN apt-get update && apt-get install -y python3 pip

# Bad: Extra files
COPY . /app
RUN rm -rf /app/tests /app/docs

# Good: .dockerignore prevents copying
# .dockerignore:
# tests/
# docs/
# .git
```

### Exercises

1. **Compare image sizes** - Different base images
2. **Use .dockerignore** - Reduce context