## Day 12: Advanced Docker

### Learning Objectives
- ✅ Custom networks
- ✅ Resource limits
- ✅ Container lifecycle
- ✅ Advanced options

### Custom Networks

```bash
# Create network
docker network create --driver bridge mynet

# Create with subnet
docker network create --driver bridge \
  --subnet 172.20.0.0/16 mynet

# Run containers on network
docker run --net mynet --name app1 ubuntu
docker run --net mynet --name app2 ubuntu

# Container communication
docker exec app1 ping app2
```

### Resource Management

```bash
# Memory limit
docker run -m 512m <image>

# CPU limit
docker run --cpus 1.5 <image>

# Memory & CPU
docker run -m 512m --cpus 1 <image>

# Compose resource limits
services:
  app:
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1G
        reservations:
          cpus: '1'
          memory: 512M
```

### Container Options

```bash
# Restart policy
docker run --restart always <image>
docker run --restart on-failure:5 <image>
docker run --restart unless-stopped <image>

# Privileged mode (be careful!)
docker run --privileged <image>

# Security options
docker run --security-opt no-new-privileges <image>
docker run --read-only <image>

# Device access
docker run --device /dev/sda:/dev/sda <image>

# Hostname
docker run --hostname myhost <image>
```

### Example: Production Setup

```bash
docker run -d \
  --name webapp \
  --net appnet \
  -m 1g \
  --cpus 2 \
  --restart unless-stopped \
  --read-only \
  --user 1000:1000 \
  -v appdata:/data \
  -p 8080:80 \
  myapp:latest
```

### Exercises

1. **Create custom network** - Connect containers
2. **Set resource limits** - CPU and memory
3. **Use restart policy** - See container restart
4. **Security options** - Read-only filesystem

### Quick Notes

- Networks enable DNS between containers
- Resource limits prevent runaway containers
- Restart policy = Automatic recovery
- Privileged mode = full host access (avoid if possible)

---