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

#### Deep Dive: Each Essential Characteristic

**1. On-Demand Self-Service**

This is the fundamental shift from traditional IT. In the old world, requesting a new server involved:
- Filing a procurement request (days)
- Waiting for budget approval (weeks)
- Purchasing, shipping, and racking hardware (weeks to months)
- Installing the OS and configuring the network (days)

In cloud computing, the same task takes **seconds to minutes** through a web portal, CLI, or API. There is zero human interaction with the provider required. A developer at 2 AM can provision a database cluster, a load balancer, and ten virtual machines — all without filing a single ticket.

**Real-world scenario:** A startup receives sudden media coverage and needs to scale from 2 servers to 50 within the hour. With on-demand self-service, the CTO writes an API call or clicks a button in the console — no phone call to a vendor, no waiting for hardware delivery.

**2. Broad Network Access**

Cloud resources are accessible over standard networks (usually the internet) using standard protocols (HTTP/HTTPS, SSH, RDP) and can be consumed from **any device**:

| Device | Access Method | Example |
|---|---|---|
| Laptop / Desktop | Web browser, CLI, SDK | Manage AWS resources via console or `aws` CLI |
| Mobile phone | Mobile app, responsive web UI | Check Azure dashboard on iPhone |
| Tablet | Web browser, dedicated apps | Monitor GCP metrics on iPad |
| IoT devices | REST APIs, MQTT | Sensor sending data to AWS IoT Core |
| Other servers | APIs, SDKs | Backend service calling S3 API |

This characteristic also means cloud services expose **standard, well-documented APIs** (typically RESTful) that any programming language can consume. The same API that works from your laptop works from a server in Singapore.

**3. Resource Pooling (Multi-Tenant Model)**

The provider maintains a massive pool of computing resources (CPU, RAM, storage, network bandwidth) and dynamically allocates slices to different customers. This is the **multi-tenant model** — multiple customers (tenants) share the same physical infrastructure while remaining logically isolated from each other.

```
Physical Server (64 cores, 256 GB RAM)
├── Tenant A: VM1 (4 cores, 16 GB)
├── Tenant B: VM2 (8 cores, 32 GB)
├── Tenant C: VM3 (2 cores, 8 GB)
├── Tenant D: VM4 (16 cores, 64 GB)
└── [remaining resources available for new tenants]
```

Key aspects of resource pooling:
- **Location independence:** The customer generally does not know the exact physical location of their resources (which server, which rack, which data centre floor). They may be able to specify a region (e.g., EU-West) but not a specific machine.
- **Dynamic assignment:** As one customer releases resources, those resources become available for others. The pool is continuously rebalanced.
- **Economies of scale:** Pooling enables the provider to achieve much higher utilisation rates (60-80%) than individual enterprises (typically 10-20%), driving down costs for everyone.

**4. Rapid Elasticity**

Elasticity means resources can **grow and shrink** automatically in response to demand. This is different from scalability (which is the ability to grow). Elasticity specifically includes the ability to **release resources** when they're no longer needed.

| Scenario | Traditional IT | Cloud with Elasticity |
|---|---|---|
| Normal traffic (100 users) | 10 servers running | 2 servers running |
| Black Friday spike (10,000 users) | 10 servers struggling, site crashes | Auto-scales to 200 servers in minutes |
| Post-spike (back to 100 users) | Still 10 servers (can't return hardware) | Scales back down to 2 servers |
| Monthly cost | $50,000/month (10 servers always) | $5,000/month avg (pay for actual use) |

**Scaling directions:**
- **Scale out (horizontal):** Add more instances (VMs, containers). Most common in cloud.
- **Scale up (vertical):** Increase size of existing instance (more CPU, RAM). Limited by hardware maximums.
- **Scale in:** Remove instances when demand drops.
- **Scale down:** Reduce size of existing instances.

**Auto-scaling** services (AWS Auto Scaling, Azure VMSS, GCP Managed Instance Groups) monitor metrics like CPU utilisation and automatically add/remove instances based on predefined rules or machine learning predictions.

**5. Measured Service (Pay-Per-Use)**

Cloud systems include metering at every level, and customers pay only for resources actually consumed. This is analogous to utility billing — you pay for the electricity you use, not for the capacity of the power plant.

| Resource | Unit of Measurement | Example Pricing |
|---|---|---|
| Compute (VMs) | Per hour or per second of runtime | $0.0116/hour for a t3.micro on AWS |
| Storage | Per GB per month | $0.023/GB/month for S3 Standard |
| Data transfer | Per GB transferred out | $0.09/GB for data leaving AWS |
| API calls | Per 1,000 or 1,000,000 requests | $0.004 per 10,000 GET requests to S3 |
| Serverless functions | Per invocation + per GB-second of compute | $0.20 per 1M invocations on Lambda |
| Database | Per hour + per I/O operation + per GB storage | RDS pricing varies by engine and size |

Measured service provides **transparency** — both the provider and consumer can monitor exactly how much is being consumed, enabling:
- **Cost allocation:** Charge departments or projects for their actual cloud usage.
- **Optimisation:** Identify underutilised resources and rightsize them.
- **Budgeting:** Set spending alerts and budget caps.
- **Governance:** Enforce resource quotas and spending limits.

### Cloud Computing vs Web Applications

A common misconception is that "cloud computing" simply means "running an application on the internet." This is incorrect. While **web applications** and **cloud applications** both run on remote servers and are accessed via browsers, they are fundamentally different:

| Aspect | Web Application | Cloud Application |
|---|---|---|
| **Architecture** | Runs on fixed, dedicated web servers | Runs on cloud infrastructure with dynamic resource allocation |
| **Scalability** | Limited to the capacity of provisioned servers; scaling requires manual intervention (buying/configuring new servers) | Automatically scales up/down based on demand using elasticity |
| **Elasticity** | None — resources are fixed regardless of load | Resources expand and contract with demand in real-time |
| **Billing** | Fixed cost (monthly server rental regardless of usage) | Pay-per-use (metered consumption) |
| **Availability** | Depends on specific server uptime; single point of failure if one server | Distributed across multiple data centres and availability zones; self-healing |
| **Multi-tenancy** | Typically single-tenant or basic shared hosting | True multi-tenant with resource pooling and isolation |
| **Self-service** | Requires admin intervention for infrastructure changes | Users provision resources on-demand without human interaction |
| **Example** | A PHP website running on a single GoDaddy shared hosting server | A Netflix-style application on AWS that auto-scales from 100 to 10,000 instances based on viewer demand |

**The key distinction:** A cloud application leverages all five NIST essential characteristics (on-demand self-service, broad network access, resource pooling, rapid elasticity, measured service). A web application merely runs on a web server accessible via HTTP — it doesn't inherently exhibit elasticity, resource pooling, or measured service.

**A web application becomes a cloud application** when it is redesigned to exploit cloud characteristics — for example, when it uses auto-scaling groups, load balancers, managed databases, and pay-per-use pricing rather than running on a single fixed server.

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

**The Utility Computing Analogy — Why It Matters:**

| Utility | How It Works | Cloud Equivalent |
|---|---|---|
| **Electricity** | Power plant generates, grid distributes, you plug in and pay per kWh | Data centre provides compute, internet distributes, you provision and pay per hour |
| **Water** | Treatment plant purifies, pipes distribute, you turn the tap and pay per litre | Storage service stores data, API provides access, you upload/download and pay per GB |
| **Telephone** | Exchange routes calls, you dial and pay per minute | Network service routes traffic, you send data and pay per GB transferred |

The key insight: **you don't build your own power plant** to run your business. You plug into the grid. Similarly, you shouldn't build your own data centre — you plug into the cloud.

**Technologies That Made Cloud Possible:**

The gap between McCarthy's 1960s vision and 2006 cloud reality was bridged by several enabling technologies:

| Technology | Contribution to Cloud | When It Matured |
|---|---|---|
| **High-speed Internet** | Made it practical to access remote resources with acceptable latency | Late 1990s-2000s |
| **Virtualisation** | Enabled resource pooling and multi-tenancy on shared hardware | 2000s (VMware ESX 2001, Xen 2003) |
| **Distributed systems** | Algorithms for consistency, availability, and partition tolerance across data centres | 1990s-2000s |
| **Web services / APIs** | Standard interfaces (REST, SOAP) for programmatic resource provisioning | 2000s |
| **Automated provisioning** | Software-defined infrastructure, configuration management | 2000s |
| **Broadband adoption** | Mass consumer and business internet connectivity | 2000s |

### The Birth of Modern Cloud (2006)

- **2006:** Amazon Web Services (AWS) launches **Elastic Compute Cloud (EC2)** and **Simple Storage Service (S3)** — the first commercially successful cloud IaaS offerings. AWS was born from Amazon.com's internal need to scale their retail infrastructure — they realised they could rent their excess capacity to others.
- **2008:** Google launches **Google App Engine** (PaaS).
- **2009:** Heroku launches (PaaS for Ruby, later expanded).
- **2010:** Microsoft launches **Azure**. OpenStack (open-source cloud platform) is released.
- **2011:** IBM launches **SmartCloud**.
- **2013:** Docker launches, revolutionising containerisation and eventually cloud-native development.
- **2014:** AWS becomes a $5B business. Kubernetes released by Google. Cloud becomes mainstream enterprise IT.
- **2015:** Cloud Native Computing Foundation (CNCF) founded.
- **2017:** Serverless computing gains traction (AWS Lambda, Azure Functions).
- **2020+:** Cloud-native, serverless, edge computing, AI/ML workloads drive the next wave. COVID-19 accelerates cloud adoption as organisations move to remote work.

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

### The Pizza Analogy — Understanding Service Models Intuitively

The service models are often explained using a **pizza analogy** that maps each model to how you might get pizza:

| Service Model | Pizza Analogy | What You Do | What's Done for You |
|---|---|---|---|
| **On-Premises** | **Homemade Pizza** | You buy ingredients, make the dough, prepare toppings, bake it in your oven, serve on your plates. You control everything. | Nothing — you do it all. |
| **IaaS** | **Take-and-Bake Pizza** | You pick up a pre-made pizza from the store and bake it in your own oven. You control cooking time, temperature, toppings to add. | The dough is made, base toppings applied. |
| **PaaS** | **Pizza Delivery** | You order a pizza, customise toppings and size. The restaurant makes it, bakes it, and delivers it. | Everything except choosing what you want on it. |
| **SaaS** | **Dining Out (Dine-In)** | You walk into a restaurant, sit down, and eat pizza from the menu. You choose a pizza and enjoy it. | The restaurant does everything — ingredients, cooking, serving, cleaning. |

```
On-Premises     IaaS              PaaS              SaaS
(Homemade)      (Take-and-Bake)   (Delivery)        (Dine-In)
┌───────────┐   ┌───────────┐     ┌───────────┐     ┌───────────┐
│ You make  │   │ You bake  │     │ You choose│     │ You eat   │
│ everything│   │ and serve │     │ toppings  │     │ the pizza │
│           │   │           │     │           │     │           │
│ Dough ✓   │   │ Bake ✓    │     │ Order ✓   │     │ Eat ✓     │
│ Toppings ✓│   │ Serve ✓   │     │           │     │           │
│ Oven ✓    │   │           │     │           │     │           │
│ Bake ✓    │   │           │     │           │     │           │
│ Serve ✓   │   │           │     │           │     │           │
│ Clean ✓   │   │           │     │           │     │           │
└───────────┘   └───────────┘     └───────────┘     └───────────┘
  Max control    High control      Med control       Min control
  Max effort     Med effort        Low effort        No effort
```

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

**Advantages:**
- No upfront capital investment — convert CapEx to OpEx.
- Elastic scaling — grow and shrink resources in minutes.
- No maintenance burden — provider handles hardware, patching, cooling, power.
- Global reach — deploy to any region worldwide instantly.
- Economies of scale — provider passes cost savings to customers.
- Access to cutting-edge services (AI/ML, analytics, IoT) without building them.

**Disadvantages:**
- Less control over infrastructure — you can't choose specific hardware or rack locations.
- Data sovereignty concerns — data may reside in another country.
- Shared tenancy — "noisy neighbour" risk where another tenant's workload affects your performance.
- Dependency on provider — outage at the provider affects all customers.
- Vendor lock-in if using proprietary services.

**Use cases:** Startups (no upfront cost), web applications, development and testing, big data analytics, SaaS products, AI/ML workloads.

**Examples:** AWS, Microsoft Azure, Google Cloud Platform, IBM Cloud, Oracle Cloud, Alibaba Cloud.

### 1.4.2 Private Cloud

The cloud infrastructure is provisioned for **exclusive use by a single organisation**. It may be owned, managed, and operated by the organisation itself, a third party, or a combination. It may exist on-premises or off-premises.

**Characteristics:**
- Dedicated to one organisation — not shared with others.
- Can be hosted on-premises (in the organisation's own data centre) or off-premises (hosted by a provider but dedicated to one customer).
- Greater control over security, compliance, and customisation.
- Higher cost — the organisation bears the full infrastructure cost.
- Limited scalability compared to public cloud.

**Advantages:**
- Maximum control and security — full control over hardware, software, network, and data.
- Compliance with strict regulations (HIPAA, GDPR, government classified data).
- Customisation — tailor the infrastructure to exact requirements (specific hardware, OS, network topology).
- Data sovereignty — data never leaves your premises (for on-premises private cloud).
- Predictable performance — no noisy neighbours.

**Disadvantages:**
- Higher cost (CapEx + OpEx) — you bear the full cost of hardware, software, power, cooling, staff.
- Limited scalability — constrained by your own hardware capacity.
- Requires in-house expertise to build, manage, and maintain.
- Slower provisioning compared to public cloud — still need to procure and install hardware.
- Underutilisation risk — you pay for peak capacity even during low-demand periods.

**Use cases:** Government agencies, financial institutions, healthcare organisations, defence and military, any organisation with strict regulatory or compliance requirements.

**Examples:** VMware vSphere private cloud, OpenStack deployments, AWS Outposts (AWS infrastructure in your data centre), Azure Stack.

### 1.4.3 Hybrid Cloud

The cloud infrastructure is a **composition of two or more distinct cloud infrastructures** (private, public) that remain unique entities but are bound together by standardised technology that enables data and application portability.

**Characteristics:**
- Combines public and private cloud — workloads can move between them.
- Sensitive data/applications stay on private cloud; less-sensitive workloads use public cloud.
- Enables **cloud bursting** — when private cloud capacity is exhausted, excess demand overflows to the public cloud.
- Requires careful management of networking, security, and data movement between environments.

**Advantages:**
- Flexibility — choose the right environment for each workload.
- Cost optimisation — use public cloud for variable/bursty workloads, private cloud for steady baseline.
- Compliance — keep regulated data on private cloud while using public for non-sensitive operations.
- Gradual migration — move to cloud incrementally rather than a risky "big bang" migration.
- Business continuity — fail over from private to public cloud during disasters.

**Disadvantages:**
- Complexity of managing two distinct environments with different tools and APIs.
- Networking challenges — latency, bandwidth, and security between private and public components.
- Skill requirements — team needs expertise in both private and public cloud.
- Data consistency — keeping data synchronised across environments is challenging.
- Higher cost than pure public cloud due to maintaining private infrastructure.

**Cloud Bursting Example:**
```
Normal load:     Private Cloud handles 100% of traffic
Peak load:       Private Cloud at capacity → overflow to Public Cloud
                 Private: 70% of traffic | Public: 30% of traffic
Post-peak:       Public Cloud resources released, back to Private only
```

**Use cases:** A bank running core banking on private cloud but using AWS for customer-facing mobile app. A hospital keeping patient records on private cloud but using Azure for analytics. Retail companies handling Black Friday overflow on public cloud.

### 1.4.4 Community Cloud

The cloud infrastructure is provisioned for **exclusive use by a specific community of consumers** from organisations that have shared concerns (mission, security requirements, policy, compliance). It may be owned and operated by one or more of the community organisations, a third party, or a combination.

**Characteristics:**
- Shared by organisations with common requirements (same industry, same regulations).
- Cost is shared among community members — cheaper than individual private clouds.
- Governed by shared policies and compliance standards.
- May be managed by a member organisation or a third-party provider.

**Advantages:**
- Cost sharing — infrastructure costs divided among community members.
- Pre-built compliance — designed to meet community-specific regulatory requirements.
- Collaboration — shared platform enables data sharing and collaboration within the community.
- More secure than public cloud — limited to vetted community members.

**Disadvantages:**
- Less flexible than public cloud — must adhere to community governance and policies.
- Limited to community members — can't easily bring in external partners.
- Governance complexity — multiple organisations must agree on policies, upgrades, and changes.
- Smaller scale than public cloud — limited to community's combined demand.

**Examples:**
- **Government:** FedRAMP-compliant cloud shared by US federal agencies.
- **Healthcare:** HIPAA-compliant cloud shared by hospitals and research institutions.
- **Financial services:** PCI DSS-compliant cloud shared by banks and payment processors.
- **Research:** Scientific computing cloud shared by universities (e.g., CERN computing grid).

### 1.4.5 Multi-Cloud

A **multi-cloud** strategy uses services from **multiple public cloud providers** (e.g. AWS + Azure + GCP) simultaneously. This is not an official NIST deployment model but is increasingly common in practice.

**Motivation:**
- **Avoid vendor lock-in** — don't depend on a single provider.
- **Best-of-breed** — use each provider's strongest services (e.g. AWS for compute, GCP for ML, Azure for enterprise integration).
- **Regulatory compliance** — some data must stay in specific geographic regions served by specific providers.
- **Resilience** — if one provider has an outage, workloads can shift to another.
- **Negotiating leverage** — ability to switch providers gives better pricing power.

**Challenges:**
- Increased operational complexity — different APIs, tools, and console for each provider.
- Need for cross-cloud networking and identity management.
- Higher skill requirements — team must be proficient in multiple platforms.
- Data transfer costs — moving data between providers is expensive.
- Inconsistent SLAs and support models across providers.

**Multi-Cloud Tools:** Terraform (infrastructure as code across providers), Kubernetes (container orchestration portable across providers), Anthos (Google's multi-cloud platform), Azure Arc (extend Azure management to other clouds).

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

#### The CapEx to OpEx Shift — The Economic Core of Cloud

This is the single most important economic benefit of cloud computing. Understanding it is critical:

| Aspect | Traditional IT (CapEx) | Cloud Computing (OpEx) |
|---|---|---|
| **Payment model** | Large upfront purchase of hardware | Pay monthly/hourly for consumption |
| **Financial classification** | Capital Expenditure — depreciates over 3-5 years | Operational Expenditure — expensed immediately |
| **Risk** | If demand doesn't materialise, hardware sits idle (sunk cost) | Scale down and stop paying if demand drops |
| **Provisioning** | Must buy for **peak capacity** (expensive during normal periods) | Pay only for **actual usage** at any moment |
| **Cash flow** | Large cash outlay upfront | Small, predictable monthly payments |
| **Tax treatment** | Depreciated over asset lifetime | Deductible as operating expense immediately |
| **Flexibility** | Hardware locked in for 3-5 years (technology becomes outdated) | Switch to latest technology anytime |

**Example:** A company expects 1,000 users but must provision for 10,000 (peak). Traditional IT: buy 10 servers at $50,000 = **$500,000 upfront**, 90% idle capacity most of the time. Cloud: run 1 server for $500/month, scale to 10 during peak = **$500-$5,000/month**, zero idle capacity.

#### Scalability vs Elasticity — They're Different

These terms are often confused:

| Concept | Definition | Direction | Speed |
|---|---|---|---|
| **Scalability** | The **ability** to handle increased load by adding resources | Usually up/out | Can be slow (planned) |
| **Elasticity** | The ability to **automatically** scale up AND down in response to real-time demand changes | Both up AND down | Must be fast (real-time) |

Scalability answers: "Can it handle more?" Elasticity answers: "Does it automatically adjust to demand?"

A system can be scalable but not elastic (you can add servers manually, but it doesn't auto-scale). Cloud computing provides both.

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

#### Deep Dive: Key Cloud Challenges

**1. Vendor Lock-In**

Vendor lock-in occurs when an organisation becomes so dependent on a specific cloud provider's proprietary services that switching to another provider becomes prohibitively expensive or technically difficult.

| Lock-in Type | Example | Mitigation |
|---|---|---|
| **Data lock-in** | Petabytes of data in AWS S3 — egress fees make migration expensive | Use open data formats, multi-cloud data strategy |
| **API lock-in** | Application uses AWS DynamoDB, Azure Cosmos DB — no equivalent on other platforms | Use open-source alternatives (PostgreSQL, MongoDB) |
| **Platform lock-in** | Serverless functions in AWS Lambda tied to AWS event sources | Use containers (portable across clouds) instead |
| **Skill lock-in** | Team trained only on AWS — retraining for Azure/GCP is expensive | Cross-train, use cloud-agnostic tools (Terraform, K8s) |

**2. Security Concerns**

Cloud security follows the **Shared Responsibility Model** — the provider secures the infrastructure, but the customer is responsible for securing their data, applications, and access.

```
┌──────────────────────────────────────────────┐
│          Customer Responsibility              │
│  Data, Applications, Identity, Access Control │
│  OS patching (IaaS), Encryption, Firewalls    │
├──────────────────────────────────────────────┤
│          Provider Responsibility              │
│  Physical security, Network infrastructure    │
│  Hypervisor, Storage systems, Data centres    │
└──────────────────────────────────────────────┘
```

Common cloud security risks: misconfigured storage buckets (publicly accessible S3 buckets have caused major data breaches), weak identity and access management (overly permissive IAM roles), unencrypted data at rest or in transit, and insider threats at the provider.

**3. Cost Management — The "Cloud Surprise Bill"**

Cloud costs can spiral out of control without proper governance:

| Cost Trap | What Happens | Prevention |
|---|---|---|
| **Zombie resources** | VMs, databases, load balancers left running but unused | Regular audits, auto-shutdown for dev environments |
| **Over-provisioning** | Using large instances when small ones suffice | Right-sizing, usage monitoring |
| **Data egress fees** | Downloading large datasets from cloud = expensive | Architecture that minimises cross-region data transfer |
| **Reserved vs On-Demand** | Paying on-demand prices for steady workloads | Use Reserved Instances or Savings Plans for predictable load |
| **Runaway auto-scaling** | Auto-scaling configured without spending caps | Set budget alerts and maximum instance limits |

**4. Migration Complexity**

Moving existing applications to the cloud (cloud migration) is not trivial. The "7 R's of Migration" framework:

| Strategy | Description | Effort | Example |
|---|---|---|---|
| **Rehost** ("Lift and shift") | Move as-is to cloud VMs | Low | Move on-prem VM to EC2 |
| **Replatform** ("Lift, tinker, shift") | Minor optimisations during migration | Medium | Move to managed database (RDS) |
| **Repurchase** | Replace with SaaS product | Medium | Replace on-prem email with Gmail |
| **Refactor** | Re-architect for cloud-native | High | Rewrite monolith as microservices |
| **Retire** | Decommission unused applications | None | Turn off legacy app nobody uses |
| **Retain** | Keep on-premises (not suitable for cloud) | None | Mainframe applications |
| **Relocate** | Move to different cloud provider | Medium | VMware on-prem to VMware Cloud |

### Notable Cloud Failures and Outages

Cloud is not infallible. Major outages demonstrate the importance of multi-region and multi-cloud architectures:

| Incident | Date | What Happened | Impact | Lesson Learned |
|---|---|---|---|---|
| **AWS S3 Outage** | Feb 2017 | A typo in a command during routine maintenance took down S3 in US-East-1. The engineer accidentally removed more servers than intended. | Thousands of websites and services went offline for ~4 hours, including Slack, Quora, and Trello. The internet appeared to be "broken." | Never rely on a single region. A single human error can cascade across thousands of customers. AWS added safeguards to prevent accidental mass-deletion. |
| **Azure Active Directory Outage** | Mar 2021 | A key rotation issue caused Azure Active Directory to go down globally. Authentication for Microsoft 365, Teams, and Azure portal failed. | Millions of users couldn't log into Microsoft services. Businesses couldn't access email, documents, or cloud resources for hours. | Identity services are a single point of failure. Implement backup authentication mechanisms and cached credentials. |
| **Google Cloud Outage** | Nov 2021 | A misconfiguration in Google's network load balancing caused widespread failures across multiple GCP services. | Google Cloud Console, Cloud Functions, BigQuery, and other services were affected. Customers experienced errors for several hours. | Even the most sophisticated infrastructure teams make configuration mistakes. Automated validation of network changes is essential. |
| **AWS US-East-1 Outage** | Dec 2021 | Networking issues in the US-East-1 region caused cascading failures across multiple AWS services including EC2, ECS, Lambda, and DynamoDB. | Major services disrupted — Netflix, Disney+, Slack, Imgur. Duration: ~7 hours. | US-East-1 is the "default" region for many services. Distribute workloads across multiple regions. Avoid dependencies on a single AZ or region. |
| **Fastly CDN Outage** | Jun 2021 | A single customer's configuration change triggered a bug in Fastly's CDN, bringing down major websites. | Reddit, Amazon, The Guardian, BBC, Stack Overflow went offline simultaneously for ~1 hour. | CDN is a critical dependency. Have fallback mechanisms. Test configuration changes in staging. |

**Key Takeaways from Cloud Failures:**
1. **Design for failure** — assume any component can fail and build redundancy.
2. **Multi-region deployment** — don't put all workloads in one region.
3. **Multi-cloud strategy** — consider using multiple providers for critical systems.
4. **Test disaster recovery** — regularly test failover and backup restoration.
5. **Monitor dependencies** — understand your dependency chain (CDN, DNS, identity, etc.).

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
