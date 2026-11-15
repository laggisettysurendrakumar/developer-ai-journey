# 03 — Dockerfiles, Build Context and Image Layers

## What a Dockerfile does

A Dockerfile is a set of instructions used to construct an image. It describes the base filesystem, application files, build steps, environment defaults, and the command to run when a container starts.

Writing a Dockerfile well matters because it affects build speed, image size, reproducibility, debugging, and security.

## 1. The build context

When you run:

```bash
docker build -t sample-api:dev .
```

The final dot means the current directory is sent as the build context. The Dockerfile can copy only files that are available in that context. A large context can make builds slower and may accidentally include secrets or local files.

Create a `.dockerignore` file:

```text
.git
.vscode
.idea
**/bin
**/obj
**/__pycache__
.venv
.env
.env.*
!.env.example
node_modules
dist
coverage
```

Review these patterns for your actual project. Do not exclude files required to build the application.

## 2. Common Dockerfile instructions

| Instruction | What it means |
|---|---|
| `FROM` | Selects a base image |
| `WORKDIR` | Sets the working directory for later instructions |
| `COPY` | Copies files from build context into image |
| `RUN` | Executes a command during image build |
| `ENV` | Sets environment variables in the image/container defaults |
| `ARG` | Defines a build-time argument |
| `EXPOSE` | Documents the intended listening port |
| `USER` | Selects the runtime user |
| `ENTRYPOINT` | Defines the executable |
| `CMD` | Provides default command or arguments |

`EXPOSE` does not publish a host port. Port publishing is configured when running a container or in the orchestrator.

## 3. Example Dockerfile for a FastAPI API

Project layout:

```text
developer-assistant/
├── api.py
├── requirements.txt
├── Dockerfile
└── .dockerignore
```

Dockerfile:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY api.py .

RUN useradd --create-home appuser
USER appuser

EXPOSE 8000
CMD ["uvicorn", "api:app", "--host", "0.0.0.0", "--port", "8000"]
```

This assumes `api.py` defines a FastAPI instance called `app` and `requirements.txt` includes FastAPI and Uvicorn. Pin and review dependencies for a real project.

## 4. Layers and build cache

Many Dockerfile instructions create image layers. Docker can reuse a previous layer when its instruction and relevant inputs have not changed.

That is why dependency files are often copied and installed before application source code. If only `api.py` changes, the dependency installation layer may still be reused.

Avoid combining unrelated steps just to reduce the number of lines. Prioritise readability and cache behaviour, then optimise based on measured build times.

## 5. Build, inspect and run

```bash
docker build -t developer-assistant:dev .
docker image ls
docker image history developer-assistant:dev
docker run --rm -p 8000:8000 developer-assistant:dev
```

Then call an endpoint that your API actually defines, for example `curl http://localhost:8000/health`.

## 6. Build arguments versus runtime environment

Build arguments are available while building an image. Environment variables are available to the running container. Neither should be used to smuggle secrets into image layers or build logs.

Keep deployment-specific configuration outside the image. Supply it when the container runs or through the hosting platform's configuration mechanism.

## Practice exercise

Create a Dockerfile for a small API. Add a `.dockerignore`, build the image, inspect its layers, modify one application file, and rebuild. Observe which steps are cached. Confirm that a local `.env` file is not included in the build context.

## Self-check

- What does the dot mean in `docker build ... .`?
- Why should dependency manifests be copied before frequently changing source files?
- Does `EXPOSE` publish a host port?
- What is the difference between `RUN` and `CMD`?
- Why must secrets not be copied into image layers?

## Official references

- Dockerfile reference: https://docs.docker.com/reference/dockerfile/
- Build context: https://docs.docker.com/build/concepts/context/
- Build cache: https://docs.docker.com/build/cache/
- Build best practices: https://docs.docker.com/build/building/best-practices/
