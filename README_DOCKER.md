# 101-App Docker Guide

A quick-start guide to building, running, and managing your Dockerized Node.js application.

## Quick Start

### 1. Build the Docker Image
```bash
docker build -t 101-app:latest .
```

### 2. Run the Container
```bash
docker run -p 3000:3000 101-app:latest
```

Your app is now running at `http://localhost:3000`

### 3. Stop the Container
```bash
docker stop <container-id>
```

Get the container ID from `docker ps` output.

---

## What's Inside?

- **Base Image:** Node.js 18 on Alpine Linux
- **Framework:** Express.js
- **Databases:** MySQL2 & SQLite3 support
- **Entry Point:** `src/index.js`
- **Port:** 3000

---

## Common Commands

### Build
```bash
# Build with default tag
docker build -t 101-app:latest .

# Build with custom tag
docker build -t 101-app:v1.0 .
```

### Run
```bash
# Basic run
docker run -p 3000:3000 101-app:latest

# Run in background (detached mode)
docker run -d -p 3000:3000 --name my-app 101-app:latest

# Run with environment variables
docker run -p 3000:3000 -e NODE_ENV=production 101-app:latest

# Run with port mapping to different host port
docker run -p 8080:3000 101-app:latest
# Access at http://localhost:8080
```

### Container Management
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# View logs
docker logs <container-id>

# Follow logs (live stream)
docker logs -f <container-id>

# Stop container
docker stop <container-id>

# Remove container
docker rm <container-id>

# Execute command in running container
docker exec -it <container-id> sh
```

### Image Management
```bash
# List images
docker images

# Remove image
docker rmi 101-app:latest

# Inspect image
docker image inspect 101-app:latest

# View image history
docker history 101-app:latest
```

---

## Development vs Production

### Development Mode (with hot reload)
Use `docker-compose.yml` for development:
```bash
docker compose up
```

### Production Mode
Build and run the container directly:
```bash
docker build -t 101-app:latest .
docker run -d -p 3000:3000 --name 101-app 101-app:latest
```

---

## Dockerfile Breakdown

| Instruction | Purpose |
|------------|---------|
| `FROM node:18-alpine` | Use Node.js 18 base image on Alpine Linux |
| `WORKDIR /app` | Set working directory inside container |
| `COPY package*.json ./` | Copy dependency files |
| `RUN npm install --production` | Install production dependencies |
| `COPY src src` | Copy application source code |
| `EXPOSE 3000` | Document the listening port |
| `CMD ["node", "src/index.js"]` | Run the app on container start |

---

## Troubleshooting

### Port Already in Use
```bash
# Use a different host port
docker run -p 8080:3000 101-app:latest
```

### View Container Logs
```bash
docker logs <container-id>
```

### Container Exited Immediately
```bash
# Check logs for error messages
docker logs <container-id>

# Keep container running for inspection
docker run -it 101-app:latest sh
```

### Delete Everything and Start Fresh
```bash
# Stop all containers
docker stop $(docker ps -q)

# Remove all containers
docker rm $(docker ps -a -q)

# Remove image
docker rmi 101-app:latest

# Rebuild
docker build -t 101-app:latest .
```

---

## File Structure

```
.
├── Dockerfile           # Container blueprint
├── .dockerignore        # Files to exclude from build
├── package.json         # Dependencies
├── package-lock.json    # Locked dependency versions
├── src/
│   └── index.js         # App entry point
└── spec/                # Tests
```

---

## Next Steps

1. **Add docker-compose.yml** for multi-container setup (app + database)
2. **Create .env file** for environment variables
3. **Add health checks** to monitor container health
4. **Set up CI/CD** to auto-build on code changes
5. **Push to registry** (Docker Hub, GitLab, etc.)

---

## Resources

- [Docker Documentation](https://docs.docker.com/)
- [Node.js Docker Best Practices](https://docs.docker.com/language/nodejs/)
- [Alpine Linux Benefits](https://alpinelinux.org/)
- [Express.js Documentation](https://expressjs.com/)

---

## Support

For issues or questions, refer to `DOCKER_LEARNING_NOTES.md` for detailed explanations of Docker concepts.
