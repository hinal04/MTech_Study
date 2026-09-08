# CS5: IaaS Services — Questions & Answers

> 10 questions covering: IAM (users/groups/roles/policies, least privilege, MFA), EC2 (instance families, pricing), Auto Scaling, ELB, VPC (subnets, security groups), S3 (storage classes), EBS, RDS, DynamoDB, CloudWatch, messaging services.

---

### Q1. What is IAM? Explain users, groups, roles, and policies.

**Answer:**

**IAM (Identity and Access Management)** controls who can do what on which cloud resources.

| Concept | What it is | Example |
|---|---|---|
| **User** | Person or app with credentials | "developer-alice" |
| **Group** | Collection of users with same permissions | "developers" group |
| **Role** | Set of permissions assumable by users/services — not tied to a person | "EC2-S3-ReadOnly" role assumed by an EC2 instance |
| **Policy** | JSON document defining allowed/denied actions on resources | Allow s3:GetObject on my-bucket |

**Principle of Least Privilege:** Grant only minimum permissions needed. If web app only reads from S3, give it only `s3:GetObject` — not admin access.

---

### Q2. What are EC2 instance families? When would you use each?

**Answer:**

| Family | Optimised for | Use case | Example |
|---|---|---|---|
| **General Purpose (t3, m6i)** | Balanced CPU/memory | Web servers, small DBs, dev environments | t3.medium |
| **Compute Optimised (c6i)** | High CPU | Batch processing, gaming, scientific modelling | c6i.xlarge |
| **Memory Optimised (r6i)** | Large memory | In-memory DBs (Redis), real-time analytics | r6i.2xlarge |
| **Storage Optimised (i3)** | High I/O | Data warehousing, distributed filesystems | i3.xlarge |
| **Accelerated (p4d, g5)** | GPU | ML training, video encoding, 3D rendering | p4d.24xlarge |

Choose based on workload profile: CPU-bound → compute optimised, memory-bound → memory optimised, I/O-bound → storage optimised, ML → accelerated.

---

### Q3. Explain Auto Scaling. What are the four types of scaling?

**Answer:**

**Auto Scaling** automatically adjusts EC2 instance count based on demand.

| Type | How it works | Example |
|---|---|---|
| **Target tracking** | Maintain a target metric | "Keep average CPU at 50%" |
| **Step scaling** | Add/remove in steps by threshold | "CPU>70% → +2, CPU>90% → +5" |
| **Scheduled** | Scale at specific times | "10 instances at 9 AM, 2 at 10 PM" |
| **Predictive** | ML predicts demand from history | AWS pre-scales for Monday morning spike |

**Setup:** Define launch template + scaling policies + min/max/desired counts. Auto Scaling monitors CloudWatch metrics and acts automatically.

---

### Q4. What is a VPC? Draw the architecture with public and private subnets.

**Answer:**

**VPC (Virtual Private Cloud)** is an isolated virtual network you define in the cloud.

```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24)
│   ├── Web servers, Load Balancer
│   └── Internet Gateway → Internet
├── Private Subnet (10.0.2.0/24)
│   ├── Application servers
│   └── NAT Gateway → Internet (outbound only)
└── Private Subnet (10.0.3.0/24)
    ├── Database servers
    └── No internet access
```

| Component | Purpose |
|---|---|
| **Subnets** | Public (internet-facing) vs Private (internal only) |
| **Security Groups** | Instance-level firewall (stateful) |
| **Network ACLs** | Subnet-level firewall (stateless) |
| **Internet Gateway** | Connects public subnet to internet |
| **NAT Gateway** | Allows private instances to reach internet (outbound only) |

---

### Q5. Compare S3 storage classes. When would you use each?

**Answer:**

| Class | Access pattern | Retrieval time | Cost | Use case |
|---|---|---|---|---|
| **S3 Standard** | Frequent | Instant | Highest | Active data, websites |
| **S3 Intelligent-Tiering** | Varying | Instant | Auto-optimised | Unknown access patterns |
| **S3 Standard-IA** | Infrequent (>30 days) | Instant | Lower storage, higher retrieval | Backups, DR |
| **S3 Glacier** | Archive | Minutes to hours | Very low | Long-term archival |
| **S3 Glacier Deep Archive** | Rarely accessed | 12+ hours | Lowest | Regulatory archives |

**Rule:** Frequently accessed → Standard. Infrequently → IA. Archival → Glacier. Unknown → Intelligent-Tiering.

---

### Q6. Compare object storage (S3), block storage (EBS), and file storage (EFS).

**Answer:**

| Aspect | S3 (Object) | EBS (Block) | EFS (File) |
|---|---|---|---|
| **Access method** | HTTP/REST API | Attached to VM as disk | NFS mount by multiple VMs |
| **Unit** | Objects (file + metadata + key) | Blocks (raw disk sectors) | Files and directories |
| **Scalability** | Virtually unlimited | Limited per volume (up to 64 TB) | Auto-scales |
| **Performance** | High throughput, higher latency | Low latency, high IOPS | Moderate |
| **Persistence** | Independent of any instance | Persists across instance stops | Independent |
| **Multi-attach** | Any number of readers | One instance (usually) | Multiple instances |
| **Use case** | Media, backups, data lakes, websites | Databases, OS boot volumes | Shared app data, content mgmt |

---

### Q7. What is Amazon RDS? What are its key features?

**Answer:**

**RDS (Relational Database Service)** is a managed relational database — the provider handles backups, patching, replication, failover.

**Supported engines:** MySQL, PostgreSQL, Oracle, SQL Server, MariaDB, Amazon Aurora.

**Key features:**
1. **Automatic backups** with point-in-time recovery.
2. **Multi-AZ deployment** for HA (synchronous replication to standby in another AZ).
3. **Read replicas** for scaling read-heavy workloads.
4. **Auto-scaling storage** — grows as data grows.
5. **Automatic patching** — database engine updates applied without manual intervention.

**vs self-managed DB on EC2:** RDS handles all the operational burden (backups, patching, replication). On EC2, you do everything yourself — but have more control.

---

### Q8. Compare RDS and DynamoDB. When would you choose each?

**Answer:**

| Aspect | RDS | DynamoDB |
|---|---|---|
| **Type** | Relational (SQL) | NoSQL (key-value + document) |
| **Schema** | Fixed (tables, rows, columns) | Flexible (each item can have different attributes) |
| **Query language** | SQL | API-based (GetItem, Query, Scan) |
| **Scaling** | Vertical (bigger instance) + read replicas | Horizontal (auto-partitions across nodes) |
| **Latency** | Milliseconds | Single-digit milliseconds at any scale |
| **Best for** | Complex queries, joins, transactions | Simple lookups, high throughput, key-value access |

**Choose RDS:** Complex relational data, SQL queries, ACID transactions (banking, ERP).
**Choose DynamoDB:** High-throughput simple access patterns, serverless, gaming leaderboards, IoT, session stores.

---

### Q9. What is CloudWatch? What can it monitor and what actions can it trigger?

**Answer:**

**CloudWatch** is AWS's monitoring and observability service.

**What it monitors:**
- EC2 metrics (CPU, memory, disk, network)
- RDS metrics (connections, IOPS, latency)
- Custom application metrics
- Log files (aggregate, search, analyse)

**What it can trigger:**
- **Auto Scaling** actions (add/remove instances when CPU exceeds threshold)
- **SNS notifications** (email/SMS alerts when errors spike)
- **Lambda functions** (automated remediation when issues detected)
- **Dashboard updates** (real-time visualisation)

**Example:** CloudWatch alarm: "If average EC2 CPU > 80% for 5 minutes → trigger Auto Scaling to add 2 instances AND send SNS email to ops team."

---

### Q10. Explain the difference between SQS, SNS, and SES.

**Answer:**

| Service | Type | Pattern | Use case |
|---|---|---|---|
| **SQS** (Simple Queue Service) | Message **queue** | Point-to-point: sender → queue → one receiver | Decouple components. Order processing — web tier queues orders, worker processes them. |
| **SNS** (Simple Notification Service) | **Pub/sub** messaging | One-to-many: publisher → topic → multiple subscribers | Fan-out notifications. Alert mobiles, send emails, trigger Lambda when an event occurs. |
| **SES** (Simple Email Service) | **Email** sending | Sender → SES → recipient inboxes | Transactional emails (order confirmations), marketing campaigns. |

**SQS vs SNS:** SQS is pull-based (receiver polls queue). SNS is push-based (message pushed to all subscribers). They're often used together: SNS publishes to multiple SQS queues for parallel processing.

---

*End of CS5 Questions & Answers*
