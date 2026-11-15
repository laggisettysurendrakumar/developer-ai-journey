# 02 — Docker Commands and the Container Lifecycle

## Learn the lifecycle before writing Dockerfiles

When an API is containerised, a developer needs to know how to start it, inspect it, view logs, enter it for debugging, stop it, and remove it. Understanding these basic operations makes later lessons much easier.

## 1. Start a long-running container

```bash
docker run -d --name web-lab -p 8080:80 nginx:1.27
```

- `-d` starts the container in the background.
- `--name` assigns a readable name.
- `-p 8080:80` maps host port 8080 to container port 80.
- `nginx:1.27` is the image reference.

Open `http://localhost:8080` in a browser. The example tag illustrates a versioned tag; select a currently supported and reviewed Nginx tag for real projects.

## 2. Inspect status and logs

```bash
docker ps
docker ps -a
docker logs web-lab
docker logs --tail 50 web-lab
docker logs -f web-lab
docker inspect web-lab
docker stats web-lab
```

Logs are the application's stdout/stderr output. `-f` follows new log lines. `stats` shows a live resource snapshot such as CPU and memory usage.

## 3. Execute a command inside a running container

```bash
docker exec -it web-lab sh
```

This starts an additional process in the existing container. It does not rebuild the image. Some minimal images do not include Bash or even a shell; try `sh` only if it exists.

Use `exec` for inspection and debugging. Do not rely on manually changing files in a running production container: those changes are not reproducible and may disappear when the container is replaced.

## 4. Stop, start, restart and remove

```bash
docker stop web-lab
docker ps -a
docker start web-lab
docker restart web-lab
docker stop web-lab
docker rm web-lab
```

A stopped container remains available for inspection and can be started again. Removing a container deletes that container instance, not necessarily its image or named volumes.

## 5. Images are managed separately

```bash
docker image ls
docker image inspect nginx:1.27
docker image history nginx:1.27
```

Images can be shared by many containers. Removing a container does not automatically remove the image, because another container may depend on it.

## 6. Safe cleanup

For only the container created in this lesson:

```bash
docker stop web-lab
docker rm web-lab
```

Avoid broad cleanup commands until you understand their effect. Commands such as `docker system prune` can remove unused resources across your local Docker environment.

## Practice exercise

Run Nginx on host port 8080. Confirm it in a browser, inspect logs, open a shell, stop and start it, then remove it. Explain the difference between stopping and removing the container.

## Self-check

- What does `-d` change?
- What is the purpose of port mapping?
- How is `docker exec` different from `docker run`?
- Why do images and containers have separate lifecycles?
- Why should broad prune commands be used carefully?

## Official references

- Container CLI: https://docs.docker.com/reference/cli/docker/container/
- Image CLI: https://docs.docker.com/reference/cli/docker/image/
- Docker logs: https://docs.docker.com/reference/cli/docker/container/logs/
