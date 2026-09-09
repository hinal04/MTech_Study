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
  - [EC2 Instance Types](#361-amazon-ec2-elastic-compute-cloud)
  - [AMI Types](#ami-types)
  - [EC2 Instance Lifecycle](#ec2-instance-lifecycle)
  - [EC2 Tenancy Options](#ec2-tenancy-options)
  - [Auto Scaling](#362-auto-scaling)
  - [Elastic Load Balancing](#363-elastic-load-balancing-elb)
  - [Amazon VPC](#364-amazon-vpc-virtual-private-cloud)
- [3.7 Storage Services](#37-storage-services)
  - [EBS vs EFS vs S3 Comparison](#ebs-vs-efs-vs-s3--comparison-table)
- [3.8 Data Services](#38-data-services)
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

EC2 is the core IaaS compute service — it provides resizable virtual machines (instances) in the cloud.

**Key characteristics:**
- Provision new server instances in **minutes** (not weeks).
- Choose from hundreds of **instance types** (varying CPU, memory, storage, GPU configurations).
- **Full OS control** — install any software, configure any service.
- Pay only for capacity you **actually use** (per-second or per-hour billing).
- Build **failure-resilient** applications by spreading instances across Availability Zones.

**Instance families:**

| Family | Optimised for | Use case | Example type |
|---|---|---|---|
| **General Purpose (t3, m6i)** | Balanced CPU, memory, network | Web servers, small databases, dev environments | t3.medium (2 vCPU, 4 GB RAM) |
| **Compute Optimised (c6i)** | High-performance CPU | Batch processing, gaming servers, scientific modelling | c6i.xlarge (4 vCPU, 8 GB RAM) |
| **Memory Optimised (r6i)** | Large memory | In-memory databases (Redis), real-time analytics | r6i.2xlarge (8 vCPU, 64 GB RAM) |
| **Storage Optimised (i3)** | High sequential I/O | Data warehousing, distributed file systems | i3.xlarge (4 vCPU, 30.5 GB RAM, NVMe SSD) |
| **Accelerated (p4d, g5)** | GPU-powered | Machine learning training, video encoding, 3D rendering | p4d.24xlarge (8 NVIDIA A100 GPUs) |

### AMI Types

An **Amazon Machine Image (AMI)** is a template that contains the OS, application server, and applications required to launch an instance. There are four ways to obtain an AMI:

| AMI Type | Description | When to use |
|---|---|---|
| **AWS Published AMIs** | Default OS images provided by AWS (Amazon Linux, Ubuntu, Windows Server, etc.). Clean, minimal installations. | Starting fresh — you install and configure everything yourself. |
| **AWS Marketplace AMIs** | Pre-configured images with licensed software (e.g. WordPress, SAP, Cisco). **Incur additional per-hour or per-month fees** on top of the EC2 instance cost. | You need commercial or pre-built software stacks without manual setup. |
| **Generated from Existing Instances** | Create a custom AMI from a running EC2 instance. Captures the OS, installed software, configurations, and data at that point in time. | **Common for corporate standards** — configure one "golden image" with approved software and security settings, then launch all future instances from it. |
| **Uploaded Virtual Servers (VM Import/Export)** | Import virtual machine images from your on-premises environment into AWS. **Supported formats:** RAW, VHD, VMDK, OVA. | Migrating existing on-premises workloads to AWS without rebuilding from scratch. |

### EC2 Instance Lifecycle

Every EC2 instance moves through a defined set of **states** from creation to termination:

```
  ┌──────────┐     ┌─────────┐     ┌─────────┐     ┌────────────┐
  │ Pending  │────▶│ Running │────▶│ Stopped │────▶│ Terminated │
  │(launching)│     │         │◀────│         │     │            │
  └──────────┘     └─────────┘     └─────────┘     └────────────┘
                        │                                 ▲
                        └─────────────────────────────────┘
                              (can terminate directly)
```

| State | Description | Billing |
|---|---|---|
| **Pending** | Instance is being launched and provisioned. | Not billed. |
| **Running** | Instance is active and accessible. | **Billed** (per-second or per-hour). |
| **Stopped** | Instance is shut down. EBS volumes are preserved, but instance store data is lost. | **Not billed** for compute (EBS storage still billed). |
| **Terminated** | Instance is permanently deleted. **Cannot be restarted.** All instance store volumes are erased. | Not billed. |

**Key lifecycle concepts:**

- **Bootstrapping:** The process of providing code (shell scripts, configuration commands) to run on an instance **at launch time**. Done via **User Data** — a script that executes automatically when the instance first boots. Example: installing packages, pulling application code from Git, starting services.
- **Tags:** Key-value pairs attached to instances for **management and organisation**. Essential for cost allocation, automation, and identifying resources. Example: `Environment: Production`, `Team: Backend`, `Project: OrderService`.
- **Terminated ≠ Stopped:** A stopped instance can be restarted. A **terminated instance is gone forever** — all associated instance store data is permanently lost. EBS volumes may persist if configured with "Delete on Termination = No".

### EC2 Tenancy Options

Tenancy defines how EC2 instances are placed on **physical hardware** in AWS data centres.

| Tenancy Model | Description | Cost | Use case |
|---|---|---|---|
| **Shared Tenancy** (default) | A single physical host machine may house instances from **different AWS customers**. Each instance is **fully isolated** at the hypervisor level — no customer can access another's data or resources. | Lowest cost | Most workloads. The isolation is strong enough for the vast majority of applications. |
| **Dedicated Instances** | Your instances run on hardware that is **dedicated to your AWS account**. No other customer's instances share the same physical host. However, different instances from your own account may share the host. | Higher cost | Compliance or regulatory requirements that mandate single-tenant hardware (e.g. HIPAA, government workloads). |
| **Dedicated Host** | An entire **physical server** is fully dedicated to your use. You get visibility into sockets, cores, and host ID. You control instance placement on the host. | Highest cost | **Server-bound software licenses** (e.g. Windows Server, SQL Server, Oracle) that require licensing per physical socket or core. Also used for strict compliance needs. |

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

VPC lets you provision a **logically isolated** section of the AWS Cloud where you launch resources in a virtual network you define.

**Key components:**

| Component | What it does |
|---|---|
| **VPC** | Your isolated virtual network. You define the IP address range (CIDR block). |
| **Subnets** | Subdivisions of a VPC. **Public subnets** have internet access. **Private subnets** do not. |
| **Internet Gateway** | Connects VPC to the internet (for public subnets). |
| **NAT Gateway** | Allows private subnet instances to access the internet (for updates) without being accessible from the internet. |
| **Route Tables** | Rules that determine where network traffic is directed. |
| **Security Groups** | Instance-level firewall (stateful). Specify allowed inbound/outbound traffic by port and source. |
| **Network ACLs** | Subnet-level firewall (stateless). Additional layer of security. |

**Architecture example:**
```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24) — web servers, load balancer
│   └── Internet Gateway → Internet
├── Private Subnet (10.0.2.0/24) — application servers
│   └── NAT Gateway → Internet (outbound only)
└── Private Subnet (10.0.3.0/24) — database servers
    └── No internet access
```

---

## 3.7 Storage Services

### Three Types of Cloud Storage

| Type | How data is accessed | Characteristics | AWS Service |
|---|---|---|---|
| **Object Storage** | Via HTTP/REST APIs. Each object has a unique key. | Massively scalable, eventually consistent (for overwrite), stores files as objects with metadata. | **Amazon S3** |
| **Block Storage** | Attached to a VM as a virtual disk drive. Accessed like a local hard drive. | Low latency, high IOPS, persistent across instance stops. | **Amazon EBS** |
| **File Storage** | Shared file system mounted by multiple VMs simultaneously via NFS/SMB. | Shared access, POSIX-compliant, concurrent reads/writes. | **Amazon EFS** |

### 3.7.1 Amazon S3 (Simple Storage Service)

*(From Lecture 5-6)*

S3 is object storage designed for storing and retrieving **any amount of data**, at **any time**, from **anywhere** on the web.

**Key concepts:**
- **Bucket:** A container for objects (like a top-level folder). Globally unique name.
- **Object:** A file + metadata + unique key. Up to 5 TB per object.
- **Storage classes:** Different tiers for different access patterns.

| Storage Class | Access frequency | Cost | Use case |
|---|---|---|---|
| **S3 Standard** | Frequent | Highest | Active data, websites, content distribution |
| **S3 Intelligent-Tiering** | Varying | Auto-optimised | Data with unknown or changing access patterns |
| **S3 Standard-IA** | Infrequent (min 30 days) | Lower storage, higher retrieval | Backups, disaster recovery |
| **S3 Glacier** | Archive (minutes-hours retrieval) | Very low | Long-term archival, compliance |
| **S3 Glacier Deep Archive** | Archive (12+ hours retrieval) | Lowest | Regulatory archives, magnetic tape replacement |

### 3.7.2 Amazon EBS (Elastic Block Store)

*(From Lecture 5-6)*

EBS provides **block-level storage volumes** for EC2 instances. Think of it as a virtual hard drive attached to your VM.

**Key characteristics:**
- **Network-attached** — persists independently from the EC2 instance lifecycle.
- **Snapshot capable** — create point-in-time backups stored in S3.
- **Encryption** — supports encryption at rest and in transit.
- **Volume types:** General Purpose SSD (gp3), Provisioned IOPS SSD (io2), Throughput Optimised HDD (st1), Cold HDD (sc1).

### 3.7.3 Amazon CloudFront (CDN)

*(From Lecture 5-6)*

CloudFront is a **Content Delivery Network** — it caches content at 600+ edge locations worldwide for low-latency delivery to end users.

**How it works:** User requests content → CloudFront routes to nearest edge location → if cached, return immediately (cache hit) → if not cached, fetch from origin (S3/EC2), cache it, then return.

### EBS vs EFS vs S3 — Comparison Table

| Aspect | **EBS** | **EFS** | **S3** |
|---|---|---|---|
| **Storage type** | Block storage | File storage (NFS) | Object storage |
| **Access pattern** | Attached to a **single EC2 instance** at a time (like a virtual hard drive). | **Shared** across multiple EC2 instances simultaneously via NFS mount. | Accessed via **HTTP/REST API** from anywhere (no mount required). |
| **Performance** | Lowest latency. Provisioned IOPS SSD (io2) delivers up to 64,000 IOPS. Best for transactional workloads. | Scales throughput automatically. Good for parallel workloads. Higher latency than EBS. | High throughput for large objects. Not suited for low-latency transactional access. |
| **Pricing model** | Pay for **provisioned capacity** (GB/month) + IOPS (for io2). You pay even if the volume is empty. | Pay for **storage used** (GB/month). No pre-provisioning needed. | Pay for **storage used** + **requests** (GET, PUT) + **data transfer out**. Cheapest at scale. |
| **Durability** | 99.999% (replicated within a single AZ). Snapshots stored in S3 for cross-AZ protection. | 99.999999999% (11 9s). Data replicated across **multiple AZs** automatically. | 99.999999999% (11 9s). Data replicated across **≥3 AZs** automatically. |
| **Availability Zone scope** | **Single AZ** — an EBS volume can only be attached to instances in the same AZ. | **Regional** — accessible from any AZ within the region. | **Regional** — accessible from anywhere via API. |
| **Max object/file size** | Volume up to 64 TB | Petabyte-scale filesystem, no per-file limit | 5 TB per object |
| **Use cases** | Boot volumes, databases (MySQL, PostgreSQL), transactional applications | Shared home directories, content management, media processing, ML training data | Backups, static website hosting, data lakes, media distribution, archival |

---

## 3.8 Data Services

### 3.8.1 Amazon RDS (Relational Database Service)

*(From Lecture 5-6)*

RDS is a managed relational database service. The provider handles backups, patching, replication, and failover — you focus on your data.

**Supported engines:** MySQL, PostgreSQL, Oracle, SQL Server, MariaDB, Amazon Aurora.

**Key features:**
- Automatic backups with point-in-time recovery.
- Multi-AZ deployment for high availability (synchronous replication to standby).
- Read replicas for scaling read-heavy workloads.
- Auto-scaling storage.

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

**Zomato** is India's leading restaurant-discovery and food-delivery platform, serving **70+ million daily users** across **24 countries**. At this scale, even small efficiency improvements in infrastructure translate into massive cost savings.

### The Challenge

Zomato's data analytics platform relied on **Trino** (distributed SQL query engine) and **Druid** (real-time analytics database) clusters running on traditional **x86-based EC2 instances**. As the user base grew, compute costs scaled linearly — the analytics infrastructure was becoming a significant expense.

### The Solution

Zomato migrated their Trino and Druid clusters from **x86 EC2 instances** to **AWS Graviton2-based (Arm architecture) EC2 instances**:

| Strategy | What Zomato did |
|---|---|
| **Graviton2 migration** | Switched compute workloads from x86 (Intel/AMD) instances to Graviton2 (Arm) instances, which offer better price-performance. |
| **EC2 Spot Instances** | Used Spot Instances (spare AWS capacity at up to 90% discount) for fault-tolerant analytics workloads to further reduce costs. |

### Results

| Metric | Improvement |
|---|---|
| **Compute cost reduction** | **30%** lower compute costs after Graviton2 migration |
| **Query performance** | **25%** improvement in query performance (Graviton2's architecture is more efficient for analytics workloads) |
| **Annual savings (Trino alone)** | **~$300,000/year** saved on the Trino cluster alone |

### Key Takeaway for Exam

This case study demonstrates several IaaS concepts in action:
- **Instance type selection matters** — choosing Graviton2 (Arm) over x86 delivered both cost and performance benefits.
- **Spot Instances** are a powerful cost-optimisation tool for workloads that can tolerate interruptions.
- Cloud migration isn't just lift-and-shift — **re-evaluating instance types** after migration can yield significant additional savings.

---

*End of CS5*
