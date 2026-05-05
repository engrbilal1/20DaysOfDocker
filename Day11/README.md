## Day 11: Troubleshooting

### Learning Objectives
- ✅ View logs
- ✅ Debug containers
- ✅ Inspect running containers
- ✅ Common issues

### Viewing Logs

```bash
# All logs
docker logs <container>

# Last 100 lines
docker logs --tail 100 <container>

# Follow logs (like tail -f)
docker logs -f <container>

# Logs from time
docker logs --since 2024-01-01 <container>

# Timestamps
docker logs -t <container>
```

### Inspect Container

```bash
# Full details
docker inspect <container>

# Specific info
docker inspect --format='{{.State.Running}}' <container>

# Network info
docker inspect --format='{{.NetworkSettings.IPAddress}}' <container>

# Mount info
docker inspect --format='{{json .Mounts}}' <container>
```

### Execute Commands

```bash
# Run command
docker exec <container> ls -la

# Interactive shell
docker exec -it <container> bash
docker exec -it <container> sh

# Check if service running
docker exec <container> curl http://localhost:8080
```

### Common Issues & Solutions

**Container exits immediately:**
```bash
# Check logs
docker logs <container>

# Run with -it to see error
docker run -it <image>
```

**Port already in use:**
```bash
# Use different port
docker run -p 9000:80 nginx

# Check what's using port
lsof -i :8080  # Mac/Linux
netstat -ano | findstr :8080  # Windows
```

**Out of memory:**
```bash
# Set memory limit
docker run -m 512m <image>

# Check Docker's disk usage
docker system df
```

**Permission denied:**
```bash
# Run with sudo or add user to docker group
sudo docker run <image>

# Add user to group (Linux)
sudo usermod -aG docker $USER
```

### Debug Commands

```bash
# Check system resources
docker stats

# View running processes
docker top <container>

# Diff with image
docker diff <container>

# Export container filesystem
docker export <container> > backup.tar
```

### Exercises

1. **View logs** - From a running container
2. **Execute command** - `docker exec` into container
3. **Inspect container** - Check IP, mounts, etc.
4. **Troubleshoot issue** - Fix common problems

### Quick Notes

- Logs show stdout/stderr
- -it flags = Interactive terminal
- inspect = Detailed container info
- Docker logs ≠ Application logs (use volumes for files)

---