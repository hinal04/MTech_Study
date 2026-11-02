# Cloud Computing — Topics Coverage Map (CS1–CS6)

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
> Quick reference showing what each session covers — use this to check you haven't missed any topic.

---

## CS1: Introduction to Cloud Computing (54 slides)

| Section | Topics Covered |
|---|---|
| **Cloud Definition** | NIST definition, cloud as on-demand delivery of IT resources over internet with pay-as-you-go pricing |
| **Cloud Origins** | Evolution: Mainframe → Client-Server → Grid Computing → Utility Computing → Cloud Computing |
| **5 Essential Characteristics** | On-demand self-service (provision without human intervention), Broad network access (any device), Resource pooling (multi-tenant), Rapid elasticity (scale up/down), Measured service (pay-per-use) |
| **3 Service Models** | IaaS (VMs, storage — you manage OS+apps), PaaS (platform — you manage apps+data), SaaS (software — you just use it). "Who manages what" comparison. Pizza analogy (homemade/takeaway/dine-in). |
| **IaaS Details** | Provision computing, networking, storage. Consumer controls OS, applications. Examples: AWS EC2, Azure VMs, GCP Compute Engine |
| **PaaS Details** | Deploy applications without managing infrastructure. Consumer controls apps and data. Examples: Heroku, Google App Engine, AWS Elastic Beanstalk |
| **SaaS Details** | Use application via browser. Provider manages everything. Examples: Gmail, Salesforce, Microsoft 365 |
| **Which AAS?** | Decision guide based on control needs vs management overhead |
| **Who Manages What** | Comparison table: On-premises vs IaaS vs PaaS vs SaaS — for networking, storage, servers, OS, middleware, runtime, data, applications |
| **5 Deployment Models** | Public (shared, owned by provider), Private (dedicated to one org), Hybrid (combination), Community (shared by orgs with common concerns), Multi-cloud (multiple providers) |
| **Public Cloud** | Pros: scalability, cost-effective, no maintenance. Cons: less control, security concerns, vendor dependency. Examples: AWS, Azure, GCP |
| **Private Cloud** | Pros: full control, security, customization, compliance. Cons: expensive, limited scalability, maintenance burden |
| **Hybrid Cloud** | Combines public + private. Pros: flexibility, cost optimization, gradual migration. Cons: complex networking, security across boundaries |
| **Community Cloud** | Shared by organizations with shared requirements (e.g., healthcare, government). Pros: cost sharing, compliance. Cons: limited to community needs |
| **Multi-Cloud** | Using multiple cloud providers simultaneously. Pros: avoid vendor lock-in, best-of-breed services. Cons: management complexity, data transfer costs |
| **Deployment Models Comparison** | Advantages & disadvantages table for all 5 models |
| **Cloud Advantages** | Cost reduction (CapEx→OpEx), scalability, elasticity, global reach, reliability, speed of deployment, innovation focus |
| **Cloud vs Web Apps** | Cloud apps leverage cloud characteristics (elasticity, multi-tenancy). Web apps just run on web servers without necessarily using cloud features. |
| **Cloud Challenges** | Security concerns, vendor lock-in, downtime/outage risk, data sovereignty, compliance complexity, internet dependency, cost management, migration difficulty |
| **Cloud Failures** | Examples of major outages (AWS S3 2017, Azure AD outage, Google Cloud incidents) — lessons learned |
| **Points to Ponder** | How cloud changes IT operations, cloud economics, when NOT to use cloud |

---

## CS2-CS3: Virtualization (51 slides)

| Section | Topics Covered |
|---|---|
| **VM History** | IBM 1960s mainframes, Popek-Goldberg 1974 definition (efficiency, isolation, fidelity) |
| **VM Definition** | Software implementation of a machine, fully protected and isolated copy of physical machine |
| **VM Classification** | System VM (full OS, e.g., VMware) vs Process VM (single program, e.g., JVM, .NET CLR) |
| **VM Advantages** | Multiple OS co-exist, provisioning/HA/DR built-in, emulated hardware environments |
| **VM Disadvantages** | Less efficient (indirect hardware access), varying/unstable performance (noisy neighbour), separate malware protection |
| **Virtualization Definition** | Technique to abstract physical resources, provide virtual computing platform |
| **Virtualization Classification** | Server virtualization (enables IaaS) vs Storage virtualization (enables Storage-as-a-Service) |
| **Server Virtualization** | System virtualization (VMM between OS and hardware) vs Process virtualization (JVM/.NET above OS) |
| **Reasons for Server Virt** | Server consolidation, energy reduction, easier management, rapid provisioning, easy relocation |
| **Hypervisor Types** | Type 1/Native (bare-metal: ESXi, KVM, Hyper-V), Type 2/Hosted (app on OS: VirtualBox, Parallels), Hybrid (KVM — kernel module) — with architecture diagrams |
| **Full Virtualization** | Binary translation, guest OS unmodified, VMware's solution (ring deprivileging), pros/cons |
| **Para-Virtualization** | Guest OS modified with hypercalls, better performance, Xen, 2 approaches (recompile kernel, para-virt drivers) |
| **Hardware-Assisted** | Intel VT-x / AMD-V, VMX root/non-root mode, VM exit/entry, EPT/SLAT, dominant approach today |
| **OS-Level (Containers)** | Shared kernel, namespaces + cgroups, lightweight, Docker/LXC |
| **Virtualization Comparison** | 7-dimension table: guest modification, performance, isolation, boot time, overhead, density, examples |
| **x86 Challenge** | Ring 0-3, sensitive-but-not-privileged instructions, trap-and-emulate failure |
| **x86 Solutions** | Binary translation (VMware 1999), ring deprivileging, hardware extensions (VT-x 2006) |
| **EPT / SLAT** | Extended Page Tables (Intel), Nested Page Tables (AMD), 20-50% memory overhead reduction |
| **Emulation** | Cross-ISA capability, interpretation vs dynamic binary translation (DBT), QEMU TCG, Apple Rosetta 2 |
| **Emulation vs Virtualization** | Cross-ISA vs same-ISA, performance (10-100x overhead vs 2-10%), use cases |
| **Server Virtualization Benefits** | Partitioning, management (VM failure isolation), encapsulation (VM state as file) |
| **Storage Virtualization** | Logical abstracted view of physical storage, shared pool, hides physical devices |
| **Storage Virt Benefits** | Resource optimization, cost of operation, increased availability, improved performance, SAN |
| **Storage Virt Implementation** | Host-based (LVM, ZFS), Device-based (array controller), Network-based (SAN) — pros/cons for each |
| **In-band vs Out-of-band** | In-band: data passes through virtualizer (caching possible). Out-of-band: only metadata passes (lower latency) |
| **Appliance vs Switch-based** | Appliance: dedicated hardware in SAN. Switch: resides in SAN switch. Both provide disk mgmt, migration, replication |
| **Network Virtualization** | VLAN (logical LAN segmentation), VRF (multiple routing tables in one router), SDN |
| **Memory Virtualization** | Sharing physical memory across VMs, page tables, MMU, TLB, two-level translation |
| **Device Virtualization** | Virtual peripherals (network adapter, disk controller, USB), guest sees real device, hypervisor talks to hardware |
| **CPU Overcommitment** | vCPU ratio, CPU Ready Time, safe ratios (1:1 latency-sensitive, 3:1-5:1 mixed, >8:1 risky), worked example |
| **Memory Overcommitment** | 4 reclamation techniques (least→most invasive): TPS/KSM → Ballooning → Compression → Host-level Swapping, worked example |
| **Virtualization Advantages** | Full benefits table |
| **Virtualization Comparison** | Full/Para/HW-Assisted/OS-Level comparison |
| **Points to Note** | Software licensing (each VM needs own license), IT training needed, hardware investment for effective virtualization |

---

## CS4: Hypervisor & IaaS (41 slides)

| Section | Topics Covered |
|---|---|
| **Hypervisor Deep Dive** | Goals (isolation, encapsulation, hardware independence, partitioning), architecture stack |
| **Hypervisor Types** | Type 1/Type 2 comparison with detailed characteristics |
| **BOCHS Emulator** | Pure emulation (interpretation-based), useful for OS development/debugging, contrast with hardware-assisted virtualization |
| **IaaS Architecture** | 4 layers: compute, storage, networking, management/orchestration |
| **IaaS Actors** | Cloud Provider, Cloud Consumer, Cloud Broker, Cloud Auditor |
| **AWS Introduction** | Amazon Web Services overview, global infrastructure |
| **AWS Regions** | Geographic areas with multiple data centres, selection criteria (latency, compliance, services available) |
| **AWS Availability Zones** | Isolated data centres within a region, connected with low-latency links (<2ms), fault isolation |
| **AWS Local Zones** | Extends AWS closer to end users for ultra-low latency (gaming, media), Mumbai example |
| **AWS Wavelength Zones** | Infrastructure in telecom carrier data centres, 5G edge computing, single-digit ms latency |
| **AWS Outposts** | AWS hardware in YOUR data centre, runs AWS services locally, fully managed by AWS |
| **AWS Edge** | CloudFront CDN + Lambda@Edge, global edge locations |
| **AWS Shared Responsibility** | AWS responsible for cloud infrastructure, customer responsible for data/config/access |
| **IAM Overview** | Identity and Access Management, controls who can do what in AWS |
| **Authentication** | "Who are you?" — username/password (console), Access Key ID + Secret (CLI/API), MFA |
| **Authorization** | "What are you allowed to do?" — permissions via IAM policies |
| **IAM Roles** | Temporary credentials for services/cross-account access, no permanent keys |
| **IAM Policies** | JSON documents: Effect (Allow/Deny), Action (e.g., s3:GetObject), Resource (ARN), conditions. Policy examples. |
| **IAM Best Practices** | Root only for initial setup, MFA on root, use roles not keys, least privilege |

---

## CS5: IaaS Services — AWS (54 slides)

| Section | Topics Covered |
|---|---|
| **EC2 Overview** | Elastic Compute Cloud, virtual servers, pay-per-second, revolutionized computing |
| **Instance Type Families** | General Purpose (M/T — web, dev), Compute Optimized (C — batch, HPC), Memory Optimized (R/X — DBs, caches), Storage Optimized (I/D — warehousing), Accelerated (P/G — ML, video) |
| **AMI (Amazon Machine Image)** | Defines initial software state (OS, patches, pre-installed apps) |
| **AMI Types (4)** | AWS Published (default OS), Marketplace (licensed software), Generated from Existing Instances (corporate standards), VM Import/Export (raw/VHD/VMDK/OVA) |
| **EC2 Lifecycle** | Pending → Running → Stopping → Stopped → Shutting-down → Terminated. Billing per state. Bootstrapping (user data scripts). Tags for management. |
| **EC2 Tenancy** | Shared (default, isolated but shared hardware), Dedicated Instance (hardware dedicated), Dedicated Host (physical server, licensing) |
| **Placement Groups** | Cluster (low latency, same rack), Spread (high availability, different racks), Partition (large distributed, separate partitions) |
| **Instance Store** | Temporary block storage physically attached to host, data lost on stop/terminate |
| **VPC** | Virtual Private Cloud — isolated network section in AWS |
| **VPC Components** | Subnets (public/private), Internet Gateway, NAT Gateway, Route Tables, Security Groups (stateful, instance-level), NACLs (stateless, subnet-level) |
| **VPC Functioning** | How traffic flows through subnets, routing, internet access |
| **S3** | Simple Storage Service — object storage. Buckets, objects (key-value), 11 nines (99.999999999%) durability, storage classes, versioning, lifecycle policies |
| **EBS** | Elastic Block Store — block storage attached to EC2. Types: gp3 (general), io2 (high IOPS), st1 (throughput), sc1 (cold). Snapshots, encryption. |
| **EFS** | Elastic File System — managed NFS, shared across multiple EC2 instances, auto-scaling |
| **EBS vs EFS vs S3** | Comparison table: storage type, access pattern, performance, pricing, durability, AZ scope, use cases |
| **Glacier** | Archival storage. 3 retrieval options: Expedited (1-5 min), Standard (3-5 hr), Bulk (5-12 hr). Vault lock for compliance. |
| **Database Services** | RDS (managed relational), DynamoDB (NoSQL), ElastiCache (in-memory), Redshift (data warehouse) |
| **RDS** | Supported engines: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora. Multi-AZ for HA, Read Replicas for scaling. |
| **Zomato Case Study** | India's food-delivery platform, 70M+ daily users. Migrated Trino/Druid to Graviton2 (ARM) + Spot Instances. Results: 30% cost reduction, 25% performance improvement, ~$300K/year saved. |

---

## CS6: Containers and Cloud-Native (49 slides)

| Section | Topics Covered |
|---|---|
| **Container Motivation** | "Works on my machine" problem, VM overhead (100 services × 100 OS kernels), dependency hell |
| **What Are Containers** | Lightweight, standalone packages with app + all dependencies, share host OS kernel, isolated user spaces |
| **Cgroups** | Control Groups — limit, account for, isolate resource usage: CPU limits, memory limits, disk I/O limits, network bandwidth limits |
| **Namespaces** | 6 types: PID (process isolation), Network (separate network stack), Mount (filesystem isolation), UTS (hostname), IPC (inter-process communication), User (user ID mapping) |
| **LXC** | Linux Containers — first container technology, OS-level virtualization, uses cgroups + namespaces directly |
| **LXD** | Next-gen system container manager built on LXC, better UX, REST API, image-based |
| **LXC vs LXD Comparison** | Feature comparison table |
| **Container Types** | OS Containers (full OS environment, long-lived, like lightweight VMs — LXC/LXD) vs Application Containers (single process, short-lived, microservices — Docker) |
| **VM vs Container** | Detailed comparison: what's virtualized (hardware vs OS), isolation level, boot time, resource overhead, density, OS support, security, image size, use cases |
| **Docker Motivation** | "Build once, run anywhere" philosophy, solving dependency hell, consistent environments |
| **Docker Introduction** | Open platform for developing, shipping, running applications in containers |
| **Docker Architecture** | Client-server: Docker Client (CLI) → REST API → Docker Daemon (dockerd) → Registry (Docker Hub) |
| **Docker Daemon** | Background service managing Docker objects (images, containers, networks, volumes) |
| **Docker Client** | CLI tool users interact with, sends commands to daemon, can connect to remote daemons |
| **Docker Registry** | Storage for Docker images. Docker Hub (default public), private registries (ECR, GCR, ACR) |
| **Docker Images** | Read-only templates with instructions for creating containers. Built from layers. Base image + each Dockerfile instruction = new layer. Immutable. |
| **Dockerfile** | Text file with instructions: FROM (base image), RUN (execute commands), COPY (add files), CMD (default command), EXPOSE (declare ports), ENV (environment variables), WORKDIR (set directory), VOLUME (mount points), ARG, LABEL |
| **Dockerfile Best Practices** | Minimize layers, use .dockerignore, multi-stage builds, don't run as root, use specific base image tags, order instructions by change frequency |
| **Docker Containers** | Running instances of images. Read-write layer on top of read-only image layers. |
| **Docker Lifecycle** | build (create image) → pull (download image) → run (create container) → stop → start → remove |
| **Docker Volumes** | Persistent data storage that survives container removal. Bind mounts (host path) vs Docker volumes (managed by Docker) |
| **Container Orchestration** | Why needed: scaling (run 100 copies), health checks (restart failed), load balancing, rolling updates, service discovery, secret management |
| **Orchestration Challenges** | Without orchestration: manual scaling, no auto-recovery, no load balancing, deployment downtime |
| **Kubernetes Overview** | Container orchestration platform. Pods (smallest unit), Nodes (machines), Clusters (group of nodes), Services (network endpoint), Deployments (desired state), ReplicaSets (maintain pod count) |
| **Imperative vs Declarative** | Imperative: step-by-step commands ("create 3 pods"). Declarative: desired state in YAML ("I want 3 pods"). K8s uses declarative model. |
| **Container Orchestration Tools** | Kubernetes (dominant), Docker Swarm, Apache Mesos, Amazon ECS, HashiCorp Nomad |
| **Cloud-Native** | Design principles: microservices architecture, declarative deployment, portability across clouds, 12-factor app principles, designed for cloud elasticity |

---

## Quick Reference: What's in Each Session

| Session | One-Line Summary |
|---|---|
| **CS1** | Cloud definition, NIST 5 characteristics, 3 service models (IaaS/PaaS/SaaS), 5 deployment models, advantages/challenges |
| **CS2-CS3** | Virtualization types (full/para/HW-assisted/OS-level), hypervisors, x86 challenge, storage/network/memory virtualization, CPU/memory overcommitment |
| **CS4** | Hypervisor deep dive, IaaS architecture, AWS global infrastructure (Regions/AZs/Local Zones/Wavelength/Outposts), IAM (auth/authz/roles/policies) |
| **CS5** | AWS EC2 (instance types, AMI, lifecycle, tenancy, placement), VPC, S3/EBS/EFS/Glacier storage, RDS, Zomato case study |
| **CS6** | Containers (cgroups, namespaces), Docker (architecture, images, Dockerfile, lifecycle), Kubernetes (pods, deployments), cloud-native principles |

---

*Use this file to verify you haven't missed any Cloud Computing topic before the exam.*
