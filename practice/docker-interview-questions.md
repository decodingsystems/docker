# Docker Interview Questions

**1. What is the difference between an Image and a Container?**
An **Image** is a read-only template/blueprint. A **Container** is a running instance of an image with a thin read-write layer on top. Multiple containers can run from the same image.

**2. What is a Dockerfile?**
A text file with ordered instructions (`FROM`, `RUN`, `COPY`, `CMD`, etc.) that Docker executes sequentially to build a custom image. Each instruction creates a new layer.

**3. Explain Docker layer caching.**
Docker caches each layer. On rebuild, it reuses cached layers until it finds a layer where something changed, then re-executes from that point. Place rarely-changing instructions (e.g., installing dependencies) before frequently-changing ones (e.g., `COPY . .`) for faster builds.

**4. What is the difference between CMD and ENTRYPOINT?**
- `CMD`: Default command/arguments. Easily overridden at `docker run`.
- `ENTRYPOINT`: The container's main executable. Arguments from `CMD` or command line are passed to it. Harder to override (requires `--entrypoint` flag).

**5. What is a multi-stage build?**
Using multiple `FROM` statements in one Dockerfile. Each stage can copy files from previous stages. Lets you use a full build environment but ship only the final artifact in a small production image.

**6. What is Docker Compose?**
A tool for defining and running multi-container applications using a `docker-compose.yml` file. `docker compose up` starts all services with their networks and volumes configured.

**7. What is the difference between `docker stop` and `docker kill`?**
`docker stop` sends SIGTERM (graceful shutdown). `docker kill` sends SIGKILL (immediate). Always prefer `stop` to allow the app to clean up.

**8. What are Docker volumes?**
Persistent storage that lives outside the container filesystem. Named volumes are Docker-managed. Bind mounts map host directories into containers. Data in volumes survives container deletion.

**9. How do containers communicate in Docker Compose?**
Compose creates a shared default network. Services can reach each other using the **service name** as the DNS hostname (e.g., the `web` service can connect to `db:5432`).

**10. What is Docker Hub?**
The public default container image registry. You can pull public images and push your own images (public or private). Equivalent: AWS ECR, GitHub Container Registry.

**11. Difference between `COPY` and `ADD` in Dockerfile?**
`COPY` simply copies files from the host. `ADD` also supports URLs and auto-extracts tar archives. Best practice is to use `COPY` unless you specifically need `ADD`'s extra features.

**12. How do you reduce Docker image size?**
- Use slim/alpine base images (`python:3.12-slim` vs `python:3.12`).
- Use multi-stage builds to discard build tools.
- Combine RUN commands with `&&` to reduce layers.
- Use `.dockerignore` to exclude unnecessary files.
- Don't install unnecessary packages (`--no-install-recommends` for apt).
