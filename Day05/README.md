## Day 5: Networking

### Learning Objectives
- ✅ Docker networking modes
- ✅ Port mapping
- ✅ Container-to-container communication
- ✅ Custom networks

### Networking Modes

```bash
# Bridge (default)
docker run ubuntu
docker run --net bridge ubuntu

# Host
docker run --net host ubuntu

# None
docker run --net none ubuntu
```

### Port Mapping

```bash
# Map port
docker run -p 8080:80 nginx

# Map to localhost only
docker run -p 127.0.0.1:8080:80 nginx

# Random port
docker run -P nginx

# Multiple ports
docker run -p 8080:80 -p 3000:3000 app
```

### Container Communication

```bash
# Two containers on same network can communicate
docker network create mynet

# Run two containers on network
docker run -d --name db --net mynet postgres
docker run -d --name app --net mynet python-app

# app can reach db at hostname 'db'
```

### Network Commands

```bash
# List networks
docker network ls

# Create network
docker network create mynet

# Connect container to network
docker network connect mynet <container>

# Disconnect
docker network disconnect mynet <container>

# Inspect network
docker network inspect mynet

# Remove network
docker network rm mynet
```

### Example: Web + Database

```bash
# Create network
docker network create appnet

# Run database
docker run -d --name database --net appnet postgres

# Run web app
docker run -d --name webapp --net appnet -p 8080:5000 myapp

# app connects to database via hostname 'database'
```

### Exercises

1. **Create network** - `docker network create mynet`
2. **Run two containers** - Connected to same network
3. **Test communication** - `docker exec app ping db`
4. **Port mapping** - Access web app on localhost:8080

### Quick Notes

- Bridge = Default, internal networking
- Host = Share host's network (fast but less isolation)
- Container names resolve to IPs within network
- Port mapping = Forward traffic from host to container

---