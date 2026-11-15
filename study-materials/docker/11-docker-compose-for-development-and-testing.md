# 11 — Compose Workflows for Development and Testing

## Compose beyond starting containers

Compose can define a repeatable local development environment. It can start an API, database, cache, and test dependency with consistent network names and data volumes.

Keep the local development configuration separate from production deployment design. Compose is useful in production contexts too, but production requirements often call for a dedicated orchestrator or managed platform.

## 1. A database-backed API

A Compose file can define a database with a persistent volume and an API that connects using the service name. The exact database image, environment variables, health command, and credentials must follow the database image's current documentation.

Use a structure like this, adjusting the database service to the chosen image's official documentation:

```yaml
services:
  api:
    build: .
    environment:
      ConnectionStrings__AppDb: "Server=db,1433;Database=AppDb;User Id=appuser;Password=${DB_PASSWORD};TrustServerCertificate=True"
    ports:
      - "8080:8080"
    depends_on:
      db:
        condition: service_healthy

  db:
    image: <approved-database-image>:<pinned-version>
    environment:
      DB_PASSWORD: ${DB_PASSWORD:?Set DB_PASSWORD}
    volumes:
      - db-data:/var/lib/database-data

volumes:
  db-data:
```

This is a structural illustration, not a ready-to-run database definition. Replace the placeholder image, environment variable names, health check, and data path with values required by the selected database image. Set `DB_PASSWORD` in a protected local environment and never commit it.

## 2. Use profiles for optional services

Compose profiles allow optional services such as an admin UI or test helper to be enabled only when needed. This keeps the default developer environment smaller.

## 3. Separate tests from the API service

A test runner can be defined as a separate service that uses the same application code and dependencies. Integration tests can then target service DNS names and container ports without relying on a developer's local database installation.

## 4. Environment file discipline

Use a local `.env` file only for non-shared development values or protected secrets where permitted. Add it to `.gitignore`. Commit a harmless `.env.example` with key names and placeholder values, never real credentials.

## Practice exercise

Create a Compose environment with an API and database. Run a database migration, execute an integration test, stop the containers, and restart them. Confirm that expected data persists. Document how the setup differs from a production database.

## Self-check

- Why can a database service name be used as a hostname?
- Why is a named volume useful for the database?
- What does a Compose health condition control?
- Why must database passwords not be committed?
- Why should local Compose settings not automatically be reused in production?

## Official references

- Compose how-tos: https://docs.docker.com/compose/how-tos/
- Compose profiles: https://docs.docker.com/compose/how-tos/profiles/
- Compose environment variables: https://docs.docker.com/compose/how-tos/environment-variables/
