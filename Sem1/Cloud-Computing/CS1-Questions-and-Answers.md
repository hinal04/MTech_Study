# Topic 1: Introduction to Cloud Computing — Questions & Answers

> 15 questions covering: NIST definition, 5 essential characteristics, evolution, service models (IaaS/PaaS/SaaS), deployment models, infrastructure overview, benefits/limitations.

---

### Q1. Define cloud computing using the NIST definition. Explain its key elements.

**Answer:**

The NIST (National Institute of Standards and Technology) defines cloud computing as:

*"A model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (e.g., networks, servers, storage, applications, and services) that can be rapidly provisioned and released with minimal management effort or service provider interaction."*

**Key elements:**
- **Ubiquitous, convenient access** — available from anywhere, any device, over the internet.
- **On-demand** — resources provisioned instantly without human intervention from the provider.
- **Shared pool** — resources are pooled and shared among multiple tenants (multi-tenancy).
- **Configurable** — resources can be customised to user needs (instance types, storage sizes, network configs).
- **Rapidly provisioned and released** — scale up in minutes, scale down when done. No weeks-long procurement.
- **Minimal management effort** — self-service portals, APIs, and automation reduce manual work.

The two core concepts behind "cloud" are **abstraction** (hiding implementation details from users) and **virtualization** (pooling and sharing physical resources as virtual resources).

---

### Q2. List and explain the five essential characteristics of cloud computing as defined by NIST.

**Answer:**

| Characteristic | Explanation | Example |
|---|---|---|
| **On-demand self-service** | Users provision resources (VMs, storage) automatically without contacting the provider. | Launch an EC2 instance via AWS console in 60 seconds. |
| **Broad network access** | Resources accessible over the network via standard protocols (HTTP, APIs) from diverse devices. | Access Google Drive from phone, laptop, and tablet. |
| **Resource pooling** | Provider's resources serve multiple tenants via a multi-tenant model. Resources dynamically assigned. Location transparent to users. | AWS runs millions of VMs on shared physical servers. You don't know which rack your VM occupies. |
| **Rapid elasticity** | Resources scale outward/inward rapidly, often automatically, appearing unlimited to the user. | Netflix adds hundreds of servers during evening peak and releases them at 3 AM. |
| **Measured service** | Resource usage is metered, monitored, and reported. Pay-per-use model provides transparency. | AWS bill shows 743 hours of EC2, 50 GB S3 — you pay only for actual consumption. |

---

### Q3. Trace the evolution of cloud computing from the 1960s to today.

**Answer:**

| Era | Paradigm | Key Idea | Why it wasn't enough |
|---|---|---|---|
| **1960s** | Mainframe / Time-sharing | McCarthy proposed "computation as a public utility." Multiple users shared one mainframe. | Expensive, centralised, not scalable. |
| **1970s-80s** | Personal Computing | Decentralised — a computer on every desk. | Isolated machines, no sharing. |
| **1990s** | Client-Server & Internet | Servers in data centres; WWW made info accessible. | Each app needed its own dedicated server. Over-provisioning. |
| **Late 1990s** | Grid Computing | Federate distributed heterogeneous resources across organisations. | Complex setup, no standard APIs, no self-service, no pay-per-use. |
| **Late 1990s** | Cluster Computing | Group of homogeneous tightly-coupled machines in one location. | Not elastic, single location only. |
| **2000s** | Utility Computing | Computing as a metered utility (pay for what you use). | Limited scale, proprietary. |
| **2006+** | Cloud Computing | Virtualisation + utility pricing + self-service + elasticity + broad access. AWS EC2 launched 2006. | Current paradigm — still evolving. |

**Key milestones:** AWS EC2/S3 (2006), Google App Engine (2008), Microsoft Azure (2010), cloud becomes mainstream enterprise IT (~2014), cloud-native/serverless era (2020+).

---

### Q4. Compare grid computing, cluster computing, and cloud computing.

**Answer:**

| Aspect | Grid Computing | Cluster Computing | Cloud Computing |
|---|---|---|---|
| **Coupling** | Loosely coupled, heterogeneous, across administrative domains. | Tightly coupled, homogeneous, single location. | Provider-managed, standard APIs, global. |
| **Resource mgmt** | Decentralised — each node has own manager. | Centralised scheduler, single system image. | Provider-managed, API-driven. |
| **User interface** | Complex, requires specialised knowledge. | Direct network access. | Self-service portal, CLI, API. |
| **Billing** | Typically free (academic/research). | Organisation-funded. | Pay-per-use, metered. |
| **Elasticity** | Limited — pre-allocated resources. | Fixed — add nodes manually. | Automatic — scale in seconds. |
| **Virtualisation** | Minimal. | Minimal. | Core technology. |
| **Location** | Geographically distributed. | Single location. | Globally distributed regions. |

---

### Q5. Explain the three cloud service models (IaaS, PaaS, SaaS) with the responsibility matrix.

**Answer:**

**IaaS (Infrastructure as a Service):** Provider gives you virtualised infrastructure (VMs, storage, network). You manage OS upward. Maximum control, maximum responsibility.
- *Examples:* AWS EC2, Azure VMs, Google Compute Engine.
- *Analogy:* Renting an empty apartment — you bring your own furniture.

**PaaS (Platform as a Service):** Provider manages infrastructure + platform (OS, middleware, runtime). You only manage application code and data. Focus on development, not infrastructure.
- *Examples:* Heroku, Google App Engine, AWS Elastic Beanstalk.
- *Analogy:* Renting a furnished apartment — just bring personal belongings.

**SaaS (Software as a Service):** Provider manages everything — infrastructure, platform, and application. You just use the software via a browser.
- *Examples:* Gmail, Salesforce, Zoom, Dropbox, Microsoft 365.
- *Analogy:* Staying at a hotel — everything is provided.

**Responsibility matrix:**

| Layer | On-Premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Applications | You | You | You | Provider |
| Data | You | You | You | You* |
| Runtime | You | You | Provider | Provider |
| Middleware | You | You | Provider | Provider |
| OS | You | You | Provider | Provider |
| Virtualisation | You | Provider | Provider | Provider |
| Servers/Storage/Network | You | Provider | Provider | Provider |

*In SaaS, the provider stores your data but you own it.

---

### Q6. Compare IaaS, PaaS, and SaaS. When would you choose each?

**Answer:**

| Aspect | IaaS | PaaS | SaaS |
|---|---|---|---|
| **Control** | Maximum (full OS access) | Medium (app code only) | Minimum (use as-is) |
| **Flexibility** | Install anything | Limited to platform capabilities | Fixed functionality |
| **Management burden** | High (patch OS, configure security) | Low (just deploy code) | None |
| **Vendor lock-in risk** | Low (standard VMs) | Medium (provider-specific APIs) | High (data + workflow tied to provider) |
| **Scaling** | Manual or auto-scaling config | Built-in auto-scaling | Provider handles completely |
| **Cost model** | Per hour/GB (granular) | Per app/request | Per user/month (subscription) |

**When to choose:**
- **IaaS:** You need full control over the environment, are running custom enterprise software, or need specific OS/hardware configurations.
- **PaaS:** You want to focus purely on writing code without managing servers. Ideal for web apps, APIs, microservices.
- **SaaS:** You need a standard business application (email, CRM, collaboration) without any development effort.

---

### Q7. Explain the five cloud deployment models with examples.

**Answer:**

| Model | Who owns it | Who uses it | Example |
|---|---|---|---|
| **Public** | Third-party provider (AWS, Azure, GCP) | Anyone — individuals, startups, enterprises | AWS EC2 for a startup's web app |
| **Private** | Organisation itself (or dedicated third-party) | Single organisation only | A bank running OpenStack in its own data centre |
| **Hybrid** | Combination of public + private | Organisation using both | Hospital: patient records on private cloud, analytics on AWS |
| **Community** | Shared by organisations with common concerns | Specific community (same industry/regulation) | Government agencies sharing a FedRAMP cloud |
| **Multi-Cloud** | Multiple public providers | Organisation using AWS + Azure + GCP simultaneously | Using AWS for compute, GCP for ML, Azure for enterprise apps |

---

### Q8. What is hybrid cloud? What is cloud bursting?

**Answer:**

**Hybrid cloud** is a composition of two or more distinct cloud infrastructures (typically private + public) bound together by technology that enables data and application portability between them.

**Cloud bursting** is a deployment pattern where an application normally runs on private cloud, but when demand exceeds private cloud capacity, it "bursts" into the public cloud to handle the excess load. When demand subsides, the extra public cloud resources are released.

**Example:** An e-commerce company runs its website on a private cloud. During Black Friday, traffic spikes 10x. Instead of buying 10x servers that sit idle 364 days/year, the private cloud handles normal traffic and the excess bursts to AWS. After the sale, AWS instances are terminated.

**Benefits:** Cost efficiency (don't over-provision private cloud), flexibility, compliance (sensitive data stays private).
**Challenges:** Network latency between private and public, complex networking/security, data synchronisation.

---

### Q9. Explain the four types of cloud infrastructure resources.

**Answer:**

**1. Compute:** Processing power to run applications.
- VMs (EC2, Azure VMs), Containers (ECS, GKE), Serverless (Lambda), Bare Metal.

**2. Storage:** Durable, scalable data storage.
- **Object storage** (S3, Azure Blob) — files as objects with metadata, accessed via HTTP. Massively scalable.
- **Block storage** (EBS, Azure Managed Disks) — raw volumes attached to VMs like virtual hard drives. Low latency.
- **File storage** (EFS, Azure Files) — shared file systems accessible by multiple VMs via NFS/SMB.

**3. Network:** Connectivity between resources and to the internet.
- VPC (isolated virtual network), Subnets, Load Balancers, CDN, VPN/Direct Connect, Security Groups, DNS.

**4. Data Centres / Regions / Availability Zones:**
- **Region** = geographic area (e.g. Mumbai). Independent and isolated.
- **Availability Zone (AZ)** = one or more data centres within a region with independent power/cooling/network. Deploy across AZs for high availability.
- **Edge Location** = small data centre for CDN caching close to end users.

---

### Q10. What is the difference between a Region, an Availability Zone, and an Edge Location?

**Answer:**

| Concept | What it is | Purpose | Example |
|---|---|---|---|
| **Region** | A geographic area containing multiple AZs. Completely independent from other regions. | Data sovereignty — data stays in the region. Geographic proximity to users. | ap-south-1 (Mumbai), us-east-1 (N. Virginia) |
| **Availability Zone (AZ)** | One or more data centres within a region, with separate power, cooling, networking. Connected to other AZs in the same region by low-latency links. | **High availability** — if one AZ fails, others keep running. Deploy across AZs. | ap-south-1a, ap-south-1b, ap-south-1c |
| **Edge Location** | Smaller data centres at the network edge, worldwide. | Cache content (CDN) close to end users for low-latency delivery. | 600+ CloudFront edge locations globally |

**Key insight:** Regions provide isolation and data sovereignty. AZs within a region provide fault tolerance. Edge locations provide low-latency content delivery.

---

### Q11. List five benefits and five limitations of cloud computing.

**Answer:**

**Benefits:**
1. **Cost efficiency** — No upfront CapEx; pay-per-use OpEx. No idle hardware costs.
2. **Elasticity** — Scale up/down automatically with demand. No over-provisioning.
3. **Speed and agility** — Provision resources in minutes. Accelerates development and time-to-market.
4. **Global reach** — Deploy in multiple regions worldwide with a few clicks.
5. **Reliability** — Multi-AZ/multi-region architectures, provider SLAs of 99.9-99.999% uptime.

**Limitations:**
1. **Vendor lock-in** — Provider-specific services/APIs make migration difficult.
2. **Security concerns** — Data on shared infrastructure; multi-tenancy risks.
3. **Downtime** — Providers do have outages (AWS us-east-1 incidents).
4. **Data transfer costs** — Egress (data out of cloud) can be expensive.
5. **Compliance complexity** — GDPR, HIPAA, data localisation laws require careful region selection.

---

### Q12. Explain the CapEx vs OpEx shift in cloud computing. Why does it matter?

**Answer:**

| Aspect | CapEx (On-Premises) | OpEx (Cloud) |
|---|---|---|
| **Cost type** | Capital Expenditure — large upfront investment to buy hardware. | Operational Expenditure — ongoing, pay-as-you-go charges. |
| **Timing** | Pay everything upfront before you use it. | Pay monthly/hourly only for what you consume. |
| **Scaling** | Buy for peak capacity — hardware sits idle during normal periods. | Scale up for peak, scale down after — pay only during peak. |
| **Depreciation** | Hardware depreciates over 3-5 years. Becomes obsolete. | Always using latest hardware/software — provider upgrades. |
| **Risk** | You bear the risk of over-provisioning or under-provisioning. | Provider absorbs infrastructure risk. |

**Why it matters:** CapEx requires predicting future needs years in advance. If you guess wrong (over-provision), you waste money. If you under-provision, you can't serve demand. OpEx eliminates this guessing game — you consume exactly what you need, when you need it.

---

### Q13. What are FaaS and BaaS? How do they extend the traditional service models?

**Answer:**

**FaaS (Function as a Service):** Run individual functions in response to events (HTTP request, file upload, database change). No servers to manage — you upload a function, define triggers, and the cloud executes it automatically. Pay per invocation (per millisecond of execution).
- *Examples:* AWS Lambda, Azure Functions, Google Cloud Functions.
- *Use case:* Image thumbnail generation on upload, webhook processing, scheduled tasks.

**BaaS (Backend as a Service):** Pre-built backend services (authentication, database, push notifications, file storage) for mobile and web apps. Developers use APIs/SDKs without building or managing the backend.
- *Examples:* Firebase, AWS Amplify, Supabase.
- *Use case:* Mobile app that needs user login, real-time database, push notifications — all without writing backend code.

**How they extend traditional models:** FaaS goes beyond PaaS by eliminating even the concept of a "server" or "application" — you just write functions. BaaS goes further by providing pre-built backend components, so developers don't even write the backend.

---

### Q14. What is multi-tenancy? Why is it important in cloud computing?

**Answer:**

**Multi-tenancy** is an architecture where a single instance of software or infrastructure serves multiple customers (tenants) simultaneously. Each tenant's data and configuration are logically isolated even though they share the same physical resources.

**Why it's important:**
1. **Cost efficiency for the provider** — One set of hardware serves thousands of customers instead of dedicating separate hardware to each. This economy of scale is what makes cloud affordable.
2. **Resource utilisation** — When one tenant is idle, their resources can serve other tenants. Overall utilisation is much higher than dedicated hardware.
3. **Simplified management** — The provider maintains one system, not thousands of separate ones.

**Risks:**
- **Noisy neighbour** — One tenant's heavy workload can affect another tenant's performance on the same physical host.
- **Data isolation** — A bug or misconfiguration could potentially expose one tenant's data to another. Cloud providers use extensive isolation mechanisms (VMs, network segmentation, encryption) to prevent this.

---

### Q15. What is the role of virtualization in cloud computing? Why is it called a "core technology"?

**Answer:**

**Virtualization** is the technology that makes cloud computing possible. It creates a software-based (virtual) version of physical resources — compute, storage, network — allowing multiple virtual resources to share a single physical resource.

**Why it's the core technology:**

1. **Resource pooling** — A single physical server can host 10-50 VMs, each running a different customer's workload. Without virtualisation, each customer would need a dedicated physical server — wasteful and expensive.

2. **Isolation** — Each VM is completely isolated from others on the same physical host. One VM's crash or security breach doesn't affect others. This enables safe multi-tenancy.

3. **Elasticity** — VMs can be created in seconds and destroyed in seconds. This enables the rapid provisioning and release that NIST requires.

4. **Hardware abstraction** — Users interact with virtual resources, not physical hardware. The provider can migrate VMs between physical servers (for maintenance, load balancing) without the user noticing.

5. **Measured service** — The hypervisor tracks exactly how much CPU, memory, storage, and network each VM consumes, enabling accurate metering and billing.

Without virtualisation, cloud computing would be impossible — you'd be back to dedicated physical servers with no sharing, no elasticity, and no self-service.

---

*End of Topic 1 Questions & Answers*
