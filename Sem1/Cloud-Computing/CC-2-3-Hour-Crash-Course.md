# Cloud Computing — 2-3 Hour Crash Course (Exam Ready)

> **BITS Pilani — CSI ZG527 / SE ZG527**
> You haven't attended lectures. Your exam is in 2 days. This covers EVERYTHING.
>
> **Exam Pattern:** 3 Qs × 10 marks = 30 marks. 2 scenario-based + 1 direct concepts. Closed book. 2 hours.
>
> **Time Plan:**
> - **Hour 1** (Topics 1-4): Virtualization Types, VMs vs Containers, AWS Core Services, Memory/CPU Overcommitment
> - **Hour 2** (Topics 5-8): Cloud Models, IAM & Security, Docker, Kubernetes & Cloud-Native
> - **Hour 3** (Topics 9-11): AWS Storage, x86 Challenge, Quick Revision

---
---

# ⏱️ HOUR 1 — Highest Yield Topics

---

## TOPIC 1: Virtualization Types ⭐⭐⭐

> Tested in every paper. Know the 4 types and the comparison table.

### 1.1 The 4 Virtualization Types

| Type | How It Works | Guest OS Modified? | Performance | Example |
|---|---|---|---|---|
| **Full** | Binary translation — VMM rewrites sensitive instructions at runtime | No | Moderate (5-15% overhead) | Early VMware, QEMU |
| **Para** | Guest OS modified to use **hypercalls** to talk to hypervisor directly | **Yes** (needs kernel source) | Good (3-8% overhead) | Xen PV mode |
| **Hardware-Assisted** | CPU extensions (VT-x/AMD-V) handle trapping in hardware | No | **Best** (1-3% overhead) | KVM, ESXi, Hyper-V — **DOMINANT TODAY** |
| **OS-Level (Containers)** | Share host kernel, isolate via namespaces + cgroups | N/A (no guest OS) | Near-native | Docker, LXC |

### 1.2 Hypervisor Types

| | Type 1 (Bare-metal) | Type 2 (Hosted) | Hybrid |
|---|---|---|---|
| Runs on | Directly on hardware | On top of host OS | Hardware, uses OS features |
| Performance | Best | Lower (OS overhead) | Good |
| Use | Cloud / data centres | Dev/test laptops | Flexible |
| Examples | ESXi, Hyper-V, Xen | VirtualBox, Parallels | KVM (Linux kernel module) |

### 1.3 Solved — [Past Year 2024] Recommend virtualization for enterprise data center

**Answer: Hardware-Assisted Full Virtualization (VT-x/AMD-V) with Type 1 hypervisor**

**Why:**
1. No guest OS modification needed — run any OS (Windows, Linux, legacy)
2. Near-native performance — hardware handles trapping
3. Strong isolation — each VM has its own kernel
4. Mature management ecosystem — VMware vCenter, live migration, snapshots
5. Disaster recovery — VM state as files

**Downsides:** 2-10% performance overhead, noisy neighbor problem, full OS per VM (5-20 GB each), licensing complexity, VM sprawl

### 1.4 Practice

**P1.** Why can't you run unmodified Windows with para-virtualization?
<details><summary>Answer</summary>
Para-virtualization requires modifying the guest OS kernel to replace sensitive instructions with hypercalls. Windows is proprietary — you don't have kernel source code to modify. Only open-source OS (Linux, BSD) can be modified.
</details>

---

## TOPIC 2: VMs vs Containers ⭐⭐⭐

> Near-guaranteed question. Know this table cold.

### 2.1 The Comparison Table

| Dimension | Virtual Machine | Container |
|---|---|---|
| **What's virtualized** | Entire hardware stack | OS-level (process space, filesystem) |
| **Kernel** | Each VM runs its own full kernel | All containers share host kernel |
| **Resource overhead** | Heavy — full OS per VM (2-20 GB) | Light — app + libraries only (50-500 MB) |
| **Boot time** | Seconds to minutes | **Milliseconds to seconds** |
| **Density** | 10-50 per host | **100-1000+ per host** |
| **Isolation** | **Strong** — separate kernels | Moderate — shared kernel = shared vulnerability |
| **Different OS** | ✅ (Windows on Linux host) | ❌ (same OS family only) |
| **Image size** | GBs | MBs |
| **Portability** | Hypervisor-dependent | Highly portable (Docker runs anywhere) |
| **Best for** | Multi-tenant isolation, different OS | Microservices, CI/CD, fast scaling |

> **Key insight:** In production, containers run INSIDE VMs. VM provides tenant isolation, container provides app packaging. Best of both worlds.

### 2.2 Solved — [Past Year 2024] Compare VMs and Containers

**Resource Utilization:**
- 3 VMs with Node.js: each 10GB OS + 2GB app = 36GB consumed
- 3 containers with Node.js: each 2GB app = 6GB consumed → **83% less memory**

**Isolation:**
- VM: hypervisor boundary = hard security barrier. Kernel exploit in VM A doesn't affect VM B.
- Container: namespace isolation can be escaped via kernel exploits. Shared kernel = shared vulnerability.

### 2.3 Practice

**P1.** A startup needs to deploy 50 microservices. Should they use VMs or containers?
<details><summary>Answer</summary>
Containers. 50 VMs = 50 OS copies = 500-1000 GB for OS overhead alone. 50 containers on a few VMs = ~100 GB total. Containers also offer faster CI/CD (Docker build in 30 sec vs VM image in 10-30 min), faster scaling (seconds vs minutes), and better density.
</details>

---

## TOPIC 3: AWS Core Services ⭐⭐⭐

> Appears in EVERY paper. Know EC2, VPC, IAM, S3.

### 3.1 EC2 (Elastic Compute Cloud)

**Instance Type Families (memorize):**

| Family | Optimized For | Example Use |
|---|---|---|
| **General (M, T)** | Balanced compute/memory | Web servers, small DBs |
| **Compute (C)** | High-performance processors | Batch processing, ML inference |
| **Memory (R, X)** | Large in-memory datasets | In-memory DBs, real-time analytics |
| **Storage (I, D)** | High sequential I/O | Data warehousing, Hadoop |
| **Accelerated (P, G)** | GPU/FPGA | ML training, video encoding |

**EC2 Lifecycle:** Pending → Running ⇄ Stopped → Terminated

**Tenancy:**

| Type | What | Cost |
|---|---|---|
| Shared (default) | Your VM on shared physical host | Lowest |
| Dedicated Instance | Your instances on hardware dedicated to YOUR account | Higher |
| Dedicated Host | Entire physical server for you (see sockets/cores) | Highest |

**Placement Groups:**

| Type | Strategy | Use Case |
|---|---|---|
| **Cluster** | Same rack, low latency | HPC, distributed DBs |
| **Spread** | Different racks, max 7/AZ | Critical small clusters (ZooKeeper) |
| **Partition** | Groups on separate racks | Large distributed (Hadoop, Kafka) |

### 3.2 VPC (Virtual Private Cloud)

```
VPC (10.0.0.0/16)
├── Public Subnet (web tier) — has Internet Gateway
│   └── EC2 web servers + NAT Gateway
├── Private Subnet (app tier) — no direct internet
│   └── EC2 app servers
└── Private Subnet (DB tier) — most restricted
    └── RDS database
```

**Security Groups vs NACLs:**

| Feature | Security Groups | NACLs |
|---|---|---|
| Level | Instance | Subnet |
| Stateful? | **Yes** (return traffic auto-allowed) | **No** (must allow both directions) |
| Rules | Allow only | Allow + **Deny** |
| Evaluation | All rules evaluated | Rules in number order, first match wins |

> **Why both?** Defense in depth. SG = room locks (per instance). NACL = floor gates (per subnet).

### 3.3 Solved — [Past Year 2024] Define IAM, EC2, Clusters, VPC — how they work together

**Answer:**
- **IAM** authenticates (who are you?) and authorizes (what can you do?) → policies define Allow/Deny
- **VPC** creates isolated network → subnets, gateways, firewalls
- **EC2** runs virtual servers inside VPC subnets
- **Clusters** (placement groups) control physical placement for performance

**Flow:** IAM authenticates user → authorizes EC2 launch → EC2 instance placed in VPC subnet → Security Groups control traffic → Cluster placement group ensures low latency between instances.

### 3.4 Practice

**P1.** A 3-tier web app needs web servers (public), app servers (private), and database (private). What Security Group rules do you set?
<details><summary>Answer</summary>

| SG | Inbound | Outbound |
|---|---|---|
| SG-Web | Port 80/443 from internet (0.0.0.0/0) | Port 8080 to SG-App |
| SG-App | Port 8080 from SG-Web only | Port 5432 to SG-DB |
| SG-DB | Port 5432 from SG-App only | Deny all outbound |

Each tier talks only to adjacent tier. No direct Web→DB access.
</details>

---

## TOPIC 4: Memory & CPU Overcommitment ⭐⭐⭐

> The 2024 past paper had a 10-mark question on this. Learn it well.

### 4.1 CPU Overcommitment

- **vCPU ratio** = virtual CPUs / physical cores
- **CPU Ready Time** = time VM is ready but waiting for a physical core

| Ratio | Safe For | Risk |
|---|---|---|
| **1:1** | Databases, real-time, latency-sensitive | Lowest |
| **3:1 to 5:1** | Mixed workloads | Moderate |
| **>8:1** | Only idle VMs | **High risk** |

> CPU Ready > 5% = overcommitted. > 10% = severely degraded.

### 4.2 Memory Overcommitment — 4 Techniques (Least → Most Invasive)

**Key fact:** Memory CAN'T be time-sliced like CPU. A page is either in RAM or it isn't.

| Priority | Technique | How | Impact |
|---|---|---|---|
| 1st | **TPS (Transparent Page Sharing)** | Merge identical pages across VMs (copy-on-write) | Minimal — transparent |
| 2nd | **Ballooning** | Guest-side driver reclaims memory from guest's own processes | Low-moderate — guest decides what to evict |
| 3rd | **Compression** | Compress pages in memory instead of swapping | Moderate — CPU overhead |
| 4th | **Host Swapping** | Swap pages to disk without guest cooperation | **SEVERE — disk is 100,000× slower than RAM** |

### 4.3 Solved — [Past Year 2024] Explain paging and ballooning

**Ballooning:**
- Balloon driver installed inside guest OS (e.g., VMware Tools)
- Hypervisor tells balloon to "inflate" → driver allocates large memory blocks inside guest
- Guest OS pages out less-important processes to make room
- Hypervisor reclaims the physical pages backing the balloon
- **Key advantage:** Guest OS decides which pages are least important (intelligent eviction)

**Host Swapping (Paging):**
- Hypervisor writes VM memory pages directly to swap file on disk
- VM experiences massive latency when accessing swapped pages (milliseconds vs nanoseconds)
- **Last resort** — only when TPS, ballooning, and compression fail

### 4.4 Solved — [Practice] Diagnose CPU overcommitment at 6:1

**Metrics to check:** CPU Ready Time (>5%?), CPU Co-Stop, Per-Host Utilization (>80%?)

**Solutions (no new hardware):**
1. **Right-size VMs** — audit usage, reduce vCPUs on over-provisioned VMs
2. **Enable DRS** — auto-balance VMs across hosts
3. **CPU reservations** for critical VMs
4. **Schedule non-critical work off-peak** (backups, batch jobs at night)
5. **Containerize** where possible — 10 Nginx VMs → 10 containers on 1 VM

### 4.5 Practice

**P1.** A host has 64GB RAM, 10 VMs each allocated 8GB. What's the overcommit ratio? Which technique activates first?
<details><summary>Answer</summary>
Ratio: 80GB / 64GB = 1.25:1. First technique: TPS (Transparent Page Sharing) — merge identical pages across VMs. If VMs run same OS, many kernel pages are identical and can be deduplicated.
</details>

---
---

# ⏱️ HOUR 2 — Core Topics

---

## TOPIC 5: Cloud Service & Deployment Models ⭐⭐⭐

### 5.1 NIST 5 Essential Characteristics (memorize)

| # | Characteristic | One-Liner | Example |
|---|---|---|---|
| 1 | **On-demand self-service** | Provision without human intervention | Launch EC2 at 2 AM, no phone call |
| 2 | **Broad network access** | Access from any device | Salesforce from phone, laptop, tablet |
| 3 | **Resource pooling** | Provider pools resources, multi-tenant | Multiple companies share same physical servers |
| 4 | **Rapid elasticity** | Scale up/down automatically | Auto-scaling adds instances during traffic spike |
| 5 | **Measured service** | Pay-per-use with metering | Billed per second of EC2, per GB of S3 |

### 5.2 Service Models — Who Manages What

| Layer | On-Premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Applications | You | You | You | **Provider** |
| Data | You | You | You | **Provider** |
| Runtime | You | You | **Provider** | Provider |
| OS | You | You | **Provider** | Provider |
| Virtualization | You | **Provider** | Provider | Provider |
| Servers/Storage/Network | You | **Provider** | Provider | Provider |

- **IaaS** = You manage OS + apps. Like renting an empty flat. (EC2, Azure VMs)
- **PaaS** = You manage apps + data. Like a co-working space. (Heroku, Elastic Beanstalk)
- **SaaS** = You just use it. Like eating at a restaurant. (Gmail, Salesforce)

### 5.3 Deployment Models

| Model | Best For | Pros | Cons |
|---|---|---|---|
| **Public** | Startups, variable workloads | Cheapest, scalable, no maintenance | Less control, data on provider servers |
| **Private** | Banks, government, compliance | Full control, security | Expensive, limited scalability |
| **Hybrid** | Migration, mixed workloads | Flexibility, burst capacity | Complex networking |
| **Community** | Hospitals, government agencies | Cost sharing, domain compliance | Limited to community scope |
| **Multi-cloud** | Avoiding lock-in | Best-of-breed, no single dependency | Management complexity |

### 5.4 Cloud Advantages (7) & Challenges (6)

**Advantages:** Cost reduction (CapEx→OpEx), Scalability, Elasticity, Global reach, Reliability, Speed of deployment, Focus on business

**Challenges:** Security concerns, Vendor lock-in, Downtime risk, Data sovereignty, Internet dependency, Cost management

### 5.5 Solved — [Past Year 2024] E-commerce company with on-premise scalability issues

**Recommend: Hybrid Cloud** — keep sensitive data on-premise, burst to public cloud for peaks.

| Service | Pick | Why |
|---|---|---|
| Compute | EC2 with Auto Scaling | Scales from 2→200 instances during flash sales |
| App deployment | Elastic Beanstalk (PaaS) | Focus on code, not servers |
| Database | RDS Multi-AZ | Managed DB with automatic failover |
| CDN | CloudFront | Cache images/CSS at 400+ global edge locations |
| CRM | Salesforce (SaaS) | Ready-to-use, no build needed |

### 5.6 Practice

**P1.** A bank needs strict security + compliance but occasional capacity for stress testing. Which deployment model?
<details><summary>Answer</summary>
Hybrid Cloud. Keep core banking on private cloud (regulatory compliance, data sovereignty). Burst to public cloud for stress testing (temporary, non-sensitive workloads). This avoids buying hardware that's idle 95% of the time.
</details>

---

## TOPIC 6: IAM & Security ⭐⭐

### 6.1 IAM Components

| Component | What It Is | Example |
|---|---|---|
| **User** | Identity for person/service with long-term credentials | Employee "hinal" with console login |
| **Group** | Collection of users with shared permissions | "Developers" group — EC2 + S3 access |
| **Role** | Temporary identity for services — no permanent keys | EC2 assumes "S3ReadOnlyRole" |
| **Policy** | JSON: Effect (Allow/Deny) + Action + Resource | Allow s3:GetObject on my-bucket/* |

**IAM Policy Example:**
```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:ListBucket"],
  "Resource": ["arn:aws:s3:::my-bucket", "arn:aws:s3:::my-bucket/*"]
}
```

### 6.2 IAM Best Practices
1. Root account → only for initial setup, enable MFA
2. Use **Roles** for services (not access keys) — temporary, auto-rotating
3. **Least privilege** — minimum permissions needed
4. Enable **CloudTrail** — log every API call
5. Rotate credentials every 90 days

### 6.3 Why Roles Over Access Keys?

| | Access Keys | IAM Roles |
|---|---|---|
| Credential type | Long-lived (until rotated) | Short-lived (auto-expire in 1-12 hrs) |
| Storage risk | Must store somewhere (config files, env vars) | No credentials to store |
| If leaked | Permanent access until discovered | Access only until expiry (hours) |

---

## TOPIC 7: Docker ⭐⭐

### 7.1 Docker Architecture

```
Docker Client (CLI) → REST API → Docker Daemon (dockerd) → Registry (Docker Hub)
```

- **Image** = read-only template (layers). Like a recipe.
- **Container** = running instance of an image. Like a cooked dish.
- **Registry** = storage for images (Docker Hub, AWS ECR)

### 7.2 Dockerfile Instructions

| Instruction | Purpose | Example |
|---|---|---|
| **FROM** | Base image (every Dockerfile starts here) | `FROM python:3.11-slim` |
| **WORKDIR** | Set working directory | `WORKDIR /app` |
| **COPY** | Copy files from host into image | `COPY requirements.txt .` |
| **RUN** | Execute command during build (creates layer) | `RUN pip install -r requirements.txt` |
| **EXPOSE** | Declare port (documentation only) | `EXPOSE 8080` |
| **CMD** | Default command when container starts | `CMD ["python", "app.py"]` |
| **ENTRYPOINT** | Fixed executable (not easily overridden) | `ENTRYPOINT ["gunicorn"]` |
| **ENV** | Set environment variable | `ENV FLASK_ENV=production` |

**Complete Dockerfile:**
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8080
CMD ["python", "app.py"]
```

### 7.3 Container Isolation

| Mechanism | Isolates | What It Means |
|---|---|---|
| **Namespaces** (6 types) | What container can SEE | Container A can't see Container B's processes |
| **Cgroups** | How much container can USE | Container A gets max 2 CPU, 4GB RAM |

**6 Namespace Types:**

| Namespace | Isolates | Example |
|---|---|---|
| **PID** | Process IDs | Each container has its own PID 1 |
| **NET** | Network stack | Each container gets its own IP address |
| **MNT** | Filesystem | Each container has its own root (/) |
| **UTS** | Hostname | Each container can have its own hostname |
| **IPC** | Inter-process communication | Shared memory isolated between containers |
| **USER** | User IDs | Root inside container can be non-root on host |

### 7.4 Multi-Stage Build

```dockerfile
# Stage 1: BUILD (large — has compiler)
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2: RUN (small — only binary)
FROM alpine:3.18
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

**Why?** Without multi-stage: 560 MB (compiler + source + binary). With multi-stage: **15 MB** (only binary). Smaller = faster pulls, smaller attack surface.

### 7.5 Docker Commands Quick Reference

```bash
docker build -t myapp .         # Build image
docker run -d -p 8080:8080 myapp # Run container
docker ps                       # List running containers
docker stop <id>                # Stop container
docker push myapp:latest        # Push to registry
docker volume create mydata     # Create persistent volume
```

---

## TOPIC 8: Kubernetes & Cloud-Native ⭐⭐

### 8.1 Kubernetes Concepts

| Concept | What It Is |
|---|---|
| **Pod** | Smallest unit — one or more containers sharing network/storage |
| **Node** | A machine (VM or physical) running pods |
| **Cluster** | Group of nodes managed by K8s |
| **Deployment** | Declares desired state ("run 3 replicas") |
| **Service** | Stable network endpoint for pods (load balances) |
| **ReplicaSet** | Ensures N pod replicas are running |

### 8.2 Imperative vs Declarative

| Aspect | Imperative | Declarative |
|---|---|---|
| Philosophy | "Tell HOW to do it" | "Tell WHAT you want" |
| Method | CLI commands | YAML files |
| Reproducible? | No — commands are ephemeral | Yes — YAML is version-controlled |
| Example | `kubectl run nginx --image=nginx --replicas=3` | YAML file with `replicas: 3` + `kubectl apply -f` |

### 8.3 How K8s Handles Scaling & Self-Healing

**Scaling:** HPA (Horizontal Pod Autoscaler) — if CPU > 70%, add pods. If < 30%, remove pods. Cluster Autoscaler adds/removes nodes.

**Self-Healing:**
- Pod crashes → K8s restarts it automatically
- Node fails → pods rescheduled to healthy nodes within 5 min
- Liveness probe fails → container restarted
- Rolling updates → replace pods one-by-one, zero downtime

### 8.4 Cloud-Native Principles

| Principle | Description |
|---|---|
| **Microservices** | Small, independently deployable services |
| **Containerization** | Each service packaged as Docker container |
| **Declarative deployment** | YAML desired state, K8s makes it happen |
| **Designed for failure** | Circuit breakers, health checks, auto-restart |
| **Observability** | Logging + monitoring + tracing built-in |
| **CI/CD automation** | Code commit → auto deploy to production |

---
---

# ⏱️ HOUR 3 — Remaining Topics

---

## TOPIC 9: AWS Storage ⭐⭐

### 9.1 EBS vs EFS vs S3 vs Glacier

| Aspect | EBS | EFS | S3 | Glacier |
|---|---|---|---|---|
| **Type** | Block storage | File storage (NFS) | Object storage | Archival |
| **Attach to** | Single EC2 (same AZ) | Multiple EC2 (cross-AZ) | Anywhere (HTTP) | Anywhere |
| **Scaling** | Manual resize | Auto-scaling | Unlimited | Unlimited |
| **Durability** | 99.999% | 11 nines | **11 nines** | 11 nines |
| **Best for** | Databases, boot volumes | Shared files, CMS | Data lake, backups, images | Compliance archives |
| **Cost** | $0.08-0.125/GB/mo | $0.30/GB/mo | **$0.023/GB/mo** | $0.004-0.001/GB/mo |

### 9.2 Quick Decision

| Need | Pick |
|---|---|
| Disk for database (single EC2) | **EBS** (io2 for high IOPS) |
| Shared files across multiple servers | **EFS** |
| Store images, videos, backups | **S3** |
| Long-term archive (rarely accessed) | **Glacier** |

### 9.3 S3 Storage Classes & Lifecycle

```
Day 0:    Upload → S3 Standard (hot)
Day 30:   → S3 Standard-IA (46% cheaper, instant access)
Day 180:  → Glacier Instant Retrieval (83% cheaper, ms access)
Day 730:  → Glacier Deep Archive (96% cheaper, 12-48hr access)
Day 2555: Delete
```

### 9.4 Practice

**P1.** A media company has 50TB of video with varying access patterns (trending=daily, catalog=monthly, archive=rarely). Design a storage strategy.
<details><summary>Answer</summary>

| Content | Storage | Why |
|---|---|---|
| Trending (2TB) | S3 Standard + CloudFront CDN | Millions of requests/day, low latency |
| Active catalog (15TB) | S3 Standard-IA | Accessed monthly, cheaper |
| Older catalog (15TB) | Glacier Instant Retrieval | Rarely accessed, retrieval in ms |
| Compliance archive (18TB) | Glacier Deep Archive | Almost never accessed, cheapest |

Total: ~$150/month (vs ~$1,150 if all in S3 Standard). **87% savings.**
</details>

---

## TOPIC 10: x86 Virtualization Challenge ⭐⭐

### 10.1 The Problem

**Popek-Goldberg Criteria (1974):** A VMM must satisfy:
1. **Equivalence** — VM behaves identically to bare metal
2. **Resource Control** — VMM controls all hardware
3. **Efficiency** — most instructions execute directly on hardware

**x86 Problem:** 17 instructions are "sensitive but NOT privileged" — they behave differently at different privilege levels but DON'T trigger a trap. The VMM never gets a chance to intercept them → **silently incorrect behavior**.

### 10.2 Solutions (Timeline)

| Solution | Year | How | Guest Modified? |
|---|---|---|---|
| **Binary Translation** (VMware) | 1999 | Scan guest code, rewrite 17 bad instructions | No |
| **Para-virtualization** (Xen) | 2003 | Modify guest to use hypercalls | **Yes** |
| **Hardware-Assisted** (VT-x/AMD-V) | 2006 | CPU adds VMX root/non-root mode — ALL sensitive instructions trap | No |

> **Today's answer:** Hardware-Assisted (VT-x) is universal. All modern CPUs support it since 2006.

### 10.3 Solved — [Past Year 2024] VMM on x86 — can it run unmodified OS?

**Why VMM can't simply execute unmodified OS:**
1. Guest OS expects Ring 0 — but VMM also needs Ring 0
2. 17 sensitive-but-not-privileged instructions don't trap → silently fail
3. Trap-and-emulate breaks on x86

**Can it run without source modification?**
- **Basic trap-and-emulate only → NO** (17 instructions slip through)
- **Binary Translation → YES** (rewrite instructions at runtime, no source change)
- **VT-x/AMD-V → YES** (hardware traps all sensitive instructions)
- **Para-virtualization → NO** (requires modifying guest kernel source)

---

## TOPIC 11: Auto-Scaling & Cost Optimization ⭐⭐

### 11.1 Auto Scaling Configuration

| Parameter | Typical Value | Purpose |
|---|---|---|
| Minimum | 2 (one per AZ) | Always maintain HA |
| Desired | 4 | Normal traffic baseline |
| Maximum | 20 | Cap to prevent runaway costs |

**Scaling Policies:**
- **Target Tracking:** Scale when avg CPU > 65%
- **Scheduled:** Pre-warm before known peaks (11 AM lunch, 6:30 PM dinner)
- **Step:** Add 10 if CPU > 80% (aggressive for spikes)

### 11.2 EC2 Pricing

| Tier | Model | Savings | Use For |
|---|---|---|---|
| Always-on base | **Reserved Instances** (1-year) | ~40% | 24/7 servers |
| Predictable peaks | **Savings Plans** | ~30% | Daily lunch/dinner |
| Rare bursts | **Spot Instances** | up to **90%** | Flash sales, batch jobs |
| Fallback | **On-Demand** | 0% | When Spot unavailable |

### 11.3 Solved — [Practice] Flash sale 10x traffic architecture

```
Users → CloudFront (CDN, absorbs 80% of reads) → ALB → EC2 Auto Scaling (2→20)
                                                        ↓
                                                  ElastiCache (Redis)
                                                        ↓
                                                  RDS Aurora (Multi-AZ + read replicas)
```

Pre-sale: Schedule scale to 10 instances 30min before. CloudFront caches product images. Redis caches product data. Auto Scaling handles incremental spikes.

---
---

# 📋 QUICK REVISION — Last 15 Minutes Before Exam

## Key Numbers

| Fact | Value |
|---|---|
| S3 durability | **11 nines (99.999999999%)** |
| NIST essential characteristics | **5** |
| Service models | **3** (IaaS, PaaS, SaaS) |
| Deployment models | **5** (Public, Private, Hybrid, Community, Multi-cloud) |
| Hypervisor types | **3** (Type 1, Type 2, Hybrid) |
| Virtualization types | **4** (Full, Para, HW-Assisted, OS-Level) |
| x86 sensitive-not-privileged instructions | **~17** |
| Container namespace types | **6** (PID, NET, MNT, UTS, IPC, USER) |
| Memory overcommitment techniques | **4** (TPS→Balloon→Compress→Swap) |
| EC2 instance families | **5** (General, Compute, Memory, Storage, Accelerated) |
| Placement groups | **3** (Cluster, Spread, Partition) |
| AMI types | **4** |
| 99.99% uptime = max downtime/year | **~52 minutes** |

## One-Liners

| Topic | Remember |
|---|---|
| Full virtualization | Binary translation, guest unmodified, moderate perf |
| Para-virtualization | Hypercalls, guest modified, good perf |
| Hardware-Assisted | VT-x/AMD-V, guest unmodified, best perf, **DOMINANT** |
| Containers | Shared kernel, namespaces+cgroups, millisecond boot |
| VM vs Container | VM=strong isolation, Container=lightweight+fast |
| IaaS | You manage OS+apps (EC2) |
| PaaS | You manage apps+data (Heroku) |
| SaaS | Just use it (Gmail) |
| Security Groups | Stateful, instance-level, allow-only |
| NACLs | Stateless, subnet-level, allow+deny |
| TPS | Merge identical pages across VMs |
| Ballooning | Guest driver reclaims memory |
| Host Swapping | LAST resort, disk 100,000× slower |
| Docker | Build once, run anywhere. Image=layers, Container=running instance |
| Kubernetes | Declarative desired state. Pod=smallest unit. Self-healing+auto-scaling |
| Cloud-Native | Microservices + containers + declarative + resilience |

## TRUE/FALSE Quick Facts

1. ✅ Hardware-Assisted virtualization is dominant today (all modern CPUs support VT-x)
2. ❌ Para-virtualization can run unmodified Windows — NO, needs kernel source modification
3. ✅ Containers share host kernel — weaker isolation than VMs
4. ✅ S3 has 11 nines durability (99.999999999%)
5. ❌ Security Groups can deny traffic — NO, allow-only. NACLs can deny.
6. ✅ Memory overcommitment: TPS activates first, host swapping is last resort
7. ❌ EBS can be shared across multiple EC2 instances — NO, single instance (same AZ). Use EFS for sharing.
8. ✅ CPU Ready > 5% indicates overcommitment
9. ✅ Kubernetes is declarative — you describe desired state, K8s makes it happen
10. ❌ Containers can run different OS from host — NO, same OS family (shared kernel)
11. ✅ In production, containers run inside VMs (VM for tenant isolation, container for app packaging)
12. ✅ x86 has 17 sensitive-but-not-privileged instructions that break trap-and-emulate

## Exam Tips

1. **Time:** 40 min per question. Don't spend 60 min on Q1.
2. **Use tables** for comparisons — always scores higher than paragraphs.
3. **Scenario questions:** Recommend FIRST in the opening sentence, THEN justify.
4. **Draw ASCII diagrams** for architecture questions.
5. **Mention specific numbers:** "11 nines durability", "17 sensitive instructions", "CPU Ready > 5%"
6. **Cover marks proportionally:** 6-mark = 6 distinct points. 4-mark = 4 points.

---

*You've got this. 2-3 hours with this document = exam ready.* 🎯
