## 7. Working with Dockerfiles & Building Images

### Core Dockerfile Directives

| Instruction | Description |
| :--- | :--- |
| `FROM` | Defines base image (e.g., `FROM python:3.9-slim`, `FROM openjdk:17-jdk-alpine`). |
| `WORKDIR` | Sets the working directory inside the container. |
| `COPY` | Copies files/directories from host system into container. |
| `ADD` | Copies files; also supports tar auto-extraction and remote URLs. |
| `RUN` | Executes build commands during image creation (e.g., `apt update`, `pip install`). |
| `ENV` | Sets environment variables. |
| `EXPOSE` | Documents the port the container listens on at runtime. |
| `CMD` | Default command executed when container starts (can be overridden). |
| `ENTRYPOINT` | Primary executable command for the container. |

---

### Project Examples

#### Example 1: Java Application Dockerfile

```dockerfile
# 1. Base Image
FROM openjdk:17-jdk-alpine

# 2. Working Directory
WORKDIR /app

# 3. Copy application files
COPY src/Main.java /app/Main.java
COPY quotes.txt /app/quotes.txt

# 4. Compile application
RUN javac Main.java

# 5. Document container port
EXPOSE 8000

# 6. Set default execution command
CMD ["java", "Main"]
```

#### Example 2: Flask Application Dockerfile

```dockerfile
# Base Image
FROM python:3.14-slim

# Working Directory
WORKDIR /app

# Copy Source Code
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose Port
EXPOSE 80

# Run Application
CMD ["python", "run.py"]
```

### Image Build & Execution Commands

```bash
# Build image with a custom tag from current directory
docker build -t java-quotes-app:latest .

# Build specify a non-standard Dockerfile path
docker build -f Dockerfile-custom -t my-app:v1.0 .

# Run container mapped to host port 8000
docker run -d -p 8000:8000 --name Java-Quotes-App java-quotes-app:latest
```

---

## 8. Multi-Stage Builds & Distroless Images

Multi-stage builds allow separating build-time dependencies from runtime requirements, resulting in lightweight, secure, and production-ready images.

```dockerfile
# ==========================================
# STAGE 1: Builder Stage
# ==========================================
FROM python:3.9-slim AS builder

WORKDIR /app

COPY requirements.txt .

# Install dependencies into a isolated directory
RUN pip install --no-cache-dir -r requirements.txt --target=/app/deps

COPY . .

# ==========================================
# STAGE 2: Distroless Runtime Stage
# ==========================================
FROM gcr.io/distroless/python3-debian12

WORKDIR /app

# Copy built dependencies and source code from Builder stage
COPY --from=builder /app/deps /app/deps
COPY --from=builder /app /app

# Configure python path to find dependencies
ENV PYTHONPATH="/app/deps"

EXPOSE 80

CMD ["run.py"]
```

**Build command:**
```bash
docker build -f Dockerfile-multi -t python-app-mini:latest .
```

---

## 9. Docker Hub Operations (Push, Pull, Tag)

### Authentication & Image Management

```bash
# Login to Docker Hub using Username and Personal Access Token (PAT)
docker login -u <your_dockerhub_username>

# Tag an existing local image for Docker Hub repository
docker tag python-app-mini:latest <your_dockerhub_username>/python-app-mini:latest

# Push image to Docker Hub
docker push <your_dockerhub_username>/python-app-mini:latest

# Pull image from Docker Hub
docker pull <your_dockerhub_username>/python-app-mini:latest

# Logout from Docker Hub
docker logout
```

---

## 10. Docker Volumes (Data Persistence)

Volumes persist container-generated data outside the container file system layer (on host disk under `/var/lib/docker/volumes/`).

### Volume Commands

```bash
# Create a managed volume
docker volume create myvolume

# List available volumes
docker volume ls

# Inspect volume details
docker volume inspect myvolume
```

### Running Containers with Volumes

#### General Application Volume Mounting
```bash
docker run -d \
  --name mycontainer \
  -v myvolume:/app/data \
  nginx
```

#### MySQL Database Volume Persistence
```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=ROOT \
  -v mysql_data:/var/lib/mysql \
  mysql:latest
```

---

## 11. Docker Networking & Multi-Tier Applications

### Docker Network Drivers

1. **Bridge (Default):** Isolated network on host; containers communicate via internal IP or container names within user-defined networks.
2. **Host:** Removes network isolation between container and host system.
3. **None:** Disables networking completely for the container.
4. **Overlay:** Enables container networking across multiple Docker hosts (Docker Swarm/Kubernetes).
5. **Macvlan:** Assigns a MAC address to a container, making it appear as a physical device on the network.

---

### Hands-on: Building a 2-Tier Architecture (Flask + MySQL)

```
[ User / Client ] 
       |
       v (Port 5000)
+-------------------------------------------------------+
|  Custom Docker Network: "twotier"                      |
|                                                       |
|  +--------------------+       +--------------------+  |
|  | Container:         |       | Container:         |  |
|  | flask-app          | ----> | mysql2             |  |
|  | (Backend Service)  |       | (Database)         |  |
|  +--------------------+       +--------------------+  |
+-------------------------------------------------------+
```

#### Step 1: Create Custom Network
```bash
docker network create twotier
docker network ls
```

#### Step 2: Launch MySQL Database Container
```bash
docker run -d \
  --name mysql2 \
  --network twotier \
  -v mysql_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DATABASE=tws_db \
  -e MYSQL_USER=admin \
  -e MYSQL_PASSWORD=admin \
  -p 3306:3306 \
  mysql:latest
```

#### Step 3: Launch Flask Backend Container Connected to DB
```bash
docker run -d \
  --name flask-app \
  --network twotier \
  -p 5000:5000 \
  -e MYSQL_HOST=mysql2 \
  -e MYSQL_USER=admin \
  -e MYSQL_PASSWORD=admin \
  -e MYSQL_DB=tws_db \
  flask-app:latest
```

#### Step 4: Verify Network Connectivity
```bash
docker inspect twotier
docker logs flask-app
```

---

## 12. Docker Compose

Docker Compose simplifies multi-container deployments using a single `docker-compose.yml` file.

### Example `docker-compose.yml`

```yaml
version: '3.8'

services:
  web:
    image: flask-app:latest
    container_name: flask_web
    ports:
      - "5000:5000"
    environment:
      - MYSQL_HOST=db
      - MYSQL_USER=admin
      - MYSQL_PASSWORD=admin
      - MYSQL_DB=tws_db
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: mysql:latest
    container_name: mysql_db
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_DATABASE: tws_db
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  db_data:
```

### Key Commands

```bash
# Start all services in detached mode
docker compose up -d

# Check status of compose services
docker compose ps

# View aggregate logs
docker compose logs -f

# Stop and remove all containers, networks, and volumes
docker compose down
```
```