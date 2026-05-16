# Dockerfile — Building Custom Images

## What is a Dockerfile?
A **Dockerfile** is a text file with ordered instructions that Docker executes to build a custom image. Every instruction creates a new layer.

## Dockerfile Instructions

| Instruction | Purpose |
|-------------|---------|
| `FROM` | Base image (every Dockerfile starts here) |
| `WORKDIR` | Set working directory for subsequent instructions |
| `COPY` | Copy files from host into the image |
| `ADD` | Like COPY, also handles URLs and auto-extracts tarballs |
| `RUN` | Execute a shell command during build |
| `ENV` | Set environment variable |
| `EXPOSE` | Document which port the container listens on |
| `CMD` | Default command when container starts (overridable) |
| `ENTRYPOINT` | Container's main executable (harder to override) |
| `ARG` | Build-time variable (not available at runtime) |

## Example: Python Flask Dockerfile
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV FLASK_ENV=production
EXPOSE 5000
CMD ["python", "app.py"]
```

## Layer Caching Optimization
Docker caches each layer. A cache miss on any layer re-executes all subsequent layers.

**Bad order (invalidates cache on every code change):**
```dockerfile
COPY . .                            # Code changes → cache miss
RUN pip install -r requirements.txt  # Reinstalls every time!
```

**Good order (dependencies cached independently):**
```dockerfile
COPY requirements.txt .             # Cache miss only if requirements.txt changes
RUN pip install -r requirements.txt # Cached!
COPY . .                            # Code changes only affect this layer
```

## Multi-Stage Builds (Smaller Production Images)
```dockerfile
# Stage 1: Build environment
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production — only ship compiled output
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
```
The final image is tiny `nginx:alpine` (~25MB) vs the full Node.js builder (~1GB).

## CMD vs ENTRYPOINT
```dockerfile
# CMD — default args, easily overridden
CMD ["python", "app.py"]
# docker run myimage → runs python app.py
# docker run myimage python other.py → overrides CMD

# ENTRYPOINT — locked-in executable
ENTRYPOINT ["python"]
CMD ["app.py"]
# docker run myimage → runs python app.py
# docker run myimage other.py → runs python other.py (still python!)
```
