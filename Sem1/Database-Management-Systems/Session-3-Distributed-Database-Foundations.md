# Session 3: Distributed Database Foundations

> BITS Pilani — Structured Data Management & Distributed Databases
>
> **References:** T1 Ch.23 (Sec 23.1, 23.2, 23.7)
>
> **Textbook:** Elmasri & Navathe, *Fundamentals of Database Systems*, 7th Ed., Pearson, 2017.

---

## Table of Contents

- [3.1 Need for Distributed Databases](#31-need-for-distributed-databases)
- [3.2 Distributed Database Architecture](#32-distributed-database-architecture)
- [3.3 Data Partitioning (Fragmentation)](#33-data-partitioning-fragmentation)
- [3.4 Replication Strategies](#34-replication-strategies)
- [3.5 Distributed Concurrency Control](#35-distributed-concurrency-control)
- [3.6 Distributed Catalog Management](#36-distributed-catalog-management)
- [3.7 Design Considerations](#37-design-considerations)

---

## 3.1 Need for Distributed Databases

### Why a Single Server Isn't Enough

As organisations grow, their computing needs inevitably outgrow a single database server. A multinational bank with branches in London, Mumbai, New York, and Tokyo cannot efficiently serve all users from one server in one city — network latency would make transactions painfully slow for distant users. A social media platform with 2 billion users cannot store all data on one machine — no server has enough storage, memory, or CPU power.

**Distributed databases** address these challenges by spreading data across multiple computers (called **nodes** or **sites**) connected by a network. Each node stores a portion of the data and can process queries locally. The nodes cooperate to present the illusion of a single, unified database.

### Key Drivers for Distribution

| Driver | Explanation |
|---|---|
| **Organisational distribution** | Companies operate across multiple cities and countries. Each location generates and consumes local data. A hospital chain wants patient records stored near the hospital — not on a server thousands of kilometres away. |
| **Improved performance** | Data placed close to where it is used reduces network latency. A user in Mumbai reading data from a Mumbai server gets sub-millisecond response. Reading from a London server adds 100-200ms of network round-trip time. |
| **Scalability** | A single server has fixed CPU, memory, and I/O limits. Adding more nodes increases total capacity — this is **horizontal scaling** (scale out), as opposed to **vertical scaling** (scale up = buy a bigger server). Horizontal scaling has no theoretical ceiling. |
| **Availability & reliability** | If one node fails, others continue serving requests. Replicated data survives hardware failures. A system with 3 replicas can tolerate 2 simultaneous node failures and still serve data. |
| **Local autonomy** | Each site can manage its own data (local schema changes, local backups, local performance tuning) while still participating in the global system. |

### Centralised vs. Distributed: A Comparison

| Aspect | Centralised | Distributed |
|---|---|---|
| **Data location** | Single site. | Multiple networked sites. |
| **Single point of failure** | Yes — if the server goes down, everything stops. | No — other nodes continue operating. |
| **Scalability** | Vertical only (bigger machine). Eventually hits a ceiling. | Horizontal (add more machines). Practically unlimited. |
| **Latency for remote users** | High — every request crosses the network. | Low — data near the user. |
| **Complexity** | Simpler to administer, debug, and maintain. | Significantly more complex: partitioning, replication, distributed transactions, failure handling. |
| **Cost** | Expensive high-end hardware (big servers are disproportionately expensive). | Commodity hardware, but more operational overhead (network management, consistency protocols). |

---

## 3.2 Distributed Database Architecture

### 3.2.1 Definition

A **Distributed Database System (DDBS)** is a collection of multiple, logically interrelated databases distributed over a computer network, managed by a **Distributed DBMS (DDBMS)** that makes the distribution **transparent** to users. From the user's perspective, the data appears to be in one place — the complexity of distribution is hidden.

### 3.2.2 Transparency Goals

Transparency is the central design principle of a DDBMS. The system should hide the complexities of distribution so that users and applications interact with it as if it were a single, centralised database.

| Transparency | What the user doesn't need to know |
|---|---|
| **Location transparency** | Where data is physically stored. A query references a table name, not a server address. The DDBMS routes the query to the correct node(s). |
| **Fragmentation transparency** | That a table has been split into fragments across multiple sites. The user queries the full table; the DDBMS reassembles fragments automatically. |
| **Replication transparency** | That multiple copies of the same data exist at different sites. The user reads/writes "the data"; the DDBMS handles keeping replicas in sync. |
| **Concurrency transparency** | That other transactions are running at the same time. Each user experiences the system as if they have exclusive access. |
| **Failure transparency** | That a node or network link has failed. The system masks the failure and continues operating (possibly with reduced performance). |

Achieving full transparency is extremely difficult in practice. Most real systems provide some transparencies but expose others to the application for performance or correctness reasons.

### 3.2.3 Architecture Types

How nodes share resources determines the architecture:

**1. Shared-Nothing Architecture**

Each node has its own CPU, memory, and disk. Nodes communicate **only** via the network. No shared hardware.

This is the most scalable architecture. Adding a node adds independent resources. A node failure affects only its local data. Used by the vast majority of modern distributed systems: Cassandra, CockroachDB, Google Spanner, Amazon DynamoDB, Apache Kafka.

**2. Shared-Disk Architecture**

All nodes access a **common storage layer** (SAN — Storage Area Network, or NAS). Each node has its own CPU and memory, but they all read/write to the same disks.

Easier data sharing (any node can access any data), but the shared storage can become a bottleneck. Also, nodes must coordinate carefully to avoid conflicting writes to the same disk blocks. Example: Oracle RAC (Real Application Clusters).

**3. Shared-Memory (Shared-Everything) Architecture**

All processors share the **same memory** and the **same disk**. This is the architecture of a single multi-core server (SMP — Symmetric Multiprocessing).

Limited scalability — adding processors increases contention for shared memory and bus bandwidth. Not truly "distributed" since everything is on one machine, but the concepts of shared memory parallelism apply.

### 3.2.4 Homogeneous vs. Heterogeneous DDBS

| Type | Characteristics | Example |
|---|---|---|
| **Homogeneous** | All sites run the same DBMS software, same data model, same query language. Easier to manage and optimise. | All sites run PostgreSQL 15. |
| **Heterogeneous** | Different sites run different DBMSs with potentially different data models. Requires middleware or translation layers to bridge the differences. | London runs Oracle, Mumbai runs PostgreSQL, Tokyo runs MySQL. A federated query engine translates between them. |

---

## 3.3 Data Partitioning (Fragmentation)

**Partitioning** (also called **fragmentation**) splits a relation into smaller pieces that are distributed across different sites. The goal is to place data close to where it's most frequently accessed, reducing network traffic and improving query performance.

### 3.3.1 Types of Fragmentation

#### Horizontal Fragmentation

Splits a table by **rows** — each fragment contains a subset of tuples selected by a predicate (condition).

**Two sub-types** (from class slides 12-13):

**Primary Horizontal Fragmentation:** The fragmentation condition is based on attributes of the **same relation** being fragmented.
- Example: Fragment EMPLOYEE by Region: σ_{Region='London'}(EMPLOYEE) goes to the London site.
- The predicate uses an attribute (Region) that belongs to the EMPLOYEE table itself.

**Derived Horizontal Fragmentation:** The fragmentation condition is based on an attribute of a **related (referenced) relation**, not the relation being fragmented.
- Example: Fragment PROJECT by the department that controls it. If DEPARTMENT is horizontally fragmented by location, then PROJECT is fragmented **derivatively** — projects controlled by London departments go to the London site, projects controlled by Mumbai departments go to the Mumbai site.
- The predicate uses a foreign key relationship — the PROJECT table is fragmented based on DEPARTMENT's location, not PROJECT's own attributes.

```
EMPLOYEE table (global):
┌──────┬──────────┬────────┐
│Emp_ID│ Name     │ Region │
├──────┼──────────┼────────┤
│  1   │ Alice    │ London │
│  2   │ Bob      │ Mumbai │
│  3   │ Carol    │ London │
│  4   │ Dave     │ Mumbai │
└──────┴──────────┴────────┘

Fragment 1 (stored at London site):  σ_{Region='London'}(EMPLOYEE)  → rows 1, 3
Fragment 2 (stored at Mumbai site):  σ_{Region='Mumbai'}(EMPLOYEE)  → rows 2, 4
```

**Reconstruction:** UNION of all fragments gives back the original table.

**When to use:** When queries at each site mostly access local data. London branch queries London employees; Mumbai branch queries Mumbai employees. Most queries are served locally with no network traffic.

#### Vertical Fragmentation

Splits a table by **columns** — each fragment contains a subset of attributes. The primary key must be included in **every** fragment so the original table can be reconstructed.

```
Fragment 1 (HR site):       Emp_ID, Name, Hire_Date
Fragment 2 (Payroll site):  Emp_ID, Salary, Bank_Account
```

**Reconstruction:** Natural JOIN on Emp_ID gives back the original table.

**When to use:** When different departments/applications use different columns. HR needs names and dates; Payroll needs salaries and bank details. Neither needs the other's columns.

#### Hybrid (Mixed) Fragmentation

Applies both horizontal and vertical fragmentation. First split by columns, then split each column-fragment by rows (or vice versa). Used when both row-based and column-based access patterns exist.

### 3.3.2 Correctness Requirements

Any fragmentation must satisfy three properties:

1. **Completeness:** Every tuple (and every attribute) in the original relation appears in at least one fragment. Nothing is lost.
2. **Reconstruction:** The original relation can be rebuilt from the fragments using UNION (horizontal) or JOIN (vertical).
3. **Disjointness** (preferred but not required): No tuple appears in more than one fragment. This avoids redundancy and simplifies updates.

### 3.3.3 Partitioning Strategies in Practice

| Strategy | How rows are assigned | Pros | Cons |
|---|---|---|---|
| **Range partitioning** | Based on a key range (e.g. orders from Jan-Mar on node 1, Apr-Jun on node 2). | Efficient range queries (all data for a range is on one node). Simple to understand and implement. | **Hot spots** — if most queries target recent data, the most recent partition is overloaded while older partitions sit idle. |
| **Hash partitioning** | A hash function on the partition key determines the node: `node = hash(key) mod N`. | Even distribution across nodes — no hot spots for uniform workloads. Good for point lookups. | Range queries must scan **all** partitions (the hash destroys ordering). Adding/removing nodes requires rehashing all data. |
| **List partitioning** | Rows assigned based on explicit value lists (e.g. Region='UK' → node 1, Region='India' → node 2). | Easy to understand. Good for categorical data with known, stable categories. | Imbalanced if some categories are much larger than others. |
| **Consistent hashing** | Keys are mapped to a hash ring. Each node owns a range of the ring. Adding/removing a node moves only the data in the adjacent range. | **Elastic scaling** — adding a node moves minimal data. No full rehash. | More complex to implement. Potential for slight imbalance (solved by virtual nodes). |

---

## 3.3.4 Data Allocation Strategies

*(From class slides 16-17)*

After fragmentation, **data allocation** determines which site stores which fragment. Three strategies:

| Strategy | Description | Pros | Cons |
|---|---|---|---|
| **Non-Replicated (Partitioned)** | Each fragment stored at exactly one site. | Lowest storage cost. Simplest updates. | Availability depends entirely on one site. If that site fails, the fragment is unavailable. |
| **Partially Replicated** | Selected fragments are replicated at multiple sites. Other fragments may exist at only one site. | Balances availability, performance, storage, and update cost. Most common in practice. | Must track which fragments are where. Update propagation needed for replicated fragments. |
| **Fully Replicated** | A complete copy of the entire database stored at every site. | Maximum availability and read performance. Read locally from any site. | Very expensive for writes — every write must propagate to all sites. Practical only for read-heavy, rarely-changing data. |

---

## 3.3.5 Challenges in Distributed Systems

*(From class slide 6)*

Before diving into replication, it's worth explicitly listing the fundamental challenges that make distributed databases harder than centralised ones:

1. **Communication delays** — Messages between nodes travel over a network with non-zero latency (milliseconds within a data centre, hundreds of milliseconds across continents). Every inter-node operation is slower than a local one.
2. **Node failures** — Individual nodes can crash, restart, or become unreachable at any time. The system must continue operating despite partial failures.
3. **Maintaining consistency** — When data is replicated, all copies must eventually agree. Keeping them in sync in the face of concurrent writes and network delays is extremely difficult.
4. **Concurrency control** — Multiple transactions at different sites may try to modify the same data simultaneously. Distributed locking and deadlock detection are harder than their single-node equivalents.
5. **Coordination overhead** — Any protocol that requires agreement between nodes (2PC, consensus) adds latency and complexity. The more nodes involved, the higher the overhead.

---

## 3.4 Replication Strategies

**Replication** maintains **multiple copies** (replicas) of the same data at different sites. While partitioning splits data so each piece lives at one site, replication duplicates data so the same piece lives at multiple sites. Most real-world systems use both.

### 3.4.1 Why Replicate?

- **Availability:** If the only copy of data is on a single node and that node crashes, the data is unavailable. With 3 replicas on 3 nodes, data remains available even if 2 nodes fail.
- **Read performance (read scaling):** 1,000 users reading the same data can be distributed across replicas — each serves a fraction of the readers.
- **Latency reduction:** Place replicas close to users geographically (Europe, Asia, Americas). Each user reads from the nearest replica.
- **Fault tolerance:** Even if an entire data centre goes offline (fire, earthquake, network failure), replicas in other data centres ensure data survival.

### 3.4.2 Replication Approaches

| Approach | Description | Trade-off |
|---|---|---|
| **Full replication** | Every site has a complete copy of the entire database. | Maximum availability and read performance. But writes are extremely expensive — every write must update every site. Only practical for read-heavy, rarely-changing data. |
| **Partial replication** | Selected tables (or fragments) are replicated at selected sites based on access patterns. | Balances availability and write cost. Most common in practice. |
| **No replication** | Each fragment stored at exactly one site. | Simplest for writes. But a node failure means that data is unavailable until recovery. |

### 3.4.3 Synchronous vs. Asynchronous Replication

This is one of the most important trade-offs in distributed systems.

| Aspect | Synchronous (Eager) | Asynchronous (Lazy) |
|---|---|---|
| **How writes work** | The write must be confirmed at **all** replicas before the transaction commits. | The write is committed at the **primary** node; replicas are updated later (within milliseconds to seconds). |
| **Consistency** | **Strong** — all replicas always agree. No stale reads. | **Eventual** — replicas may temporarily show different values. Reads from a stale replica return outdated data. |
| **Write latency** | Higher — must wait for the slowest replica (including network round-trip). | Lower — only wait for the primary. |
| **Failure impact** | If any replica is down, writes may **block** until it recovers (or the system times out and degrades). | Writes succeed even if replicas are down. But if the primary fails before propagating, **data can be lost**. |
| **Use case** | Financial transactions, inventory counts — where correctness is critical. | Social media feeds, analytics, caching — where slight staleness is acceptable. |

### 3.4.4 Replication Topologies

*(Enhanced with class slides 22-24)*

| Topology | Description | Pros | Cons |
|---|---|---|---|
| **Single-leader (primary–replica)** | One designated node (the leader/primary) accepts all writes. Replicas serve reads and receive updates from the leader. | Simple. No write conflicts (only one writer). Easy to reason about consistency. | Leader is a bottleneck and a single point of failure. Failover (promoting a replica to leader) can be complex. |
| **Multi-leader** | Multiple nodes accept writes. Each leader replicates its changes to other leaders. | Good for multi-region deployments (each region has a local leader → low write latency). | **Write conflicts** — two leaders may independently modify the same row. Conflict resolution is required. |
| **Leaderless (peer-to-peer)** | Any node accepts reads and writes. No special leader role. Consistency achieved via **quorum** reads/writes. | No single point of failure. Highly available. | Most complex conflict resolution. Quorum overhead. Examples: Cassandra, DynamoDB. |

#### Leader-Follower Replication — Detail (Class Slide 22)

**How it works step by step:**
1. All writes are sent to the **Leader** (primary).
2. Leader updates its local database.
3. Leader sends changes to its **Followers** (replicas/secondaries).
4. Followers apply the changes to their local copies.
5. Reads may be served by the leader or followers, depending on configuration.

**Key characteristics:**
- Provides a single coordination point for writes — no write conflicts possible.
- Followers can help **scale read workloads** (distribute read traffic across replicas).
- Replication may be synchronous or asynchronous (see Section 3.4.3).
- If the leader fails, a follower may be **promoted** as the new leader (failover). This process can be complex — the promoted follower may have missed some recent writes if replication was asynchronous.
- **Replication lag:** Asynchronous followers may return stale data because updates haven't propagated yet. This is the trade-off for lower write latency.

#### Multi-Leader Replication — Detail (Class Slide 23)

**How it works:**
1. Multiple nodes act as Leaders — each can accept writes independently.
2. Clients write to whichever leader is nearest (lowest latency).
3. Each leader applies its local writes, then replicates changes to other leaders.
4. **Conflicting concurrent updates require conflict detection and resolution.**

**The Major Challenge — Write Conflicts:**
If Leader A sets X = 100 and Leader B simultaneously sets X = 200, which value wins? Both writes are valid locally, but they're incompatible globally. The system must resolve this conflict.

**Where it's useful:**
- Multi-datacenter deployments (each data centre has a local leader).
- Applications with users distributed across geographical regions.
- Environments needing local write processing with intermittent connectivity.

#### Leaderless Replication — Detail (Class Slide 24)

**How it works:**
1. Clients send writes to **multiple replicas directly** (no designated leader).
2. A write is considered successful after a required number of replicas acknowledge it.
3. Reads also query multiple replicas.
4. Differences between replicas are detected and repaired (read repair, anti-entropy).

**Quorum-Based Approach:**
Let N = number of replicas, W = write acknowledgements required, R = read replicas contacted.

The commonly discussed quorum condition is:

```
W + R > N
```

This ensures at least one replica in the read set has the most recent write (the read and write sets overlap). See Session 4 Section 4.5.3 for detailed quorum examples.

### 3.4.5 Conflict Resolution (Multi-leader / Leaderless)

When two nodes independently update the same data, you have a **write conflict**. Strategies:

| Strategy | How it works | Pros | Cons |
|---|---|---|---|
| **Last-writer-wins (LWW)** | The write with the latest timestamp wins. Earlier write is silently discarded. | Simple to implement. | **Data loss** — the earlier write is gone. Relies on synchronised clocks (problematic in distributed systems). |
| **CRDTs (Conflict-free Replicated Data Types)** | Data structures mathematically designed so that concurrent updates can always be merged automatically without conflicts. | No data loss. Fully automatic. | Limited to specific data types (counters, sets, registers). Cannot model arbitrary business logic. |
| **Application-level resolution** | The application defines custom merge logic (e.g. show both versions to the user, pick the one with more edits, merge fields individually). | Most flexible. Can implement any business rule. | Most complex. Application developers must handle conflicts correctly. |

---

## 3.5 Distributed Concurrency Control

### 3.5.1 The Challenge

In a centralised system, one lock manager handles all concurrency. In a distributed system, locks may need to be acquired at multiple sites, and deadlocks can span multiple nodes. The fundamental challenge: how do you coordinate locking across independent machines that can only communicate via a network (which can fail or be slow)?

### 3.5.2 Approaches

| Approach | How it works | Pros | Cons |
|---|---|---|---|
| **Centralised locking** | One designated node manages **all** locks for the entire system. All lock requests go to this node. | Simple to implement. No distributed deadlocks (all detected at one place). | Bottleneck — every lock request crosses the network. Single point of failure — if the lock manager crashes, the entire system blocks. |
| **Primary copy locking** | For each data item, one replica is designated the "primary." Lock requests for that item go to the primary's node. | Load is distributed across nodes (different items have different primaries). | Must track which node is primary for each item. If a primary fails, must elect a new one. |
| **Distributed locking** | Lock requests go to the node that stores the data. A transaction may hold locks at multiple nodes simultaneously. | No single bottleneck. Most natural for shared-nothing architectures. | Requires **distributed deadlock detection**. |

### 3.5.3 Distributed Deadlock Detection

- **Centralised detector:** One node collects wait-for information from all sites, builds a global wait-for graph, and checks for cycles. Simple but has a single point of failure.
- **Distributed detector:** Each site maintains a local wait-for graph. Periodically, sites exchange edge information to detect global cycles. More complex but no SPOF.
- **Timeout-based:** If a transaction waits longer than a threshold, assume deadlock and abort. Simple but may abort non-deadlocked transactions (false positives) or wait too long (if timeout is too generous).

---

## 3.6 Distributed Catalog Management

The **catalog** (data dictionary/metadata) in a distributed system must track not only schema information (table names, column types, constraints) but also:

- **Which sites store which fragments** (fragmentation schema).
- **Which sites hold replicas** of each fragment (replication schema).
- **Fragmentation definitions** — the predicates for horizontal fragmentation, the attribute lists for vertical fragmentation.

### Catalog Placement Strategies

| Strategy | Description | Trade-off |
|---|---|---|
| **Centralised catalog** | One site holds all metadata. All metadata queries go there. | Simple to maintain. But it's a bottleneck and SPOF. |
| **Fully replicated catalog** | Every site has a complete copy of all metadata. | Fast reads (local lookup). But expensive to update — every schema change must propagate to all sites. |
| **Partitioned catalog** | Each site stores metadata only for its local data. | No redundancy; minimal update cost. But non-local metadata queries require network calls. |

Most real systems use a mix: critical metadata (schema definitions) is fully replicated for fast access, while operational metadata (statistics, temporary allocations) is partitioned.

---

## 3.7 Design Considerations

Designing a distributed database requires answering four key questions:

### 3.7.1 The Four Design Questions

1. **What to fragment?** — Which tables benefit from being split? Small lookup tables (e.g. country codes) usually aren't worth fragmenting. Large transaction tables (e.g. orders, events) almost always are.
2. **How to fragment?** — Horizontal, vertical, or hybrid? Depends on query access patterns.
3. **Where to place fragments?** — Which site stores which fragment? Place data close to the applications that use it most.
4. **What to replicate?** — Which fragments need copies for availability/performance? Replicate read-heavy data; keep write-heavy data at fewer sites.

### 3.7.2 Design Goals

- **Locality of reference:** Place data close to the applications that use it most frequently. This minimises network traffic for the most common queries.
- **Load balancing:** Distribute data evenly to avoid hot spots where one node is overloaded while others are idle.
- **Minimise network traffic:** The most expensive operation in a distributed system is sending data across the network. Design fragmentation and replication to minimise cross-site data movement for common queries.
- **Meet availability requirements:** Replicate critical data. Accept that replication increases write cost and complexity.

### 3.7.3 The Replication Trade-off

```
                   Read-heavy workload          Write-heavy workload
                   ──────────────────           ────────────────────
Full replication   Best (read from local)       Worst (write to all sites)
No replication     Worst (remote reads)         Best (write to one site)
Partial rep.       Balanced                     Balanced
```

The right replication strategy depends entirely on your read/write ratio and consistency requirements.

---

*End of Session 3*
