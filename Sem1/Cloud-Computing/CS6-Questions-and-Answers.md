# CS6: Containers and Cloud-Native — Questions & Answers

> 12 questions covering: Docker, namespaces/cgroups, system vs app containers, VMs vs containers, Kubernetes, cloud-native principles, 12-factor app.

---

### Q1. What is a container? How does it differ from a VM at the architecture level?

**Answer:**

A **container** is a lightweight, standalone, executable package containing an application and all its dependencies (code, runtime, libraries, settings). Containers share the host OS kernel but run in isolated user spaces.

**Architecture difference:**

```
VM Architecture:                    Container Architecture:
┌──────┐ ┌──────┐ ┌──────┐        ┌──────┐ ┌──────┐ ┌──────┐
│App A │ │App B │ │App C │        │App A │ │App B │ │App C │
│Libs  │ │Libs  │ │Libs  │        │Libs  │ │Libs  │ │Libs  │
│Guest │ │Guest │ │Guest │        ├──────┴─┴──────┴─┴──────┤
│ OS   │ │ OS   │ │ OS   │        │   Container Runtime     │
├──────┴─┴──────┴─┴──────┤        ├─────────────────────────┤
│      Hypervisor         │        │   Host OS Kernel        │
├─────────────────────────┤        ├─────────────────────────┤
│   Physical Hardware     │        │   Physical Hardware     │
└─────────────────────────┘        └─────────────────────────┘
```

**Key difference:** VMs virtualize the **hardware** (each gets its own kernel). Containers virtualize the **OS** (all share one kernel). This makes containers much lighter (MBs vs GBs), faster to start (milliseconds vs minutes), and higher density (hundreds vs tens per host), but with weaker isolation (shared kernel).

---

### Q2. Explain Docker images, layers, and the Dockerfile build process.

**Answer:**

**Docker image:** A read-only template containing application code, runtime, libraries, dependencies, and configuration. Images are the blueprint from which containers are created.

**Layers:** Images are built in layers. Each Dockerfile instruction creates a layer. Layers are cached and shared — if two images use the same base, that base layer is stored once.

```
Layer stack:
┌──────────────────────┐
│ LAYER 4: COPY app.py │  ← Application code
├──────────────────────┤
│ LAYER 3: RUN pip     │  ← Dependencies
├──────────────────────┤
│ LAYER 2: RUN apt     │  ← System packages
├──────────────────────┤
│ LAYER 1: python:3.11 │  ← Base image
└──────────────────────┘
```

**Dockerfile:** A text file with build instructions.

```dockerfile
FROM python:3.11-slim          # Base image
WORKDIR /app                   # Set working directory
COPY requirements.txt .        # Copy dependency file
RUN pip install -r requirements.txt  # Install dependencies
COPY . .                       # Copy application code
EXPOSE 8000                    # Document port
CMD ["python", "app.py"]       # Default run command
```

**Build process:** `docker build -t myapp .` reads the Dockerfile, executes each instruction creating a layer, and produces a tagged image. Each layer is cached — rebuild is fast if only the last layers change.

---

### Q3. What are Docker volumes? Why are they needed?

**Answer:**

By default, data inside a container is **ephemeral** — when the container is deleted, data is lost. **Volumes** provide persistent storage that survives container restarts and deletions.

| Type | Description | Use case |
|---|---|---|
| **Volume** | Managed by Docker, stored in Docker-managed area on host. | Database storage, application state. |
| **Bind mount** | Maps a specific host directory into the container. | Development — mount source code for live editing. |
| **tmpfs mount** | Stored in host memory only. Lost when container stops. | Sensitive data that shouldn't be on disk. |

```bash
docker volume create mydata
docker run -v mydata:/var/lib/mysql mysql:8      # Named volume
docker run -v /host/path:/app/data myapp         # Bind mount
```

**Why needed:** Containers follow the "cattle, not pets" philosophy — they're disposable and replaceable. But data (databases, user uploads, logs) must persist across container replacements. Volumes decouple data lifecycle from container lifecycle.

---

### Q4. Explain Linux namespaces. List the six types and what each isolates.

**Answer:**

**Namespaces** provide **isolation** — each container gets its own view of system resources, preventing it from seeing or affecting other containers.

| Namespace | What it isolates | Effect |
|---|---|---|
| **PID** | Process IDs | Container sees only its own processes. PID 1 inside container ≠ PID 1 on host. |
| **NET** | Network stack | Container has its own IP address, routing table, ports. Port 80 in container A doesn't conflict with port 80 in container B. |
| **MNT** | File system mounts | Container has its own root filesystem. Cannot see host or other containers' files. |
| **UTS** | Hostname and domain | Container can have its own hostname (e.g. "web-server-1"). |
| **IPC** | Inter-process communication | Container has its own message queues, semaphores, shared memory segments. |
| **USER** | User/group IDs | Container's root (UID 0) can map to a non-root user on host. Improves security. |

**Key insight:** Namespaces answer "what can you see?" — they create the illusion that each container is an independent system.

---

### Q5. Explain Linux cgroups. What resources do they control?

**Answer:**

**Cgroups (Control Groups)** limit, account for, and isolate **resource usage** of a group of processes (a container).

| Resource | What cgroups control | Example |
|---|---|---|
| **CPU** | CPU time/cores allocated | Container A: max 2 cores |
| **Memory** | RAM limit | Container B: max 512 MB (OOM-killed if exceeded) |
| **Disk I/O** | Read/write bandwidth | Container C: max 100 MB/s |
| **Network** | Network bandwidth | Container D: max 1 Gbps |
| **PIDs** | Max number of processes | Container E: max 100 processes (prevents fork bombs) |

**Key insight:** Cgroups answer "how much can you use?" — they prevent one container from consuming all host resources.

**Together:** Namespaces (isolation) + Cgroups (resource limits) = a container. Namespaces ensure container A can't see container B. Cgroups ensure container A can't starve container B of resources.

---

### Q6. Compare system containers and application containers.

**Answer:**

| Aspect | System Container | Application Container |
|---|---|---|
| **Purpose** | Lightweight VM replacement — run a full OS environment | Package and run a single application/microservice |
| **Processes** | Multiple (init system, sshd, cron, apps) | One main process (the application) |
| **Init system** | Full init (systemd) | No init — app IS the main process |
| **Lifecycle** | Long-lived (weeks/months, like a server) | Often short-lived (seconds to hours, replaceable) |
| **Philosophy** | "Pet" — maintain, patch, upgrade | "Cattle" — replace, don't repair |
| **Examples** | LXC, LXD, systemd-nspawn | Docker, Podman |
| **K8s integration** | Less common | Native (pods run application containers) |

**In cloud-native:** Application containers (Docker) dominate. System containers are used for specific scenarios needing a full OS-like environment (development, legacy hosting).

---

### Q7. Compare VMs and containers across 10 dimensions. When would you use each?

**Answer:**

| Aspect | Virtual Machines | Containers |
|---|---|---|
| **Virtualizes** | Hardware (CPU, memory, disk) | OS (process space, filesystem, network) |
| **Kernel** | Each VM has its own kernel | All containers share host kernel |
| **Size** | GBs (full OS + app) | MBs (app + libraries only) |
| **Boot time** | Seconds to minutes | Milliseconds to seconds |
| **Overhead** | Heavy (full OS per VM) | Light (no separate OS) |
| **Density** | 10-50 per host | 100-1000+ per host |
| **Isolation** | Strong (separate kernels, hypervisor boundary) | Moderate (shared kernel, namespace isolation) |
| **Security** | More secure (smaller attack surface) | Less secure (shared kernel vulnerabilities) |
| **Portability** | Less (large images, hypervisor-specific formats) | Highly portable (Docker images run anywhere) |
| **Use case** | Legacy apps, different OS needs, strong isolation | Microservices, CI/CD, cloud-native apps |

**Use VMs when:** Different OS needed, strong isolation required, legacy apps, custom kernel.
**Use containers when:** Microservices, CI/CD, high density, fast startup, cloud-native.
**In practice:** Containers run inside VMs — VMs provide tenant isolation, containers provide lightweight app packaging.

---

### Q8. What is Kubernetes? List its key concepts and explain what it does.

**Answer:**

**Kubernetes (K8s)** is the de facto standard container orchestration platform, originally developed by Google. It automates deployment, scaling, and management of containerized applications across clusters of machines.

**Key concepts:**

| Concept | What it is |
|---|---|
| **Pod** | Smallest deployable unit — one or more containers sharing network/storage. |
| **Node** | Physical/virtual machine running pods (has kubelet, kube-proxy, container runtime). |
| **Cluster** | Set of nodes managed by a control plane. |
| **Deployment** | Declares desired state ("run 3 replicas of myapp:v2"). K8s ensures reality matches. |
| **Service** | Stable network endpoint load-balancing traffic across ephemeral pods. |
| **Namespace** | Virtual partition within cluster for organisation and access control. |
| **ConfigMap/Secret** | External configuration and sensitive data injected into pods. |

**What K8s does:**
1. **Scheduling** — decides which node runs each pod.
2. **Self-healing** — restarts crashed pods, reschedules pods from dead nodes.
3. **Scaling** — Horizontal Pod Autoscaler adds/removes replicas based on metrics.
4. **Load balancing** — Services distribute traffic across healthy pods.
5. **Rolling updates** — update pods incrementally without downtime.
6. **Rollback** — revert to previous version if update fails.
7. **Secret management** — securely store and inject passwords, API keys.
8. **Storage orchestration** — automatically provision persistent storage for pods.

---

### Q9. What are cloud-native design principles? Explain microservices, declarative deployment, and portability.

**Answer:**

**Cloud-native** = designing applications specifically FOR the cloud — exploiting elasticity, resilience, scalability, and managed services.

**Microservices:** Break a monolith into small, independent services. Each has its own codebase, deploys independently, communicates via APIs, can scale independently, can use different technologies.
- *Monolith:* Deploy entire app for any change. Scale everything. One bug crashes all.
- *Microservices:* Deploy individual services. Scale what's needed. One failure is contained.

**Declarative deployment:** Instead of step-by-step scripts ("install nginx, copy files, start service"), declare the desired state and let the system achieve it.
```yaml
spec:
  replicas: 3              # "I want 3 copies"
  containers:
  - image: myapp:v2.0      # "Running this version"
```
Kubernetes ensures this is always true — if a pod dies, K8s creates a new one.

**Portability:** Applications run on any cloud without modification.
- Containers = same image runs on AWS/Azure/GCP/laptop.
- Kubernetes = same API on EKS/GKE/AKS.
- IaC (Terraform) = same code provisions on any provider.

---

### Q10. What is the "noisy neighbour" problem? How do VMs and containers address it differently?

**Answer:**

The **noisy neighbour problem** occurs when one tenant's heavy workload on shared physical infrastructure degrades performance for other tenants on the same host.

**In VMs:** The hypervisor allocates dedicated resources (CPU cores, RAM) to each VM. A VM can't exceed its allocation. However, shared resources like disk I/O and network bandwidth can still be contended. Hypervisors use CPU scheduling (fair-share, priority) and I/O throttling to mitigate.

**In containers:** Cgroups limit CPU, memory, and I/O per container. However, containers share the kernel, which means kernel-level resources (kernel threads, network stack, filesystem cache) can still be contended. The shared kernel makes the noisy neighbour problem slightly worse than with VMs.

**Mitigation strategies:**
- Set CPU and memory **limits** (not just requests) in Kubernetes.
- Use **resource quotas** per namespace.
- Use **dedicated node pools** for sensitive workloads.
- Use **VM-level isolation** for strong tenant boundaries, containers within VMs for app isolation.

---

### Q11. Explain the Docker container lifecycle with commands.

**Answer:**

```
Image (blueprint) → Container (running instance) → Stopped → Removed

docker build -t myapp .              # Build image from Dockerfile
docker images                        # List local images
docker push registry.com/myapp:v1    # Push image to registry
docker pull registry.com/myapp:v1    # Pull image from registry

docker run -d -p 8000:8000 myapp     # Create + start container
docker ps                            # List running containers
docker ps -a                         # List all containers (including stopped)
docker logs <id>                     # View container stdout/stderr
docker exec -it <id> /bin/bash       # Open shell inside running container

docker stop <id>                     # Stop container (SIGTERM)
docker start <id>                    # Restart stopped container
docker rm <id>                       # Remove stopped container
docker rmi myapp                     # Remove image
```

**Key flags:**
- `-d` = detached (background)
- `-p 8000:8000` = map host port 8000 to container port 8000
- `-v mydata:/app/data` = mount volume
- `-e KEY=VALUE` = set environment variable
- `--name mycontainer` = assign a name
- `--rm` = auto-remove container when it exits

---

### Q12. What is the 12-Factor App methodology? List at least 6 factors.

**Answer:**

The **12-Factor App** is a methodology for building modern, cloud-native SaaS applications that are portable, scalable, and maintainable.

| Factor | Principle | Why it matters for cloud |
|---|---|---|
| **1. Codebase** | One codebase in version control, many deploys (dev, staging, prod). | Consistency across environments. |
| **2. Dependencies** | Explicitly declare and isolate all dependencies (no system-wide packages). | Containers bundle all dependencies — no "works on my machine." |
| **3. Config** | Store config in environment variables, NOT in code. | Same image deployed to dev/staging/prod with different env vars. |
| **4. Backing services** | Treat databases, caches, queues as attached resources swappable via config. | Switch from local PostgreSQL to RDS by changing a URL. |
| **5. Build, release, run** | Strictly separate build (compile), release (build + config), and run (execute) stages. | Enables reproducible deployments and rollbacks. |
| **6. Processes** | Run the app as one or more stateless processes. | Horizontal scaling — any instance can handle any request. |
| **7. Port binding** | Export services via port binding (the app is self-contained, not deployed into a server). | Containers expose ports — no external web server needed. |
| **8. Concurrency** | Scale out via the process model (more instances, not bigger instances). | Cloud-native horizontal scaling. |
| **9. Disposability** | Fast startup, graceful shutdown. Processes are disposable. | Containers start in milliseconds, can be killed and replaced. |
| **10. Dev/prod parity** | Keep dev, staging, and production as similar as possible. | Containers ensure identical environments everywhere. |
| **11. Logs** | Treat logs as event streams (write to stdout, not files). | Container runtime captures stdout; shipped to log aggregation (ELK, Splunk). |
| **12. Admin processes** | Run admin/management tasks as one-off processes. | Use `docker exec` or Kubernetes Jobs for migrations, scripts. |

---

*End of Topic 2 Questions & Answers*

---

*End of CS6 Questions & Answers*
