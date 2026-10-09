# Docker Learning Guide - 101-App

## What is a Dockerfile?
A Dockerfile is a text file containing instructions to build a Docker image. Think of it as a blueprint that defines how your application and its environment should be packaged.

---

## Breaking Down Your Dockerfile

### 1. **FROM node:18-alpine**
- `FROM`: Specifies the base image to build upon
- `node:18-alpine`: Uses Node.js version 18 on Alpine Linux (a lightweight, minimal Linux distro ~5MB)
- **Why Alpine?**: Smaller image size = faster downloads and less storage
- **Why Node 18?**: Your app needs Node.js runtime to execute JavaScript

### 2. **WORKDIR /app**
- Sets the working directory inside the container to `/app`
- All subsequent commands run from this directory
- Think of it like `cd /app` on your computer
- Creates the directory if it doesn't exist

### 3. **COPY package.json package-lock.json ./**
- `COPY`: Copies files from your local machine (build context) into the container
- `package.json` & `package-lock.json`: Your app's dependency definitions
- `./`: Destination = current working directory (which is `/app` from WORKDIR)
- **Why copy these first?** Docker caches layers. If source code changes, dependencies don't re-install. If dependencies change, Docker knows to re-run `npm install`.

### 4. **RUN npm install --production**
- `RUN`: Executes a command during image build (not when container runs)
- `npm install --production`: Installs only production dependencies (no dev tools)
- Creates `node_modules/` directory inside the image

### 5. **COPY src src**
- Copies your application code (`src/` directory) into the container
- Placed after `npm install` so code changes don't trigger dependency reinstalls

### 6. **EXPOSE 3000**
- Documents that the app listens on port 3000
- **Does NOT actually publish the port** — this is informational for anyone using the image
- Publishing happens at runtime with `docker run -p 8080:3000`

### 7. **CMD ["node", "src/index.js"]**
- `CMD`: Default command to run when the container starts
- Equivalent to running `node src/index.js`
- Can be overridden when running: `docker run myimage node other-file.js`

---

## Layer Caching (Optimization Concept)

Your Dockerfile creates layers (like stacked images):
```
1. node:18-alpine           (base OS + Node)
2. WORKDIR                  (empty layer, mostly metadata)
3. COPY package.json        (dependency files)
4. RUN npm install          (node_modules folder)
5. COPY src                 (source code)
6. EXPOSE                   (metadata)
7. CMD                      (metadata)
```

**Docker's cache strategy:** If layer 3 hasn't changed, layers 3-7 are reused. But if `src/` changes, layers 5-7 rebuild.

---

## Building Your Image

```bash
docker build -t 101-app:latest .
```

**Breakdown:**
- `-t 101-app:latest`: Tags the image with name `101-app` and version `latest`
- `.`: Build context = current directory (Docker reads files relative to this)

---

## Running Your Container

```bash
docker run -p 3000:3000 101-app:latest
```

**Breakdown:**
- `-p 3000:3000`: Port mapping = `host-port:container-port`
- Container's port 3000 (from EXPOSE) maps to your machine's port 3000
- Access app at `http://localhost:3000`

---

## Key Docker Concepts

| Concept | Definition |
|---------|-----------|
| **Image** | Blueprint (like a class in programming) — read-only, defines everything needed |
| **Container** | Running instance of an image (like an object instantiated from a class) |
| **Layer** | Intermediate image from each Dockerfile instruction; enables caching |
| **Base Image** | Starting point (FROM node:18-alpine here) |
| **Build Context** | Files Docker can access during build (everything in current dir) |

---

## Why .dockerignore?

Your `.dockerignore` file prevents unnecessary files from being sent to the build context:
```
node_modules      # Don't copy; will reinstall anyway via npm install
Dockerfile        # Self-referential; not needed in image
```

This makes builds faster by reducing context size.

---

## Common Docker Commands

```bash
# Build an image
docker build -t image-name:tag .

# Run a container
docker run -p host-port:container-port image-name:tag

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# View container logs
docker logs container-id

# Stop a container
docker stop container-id

# Remove an image
docker rmi image-name:tag
```

---

## Next Steps to Learn

1. **Try running your app:** `docker run -p 3000:3000 101-app:latest`
2. **Inspect the image:** `docker image inspect 101-app:latest`
3. **Learn docker-compose:** Orchestrate multiple containers (database + app)
4. **Multi-stage builds:** Reduce final image size by separating build/runtime stages
5. **Environment variables:** Pass config at runtime instead of hardcoding

---

## Your App Stack

- **Runtime:** Node.js 18 on Alpine Linux
- **Framework:** Express.js (web server)
- **Databases:** MySQL2 and SQLite3 support
- **Entry point:** `src/index.js`

This is a full-stack web app template ready for containerization!
