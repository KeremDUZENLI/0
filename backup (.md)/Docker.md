## 🔧 DOCKER IMAGE
```bash
# Build Docker image with tag
docker build -t my-go-app .

# Run docker-compose
docker-compose up

# Run container from 'getting-started' image, map port 3000
docker run -dp 3000:3000 getting-started

# Run 'my-go-app' image, interactive mode, map port 8080:8081
docker run -it -p 8080:8081 my-go-app

# Run 'my-go-app' in detached mode, map port 8080:8081
docker run -d -p 8080:8081 my-go-app

# List all Docker images
docker images

# Force remove Docker image by ID
docker image rm <IMAGE_ID> -f
````

---

## 📦 DOCKER CONTAINER

```bash
# List running containers
docker ps

# List all containers (running and stopped)
docker ps -a

# Kill a container
docker kill <Container_ID>

# Remove all stopped containers
docker container prune

# Stop a running container
docker stop <Container_ID>

# Remove a container
docker rm <Container_ID>

# Force remove a container
docker rm -f <Container_ID>
```

---

## 🖥️ DOCKER TERMINAL

```bash
# Access terminal of a running container
docker exec -it <Container_ID> /bin/bash
```

---

## 🔐 DOCKER PUBLIC KEY (GPG & pass)

```bash
# Generate a GPG key
gpg --generate-key

# Initialize password store with GPG ID
pass init XXXXXXXXXXXXXXXXXXXXXXXX
```