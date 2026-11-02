# Cloud Computing - Mid-Semester Questions & Answers
## CSI ZG527 - Chapters 1-6 (Introduction, Virtualization, IaaS, Containers)

---

## Q1. Cloud Computing Definition and Characteristics [3 Marks]

### Question:
Define cloud computing according to NIST. Explain the two core concepts that define "cloud" and list the five essential characteristics of cloud computing services.

### Answer:

**NIST Definition [1 mark]:**
Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (networks, servers, storage, applications, services) that can be rapidly provisioned and released with minimal management effort or service provider interaction.

**Two Core Concepts Behind "Cloud" [1 mark]:**

1. **Abstraction:** Cloud abstracts system implementation details from users and developers. Applications run on unspecified physical systems, data is stored at unknown locations, and system administration is outsourced. Users don't know or need to know which physical server runs their application.

2. **Virtualization:** Resources are pooled and shared among users, giving each the illusion of being the sole owner. Resources scale up/down in short time without human intervention, are charged on a metered basis, and support multi-tenancy (multiple customers sharing physical infrastructure while remaining isolated).

**Five Essential Characteristics (NIST) [1 mark]:**

1. **On-Demand Self-Service:** Consumers can provision computing resources (server time, storage, applications) without human interaction with service providers. Automated provisioning.

2. **Broad Network Access:** Resources are accessed over the network using standard mechanisms (HTTP, APIs) on diverse devices (desktop, laptop, mobile phone).

3. **Resource Pooling:** Provider's computing resources are pooled in a multi-tenant model to serve multiple consumers. Physical and virtual resources are dynamically assigned and reassigned per consumer demand.

4. **Rapid Elasticity:** Capabilities can be elastically provisioned and released rapidly. To the consumer, available capabilities appear unlimited and can be acquired in any quantity at any time.

5. **Measured Service:** Cloud systems automatically control and optimize resource use by leveraging a metering capability at some level of abstraction appropriate to the type of service. Resource usage is monitored, controlled, and reported transparently to both provider and consumer.

---

## Q2. Cloud Service Models Comparison [4 Marks]

### Question:
Explain the three cloud service models (IaaS, PaaS, SaaS) with specific examples for each. Create a table comparing: (i) what the provider manages, (ii) what the customer manages, (iii) typical use cases, and (iv) one advantage and one disadvantage of each model.

### Answer:

**IaaS - Infrastructure as a Service [1.5 marks]:**
- **Definition:** Provider manages physical infrastructure (servers, storage, networking, virtualization). Customer manages OS, middleware, applications, data.
- **Examples:** AWS EC2, Microsoft Azure VMs, Google Compute Engine, DigitalOcean
- **Use case:** Running custom applications, databases, development environments where you need control over infrastructure

**PaaS - Platform as a Service [1.5 marks]:**
- **Definition:** Provider manages infrastructure AND middleware/platform. Customer manages applications and data.
- **Examples:** Heroku, Google App Engine, AWS Elastic Beanstalk, Azure App Service
- **Use case:** Rapid application development without managing underlying infrastructure; deploying web applications, APIs, microservices

**SaaS - Software as a Service [1.5 marks]:**
- **Definition:** Provider manages everything (infrastructure, platform, application). Customer manages only user accounts, data input.
- **Examples:** Salesforce, Microsoft Office 365, Google Workspace, Slack, Dropbox
- **Use case:** End-user applications accessed via web browser; no installation or maintenance required

**Comparison Table [1 mark]:**

| Aspect | IaaS | PaaS | SaaS |
|--------|------|------|------|
| **Provider Manages** | Infrastructure, virtualization, servers, storage, networking | Infrastructure + middleware, runtime, OS, databases | Everything - infrastructure + platform + applications |
| **Customer Manages** | OS, middleware, runtime, applications, data, security | Applications, data, security policies | User accounts, business logic, data |
| **Typical Use Cases** | Custom applications, dev/test environments, high-performance computing | Web app development, API services, rapid prototyping | Email, productivity, CRM, collaboration |
| **Advantage** | Maximum flexibility and control; pay-as-you-go | Faster development; less infrastructure management | Easiest to use; no installation/maintenance; accessible anywhere |
| **Disadvantage** | Requires technical expertise; more management overhead | Less flexibility than IaaS; vendor lock-in | Least flexibility; limited customization; data privacy concerns |

---

## Q3. Cloud Deployment Models [3.5 Marks]

### Question:
Describe the four cloud deployment models (public, private, hybrid, community). For each: (i) define the model, (ii) explain who owns/manages the infrastructure, (iii) provide one advantage and one disadvantage, (iv) state when you would choose this model.

### Answer:

**Public Cloud [0.9 marks]:**
- **Definition:** Cloud services are offered over the public internet to any organization or individual willing to pay. Infrastructure is shared among multiple customers.
- **Ownership:** Owned and operated by third-party cloud service providers (AWS, Azure, GCP)
- **Advantage:** Cost-effective (pay-as-you-go); highly scalable; minimal upfront investment
- **Disadvantage:** Less control; data privacy concerns; shared resources (noisy neighbor problem)
- **When to choose:** Startups, variable workloads, non-sensitive data, global reach required

**Private Cloud [0.9 marks]:**
- **Definition:** Cloud infrastructure is provisioned for exclusive use by a single organization. Can be on-premises or hosted by third-party.
- **Ownership:** Owned and operated by the organization (or managed service provider on their behalf)
- **Advantage:** Enhanced security and control; compliance with regulations; customization options
- **Disadvantage:** High capital and operational costs; limited scalability; requires skilled IT staff
- **When to choose:** Sensitive data (healthcare, finance), regulatory requirements (compliance), consistent workloads

**Hybrid Cloud [0.9 marks]:**
- **Definition:** Combination of public and private cloud infrastructure, with workloads distributed between them. Data and applications can move between clouds.
- **Ownership:** Mix of private infrastructure (owned by organization) and public cloud (third-party)
- **Advantage:** Flexibility (use public cloud for peaks, private for baseline); balance cost and control; can meet compliance requirements selectively
- **Disadvantage:** Complex management; integration challenges; potential security gaps
- **When to choose:** Variable workloads with baseline demand; need for both control and scalability; phased cloud migration

**Community Cloud [0.8 marks]:**
- **Definition:** Infrastructure shared among organizations with shared concerns or objectives (industry-specific, security requirements, etc.).
- **Ownership:** Owned/managed by consortium of organizations or third-party provider
- **Advantage:** Cost-sharing among organizations; specialized infrastructure for specific industry needs
- **Disadvantage:** Limited to specific community; less flexibility; governance complexity
- **When to choose:** Industry consortiums, research collaborations, specialized regulatory requirements (banking, healthcare networks)

---

## Q4. Evolution and History of Cloud Computing [2.5 Marks]

### Question:
Trace the evolution of cloud computing from its origins. What drove the shift from on-premises data centers to cloud? Discuss the role of virtualization in enabling cloud computing.

### Answer:

**Historical Origins [0.8 marks]:**
- **1960s - IBM Mainframes:** Virtual machines originated at IBM to provide parallel and interactive access to expensive mainframe computers. Multiple users could share one mainframe through software-based virtual machines.
- **Popek & Goldberg (1974):** Formally defined virtual machines requiring three properties: efficiency (near-native speed), isolation (VMs isolated from each other), and fidelity (indistinguishable from real machine).
- **1980s-1990s - Server Virtualization:** x86 virtualization technologies developed (VMware ESX, Xen). Allowed running multiple virtual machines on single physical server.

**Drivers of Cloud Computing Shift [1 mark]:**

1. **Cost Reduction:** Virtualization allowed consolidation of multiple workloads on fewer physical servers, reducing hardware and power costs.
2. **Scalability Needs:** Web applications (Google, Amazon, Facebook) needed elastic, on-demand resource scaling impossible with fixed data centers.
3. **Business Model Change:** Transition from CapEx (capital expenditure on hardware) to OpEx (operational expenditure through cloud services).
4. **Standardization:** Growth of Linux, open-source software, and standard APIs enabled cloud platforms.
5. **Network Bandwidth:** Increased internet bandwidth and reliability made network-based service delivery feasible.

**Role of Virtualization in Enabling Cloud [0.7 marks]:**
- Virtualization decouples software from hardware, allowing multiple isolated environments on single physical server
- Enables multi-tenancy: multiple customers' workloads run on same hardware while remaining isolated
- Allows rapid provisioning: VM can be created/deleted in seconds without hardware changes
- Provides resource pooling: physical resources shared among multiple virtual machines
- Foundation for elasticity: VMs can be dynamically created/destroyed based on demand

---

## Q5. Virtualization Types [3 Marks]

### Question:
Explain the different types of virtualization: platform virtualization, application virtualization, and network virtualization. For each type, provide: (i) a brief definition, (ii) how it works, (iii) one practical example.

### Answer:

**Platform (or Hypervisor) Virtualization [1 mark]:**
- **Definition:** Creation of virtual machines that simulate complete computer hardware platforms. Multiple OSes can run on single physical hardware via hypervisor.
- **How it works:** Hypervisor (VMM - Virtual Machine Monitor) intercepts hardware requests from VMs, manages hardware access, ensures isolation between VMs.
- **Example:** VMware ESXi running Windows VM, Linux VM, and another Linux VM on same physical server simultaneously; each runs its own OS independently.

**Application Virtualization [1 mark]:**
- **Definition:** Applications run in isolated environments (containers or sandboxes) on shared OS, rather than directly on OS.
- **How it works:** Application packaged with dependencies; runtime environment virtualizes system calls and hardware resources for the application.
- **Example:** Docker containers allow running multiple applications on same Linux OS in isolated containers; application thinks it has dedicated environment but shares kernel.

**Network Virtualization [1 mark]:**
- **Definition:** Creation of multiple isolated logical networks on shared physical network infrastructure.
- **How it works:** Virtual LANs (VLANs), virtual network interfaces, software-defined networking (SDN) separate network traffic logically.
- **Example:** VLANs allow different departments to have separate networks on same physical switch; traffic between VLANs isolated even though physical hardware is shared.

---

## Q6. Hypervisor Types and Comparison [3.5 Marks]

### Question:
Explain the two types of hypervisors (Type 1 and Type 2). Compare their: (i) architecture, (ii) performance characteristics, (iii) use cases, (iv) examples, and (v) advantages/disadvantages.

### Answer:

**Type 1 Hypervisor (Bare-metal) [1.75 marks]:**
- **Definition:** Hypervisor runs directly on physical hardware without an intermediate OS; has direct hardware access.
- **Architecture:** Hypervisor sits between hardware and VMs; manages all hardware resources directly
- **Performance:** Highest performance; minimal overhead; direct hardware control
- **Use cases:** Data center server virtualization, cloud infrastructure, high-performance computing
- **Examples:** VMware ESXi, Microsoft Hyper-V, KVM (Kernel Virtual Machine), Xen
- **Advantages:** Efficient resource utilization; high performance; better security isolation
- **Disadvantages:** More complex to install and manage; requires dedicated hardware

**Type 2 Hypervisor (Hosted) [1.75 marks]:**
- **Definition:** Hypervisor runs as application on top of a host operating system; depends on host OS for hardware access.
- **Architecture:** Host OS → Hypervisor application → VMs; hardware access mediated through host OS
- **Performance:** Lower performance than Type 1; overhead of host OS + hypervisor; suitable for development, not production
- **Use cases:** Development environments, testing, desktop virtualization, learning
- **Examples:** VMware Workstation, VirtualBox, Parallels Desktop, QEMU
- **Advantages:** Easy to install (just an application); works on existing OS; good for testing
- **Disadvantages:** Performance overhead; resource competition with host OS; not suitable for production

**Comparison Table [0 marks - summary]:**

| Criteria | Type 1 | Type 2 |
|----------|--------|--------|
| **Hardware Access** | Direct | Through host OS |
| **Performance** | High | Moderate to Low |
| **Installation** | Complex (replaces OS) | Simple (installs like app) |
| **Suitable for** | Production, data centers | Development, testing |
| **Resource Efficiency** | Better | Worse |
| **Isolation** | Strong | Weaker |

---

## Q7. IaaS Architecture and Components [4 Marks]

### Question:
Explain the IaaS architecture with at least 3 layers. For each layer, describe: (i) components, (ii) what the provider manages, (iii) what the customer manages, (iv) at least one AWS service example.

### Answer:

**Layer 1: Physical Infrastructure [1 mark]**
- **Components:** Data centers with physical servers, storage devices, network equipment, power distribution, cooling systems
- **Provider Manages:** Physical hardware, data center security, power, cooling, network connections, disaster recovery infrastructure
- **Customer Manages:** Nothing (completely provider responsibility)
- **AWS Example:** AWS data center infrastructure (not directly exposed to users)

**Layer 2: Virtualization Layer [1.5 marks]**
- **Components:** Hypervisors (AWS uses Xen), virtual machine management, virtual network creation, virtual storage provisioning
- **Provider Manages:** Hypervisor software, VM isolation, virtual network switching, virtual storage management, resource allocation to VMs
- **Customer Manages:** VMs configuration (OS choice, resource sizing), VM lifecycle (create, start, stop, terminate)
- **AWS Example:** EC2 compute instances (VMs), VPC (Virtual Private Cloud), EBS (Elastic Block Storage)

**Layer 3: Resource Management/Orchestration [1 mark]**
- **Components:** Provisioning automation, load balancing, auto-scaling, monitoring, API management
- **Provider Manages:** Provisioning APIs, automatic VM deployment, load balancing infrastructure, auto-scaling policies, monitoring systems
- **Customer Manages:** Which resources to provision, how to configure them, setting auto-scaling policies, defining load balancing rules
- **AWS Example:** ELB (Elastic Load Balancer), Auto Scaling Groups, CloudWatch monitoring, CloudFormation

**Layer 4: Management Services [0.5 marks]**
- **Components:** Identity and access management, billing, support, compliance tools
- **Provider Manages:** IAM infrastructure, billing systems, support systems, compliance certifications
- **Customer Manages:** User access policies, budget management, choosing compliance requirements
- **AWS Example:** IAM (Identity and Access Management), Billing Dashboard, AWS Support

---

## Q8. Containers vs. Virtual Machines [3 Marks]

### Question:
Compare containers and virtual machines. Explain: (i) what each is, (ii) architecture differences, (iii) performance characteristics, (iv) when to use each, and (v) create a comparison table.

### Answer:

**Virtual Machines [0.75 marks]:**
- **Definition:** Complete virtualized computer including OS, kernel, libraries, and application. Runs on hypervisor.
- **Architecture:** Hypervisor → Multiple VMs, each with own OS → Each VM has isolated kernel and libraries → Applications run on VMs

**Containers [0.75 marks]:**
- **Definition:** Lightweight, isolated execution environment that shares host OS kernel. Packages application with dependencies but not entire OS.
- **Architecture:** Host OS with shared kernel → Container engine (Docker, containerd) → Multiple containers sharing kernel → Each container isolated

**Architecture Differences [0.5 marks]:**
- VMs: Heavy virtualization; each VM has complete OS; high resource consumption
- Containers: Lightweight; all containers share host OS kernel; low resource consumption

**Performance Characteristics [0.5 marks]:**
- VMs: Higher overhead (bootup seconds to minutes); more memory per instance; lower density (fewer per server)
- Containers: Near-native performance; fast startup (milliseconds); minimal memory overhead; high density (100+ containers per server)

**When to Use [0.25 marks - shared in table]**

**Comparison Table:**

| Aspect | Virtual Machines | Containers |
|--------|-----------------|-----------|
| **Isolation** | Complete (separate OS kernel) | Partial (shared kernel) |
| **Size** | 1-10 GB+ | 10-100 MB |
| **Startup Time** | 30 seconds - 2 minutes | Milliseconds |
| **Memory per Instance** | 512 MB - 2+ GB | 10-100 MB |
| **Density per Server** | 4-10 VMs | 100+ containers |
| **Security** | Stronger isolation | Weaker isolation (shared kernel) |
| **Use Case** | Different OS, legacy apps, strong isolation needed | Microservices, DevOps, rapid scaling |
| **Examples** | AWS EC2, Azure VMs, VMware | Docker, Kubernetes, containerd |

---

## Q9. Docker and Containerization Basics [3 Marks]

### Question:
Explain what Docker is and how containerization works. Include: (i) Docker components (images, containers, registry), (ii) difference between Docker image and container, (iii) Docker workflow, (iv) advantages of Docker.

### Answer:

**What is Docker? [0.75 marks]:**
Docker is a containerization platform that allows developers to package applications and dependencies into lightweight, portable containers. Containers are isolated execution environments that share host OS kernel while appearing isolated to the application inside.

**Docker Components [1 mark]:**

1. **Docker Image:** Read-only template containing application code, runtime, libraries, dependencies. Blueprint for creating containers. Built in layers using Dockerfile.
   
2. **Docker Container:** Running instance of a Docker image. Mutable layer added on top of image layers. Isolated execution environment for application.

3. **Docker Registry:** Repository for storing and sharing Docker images. Docker Hub is public registry; private registries can also be created (AWS ECR, Azure Container Registry).

**Difference between Image and Container [0.5 marks]:**
- **Image:** Static template; blueprint; immutable; can be shared/versioned/reused
- **Container:** Running instance of image; mutable; temporary; isolated execution with its own filesystem, network, process namespace

**Docker Workflow [0.5 marks]:**
1. Developer writes Dockerfile specifying base image, dependencies, commands
2. `docker build` creates Docker image from Dockerfile
3. `docker run` creates and starts container from image
4. Container runs application isolated from host system
5. `docker push` uploads image to registry for sharing
6. Others `docker pull` and `docker run` the image

**Advantages of Docker [0.25 marks]:**
- **Portability:** Container runs identically on laptop, test server, production
- **Lightweight:** Containers share OS kernel; minimal overhead compared to VMs
- **Rapid deployment:** Containers start in milliseconds, enabling fast scaling
- **Consistent environment:** Same environment for development, testing, production
- **Resource efficiency:** High density; multiple containers per server

---

## Q10. Container Orchestration Concepts [2.5 Marks]

### Question:
What is container orchestration? Why is it needed? Briefly explain: (i) automatic deployment, (ii) scaling, (iii) networking, (iv) storage, (v) give examples of orchestration platforms.

### Answer:

**What is Container Orchestration? [0.75 marks]:**
Container orchestration is the automated management of containerized applications across multiple machines. It handles deployment, scaling, networking, storage, and lifecycle management of containers.

**Why is it Needed? [0.75 marks]:**
- **Complexity at scale:** Manual management infeasible when running hundreds of containers
- **High availability:** Automatic restart of failed containers, load balancing across instances
- **Resource optimization:** Intelligent placement of containers on optimal servers
- **Scaling:** Automatically scale up/down containers based on demand
- **Rolling updates:** Deploy new versions without downtime

**Key Orchestration Functions [1 mark]:**

1. **Automatic Deployment:** Place containers on appropriate nodes based on resource requirements and constraints

2. **Scaling:** Scale containers up/down based on CPU, memory, or custom metrics

3. **Networking:** Create virtual networks, manage DNS, service discovery so containers can communicate

4. **Storage:** Manage persistent storage volumes, attach to containers as needed

5. **Self-healing:** Restart failed containers, replace unhealthy nodes automatically

**Examples of Orchestration Platforms:**
- Kubernetes (industry standard; very feature-rich)
- Docker Swarm (simpler; integrated with Docker)
- AWS ECS (Amazon's container service)

---

## Q11. Infrastructure as Code (IaC) Concepts [2.5 Marks]

### Question:
What is Infrastructure as Code (IaC)? Explain: (i) the concept, (ii) benefits, (iii) examples of IaC tools, (iv) how IaC enables cloud operations, (v) difference between declarative and imperative approaches.

### Answer:

**What is Infrastructure as Code? [0.75 marks]:**
Infrastructure as Code is practice of managing and provisioning cloud infrastructure through configuration files (code) rather than manual processes. Infrastructure is defined in version-controlled files, enabling reproducibility and automation.

**Benefits of IaC [0.75 marks]:**
- **Reproducibility:** Define infrastructure once, deploy identically across environments
- **Version control:** Infrastructure definitions tracked in git; history of changes; rollback capability
- **Automation:** Eliminate manual errors; faster deployment
- **Documentation:** Code serves as documentation of infrastructure
- **Scalability:** Scale infrastructure by modifying code, not manual configuration

**Examples of IaC Tools [0.5 marks]:**
- **Terraform:** Multi-cloud IaC tool; HCL language; very flexible
- **CloudFormation:** AWS-specific; JSON/YAML templates
- **Ansible:** Configuration management; imperative approach
- **Chef/Puppet:** Configuration management tools

**Enabling Cloud Operations [0.25 marks]:**
IaC enables practices like Infrastructure-as-Code pipeline (define → test → deploy), rapid environment setup, disaster recovery (reproduce entire infrastructure), cost optimization (destroy unused infrastructure quickly).

**Declarative vs. Imperative [0.25 marks]:**
- **Declarative:** Specify desired end state (what should exist). Terraform, CloudFormation.
- **Imperative:** Specify steps to reach desired state (how to get there). Ansible, scripts.

---

## Q12. Shared Responsibility Model [3 Marks]

### Question:
Explain the shared responsibility model for cloud services. For IaaS, PaaS, and SaaS: (i) what the provider is responsible for, (ii) what the customer is responsible for, (iii) create a comparison table, (iv) why is this important?

### Answer:

**Concept of Shared Responsibility [0.75 marks]:**
The shared responsibility model clarifies what security and compliance responsibilities belong to the cloud provider versus the customer. As you move from IaaS to PaaS to SaaS, the provider assumes more responsibility, and customer responsibility decreases.

**IaaS Responsibilities [0.75 marks]:**
- **Provider Responsible:** Physical infrastructure, servers, storage, virtualization, physical network, power, cooling, physical security, DDoS protection at network layer
- **Customer Responsible:** OS security, patches, network configuration, security groups/firewall rules, encryption keys, access control, application security, data protection, compliance monitoring

**PaaS Responsibilities [0.75 marks]:**
- **Provider Responsible:** Everything in IaaS + operating system, middleware, runtime, patches, platform-level security, compliance certifications
- **Customer Responsible:** Application code, application-level security, data encryption, API security, access control for application, compliance of business logic

**SaaS Responsibilities [0.75 marks]:**
- **Provider Responsible:** Everything in PaaS + application software, features, compliance certifications, service availability, data backup
- **Customer Responsible:** User access management, business processes, data accuracy, compliance of data use, acceptable use policies

**Comparison Table:**

| Responsibility | IaaS | PaaS | SaaS |
|---|---|---|---|
| Physical Infrastructure | Provider | Provider | Provider |
| Operating System | Customer | Provider | Provider |
| Middleware/Runtime | Customer | Provider | Provider |
| Application Code | Customer | Customer | Provider |
| Data Protection | Customer | Customer | Customer* |
| Encryption Keys | Customer | Customer | Customer* |
| Access Control | Customer | Customer | Provider (users) + Customer (data) |
| Compliance | Shared | Shared | Shared |

**Why This Matters [0.5 marks - summary]:**
Understanding shared responsibility prevents security gaps. Neither provider nor customer should assume "the other is responsible." Both must implement security measures in their domain. Miscommunication about responsibilities leads to vulnerabilities.

---

## Q13. Cloud Benefits and Limitations [2.5 Marks]

### Question:
List and explain at least 5 major benefits of cloud computing and at least 4 limitations. For each benefit and limitation, provide a practical example.

### Answer:

**Benefits of Cloud Computing [1.5 marks]:**

1. **Cost Efficiency:** Pay-as-you-go model eliminates large upfront capital expenditure. *Example: Startup runs 10 servers on AWS, paying only $500/month instead of investing $50K in hardware.*

2. **Scalability:** Easily scale resources up or down based on demand. *Example: E-commerce site scales from 50 servers at baseline to 1000+ servers during holiday shopping season, then back down.*

3. **Accessibility:** Resources accessible from anywhere via internet; enables remote work. *Example: Developer accesses development server, database, version control from home, office, or coffee shop.*

4. **Automatic Updates:** Providers handle patches, security updates, infrastructure maintenance. *Example: AWS automatically applies security patches to infrastructure; no downtime for customer applications.*

5. **Global Reach:** Deploy applications in multiple geographic regions with data residency compliance. *Example: Company deploys application in EU data center for EU users (GDPR compliance), US for US users.*

6. **High Availability:** Redundancy, failover, load balancing built-in. *Example: AWS RDS automatically replicates database across availability zones; if one zone fails, application continues.*

**Limitations of Cloud Computing [1 mark]:**

1. **Vendor Lock-in:** Difficulty switching providers due to proprietary services, data migration costs, APIs. *Example: Heavy use of AWS Lambda, DynamoDB, S3 makes migration to Azure extremely costly.*

2. **Data Security/Privacy Concerns:** Data stored on third-party servers; potential data breaches, compliance issues. *Example: Healthcare company reluctant to store patient data on public cloud due to HIPAA requirements.*

3. **Downtime/Availability:** While generally reliable, cloud services can experience outages. *Example: AWS S3 outage in 2017 took down thousands of websites relying on it.*

4. **Limited Control:** Less control over infrastructure, customization, performance tuning compared to on-premises. *Example: Cannot optimize OS kernel, network routing for latency-sensitive applications.*

5. **Network Latency:** Internet-based access can introduce latency. *Example: High-frequency trading systems cannot tolerate cloud latency; must use on-premises infrastructure.*

---

## Q14. Cloud Adoption Drivers [2 Marks]

### Question:
What are the main drivers for cloud adoption in organizations? Explain at least 5 factors.

### Answer:

**Main Drivers for Cloud Adoption [2 marks]:**

1. **Cost Reduction:** Lower infrastructure costs, reduced capital expenditure, pay-as-you-go model. Operations costs also reduced through automation and reduced staffing needs.

2. **Agility and Speed:** Faster application development and deployment. Resource provisioning in minutes instead of weeks. Rapid market response.

3. **Scalability and Elasticity:** Handle demand spikes without overprovisioning. Scale up for peak traffic, down to baseline during off-peak.

4. **Focus on Core Business:** Outsource infrastructure management to cloud provider; IT staff focus on applications and business logic, not server maintenance.

5. **Global Expansion:** Deploy applications globally without building data centers in each region. Leverage provider's global infrastructure.

6. **Reliability and Availability:** Cloud providers offer high availability, disaster recovery, redundancy at lower cost than building in-house.

7. **Business Continuity:** Automatic backup, disaster recovery, replication reduce risk of data loss.

8. **Competitive Pressure:** Competitors using cloud to reduce costs and accelerate time-to-market; pressure to adopt cloud competitively.

---

## Q15. Multi-Tenancy and Resource Pooling [2.5 Marks]

### Question:
Explain multi-tenancy in cloud computing. Discuss: (i) what it means, (ii) how it works in cloud, (iii) benefits, (iv) security implications, (v) contrast with single-tenancy.

### Answer:

**What is Multi-Tenancy? [0.75 marks]:**
Multi-tenancy is cloud architecture where single instance of software/infrastructure serves multiple customers (tenants) simultaneously. Each tenant's data and configuration is logically isolated from others, though physical infrastructure is shared.

**How Multi-Tenancy Works [0.75 marks]:**
- Single database with tenant identifier on each record; queries filtered by tenant ID
- Virtual machines for multiple customers on same physical server; hypervisor enforces isolation
- Shared services (load balancer, monitoring) apply resources to multiple customers' workloads
- Resource pooling: idle resources of one tenant can be used by another's peak workload

**Benefits [0.5 marks]:**
- **Cost efficiency:** Physical resources shared; provider reduces hardware and power costs, passing savings to customers
- **Scalability:** Providers can add new customers without adding servers
- **Resource optimization:** Efficient utilization of capacity across diverse workloads
- **Reduced maintenance:** Single version of software maintained for all tenants

**Security Implications [0.25 marks]:**
- **Risk:** If isolation fails, one tenant could access another's data (critical security issue)
- **Mitigation:** Strong encryption, access controls, logical separation, firewalls, network segmentation

**Multi-Tenancy vs. Single-Tenancy:**
- **Multi-tenancy:** Shared resources; lower cost; higher density; requires strong isolation mechanisms
- **Single-tenancy:** Dedicated resources per customer; higher cost; weaker isolation risk; more customization possible

---

## Q16. Cloud Security Responsibility [2 Marks]

### Question:
Explain the customer's role in cloud security. What security measures should a customer implement for: (i) data protection, (ii) access control, (iii) encryption, (iv) compliance? Provide practical examples.

### Answer:

**Customer's Data Protection Responsibility [0.5 marks]:**
- **Encrypt data at-rest:** Use server-side encryption (AWS S3-SSE), customer-managed keys (AWS KMS), database encryption
- **Backup:** Regular backups to separate location; test restore procedures
- **Example:** Healthcare provider encrypts patient data using AES-256 at-rest on AWS S3; maintains separate backup in different region

**Access Control [0.5 marks]:**
- **Principle of least privilege:** Grant minimum permissions needed for role
- **Multi-factor authentication:** Require MFA for administrative access
- **Identity management:** Manage user accounts, roles, permissions using IAM (AWS) or similar
- **Example:** Database administrator has access only to database; application developers cannot access production database; developers use MFA for AWS access

**Encryption [0.25 marks]:**
- **Encryption in-transit:** Use TLS/HTTPS for data movement
- **Encryption at-rest:** Encrypt stored data
- **Key management:** Manage encryption keys securely; rotate keys regularly
- **Example:** Web application uses HTTPS (TLS 1.2) for all data in-transit; encrypts database with AWS KMS customer-managed keys

**Compliance [0.25 marks]:**
- **Audit logging:** Monitor access, changes, delete operations; maintain audit trails
- **Compliance monitoring:** Ensure adherence to regulations (GDPR, HIPAA, PCI-DSS)
- **Incident response:** Plan for security incidents; maintain response procedures
- **Example:** Financial institution audits all access to customer data; logs stored in immutable storage; quarterly compliance reports

---

## Marks Summary
- Q1: 3 marks
- Q2: 4 marks
- Q3: 3.5 marks
- Q4: 2.5 marks
- Q5: 3 marks
- Q6: 3.5 marks
- Q7: 4 marks
- Q8: 3 marks
- Q9: 3 marks
- Q10: 2.5 marks
- Q11: 2.5 marks
- Q12: 3 marks
- Q13: 2.5 marks
- Q14: 2 marks
- Q15: 2.5 marks
- Q16: 2 marks

**Total: 46 marks**

