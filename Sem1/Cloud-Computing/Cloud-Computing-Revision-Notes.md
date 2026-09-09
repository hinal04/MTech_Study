# Cloud Computing — Quick Revision Notes
> BITS Pilani | CS1-CS6 | Last-Minute Reference

---

## CS1: Introduction to Cloud Computing

### NIST Definition
- Cloud computing = on-demand network access to a shared pool of configurable computing resources (networks, servers, storage, applications, services) that can be rapidly provisioned and released with minimal management effort

### 5 Essential Characteristics (NIST)

| # | Characteristic | One-Liner |
|---|---------------|-----------|
| 1 | **On-demand self-service** | Provision resources automatically without human interaction |
| 2 | **Broad network access** | Access via standard mechanisms (mobile, laptop, workstation) |
| 3 | **Resource pooling** | Provider resources pooled using multi-tenant model; location independence |
| 4 | **Rapid elasticity** | Scale out/in quickly; appears unlimited to consumer |
| 5 | **Measured service** | Pay-per-use; resource usage monitored, controlled, reported |

### 3 Service Models — Who Manages What

| Layer | IaaS | PaaS | SaaS |
|-------|------|------|------|
| Application | You | You | Provider |
| Data | You | You | Provider |
| Runtime | You | Provider | Provider |
| Middleware | You | Provider | Provider |
| OS | You | Provider | Provider |
| Virtualization | Provider | Provider | Provider |
| Servers | Provider | Provider | Provider |
| Storage | Provider | Provider | Provider |
| Networking | Provider | Provider | Provider |

- **IaaS** — VMs, storage, networks (e.g., AWS EC2, Azure VMs)
- **PaaS** — Platform + runtime, you deploy code (e.g., Heroku, AWS Elastic Beanstalk)
- **SaaS** — Ready-to-use software (e.g., Gmail, Salesforce, Zoom)

### 5 Deployment Models

| Model | Description | Pros | Cons |
|-------|------------|------|------|
| **Public** | Resources owned by third-party, shared across tenants | Low cost, scalable, no maintenance | Less control, security concerns |
| **Private** | Dedicated infra for single organization | Full control, security, compliance | Expensive, limited scalability |
| **Hybrid** | Mix of public + private | Flexibility, burst capacity | Complex management |
| **Community** | Shared by organizations with common concerns | Cost sharing, domain-specific | Limited scalability |
| **Multi-cloud** | Using services from multiple cloud providers | No vendor lock-in, best-of-breed | Integration complexity |

### Cloud Advantages
- **Scalability** — scale up/down based on demand
- **Cost efficiency** — pay-as-you-go, no upfront CapEx
- **Elasticity** — automatic resource adjustment
- **Global reach** — deploy worldwide in minutes
- **Agility** — rapid provisioning, faster time-to-market
- **Reliability** — built-in redundancy and failover

### Cloud Challenges
- **Security** — shared responsibility, data breaches
- **Vendor lock-in** — migration difficulty between providers
- **Downtime** — dependent on provider's SLA
- **Compliance** — regulatory requirements (GDPR, HIPAA)
- **Data sovereignty** — data must reside in specific jurisdictions
- **Latency** — network delays for distant data centers

### Cloud vs Web Application
- **Web app** — software accessed via browser; may run on single server
- **Cloud app** — leverages cloud infrastructure; inherently scalable, elastic, distributed
- All cloud apps are web-accessible, but not all web apps are cloud-native

---

## CS2-CS3: Virtualization

### Core Concepts
- **Virtualization** = creating virtual versions of physical resources (servers, storage, networks)
- Enables cloud computing by allowing resource sharing, isolation, and efficient utilization
- **VM** = a software emulation of a physical computer; runs its own OS and apps

### Popek-Goldberg Criteria
- A VMM (Virtual Machine Monitor) must satisfy:
  1. **Equivalence/Fidelity** — program behaves identically in VM as on bare metal
  2. **Resource control/Safety** — VMM controls all hardware resources
  3. **Efficiency/Performance** — majority of instructions execute directly on hardware

### System VM vs Process VM

| Feature | System VM | Process VM |
|---------|-----------|------------|
| Virtualizes | Entire machine (hardware) | Single process/application |
| OS | Runs full guest OS | Shares host OS |
| Example | VMware, VirtualBox, KVM | JVM, .NET CLR, Wine |
| Use case | Server consolidation | Cross-platform app execution |

### Hypervisor Types — Comparison Table

| Feature | Type 1 (Bare-metal) | Type 2 (Hosted) | Hybrid |
|---------|---------------------|-----------------|--------|
| Runs on | Directly on hardware | On top of host OS | Blend of both |
| Performance | High (near-native) | Lower (extra OS layer) | Medium-High |
| Examples | VMware ESXi, Xen, Hyper-V, KVM | VirtualBox, VMware Workstation | KVM (kernel module) |
| Use case | Data centers, production | Development, testing | Flexible deployments |

### 4 Virtualization Types

| Type | Mechanism | Guest OS Modified? | Performance |
|------|-----------|-------------------|-------------|
| **Full Virtualization** | Binary translation; traps & emulates privileged instructions | No | Moderate |
| **Para-virtualization** | Guest uses **hypercalls** to communicate with hypervisor | Yes (modified kernel) | Good |
| **Hardware-assisted** | CPU extensions (VT-x/AMD-V); hardware traps | No | Best |
| **OS-level (Containers)** | Shared kernel; namespace + cgroup isolation | N/A (no guest OS) | Near-native |

### x86 Virtualization Challenge
- x86 has 4 privilege rings: **Ring 0** (kernel) → **Ring 3** (user apps)
- Problem: 17 **sensitive-but-not-privileged** instructions — don't trap when executed in Ring 1+
- Violates Popek-Goldberg (not all sensitive instructions are privileged)
- **VMware's solution**: Ring deprivileging — guest OS runs in Ring 1 (or Ring 3), VMM in Ring 0
- Binary translation catches and rewrites problematic instructions at runtime

### VT-x / AMD-V (Hardware-Assisted Virtualization)
- Introduced **VMX root mode** (hypervisor) and **VMX non-root mode** (guest)
- Guest OS runs in Ring 0 of non-root mode — no binary translation needed
- **VM Entry** = transition from root → non-root; **VM Exit** = non-root → root
- **EPT (Extended Page Tables) / SLAT** = hardware-managed two-level page translation (guest virtual → guest physical → host physical)
- Eliminates need for shadow page tables

### Emulation vs Virtualization

| Aspect | Emulation | Virtualization |
|--------|-----------|---------------|
| ISA | Cross-ISA (different architecture) | Same ISA |
| Method | Software interprets every instruction | Direct execution + trap-and-emulate |
| Speed | Slow (10-100x overhead) | Near-native |
| Example | QEMU (ARM on x86), BOCHS | VMware, KVM, Xen |

### Virtualization Types — One-Liners
- **Server virtualization** — multiple VMs on one physical server (consolidation)
- **Storage virtualization** — abstract physical storage into logical pools
- **Network virtualization** — create virtual networks (VLANs, VPNs, SDN) decoupled from physical
- **Memory virtualization** — abstract physical memory; each VM sees contiguous address space
- **Device/I/O virtualization** — share physical devices (GPU, NIC) across VMs; SR-IOV

### Storage Virtualization

| Type | Description | Example |
|------|-------------|---------|
| **Host-based** | Software on host OS manages storage | LVM, software RAID |
| **Device-based** | Storage array controller manages | EMC, NetApp |
| **Network-based** | Appliance in SAN fabric | SAN switches, SVC |

- **In-band** — data and control flow through same path (inline appliance)
- **Out-of-band** — control plane separated from data path (metadata server)

### CPU Overcommitment
- **vCPU ratio** = total vCPUs allocated / physical CPU cores
- **CPU Ready Time** = time VM is ready to run but waiting for physical CPU
- Safe ratios: 3:1 to 5:1 (depends on workload)
- High CPU Ready (>5%) = overcommitted, VM starvation

### Memory Overcommitment — 4 Techniques (in order of activation)

| Priority | Technique | How It Works |
|----------|-----------|-------------|
| 1st | **TPS (Transparent Page Sharing)** | Deduplicate identical memory pages across VMs (CoW) |
| 2nd | **Ballooning** | Balloon driver in guest reclaims unused pages |
| 3rd | **Compression** | Compress pages before swapping |
| 4th | **Swapping** | Hypervisor swaps VM pages to disk (last resort, slow) |

### VM Benefits vs Limitations

| Benefits | Limitations |
|----------|------------|
| Server consolidation, fewer physical servers | Performance overhead (hypervisor layer) |
| Isolation between workloads | Resource contention when overcommitted |
| Snapshot, clone, live migration | VM sprawl (unmanaged proliferation) |
| Hardware independence | Full OS per VM = heavy (GBs of RAM) |
| Disaster recovery, HA | Licensing complexity |

---

## CS4: Hypervisor & IaaS

### Hypervisor Goals
- **Isolation** — VMs don't interfere with each other
- **Encapsulation** — VM state captured as files (snapshots, migration)
- **Interposition** — hypervisor intercepts and mediates all resource access
- **Performance** — minimal overhead vs bare metal

### IaaS Architecture
- **Provider** — owns/manages data centers, physical infra, virtualization layer
- **Consumer** — provisions VMs, storage, networks via API/console
- Consumer manages: OS, middleware, runtime, apps, data
- Provider manages: hardware, hypervisor, networking fabric

### AWS Global Infrastructure

| Concept | Description |
|---------|-------------|
| **Region** | Geographic area with 2+ AZs (e.g., us-east-1) |
| **Availability Zone (AZ)** | One or more discrete data centers with redundant power/networking |
| **Local Zone** | Extension of a region closer to end users (low latency) |
| **Wavelength Zone** | AWS infra embedded in telecom 5G networks (ultra-low latency) |
| **Outposts** | AWS hardware installed on-premises for hybrid |

### IAM (Identity and Access Management)
- **Authentication** — proving identity (who are you?) — username/password, MFA
- **Authorization** — what can you do? — policies define permissions
- **Users** — individual identities
- **Groups** — collection of users; attach policies to groups
- **Roles** — temporary credentials for services/cross-account access (no permanent keys)
- **Policies** — JSON documents defining Allow/Deny on resources
  ```
  { "Effect": "Allow", "Action": "s3:GetObject", "Resource": "arn:aws:s3:::bucket/*" }
  ```
- **Best Practices**: root account MFA, least privilege, use roles over access keys, rotate credentials, enable CloudTrail

### BOCHS Emulator
- Open-source x86 PC emulator (full system emulation)
- Emulates CPU instruction-by-instruction (very slow but highly portable)
- Used for OS development, debugging, education
- Cross-ISA: can run x86 OS on non-x86 hardware

---

## CS5: IaaS Services (AWS)

### EC2 Instance Types

| Family | Optimized For | Example Use Case |
|--------|--------------|-----------------|
| **General Purpose** (M, T) | Balanced compute/memory/network | Web servers, small DBs |
| **Compute Optimized** (C) | High-performance processors | Batch processing, ML inference |
| **Memory Optimized** (R, X) | Large in-memory datasets | In-memory DBs, real-time analytics |
| **Storage Optimized** (I, D) | High sequential read/write to local storage | Data warehousing, Hadoop |
| **Accelerated** (P, G, Inf) | GPU/FPGA hardware accelerators | ML training, video encoding |

### AMI Types (4)
1. **Amazon-provided** — official AWS AMIs (Amazon Linux, Ubuntu)
2. **Marketplace** — third-party vendor AMIs
3. **Community** — shared by other AWS users
4. **Custom/Private** — your own AMIs

### EC2 Lifecycle
```
Launch → [Pending] → Running ⇄ Stopped → [Shutting-down] → Terminated
                        ↕
                    Rebooting
```
- **Pending** — instance launching
- **Running** — active, billing starts (on-demand)
- **Stopped** — EBS-backed only; no compute charges, EBS charges continue
- **Terminated** — instance deleted; EBS root volume deleted (default)

### EC2 Tenancy

| Type | Description | Cost |
|------|-------------|------|
| **Shared (default)** | Multiple customers on same physical host | Lowest |
| **Dedicated Instance** | Your instances on dedicated hardware; may share with your other instances | Higher |
| **Dedicated Host** | Entire physical server for you; socket/core visibility | Highest (licensing control) |

### Placement Groups
- **Cluster** — instances in same rack; low latency, high throughput (HPC)
- **Spread** — instances on distinct hardware; max 7 per AZ; high availability
- **Partition** — groups of instances on separate racks; large distributed workloads (Hadoop, Kafka)

### VPC (Virtual Private Cloud)
- **VPC** — your logically isolated network in AWS
- **Subnet** — segment of VPC; public (internet-facing) or private
- **Internet Gateway (IGW)** — connects VPC to internet
- **NAT Gateway** — allows private subnet instances to access internet (outbound only)
- **Route Table** — rules determining where network traffic is directed
- **Security Groups** — instance-level firewall; **stateful**, allow rules only, default deny inbound
- **NACLs** — subnet-level firewall; **stateless**, allow + deny rules, processed in order

| Feature | Security Groups | NACLs |
|---------|----------------|-------|
| Level | Instance | Subnet |
| Stateful? | Yes | No |
| Rules | Allow only | Allow + Deny |
| Evaluation | All rules evaluated | Rules processed in order |

### AWS Storage Services

#### S3 (Simple Storage Service)
- **Object storage** — key-value (key = object name, value = data + metadata)
- **Buckets** — containers for objects; globally unique name
- **11 nines (99.999999999%) durability**; 99.99% availability
- Storage classes: Standard, IA, One Zone-IA, Glacier, Glacier Deep Archive
- Unlimited storage; max object size = 5 TB

#### EBS (Elastic Block Store)
- **Block storage** — attached to single EC2 instance (same AZ)
- Persistent; survives instance stop (not termination by default)
- Types:

| Type | Category | Use Case |
|------|----------|----------|
| **gp3/gp2** | General Purpose SSD | Boot volumes, dev/test |
| **io2/io1** | Provisioned IOPS SSD | Databases, latency-sensitive |
| **st1** | Throughput Optimized HDD | Big data, log processing |
| **sc1** | Cold HDD | Infrequent access, lowest cost |

#### EFS (Elastic File System)
- **File storage** (NFS protocol) — shared across multiple EC2 instances
- Auto-scales; pay for what you use
- Regional service; accessible from any AZ

#### Glacier
- **Archival storage** — very cheap; retrieval takes minutes to hours
- Glacier Instant (ms), Flexible (1-5 hrs), Deep Archive (12-48 hrs)
- For compliance archives, backups, rarely accessed data

### EBS vs EFS vs S3 Comparison

| Feature | EBS | EFS | S3 |
|---------|-----|-----|-----|
| Type | Block | File (NFS) | Object |
| Access | Single EC2 (same AZ) | Multiple EC2 (cross-AZ) | Anywhere (HTTP) |
| Performance | Highest IOPS | Good throughput | High throughput |
| Scalability | Fixed size (manual resize) | Auto-scaling | Unlimited |
| Use case | Databases, boot volumes | Shared files, CMS | Static assets, backups, data lakes |
| Durability | 99.999% (within AZ) | 99.999999999% | 99.999999999% |

### RDS (Relational Database Service)
- Managed relational DB: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora
- Automated backups, patching, scaling, Multi-AZ failover
- Read replicas for read scaling

### Zomato Case Study
- Migrated to AWS **Graviton2** (ARM) processors — better price-performance
- Used **Spot Instances** for non-critical workloads
- Result: **30% cost reduction** while improving performance
- Key takeaway: right instance type + pricing model = significant savings

---

## CS6: Containers

### Container Motivation
- **"Works on my machine"** problem — inconsistent environments
- VMs are heavy: each needs full OS, GBs of RAM, minutes to boot
- Containers: share host kernel, lightweight (MBs), start in seconds

### Container vs VM Comparison

| Feature | Container | VM |
|---------|-----------|-----|
| OS | Shares host kernel | Full guest OS |
| Size | MBs | GBs |
| Boot time | Seconds | Minutes |
| Isolation | Process-level (weaker) | Hardware-level (stronger) |
| Density | 100s per host | 10s per host |
| Portability | Very high (image-based) | Lower (hypervisor-dependent) |
| Overhead | Minimal | Significant |
| Use case | Microservices, CI/CD | Legacy apps, multi-OS |

### Linux Kernel Features for Containers

| Feature | Purpose |
|---------|---------|
| **Namespaces** | Isolation — each container gets its own PID, network, mount, UTS, IPC, user namespace |
| **cgroups** | Resource limits — CPU, memory, I/O, network bandwidth caps per container |

### LXC / LXD
- **LXC** — Linux Containers; OS-level virtualization using namespaces + cgroups
- **LXD** — next-gen system container manager (REST API, image-based)
- **System containers** = lightweight VMs (run full OS userspace, init system)
- **Application containers** = single process/app (Docker model)

| Type | System Container (LXC/LXD) | Application Container (Docker) |
|------|---------------------------|-------------------------------|
| Init system | Yes (systemd) | No (single process) |
| Multi-process | Yes | No (one process per container) |
| Feels like | Lightweight VM | Isolated application |
| Use case | Replace VMs | Microservices deployment |

### Docker Architecture
- **Docker Daemon (dockerd)** — background service managing containers
- **Docker Client (CLI)** — user interface; sends commands to daemon
- **Docker Registry** — stores images (Docker Hub, ECR, private registries)
- Client → (REST API) → Daemon → (pull/push) → Registry

### Docker Objects
- **Image** — read-only template; built in layers; defined by Dockerfile
- **Container** — running instance of an image; writable layer on top
- **Volume** — persistent storage; survives container removal
- **Network** — communication between containers

### Dockerfile Key Instructions

| Instruction | Purpose |
|------------|---------|
| `FROM` | Base image (e.g., `FROM python:3.11-slim`) |
| `RUN` | Execute command during build (e.g., `RUN pip install flask`) |
| `COPY` / `ADD` | Copy files from host to image |
| `CMD` | Default command when container starts (overridable) |
| `ENTRYPOINT` | Fixed command (not easily overridden) |
| `EXPOSE` | Document which port the container listens on |
| `WORKDIR` | Set working directory inside container |
| `ENV` | Set environment variables |

**Best Practices**: use small base images (alpine/slim), minimize layers, use `.dockerignore`, don't run as root, multi-stage builds

### Docker Lifecycle
```
Dockerfile → [docker build] → Image → [docker pull/push] → Registry
                                  ↓
                          [docker run] → Container (running)
                                  ↓
                          [docker stop] → Container (stopped)
                                  ↓
                          [docker rm] → Removed
```

### Container Orchestration
- **Why needed**: managing 100s-1000s of containers manually is impossible
- Need: auto-scaling, load balancing, self-healing, rolling updates, service discovery
- **Kubernetes (K8s)**: industry standard orchestrator
  - **Pod** — smallest deployable unit (1+ containers)
  - **Node** — worker machine running pods
  - **Cluster** — set of nodes managed by control plane
  - **Service** — stable network endpoint for pods
  - **Deployment** — declarative desired state for pods

### Imperative vs Declarative

| Imperative | Declarative |
|-----------|-------------|
| "Run this container on port 8080" | "I want 3 replicas on port 8080" |
| Step-by-step commands | Desired end-state (YAML) |
| `kubectl run`, `kubectl create` | `kubectl apply -f deployment.yaml` |
| Hard to reproduce | Version-controlled, reproducible |

### Cloud-Native Principles
- **Microservices** — small, independently deployable services
- **Declarative deployment** — infrastructure as code, desired state
- **Portability** — runs on any cloud (containerized)
- **Resilience** — designed for failure; self-healing
- **Observability** — logging, monitoring, tracing built-in
- **CI/CD** — automated build, test, deploy pipelines

---

## Quick-Fire Formulas & Numbers
- S3 durability: **11 nines (99.999999999%)**
- EC2 Spot savings: up to **90%** vs on-demand
- CPU Ready Time warning threshold: **> 5%**
- Safe vCPU overcommit ratio: **3:1 to 5:1**
- x86 problematic instructions: **17 sensitive-but-not-privileged**
- Popek-Goldberg conditions: **3 (equivalence, resource control, efficiency)**
- Memory overcommit order: **TPS → Ballooning → Compression → Swapping**
- VT-x modes: **VMX root (hypervisor) + VMX non-root (guest)**
- Containers boot: **seconds** | VMs boot: **minutes**
- Zomato savings: **30% cost reduction** (Graviton2 + Spot)

---

*Good luck with the exam!*
