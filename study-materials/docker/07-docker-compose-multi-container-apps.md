# 07 — Docker Compose and Multi-Container Applications

## Why Compose exists

An application often needs several services: an API, database, cache, search engine, and worker. Starting each one with a long `docker run` command becomes difficult to reproduce and maintain.

Docker Compose describes the multi-container application in a YAML file. It helps developers define services, networks, volumes, environment settings, and dependencies together.

## 1. Example application layout

```text
developer-assistant/
├── api/
│   ├── Dockerfile
│   └── ...
└── compose.yaml
```

Example Compose file:

```yaml
services:
  api:
    build:
      context: ./api
    ports:
      - "8000:8000"
    environment:
      APP_ENV: development
      VECTOR_URL: http://vector-store:6333
    depends_on:
      vector-store:
        condition: service_healthy

  vector-store:
    image: qdrant/qdrant:v1.15.0
    volumes:
      - vector-data:/qdrant/storage
    healthcheck:
      test: ["CMD", "bash", "-c", "echo > /dev/tcp/127.0.0.1/6333"]
      interval: 10s
      timeout: 3s
      retries: 5

volumes:
  vector-data:
```

This is a learning illustration. Check the selected image version, health-check tooling, and service's recommended health endpoint before using it. Minimal images may not contain Bash or networking utilities, so the health check may need to be changed.

## 2. Understand the YAML structure

- `services` lists application components.
- `build` tells Compose how to build an image from a Dockerfile.
- `image` selects an existing image.
- `ports` publishes a container port on the host.
- `environment` provides runtime settings.
- `depends_on` expresses service dependencies and can wait for health when configured with supported conditions.
- `volumes` provides persistent storage.

Compose creates a default network for the application. Services can normally reach each other using their service names, such as `vector-store`.

## 3. Common Compose commands

```bash
docker compose config
docker compose up --build -d
docker compose ps
docker compose logs -f api
docker compose exec api sh
docker compose stop
docker compose start
docker compose down
```

- `config` validates and renders the effective Compose configuration.
- `up` creates/recreates services and starts them.
- `ps` shows service status.
- `logs -f` follows service logs.
- `exec` runs a command in a running service container.
- `stop` stops services without removing them.
- `start` starts stopped services.
- `down` removes the Compose containers and network. Named volumes are retained by default unless explicitly requested for removal.

## 4. Startup ordering versus readiness

A dependency can be started but not yet ready to serve requests. The API should have sensible connection timeouts and retry behaviour. A Compose health check can help orchestrate local startup, but application-level resilience is still important.

## 5. Configuration and secrets

Keep non-secret defaults in Compose. Use local environment files only when protected and excluded from source control. Never commit a developer's real API keys or database passwords. Production secrets should be injected by an approved deployment platform or secret manager.

## Practice exercise

Create a Compose project with an API and a local data service. Add a named volume, a health check, and service-to-service connection by service name. Stop and restart the project, then verify persistent data and inspect logs.

## Self-check

- Why use Compose instead of several long `docker run` commands?
- How do services find one another on the default network?
- What is the purpose of a health check?
- What does `docker compose down` remove by default?
- Why does the API still need retry and timeout handling?

## Official references

- Docker Compose: https://docs.docker.com/compose/
- Compose file reference: https://docs.docker.com/reference/compose-file/
- Compose startup order: https://docs.docker.com/compose/how-tos/startup-order/
