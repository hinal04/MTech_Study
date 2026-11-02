# Cloud Computing — Exam-Style Questions (BITS Pilani Pattern)

> **Based on:** BITS Pilani CSI ZG527 past exam papers (EC-2R Mid-Semester, EC-3R Comprehensive, 2025-2026)
>
> **Exam Pattern Observed:**
> - **Mid-Semester (EC-2):** Closed book, 2 hours, 30% weightage. Mix of short-answer, comparison, and scenario-based questions.
> - **Comprehensive (EC-3):** Open book, 2.5 hours, 40% weightage. Longer analytical questions, design scenarios, and comparisons.
> - Topics 1-2 are heavily tested in mid-semester.
>
> **Source:** Questions are rephrased and modelled on the patterns observed in the actual papers. Content was rephrased for compliance with licensing restrictions.

---
---

## SECTION A — Short Answer (3-5 marks each)

---

### A1. Define cloud computing as per NIST. List its five essential characteristics. [5 marks]

**Answer:**

NIST defines cloud computing as a model enabling ubiquitous, on-demand network access to a shared pool of configurable computing resources that can be rapidly provisioned and released with minimal management effort.

**Five essential characteristics:**
1. **On-demand self-service** — Provision resources automatically without provider interaction.
2. **Broad network access** — Accessible over the network via standard protocols from diverse devices.
3. **Resource pooling** — Provider resources serve multiple tenants via multi-tenant model; location transparent.
4. **Rapid elasticity** — Scale outward/inward rapidly, appearing unlimited to the consumer.
5. **Measured service** — Resource usage metered, monitored, reported — pay-per-use transparency.

---

### A2. Distinguish between the three cloud service models with respect to user control and provider responsibility. [5 marks]

**Answer:**

| Aspect | IaaS | PaaS | SaaS |
|---|---|---|---|
| **User controls** | OS, middleware, runtime, apps, data | Apps, data only | Data, user settings only |
| **Provider controls** | Hardware, hypervisor, network | Hardware + OS + middleware + runtime | Everything |
| **Flexibility** | Maximum — install anything | Medium — deploy code only | Minimum — use as-is |
| **Example** | AWS EC2, Azure VMs | Heroku, Google App Engine | Gmail, Salesforce, Zoom |
| **Analogy** | Empty apartment (bring furniture) | Furnished apartment (bring belongings) | Hotel room (everything provided) |

The stack progresses from maximum control (IaaS) to minimum control (SaaS), with the provider absorbing increasing responsibility at each level.

---

### A3. What is a hypervisor? Compare Type 1 and Type 2 hypervisors with examples. [4 marks]

**Answer:**

A **hypervisor** (Virtual Machine Monitor) is software that creates and manages virtual machines by sitting between physical hardware and guest operating systems, mediating all hardware access.

| Aspect | Type 1 (Bare-metal) | Type 2 (Hosted) |
|---|---|---|
| **Runs on** | Directly on hardware (IS the OS) | On top of existing host OS (as an app) |
| **Performance** | Better — direct hardware access | Worse — host OS overhead |
| **Security** | More secure — smaller attack surface | Less secure — host OS vulnerabilities |
| **Use case** | Production cloud, data centres | Development, testing on personal machines |
| **Examples** | VMware ESXi, KVM, Xen, Hyper-V | VirtualBox, VMware Workstation, Parallels |

Cloud computing uses Type 1 exclusively for performance and security reasons.

---

### A4. What problem does x86 hardware virtualization (Intel VT-x) solve? Explain briefly. [3 marks]

**Answer:**

**The problem:** x86 processors have four privilege rings (Ring 0-3). Both the hypervisor and guest OS kernel need Ring 0 (highest privilege). Giving the guest Ring 0 lets it bypass the hypervisor. Moving the guest to Ring 1 causes 17 sensitive instructions to silently fail instead of trapping.

**VT-x solution:** Intel added new CPU modes — **VMX root mode** (for hypervisor, below Ring 0) and **VMX non-root mode** (for guest, at Ring 0). When the guest executes a privileged instruction, the CPU hardware automatically traps to the hypervisor (VM exit) — no binary translation needed. The hypervisor handles the instruction and returns control (VM entry).

This provides unmodified guest OS support with near-native performance.

---

### A5. What are namespaces and cgroups? How do they together create a container? [4 marks]

**Answer:**

**Namespaces** provide **isolation** — each container gets its own view of system resources:
- PID namespace: own process IDs
- NET namespace: own IP address, ports
- MNT namespace: own filesystem
- UTS: own hostname
- IPC: own message queues
- USER: own user IDs

**Cgroups** provide **resource limits** — cap how much CPU, memory, disk I/O, network each container can consume.

**Together:** Namespaces answer "what can you see?" (isolation). Cgroups answer "how much can you use?" (limits). A container = namespaces + cgroups applied to a process group. This creates an isolated, resource-limited environment without the overhead of a full VM.

---

### A6. List four benefits and three limitations of virtualization. [3 marks]

**Answer:**

**Benefits:**
1. **Server consolidation** — 10-50 VMs per physical host; utilization from 10% to 60-80%.
2. **Isolation** — Each VM is a complete sandbox; crashes don't propagate.
3. **Hardware independence** — VMs abstracted from physical hardware; enables live migration.
4. **Rapid provisioning** — Create VMs in seconds from templates (vs weeks for physical servers).

**Limitations:**
1. **Performance overhead** — Hypervisor adds 2-10% CPU overhead, more for I/O-intensive workloads.
2. **Resource contention** — Multiple VMs competing for physical resources (noisy neighbour problem).
3. **Storage overhead** — Each VM carries a full OS copy (several GB), even for identical OS across VMs.

---
---

## SECTION B — Comparison & Analysis (5-8 marks each)

---

### B1. Compare virtual machines and containers across at least 8 dimensions. When would you use each? [8 marks]

**Answer:**

| Dimension | Virtual Machines | Containers |
|---|---|---|
| **Virtualizes** | Hardware (CPU, memory, disk) | OS (process space, filesystem, network) |
| **Kernel** | Each VM has its own kernel | All containers share host kernel |
| **Size** | GBs (full OS + app) | MBs (app + libraries only) |
| **Boot time** | Seconds to minutes | Milliseconds to seconds |
| **Resource overhead** | Heavy (full OS per VM) | Light (no separate OS) |
| **Density** | 10-50 per host | 100-1000+ per host |
| **Isolation** | Strong (separate kernels, hypervisor boundary) | Moderate (shared kernel, namespace isolation) |
| **Security** | More secure (hypervisor is smaller attack surface) | Less secure (shared kernel = shared vulnerabilities) |
| **Portability** | Less (large images, hypervisor-specific) | Highly portable (Docker images run anywhere) |
| **Use case** | Legacy apps, different OS, strong isolation | Microservices, CI/CD, cloud-native |

**When to use VMs:** Different OS needed, strong security isolation required, legacy apps, custom kernel.
**When to use containers:** Microservices, CI/CD pipelines, high density, fast startup, cloud-native.
**In practice:** Containers run inside VMs — VMs provide tenant isolation, containers provide app packaging.

---

### B2. Compare grid computing, cluster computing, and cloud computing. What key features distinguish cloud from its predecessors? [6 marks]

**Answer:**

| Aspect | Grid Computing | Cluster Computing | Cloud Computing |
|---|---|---|---|
| **Coupling** | Loosely coupled, heterogeneous, across domains | Tightly coupled, homogeneous, single location | Provider-managed, standard APIs, global |
| **Resource mgmt** | Decentralised (each node autonomous) | Centralised scheduler, single system image | Provider-managed, API-driven |
| **User interface** | Complex, specialised knowledge | Direct network access | Self-service portal, CLI, API |
| **Billing** | Typically free (academic) | Organisation-funded | Pay-per-use, metered |
| **Elasticity** | Limited (pre-allocated) | Fixed (add nodes manually) | Automatic (scale in seconds) |
| **Virtualisation** | Minimal | Minimal | Core technology |

**Key features distinguishing cloud:**
1. **On-demand self-service** — users provision without contacting anyone.
2. **Pay-per-use** — utility billing model.
3. **Virtualisation** — enables multi-tenancy, isolation, rapid provisioning.
4. **Elasticity** — automatic scaling to match demand.
5. **Standard APIs** — programmatic access via REST/CLI, enabling automation.

---

### B3. Compare full virtualization, para-virtualization, and hardware-assisted virtualization. Which is dominant in cloud and why? [6 marks]

**Answer:**

| Aspect | Full Virtualization | Para-Virtualization | Hardware-Assisted |
|---|---|---|---|
| **Technique** | Binary translation | Hypercalls (modified guest kernel) | CPU extensions (VT-x/AMD-V) |
| **Guest OS modified?** | No | Yes (kernel modified) | No |
| **Performance** | Lower (translation overhead) | Good (direct hypercalls) | Near-native (hardware trapping) |
| **Compatibility** | Any OS | Only modified OS | Any OS |
| **Complexity** | High (translator is complex) | Medium (kernel changes) | Low (hardware does the work) |
| **Examples** | Early VMware, QEMU | Xen PV mode | KVM, ESXi, Hyper-V |

**Hardware-assisted dominates** because it combines the best of both worlds: runs unmodified guest OS (like full virtualization) with near-native performance (no binary translation). Since virtually all modern CPUs support VT-x/AMD-V (since ~2006), there's no reason to use older techniques. KVM (the Linux-based hypervisor used by AWS, GCP, and many providers) relies entirely on hardware-assisted virtualization.

---

### B4. Compare the five cloud deployment models. A financial institution needs to run both a customer-facing app and core banking. Which deployment model would you recommend and why? [7 marks]

**Answer:**

| Model | Owner | Tenancy | Cost | Control | Scalability |
|---|---|---|---|---|---|
| **Public** | Provider (AWS/Azure) | Multi-tenant | Lowest (pay-per-use) | Least | Virtually unlimited |
| **Private** | Organisation | Single-tenant | Highest | Most | Limited |
| **Hybrid** | Both | Mixed | Medium | Medium | High (burst to public) |
| **Community** | Shared by community | Community-tenant | Shared | Shared | Moderate |
| **Multi-Cloud** | Multiple providers | Multi per provider | Variable | Medium | Very high |

**Recommendation for financial institution: Hybrid Cloud.**

**Reasoning:**
1. **Core banking** (sensitive financial data, regulatory compliance) runs on **private cloud** — full control over security, data sovereignty, and compliance (RBI/PCI-DSS regulations require data to stay within controlled boundaries).
2. **Customer-facing app** (mobile banking, website) runs on **public cloud** — benefits from elastic scaling (handle peak traffic during salary days), global CDN for low latency, and cost-efficient pay-per-use.
3. **Cloud bursting** — during extreme peaks (festival sales, year-end), the private cloud can burst to public cloud for additional capacity.
4. Data synchronisation between private and public clouds is managed through secure VPN/Direct Connect links.

---
---

## SECTION C — Scenario & Design Questions (8-12 marks each)

---

### C1. An e-commerce startup is building a new platform. They expect traffic to vary from 100 users during quiet periods to 100,000 during flash sales. Compare deploying on (a) traditional on-premises infrastructure, (b) IaaS, (c) PaaS. Which would you recommend and why? [10 marks]

**Answer:**

**(a) On-Premises:**
- Must buy hardware for peak (100,000 users). Cost: ₹50-100 lakhs CapEx for servers, storage, network.
- Hardware sits 99% idle during quiet periods (100 users on 100,000-user capacity).
- 2-3 month procurement cycle. Can't respond to unexpected traffic spikes.
- Need full IT team (sysadmins, DBAs, network engineers, security).
- **Verdict:** Wasteful and inflexible for a startup.

**(b) IaaS (e.g. AWS EC2):**
- No upfront cost. Provision VMs on demand.
- Auto-scaling group: 2 VMs during quiet → 200 VMs during flash sale → back to 2 after.
- Pay only for actual usage. Estimated cost: ₹1-5 lakhs/month during quiet, ₹10-20 lakhs during flash sale week.
- Still need to manage OS, patching, security, load balancers, deployment.
- **Verdict:** Cost-effective and elastic, but requires DevOps expertise.

**(c) PaaS (e.g. Heroku, AWS Elastic Beanstalk):**
- No server management at all. Deploy code, platform handles scaling.
- Auto-scales automatically based on request rate.
- Built-in load balancing, SSL, monitoring.
- Less control — can't customise OS or install custom software.
- Potential vendor lock-in if using provider-specific APIs.
- **Verdict:** Fastest time-to-market, lowest operational burden.

**Recommendation: PaaS for the web application tier + IaaS for the database and custom components.**
- Use PaaS for the web/API layer (auto-scaling, zero server management, fast deployment).
- Use managed database (DBaaS like RDS) for data (auto-backups, failover).
- Use IaaS only for components that need custom configuration (e.g. ML model serving).
- Total monthly cost: ₹2-8 lakhs (vs ₹50-100 lakhs CapEx for on-prem).

---

### C2. A company runs 50 monolithic applications on physical servers. They want to migrate to the cloud. Explain a migration strategy using VMs first, then containers. What are the benefits of each stage? [10 marks]

**Answer:**

**Stage 1: Lift-and-Shift to VMs (IaaS)**

Convert each physical server to a virtual machine image and run on cloud IaaS (e.g. AWS EC2).

- **How:** Use tools like AWS VM Import, Azure Migrate, or VMware HCX to convert physical servers to VM images. Deploy on cloud with equivalent VM sizes.
- **Benefits:**
  - Fastest migration path — no application changes needed.
  - Immediate benefits: no hardware to maintain, pay-per-use, disaster recovery.
  - Familiar operations — same OS, same admin tools.
- **Limitations:**
  - Still running monoliths — no cloud-native benefits (auto-scaling is coarse, whole VM scales).
  - Still paying for full OS overhead per VM.
  - Vendor lock-in is minimal (VMs are portable).

**Stage 2: Containerize and Decompose (Cloud-Native)**

Gradually refactor monoliths into microservices, containerize with Docker, orchestrate with Kubernetes.

- **How:** For each application:
  1. Containerize the monolith first (put the whole app in a Docker container).
  2. Extract microservices one by one (auth service, payment service, etc.).
  3. Deploy on Kubernetes (EKS/GKE/AKS).
- **Benefits:**
  - Fine-grained scaling — scale individual services, not entire apps.
  - Higher density — 10x more containers per VM than VMs per physical host.
  - Faster deployments — container builds in seconds, rolling updates without downtime.
  - Portability — Kubernetes runs on any cloud provider.
  - CI/CD integration — automated testing and deployment pipelines.
- **Limitations:**
  - Requires significant refactoring effort.
  - Distributed systems complexity (service discovery, circuit breakers, distributed tracing).
  - Team needs new skills (Docker, Kubernetes, microservices patterns).

**Timeline:** Stage 1 = 3-6 months (quick wins). Stage 2 = 12-24 months (gradual refactoring, application by application).

---

### C3. Explain how Docker uses namespaces and cgroups to create isolated containers. Illustrate with an example of two containers running on the same host. [8 marks]

**Answer:**

**Setup:** Host running Linux with Docker. Two containers:
- Container A: Nginx web server (port 80)
- Container B: PostgreSQL database (port 5432)

**Namespace isolation (what each container sees):**

| Namespace | Container A sees | Container B sees | Host sees |
|---|---|---|---|
| **PID** | PID 1 = nginx process, PID 2 = worker | PID 1 = postgres process, PID 2 = checkpointer | PID 1 = systemd, PID 1234 = nginx, PID 1235 = postgres |
| **NET** | IP 172.17.0.2, port 80 open | IP 172.17.0.3, port 5432 open | All IPs, all ports, full routing table |
| **MNT** | /usr/share/nginx/html (nginx files) | /var/lib/postgresql/data (DB files) | Full host filesystem |
| **UTS** | hostname = "web-server" | hostname = "db-server" | hostname = "host-machine" |

Container A cannot see Container B's processes, files, or network. They are completely isolated despite running on the same host.

**Cgroup resource limits:**

```
Container A (nginx):    CPU: max 1 core, Memory: max 256 MB
Container B (postgres): CPU: max 2 cores, Memory: max 1 GB
```

If Container A tries to use more than 256 MB RAM, the kernel kills it (OOM). If it tries to use more than 1 CPU core, the scheduler throttles it. Container B gets more resources because databases need more compute.

**Result:** Two completely isolated, resource-limited environments running on one Linux kernel — with none of the overhead of running two separate VMs with two separate kernels.

---

### C4. A Kubernetes cluster has 3 worker nodes. You deploy a web application with `replicas: 3`. One node crashes. Describe step by step what Kubernetes does. [6 marks]

**Answer:**

**Initial state:** 3 nodes, 3 pods (one per node).

```
Node 1: Pod A (web-app)  ← running
Node 2: Pod B (web-app)  ← running
Node 3: Pod C (web-app)  ← running
Service: load-balances across all 3 pods
```

**Node 2 crashes. Step-by-step recovery:**

1. **Detection (seconds):** Node 2 stops sending heartbeats to the control plane. The kubelet on Node 2 is unreachable. After a timeout (default ~40 seconds), the control plane marks Node 2 as `NotReady`.

2. **Pod eviction (~5 minutes):** After the `pod-eviction-timeout` (default 5 minutes), the controller manager evicts all pods on Node 2. Pod B is marked as `Terminated`.

3. **Desired state check:** The Deployment controller detects that only 2 pods exist but the desired state says 3. A new pod must be created.

4. **Scheduling:** The scheduler finds a suitable node for the new pod. Node 1 and Node 3 have capacity. Scheduler assigns new Pod D to Node 1 (or Node 3, based on resource availability).

5. **Pod creation:** kubelet on the assigned node pulls the container image (if not cached), creates the container, and starts it.

6. **Service update:** The Service's endpoint list is updated — Pod B is removed, Pod D is added. Traffic is now load-balanced across Pod A, Pod C, and Pod D.

**Final state:**
```
Node 1: Pod A + Pod D (web-app)  ← running
Node 2: DOWN
Node 3: Pod C (web-app)          ← running
Service: load-balances across Pod A, Pod C, Pod D
```

**Key insight:** The application remained available throughout — the Service continued directing traffic to the 2 healthy pods while the 3rd was being replaced. Users experienced no downtime (assuming the remaining 2 pods could handle the load).

---
---

## SECTION D — MCQ / True-False with Justification (1-2 marks each)

---

### D1. Which of the following is NOT an essential characteristic of cloud computing per NIST?

(a) On-demand self-service
(b) Broad network access
(c) Guaranteed 99.999% uptime
(d) Resource pooling
(e) Measured service

**Answer: (c)** — NIST does not define any specific uptime guarantee. High availability is a common cloud benefit but is governed by SLAs, not by the NIST definition. The five essential characteristics are: on-demand self-service, broad network access, resource pooling, rapid elasticity, and measured service.

---

### D2. True or False: In para-virtualization, the guest OS runs completely unmodified.

**Answer: False.** In para-virtualization, the guest OS kernel is **modified** to make hypercalls directly to the hypervisor instead of executing privileged instructions. This is the key difference from full virtualization (which runs unmodified guest OS via binary translation) and hardware-assisted virtualization (which runs unmodified guest OS via CPU extensions).

---

### D3. Which deployment model would best suit multiple government agencies needing to comply with the same regulatory framework?

(a) Public cloud
(b) Private cloud
(c) Hybrid cloud
(d) Community cloud

**Answer: (d) Community cloud.** A community cloud is provisioned for exclusive use by a specific community of organisations with shared concerns (same mission, security requirements, policy, compliance). Government agencies sharing the same regulatory framework (e.g. FedRAMP) would benefit from shared infrastructure costs while maintaining compliance.

---

### D4. True or False: Containers provide stronger isolation than VMs because they use namespaces.

**Answer: False.** VMs provide **stronger** isolation than containers. VMs run separate kernels with a hypervisor boundary — a vulnerability in one VM's kernel doesn't affect others. Containers share the host kernel — a kernel vulnerability affects ALL containers. Namespaces provide process-level isolation but not kernel-level isolation. This is why in multi-tenant cloud environments, VMs are used for tenant isolation and containers run inside VMs.

---

### D5. Which Linux kernel feature limits how much CPU and memory a container can use?

(a) Namespaces
(b) Cgroups
(c) SELinux
(d) iptables

**Answer: (b) Cgroups.** Namespaces provide isolation (what you can see). Cgroups (control groups) provide resource limits (how much you can use). SELinux provides mandatory access control. iptables provides network packet filtering.

---

### D6. A Docker image has 4 layers. If you change only the top layer (application code), how many layers need to be rebuilt?

(a) All 4
(b) Only the top 1
(c) The top 2
(d) None — images are immutable

**Answer: (b) Only the top 1.** Docker uses a layered filesystem where each layer is cached independently. When you change a Dockerfile instruction, only that layer and layers above it are rebuilt. Lower layers (base OS, dependencies) are cached and reused. This is why Dockerfiles are structured with infrequently changing layers at the bottom (base image, system packages) and frequently changing layers at the top (application code).

---

### D7. In the IaaS shared responsibility model, who is responsible for patching the guest OS?

(a) Cloud provider
(b) Customer
(c) Shared equally
(d) Neither — it's automated

**Answer: (b) Customer.** In IaaS, the customer manages everything from the OS upward (OS, middleware, runtime, applications, data). The provider manages everything below (physical hardware, hypervisor, network, storage hardware). OS patching, security configuration, and firewall rules are the customer's responsibility.

---

### D8. True or False: Kubernetes can automatically restart a crashed pod and reschedule it to another node if the original node fails.

**Answer: True.** This is Kubernetes' **self-healing** capability. If a pod crashes, the kubelet on the same node restarts it. If a node fails entirely, the Deployment controller detects fewer running pods than desired and the scheduler places new pods on healthy nodes. This happens automatically without human intervention.

---

### D9. Which of the following best describes the "cloud bursting" pattern?

(a) Data stored in the cloud is accessed in bursts
(b) Applications run on private cloud and overflow to public cloud during demand spikes
(c) Cloud resources burst into on-premises infrastructure
(d) A DDoS attack that overwhelms cloud resources

**Answer: (b).** Cloud bursting is a hybrid cloud pattern where an application normally runs on private cloud infrastructure. When demand exceeds the private cloud's capacity, the excess workload "bursts" into the public cloud. When demand subsides, the public cloud resources are released.

---

### D10. What is the primary motivation for the CapEx to OpEx shift in cloud computing?

(a) Cloud is always cheaper than on-premises
(b) Eliminates the need to predict future capacity — pay only for actual usage
(c) Cloud providers don't charge for compute
(d) OpEx is tax-deductible while CapEx is not

**Answer: (b).** The CapEx model requires predicting needs years in advance and buying hardware upfront. Over-provision = wasted money. Under-provision = can't serve demand. The OpEx model (cloud) eliminates this guessing — you consume exactly what you need, when you need it. Cloud is NOT always cheaper (choice a is incorrect); it depends on the workload pattern.

---
---

## Exam Preparation Tips (BITS Pilani CSI ZG527 Pattern)

1. **NIST definition and 5 characteristics** — memorise precisely. Almost always asked directly or as part of another question.
2. **Service model comparison (IaaS/PaaS/SaaS)** — know the responsibility matrix cold. Draw it from memory.
3. **VM vs Container comparison** — the most frequently tested comparison. Know at least 8 dimensions.
4. **x86 virtualization (VT-x)** — understand the ring problem and how VT-x solves it. This is a common 3-5 mark question.
5. **Namespaces + Cgroups** — know what each namespace type isolates and what each cgroup resource limits.
6. **Kubernetes basics** — pod, node, cluster, deployment, service. What happens when a pod/node fails.
7. **Scenario questions** — practice recommending deployment models and service models for given business requirements. Justify your choice.
8. **Docker commands** — know the lifecycle: build → push → pull → run → stop → rm.
9. **Cloud-native principles** — microservices vs monolith, declarative vs imperative, 12-factor app.
10. **Open book (comprehensive)** — focus on understanding concepts deeply rather than memorising. Know WHERE to find things quickly in your notes.

---

*End of Exam-Style Questions*
