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
- [3.7 Storage Services](#37-storage-services)
- [3.8 Data Services](#38-data-services)
- [3.9 Big Data and Analytics Services](#39-big-data-and-analytics-services)
- [Additional AWS Services (from Lecture Notes)](#additional-aws-services)

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

*End of CS5*
