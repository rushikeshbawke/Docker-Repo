# 101-App

A Node.js and Express application with SQLite and MySQL persistence options, packaged with Docker.

## Quick Start

Build the image from the repository root:

```bash
docker build -t 101-app:latest .
```

Start the application and publish its port:

```bash
docker run --rm -p 3000:3000 101-app:latest
```

Open [http://localhost:3000](http://localhost:3000). The app serves its UI and exposes item routes at `/items` (GET and POST) and `/items/:id` (PUT and DELETE).

To run in the background with a named container:

```bash
docker run -d -p 3000:3000 --name 101-app 101-app:latest
```

Stop it with `docker stop 101-app`. To remove that stopped container, use `docker rm 101-app`.

## Dockerfile Overview

| Instruction | Purpose |
| --- | --- |
| `FROM node:18-alpine` | Starts from Node.js 18 on lightweight Alpine Linux. |
| `WORKDIR /app` | Sets the container's working directory. |
| `COPY package.json package-lock.json ./` | Copies dependency manifests before the application source. |
| `RUN npm install --production` | Installs runtime dependencies without development tools. |
| `COPY src src` | Copies the application source into the image. |
| `EXPOSE 3000` | Documents the port the application listens on; it does not publish the port. |
| `CMD ["node", "src/index.js"]` | Starts the application when a container runs. |

Docker builds an image as a stack of cached layers. Copying the dependency manifests and installing packages before copying `src` lets Docker reuse the dependency-install layer when only application code changes. Port publishing happens when starting a container, for example with `-p 8080:3000` to access container port 3000 at `http://localhost:8080`.

## Common Commands

### Build and run

```bash
docker build -t 101-app:v1.0 .
docker run --rm -p 8080:3000 101-app:v1.0
docker run -d -p 3000:3000 --name 101-app -e NODE_ENV=production 101-app:v1.0
```

### Containers

```bash
docker ps                         # Running containers
docker ps -a                      # Running and stopped containers
docker logs 101-app               # Show logs
docker logs -f 101-app            # Follow logs
docker exec -it 101-app sh        # Open a shell in a running container
docker stop 101-app               # Stop a container
docker rm 101-app                 # Remove a stopped container
```

### Images

```bash
docker images
docker image inspect 101-app:latest
docker history 101-app:latest
docker rmi 101-app:latest
```

## Key Concepts

| Concept | Meaning |
| --- | --- |
| Image | Read-only package containing the application and its runtime. |
| Container | A running instance of an image. |
| Layer | A result of a Dockerfile instruction that can be cached and reused. |
| Base image | The starting image specified by `FROM`. |
| Build context | Files made available to Docker during `docker build`; here it is the current directory (`.`). |

The `.dockerignore` file excludes unnecessary files from the build context, reducing what Docker needs to send and process. Keep local dependencies and other files that do not belong in the image out of the context.

## Troubleshooting

**Port 3000 is already in use:** publish a different host port while keeping container port 3000:

```bash
docker run --rm -p 8080:3000 101-app:latest
```

**The container exits or the app is unavailable:** check its output and state:

```bash
docker ps -a
docker logs <container-id>
```

**Inspect a container interactively:** start a shell instead of the normal app command:

```bash
docker run --rm -it 101-app:latest sh
```

## Project Details

- Runtime: Node.js 18 on Alpine Linux
- Web framework: Express
- Persistence dependencies: SQLite and MySQL
- Application entry point: `src/index.js`
- Container port: `3000`
- Tests: `spec/`

## Further Learning

- [Docker documentation](https://docs.docker.com/)
- [Node.js Docker guide](https://docs.docker.com/language/nodejs/)
- [Express documentation](https://expressjs.com/)

Possible next steps include adding a Compose setup for the app and database, configuring runtime environment variables, adding a container health check, and building or publishing the image in CI/CD.

