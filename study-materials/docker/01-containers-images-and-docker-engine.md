# 01 — Containers, Images and Docker Engine

## What problem does Docker solve?

A program depends on more than source code. It may need a particular runtime version, operating-system libraries, environment settings, and startup commands. A project that works on one laptop may fail on another because those dependencies differ.

Docker packages an application and its required runtime files into an image. A container runs a process from that image with isolated filesystem, process, and network settings. This makes development, testing, and deployment more repeatable.

A container is not a full virtual machine. Containers share the host operating-system kernel, while virtual machines include their own guest operating system.

## 1. The important building blocks

| Term | Plain-English meaning | Why it matters |
|---|---|---|
| Docker CLI | Command-line client | Sends build/run instructions |
| Docker Engine | Background service that manages containers, images and networks | Performs the work requested by the CLI |
| Image | Packaged, read-only template | Can be reused to create containers |
| Container | An instance created from an image | Runs the application process |
| Dockerfile | Text instructions to build an image | Makes the build repeatable |
| Registry | Service that stores and distributes images | Shares builds between machines and environments |
| Volume | Docker-managed persistent storage | Keeps data independent of a container's writable layer |
| Network | Virtual connectivity for containers | Lets services communicate |
| Layer | A reusable part of an image | Supports caching and efficient storage |

## 2. Install and verify Docker

Install Docker Desktop on supported Windows or macOS systems, or Docker Engine on a supported Linux distribution. Follow the official installation guide for your operating system. On Windows, check whether your setup uses WSL 2 and whether the Docker daemon is running.

Run:

```bash
docker version
docker info
docker run hello-world
```

- `docker version` displays client and server versions. A missing server response can mean the engine is not running or reachable.
- `docker info` shows engine configuration, storage driver, runtime, and other diagnostics.
- `docker run hello-world` pulls a demonstration image if needed, creates a container, runs its process, and prints a success message.

## 3. Understand the container lifecycle

```bash
docker run --name hello-lab hello-world
docker ps
docker ps -a
docker inspect hello-lab
docker rm hello-lab
docker image ls
```

- `docker ps` lists running containers.
- `docker ps -a` also lists stopped containers.
- `docker inspect` returns detailed configuration and runtime state.
- `docker rm` removes the stopped container.
- `docker image ls` lists local images.

A short-lived container such as `hello-world` exits after printing its message. This is expected; not every container runs forever.

## 4. Run an interactive Linux container

```bash
docker run --rm -it alpine:3.21 sh
```

- `--rm` removes the container when its process exits.
- `-i` keeps standard input open.
- `-t` allocates a terminal.
- `sh` starts a shell inside the container.

Try `pwd`, `ls`, `cat /etc/os-release`, and `exit`. These commands execute inside the container, not directly on the host. Use a currently supported, reviewed image tag for longer-lived projects.

## 5. Docker and the host

The CLI can run on a developer machine while the engine manages containers. Docker Desktop bundles a managed environment for the engine. On Linux, the engine commonly runs as a service.

Remember:
- The host provides the kernel.
- The image contains the application filesystem and required user-space dependencies.
- The container process runs with isolation and resource controls.
- The engine creates and manages the container.

## Practice exercise

Run `hello-world`, inspect its stopped container, then run Alpine interactively. Write down which commands execute on the host and which execute inside the container. Remove only the containers you created for the exercise.

## Self-check

- What is the difference between Docker CLI and Docker Engine?
- Why is a container not a full virtual machine?
- What is the difference between an image and a container?
- Why might `docker ps` show no containers even though an image exists?
- What does `--rm` do?

## Official references

- Docker overview: https://docs.docker.com/get-started/docker-overview/
- Install Docker: https://docs.docker.com/get-started/get-docker/
- Docker run reference: https://docs.docker.com/reference/cli/docker/container/run/
