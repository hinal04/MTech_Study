# Topic 1: Introduction to Cloud Computing

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Morgan Kaufmann
> - T2: Erl, Puttini & Mahmood, *Cloud Computing: Concepts, Technology & Architecture*
> - R5: NIST SP 800-145, *The NIST Definition of Cloud Computing*
>
> **Contact Hours:** 4 (Lectures 1-4)

---

## Table of Contents

- [1.1 Cloud Computing: Definition, Characteristics, and Motivation](#11-cloud-computing-definition-characteristics-and-motivation)
- [1.2 Evolution and Origins of Cloud Computing](#12-evolution-and-origins-of-cloud-computing)
- [1.3 Cloud Service Models: IaaS, PaaS, SaaS](#13-cloud-service-models-iaas-paas-saas)
- [1.4 Cloud Deployment Models](#14-cloud-deployment-models)
- [1.5 Cloud Infrastructure Overview](#15-cloud-infrastructure-overview)
- [1.6 Benefits, Limitations, and Adoption Drivers](#16-benefits-limitations-and-adoption-drivers)

---

## 1.1 Cloud Computing: Definition, Characteristics, and Motivation

### What is Cloud Computing?

Cloud computing is a model for delivering computing resources — servers, storage, databases, networking, software, analytics — over the internet ("the cloud") as on-demand, self-service utilities. Instead of owning and maintaining physical data centres and servers, organisations rent access to computing resources from a cloud provider, paying only for what they use.

### The NIST Definition

The **US National Institute of Standards and Technology (NIST)** provides the most widely accepted formal definition:

> *Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (e.g., networks, servers, storage, applications, and services) that can be rapidly provisioned and released with minimal management effort or service provider interaction.*

This definition captures the essence: **shared resources**, **on-demand access**, **rapid provisioning**, and **minimal management**.

### The Two Core Concepts Behind "Cloud"

The word "cloud" references two fundamental ideas:

1. **Abstraction:** Cloud abstracts the details of system implementation from users and developers. Applications run on unspecified physical systems, data is stored at unknown locations, and system administration is outsourced. Users don't need to know (or care) which physical server runs their application.

2. **Virtualization:** Resources are pooled and shared among users, giving each the illusion of being the sole owner. Resources scale up and down in very short time without human intervention, are charged on a metered basis, and support multi-tenancy (multiple customers sharing the same physical infrastructure while remaining isolated from each other).

### The Five Essential Characteristics (NIST)

NIST defines five essential characteristics that distinguish cloud computing from traditional IT:

| Characteristic | Description | Example |
|---|---|---|
| **On-demand self-service** | A consumer can provision computing resources (servers, storage, network) automatically, without requiring human interaction with the service provider. | Spinning up a new virtual machine on AWS in 60 seconds via a web console — no phone call, no ticket, no waiting. |
| **Broad network access** | Resources are available over the network and accessed through standard mechanisms (HTTP, HTTPS, APIs) from a wide range of devices (laptops, phones, tablets, workstations). | Accessing Google Drive from your phone, laptop, and office desktop — same data, any device, anywhere. |
| **Resource pooling** | The provider's computing resources are pooled to serve multiple consumers using a **multi-tenant model**. Physical and virtual resources are dynamically assigned and reassigned according to demand. The customer generally has no control or knowledge over the exact location of resources. | AWS runs millions of virtual machines on shared physical servers. You don't know which physical rack your VM is on — and you don't need to. |
| **Rapid elasticity** | Resources can be elastically provisioned and released, in some cases automatically, to scale rapidly outward and inward with demand. To the consumer, the resources appear to be **unlimited** and can be appropriated in any quantity at any time. | Netflix automatically adds hundreds of servers during peak evening hours and releases them at 3 AM when traffic drops. |
| **Measured service** | Cloud systems automatically control and optimise resource use by leveraging a metering capability. Resource usage is monitored, controlled, and reported, providing transparency for both the provider and consumer (pay-per-use). | Your AWS bill shows exactly 743 hours of EC2 usage, 50 GB of S3 storage, and 12 GB of data transfer — you pay only for what you consumed. |

### Why Cloud Computing? The Motivation

Traditional IT infrastructure requires organisations to:
- **Buy** expensive servers, storage, and networking equipment upfront (capital expenditure — CapEx).
- **Provision for peak load** — if your Black Friday traffic is 10x your normal traffic, you need 10x the servers sitting idle 364 days a year.
- **Maintain** hardware, software patches, security, cooling, power — requiring dedicated IT staff.
- **Wait** weeks or months for procurement, installation, and configuration of new servers.

Cloud computing solves all of these:
- **No upfront cost** — pay only for what you use (operational expenditure — OpEx).
- **Elastic scaling** — scale up for peak, scale down after. No idle hardware.
- **No maintenance** — the cloud provider handles hardware, patching, security, cooling, power.
- **Instant provisioning** — spin up 100 servers in minutes, not months.

This shift from CapEx to OpEx, from owned infrastructure to rented services, is the fundamental economic driver of cloud adoption.

---

## 1.2 Evolution and Origins of Cloud Computing

### The Journey to Cloud

Cloud computing didn't appear overnight — it evolved through decades of computing paradigms, each building on the previous:

| Era | Paradigm | Key Idea | Limitation that Led to the Next |
|---|---|---|---|
| **1960s** | **Mainframe / Time-sharing** | John McCarthy proposed "computation as a public utility" like electricity. Multiple users shared a single mainframe. | Expensive, centralised, not scalable. |
| **1970s-80s** | **Personal Computing** | Decentralised computing power on every desk. | Isolated machines, no resource sharing. |
| **1990s** | **Client-Server & Internet** | Servers in data centres served clients over networks. The World Wide Web made information universally accessible. | Each application needed its own dedicated server. Massive over-provisioning. |
| **Late 1990s** | **Grid Computing** | Federate geographically distributed resources to solve large problems (e.g. SETI@home). Heterogeneous, loosely coupled. | Complex setup, no standard APIs, no self-service, no pay-per-use. |
| **Late 1990s** | **Cluster Computing** | Group of homogeneous, tightly coupled machines in one location acting as one system. | Limited to one location, not elastic. |
| **2000s** | **Utility Computing** | Computing resources offered as metered services (like electricity). Pay for what you use. | Limited scale, proprietary platforms. |
| **2006+** | **Cloud Computing** | Combines virtualisation + utility pricing + self-service + elasticity + broad network access. AWS launched EC2 in 2006. | The current paradigm — still evolving. |

### Key Predecessors in Detail

#### Grid Computing vs Cloud Computing

Grid computing and cloud computing both involve distributed resources, but they differ fundamentally:

| Aspect | Grid Computing | Cloud Computing |
|---|---|---|
| **Coupling** | Loosely coupled, heterogeneous systems across administrative domains. | Tightly managed by a single provider with standard APIs. |
| **Resource management** | Decentralised — each node has its own resource manager. | Centralised — the cloud provider manages all resources. |
| **User interface** | Complex setup, requires specialised knowledge. | Self-service portal, CLI, or API — usable by anyone. |
| **Billing** | Typically free (academic/research) or grant-funded. | Pay-per-use, metered billing. |
| **Elasticity** | Limited — resources are pre-allocated. | Automatic — scale up/down on demand. |
| **Virtualisation** | Minimal use. | Core technology — everything is virtualised. |

#### Cluster Computing vs Cloud Computing

| Aspect | Cluster Computing | Cloud Computing |
|---|---|---|
| **Location** | Single location (one data centre). | Globally distributed across regions. |
| **Homogeneity** | Homogeneous nodes (same hardware/OS). | Heterogeneous (various instance types, GPUs, ARM, etc.). |
| **Management** | Single system image, centralised scheduler. | Provider-managed, API-driven. |
| **Scaling** | Fixed — add physical nodes manually. | Elastic — add/remove VMs in seconds. |
| **Access** | Direct network access within the cluster. | Over the internet, from anywhere. |

#### Utility Computing — The Direct Ancestor

In the 1960s, **John McCarthy** proposed that computing could be organised as a public utility — just as electricity is generated by power plants and consumed by households, computing power could be generated by data centres and consumed over a network, charged per unit of use.

This vision took 40+ years to materialise because the required technologies (high-speed internet, virtualisation, distributed systems, automated provisioning) didn't exist yet. Cloud computing is the realisation of McCarthy's vision.

### The Birth of Modern Cloud (2006)

- **2006:** Amazon Web Services (AWS) launches **Elastic Compute Cloud (EC2)** and **Simple Storage Service (S3)** — the first commercially successful cloud IaaS offerings.
- **2008:** Google launches **Google App Engine** (PaaS).
- **2010:** Microsoft launches **Azure**.
- **2011:** IBM launches **SmartCloud**.
- **2014:** AWS becomes a $5B business. Cloud becomes mainstream enterprise IT.
- **2020+:** Cloud-native, serverless, edge computing, AI workloads drive the next wave.

---

## 1.3 Cloud Service Models: IaaS, PaaS, SaaS

Cloud services are delivered in three fundamental models, each offering a different level of abstraction and control. Think of it as a spectrum from "you manage everything" to "you manage nothing":

### The Cloud Service Model Stack

```
┌─────────────────────────────────────────────┐
│              SaaS                            │  ← You manage: Nothing (just use the app)
│   (Gmail, Salesforce, Dropbox, Zoom)        │
├─────────────────────────────────────────────┤
│              PaaS                            │  ← You manage: App code + data
│   (Heroku, Google App Engine, AWS Elastic   │
│    Beanstalk, Azure App Service)            │
├─────────────────────────────────────────────┤
│              IaaS                            │  ← You manage: OS, middleware, runtime, app, data
│   (AWS EC2, Azure VMs, Google Compute       │
│    Engine, DigitalOcean)                    │
├─────────────────────────────────────────────┤
│        Physical Infrastructure              │  ← Provider manages: Servers, storage, network,
│   (Data centres, servers, networking)       │     cooling, power, physical security
└─────────────────────────────────────────────┘
```

### 1.3.1 Infrastructure as a Service (IaaS)

**IaaS** provides virtualised computing resources over the internet. The cloud provider manages the physical infrastructure (servers, storage, networking, data centres), while the customer manages everything from the operating system upward.

**What you get:** Virtual machines, virtual networks, block storage, object storage — the raw building blocks of computing infrastructure.

**What you manage:** Operating system, middleware, runtime, applications, data. You have full control over the OS — you can install any software, configure any service.

**What the provider manages:** Physical servers, storage hardware, networking equipment, data centre facilities (power, cooling, physical security), hypervisor.

**Characteristics:**
- Maximum flexibility and control over the computing environment.
- You are responsible for patching the OS, configuring firewalls, managing security.
- Resources are provisioned on demand and billed per use (per hour, per GB, per request).
- You can scale up/down by adding/removing VMs.

**Examples:** AWS EC2, Microsoft Azure Virtual Machines, Google Compute Engine, IBM Cloud Virtual Servers, DigitalOcean Droplets.

**Use cases:** Hosting websites, running custom enterprise applications, development and testing environments, high-performance computing, big data analytics.

**Analogy:** Renting an empty apartment. The landlord provides the building (physical infrastructure), but you bring your own furniture, appliances, and decorations (OS, software, data).

### 1.3.2 Platform as a Service (PaaS)

**PaaS** provides a complete development and deployment platform. The cloud provider manages the infrastructure AND the platform (OS, middleware, runtime), while the customer focuses only on writing application code and managing data.

**What you get:** A managed platform with development tools, database services, application hosting — everything needed to build, test, and deploy applications without worrying about the underlying infrastructure.

**What you manage:** Application code and data. That's it.

**What the provider manages:** Physical infrastructure + OS + middleware + runtime + scaling + load balancing + patching.

**Characteristics:**
- Developers focus purely on code — no server management, no OS patching.
- Built-in scaling, load balancing, and high availability.
- Often includes development tools, database management, business intelligence services.
- Less control than IaaS — you can't customise the OS or install arbitrary software.
- Potential for **vendor lock-in** — applications may use provider-specific APIs.

**Examples:** Google App Engine, Heroku, AWS Elastic Beanstalk, Microsoft Azure App Service, Red Hat OpenShift.

**Use cases:** Web application development, API backends, microservices, rapid prototyping, collaborative development.

**Analogy:** Renting a furnished apartment. The landlord provides the building AND the furniture/appliances. You just bring your personal belongings (code, data).

### 1.3.3 Software as a Service (SaaS)

**SaaS** delivers complete, ready-to-use software applications over the internet. The cloud provider manages everything — infrastructure, platform, and the application itself. The user simply accesses the application through a web browser or API.

**What you get:** A fully functional application accessible via the internet.

**What you manage:** Your data and user-level configuration (settings, preferences, permissions).

**What the provider manages:** Everything — infrastructure, platform, application code, updates, security, availability, scaling.

**Characteristics:**
- Zero installation — access via web browser from any device.
- Automatic updates — the provider pushes updates without user intervention.
- Subscription-based pricing (monthly/annual).
- Multi-tenant — one application instance serves many customers (with data isolation).
- Minimal customisation — you use the application as designed.

**Examples:** Google Workspace (Gmail, Docs, Sheets), Microsoft 365, Salesforce, Dropbox, Zoom, Slack, Shopify.

**Use cases:** Email, collaboration, CRM, HR management, accounting, project management — any standardised business function.

**Analogy:** Staying at a hotel. Everything is provided — the building, the furniture, the room service. You just show up and use it.

### Responsibility Comparison Table

| Layer | On-Premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| **Applications** | You | You | You | Provider |
| **Data** | You | You | You | You* |
| **Runtime** | You | You | Provider | Provider |
| **Middleware** | You | You | Provider | Provider |
| **Operating System** | You | You | Provider | Provider |
| **Virtualisation** | You | Provider | Provider | Provider |
| **Servers** | You | Provider | Provider | Provider |
| **Storage** | You | Provider | Provider | Provider |
| **Networking** | You | Provider | Provider | Provider |

*In SaaS, the provider stores your data but you own it and are responsible for its content.

### Beyond the Big Three: Emerging Service Models

| Model | What it provides | Examples |
|---|---|---|
| **FaaS (Function as a Service)** | Run individual functions in response to events. No server management at all. Pay per invocation. | AWS Lambda, Azure Functions, Google Cloud Functions |
| **BaaS (Backend as a Service)** | Pre-built backend services (auth, database, push notifications) for mobile/web apps. | Firebase, AWS Amplify, Supabase |
| **CaaS (Container as a Service)** | Managed container orchestration. | AWS ECS/EKS, Google GKE, Azure AKS |
| **DBaaS (Database as a Service)** | Managed database instances. | Amazon RDS, Azure SQL Database, Google Cloud SQL |

---

## 1.4 Cloud Deployment Models

Deployment models describe **who owns and operates the cloud infrastructure** and **who can use it**. The five deployment models address different requirements for control, security, cost, and compliance.

### 1.4.1 Public Cloud

The cloud infrastructure is owned, managed, and operated by a **third-party cloud provider** and made available to the general public over the internet. Multiple organisations (tenants) share the same physical infrastructure, but their data and applications are logically isolated.

**Characteristics:**
- Owned and operated by the cloud provider (AWS, Azure, GCP).
- Available to anyone who wants to use it — individuals, startups, enterprises.
- Multi-tenant — physical resources shared across many customers.
- Pay-per-use pricing — no upfront capital expenditure.
- Provider is responsible for all hardware, maintenance, security of infrastructure.
- Virtually unlimited scalability.

**Advantages:** No upfront cost, elastic scaling, no maintenance burden, global reach.
**Disadvantages:** Less control over infrastructure, potential data sovereignty concerns, shared tenancy (noisy neighbour risk), dependency on provider.

**Examples:** AWS, Microsoft Azure, Google Cloud Platform, IBM Cloud, Oracle Cloud.

### 1.4.2 Private Cloud

The cloud infrastructure is provisioned for **exclusive use by a single organisation**. It may be owned, managed, and operated by the organisation itself, a third party, or a combination. It may exist on-premises or off-premises.

**Characteristics:**
- Dedicated to one organisation — not shared with others.
- Can be hosted on-premises (in the organisation's own data centre) or off-premises (hosted by a provider but dedicated to one customer).
- Greater control over security, compliance, and customisation.
- Higher cost — the organisation bears the full infrastructure cost.
- Limited scalability compared to public cloud.

**Advantages:** Maximum control and security, compliance with regulations (HIPAA, GDPR, government), customisation, data sovereignty.
**Disadvantages:** Higher cost (CapEx + OpEx), limited scalability, requires in-house expertise to manage.

**Examples:** VMware vSphere private cloud, OpenStack deployments, AWS Outposts (AWS infrastructure in your data centre).

**Use cases:** Government agencies, financial institutions, healthcare organisations — where regulatory compliance requires data to stay within controlled boundaries.

### 1.4.3 Hybrid Cloud

The cloud infrastructure is a **composition of two or more distinct cloud infrastructures** (private, public) that remain unique entities but are bound together by standardised technology that enables data and application portability.

**Characteristics:**
- Combines public and private cloud — workloads can move between them.
- Sensitive data/applications stay on private cloud; less-sensitive workloads use public cloud.
- Enables **cloud bursting** — when private cloud capacity is exhausted, excess demand overflows to the public cloud.
- Requires careful management of networking, security, and data movement between environments.

**Advantages:** Flexibility, cost optimisation (use public for variable workloads, private for steady), compliance (keep sensitive data private), gradual cloud migration.
**Disadvantages:** Complexity of managing two environments, networking challenges, potential latency between private and public components.

**Examples:** A bank running core banking on private cloud but using AWS for customer-facing mobile app. A hospital keeping patient records on private cloud but using Azure for analytics.

### 1.4.4 Community Cloud

The cloud infrastructure is provisioned for **exclusive use by a specific community of consumers** from organisations that have shared concerns (mission, security requirements, policy, compliance). It may be owned and operated by one or more of the community organisations, a third party, or a combination.

**Characteristics:**
- Shared by organisations with common requirements (same industry, same regulations).
- Cost is shared among community members — cheaper than individual private clouds.
- Governed by shared policies and compliance standards.

**Examples:** Government agencies sharing a FedRAMP-compliant cloud. Healthcare organisations sharing a HIPAA-compliant cloud. Research institutions sharing a scientific computing cloud.

### 1.4.5 Multi-Cloud

A **multi-cloud** strategy uses services from **multiple public cloud providers** (e.g. AWS + Azure + GCP) simultaneously. This is not an official NIST deployment model but is increasingly common in practice.

**Motivation:**
- **Avoid vendor lock-in** — don't depend on a single provider.
- **Best-of-breed** — use each provider's strongest services (e.g. AWS for compute, GCP for ML, Azure for enterprise integration).
- **Regulatory compliance** — some data must stay in specific geographic regions served by specific providers.
- **Resilience** — if one provider has an outage, workloads can shift to another.

**Challenges:** Increased operational complexity, need for cross-cloud networking, different APIs and tooling for each provider, higher skill requirements.

### Deployment Model Comparison

| Aspect | Public | Private | Hybrid | Community | Multi-Cloud |
|---|---|---|---|---|---|
| **Ownership** | Provider | Organisation or third party | Mixed | Shared by community | Multiple providers |
| **Tenancy** | Multi-tenant | Single-tenant | Mixed | Community-tenant | Multi-tenant per provider |
| **Cost** | Pay-per-use (lowest) | Highest (own infra) | Medium | Shared cost | Variable |
| **Control** | Least | Most | Medium | Shared | Medium |
| **Scalability** | Virtually unlimited | Limited | High (burst to public) | Moderate | Very high |
| **Security** | Provider-managed | Fully customisable | Mixed | Community-governed | Per-provider |
| **Compliance** | Provider certifications | Full control | Flexible | Community standards | Complex |

---

## 1.5 Cloud Infrastructure Overview

### The Four Pillars of Cloud Infrastructure

Every cloud provider's infrastructure is built on four fundamental resource categories:

### 1.5.1 Compute

Compute resources provide the processing power to run applications. In the cloud, compute is delivered primarily through **virtual machines** (VMs) and **containers**.

**Types of compute services:**

| Service Type | Description | Example |
|---|---|---|
| **Virtual Machines** | Full OS instances running on shared physical servers via hypervisors. | AWS EC2, Azure VMs, GCE |
| **Containers** | Lightweight, isolated application packages sharing the host OS kernel. | AWS ECS/EKS, GKE, Azure AKS |
| **Serverless / Functions** | Run code in response to events without managing any server. | AWS Lambda, Azure Functions |
| **Bare Metal** | Dedicated physical servers with no virtualisation overhead. | AWS Bare Metal, IBM Bare Metal |

### 1.5.2 Storage

Cloud storage provides durable, scalable data storage. Three main types:

| Type | Description | Use Case | Example |
|---|---|---|---|
| **Object Storage** | Stores data as objects (file + metadata + unique ID). Accessed via HTTP/REST APIs. Massively scalable, eventually consistent. | Media files, backups, data lakes, static websites. | AWS S3, Azure Blob, GCS |
| **Block Storage** | Provides raw storage volumes that attach to VMs like virtual hard drives. Low latency, high IOPS. | Databases, OS boot volumes, transactional workloads. | AWS EBS, Azure Managed Disks |
| **File Storage** | Shared file systems accessible by multiple VMs simultaneously via NFS/SMB protocols. | Shared application data, home directories, content management. | AWS EFS, Azure Files, GCP Filestore |

### 1.5.3 Network

Cloud networking connects compute and storage resources and provides connectivity to the internet and on-premises networks.

**Key networking concepts:**

| Concept | Description |
|---|---|
| **Virtual Private Cloud (VPC)** | An isolated virtual network within the cloud where you deploy resources. You define IP ranges, subnets, route tables, and gateways. |
| **Subnets** | Subdivisions of a VPC. Public subnets have internet access; private subnets do not. |
| **Load Balancer** | Distributes incoming traffic across multiple instances for scalability and availability. |
| **CDN (Content Delivery Network)** | Caches content at edge locations worldwide for low-latency delivery. |
| **VPN / Direct Connect** | Secure, private connection between on-premises network and the cloud VPC. |
| **Security Groups / Firewalls** | Rules controlling inbound and outbound traffic to instances. |
| **DNS** | Domain name resolution (e.g. AWS Route 53, Azure DNS). |

### 1.5.4 Data Centres, Regions, and Availability Zones

Cloud providers organise their physical infrastructure into a hierarchy:

```
Region (e.g. ap-south-1 = Mumbai)
├── Availability Zone 1 (ap-south-1a) — one or more data centres
├── Availability Zone 2 (ap-south-1b) — separate power, network, cooling
└── Availability Zone 3 (ap-south-1c) — physically separated (km apart)
```

| Concept | Description |
|---|---|
| **Region** | A geographic area (e.g. US East, EU West, Asia Pacific Mumbai). Each region is completely independent and isolated from other regions. Data stays within a region unless you explicitly move it (data sovereignty). |
| **Availability Zone (AZ)** | One or more data centres within a region, with independent power, cooling, and networking. AZs within a region are connected by low-latency links. Deploying across multiple AZs provides **high availability** — if one AZ goes down, the others keep running. |
| **Edge Location** | Smaller data centres at the network edge, used by CDNs to cache content close to end users for low latency. |

**Example:** AWS has 33+ regions, 105+ AZs, and 600+ edge locations globally (as of 2024).

---

## 1.6 Benefits, Limitations, and Adoption Drivers

### Benefits of Cloud Computing

| Benefit | Explanation |
|---|---|
| **Cost efficiency** | No upfront CapEx. Pay only for resources consumed (OpEx). No cost for idle hardware. The cloud provider achieves economies of scale and passes savings to customers. |
| **Elasticity and scalability** | Scale resources up during peak demand and down during lulls — automatically or with a few clicks. No over-provisioning. |
| **Speed and agility** | Provision new resources in minutes, not months. Experiment, iterate, and deploy faster. Accelerates time-to-market for new applications. |
| **Global reach** | Deploy applications in multiple regions worldwide with a few clicks. Serve users from the nearest data centre for low latency. |
| **Reliability and availability** | Cloud providers offer SLAs of 99.9% to 99.999% uptime. Multi-AZ and multi-region architectures provide fault tolerance. |
| **Security** | Major cloud providers invest billions in security — physical security, encryption, identity management, compliance certifications (SOC 2, ISO 27001, HIPAA, PCI DSS). Often better than what individual organisations can afford. |
| **Automatic updates** | Infrastructure and platform patches handled by the provider. No planned downtime for hardware maintenance. |
| **Focus on core business** | IT teams spend less time managing infrastructure and more time building applications that create business value. |
| **Disaster recovery** | Built-in backup, replication, and recovery capabilities across geographically separated regions. |
| **Environmental sustainability** | Cloud providers optimise data centre energy efficiency (PUE ratios, renewable energy). Shared infrastructure reduces overall carbon footprint vs. every company running its own data centre. |

### Limitations and Challenges

| Limitation | Explanation |
|---|---|
| **Vendor lock-in** | Applications built using provider-specific services (AWS Lambda, Azure Cosmos DB) are difficult to migrate to another provider. Proprietary APIs create dependency. |
| **Security and privacy concerns** | Data stored on shared infrastructure raises concerns about unauthorised access, data breaches, and government surveillance. Multi-tenancy means your data physically coexists with other customers' data. |
| **Downtime and outages** | Despite high SLAs, cloud providers do experience outages. An AWS US-East-1 outage can take down thousands of websites simultaneously. |
| **Limited control** | In PaaS/SaaS, you have limited control over the underlying infrastructure. You can't customise the OS, choose specific hardware, or optimise at the system level. |
| **Data transfer costs** | Moving large amounts of data into the cloud is often free, but moving data OUT (egress) can be expensive. This is sometimes called the "data gravity" problem. |
| **Compliance and data sovereignty** | Regulations like GDPR, HIPAA, and data localisation laws require data to be stored in specific geographic regions. Not all providers have data centres in all required locations. |
| **Network dependency** | Cloud requires reliable internet connectivity. Latency-sensitive applications may suffer if the network is slow or unreliable. |
| **Cost management complexity** | While cloud eliminates CapEx, OpEx costs can spiral if not carefully monitored. "Cloud sprawl" — unused VMs, over-provisioned resources — can make cloud more expensive than on-premises. |
| **Skill gap** | Cloud requires new skills — cloud architecture, DevOps, security, cost management — that traditional IT teams may not have. |

### Adoption Drivers

What motivates organisations to move to the cloud?

| Driver | Explanation |
|---|---|
| **Digital transformation** | Businesses must modernise IT to remain competitive. Cloud enables agile development, microservices, AI/ML, and data analytics. |
| **Cost reduction** | Shift from CapEx to OpEx. Eliminate idle hardware costs. Pay only for what you use. |
| **Scalability needs** | Startups need to scale from 10 to 10 million users. Enterprises need to handle seasonal peaks (Black Friday, tax season). |
| **Remote workforce** | COVID-19 accelerated cloud adoption as organisations needed to support remote work instantly. SaaS tools (Zoom, Teams, Slack) became essential. |
| **Innovation velocity** | Cloud provides access to cutting-edge services (AI/ML, IoT, blockchain, quantum computing) without building them from scratch. |
| **Business continuity** | Cloud provides built-in disaster recovery and geo-redundancy that would be prohibitively expensive to build on-premises. |
| **Regulatory compliance** | Some cloud providers now offer sovereign cloud and regulated cloud environments specifically designed for compliance (FedRAMP, HIPAA, PCI DSS). |

---

*End of Topic 1*
