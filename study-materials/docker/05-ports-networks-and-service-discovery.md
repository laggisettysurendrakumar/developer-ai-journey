# 05 — Ports, Networks and Service Discovery

## Why container networking matters

Real applications usually contain more than one process. An API may call Redis, SQL Server, a vector database, or a background worker. Containers need a way to reach each other without hardcoding unstable IP addresses.

Docker networks provide isolated virtual networks and built-in name resolution for containers attached to the same user-defined network.

## 1. Host ports and container ports

A container process listens on a port inside its own network namespace. Port publishing maps a host port to that container port.

```bash
docker run -d --name web-lab -p 8080:80 nginx:1.27
```

The mapping means host port 8080 forwards to container port 80. The application inside the container must actually listen on port 80.

## 2. Create a user-defined network

```bash
docker network create app-lab
docker network ls
docker network inspect app-lab
```

Start a service:

```bash
docker run -d --name web-one --network app-lab nginx:1.27
```

Start a temporary client to test name resolution and HTTP connectivity:

```bash
docker run --rm --network app-lab curlimages/curl:8.10.1 http://web-one
```

The client addresses `web-one` by its container name rather than needing to know its IP address.

## 3. Keep databases off unnecessary public ports

A database used only by the API usually does not need a host-published port. Attach both services to a network and let the API connect to the database by container/service name and internal port.

Do not assume an unpublished port makes a database fully secure. Use authentication, least privilege, network segmentation, encryption where appropriate, and safe configuration.

## 4. Inspect and troubleshoot

```bash
docker network ls
docker network inspect app-lab
docker inspect web-one
docker logs web-one
```

Check whether both containers are attached to the same network, whether the process listens on the expected port, whether the client uses the right hostname, and whether application retry behaviour is needed.

## 5. Clean up only lab resources

```bash
docker rm -f web-one
docker network rm app-lab
```

This force-removes the named lab container and removes the lab network. Do not reuse these commands with production container names or shared networks.

## Practice exercise

Run an API and a mock dependency on one user-defined network. Verify service-to-service communication by name. Then detach one service or use an incorrect port and observe the failure. Record how you diagnosed it.

## Self-check

- What is the difference between a container port and host port?
- Why are service names better than fixed container IPs?
- Why should a database port not be published to the host unless needed?
- What does a user-defined bridge network provide?
- What evidence helps diagnose a connection failure?

## Official references

- Docker networking: https://docs.docker.com/engine/network/
- Bridge network driver: https://docs.docker.com/engine/network/drivers/bridge/
- Port publishing: https://docs.docker.com/get-started/docker-concepts/running-containers/publishing-ports/
