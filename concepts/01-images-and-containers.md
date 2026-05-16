# Docker — Images and Containers

## What is Docker?
**Docker** is an open-source platform that packages applications and their dependencies into **containers** — lightweight, portable, self-sufficient units that run consistently across any environment. The classic developer problem it solves: *"It works on my machine."*

Before Docker, shipping an application meant setting up the exact same OS, runtime versions, libraries, and environment variables on every server. Containers bundle all of that into a single artifact.

## Virtual Machines vs Containers

| Aspect | Virtual Machine | Container |
|--------|----------------|-----------|
| Isolation | Full OS per VM | Shares host OS kernel |
| Boot time | Minutes | Milliseconds |
| Image size | Gigabytes | Megabytes |
| Overhead | High (hypervisor) | Very low |

Containers are not VMs. They share the host kernel but isolate processes using Linux **namespaces** (what it can see) and **cgroups** (CPU/memory limits).

## Core Concepts

### Images
A Docker **Image** is a read-only blueprint built in **layers**. Each Dockerfile instruction creates one layer. Layers are cached for fast rebuilds. Images are stored in a **Registry** (Docker Hub, ECR).

```bash
docker pull nginx:latest         # Download image
docker images                    # List local images
docker image inspect nginx       # Detailed metadata
docker rmi nginx                 # Delete image
```

### Containers
A **Container** is a running instance of an image with a thin read-write layer on top. Multiple containers can run from the same image independently.

```bash
docker run -d -p 8080:80 --name web nginx  # Run detached, port mapped
docker ps                                   # List running containers
docker ps -a                                # All containers
docker stop web && docker rm web            # Stop and remove
docker logs web                             # Container output
docker exec -it web bash                    # Shell inside container
```

### Key Flags
| Flag | Meaning |
|------|---------|
| `-d` | Detached (background) |
| `-p host:container` | Port mapping |
| `--name` | Named container |
| `-e KEY=VALUE` | Environment variable |
| `-v host:container` | Mount volume |
| `--rm` | Auto-delete when stopped |

## Docker Architecture
- **Docker Client:** CLI sends commands via REST API.
- **Docker Daemon (`dockerd`):** Background service managing images, containers, networks, volumes.
- **Docker Registry:** Stores and distributes images (Docker Hub is the default).
