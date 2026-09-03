# Session 4: Distributed Transactions and Consistency — Questions & Answers

> 15 questions covering: 2PC/3PC, Distributed Query Processing, CAP Theorem, ACID vs BASE, Eventual Consistency, Tunable Consistency, Paxos/Raft, NewSQL, Data Warehousing.

---

### Q1. Explain the Two-Phase Commit (2PC) protocol in detail. What is its critical weakness?

**Answer:**

**2PC** ensures atomicity for distributed transactions — all nodes commit together or abort together.

**Phase 1 — Voting (Prepare):**
1. Coordinator sends `PREPARE` to all participants.
2. Each participant checks locally: can I commit? If yes → writes changes to durable log, sends `VOTE YES`. If no → sends `VOTE NO`, aborts locally.

**Phase 2 — Decision:**
1. If ALL voted YES → coordinator sends `GLOBAL COMMIT` → all commit and ACK.
2. If ANY voted NO → coordinator sends `GLOBAL ABORT` → all rollback and ACK.

**Critical weakness — Blocking:** If the coordinator crashes after Phase 1 (after collecting YES votes but before sending the decision), participants are **stuck**. They voted YES and promised to commit — they can't unilaterally abort or commit. They hold locks and wait indefinitely for the coordinator to recover.

This makes 2PC unsuitable for wide-area networks where coordinator failure + network partitions can keep participants blocked for extended periods.

---

### Q2. How does Three-Phase Commit (3PC) improve on 2PC? What limitation remains?

**Answer:**

3PC adds a **PreCommit** phase between voting and commit:

1. **CanCommit?** — Same as 2PC Phase 1 (collect votes).
2. **PreCommit** — If all voted YES, coordinator sends PreCommit (participants prepare but don't commit).
3. **DoCommit** — Coordinator sends final commit.

**Improvement:** If the coordinator crashes after PreCommit:
- Participants who received PreCommit know the decision was to commit → they can commit.
- Participants who did NOT receive PreCommit → safe to abort.

**Remaining limitation:** 3PC does **not** handle network partitions correctly. A partition can split participants into two groups — one that received PreCommit (commits) and one that didn't (aborts). The database becomes inconsistent.

Modern systems use **Paxos/Raft** instead — they handle both crashes and partitions.

---

### Q3. Describe the key steps and cost factors in distributed query processing.

**Answer:**

**Steps:**
1. **Query Parsing** — Syntax check, validate table/column names.
2. **Global Optimization** — Consider data locations, fragment placement, network costs.
3. **Fragment Localization** — Map global table names to physical fragments at specific sites.
4. **Local Optimization** — Each site optimizes its local sub-query.
5. **Distributed Execution** — Execute sub-queries at relevant sites; transfer intermediates.
6. **Result Assembly** — Combine partial results at the query origination site.

**Cost factors (in order of importance):**

| Factor | Why it matters |
|---|---|
| **Network transfer** | **Dominant** — moving data between nodes is the most expensive operation. A good optimizer minimises bytes transferred. |
| **Local I/O** | Disk reads/writes at each site. Addressed by local indexes and buffer caching. |
| **Local CPU** | Filtering, sorting, joining. Usually least significant on modern hardware. |

**Join strategies:** Ship-whole (send small table), Semi-join (send join keys first to pre-filter), Bloom-filter join (send compact filter), Parallel execution (independent sub-queries simultaneously).

---

### Q4. State and explain the CAP theorem. Why is "pick 2 of 3" misleading?

**Answer:**

**CAP Theorem** (Brewer 2000, proved Gilbert & Lynch 2002): In a distributed data store, at most 2 of 3 properties can be guaranteed simultaneously:

| Property | Definition |
|---|---|
| **Consistency (C)** | Every read returns the most recent write (linearisability). All nodes see the same data. |
| **Availability (A)** | Every request gets a non-error response. System always responds. |
| **Partition Tolerance (P)** | System operates despite network failures between nodes. |

**Why "pick 2" is misleading:** Network partitions **will** happen in any real distributed system — cables break, switches fail, cloud regions disconnect. Since **P is mandatory** (not a choice), the real question is:

**During a partition, sacrifice C or A?**

- **CP:** Return errors/block rather than stale data. (HBase, Zookeeper, MongoDB default)
- **AP:** Return possibly stale data rather than errors. (Cassandra, DynamoDB, CouchDB)
- **CA:** Only possible without partitions — single-server systems. Not a real distributed option.

Most real systems offer **tunable consistency**, not a binary CP/AP choice.

---

### Q5. What is the PACELC model? How does it extend CAP?

**Answer:**

**PACELC** (Daniel Abadi) adds a second dimension to CAP:

- **If Partition (P):** choose **Availability (A)** or **Consistency (C)**.
- **Else (E)** (normal operation, no partition): choose **Latency (L)** or **Consistency (C)**.

CAP only describes behaviour during partitions. PACELC recognises that even during normal operation, there's a trade-off: strong consistency requires inter-node coordination, which adds latency.

| System | During Partition | Normal Operation |
|---|---|---|
| Cassandra | PA (availability) | EL (low latency) |
| HBase | PC (consistency) | EC (consistency) |
| DynamoDB | PA (availability) | EL (low latency) |
| CockroachDB | PC (consistency) | EC (consistency) |

---

### Q6. Compare ACID and BASE in detail with examples.

**Answer:**

| Aspect | ACID | BASE |
|---|---|---|
| **Consistency** | Strong (immediate). All reads return latest write. | Eventual. Replicas converge over time. |
| **Availability** | May sacrifice — reject requests if consistency can't be guaranteed. | Prioritises — always respond, even with stale data. |
| **Scalability** | Harder horizontally (distributed locks, 2PC). | Built for horizontal scale (no coordination overhead). |
| **Use cases** | Bank transfers, inventory, flight bookings. | Social feeds, product reviews, analytics, IoT. |
| **App complexity** | Simpler — DB handles correctness. | Higher — app handles stale reads, conflicts. |
| **Write latency** | Higher (synchronous replication, lock coordination). | Lower (write locally, replicate async). |

**Real-world mix:** A bank uses ACID for balance updates but BASE for generating monthly statements (analytics). An e-commerce site uses ACID for inventory (prevent overselling) but BASE for product recommendations.

---

### Q7. Explain eventual consistency and its variants.

**Answer:**

**Eventual consistency:** If no new updates are made, all replicas will eventually converge to the same value. No guarantee about when.

**Variants (increasingly strong):**

| Variant | Guarantee | Example |
|---|---|---|
| **Causal** | If A happened-before B, everyone sees A before B. | Post before reply is always visible in that order. |
| **Read-your-writes** | After writing, the same client always sees its own update. | You update your bio → you always see the new bio, not the old one. |
| **Monotonic reads** | Once you see a value, you never see an older value. | If you see 15 likes, next refresh never shows 12. |
| **Monotonic writes** | Same client's writes applied in order. | Setting name to "Alice" then "Alicia" → never applied in reverse. |
| **Session** | Read-your-writes + monotonic reads within a session. | Most web apps use this — your session is consistent. |

---

### Q8. What is tunable consistency? Explain the quorum formula R + W > N.

**Answer:**

**Tunable consistency** lets you choose the consistency level per query, trading latency for freshness.

**Cassandra example:**

| Level | Replicas that must ACK | Speed | Consistency |
|---|---|---|---|
| `ONE` | 1 | Fastest | Weakest (may read stale) |
| `QUORUM` | ⌊N/2⌋ + 1 | Moderate | Strong if paired with quorum writes |
| `ALL` | N | Slowest | Strongest (but fails if any replica down) |

**Quorum formula: R + W > N**

Where R = replicas read, W = replicas written, N = total replicas. If this holds, at least one replica in every read set has the latest write.

**N=3 examples:**
- W=2, R=2: 4 > 3 → **Strong** ✓
- W=1, R=1: 2 ≤ 3 → **Not strong** ✗ (read might miss latest write)
- W=3, R=1: 4 > 3 → **Strong** ✓ (but writes are slow)

---

### Q9. A system has N=5 replicas. If W=3, R=3: is strong consistency guaranteed? What about W=3, R=2?

**Answer:**

**Case 1: W=3, R=3, N=5**
R + W = 6 > 5 → **Yes, strong consistency guaranteed.** At least one replica in the read set must overlap with the write set.

**Case 2: W=3, R=2, N=5**
R + W = 5 = 5 → **No, NOT guaranteed.** The formula requires R + W **strictly greater than** N. With equality, a scenario exists where the read set and write set have zero overlap (due to timing).

**Fix:** Increase R to 3 (making R+W=6>5) or increase W to 4 (R+W=6>5).

---

### Q10. Compare 2PC, 3PC, Paxos, and Raft. When would you use each?

**Answer:**

| Protocol | Purpose | Blocking? | Partition tolerant? | Fault tolerance | When to use |
|---|---|---|---|---|---|
| **2PC** | Atomic distributed commit | Yes (coordinator crash) | No | Coordinator = SPOF | Traditional distributed transactions within a single data centre. |
| **3PC** | Reduce 2PC blocking | Partially | Not fully | Better than 2PC | Theoretical improvement; rarely used in practice. |
| **Paxos** | Distributed consensus | No (leader election) | Yes (majority quorum) | < N/2 failures | Foundation for distributed systems. Used in Google Spanner (Multi-Paxos). |
| **Raft** | Consensus (understandable) | No (automatic leader election) | Yes (majority quorum) | < N/2 failures | etcd (Kubernetes), CockroachDB, TiKV, Consul. Preferred over Paxos for new implementations due to clarity. |

**Modern pattern:** Use Raft/Paxos for replication and leader election, build transaction protocols on top. This eliminates 2PC's coordinator SPOF.

---

### Q11. Explain the difference between Consistency in CAP vs Consistency in ACID.

**Answer:**

Despite sharing the same word, these are **completely different concepts:**

| ACID Consistency | CAP Consistency |
|---|---|
| Database moves from one **valid state** to another. All integrity constraints (PK, FK, CHECK, domain) satisfied before and after a transaction. | Every read returns the **most recent write** — **linearisability**. All nodes see the same data at the same time. |
| About **correctness of data** (constraint satisfaction). | About **freshness and agreement** across distributed replicas. |
| Enforced by constraint checking at commit time. | Enforced by replication and consensus protocols. |
| Relevant even in a **single-node** database. | Only relevant in **distributed** systems with replicated data. |

A system can satisfy ACID-C (all constraints valid) while violating CAP-C (replicas temporarily disagree). The terms should never be confused.

---

### Q12. What is NewSQL? How does it achieve ACID + horizontal scalability?

**Answer:**

**NewSQL** databases combine relational SQL + ACID transactions + horizontal scalability + high availability.

**How they achieve this:**
- **Consensus protocols (Raft/Paxos)** for leader election and log replication — replaces 2PC, eliminates coordinator SPOF.
- **MVCC + distributed timestamps** for isolation without distributed locking. Spanner uses TrueTime (GPS clocks); CockroachDB uses Hybrid Logical Clocks.
- **Automatic sharding** — data partitioned across nodes, rebalanced automatically as nodes are added/removed.

| System | Key Feature |
|---|---|
| Google Spanner | Global distribution, TrueTime (GPS + atomic clocks). |
| CockroachDB | Open-source, Raft, PostgreSQL-compatible. |
| YugabyteDB | Raft, PostgreSQL + Cassandra APIs. |
| TiDB | MySQL-compatible, TiKV storage layer. |

---

### Q13. Compare OLTP and OLAP. What is a star schema?

**Answer:**

| Aspect | OLTP | OLAP |
|---|---|---|
| Purpose | Day-to-day operations (orders, transfers). | Business analysis (quarterly reports). |
| Queries | Short, simple, frequent. | Complex, long-running aggregations. |
| Data | Current, detailed, normalised (3NF). | Historical, summarised, denormalised. |
| Users | App users, clerks. | Analysts, managers. |
| Systems | PostgreSQL, MySQL, Oracle. | Redshift, BigQuery, Snowflake. |

**Star schema:** A fact table (measures + FKs) at the centre, surrounded by denormalised dimension tables (who/what/when/where). Optimised for analytical queries with fast joins.

**Snowflake schema:** Same as star but dimension tables are normalised. Saves space, needs more joins.

---

### Q14. How do you choose between strong and eventual consistency for a system?

**Answer:**

**Decision framework:**

1. **Is the data safety-critical?** (money, health records, inventory)
   - **Yes → Strong consistency** (ACID / CP / NewSQL). Incorrect data could cause financial loss, health risk, or overselling.

2. **Can the app tolerate temporary stale reads?**
   - **Yes → Eventual consistency** (BASE / AP). Enjoy better scalability and lower latency. Suitable for social feeds, recommendations, analytics.

3. **Need a middle ground?**
   - **Causal or session consistency** — stronger than basic eventual, cheaper than full linearisability. Good for user profile reads, activity feeds.

**Best practice: Mix models in the same system.**
- ACID for the payment service (correctness critical).
- Eventual consistency for the recommendation engine (staleness is fine).
- Session consistency for user profile reads (user sees own changes immediately).

---

### Q15. Explain distributed recovery. How does a system handle 2PC coordinator failure?

**Answer:**

**Types of distributed failure:**
1. **Node crash** — Node restarts and recovers local state via its WAL (UNDO/REDO).
2. **Network partition** — Nodes can't communicate temporarily. Reconcile when network heals.
3. **2PC coordinator crash** — Participants holding YES votes are stuck.

**Handling coordinator failure:**

1. **Log-based:** Coordinator reads its log on restart. If `GLOBAL COMMIT` logged → re-send commit. If `GLOBAL ABORT` logged → re-send abort. If neither → abort (safe default, no promise was made).

2. **Participant inquiry:** Blocked participant asks other participants what they voted. If any voted NO → all can abort. If all voted YES but no decision received → must wait (blocking case).

3. **Cooperative termination:** Participants talk to each other (not just coordinator) to determine outcome. If any participant knows the decision (received it before crash), it shares with others.

**Modern solution:** Use Raft/Paxos for replication. When a leader (coordinator) crashes, a new leader is elected within seconds. Transaction decisions stored in the replicated log survive node failures. No blocking.

---

*End of Session 4 Questions & Answers*
