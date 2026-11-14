# Docker for GenAI and Agentic Applications — Beginner Course

I use Docker to package an application and its runtime dependencies so the same build can run consistently on a developer machine, in CI, and in deployment.

## Prerequisites and learning order

Know basic Linux commands, Python scripts, environment variables, and HTTP APIs. Learn in this order:

1. Understand images, containers, registries, layers, and Docker Engine.
2. Run and inspect an existing container.
3. Write a Dockerfile for a small Python API.
4. Build, tag, and run a local image.
5. Add ports, environment variables, logs, and health checks.
6. Use volumes for persistent data and networks for service communication.
7. Use Compose for an API plus database/vector store.
8. Improve image size, security, reproducibility, and CI builds.
9. Containerise the Developer Knowledge Assistant.

## 1. Docker in plain English

Think of an image as a packaged recipe and a container as a running instance made from that recipe. An image includes application files, a runtime, libraries, and startup instructions. A container is an isolated process environment that shares the host kernel.

| Term | Simple meaning |
|---|---|
| Docker Engine | Software that builds and runs containers |
| Image | Read-only template used to create containers |
| Container | Running or stopped instance of an image |
| Dockerfile | Instructions for building an image |
| Registry | Place to store and share images |
| Volume | Persistent storage managed outside a container's writable layer |
| Network | Virtual connectivity between containers and other endpoints |
| Layer | Reusable image build content |

Containers are not full virtual machines.

## 2. Run and inspect a container

```bash
docker run --name hello-lab hello-world
docker ps -a
docker inspect hello-lab
docker rm hello-lab
```

**What it does:** Downloads the demonstration image if needed, creates and runs a container, lists containers, inspects it, and removes the stopped lab container.

**Why useful?** It confirms Docker works and introduces the container lifecycle.

**Use case:** Reproduce a service runtime without installing all dependencies directly on the host.

## 3. Dockerfile for a Python API

Assume the project contains `api.py` with a FastAPI object named `app` and a reviewed `requirements.txt`.

```dockerfile
FROM python:3.12-slim

WORKDIR /app
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd --create-home appuser
USER appuser

EXPOSE 8000
CMD ["uvicorn", "api:app", "--host", "0.0.0.0", "--port", "8000"]
```

- `FROM` selects a base image.
- `WORKDIR` sets the working directory inside the image.
- `COPY` adds files to the image.
- `RUN` executes a build-time command.
- `USER` selects the runtime user.
- `EXPOSE` documents the port the app listens on.
- `CMD` defines the default process when the container starts.

**Why useful?** The container has a known Python runtime and dependency set. Running as non-root reduces privilege.

For production, use a reviewed base image, pinned dependencies, vulnerability scanning, and a multi-stage build where appropriate.

## 4. Build and run

```bash
docker build -t developer-assistant:dev .
docker run --rm --name assistant-api -p 8000:8000 developer-assistant:dev
```

`-t` names the image. `-p 8000:8000` maps host port 8000 to container port 8000. `--rm` removes the container after it stops.

In another terminal:

```bash
curl http://localhost:8000/health
docker logs assistant-api
docker stop assistant-api
```

Create the `/health` route in the application or change the test to a route that exists.

## 5. Environment variables and secrets

```bash
docker run --rm -e APP_ENV=development -p 8000:8000 developer-assistant:dev
```

Environment variables provide runtime configuration. They are suitable for non-secret settings. Do not bake API keys into Dockerfiles, images, build arguments, or source control. For local development use a protected env file excluded from Git; in production use secret injection or workload identity.

## 6. Volumes and persistence

A container's writable layer is not the right place for important data that must survive container replacement.

```bash
docker volume create assistant-data
docker run --rm -v assistant-data:/data alpine sh -c "echo sample > /data/note.txt"
docker run --rm -v assistant-data:/data alpine cat /data/note.txt
```

**Use case:** Persist a local vector database or uploaded documents across container restarts. Production data needs backups, access control, and restore tests.

## 7. Docker Compose

Compose describes related containers in one YAML file.

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      APP_ENV: development
    depends_on:
      - vector-store

  vector-store:
    image: qdrant/qdrant:v1.15.0
    volumes:
      - vector-data:/qdrant/storage

volumes:
  vector-data:
```

Run with `docker compose up --build`; stop with `docker compose down`. Pin reviewed image versions for reproducible deployments. `depends_on` alone does not prove that a dependency is ready; add health checks and retry logic.

## 8. Useful commands

| Command | Why I use it |
|---|---|
| `docker images` | List local images |
| `docker ps -a` | List running and stopped containers |
| `docker logs <container>` | Inspect application output |
| `docker exec -it <container> sh` | Open a shell for debugging, when available |
| `docker inspect <container>` | Inspect container configuration/state |
| `docker stats` | View runtime resource usage |
| `docker compose ps` | Check Compose service state |
| `docker compose logs -f` | Follow Compose service logs |

Use `exec` only for debugging; do not manually repair a production container as a substitute for rebuilding and deploying a reviewed image.

## 9. Image hygiene and security

- Add `.dockerignore` for `.venv`, Git metadata, local `.env`, caches, and test artifacts.
- Use a small trusted base image and pin versions deliberately.
- Run as non-root and avoid privileged mode.
- Do not copy secrets into images.
- Scan images and dependencies.
- Set CPU/memory constraints at the runtime platform.
- Expose only required ports.
- Keep application logs on stdout/stderr and centralise them.
- Rebuild and redeploy when base-image security updates are needed.

## 10. Practice project

Containerise the Python Developer Knowledge Assistant:
1. Add FastAPI health/readiness endpoints.
2. Create a reviewed requirements file.
3. Add Dockerfile and `.dockerignore`.
4. Run as non-root.
5. Add Compose for the API and a local vector store.
6. Persist vector data in a volume.
7. Test container restart and data persistence.
8. Document safe model API key injection.

## Self-check

1. What is the difference between an image and a container?
2. Why should important data live in a volume?
3. Why is `latest` a weak production pin?
4. Why should an application run as non-root?
5. Why does `depends_on` not necessarily mean a database is ready?

## References

- Docker getting started: https://docs.docker.com/get-started/
- Dockerfile reference: https://docs.docker.com/reference/dockerfile/
- Docker build best practices: https://docs.docker.com/build/building/best-practices/
- Docker Compose: https://docs.docker.com/compose/
