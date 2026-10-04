

## 3. Docker Architecture & Engine Components


---

## 4. Docker Terminologies
---


---

## 6. Docker Core Command Reference

### Image & Container Management

```bash
# List all running containers
docker ps

# List all containers (including stopped/exited)
docker ps -a

# Search and run a container (interactively with pseudo-TTY)
docker run -it ubuntu bash

# Run a container in detached mode (-d) with port mapping (-p) and custom name
docker run -d --name web-server -p 8080:80 nginx:latest

# View container logs
docker logs <container_id_or_name>

# Execute interactive command inside a running container
docker exec -it <container_id_or_name> bash

# Stop a running container
docker stop <container_id_or_name>

# Remove a stopped container
docker rm <container_id_or_name>

# Forcefully remove a running container
docker rm -f <container_id_or_name>

# List locally available images
docker images

# Remove an image
docker rmi <image_id_or_name>
```

### Useful Cleanup Commands & Interview Tips

```bash
# Remove all stopped containers, unused networks, dangling images, and build cache
docker system prune -f

# INTERVIEW TRICK: Remove ALL stopped containers without using 'docker system prune'
docker rm $(docker ps -aq)

# INTERVIEW TRICK: Remove ALL local docker images
docker rmi $(docker images -aq)
```

---



All topics, code snippets, architecture details, and commands from the PDF document are included in `Docker_Complete_Guide.md`.