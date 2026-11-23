# Session 9: Cloud Databases and NewSQL Systems

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [9.1 Cloud Database Fundamentals](#91-cloud-database-fundamentals)
- [9.2 Database-as-a-Service (DBaaS)](#92-database-as-a-service-dbaas)
- [9.3 Scaling Strategies](#93-scaling-strategies)
- [9.4 Replication and Consistency](#94-replication-and-consistency)
- [9.5 Sharding and Data Partitioning](#95-sharding-and-data-partitioning)
- [9.6 Multi-region Databases](#96-multi-region-databases)
- [9.7 NewSQL Systems](#97-newsql-systems)
- [9.8 CAP Theorem](#98-cap-theorem)

---

## 9.1 Cloud Database Fundamentals

### Why Cloud Databases?

Traditional on-premises databases require:
- Hardware procurement and setup
- System administration and maintenance
- Capacity planning and upgrades
- Backup and disaster recovery management
- Security and compliance implementation

**Cloud databases** provide:

| Benefit | Impact |
|---|---|
| **On-demand provisioning** | Deploy instances in minutes |
| **Resource elasticity** | Scale up/down based on demand |
| **Managed service** | Less operational overhead |
| **High availability** | Built-in replication and failover |
| **Geographic distribution** | Deploy near users globally |
| **Pay-per-use pricing** | No upfront capital investment |
| **Automated backups** | Data protection built-in |

### Cloud Database Characteristics

**Cloud-native architecture:**
- Designed for cloud environments from ground up
- Separation of compute and storage
- Stateless compute nodes
- Shared storage layer
- Horizontal scalability

**Traditional cloud-hosted:**
- Existing databases deployed on cloud VMs
- Maintains monolithic architecture
- Limited elasticity
- Familiar management model

### Key Deployment Models

| Model | Description | Examples |
|---|---|---|
| **IaaS** | Manage own database software | EC2 + PostgreSQL |
| **PaaS** | Managed database service | AWS RDS, Azure SQL |
| **Serverless** | Auto-scaling, pay-per-query | AWS Aurora Serverless |
| **Multi-cloud** | Avoid single-cloud vendor lock-in | CockroachDB, Vitess |

---

## 9.2 Database-as-a-Service (DBaaS)

### Shared Responsibility Model

Cloud DBaaS divides responsibilities between provider and customer:

| Layer | Cloud Provider | Customer |
|---|---|---|
| **Hardware** | Provision, maintenance, replacement | - |
| **OS & Patches** | Update, security patches | - |
| **Database Software** | Install, upgrade, patches | - |
| **Data** | - | Responsible for data quality |
| **Access Control** | Provide IAM tools | Configure user permissions |
| **Encryption** | Key infrastructure | Key management (optional) |
| **Backups** | Automated backups | Restore procedures, retention |
| **Compliance** | Infrastructure compliance | Data classification, audit logs |

### Managed Service Benefits

**Reduced administrative overhead:**
- No OS/database patching
- Automatic failover
- Automated backups
- Performance monitoring
- Capacity management

**Operational improvements:**
- Higher availability (SLA 99.95%+)
- Faster deployment
- Simplified scaling
- Built-in security features

### Managed Service Trade-offs

| Benefit | Trade-off |
|---|---|
| **Less maintenance** | Less control over configuration |
| **High availability** | Potential vendor lock-in |
| **Automated scaling** | Less predictable costs |
| **Built-in backups** | Limited customization |

---

## 9.3 Scaling Strategies

### Vertical Scaling (Scale Up)

**Increase resources on existing instance:**

```
Before: 4 vCPU, 16GB RAM, 1TB storage
After:  32 vCPU, 128GB RAM, 10TB storage
```

**Characteristics:**
- Simple to implement
- No application changes
- Requires downtime for most databases
- Hardware limitations (can only scale so much)
- Higher costs per unit of performance

**When to use:**
- Initial growth phase
- Moderate workloads
- Applications requiring single-instance consistency

### Horizontal Scaling (Scale Out)

**Add more database nodes:**

```
Single Node
    ↓
  Node 1 ← Primary
  Node 2 ← Replica
  Node 3 ← Replica
```

**Distribution mechanisms:**
1. **Read replicas**: Distribute read-only queries
2. **Sharding**: Distribute data via partition key
3. **Multi-master**: Multiple writable nodes

**Characteristics:**
- More complex to implement
- Application changes may be needed
- No downtime for scaling
- Near-unlimited horizontal scaling
- Cost-effective at scale

**When to use:**
- Very large datasets
- High query volume
- Global distribution needed
- Scale-out architectures preferred

### Elastic Scaling

**Automatically adjust resources based on demand:**

```
Time    Load      Resources
08:00   High      32 vCPU
09:00   Normal    8 vCPU
10:00   Peak      64 vCPU
11:00   Normal    8 vCPU
```

**Metrics triggering scale:**
- CPU utilization
- Memory usage
- Query queue length
- Connections count
- Storage usage

**Cloud implementations:**
- AWS Aurora Auto Scaling
- Azure Database for PostgreSQL flexible server
- Google Cloud SQL autoscaling

---

## 9.4 Replication and Consistency

### Replication Architectures

**Primary-Replica (Master-Slave):**
- One primary (writable)
- Multiple replicas (read-only)
- Asynchronous replication typically
- Simple to implement, good for scaling reads

```
Write → Primary → Replication Log → Replica1
                              ↓ Replica2
                              ↓ Replica3
Read ← Replica (any of them)
```

**Multi-Primary (Multi-Master):**
- Multiple writable nodes
- Each can accept writes
- Complex conflict resolution needed
- Used for geographic distribution

```
Write → Node 1 ↔ Replication ↔ Node 2 ← Write
        ↓                       ↓
      Replica 1                Replica 1
```

**Consensus-based:**
- Multiple nodes vote on data changes
- Requires majority agreement
- Strong consistency guaranteed
- Used in distributed systems (Raft, Paxos)

### Replication Consistency Models

**Synchronous Replication:**

```
Write Request
    ↓
Primary applies change
    ↓
Waits for replicas to confirm
    ↓
Returns to client
```

**Characteristics:**
- Strong consistency (read-after-write)
- Higher latency (waits for replicas)
- If replicas fail, write stalls
- Safe for critical data

**Asynchronous Replication:**

```
Write Request
    ↓
Primary applies change
    ↓
Returns to client immediately
    ↓
Sends to replicas in background
```

**Characteristics:**
- Higher performance (no wait for replicas)
- Eventual consistency (slight lag)
- Tolerates replica failures
- Risk of data loss on primary failure

### Replication Lag

**Lag** = time between write to primary and visibility on replica

**Causes:**
- Network latency
- Primary → replica queue
- Replica applying transactions
- Complex transactions taking time

**Problems created:**
- Read-after-write inconsistency (read from replica before update applied)
- Inconsistent reads (different replicas at different lag levels)
- Causality violations (reading effects before causes)

**Mitigation strategies:**
- Read from primary for critical data
- Sticky reads (same replica for session)
- Replication monitoring and alerts
- Acceptable lag SLAs in design

---

## 9.5 Sharding and Data Partitioning

### Sharding Concept

**Sharding** horizontally partitions data across multiple database instances:

```
Data
├─ Shard 1: Users A-G
├─ Shard 2: Users H-N
├─ Shard 3: Users O-T
└─ Shard 4: Users U-Z

Query: "Find user ALICE"
  → Hash/lookup shard key
  → Route to Shard 1
  → Execute query on Shard 1 only
```

### Sharding Keys

**Shard key** determines data distribution (like partition key).

**Good shard keys:**
- Relatively uniformly distributed
- Unlikely to change
- Used frequently in queries
- Enables range-based queries (optional)

**Common shard keys:**
- User ID (for user-centric apps)
- Customer ID (for multi-tenant systems)
- Geographic region (for location-based apps)
- Timestamp (for time-series data)

### Sharding Strategies

**Hash-based sharding:**
```
shard_number = hash(shard_key) % number_of_shards

Example: hash("user123") % 4 = 2 → Shard 2
```

**Characteristics:**
- Uniform distribution
- Requires rehashing on shard count changes
- Cannot do range queries easily

**Range-based sharding:**
```
if shard_key in [A-G]:    Shard 1
if shard_key in [H-N]:    Shard 2
if shard_key in [O-T]:    Shard 3
if shard_key in [U-Z]:    Shard 4
```

**Characteristics:**
- Enables range queries
- Can cause hot shards (uneven distribution)
- Easier to add/remove shards

**Directory-based sharding:**
```
Shard Directory
  user123 → Shard 2
  user456 → Shard 1
  user789 → Shard 3
```

**Characteristics:**
- Maximum flexibility
- Requires lookup on every query
- Can map any key to any shard

### Challenges of Sharding

| Challenge | Impact |
|---|---|
| **Cross-shard queries** | Must query all shards and merge results |
| **Transactions** | Multi-shard transactions complex/slow |
| **Joins** | Cannot easily join across shards |
| **Rebalancing** | Adding/removing shards requires data movement |
| **Hot spots** | Uneven distribution causes overload |
| **Operational complexity** | More shards = more complexity |

---

## 9.6 Multi-region Databases

### Geographic Distribution

**Multi-region databases** replicate data across geographically separated regions:

```
North America Region
  ├─ us-east-1
  └─ us-west-1

Europe Region
  ├─ eu-west-1
  └─ eu-central-1

Asia Pacific Region
  ├─ ap-southeast-1
  └─ ap-northeast-1
```

### Benefits of Multi-region

| Benefit | Use Case |
|---|---|
| **Reduced latency** | Serve users from nearby region |
| **Disaster recovery** | Data survives regional outages |
| **Business continuity** | Switch regions on failure |
| **Compliance** | Keep data within jurisdictions |
| **Performance** | Avoid long-distance queries |

### Multi-region Replication Patterns

**Read replicas in multiple regions:**
- Primary in one region
- Replicas (read-only) in other regions
- Good for read-heavy workloads
- Writes go to primary (distant)

**Multi-primary in multiple regions:**
- Writable replicas in each region
- Complex conflict resolution
- Each region autonomous
- Low latency for local writes

**Hub-and-spoke:**
- Central hub (usually home region)
- Spoke regions with caches/read-only replicas
- Balance between latency and consistency

### Cross-region Challenges

| Challenge | Impact |
|---|---|
| **Network latency** | Replication delays |
| **Write amplification** | Writes replicated globally |
| **Consistency** | Eventual consistency in multi-primary |
| **Split-brain** | Region disconnection causing conflicts |
| **Failure detection** | Network issues vs. real failure |

---

## 9.7 NewSQL Systems

### NewSQL Overview

**NewSQL** databases combine:
- **SQL** compatibility (familiar query language)
- **ACID** transactions (reliability like traditional databases)
- **Distributed** architecture (scalability like NoSQL)

**Goal:** Avoid trade-offs between consistency and scalability

### CockroachDB Example

**CockroachDB** is a leading NewSQL system:

```
CockroachDB Cluster
├─ Node 1 (Zone A)
├─ Node 2 (Zone B)
└─ Node 3 (Zone C)

Features:
- Distributed ACID transactions
- Multi-region support
- Automatic replication (3 replicas default)
- SQL interface (PostgreSQL compatible)
- Horizontal scalability
- Fault tolerance (tolerates 1 node failure with 3 replicas)
```

### NewSQL Characteristics

| Characteristic | Implementation |
|---|---|
| **SQL transactions** | Full ACID support across shards |
| **Distributed** | Transparent sharding |
| **Scalability** | Add nodes, automatic distribution |
| **Availability** | Replication + failover |
| **Consistency** | Serializable isolation level |

### NewSQL Advantages

1. **Familiar SQL** - No complex query rewrites
2. **Consistency** - ACID guarantees reduce bugs
3. **Scalability** - Add nodes for more capacity
4. **Availability** - Built-in replication
5. **Multi-region** - Easy geographic deployment

### NewSQL Trade-offs

| Trade-off | Impact |
|---|---|
| **Higher latency** than NoSQL | Consistency checks cost |
| **Lower throughput** than optimized NoSQL | ACID overhead |
| **Operational complexity** | Distributed systems harder to debug |
| **Cost** | More complex engineering |

---

## 9.8 CAP Theorem

### The CAP Theorem

In distributed systems, you can guarantee at most 2 of 3 properties during network partition:

```
        ┌─── Consistency ───┐
        │   (all nodes see  │
        │   same data at    │
        │   same time)      │
        │                   │
   ┌────┴───────────────────┴───┐
   │                             │
Availability            Partition
(system responds        Tolerance
 to requests)           (survives
                        network
                        split)
```

### CAP Theorem Explanation

**Consistency (C):**
- All nodes have same data simultaneously
- After write, all subsequent reads get new value
- Strong consistency, no stale reads

**Availability (A):**
- System always responds to requests
- No timeouts or rejections
- Every request gets response (success or fail)

**Partition Tolerance (P):**
- System continues despite network partition
- Nodes can be isolated from each other
- Required in distributed systems

### Partition Tolerance is Non-negotiable

Network partitions **will happen**:
- Hardware failures
- Network cable cuts
- Software bugs
- Maintenance windows

You cannot choose "no partitions" - you must handle them.

### Choosing Between Consistency and Availability

**CP Systems (Consistency + Partition Tolerance):**
- Choose consistency during partition
- Some requests fail or timeout
- Examples: HBase, strong-consistency databases
- Use case: Financial systems where consistency critical

```
Write during partition
├─ Strong consistency: Refuse write
└─ All reads see old data (safe but unavailable)
```

**AP Systems (Availability + Partition Tolerance):**
- Choose availability during partition
- Accept temporary inconsistency
- Examples: Cassandra, DynamoDB (without strong consistency)
- Use case: Social media, caching, analytics

```
Write during partition
├─ Availability maintained: Accept write
└─ Conflict resolution after partition heals
```

### CAP in Practice

**Most systems aren't strictly CP or AP:**

**CockroachDB (CP-leaning):**
- Prefers consistency during partitions
- Ranges become unavailable if quorum lost
- Best for financial/transactional workloads

**DynamoDB (AP-leaning):**
- Always available
- Eventual consistency by default
- Strong consistency optional (at cost)

**PostgreSQL (CA-leaning, with P through replication):**
- Not designed for partitions
- In partition, splits into islands
- Need careful handling of multi-primary scenarios

---

## Summary

Cloud databases and NewSQL systems represent the evolution of database technology:

1. **Cloud fundamentals** - On-demand provisioning, elasticity, managed services
2. **DBaaS** - Shared responsibility reduces operational burden
3. **Scaling strategies** - Vertical and horizontal scaling with different trade-offs
4. **Replication** - Synchronous vs. asynchronous with consistency trade-offs
5. **Sharding** - Horizontal partitioning enables unlimited scale
6. **Multi-region** - Geographic distribution for performance and resilience
7. **NewSQL** - Combining SQL, ACID, and distributed scalability
8. **CAP theorem** - Understanding partition tolerance trade-offs

Understanding these concepts enables architecting databases for scalability, availability, and consistency appropriate to application requirements.
