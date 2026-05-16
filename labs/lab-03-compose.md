# Lab: Multi-Container App with Docker Compose (Flask + Redis)

## Objective
Use Docker Compose to run a Flask app and Redis together, where the app tracks a visit counter stored in Redis.

## Steps

### 1. Create project files
```bash
mkdir compose-demo && cd compose-demo
```

**`app.py`:**
```python
from flask import Flask
import redis

app = Flask(__name__)
r = redis.Redis(host='redis', port=6379)

@app.route("/")
def index():
    count = r.incr("visits")
    return f"<h1>This page has been visited {count} times!</h1>"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

**`requirements.txt`:** `flask redis`

**`Dockerfile`:**
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

**`docker-compose.yml`:**
```yaml
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app          # Live code reload
    depends_on:
      - redis
  redis:
    image: redis:7-alpine
```

### 2. Start the Stack
```bash
docker compose up -d
docker compose ps        # Both services running
curl http://localhost:5000  # Visit counter shows 1
curl http://localhost:5000  # Shows 2, 3... persisted in Redis!
```

### 3. Edit App Live (Bind Mount)
Change the return string in `app.py` and refresh the browser — no rebuild needed!

### 4. View Logs and Scale
```bash
docker compose logs web
```

### 5. Tear Down
```bash
docker compose down        # Stops containers but Redis data persists
docker compose down -v     # Also deletes volumes (counter resets)
```
