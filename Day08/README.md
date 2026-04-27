## Day 8: Docker Compose Advanced

### Learning Objectives
- ✅ Advanced networking
- ✅ Environment management
- ✅ Health checks
- ✅ Multi-file compose

### Advanced docker-compose.yml

```yaml
version: '3.8'

services:
  web:
    build: .                  # Build from Dockerfile
    image: myapp:latest
    container_name: webapp
    ports:
      - "8080:80"
    environment:
      DATABASE_URL: postgresql://db:5432/myapp
      SECRET_KEY: ${SECRET_KEY}  # From .env
    depends_on:
      db:
        condition: service_healthy
    networks:
      - appnet
    restart: unless-stopped
    
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    secrets:
      - db_password
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - appnet
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

networks:
  appnet:
    driver: bridge

volumes:
  pgdata:

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

### Environment Files

**.env file:**
```
SECRET_KEY=my-secret-key
DEBUG=true
DATABASE_URL=postgresql://db/myapp
```

**Usage in docker-compose.yml:**
```yaml
environment:
  SECRET_KEY: ${SECRET_KEY}
  DEBUG: ${DEBUG}
```

### Health Checks

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

### Multiple Compose Files

**docker-compose.yml** - Base
```yaml
services:
  web:
    image: myapp:latest
    ports:
      - "8000:8000"
```

**docker-compose.prod.yml** - Production overrides
```yaml
services:
  web:
    restart: always
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 1G
```

**Run with:**
```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

### Exercises

1. **Create .env file** - With variables
2. **Use in compose** - Reference variables
3. **Add health checks** - To services
4. **Test restart policy** - Stop service, see it restart

### Quick Notes

- Build = Create image from Dockerfile
- Environment = Runtime variables
- Secrets = Sensitive data (passwords, keys)
- Health checks = Docker monitors container health
- depends_on = Start order

---
