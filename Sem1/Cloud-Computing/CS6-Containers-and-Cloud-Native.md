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

---

## 2.7 Docker

**Docker** is the most widely used containerization platform. It provides the tools to build, ship, and run containers.

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
| **Docker Daemon (`dockerd`)** | The background service that runs on the host. It manages all Docker objects — images, containers, networks, and volumes. Listens for Docker API requests on a Unix socket or TCP port. Does the heavy lifting of building, running, and distributing containers. |
| **Docker Client (`docker` CLI)** | The command-line tool users interact with. Every command (`docker run`, `docker build`, `docker ps`) sends a REST API request to the daemon. The client and daemon can run on the same machine or the client can connect to a remote daemon. |
| **Docker Registry** | A storage and distribution service for Docker images. **Docker Hub** is the default public registry (like GitHub for container images). Organisations use **private registries** (Amazon ECR, Azure ACR, self-hosted) for proprietary images. When you `docker pull`, the daemon fetches the image from a registry. When you `docker push`, it uploads to one. |

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

### 2.7.2 Dockerfiles

A **Dockerfile** is a text file containing instructions to build a Docker image. Each instruction creates a layer.

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

**Key Dockerfile instructions:**

| Instruction | Purpose |
|---|---|
| `FROM` | Base image to build upon (every Dockerfile starts with this). |
| `WORKDIR` | Set the working directory inside the container. |
| `COPY` / `ADD` | Copy files from host into the image. |
| `RUN` | Execute a command during build (install packages, compile code). |
| `EXPOSE` | Document which port the container listens on. |
| `ENV` | Set environment variables. |
| `CMD` | Default command to run when the container starts. |
| `ENTRYPOINT` | Configure the container to run as an executable. |

### 2.7.3 Docker Containers

A **container** is a running instance of an image. When you `docker run` an image, Docker creates a container by adding a **writable layer** on top of the read-only image layers. This writable layer is where runtime changes (new files, log output, database writes) are stored.

**Container lifecycle:**

```
docker build -t myapp .        → Build an image from a Dockerfile
docker run -d -p 8000:8000 myapp → Create and start a container
docker ps                       → List running containers
docker stop <container_id>      → Stop a container
docker rm <container_id>        → Remove a stopped container
docker logs <container_id>      → View container logs
docker exec -it <id> /bin/bash  → Open a shell inside a running container
```

### 2.7.4 Docker Registries

A **registry** is a repository for storing and distributing Docker images. It's like GitHub for container images.

| Registry | Description |
|---|---|
| **Docker Hub** | The default public registry. Millions of pre-built images (nginx, postgres, python, node). |
| **Amazon ECR** | AWS's private container registry. |
| **Google GCR / Artifact Registry** | GCP's container registry. |
| **Azure ACR** | Azure's container registry. |
| **GitHub Container Registry** | GitHub's registry, integrated with GitHub Actions. |
| **Private registry** | Self-hosted registry for organisations that need full control. |

```bash
docker push myregistry.com/myapp:v1.0    # Push image to registry
docker pull myregistry.com/myapp:v1.0    # Pull image from registry
```

### 2.7.5 Docker Volumes

By default, data inside a container is **ephemeral** — when the container is deleted, the data is lost. **Volumes** provide persistent storage that survives container restarts and deletions.

**Types of storage in Docker:**

| Type | Description | Use case |
|---|---|---|
| **Volume** | Managed by Docker, stored in a Docker-managed area on the host. Best for persistent data. | Database storage, application state. |
| **Bind mount** | Maps a specific host directory into the container. | Development (mount source code for live editing). |
| **tmpfs mount** | Stored in the host's memory only (not on disk). Lost when container stops. | Sensitive data that shouldn't be written to disk. |

```bash
docker volume create mydata                              # Create a volume
docker run -v mydata:/var/lib/mysql mysql:8              # Mount volume into container
docker run -v /host/path:/container/path myapp           # Bind mount
```

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

### Cgroups (Control Groups) — Resource Limits

**Cgroups** limit, account for, and isolate the resource usage (CPU, memory, disk I/O, network) of a group of processes (a container).

| Resource | What cgroups control | Example |
|---|---|---|
| **CPU** | Limit CPU time a container can use. | Container A gets max 2 CPU cores. |
| **Memory** | Limit RAM a container can consume. | Container B gets max 512 MB. If it exceeds, it's killed (OOM). |
| **Disk I/O** | Limit read/write bandwidth to disk. | Container C gets max 100 MB/s disk I/O. |
| **Network** | Limit network bandwidth. | Container D gets max 1 Gbps. |
| **PIDs** | Limit number of processes. | Container E can create max 100 processes (prevents fork bombs). |

### How Namespaces and Cgroups Work Together

```
Namespaces → "What can you see?"   (Isolation)
Cgroups    → "How much can you use?" (Resource limits)

Together, they create a container:
- Namespaces ensure Container A can't see Container B's processes, files, or network.
- Cgroups ensure Container A can't consume all the host's CPU or memory.
```

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

**Kubernetes** (K8s), originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), is the de facto standard for container orchestration.

### Key Kubernetes Concepts

| Concept | What it is | Analogy |
|---|---|---|
| **Pod** | The smallest deployable unit. A group of one or more containers that share network and storage. Most pods contain one container. | A single apartment in a building. |
| **Node** | A physical or virtual machine that runs pods. Each node has a container runtime (Docker/containerd), kubelet, and kube-proxy. | A building that contains apartments. |
| **Cluster** | A set of nodes managed by a control plane. The control plane makes scheduling decisions and manages the cluster state. | The entire apartment complex. |
| **Deployment** | Declares the desired state (e.g. "run 3 replicas of my web app"). Kubernetes ensures reality matches the desired state. | A property manager ensuring 3 apartments are always occupied. |
| **Service** | A stable network endpoint that load-balances traffic across pods. Pods are ephemeral (come and go); Services provide a stable address. | The building's front door — it stays the same even if tenants change. |
| **Namespace** | A virtual partition within a cluster for organising resources and enforcing access control. | Floors in a building — separate but in the same structure. |
| **ConfigMap / Secret** | External configuration and sensitive data injected into pods without baking them into the image. | Mailbox for each apartment — configuration delivered separately. |

### Kubernetes Architecture

```
┌──────────────────────────────────────────────────┐
│                  Control Plane                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│  │ API      │ │Scheduler │ │Controller│          │
│  │ Server   │ │          │ │ Manager  │          │
│  └────┬─────┘ └──────────┘ └──────────┘          │
│       │       ┌──────────┐                        │
│       │       │  etcd    │ (cluster state store)  │
│       │       └──────────┘                        │
└───────┼──────────────────────────────────────────┘
        │ (API calls)
┌───────┼──────────────────────────────────────────┐
│  Worker Nodes                                      │
│  ┌──────────────────┐  ┌──────────────────┐       │
│  │  Node 1          │  │  Node 2          │       │
│  │ ┌─────┐ ┌─────┐ │  │ ┌─────┐ ┌─────┐ │       │
│  │ │Pod A│ │Pod B│ │  │ │Pod C│ │Pod D│ │       │
│  │ └─────┘ └─────┘ │  │ └─────┘ └─────┘ │       │
│  │ kubelet          │  │ kubelet          │       │
│  │ kube-proxy       │  │ kube-proxy       │       │
│  │ container runtime│  │ container runtime│       │
│  └──────────────────┘  └──────────────────┘       │
└───────────────────────────────────────────────────┘
```

### What Kubernetes Does

| Function | How |
|---|---|
| **Scheduling** | Decides which node runs each pod based on resource requirements and constraints. |
| **Self-healing** | If a pod crashes, K8s automatically restarts it. If a node dies, K8s reschedules its pods to other nodes. |
| **Scaling** | Horizontal Pod Autoscaler adds/removes pod replicas based on CPU/memory utilization or custom metrics. |
| **Load balancing** | Services distribute traffic across healthy pods. |
| **Rolling updates** | Update pods incrementally without downtime — replace old pods with new ones gradually. |
| **Rollback** | If an update fails, roll back to the previous version automatically. |
| **Secret management** | Securely store and inject passwords, API keys, certificates. |
| **Storage orchestration** | Automatically provision and attach persistent storage to pods. |

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

**Monolith vs Microservices:**

| Aspect | Monolith | Microservices |
|---|---|---|
| Deployment | Deploy entire app for any change | Deploy individual services independently |
| Scaling | Scale entire app (even if only one component is overloaded) | Scale individual services based on demand |
| Technology | Single tech stack | Each service can use best-fit technology |
| Failure | One bug can crash entire app | One service failure doesn't crash others |
| Complexity | Simpler initially | More complex (distributed systems challenges) |

### 2.12.2 Declarative Deployment

Instead of writing step-by-step scripts to deploy your application (imperative: "install nginx, then copy files, then start service"), you **declare the desired state** and let the system figure out how to achieve it.

```yaml
# Kubernetes Deployment (declarative)
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

You declare "I want 3 replicas of myapp:v2.0." Kubernetes ensures this is always true — if a pod dies, K8s creates a new one. If you change the image to v2.1, K8s performs a rolling update.

### 2.12.3 Portability

Cloud-native applications are designed to run on **any cloud provider** or even on-premises — without modification. This is achieved through:

- **Containers** — package the app with its dependencies. Runs the same on AWS, Azure, GCP, or a laptop.
- **Kubernetes** — same orchestration API across all providers (EKS, GKE, AKS all run standard Kubernetes).
- **Infrastructure as Code (IaC)** — define infrastructure in code (Terraform, Pulumi) that can target any provider.
- **Avoid provider-specific services** where portability matters (use PostgreSQL instead of DynamoDB).

### 2.12.4 Additional Cloud-Native Principles

| Principle | Description |
|---|---|
| **12-Factor App** | A methodology for building SaaS apps: codebase in version control, explicit dependencies, config in environment, stateless processes, etc. |
| **Immutable infrastructure** | Don't patch running servers — instead, build a new image with the patch and replace the old instances. Containers are naturally immutable. |
| **Design for failure** | Assume any component can fail at any time. Build resilience with retries, circuit breakers, health checks, redundancy. |
| **Observability** | Build in logging, metrics, and tracing from the start. Use tools like Prometheus, Grafana, Jaeger. You can't fix what you can't see. |
| **CI/CD** | Continuous Integration (automated builds and tests) + Continuous Deployment (automated deployment to production). |
| **API-first** | Design services around well-defined APIs. Services communicate through APIs, not direct database access. |

---

*End of Topic 2*
