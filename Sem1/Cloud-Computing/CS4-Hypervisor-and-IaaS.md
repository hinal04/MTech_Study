# CS4: Hypervisors and Introduction to IaaS

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:** T1: Ch2, Ch9 | T2: Ch3, Ch5 | R3 | R8
>
> **Contact Hours:** 7-8, 13-14 (Handout Topics 2.4 + 3.1-3.4)
> **Class Slide:** CS4 - HYPERVISOR_IAAS

---

## Table of Contents

- [2.4 x86 Hardware Virtualization (Detailed)](#24-x86-hardware-virtualization-detailed)
  - [Goals of a Hypervisor](#goals-of-a-hypervisor)
  - [The Virtualization Stack](#the-virtualization-stack--how-it-all-fits-together)
  - [The Ring Problem Revisited](#the-ring-problem-revisited)
  - [BOCHS — Pure Emulation](#bochs--pure-emulation-contrast-with-virtualization)
  - [Hypervisor Deep-Dive](#hypervisor-deep-dive)
- [3.1 Introduction to IaaS](#31-introduction-to-iaas)
- [3.2 IaaS Architecture and Reference Model](#32-iaas-architecture-and-reference-model)
  - [IaaS Architecture — The Four Layers](#iaas-architecture--the-four-layers-in-detail)
  - [IaaS Actors — Who Does What?](#iaas-actors--who-does-what)
- [3.3 AWS as an IaaS Reference Platform](#33-aws-as-an-iaas-reference-platform)
  - [AWS IAM](#aws-iam-identity-and-access-management)
- [3.4 Regions, Availability Zones, and Edge Locations](#34-regions-availability-zones-and-edge-locations)
  - [AWS Local Zones](#aws-local-zones--extending-a-region-closer-to-users)
  - [AWS Wavelength Zones](#aws-wavelength-zones--5g-edge-computing)
  - [AWS Outposts](#aws-outposts--aws-in-your-data-centre)
- [VM Provisioning and Migration (from Lecture 7)](#vm-provisioning-and-migration)

---

## 2.4 x86 Hardware Virtualization (Detailed)

### Goals of a Hypervisor

Before diving into the technical details of x86 virtualization, it's important to understand **what a hypervisor is trying to achieve**. A hypervisor (also called a Virtual Machine Monitor or VMM) has four fundamental goals:

#### 1. Isolation — Fault and Security Containment

Each virtual machine runs in its own **isolated sandbox**. A crash, security breach, or misconfiguration inside one VM **cannot** affect any other VM running on the same physical host.

**Analogy:** Think of apartments in a building. If the kitchen catches fire in Apartment 3A, the fire walls prevent the fire from spreading to 3B. Each apartment has its own plumbing, electricity meter, and front door — they share the building's infrastructure but are isolated from each other's problems.

**Why this matters in the cloud:** AWS might run your VM on the same physical server as a completely different company's VM. Without isolation, a malicious or buggy workload on one VM could read memory, consume all CPU, or crash VMs belonging to other customers. The hypervisor enforces strict boundaries:

| Isolation Type | What it Prevents | How it's Enforced |
|---|---|---|
| **Memory isolation** | One VM reading/writing another VM's RAM | Hardware page tables + hypervisor memory management (EPT/RVI) |
| **CPU isolation** | One VM monopolising all CPU cycles | CPU scheduling with fair-share allocation and time slicing |
| **I/O isolation** | One VM flooding the network or disk, starving others | I/O schedulers, rate limiting, SR-IOV for network isolation |
| **Security isolation** | One VM exploiting vulnerabilities to access another | Separate address spaces, privilege ring enforcement, hardware-assisted boundaries |

#### 2. Encapsulation — VM as a Portable File

The **entire state** of a virtual machine — its memory contents, CPU register state, disk contents, device configuration — can be captured and saved as a **set of files**. This makes VMs portable, snapshotable, and easy to manage.

**What gets encapsulated:**
- **Virtual disk files** (e.g., `.vmdk` for VMware, `.qcow2` for KVM) — the entire contents of the VM's hard drive
- **Configuration files** — number of CPUs, memory size, network adapters, boot order
- **Memory state file** (when suspended) — a snapshot of all RAM contents
- **Snapshot metadata** — point-in-time snapshots that let you roll back

**Practical benefits:**
- **Snapshots:** Take a snapshot before a risky software update. If it breaks, roll back in seconds.
- **Cloning:** Create an exact copy of a VM for testing — no reinstallation needed.
- **Backup:** Back up an entire server by copying a few files.
- **Migration:** Move a VM from one physical host to another by transferring files (this is what vMotion and live migration do).

**Analogy:** Imagine if you could save your entire laptop — every open application, every file, every browser tab — as a single ZIP file. You could copy that ZIP to a completely different laptop, extract it, and pick up exactly where you left off. That's what VM encapsulation enables, but for servers.

#### 3. Hardware Independence — Run Any OS on Any Hardware

Virtual machines are **decoupled from the physical hardware** they run on. The hypervisor presents a **standardised set of virtual hardware** to each VM, regardless of what the actual physical hardware looks like.

**What this means in practice:**
- A VM created on a Dell server with Intel CPUs can be migrated to an HP server with different Intel CPUs — the VM doesn't notice.
- A Windows VM can run on a Linux host. A Linux VM can run on a Windows host.
- VMs don't need hardware-specific drivers for every physical NIC, storage controller, or GPU — they use virtualised device drivers.

**Analogy:** It's like how a USB port works. You don't need a different USB port for every USB device — the standard interface abstracts away the hardware differences. Similarly, the hypervisor provides a standard "virtual hardware interface" that any guest OS can talk to.

| Without Hardware Independence | With Hardware Independence |
|---|---|
| OS tied to specific server hardware | OS runs in a VM on any compatible host |
| Server replacement = OS reinstall | Server replacement = VM migration |
| Hardware driver issues when moving | Standard virtual hardware drivers everywhere |
| Vendor lock-in to hardware | Freedom to choose/mix hardware vendors |

#### 4. Partitioning — Divide Physical Resources Among VMs

A single physical server's resources (CPU cores, RAM, storage, network bandwidth) can be **divided** among multiple virtual machines, each getting its own allocated share.

**How resources are partitioned:**

| Physical Resource | How it's Partitioned | Example |
|---|---|---|
| **CPU** | Virtual CPUs (vCPUs) assigned to each VM. Hypervisor schedules vCPUs onto physical cores. | 32-core server → VM1 gets 8 vCPUs, VM2 gets 16 vCPUs, VM3 gets 8 vCPUs |
| **Memory** | RAM allocated to each VM from the physical pool. Can be overcommitted (allocate more total than physically available). | 128 GB RAM → VM1 gets 32 GB, VM2 gets 64 GB, VM3 gets 32 GB |
| **Storage** | Virtual disks carved from physical storage (local SSDs or SAN/NAS). | 2 TB SSD → VM1 gets 500 GB, VM2 gets 1 TB, VM3 gets 500 GB |
| **Network** | Virtual NICs with bandwidth allocation. VLANs for traffic isolation. | 10 Gbps NIC → VM1 gets 2 Gbps, VM2 gets 5 Gbps, VM3 gets 3 Gbps |

**Overcommitment:** Hypervisors allow **overcommitting** resources — allocating more virtual resources than physically exist. This works because most VMs don't use 100% of their allocated resources simultaneously. For example, you might allocate 256 GB of virtual RAM across VMs on a host with only 128 GB of physical RAM, relying on the fact that most VMs use far less than their allocation at any given moment. This significantly improves hardware utilisation.

**Analogy:** Airlines overbook flights because not everyone shows up. Similarly, hypervisors overcommit resources because not all VMs use their full allocation simultaneously. Both work well — until everyone actually needs their resources at the same time (then you have a problem).

#### Summary: The Four Pillars of Virtualization

```
┌─────────────────────────────────────────────────────────────────┐
│                    HYPERVISOR GOALS                               │
├────────────────┬────────────────┬─────────────────┬──────────────┤
│   ISOLATION    │ ENCAPSULATION  │   HARDWARE      │ PARTITIONING │
│                │                │   INDEPENDENCE   │              │
│ VM crash in A  │ Entire VM      │ Run any OS on   │ Divide CPU,  │
│ doesn't affect │ state saved    │ any hardware    │ RAM, storage │
│ VM B           │ as files       │ without changes │ among VMs    │
└────────────────┴────────────────┴─────────────────┴──────────────┘
```

---

### The Ring Problem Revisited

The x86 architecture uses four privilege levels (Ring 0-3). The OS kernel needs Ring 0 for hardware access. In virtualization, the hypervisor also needs Ring 0. This creates a conflict since only one entity can have Ring 0 at a time.

### Solutions Implemented in Production

| Solution | How it works | Used by |
|---|---|---|
| **Binary Translation** | Hypervisor at Ring 0, guest OS at Ring 1. Hypervisor scans guest instructions and replaces privileged ones at runtime. | Early VMware (before VT-x) |
| **Para-virtualization** | Guest OS kernel modified to make hypercalls directly to hypervisor. No trapping needed. | Xen (PV mode) |
| **Hardware-Assisted (VT-x/AMD-V)** | CPU adds VMX root mode (hypervisor) and non-root mode (guest). Hardware traps privileged instructions automatically. | KVM, ESXi, Hyper-V — **dominant today** |
| **Extended Page Tables (EPT/RVI)** | Hardware handles two-level address translation (guest virtual → guest physical → host physical) without software shadow page tables. | All modern hypervisors |

### BOCHS — Pure Emulation (Contrast with Virtualization)

**BOCHS** is an open-source **x86 PC emulator** that takes a fundamentally different approach from hypervisors. Instead of using hardware-assisted virtualization (VT-x/AMD-V), BOCHS uses **pure interpretation** — it emulates the entire x86 hardware stack (CPU, memory, I/O devices, BIOS) in software, translating every single guest instruction at runtime.

This makes BOCHS **extremely slow** compared to hypervisor-based virtualization (orders of magnitude slower), but it is invaluable for **OS development, debugging, and education**. Because BOCHS emulates hardware entirely in software, it can run on any host architecture and provides deep introspection into guest behaviour — you can step through individual CPU instructions, inspect register state, and debug boot sequences that would be opaque on a real hypervisor.

**Why emulation matters for understanding virtualization:**

Emulation is the **conceptual foundation** on which all virtualization techniques are built. Before you can understand why hardware-assisted virtualization (VT-x/AMD-V) is fast, you need to understand what pure emulation does and why it's slow. BOCHS demonstrates the baseline approach:

| Aspect | BOCHS (Pure Emulation) | Hypervisor (KVM/ESXi with VT-x) |
|---|---|---|
| **How instructions execute** | Every guest CPU instruction is read, decoded, and simulated in software | Guest instructions run directly on the physical CPU; only privileged ops are trapped |
| **Performance** | 100–1000× slower than bare metal | 2–5% overhead vs bare metal |
| **Portability** | Runs on ANY host CPU (x86, ARM, MIPS) — the guest CPU is fully simulated | Requires host CPU with VT-x/AMD-V and same architecture (x86 guest on x86 host) |
| **Introspection** | Full visibility — step through every instruction, inspect every register | Limited visibility — guest runs at near-native speed, harder to inspect |
| **Use case** | OS development, debugging boot sequences, education, security research | Production cloud computing, running real workloads at scale |

**Analogy:** Imagine translating a book from English to French. BOCHS is like translating every single word one at a time using a dictionary (accurate but painfully slow). A hypervisor with VT-x is like hiring a bilingual reader who reads English natively and only pauses to translate the few words that don't exist in French (fast, with minimal interruption).

**The virtualization spectrum:**

```
SLOW ◄──────────────────────────────────────────────────────────► FAST

Pure Emulation     Binary Translation     Para-virtualization     Hardware-Assisted
(BOCHS)            (Early VMware)         (Xen PV)                (KVM + VT-x)

Every instruction   Privileged instr.      Guest OS modified       CPU handles traps
simulated in SW     rewritten at runtime   to call hypervisor      in hardware (VMX)
                                           directly (hypercalls)

100-1000× slower    10-20× slower          5-10% overhead          2-5% overhead
```

Understanding this spectrum — from BOCHS's full emulation to KVM's hardware-assisted approach — is essential for appreciating why modern cloud computing is possible at all. If we were still stuck at BOCHS-level performance, running thousands of VMs in a data centre would be computationally infeasible.

### The Virtualization Stack — How It All Fits Together

Understanding the **layered architecture** of a virtualized system is critical. Here's how the layers stack from bottom to top:

```
┌──────────────────────────────────────────────────┐
│         APPLICATION LAYER                         │
│  Web servers, databases, custom applications      │
│  (runs inside the guest OS, thinks it's on real   │
│   hardware — completely unaware of virtualization) │
├──────────────────────────────────────────────────┤
│         GUEST OS LAYER                            │
│  Windows, Linux, macOS — a full operating system  │
│  running inside the VM. Manages processes, memory,│
│  file systems, device drivers (virtual devices).  │
├──────────────────────────────────────────────────┤
│         VIRTUAL MACHINE (VM) LAYER                │
│  The VM abstraction — virtual CPU, virtual RAM,   │
│  virtual disk, virtual NIC. Presented to the      │
│  guest OS as if they were real hardware.           │
├──────────────────────────────────────────────────┤
│         HYPERVISOR LAYER                          │
│  The VMM — manages all VMs, intercepts privileged │
│  instructions, schedules vCPUs on physical cores, │
│  manages memory mapping, controls I/O access.     │
├──────────────────────────────────────────────────┤
│         PHYSICAL HARDWARE LAYER                   │
│  CPU (with VT-x/AMD-V), RAM, SSDs, NICs,         │
│  GPUs, motherboard, BIOS/UEFI firmware            │
└──────────────────────────────────────────────────┘
```

#### How the Hypervisor Intercepts Privileged Instructions

When a guest OS (running inside a VM) tries to execute a **privileged instruction** — such as modifying page tables, accessing hardware I/O ports, or changing interrupt settings — it cannot be allowed to execute directly on the CPU. If it did, it would affect the entire physical machine and other VMs.

The hypervisor handles this through a mechanism called **trap-and-emulate**:

1. **Guest OS issues a privileged instruction** (e.g., writing to a control register).
2. **CPU hardware detects** that this instruction is being executed in non-root mode (inside a VM, not by the hypervisor).
3. **CPU traps** (generates a VM exit) — execution transfers from the VM to the hypervisor.
4. **Hypervisor examines** the trapped instruction and determines what the guest was trying to do.
5. **Hypervisor emulates** the intended effect safely — updating virtual state without affecting other VMs or the host.
6. **Hypervisor resumes** the VM — execution returns to the guest where it left off.

**Example:** When a Linux guest OS running in a VM tries to update its page table (a Ring 0 operation), the CPU traps to the hypervisor. The hypervisor updates the guest's **virtual page table** mapping and then uses Extended Page Tables (EPT) to translate the guest's physical addresses to the host's actual physical addresses. The guest never touches real hardware.

| Step | What Happens | Who's in Control |
|---|---|---|
| 1. Guest executes `mov cr3, eax` | Tries to change page table base register | Guest OS (VM) |
| 2. CPU detects privileged op in non-root mode | Hardware trap triggers VM exit | CPU hardware |
| 3. Hypervisor entry point invoked | Hypervisor reads exit reason and instruction | Hypervisor |
| 4. Hypervisor emulates the effect | Updates guest's virtual CR3, adjusts EPT | Hypervisor |
| 5. Hypervisor executes `vmresume` | Returns control to guest VM | Hypervisor → Guest |

This trap-and-emulate cycle happens **thousands of times per second** during normal VM operation, but with hardware-assisted virtualization (VT-x/AMD-V), the overhead is minimal — typically **2-5%** compared to bare-metal performance.

---

### Hypervisor Deep-Dive

#### Type 1 Hypervisors in Production

| Hypervisor | Developer | Key characteristics | Used by |
|---|---|---|---|
| **KVM** (Kernel-based Virtual Machine) | Open-source (Linux) | Built into Linux kernel. Uses hardware virtualization (VT-x). Lightweight — the Linux kernel IS the hypervisor. | AWS (Nitro/KVM), Google Cloud, DigitalOcean, Red Hat |
| **VMware ESXi** | VMware (Broadcom) | Commercial bare-metal hypervisor. Mature ecosystem (vSphere, vCenter, vMotion). Industry standard for enterprise. | Enterprise private clouds, VMware Cloud on AWS |
| **Microsoft Hyper-V** | Microsoft | Built into Windows Server. Strong Windows VM support. Also runs Linux guests. | Azure, enterprise Windows environments |
| **Xen** | Open-source (Linux Foundation) | Pioneered para-virtualization. Supports both PV and HVM modes. | Citrix (XenServer), early AWS (before Nitro) |

#### Type 2 Hypervisors (Development/Testing Only)

| Hypervisor | Platform | Use case |
|---|---|---|
| **Oracle VirtualBox** | Cross-platform (free) | Development, testing, learning |
| **VMware Workstation/Fusion** | Windows/macOS (commercial) | Professional development |
| **Parallels Desktop** | macOS (commercial) | Running Windows on Mac |

#### AWS Nitro System — Modern Cloud Hypervisor

AWS evolved from Xen to the **Nitro System**, a custom hypervisor architecture:

- **Nitro Hypervisor:** Lightweight KVM-based hypervisor. Offloads networking, storage, and security to dedicated Nitro hardware cards.
- **Nitro Cards:** Custom ASIC chips that handle VPC networking, EBS storage I/O, and instance monitoring — removing this work from the host CPU.
- **Nitro Security Chip:** Hardware-based security that prevents unauthorized access to instance storage and memory.
- **Result:** Near-bare-metal performance for VMs because the hypervisor overhead is offloaded to hardware.

---

## 3.1 Introduction to IaaS

### What is IaaS?

**Infrastructure as a Service (IaaS)** is the delivery of computing infrastructure — servers, storage, networking, and data centre resources — as on-demand, pay-per-use services over the internet.

From the lecture notes: *"The delivery of services such as hardware, software, storage, networking, data center space, and various utility software elements on request. Both public and private versions of IaaS exist."*

### Key Characteristics of IaaS

| Characteristic | Description |
|---|---|
| **On-demand provisioning** | Users can spin up VMs, storage, and networks in minutes via a self-service portal or API. No procurement cycle. |
| **Pay-per-use** | Billed based on resource consumption (per hour of compute, per GB of storage, per GB of data transfer). No upfront CapEx. |
| **Elastic scaling** | Scale resources up during peaks, down during lulls. Automatically or manually. |
| **Multi-tenancy** | Multiple customers share the same physical infrastructure, isolated by hypervisor and virtual networking. |
| **Self-service** | Users provision resources through web consoles, CLIs, or APIs without contacting the provider. |
| **Dynamic** | Infrastructure can be reconfigured, resized, or destroyed at any time. |

### Public vs Private IaaS

| Aspect | Public IaaS | Private IaaS |
|---|---|---|
| **Provider** | Third-party cloud provider (AWS, Azure, GCP) | Organisation's own IT team or dedicated third party |
| **Users** | Anyone with a credit card | Internal users and sometimes partners |
| **Infrastructure** | Shared across thousands of customers | Dedicated to one organisation |
| **Sign-up** | Simple online registration | Internal approval process |
| **Examples** | AWS EC2, Azure VMs, Google Compute Engine | OpenStack, VMware vSphere, AWS Outposts |

### Why IaaS Matters

IaaS is the **foundation** on which PaaS and SaaS are built. Understanding the responsibility split between provider and customer is essential:

**The IaaS Shared Responsibility Model:**

```
┌─────────────────────────────────────────────────────────────────┐
│                    CUSTOMER RESPONSIBILITY                       │
│  ("Security IN the cloud")                                       │
│                                                                  │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────────────┐  │
│  │ Applications│  │ Data         │  │ Security configuration │  │
│  │ & Code      │  │ & Encryption │  │ (IAM, firewalls,       │  │
│  └─────────────┘  └──────────────┘  │  patching guest OS)    │  │
│  ┌─────────────┐  ┌──────────────┐  └────────────────────────┘  │
│  │ Runtime &   │  │ Operating    │                               │
│  │ Middleware   │  │ System       │                               │
│  └─────────────┘  └──────────────┘                               │
├─────────────────────────────────────────────────────────────────┤
│                    PROVIDER RESPONSIBILITY                       │
│  ("Security OF the cloud")                                       │
│                                                                  │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────────────┐  │
│  │ Hypervisor  │  │ Physical     │  │ Data centre security   │  │
│  │ Layer       │  │ Networking   │  │ (power, cooling, locks,│  │
│  └─────────────┘  └──────────────┘  │  cameras, guards)      │  │
│  ┌─────────────┐  ┌──────────────┐  └────────────────────────┘  │
│  │ Physical    │  │ Storage      │                               │
│  │ Servers     │  │ Hardware     │                               │
│  └─────────────┘  └──────────────┘                               │
└─────────────────────────────────────────────────────────────────┘
```

**Key exam point:** In IaaS, the customer has MORE responsibility than in PaaS or SaaS. The provider manages the physical infrastructure and hypervisor; the customer manages everything from the OS upward. This is a trade-off — more control means more responsibility for security and maintenance.

**IaaS vs Traditional IT — Total Cost Comparison:**

| Cost Category | Traditional IT (On-Premises) | IaaS (Cloud) |
|---|---|---|
| **Hardware purchase** | $50K–$500K per server rack (CapEx) | $0 upfront (OpEx — pay monthly) |
| **Data centre space** | $1K–$10K/month rent, power, cooling | Included in service price |
| **IT staff for hardware** | 2–5 system admins ($80K–$150K/year each) | 0 for hardware (focus on application ops) |
| **Time to provision** | 6–12 weeks | 2–5 minutes |
| **Scaling** | Buy new hardware (weeks + CapEx) | API call (minutes, no CapEx) |
| **Hardware refresh cycle** | Every 3–5 years (major CapEx event) | Provider handles continuously |
| **Utilisation rate** | Typically 15–30% (massive waste) | Near 100% (pay only for what you use) |
| **Disaster recovery** | Secondary data centre ($$$) | Multi-AZ/Multi-Region (built-in) |

---

## 3.2 IaaS Architecture and Reference Model

### The IaaS Stack

```
┌─────────────────────────────────────────────────┐
│           Customer-Managed Layer                  │
│  Applications, Data, Runtime, Middleware, OS      │
├─────────────────────────────────────────────────┤
│           Virtualisation Layer                    │
│  VMs, Containers, Virtual Networks, Virtual Disks │
├─────────────────────────────────────────────────┤
│           Physical Infrastructure                 │
│  Compute (servers), Storage (SAN/SSD),            │
│  Network (switches/routers), Data Centres         │
├─────────────────────────────────────────────────┤
│           Management & Orchestration              │
│  Provisioning, Monitoring, Billing, IAM,          │
│  Auto-scaling, Load Balancing                     │
└─────────────────────────────────────────────────┘
```

### IaaS Components

| Component | What it provides | AWS Example |
|---|---|---|
| **Compute** | Virtual machines (instances) with configurable CPU, memory, GPU | EC2 (Elastic Compute Cloud) |
| **Storage** | Block storage (virtual disks), Object storage (files), File storage (shared) | EBS (block), S3 (object), EFS (file) |
| **Networking** | Virtual private clouds, subnets, load balancers, DNS, CDN | VPC, ELB, Route 53, CloudFront |
| **Security** | Firewalls, identity management, encryption, DDoS protection | Security Groups, IAM, KMS, Shield |
| **Management** | Monitoring, auto-scaling, provisioning APIs, billing | CloudWatch, Auto Scaling, CloudFormation |

### IaaS Architecture — The Four Layers in Detail

The IaaS architecture can be understood as four distinct layers, each with its own responsibilities:

#### 1. Compute Layer

The compute layer provides the **raw processing power** — virtual machines, bare-metal servers, and containers that run customer workloads.

| Aspect | Detail |
|---|---|
| **What it provides** | Virtual CPUs, RAM, and GPU resources allocated to customer instances |
| **Key technology** | Hypervisors (KVM, ESXi) partition physical servers into multiple VMs |
| **How it scales** | Horizontal (add more VMs) or vertical (resize to larger instance type) |
| **AWS implementation** | EC2 instances, Lambda (serverless compute), ECS/EKS (container compute) |

**How compute works under the hood:** The cloud provider maintains vast pools of physical servers in data centres. When a customer requests a VM, the orchestration layer selects a physical host with available capacity, the hypervisor creates a new VM with the requested CPU and memory, loads the OS image (AMI), and the instance becomes available within minutes. The customer sees a virtual server; the provider manages thousands of physical servers behind the scenes.

#### 2. Storage Layer

The storage layer provides **persistent data storage** that survives beyond the life of individual compute instances.

| Storage Type | How it Works | Characteristics | AWS Service |
|---|---|---|---|
| **Block storage** | Presents raw disk volumes to VMs. Data stored in fixed-size blocks. Accessed like a local hard drive. | Low latency, high IOPS, single-instance attachment | EBS |
| **Object storage** | Stores data as objects (file + metadata + unique key) via HTTP APIs. Flat namespace, no hierarchy. | Massively scalable, cheap, accessed via REST API | S3 |
| **File storage** | Presents a shared file system (NFS/SMB) that multiple VMs can mount simultaneously. | Concurrent access, POSIX-compliant, shared state | EFS |
| **Archival storage** | Extremely low-cost storage for data that is rarely accessed. Retrieval takes minutes to hours. | Cheapest per GB, slow retrieval, compliance use | Glacier |

**Analogy:** Think of storage types like different containers for your belongings:
- **Block storage** = a personal locker — fast access, only you have the key, fixed size
- **Object storage** = a warehouse with labelled boxes — massive capacity, find anything by label, but you go through a reception desk (API) to access it
- **File storage** = a shared filing cabinet — multiple people can open drawers simultaneously
- **Archival storage** = a long-term storage unit — cheap rent, but it takes a while to retrieve your stuff

#### 3. Networking Layer

The networking layer provides the **virtual network infrastructure** that connects compute instances to each other, to storage, and to the internet.

| Component | Purpose | AWS Service |
|---|---|---|
| **Virtual Private Cloud** | Isolated virtual network with customer-defined IP ranges | VPC |
| **Subnets** | Divide VPC into public (internet-facing) and private (internal) segments | VPC Subnets |
| **Load Balancers** | Distribute traffic across multiple instances for availability | ELB (ALB, NLB, GWLB) |
| **DNS** | Translate domain names to IP addresses, with health-checking and routing policies | Route 53 |
| **CDN** | Cache content at edge locations globally for low-latency delivery | CloudFront |
| **Firewalls** | Filter inbound/outbound traffic at instance (Security Groups) and subnet (NACLs) levels | Security Groups, NACLs |
| **Private connectivity** | Dedicated network connections from on-premises to cloud, bypassing public internet | Direct Connect |

**Key concept — Software-Defined Networking (SDN):** In traditional networking, every network change requires configuring physical switches and routers. In IaaS, the networking layer is **software-defined** — you create VPCs, subnets, and routing rules via API calls. The physical network hardware is abstracted away. This is why you can create an entirely new network topology in minutes, something that would take weeks with physical hardware.

#### 4. Management and Orchestration Layer

This is the **brain** of the IaaS platform — the control plane that coordinates all the other layers.

| Function | What it Does | AWS Service |
|---|---|---|
| **Provisioning** | Automate the creation and configuration of infrastructure resources | CloudFormation, Terraform |
| **Monitoring** | Track resource health, performance metrics, and set alarms | CloudWatch |
| **Auto-scaling** | Automatically adjust compute capacity based on demand metrics | Auto Scaling |
| **Billing & metering** | Track resource usage and generate cost reports | AWS Cost Explorer, Billing |
| **Identity & access** | Control who can access what resources | IAM |
| **Logging & auditing** | Record all API calls for security and compliance | CloudTrail |

**Why orchestration matters:** Without a management layer, a cloud provider would just be a bunch of servers. The orchestration layer is what makes it a **service** — enabling self-service provisioning, automatic scaling, usage-based billing, and API-driven infrastructure. It's the difference between renting a raw server and using a cloud platform.

### IaaS Actors — Who Does What?

The IaaS ecosystem involves four key actors (defined by NIST), each with distinct roles:

| Actor | Role | Responsibilities | Example |
|---|---|---|---|
| **Cloud Provider** | Owns, operates, and maintains the physical infrastructure and cloud platform | Manages data centres, hardware, hypervisors, networking; ensures SLAs for uptime, performance, and security; provides APIs and management console | AWS, Microsoft Azure, Google Cloud Platform |
| **Cloud Consumer** | Uses cloud services to build and run applications | Selects services, configures resources (VMs, storage, networks), deploys applications, manages OS and above, pays usage-based bills | A startup running its web application on EC2, a bank storing data in S3 |
| **Cloud Broker** | Intermediary that manages use, performance, and delivery of cloud services | Aggregates services from multiple providers, provides a single management interface, handles billing consolidation, helps consumers select optimal services | Rackspace Managed Cloud, RightScale (Flexera), Morpheus |
| **Cloud Auditor** | Independent party that assesses cloud services for security, privacy, and compliance | Performs security audits, verifies compliance (SOC 2, ISO 27001, HIPAA), evaluates privacy controls, assesses performance against SLAs | PwC, Deloitte, independent security assessors |

**How the actors interact:**

```
                    ┌───────────────┐
                    │  Cloud Auditor │
                    │ (independent   │
                    │  assessment)   │
                    └───────┬───────┘
                            │ audits
                            ▼
┌──────────────┐    ┌───────────────┐    ┌──────────────┐
│ Cloud        │◄──►│ Cloud         │◄──►│ Cloud        │
│ Consumer     │    │ Broker        │    │ Provider     │
│ (uses)       │    │ (intermediary)│    │ (operates)   │
└──────────────┘    └───────────────┘    └──────────────┘
```

**Cloud Broker in detail:** A cloud broker adds value in three ways:
1. **Service Intermediation** — enhances a service (e.g., adding security monitoring on top of basic EC2)
2. **Service Aggregation** — combines multiple cloud services into a single solution (e.g., compute from AWS + storage from Azure)
3. **Service Arbitrage** — dynamically selects the best provider based on price, performance, or availability

---

## 3.3 AWS as an IaaS Reference Platform

### Why AWS as Reference?

AWS is the largest and most mature cloud provider (33% market share as of 2024). It launched in 2006 and has the broadest service catalogue. The lecture notes use AWS as the primary reference for IaaS concepts.

From lecture notes: *"Amazon launched AWS so that other organizations could benefit from Amazon's experience and investment in running a large-scale distributed, transactional IT infrastructure. AWS has been operating since 2006, and today serves hundreds of thousands of customers worldwide."*

### AWS Distinguishing Characteristics (from Lecture 5-6)

| Characteristic | Description |
|---|---|
| **Flexible** | Supports any programming model, OS, database, and architecture. Mix and match as needed. |
| **Cost-effective** | Pay only for what you use. No upfront commitments. |
| **Scalable and elastic** | Add/remove resources to meet demand and manage costs. |
| **Secure** | Built with security best practices. End-to-end encryption. Compliance certifications. |
| **Experienced** | Leverages Amazon's 15+ years of large-scale infrastructure experience. |

### Key AWS IaaS Services Overview

| Category | Service | What it does |
|---|---|---|
| **Compute** | EC2 | Resizable VMs in the cloud. Full OS control. |
| **Compute** | Auto Scaling | Automatically scale EC2 capacity based on demand. |
| **Compute** | Elastic Load Balancing | Distribute traffic across multiple EC2 instances. |
| **Networking** | VPC | Isolated virtual network you define. |
| **Networking** | Route 53 | DNS service (domain name → IP address). |
| **Storage** | S3 | Object storage — any amount of data, any time, from anywhere. |
| **Storage** | EBS | Block storage volumes attached to EC2 instances. |
| **Storage** | Glacier | Extremely low-cost archival storage. |
| **CDN** | CloudFront | Content delivery via global edge locations. |
| **Management** | CloudWatch | Monitoring for cloud resources and applications. |
| **Deployment** | Elastic Beanstalk | PaaS-like deployment for web apps (deploys on EC2 behind the scenes). |

### AWS IAM (Identity and Access Management)

**IAM** is the AWS service that controls **who** (authentication) can do **what** (authorization) in your AWS account. Every API call to AWS is checked against IAM policies. It is a global service — not tied to any single region.

**Analogy:** IAM is like the security system of a large office building:
- **Users** = employees with ID badges (each person has their own credentials)
- **Groups** = departments (everyone in "Engineering" has access to the server room)
- **Roles** = temporary visitor passes (a contractor gets a temporary badge for specific floors)
- **Policies** = the access rules programmed into the badge system (which doors each badge can open)
- **Root account** = the building master key (opens every door — extremely dangerous if stolen)

#### Authentication — "Who are you?"

| Method | Used for | How it works | Security Level |
|---|---|---|---|
| **Username + Password** | AWS Management Console (web UI) | Human users sign in via browser. | Base (add MFA for stronger security) |
| **Access Key ID + Secret Access Key** | AWS CLI and SDKs (programmatic access) | Long-lived credentials used in scripts and applications. | Lower (keys can be leaked; prefer roles) |
| **MFA (Multi-Factor Authentication)** | Added security layer on top of password or keys | Requires a second factor (virtual MFA app, hardware token) in addition to credentials. | High (recommended for all accounts) |
| **Temporary Security Credentials** | Roles assumed by services, federated users | Short-lived credentials (15 min to 36 hours) obtained via AWS STS. Auto-rotate. | Highest (no long-term secrets to manage) |

#### Authorization — "What are you allowed to do?"

Permissions are defined by **IAM Policies** and evaluated every time an API call is made. By default, all actions are **denied** — you must explicitly grant access.

#### IAM Identities — Deep Dive

| Identity | Description | Credentials | Lifetime | Best For |
|---|---|---|---|---|
| **User** | Represents a person or application with **permanent credentials**. Each user has a unique name within the account. | Password (console) + Access keys (CLI/API) | Permanent until deleted | Individual developers, CI/CD service accounts |
| **Group** | A **collection of users**. Policies attached to the group apply to **all members**. A user can belong to multiple groups. Groups cannot be nested (no groups within groups). | None (groups don't have credentials) | As long as it exists | Managing permissions for teams: `Developers`, `Admins`, `ReadOnlyAuditors` |
| **Role** | An identity **without permanent credentials**. Any user, service, or application can **assume** a role to get temporary credentials. Roles are the preferred way to grant access to AWS services. | Temporary (via AWS STS) — automatically rotated | Session-based (15 min to 36 hours) | EC2 instances accessing S3, Lambda functions calling DynamoDB, cross-account access, federated users |

**Key insight — Why Roles over Access Keys for services:**

| Approach | Risk | Management Overhead |
|---|---|---|
| **Hardcoded access keys** in EC2 instance | If keys are leaked (e.g., committed to Git), attacker has permanent access | Must manually rotate keys, track where they're stored |
| **IAM Role** attached to EC2 instance | No long-term credentials exist to leak. Temporary credentials auto-rotate every few hours. | Zero — AWS handles credential rotation automatically |

**Example scenario:** An EC2 instance needs to read files from an S3 bucket. The secure approach:
1. Create an IAM Role called `EC2-S3-ReadOnly` with a policy allowing `s3:GetObject`.
2. Attach the role to the EC2 instance.
3. The EC2 instance automatically receives temporary credentials (via the instance metadata service).
4. The application on EC2 uses these credentials to access S3 — no access keys stored anywhere.

#### IAM Policies — The Permission Documents

Policies are **JSON documents** that define permissions. They specify:
- **Effect** — `Allow` or `Deny`
- **Action** — the AWS API action (e.g. `s3:GetObject`, `ec2:StartInstances`)
- **Resource** — the specific AWS resource (identified by ARN)
- **Condition** (optional) — when the policy applies (e.g., only from specific IP, only with MFA)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

This policy allows reading and writing objects in `my-bucket` but explicitly denies deletion. Policies are attached to users, groups, or roles.

**Types of IAM Policies:**

| Policy Type | Where it's Defined | Where it's Attached | Example |
|---|---|---|---|
| **AWS Managed Policy** | Created and maintained by AWS | Any user, group, or role | `AmazonS3ReadOnlyAccess` — a pre-built policy for S3 read access |
| **Customer Managed Policy** | Created by you in your account | Any user, group, or role | Your custom policy for specific bucket access |
| **Inline Policy** | Embedded directly in a single user, group, or role | Only the entity it's embedded in | A policy that should only ever apply to one specific role |

**Policy evaluation logic:** When a user makes an API call, IAM evaluates all applicable policies:
1. **Default deny** — everything is denied unless explicitly allowed.
2. Check all policies attached to the user, their groups, and any assumed role.
3. If ANY policy has an explicit **Deny**, the action is **denied** (Deny always wins).
4. If any policy has an **Allow** and no explicit Deny exists, the action is **allowed**.
5. If no policy mentions the action at all, it remains **denied** (default deny).

#### The Root Account — Handle with Extreme Care

The **root account** is the account created when you first sign up for AWS. It has **unrestricted access** to every service and resource in the account — it cannot be limited by IAM policies.

**Root account security best practices:**
- **Never use the root account for daily tasks** — create IAM users instead
- **Enable MFA on the root account immediately** (use a hardware MFA device if possible)
- **Do not create access keys for root** — if root access keys are compromised, the attacker has unlimited access
- **Lock away root credentials** — store the password and MFA device in a secure location (physical safe)
- **Use root only for tasks that require it** — changing account settings, closing the account, restoring IAM permissions

#### IAM Best Practices Summary

| Practice | Why |
|---|---|
| **Use root account only for initial setup** | Root has unrestricted access — too dangerous for daily use. |
| **Enable MFA on root account** | Protects against compromised root credentials. |
| **Use roles, not access keys, for services** | Roles provide temporary credentials that rotate automatically. Access keys are long-lived and can be leaked. |
| **Principle of least privilege** | Grant only the minimum permissions needed. Start with zero access and add permissions as required. |
| **Use groups for permission assignment** | Attach policies to groups, then add users to groups. Easier to manage than per-user policies. |
| **Rotate credentials regularly** | Minimises exposure if keys are compromised. |
| **Use IAM Access Analyzer** | Identifies resources shared externally and helps verify least privilege. |
| **Apply Service Control Policies (SCPs)** | In AWS Organisations, SCPs set permission guardrails across multiple accounts. |

---

## 3.4 Regions, Availability Zones, and Edge Locations

### The AWS Global Infrastructure Hierarchy

```
Region (e.g. ap-south-1 = Mumbai)
├── Availability Zone 1 (ap-south-1a) — one or more data centres
├── Availability Zone 2 (ap-south-1b) — separate power, network, cooling
└── Availability Zone 3 (ap-south-1c) — physically separated (km apart)

Edge Locations — 600+ worldwide (CDN caching points)
Local Zones — Extensions of Regions for ultra-low latency
Wavelength Zones — Embedded in 5G carrier networks
```

### AWS Regions — Geographic Isolation

A **Region** is a **geographic area** (typically a country or part of a continent) that contains **multiple physically separated data centres** (Availability Zones). Each Region is **completely independent** — it has its own power grids, networking, water supply, and services.

**Key facts about Regions:**
- As of 2024, AWS operates **33+ Regions** worldwide (e.g., `us-east-1` in Virginia, `eu-west-1` in Ireland, `ap-south-1` in Mumbai).
- Each Region has a **minimum of 3 Availability Zones** (most have 3, some have 6).
- **Data never leaves a Region** unless you explicitly transfer it — this is critical for data sovereignty and compliance (e.g., GDPR requires EU data to stay in EU Regions).
- Services and pricing **vary by Region** — not all AWS services are available in every Region, and prices differ.

**How to choose a Region:**

| Factor | Consideration | Example |
|---|---|---|
| **Latency** | Deploy closest to your users for lowest network latency | Indian users → `ap-south-1` (Mumbai), US East Coast users → `us-east-1` (Virginia) |
| **Data residency** | Regulations may require data to stay within specific borders | EU data under GDPR → `eu-west-1` (Ireland) or `eu-central-1` (Frankfurt) |
| **Service availability** | Some services launch first in `us-east-1` and expand later | If you need the latest ML service, check `us-east-1` first |
| **Pricing** | Costs vary by Region; US Regions are typically cheapest | `us-east-1` is often 10-20% cheaper than `ap-south-1` for the same services |

### Availability Zones (AZs) — Fault Isolation Within a Region

An **Availability Zone** is one or more **physically separate data centres** within a Region. Each AZ has its own:
- **Independent power supply** (with multiple backup generators)
- **Independent cooling systems**
- **Independent network connectivity** (separate fibre paths)
- **Physical separation** — AZs within a Region are typically **kilometres apart** (far enough to survive localised disasters, but close enough for low-latency connections)

**Key facts about AZs:**
- AZs within a Region are connected by **high-bandwidth, low-latency private fibre links** with latency typically **under 2 milliseconds** between AZs.
- Each AZ is designed to be an **independent failure domain** — a power outage, flood, or fire in one AZ should not affect other AZs.
- AWS recommends deploying across **at least 2 AZs** for any production workload.

**Analogy:** Think of a Region as a city and AZs as hospitals in that city. Each hospital has its own power, water, and staff. If one hospital has a power failure, patients can be redirected to other hospitals in the same city. They're close enough for fast transfers (low latency), but far enough apart that a local disaster won't take out multiple hospitals simultaneously.

**Designing for High Availability with AZs:**

| Architecture | AZs Used | Behaviour on AZ Failure | Use Case |
|---|---|---|---|
| **Single-AZ** | 1 | Application goes **completely down** | Dev/test environments (non-critical) |
| **Multi-AZ (2 AZ)** | 2 | 50% capacity lost, other AZ takes over | Standard production workloads |
| **Multi-AZ (3 AZ)** | 3 | ~33% capacity lost, remaining AZs handle load | High-availability production systems |
| **Multi-Region** | All AZs in 2+ Regions | Entire Region can fail; other Region takes over | Mission-critical, globally distributed (e.g., banking, e-commerce) |

### Edge Locations — Content Delivery at the Network Edge

**Edge Locations** are small data centres located in **600+ cities worldwide**. They are NOT for running VMs — they are used exclusively by:
- **CloudFront** (CDN) — caches static and dynamic content close to end users
- **Route 53** (DNS) — resolves domain names from the nearest point
- **AWS Shield** (DDoS protection) — absorbs attacks at the edge

**How edge locations reduce latency:** Without CloudFront, a user in Delhi requesting an image from an S3 bucket in `us-east-1` (Virginia) would travel ~14,000 km round trip (~200ms+ latency). With CloudFront, the image is cached at a Delhi edge location — the user gets it in ~5ms from a server just a few km away.

### AWS Local Zones — Extending a Region Closer to Users

**Local Zones** are extensions of an AWS Region that **place compute, storage, and database services closer to large population centres**. They are designed for workloads that require **single-digit millisecond latency** to end users.

**How Local Zones differ from Regions and AZs:**

| Aspect | Region | Availability Zone | Local Zone |
|---|---|---|---|
| **What it is** | Geographic area with multiple AZs | One or more data centres within a Region | Single data centre extending a Region |
| **Purpose** | Full AWS service portfolio | Fault isolation within a Region | Ultra-low latency for specific cities |
| **Services available** | All AWS services | All Region services | Subset (EC2, EBS, VPC, ELB, etc.) |
| **Latency** | Base latency from Region | <2ms between AZs | Single-digit ms to local users |
| **Example** | `ap-south-1` (Mumbai) | `ap-south-1a`, `ap-south-1b` | `ap-south-1-del-1a` (Delhi) |

**When to use Local Zones:**
- **Real-time gaming** — game servers need <10ms latency for a smooth experience
- **Live video streaming** — encoding and transcoding close to the broadcaster
- **AR/VR applications** — immersive experiences need ultra-low latency
- **Media and entertainment** — content creation studios need fast access to cloud rendering

**Example:** An online gaming company has most of its players in Hyderabad. The nearest AWS Region is Mumbai (`ap-south-1`), which adds ~30ms of network latency. By launching game servers in a Local Zone in Hyderabad, the latency drops to <5ms — the difference between a responsive game and noticeable lag.

### AWS Wavelength Zones — 5G Edge Computing

**Wavelength Zones** are AWS infrastructure **embedded directly within telecom carrier data centres** at the edge of 5G networks. They are designed for applications that need **single-digit millisecond latency** to mobile devices on 5G networks.

**How Wavelength works:**

```
Traditional path:
  Mobile device → 5G tower → Carrier network → Internet → AWS Region (50-100ms)

With Wavelength:
  Mobile device → 5G tower → Carrier data centre [AWS Wavelength Zone] (< 10ms)
                              (compute is HERE, right inside the carrier)
```

**Key characteristics:**
- AWS infrastructure (EC2, EBS, VPC) is deployed **inside the telecom carrier's data centre**, at the very edge of the 5G network
- Traffic from 5G devices reaches the Wavelength Zone **without ever leaving the carrier's network** — no internet hops, no extra latency
- Wavelength Zones connect back to the parent AWS Region for access to other services (S3, DynamoDB, etc.)
- Currently available with carriers like Verizon (US), Vodafone (UK/Germany), KDDI (Japan), and SK Telecom (South Korea)

**Use cases for Wavelength:**
- **Autonomous vehicles** — real-time decision-making at the edge (can't afford 100ms latency when a car needs to brake)
- **Interactive live streaming** — real-time video processing on mobile devices
- **Remote surgery/telemedicine** — surgeons controlling robotic instruments need guaranteed low latency
- **Industrial IoT** — real-time monitoring and control of factory equipment over 5G
- **Cloud gaming on mobile** — stream high-quality games to phones with no perceptible lag

### AWS Outposts — AWS in YOUR Data Centre

**AWS Outposts** is AWS-managed hardware that is **physically installed in your own data centre or co-location facility**, running the same AWS services you use in the cloud.

**How Outposts works:**
1. You order AWS Outposts hardware (physical racks of servers).
2. AWS ships and installs the hardware **in your data centre**.
3. The hardware runs AWS services (EC2, EBS, ECS, RDS, S3) **locally on your premises**.
4. AWS **remotely manages** the hardware — software updates, monitoring, maintenance are all handled by AWS.
5. The Outpost connects back to the nearest AWS Region for management, billing, and access to other AWS services.

**Why would you want AWS in your data centre?**

| Reason | Explanation |
|---|---|
| **Data residency** | Regulations require data to stay on-premises (e.g., government, healthcare, financial services in some countries) |
| **Low latency to local systems** | On-premises applications need sub-millisecond access to cloud services (e.g., factory floor systems, hospital equipment) |
| **Hybrid architecture** | You want a consistent AWS experience across cloud and on-premises — same APIs, same tools, same services |
| **Migration stepping stone** | Gradually migrate workloads from on-premises to cloud at your own pace, using familiar AWS tools |

**What you get vs what you don't:**

| You Get | You Don't Get |
|---|---|
| EC2, EBS, S3 on Outposts, ECS, EKS, RDS, EMR | Full breadth of 200+ AWS services |
| Same AWS APIs and tools (CLI, CloudFormation, CDK) | The scalability of a full Region (capacity is fixed to what's installed) |
| Fully managed by AWS (patching, monitoring, updates) | Pay-per-use flexibility (Outposts have a fixed monthly cost) |
| Connection back to the parent AWS Region | Automatic AZ-level redundancy (you'd need 2+ Outposts for HA) |

### Summary: Choosing the Right AWS Infrastructure Extension

| Extension | Location | Latency | Best For |
|---|---|---|---|
| **Region** | AWS data centres | Base (varies by user distance) | General workloads, full service access |
| **Availability Zone** | Within a Region | <2ms between AZs | Fault tolerance, high availability |
| **Edge Location** | 600+ global cities | Lowest for cached content | CDN (CloudFront), DNS (Route 53) |
| **Local Zone** | Metro areas | Single-digit ms to local users | Gaming, media, real-time apps |
| **Wavelength Zone** | Telecom carrier DCs | Single-digit ms to 5G devices | Mobile edge, IoT, autonomous vehicles |
| **Outpost** | Your data centre | Sub-ms to local systems | Data residency, hybrid, low latency to on-prem |

---

## VM Provisioning and Migration

*(From Lecture 7 — VM Management)*

### VM Lifecycle

A virtual machine goes through distinct phases during its life:

```
1. IT Service Request
   → Analyse server resource pool
   → Match resources with requirements
       ↓
2. VM Provisioning
   → Load OS + applications on VM instance
   → Customise and configure (IP, network, storage)
   → Start the server
       ↓
3. VM in Operation
   → Serves requests
   → Supports migration
   → Scale on-demand compute resources
       ↓
4. Release VM
   → End of service
   → Compute resources reallocated to another VM
```

### VM Provisioning Process

1. **Select server** from pool of available servers with appropriate OS template.
2. **Load software** — device drivers, middleware, application packages.
3. **Customise and configure** — IP address, network connectivity, storage attachment.
4. **Virtual server is ready** to serve requests.

In cloud: This entire process takes **minutes** (e.g. AWS EC2 provisioning). In traditional IT: This takes **weeks** (procurement, shipping, racking, cabling).

### VM Migration Techniques

| Technique | What moves | VM state during migration | Shared storage required? | Downtime |
|---|---|---|---|---|
| **Hot/Live Migration** | Running VM from one physical host to another. VM stays powered on. | Running | Yes | Milliseconds (almost none) |
| **Cold/Regular Migration** | Powered-off VM from one host to another. Disks and config files moved. | Powered off | No | Minutes (VM is off during move) |
| **Live Storage Migration** | VM's virtual disks and config from one data store to another. VM stays running. | Running | No (moving between stores) | None |

### Live Migration Algorithm (Xen Hypervisor — from Lecture 7)

| Stage | What happens |
|---|---|
| **Stage 0: Pre-Migration** | Active VM exists on Host A. |
| **Stage 1: Reservation** | Request sent to migrate VM from Host A to Host B. Resources reserved on B. |
| **Stage 2: Iterative Pre-Copy** | First iteration: ALL memory pages copied from A to B. Subsequent iterations: only **dirtied pages** (pages modified since last copy) are re-copied. Each iteration copies fewer pages. |
| **Stage 3: Stop-and-Copy** | VM on A is suspended. Remaining dirty pages + CPU state copied to B. Network traffic routed to B. Consistent copy now exists on both A and B. |
| **Stage 4: Commitment** | B confirms successful receipt of complete VM image. A acknowledges. Original VM on A is discarded. B becomes primary host. |
| **Stage 5: Activation** | Migrated VM on B is activated. Device drivers reattached. IP addresses advertised on new network. |

**Key insight:** Most of the data is copied WHILE the VM is still running (pre-copy). The actual downtime (stop-and-copy) is only milliseconds because only the last few dirty pages need to be transferred.

### Cloud Provisioning Platforms (from Lecture 7)

| Platform | Type | Key feature |
|---|---|---|
| **Amazon EC2** | Public cloud | Provision VMs in minutes via API. Pay-as-you-use. Auto Scaling + CloudWatch + ELB. |
| **Eucalyptus** | Private/Hybrid cloud | Open-source. API-compatible with AWS (EC2, S3, EBS). Runs on-premises. |
| **OpenNebula** | Private/Hybrid/Public cloud | Open-source VM orchestrator. Supports KVM, VMware, Xen. Manages storage, network, and compute. |
| **Aneka** | Distributed cloud | .NET-based platform for building distributed applications on cloud. APIs for resource management. |

---

*End of CS4*
