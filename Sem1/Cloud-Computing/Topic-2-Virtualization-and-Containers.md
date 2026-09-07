# Topic 2: Virtualization and Containers

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Ch.9
> - T2: Erl, Puttini & Mahmood, *Cloud Computing*, Ch.5
> - R3: VMware Virtualization Documentation
> - R6: Docker Documentation
> - R7: Kubernetes Documentation
>
> **Contact Hours:** 6 (Lectures 5-12)

---

## Table of Contents

- [2.1 Introduction to Virtualization](#21-introduction-to-virtualization)
- [2.2 Benefits and Limitations of Virtualization](#22-benefits-and-limitations-of-virtualization)
- [2.3 Types of Virtualization](#23-types-of-virtualization)
- [2.4 x86 Hardware Virtualization](#24-x86-hardware-virtualization)
- [2.5 Resource Management for SaaS, PaaS, and IaaS Models](#25-resource-management-for-saas-paas-and-iaas-models)
- [2.6 Containers and Containerization Concepts](#26-containers-and-containerization-concepts)
- [2.7 Docker: Images, Dockerfiles, Containers, Registries, and Volumes](#27-docker)
- [2.8 Namespaces and Cgroups](#28-namespaces-and-cgroups)
- [2.9 System Containers and Application Containers](#29-system-containers-and-application-containers)
- [2.10 Virtual Machines vs Containers](#210-virtual-machines-vs-containers)
- [2.11 Container Orchestration: Kubernetes Overview](#211-container-orchestration-kubernetes-overview)
- [2.12 Cloud-Native Design Principles](#212-cloud-native-design-principles)

---

## 2.1 Introduction to Virtualization

### What is Virtualization?

**Virtualization** is the creation of a software-based (virtual) representation of something physical — a server, a storage device, a network, or even an entire operating system. Instead of running one OS on one physical machine, virtualization allows multiple virtual machines (VMs) to share a single physical machine, each running its own OS and applications in isolation.

At its core, virtualization inserts a layer of software called a **hypervisor** (or Virtual Machine Monitor — VMM) between the physical hardware and the operating systems. The hypervisor manages the physical resources (CPU, memory, disk, network) and presents each VM with the illusion that it has its own dedicated hardware.

### Why Virtualization Matters for Cloud Computing

Virtualization is the **foundational technology** that makes cloud computing possible. Without it, cloud computing would simply be "renting physical servers" — no multi-tenancy, no elasticity, no resource pooling.

Specifically, virtualization enables:

1. **Server consolidation** — Instead of running one application per physical server (with typical utilization of 10-15%), you run 10-50 VMs on one physical server, achieving 60-80% utilization. This is how cloud providers make economics work.

2. **Isolation** — Each VM is a completely isolated sandbox. One VM's crash, security breach, or resource spike doesn't affect other VMs on the same host. This is what makes safe multi-tenancy possible.

3. **Hardware abstraction** — VMs interact with virtual hardware, not physical hardware. The hypervisor can migrate a VM from one physical server to another (live migration) without the VM or its users noticing. This enables maintenance without downtime.

4. **Rapid provisioning** — Creating a new VM takes seconds (it's just creating a software configuration and allocating virtual resources). Creating a physical server takes weeks (procurement, shipping, racking, cabling, OS installation).

5. **Snapshotting and cloning** — The entire state of a VM (memory, disk, configuration) can be captured as a snapshot and restored later. VMs can be cloned to create identical copies instantly.

### The Hypervisor (Virtual Machine Monitor)

The hypervisor is the software layer that creates and manages virtual machines. It sits between the physical hardware and the guest operating systems, mediating all access to hardware resources.

**Two types of hypervisors:**

| Type | Where it runs | How it works | Examples |
|---|---|---|---|
| **Type 1 (Bare-metal)** | Directly on the physical hardware, replacing the host OS. | The hypervisor IS the operating system. It has direct access to hardware, providing the best performance and lowest overhead. | VMware ESXi, Microsoft Hyper-V, Xen, KVM (Linux) |
| **Type 2 (Hosted)** | On top of an existing host operating system, as an application. | The hypervisor runs as a user-level application on a conventional OS. The host OS mediates hardware access, adding overhead. | VMware Workstation, Oracle VirtualBox, Parallels Desktop |

```
Type 1 (Bare-metal):                Type 2 (Hosted):

┌───────┐ ┌───────┐ ┌───────┐      ┌───────┐ ┌───────┐
│ VM 1  │ │ VM 2  │ │ VM 3  │      │ VM 1  │ │ VM 2  │
│(Guest │ │(Guest │ │(Guest │      │(Guest │ │(Guest │
│  OS)  │ │  OS)  │ │  OS)  │      │  OS)  │ │  OS)  │
├───────┴─┴───────┴─┴───────┤      ├───────┴─┴───────┤
│       Hypervisor           │      │    Hypervisor     │
├────────────────────────────┤      ├───────────────────┤
│    Physical Hardware       │      │   Host OS (Linux, │
└────────────────────────────┘      │   Windows, macOS) │
                                    ├───────────────────┤
                                    │ Physical Hardware  │
                                    └───────────────────┘
```

**In cloud computing, Type 1 hypervisors are used exclusively** because they provide better performance, security, and resource efficiency. Type 2 hypervisors are used primarily for development and testing on personal machines.

---

## 2.2 Benefits and Limitations of Virtualization

### Benefits

| Benefit | Explanation |
|---|---|
| **Server consolidation** | Run multiple VMs on one physical server. Reduce the number of physical servers from hundreds to tens. A typical physical server runs at 10-15% utilization; with virtualization, you achieve 60-80%. |
| **Cost reduction** | Fewer physical servers means less hardware to buy, less power to consume, less cooling, less data centre space, fewer staff to manage. |
| **Isolation** | Each VM is a fully isolated sandbox with its own OS, kernel, libraries, and applications. A crash, vulnerability, or misconfiguration in one VM does not affect others. |
| **Hardware independence** | VMs are abstracted from physical hardware. Move a VM from an Intel server to an AMD server — the VM doesn't know or care. This enables live migration and hardware upgrades without downtime. |
| **Rapid provisioning** | Create a new VM in seconds from a template or snapshot. No physical procurement cycle. |
| **Snapshot and rollback** | Capture the complete state of a VM at any point. If an update fails, roll back to the snapshot instantly. |
| **Disaster recovery** | VM images can be replicated to a remote site. In a disaster, VMs are restarted from the replicated images — much faster than rebuilding physical servers. |
| **Testing and development** | Create isolated test environments that mirror production. Test patches, upgrades, and new software without risk to production systems. Destroy and recreate environments in minutes. |
| **Legacy support** | Run legacy applications that require old OS versions (Windows XP, RHEL 5) on modern hardware by hosting them in VMs with the required OS. |

### Limitations

| Limitation | Explanation |
|---|---|
| **Performance overhead** | The hypervisor layer adds CPU, memory, and I/O overhead. A VM will always be slightly slower than running the same workload directly on physical hardware (bare metal). Typical overhead: 2-10% for CPU, more for I/O-intensive workloads. |
| **Resource contention** | Multiple VMs sharing one physical host compete for CPU, memory, disk I/O, and network bandwidth. One VM's spike can affect others — the "noisy neighbour" problem. |
| **Complexity** | Managing a virtualized environment (hypervisor configuration, VM lifecycle, storage management, networking) adds operational complexity. |
| **Single point of failure** | If the physical host running multiple VMs crashes, ALL VMs on that host go down simultaneously. Mitigation: high availability clusters that automatically restart VMs on other hosts. |
| **Licensing costs** | Some hypervisors (VMware vSphere) and guest OS licenses (Windows Server) can be expensive. Per-socket, per-VM, or per-core licensing models add cost. |
| **Security surface** | The hypervisor itself becomes a critical attack surface. A vulnerability in the hypervisor (VM escape) could compromise all VMs on the host. Although rare, this is a serious threat. |
| **Storage overhead** | Each VM has its own full OS installation (often several GB), consuming storage even for identical OS copies. 50 VMs with the same OS = 50 separate OS copies on disk. |

---

## 2.3 Types of Virtualization

Virtualization can be implemented using several techniques, each with different trade-offs between performance, compatibility, and complexity.

### 2.3.1 Full Virtualization

In full virtualization, the hypervisor creates a **complete simulation** of the underlying hardware. The guest OS runs completely unmodified — it doesn't know it's running in a virtual machine. The hypervisor intercepts all hardware-access instructions from the guest OS and translates them into operations on the actual physical hardware.

**How it works:** The guest OS issues privileged instructions (e.g. accessing hardware registers, managing memory pages). The hypervisor traps these instructions (since the guest doesn't actually have hardware access) and emulates them in software.

**Technique: Binary Translation** — The hypervisor scans the guest OS's instruction stream, identifies privileged instructions, and replaces them with equivalent safe instructions at runtime. This is done transparently — the guest OS doesn't know its instructions are being modified.

**Pros:** Guest OS runs completely unmodified. Any OS that runs on the physical hardware can run as a guest.
**Cons:** Performance overhead from trapping and translating privileged instructions. Binary translation is computationally expensive.

**Examples:** VMware Workstation (early versions), QEMU (without KVM).

### 2.3.2 Para-Virtualization

In para-virtualization, the guest OS is **modified** to be aware that it's running in a virtual machine. Instead of issuing privileged instructions that must be trapped and emulated, the guest OS makes explicit **hypercalls** — direct calls to the hypervisor's API.

**How it works:** The guest OS kernel is modified to replace privileged instructions with hypercalls. When the guest needs to access hardware (e.g. write to disk), it calls the hypervisor directly through the hypercall interface, bypassing the trap-and-emulate overhead.

**Pros:** Better performance than full virtualization — no binary translation overhead. Hypercalls are more efficient than trap-and-emulate.
**Cons:** Requires modifying the guest OS kernel. Cannot run unmodified proprietary OS (e.g. Windows) unless the vendor provides a para-virtualized version. Tight coupling between guest and hypervisor.

**Examples:** Xen (with para-virtualized guests), early versions of VMware Tools.

### 2.3.3 Hardware-Assisted Virtualization

Modern CPUs from Intel (**VT-x**) and AMD (**AMD-V**) include hardware extensions specifically designed to support virtualization. These extensions add a new privilege level (root mode) below the OS, allowing the hypervisor to run at this level while the guest OS runs at its normal privilege level.

**How it works:** The CPU provides hardware support for trapping privileged instructions from the guest OS and redirecting them to the hypervisor — without binary translation or guest modification. The guest OS runs unmodified at its normal privilege level, and the CPU hardware handles the privilege boundary.

**Pros:** Best of both worlds — runs unmodified guest OS (like full virtualization) with near-native performance (no binary translation overhead). The hardware does the heavy lifting.
**Cons:** Requires CPU support (virtually all modern CPUs have it since ~2006). Early implementations had performance issues, but modern hardware-assisted virtualization is excellent.

**Examples:** KVM (Linux), VMware ESXi (modern), Microsoft Hyper-V, Xen (with HVM guests). This is **the dominant approach in cloud computing today**.

### 2.3.4 OS-Level Virtualization (Containerization)

OS-level virtualization creates multiple isolated **user-space instances** (containers) on a single OS kernel. Unlike VMs, containers do **not** run separate kernels — they all share the host's kernel.

**How it works:** The host OS kernel uses isolation mechanisms (namespaces for process/network/filesystem isolation, cgroups for resource limits) to create containers that appear to be independent systems but are actually processes running on the same kernel.

**Pros:** Extremely lightweight — no separate kernel per container. Start in milliseconds (vs. seconds/minutes for VMs). Much less resource overhead. Higher density (hundreds of containers per host vs. tens of VMs).
**Cons:** All containers share the same kernel — a kernel vulnerability affects all containers. Less isolation than VMs. Can only run the same OS family as the host (Linux containers on Linux host).

**Examples:** Docker, LXC, Podman, containerd.

### Comparison of Virtualization Types

| Aspect | Full Virtualization | Para-Virtualization | Hardware-Assisted | OS-Level (Containers) |
|---|---|---|---|---|
| **Guest OS modification** | None | Required | None | N/A (shares host kernel) |
| **Performance** | Lower (binary translation) | Good (hypercalls) | Near-native | Native |
| **Isolation** | Strong (separate kernels) | Strong | Strong | Moderate (shared kernel) |
| **Boot time** | Seconds to minutes | Seconds to minutes | Seconds to minutes | Milliseconds |
| **Resource overhead** | High (full OS per VM) | Medium | Medium | Very low |
| **Density** | 10-50 per host | 10-50 per host | 10-50 per host | 100-1000+ per host |
| **Example** | QEMU | Xen (PV) | KVM, ESXi, Hyper-V | Docker, LXC |

---

## 2.4 x86 Hardware Virtualization

### The x86 Virtualization Challenge

The x86 processor architecture (used in virtually all servers and PCs) was not originally designed for virtualization. The x86 has four privilege levels called **rings** (Ring 0 to Ring 3):

- **Ring 0:** Highest privilege — the OS kernel runs here. Direct access to hardware.
- **Ring 1-2:** Rarely used.
- **Ring 3:** Lowest privilege — user applications run here. No direct hardware access.

**The problem:** In a virtualized environment, both the hypervisor and the guest OS kernel want to run at Ring 0. But only one entity can have Ring 0 privileges on a physical CPU. If the guest OS runs at Ring 0, it has direct hardware access and can bypass the hypervisor — breaking isolation. If the guest runs at a lower ring, some of its privileged instructions silently fail instead of trapping to the hypervisor.

### Solutions

**1. Binary Translation (Software Solution):**
The hypervisor runs at Ring 0. The guest OS kernel is moved to Ring 1. The hypervisor scans the guest's instruction stream and replaces problematic privileged instructions with safe equivalents that trap to the hypervisor. This works but is complex and has overhead.

**2. Intel VT-x / AMD-V (Hardware Solution):**
Intel and AMD added new CPU modes:
- **VMX root mode** — the hypervisor runs here (below Ring 0).
- **VMX non-root mode** — the guest OS runs here at Ring 0.

When the guest executes a privileged instruction in non-root mode, the CPU automatically traps to the hypervisor in root mode (a **VM exit**). The hypervisor handles the operation and returns control to the guest (a **VM entry**). This is all done in hardware — no binary translation needed.

```
Traditional x86:                  With VT-x:

Ring 3: User Apps                 Non-root Ring 3: Guest Apps
Ring 0: OS Kernel                 Non-root Ring 0: Guest OS Kernel
                                  ─── VM Exit / VM Entry ───
                                  Root Ring 0: Hypervisor
                                  
Hardware                          Hardware (with VT-x extensions)
```

**3. Extended Page Tables (EPT) / Nested Paging:**
VT-x also includes hardware support for **nested page tables** (Intel EPT, AMD RVI). In a virtualized environment, there are two levels of memory address translation: guest virtual → guest physical → host physical. Without hardware support, the hypervisor must maintain complex shadow page tables. With EPT, the CPU handles both levels of translation in hardware, significantly improving memory-intensive workload performance.

### Second Level Address Translation (SLAT)

SLAT is the general term for hardware-assisted nested page tables. It eliminates the overhead of shadow page tables maintained by the hypervisor in software.

- **Intel:** Extended Page Tables (EPT)
- **AMD:** Rapid Virtualization Indexing (RVI) / Nested Page Tables (NPT)

**Impact:** SLAT reduces memory virtualization overhead by 20-50%, making hardware-assisted virtualization practical for memory-intensive workloads like databases and big data applications.

---

## 2.5 Resource Management for SaaS, PaaS, and IaaS Models

Each cloud service model requires different levels of resource management. The underlying virtualization layer provides the isolation and control needed for each.

### IaaS Resource Management

In IaaS, the customer gets **virtual machines** with allocated CPU, memory, storage, and network. The cloud provider must:

- **Allocate physical resources** to VMs efficiently (CPU scheduling, memory management).
- **Isolate tenants** — one tenant's VM must not access another's resources or data.
- **Handle overcommitment** — providers often allocate more virtual resources than physical capacity exists (e.g. 20 vCPUs on a 16-core host), relying on the statistical likelihood that not all VMs peak simultaneously.
- **Enable live migration** — move VMs between physical hosts for load balancing and maintenance.
- **Enforce SLAs** — guarantee minimum CPU, IOPS, and network bandwidth.

**Resource types managed:** vCPUs, RAM, disk (IOPS + capacity), network bandwidth.

### PaaS Resource Management

In PaaS, the provider manages the infrastructure AND the platform. Resource management is more abstracted:

- **Auto-scaling** — the platform automatically adds/removes instances based on load.
- **Container orchestration** — PaaS often uses containers internally (managed by Kubernetes or similar).
- **Resource quotas** — each application is assigned a resource budget (CPU, memory limits).
- **Multi-tenancy at the application level** — multiple customers' applications share the same platform runtime.

**Resource types managed:** Application instances, request concurrency, memory per instance, execution time.

### SaaS Resource Management

In SaaS, the provider manages everything. Resource management is completely invisible to the user:

- **Database-level multi-tenancy** — one database serves all tenants (with row-level isolation) or one database per tenant (with dedicated isolation).
- **Application-level scaling** — the SaaS provider scales the application tier (web servers, API servers) to handle user load.
- **Feature-based quotas** — users are limited by subscription tier (free: 5GB storage, pro: 100GB, enterprise: unlimited).

---

## 2.6 Containers and Containerization Concepts

### What is a Container?

A **container** is a lightweight, standalone, executable package that includes everything needed to run a piece of software: the application code, runtime, system tools, libraries, and settings. Containers share the host operating system's kernel but run in isolated user spaces.

Think of a container as a **lightweight, portable box** containing your application and all its dependencies. The box runs the same way on any machine — your laptop, a test server, or a production cloud instance — because it carries everything it needs inside.

### How Containers Differ from VMs

The fundamental difference is **what is virtualized**:

- **VMs** virtualize the **hardware** — each VM gets a virtual CPU, virtual memory, virtual disk, and runs its own full operating system kernel. This provides strong isolation but heavy overhead.
- **Containers** virtualize the **operating system** — each container gets its own isolated process space, file system, and network stack, but they all share the host's OS kernel. This provides lighter isolation but much less overhead.

### Why Containers?

The motivation for containers comes from the problems of traditional deployment:

**The "Works on My Machine" Problem:** A developer builds an application on their laptop (macOS, Python 3.9, specific library versions). When deployed to a production server (Ubuntu, Python 3.8, different library versions), it breaks. Containers solve this by packaging the application WITH its exact environment.

**The VM Overhead Problem:** If you have 100 microservices and run each in its own VM, that's 100 separate OS kernels consuming memory and disk. Containers run 100 services sharing one kernel — dramatically less overhead.

### Container Architecture

```
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│ App A  │ │ App B  │ │ App C  │ │ App D  │
│ + Libs │ │ + Libs │ │ + Libs │ │ + Libs │
├────────┴─┴────────┴─┴────────┴─┴────────┤
│           Container Runtime               │  (Docker, containerd)
├───────────────────────────────────────────┤
│           Host OS Kernel (Linux)          │  (shared by ALL containers)
├───────────────────────────────────────────┤
│           Physical / Virtual Hardware      │
└───────────────────────────────────────────┘
```

Each container is a **process** (or group of processes) running on the host OS, isolated using kernel features (namespaces and cgroups — see Section 2.8).

---

## 2.7 Docker

**Docker** is the most widely used containerization platform. It provides the tools to build, ship, and run containers.

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
