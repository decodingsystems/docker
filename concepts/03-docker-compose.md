# Docker Compose — Multi-Container Applications

## What is Docker Compose?
**Docker Compose** is a tool for defining and running multi-container applications using a single `docker-compose.yml` file. Start your entire stack — app + database + cache — with one command: `docker compose up`.

## docker-compose.yml Structure

```yaml
version: "3.9"

services:
  web:                          # Service name (also the DNS hostname)
    build: .                    # Build from local Dockerfile
    ports:
      - "5000:5000"             # host:container
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      - db                      # Start db before web
    volumes:
      - .:/app                  # Bind mount for dev hot-reload

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data   # Named volume (persistent)

  redis:
    image: redis:7-alpine

volumes:
  postgres_data:                # Declare named volumes
```

## Key Commands
```bash
docker compose up              # Start all services
docker compose up -d           # Start detached
docker compose up --build      # Force rebuild before start
docker compose down            # Stop and remove containers
docker compose down -v         # Also remove volumes (DELETES DATA)
docker compose ps              # List services
docker compose logs web        # Logs for specific service
docker compose exec web bash   # Shell into service
docker compose restart web     # Restart one service
```

## Networking in Compose
All services share a single default network. Services reach each other using the **service name as hostname**:

```python
# Web service connecting to 'db' service
conn = psycopg.connect("host=db port=5432 dbname=mydb user=user password=pass")
# 'db' resolves to the db container's IP automatically
```

## Volumes: Types
- **Named volumes** (`postgres_data:/var/...`): Docker-managed, persist after container deletion. Ideal for databases.
- **Bind mounts** (`.:/app`): Maps host directory into container. Changes reflect immediately on both sides. Ideal for development.

## Dependency and Health
```yaml
services:
  web:
    depends_on:
      db:
        condition: service_healthy   # Wait for db health check to pass
  db:
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "user"]
      interval: 5s
      retries: 5
```
