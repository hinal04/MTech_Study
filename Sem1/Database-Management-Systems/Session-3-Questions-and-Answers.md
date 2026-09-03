# Session 3: Distributed Database Foundations — Questions & Answers

> 16 questions covering: Need for distribution, Architecture, Fragmentation (including Primary vs Derived), Partitioning strategies, Data Allocation, Replication topologies (Leader-Follower, Multi-Leader, Leaderless with Quorum), Distributed concurrency, Catalog management, Design.

---

### Q1. Why do we need distributed databases? List the key drivers.

**Answer:**

| Driver | Explanation |
|---|---|
| **Organisational distribution** | Companies operate in multiple locations, each generating local data. A hospital chain wants patient records near the hospital, not on a distant server. |
| **Improved performance** | Data placed close to users reduces network latency from 100-200ms (cross-continent) to sub-millisecond (local). |
| **Scalability** | Horizontal scaling (add more nodes) has no theoretical ceiling, unlike vertical scaling (bigger server) which hits hardware limits. |
| **Availability & reliability** | With 3 replicas, data survives 2 simultaneous node failures. A single-server system has zero fault tolerance. |
| **Local autonomy** | Each site manages its own data (local schema, backups, tuning) while participating in the global system. |

---

### Q2. Compare centralised and distributed database systems.

**Answer:**

| Aspect | Centralised | Distributed |
|---|---|---|
| Data location | Single site | Multiple networked sites |
| Single point of failure | Yes — server down = system down | No — other nodes continue |
| Scalability | Vertical (bigger machine, has a ceiling) | Horizontal (add machines, no ceiling) |
| Remote user latency | High (every request crosses network) | Low (data near user) |
| Complexity | Simpler administration | Much higher (partitioning, replication, distributed transactions, failure handling) |
| Cost model | Expensive high-end hardware | Commodity hardware + operational overhead |

---

### Q3. What are the transparency goals of a Distributed DBMS? Why are they important?

**Answer:**

Transparency means the user interacts with the distributed system **as if it were a single, centralised database**. The DDBMS hides the complexity of distribution.

| Transparency | User doesn't need to know... |
|---|---|
| **Location** | ...where data is physically stored. Query uses table names, not server addresses. |
| **Fragmentation** | ...that a table is split across multiple sites. The full table appears intact. |
| **Replication** | ...that multiple copies exist. Reads and writes go to "the data," not specific replicas. |
| **Concurrency** | ...that other transactions run simultaneously. Each user perceives exclusive access. |
| **Failure** | ...that a node or network link has failed. The system masks failures and continues. |

**Why important:** Without transparency, every application would need to know which server holds which fragment, how to handle replica consistency, and what to do when nodes fail. This would make application development impossibly complex.

---

### Q4. Explain Shared-Nothing, Shared-Disk, and Shared-Memory architectures.

**Answer:**

**Shared-Nothing:** Each node has its own CPU, memory, and disk. Communication only via network. Most scalable — adding a node adds independent resources. Failure of one node doesn't affect others. Used by Cassandra, CockroachDB, Spanner, DynamoDB.

**Shared-Disk:** All nodes share a common storage layer (SAN/NAS). Each node has own CPU and memory. Easier data sharing but storage can be a bottleneck. Nodes must coordinate disk access carefully. Example: Oracle RAC.

**Shared-Memory:** All processors share same memory and disk. Single multi-core server. Limited scalability due to memory bus contention. Not truly distributed.

**In practice:** Shared-Nothing dominates modern cloud-native distributed systems because of its superior scalability and fault isolation.

---

### Q5. What is data fragmentation? Explain horizontal and vertical fragmentation with examples.

**Answer:**

**Fragmentation** splits a relation into smaller pieces distributed across sites to improve locality and performance.

**Horizontal fragmentation** splits by **rows** based on a predicate:
```
London site: σ_{Region='London'}(EMPLOYEE)  → employees in London
Mumbai site: σ_{Region='Mumbai'}(EMPLOYEE)  → employees in Mumbai
```
Reconstruction: UNION of all fragments.

**Vertical fragmentation** splits by **columns** (PK always included):
```
HR site:      Emp_ID, Name, Hire_Date
Payroll site: Emp_ID, Salary, Bank_Account
```
Reconstruction: Natural JOIN on Emp_ID.

**Hybrid:** Both applied — first split by columns, then each column-fragment by rows.

**Correctness requirements:**
1. **Completeness** — every tuple in at least one fragment.
2. **Reconstruction** — original table can be rebuilt.
3. **Disjointness** (preferred) — no tuple in more than one fragment.

---

### Q6. Compare range, hash, list, and consistent hashing partitioning strategies.

**Answer:**

| Strategy | How rows are assigned | Pros | Cons |
|---|---|---|---|
| **Range** | Key ranges (e.g. Jan-Mar → node 1) | Efficient range queries; simple. | Hot spots if data skewed (e.g. recent dates accessed most). |
| **Hash** | hash(key) mod N → node number | Even distribution; no hot spots. | Range queries scan ALL nodes; rehash needed when nodes change. |
| **List** | Explicit value lists (e.g. UK → node 1, India → node 2) | Easy to understand; good for categories. | Imbalanced if some categories much larger. |
| **Consistent hashing** | Hash ring; each node owns a range. Adding/removing a node moves only adjacent data. | Elastic scaling; minimal data movement. | More complex; slight imbalance (fixed with virtual nodes). |

---

### Q7. Why do we replicate data? Compare synchronous and asynchronous replication.

**Answer:**

**Why:** Availability (survive node failures), read performance (distribute reads across replicas), latency (serve from nearest replica), fault tolerance (survive data centre outages).

| Aspect | Synchronous (Eager) | Asynchronous (Lazy) |
|---|---|---|
| Write confirmation | All replicas must confirm before commit. | Primary commits first; replicas updated later. |
| Consistency | **Strong** — all replicas always agree. | **Eventual** — replicas may temporarily diverge. |
| Write latency | Higher (wait for slowest replica + network RTT). | Lower (only wait for primary). |
| Failure risk | Writes block if any replica is down. | Writes succeed but data may be lost if primary fails before propagation. |
| Use case | Financial transactions, inventory. | Social feeds, analytics, caching. |

---

### Q8. Explain the three replication topologies and their trade-offs.

**Answer:**

**Single-leader (primary–replica):** One node accepts all writes; replicas serve reads.
- **Pros:** Simple, no write conflicts, easy consistency reasoning.
- **Cons:** Leader is bottleneck and SPOF. Failover (promoting a replica) can be complex and may cause brief unavailability.

**Multi-leader:** Multiple nodes accept writes; changes replicated between leaders.
- **Pros:** Low write latency in multi-region setups (each region has a local leader).
- **Cons:** Write conflicts when two leaders modify the same data. Needs conflict resolution (LWW, CRDTs, or application logic).

**Leaderless (peer-to-peer):** Any node accepts reads and writes. Consistency via quorum (R+W>N).
- **Pros:** No SPOF, highest availability.
- **Cons:** Most complex. Quorum overhead. Conflict resolution required. Examples: Cassandra, DynamoDB.

---

### Q9. What is the conflict resolution problem in multi-leader/leaderless systems? Describe three strategies.

**Answer:**

**Problem:** Two nodes independently update the same row (e.g. Node A sets name="Alice", Node B sets name="Alicia" at the same time). When they sync, which value wins?

| Strategy | How it works | Pros | Cons |
|---|---|---|---|
| **Last-writer-wins (LWW)** | Highest timestamp wins. Earlier write discarded. | Simple. | Data loss. Depends on clock synchronisation. |
| **CRDTs** | Mathematically designed data structures that auto-merge (counters, sets, registers). | No data loss. Automatic. | Limited to specific data types. Can't model arbitrary logic. |
| **Application-level** | App defines merge rules (e.g. show both versions, merge fields, pick by edit count). | Most flexible. | Most complex for developers. |

---

### Q10. What is distributed concurrency control? Compare centralised locking vs distributed locking.

**Answer:**

In a distributed system, transactions may need locks at multiple sites. Three approaches:

| Approach | How it works | Pros | Cons |
|---|---|---|---|
| **Centralised locking** | One designated node manages all locks. | Simple; no distributed deadlocks. | Bottleneck + SPOF. |
| **Primary copy locking** | Each data item has a designated primary; locks go there. | Load distributed across nodes. | Must track primaries; handle primary failure. |
| **Distributed locking** | Locks acquired at the node storing the data. | No single bottleneck. | Distributed deadlock detection needed. |

**Distributed deadlock detection:** Centralised detector (one node builds global wait-for graph), distributed detector (sites exchange edges), or timeout (assume deadlock if waiting too long).

---

### Q11. Differentiate between homogeneous and heterogeneous distributed databases.

**Answer:**

| Aspect | Homogeneous | Heterogeneous |
|---|---|---|
| DBMS software | Same at all sites (e.g. all PostgreSQL). | Different at different sites (Oracle + PostgreSQL + MySQL). |
| Data model | Identical across sites. | May differ (relational + document + graph). |
| Query language | Same (all use same SQL dialect). | Different; translation/middleware needed. |
| Management | Easier — uniform configuration, upgrades, and tuning. | Harder — must maintain compatibility across different systems. |
| Example | All branches run PostgreSQL 15. | London runs Oracle, Mumbai runs MongoDB, Tokyo runs MySQL. |

---

### Q12. What are the key design questions when building a distributed database? What goals should guide the design?

**Answer:**

**Four design questions:**
1. **What to fragment?** — Large transaction tables (orders, events) benefit most. Small lookup tables usually don't.
2. **How to fragment?** — Horizontal (by rows), vertical (by columns), or hybrid. Depends on query access patterns.
3. **Where to place fragments?** — Place data close to the applications that use it most frequently.
4. **What to replicate?** — Replicate read-heavy data for performance; keep write-heavy data at fewer sites to reduce write amplification.

**Design goals:**
- **Locality of reference:** Minimise network traffic for common queries.
- **Load balancing:** Distribute data evenly; avoid hot spots.
- **Minimise network transfers:** The most expensive distributed operation.
- **Meet availability SLAs:** Replicate critical data, accepting higher write cost.

---

---

### Q13. Distinguish between primary and derived horizontal fragmentation with examples.

**Answer:**

**Primary horizontal fragmentation:** The condition is based on attributes of the **same relation** being fragmented.
- Example: Fragment EMPLOYEE by Region: σ_{Region='London'}(EMPLOYEE) → London site.
- The predicate (Region) belongs to EMPLOYEE itself.

**Derived horizontal fragmentation:** The condition is based on an attribute of a **related relation** (via a foreign key), not the relation being fragmented.
- Example: Fragment PROJECT by the department that controls it. DEPARTMENT is fragmented by location → PROJECT is fragmented derivatively based on its controlling department's location.
- PROJECT doesn't have a Location column used for fragmentation — it inherits the fragmentation from DEPARTMENT through the FK relationship.

**When to use each:** Primary when the relation has a natural partitioning attribute (Region, Country). Derived when the relation should follow the fragmentation of a parent/referenced table for data locality.

---

### Q14. List and explain the five fundamental challenges in distributed database systems.

**Answer:**

1. **Communication delays** — Messages between nodes travel over a network with non-zero latency (milliseconds within a data centre, hundreds of milliseconds across continents). Every inter-node operation is slower than a local one.

2. **Node failures** — Individual nodes can crash, restart, or become unreachable at any time. The system must handle partial failures gracefully — continuing to operate even when some nodes are down.

3. **Maintaining consistency** — When data is replicated across multiple sites, all copies must eventually agree. Keeping them in sync despite concurrent writes and network delays is extremely difficult. This is the core of the CAP theorem trade-off (Session 4).

4. **Concurrency control** — Multiple transactions at different sites may modify the same data simultaneously. Distributed locking is harder than single-node locking, and deadlocks can span multiple nodes.

5. **Coordination overhead** — Any protocol requiring node agreement (2PC, consensus) adds latency and message overhead. The more nodes involved, the slower the coordination.

---

### Q15. Explain Leader-Follower replication step by step. What is replication lag?

**Answer:**

**How Leader-Follower works:**
1. All writes are sent to the **Leader** (primary) node.
2. Leader updates its local database.
3. Leader sends changes to **Followers** (replicas/secondaries).
4. Followers apply changes to their local copies.
5. Reads may be served by leader or followers (configurable).

**Replication lag:** When replication is asynchronous, followers may not have received the latest writes yet. The delay between a write on the leader and its appearance on a follower is called **replication lag**. During this window, a read from a follower returns stale data.

**Failover:** If the leader crashes, a follower is promoted as the new leader. With asynchronous replication, the promoted follower may have missed some recent writes — those writes are effectively **lost** unless the old leader recovers and its log can be reconciled.

---

### Q16. What is leaderless replication? Explain the quorum condition W + R > N.

**Answer:**

In **leaderless replication** (used by Cassandra, DynamoDB), there is no designated leader. Any node can accept reads and writes.

**How it works:**
1. Client sends writes to **multiple replicas** directly (or via a coordinator).
2. A write succeeds if **W** replicas acknowledge it.
3. A read queries **R** replicas and returns the most recent value.
4. Differences between replicas are repaired via **read repair** (fix stale replicas during reads) or **anti-entropy** (background synchronisation).

**Quorum condition: W + R > N**

Where N = total replicas, W = write acknowledgements, R = read replicas.

This ensures the read set and write set **overlap** — at least one replica contacted during a read must have the latest write.

**Example (N=3):**
- W=2, R=2: 2+2=4 > 3 → overlap guaranteed → **strong consistency** ✓
- W=1, R=1: 1+1=2 ≤ 3 → no overlap guaranteed → may read stale data ✗

**Trade-off:** Higher W/R → stronger consistency but higher latency and lower availability. Lower W/R → faster but may read stale.

*End of Session 3 Questions & Answers*
