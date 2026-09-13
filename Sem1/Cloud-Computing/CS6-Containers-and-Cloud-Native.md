# CS6: Containers and Cloud-Native

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:** R6 (Docker Docs), R7 (K8s Docs), Kubernetes Documentation
>
> **Contact Hours:** 9-12 (Handout Topics 2.7-2.12)
> **Class Slide:** CS6 - Containers

---

## Why Containers? — The Motivation

### The "Works on My Machine" Problem

A developer builds an application on their laptop: macOS, Python 3.9, `numpy 1.24`, `flask 2.3`. Everything works. They deploy to production: Ubuntu 22.04, Python 3.8, `numpy 1.21`, `flask 2.1`. The app crashes with cryptic import errors.

This happens because the **application is separate from its environment**. The code depends on a specific OS, language version, and library versions — but those are never guaranteed to match across machines.

**A detailed scenario of the problem:**

```
Developer's Machine (macOS):          Production Server (Ubuntu):
┌─────────────────────────┐          ┌──────────────────────────┐
│ Python 3.9.7            │          │ Python 3.8.10            │  ← Different version!
│ numpy 1.24.0            │          │ numpy 1.21.0             │  ← Different version!
│ flask 2.3.2             │          │ flask 2.1.0              │  ← Different version!
│ openssl 3.0.8           │          │ openssl 1.1.1            │  ← Different version!
│ macOS Ventura            │          │ Ubuntu 22.04             │  ← Different OS!
│ Apple Silicon (ARM)     │          │ Intel x86_64             │  ← Different architecture!
└─────────────────────────┘          └──────────────────────────┘
        ✅ Works!                              ❌ Crashes!
```

This is not just a Python problem. It happens across all languages and platforms:
- Java: "But I compiled it with JDK 17" → production runs JDK 11.
- Node.js: "But I tested with Node 18" → CI server runs Node 16.
- C/C++: "But I linked against glibc 2.35" → production has glibc 2.31.

The problem multiplies in microservices: if you have 50 services, each with different dependency requirements, managing consistent environments across development, testing, staging, and production becomes a nightmare.

**Containers solve this** by packaging the application **together with** its exact runtime environment — OS libraries, language runtime, dependencies, configuration — into a single portable artifact. If it runs in a container on your laptop, it runs the same in production. The environment travels with the application.

### The VM Overhead Problem

Before containers, the answer was virtual machines: one VM per service. But VMs carry a full OS each:

```
100 microservices × 100 VMs = 100 OS kernels (each consuming CPU, memory, disk)
```

Containers share the host kernel, eliminating that redundancy:

```
100 microservices × 100 containers = 1 shared OS kernel
```

This makes containers far lighter (MBs vs GBs), faster to start (milliseconds vs minutes), and denser (hundreds per host vs tens).

**The overhead in numbers:**

| Metric | VM (with full OS) | Container |
|---|---|---|
| **Disk footprint** | 1-10 GB per VM (includes full OS) | 10-500 MB per container (app + libs only) |
| **Memory overhead** | 512 MB - 2 GB per VM (OS kernel + services) | 5-50 MB per container (shared kernel) |
| **Boot time** | 30 seconds - 5 minutes | 50 ms - 2 seconds |
| **Density per host** | 10-50 VMs on a single physical server | 100-1,000+ containers on a single server |
| **CPU overhead** | Hypervisor virtualisation overhead (~5-10%) | Near-native performance (<1% overhead) |

**Example:** A company running 100 microservices:
- **With VMs:** 100 VMs × 2 GB OS overhead = **200 GB of RAM** just for operating systems, before any application code runs.
- **With containers:** 100 containers × 0 GB OS overhead = **0 GB** for OS duplication. All 100 containers share one kernel.

### What is a Container? — A Precise Definition

A **container** is a standard unit of software that packages an application's code together with all its dependencies (libraries, runtime, system tools, configuration) so it runs quickly and reliably across different computing environments.

**Key properties of containers:**
- **Isolated:** Each container has its own filesystem, network stack, and process space. Processes in one container cannot see or affect processes in another.
- **Lightweight:** Containers share the host OS kernel. They don't carry a full operating system — just the application and its user-space dependencies.
- **Portable:** A container image runs identically on any machine with a compatible container runtime (Docker, containerd, CRI-O), regardless of the host OS distribution.
- **Immutable:** Container images are read-only. A running container adds a writable layer on top, but the image itself never changes. This ensures consistency across deployments.
- **Fast:** Containers start in milliseconds (no OS boot required). They stop just as quickly.
- **Reproducible:** Given the same Dockerfile/build instructions, you get the same image every time. No "it worked yesterday" surprises.

**How containers achieve isolation without a full OS:**

Containers use two Linux kernel features to create isolation:
1. **Namespaces** — control what a container can **see** (its own processes, network, filesystem, hostname, users)
2. **Cgroups** — control what a container can **use** (CPU, memory, disk I/O, network bandwidth)

Together, namespaces and cgroups create the illusion of an isolated environment without the overhead of a separate OS kernel.

### Container Timeline — Brief History

| Year | Milestone |
|---|---|
| **1979** | Unix `chroot` — first form of filesystem isolation ("change root" restricts a process's view of the filesystem) |
| **2000** | FreeBSD Jails — isolated environments on FreeBSD (filesystem, process, network isolation) |
| **2004** | Solaris Zones/Containers — Sun Microsystems implements OS-level virtualisation |
| **2006** | Google process containers (later renamed cgroups) — resource limiting for groups of processes |
| **2008** | Linux Namespaces mature — PID, NET, MNT, UTS, IPC, USER namespaces complete |
| **2008** | LXC (Linux Containers) — first complete Linux container implementation using cgroups + namespaces |
| **2013** | **Docker launches** — makes containers user-friendly with simple CLI, Dockerfiles, and Docker Hub |
| **2014** | **Kubernetes released** by Google — container orchestration at scale |
| **2015** | Open Container Initiative (OCI) — standardises container image format and runtime specification |
| **2017** | containerd donated to CNCF — Docker extracts its core runtime as an industry-standard component |
| **2020+** | Containers become the standard deployment unit for cloud-native applications |

---

## 2.7 Docker

**Docker** is the most widely used containerization platform. It provides the tools to build, ship, and run containers. Launched in 2013 by Solomon Hykes at dotCloud (later renamed Docker, Inc.), Docker didn't invent containers — LXC existed before it — but Docker made containers **easy to use** and **practical for developers**.

### Why Docker Changed Everything

Before Docker, using containers (LXC) required deep Linux kernel knowledge — manually configuring namespaces, cgroups, and root filesystems. Docker provided:

| Innovation | What Docker Added | Impact |
|---|---|---|
| **Dockerfile** | A simple text file to define how to build an image | Anyone can create a container — no kernel expertise needed |
| **Docker Hub** | A public registry for sharing pre-built images | "npm for containers" — pull and run nginx, postgres, python in one command |
| **Layered images** | Images built in cached, shareable layers | Efficient storage, fast builds, incremental updates |
| **Simple CLI** | `docker run`, `docker build`, `docker push` | Developer-friendly — 3 commands to go from code to running container |
| **Portability** | Same image runs on any Docker host | True "build once, run anywhere" |
| **Ecosystem** | Docker Compose, Docker Swarm, Docker Desktop | Complete developer workflow from local development to production |

**Docker's philosophy: "Build once, run anywhere."** Just as Java promised "write once, run anywhere" for code, Docker delivers it for entire environments. The Dockerfile is the recipe, the image is the dish, and the container is the meal being served.

### 2.7.0 Docker Architecture

Docker uses a **client-server architecture** with three main components:

```
┌──────────────┐         REST API         ┌──────────────────────────────┐
│ Docker Client│ ──────────────────────── │      Docker Daemon (dockerd) │
│ (docker CLI) │                          │                              │
│              │  docker build            │  Manages:                    │
│              │  docker run              │  • Images                    │
│              │  docker pull             │  • Containers                │
│              │  docker push             │  • Networks                  │
│              │                          │  • Volumes                   │
└──────────────┘                          └──────────┬───────────────────┘
                                                     │ pull / push
                                                     ▼
                                          ┌──────────────────────┐
                                          │   Docker Registry     │
                                          │  (Docker Hub, ECR,    │
                                          │   ACR, private)       │
                                          └──────────────────────┘
```

| Component | Role |
|---|---|
| **Docker Daemon (`dockerd`)** | The background service that runs on the host. It manages all Docker objects — images, containers, networks, and volumes. Listens for Docker API requests on a Unix socket (`/var/run/docker.sock`) or TCP port. Does the heavy lifting of building, running, and distributing containers. Internally, the daemon uses **containerd** (a lower-level container runtime) which in turn uses **runc** to actually create and start containers. |
| **Docker Client (`docker` CLI)** | The command-line tool users interact with. Every command (`docker run`, `docker build`, `docker ps`) sends a REST API request to the daemon. The client and daemon can run on the same machine or the client can connect to a remote daemon. The API is a standard HTTP REST API — you could use `curl` instead of the `docker` CLI if you wanted. |
| **Docker Registry** | A storage and distribution service for Docker images. **Docker Hub** is the default public registry (like GitHub for container images). Organisations use **private registries** (Amazon ECR, Azure ACR, self-hosted) for proprietary images. When you `docker pull`, the daemon fetches the image from a registry. When you `docker push`, it uploads to one. |

**Docker Architecture — Full Stack:**

```
┌─────────────────────────────────────────────────┐
│            Docker Client (docker CLI)            │
│       docker build | run | pull | push           │
└───────────────────┬─────────────────────────────┘
                    │ REST API (HTTP)
┌───────────────────▼─────────────────────────────┐
│            Docker Daemon (dockerd)                │
│   Manages images, containers, networks, volumes  │
└───────────────────┬─────────────────────────────┘
                    │ gRPC
┌───────────────────▼─────────────────────────────┐
│               containerd                         │
│   High-level container runtime (image pull,      │
│   storage, networking, execution supervision)    │
└───────────────────┬─────────────────────────────┘
                    │ OCI Runtime Spec
┌───────────────────▼─────────────────────────────┐
│                  runc                            │
│   Low-level container runtime (creates the       │
│   actual container: namespaces + cgroups)         │
└─────────────────────────────────────────────────┘
```

### 2.7.1 Docker Images

A **Docker image** is a read-only template containing the application code, runtime, libraries, dependencies, and configuration. It's the "blueprint" from which containers are created.

Images are built in **layers**. Each layer represents a change (install a package, copy a file, set an environment variable). Layers are cached and shared — if two images both use Ubuntu 22.04 as the base, that base layer is stored only once on disk.

```
Image Layer Stack:
┌─────────────────────────┐
│ LAYER 4: COPY app.py    │  ← Your application code
├─────────────────────────┤
│ LAYER 3: RUN pip install│  ← Install dependencies
├─────────────────────────┤
│ LAYER 2: RUN apt update │  ← Update package manager
├─────────────────────────┤
│ LAYER 1: Ubuntu 22.04   │  ← Base OS layer
└─────────────────────────┘
```

**Key concepts about Docker images:**

| Concept | Explanation |
|---|---|
| **Layers** | Each Dockerfile instruction creates a new layer. Layers are stacked on top of each other. Each layer only stores the **differences** from the layer below it (like git commits). |
| **Immutability** | Once built, image layers are read-only. They never change. This guarantees that the same image always produces the same container. |
| **Layer caching** | Docker caches layers during builds. If a layer hasn't changed, Docker reuses the cached version instead of rebuilding it. This makes subsequent builds much faster. |
| **Layer sharing** | If multiple images use the same base image (e.g., `python:3.11-slim`), that base layer is stored only once on disk and shared among all images. Saves significant disk space. |
| **Tags** | Images are identified by `name:tag` (e.g., `python:3.11-slim`, `nginx:1.25-alpine`). The tag `latest` is used by default if no tag is specified. |
| **Digests** | Each image has a SHA256 digest (content hash) for exact identification. Tags are mutable (someone can push a new `latest`), but digests are immutable. |

**Image size optimisation techniques:**

| Technique | Description | Example |
|---|---|---|
| **Use slim/alpine base images** | Alpine Linux is ~5 MB vs Ubuntu at ~70 MB | `FROM python:3.11-alpine` instead of `FROM python:3.11` |
| **Multi-stage builds** | Use one stage to build, another to run. Only the final stage goes into the image. | Build Go binary in one stage, copy just the binary to a minimal final image |
| **Minimise layers** | Combine `RUN` commands with `&&` to reduce layer count | `RUN apt update && apt install -y curl && rm -rf /var/lib/apt/lists/*` |
| **Use .dockerignore** | Exclude unnecessary files from the build context | Ignore `node_modules/`, `.git/`, `*.log`, test files |
| **Clean up in the same layer** | Remove temporary files in the same RUN instruction | Install, build, then clean in one `RUN` command |

**Multi-Stage Build Example:**

```dockerfile
# Stage 1: Build the application
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp .

# Stage 2: Create the minimal runtime image
FROM alpine:3.18
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]

# Result: Final image is ~15 MB instead of ~1 GB (no Go compiler, no source code)
```

### 2.7.2 Dockerfiles

A **Dockerfile** is a text file containing instructions to build a Docker image. Each instruction creates a layer. The Dockerfile is the **recipe** — it defines exactly how to construct the image step by step.

```dockerfile
# Start from a base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose the port the app runs on
EXPOSE 8000

# Command to run the application
CMD ["python", "app.py"]
```

**Complete Dockerfile Instruction Reference:**

| Instruction | Purpose | Example |
|---|---|---|
| `FROM` | Base image to build upon. Every Dockerfile starts with this. Can use `AS` for multi-stage builds. | `FROM python:3.11-slim AS builder` |
| `WORKDIR` | Set the working directory inside the container. All subsequent instructions run relative to this path. Creates the directory if it doesn't exist. | `WORKDIR /app` |
| `COPY` | Copy files/directories from the host (build context) into the image. Preferred over ADD for simple file copies. | `COPY requirements.txt .` |
| `ADD` | Like COPY, but with extra features: can auto-extract tar archives, and can download files from URLs. Use COPY unless you need these extras. | `ADD app.tar.gz /app/` |
| `RUN` | Execute a command during the build process. Each RUN creates a new layer. Use `&&` to chain commands in one layer. | `RUN apt update && apt install -y curl` |
| `CMD` | Default command to run when the container starts. Only one CMD per Dockerfile (last one wins). Can be overridden at runtime. | `CMD ["python", "app.py"]` |
| `ENTRYPOINT` | Configure the container to run as an executable. Unlike CMD, ENTRYPOINT is not easily overridden. CMD arguments are appended to ENTRYPOINT. | `ENTRYPOINT ["python"]` then `CMD ["app.py"]` |
| `EXPOSE` | Document which port the container listens on. Does NOT actually publish the port — it's metadata. Use `-p` flag with `docker run` to publish. | `EXPOSE 8000` |
| `ENV` | Set environment variables that persist into the running container. | `ENV FLASK_ENV=production` |
| `ARG` | Define build-time variables (only available during `docker build`, not in the running container). | `ARG VERSION=1.0` |
| `VOLUME` | Create a mount point and mark it as holding externally mounted volumes. Data at this path is persisted. | `VOLUME /var/lib/mysql` |
| `LABEL` | Add metadata to the image (author, version, description). | `LABEL maintainer="dev@example.com"` |
| `USER` | Set the user (and optionally group) to use when running the container. Security best practice: don't run as root. | `USER appuser:appgroup` |
| `HEALTHCHECK` | Define a command to check if the container is healthy. Docker runs this periodically. | `HEALTHCHECK CMD curl -f http://localhost:8000/health` |

**CMD vs ENTRYPOINT — A Common Source of Confusion:**

| Aspect | CMD | ENTRYPOINT |
|---|---|---|
| **Purpose** | Default command/arguments | The executable to run |
| **Override at runtime** | Easily overridden: `docker run myapp /bin/bash` | Not overridden by default (use `--entrypoint` flag) |
| **Multiple allowed?** | Only the last CMD applies | Only the last ENTRYPOINT applies |
| **Combined use** | CMD provides default arguments to ENTRYPOINT | ENTRYPOINT defines the executable |
| **Example** | `CMD ["app.py"]` | `ENTRYPOINT ["python"]` |
| **Together** | `ENTRYPOINT ["python"]` + `CMD ["app.py"]` → runs `python app.py` | Override: `docker run myapp test.py` → runs `python test.py` |

**Dockerfile Best Practices:**

| Practice | Why | Example |
|---|---|---|
| **Order instructions by change frequency** | Put rarely-changing instructions first (base image, dependencies) and frequently-changing instructions last (app code). This maximises layer cache hits. | `COPY requirements.txt` before `COPY . .` |
| **Use .dockerignore** | Exclude unnecessary files from the build context to speed up builds and reduce image size. | Add `.git/`, `node_modules/`, `*.log`, `__pycache__/` to `.dockerignore` |
| **Don't run as root** | Running containers as root is a security risk. Create a non-root user. | `RUN adduser --disabled-password appuser` then `USER appuser` |
| **Use multi-stage builds** | Separate build dependencies from runtime dependencies. Final image only contains what's needed to run. | Build stage with compiler → final stage with just the binary |
| **Minimise the number of layers** | Combine related RUN commands with `&&`. Fewer layers = smaller image. | `RUN apt update && apt install -y curl && rm -rf /var/lib/apt/lists/*` |
| **Pin dependency versions** | Specify exact versions for reproducible builds. | `FROM python:3.11.7-slim` not `FROM python:latest` |
| **Use COPY, not ADD** | COPY is simpler and more transparent. Use ADD only for tar extraction or URLs. | `COPY app.py /app/` |

### 2.7.3 Docker Containers

A **container** is a running instance of an image. When you `docker run` an image, Docker creates a container by adding a **writable layer** (also called the **container layer**) on top of the read-only image layers. This writable layer is where runtime changes (new files, log output, database writes) are stored.

**The relationship between images and containers:**

```
Image (read-only):                    Container (read-only + write):
┌─────────────────────────┐          ┌─────────────────────────┐
│                         │          │ WRITE LAYER (ephemeral) │  ← Runtime changes go here
│                         │          ├─────────────────────────┤
│ LAYER 4: COPY app.py   │          │ LAYER 4: COPY app.py   │  ← Same as image (read-only)
│ LAYER 3: RUN pip install│          │ LAYER 3: RUN pip install│
│ LAYER 2: RUN apt update │          │ LAYER 2: RUN apt update │
│ LAYER 1: Ubuntu 22.04  │          │ LAYER 1: Ubuntu 22.04  │
└─────────────────────────┘          └─────────────────────────┘

One image → Many containers (each with its own write layer)
```

**Key property: containers are ephemeral.** When a container is deleted, its writable layer is lost. Any data written inside the container (log files, database records, temporary files) disappears. This is by design — containers should be stateless and replaceable. For persistent data, use **volumes**.

**Container lifecycle:**

```
                    docker create
Image ──────────────────────────────► Created Container
                                           │
                                           │ docker start
                                           ▼
                                     Running Container
                                      │           │
                               docker stop    docker kill
                                      │           │
                                      ▼           ▼
                                    Stopped Container
                                           │
                                           │ docker rm
                                           ▼
                                       Removed (gone)
```

**Essential container commands:**

```bash
# Build, run, manage lifecycle
docker build -t myapp .                # Build an image from a Dockerfile
docker run -d -p 8000:8000 myapp       # Create and start a container (detached, port mapped)
docker run -it ubuntu /bin/bash        # Interactive container with terminal
docker ps                              # List running containers
docker ps -a                           # List ALL containers (including stopped)
docker stop <container_id>             # Gracefully stop (sends SIGTERM, then SIGKILL after 10s)
docker kill <container_id>             # Force stop (sends SIGKILL immediately)
docker rm <container_id>               # Remove a stopped container
docker rm -f <container_id>            # Force remove a running container

# Inspect and debug
docker logs <container_id>             # View container stdout/stderr logs
docker logs -f <container_id>          # Follow logs in real-time (like tail -f)
docker exec -it <id> /bin/bash         # Open a shell inside a running container
docker inspect <container_id>          # Detailed JSON metadata about the container
docker stats                           # Live resource usage (CPU, memory, I/O) for all containers
docker top <container_id>              # Show running processes inside the container

# Cleanup
docker container prune                 # Remove all stopped containers
docker system prune                    # Remove all unused containers, images, networks, volumes
```

**Common `docker run` flags explained:**

| Flag | Purpose | Example |
|---|---|---|
| `-d` | Run in detached mode (background) | `docker run -d nginx` |
| `-p host:container` | Map host port to container port | `-p 8080:80` (host 8080 → container 80) |
| `-v host:container` | Mount a volume or bind mount | `-v /data:/var/lib/mysql` |
| `-e KEY=VALUE` | Set environment variable | `-e MYSQL_ROOT_PASSWORD=secret` |
| `--name` | Assign a name to the container | `--name web-server` |
| `--rm` | Automatically remove container when it stops | `docker run --rm myapp` |
| `-it` | Interactive mode with terminal (stdin + TTY) | `docker run -it ubuntu /bin/bash` |
| `--network` | Connect to a Docker network | `--network my-bridge` |
| `--memory` | Set memory limit | `--memory=512m` |
| `--cpus` | Set CPU limit | `--cpus=2.0` |
| `--restart` | Restart policy (no, always, on-failure, unless-stopped) | `--restart=always` |

### 2.7.4 Docker Registries

A **registry** is a repository for storing and distributing Docker images. It's like GitHub for container images — you push images to a registry and pull them onto any machine that needs to run them.

| Registry | Description | Type |
|---|---|---|
| **Docker Hub** | The default public registry. Millions of pre-built images (nginx, postgres, python, node). Free for public images, paid plans for private. | Public + Private |
| **Amazon ECR** | AWS's fully managed container registry. Integrated with ECS, EKS, and IAM. | Private (primarily) |
| **Google GCR / Artifact Registry** | GCP's container registry. Artifact Registry is the newer, recommended service. | Private |
| **Azure ACR** | Azure's managed container registry. Integrated with AKS and Azure DevOps. | Private |
| **GitHub Container Registry (ghcr.io)** | GitHub's registry, integrated with GitHub Actions. Great for open-source projects. | Public + Private |
| **Harbor** | Open-source enterprise registry with vulnerability scanning, RBAC, and replication. | Self-hosted |
| **Private registry** | Self-hosted registry using the official Docker `registry` image. Full control over storage and access. | Self-hosted |

**Image naming convention:**

```
registry/repository:tag

Examples:
docker.io/library/nginx:1.25          # Docker Hub official image (docker.io is the default)
nginx:latest                           # Shorthand for Docker Hub (registry and library omitted)
mycompany.azurecr.io/api:v2.1         # Azure Container Registry
123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest   # AWS ECR
ghcr.io/myorg/myservice:sha-abc123    # GitHub Container Registry
```

```bash
# Registry operations
docker login myregistry.com                   # Authenticate to a private registry
docker push myregistry.com/myapp:v1.0         # Push image to registry
docker pull myregistry.com/myapp:v1.0         # Pull image from registry
docker tag myapp:latest myregistry.com/myapp:v1.0  # Tag an image for a specific registry
```

**Docker Hub image types:**
- **Official images:** Maintained by Docker or the software vendor (e.g., `nginx`, `postgres`, `python`). Curated, reviewed for security, well-documented.
- **Verified publisher images:** From commercial software vendors (e.g., Oracle, IBM). Verified identity.
- **Community images:** Published by anyone. Use with caution — check download count, star rating, and last update date.

### 2.7.5 Docker Volumes

By default, data inside a container is **ephemeral** — when the container is deleted, the data is lost. **Volumes** provide persistent storage that survives container restarts and deletions.

**The problem volumes solve:**

```
Container A starts → writes data to /data/myfile.txt
Container A stops → container removed → /data/myfile.txt is GONE forever

With a volume:
Container A starts → writes data to /data/myfile.txt (stored in volume)
Container A stops → container removed → volume still exists
Container B starts → mounts same volume → /data/myfile.txt is still there!
```

**Types of storage in Docker:**

| Type | Description | Managed By | Persists After Container Removal | Use Case |
|---|---|---|---|---|
| **Volume** | Stored in a Docker-managed area on the host (`/var/lib/docker/volumes/`). Best for persistent data. Portable across hosts. | Docker | ✅ Yes | Database storage, application state, shared data between containers |
| **Bind mount** | Maps a specific host directory directly into the container. The container reads/writes directly to the host filesystem. | User | ✅ Yes (it's on the host) | Development (mount source code for live editing), sharing config files |
| **tmpfs mount** | Stored in the host's memory only (not on disk). Lost when container stops. | Kernel | ❌ No | Sensitive data that shouldn't be written to disk (secrets, session tokens) |

```bash
# Volume operations
docker volume create mydata                              # Create a named volume
docker volume ls                                         # List all volumes
docker volume inspect mydata                             # Show volume details
docker volume rm mydata                                  # Remove a volume
docker volume prune                                      # Remove all unused volumes

# Using volumes
docker run -v mydata:/var/lib/mysql mysql:8               # Named volume mount
docker run -v /host/path:/container/path myapp            # Bind mount
docker run --mount type=tmpfs,destination=/app/tmp myapp  # tmpfs mount

# Share a volume between two containers
docker run -d -v shared-data:/data --name writer myapp-writer
docker run -d -v shared-data:/data --name reader myapp-reader
```

**When to use each type:**

| Scenario | Use |
|---|---|
| Database running in a container (MySQL, PostgreSQL) | **Volume** — data persists, managed by Docker |
| Local development (edit code on host, run in container) | **Bind mount** — changes on host instantly reflected in container |
| Storing secrets or tokens temporarily | **tmpfs** — never written to disk, cleared on stop |
| Sharing data between multiple containers | **Volume** — multiple containers can mount the same volume |

---

## 2.8 Namespaces and Cgroups

Containers are not a first-class OS concept — they're built from two Linux kernel features: **namespaces** (for isolation) and **cgroups** (for resource limits).

### Namespaces — Isolation

**Namespaces** provide isolation by giving each container its own view of system resources. A process inside a container cannot see or affect processes in other containers or on the host.

| Namespace | What it isolates | Effect |
|---|---|---|
| **PID** | Process IDs | Container sees only its own processes. PID 1 inside the container is not PID 1 on the host. |
| **NET** | Network stack | Container has its own IP address, routing table, ports. Port 80 in container A doesn't conflict with port 80 in container B. |
| **MNT** | File system mounts | Container has its own root filesystem. Cannot see the host's files or other containers' files. |
| **UTS** | Hostname and domain name | Container can have its own hostname. |
| **IPC** | Inter-process communication | Container has its own message queues, semaphores, shared memory. |
| **USER** | User and group IDs | Container can have its own root user (UID 0) that maps to a non-root user on the host. Improves security. |

#### Deep Dive: Each Namespace Type

**1. PID Namespace (Process Isolation)**

Each container gets its own PID numbering starting from 1. The container's main process (e.g., the application) runs as PID 1 inside the container, but on the host it has a different PID (e.g., 4532). The container can only see and manage its own processes — it cannot `kill` or `ps` processes from other containers or the host.

```
Host System PID Space:              Container A PID Space:     Container B PID Space:
PID 1: systemd (init)              PID 1: nginx              PID 1: python app.py
PID 2: kthreadd                    PID 2: nginx worker       PID 2: gunicorn worker
PID 1024: dockerd                  (can't see host processes) (can't see host processes)
PID 4532: nginx (Container A)
PID 4533: nginx worker (Cont. A)
PID 5100: python (Container B)
PID 5101: gunicorn (Container B)
```

**Why PID 1 matters:** In Linux, PID 1 is the init process — it receives orphaned child processes and handles signals. In containers, the application IS PID 1, which means it must handle SIGTERM/SIGINT properly for graceful shutdown.

**2. Network Namespace (Network Isolation)**

Each container gets its own complete network stack: IP addresses, routing tables, port numbers, firewall rules, and network interfaces. This means:
- Two containers can both listen on port 80 without conflict (they have different IP addresses).
- Containers cannot sniff each other's network traffic.
- Each container can have its own DNS resolution configuration.

```
Container A Network:                Container B Network:
IP: 172.17.0.2                     IP: 172.17.0.3
Port 80: nginx                     Port 80: apache
Port 443: nginx                    Port 8080: tomcat
Routing: default via 172.17.0.1    Routing: default via 172.17.0.1
```

Docker creates a virtual bridge network (`docker0`) and assigns each container a virtual ethernet interface connected to this bridge.

**3. Mount Namespace (Filesystem Isolation)**

Each container gets its own root filesystem (`/`). The container's filesystem is composed of the image layers (read-only) plus a writable layer on top. The container cannot access the host's filesystem or other containers' filesystems unless explicitly mounted via volumes or bind mounts.

**4. UTS Namespace (Hostname Isolation)**

Each container can have its own hostname and domain name. This is useful for logging and identification — `container-web-01` is more meaningful than a random hash.

```bash
# Inside Container A:
$ hostname
web-frontend-prod

# Inside Container B:
$ hostname
api-backend-v2
```

**5. IPC Namespace (Inter-Process Communication Isolation)**

Each container gets its own IPC resources: System V IPC objects (message queues, semaphores, shared memory segments) and POSIX message queues. Processes in Container A cannot access shared memory created by processes in Container B.

**6. User Namespace (User ID Mapping)**

User namespaces allow the container to have a different mapping of user IDs. A process running as root (UID 0) inside the container can be mapped to an unprivileged user (e.g., UID 65534) on the host. This provides a critical security layer:

```
Inside Container:    Host System:
UID 0 (root)    →   UID 65534 (nobody)    ← NOT actual root on host!
UID 1000 (app)  →   UID 100000 (mapped)
```

Even if an attacker escapes the container, they land as an unprivileged user on the host.

### Cgroups (Control Groups) — Resource Limits

**Cgroups** (Control Groups) are a Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes. They answer the question: "How much of the host's resources can this container use?"

Without cgroups, a single container could consume all available CPU and memory, starving other containers and the host. Cgroups prevent this by enforcing hard limits.

| Resource | What cgroups control | Example | What happens if exceeded |
|---|---|---|---|
| **CPU** | Limit CPU time a container can use. Can set CPU shares (relative weight) or hard limits (e.g., max 2 cores). | Container A gets max 2 CPU cores. | Process is throttled — it runs slower but isn't killed. |
| **Memory** | Limit RAM a container can consume. Includes both physical memory and swap. | Container B gets max 512 MB. | Container is killed by the OOM (Out of Memory) killer. Docker logs show "OOMKilled." |
| **Disk I/O** | Limit read/write bandwidth and IOPS to block devices. | Container C gets max 100 MB/s disk I/O. | I/O operations are throttled — reads/writes are delayed. |
| **Network** | Limit network bandwidth (via tc/traffic control integration). | Container D gets max 1 Gbps. | Packets are queued or dropped. |
| **PIDs** | Limit number of processes a container can create. | Container E can create max 100 processes. | Fork fails with "resource temporarily unavailable." Prevents fork bombs. |

**Docker cgroup commands in practice:**

```bash
# Limit container to 512 MB of RAM and 1 CPU core
docker run --memory=512m --cpus=1.0 myapp

# Limit to 2 CPU cores and 1 GB of memory, with 512 MB swap
docker run --memory=1g --memory-swap=1.5g --cpus=2.0 myapp

# Limit to max 100 processes (prevent fork bombs)
docker run --pids-limit=100 myapp

# Limit disk I/O to 10 MB/s write speed
docker run --device-write-bps /dev/sda:10mb myapp
```

### How Namespaces and Cgroups Work Together

```
Namespaces → "What can you see?"   (Isolation)
Cgroups    → "How much can you use?" (Resource limits)

Together, they create a container:
- Namespaces ensure Container A can't see Container B's processes, files, or network.
- Cgroups ensure Container A can't consume all the host's CPU or memory.
```

**The container equation:**

```
Container = Namespaces (isolation) + Cgroups (resource limits) + Union Filesystem (layered images)
```

It's important to understand that containers are NOT a separate kernel feature. They are a **combination** of existing kernel features (namespaces, cgroups, seccomp, AppArmor, capabilities) assembled by a container runtime (Docker, containerd) to create an isolated execution environment.

---

## 2.8.1 LXC/LXD — Linux Containers

### LXC (Linux Containers)

**LXC** was the first complete implementation of Linux containers (2008). It uses namespaces and cgroups to create isolated Linux environments — essentially lightweight virtual machines without the hypervisor overhead.

| Aspect | Detail |
|---|---|
| **What it is** | A userspace interface for the Linux kernel's container features (namespaces + cgroups) |
| **Type** | System container — runs a full Linux OS environment (init system, multiple processes) |
| **Use case** | Running multiple isolated Linux instances on a single host, development environments, lightweight VMs |
| **Init system** | Full init system (systemd) — behaves like a real Linux machine |
| **Networking** | Full network stack — bridges, VLANs, macvlan |
| **Storage** | Full root filesystem, supports ZFS, Btrfs, LVM, overlayfs |

### LXD (Next-Gen System Container Manager)

**LXD** is the next-generation system container manager built on top of LXC. It provides a better user experience with a REST API, image management, and remote management capabilities.

| Aspect | LXC | LXD |
|---|---|---|
| **Interface** | Low-level CLI tools | REST API + CLI (`lxc` command) |
| **Image management** | Manual rootfs creation | Image-based (pull from image servers) |
| **Remote management** | Limited | Full remote management via API |
| **Clustering** | Not built-in | Built-in clustering across multiple hosts |
| **VM support** | Containers only | Containers + lightweight VMs (QEMU) |
| **Security** | Manual configuration | Built-in security profiles |

### LXC/LXD vs Docker — Key Differences

| Aspect | LXC/LXD | Docker |
|---|---|---|
| **Container type** | System containers (full OS) | Application containers (single process) |
| **Philosophy** | "Machine-like" — runs a complete Linux | "Process-like" — runs one application |
| **Init system** | Has systemd/init | No init — the app IS PID 1 |
| **Processes** | Multiple (sshd, cron, app, etc.) | Single (the application) |
| **Lifecycle** | Long-lived (weeks/months) | Short-lived (minutes to hours, easily replaced) |
| **Image format** | Full root filesystem images | Layered images (Dockerfile) |
| **Orchestration** | Not K8s-native | Native K8s/Docker Compose support |
| **Primary use** | Dev environments, lightweight VMs, legacy apps | Microservices, CI/CD, cloud-native |

---

## 2.9 System Containers and Application Containers

Not all containers serve the same purpose. There are two broad categories:

### System Containers

A **system container** behaves like a lightweight virtual machine. It runs a full init system (systemd, upstart), can host multiple processes, and provides a complete OS-like environment.

| Characteristic | Detail |
|---|---|
| **Purpose** | Replace lightweight VMs. Run a full Linux environment. |
| **Processes** | Multiple processes (init system, sshd, cron, applications). |
| **Lifecycle** | Long-lived — runs for weeks/months like a server. |
| **Init system** | Has a full init system (systemd). |
| **Use case** | Development environments, legacy application hosting, testing. |
| **Examples** | LXC, LXD, systemd-nspawn. |

### Application Containers

An **application container** runs a **single process** (or a small group of related processes) that constitutes one application or microservice.

| Characteristic | Detail |
|---|---|
| **Purpose** | Package and run a single application/microservice. |
| **Processes** | One main process (the application). |
| **Lifecycle** | Often short-lived — may run for seconds to hours. Easily replaced. |
| **Init system** | No init system. The application IS the container's main process. |
| **Use case** | Microservices, CI/CD pipelines, serverless functions, API services. |
| **Examples** | Docker containers, Podman containers. |

### Comparison

| Aspect | System Container | Application Container |
|---|---|---|
| **Analogy** | A lightweight VM | A single application in a box |
| **Processes** | Many | One (or few) |
| **Overhead** | More (full init system) | Less (just the app) |
| **Isolation** | VM-like experience | Process-level |
| **Orchestration** | Less common with K8s | Native with K8s |
| **Philosophy** | "Pet" (maintain, patch, upgrade) | "Cattle" (replace, don't repair) |

**In cloud-native development, application containers (Docker) dominate.** System containers are used for specific scenarios requiring a full OS-like environment.

---

## 2.10 Virtual Machines vs Containers

This is one of the most important comparisons in cloud computing. VMs and containers are not competitors — they're complementary tools for different use cases.

**The key insight:** VMs virtualise **hardware** (each VM gets its own virtual CPU, memory, disk, and runs its own OS kernel). Containers virtualise the **operating system** (each container gets its own process space, filesystem, and network stack but shares the host kernel).

```
Virtual Machines:                        Containers:
┌─────────┐ ┌─────────┐                ┌─────────┐ ┌─────────┐
│  App A  │ │  App B  │                │  App A  │ │  App B  │
│  Bins/  │ │  Bins/  │                │  Bins/  │ │  Bins/  │
│  Libs   │ │  Libs   │                │  Libs   │ │  Libs   │
│ Guest OS│ │ Guest OS│                └────┬────┘ └────┬────┘
└────┬────┘ └────┬────┘                     │           │
     │           │                     ┌────┴───────────┴────┐
┌────┴───────────┴────┐                │   Container Runtime  │
│     Hypervisor      │                │   (Docker/containerd)│
├─────────────────────┤                ├──────────────────────┤
│     Host OS         │                │      Host OS         │
├─────────────────────┤                │   (shared kernel)    │
│     Hardware        │                ├──────────────────────┤
└─────────────────────┘                │      Hardware        │
                                       └──────────────────────┘
Each VM: own kernel                    All containers: shared kernel
```

### Detailed Comparison

| Aspect | Virtual Machines | Containers |
|---|---|---|
| **What is virtualized** | Hardware (CPU, memory, disk, NIC) | Operating system (process space, filesystem, network) |
| **Kernel** | Each VM has its own OS kernel | All containers share the host's kernel |
| **Size** | GBs (full OS + app) | MBs (just app + libraries) |
| **Boot time** | Seconds to minutes | Milliseconds to seconds |
| **Resource overhead** | Heavy (full OS per VM) | Light (no separate OS) |
| **Density** | 10-50 per host | 100-1000+ per host |
| **Isolation** | Strong (separate kernels, hypervisor boundary) | Moderate (shared kernel, namespace isolation) |
| **Security** | More secure (hypervisor is a smaller attack surface) | Less secure (shared kernel = shared vulnerabilities) |
| **Portability** | Less (VM images are large, tied to hypervisor format) | Highly portable (Docker images run anywhere Docker runs) |
| **Persistence** | Persistent by default (VM disk survives reboots) | Ephemeral by default (data lost when container removed) |
| **Use case** | Monolithic apps, legacy software, different OS needs | Microservices, CI/CD, cloud-native apps |
| **Management** | VM lifecycle (create, start, stop, snapshot, migrate) | Container orchestration (Kubernetes, Docker Compose) |

### When to Use VMs

- Running **different operating systems** on the same host (Linux + Windows).
- Applications requiring **strong isolation** and security boundaries (multi-tenant hosting).
- **Legacy applications** that can't be containerized.
- Workloads requiring **dedicated kernel** configurations or custom kernel modules.

### When to Use Containers

- **Microservices architecture** — each service in its own container.
- **CI/CD pipelines** — build, test, deploy in containers for consistency.
- **Cloud-native applications** — designed for horizontal scaling and portability.
- **Development environments** — identical environment on every developer's machine.
- Workloads requiring **high density** and **fast startup**.

### Using Both Together

In practice, most cloud environments use **containers running inside VMs**:

```
┌────────────────────────────────────┐
│           Physical Server           │
├────────────────────────────────────┤
│           Hypervisor (KVM)          │
├──────────────┬─────────────────────┤
│    VM 1      │      VM 2           │
│ ┌──────────┐ │ ┌──────────────────┐│
│ │Container1│ │ │ Container 3      ││
│ │Container2│ │ │ Container 4      ││
│ └──────────┘ │ │ Container 5      ││
│   Docker     │ │   Docker          ││
│   Linux      │ │   Linux           ││
└──────────────┴─┴───────────────────┘
```

VMs provide the strong isolation boundary between tenants. Containers within each VM provide lightweight, fast application packaging.

---

## 2.11 Container Orchestration: Kubernetes Overview

### Why Orchestration?

Running a single container is easy. Running **hundreds or thousands of containers** across multiple hosts, ensuring they communicate, scale, recover from failures, and update without downtime — that's container orchestration.

**The problems of running containers at scale without orchestration:**

| Challenge | What Goes Wrong Without Orchestration |
|---|---|
| **Scheduling** | Which host should each container run on? Manual placement doesn't scale beyond a few containers. |
| **Scaling** | Traffic spikes — you need more container instances. Manually running `docker run` on each host is impractical. |
| **Health monitoring** | A container crashes at 3 AM. Nobody notices until customers complain. |
| **Self-healing** | If a container or host dies, someone must manually restart/recreate it. |
| **Load balancing** | How do requests find the right container? Manually configuring load balancers for ephemeral containers is a nightmare. |
| **Service discovery** | Containers get new IP addresses every time they restart. Other services can't find them. |
| **Rolling updates** | How do you deploy a new version without downtime? Manually stopping old and starting new containers risks errors. |
| **Secret management** | Passwords, API keys, certificates must reach the right containers securely. |
| **Storage** | Stateful containers need persistent storage that follows them across hosts. |
| **Networking** | Containers across multiple hosts need to communicate as if on the same network. |

**Without orchestration:** You're manually SSH-ing into servers, running `docker run`, tracking which container runs where in a spreadsheet, and hoping nothing crashes overnight.

**With orchestration:** You declare "I want 5 replicas of my web app, with 2 GB RAM each, with a health check every 30 seconds" — and Kubernetes makes it happen and keeps it that way.

### Orchestration Tools Comparison

| Tool | Developer | Status | Key Features |
|---|---|---|---|
| **Kubernetes (K8s)** | Google → CNCF | Industry standard | Full-featured, declarative, huge ecosystem, steep learning curve |
| **Docker Swarm** | Docker, Inc. | Maintenance mode | Simple, integrated with Docker CLI, limited features |
| **Amazon ECS** | AWS | Active (AWS only) | Deeply integrated with AWS, simpler than K8s, vendor-specific |
| **HashiCorp Nomad** | HashiCorp | Active | Multi-workload (containers + VMs + bare metal), simpler than K8s |
| **Apache Mesos** | Apache Foundation | Declining use | General-purpose cluster manager, complex |

**Kubernetes** (K8s), originally developed by Google based on their internal system (Borg) and now maintained by the Cloud Native Computing Foundation (CNCF), is the de facto standard for container orchestration.

### Key Kubernetes Concepts

| Concept | What it is | Analogy |
|---|---|---|
| **Pod** | The smallest deployable unit. A group of one or more containers that share network and storage. Most pods contain one container. | A single apartment in a building. |
| **Node** | A physical or virtual machine that runs pods. Each node has a container runtime (Docker/containerd), kubelet, and kube-proxy. | A building that contains apartments. |
| **Cluster** | A set of nodes managed by a control plane. The control plane makes scheduling decisions and manages the cluster state. | The entire apartment complex. |
| **Deployment** | Declares the desired state (e.g. "run 3 replicas of my web app"). Kubernetes ensures reality matches the desired state. | A property manager ensuring 3 apartments are always occupied. |
| **ReplicaSet** | Ensures a specified number of pod replicas are running at any time. Deployments manage ReplicaSets automatically. | The occupancy target — "always keep 3 units rented." |
| **Service** | A stable network endpoint that load-balances traffic across pods. Pods are ephemeral (come and go); Services provide a stable address. | The building's front door — it stays the same even if tenants change. |
| **Namespace** | A virtual partition within a cluster for organising resources and enforcing access control. | Floors in a building — separate but in the same structure. |
| **ConfigMap** | External configuration data (non-sensitive) injected into pods as environment variables or files. | Notice board in the lobby — configuration visible to all residents. |
| **Secret** | Like ConfigMap, but for sensitive data (passwords, tokens, keys). Stored encrypted. | A secure mailbox — only the intended recipient can access it. |
| **Ingress** | Manages external HTTP/HTTPS access to services within the cluster. Provides routing rules, SSL termination, and virtual hosting. | The reception desk — routes visitors to the right apartment. |
| **PersistentVolume (PV)** | A piece of storage provisioned by an admin or dynamically. | A storage locker in the building basement. |
| **PersistentVolumeClaim (PVC)** | A request for storage by a pod. Binds to a PV. | A reservation ticket for a storage locker. |

### Kubernetes Architecture — Detailed

```
┌──────────────────────────────────────────────────────────────┐
│                    CONTROL PLANE (Master)                      │
│                                                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐│
│  │  API Server   │  │  Scheduler   │  │  Controller Manager  ││
│  │  (kube-api)   │  │              │  │                      ││
│  │              │  │ Decides which│  │ - Deployment ctrl    ││
│  │ Front door   │  │ node runs    │  │ - ReplicaSet ctrl    ││
│  │ for all K8s  │  │ each new pod │  │ - Node ctrl          ││
│  │ operations   │  │              │  │ - Job ctrl           ││
│  └──────┬───────┘  └──────────────┘  └──────────────────────┘│
│         │                                                      │
│  ┌──────▼───────┐                                             │
│  │    etcd      │  Distributed key-value store                │
│  │              │  Stores ALL cluster state (single source    │
│  │              │  of truth). If etcd is lost, the cluster    │
│  │              │  state is lost.                              │
│  └──────────────┘                                             │
└───────────────────────────┬──────────────────────────────────┘
                            │ API calls (HTTPS)
┌───────────────────────────▼──────────────────────────────────┐
│                    WORKER NODES                                │
│                                                                │
│  ┌──────────────────────┐    ┌──────────────────────┐        │
│  │  Worker Node 1       │    │  Worker Node 2       │        │
│  │  ┌──────┐ ┌──────┐  │    │  ┌──────┐ ┌──────┐  │        │
│  │  │Pod A │ │Pod B │  │    │  │Pod C │ │Pod D │  │        │
│  │  └──────┘ └──────┘  │    │  └──────┘ └──────┘  │        │
│  │                      │    │                      │        │
│  │  kubelet  (agent)    │    │  kubelet  (agent)    │        │
│  │  kube-proxy (network)│    │  kube-proxy (network)│        │
│  │  containerd (runtime)│    │  containerd (runtime)│        │
│  └──────────────────────┘    └──────────────────────┘        │
└──────────────────────────────────────────────────────────────┘
```

**Control Plane Components Explained:**

| Component | Role |
|---|---|
| **API Server (`kube-apiserver`)** | The front door for all cluster operations. Every `kubectl` command, every internal component communication goes through the API Server. It validates and processes REST requests, then stores the result in etcd. |
| **Scheduler (`kube-scheduler`)** | Watches for newly created pods with no assigned node and selects a node based on resource requirements, constraints, affinity rules, and available capacity. |
| **Controller Manager (`kube-controller-manager`)** | Runs a set of controllers that continuously watch the cluster state and take action to move the current state toward the desired state. Examples: Deployment controller ensures the right number of pods, Node controller detects when nodes go down. |
| **etcd** | A distributed, consistent key-value store that holds all cluster configuration and state. It's the single source of truth. Backed up regularly in production. |

**Worker Node Components:**

| Component | Role |
|---|---|
| **kubelet** | An agent running on every node. It receives pod specifications from the API server and ensures the described containers are running and healthy. Reports node status back to the control plane. |
| **kube-proxy** | Maintains network rules on each node. Implements Kubernetes Services — routes traffic to the correct pod. |
| **Container runtime** | The software that actually runs containers (containerd, CRI-O). Kubernetes is container-runtime agnostic — it uses the Container Runtime Interface (CRI). |

### What Kubernetes Does

| Function | How |
|---|---|
| **Scheduling** | Decides which node runs each pod based on resource requirements and constraints. |
| **Self-healing** | If a pod crashes, K8s automatically restarts it. If a node dies, K8s reschedules its pods to other nodes. |
| **Scaling** | Horizontal Pod Autoscaler (HPA) adds/removes pod replicas based on CPU/memory utilization or custom metrics. Vertical Pod Autoscaler (VPA) adjusts resource requests. |
| **Load balancing** | Services distribute traffic across healthy pods. Supports ClusterIP (internal), NodePort (external via node), LoadBalancer (cloud provider LB), and ExternalName. |
| **Rolling updates** | Update pods incrementally without downtime — replace old pods with new ones gradually. If the new version fails health checks, K8s automatically pauses the rollout. |
| **Rollback** | If an update fails, roll back to the previous version with a single command: `kubectl rollout undo deployment/myapp`. |
| **Secret management** | Securely store and inject passwords, API keys, certificates as environment variables or mounted files. |
| **Storage orchestration** | Automatically provision and attach persistent storage (EBS, Azure Disk, NFS) to pods via PersistentVolumeClaims. |
| **Batch execution** | Run one-off Jobs and scheduled CronJobs for batch processing tasks. |

---

## 2.12 Cloud-Native Design Principles

**Cloud-native** is a design philosophy for building applications that fully exploit cloud capabilities — elasticity, resilience, scalability, and managed services. It's not just "running in the cloud" — it's building specifically FOR the cloud.

### Key Principles

### 2.12.1 Microservices Architecture

Instead of building one large application (monolith), break it into **small, independent services** that each do one thing well. Each microservice:
- Has its own codebase and can be developed/deployed independently.
- Communicates with other services via well-defined APIs (REST, gRPC, message queues).
- Can be scaled independently (scale the payment service without scaling the user service).
- Can be written in different languages (Python service + Go service + Java service).
- Has its own database (database per service) — no shared database across services.

**Monolith vs Microservices:**

| Aspect | Monolith | Microservices |
|---|---|---|
| Deployment | Deploy entire app for any change | Deploy individual services independently |
| Scaling | Scale entire app (even if only one component is overloaded) | Scale individual services based on demand |
| Technology | Single tech stack | Each service can use best-fit technology |
| Failure | One bug can crash entire app | One service failure doesn't crash others (if designed with resilience) |
| Complexity | Simpler initially, harder to maintain as it grows | More complex initially (distributed systems), but each service stays simple |
| Team structure | One large team works on one codebase | Small teams own individual services ("two-pizza teams") |
| Data management | Single shared database | Each service owns its own data (database per service) |
| Testing | End-to-end testing of entire application | Test individual services independently + integration tests |
| Deployment speed | Slow (deploy everything for a one-line change) | Fast (deploy only the changed service) |

**Example: E-commerce application**

```
Monolith:                               Microservices:
┌──────────────────────────┐           ┌─────────┐  ┌─────────┐  ┌──────────┐
│                          │           │  User    │  │  Product │  │  Order   │
│  User Management         │           │  Service │  │  Service │  │  Service │
│  Product Catalog         │           │  (Node)  │  │  (Python)│  │  (Java)  │
│  Order Processing        │   →→→     └─────────┘  └─────────┘  └──────────┘
│  Payment                 │           ┌─────────┐  ┌─────────┐  ┌──────────┐
│  Inventory               │           │ Payment  │  │Inventory │  │ Shipping │
│  Shipping                │           │ Service  │  │ Service  │  │ Service  │
│                          │           │  (Go)    │  │  (Rust)  │  │ (Python) │
│  [Single Database]       │           └─────────┘  └─────────┘  └──────────┘
└──────────────────────────┘            Each has its own database
```

### 2.12.2 Declarative Deployment (Imperative vs Declarative)

This is a fundamental concept in Kubernetes and cloud-native computing:

**Imperative approach:** You tell the system **what to do**, step by step.

```bash
# Imperative: step-by-step commands
kubectl create deployment web-app --image=myapp:v1.0
kubectl scale deployment web-app --replicas=3
kubectl set image deployment/web-app web-app=myapp:v2.0
kubectl expose deployment web-app --port=80 --type=LoadBalancer
```

**Declarative approach:** You tell the system **what you want** (desired state), and the system figures out how to achieve it.

```yaml
# Declarative: desired state in YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3                    # "I want 3 copies running"
  selector:
    matchLabels:
      app: web-app
  template:
    spec:
      containers:
      - name: web-app
        image: myapp:v2.0
        resources:
          limits:
            cpu: "500m"
            memory: "256Mi"
```

| Aspect | Imperative | Declarative |
|---|---|---|
| **How it works** | "Create 3 pods, then expose port 80, then…" | "I want 3 pods running myapp:v2.0" |
| **Analogy** | Giving a taxi driver turn-by-turn directions | Giving a taxi driver the destination address |
| **State management** | You track current state manually | Kubernetes tracks and maintains desired state |
| **Self-healing** | If a pod dies, you must notice and fix it | K8s detects the discrepancy and recreates the pod automatically |
| **Version control** | Commands in bash history (hard to reproduce) | YAML files in Git (easy to review, audit, reproduce) |
| **Idempotency** | Running `create` twice → error ("already exists") | Running `apply` twice → no change (already at desired state) |
| **Recommended for** | Quick ad-hoc testing, learning | Production, CI/CD pipelines, GitOps |

**Kubernetes uses the declarative model.** You declare "I want 3 replicas of myapp:v2.0." Kubernetes continuously monitors the cluster and ensures this is always true:
- If a pod crashes → K8s creates a new one (back to 3).
- If you change the image to v2.1 → K8s performs a rolling update.
- If a node dies → K8s reschedules the lost pods to other nodes.

The command `kubectl apply -f deployment.yaml` is declarative — "make reality match this file."

### 2.12.3 Portability

Cloud-native applications are designed to run on **any cloud provider** or even on-premises — without modification. This is achieved through:

- **Containers** — package the app with its dependencies. Runs the same on AWS, Azure, GCP, or a laptop.
- **Kubernetes** — same orchestration API across all providers (EKS, GKE, AKS all run standard Kubernetes).
- **Infrastructure as Code (IaC)** — define infrastructure in code (Terraform, Pulumi) that can target any provider.
- **Avoid provider-specific services** where portability matters (use PostgreSQL instead of DynamoDB, use Redis instead of ElastiCache).
- **Use open standards** — OCI container images, Kubernetes APIs, Prometheus metrics, OpenTelemetry tracing.

### 2.12.4 The 12-Factor App Methodology

The **12-Factor App** is a set of best practices for building cloud-native applications, originally defined by Heroku developers. Every cloud-native developer should know these:

| Factor | Principle | Cloud-Native Implication |
|---|---|---|
| **1. Codebase** | One codebase tracked in version control, many deploys | Each microservice has its own Git repo. Dev, staging, and prod deploy from the same codebase. |
| **2. Dependencies** | Explicitly declare and isolate dependencies | Use `requirements.txt`, `package.json`, `go.mod`. Never rely on system-wide packages. |
| **3. Config** | Store config in the environment (not in code) | Use environment variables, Kubernetes ConfigMaps, Secrets. Never hardcode database URLs or API keys. |
| **4. Backing services** | Treat backing services (databases, queues, caches) as attached resources | Connect to MySQL via a URL in an env var. Swap local MySQL for Amazon RDS by changing one config. |
| **5. Build, release, run** | Strictly separate build, release, and run stages | CI/CD pipeline: Build (compile + test) → Release (image + config) → Run (deploy to K8s). |
| **6. Processes** | Execute the app as one or more **stateless** processes | Store nothing in memory between requests. Use Redis/DB for shared state. Any instance can handle any request. |
| **7. Port binding** | Export services via port binding | The app binds to a port (e.g., 8080) and serves requests. No external web server (Apache) needed. |
| **8. Concurrency** | Scale out via the process model | Need more capacity? Add more container instances (horizontal scaling), not bigger machines. |
| **9. Disposability** | Fast startup and graceful shutdown | Containers start in seconds and handle SIGTERM gracefully. Enables rapid scaling and deployments. |
| **10. Dev/prod parity** | Keep development, staging, and production as similar as possible | Use Docker — same image in dev and prod. Avoid "it works on my machine." |
| **11. Logs** | Treat logs as event streams | Write to stdout. Let the platform (Docker, K8s, Fluentd) collect, aggregate, and store logs. |
| **12. Admin processes** | Run admin/management tasks as one-off processes | Database migrations, data fixes run as K8s Jobs, not manual SSH sessions. |

### 2.12.5 Additional Cloud-Native Principles

| Principle | Description |
|---|---|
| **Immutable infrastructure** | Don't patch running servers — instead, build a new image with the patch and replace the old instances. Containers are naturally immutable: don't SSH into a running container to fix something — fix the Dockerfile, rebuild, redeploy. |
| **Design for failure** | Assume any component can fail at any time. Build resilience with retries (with exponential backoff), circuit breakers (stop calling a failing service), health checks, redundancy, and graceful degradation. **"Everything fails, all the time."** — Werner Vogels, CTO of Amazon. |
| **Observability** | Build in logging, metrics, and distributed tracing from the start. The three pillars: **Logs** (what happened), **Metrics** (how much/how often), **Traces** (the journey of a request across services). Use tools like Prometheus (metrics), Grafana (dashboards), Jaeger/Zipkin (tracing), ELK stack (logs). You can't fix what you can't see. |
| **CI/CD** | Continuous Integration (automated builds and tests on every commit) + Continuous Deployment (automated deployment to production). Every commit that passes tests is automatically deployed. Tools: GitHub Actions, Jenkins, GitLab CI, ArgoCD (GitOps). |
| **API-first** | Design services around well-defined APIs. Services communicate through APIs (REST, gRPC, GraphQL), not direct database access. This enables loose coupling — services can be rewritten, replaced, or scaled independently. |
| **GitOps** | Use Git as the single source of truth for both code and infrastructure. All changes (app code, K8s manifests, infrastructure) go through pull requests. Tools like ArgoCD watch Git repos and automatically deploy changes. |

---

*End of Topic 2*
