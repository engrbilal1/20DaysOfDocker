## Day 13: Security Basics

### Learning Objectives
- ✅ Container security
- ✅ User permissions
- ✅ Network security
- ✅ Secrets management

### User Permissions

```dockerfile
# ✅ GOOD: Non-root user
FROM ubuntu:22.04
RUN useradd -m appuser
USER appuser

# ❌ BAD: Running as root (default)
FROM ubuntu:22.04
# User not specified = root
```

### Network Security

```yaml
# ✅ GOOD: Custom network, not exposed
services:
  db:
    image: postgres:15
    networks:
      - internal
    # No ports exposed!

  web:
    image: myapp:latest
    ports:
      - "8080:80"
    networks:
      - internal

networks:
  internal:
    internal: true
```

### Capabilities

```bash
# Drop all capabilities
docker run --cap-drop=ALL <image>

# Add specific capability
docker run --cap-add=NET_BIND_SERVICE <image>

# In compose
cap_drop:
  - ALL
cap_add:
  - NET_BIND_SERVICE
```

### Secrets Management

```bash
# Using environment (not ideal)
docker run -e PASSWORD=secret <image>

# Using files
docker run -v /run/secrets:/run/secrets:ro <image>

# Using Docker secrets (Swarm)
docker secret create db_password /path/to/password
docker run --secret db_password <image>
```

### Exercises

1. **Create non-root user** - In Dockerfile
2. **Network isolation** - Custom network
3. **Drop capabilities** - Reduce permissions
4. **Manage secrets** - Environment files

### Quick Notes

- Root user = Security risk
- Containers isolated by default
- Network policies = Extra security
- Secrets ≠ Environment variables

---