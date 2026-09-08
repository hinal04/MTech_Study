# CS4: Hypervisors and Introduction to IaaS — Questions & Answers

> 8 questions covering: Hypervisor deep-dive (Type 1/2, KVM, ESXi, Nitro), IaaS definition and characteristics, IaaS architecture, AWS as reference platform, Regions/AZs/Edge, VM lifecycle, VM migration (hot/cold/live storage), cloud provisioning platforms.

---

### Q1. Compare Type 1 and Type 2 hypervisors. Name 4 production Type 1 hypervisors and their users.

**Answer:**

| Aspect | Type 1 (Bare-metal) | Type 2 (Hosted) |
|---|---|---|
| Runs on | Directly on hardware | On top of host OS |
| Performance | Better (direct access) | Worse (host OS overhead) |
| Security | More secure (smaller surface) | Less secure |
| Use case | Production cloud | Development/testing |

**4 Type 1 hypervisors:**

| Hypervisor | Developer | Used by |
|---|---|---|
| **KVM** | Open-source (Linux) | AWS (Nitro), Google Cloud, DigitalOcean |
| **VMware ESXi** | VMware/Broadcom | Enterprise private clouds |
| **Microsoft Hyper-V** | Microsoft | Azure, Windows enterprise |
| **Xen** | Linux Foundation | Citrix, early AWS |

---

### Q2. What is the AWS Nitro System? Why is it significant?

**Answer:**

**Nitro** is AWS's custom hypervisor architecture that evolved from Xen:

- **Nitro Hypervisor:** Lightweight KVM-based. Offloads networking, storage, and security to dedicated hardware cards.
- **Nitro Cards:** Custom ASICs handling VPC networking, EBS storage I/O, and monitoring — removing this from the host CPU.
- **Nitro Security Chip:** Hardware-based security preventing unauthorized access to instance memory/storage.

**Significance:** Near-bare-metal performance because hypervisor overhead is offloaded to hardware. The CPU is almost entirely available to the customer's workload instead of being consumed by virtualization overhead.

---

### Q3. Define IaaS. Compare public and private IaaS.

**Answer:**

**IaaS** delivers computing infrastructure (servers, storage, networking) as on-demand, pay-per-use services over the internet. The provider manages physical infrastructure; the customer manages OS upward.

| Aspect | Public IaaS | Private IaaS |
|---|---|---|
| **Provider** | Third-party (AWS, Azure, GCP) | Organisation's own IT or dedicated third party |
| **Users** | Anyone with a credit card | Internal users and partners |
| **Infrastructure** | Shared across thousands of customers | Dedicated to one organisation |
| **Examples** | AWS EC2, Azure VMs, GCE | OpenStack, VMware vSphere, AWS Outposts |

---

### Q4. Explain the IaaS architecture stack. What does the customer manage vs the provider?

**Answer:**

```
Customer manages:  Applications, Data, Runtime, Middleware, OS
Provider manages:  Virtualization, Servers, Storage, Network, Data Centre
```

**IaaS stack layers:**

| Layer | What it includes |
|---|---|
| **Customer-managed** | Applications, data, runtime, middleware, OS (install, patch, configure) |
| **Virtualization** | VMs, containers, virtual networks, virtual disks |
| **Physical infrastructure** | Compute servers, SAN/SSD storage, switches/routers, data centres |
| **Management & orchestration** | Provisioning APIs, monitoring, billing, IAM, auto-scaling, load balancing |

---

### Q5. Explain Regions, Availability Zones, and Edge Locations. Why deploy across multiple AZs?

**Answer:**

| Concept | What it is | Purpose |
|---|---|---|
| **Region** | Geographic area with multiple AZs. Completely independent. | Data sovereignty (keep data in specific country). Low latency (deploy near users). |
| **Availability Zone** | 1+ data centres within a region with independent power/cooling/network. | **High availability** — if one AZ fails, others keep running. |
| **Edge Location** | Small data centres worldwide for CDN caching. | Low-latency content delivery to end users. |

**Why multi-AZ:** Single-AZ = single point of failure. If that AZ has a power outage, your app is down. Multi-AZ = load balancer routes traffic to healthy AZs. AWS SLAs require multi-AZ for production workloads.

---

### Q6. Describe the VM lifecycle stages and the VM provisioning process.

**Answer:**

**VM lifecycle:**
1. **IT Service Request** → Analyse resource pool, match requirements.
2. **VM Provisioning** → Load OS + apps, customise/configure, start server.
3. **VM in Operation** → Serve requests, support migration, scale on demand.
4. **Release VM** → End of service, resources reallocated.

**Provisioning process:**
1. Select server from pool with appropriate OS template.
2. Load software (drivers, middleware).
3. Customise (IP address, network, storage).
4. Server ready to serve requests.

In cloud (EC2): takes **minutes**. In traditional IT: takes **weeks**.

---

### Q7. Compare hot migration, cold migration, and live storage migration.

**Answer:**

| Technique | VM state | What moves | Shared storage? | Downtime |
|---|---|---|---|---|
| **Hot/Live** | Running | VM from host A to host B while powered on | Yes | Milliseconds |
| **Cold/Regular** | Powered off | VM files from host A to host B | No | Minutes |
| **Live Storage** | Running | VM disks from one datastore to another | No | None |

**Live migration works because:** Most memory is copied during pre-copy (while VM runs). Only the last few dirty pages require stop-and-copy — resulting in milliseconds of actual downtime.

---

### Q8. Explain the Xen live migration algorithm (6 stages).

**Answer:**

| Stage | What happens |
|---|---|
| **0: Pre-Migration** | Active VM on Host A. |
| **1: Reservation** | Migration request sent. Resources reserved on Host B. |
| **2: Iterative Pre-Copy** | All memory pages copied A→B. Subsequent iterations copy only dirtied pages. Each iteration copies fewer pages. |
| **3: Stop-and-Copy** | VM suspended on A. Remaining dirty pages + CPU state copied to B. Network traffic routed to B. |
| **4: Commitment** | B confirms complete image received. A discards original VM. B becomes primary. |
| **5: Activation** | VM on B activated. Device drivers reattached. IP addresses advertised on new network. |

**Key insight:** Stage 2 (pre-copy) does most of the work while the VM is still running. The actual downtime (stage 3) is only milliseconds.

---

*End of CS4 Questions & Answers*
