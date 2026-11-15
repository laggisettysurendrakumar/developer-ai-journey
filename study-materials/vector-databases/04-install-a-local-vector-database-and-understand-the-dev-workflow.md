# 04 — Install a Local Vector Database and Understand the Development Workflow

## Goal

I will run a vector database locally and understand the developer workflow before connecting real embeddings or a cloud service. Qdrant is used here as an example; the concepts transfer to other vector stores.

## 1. Local development with Docker

A common local Qdrant development setup uses the official container image. Check the official documentation for the current image and configuration.

```bash
docker run --name qdrant-learning \
  -p 127.0.0.1:6333:6333 \
  -p 127.0.0.1:6334:6334 \
  -v qdrant_learning_data:/qdrant/storage \
  qdrant/qdrant
```

What each option means:
- `--name`: gives the container a readable name.
- `-p 127.0.0.1:6333:6333`: exposes the HTTP API only on the local machine.
- `-p 127.0.0.1:6334:6334`: exposes the gRPC API only locally.
- `-v`: stores database files in a Docker-managed volume so they survive container replacement.

Do not expose an unauthenticated learning database publicly. This local command is for a disposable development environment, not production.

## 2. Verify the service

In another terminal:

```bash
curl http://127.0.0.1:6333/
```

A response indicates that the local HTTP service is reachable. The exact response can vary by version.

## 3. Stop and remove the container

```bash
docker stop qdrant-learning
docker rm qdrant-learning
```

The named Docker volume remains. To inspect volumes:

```bash
docker volume ls
```

Do not delete a volume until you are sure it contains only disposable learning data. Volume deletion permanently removes its stored database data.

## 4. Why persistence matters

A container can be removed and recreated. A persistent volume stores database data outside the container's writable layer. Production storage needs additional design: backups, encryption, access control, capacity, availability, monitoring, and restore tests.

## 5. Alternative local option

For learning PostgreSQL vector search, use PostgreSQL with the pgvector extension. It allows vectors to be stored alongside relational data. The extension and PostgreSQL versions must be compatible; follow the official installation guide for your operating system or container setup.

## Practice exercise

Start Qdrant locally, verify the HTTP endpoint, stop the container, and start it again using the same volume. Explain which data survives container replacement and why the port is bound to localhost.

## Self-check

1. Why use a persistent volume?
2. What does binding to 127.0.0.1 achieve?
3. What is the difference between stopping and removing a container?
4. Why is a local development database not automatically production-ready?
5. What should you check before deleting a database volume?

## Further reading

- [Qdrant quickstart](https://qdrant.tech/documentation/quickstart/)
- [Qdrant Docker deployment](https://qdrant.tech/documentation/guides/installation/)
- [pgvector](https://github.com/pgvector/pgvector)
