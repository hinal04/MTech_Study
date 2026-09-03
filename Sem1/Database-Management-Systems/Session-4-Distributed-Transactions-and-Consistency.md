# Session 4: Distributed Transactions and Consistency Models

> BITS Pilani — Structured Data Management & Distributed Databases
>
> **References:** T1 Ch.23 (Sec 23.4, 23.5) | Ch.24 (Sec 24.1)
>
> **Textbook:** Elmasri & Navathe, *Fundamentals of Database Systems*, 7th Ed., Pearson, 2017.

---

## Table of Contents

- [4.1 Distributed Transactions](#41-distributed-transactions)
- [4.2 Distributed Query Processing](#42-distributed-query-processing)
- [4.3 The CAP Theorem](#43-the-cap-theorem)
- [4.4 ACID vs. BASE](#44-acid-vs-base)
- [4.5 Consistency Models and Tunable Consistency](#45-consistency-models-and-tunable-consistency)
- [4.6 Consensus Protocols — Paxos and Raft](#46-consensus-protocols)
- [4.7 Distributed Recovery](#47-distributed-recovery)
- [4.8 NewSQL](#48-newsql)
- [4.9 Data Warehousing Concepts](#49-data-warehousing-concepts)

---

## 4.1 Distributed Transactions

### The Fundamental Challenge

A **distributed transaction** spans multiple nodes — parts of the transaction execute at different sites. The fundamental challenge is ensuring **atomicity** when multiple independent nodes must agree on whether to commit or abort. If Node A commits but Node B aborts, the database is in an inconsistent state.

In a single-server system, atomicity is straightforward: one transaction manager makes the commit/abort decision and applies it locally. In a distributed system, multiple independent transaction managers must reach the **same** decision despite the possibility of network failures, node crashes, and message delays. This is the distributed commit problem.

### 4.1.1 Two-Phase Commit (2PC)

The **Two-Phase Commit** protocol is the most widely used solution for atomic distributed commits. It ensures that all participating nodes either commit together or abort together.

**Roles:**
- **Coordinator:** The node that initiates and manages the protocol. Typically the node where the transaction was started.
- **Participants:** All other nodes that hold data modified by the transaction.

**Phase 1 — Voting (Prepare)**

The coordinator asks every participant: "Can you commit your part of the transaction?"

1. Coordinator sends `PREPARE` message to all participants.
2. Each participant checks whether it can commit its local portion (enough resources, no constraint violations, log records written to stable storage).
3. If YES → participant writes all changes to its local durable log (so it can recover after a crash), then sends `VOTE YES` to the coordinator. At this point, the participant has **promised** to commit if told to — it cannot unilaterally abort.
4. If NO → participant sends `VOTE NO` and aborts its local portion immediately.

**Phase 2 — Decision (Commit/Abort)**

The coordinator makes the global decision based on the votes:

- If **ALL** participants voted YES → coordinator writes `GLOBAL COMMIT` to its log and sends `GLOBAL COMMIT` to all participants. Each participant makes changes permanent and sends ACK.
- If **ANY** participant voted NO → coordinator writes `GLOBAL ABORT` to its log and sends `GLOBAL ABORT` to all participants. Each participant rolls back and sends ACK.

```
Coordinator                     Participants
    │                               │
    │──── PREPARE ─────────────────→│  "Can you commit?"
    │                               │
    │←─── VOTE YES / VOTE NO ──────│  Each participant votes
    │                               │
    │  (Decision based on votes)    │
    │──── GLOBAL COMMIT/ABORT ────→│  "Commit" or "Abort"
    │                               │
    │←─── ACK ─────────────────────│  Confirmation
```

**The Blocking Problem — 2PC's Critical Weakness**

If the coordinator crashes **after Phase 1** (after collecting YES votes but before sending the decision), participants are **stuck**. They voted YES and promised to commit — they cannot unilaterally abort (the coordinator might have decided to commit). They also cannot commit (the coordinator might have decided to abort because another participant voted NO). They must **wait** while holding locks, potentially blocking other transactions indefinitely.

This blocking is 2PC's most serious problem. In practice, it means 2PC is only suitable for environments with reliable, low-latency networks (typically within a single data centre).

### 4.1.2 Three-Phase Commit (3PC)

3PC adds an extra phase to reduce blocking:

1. **CanCommit?** — Coordinator asks participants if they can commit (same as 2PC Phase 1).
2. **PreCommit** — If all agree, coordinator sends a `PRE-COMMIT` message. Participants prepare but don't commit yet. This phase ensures that all participants know the coordinator **intends** to commit.
3. **DoCommit** — Coordinator sends final `COMMIT`. Participants make changes permanent.

**How this reduces blocking:** If the coordinator crashes after Phase 2 (PreCommit sent), participants can determine the outcome:
- If a participant received PreCommit → the decision was likely to commit.
- If a participant did NOT receive PreCommit → safe to abort.

**Limitation:** 3PC adds latency (extra network round-trip) and still does **not** fully handle network partitions (a partition can split participants into two groups that make different decisions). For this reason, 3PC is rarely used in practice. Modern systems use consensus protocols (Paxos, Raft) instead.

---

## 4.2 Distributed Query Processing

### 4.2.1 The Challenge

In a distributed system, a single SQL query may need data from multiple sites. The query optimizer must decide:

- **Which fragments** contain the relevant data?
- **Where** should each part of the query execute?
- **How** should intermediate results be transferred between sites?

The key cost factor is **network transfer** — moving data between nodes is orders of magnitude slower than local disk I/O or CPU processing. A good distributed query optimizer minimises total data transfer.

### 4.2.2 Query Processing Steps

```
SQL Query
    ↓
Query Parsing (syntax check, validate tables/columns)
    ↓
Global Query Optimization (consider data locations, network cost, fragment locations)
    ↓
Fragment Localization (map global relations to local fragments)
    ↓
Local Query Optimization (each site optimizes its local sub-query)
    ↓
Distributed Execution (execute sub-queries at relevant sites, transfer intermediates)
    ↓
Result Assembly (combine partial results at the query site)
```

### 4.2.3 Strategies for Distributed Joins

When a join involves data at two different sites, several strategies exist:

| Strategy | How it works | When to use | Data transfer cost |
|---|---|---|---|
| **Ship-whole** | Send the entire smaller table/fragment to the other site. Perform the join locally. | One table is much smaller than the other. | Size of the smaller table. |
| **Semi-join** | Site A sends only the **join column values** to Site B. Site B filters its rows using these values, then sends matching rows back to A. A performs the final join. | Tables are large but join selectivity is high (few matches). | Join column values + matching rows only. |
| **Bloom-filter join** | Site A sends a compact **Bloom filter** (probabilistic data structure) of its join keys. Site B uses it to pre-filter, then sends matching rows. | Very large tables where even sending join columns is expensive. | Bloom filter (small) + matching rows. |
| **Parallel execution** | Break the query into independent sub-queries that execute simultaneously at different sites. Merge results. | Multiple partitions can be scanned in parallel. | Depends on partition scheme. |

### 4.2.4 Cost Factors

| Factor | Relative importance |
|---|---|
| **Network transfer cost** | **Dominant** — moving data between nodes is by far the most expensive. A distributed optimizer primarily minimises data transfer. |
| **Local I/O cost** | Important — disk reads/writes at each site. Addressed by local indexes and buffer management. |
| **Local CPU cost** | Usually least significant — filtering, sorting, and joining are CPU-bound but modern CPUs are fast. |

---

## 4.3 The CAP Theorem

### 4.3.1 Statement and Intuition

The CAP Theorem, proposed by **Eric Brewer** in 2000 and formally proved by **Seth Gilbert and Nancy Lynch** in 2002, is one of the most important theoretical results in distributed systems.

**Statement:** In a distributed data store, it is impossible to simultaneously guarantee all three of the following properties. At most two can be achieved.

| Property | Definition | Practical meaning |
|---|---|---|
| **Consistency (C)** | Every read receives the most recent write or an error. All nodes see the same data at the same time. This is **linearisability** — the strictest form of consistency. | If you write "balance = 500" and immediately read from any node, you always see 500. No stale data window. |
| **Availability (A)** | Every request receives a non-error response, without guarantee it contains the most recent write. | The system never refuses to answer. Every functioning node returns a result, even if it might be outdated. No "service unavailable" errors. |
| **Partition tolerance (P)** | The system continues operating despite arbitrary message loss or network failures between nodes. | If the network cable between London and Mumbai is cut, both sites continue serving requests rather than shutting down. |

### 4.3.2 The Real Choice: CP or AP

The way CAP is often taught — "pick any 2" — is misleading because it implies CA, CP, and AP are equally valid choices. In reality, **network partitions are not optional**. In any distributed system on real hardware, partitions will happen — switches fail, cables are cut, cloud availability zones disconnect, packets are dropped.

Since P is a fact of life (not a choice), the real question becomes:

**When a partition occurs, do you sacrifice Consistency or Availability?**

Imagine: the network between Node A and Node B is down. A write arrives at Node A. A read for the same data arrives at Node B. Two options:

1. **Sacrifice Availability (CP):** Node B refuses to answer (returns error or blocks) because it can't confirm it has the latest data. Users get errors, but no one ever sees stale data.

2. **Sacrifice Consistency (AP):** Node B returns whatever data it has, which might be stale (the write at Node A hasn't propagated yet). Users always get a response, but it might be outdated.

| Choice | Behaviour during partition | Best for | Examples |
|---|---|---|---|
| **CP** | Return errors rather than stale data. | Financial systems, inventory, medical records — where incorrect data can cause real harm. | HBase, MongoDB (default), Zookeeper, etcd |
| **AP** | Return best available (possibly stale) data. | Social media, e-commerce product catalog, analytics — where brief staleness is acceptable. | Cassandra, DynamoDB, CouchDB, DNS |
| **CA** | Only possible without partitions — a single-server system. Not a real distributed option. | Traditional single-server databases. | PostgreSQL on one server, SQLite |

### 4.3.3 CAP in Practice — PACELC

Most real systems are not purely CP or AP — they offer **tunable consistency**. Daniel Abadi extended CAP with the **PACELC** model:

- **If Partition (P):** choose **Availability (A)** or **Consistency (C)**.
- **Else (E)** (no partition, normal operation): choose **Latency (L)** or **Consistency (C)**.

This captures the fact that even during normal (non-partitioned) operation, there is a trade-off: strong consistency requires coordination between nodes, which adds latency.

| System | During partition | Normal operation |
|---|---|---|
| Cassandra | PA (availability) | EL (low latency) |
| HBase | PC (consistency) | EC (consistency) |
| DynamoDB | PA (availability) | EL (low latency) |
| CockroachDB | PC (consistency) | EC (consistency) |

---

## 4.4 ACID vs. BASE

### 4.4.1 ACID

ACID is the cornerstone of traditional relational database reliability. These four properties guarantee that transactions are processed correctly and completely, even in the face of failures and concurrent access.

| Property | Meaning | Why it matters |
|---|---|---|
| **Atomicity** | All or nothing — every operation completes, or none do. | A bank transfer that debits but doesn't credit would lose money. Atomicity prevents this. |
| **Consistency** | Database moves from one valid state to another. All constraints satisfied. | If a rule says "balance ≥ 0", no transaction can violate it, even temporarily from another transaction's view. |
| **Isolation** | Concurrent transactions behave as if serial. Intermediate states invisible. | Two simultaneous transfers don't interfere — each sees a clean snapshot. |
| **Durability** | Once committed, changes survive any failure. | After the bank confirms "transfer successful," the money stays transferred even if the server crashes immediately after. |

ACID provides **strong guarantees** but can limit scalability in distributed settings. The coordination overhead (distributed locks, two-phase commit) adds latency and reduces availability.

### 4.4.2 BASE

BASE is the philosophical alternative adopted by many NoSQL and distributed systems that prioritise **availability and scalability** over immediate consistency. The acronym was coined by **Dan Pritchett** of eBay as a deliberate contrast to ACID.

| Property | Meaning | Practical example |
|---|---|---|
| **Basically Available** | System always returns a response, even if some data is temporarily inconsistent. | Amazon's shopping cart always works. If one data centre is unreachable, the system still accepts orders. |
| **Soft state** | System state may change over time even without new input, as replicas synchronise. | After updating your profile picture, different servers may show the old picture for a few seconds. |
| **Eventually consistent** | If no new updates are made, all replicas converge to the same value. No guarantee about when. | A tweet you post may not be visible to a friend in another country for a few seconds. |

### 4.4.3 Comparison

| Aspect | ACID | BASE |
|---|---|---|
| **Consistency model** | Strong (immediate). | Eventual (may take time to converge). |
| **Availability** | May sacrifice for consistency. | Prioritises availability. |
| **Scalability** | Harder to scale horizontally (coordination overhead). | Designed for horizontal scale. |
| **Use cases** | Financial transactions, inventory counts, bookings. | Social media feeds, analytics dashboards, caching, IoT telemetry. |
| **Application complexity** | Simpler — DB handles correctness. | Higher — app must handle temporary inconsistency (stale reads, conflict resolution). |
| **Write latency** | Higher (coordination with replicas). | Lower (write locally, propagate later). |

**Real-world systems mix both:** A banking platform uses ACID for account balances but eventual consistency for transaction history analytics. An e-commerce platform uses strong consistency for inventory counts (to avoid overselling) but eventual consistency for product reviews and recommendations.

---

## 4.5 Consistency Models and Tunable Consistency

### 4.5.1 Eventual Consistency is a Spectrum

Eventual consistency is not a single model — it's a **spectrum** of increasingly strong guarantees:

| Variant | Guarantee | Example |
|---|---|---|
| **Basic eventual** | All replicas converge eventually. No ordering guarantees. | DNS updates propagate within hours. |
| **Causal consistency** | If operation A causally precedes B (A happened-before B), everyone sees A before B. | If Alice posts "I got a job!" and Bob replies "Congrats!", everyone sees Alice's post before Bob's reply — never the reply without the post. |
| **Read-your-writes** | After a write, the same client always sees its own update on subsequent reads. | You update your profile bio. When you refresh the page, you see your new bio — not the old one from a stale replica. |
| **Monotonic reads** | Once a client reads a value, it never sees an older value on subsequent reads. | If you see 15 likes on a post, you'll never see 12 likes on the next refresh (even if you're load-balanced to a different replica). |
| **Monotonic writes** | Writes by the same client are applied in order. | If you set your name to "Alice" then to "Alicia", all replicas apply the changes in that order — never the reverse. |
| **Session consistency** | Within a session: read-your-writes + monotonic reads. | Common in web applications. Your session is "sticky" to a replica or tracks a consistency token. |

### 4.5.2 Tunable Consistency

Many distributed systems let you choose the consistency level **per query**, trading off between latency and freshness.

**Cassandra's consistency levels:**

| Level | Meaning | Latency | Consistency |
|---|---|---|---|
| `ONE` | Read/write acknowledged by 1 replica. | Fastest | Weakest — may read stale data. |
| `QUORUM` | Acknowledged by a majority (⌊N/2⌋+1) of replicas. | Moderate | Balanced. |
| `ALL` | Acknowledged by all replicas. | Slowest | Strongest — but if any replica is down, the operation fails. |
| `LOCAL_QUORUM` | Quorum within the local data centre only. | Low (no cross-DC latency) | Strong within DC; eventual across DCs. |

### 4.5.3 The Quorum Formula

For a system with N replicas, if you write to W replicas and read from R replicas:

```
Strong consistency guaranteed when:  R + W > N
```

This ensures that every read set (R replicas) overlaps with the latest write set (W replicas) — at least one replica in the read group has the most recent write.

**Examples (N=3):**
- W=2, R=2: 2+2=4 > 3 → **Strong consistency.** ✓
- W=1, R=3: 1+3=4 > 3 → Strong, but reads are slow (must contact all 3). ✓
- W=3, R=1: 3+1=4 > 3 → Strong, but writes are slow (must contact all 3). ✓
- W=1, R=1: 1+1=2 ≤ 3 → **NOT strong.** Reads may miss the latest write. ✗

---

## 4.6 Consensus Protocols

### 4.6.1 The Consensus Problem

2PC's blocking problem and 3PC's partition vulnerability led to the development of **consensus protocols** — algorithms that allow a group of nodes to agree on a value even when some nodes crash. The key difference: consensus protocols tolerate node failures without blocking.

### 4.6.2 Paxos (Lamport, 1990)

Proposed by **Leslie Lamport**, Paxos is the foundational consensus algorithm. It guarantees **safety** (only one value is chosen, and a node only learns a value that was actually proposed) as long as a majority of nodes are operational.

**Roles:** Proposers (propose values), Acceptors (vote on proposals), Learners (learn the agreed value).

**Key properties:**
- Tolerates failure of up to ⌊(N-1)/2⌋ nodes out of N.
- No blocking — if a proposer crashes, another can take over.
- Partition tolerant (majority quorum).
- **Notoriously difficult** to understand and implement correctly. Lamport himself noted it took years for the community to accept the algorithm.

### 4.6.3 Raft (Ongaro & Ousterhout, 2014)

**Raft** was designed as an understandable alternative to Paxos. It provides the same guarantees but decomposes consensus into three clean sub-problems:

1. **Leader election:** One node is elected leader. It manages all client requests and log replication. If the leader crashes, followers detect the absence (via heartbeat timeout) and elect a new leader.

2. **Log replication:** The leader receives client requests, appends them to its log, and replicates log entries to followers. Once a majority acknowledge an entry, it is **committed** and applied to the state machine.

3. **Safety:** Only nodes with up-to-date logs can become leaders. This prevents stale nodes from overwriting committed data.

**Node states:** Follower → Candidate (on election timeout) → Leader (on majority vote).

**Used in:** etcd (Kubernetes), CockroachDB, TiKV, Consul, InfluxDB.

### 4.6.4 Comparison

| Aspect | 2PC | Paxos | Raft |
|---|---|---|---|
| **Purpose** | Atomic commit (all-or-nothing for distributed transactions). | Consensus (agree on a value/log entry). | Consensus (leader-based, understandable). |
| **Blocking?** | Yes — coordinator failure blocks participants. | No — leader election handles failures. | No — automatic leader election. |
| **Partition tolerant?** | No — requires all participants reachable. | Yes — works with majority quorum. | Yes — works with majority quorum. |
| **Fault tolerance** | Coordinator is SPOF. | Tolerates < N/2 failures. | Tolerates < N/2 failures. |
| **Complexity** | Simple to implement. | Very complex — hard to get right. | Moderate — designed for understandability. |
| **Use in practice** | Traditional distributed DBs within one data centre. | Google Spanner (Multi-Paxos). | etcd, CockroachDB, TiKV. |

---

## 4.7 Distributed Recovery

### 4.7.1 The Challenge

Recovery in a distributed system must handle three types of failures:

1. **Node crashes:** A node restarts and must recover its local state using its local WAL (same as single-server recovery).
2. **Network partitions:** Communication between nodes is lost temporarily. Nodes may make independent decisions that later need to be reconciled.
3. **Coordinator failures in 2PC:** The coordinator holds the commit/abort decision. If it crashes, participants are stuck.

### 4.7.2 Handling 2PC Coordinator Failure

1. **Log-based recovery:** When the coordinator restarts, it reads its log. If the log contains a `GLOBAL COMMIT` or `GLOBAL ABORT` record, it re-sends the decision. If neither exists, it sends `GLOBAL ABORT` (the safe default — no promise was made).

2. **Participant inquiry:** A blocked participant can ask other participants what they voted. If any voted NO, everyone can safely abort. If all voted YES but no decision was received, they must wait (this is the blocking case).

3. **Cooperative termination protocol:** Participants communicate with each other (not just the coordinator) to determine the outcome. If any participant knows the decision, it shares it.

### 4.7.3 Why Consensus Beats 2PC for Recovery

Modern distributed databases (CockroachDB, Spanner, TiDB) use Raft or Paxos for replication and leader election. When a leader crashes, a new leader is elected within seconds — no blocking. Transaction decisions are stored in the replicated log, so they survive node failures. This eliminates the 2PC coordinator SPOF.

---

## 4.8 NewSQL

**NewSQL** databases aim to provide the best of both worlds: the **scalability of NoSQL** with the **ACID guarantees of traditional relational databases**.

### Characteristics

| Feature | Detail |
|---|---|
| **SQL interface** | Full SQL support — not a limited subset. Standard query language. |
| **ACID transactions** | Including distributed transactions across multiple nodes. |
| **Horizontal scalability** | Shared-nothing architecture with automatic sharding. Adding nodes increases capacity. |
| **High availability** | Automatic replication with consensus-based failover. |

### How They Achieve This

- **Distributed consensus (Raft/Paxos)** replaces 2PC for many operations. Leader election handles node failures without blocking.
- **MVCC + distributed timestamps** provide isolation without distributed locking. Google Spanner uses TrueTime (GPS + atomic clocks); CockroachDB uses Hybrid Logical Clocks.
- **Automatic sharding and rebalancing** — data is partitioned across nodes and automatically rebalanced as nodes are added/removed.

### Notable Systems

| System | Key Feature |
|---|---|
| **Google Spanner** | Globally distributed. Uses GPS and atomic clocks (TrueTime) for externally consistent timestamps across continents. |
| **CockroachDB** | Open-source. Raft-based. PostgreSQL-compatible wire protocol. Designed for multi-region deployment. |
| **YugabyteDB** | Open-source. Raft-based. Supports both PostgreSQL (SQL) and Cassandra (CQL) APIs. |
| **TiDB** | Open-source. MySQL-compatible. Uses TiKV (Raft-based distributed key-value store) for storage. |
| **VoltDB** | In-memory. Deterministic execution model for extreme throughput. |

---

## 4.9 Data Warehousing Concepts

### 4.9.1 OLTP vs. OLAP

| Aspect | OLTP (Online Transaction Processing) | OLAP (Online Analytical Processing) |
|---|---|---|
| **Purpose** | Day-to-day operations (place an order, transfer money). | Business analysis and reporting (quarterly sales by region). |
| **Queries** | Short, simple, frequent (INSERT, UPDATE, point SELECTs). | Complex, long-running (aggregations, multi-table joins, window functions). |
| **Data** | Current, detailed, normalised. | Historical, summarised, denormalised. |
| **Schema** | Normalised (3NF) to minimise redundancy. | Denormalised (star/snowflake) for query performance. |
| **Users** | Application users, clerks, customers. | Business analysts, data scientists, managers. |
| **Concurrency** | High (thousands of simultaneous transactions). | Low (few analysts running heavy queries). |
| **Example system** | PostgreSQL, MySQL, Oracle (operational). | Amazon Redshift, Google BigQuery, Snowflake, ClickHouse. |

### 4.9.2 Star Schema and Snowflake Schema

**Star Schema:**
- **Fact table** (centre): Contains quantitative measures (sales_amount, quantity, profit) and foreign keys to dimension tables. One row per transaction/event.
- **Dimension tables** (points of the star): Describe the who/what/when/where — Product, Time, Customer, Store. Denormalised for fast joins.

**Snowflake Schema:**
- Same as star, but dimension tables are **normalised** (e.g. DIM_PRODUCT references DIM_CATEGORY, which references DIM_DEPARTMENT).
- Saves storage but requires more joins.

### 4.9.3 ETL vs. ELT

| Approach | Where transformation happens | When to use |
|---|---|---|
| **ETL** (Extract, Transform, Load) | In a staging area **before** loading into the warehouse. | Traditional on-premises warehouses where compute is limited. |
| **ELT** (Extract, Load, Transform) | Inside the warehouse **after** loading raw data. | Cloud warehouses (BigQuery, Snowflake, Redshift) where compute is elastic and cheap. |

---

## Decision Framework — Choosing a Consistency Model

```
Is your data safety-critical (money, health, inventory)?
    YES → Strong consistency (CP / ACID / NewSQL)
    NO  ↓
Can your app tolerate temporary stale reads?
    YES → Eventual consistency (AP / BASE) — scale out freely
    NO  → Causal or session consistency (middle ground)
```

**Best practice: Mix models within the same system.**
- ACID for the payment/order service.
- Eventual consistency for the recommendation engine.
- Session consistency for user profile reads.

---

*End of Session 4*
