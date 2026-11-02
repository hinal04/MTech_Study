# Cloud Computing — Exam Cheatsheet (Quick Reference Before Exam)

> Scan this 30 minutes before the exam. All decisions, comparisons, and key facts.

---

## 1. WHEN TO PICK WHAT — Service Model

| Scenario | Pick | Why |
|---|---|---|
| Need full control over OS, networking, security | **IaaS** (EC2, Azure VMs) | You manage OS, apps, middleware. Provider manages hardware. Maximum control for custom infrastructure needs. Like renting an empty flat — you furnish it yourself. |
| Need to deploy apps without managing servers | **PaaS** (Heroku, Elastic Beanstalk) | You manage only apps + data. Provider handles OS, runtime, scaling. Like a co-working space — desk is ready, just bring your laptop. |
| Need ready-to-use software | **SaaS** (Gmail, Salesforce, Microsoft 365) | You just use it via browser. Provider manages everything. Like eating at a restaurant — food is ready, you just eat. |
| ML model training on cloud GPUs | **IaaS** (EC2 P4d instances) | Need specific GPU hardware, custom libraries, full OS control. |
| Build and deploy a web app quickly | **PaaS** (Heroku, App Engine) | Auto-scaling, managed runtime, focus on code not infrastructure. |

### Who Manages What

| Component | On-Premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Applications | You | You | You | Provider |
| Data | You | You | You | Provider |
| Runtime | You | You | Provider | Provider |
| Middleware | You | You | Provider | Provider |
| OS | You | You | Provider | Provider |
| Virtualization | You | Provider | Provider | Provider |
| Servers | You | Provider | Provider | Provider |
| Storage | You | Provider | Provider | Provider |
| Networking | You | Provider | Provider | Provider |

---

## 2. WHEN TO PICK WHAT — Deployment Model

| Scenario | Pick | Why |
|---|---|---|
| Startup, need scalability, cost-effective | **Public Cloud** | Pay-per-use, infinite scaling, no upfront cost. Shared infrastructure. AWS, Azure, GCP. Trade-off: less control, data on provider's servers. |
| Bank/Government, strict security + compliance | **Private Cloud** | Dedicated infrastructure, full control, regulatory compliance (data sovereignty). Trade-off: expensive, limited scalability, maintenance burden. |
| Migration from on-premises, need flexibility | **Hybrid Cloud** | Keep sensitive workloads on-premise, burst to cloud for peaks. Gradual migration. Trade-off: complex networking across environments. |
| Group of hospitals sharing health data | **Community Cloud** | Shared by organizations with common needs (compliance, security). Cost split among members. Trade-off: limited to community's scope. |
| Avoid vendor lock-in, best-of-breed | **Multi-Cloud** | Use AWS for compute + GCP for ML + Azure for Office 365. No single vendor dependency. Trade-off: management complexity, data transfer costs. |

---

## 3. WHEN TO PICK WHAT — Virtualization Type

| Scenario | Pick | Why |
|---|---|---|
| Run unmodified guest OS, modern hardware | **Hardware-Assisted (VT-x/AMD-V)** | CPU handles trapping. Near-native performance. No guest OS modification. **Dominant approach today.** KVM, ESXi, Hyper-V. |
| Maximum compatibility, legacy systems | **Full Virtualization (Binary Translation)** | Guest OS runs unmodified. VMM translates privileged instructions at runtime. Slower than HW-assisted but works on old CPUs. |
| Open-source guest OS, maximum performance | **Para-Virtualization** | Guest OS modified with hypercalls — eliminates trap overhead. Best performance but needs kernel source code. Xen. Cannot run unmodified Windows. |
| Lightweight, microservices, fast startup | **OS-Level (Containers)** | Shared host kernel. Millisecond boot. 100-1000 per host. Docker, LXC. Trade-off: weaker isolation (shared kernel). |

### VM vs Container — Quick Decision

| Need | VM | Container |
|---|---|---|
| Strong isolation (multi-tenant) | ✅ Separate kernels | ❌ Shared kernel |
| Fast startup | ❌ Seconds-minutes | ✅ Milliseconds |
| Resource efficiency | ❌ Full OS per VM (GB) | ✅ App only (MB) |
| Run different OS | ✅ Windows on Linux host | ❌ Same OS family only |
| Density | 10-50 per host | 100-1000+ per host |
| Microservices | ❌ Too heavy | ✅ Perfect fit |

---

## 4. WHEN TO PICK WHAT — AWS Storage

| Scenario | Pick | Why |
|---|---|---|
| Attach disk to EC2 (database, boot volume) | **EBS** | Block storage, single-instance attach, persistent. Types: gp3 (general), io2 (high IOPS for DBs), st1 (throughput), sc1 (cold). |
| Store files shared across multiple EC2 instances | **EFS** | Managed NFS, auto-scaling, shared access. Pay per GB used. Good for content management, web serving. |
| Store objects (images, videos, backups, data lake) | **S3** | Object storage, 11 nines durability, unlimited. Storage classes from Standard to Glacier. RESTful API. |
| Long-term archive (compliance, rarely accessed) | **Glacier** | Cheapest storage. Retrieval: Expedited (1-5 min), Standard (3-5 hr), Bulk (5-12 hr). Vault lock for compliance. |

### EBS vs EFS vs S3

| Aspect | EBS | EFS | S3 |
|---|---|---|---|
| Type | Block storage | File storage | Object storage |
| Attach to | Single EC2 instance | Multiple EC2 instances | Any (via API/HTTP) |
| Access | OS-level (mount as disk) | NFS mount | REST API / SDK |
| Performance | High IOPS (io2: 64,000) | Moderate | High throughput |
| Durability | 99.999% (within AZ) | 99.999999999% | 99.999999999% (11 nines) |
| Scaling | Manual (resize volume) | Automatic | Unlimited |
| Best for | Databases, boot volumes | Shared files, CMS | Data lake, backups, static website |
| Pricing | Per GB provisioned | Per GB used | Per GB stored + requests |

---

## 5. NIST 5 ESSENTIAL CHARACTERISTICS

| Characteristic | Definition | Example |
|---|---|---|
| **On-demand self-service** | Provision resources without human intervention | Spin up EC2 instance via AWS console in minutes |
| **Broad network access** | Accessible from any device over the network | Access from laptop, mobile, tablet, any location |
| **Resource pooling** | Provider pools resources for multiple tenants (multi-tenant) | Multiple companies share same physical servers (isolated VMs) |
| **Rapid elasticity** | Scale up/down automatically based on demand | Auto-scaling group adds EC2 instances during traffic spike |
| **Measured service** | Pay-per-use with transparent metering | Billed per second of EC2 usage, per GB of S3 storage |

---

## 6. KEY COMPARISONS

### Hypervisor Types

| | Type 1 (Bare-metal) | Type 2 (Hosted) | Hybrid |
|---|---|---|---|
| Runs on | Directly on hardware | On top of host OS | On hardware, uses OS features |
| Performance | Best | Lower (OS overhead) | Good |
| Use | Cloud/data centres | Dev/test laptops | KVM (Linux kernel module) |
| Examples | ESXi, Hyper-V, Xen | VirtualBox, Parallels | KVM, bhyve |

### 4 Virtualization Types

| | Full | Para | HW-Assisted | OS-Level (Container) |
|---|---|---|---|---|
| Guest OS modified? | No | Yes (hypercalls) | No | N/A (shares kernel) |
| Performance | Lower (binary translation) | Good | Near-native | Native |
| Isolation | Strong | Strong | Strong | Moderate |
| Boot time | Seconds-minutes | Seconds-minutes | Seconds-minutes | Milliseconds |
| Example | QEMU | Xen (PV) | KVM, ESXi | Docker, LXC |

### x86 Virtualization Challenge

**Problem:** 17 x86 instructions are "sensitive but not privileged" — they don't trap when executed outside Ring 0, so trap-and-emulate fails silently.

| Solution | How | Year |
|---|---|---|
| **Binary Translation** (VMware) | Scan guest code, replace 17 bad instructions at runtime | 1999 |
| **Para-virtualization** (Xen) | Modify guest OS to use hypercalls instead | 2003 |
| **HW-Assisted** (Intel VT-x, AMD-V) | CPU adds VMX root/non-root mode, traps all sensitive instructions in hardware | 2006 |

### Memory Overcommitment (4 techniques, least → most invasive)

| Technique | How | Impact |
|---|---|---|
| **1. TPS/KSM (Page Sharing)** | Merge identical memory pages across VMs | Minimal — transparent |
| **2. Ballooning** | Guest-side driver reclaims memory from guest's own processes | Low-moderate — guest OS manages |
| **3. Compression** | Compress pages in memory instead of swapping to disk | Moderate — CPU overhead |
| **4. Host Swapping** | Swap pages to disk without guest cooperation | **Severe** — disk is 100,000× slower than RAM |

### CPU Overcommitment

| Ratio | Safe For |
|---|---|
| 1:1 | Latency-sensitive (DBs, real-time) |
| 3:1–5:1 | Mixed workloads |
| >8:1 | High risk |

---

## 7. DOCKER — Quick Reference

```dockerfile
# Dockerfile
FROM python:3.10-slim          # Base image
WORKDIR /app                    # Set working directory
COPY requirements.txt .         # Copy file
RUN pip install -r requirements.txt  # Execute command (creates layer)
COPY . .                        # Copy app code
EXPOSE 8080                     # Declare port
CMD ["python", "app.py"]        # Default command when container starts
```

```bash
# Docker commands
docker build -t myapp .         # Build image from Dockerfile
docker run -d -p 8080:8080 myapp # Run container (detached, port mapping)
docker ps                       # List running containers
docker stop <id>                # Stop container
docker images                   # List images
docker push myapp:latest        # Push to registry
docker pull nginx:latest        # Pull from registry
docker volume create mydata     # Create persistent volume
```

**Architecture:** Client (CLI) → REST API → Daemon (dockerd) → Registry (Docker Hub)

### Container Isolation

| Mechanism | Isolates | Example |
|---|---|---|
| **Namespaces** (6 types) | PID (processes), Network, Mount (filesystem), UTS (hostname), IPC, User | Container A can't see Container B's processes |
| **Cgroups** | CPU, Memory, Disk I/O, Network bandwidth limits | Container A gets max 2 CPU cores, 4GB RAM |

### Kubernetes — Key Concepts

| Concept | What It Is |
|---|---|
| **Pod** | Smallest deployable unit — one or more containers sharing network/storage |
| **Node** | A machine (VM or physical) running pods |
| **Cluster** | Group of nodes managed by K8s |
| **Deployment** | Declares desired state ("run 3 replicas of this app") |
| **Service** | Stable network endpoint to access pods (load balances across pods) |
| **ReplicaSet** | Ensures desired number of pod replicas are running |
| **ConfigMap/Secret** | Store configuration and sensitive data separately from code |

**Imperative:** `kubectl create deployment nginx --image=nginx --replicas=3` (step-by-step commands)
**Declarative:** Write YAML file describing desired state, `kubectl apply -f deployment.yaml` (K8s makes it happen)

---

## 8. AWS SERVICES — Quick Reference

| Service | Category | One-Line Description |
|---|---|---|
| **EC2** | Compute | Virtual servers (VMs) in the cloud |
| **S3** | Storage | Object storage (files, images, data lake) |
| **EBS** | Storage | Block storage (disks for EC2) |
| **EFS** | Storage | Shared file storage (NFS) |
| **Glacier** | Storage | Archival storage (cheap, slow retrieval) |
| **RDS** | Database | Managed relational DB (MySQL, PostgreSQL, Aurora) |
| **VPC** | Networking | Isolated virtual network |
| **IAM** | Security | Authentication, authorization, roles, policies |
| **CloudFront** | CDN | Content delivery network (edge caching) |

### IAM Quick Recall

| Component | What |
|---|---|
| **User** | Human or service identity with long-term credentials |
| **Group** | Collection of users with shared permissions |
| **Role** | Temporary identity for services (EC2, Lambda) — no permanent keys |
| **Policy** | JSON document: Effect (Allow/Deny) + Action (s3:GetObject) + Resource (ARN) |

**Best practices:** Root account only for setup → MFA on root → Roles for services (not keys) → Least privilege

### VPC Quick Recall

| Component | What |
|---|---|
| **Subnet** | Segment of VPC. Public (internet access) or Private (internal only) |
| **Internet Gateway** | Connects VPC to internet |
| **NAT Gateway** | Lets private subnet reach internet (outbound only) |
| **Route Table** | Directs traffic between subnets and gateways |
| **Security Group** | Stateful firewall at instance level (allow rules only) |
| **NACL** | Stateless firewall at subnet level (allow + deny rules) |

---

## 9. KEY NUMBERS

| Fact | Value |
|---|---|
| S3 durability | **11 nines** (99.999999999%) |
| NIST essential characteristics | **5** (on-demand, broad access, pooling, elasticity, measured) |
| Cloud service models | **3** (IaaS, PaaS, SaaS) |
| Cloud deployment models | **5** (Public, Private, Hybrid, Community, Multi-cloud) |
| Hypervisor types | **3** (Type 1 bare-metal, Type 2 hosted, Hybrid) |
| Virtualization types | **4** (Full, Para, HW-Assisted, OS-Level/Container) |
| x86 sensitive-not-privileged instructions | **~17** |
| Container namespace types | **6** (PID, Network, Mount, UTS, IPC, User) |
| Memory overcommitment techniques | **4** (TPS → Balloon → Compress → Swap) |
| EC2 instance families | **5** (General, Compute, Memory, Storage, Accelerated) |
| EC2 tenancy types | **3** (Shared, Dedicated Instance, Dedicated Host) |
| EC2 placement groups | **3** (Cluster, Spread, Partition) |
| AMI types | **4** (AWS Published, Marketplace, From Existing, VM Import) |

---

## 10. CLOUD COMPUTING — Definition & Advantages/Disadvantages

### NIST Definition
> "On-demand network access to shared pool of configurable computing resources that can be rapidly provisioned and released with minimal management effort"

### Advantages (7)

| # | Advantage | Details |
|---|---|---|
| 1 | **Cost reduction** | CapEx → OpEx, pay only for what you use |
| 2 | **Scalability** | Scale up/down based on demand |
| 3 | **Elasticity** | Auto-scaling — resources grow/shrink automatically |
| 4 | **Global reach** | Deploy in any region worldwide |
| 5 | **Reliability** | Multi-AZ, auto-failover |
| 6 | **Speed of deployment** | Minutes vs weeks for physical servers |
| 7 | **Focus on business** | No infrastructure maintenance |

### Disadvantages (6)

| # | Disadvantage | Details |
|---|---|---|
| 1 | **Security concerns** | Data on provider's servers |
| 2 | **Vendor lock-in** | Hard to migrate between providers |
| 3 | **Downtime risk** | Provider outages affect you |
| 4 | **Data sovereignty** | Data must stay in specific countries |
| 5 | **Internet dependency** | No internet = no cloud |
| 6 | **Cost management** | Easy to overspend without monitoring |

---

## 11. VIRTUALIZATION — Complete Details

- **Definition:** Creating virtual version of physical resources (server, storage, network)
- **Why it matters:** Foundation of cloud computing — enables multi-tenancy, elasticity, resource pooling
- **VM Definition (Popek-Goldberg):** "Efficient, isolated duplicate of a real machine"

### VM Advantages
- Multiple OS co-exist on same hardware
- Isolation between VMs
- Snapshots/rollback
- Rapid provisioning
- Live migration (move running VM between hosts)

### VM Disadvantages
- Performance overhead (2-10%)
- Noisy neighbour problem
- Resource contention
- Hypervisor attack surface

> **Key insight:** Hypervisor = "OS for VMs" (manages VMs like an OS manages processes)

---

## 12. VIRTUALIZATION TYPES — Detailed Comparison

### Full Virtualization

| Aspect | Details |
|---|---|
| **How it works** | Binary translation — VMM scans guest instructions, replaces privileged ones |
| **Pro** | Guest OS runs unmodified (any OS works) |
| **Con** | Overhead from translation |
| **Example** | VMware Workstation (early), QEMU |
| **When to pick** | Need to run unmodified OS on old hardware without VT-x support |

### Para-Virtualization

| Aspect | Details |
|---|---|
| **How it works** | Guest OS modified to use hypercalls instead of privileged instructions |
| **Pro** | Better performance (no binary translation overhead) |
| **Con** | Must modify guest OS source code (can't run Windows) |
| **Example** | Xen (PV mode) |
| **When to pick** | Open-source guest OS + need maximum performance without HW-assisted support |

### Hardware-Assisted Virtualization

| Aspect | Details |
|---|---|
| **How it works** | CPU adds VMX root/non-root mode (VT-x/AMD-V). Hardware handles traps. |
| **Pro** | Best of both — unmodified OS + near-native performance |
| **Con** | Needs modern CPU (all CPUs since 2006 have it) |
| **Example** | KVM, ESXi, Hyper-V — **DOMINANT TODAY** |
| **When to pick** | Default choice for all modern deployments |

### OS-Level Virtualization (Containers)

| Aspect | Details |
|---|---|
| **How it works** | Share host kernel, isolate using namespaces + cgroups |
| **Pro** | Millisecond boot, minimal overhead, 100-1000 per host |
| **Con** | Weaker isolation (shared kernel), same OS family only |
| **Example** | Docker, LXC |
| **When to pick** | Microservices, fast scaling, high density, same OS workloads |

---

## 13. STORAGE VIRTUALIZATION — Details

| Type | How | Pros | Cons |
|---|---|---|---|
| **Host-based (LVM)** | Volume manager on host | Simple setup | Per-host only, not shared |
| **Device-based** | Storage array controller handles it | No extra hardware needed | Same-vendor only |
| **Network-based (SAN)** | Most common. Heterogeneous storage. | Works across vendors, centralized | More complex, needs SAN infrastructure |

### Network-based: In-band vs Out-of-band

| | In-band | Out-of-band |
|---|---|---|
| **Data path** | Data flows through the virtualization device | Only metadata through device |
| **Advantage** | Caching possible | Lower latency |
| **Disadvantage** | Adds latency (data passes through extra hop) | Needs host software agent |

---

## 14. CPU & MEMORY OVERCOMMITMENT — Exam Points

### CPU Overcommitment

- **vCPU ratio** = virtual CPUs : physical cores
- **CPU Ready Time** = time a VM waits for a physical core to become available

| Ratio | Safe For | Risk |
|---|---|---|
| **1:1** | Databases, real-time, latency-sensitive | Lowest |
| **3:1–5:1** | Mixed workloads | Moderate |
| **>8:1** | Only idle VMs | **High risk** |

### Memory Overcommitment

- **Key fact:** Memory CAN'T be time-sliced like CPU — a page is either in RAM or it isn't
- 4 techniques ordered least → most invasive:

| Order | Technique | How | Impact |
|---|---|---|---|
| 1st | **TPS (Transparent Page Sharing)** | Merge identical pages across VMs | Minimal — transparent |
| 2nd | **Ballooning** | Guest driver reclaims memory from guest processes | Low-moderate |
| 3rd | **Compression** | Compress pages in memory instead of swapping | Moderate — CPU overhead |
| 4th | **Host Swapping** | Swap pages to disk without guest cooperation | **Severe** — disk is 100,000× slower |

---

## 15. AWS SERVICES — Scenario-Based Selection

| I need to... | Use | Why |
|---|---|---|
| Run a virtual server | **EC2** | Full control over OS, apps, networking |
| Store files/images/videos | **S3** | Object storage, 11 nines durability, unlimited |
| Attach a disk to EC2 | **EBS** | Block storage, persistent, snapshots |
| Share files across multiple servers | **EFS** | Managed NFS, auto-scaling |
| Archive data cheaply | **Glacier** | Cheapest storage, hours to retrieve |
| Managed database | **RDS** | MySQL/PostgreSQL/Aurora, Multi-AZ, backups |
| Isolate my network | **VPC** | Subnets, security groups, route tables |
| Control who accesses what | **IAM** | Users, roles, policies, MFA |
| Distribute content globally | **CloudFront** | CDN, edge caching, low latency |
| Handle 10x traffic spike | **Auto Scaling + ELB** | Add/remove EC2 instances automatically |

---

## 16. DOCKER — Advantages/Disadvantages

### Advantages
- **Consistent environments** — "works on my machine" problem solved
- **Lightweight** — MB not GB
- **Fast startup** — milliseconds not minutes
- **Portable** — run anywhere Docker runs
- **Microservices-friendly** — one container per service

### Disadvantages
- **Shared kernel** — weaker isolation than VM
- **Linux-only** containers on Linux host
- **Persistent storage** complexity
- **Networking** complexity
- **Security** — kernel vulnerability affects all containers

---

## 17. KUBERNETES — Advantages/Disadvantages

### Advantages
- **Auto-scaling** — scale pods based on CPU/memory/custom metrics
- **Self-healing** — restarts failed pods automatically
- **Rolling updates** — zero-downtime deployment
- **Service discovery** — automatic DNS for services
- **Load balancing** — distribute traffic across pods
- **Declarative configuration** — YAML desired state, K8s makes it happen

### Disadvantages
- **Complex to set up and manage** — many components (etcd, API server, scheduler, etc.)
- **Steep learning curve** — YAML, networking, RBAC, etc.
- **Overkill for simple apps** — monolith doesn't need K8s
- **Resource overhead** — control plane consumes resources
- **Debugging distributed systems is hard** — tracing across pods/services

---

## 18. CLOUD-NATIVE — Key Principles

| # | Principle | What It Means |
|---|---|---|
| 1 | **Microservices** | Small, independent services — each does one thing well |
| 2 | **Containers** | Package each service with its dependencies |
| 3 | **Declarative deployment** | YAML desired state — system converges automatically |
| 4 | **CI/CD** | Automated build → test → deploy pipeline |
| 5 | **Observability** | Logging, monitoring, tracing — know what's happening |
| 6 | **Portability** | Run on any cloud — avoid vendor lock-in |

---

*Good luck with your exam!* 🎯
