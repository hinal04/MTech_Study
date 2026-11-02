# Cloud Computing — Past Papers & Practice Questions with Answers

> BITS Pilani — CSI ZG527 / SE ZG527 / SS ZG527
> Paper Pattern: 3 Questions × 10 Marks = 30 Marks
> 2 Scenario-Based + 1 Direct Concepts
> Covers: CS1 (Intro) to CS6 (Containers)
> Content was rephrased for compliance with licensing restrictions. Source: BITS Pilani Mid-Semester Test 2024.

---
---

## SECTION 1: PAST YEAR PAPER (Mid-Sem 2024, EC-2 Regular)

> Closed Book | 2 Hours | 30 Marks | 30% Weightage
> 3 Questions × 10 Marks each | Answer ALL questions

---

### Q1. [10 Marks] — Scenario-Based (Cloud Adoption & Virtualization)

**(a) An e-commerce company currently runs all operations on-premise and faces scalability issues during peak sales seasons. As a cloud consultant, recommend an appropriate cloud deployment model and specific cloud services they should adopt. Justify your recommendations. [6 marks]**

**Model Answer:**

**Recommended Deployment Model: Hybrid Cloud**

A hybrid cloud combines the control of private infrastructure with the elasticity of public cloud, making it ideal for an e-commerce company transitioning from on-premise.

**Justification:** The company's existing on-premise infrastructure can be retained for sensitive operations (customer payment data, inventory databases) while burst traffic during peak sales seasons can be offloaded to the public cloud. This avoids a risky "big bang" migration while immediately solving scalability.

**Recommended Cloud Services:**

| Service Type | Specific Service | Purpose | Justification |
|---|---|---|---|
| **IaaS** | AWS EC2 with Auto Scaling | Compute for web/app servers | Auto-scales during flash sales (2 instances → 200 instances), scales back during quiet periods. Pay only for what you use. |
| **PaaS** | AWS Elastic Beanstalk | Application deployment | Developers deploy code without managing servers. Built-in load balancing, health monitoring. Faster release cycles. |
| **SaaS** | Salesforce / Zendesk | CRM and customer support | No need to build CRM from scratch. Ready-to-use, pay-per-user, accessible from anywhere. |
| **DBaaS** | Amazon RDS (Multi-AZ) | Managed relational database | Automated backups, failover, patching. Multi-AZ for high availability during peak traffic. |
| **CDN** | Amazon CloudFront | Content delivery | Caches product images and static assets at edge locations globally. Reduces latency for customers worldwide. |
| **DRaaS** | AWS Backup + Cross-Region Replication | Disaster recovery | Ensures business continuity. RPO/RTO targets met without maintaining a secondary data center. |

**Architecture Flow:**
- Normal traffic → On-premise servers handle requests.
- Peak traffic → Cloud bursting: excess traffic routed to AWS EC2 Auto Scaling group via load balancer.
- Static content → Served via CloudFront CDN (faster, cheaper).
- Database → RDS with read replicas for read-heavy product catalog queries.

This hybrid approach provides immediate scalability benefits while allowing a phased migration, minimizing risk and cost.

**(b) An enterprise is modernizing its data center by adopting virtualization technology. Recommend a specific type of virtualization for their environment and discuss its potential downsides. [4 marks]**

**Model Answer:**

**Recommended: Full Virtualization with Hardware-Assisted Extensions (VT-x/AMD-V)**

For an enterprise modernizing a data center, hardware-assisted full virtualization is the best choice. This uses Type 1 bare-metal hypervisors (VMware ESXi, KVM, or Microsoft Hyper-V) with Intel VT-x / AMD-V CPU extensions.

**Why Full Virtualization:**

1. **No guest OS modification required** — existing Windows, Linux, and legacy applications run unmodified on VMs. Critical for enterprises with diverse OS environments.
2. **Strong isolation** — each VM is a complete sandbox with its own kernel. A crash or security breach in one VM does not affect others.
3. **Flexibility** — run any x86 OS (Windows Server, RHEL, Ubuntu, even older OS versions) on the same physical hardware.
4. **Management ecosystem** — mature tools like VMware vCenter, vMotion (live migration), DRS (load balancing), HA (automatic failover) provide enterprise-grade management.
5. **Disaster recovery** — VM state is encapsulated in files. Snapshots, clones, and replication are straightforward.

**Potential Downsides:**

| Downside | Explanation |
|---|---|
| **Performance overhead** | Hypervisor layer adds 2-10% CPU overhead. I/O-intensive workloads (databases, storage) suffer more due to virtualized I/O path. |
| **Resource contention** | Multiple VMs competing for physical CPU, memory, and I/O. "Noisy neighbor" problem — one VM's heavy workload degrades others. |
| **Storage overhead** | Each VM carries a full OS copy (5-20 GB). 50 VMs = 250-1000 GB just for OS images. |
| **Licensing complexity** | Each VM may need its own OS license (Windows Server, Oracle DB). Licensing costs can exceed hardware savings. |
| **VM sprawl** | Easy provisioning leads to unmanaged proliferation. 100s of VMs created but never decommissioned, wasting resources. |
| **Single point of failure** | If the hypervisor host fails, all VMs on that host go down simultaneously (mitigated by HA clustering). |

**Mitigation:** Use CPU/memory overcommitment ratios wisely (3:1-5:1 for CPU), implement resource reservations for critical VMs, and deploy HA clusters with automatic failover.

---

### Q2. [10 Marks] — Scenario-Based (Memory Overcommitment)

**A virtualized data center is experiencing memory overcommitment issues. Address the following:**

**(a) Explain the concepts of paging and ballooning in the context of memory overcommitment in a virtualized environment. [5 marks]**

**Model Answer:**

Memory overcommitment occurs when the total memory allocated to all VMs exceeds the physical RAM available on the host. For example, a host with 64 GB RAM running 10 VMs each allocated 8 GB = 80 GB committed (1.25:1 overcommit ratio). The hypervisor uses several techniques to manage this, with paging and ballooning being two key mechanisms.

**Paging (Hypervisor-Level Swapping):**

- **Mechanism:** The hypervisor maintains a swap file on disk. When physical memory is exhausted, the hypervisor identifies VM memory pages that haven't been recently accessed and writes them to the swap file, freeing physical RAM for active pages.
- **How it works with EPT/SLAT:** Hardware-assisted virtualization uses Extended Page Tables (EPT) for two-level address translation: Guest Virtual → Guest Physical → Host Physical. The hypervisor can mark host physical pages as "not present," causing a page fault when accessed. The hypervisor then reads the page from swap back into RAM.
- **Performance impact:** Disk I/O is 100-1000x slower than RAM access. When a VM accesses a swapped-out page, it experiences significant latency (milliseconds instead of nanoseconds).
- **When activated:** This is the **last resort** technique (4th priority). The hypervisor uses this only when TPS, ballooning, and compression have failed to reclaim enough memory.

**Ballooning (Guest-Assisted Reclamation):**

- **Mechanism:** A special "balloon driver" is installed inside each guest OS (e.g., VMware Tools balloon driver). When the hypervisor needs to reclaim memory, it signals the balloon driver to "inflate."
- **Inflation process:** The balloon driver allocates large blocks of memory inside the guest OS (the guest thinks a normal process is using memory). The guest OS's own memory manager pages out less-important processes to make room. The hypervisor then reclaims the physical pages backing the balloon.
- **Deflation process:** When memory pressure decreases, the hypervisor signals the balloon driver to "deflate" — it releases the allocated memory, returning physical pages to the VM.
- **Key advantage:** The guest OS decides which pages are least important (using its own LRU/page replacement algorithms), resulting in more intelligent reclamation than hypervisor-level swapping.
- **When activated:** This is the **2nd priority** technique, after TPS (Transparent Page Sharing) but before compression and swapping.

**Complete Memory Reclamation Order:**

| Priority | Technique | Impact |
|---|---|---|
| 1st | TPS (Transparent Page Sharing) | None — deduplicates identical pages |
| 2nd | **Ballooning** | Low — guest OS manages intelligently |
| 3rd | Compression | Low — compress before swapping |
| 4th | **Hypervisor Swapping (Paging)** | High — disk I/O latency |

**(b) Discuss the benefits and risks of memory overcommitment in a virtualized environment. [5 marks]**

**Model Answer:**

**Benefits of Memory Overcommitment:**

| Benefit | Explanation |
|---|---|
| **Higher resource utilization** | Most VMs don't use all allocated memory simultaneously. A VM allocated 8 GB may actively use only 3-4 GB. Overcommitment reclaims the unused 4 GB for other VMs. |
| **Cost efficiency** | Fewer physical servers needed. Instead of buying 128 GB RAM to serve 16 × 8 GB VMs, 64 GB RAM with 2:1 overcommit may suffice. Direct CapEx savings. |
| **Increased VM density** | More VMs per physical host. A 64 GB host can run 10-16 VMs at 8 GB each instead of only 8 (1:1 commit). Higher consolidation ratio. |
| **Flexible resource allocation** | VMs can be allocated generous memory limits without wasting physical resources. Accommodates workloads with variable memory usage patterns (high at startup, low at steady state). |
| **Simplified capacity planning** | Don't need to precisely predict every VM's exact memory needs upfront. Over-allocate to be safe, let overcommitment handle the actual physical distribution. |

**Risks of Memory Overcommitment:**

| Risk | Explanation |
|---|---|
| **Performance degradation** | When all VMs simultaneously demand their allocated memory, the host runs out of physical RAM. Ballooning and swapping activate, causing latency spikes. Database queries that normally take 5ms may take 500ms. |
| **VM instability** | Aggressive ballooning can cause the guest OS to page heavily, leading to application crashes or out-of-memory (OOM) kills inside the VM. |
| **Unpredictable behavior** | Performance becomes non-deterministic. A VM may run fine for weeks, then suddenly slow down because another VM on the same host started using more memory. Classic "noisy neighbor" problem. |
| **Cascading failures** | If the hypervisor starts swapping heavily, ALL VMs on the host degrade simultaneously. A single memory-hungry VM can impact every other VM on the host. |
| **Monitoring complexity** | Need to actively monitor memory balloon sizes, swap rates, and TPS savings. Without proper monitoring, overcommitment issues go undetected until users complain. |

**Best Practices:**
- Keep overcommitment ratio below **1.5:1** for production workloads.
- Set **memory reservations** for critical VMs (guaranteed minimum physical RAM).
- Monitor **balloon target**, **swap rate**, and **compression rate** metrics.
- Never overcommit memory for latency-sensitive workloads (databases, real-time systems).
- Use TPS and ballooning proactively; swapping should never activate in normal operation.

---

### Q3. [10 Marks] — Direct Concepts (VMs vs Containers + AWS Services)

**(a) Compare Virtual Machines and Containers in terms of resource utilization and isolation mechanisms. [5 marks]**

**Model Answer:**

| Dimension | Virtual Machines | Containers |
|---|---|---|
| **What is virtualized** | Entire hardware stack (CPU, memory, storage, network) | OS-level (process space, filesystem, network namespace) |
| **Kernel** | Each VM runs its own full kernel | All containers share the host kernel |
| **Resource overhead** | Heavy — full OS per VM (2-20 GB RAM, 10-50 GB disk per VM) | Light — only app + libraries (50-500 MB typically) |
| **CPU utilization** | Hypervisor scheduling adds 2-10% overhead; each VM has dedicated vCPUs | Near-native; containers are just processes with cgroup limits |
| **Memory utilization** | Each VM reserves full allocated RAM (8 GB allocated = 8 GB reserved, even if using 2 GB). Overcommitment techniques (TPS, ballooning) partially mitigate. | Containers use only what they need. 256 MB limit but using 100 MB = only 100 MB physical RAM consumed. |
| **Density** | 10-50 VMs per physical host | 100-1000+ containers per host |
| **Boot time** | 30 seconds to several minutes (full OS boot) | Milliseconds to seconds (just start a process) |
| **Isolation mechanism** | Hardware-level: hypervisor creates complete hardware abstraction. Separate kernels mean a vulnerability in one VM's kernel cannot affect another VM. | OS-level: Linux namespaces (PID, NET, MNT, UTS, IPC, USER) provide process isolation. Cgroups limit resource usage. Shared kernel = shared vulnerability surface. |
| **Isolation strength** | Strong — hypervisor boundary is a hard security barrier | Moderate — namespace isolation can be escaped via kernel exploits |
| **Multi-tenancy** | Preferred for tenant isolation (AWS uses VMs to separate customers) | Used within a single tenant for app isolation (microservices) |

**Key Insight:** In production cloud environments, containers run *inside* VMs. The VM provides tenant-level security isolation (strong boundary), while containers provide application-level packaging and scaling (lightweight, fast). This gives the best of both worlds.

**(b) Define the following AWS services and explain how they work together to form a complete cloud infrastructure: IAM, EC2, Clusters, VPC. [5 marks]**

**Model Answer:**

**Definitions:**

**1. IAM (Identity and Access Management):**
The security gatekeeper of AWS. IAM controls *who* (authentication) can do *what* (authorization) on *which* resources. It manages Users (individual identities), Groups (collections of users), Roles (temporary credentials for services/cross-account access), and Policies (JSON documents specifying Allow/Deny on specific actions and resources). IAM enforces the principle of least privilege — give only the minimum permissions needed.

**2. EC2 (Elastic Compute Cloud):**
AWS's core compute service providing resizable virtual servers (instances) in the cloud. EC2 allows you to choose instance type (CPU, RAM, storage optimized), AMI (pre-configured OS image), and pricing model (On-Demand, Reserved, Spot). Instances can be launched, stopped, terminated, and scaled via Auto Scaling groups. EC2 is the "workhorse" of IaaS.

**3. Clusters (Placement Groups / ECS Clusters):**
In EC2 context, a **Cluster Placement Group** places instances in the same rack/AZ for low-latency, high-throughput communication (used for HPC, distributed databases). In ECS/EKS context, a **cluster** is a logical grouping of compute resources (EC2 instances or Fargate tasks) that run containerized applications. The cluster manages task scheduling, scaling, and health monitoring.

**4. VPC (Virtual Private Cloud):**
A logically isolated virtual network within AWS. VPC gives you full control over IP addressing (CIDR blocks), subnets (public/private), route tables, internet gateways (IGW for internet access), NAT gateways (outbound-only internet for private subnets), Security Groups (stateful instance-level firewall), and NACLs (stateless subnet-level firewall).

**How They Work Together:**

```
[IAM] → Authenticates user/service → Authorizes specific actions
   ↓
[VPC] → Creates isolated network → Public subnet + Private subnet
   ↓                                    ↓              ↓
[EC2 in Public Subnet]           [EC2 in Private Subnet]
   (Web servers)                    (App/DB servers)
   ↓                                    ↓
[Cluster Placement Group]        [ECS Cluster]
   (HPC workloads, low-latency)     (Container orchestration)
```

**Integration Example — 3-Tier Web App:**
1. **IAM** creates roles: EC2 instances get a role allowing S3 read access and RDS connectivity. Developers get a role with EC2 launch permissions only.
2. **VPC** is configured with a public subnet (web tier) and two private subnets (app tier, database tier). IGW attached for internet access. NAT Gateway allows private instances to download updates.
3. **EC2** instances are launched: web servers in public subnet (with Elastic IPs), application servers in private subnet. Auto Scaling group maintains 2-10 instances based on CPU utilization.
4. **Cluster** placement group used for the database tier — EC2 instances running in a cluster placement group for low-latency inter-node communication (e.g., distributed database replication).
5. **Security Groups** on web servers allow port 80/443 from internet. App server SGs allow traffic only from web server SG. DB SGs allow traffic only from app server SG.

This layered architecture provides security (IAM + VPC), scalability (EC2 Auto Scaling), performance (Cluster placement), and network isolation (VPC subnets + security groups).

---
---

---

## ADDITIONAL PAST YEAR QUESTIONS (from 2024 Mid-Sem Paper)

### Q4. [6 Marks] — VMM and Unmodified Guest OS

**Question:** A Virtual Machine Monitor (VMM) uses the trap-and-emulate approach to virtualize a Linux-like operating system designed to run directly on bare-metal hardware. The OS uses privileged instructions to interact with hardware (CPU, memory, I/O devices). On the x86 architecture, some instructions are "sensitive but not privileged" — they behave differently depending on the CPU ring but do NOT trigger a trap when executed outside Ring 0.

(a) Explain why the VMM cannot simply execute the unmodified OS binary directly on the hardware. [3 marks]
(b) Can the VMM run this OS as a guest without modifying its source code and achieve correct virtualization? Justify your answer. [3 marks]

**Answer:**

**(a) Why VMM cannot execute the unmodified OS directly:**

The VMM cannot run the unmodified OS because of the fundamental x86 virtualization challenge:

1. **Ring conflict:** The OS kernel expects to run at Ring 0 (highest privilege) with full hardware access. But in a virtualized environment, the VMM must run at Ring 0 to maintain control. If the guest OS also runs at Ring 0, it would have direct hardware access and could bypass the VMM — breaking isolation between VMs.

2. **Sensitive but not privileged instructions:** On x86, there are approximately 17 instructions that are "sensitive" (they behave differently depending on the ring level) but NOT "privileged" (they don't trigger a trap/fault when run outside Ring 0). When the guest OS executes these instructions at a lower ring, they **silently execute incorrectly** instead of trapping to the VMM. The VMM never gets a chance to intercept and emulate them.

3. **Trap-and-emulate breaks:** The standard trap-and-emulate technique (which works on architectures like IBM mainframes) relies on ALL sensitive instructions trapping when executed by the guest. On x86, this assumption fails — the 17 sensitive-but-unprivileged instructions slip through without trapping, causing the guest OS to produce incorrect results silently.

Therefore, the VMM cannot execute the unmodified OS binary correctly using basic trap-and-emulate on x86.

**(b) Can it run without source code modification?**

**No, with only trap-and-emulate, the VMM cannot run this unmodified OS correctly.** The sensitive-but-unprivileged instructions will execute incorrectly without the VMM being able to intercept them.

**However, two workarounds exist that do NOT require modifying the guest OS source code:**

| Solution | How It Works | Modifies Guest Source? |
|---|---|---|
| **Binary Translation** (VMware's approach, 1999) | The VMM scans the guest's instruction stream at runtime, identifies the 17 problematic instructions, and replaces them with safe equivalents that DO trap. The guest OS runs at Ring 1 (ring deprivileging). | **No** — translation happens on the binary, not the source code |
| **Hardware-Assisted Virtualization** (Intel VT-x / AMD-V, 2006) | The CPU adds a new privilege level below Ring 0 (VMX root mode for VMM, VMX non-root mode for guest). ALL sensitive instructions now correctly trap via hardware support. | **No** — the CPU hardware handles the problem |

**Conclusion:** Using only basic trap-and-emulate → **No**, the unmodified OS cannot be correctly virtualized. Using binary translation (software fix) or VT-x/AMD-V (hardware fix) → **Yes**, the unmodified OS CAN run correctly without source code changes.

> **Note:** Para-virtualization (e.g., Xen) DOES require modifying the guest OS source code (replacing privileged instructions with explicit hypercalls). This is the only approach that needs source modification.

---

### Q5. [4 Marks] — Why Are Containers More Lightweight Than VMs?

**Question:** Why are containers often considered more lightweight compared to virtual machines? Explain in terms of architecture, boot time, and resource overhead.

**Answer:**

Containers are lighter than VMs because of fundamental architectural differences:

| Aspect | Virtual Machine | Container |
|---|---|---|
| **What is virtualized** | Entire hardware stack — each VM runs its own OS kernel | Only the application layer — containers share the host OS kernel |
| **OS overhead** | Each VM includes a full OS (2-10 GB per VM) | No separate OS — shares host kernel (container image is typically 50-500 MB) |
| **Boot time** | Seconds to minutes (must boot full OS) | Milliseconds to seconds (just starts a process) |
| **Resource overhead** | High — OS processes, drivers, services consume CPU/memory per VM | Minimal — only the application and its dependencies |
| **Density** | 10-50 VMs per host | 100-1000+ containers per host |
| **Isolation mechanism** | Hypervisor provides hardware-level isolation | Linux namespaces + cgroups provide process-level isolation |

**Why lighter:**
- A VM includes: Guest OS + kernel + drivers + system services + application = heavy
- A container includes: Application + libraries + dependencies only = light
- Example: 3 VMs running Node.js apps on a 64GB host → each VM uses ~10GB for OS + 2GB for app = 36GB consumed. 3 containers on the same host → each uses ~2GB for app = 6GB consumed. The containers leave 58GB free vs 28GB with VMs.

**Trade-off:** Containers are lighter but share the host kernel — a kernel vulnerability affects ALL containers. VMs are heavier but provide stronger isolation (separate kernels).

---

### Q6. [6 Marks] — IAM Authentication, Authorization, Roles, and Policies

**Question:** Explain how AWS Identity and Access Management (IAM) handles authentication and authorization. Define IAM Users, Groups, Roles, and Policies with examples.

**Answer:**

**Authentication ("Who are you?"):**
IAM verifies identity through:
- **Console access:** Username + password + optional MFA (Multi-Factor Authentication)
- **Programmatic access:** Access Key ID + Secret Access Key (for CLI/SDK/API)
- **Temporary credentials:** Security Token Service (STS) for short-lived access via roles

**Authorization ("What can you do?"):**
After authentication, IAM evaluates policies to determine allowed actions:
- Every API call is checked against attached policies
- Default: everything is **denied** unless explicitly allowed
- Explicit deny always overrides allow

**IAM Components:**

| Component | What It Is | Example |
|---|---|---|
| **IAM User** | Identity for a person or service needing long-term AWS access | Employee "hinal" with console login + access keys |
| **IAM Group** | Collection of users with shared permissions | "Developers" group — all members can access EC2 and S3 |
| **IAM Role** | Temporary identity assumed by users/services — no permanent credentials | EC2 instance assumes "S3ReadOnlyRole" to access S3 without hardcoded keys |
| **IAM Policy** | JSON document defining permissions (Effect + Action + Resource) | Policy allowing `s3:GetObject` on `arn:aws:s3:::my-bucket/*` |

**Example IAM Policy (JSON):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
```
This policy allows reading objects from `my-bucket` but NOT writing, deleting, or accessing other buckets.

**Best Practices:**
- Use **root account** only for initial setup — never for daily tasks
- Enable **MFA** on root and all human users
- Use **roles** for EC2/Lambda instead of embedding access keys
- Follow **principle of least privilege** — grant only the minimum permissions needed

---

## SECTION 2: PRACTICE PAPERS (Same Pattern — 3 × 10 = 30 Marks)

> 5 complete practice papers following the exact mid-semester pattern.
> Each paper: 2 Scenario-Based + 1 Direct Concepts.
> Use these for timed practice — 40 minutes per paper (2 hours ÷ 3 questions × some buffer).

---
---

## PRACTICE PAPER 1 — Cloud Service Models + Virtualization

---

### Q1. [10 Marks] — Scenario-Based (Healthcare Cloud Deployment)

**(a) A healthcare company needs to deploy a patient records management system that stores sensitive medical data. Recommend the most appropriate cloud service model and deployment model. Justify your choice with reference to compliance requirements (HIPAA / India's DPDP Act). [6 marks]**

**Model Answer:**

**Recommended Service Model: PaaS (with IaaS for database tier)**

Healthcare applications need rapid development (PaaS benefit) but also strict control over data storage and encryption (IaaS benefit for the database layer).

**Recommended Deployment Model: Private Cloud (or Hybrid Cloud)**

| Aspect | Recommendation | Justification |
|---|---|---|
| **Deployment Model** | Private Cloud (primary) with Hybrid option | Patient medical records are classified as sensitive personal data under both HIPAA (USA) and DPDP Act 2023 (India). Private cloud ensures data never leaves organizational control. |
| **Service Model — App Tier** | PaaS (e.g., AWS Elastic Beanstalk on dedicated instances) | Developers focus on building the patient portal. Platform handles scaling, patching, load balancing. Faster time-to-market for a critical system. |
| **Service Model — DB Tier** | IaaS with self-managed encrypted DB or DBaaS (RDS with encryption at rest + in transit) | Full control over encryption keys (AWS KMS), access logging (CloudTrail), and data residency. Meets compliance requirement for data sovereignty. |
| **Data Residency** | Region-locked deployment (e.g., AWS Mumbai ap-south-1) | DPDP Act requires personal data of Indian citizens to be stored within India. HIPAA requires data to stay within covered entity's control. |

**Compliance-Specific Measures:**

1. **Encryption:** AES-256 encryption at rest (EBS encrypted volumes, S3 SSE-KMS). TLS 1.2+ for data in transit.
2. **Access Control:** IAM roles with least privilege. MFA mandatory for all users accessing patient data. Role-based access — doctors see patient records, billing staff sees only billing data.
3. **Audit Trail:** CloudTrail logs every API call. CloudWatch monitors access patterns. All logs retained for 7 years (HIPAA requirement).
4. **Data Backup:** Automated daily backups with cross-region replication (within India). RPO < 1 hour, RTO < 4 hours.
5. **BAA (Business Associate Agreement):** Required with AWS under HIPAA. AWS signs BAAs for HIPAA-eligible services.

**Why NOT Public Cloud alone:** Multi-tenant public cloud introduces risks of data co-mingling. While technically isolated, regulatory auditors may require physical or logical separation guarantees. Private or dedicated-tenancy public cloud addresses this.

**(b) The healthcare company also wants to ensure high availability. Recommend an AWS architecture that provides 99.99% uptime for the patient records system. [4 marks]**

**Model Answer:**

**High Availability Architecture:**

```
Region: ap-south-1 (Mumbai)
├── AZ-1 (ap-south-1a)
│   ├── Public Subnet: ALB node, NAT Gateway
│   ├── Private Subnet: EC2 App Server (Auto Scaling)
│   └── Private Subnet: RDS Primary (Multi-AZ master)
├── AZ-2 (ap-south-1b)
│   ├── Public Subnet: ALB node, NAT Gateway
│   ├── Private Subnet: EC2 App Server (Auto Scaling)
│   └── Private Subnet: RDS Standby (automatic failover)
└── S3: Encrypted backup storage (cross-AZ replication built-in)
```

**Key HA Components:**
1. **Multi-AZ RDS:** Primary database in AZ-1, synchronous standby in AZ-2. If AZ-1 fails, automatic failover to AZ-2 in ~60 seconds.
2. **Auto Scaling Group across AZs:** Minimum 2 EC2 instances (one per AZ). If one AZ goes down, the other AZ handles all traffic.
3. **Application Load Balancer (ALB):** Distributes traffic across both AZs. Health checks detect unhealthy instances and stop routing to them.
4. **S3 for backups:** 11 nines durability, automatically replicated across 3+ AZs.

This architecture survives a full AZ failure (fire, power outage, network cut) without data loss or service interruption. 99.99% uptime = maximum ~52 minutes downtime per year.

---

### Q2. [10 Marks] — Scenario-Based (CPU Overcommitment Diagnosis)

**A company runs 500 VMs on a VMware ESXi cluster. The vCPU overcommitment ratio is 6:1. Users are reporting application slowness and intermittent timeouts.**

**(a) Diagnose the likely cause of the performance issues. What metrics would you check? [5 marks]**

**Model Answer:**

**Likely Cause: CPU overcommitment at 6:1 is above the safe threshold.**

A 6:1 vCPU ratio means 6 virtual CPUs are competing for every 1 physical CPU core. The safe ratio for mixed workloads is 3:1 to 5:1. At 6:1, VMs are likely experiencing CPU contention — they are ready to execute but must wait for a physical core to become available.

**Key Metrics to Check:**

| Metric | What It Tells You | Warning Threshold |
|---|---|---|
| **CPU Ready Time (%)** | Time a VM is ready to run but waiting for a physical core | > 5% = overcommitted. > 10% = severely degraded. |
| **CPU Co-Stop Time** | Time a multi-vCPU VM waits for all its vCPUs to be scheduled simultaneously | > 3% = problem (common with 4+ vCPU VMs) |
| **CPU Used (MHz)** | Actual CPU consumed vs entitled | If consistently near entitlement, VM is CPU-starved |
| **CPU Wait Time** | Time spent waiting for I/O (not CPU contention) | Helps distinguish CPU vs I/O bottleneck |
| **Per-Host CPU Utilization** | Overall host CPU usage | > 80% sustained = danger zone |
| **VM CPU Latency** | End-to-end CPU scheduling delay per VM | > 5ms = noticeable to applications |

**Diagnostic Steps:**
1. Open vCenter → Performance tab → check CPU Ready across all hosts.
2. Identify hosts with CPU Ready > 5% — these are the overcommitted ones.
3. On those hosts, identify VMs with the highest vCPU counts (VMs with 8 vCPUs cause more co-stop than VMs with 2 vCPUs).
4. Check if the problem is cluster-wide or localized to specific hosts (DRS may not be balancing properly).

**(b) Recommend specific solutions to resolve the performance issues without purchasing new hardware. [5 marks]**

**Model Answer:**

**Immediate Fixes (No Hardware Purchase):**

**1. Right-size VMs (reduce vCPU allocation):**
- Many VMs are over-provisioned. A web server allocated 8 vCPUs but using only 2 creates unnecessary scheduling overhead.
- Audit CPU usage: any VM consistently using < 50% of allocated vCPUs should be downsized.
- Reducing from 8 vCPUs to 2 vCPUs on 100 VMs recovers 600 vCPUs from the contention pool.

**2. Enable DRS (Distributed Resource Scheduler):**
- DRS automatically migrates VMs between hosts to balance CPU load.
- Set DRS to "Fully Automated" with a moderate migration threshold.
- This prevents hotspots where one host is at 90% CPU while another is at 30%.

**3. Set CPU reservations for critical VMs:**
- Business-critical VMs (databases, payment processing) get CPU reservations — guaranteed minimum MHz.
- Less critical VMs (dev/test, monitoring) get no reservation — they yield CPU to critical VMs during contention.

**4. Reduce vCPU-to-pCPU ratio to 4:1:**
- Target: bring the cluster from 6:1 to 4:1.
- Method: migrate 15-20% of non-critical VMs to off-peak schedules, or consolidate low-usage VMs.

**5. Schedule non-critical workloads off-peak:**
- Batch jobs, backups, reporting queries — schedule these during nights/weekends.
- Use vSphere Scheduled Tasks or cron jobs to shut down dev/test VMs outside business hours.

**6. Convert to containers where possible:**
- Stateless web servers and API services can be containerized.
- 10 VMs running Nginx (10 × OS overhead) → 10 containers on 1 VM (1 × OS overhead).
- Significant CPU and memory savings.

**Expected Outcome:** Reducing ratio from 6:1 to 4:1 should bring CPU Ready below 5% for all production VMs, eliminating user-visible slowness.

---

### Q3. [10 Marks] — Direct Concepts (Virtualization Types)

**(a) Compare full virtualization, para-virtualization, and hardware-assisted virtualization across at least 6 dimensions. [5 marks]**

**Model Answer:**

| Dimension | Full Virtualization | Para-Virtualization | Hardware-Assisted |
|---|---|---|---|
| **Mechanism** | Binary translation — VMM rewrites sensitive instructions at runtime | Hypercalls — guest kernel modified to call hypervisor API directly | CPU extensions (VT-x/AMD-V) — hardware traps sensitive instructions automatically |
| **Guest OS Modified?** | No — runs unmodified guest OS | Yes — guest kernel must be rewritten to replace privileged instructions with hypercalls | No — runs unmodified guest OS |
| **Performance** | Moderate — binary translation adds overhead (5-15%) | Good — hypercalls are faster than trapping (3-8% overhead) | Best — near-native (1-3% overhead), hardware does the work |
| **Compatibility** | Any OS (including proprietary like Windows) | Only open-source OS where kernel can be modified (Linux, BSD). Cannot run unmodified Windows. | Any OS (same as full virtualization) |
| **Implementation Complexity** | High — binary translator is complex software | Medium — requires kernel modifications and maintenance | Low — relies on CPU hardware; simpler hypervisor code |
| **Historical Context** | VMware's original approach (1999) to solve x86 ring problem | Xen's approach (2003) — chose performance over compatibility | Intel VT-x (2005) and AMD-V (2006) made others obsolete |
| **Examples** | Early VMware Workstation, QEMU (with TCG) | Xen PV mode, early KVM paravirt drivers | KVM, VMware ESXi (modern), Hyper-V, Xen HVM |
| **Current Status** | Legacy — rarely used alone today | Partially used — paravirt drivers (virtio) still used for I/O inside HW-assisted VMs | **Dominant** — all modern hypervisors and cloud providers use this |

**(b) Explain the x86 virtualization challenge. Why do standard x86 processors not satisfy the Popek-Goldberg criteria? How was this solved? [5 marks]**

**Model Answer:**

**The Popek-Goldberg Criteria (1974):**
A Virtual Machine Monitor (VMM/hypervisor) must satisfy three conditions:
1. **Equivalence (Fidelity):** A program running in a VM must behave identically to running on bare metal.
2. **Resource Control (Safety):** The VMM must have complete control over all hardware resources.
3. **Efficiency (Performance):** A majority of guest instructions must execute directly on the hardware without VMM intervention.

The classic "trap-and-emulate" approach achieves this: guest runs in unprivileged mode; when it executes a privileged (sensitive) instruction, the CPU traps to the VMM, which emulates the instruction safely.

**The x86 Problem:**
x86 CPUs have 4 privilege rings: Ring 0 (most privileged, kernel) → Ring 3 (least privileged, user apps).

The VMM needs Ring 0 to control hardware. The guest OS also expects Ring 0 to manage its own kernel operations. Both can't occupy Ring 0 simultaneously.

**Critical issue:** x86 has **17 sensitive-but-not-privileged instructions** — instructions that behave differently depending on the privilege level but do NOT trigger a trap when executed outside Ring 0. Instead of trapping to the VMM, they silently fail or return incorrect results.

Example: `POPF` (pop flags) — when executed in Ring 0, it modifies the interrupt flag. When executed in Ring 1 or Ring 3, it silently ignores the interrupt flag change without trapping. The guest OS thinks it disabled interrupts, but it didn't.

This violates Popek-Goldberg because the VMM never gets a chance to intercept and emulate these instructions.

**Solutions (chronological):**

| Solution | Year | How It Works |
|---|---|---|
| **Binary Translation (VMware)** | 1999 | VMM scans guest code blocks before execution, rewrites the 17 problematic instructions with safe equivalents. Guest runs in Ring 1 (ring deprivileging). Performance overhead: 5-15%. |
| **Para-virtualization (Xen)** | 2003 | Modify the guest kernel to replace all sensitive instructions with explicit hypercalls to the hypervisor. Avoids the trap problem entirely. Requires guest kernel source code. |
| **Hardware-Assisted (Intel VT-x / AMD-V)** | 2005-2006 | CPU adds two new modes: VMX root (hypervisor) and VMX non-root (guest). Guest runs at Ring 0 in non-root mode. ALL sensitive instructions in non-root mode automatically trap (VM Exit) to the VMM in root mode. Problem solved in hardware. |

**Today's answer:** Hardware-assisted virtualization (VT-x/AMD-V) is the universal solution. All modern x86 CPUs support it since ~2006. It provides unmodified guest OS support with near-native performance — the best of all worlds.

---
---

## PRACTICE PAPER 2 — AWS Services + Containers

---

### Q1. [10 Marks] — Scenario-Based (AWS Architecture for Startup)

**(a) A startup is launching a food delivery application. Design an AWS architecture using EC2, S3, RDS, VPC, and ELB. Explain the role of each service and how they interact. [6 marks]**

**Model Answer:**

**Architecture Design:**

```
Internet
   ↓
[Route 53 — DNS]
   ↓
[CloudFront — CDN] ←→ [S3 — Static Assets (images, menus, CSS/JS)]
   ↓
[Application Load Balancer (ALB)] — distributes across AZs
   ↓                    ↓
[AZ-1]              [AZ-2]
┌──────────┐     ┌──────────┐
│ Public    │     │ Public    │
│ Subnet   │     │ Subnet   │
│ NAT GW   │     │ NAT GW   │
├──────────┤     ├──────────┤
│ Private   │     │ Private   │
│ EC2: App  │     │ EC2: App  │
│ Servers   │     │ Servers   │
│ (Auto     │     │ (Auto     │
│ Scaling)  │     │ Scaling)  │
├──────────┤     ├──────────┤
│ Private   │     │ Private   │
│ RDS       │     │ RDS       │
│ Primary   │     │ Standby   │
└──────────┘     └──────────┘
```

**Role of Each Service:**

| Service | Role in Food Delivery App | Configuration |
|---|---|---|
| **VPC** | Isolated virtual network containing all resources. Defines IP ranges, subnets, security boundaries. | CIDR: 10.0.0.0/16. Public subnets (10.0.1.0/24, 10.0.2.0/24) for ALB. Private subnets (10.0.3.0/24, 10.0.4.0/24) for app servers. Private subnets (10.0.5.0/24, 10.0.6.0/24) for database. |
| **EC2** | Application servers running the food delivery backend (order processing, restaurant matching, driver tracking, payment gateway). | Instance type: M5.large (balanced compute/memory). Auto Scaling Group: min 2, max 20, scale on CPU > 70%. |
| **ELB (ALB)** | Distributes incoming HTTP/HTTPS requests across EC2 instances in both AZs. Health checks every 30 seconds — unhealthy instances removed from rotation. | Path-based routing: /api/* → app servers, /admin/* → admin servers. SSL termination at ALB. |
| **RDS** | Managed PostgreSQL database storing users, orders, restaurants, menus, payment records. Multi-AZ for automatic failover. | db.r5.large, Multi-AZ enabled, automated backups (7-day retention), read replica for reporting queries. |
| **S3** | Stores static assets: restaurant images, menu photos, app icons, user profile pictures, delivery receipts/invoices. | Bucket with versioning enabled. Lifecycle policy: move images older than 90 days to S3-IA (cost savings). |

**Interaction Flow (User places an order):**
1. User opens app → DNS (Route 53) resolves to CloudFront → static assets (images, JS) served from S3 via CDN.
2. User searches restaurants → request hits ALB → routed to EC2 app server → queries RDS for restaurant data.
3. User places order → EC2 processes order logic → writes to RDS → triggers notification to restaurant and driver.
4. Order confirmation page → served from EC2 with restaurant image URLs pointing to S3/CloudFront.

**(b) The startup expects 10x traffic during lunch and dinner hours. How would you configure Auto Scaling to handle this efficiently? Include scaling policies and cost optimization. [4 marks]**

**Model Answer:**

**Auto Scaling Configuration:**

| Parameter | Value | Rationale |
|---|---|---|
| **Minimum instances** | 2 (one per AZ) | Always maintain HA even during lowest traffic (2 AM) |
| **Desired instances** | 4 | Baseline for normal traffic hours |
| **Maximum instances** | 20 | Cap to prevent runaway costs during unexpected spikes |

**Scaling Policies (Two-Tier):**

**1. Target Tracking Policy (Primary):**
- Metric: Average CPU utilization
- Target: 65%
- Cooldown: 300 seconds (scale-out), 600 seconds (scale-in)
- When average CPU > 65%, add instances. When < 65%, remove instances.

**2. Scheduled Scaling (Predictable Patterns):**
- 11:00 AM → scale to 10 instances (pre-warm for lunch rush)
- 2:30 PM → scale to 4 instances (lunch over)
- 6:30 PM → scale to 12 instances (pre-warm for dinner rush)
- 10:00 PM → scale to 2 instances (night quiet period)

**Cost Optimization:**
- **Baseline (min 2):** Reserved Instances (1-year, partial upfront) — 40% savings vs On-Demand.
- **Predictable scaling (next 4-6):** On-Demand instances for peak hours.
- **Burst capacity (above 8):** Spot Instances for additional capacity — up to 90% savings. Use Spot Fleet with multiple instance types to reduce interruption risk.
- **Estimated monthly cost:** ₹1.5-3 lakhs (vs ₹15-20 lakhs for provisioning 20 servers 24/7).

---

### Q2. [10 Marks] — Scenario-Based (Monolith to Microservices Migration)

**(a) A company is migrating its monolithic order management application to microservices. Compare VM-based deployment vs container-based deployment for this migration. [5 marks]**

**Model Answer:**

| Dimension | VM-Based Deployment | Container-Based Deployment |
|---|---|---|
| **Deployment unit** | Each microservice runs in a separate VM (full OS per service) | Each microservice runs in a separate container (shared OS kernel) |
| **Resource overhead** | High — 10 microservices = 10 VMs = 10 OS copies = 50-100 GB RAM just for OS | Low — 10 microservices = 10 containers = 1 OS = 2-5 GB RAM for app code |
| **Startup time** | 30-120 seconds per VM (boot OS, initialize services) | 1-5 seconds per container (start process directly) |
| **Scaling speed** | Slow — spinning up a new VM takes minutes | Fast — new container starts in seconds. Can scale from 3 to 30 instances in under a minute. |
| **Isolation** | Strong — each VM has own kernel. Security breach in one VM doesn't affect others. | Moderate — shared kernel. Stronger isolation possible with gVisor/Kata containers. |
| **Image size** | GBs (OS + app + dependencies) | MBs (app + dependencies only). Faster pulls, faster deployments. |
| **CI/CD integration** | Harder — building VM images (AMIs) takes 10-30 minutes | Easy — Docker build takes 30 seconds. Push to registry, pull and deploy. |
| **Portability** | Hypervisor-dependent (VMware image ≠ KVM image) | Highly portable — same Docker image runs on any platform with Docker |
| **Cost** | Higher — more compute/memory wasted on OS overhead | Lower — higher density means fewer hosts needed for the same workload |
| **Operational complexity** | Familiar to traditional ops teams. Standard patching, monitoring. | Requires new skills: Docker, Kubernetes, container networking, image security scanning. |

**Verdict for this migration:** Container-based deployment is strongly preferred for microservices because:
1. Microservices need fast scaling — containers scale in seconds, VMs in minutes.
2. Microservices are numerous — 10-50 services means 10-50 OS copies with VMs (wasteful).
3. CI/CD is critical for microservices — container image builds are fast and reproducible.

**(b) Recommend a Docker + Kubernetes setup for the migrated microservices. Explain how Kubernetes handles scaling and self-healing for this application. [5 marks]**

**Model Answer:**

**Recommended Setup:**

**Docker Side:**
- Each microservice has its own Dockerfile and builds into a separate Docker image.
- Images are tagged with version numbers (e.g., `order-service:v2.3.1`) and pushed to Amazon ECR (private container registry).
- Multi-stage builds used to keep images small (builder stage compiles code, final stage copies only the binary).

**Kubernetes Side (Amazon EKS):**
- **EKS Cluster** with 3-5 worker nodes (EC2 instances) across 2 AZs.
- Each microservice defined as a **Deployment** with desired replica count.

```yaml
# Example: Order Service Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  template:
    spec:
      containers:
      - name: order-service
        image: 123456.dkr.ecr.ap-south-1.amazonaws.com/order-service:v2.3.1
        resources:
          requests: { cpu: "250m", memory: "256Mi" }
          limits: { cpu: "500m", memory: "512Mi" }
        livenessProbe:
          httpGet: { path: /health, port: 8080 }
          periodSeconds: 10
```

**How Kubernetes Handles Scaling:**

1. **Horizontal Pod Autoscaler (HPA):**
   - Monitors CPU/memory usage of order-service pods.
   - Rule: if average CPU > 70%, add pods. If < 30%, remove pods.
   - During lunch rush: 3 pods → 10 pods automatically.
   - After rush: 10 pods → 3 pods. Resources freed for other services.

2. **Cluster Autoscaler:**
   - If HPA wants to create pods but no nodes have capacity, Cluster Autoscaler adds new EC2 worker nodes.
   - When pods are removed and nodes are underutilized, Cluster Autoscaler removes nodes (cost savings).

**How Kubernetes Handles Self-Healing:**

1. **Liveness Probe Failure:** If `/health` endpoint of a pod stops responding, Kubernetes restarts that container automatically (within 10-30 seconds).
2. **Pod Crash:** If the order-service process crashes (exit code ≠ 0), Kubernetes restarts it with exponential backoff (immediately, then 10s, 20s, 40s...).
3. **Node Failure:** If a worker node goes down, all pods on that node are rescheduled to healthy nodes within 5 minutes. The Deployment controller ensures 3 replicas are always running.
4. **Rolling Updates:** New version deployed with zero downtime — old pods are replaced one at a time. If the new version fails health checks, Kubernetes automatically rolls back.

---

### Q3. [10 Marks] — Direct Concepts (Docker & Orchestration)

**(a) Define the following Dockerfile instructions and give an example of each: FROM, RUN, COPY, CMD, ENTRYPOINT, EXPOSE, WORKDIR, ENV. [5 marks]**

**Model Answer:**

| Instruction | Purpose | Example |
|---|---|---|
| **FROM** | Sets the base image. Every Dockerfile starts with FROM. Defines the foundation (OS + runtime). | `FROM python:3.11-slim` — starts with a lightweight Python 3.11 image |
| **RUN** | Executes a command during image build. Used to install packages, create directories, compile code. Each RUN creates a new image layer. | `RUN pip install flask gunicorn` — installs Python packages into the image |
| **COPY** | Copies files/directories from host machine into the image. Preferred over ADD for simple copies. | `COPY ./app /usr/src/app` — copies the app directory from host into the image |
| **CMD** | Sets the default command executed when a container starts. Can be overridden at runtime with `docker run <image> <command>`. | `CMD ["python", "app.py"]` — runs the Python app when container starts |
| **ENTRYPOINT** | Sets the fixed executable for the container. Unlike CMD, it is NOT easily overridden. Arguments passed at runtime are appended to ENTRYPOINT. | `ENTRYPOINT ["gunicorn"]` with `CMD ["--bind", "0.0.0.0:8080", "app:app"]` — gunicorn is always the executable, CMD provides default args |
| **EXPOSE** | Documents which port the container listens on. Does NOT actually publish the port — that's done with `docker run -p`. | `EXPOSE 8080` — declares the app listens on port 8080 |
| **WORKDIR** | Sets the working directory for subsequent RUN, CMD, COPY instructions. Creates the directory if it doesn't exist. | `WORKDIR /usr/src/app` — all subsequent commands run from this directory |
| **ENV** | Sets environment variables available during build and at runtime inside the container. | `ENV FLASK_ENV=production` — sets the Flask environment variable |

**Complete Example Dockerfile:**
```dockerfile
FROM python:3.11-slim
WORKDIR /usr/src/app
ENV FLASK_ENV=production
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8080
ENTRYPOINT ["gunicorn"]
CMD ["--bind", "0.0.0.0:8080", "app:app"]
```

**Layer optimization note:** COPY requirements.txt and RUN pip install are placed BEFORE COPY . . so that dependency installation is cached. Only when requirements.txt changes does pip install re-run. Code changes only rebuild the final COPY layer.

**(b) Explain container orchestration. Why is it needed? Compare the imperative and declarative approaches to managing containers with examples. [5 marks]**

**Model Answer:**

**What is Container Orchestration?**
Container orchestration is the automated management of the lifecycle of containers at scale — including deployment, scaling, networking, load balancing, health monitoring, and self-healing across a cluster of machines. When running hundreds or thousands of containers, manual management becomes impossible.

**Why is it Needed?**

| Challenge Without Orchestration | How Orchestration Solves It |
|---|---|
| **Scaling:** Manually start/stop containers on different hosts | Auto-scaling: define desired replicas, orchestrator maintains count |
| **Failure recovery:** Container crashes → manual restart, node fails → manual redeployment | Self-healing: automatic restart, rescheduling to healthy nodes |
| **Load balancing:** Manually configure nginx/HAProxy for each service | Built-in service discovery and load balancing across pods |
| **Updates:** Stop old containers, start new ones (downtime) | Rolling updates: replace containers one-by-one with zero downtime |
| **Resource allocation:** Guessing which host has capacity | Scheduler automatically places containers on hosts with available resources |
| **Networking:** Manually manage IP addresses and port conflicts | Overlay networks: each container gets a unique IP, automatic DNS-based service discovery |

**Imperative vs Declarative Approaches:**

| Aspect | Imperative | Declarative |
|---|---|---|
| **Philosophy** | "Tell the system HOW to do it" — step-by-step commands | "Tell the system WHAT you want" — describe desired end-state |
| **Method** | Individual CLI commands executed in sequence | YAML/JSON manifest files applied to the cluster |
| **Example** | `kubectl run nginx --image=nginx --replicas=3` then `kubectl expose deployment nginx --port=80` | Write a `deployment.yaml` with `replicas: 3`, `image: nginx`, `port: 80` and apply with `kubectl apply -f deployment.yaml` |
| **Reproducibility** | Poor — commands are ephemeral, hard to repeat exactly | Excellent — YAML files are version-controlled, repeatable, reviewable |
| **Drift detection** | None — system state may diverge from intent over time | Continuous — Kubernetes constantly reconciles actual state with desired state |
| **Use case** | Quick experiments, debugging, one-time tasks | Production deployments, CI/CD pipelines, team collaboration |
| **Kubernetes tools** | `kubectl run`, `kubectl create`, `kubectl scale` | `kubectl apply -f`, `kubectl diff`, Helm charts |

**Key Insight:** Kubernetes is fundamentally declarative. You declare "I want 3 replicas of nginx on port 80" in a YAML file. Kubernetes continuously ensures reality matches the declaration. If a pod dies, Kubernetes creates a new one automatically to maintain the declared state of 3 replicas. This is the "reconciliation loop" — the core of Kubernetes.

---
---

## PRACTICE PAPER 3 — Storage + Networking

---

### Q1. [10 Marks] — Scenario-Based (Cloud Storage Architecture)

**(a) A media company stores 50 TB of video content, with varying access patterns: some videos are accessed millions of times daily (trending), some monthly (catalog), and some rarely (archive/compliance). Recommend AWS storage services and design a lifecycle policy. [6 marks]**

**Model Answer:**

**Storage Architecture by Access Pattern:**

| Content Type | Volume | Access Pattern | Recommended Storage | Cost (approx/GB/month) |
|---|---|---|---|---|
| **Trending videos** (top 500) | ~2 TB | Millions of requests/day, low latency critical | **S3 Standard** + **CloudFront CDN** | $0.023 (S3) + CDN caching |
| **Active catalog** (recent 6 months) | ~15 TB | Accessed weekly-monthly, moderate latency OK | **S3 Standard-IA (Infrequent Access)** | $0.0125 |
| **Older catalog** (6-24 months) | ~15 TB | Accessed rarely, retrieval in minutes acceptable | **S3 Glacier Instant Retrieval** | $0.004 |
| **Compliance archive** (>2 years) | ~18 TB | Almost never accessed, retrieval in hours OK | **S3 Glacier Deep Archive** | $0.00099 |

**Total monthly storage cost:** ~$150 (vs ~$1,150 if everything was in S3 Standard). **87% cost savings.**

**Lifecycle Policy Design:**

```
Day 0:   Video uploaded → S3 Standard (hot storage)
         ↓ (CloudFront caches trending videos at edge)
Day 30:  If views < 1000/day → Move to S3 Standard-IA
         (saves 46% storage cost, retrieval still instant)
         ↓
Day 180: If views < 100/month → Move to S3 Glacier Instant Retrieval
         (saves 83% storage cost, retrieval in milliseconds)
         ↓
Day 730: Move to S3 Glacier Deep Archive
         (saves 96% storage cost, retrieval in 12-48 hours)
         ↓
Day 2555 (7 years): Delete (if no compliance hold)
```

**Implementation — S3 Lifecycle Rule:**
```json
{
  "Rules": [
    {
      "ID": "MediaLifecycle",
      "Status": "Enabled",
      "Transitions": [
        { "Days": 30, "StorageClass": "STANDARD_IA" },
        { "Days": 180, "StorageClass": "GLACIER_IR" },
        { "Days": 730, "StorageClass": "DEEP_ARCHIVE" }
      ],
      "Expiration": { "Days": 2555 }
    }
  ]
}
```

**Additional Considerations:**
- **CloudFront** caches trending videos at 400+ edge locations globally, reducing S3 request costs and latency.
- **S3 Intelligent-Tiering** can be used for videos with unpredictable access patterns (auto-moves between tiers based on access).
- **S3 Object Lock** with Compliance mode for regulatory content (prevents deletion even by root user).

**(b) The media company also needs to store metadata for its video catalog in a database. Compare EBS and EFS for the database storage needs. Which would you recommend? [4 marks]**

**Model Answer:**

| Dimension | EBS (Elastic Block Store) | EFS (Elastic File System) |
|---|---|---|
| **Storage type** | Block storage (like a virtual hard disk) | File storage (NFS — network file system) |
| **Access** | Attached to a **single EC2 instance** (same AZ) | Shared across **multiple EC2 instances** (cross-AZ) |
| **Performance** | Highest IOPS available (io2: up to 64,000 IOPS). Consistent, low-latency. | Good throughput for parallel reads, but lower per-operation IOPS than EBS io2. |
| **Scaling** | Fixed size — you provision 500 GB, you pay for 500 GB even if using 100 GB. Manual resize possible. | Auto-scaling — grows/shrinks with usage. Pay only for what you store. |
| **Durability** | 99.999% (within a single AZ). Snapshots to S3 for cross-AZ backup. | 99.999999999% (replicated across 3+ AZs automatically). |
| **Cost** | gp3: $0.08/GB/month. io2: $0.125/GB/month + $0.065/provisioned IOPS. | $0.30/GB/month (Standard). More expensive per GB. |
| **Best for** | Databases (MySQL, PostgreSQL), boot volumes, single-instance workloads | Shared file systems, CMS, machine learning training data, content repositories |

**Recommendation for Video Metadata Database: EBS (io2 type)**

**Justification:**
1. **Databases need block storage.** Relational databases (PostgreSQL, MySQL) require block-level I/O for random read/write operations (index lookups, joins, transactions). EBS provides this; EFS does not.
2. **IOPS requirement.** A metadata database serving millions of video lookups needs consistent, low-latency I/O. EBS io2 provides up to 64,000 IOPS with single-digit millisecond latency.
3. **Single-instance attachment is fine.** The database runs on one EC2 instance (with RDS Multi-AZ for failover). No need for multi-instance file sharing.
4. **Cost-effective for databases.** EBS io2 at $0.125/GB is cheaper than EFS at $0.30/GB for database workloads.

**Exception — use EFS if:** The metadata needs to be shared as files across multiple processing servers (e.g., video transcoding workers reading metadata simultaneously). In that case, EFS's shared NFS access is valuable.

---

### Q2. [10 Marks] — Scenario-Based (VPC Design for 3-Tier App)

**(a) Design a VPC architecture for a 3-tier web application (web tier, application tier, database tier). Include subnet design, security groups, and NACLs. Explain traffic flow. [6 marks]**

**Model Answer:**

**VPC Architecture:**

```
VPC: 10.0.0.0/16 (65,536 IPs)
│
├── AZ-1 (ap-south-1a)
│   ├── Public Subnet: 10.0.1.0/24 (Web Tier)
│   │   └── EC2: Nginx/Apache web servers
│   │   └── NAT Gateway (for private subnet outbound)
│   ├── Private Subnet: 10.0.3.0/24 (App Tier)
│   │   └── EC2: Node.js/Java application servers
│   └── Private Subnet: 10.0.5.0/24 (DB Tier)
│       └── RDS: PostgreSQL primary
│
├── AZ-2 (ap-south-1b)
│   ├── Public Subnet: 10.0.2.0/24 (Web Tier)
│   │   └── EC2: Nginx/Apache web servers
│   │   └── NAT Gateway
│   ├── Private Subnet: 10.0.4.0/24 (App Tier)
│   │   └── EC2: Node.js/Java application servers
│   └── Private Subnet: 10.0.6.0/24 (DB Tier)
│       └── RDS: PostgreSQL standby
│
├── Internet Gateway (attached to VPC)
└── ALB (in public subnets, routes to app tier)
```

**Security Groups (Stateful — Instance Level):**

| Security Group | Inbound Rules | Outbound Rules |
|---|---|---|
| **SG-Web** (Web tier) | Port 80/443 from 0.0.0.0/0 (internet) | Port 8080 to SG-App only |
| **SG-App** (App tier) | Port 8080 from SG-Web only | Port 5432 to SG-DB only; Port 443 to 0.0.0.0/0 (external APIs) |
| **SG-DB** (DB tier) | Port 5432 from SG-App only | Deny all (database doesn't initiate outbound) |

**Key principle:** Each tier can only talk to its adjacent tier. Web → App → DB. No direct Web → DB access. This is **defense in depth**.

**NACLs (Stateless — Subnet Level):**

| NACL | Inbound Allow | Inbound Deny | Outbound Allow |
|---|---|---|---|
| **NACL-Public** | 80, 443 from 0.0.0.0/0; Ephemeral ports (1024-65535) for return traffic | All other ports | 80, 443, 8080; Ephemeral ports |
| **NACL-Private-App** | 8080 from 10.0.1.0/24 and 10.0.2.0/24 (web subnets) | All from 0.0.0.0/0 (no internet) | 5432 to 10.0.5.0/24 and 10.0.6.0/24 (DB subnets) |
| **NACL-Private-DB** | 5432 from 10.0.3.0/24 and 10.0.4.0/24 (app subnets) | All else | Ephemeral ports to app subnets |

**(b) Explain the difference between Security Groups and NACLs. Why do we need both? [4 marks]**

**Model Answer:**

| Feature | Security Groups | NACLs |
|---|---|---|
| **Level** | Instance-level (attached to ENI/instance) | Subnet-level (applies to all instances in subnet) |
| **Stateful?** | **Yes** — if inbound is allowed, return traffic is automatically allowed | **No** — must explicitly allow both inbound AND outbound (including return traffic on ephemeral ports) |
| **Rule type** | **Allow only** — you specify what to allow; everything else is implicitly denied | **Allow + Deny** — can explicitly deny specific IPs/ports (useful for blocking known bad actors) |
| **Rule evaluation** | All rules evaluated together. If any rule allows, traffic passes. | Rules processed **in number order** (lowest first). First match wins. |
| **Default** | Default SG: allows all outbound, denies all inbound | Default NACL: allows all inbound and outbound |
| **Scope** | Can reference other Security Groups (SG-Web → SG-App) | Can only reference CIDR blocks (IP ranges) |

**Why We Need Both (Defense in Depth):**

1. **Security Groups = fine-grained, identity-based control.** "Only the web servers (SG-Web) can talk to app servers (SG-App) on port 8080." This is the primary security mechanism.

2. **NACLs = coarse-grained, network-based control.** "Block all traffic from IP range 203.0.113.0/24 (known attacker)." NACLs can explicitly DENY traffic — Security Groups cannot.

3. **Layered defense.** If an attacker compromises a web server and modifies its Security Group (unlikely but possible), the NACL still blocks unauthorized traffic at the subnet boundary. Two independent layers mean both must be breached.

4. **Compliance requirement.** Many security frameworks (SOC 2, ISO 27001) require network segmentation at multiple levels. Having both SGs and NACLs satisfies auditors.

**Analogy:** Security Groups = door locks on each room (per-instance). NACLs = security gates on each floor (per-subnet). A visitor must pass the floor gate AND the room lock.

---

### Q3. [10 Marks] — Direct Concepts (Storage + Compute)

**(a) Compare EBS, EFS, and S3 across at least 7 dimensions. When would you use each? [5 marks]**

**Model Answer:**

| Dimension | EBS | EFS | S3 |
|---|---|---|---|
| **Storage type** | Block storage (raw disk) | File storage (NFS) | Object storage (key-value) |
| **Access pattern** | Single EC2 instance (same AZ) | Multiple EC2 instances (cross-AZ, shared) | Anywhere (HTTP/HTTPS, any device) |
| **Performance** | Highest IOPS (io2: 64,000 IOPS). Low latency for random I/O. | Good throughput for parallel reads. Burst mode for small files. | High throughput for large objects. Not designed for random I/O. |
| **Scaling** | Fixed size — provision upfront (e.g., 500 GB). Manual resize possible but requires planning. | Auto-scaling — grows/shrinks automatically. No provisioning needed. | Unlimited — no capacity planning. Store petabytes. |
| **Durability** | 99.999% (single AZ). Snapshots to S3 for cross-AZ/region backup. | 99.999999999% (replicated across 3+ AZs). | 99.999999999% (11 nines, 3+ AZ replication). |
| **Cost** | gp3: $0.08/GB/month. io2: $0.125/GB. Pay for provisioned size. | $0.30/GB/month (Standard). Pay for actual usage. | $0.023/GB/month (Standard). Cheapest per GB. |
| **Data model** | Raw blocks — no file system awareness. OS formats it (ext4, NTFS). | POSIX file system — files, directories, permissions. | Objects — flat namespace with key (path), value (data), metadata. No directories (prefixes simulate folders). |
| **Latency** | Sub-millisecond (for io2) | Low single-digit milliseconds | ~100-200ms for first byte (HTTP overhead) |

**When to Use Each:**

- **EBS:** Databases (PostgreSQL, MySQL, MongoDB), boot volumes, any workload needing low-latency random I/O on a single instance.
- **EFS:** Shared file systems across multiple servers (web content, CMS media, ML training data shared across GPU instances), home directories.
- **S3:** Static website hosting, data lakes, backup/archive, log storage, media distribution, application assets (images, videos, documents).

**(b) Define EC2 tenancy options (Shared, Dedicated Instance, Dedicated Host). Explain placement groups and when each type is used. [5 marks]**

**Model Answer:**

**EC2 Tenancy Options:**

| Tenancy | Description | Hardware Sharing | Cost | Use Case |
|---|---|---|---|---|
| **Shared (default)** | Your EC2 instance runs on a physical host shared with other AWS customers' instances. Isolated at the hypervisor level — you can't see or access other tenants' data. | Shared with other customers | Lowest | Standard workloads, web servers, dev/test. Most common choice. |
| **Dedicated Instance** | Your instance runs on hardware dedicated to your AWS account only. Other customers' instances are NOT on the same physical host. However, your other instances may share the same host. | Shared only within your account | ~10-30% more than shared | Compliance requirements mandating single-tenant hardware (banking, government). Software licenses tied to physical sockets. |
| **Dedicated Host** | You get an entire physical server. You have visibility into physical cores, sockets, and host ID. Instances run only on YOUR designated host. | Exclusively yours | Highest (pay per host) | Software licensing that requires binding to specific physical servers (Oracle, SQL Server per-core licensing). Regulatory requirements needing physical server audit trails. |

**Key Difference — Dedicated Instance vs Dedicated Host:**
- Dedicated Instance: "I don't want other *customers* on my hardware" (but AWS manages host placement).
- Dedicated Host: "Give me a specific physical server with specific core counts" (you control placement and see hardware details).

**Placement Groups:**

Placement groups control WHERE EC2 instances are physically placed relative to each other.

| Type | Strategy | Max per AZ | Use Case | Trade-off |
|---|---|---|---|---|
| **Cluster** | All instances packed into the **same rack** within a single AZ. Connected via high-bandwidth, low-latency network. | Unlimited | HPC (molecular simulations, weather modeling), distributed databases needing fast inter-node communication. Tightly coupled workloads. | Single rack = single point of failure. If rack fails, ALL instances go down. |
| **Spread** | Each instance placed on **distinct physical hardware** (different racks, different power sources). | **Max 7 instances per AZ** | Critical applications where each instance must survive independent hardware failures. HA for small, critical deployments (e.g., 3 ZooKeeper nodes, etcd cluster). | Limited to 7 per AZ. Not for large-scale deployments. |
| **Partition** | Instances grouped into **partitions** (logical groups), each partition on a separate rack. Instances within a partition share a rack, but different partitions don't. | Up to 7 partitions per AZ | Large distributed workloads (Hadoop, Kafka, Cassandra). Need some locality (within partition) but also fault isolation (between partitions). A rack failure affects only one partition. | More complex to manage. Application must be partition-aware. |

**Summary:** Cluster = performance. Spread = maximum fault isolation. Partition = balance of both for large distributed systems.

---
---

## PRACTICE PAPER 4 — Deployment Models + Security

---

### Q1. [10 Marks] — Scenario-Based (Government Cloud Adoption)

**(a) A government agency needs to deploy a cloud-based citizen services portal (e.g., tax filing, license renewals). Discuss the suitability of Public, Private, and Community cloud deployment models for this use case. Address data sovereignty concerns. [6 marks]**

**Model Answer:**

**Analysis of Each Deployment Model:**

**1. Public Cloud (AWS / Azure / GCP):**

| Aspect | Assessment |
|---|---|
| **Pros** | Scalable (tax filing season = 10x traffic), cost-effective (pay-per-use, no CapEx), global edge locations for CDN, mature security certifications (SOC 2, ISO 27001). |
| **Cons** | Data sovereignty risk — citizen data (Aadhaar, PAN, tax records) stored on shared infrastructure. Provider (usually US-based) may be subject to foreign laws (US CLOUD Act). Multi-tenant nature raises concerns about data co-mingling. |
| **Data Sovereignty** | Mitigated by choosing India-region (AWS Mumbai, Azure Central India). But physical infrastructure is still owned/operated by a foreign company. May not satisfy government audit requirements. |
| **Verdict** | Suitable for non-sensitive, public-facing components (static website, information portal). NOT suitable for sensitive citizen data processing/storage without additional controls. |

**2. Private Cloud (Government-Owned Data Center):**

| Aspect | Assessment |
|---|---|
| **Pros** | Full control over data residency — data never leaves government-owned infrastructure. Complete audit trail. Meets strictest compliance requirements. Can implement custom security policies. |
| **Cons** | Expensive — ₹10-50 crore CapEx for data center. Slow to provision (months). Requires in-house IT talent for maintenance, patching, security. Limited scalability for traffic spikes (tax filing deadline). |
| **Data Sovereignty** | Fully addressed — data stays on government soil, managed by government personnel. |
| **Verdict** | Suitable for core backend systems storing citizen PII. But cannot handle elastic demand alone. |

**3. Community Cloud (e.g., India's MeghRaj / GI Cloud):**

| Aspect | Assessment |
|---|---|
| **Pros** | Shared by government agencies with common compliance, security, and sovereignty requirements. Cost shared across agencies. Purpose-built for government workloads. Operated under government policy frameworks. India's NIC (National Informatics Centre) operates GI Cloud specifically for this. |
| **Cons** | Limited service catalog compared to public cloud. May lack advanced services (ML, serverless). Capacity constraints if many agencies scale simultaneously. |
| **Data Sovereignty** | Fully addressed — infrastructure owned and operated by the government or government-controlled entities within India. |
| **Verdict** | **Best fit** for citizen services. Purpose-built for government compliance + cost sharing + data sovereignty. |

**Recommended Architecture: Community Cloud (primary) + Private Cloud (sensitive data):**

- **Community Cloud (GI Cloud / MeghRaj):** Host the citizen portal, APIs, authentication, and standard services. Shared across multiple government departments.
- **Private Cloud (government data center):** Store the most sensitive citizen data (biometric, financial records). Connected to community cloud via secure dedicated links.
- **Public Cloud (limited use):** Only for non-sensitive, high-scale components — CDN for static content delivery, DDoS protection (AWS Shield/CloudFlare).

**(b) The government agency is concerned about vendor lock-in. Explain what vendor lock-in means and recommend strategies to mitigate it. [4 marks]**

**Model Answer:**

**What is Vendor Lock-in?**
Vendor lock-in occurs when a customer becomes dependent on a single cloud provider's proprietary services, APIs, data formats, or tools, making it technically difficult and financially expensive to migrate to another provider or back to on-premises. The more provider-specific services you use, the deeper the lock-in.

**Examples of Lock-in:**
- Application built using AWS Lambda (serverless) — no direct equivalent migration path to Azure Functions without code rewrite.
- Data stored in DynamoDB (AWS proprietary NoSQL) — can't migrate to Azure Cosmos DB without data transformation.
- Infrastructure defined using AWS CloudFormation templates — useless on GCP.

**Mitigation Strategies:**

| Strategy | How It Helps | Implementation |
|---|---|---|
| **Use open standards** | Avoid proprietary APIs. Use Kubernetes (not ECS), PostgreSQL (not Aurora), Docker (not AWS-specific containers). | Deploy on EKS/GKE/AKS — Kubernetes manifests work on any cloud. Use standard SQL databases. |
| **Multi-cloud architecture** | Spread workloads across 2+ providers. Critical services run on both. | Use Terraform (cloud-agnostic IaC) instead of CloudFormation. Abstract provider-specific services behind interfaces. |
| **Containerization** | Docker containers run identically on any cloud. Application logic is cloud-independent. | Package all services as Docker images. Use Kubernetes for orchestration. |
| **Data portability** | Store data in standard formats. Ensure regular exports. Avoid provider-specific data stores. | Use S3-compatible APIs (MinIO runs on-prem), standard database exports (pg_dump), avoid proprietary ETL. |
| **Abstraction layer** | Build application logic on top of an abstraction layer that maps to provider-specific APIs. | Use cloud-agnostic libraries (e.g., Apache libcloud for compute, Pulumi for IaC). |
| **Exit strategy** | Document migration procedures. Estimate migration cost/timeline. Test migration periodically. | Annual DR drill: migrate a non-critical workload to the alternative provider. Validate data export completeness. |

**For the government agency:** Mandate open standards (Kubernetes, PostgreSQL, standard APIs) in procurement requirements. This ensures that if the community cloud provider changes or a better option emerges, migration is feasible within months rather than years.

---

### Q2. [10 Marks] — Scenario-Based (IAM for Financial Services)

**(a) A bank is deploying an AI-based fraud detection system on AWS. Design IAM policies following the principle of least privilege. Define roles for: (i) EC2 instances running the ML model, (ii) Lambda functions processing transactions, (iii) Data scientists accessing training data. [6 marks]**

**Model Answer:**

**Principle of Least Privilege:** Every identity (user, service, application) should have ONLY the minimum permissions required to perform its specific task — nothing more. This limits the blast radius if any credential is compromised.

**Role (i): EC2 ML Model Role (`fraud-model-ec2-role`)**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadTrainingData",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::bank-ml-training-data",
        "arn:aws:s3:::bank-ml-training-data/*"
      ]
    },
    {
      "Sid": "WritePredictions",
      "Effect": "Allow",
      "Action": ["s3:PutObject"],
      "Resource": "arn:aws:s3:::bank-fraud-predictions/*"
    },
    {
      "Sid": "ReadModelArtifacts",
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::bank-ml-models/*"
    },
    {
      "Sid": "PublishMetrics",
      "Effect": "Allow",
      "Action": ["cloudwatch:PutMetricData"],
      "Resource": "*",
      "Condition": { "StringEquals": { "cloudwatch:namespace": "FraudDetection" } }
    }
  ]
}
```

**Justification:** EC2 instances running the model can READ training data and model files, WRITE predictions, and publish CloudWatch metrics. They CANNOT delete data, access other buckets, launch instances, or modify IAM.

**Role (ii): Lambda Transaction Processing Role (`fraud-lambda-role`)**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadTransactionQueue",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage"],
      "Resource": "arn:aws:sqs:ap-south-1:123456:transaction-queue"
    },
    {
      "Sid": "InvokeModel",
      "Effect": "Allow",
      "Action": ["sagemaker:InvokeEndpoint"],
      "Resource": "arn:aws:sagemaker:ap-south-1:123456:endpoint/fraud-model-endpoint"
    },
    {
      "Sid": "WriteFraudAlerts",
      "Effect": "Allow",
      "Action": ["sns:Publish"],
      "Resource": "arn:aws:sns:ap-south-1:123456:fraud-alerts"
    },
    {
      "Sid": "WriteAuditLog",
      "Effect": "Allow",
      "Action": ["dynamodb:PutItem"],
      "Resource": "arn:aws:dynamodb:ap-south-1:123456:table/fraud-audit-log"
    }
  ]
}
```

**Justification:** Lambda reads from SQS (transaction queue), calls the ML model endpoint for prediction, publishes fraud alerts to SNS, and writes audit logs to DynamoDB. It CANNOT read/modify training data, access other databases, or change infrastructure.

**Role (iii): Data Scientist Role (`fraud-data-scientist-role`)**

- Attached to IAM users (human data scientists), not services.
- Requires MFA for all actions (condition key `aws:MultiFactorAuthPresent`).
- Read-only access to training data. Write access to experiment buckets. No access to production data.
- Can launch SageMaker notebooks but NOT EC2 instances directly.
- No access to production fraud predictions or customer transaction data.

**(b) Explain IAM best practices for the bank. Why should the bank use Roles instead of Access Keys for EC2 and Lambda? [4 marks]**

**Model Answer:**

**IAM Best Practices for Banking:**

| Best Practice | Why It Matters for a Bank |
|---|---|
| **Enable MFA on ALL accounts** | Banking data is high-value target. Password alone is insufficient. Hardware MFA tokens for root and admin accounts. Virtual MFA for developers. |
| **Never use root account for daily operations** | Root has unrestricted access. If compromised, attacker owns everything. Create IAM admin users instead. Root only for initial setup and billing. |
| **Principle of least privilege** | Fraud detection system should NOT access payroll data. Each service gets only the permissions it needs. Regularly audit and revoke unused permissions. |
| **Enable CloudTrail** | Log every API call across all AWS services. Required for regulatory compliance (RBI, PCI-DSS). Detect unauthorized access attempts. Retain logs for 7+ years. |
| **Rotate credentials regularly** | Access keys older than 90 days should be rotated. Automated rotation using AWS Secrets Manager. |
| **Use IAM Access Analyzer** | Continuously monitors resource policies. Alerts when a resource (S3 bucket, IAM role) is shared outside the AWS account — critical for preventing data leaks. |

**Why Roles Instead of Access Keys?**

| Aspect | Access Keys | IAM Roles |
|---|---|---|
| **Credential type** | Long-lived (static key ID + secret key). Valid until manually rotated. | Short-lived (temporary credentials via STS). Auto-expire in 1-12 hours. |
| **Storage risk** | Must be stored somewhere — environment variables, config files, code. Risk of accidental exposure (committed to Git, logged, shared). | No credentials to store. EC2/Lambda automatically receives temporary credentials from metadata service. |
| **Rotation** | Manual — someone must rotate keys and update all services using them. | Automatic — credentials auto-rotate. No human intervention. |
| **Blast radius if leaked** | Attacker has permanent access until key is discovered and revoked (could be days/weeks). | Attacker has access only until credential expires (1-12 hours). Much smaller window. |
| **Auditability** | Harder to trace which instance used the key. | Each role assumption is logged with instance ID, timestamp, and actions. |

**For the bank:** Using access keys for EC2 instances is a security anti-pattern. If an access key is accidentally committed to Git or logged, an attacker gets permanent access to S3 training data and ML models. With IAM Roles, the EC2 instance gets temporary credentials that expire automatically — even if exposed, the window of vulnerability is hours, not months.

---

### Q3. [10 Marks] — Direct Concepts (NIST + Deployment Models)

**(a) Define the 5 NIST essential characteristics of cloud computing. Give a real-world example for each. [5 marks]**

**Model Answer:**

| # | Characteristic | Definition | Real-World Example |
|---|---|---|---|
| 1 | **On-demand self-service** | Users can provision computing resources (servers, storage, networks) automatically through a web portal or API, without requiring human interaction with the service provider. | A developer logs into AWS Console at 2 AM and launches 10 EC2 instances. No phone call, no ticket, no waiting for IT approval. Instances are running in 60 seconds. |
| 2 | **Broad network access** | Cloud services are available over the network via standard mechanisms (HTTP, HTTPS, APIs) and accessible from diverse client platforms (laptops, phones, tablets, workstations). | A Salesforce (SaaS) user accesses CRM data from their iPhone on the train, then from their laptop at the office, then from a tablet at a client meeting — same data, any device, any network. |
| 3 | **Resource pooling** | The provider's computing resources (CPU, memory, storage, network) are pooled to serve multiple consumers using a multi-tenant model. Resources are dynamically assigned and reassigned based on demand. The consumer generally has no control or knowledge of the exact physical location. | AWS runs millions of VMs across thousands of physical servers. When you launch an EC2 instance, you don't know (or care) which physical server it runs on. AWS pools and assigns resources dynamically. Your VM may run alongside another company's VM on the same host — fully isolated. |
| 4 | **Rapid elasticity** | Resources can be elastically provisioned and released (scaled out and in) rapidly, in some cases automatically. To the consumer, the available resources appear to be unlimited and can be appropriated in any quantity at any time. | Netflix handles 250 million viewers. During peak (new season release on Friday evening), AWS auto-scaling adds thousands of EC2 instances within minutes. By Tuesday morning, traffic drops, and instances are terminated. Netflix pays only for peak-hour capacity during peak hours. |
| 5 | **Measured service** | Cloud systems automatically control and optimize resource use by leveraging a metering capability. Resource usage is monitored, controlled, reported, and billed transparently (pay-per-use). | AWS bills EC2 per second, S3 per GB stored + per request, data transfer per GB. The monthly bill itemizes exactly: 720 hours of m5.large ($62.21), 500 GB S3 ($11.50), 100 GB data out ($9.00). No waste, no guessing. |

**(b) Compare all 5 deployment models (Public, Private, Hybrid, Community, Multi-Cloud) in a table covering: ownership, cost, scalability, control, security, and best use case. [5 marks]**

**Model Answer:**

| Dimension | Public | Private | Hybrid | Community | Multi-Cloud |
|---|---|---|---|---|---|
| **Ownership** | Third-party provider (AWS, Azure, GCP) | Single organization (self-managed or hosted) | Organization + provider (mix) | Shared by organizations with common concerns | Multiple third-party providers |
| **Cost** | Lowest (pay-per-use, no CapEx, shared infrastructure) | Highest (build/buy data center, staff, maintenance) | Medium (optimize by placing workloads appropriately) | Shared (split costs among community members) | Variable (can optimize by using cheapest provider per service) |
| **Scalability** | Virtually unlimited (provider has massive capacity) | Limited (constrained by owned hardware). Scaling requires procurement. | High (burst to public cloud when private capacity exceeded) | Moderate (limited to community's shared pool) | Very high (aggregate capacity of multiple providers) |
| **Control** | Least (provider manages infrastructure; you manage apps/data) | Most (full control over hardware, network, security policies) | Medium (full control over private component, limited over public) | Shared (governance shared among community members) | Medium (control over app logic; each provider manages their infrastructure) |
| **Security** | Provider-managed. Shared responsibility model. Multi-tenant concerns. | Highest control. Single-tenant. Custom security policies. | Complex — must secure data flow between private and public. VPN/Direct Connect needed. | Good — purpose-built for community's compliance needs. | Complex — must manage security across multiple providers with different models. |
| **Compliance** | Challenging — depends on provider's certifications. May not meet all regulatory needs. | Easiest — full control enables any compliance standard. | Achievable — sensitive data on private, non-sensitive on public. | Built for specific compliance (e.g., government, healthcare). | Challenging — must verify compliance across all providers. |
| **Vendor Lock-in** | High (proprietary services) | None (you own everything) | Medium (public cloud portion has lock-in) | Low-Medium (depends on community platform) | Lowest (can shift between providers) |
| **Best Use Case** | Startups, SaaS apps, variable workloads, dev/test | Banking core systems, classified government data, healthcare PII | E-commerce (sensitive data private, web tier public), gradual cloud migration | Government agencies, healthcare networks, research institutions | Large enterprises avoiding lock-in, global companies needing best-of-breed |

**Key Decision Framework:**
- "I need maximum scalability at minimum cost" → **Public Cloud**
- "I need maximum control and security" → **Private Cloud**
- "I need both — some data is sensitive, some isn't" → **Hybrid Cloud**
- "Multiple organizations share the same compliance needs" → **Community Cloud**
- "I refuse to depend on a single vendor" → **Multi-Cloud**

---
---

## PRACTICE PAPER 5 — Comprehensive (Mixed Topics CS1-CS6)

---

### Q1. [10 Marks] — Scenario-Based (Auto-Scaling Architecture for Flash Sale)

**(a) An e-commerce company is running a flash sale event expecting 10x normal traffic for 4 hours. Design an auto-scaling architecture using EC2, ELB, S3, and CloudFront. Explain how each component handles the traffic spike. [6 marks]**

**Model Answer:**

**Architecture for Flash Sale:**

```
[Users — 10x traffic]
        ↓
[Route 53 — DNS with latency-based routing]
        ↓
[CloudFront — 400+ global edge locations]
  ├── Cache HIT → Serve static content directly (images, CSS, JS, product pages)
  └── Cache MISS ↓
[Application Load Balancer — distributes across AZs]
        ↓                    ↓
   [AZ-1]               [AZ-2]
   EC2 Auto Scaling      EC2 Auto Scaling
   Group (2→20)          Group (2→20)
        ↓                    ↓
   [ElastiCache — Redis cluster for session/cart data]
        ↓
   [RDS Aurora — Multi-AZ with read replicas]
        ↓
   [S3 — Product images, order receipts, static assets]
```

**How Each Component Handles the Spike:**

| Component | Normal Load | During Flash Sale (10x) | How It Scales |
|---|---|---|---|
| **CloudFront (CDN)** | Serves cached content from nearest edge | Absorbs 60-80% of traffic at edge — product images, CSS, JS never reach origin servers | Automatically scales — AWS manages edge capacity. No configuration needed. Reduces origin load by 80%. |
| **ALB (ELB)** | Routes requests to 4 EC2 instances | Routes requests to 20-40 EC2 instances across 2 AZs | ALB auto-scales its internal capacity. Pre-warming available by contacting AWS support before planned events. |
| **EC2 Auto Scaling** | 4 instances (2 per AZ) | Scales to 20-40 instances within 2-5 minutes | **Scheduled scaling:** Pre-warm to 15 instances 30 min before sale starts. **Target tracking:** Scale on CPU > 60% or request count > 1000/instance. **Step scaling:** Add 10 instances if CPU > 80%. |
| **S3** | Stores product images, assets | Handles unlimited concurrent requests (S3 scales automatically) | S3 partitions data across multiple servers. No capacity limit. 5,500 GET requests/second/prefix. Use randomized prefixes for high throughput. |
| **ElastiCache (Redis)** | 1 primary + 1 replica | Cluster mode: add read replicas for read scaling | Redis handles 100,000+ operations/second. Caches product data, session data, cart data. Reduces database load by 90%. |
| **RDS Aurora** | 1 writer + 1 read replica | 1 writer + 5 read replicas (auto-add replicas before sale) | Read replicas handle product catalog queries. Writer handles only orders (writes). Aurora can add read replicas in ~10 minutes. |

**Pre-Sale Preparation (Critical!):**
1. **T-24 hours:** Scale EC2 Auto Scaling group minimum to 10 (pre-warm).
2. **T-12 hours:** Add 3 Aurora read replicas (take time to sync).
3. **T-1 hour:** Contact AWS to pre-warm ALB (if expecting > 50,000 concurrent connections).
4. **T-0 (sale starts):** CloudFront cache is warm from pre-sale browsing. Auto Scaling handles incremental spikes.
5. **T+4 hours (sale ends):** Scheduled scaling reduces to normal capacity over 30 minutes (gradual cool-down, not immediate, to handle post-sale order tracking).

**(b) After the flash sale, the company wants to optimize costs. The EC2 fleet has 4 always-on instances and up to 36 burst instances. Recommend pricing models for each tier. [4 marks]**

**Model Answer:**

**EC2 Pricing Strategy (Three-Tier):**

| Tier | Instance Count | Pricing Model | Savings vs On-Demand | Rationale |
|---|---|---|---|---|
| **Base (always-on)** | 4 instances | **Reserved Instances (1-year, partial upfront)** | ~40% savings | These 4 instances run 24/7/365. Reserved pricing is the most cost-effective for predictable, steady-state workloads. 1-year commitment with partial upfront balances savings vs flexibility. |
| **Predictable burst** | Next 12 instances (for known peak hours) | **Savings Plans (Compute)** | ~30% savings | Daily lunch/dinner peaks are predictable. Savings Plans offer flexibility (any instance type, any region) with committed hourly spend. Better than Reserved for variable instance types. |
| **Flash sale burst** | Up to 24 additional instances (rare, 4-hour events) | **Spot Instances (with On-Demand fallback)** | Up to 90% savings | Flash sale traffic is temporary. Spot Instances at up to 90% discount. Use Spot Fleet with diversified instance types (m5.large, m5.xlarge, m4.large) to minimize interruption risk. If Spot capacity is unavailable, Auto Scaling falls back to On-Demand. |

**Cost Comparison (Monthly Estimate):**

| Strategy | Monthly Cost |
|---|---|
| All On-Demand (worst case) | ~₹3,50,000 |
| Optimized (Reserved + Savings Plan + Spot) | ~₹1,40,000 |
| **Savings** | **~60% cost reduction** |

**Additional Optimizations:**
- Use **Graviton2 (ARM) instances** (m6g instead of m5) — 20% cheaper with 40% better price-performance (Zomato case study validated this).
- Schedule dev/test environments to shut down nights and weekends (saves 65% on those instances).
- Use **S3 Intelligent-Tiering** for assets with unpredictable access patterns.

---

### Q2. [10 Marks] — Scenario-Based (Docker CI/CD Pipeline)

**(a) A company uses Docker for their CI/CD pipeline. Explain Docker image layers, the build process, and the registry workflow. What is a multi-stage build and why is it used? [6 marks]**

**Model Answer:**

**Docker Image Layers:**

A Docker image is built in layers, where each Dockerfile instruction creates a new read-only layer stacked on top of the previous one.

```
Layer 4: COPY . .                   (application code — 5 MB)
Layer 3: RUN pip install -r req.txt (Python packages — 200 MB)
Layer 2: RUN apt-get install -y gcc (system packages — 150 MB)
Layer 1: FROM python:3.11-slim      (base OS + Python — 120 MB)
```

**Key properties:**
- Layers are **immutable and cached**. If Layer 1-2 haven't changed, Docker reuses cached layers and only rebuilds Layer 3-4.
- Layers are **shared** across images. 10 images using `python:3.11-slim` as base store Layer 1 only once on disk.
- When a container runs, a thin **writable layer** is added on top. Changes in the container (new files, modified files) exist only in this writable layer. The image layers remain unchanged.

**Build Process:**

```
Developer writes Dockerfile
        ↓
$ docker build -t myapp:v1.2 .
        ↓
Docker reads Dockerfile line-by-line:
  1. FROM python:3.11-slim → Pull base image (or use cache)
  2. RUN apt-get install   → Execute in temporary container → Commit as new layer
  3. COPY requirements.txt → Copy file → Commit as new layer
  4. RUN pip install        → Execute → Commit as new layer
  5. COPY . .              → Copy app code → Commit as new layer
  6. CMD ["python", "app.py"] → Store as metadata (no new layer)
        ↓
Image created: myapp:v1.2 (475 MB, 4 layers)
```

**Registry Workflow:**

```
Developer machine                Docker Registry (ECR/Docker Hub)
                                 
$ docker build → Image created
$ docker tag myapp:v1.2 registry.example.com/myapp:v1.2
$ docker push → Layers uploaded (only new/changed layers)
                                      ↓
                              Image stored in registry
                                      ↓
CI/CD server or Production:
$ docker pull registry.example.com/myapp:v1.2
                → Layers downloaded (only missing layers)
$ docker run   → Container starts from image
```

**Multi-Stage Build:**

A multi-stage build uses multiple FROM statements in a single Dockerfile. Each FROM starts a new build stage. Only the final stage becomes the output image — intermediate stages are discarded.

```dockerfile
# Stage 1: BUILD (large — has compiler, build tools)
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp          # Compile Go application

# Stage 2: RUN (small — only contains the binary)
FROM alpine:3.18
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

**Why multi-stage?**

| Without Multi-Stage | With Multi-Stage |
|---|---|
| Final image contains: Go compiler (500 MB) + source code (50 MB) + binary (10 MB) = **560 MB** | Final image contains: Alpine OS (5 MB) + binary (10 MB) = **15 MB** |
| Security risk: compiler and build tools in production image (larger attack surface) | Minimal attack surface: only the runtime binary. No compiler, no source code, no build tools. |
| Slower pulls, slower deployments | 37x smaller image = faster pulls, faster deployments |

**(b) The company wants to scan Docker images for vulnerabilities before deploying to production. Recommend a CI/CD pipeline with security checks. [4 marks]**

**Model Answer:**

**Secure CI/CD Pipeline Design:**

```
Developer pushes code to Git
        ↓
[Stage 1: BUILD]
  - docker build -t myapp:v1.2 .
  - Run unit tests inside build container
  - If tests fail → pipeline stops, developer notified
        ↓
[Stage 2: SCAN]
  - Vulnerability scan (Trivy / Snyk / AWS ECR scanning)
  - Checks base image for CVEs (known vulnerabilities)
  - Checks application dependencies for vulnerabilities
  - Policy: CRITICAL/HIGH vulnerabilities → block deployment
  - MEDIUM/LOW → warn but allow (fix in next sprint)
        ↓
[Stage 3: PUSH]
  - docker push to private registry (Amazon ECR)
  - Image tagged with Git commit SHA (traceability)
  - Image signed with Docker Content Trust (authenticity)
        ↓
[Stage 4: DEPLOY to STAGING]
  - kubectl apply -f deployment.yaml (staging cluster)
  - Run integration tests against staging
  - Run performance tests (ensure no regression)
        ↓
[Stage 5: DEPLOY to PRODUCTION]
  - Rolling update via Kubernetes Deployment
  - Health check validation (liveness + readiness probes)
  - If health checks fail → automatic rollback to previous version
  - If successful → old pods terminated gracefully
```

**Security Checks at Each Stage:**

| Stage | Security Check | Tool |
|---|---|---|
| Build | Use minimal base image (alpine/distroless), non-root user, no secrets in image | Dockerfile linting (hadolint) |
| Scan | CVE scanning, license compliance, secrets detection | Trivy, Snyk, AWS ECR native scanning |
| Push | Image signing, private registry only (no Docker Hub for production) | Docker Content Trust, ECR |
| Deploy | Network policies (pod-to-pod restrictions), resource limits, read-only filesystem | Kubernetes NetworkPolicy, PodSecurityPolicy |
| Runtime | Runtime threat detection, anomaly monitoring | Falco, AWS GuardDuty |

**Key Principle:** "Shift left" — catch vulnerabilities as early as possible in the pipeline. Fixing a vulnerability during build costs 10 minutes. Fixing it in production costs hours of downtime.

---

### Q3. [10 Marks] — Direct Concepts (Containers Deep Dive)

**(a) Compare namespaces and cgroups. List all 6 namespace types and explain what each isolates. [5 marks]**

**Model Answer:**

**Namespaces vs Cgroups — Fundamental Comparison:**

| Aspect | Namespaces | Cgroups (Control Groups) |
|---|---|---|
| **Purpose** | **Isolation** — what a container can SEE | **Resource limits** — how much a container can USE |
| **Question answered** | "What is my world?" (each container thinks it's alone) | "How much can I consume?" (prevent any container from hogging resources) |
| **Mechanism** | Wraps global system resources into instance-specific views | Organizes processes into hierarchical groups and sets resource caps |
| **Enforced by** | Linux kernel (each namespace type is a kernel feature) | Linux kernel (cgroup filesystem, typically at /sys/fs/cgroup) |
| **Without it** | All containers would see all processes, all network interfaces, all filesystems | One container could consume all CPU/memory, starving others |

**The 6 Linux Namespace Types:**

| # | Namespace | What It Isolates | Container Effect |
|---|---|---|---|
| 1 | **PID** (Process ID) | Process ID number space | Container sees only its own processes. PID 1 inside container = the main application process. Host PID 1 (systemd) is invisible. Two containers can both have a "PID 1" without conflict. |
| 2 | **NET** (Network) | Network stack — interfaces, IP addresses, ports, routing tables, firewall rules | Each container gets its own IP address (e.g., 172.17.0.2), its own port space (port 80 in container A ≠ port 80 in container B), its own routing table. Containers communicate via Docker bridge network. |
| 3 | **MNT** (Mount) | Filesystem mount points | Each container has its own root filesystem (/) from the Docker image. Container A's /usr is different from Container B's /usr. Host filesystem is invisible unless explicitly mounted (bind mount or volume). |
| 4 | **UTS** (Unix Time-Sharing) | Hostname and domain name | Each container can have its own hostname (`docker run --hostname=web-server`). Container A's hostname "web-1" is independent of Container B's hostname "db-1". |
| 5 | **IPC** (Inter-Process Communication) | System V IPC objects — message queues, semaphores, shared memory | Processes in Container A cannot access Container B's shared memory segments or message queues. Prevents data leakage between containers. |
| 6 | **USER** | User and group IDs | UID 0 (root) inside container can be mapped to UID 10000 on the host (unprivileged). Even if a container process runs as "root," it has no root privileges on the host. Critical for security. |

**Together:** A Docker container = a process group with all 6 namespaces applied + cgroup resource limits. This creates the illusion of an isolated machine using only kernel features — no hypervisor, no separate kernel, no hardware emulation.

**(b) Compare LXC/LXD (system containers) with Docker (application containers). Define "cloud-native" and list its core principles. [5 marks]**

**Model Answer:**

**LXC/LXD vs Docker Comparison:**

| Dimension | LXC / LXD (System Containers) | Docker (Application Containers) |
|---|---|---|
| **Philosophy** | "Lightweight VM" — run a complete OS environment | "One process per container" — run a single application |
| **Init system** | Yes — runs systemd/init (PID 1 = init, manages child processes) | No — PID 1 = your application process (nginx, python, java) |
| **Multi-process** | Yes — can run SSH, cron, syslog, multiple services inside one container | No — designed for single process. Multi-process requires process managers (supervisord). |
| **Lifecycle** | Long-lived — treated like a VM (SSH into it, install packages, configure) | Ephemeral — build from Dockerfile, deploy, destroy and recreate for updates |
| **Image model** | OS templates (Ubuntu, CentOS images from Canonical/LXD image server) | Application images (layered, built from Dockerfile, stored in registries) |
| **Use case** | Replace VMs for workloads that need full OS environment (legacy apps, multi-service systems) | Microservices, CI/CD, cloud-native apps. Each service = separate container. |
| **Orchestration** | LXD manages instances (similar to VM management) | Kubernetes, Docker Swarm (massive ecosystem) |
| **Ecosystem** | Smaller — primarily Linux community | Massive — Docker Hub has 10M+ images, Kubernetes is industry standard |
| **Typical user** | Sysadmins replacing VMs with lighter alternative | Developers building and deploying microservices |

**Key Insight:** LXC/LXD containers feel like VMs (you SSH in, install packages, manage services). Docker containers feel like processes (build image → run → destroy → rebuild from Dockerfile). Docker's disposable, image-based model won the industry for cloud-native applications.

**Cloud-Native Definition:**

Cloud-native is an approach to building and running applications that fully exploits the advantages of cloud computing — elasticity, scalability, resilience, and manageability. Cloud-native applications are designed *for* the cloud, not merely *moved to* the cloud (lift-and-shift).

**Core Principles:**

| # | Principle | Description |
|---|---|---|
| 1 | **Microservices architecture** | Application decomposed into small, independently deployable services. Each service owns its data and logic. Teams can develop, deploy, and scale services independently. |
| 2 | **Containerization** | Each microservice packaged as a Docker container — consistent across dev, test, and production. Portable across any cloud provider. |
| 3 | **Declarative deployment** | Infrastructure and application state defined in YAML/code (Infrastructure as Code). Kubernetes YAML declares desired state; the system ensures reality matches. |
| 4 | **Designed for failure (Resilience)** | Applications assume components WILL fail. Circuit breakers prevent cascading failures. Health checks enable auto-restart. Multiple replicas ensure no single point of failure. |
| 5 | **Observability** | Built-in logging (centralized with ELK/Fluentd), monitoring (Prometheus/Grafana), and distributed tracing (Jaeger/Zipkin). You can't fix what you can't see. |
| 6 | **CI/CD automation** | Automated build → test → deploy pipelines. Code committed to Git triggers automatic deployment to production. Zero manual steps. |
| 7 | **Elasticity and auto-scaling** | Horizontal scaling based on metrics (CPU, requests, queue depth). Scale to zero during idle. Pay only for what you use. |

**Cloud-Native vs Cloud-Enabled:**
- **Cloud-enabled:** Take existing monolith → lift and shift to a VM on AWS. Runs *on* the cloud but doesn't use cloud capabilities.
- **Cloud-native:** Redesign as microservices → containerize → orchestrate with Kubernetes → auto-scale. Built *for* the cloud.

---
---

## QUICK REFERENCE — Topic-to-Paper Mapping

| Topic Area | Past Paper Q | PP1 Q | PP2 Q | PP3 Q | PP4 Q | PP5 Q |
|---|---|---|---|---|---|---|
| Cloud deployment models | Q1a | Q1a | — | — | Q1a, Q3b | — |
| Cloud service models (IaaS/PaaS/SaaS) | Q1a | Q1a | — | — | Q3a | — |
| NIST 5 characteristics | — | — | — | — | Q3a | — |
| Virtualization types (full/para/HW) | Q1b | Q3a, Q3b | — | — | — | — |
| Memory overcommitment | Q2a, Q2b | — | — | — | — | — |
| CPU overcommitment | — | Q2a, Q2b | — | — | — | — |
| VMs vs Containers | Q3a | — | Q2a | — | — | — |
| AWS services (EC2, VPC, S3, RDS, IAM) | Q3b | — | Q1a | Q1b, Q2a, Q3a, Q3b | Q2a | Q1a, Q1b |
| Docker (Dockerfile, layers, lifecycle) | — | — | Q3a | — | — | Q2a |
| Container orchestration (Kubernetes) | — | — | Q2b, Q3b | — | — | — |
| Namespaces + Cgroups | — | — | — | — | — | Q3a |
| LXC vs Docker / Cloud-Native | — | — | — | — | — | Q3b |
| Security (IAM, VPC, SGs, NACLs) | — | — | — | Q2a, Q2b | Q2a, Q2b | Q2b |
| Storage (EBS/EFS/S3/Glacier) | — | — | — | Q1a, Q1b, Q3a | — | — |
| Auto-scaling & cost optimization | — | Q1b | Q1b | — | — | Q1a, Q1b |
| Compliance (HIPAA/DPDP/sovereignty) | — | Q1a | — | — | Q1a, Q1b | — |

---

## EXAM STRATEGY TIPS

1. **Time management:** 2 hours for 3 questions = ~40 minutes per question. Don't spend 60 minutes on Q1 and rush Q3.
2. **Use tables.** Comparison questions BEG for tables. A well-structured table scores higher than paragraphs of text for the same content.
3. **Label sub-parts clearly.** Write **(a)** and **(b)** prominently. Examiners scan for structure.
4. **Scenario questions = Recommend + Justify.** State your recommendation in the first sentence. Then justify with specifics. "I recommend Hybrid Cloud because..." not three paragraphs of theory before the answer.
5. **Draw ASCII diagrams.** For architecture questions, a simple diagram (VPC layout, Docker workflow, Auto Scaling setup) adds visual clarity and scores marks.
6. **Mention numbers.** "99.999999999% durability," "17 sensitive-but-not-privileged instructions," "CPU Ready > 5% indicates overcommitment." Specific numbers show mastery.
7. **Cover marks proportionally.** 6-mark question = 6 distinct points or a comprehensive table. 4-mark question = 4 key points. Don't write the same amount for both.

---

*End of Past Papers & Practice Questions. Good luck!*
