# Lab: Run Your First Docker Container

## Objective
Pull an official image, run a container, modify it live, and explore the container filesystem.

## Steps

### 1. Pull and Run nginx
```bash
docker run -d -p 8080:80 --name my-nginx nginx
```

### 2. Verify it's Running
```bash
docker ps
curl http://localhost:8080    # or open in browser → nginx default page
```

### 3. Explore Inside the Container
```bash
docker exec -it my-nginx bash
ls /usr/share/nginx/html     # Web root
cat /etc/nginx/nginx.conf    # Config
exit
```

### 4. Modify Content Live
```bash
docker exec -it my-nginx bash -c \
  "echo '<h1>Hello from Docker!</h1>' > /usr/share/nginx/html/index.html"
curl http://localhost:8080   # See your change!
```

### 5. View Logs
```bash
docker logs my-nginx
docker logs -f my-nginx      # Follow live
```

### 6. Stop and Remove
```bash
docker stop my-nginx && docker rm my-nginx
```
