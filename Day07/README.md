## Day 7: Docker Compose Basics

### Learning Objectives
- ✅ Write docker-compose.yml
- ✅ Multi-container setup
- ✅ Service definition
- ✅ Basic networking

### Docker Compose Installation

```bash
# Usually included with Docker Desktop
docker-compose --version

# If not, install:
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### Simple docker-compose.yml

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
```

### Docker Compose Commands

```bash
# Start services
docker-compose up

# Start in background
docker-compose up -d

# View running
docker-compose ps

# View logs
docker-compose logs
docker-compose logs web

# Stop services
docker-compose stop

# Stop and remove
docker-compose down

# Rebuild images
docker-compose up --build
```

### Example: Web + Database

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    depends_on:
      - db
  
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: mypassword
    volumes:
      - dbdata:/var/lib/postgresql/data

volumes:
  dbdata:
```

### Service Definition

```yaml
services:
  myapp:
    image: myapp:1.0          # Image to use
    ports:
      - "8000:5000"           # Port mapping
    environment:              # Environment variables
      DEBUG: "true"
    volumes:
      - ./data:/app/data      # Mount directory
    depends_on:               # Wait for other service
      - db
```

### Exercises

1. **Create docker-compose.yml** - Two services
2. **Start with** `docker-compose up`
3. **View logs** - `docker-compose logs`
4. **Stop** - `docker-compose down`

### Quick Notes

- version: '3.8' is latest stable
- Services automatically networked
- Service names = hostnames
- Volumes can be named or paths

---