## Day 4: Containers

### Learning Objectives
- ✅ Container lifecycle
- ✅ Container management
- ✅ View logs and inspect
- ✅ Execute commands in containers

### Container Lifecycle

```
Image → Create Container → Start → Running → Stop → Stopped → Remove
```

### Container Commands

```bash
# Create and run
docker run ubuntu echo "Hello"
docker run -d -p 8080:80 nginx              # Detached
docker run -it ubuntu bash                  # Interactive
docker run --name mycontainer ubuntu        # With name

# List containers
docker ps                   # Running
docker ps -a               # All
docker ps -q               # Only IDs

# Start/Stop
docker start <container>
docker stop <container>
docker restart <container>

# Remove
docker rm <container>
docker rm $(docker ps -aq) # Remove all

# Inspect
docker inspect <container>
docker logs <container>
docker logs -f <container> # Follow logs

# Execute command
docker exec <container> ls
docker exec -it <container> bash
```

### Container States

```
Created → Running → Paused → Exited → Removed

docker create ...  → Created
docker start ...   → Running
docker pause ...   → Paused
docker unpause ... → Running
docker stop ...    → Exited
docker rm ...      → Removed
```

### Example: Web Server

```bash
# Create and run
docker run -d -p 8080:80 --name webserver nginx

# Check it's running
docker ps

# View logs
docker logs webserver

# Access it
curl http://localhost:8080

# Stop it
docker stop webserver

# Start again
docker start webserver

# Remove
docker rm webserver
```

### Exercises

1. **Run container** - See different states
2. **View logs** - See application output
3. **Execute command** - `docker exec -it container bash`
4. **Clean up** - Stop and remove containers

### Quick Notes

- Container = Running instance of image
- Containers are ephemeral (data lost when removed)
- Use volumes for persistent data
- One main process per container (best practice)

---