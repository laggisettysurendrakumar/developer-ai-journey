# 06 — Volumes, Bind Mounts and Persistent Data

## Why container filesystems need care

A container's writable layer belongs to that container instance. When the container is removed and recreated, data written only inside that layer is normally lost.

Databases, uploaded files, and indexes need storage that is managed independently of the disposable container lifecycle.

## 1. Named volumes

```bash
docker volume create learning-data
docker volume ls
docker run --rm -v learning-data:/data alpine:3.21 sh -c "echo saved > /data/note.txt"
docker run --rm -v learning-data:/data alpine:3.21 cat /data/note.txt
```

The second container reads the file written by the first. The container instances are different, but the named volume persists.

## 2. Bind mounts

A bind mount connects a specific host path to a path inside the container. It is useful for local development when you want source-code changes to appear without rebuilding the image.

```bash
docker run --rm -v "$(pwd)/src:/app/src" my-api:dev
```

Host path syntax differs across shells and operating systems. Bind mounts can expose host files to the container, so mount only paths the application actually needs.

## 3. Volume versus bind mount

| Type | Managed by | Common use |
|---|---|---|
| Named volume | Docker | Local database data, persistent service state |
| Bind mount | Developer/host | Source code, configuration files, selected host folders |
| tmpfs mount | Host memory | Temporary sensitive or transient data where appropriate |

Do not store production database data in an arbitrary container writable layer. For cloud deployments, evaluate managed database/storage services rather than assuming a local Docker volume is a production data platform.

## 4. Inspect and remove volumes

```bash
docker volume inspect learning-data
docker volume ls
docker volume rm learning-data
```

Removing a volume deletes its stored data. Make sure no container or workflow still needs it. Avoid broad prune commands when learning; inspect exactly what will be removed.

## 5. Persistence is not backup

A named volume helps data survive container replacement on the same Docker host. It is not automatically a backup, high-availability solution, or disaster-recovery plan.

For important data, define backup frequency and retention, access controls, encryption, restore testing, recovery objectives, and storage monitoring.

## Practice exercise

Write a file to a named volume, remove the container, and read the file from another container. Then compare this with writing to the container's own filesystem. Record what survives each lifecycle operation.

## Self-check

- Why does a container writable layer not suit important data?
- What is the difference between a volume and a bind mount?
- Does a named volume automatically create backups?
- Why can deleting a volume be destructive?
- When might a managed cloud storage service be a better fit?

## Official references

- Docker storage: https://docs.docker.com/engine/storage/
- Volumes: https://docs.docker.com/engine/storage/volumes/
- Bind mounts: https://docs.docker.com/engine/storage/bind-mounts/
