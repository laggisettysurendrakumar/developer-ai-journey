# 04 — Multi-Stage Builds and ASP.NET Core Containers

## Why multi-stage builds are useful

A build often needs compilers, SDKs, and package managers. The final application needs only the published output and runtime dependencies. A multi-stage Dockerfile separates these responsibilities so build tools do not need to remain in the final runtime image.

This pattern is especially useful for .NET applications, where the SDK image is larger than the ASP.NET Core runtime image.

## 1. Example ASP.NET Core multi-stage Dockerfile

Assume the repository contains `src/DeveloperApi/DeveloperApi.csproj` and the application targets .NET 8. Adjust paths and image tags to match the project and supported platform image.

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

COPY ["src/DeveloperApi/DeveloperApi.csproj", "src/DeveloperApi/"]
RUN dotnet restore "src/DeveloperApi/DeveloperApi.csproj"

COPY . .
WORKDIR /src/src/DeveloperApi
RUN dotnet publish "DeveloperApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

COPY --from=build /app/publish .
ENV ASPNETCORE_HTTP_PORTS=8080
EXPOSE 8080

USER $APP_UID
ENTRYPOINT ["dotnet", "DeveloperApi.dll"]
```

The example uses the .NET container image's non-root user convention; check the exact image and runtime version documentation before relying on environment-specific defaults.

## 2. Understand the stages

- The build stage uses the .NET SDK to restore and publish the application.
- The runtime stage uses the ASP.NET Core runtime image.
- `COPY --from=build` transfers only the published files into the final image.
- `ENTRYPOINT` starts the API process.

The Docker build context and project paths must match your actual repository layout. For a multi-project solution, copy all project files required for restore before running `dotnet restore`.

## 3. Build and run

```bash
docker build -t developer-api:dev .
docker run --rm --name developer-api -p 8080:8080 developer-api:dev
```

The API must listen on the port configured inside the container. The host maps port 8080 to that container port.

## 4. Environment configuration

ASP.NET Core reads configuration from standard providers such as JSON files and environment variables. For hierarchical settings, environment variables commonly use double underscores:

```bash
docker run --rm -p 8080:8080 \
  -e ASPNETCORE_ENVIRONMENT=Development \
  -e Logging__LogLevel__Default=Information \
  developer-api:dev
```

Do not use a Development environment in a production deployment unless you have a specific, reviewed reason. Supply environment-specific settings via your deployment platform.

## 5. Health checks

A health endpoint should report meaningful application readiness. A process being alive does not prove dependencies are accessible or that the service can handle requests.

Implement ASP.NET Core health checks appropriate to the application. Configure container or platform probes to use the endpoint. Avoid making a liveness check fail merely because a temporary downstream dependency is unavailable; readiness and liveness checks have different purposes.

## Practice exercise

Containerise a small ASP.NET Core API using a multi-stage Dockerfile. Compare the final image to a single-stage SDK image. Verify the application listens on the expected port, runs as a non-root user, reads environment-specific configuration, and responds on its health endpoint.

## Self-check

- Why use an SDK image during build but a runtime image for the final stage?
- What does `COPY --from=build` do?
- Why must the port mapping match the port the app listens on?
- What is the difference between readiness and liveness?
- Why is a production environment setting important?

## Official references

- .NET container images: https://learn.microsoft.com/dotnet/core/docker/
- .NET 8 container guidance: https://learn.microsoft.com/dotnet/core/whats-new/dotnet-8/containers
- Docker multi-stage builds: https://docs.docker.com/build/building/multi-stage/
