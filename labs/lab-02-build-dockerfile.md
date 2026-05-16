# Lab: Build a Custom Docker Image (Flask App)

## Objective
Build a custom Docker image from a Dockerfile for a Python Flask app and observe layer caching in action.

## Steps

### 1. Create project folder and files
```bash
mkdir flask-docker && cd flask-docker
```

**`app.py`:**
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1>Hello from my custom Docker image!</h1>"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

**`requirements.txt`:**
```
flask==3.0.0
```

**`Dockerfile`:**
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

### 2. Build the Image
```bash
docker build -t my-flask-app:1.0 .
# Watch each step → notice "CACHED" for unchanged layers
```

### 3. Run and Test
```bash
docker run -d -p 5000:5000 --name flask my-flask-app:1.0
curl http://localhost:5000
```

### 4. Test Layer Caching
Edit `app.py` (change the return message), then rebuild:
```bash
docker build -t my-flask-app:1.1 .
# Steps 1-3 are CACHED. Only COPY . . and after run again.
```

### 5. Cleanup
```bash
docker stop flask && docker rm flask
docker rmi my-flask-app:1.0 my-flask-app:1.1
```
