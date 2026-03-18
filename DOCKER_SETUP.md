# 🐳 Docker Setup Guide

## Prerequisites

1. **Docker Desktop** installed and running
   - Download from: https://www.docker.com/products/docker-desktop
   - Verify installation: `docker --version` and `docker-compose --version`

2. **Project structure** with pom.xml files in each service directory

## Quick Start - Run All Services with Docker

### Step 1: Build and Start All Services

```powershell
cd c:\Projects\Personal\EmployeeServices

# Build and start all services in detached mode
docker-compose up -d --build
```

This will:
- Build Docker images for all services
- Start all containers
- Create an internal network for service communication
- Run health checks

### Step 2: Verify Services are Running

```powershell
# View running containers
docker-compose ps

# View logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f employee-service
docker-compose logs -f department-service
```

### Step 3: Access the Dashboard

Open in your browser:
```
file:///c:/Projects/Personal/EmployeeServices/dashboard.html
```

The dashboard is already configured for Docker URLs (localhost:8090, 8091, 8092, etc.)

---

## Common Docker Commands

### Start Services
```powershell
# Start all services
docker-compose up -d

# Start with build (if code changed)
docker-compose up -d --build

# Start specific service
docker-compose up -d employee-service
```

### Stop Services
```powershell
# Stop all services
docker-compose down

# Stop and remove volumes (clean slate)
docker-compose down -v

# Stop specific service
docker-compose stop employee-service
```

### View Information
```powershell
# List running containers
docker-compose ps

# View logs
docker-compose logs

# View logs for specific service
docker-compose logs -f employee-service

# Follow logs in real-time
docker-compose logs -f

# View last 100 lines
docker-compose logs --tail=100
```

### Restart Services
```powershell
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart employee-service
```

---

## Service URLs (Docker)

All services run on localhost with exposed ports:

| Service | Port | URL |
|---------|------|-----|
| Eureka Service | 8761 | http://localhost:8761 |
| Department Service | 8090 | http://localhost:8090/api/department |
| Employee Service | 8091 | http://localhost:8091/api/employee |
| Cloud Gateway | 8092 | http://localhost:8092 |
| Config Server | 8093 | http://localhost:8093 |

---

## Troubleshooting

### Services won't start
```powershell
# Clean build and start fresh
docker-compose down -v
docker-compose up -d --build
```

### View build output
```powershell
docker-compose logs --no-timestamp
```

### Force rebuild
```powershell
docker-compose build --no-cache
docker-compose up -d
```

### Check service health
```powershell
docker-compose logs eureka-service | Select-String "started"
```

### Access service shell
```powershell
# Connect to a running container
docker-compose exec employee-service sh
```

---

## Network Communication (Inside Docker)

Services communicate via the internal `microservices-net` bridge network:
- Employee Service: `http://employee-service:8091`
- Department Service: `http://department-service:8090`
- Eureka Service: `http://eureka-service:8761`
- Config Server: `http://config-server:8093`
- Cloud Gateway: `http://cloud-gateway:8092`

---

## Performance Notes

- **First build**: Takes 3-5 minutes (downloads dependencies)
- **Subsequent builds**: Much faster (cached layers)
- **Memory**: Requires ~2GB RAM for all services
- **Disk**: ~1GB for Docker images

---

## Hybrid Approach (Optional)

Run some services with Docker and others locally:

```powershell
# Start only Eureka with Docker
docker-compose up -d eureka-service

# Then start Employee Service locally
cd employee-service
mvn spring-boot:run
```

---

## Advanced: Custom Configuration

Edit `docker-compose.yml` to:
- Change port mappings
- Set custom environment variables
- Add volume mounts for persistent data
- Modify restart policies

Example - Change ports:
```yaml
employee-service:
  ports:
    - "9091:8091"  # External:Internal mapping
```

---

## Cleanup

```powershell
# Remove stopped containers
docker-compose rm

# Remove unused images
docker image prune

# Full cleanup (remove everything)
docker-compose down -v
```
