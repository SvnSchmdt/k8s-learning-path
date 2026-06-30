# Module 01 – Container Basics

## Goal

After this module you understand the difference between images and containers, can build your own Docker image, and know the core Docker commands for day-to-day work.

## Why does this matter?

Kubernetes orchestrates containers. Before you can understand Kubernetes, you need to understand what a container is, how it differs from a virtual machine, and how images are built and stored. Every Pod in Kubernetes runs one or more containers.

## Key Concepts

- **Container image:** An immutable, layered package that contains everything needed to run an application — code, runtime, dependencies, and configuration. Images are built from a Dockerfile and stored in a registry.
- **Container:** A running instance of an image. Containers are isolated processes on the host operating system. Multiple containers can run from the same image simultaneously.
- **Dockerfile:** A text file with instructions for building an image (`FROM`, `RUN`, `COPY`, `CMD`, etc.).
- **Registry:** A service for storing and distributing container images. Examples: Docker Hub, GitHub Container Registry, AWS ECR.
- **Layer caching:** Each Dockerfile instruction creates a layer. Docker caches layers — unchanged layers are reused in subsequent builds.

## Image vs. Container

```
Dockerfile  →  docker build  →  Image  →  docker run  →  Container
                                  ↓
                              Registry (push/pull)
```

An image is like a class definition; a container is like an instance of that class.

## Hands-On Task

### Run your first container

```bash
docker run --rm nginx:1.27-alpine echo "Hello from container"
```

### Explore a running container

```bash
# Run nginx in background
docker run -d --name my-nginx -p 8080:80 nginx:1.27-alpine

# Check logs
docker logs my-nginx

# Execute a command inside
docker exec -it my-nginx sh

# Stop and remove
docker stop my-nginx && docker rm my-nginx
```

### Build a custom image

```dockerfile
# Dockerfile
FROM nginx:1.27-alpine
COPY index.html /usr/share/nginx/html/index.html
```

```bash
echo "<h1>My Kubernetes App</h1>" > index.html
docker build -t my-app:v1 .
docker run -d -p 8080:80 my-app:v1
curl http://localhost:8080
```

## Common Mistakes

- **`latest` tag in production:** Always use specific image tags (e.g., `nginx:1.27-alpine`). `latest` is unpredictable and makes debugging harder.
- **Large images:** Don't install unnecessary packages. Use Alpine-based images where possible.
- **Processes running as root:** Containers should run as non-root users. Use the `USER` instruction in your Dockerfile.

## Checkpoint

- [ ] What is the difference between a container image and a running container?
- [ ] What does a Dockerfile describe?
- [ ] How do you see the logs of a running container?
- [ ] What happens when you `docker stop` a container — are its files gone?

## Definition of Done

You are done with this module when you:

- [ ] Can explain the difference between an image and a container
- [ ] Have built a custom Docker image from a Dockerfile
- [ ] Have run a container and inspected it with `docker exec` and `docker logs`
- [ ] Can stop and remove a container
- [ ] Can answer all checkpoint questions

## Further Reading

- [Docker Overview](https://docs.docker.com/get-started/overview/)
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
