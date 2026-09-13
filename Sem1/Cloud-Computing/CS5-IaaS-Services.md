# CS5: IaaS Services — Compute, Storage, Network, and Data Services

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:** T1: Ch2 | T2: Ch3 | R8 (AWS Documentation)
>
> **Contact Hours:** 15-16 (Handout Topics 3.5-3.9)
> **Class Slide:** CS5 - IAAS
> **Lecture Notes:** Lecture 5 & 6 - IaaS (AWS white papers)

---

## Table of Contents

- [3.5 Identity and Access Management (IAM)](#35-identity-and-access-management-iam)
- [3.6 Compute Services](#36-compute-services)
  - [EC2 Instance Types & Pricing](#361-amazon-ec2-elastic-compute-cloud)
  - [AMI Types](#ami-types)
  - [EC2 Instance Lifecycle](#ec2-instance-lifecycle)
  - [EC2 Tenancy Options](#ec2-tenancy-options)
  - [EC2 Placement Groups](#ec2-placement-groups)
  - [Auto Scaling](#362-auto-scaling)
  - [Elastic Load Balancing](#363-elastic-load-balancing-elb)
  - [Amazon VPC](#364-amazon-vpc-virtual-private-cloud)
- [3.7 Storage Services](#37-storage-services)
  - [Amazon S3](#371-amazon-s3-simple-storage-service)
  - [Amazon EBS](#372-amazon-ebs-elastic-block-store)
  - [Amazon EFS](#373-amazon-efs-elastic-file-system)
  - [Amazon Glacier](#374-amazon-s3-glacier--archival-storage-deep-dive)
  - [EBS vs EFS vs S3 Comparison](#ebs-vs-efs-vs-s3--comprehensive-comparison-table)
- [3.8 Data Services](#38-data-services)
  - [Amazon RDS](#381-amazon-rds-relational-database-service)
  - [Amazon DynamoDB](#382-amazon-dynamodb)
  - [Amazon ElastiCache](#383-amazon-elasticache)
- [3.9 Big Data and Analytics Services](#39-big-data-and-analytics-services)
- [Additional AWS Services (from Lecture Notes)](#additional-aws-services)
- [Case Study: Zomato on AWS](#case-study-zomato-on-aws)

---

## 3.5 Identity and Access Management (IAM)

### What is IAM?

**Identity and Access Management (IAM)** is the security framework that controls **who** (identity) can do **what** (access) on **which resources** in a cloud environment. It's the gatekeeper of your entire cloud infrastructure.

### Core IAM Concepts

| Concept | What it is | Example |
|---|---|---|
| **User** | A person or application that interacts with cloud resources. Has credentials (username/password or access keys). | "developer-alice" — a developer who needs access to EC2 and S3. |
| **Group** | A collection of users with the same permissions. Simplifies permission management. | "developers" group — all members get the same access policy. |
| **Role** | A set of permissions that can be **assumed** by users, services, or applications. Not tied to a specific person. | "EC2-S3-ReadOnly" role — an EC2 instance assumes this role to read from S3 without hardcoded credentials. |
| **Policy** | A JSON document that defines what actions are allowed or denied on which resources. Attached to users, groups, or roles. | `{"Effect": "Allow", "Action": "s3:GetObject", "Resource": "arn:aws:s3:::my-bucket/*"}` |
| **Permissions** | The actual allowed/denied actions. Determined by evaluating all attached policies. | User alice can read S3 but cannot delete EC2 instances. |

### The Principle of Least Privilege

The fundamental IAM security principle: **grant only the minimum permissions necessary** for a user or service to perform its intended function. Nothing more.

**Example:** A web application that reads product images from S3 should have a role with ONLY `s3:GetObject` permission on the specific bucket — NOT `s3:*` on all buckets, and definitely NOT `*:*` (full admin access).

**Why it matters:** If credentials are compromised, the damage is limited to what those credentials can access. Full admin credentials leaked = entire cloud account compromised.

### AWS IAM Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::product-images",
        "arn:aws:s3:::product-images/*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "*"
    }
  ]
}
```

This policy: allows reading objects and listing the "product-images" bucket, but explicitly denies deleting ANY object in ANY bucket.

### Multi-Factor Authentication (MFA)

IAM supports MFA — requiring a second authentication factor (phone app code, hardware token) in addition to the password. **Always enable MFA for root and admin accounts.**

---

## 3.6 Compute Services

### 3.6.1 Amazon EC2 (Elastic Compute Cloud)

*(From Lecture 5-6)*

EC2 is the core IaaS compute service — it provides resizable virtual machines (instances) in the cloud. EC2 is arguably the most important AWS service and the foundation upon which much of modern cloud computing is built.

**How EC2 revolutionised computing:**

Before EC2 (launched in 2006), if you needed a server, you had to:
1. **Plan capacity** months in advance (guess how much you'd need)
2. **Purchase hardware** — servers, networking, storage ($10,000–$100,000+ per server)
3. **Wait for delivery** — 4–12 weeks for procurement and shipping
4. **Rack and cable** — physical installation in a data centre
5. **Install and configure** — OS, security patches, middleware, application
6. **Maintain** — hardware failures, upgrades, cooling, power

With EC2, the entire process takes **minutes** via an API call or web console click. You pay only for what you use (per-second billing since 2017), and you can terminate the server when you're done.

| Aspect | Before EC2 (Traditional) | With EC2 (Cloud IaaS) |
|---|---|---|
| **Time to provision** | 4–12 weeks | 2–5 minutes |
| **Upfront cost** | $10,000–$100,000+ per server | $0 (pay-as-you-go) |
| **Billing model** | Buy the entire server (CapEx) | Per-second or per-hour (OpEx) |
| **Scaling** | Buy more servers (weeks) | API call to add instances (minutes) |
| **Over-provisioning risk** | Buy too many servers → waste money | Scale down → stop paying |
| **Under-provisioning risk** | Buy too few → can't handle traffic | Scale up instantly |
| **Hardware maintenance** | Your responsibility (24/7 ops team) | AWS handles everything |

**Key characteristics:**
- Provision new server instances in **minutes** (not weeks).
- Choose from hundreds of **instance types** (varying CPU, memory, storage, GPU configurations).
- **Full OS control** — install any software, configure any service.
- Pay only for capacity you **actually use** (per-second billing for Linux, per-hour for Windows).
- Build **failure-resilient** applications by spreading instances across Availability Zones.
- **Elastic** — scale from 1 instance to thousands, and back down, automatically.

**EC2 Pricing Models:**

| Pricing Model | How it Works | Savings vs On-Demand | Best For |
|---|---|---|---|
| **On-Demand** | Pay per second/hour, no commitment. Start and stop anytime. | Baseline (0%) | Short-term, unpredictable workloads, testing |
| **Reserved Instances (RI)** | Commit to 1 or 3 years for a specific instance type. Pay upfront, partial upfront, or no upfront. | Up to **72%** | Steady-state, predictable workloads (databases, base web servers) |
| **Savings Plans** | Commit to a $/hour spend for 1 or 3 years. More flexible than RIs — applies across instance types. | Up to **72%** | Similar to RIs but with more flexibility |
| **Spot Instances** | Bid on spare AWS capacity. AWS can reclaim instances with 2-minute warning. | Up to **90%** | Fault-tolerant workloads (batch jobs, data analysis, CI/CD, rendering) |
| **Dedicated Hosts** | Physical server dedicated to your account. Per-host billing. | Varies | License compliance (per-socket licensing), regulatory requirements |

**Instance families:**

| Family | Optimised for | Use case | Example type | Typical Specs |
|---|---|---|---|---|
| **General Purpose (t3, m6i)** | Balanced CPU, memory, network | Web servers, small databases, dev environments, microservices | t3.medium | 2 vCPU, 4 GB RAM |
| **Compute Optimised (c6i, c7g)** | High-performance CPU | Batch processing, gaming servers, scientific modelling, HPC, video encoding | c6i.xlarge | 4 vCPU, 8 GB RAM |
| **Memory Optimised (r6i, x2idn)** | Large memory | In-memory databases (Redis, Memcached), real-time analytics, SAP HANA | r6i.2xlarge | 8 vCPU, 64 GB RAM |
| **Storage Optimised (i3, d3)** | High sequential I/O | Data warehousing, distributed file systems (HDFS), log processing | i3.xlarge | 4 vCPU, 30.5 GB RAM, NVMe SSD |
| **Accelerated (p4d, g5, inf2)** | GPU/custom silicon | Machine learning training, video encoding, 3D rendering, ML inference | p4d.24xlarge | 8 NVIDIA A100 GPUs, 96 vCPU, 1152 GB RAM |

**Reading an instance type name:**

```
  m   6   i   .   xlarge
  │   │   │       │
  │   │   │       └── Size (nano, micro, small, medium, large, xlarge, 2xlarge, ...)
  │   │   └── Additional info (i=Intel, g=Graviton, a=AMD, n=networking optimised)
  │   └── Generation (higher = newer and better price-performance)
  └── Family (m=general, c=compute, r=memory, i=storage, p=GPU)
```

**Example:** `c7g.2xlarge` = Compute Optimised (c), 7th generation (7), Graviton processor (g), 2× extra-large size.

### AMI Types

An **Amazon Machine Image (AMI)** is a template that contains the OS, application server, and applications required to launch an instance. Think of an AMI as a **snapshot of a configured server** — everything needed to reproduce that exact server state. There are four ways to obtain an AMI:

| AMI Type | Description | What it Contains | When to Use | Cost |
|---|---|---|---|---|
| **AWS Published AMIs** | Default OS images provided and maintained by AWS. Clean, minimal installations with only the OS and essential packages. | Base OS (Amazon Linux, Ubuntu, Windows Server, Red Hat, SUSE), security patches, basic drivers | Starting fresh — you want full control over what gets installed. Good for learning and custom builds. | Free (you only pay for the EC2 instance) |
| **AWS Marketplace AMIs** | Pre-configured images created by third-party vendors with licensed software already installed and configured. | OS + commercial software stack (e.g., WordPress on Apache, SAP HANA, Cisco CSR, Splunk, Fortinet firewall) | You need commercial or complex software stacks without the effort of manual installation and licensing. | **Additional per-hour or per-month software fees** on top of EC2 instance cost |
| **Generated from Existing Instances** | Create a custom AMI from a running EC2 instance. Captures everything: OS, installed packages, configurations, application code, data at that point in time. | Exact replica of the source instance's root volume — every file, every config, every installed package | **The most common approach in enterprises.** Build a "golden image" with approved software, security hardening, and company configs → launch all future instances from it. Also used for disaster recovery — snapshot before risky changes. | Free to create (you pay for storage of the AMI in S3) |
| **VM Import/Export** | Import existing virtual machine images from your on-premises environment (VMware, Hyper-V, VirtualBox) into AWS as AMIs. | The exact VM you were running on-premises, converted to an AWS-compatible format | Migrating existing on-premises workloads to AWS without rebuilding from scratch. Also used for compliance — maintaining the exact same software stack in cloud as on-prem. | Free for the import process (you pay for resulting storage and instances) |

**Supported import formats:** RAW, VHD (Hyper-V), VMDK (VMware), OVA (Open Virtualization Archive).

**The "Golden AMI" pattern — how enterprises use AMIs:**

```
Step 1: Start with AWS Published AMI (e.g., Amazon Linux 2)
          ↓
Step 2: Install required packages (Java, monitoring agents, security tools)
          ↓
Step 3: Apply security hardening (disable SSH root login, configure firewall rules)
          ↓
Step 4: Install company certificates and configure logging
          ↓
Step 5: Create AMI from this instance → "Golden AMI v1.0"
          ↓
Step 6: All teams launch instances from this Golden AMI
          ↓
Step 7: Monthly update cycle → create "Golden AMI v1.1" with latest patches
```

This ensures **consistency** — every server in the organisation starts from the same base, with the same security settings and monitoring tools. No "snowflake servers" where each one was configured slightly differently.

### EC2 Instance Lifecycle

Every EC2 instance moves through a defined set of **states** from creation to termination:

```
  ┌──────────┐     ┌─────────┐     ┌──────────┐     ┌─────────┐     ┌────────────┐
  │ Pending  │────▶│ Running │────▶│ Stopping │────▶│ Stopped │────▶│ Terminated │
  │(launching)│     │         │◀────│          │     │         │     │            │
  └──────────┘     └─────────┘     └──────────┘     └─────────┘     └────────────┘
                        │                                                  ▲
                        │              ┌──────────────┐                    │
                        └─────────────▶│ Shutting-down │───────────────────┘
                        (can terminate  └──────────────┘
                         directly)
```

| State | Description | Billing | What Happens to Data |
|---|---|---|---|
| **Pending** | Instance is being launched — hypervisor allocating resources, loading AMI, configuring networking. | **Not billed.** | N/A — instance is being created |
| **Running** | Instance is active, accessible, and serving requests. You can SSH/RDP into it. | **Billed** (per-second for Linux, per-hour for Windows). | Instance store and EBS volumes are active |
| **Stopping** | Instance is transitioning to stopped state. Takes a few seconds. | **Not billed** for compute once stopped. | Instance store data is **lost**. EBS data persists. |
| **Stopped** | Instance is shut down but still exists. EBS volumes preserved. Can be restarted. | **Not billed** for compute. EBS storage still billed. | EBS data preserved. Instance store data is **gone forever**. |
| **Shutting-down** | Instance is being terminated. Transitional state. | Not billed. | Data being cleaned up |
| **Terminated** | Instance is **permanently deleted**. Cannot be restarted. | Not billed. | All instance store data is **permanently erased**. EBS volumes may persist if "Delete on Termination = No". |

**Key lifecycle concepts:**

#### Bootstrapping with User Data

**Bootstrapping** is the process of providing code (shell scripts, configuration commands) to run on an instance **at launch time**. This is done via **User Data** — a script that executes automatically when the instance first boots.

**Example User Data script:**
```bash
#!/bin/bash
# This script runs automatically when the instance launches
yum update -y                              # Update all packages
yum install -y httpd                       # Install Apache web server
systemctl start httpd                      # Start the web server
systemctl enable httpd                     # Start on boot
echo "Hello from $(hostname)" > /var/www/html/index.html
```

**Why bootstrapping matters:** Without bootstrapping, after launching an EC2 instance from an AMI, you'd need to manually SSH in and configure it. With bootstrapping, the instance configures itself automatically — essential for auto-scaling (you can't manually configure instances that are created automatically at 3 AM during a traffic spike).

**Bootstrapping vs Golden AMI:**

| Approach | Speed | Flexibility | Maintenance |
|---|---|---|---|
| **Golden AMI** (everything pre-baked) | Fastest boot — everything already installed | Less flexible — need a new AMI for each configuration change | Must rebuild AMI for every update |
| **User Data bootstrapping** (install at boot) | Slower boot — packages downloaded and installed each time | Very flexible — change the script to change the config | Easy to update — just edit the script |
| **Hybrid** (Golden AMI + light bootstrapping) | Fast boot with minor customisation at launch | Best of both worlds | Recommended for production |

#### Tags for Management

**Tags** are key-value pairs attached to instances (and other AWS resources) for **management, automation, cost allocation, and identification**.

**Essential tags for any organisation:**

| Tag Key | Example Value | Purpose |
|---|---|---|
| `Name` | `web-server-prod-01` | Human-readable identifier |
| `Environment` | `Production` / `Staging` / `Dev` | Identify which environment |
| `Team` | `Backend` / `Frontend` / `DevOps` | Track which team owns the resource |
| `Project` | `OrderService` / `PaymentGateway` | Group resources by project |
| `CostCenter` | `CC-1234` | Allocate costs to business units |

**Why tags matter:** Without tags, when you have 500 EC2 instances, you can't tell which team owns which instance, which project it belongs to, or whether it's a production or test server. Tags make resources manageable at scale and enable automated actions (e.g., "shut down all instances tagged `Environment: Dev` at 7 PM to save costs").

#### Terminated ≠ Stopped — A Critical Distinction

| Action | Can Restart? | EBS Data | Instance Store Data | IP Address |
|---|---|---|---|---|
| **Stop** | ✅ Yes — restart anytime | ✅ Preserved | ❌ Lost | Elastic IP preserved; public IP changes |
| **Terminate** | ❌ No — **gone forever** | ⚠️ Depends on "Delete on Termination" setting | ❌ Lost permanently | Released |

**Termination protection:** You can enable **termination protection** on critical instances. This prevents accidental termination — the instance cannot be terminated via the console or API until protection is explicitly disabled. Always enable this for production instances.

### EC2 Tenancy Options

Tenancy defines how EC2 instances are placed on **physical hardware** in AWS data centres. This is important for compliance, licensing, and performance requirements.

| Tenancy Model | Description | Cost | Use case |
|---|---|---|---|
| **Shared Tenancy** (default) | A single physical host machine may house instances from **different AWS customers**. Each instance is **fully isolated** at the hypervisor level — no customer can access another's data or resources. | Lowest cost | Most workloads. The isolation is strong enough for the vast majority of applications. |
| **Dedicated Instances** | Your instances run on hardware that is **dedicated to your AWS account**. No other customer's instances share the same physical host. However, different instances from your own account may share the host. | Higher cost (~2× On-Demand price) | Compliance or regulatory requirements that mandate single-tenant hardware (e.g. HIPAA, government workloads). |
| **Dedicated Host** | An entire **physical server** is fully dedicated to your use. You get visibility into sockets, cores, and host ID. You control instance placement on the host. | Highest cost | **Server-bound software licenses** (e.g. Windows Server, SQL Server, Oracle) that require licensing per physical socket or core. Also used for strict compliance needs. |

**Analogy:** Think of it like booking a hotel:
- **Shared Tenancy** = staying in a hotel where other guests share the building, but each room is locked and soundproofed (shared hardware, isolated VMs)
- **Dedicated Instance** = booking a private floor of the hotel — only your group on that floor, but the hotel manages everything (dedicated hardware for your account)
- **Dedicated Host** = renting the entire building — you decide which rooms to use and how to arrange them (full control over the physical server)

**When to use Dedicated Hosts specifically:**

Many enterprise software licenses (Oracle, SQL Server, Windows Server) are tied to the **number of physical CPU sockets or cores** on the server. With shared tenancy, you don't know how many sockets the physical server has, so you can't prove compliance with per-socket licensing. Dedicated Hosts give you visibility into the physical hardware, allowing you to:
- Count exactly how many sockets and cores your software runs on
- Bring Your Own License (BYOL) from on-premises to cloud
- Provide audit evidence for license compliance

### EC2 Placement Groups

**Placement Groups** let you control how EC2 instances are physically placed within AWS infrastructure. There are three strategies, each optimised for different workload patterns:

| Strategy | How Instances are Placed | Benefit | Limitation | Use Case |
|---|---|---|---|---|
| **Cluster** | All instances packed into the **same rack** (or very close racks) in a single AZ. | **Lowest network latency** — instances can communicate at up to 10 Gbps with enhanced networking. | Single point of failure — if the rack fails, all instances fail. Cannot span AZs. | HPC (High Performance Computing), tightly coupled distributed systems, real-time data feeds |
| **Spread** | Each instance placed on **different underlying hardware** (different racks) with independent power and networking. Max 7 instances per AZ per group. | **Highest availability** — a hardware failure affects only one instance. | Max 7 instances per AZ (limited scale). | Critical instances that must not fail together — e.g., primary and secondary database nodes, master nodes in a cluster |
| **Partition** | Instances divided into **partitions** (logical groups), each partition on separate hardware. Partitions don't share racks with other partitions. Up to 7 partitions per AZ. | **Balance of scale and fault tolerance** — a rack failure affects one partition, not all. Can have hundreds of instances. | More complex to manage than simple spread. | Large distributed workloads — HDFS, HBase, Cassandra, Kafka (each partition = one replica set) |

**Visual representation:**

```
CLUSTER (same rack, lowest latency):
┌─────────────────────────┐
│  Rack A                 │
│  [VM1] [VM2] [VM3] [VM4]│
│  ← 10 Gbps between VMs →│
└─────────────────────────┘

SPREAD (different racks, highest availability):
┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐
│ Rack A │  │ Rack B │  │ Rack C │  │ Rack D │
│ [VM1]  │  │ [VM2]  │  │ [VM3]  │  │ [VM4]  │
└────────┘  └────────┘  └────────┘  └────────┘
  (if Rack A fails, only VM1 is affected)

PARTITION (grouped by partition, racks isolated):
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│ Partition 1  │  │ Partition 2  │  │ Partition 3  │
│ Rack A+B     │  │ Rack C+D     │  │ Rack E+F     │
│ [VM1][VM2]   │  │ [VM3][VM4]   │  │ [VM5][VM6]   │
│ [VM3]        │  │ [VM5]        │  │ [VM7]        │
└──────────────┘  └──────────────┘  └──────────────┘
  (if Rack A fails, only Partition 1 VMs on Rack A are affected)
```

### 3.6.2 Auto Scaling

*(From Lecture 5-6)*

Auto Scaling automatically adjusts the number of EC2 instances based on demand conditions you define.

**How it works:**
1. Define a **launch template** (which instance type, AMI, security groups).
2. Set **scaling policies** (e.g. "add 2 instances when CPU > 80% for 5 minutes").
3. Set **min/max/desired** instance counts (e.g. min=2, max=20, desired=4).
4. Auto Scaling monitors CloudWatch metrics and adds/removes instances automatically.

**Types of scaling:**

| Type | How it works | Example |
|---|---|---|
| **Target tracking** | Maintain a target metric value (e.g. keep average CPU at 50%). | "Scale so that average CPU stays at 50%." |
| **Step scaling** | Add/remove instances in steps based on alarm thresholds. | "If CPU > 70%, add 2. If CPU > 90%, add 5." |
| **Scheduled scaling** | Scale at specific times based on known patterns. | "Scale to 10 instances at 9 AM, back to 2 at 10 PM." |
| **Predictive scaling** | ML-based prediction of future demand based on historical patterns. | AWS predicts Monday morning traffic spike and pre-scales. |

### 3.6.3 Elastic Load Balancing (ELB)

*(From Lecture 5-6)*

ELB automatically distributes incoming application traffic across multiple EC2 instances for fault tolerance and scalability.

**Types of load balancers:**

| Type | Layer | Best for |
|---|---|---|
| **Application Load Balancer (ALB)** | Layer 7 (HTTP/HTTPS) | Web applications, microservices, path-based routing |
| **Network Load Balancer (NLB)** | Layer 4 (TCP/UDP) | Ultra-low latency, millions of requests/sec |
| **Gateway Load Balancer (GWLB)** | Layer 3 | Third-party virtual appliances (firewalls, IDS) |

**Key features:**
- **Health checks:** Automatically detects unhealthy instances and routes traffic only to healthy ones.
- **Cross-AZ:** Distribute traffic across instances in multiple Availability Zones.
- **SSL termination:** Handle HTTPS encryption/decryption at the load balancer, not at each instance.

### 3.6.4 Amazon VPC (Virtual Private Cloud)

*(From Lecture 5-6)*

VPC lets you provision a **logically isolated** section of the AWS Cloud where you launch resources in a virtual network you define. Think of a VPC as your **own private data centre inside AWS** — you control the IP address ranges, create subnets, configure route tables, and set up network gateways.

**Why VPC matters:** Without VPC, all your EC2 instances would be on a shared flat network with every other AWS customer. VPC gives you an isolated network boundary — your resources communicate within your VPC, and you explicitly control what traffic enters and leaves.

**Key components:**

| Component | What it Does | Analogy |
|---|---|---|
| **VPC** | Your isolated virtual network. You define the IP address range (CIDR block, e.g., `10.0.0.0/16` = 65,536 IP addresses). | The walls around your private campus |
| **Subnets** | Subdivisions of a VPC tied to specific AZs. **Public subnets** have routes to the internet. **Private subnets** do not. | Buildings within your campus — some face the street (public), some are internal only (private) |
| **Internet Gateway (IGW)** | Connects your VPC to the public internet. Attached to the VPC. Resources in public subnets use it to reach the internet. | The main gate of your campus |
| **NAT Gateway** | Allows instances in **private subnets** to access the internet (for software updates, API calls) **without** being accessible from the internet. One-way door — traffic goes out but can't come in. | A mail room — your internal staff can send mail out, but outsiders can't walk in through the mail room |
| **Route Tables** | Rules that determine where network traffic is directed. Each subnet is associated with a route table. | Road signs in your campus telling traffic where to go |
| **Security Groups** | **Instance-level** firewall. Stateful — if you allow inbound traffic, the response is automatically allowed out. Specify rules by port, protocol, and source IP/security group. | A bouncer at each building's door — checks who can enter |
| **Network ACLs (NACLs)** | **Subnet-level** firewall. Stateless — you must explicitly allow both inbound AND outbound traffic. Rules are evaluated in order (lowest number first). | A gate guard at the campus entrance — checks all traffic in both directions |
| **VPC Peering** | Connects two VPCs so instances in each can communicate using private IP addresses (no internet traversal). Can be cross-account or cross-region. | A private tunnel between two campuses |

#### Security Groups vs NACLs — Key Differences

| Feature | Security Group | Network ACL (NACL) |
|---|---|---|
| **Scope** | Instance level (attached to ENI) | Subnet level |
| **Statefulness** | **Stateful** — return traffic automatically allowed | **Stateless** — must explicitly allow return traffic |
| **Rules** | Allow rules only (implicit deny) | Allow AND Deny rules |
| **Rule evaluation** | All rules evaluated together | Rules evaluated in order (lowest number first, first match wins) |
| **Default** | Denies all inbound, allows all outbound | Allows all inbound and outbound |
| **Association** | Must be explicitly assigned to instances | Automatically applies to all instances in the subnet |

**Practical example:** You want a web server to accept HTTP traffic (port 80) from anywhere. With a Security Group, you add an inbound rule allowing port 80 from `0.0.0.0/0`. Because Security Groups are stateful, the HTTP response traffic is automatically allowed out. With a NACL, you'd need TWO rules: one allowing inbound port 80, AND another allowing outbound ephemeral ports (1024-65535) for the response.

**Architecture example:**
```
VPC (10.0.0.0/16)
│
├── Public Subnet (10.0.1.0/24) — AZ-a
│   ├── Internet Gateway → Internet
│   ├── ALB (Application Load Balancer)
│   └── NAT Gateway (for private subnet internet access)
│
├── Private Subnet (10.0.2.0/24) — AZ-a
│   ├── Application servers (EC2)
│   └── Route: 0.0.0.0/0 → NAT Gateway (outbound internet only)
│
├── Private Subnet (10.0.3.0/24) — AZ-a
│   ├── Database servers (RDS)
│   └── No internet route (fully isolated)
│
├── Public Subnet (10.0.4.0/24) — AZ-b  ← (multi-AZ mirror)
│   ├── ALB (cross-AZ)
│   └── NAT Gateway (redundant)
│
├── Private Subnet (10.0.5.0/24) — AZ-b
│   └── Application servers (EC2)
│
└── Private Subnet (10.0.6.0/24) — AZ-b
    └── Database servers (RDS standby)
```

**VPC design best practices:**
- **Multi-AZ subnets** — create matching public and private subnets in at least 2 AZs for high availability
- **Separate tiers into subnets** — web tier (public), application tier (private), database tier (private, no internet)
- **Use NAT Gateway, not NAT Instance** — NAT Gateway is managed, scalable, and highly available
- **Restrict Security Group rules** — never use `0.0.0.0/0` for SSH (port 22); restrict to your IP range
- **Enable VPC Flow Logs** — log all traffic metadata for security monitoring and troubleshooting

---

## 3.7 Storage Services

### Three Types of Cloud Storage

Understanding the three fundamental storage types is critical — each is designed for different access patterns, and choosing wrong impacts both performance and cost.

| Type | How Data is Accessed | Data Structure | Characteristics | AWS Service |
|---|---|---|---|---|
| **Object Storage** | Via HTTP/REST APIs. Each object has a unique key (like a URL path). No hierarchy — flat namespace. | Objects = file + metadata + key | Massively scalable (unlimited), highly durable (11 nines), accessed over the network, eventually consistent for overwrites | **Amazon S3** |
| **Block Storage** | Attached to a VM as a virtual disk drive. Accessed exactly like a local hard drive — raw blocks read/written. | Fixed-size blocks (512 bytes to 64 KB) | Lowest latency, highest IOPS, persistent across instance stops, formatted with any file system (ext4, NTFS) | **Amazon EBS** |
| **File Storage** | Shared file system mounted by multiple VMs simultaneously via NFS/SMB protocol. | Standard file/directory hierarchy (POSIX) | Shared concurrent access, supports file locking, auto-scales capacity | **Amazon EFS** |

**Analogy for the three types:**
- **Block Storage (EBS)** = your laptop's internal SSD. Fast, directly attached, only you can use it, you format it however you want.
- **File Storage (EFS)** = a shared network drive at your office. Everyone mounts the same drive and sees the same files. Multiple people can read/write simultaneously.
- **Object Storage (S3)** = Google Drive / Dropbox. You upload and download files via a web interface or API. You don't "mount" it as a drive — you access objects by their key (URL). Unlimited storage, accessible from anywhere.

### 3.7.1 Amazon S3 (Simple Storage Service)

*(From Lecture 5-6)*

S3 is object storage designed for storing and retrieving **any amount of data**, at **any time**, from **anywhere** on the web. It's one of the oldest and most widely used AWS services (launched in 2006) and underpins much of the internet — Netflix, Airbnb, and NASA all store data in S3.

**Key concepts:**
- **Bucket:** A container for objects (like a top-level folder). Name must be **globally unique** across all AWS accounts worldwide. Buckets are created in a specific Region.
- **Object:** A file + metadata + unique key. Each object can be up to **5 TB** in size. The key is the full path within the bucket (e.g., `images/product/shirt-blue.jpg`).
- **Flat namespace:** S3 doesn't have real directories. Folder-like paths (`images/product/`) are just key prefixes — the console displays them as folders for convenience, but the underlying storage is flat.
- **Durability:** S3 Standard provides **99.999999999% durability** (11 nines). This means if you store 10,000,000 objects, you can expect to lose a single object once every 10,000 years. Data is automatically replicated across a **minimum of 3 Availability Zones**.
- **Availability:** S3 Standard provides **99.99% availability** (about 52 minutes of downtime per year).

**S3 Storage classes:** Different tiers optimised for different access patterns and cost profiles.

| Storage Class | Access Frequency | Min Storage Duration | Retrieval Fee | Cost (Storage) | Use Case |
|---|---|---|---|---|---|
| **S3 Standard** | Frequent (daily) | None | None | Highest | Active data, websites, content distribution, big data analytics |
| **S3 Intelligent-Tiering** | Varying/unknown | None | None (small monitoring fee) | Auto-optimised | Data with unpredictable access patterns — S3 automatically moves data between frequent and infrequent tiers |
| **S3 Standard-IA** (Infrequent Access) | Infrequent (monthly) | 30 days | Per-GB retrieval fee | Lower storage | Backups, disaster recovery copies, long-lived data accessed occasionally |
| **S3 One Zone-IA** | Infrequent | 30 days | Per-GB retrieval fee | Even lower | Easily reproducible data (thumbnails, transcoded media) — stored in only 1 AZ |
| **S3 Glacier Instant Retrieval** | Rare (quarterly) | 90 days | Per-GB retrieval | Very low | Medical images, news media archives — need millisecond access when retrieved |
| **S3 Glacier Flexible Retrieval** | Rare (1-2× per year) | 90 days | Per-GB + per-request | Very low | Archive data retrieved in minutes to hours |
| **S3 Glacier Deep Archive** | Very rare (1× per year or less) | 180 days | Per-GB + per-request | Lowest | Regulatory archives (7-10 year retention), magnetic tape replacement |

**S3 Key Features:**

| Feature | What it Does | Why it Matters |
|---|---|---|
| **Versioning** | Keeps all versions of an object (including deleted ones). Each upload creates a new version ID. | Protection against accidental deletion and overwrites. Can restore any previous version. |
| **Lifecycle Policies** | Automatically move objects between storage classes or delete them based on age rules. | Cost optimisation — e.g., move logs to Glacier after 30 days, delete after 365 days. |
| **Pre-signed URLs** | Generate a temporary URL that grants time-limited access to a private object. No need to make the object public. | Share files securely — e.g., generate a download link valid for 1 hour for a customer's invoice. |
| **Cross-Region Replication (CRR)** | Automatically replicate objects to a bucket in a different Region. | Disaster recovery, latency reduction for globally distributed users, compliance with geographic data requirements. |
| **S3 Event Notifications** | Trigger Lambda functions, SQS queues, or SNS topics when objects are created, deleted, or modified. | Event-driven architecture — e.g., automatically generate thumbnails when an image is uploaded. |
| **Server-Side Encryption** | Encrypt objects at rest using AES-256. Options: SSE-S3 (AWS manages keys), SSE-KMS (you manage keys), SSE-C (customer-provided keys). | Data protection and compliance. |

**Analogy:** S3 is like a massive warehouse with unlimited shelf space. Each bucket is a warehouse section, and each object is a labelled box on a shelf. You find boxes by their label (key), not by walking through aisles. You can choose premium shelving (Standard) for items you grab daily, or deep storage vaults (Glacier) for items you might not touch for years — the vault is much cheaper but takes longer to retrieve from.

### 3.7.2 Amazon EBS (Elastic Block Store)

*(From Lecture 5-6)*

EBS provides **block-level storage volumes** for EC2 instances. Think of it as a virtual hard drive (SSD or HDD) that you attach to your VM — the EC2 instance sees it as a local disk and can format it with any file system (ext4, NTFS, XFS).

**Key characteristics:**
- **Network-attached** — EBS volumes exist on the network, not physically inside the EC2 host. They persist independently from the EC2 instance lifecycle (survives stop/start, can be detached and reattached to a different instance).
- **Single-AZ scope** — an EBS volume exists in a **single Availability Zone** and can only be attached to EC2 instances in the same AZ.
- **Snapshot capable** — create point-in-time backups (snapshots) that are stored in S3 across multiple AZs. Snapshots can be used to restore volumes or create new volumes in different AZs/Regions.
- **Encryption** — supports AES-256 encryption at rest and in transit between the volume and the instance. Encryption uses AWS KMS keys and is transparent to the application.

**EBS Volume Types — Choosing the Right One:**

| Volume Type | Technology | Max IOPS | Max Throughput | Cost | Best For |
|---|---|---|---|---|---|
| **gp3** (General Purpose SSD) | SSD | 16,000 | 1,000 MB/s | Low-medium | **Default choice.** Boot volumes, dev/test, small-medium databases, virtual desktops |
| **gp2** (General Purpose SSD, prev gen) | SSD | 16,000 | 250 MB/s | Low-medium | Legacy workloads (gp3 is recommended for new workloads — same price, better performance) |
| **io2 Block Express** (Provisioned IOPS SSD) | SSD | **256,000** | 4,000 MB/s | High | **Mission-critical databases** (Oracle, SAP HANA, SQL Server), workloads needing guaranteed consistent IOPS |
| **io1** (Provisioned IOPS SSD, prev gen) | SSD | 64,000 | 1,000 MB/s | High | Legacy high-IOPS workloads |
| **st1** (Throughput Optimised HDD) | HDD | 500 | 500 MB/s | Low | **Sequential reads** — big data, data warehousing, log processing, ETL pipelines |
| **sc1** (Cold HDD) | HDD | 250 | 250 MB/s | Lowest | Infrequently accessed data, cold storage, archival (cheapest EBS option) |

**When to use SSD vs HDD:**
- **SSD (gp3, io2):** Random access patterns — databases, boot volumes, transactional applications (where you need to read/write small blocks at random positions on the disk)
- **HDD (st1, sc1):** Sequential access patterns — streaming large files, log processing, data warehousing (where you read/write large blocks in order)

**EBS Snapshots:**
- **Incremental** — only blocks that changed since the last snapshot are saved, saving storage cost and time
- **Stored in S3** — automatically replicated across multiple AZs for durability
- **Can be shared** — share snapshots across AWS accounts or make them public
- **Cross-Region copy** — copy snapshots to other Regions for disaster recovery
- **Create volumes from snapshots** — restore a volume from any snapshot, even in a different AZ

### 3.7.3 Amazon EFS (Elastic File System)

*(From Lecture 5-6)*

EFS is a fully managed **file storage service** that provides a shared file system accessible from **multiple EC2 instances simultaneously** via the NFS (Network File System) protocol.

**Analogy:** If EBS is like a USB drive plugged into one computer, EFS is like a shared network drive (NAS) that everyone in the office can access at the same time.

**Key characteristics:**
- **Shared access** — multiple EC2 instances (even hundreds) across different AZs can mount the same EFS file system simultaneously. This is the key differentiator from EBS (which is single-instance).
- **POSIX-compliant** — supports standard file system semantics (permissions, directory hierarchy, locking). Applications that work with local file systems work with EFS without modification.
- **Auto-scaling** — EFS automatically grows and shrinks as you add or remove files. No need to pre-provision capacity. Start at 0 bytes, scale to petabytes.
- **Regional service** — data is automatically replicated across **multiple AZs** within the Region for durability and availability.
- **Lifecycle management** — automatically move files not accessed for 30 days (configurable) to EFS Infrequent Access (IA) storage class at a lower cost.

**EFS Performance Modes:**

| Mode | Throughput | Latency | Use Case |
|---|---|---|---|
| **General Purpose** (default) | Scales with file system size | Lowest latency | Web serving, content management, home directories |
| **Max I/O** | Highest aggregate throughput | Higher latency per operation | Big data analytics, media processing, highly parallelised workloads |

**When to use EFS:**
- **Shared content** — multiple web servers serving the same content (e.g., WordPress media uploads shared across an auto-scaling group)
- **Machine learning** — shared training data accessed by multiple GPU instances simultaneously
- **Home directories** — shared home directories for developers on multiple EC2 instances
- **Container storage** — shared persistent storage for ECS or EKS containers

### 3.7.4 Amazon S3 Glacier — Archival Storage Deep Dive

*(From Lecture 5-6)*

Glacier is AWS's archival storage service, designed for data that must be retained long-term but is rarely accessed. It's the cloud equivalent of **moving boxes from your office desk to a secure warehouse** — extremely cheap to store, but it takes time and costs more to retrieve.

**Glacier Retrieval Options:**

| Retrieval Tier | Time to Retrieve | Cost | Use Case |
|---|---|---|---|
| **Expedited** | **1–5 minutes** | Highest retrieval cost | Urgent access to archived data (e.g., doctor needs archived medical records immediately) |
| **Standard** | **3–5 hours** | Medium retrieval cost | Default retrieval — acceptable for most business needs |
| **Bulk** | **5–12 hours** | Lowest retrieval cost (or free for Deep Archive) | Large-scale retrieval of many files — cost-sensitive batch processing |

**Glacier Deep Archive** has even slower retrieval:
- **Standard retrieval:** 12 hours
- **Bulk retrieval:** 48 hours
- **Cost:** As low as $0.00099 per GB/month (~$1/TB/month) — cheaper than storing data on physical tape

**Vault Lock for Compliance:**

Glacier Vault Lock enforces compliance controls on a vault using a **lockable policy** — once locked, the policy **cannot be changed or deleted** by anyone, including the root account.

**Why this matters:** Regulations like SEC Rule 17a-4 (financial records), HIPAA (medical records), and GDPR require that certain data be retained for specific periods and that deletion be prevented. Vault Lock ensures that even if an administrator's credentials are compromised, the archived data cannot be deleted or modified before the retention period expires.

**Example:** A bank must retain all trading records for 7 years per SEC regulations. They store the records in a Glacier vault with a Vault Lock policy that prevents any deletion for 7 years. Even the AWS root account cannot override this policy once locked.

### 3.7.5 Amazon CloudFront (CDN)

*(From Lecture 5-6)*

CloudFront is a **Content Delivery Network** — it caches content at 600+ edge locations worldwide for low-latency delivery to end users.

**How it works:** User requests content → CloudFront routes to nearest edge location → if cached, return immediately (cache hit) → if not cached, fetch from origin (S3/EC2), cache it, then return.

### EBS vs EFS vs S3 — Comprehensive Comparison Table

| Aspect | **EBS** | **EFS** | **S3** |
|---|---|---|---|
| **Storage type** | Block storage | File storage (NFS) | Object storage |
| **Access method** | Attached to a **single EC2 instance** as a virtual disk drive | **Mounted** by multiple EC2 instances simultaneously via NFS | Accessed via **HTTP/REST API** from anywhere (no mount required) |
| **Multi-instance access** | ❌ No (one instance at a time) | ✅ Yes (hundreds of instances) | ✅ Yes (unlimited concurrent API access) |
| **Performance** | Lowest latency. io2 delivers up to **256,000 IOPS**. Best for transactional workloads. | Scales throughput automatically. Good for parallel workloads. Higher latency than EBS. | High throughput for large objects. Not suited for low-latency transactional access. |
| **Pricing model** | Pay for **provisioned capacity** (GB/month) + IOPS (for io2). You pay even if the volume is empty. | Pay for **storage used** (GB/month). No pre-provisioning needed. Grows/shrinks automatically. | Pay for **storage used** + **requests** (GET, PUT) + **data transfer out**. Cheapest at scale. |
| **Durability** | 99.999% (replicated within a single AZ). Use snapshots for cross-AZ protection. | 99.999999999% (11 nines). Data replicated across **multiple AZs** automatically. | 99.999999999% (11 nines). Data replicated across **≥3 AZs** automatically. |
| **AZ scope** | **Single AZ** — volume locked to one AZ | **Regional** — accessible from any AZ within the Region | **Regional** — accessible from anywhere via API |
| **Capacity** | Volume up to 64 TB (must be pre-provisioned) | Petabyte-scale, auto-grows (no pre-provisioning) | Unlimited total, 5 TB per object |
| **File system support** | ext4, XFS, NTFS (any block-level FS) | NFS v4 (POSIX-compliant) | No file system — key-value object store |
| **Encryption** | ✅ At rest (KMS) + in transit | ✅ At rest (KMS) + in transit | ✅ At rest (SSE-S3/KMS/C) + in transit (HTTPS) |
| **Backup** | Snapshots (stored in S3) | AWS Backup or cross-Region replication | Versioning + Cross-Region Replication |
| **Use cases** | Boot volumes, databases, transactional apps | Shared content, ML training data, CMS, home directories | Backups, data lakes, static websites, media distribution, archival |

**Decision guide — which storage to use:**

```
Do you need a file system mounted as a disk?
├── YES → Does the storage need to be shared across multiple instances?
│         ├── YES → Use EFS
│         └── NO  → Use EBS
└── NO  → Do you need API-based access to objects from anywhere?
          └── YES → Use S3
```

---

## 3.8 Data Services

### 3.8.1 Amazon RDS (Relational Database Service)

*(From Lecture 5-6)*

RDS is a **fully managed relational database service** that handles the undifferentiated heavy lifting of database administration — backups, patching, replication, failover, and hardware provisioning. You focus on your data and queries; AWS manages the infrastructure.

**What "managed" means — RDS vs self-managed database on EC2:**

| Task | Self-Managed DB on EC2 | Amazon RDS |
|---|---|---|
| OS installation and patching | You | AWS |
| Database engine installation | You | AWS |
| Database patching | You | AWS (automated maintenance windows) |
| Backups | You (write scripts, manage storage) | AWS (automated daily snapshots + transaction logs) |
| High availability (failover) | You (configure replication, write failover scripts) | AWS (one-click Multi-AZ deployment) |
| Scaling storage | You (add EBS volumes, resize, manage LVM) | AWS (auto-scaling storage, one-click) |
| Monitoring | You (install and configure monitoring tools) | AWS (CloudWatch integration, Enhanced Monitoring) |
| Security patching | You (stay on top of CVEs, test patches, apply) | AWS (automated with configurable windows) |

**Supported database engines:**

| Engine | Type | Key Characteristics |
|---|---|---|
| **Amazon Aurora** | MySQL/PostgreSQL compatible | AWS-built engine, **5× faster than MySQL**, **3× faster than PostgreSQL**, auto-scales up to 128 TB, 15 read replicas |
| **MySQL** | Open-source | Most popular open-source DB, wide community support, good for web applications |
| **PostgreSQL** | Open-source | Advanced features (JSON, geospatial, full-text search), ACID-compliant, excellent for complex queries |
| **MariaDB** | Open-source | MySQL fork with enhanced features, community-driven, drop-in MySQL replacement |
| **Oracle** | Commercial | Enterprise-grade, complex business applications, strong PL/SQL support |
| **SQL Server** | Commercial | Microsoft ecosystem, strong Windows/.NET integration, Business Intelligence tools |

**RDS High Availability — Multi-AZ Deployment:**

```
                    ┌─────────────────┐
   Write requests  →│  PRIMARY         │
   Read requests   →│  (AZ-a)         │──── Synchronous ────▶┌─────────────────┐
                    │  Active DB       │     replication       │  STANDBY         │
                    └─────────────────┘                       │  (AZ-b)         │
                           │                                  │  Passive (no     │
                           │ (if Primary fails)               │  traffic served) │
                           └──────────── Automatic ──────────▶│  Promoted to     │
                                         failover             │  Primary         │
                                         (60-120 sec)         └─────────────────┘
```

- **Synchronous replication** — every write to the primary is immediately replicated to the standby. Zero data loss on failover.
- **Automatic failover** — if the primary instance fails, AWS automatically promotes the standby to primary. DNS record updated automatically. Typically completes in **60–120 seconds**.
- **No code changes** — your application connects to a DNS endpoint, not an IP address. The failover is transparent to the application.

**RDS Read Replicas — Scaling Reads:**

| Feature | Multi-AZ (High Availability) | Read Replica (Read Scaling) |
|---|---|---|
| **Purpose** | Protect against AZ failure | Scale read-heavy workloads |
| **Replication** | Synchronous (zero data loss) | Asynchronous (slight lag possible) |
| **Failover** | Automatic (standby promoted) | Manual promotion if needed |
| **Read traffic** | Not served by standby | ✅ Serves read queries |
| **Max replicas** | 1 standby | Up to 5 (15 for Aurora) |
| **Cross-Region** | No (same Region only) | ✅ Yes (cross-Region read replicas) |

**Use case example:** An e-commerce application with a MySQL database. The primary database handles all writes (orders, inventory updates). Three read replicas handle read queries (product searches, user profile views, report generation). This distributes 80% of the database load (reads) across replicas, keeping the primary fast for writes.

**RDS Automated Backups:**
- **Daily snapshots** — automated full backup of the database during the maintenance window
- **Transaction logs** — backed up every 5 minutes, enabling point-in-time recovery
- **Retention period** — configurable from 1 to 35 days (default: 7 days)
- **Point-in-time recovery** — restore to any second within the retention period (e.g., restore to the state at exactly 2:34:17 PM yesterday)

### 3.8.2 Amazon DynamoDB

*(From Lecture 5-6)*

DynamoDB is a fully managed **NoSQL** (key-value + document) database.

**Key characteristics:**
- Single-digit millisecond latency at any scale.
- Stored on SSDs, replicated across 3 AZs automatically.
- Serverless — no servers to manage, auto-scales throughput.
- Pay per request or provisioned capacity.

**Use cases:** Gaming leaderboards, IoT device data, session management, shopping carts.

### 3.8.3 Amazon ElastiCache

*(From Lecture 5-6)*

ElastiCache is a managed **in-memory caching** service supporting Redis and Memcached.

**Purpose:** Speed up application performance by caching frequently accessed data in memory instead of hitting the database every time.

**Example:** An e-commerce site caches product catalog in ElastiCache. Instead of querying the database for every page view (50ms), it reads from cache (1ms). Database load drops by 90%.

---

## 3.9 Big Data and Analytics Services

### 3.9.1 Amazon CloudSearch

*(From Lecture 5-6)*

Fully managed search service for adding search functionality to websites and applications. Supports text search, faceting, filtering, and sorting across large collections of documents.

### 3.9.2 Amazon CloudWatch

*(From Lecture 5-6)*

CloudWatch is the monitoring and observability service for AWS resources and applications.

**What it monitors:**
- EC2 instance metrics (CPU, memory, disk, network).
- RDS database metrics (connections, IOPS, latency).
- Custom application metrics.
- Log aggregation and analysis.

**Key features:**
- **Alarms** — trigger actions (Auto Scaling, SNS notifications) when metrics exceed thresholds.
- **Dashboards** — visualise metrics in real-time.
- **Logs Insights** — query and analyse log data.

### 3.9.3 Messaging and Integration Services

| Service | What it does | Use case |
|---|---|---|
| **Amazon SQS** (Simple Queue Service) | Fully managed message queue. Decouple application components. | Order processing — web tier sends orders to queue, worker tier processes them independently. |
| **Amazon SNS** (Simple Notification Service) | Push messaging — send notifications to multiple subscribers simultaneously. | Alert mobile devices, send emails, trigger Lambda functions when events occur. |
| **Amazon SES** (Simple Email Service) | Scalable email sending service. | Transactional emails (order confirmations, password resets), marketing campaigns. |

### 3.9.4 AWS Elastic Beanstalk

*(From Lecture 5-6)*

Beanstalk is a PaaS-like service built on IaaS — you upload your application code, and Beanstalk automatically handles deployment, capacity provisioning, load balancing, auto-scaling, and health monitoring.

**Supported languages:** Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker.

**Key distinction:** Unlike pure PaaS, Beanstalk gives you **full access to underlying AWS resources** (EC2 instances, load balancers, security groups). You can take control of any component at any time.

---

## Additional AWS Services (from Lecture Notes)

### Networking Services

| Service | Purpose |
|---|---|
| **Route 53** | Highly available DNS service. Translates domain names to IP addresses. Supports latency-based and geolocation routing. |
| **AWS Import/Export** | Physical data transfer using portable storage devices for very large datasets where internet transfer is too slow. |

### Complete AWS Service Map (for exam reference)

```
COMPUTE          STORAGE           NETWORKING         DATABASE
─────────        ─────────         ──────────         ────────
EC2              S3                VPC                RDS
Auto Scaling     EBS               ELB                DynamoDB
Lambda           EFS               Route 53           ElastiCache
Elastic          Glacier           CloudFront         Redshift
Beanstalk        Import/Export     Direct Connect

MONITORING       MESSAGING         SECURITY           DEPLOYMENT
──────────       ─────────         ────────           ──────────
CloudWatch       SQS               IAM                CloudFormation
CloudTrail       SNS               KMS                Elastic Beanstalk
                 SES               Shield             CodeDeploy
                                   WAF                CodePipeline
```

---

## Case Study: Zomato on AWS

### Company Overview

**Zomato** is India's leading restaurant-discovery and food-delivery platform, serving **70+ million daily users** across **24 countries**. At this scale, even small efficiency improvements in infrastructure translate into massive cost savings. Zomato processes millions of restaurant searches, real-time delivery tracking updates, and payment transactions every day — all of which require substantial compute power.

### The Challenge

Zomato's data analytics platform relied on **Trino** (distributed SQL query engine, formerly PrestoSQL) and **Druid** (real-time analytics database) clusters running on traditional **x86-based EC2 instances** (Intel and AMD processors). As the user base grew exponentially (especially during COVID-19 pandemic-driven food delivery demand), compute costs scaled linearly — the analytics infrastructure was becoming a significant and growing expense.

**Specific problems:**
- **Rising compute costs** — Trino clusters alone cost hundreds of thousands of dollars per year
- **Over-provisioned capacity** — x86 instances had more capacity than needed for many workloads, but the next size down was too small
- **Performance bottlenecks** — analytics queries on large datasets were slow, affecting data-driven decision making
- **Inflexible pricing** — On-Demand pricing for always-on analytics clusters was expensive, and not all workloads required 100% uptime

### The Solution

Zomato migrated their Trino and Druid clusters from **x86 EC2 instances** to **AWS Graviton2-based (Arm architecture) EC2 instances**, combined with strategic use of Spot Instances:

| Strategy | What Zomato Did | Why It Worked |
|---|---|---|
| **Graviton2 migration** | Switched from x86 instances (e.g., `m5.xlarge`) to Graviton2 instances (e.g., `m6g.xlarge`). Graviton2 is AWS's custom Arm-based processor designed for cloud workloads. | Graviton2 provides **up to 40% better price-performance** than comparable x86 instances. Same workload, lower cost, better performance. |
| **EC2 Spot Instances** | Used Spot Instances (spare AWS capacity at up to **90% discount**) for fault-tolerant analytics workloads. Trino queries can be restarted if a Spot Instance is reclaimed. | Analytics workloads are inherently tolerant to interruption — if a query is interrupted, it can simply be restarted. This made Spot Instances a natural fit. |
| **Instance right-sizing** | Analysed actual resource usage and selected Graviton2 instance sizes that better matched workload requirements. | Eliminated over-provisioning waste — not paying for CPU and memory that was never used. |

### Technical Details of the Migration

**Why Graviton2 (Arm) outperforms x86 for analytics:**
- Graviton2 uses a **custom Arm Neoverse N1** core design with large caches
- **64 cores per processor** (vs 32–48 for comparable Intel)
- Better **performance-per-watt** — more compute per dollar
- Optimised for throughput-heavy cloud workloads (exactly what Trino and Druid do)

**Migration approach:**
1. **Tested** Graviton2 instances with production-like workloads in a staging environment
2. **Verified** that Trino and Druid (Java-based) ran correctly on Arm (Java is cross-platform, so no code changes needed)
3. **Gradual rollout** — migrated cluster nodes one at a time, monitoring performance
4. **Optimised** — tuned JVM settings for Arm architecture for additional performance gains

### Results

| Metric | Improvement | Impact |
|---|---|---|
| **Compute cost reduction** | **30%** lower compute costs | Millions of dollars saved annually across all analytics infrastructure |
| **Query performance** | **25%** faster query execution | Data analysts get results faster, enabling quicker business decisions |
| **Annual savings (Trino alone)** | **~$300,000/year** saved | Significant budget freed up for product development |
| **Spot Instance savings** | Additional **60-90%** on fault-tolerant workloads | Further reduced costs beyond Graviton2 savings |
| **Carbon footprint** | Lower energy consumption (Graviton2 is more power-efficient) | Aligned with sustainability goals |

### Key Takeaways for Exam

This case study demonstrates several IaaS concepts in action:

1. **Instance type selection matters** — choosing Graviton2 (Arm) over x86 delivered both cost and performance benefits. This is why AWS offers so many instance families — the right choice for your workload can save 30%+ costs.

2. **Spot Instances** are a powerful cost-optimisation tool for workloads that can tolerate interruptions. Zomato's analytics queries could be restarted if interrupted, making them ideal Spot candidates. Not all workloads qualify — you wouldn't run a primary database on Spot.

3. **Cloud migration is iterative** — Zomato didn't just lift-and-shift. They migrated to the cloud, then **re-evaluated instance types** to find Graviton2, then added Spot Instances. Each iteration yielded additional savings.

4. **Right-sizing is continuous** — initial instance choices are often too large (fear of under-provisioning). Regularly analysing actual usage and downsizing saves money.

5. **Architecture matters for cost** — a well-architected cloud deployment (right instance types + right pricing models) can be dramatically cheaper than a naïve deployment using default instance types at On-Demand prices.

---

*End of CS5*
