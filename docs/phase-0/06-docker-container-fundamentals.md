# Phase 0 — Topic 6: Docker and Container Fundamentals

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.6

## Learning objectives

By the end of this topic, you should be able to:
- Explain Docker, containers, images, and Dockerfiles.
- Build an image from a Dockerfile.
- Run, inspect, troubleshoot, stop, and remove containers.
- Understand port mapping and container logs.
- Explain image layers and build caching.
- Compare containers with virtual machines.
- Explain how Docker-built images are used by Kubernetes and Amazon EKS.

## 1. What is Docker?

Docker is a platform and set of tools for building, packaging, distributing, and running applications in containers.

A container packages an application with its runtime and dependencies. Containers use operating-system-level isolation and share the host kernel, rather than each requiring a complete guest operating system.

## 2. Core Docker concepts

- **Dockerfile:** Instructions used to build an image.
- **Image:** A packaged, versioned template used to create containers.
- **Container:** An isolated running process created from an image.
- **Registry:** A service that stores and distributes images, such as Docker Hub or Amazon ECR.

## 3. How Docker works

```text
Application source code
        |
        v
    Dockerfile
        |
        | docker build
        v
    Docker image
        |
        | docker run
        v
   Running container
```

Useful commands:

| Command | Purpose |
|---|---|
| `docker --version` | Check Docker version |
| `docker info` | Inspect Docker engine information |
| `docker pull nginx` | Download an image |
| `docker images` | List local images |
| `docker build -t myapp:v1 .` | Build an image |
| `docker run nginx` | Run a container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker logs <container>` | View container logs |
| `docker inspect <container>` | Inspect container configuration |
| `docker stop <container>` | Stop a container |
| `docker rm <container>` | Remove a container |
| `docker rmi <image>` | Remove an image |

## 4. Install and verify Docker

Use Docker Desktop on macOS or Windows, or Docker Engine on Linux.

Check installation and daemon availability:

```bash
docker --version
docker info
```

Run the test container:

```bash
docker run hello-world
```

Docker downloads the `hello-world` image if needed, starts a container, and prints a success message.

## 5. Hands-on lab: Run Nginx

Download the Nginx image:

```bash
docker pull nginx
docker images
```

Start a container in the background and publish its HTTP port:

```bash
docker run -d --name my-nginx -p 8080:80 nginx
```

Port mapping uses this format:

```text
-p HOST_PORT:CONTAINER_PORT
-p 8080:80
```

Port 8080 on your computer forwards to port 80 inside the container.

Open `http://localhost:8080` in a browser, or test it:

```bash
curl -I http://localhost:8080
```

Inspect the container:

```bash
docker ps
docker logs my-nginx
docker inspect my-nginx
```

Stop and remove it:

```bash
docker stop my-nginx
docker rm my-nginx
```

## 6. Build your own Python image

### Step 1 — Create the project

```bash
mkdir -p ~/kubernetes-learning/docker-lab
cd ~/kubernetes-learning/docker-lab
```

Create `app.py`:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path == "/health":
            self.send_response(200)
            self.end_headers()
            self.wfile.write(b"Application is healthy")
        else:
            self.send_response(200)
            self.end_headers()
            self.wfile.write(b"Hello from my Docker container!")

server = HTTPServer(("0.0.0.0", 5000), Handler)
print("Application listening on port 5000")
server.serve_forever()
```

### Step 2 — Create a Dockerfile

Create a file named exactly `Dockerfile` (no extension):

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

| Instruction | Purpose |
|---|---|
| `FROM` | Selects the base image |
| `WORKDIR` | Sets the working directory |
| `COPY` | Copies files into the image |
| `EXPOSE` | Documents the container's listening port |
| `CMD` | Defines the default command when the container starts |

Important: `EXPOSE` does not publish a port on your computer. Use `-p` when running the container.

### Step 3 — Build the image

```bash
docker build -t my-python-app:v1 .
docker images
```

The final dot is the build context: the current directory sent to the Docker build process.

### Step 4 — Run the application

```bash
docker run -d --name python-app -p 5000:5000 my-python-app:v1
```

### Step 5 — Test the application

```bash
curl http://localhost:5000
curl http://localhost:5000/health
```

Expected response bodies:

```text
Hello from my Docker container!
Application is healthy
```

### Step 6 — Inspect logs and clean up

```bash
docker ps
docker logs python-app
docker stop python-app
docker rm python-app
```

Optionally remove the image after the container is removed:

```bash
docker rmi my-python-app:v1
```

## 7. Docker image layers and caching

Docker images are built from layers. Build steps can be cached and reused when their inputs have not changed. This can make repeated builds faster.

For example:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

For larger applications, copy dependency manifests and install dependencies before copying frequently changing source code. This can help preserve dependency-installation cache layers. We will practice more image optimization in the next topic.

## 8. Containers vs. virtual machines

| Containers | Virtual machines |
|---|---|
| Share the host kernel | Run a guest operating system |
| Usually lightweight | Usually use more resources |
| Start quickly in many workloads | Often take longer to boot |
| Isolate application processes | Virtualize hardware and isolate a full OS |

Containers provide process and resource isolation, but should not automatically be treated as a strong security boundary. Production workloads should use least privilege and appropriate security controls.

## 9. Docker and Kubernetes / EKS

A typical delivery flow is:

```text
Developer writes application
        |
        v
Docker builds an image
        |
        v
Image is pushed to Amazon ECR
        |
        v
Kubernetes schedules a Pod
        |
        v
Container runs on an EKS worker node
```

Important production detail: Kubernetes does not require Docker Engine to run containers. EKS commonly uses `containerd` through the Container Runtime Interface (CRI). Docker remains useful for building, testing, and understanding container images.

## Assignment 0.6 checklist

- [ ] Verify Docker installation using `docker --version` and `docker info`.
- [ ] Run the `hello-world` container.
- [ ] Run Nginx and access it through `http://localhost:8080`.
- [ ] Inspect containers using `docker ps` and `docker ps -a`.
- [ ] Create the Python application and Dockerfile.
- [ ] Build the image `my-python-app:v1`.
- [ ] Run the Python container on port 5000.
- [ ] Test the application and health endpoint using `curl`.
- [ ] Inspect container logs.
- [ ] Stop and remove the container.
- [ ] Explain Dockerfile, image, container, and registry.
- [ ] Explain Docker vs. virtual machines.
- [ ] Explain why Kubernetes uses containerd rather than requiring Docker Engine.

## Knowledge check

1. What is the difference between a Docker image and a container?
2. What does `-p 8080:80` mean?
3. Which Dockerfile instruction selects the base image?
4. Which command builds a Docker image?
5. Does Kubernetes require Docker Engine to run containers?

**Completion gate:** Complete the checklist and be able to build, run, inspect, and clean up your own containerized application. Then continue to [Topic 7 — Container Images](07-container-images.md).
