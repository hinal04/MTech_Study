# CS2-CS3: Virtualization

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Ch.9
> - T2: Erl, Puttini & Mahmood, *Cloud Computing*, Ch.5
> - R3: VMware Virtualization Documentation
> - R6: Docker Documentation
> - R7: Kubernetes Documentation
>
> **Contact Hours:** 5-8 (Handout Topics 2.1-2.6)

---

## Table of Contents

- [2.1 Introduction to Virtualization](#21-introduction-to-virtualization)
- [2.2 Benefits and Limitations of Virtualization](#22-benefits-and-limitations-of-virtualization)
- [2.3 Types of Virtualization](#23-types-of-virtualization)
- [2.4 x86 Hardware Virtualization](#24-x86-hardware-virtualization)
- [2.5 Resource Management for SaaS, PaaS, and IaaS Models](#25-resource-management-for-saas-paas-and-iaas-models)
- [2.6 Storage Virtualization](#26-storage-virtualization)
- [2.7 Containers and Containerization Concepts](#27-containers-and-containerization-concepts)
- [2.8 Emulation](#28-emulation)
- [2.9 Other Types of Virtualization (Network, Memory, Device)](#29-other-types-of-virtualization)
- [2.10 CPU and Memory Overcommitment](#210-cpu-and-memory-overcommitment)
- [2.11 Practical Considerations](#211-practical-considerations)

---

## 2.1 Introduction to Virtualization

### History of Virtual Machines

The concept of virtual machines originated at **IBM in the 1960s** to provide parallel and interactive access to expensive mainframe computers. Instead of giving each user a dedicated physical machine, IBM created **virtual machines** — software copies of the physical machine — so multiple users could share one mainframe simultaneously.

**Popek and Goldberg (1974)** formally defined a virtual machine as:

> *"An efficient, isolated duplicate of a real machine."*

This definition has three key requirements:
1. **Efficiency** — Programs running in a VM should execute at near-native speed (no excessive overhead).
2. **Isolation** — VMs should be completely isolated from each other. One VM cannot access another's memory or data.
3. **Fidelity** — A VM should be indistinguishable from the real machine. Software should behave identically whether running on a VM or bare metal.

Each VM is a **fully protected and isolated copy** of the physical machine. Virtualization allowed sharing expensive hardware, reducing costs and improving productivity as many users could concurrently use the same hardware.

### What is Virtualization?

**Virtualization** is a technique by which the physical characteristics of computing resources (hardware, storage, network) are **hidden (abstracted)** from the users, and another abstract computing platform is provided in its place. Instead of running one OS on one physical machine, virtualization allows multiple virtual machines (VMs) to share a single physical machine, each running its own OS and applications in isolation.

At its core, virtualization inserts a layer of software called a **hypervisor** (or Virtual Machine Monitor — VMM) between the physical hardware and the operating systems. The hypervisor manages the physical resources (CPU, memory, disk, network) and presents each VM with the illusion that it has its own dedicated hardware.

### What is a Virtual Machine?

A **virtual machine (VM)** is a software implementation of a machine that executes programs like a physical machine. It gives the user an illusion that they are interacting with the physical machine itself. The end user has the same experience on a virtual machine as they would have on dedicated hardware.

### Classification of Virtual Machines

Virtual machines are separated into **two major classes** based on their use:

| Class | What It Does | How It Works | Example |
|---|---|---|---|
| **System Virtual Machine** | Provides a complete system platform that supports the execution of a **complete operating system** | Emulates an existing architecture. Multiple instances lead to more efficient use of computing resources (hardware virtualization). This is the key to cloud computing. | VMware ESXi, KVM, Hyper-V — running multiple OS instances on one physical server |
| **Process Virtual Machine** (Language VM) | Designed to run a **single program/process** | Provides user-level instruction compatibility. The software running inside is limited to the resources provided by the VM — it cannot break out. | **JVM** (Java Virtual Machine) — "write once, run anywhere." Also **.NET CLR** (Common Language Runtime) |

> **Key distinction:** A system VM gives you an entire operating system. A process VM gives you a runtime environment for one application.

### VM Advantages (from class slides)

- Multiple OS environments can **co-exist** on the same physical hardware
- Application **provisioning, maintenance, high availability, and disaster recovery** are built into the VM management software
- Can provide **emulated hardware environments** different from the host's instruction set architecture (ISA) — for example, running ARM software on an x86 machine through emulation

### VM Disadvantages (from class slides)

- A VM is **less efficient** than a physical machine when accessing the host hard drive indirectly (extra abstraction layer)
- When multiple VMs run concurrently, performance may be **varying and unstable** depending on the data load imposed by other VMs — the **"noisy neighbour" problem** (unless temporal isolation is enforced)
- **Malware protection** for VMs may not be compatible with the host OS and may require separate security software for each VM

### Classification of Virtualization

Based on the computing resource that is virtualized, virtualization can be classified as:

| Type | What is Virtualized | What It Enables | Key Concept |
|---|---|---|---|
| **Server Virtualization** (Hardware/Platform Virtualization) | The physical server/machine | Multiple VMs on one physical server. Enables **Infrastructure as a Service (IaaS)**. | Abstracts the physical machine — software believes it's running on real hardware |
| **Storage Virtualization** | Physical storage devices | Multiple physical disks appear as one logical storage pool. Enables **Storage as a Service**. | Abstracts physical storage — users/applications don't know which disk their data is on |

### Reasons for Server Virtualization (from class slides)

1. **Server consolidation** — Many small physical servers are replaced by one larger physical server to increase utilization of costly hardware (CPU, memory)
2. **Energy reduction** — Fewer physical servers = less electricity for computing and cooling
3. **Easier management** — A VM can be more easily controlled, inspected, and configured from outside than a physical machine
4. **Rapid provisioning** — A new VM can be provisioned in seconds without an upfront hardware purchase
5. **Easy relocation** — A VM can easily be relocated (migrated) from one physical machine to another as needed

### Server Virtualization Classification

Server virtualization itself is further classified into:

| Type | Where VMM Runs | User Sees | Example |
|---|---|---|---|
| **System Virtualization** | VMM sits **between the OS and hardware** | Full operating system in each VM | VMware ESXi, KVM, Hyper-V |
| **Process Virtualization** | VMM runs **above the operating system** | User-level instruction compatibility for a single program | JVM (Java), .NET CLR |

In **process virtualization**, the VM management software runs above the operating system and provides user-level instruction compatibility. Example: JVM — Java code compiles to bytecode that runs on the JVM regardless of the underlying OS (Windows, Linux, macOS).

In **system virtualization**, the virtualization software is present between the operating system and the physical hardware. Example: VMware ESXi — multiple complete operating systems (Windows, Linux) run simultaneously on one physical server.

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

In server virtualization, the host OS is not very important — it's mainly confined to booting up and running the VMs. Since a regular OS is not ideal for running multiple VMs, a new breed of software called the **Hypervisor** takes over the OS role. A hypervisor is an efficient VMM designed from the ground up to run multiple high-performance VMs.

> **Key insight:** A Hypervisor is to VMs what an OS is to processes.

**Three types of hypervisors (from class slides):**

| Type | Where it Runs | How it Works | Examples |
|---|---|---|---|
| **Native (Bare-metal / Type 1)** | Directly on physical hardware, **replacing** the host OS | The hypervisor IS the OS. Has direct access to hardware. Best performance and lowest overhead. | VMware ESXi, Microsoft Hyper-V (standalone), Xen, KVM (Linux) |
| **Hosted (Type 2)** | On top of an existing host OS, as an **application** | Runs as a user-level app. The host OS mediates hardware access, adding overhead. | VMware Workstation, Oracle VirtualBox, Parallels Desktop (macOS) |
| **Hybrid** | Directly on hardware but **uses features of the host OS** | Runs on bare metal like Type 1, but leverages an existing OS for device drivers and hardware support. Combines benefits of both. | KVM (technically — it's a Linux kernel module that turns the Linux kernel into a Type 1 hypervisor), bhyve (FreeBSD) |

```
Native (Type 1):              Hosted (Type 2):              Hybrid:

┌───────┐ ┌───────┐          ┌───────┐ ┌───────┐          ┌───────┐ ┌───────┐
│ VM 1  │ │ VM 2  │          │ VM 1  │ │ VM 2  │          │ VM 1  │ │ VM 2  │
│(Guest │ │(Guest │          │(Guest │ │(Guest │          │(Guest │ │(Guest │
│  OS)  │ │  OS)  │          │  OS)  │ │  OS)  │          │  OS)  │ │  OS)  │
├───────┴─┴───────┤          ├───────┴─┴───────┤          ├───────┴─┴───────┤
│   Hypervisor    │          │   Hypervisor     │          │   Hypervisor    │
│   (bare-metal)  │          │   (application)  │          │ (uses OS parts) │
├─────────────────┤          ├──────────────────┤          ├─────────────────┤
│Physical Hardware│          │   Host OS        │          │ Host OS + HW    │
└─────────────────┘          ├──────────────────┤          └─────────────────┘
                             │Physical Hardware │
                             └──────────────────┘
```

**In cloud computing, Type 1 (Native) hypervisors are used exclusively** because they provide better performance, security, and resource efficiency. Type 2 (Hosted) hypervisors are used primarily for development and testing on personal machines.

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

## 2.6 Storage Virtualization

Storage virtualization uses virtualization techniques to enable better functionality and advanced features in computer data storage systems. It abstracts the physical storage system from users and applications, presenting storage as **logical entities** while hiding the complexities of accessing them.

> **Simple definition:** Storage virtualization makes multiple physical storage devices (hard drives, SSDs, SANs) appear as a **single, unified storage pool** to users and applications. Users don't know (or care) which physical disk their data is actually stored on.

### Why Storage Virtualization?

Without storage virtualization, each server connects to specific physical disks. This leads to:
- Some disks are 90% full while others are 20% used
- Adding storage requires downtime
- Migrating data between disks is disruptive
- Managing hundreds of individual disks is a nightmare

With storage virtualization, all disks are pooled together and managed as one logical unit.

### Advantages of Storage Virtualization (from class slides)

| Advantage | Explanation |
|---|---|
| **Non-disruptive data migration** | Data can be moved between physical storage devices **while applications continue to read and write**. The host only knows the logical disk (mapped LUN), so any changes to the physical mapping are transparent. |
| **Improved utilization** | Pooling, migration, and **thin provisioning** allow better use of storage. Users avoid over-buying and over-provisioning. Multiple physical disks used as one pool = less waste. |
| **Fewer points of management** | Multiple independent storage devices, even scattered across a network, appear as a **single monolithic storage device** and can be managed centrally from one console. |
| **Thin provisioning** | Allocate virtual storage to a server that's larger than the physical storage available. Physical storage is consumed only when data is actually written. Example: Give a VM 1TB virtual disk but only 100GB is physically used. |

### Implementation Approaches

Storage virtualization can be implemented at three levels:

#### 1. Host-Based Storage Virtualization

The virtualization software runs **on the host server** itself, as a privileged task or process.

**How it works:** A software layer called the **volume manager** sits above the physical disk device driver and intercepts I/O requests, performing metadata lookup and I/O mapping. The host OS's Logical Volume Manager (LVM) is a common example.

```
┌─────────────────────────┐
│    Application           │
├─────────────────────────┤
│    File System           │
├─────────────────────────┤
│    Volume Manager        │  ← Virtualization layer
│    (LVM, ZFS, LDM)      │
├─────────────────────────┤
│    Physical Disk Driver  │
├─────────────────────────┤
│    Physical Disks        │
└─────────────────────────┘
```

**Examples:** Linux LVM (Logical Volume Manager), Solaris/FreeBSD ZFS zpool, Windows LDM (Logical Disk Manager)

| Pros | Cons |
|---|---|
| Simple to design and code | Storage utilization optimized only on a per-host basis |
| Supports any storage type | Replication and data migration only possible locally to that host |
| Improves storage utilization | Software is unique to each operating system |
| | No easy way to keep host instances in sync |

#### 2. Storage Device-Based Virtualization

The virtualization is performed by the **storage array controller** itself.

**How it works:** Advanced disk arrays use RAID schemes to join multiple physical disks into a single array, and may later divide the array into smaller volumes. A primary storage controller provides pooling and metadata management services and allows attachment of other storage controllers.

| Pros | Cons |
|---|---|
| No additional hardware or infrastructure needed | Utilization optimized only across connected controllers |
| Provides most benefits of storage virtualization | Replication limited to connected controllers and same vendor |
| Does not add latency to individual I/Os | Downstream controller support limited to vendor's matrix |

#### 3. Network-Based Storage Virtualization

The virtualization operates on a **network-based device** (server or smart switch) using iSCSI or Fibre Channel (FC) networks to connect as a SAN (Storage Area Network). This is the **most commonly implemented** form of storage virtualization.

**How it works:** The virtualization device sits in the SAN between the hosts and the storage controllers, providing the layer of abstraction.

| Pros | Cons |
|---|---|
| **True heterogeneous** storage virtualization (any vendor) | Complex interoperability matrices |
| Caching of data possible (performance benefit) when in-band | Difficult to implement fast metadata updates in switched devices |
| Single management interface for all virtualized storage | In-band may add latency to I/O |
| Replication across heterogeneous devices | Most complicated to design and code |

#### Network-Based: Appliance-Based vs Switch-Based

| Type | What It Is | How It Works |
|---|---|---|
| **Appliance-based** | Dedicated hardware devices providing SAN connectivity | Sits between hosts and storage (in-band). I/O requests are targeted at the appliance, which performs metadata mapping before redirecting I/O to underlying storage. Can cache data and cluster for high availability. |
| **Switch-based** | Resides in the physical SAN switch hardware | Uses techniques like packet cracking to snoop on I/O requests and perform redirection. More difficult to ensure atomic metadata updates. |

Both models provide: disk management, metadata lookup, data migration, and replication.

### In-Band vs Out-of-Band Virtualization (from class slides)

| Aspect | In-Band (Symmetric) | Out-of-Band (Asymmetric) |
|---|---|---|
| **Data path** | Virtualization device sits **in** the data path. All I/O passes through it. | Virtualization device is a **metadata server** only. Data does NOT pass through it. |
| **How it works** | Hosts perform I/O to the virtualization device. The device performs I/O to the actual storage on behalf of the host. | Host intercepts its own I/O request → asks metadata server for physical location → then sends I/O directly to storage. |
| **Caching** | Yes — data passes through the device, so caching is possible | No — data never passes through the device |
| **Performance** | May add latency (extra hop in data path) | Lower latency for data (direct host-to-storage) but metadata lookup adds overhead |
| **Additional software** | No additional host software needed | Requires additional software on the host to intercept I/O and query metadata server |
| **Best for** | Environments needing caching, replication, migration | Environments where latency is critical and caching isn't needed |

```
In-Band:                           Out-of-Band:

Host → Virtualization → Storage    Host → Metadata Server (location query)
       Device                            ↓
   (All data flows through)        Host → Storage (direct I/O)
                                   (Only metadata goes through server)
```

---

## 2.7 Containers and Containerization Concepts

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

## 2.8 Emulation (from class slides P19-20)

**Emulation** is the process by which software reproduces the behaviour of hardware — CPU instruction set, memory, and I/O devices — presenting the guest OS with a virtual hardware profile that is completely **decoupled** from the physical hardware underneath.

### Emulation vs Virtualization — Key Difference

| Aspect | Emulation | Virtualization |
|---|---|---|
| **ISA (Instruction Set Architecture)** | **Cross-ISA** — can run ARM guest on x86 host | **Same-ISA only** — x86 guest on x86 host |
| **How it works** | Software reproduces entire hardware behaviour | Hypervisor intercepts privileged instructions, runs rest natively |
| **Performance** | Slow (10-100x overhead) | Near-native (2-10% overhead) |
| **Guest awareness** | Guest OS completely unaware | Guest may or may not be aware (depends on type) |
| **Use case** | Cross-platform (ARM on x86), legacy hardware, architecture migration | Cloud computing, server consolidation, multi-tenancy |
| **Examples** | QEMU, Apple Rosetta 2, Android emulator | KVM, VMware ESXi, Hyper-V |

### Two Emulation Techniques

| Technique | How It Works | Performance | Example |
|---|---|---|---|
| **Interpretation** | Fetch, decode, and execute each guest instruction **one at a time, every time** | Slow — no reuse. Every instruction is re-decoded each time. | Simple emulators, early QEMU |
| **Dynamic Binary Translation (DBT)** | Translate a block of guest instructions to equivalent host instructions **once**, cache the result, and jump directly to it on repeat execution | Much faster — translated blocks are reused | QEMU's TCG engine, Apple Rosetta 2 |

> **Key insight from class slides:** Full emulation is reserved for cases where ISAs genuinely differ (cross-platform, legacy hardware). For standard same-ISA cloud virtualization, hardware-assisted virtualization (VT-x/AMD-V) dominates instead.

---

## 2.9 Other Types of Virtualization (from class slides P39-41)

### Network Virtualization

**Network virtualization** creates a logical, software-defined network on top of physical network infrastructure. It allows logical segmentation without physical changes.

| Type | What It Does | Example |
|---|---|---|
| **VLAN (Virtual LAN)** | Logically segments a LAN into several broadcast domains. Defined on a switch, port-by-port. Ports in the same VLAN don't need to be physically contiguous — users on different floors or buildings can be in the same VLAN. | Ports 1-10 = VLAN 1 (HR), Ports 11-20 = VLAN 2 (Engineering) |
| **VRF (Virtual Routing and Forwarding)** | Allows **multiple instances of a routing table** to coexist within the same physical router simultaneously. Used in MPLS (Multi-Protocol Label Switching) networks. | ISP uses one physical router to serve multiple customers, each with isolated routing |
| **SDN (Software-Defined Networking)** | Separates the control plane (routing decisions) from the data plane (actual packet forwarding). Network behaviour is controlled by software. | VMware NSX, Cisco ACI |

### Memory Virtualization (from class slides P40)

Beyond CPU virtualization, the hypervisor must also virtualise memory.

- Involves **sharing physical system memory** and dynamically allocating it to VMs
- Very similar to virtual memory in modern OSes — applications see a contiguous address space not necessarily tied to physical memory
- The OS maintains **page tables** mapping virtual page numbers to physical page numbers
- All modern x86 CPUs include:
  - **MMU (Memory Management Unit)** — handles virtual-to-physical address translation
  - **TLB (Translation Lookaside Buffer)** — caches recent translations for faster lookup

In a virtualised environment, there are **two levels of translation:**
```
Guest Virtual Address → Guest Physical Address → Host Physical Address
    (managed by guest OS)     (managed by hypervisor)
```

This is handled efficiently by **Extended Page Tables (EPT)** on Intel or **Nested Page Tables (NPT)** on AMD (see Section 2.4).

### Device Virtualization (from class slides P41)

**Device virtualization** presents each VM with a virtualised version of peripheral devices — network adapters, disk controllers, video adapters, USB controllers, etc.

- The guest OS interacts with what it believes is a **real device**
- The hypervisor is the one actually talking to the physical hardware underneath
- Enables remote work — users can access their VM from any location and any device
- Protects confidential data by keeping it on central servers (not on endpoint devices)
- Involves managing the **routing of I/O requests** between virtual devices and shared physical hardware

---

## 2.10 CPU and Memory Overcommitment (from class slides P42-46)

### CPU Overcommitment

The hypervisor can allocate **more virtual CPUs (vCPUs) across VMs than physical cores (pCPUs)** exist on the host. This works because CPU time is **time-sliced** — the scheduler hands out short slices of physical core time, the same way an OS multiplexes processes onto cores.

**Overcommit Ratio = Total vCPUs / Total pCPUs**

| Ratio | Suitable For |
|---|---|
| **1:1** | Latency-sensitive workloads (databases, real-time systems) |
| **3:1 to 5:1** | Workable for mixed workloads |
| **> 8:1** | High risk — performance degradation likely |

**CPU Ready Time** — Time a vCPU is ready to run but **waiting** for a physical core to free up. Rising CPU Ready (flagged above ~5%) signals the host is oversubscribed.

**Example (from class slides):**
```
Setup:  Host has 4 physical cores (pCPUs), no hyperthreading.
        6 VMs deployed, each with 2 vCPUs.

Total vCPUs = 6 VMs × 2 vCPUs = 12 vCPUs
Overcommit ratio = 12 / 4 = 3:1

Is this safe?
3:1 sits at the edge of "safe zone" (3:1–5:1) — acceptable but watch closely.

Subtlety: If 3 of 6 VMs simultaneously peg both vCPUs to 100%,
that's 6 vCPUs of demand against 4 pCPUs — effective contention = 1.5:1 
for that subset. The aggregate ratio is a planning heuristic, not a guarantee.
```

### Memory Overcommitment

Unlike CPU (which can be time-sliced), **memory cannot be time-sliced** — a page of RAM is either allocated to a VM or it isn't. The hypervisor must actively reclaim or deduplicate memory.

**Overcommit Ratio = Configured Memory / Physical RAM**

**Reclamation techniques (from least to most invasive):**

| Technique | How It Works | Performance Impact |
|---|---|---|
| **1. Page Sharing (TPS / KSM)** | Merges **identical memory pages** across VMs. If 10 VMs run the same OS, many memory pages are identical — share them instead of duplicating. (Note: Inter-VM sharing disabled by default in vSphere 6.0+ for security — only intra-VM sharing is on.) | Minimal — transparent to VMs |
| **2. Ballooning** | A **guest-side driver** reclaims memory from the guest's own processes. The guest OS decides what to page out — better than hypervisor guessing. | Low-moderate — guest OS handles it gracefully |
| **3. Compression** | Hypervisor **compresses pages** in memory instead of writing to disk. Slower than RAM but much faster than disk. | Moderate — decompression adds CPU overhead |
| **4. Host-level Swapping** | **Last resort** — pages swapped to disk without guest cooperation. | **Severe** — disk is ~100,000× slower than RAM |

**Example (from class slides):**
```
Setup: Host has 64 GB physical RAM.
       10 VMs, each configured with 8 GB = 80 GB total.
       Overcommit ratio = 80/64 = 1.25:1

Reclamation order if memory is tight:
1. TPS/KSM finds 5 GB of identical pages across VMs → freed
2. Balloon driver reclaims 3 GB from idle VMs → freed
3. Compression saves another 2 GB → still in memory but compressed
4. If still short → host swapping kicks in → severe performance hit
```

---

## 2.11 Practical Considerations (from class slides P49)

### Points to Note About Virtualization

| Consideration | Details |
|---|---|
| **Software Licensing** | Each VM requires its own separate software license. Virtualization makes it easy to create new servers, but organisations could pay large amounts in license fees if they don't control server sprawl. Some licenses are per-socket, per-VM, or per-core — all add cost. |
| **IT Training** | Staff used to physical systems need training on virtualization. Essential for debugging issues in virtual environments, securing and managing VMs, and effectively planning capacity. |
| **Hardware Investment** | Server virtualization is most effective on **powerful physical machines**. Organisations with existing weak hardware may still need upfront investment in new physical servers to harvest virtualization benefits. |

---

---

*End of CS2-CS3*
