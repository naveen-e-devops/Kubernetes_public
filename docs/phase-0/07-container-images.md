# Phase 0 — Topic 7: Container Images

**Level:** Beginner to intermediate · **Format:** Hands-on · **Assignment:** 0.7

## Learning objectives

By the end of this topic, you should be able to:
- Explain image layers, tags, digests, and image metadata.
- Build and tag images consistently.
- Use a multi-stage Docker build.
- Reduce image size and improve build caching.
- Create and use a `.dockerignore` file.
- Scan images for known vulnerabilities.
- Explain image immutability, reproducibility, and supply-chain considerations.

## 1. What is a container image?

A container image is a packaged, read-only template containing the filesystem and metadata needed to start a container. It commonly includes an application, runtime, dependencies, configuration defaults, and startup command.

An image is not a running application. A container is created from an image, with a writable container layer added at runtime.

## 2. Image layers

Images are composed of layers. Build instructions such as `FROM`, `RUN`, and `COPY` can create layers. Layers can be reused between images and builds when their content and build inputs match.

```text
Container writable layer (runtime changes)
-------------------------------------------
Application files layer
Installed dependencies layer
Base image layers
-------------------------------------------
Image layers (read-only)
```

Inspect image metadata and layers:

```bash
docker image ls
docker image history my-python-app:v1
docker image inspect my-python-app:v1
docker image inspect --format='{{.Id}}' my-python-app:v1
```

Avoid putting frequently changing files before expensive dependency-installation steps when doing so would invalidate useful cache layers.

## 3. Tags and digests

A tag is a human-readable reference, for example:

- `my-python-app:1.0.0`
- `my-python-app:1.1.0`
- `my-python-app:dev`

A digest identifies image content using a cryptographic hash, for example `sha256:...`.

Tags are convenient but can be moved to point to different image content. A digest is content-addressed and is a stronger reference when you need to pin exactly which image content is deployed.

For production, prefer immutable version tags and/or deploy by digest. Avoid relying on a mutable `latest` tag for reproducible releases.

Tag an existing image:

```bash
docker tag my-python-app:v1 my-python-app:1.0.0
docker image ls
```

## 4. Create a project and .dockerignore

Create a lab directory:

```bash
mkdir -p ~/kubernetes-learning/image-lab
cd ~/kubernetes-learning/image-lab
```

Create `app.py`:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"Image optimization lab")

HTTPServer(("0.0.0.0", 5000), Handler).serve_forever()
```

Create `requirements.txt` (this minimal example has no third-party packages):

```text
# Add application dependencies here as needed.
```

Create `.dockerignore`:

```text
.git
.gitignore
__pycache__/
*.pyc
.env
.venv/
venv/
.pytest_cache/
.coverage
dist/
build/
*.log
```

The Docker build context is the set of files made available to the builder. A `.dockerignore` file excludes unnecessary files from that context. It helps reduce context size and lowers the chance of accidentally copying local secrets or unrelated files into an image. It is not a substitute for secret management.

## 5. Build an optimized single-stage image

Create a Dockerfile:

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

Build and inspect:

```bash
docker build -t image-lab:1.0.0 .
docker image ls image-lab
docker image inspect image-lab:1.0.0
```

Run and test:

```bash
docker run -d --name image-lab -p 5001:5000 image-lab:1.0.0
curl http://localhost:5001
docker logs image-lab
docker stop image-lab
docker rm image-lab
```

## 6. Multi-stage builds

Multi-stage builds use more than one `FROM` stage. Build tools and temporary files can stay in a builder stage, while only required runtime artifacts are copied into the final stage.

This example compiles a small Go application in one stage and copies the executable into a minimal runtime stage:

```dockerfile
FROM golang:1.23-alpine AS builder
WORKDIR /src
COPY main.go .
RUN CGO_ENABLED=0 go build -o /out/app main.go

FROM scratch
COPY --from=builder /out/app /app
ENTRYPOINT ["/app"]
```

The example assumes a compatible `main.go` file is present. A `scratch` image contains no shell or system utilities, so debugging it differs from debugging a general-purpose Linux image.

For Python applications, dependencies often need native libraries and a compatible runtime in the final stage. Multi-stage builds can still help separate compilation from runtime, but the final stage must contain every required runtime dependency.

## 7. Image size and security best practices

- Use trusted, maintained base images.
- Prefer small, purpose-built base images when compatible with your application.
- Pin base image versions; consider digest pinning for stronger reproducibility.
- Use multi-stage builds where they reduce unnecessary build tooling.
- Use `.dockerignore` to keep build context focused.
- Do not copy credentials, tokens, private keys, or local environment files into images.
- Run as a non-root user where practical.
- Keep packages and base images updated.
- Scan images for vulnerabilities and review severity, exploitability, and available fixes.
- Rebuild and redeploy images when security updates are required.
- Do not assume a smaller image is automatically a more secure image.

A non-root example for a compatible base image:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY app.py .
RUN useradd --create-home --uid 10001 appuser
USER 10001
EXPOSE 5000
CMD ["python", "app.py"]
```

## 8. Scan an image

If Docker Scout is available in your Docker installation/account, you can inspect an image:

```bash
docker scout quickview image-lab:1.0.0
docker scout cves image-lab:1.0.0
```

Alternatively, use an approved scanner in your organization, such as Trivy. Scanner availability, database freshness, and policy settings affect results. A scan is a point-in-time assessment, not a guarantee that an image is secure.

## 9. Image troubleshooting

Useful commands:

```bash
docker image ls
docker image history image-lab:1.0.0
docker image inspect image-lab:1.0.0
docker build --no-cache -t image-lab:test .
docker system df
```

Common problems:

| Symptom | Things to check |
|---|---|
| Build cannot find a file | Build context, current directory, `COPY` path, and `.dockerignore` |
| Unexpectedly large image | Base image, copied files, build tools, caches, and layers |
| Old code appears | Image tag, build context, cache, and whether the intended image was rebuilt |
| Container exits immediately | Entrypoint/CMD, process exit code, and `docker logs` |
| Works locally but not elsewhere | CPU architecture, runtime dependencies, environment, and ports |

## Assignment 0.7 checklist

- [ ] Build the image from the lab Dockerfile.
- [ ] Inspect its layers using `docker image history`.
- [ ] Inspect image metadata using `docker image inspect`.
- [ ] Create a versioned tag such as `image-lab:1.0.0`.
- [ ] Explain the difference between a tag and a digest.
- [ ] Add a `.dockerignore` file and explain why each entry is excluded.
- [ ] Build and run the image, then verify it using `curl`.
- [ ] Explain how a multi-stage build works.
- [ ] Identify at least three ways to reduce image size or attack surface.
- [ ] Run an image vulnerability scan with an available approved tool, or document why one is unavailable.
- [ ] Explain why secrets must not be baked into an image.

## Knowledge check

1. What is an image layer?
2. Why can a mutable tag such as `latest` be risky for production deployments?
3. What is the difference between an image tag and a digest?
4. What does `.dockerignore` do?
5. Why use a multi-stage build?
6. Does a vulnerability scan prove that an image is secure?

**Completion gate:** Build, inspect, tag, run, and assess an image. Be able to explain how you would make the image reproducible and reduce unnecessary contents. Then continue to [Topic 8 — Container Registries](08-container-registries.md).
