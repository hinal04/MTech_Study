# DBMS — Quick Revision Notes
> BITS Pilani | Sessions 1–4 | Last-Minute Reference

---

## Session 1: Foundations

### Data → Information → Database → DBMS

| Term | Definition |
|------|-----------|
| **Data** | Raw facts and figures without context (e.g., "42", "Hinal") |
| **Information** | Data processed with meaning and context (e.g., "Hinal scored 42") |
| **Database** | Organised collection of related data stored for easy access |
| **DBMS** | Software system to create, manage, and query databases (e.g., MySQL, PostgreSQL) |

### File System Problems

- **Data redundancy** — same data stored in multiple files
- **Data inconsistency** — conflicting copies of the same data
- **No concurrent access** — multiple users can't safely read/write simultaneously
- **No security** — no fine-grained access control
- **Data isolation** — data scattered across files in different formats
- **Integrity problems** — hard to enforce constraints (e.g., balance ≥ 0)
- **No atomicity** — partial updates on failure

### DBMS Advantages (7)

| # | Advantage | Meaning |
|---|----------|---------|
| 1 | **Data independence** | Change storage/schema without changing applications |
| 2 | **Reduced redundancy** | Centralised data, normalisation minimises duplication |
| 3 | **Data sharing** | Multiple users/applications access same database |
| 4 | **Integrity** | Constraints enforced at database level |
| 5 | **Security** | Authentication, authorisation, access control |
| 6 | **Backup & Recovery** | Automatic backup, transaction logs, crash recovery |
| 7 | **Concurrent access** | Locking, MVCC → safe multi-user access |

### Types of Data

| Type | Description | Examples |
|------|------------|---------|
| **Structured** | Fixed schema, rows & columns | SQL tables, CSV, spreadsheets |
| **Semi-structured** | Self-describing, flexible schema | JSON, XML, YAML |
| **Unstructured** | No predefined model | Images, videos, text, audio |

### Relational Model

| Relational Term | Common Name |
|----------------|-------------|
| **Relation** | Table |
| **Tuple** | Row / Record |
| **Attribute** | Column / Field |
| **Domain** | Set of allowed values for an attribute |
| **Degree** | Number of attributes (columns) |
| **Cardinality** | Number of tuples (rows) |

### Keys

| Key | Definition |
|-----|-----------|
| **Super Key** | Any set of attributes that uniquely identifies a tuple |
| **Candidate Key** | Minimal super key (no proper subset is a super key) |
| **Primary Key** | Chosen candidate key — uniquely identifies each tuple, **NOT NULL** |
| **Alternate Key** | Candidate keys not chosen as primary key |
| **Foreign Key** | Attribute in one relation that references the primary key of another |
| **Composite Key** | Key consisting of two or more attributes |

### Constraints

| Constraint | Rule |
|-----------|------|
| **Domain** | Attribute value must be from its defined domain |
| **Key** | Values of key attributes must be unique |
| **Entity Integrity** | Primary key **cannot be NULL** |
| **Referential Integrity** | Foreign key must match an existing primary key or be NULL |

### ER Model

- **Entity**: Real-world object with attributes (rectangle)
- **Attribute types**:
  - **Simple** — atomic (e.g., Age)
  - **Composite** — can be divided (e.g., Name → First + Last)
  - **Multivalued** — multiple values (e.g., Phone numbers) — double oval
  - **Derived** — computed from other attributes (e.g., Age from DOB) — dashed oval
  - **Key attribute** — underlined
- **Relationship**: Association between entities (diamond)
- **Cardinality**: 1:1, 1:M (1:N), M:N
- **Participation**:
  - **Total** (double line) — every entity must participate
  - **Partial** (single line) — some entities may not participate
- **Weak Entity**: No primary key of its own, depends on a **strong/owner entity** via an **identifying relationship** (double diamond, double rectangle)

### Normalisation

| Normal Form | Rule | Eliminates |
|-------------|------|-----------|
| **1NF** | All attributes are **atomic** (no repeating groups/multivalued) | Repeating groups |
| **2NF** | 1NF + No **partial dependency** (non-key attribute depends on part of composite PK) | Partial dependencies |
| **3NF** | 2NF + No **transitive dependency** (non-key depends on another non-key) | Transitive dependencies |
| **BCNF** | Every **determinant** is a **super key** (stricter than 3NF) | All remaining anomalies |

> **Memory**: 1NF = atomic → 2NF = full functional dep on whole PK → 3NF = depends on "the key, the whole key, and nothing but the key" → BCNF = every determinant is a superkey

---

## Session 2: SQL & Transactions

### SQL Command Categories

| Category | Commands | Purpose |
|----------|---------|---------|
| **DDL** (Data Definition) | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` | Define/modify schema |
| **DML** (Data Manipulation) | `INSERT`, `UPDATE`, `DELETE`, `SELECT` | Manipulate data |
| **DCL** (Data Control) | `GRANT`, `REVOKE` | Access permissions |
| **TCL** (Transaction Control) | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Manage transactions |

### Joins — One-Liner Each

| Join | What it returns |
|------|----------------|
| **INNER JOIN** | Only rows with matching keys in **both** tables |
| **LEFT (OUTER) JOIN** | All rows from left table + matching rows from right (NULL if no match) |
| **RIGHT (OUTER) JOIN** | All rows from right table + matching rows from left (NULL if no match) |
| **FULL (OUTER) JOIN** | All rows from both tables (NULL where no match on either side) |
| **CROSS JOIN** | Cartesian product — every row of A paired with every row of B |
| **SELF JOIN** | Table joined with itself (uses aliases) |

### Subqueries

| Type | Returns | Example |
|------|---------|---------|
| **Scalar** | Single value | `WHERE salary > (SELECT AVG(salary) FROM emp)` |
| **Row** | Single row | `WHERE (dept, salary) = (SELECT dept, MAX(salary) FROM emp)` |
| **Table** | Multiple rows/columns | `FROM (SELECT ... ) AS sub` |
| **Correlated** | Depends on outer query (re-executes per row) | `WHERE salary > (SELECT AVG(salary) FROM emp e2 WHERE e2.dept = e1.dept)` |
| **EXISTS** | Boolean — true if subquery returns any rows | `WHERE EXISTS (SELECT 1 FROM orders WHERE orders.cid = c.id)` |

### ACID Properties

| Property | Meaning | Ensures |
|----------|---------|---------|
| **Atomicity** | All or nothing — transaction fully completes or fully rolls back | No partial updates |
| **Consistency** | Database moves from one valid state to another | Constraints preserved |
| **Isolation** | Concurrent transactions don't interfere with each other | As if serial execution |
| **Durability** | Once committed, changes survive crashes | Persisted to disk/logs |

### Transaction States

```
Active → Partially Committed → Committed
  ↓                ↓
Failed ←──────── Failed
  ↓
Aborted (rollback complete → restart or kill)
```

- **Active**: Transaction executing operations
- **Partially Committed**: Final operation executed, awaiting commit
- **Committed**: Changes permanently saved
- **Failed**: Error detected, cannot proceed
- **Aborted**: Rolled back, database restored to prior state

### Schema Evolution & Versioning

- **Schema Evolution**: Modifying the database schema while preserving existing data
- **Schema Versioning**: Maintaining multiple schema versions simultaneously
- **Expand-Contract pattern**:
  1. **Expand** — add new columns/tables alongside old ones
  2. **Migrate** — move data from old to new format
  3. **Contract** — remove old columns/tables once all clients updated

### Denormalisation

- **What**: Intentionally adding redundancy (duplicating data) to a normalised schema
- **Why**: Improve **read performance** by reducing joins
- **Trade-off**: Faster reads ↔ slower writes, risk of inconsistency
- **Common techniques**: Pre-joined tables, materialized views, redundant columns, summary tables

### RDBMS Limitations → NoSQL Motivation

- Fixed schema is rigid for evolving data
- Horizontal scaling (sharding) is complex in RDBMS
- Joins are expensive at massive scale
- Not ideal for unstructured/semi-structured data
- → **NoSQL**: flexible schema, horizontal scaling, high throughput, eventual consistency

---

## Session 3: Distributed Databases

### Core Definitions

- **DDB (Distributed Database)**: A logically interrelated collection of shared data physically distributed over a network
- **DDBMS**: Software that manages a DDB, making distribution transparent to users

### 7 Characteristics of DDBMS

1. Data stored across multiple sites
2. Sites connected by a network
3. Data is logically related
4. Single logical database, multiple physical fragments
5. Common interface for all data
6. Each site can operate independently
7. Transactions can span multiple sites

### 6 Advantages of DDB

1. **Local autonomy** — each site controls its own data
2. **Improved reliability/availability** — no single point of failure
3. **Improved performance** — data close to users, parallel processing
4. **Scalability** — add sites incrementally
5. **Transparency** — users see a single unified database
6. **Cost efficiency** — cheaper nodes vs one supercomputer

### Centralised vs Distributed

| Feature | Centralised | Distributed |
|---------|------------|-------------|
| Data location | Single site | Multiple sites |
| Single point of failure | Yes | No (if replicated) |
| Scalability | Vertical (scale up) | Horizontal (scale out) |
| Network dependency | Low | High |
| Complexity | Simple | Complex |
| Performance | Limited by one server | Parallel processing |

### Types of Distributed Databases

| Type | Description |
|------|------------|
| **Homogeneous** | Same DBMS at all sites, easy coordination |
| **Heterogeneous** | Different DBMS at different sites, needs translation |
| **Multidatabase (Federated)** | Autonomous databases cooperating, each retains local control |

### Local vs Global Transactions

- **Local transaction**: Accesses data at **one site** only
- **Global transaction**: Accesses data at **multiple sites**, requires distributed coordination

### Fragmentation

| Type | Splits by | Operator | Reconstruct |
|------|-----------|----------|-------------|
| **Horizontal** | Rows (σ — selection) | σ (selection predicate) | UNION |
| **Vertical** | Columns (π — projection) | π (projection) + PK in each fragment | JOIN on PK |
| **Mixed/Hybrid** | Combination of both | σ then π or vice versa | UNION + JOIN |

**Rules of fragmentation**:
- **Completeness** — every data item in at least one fragment
- **Reconstruction** — original relation can be rebuilt from fragments
- **Disjointness** — no overlap (horizontal), except PK must repeat (vertical)

**Primary vs Derived Horizontal Fragmentation**:
- **Primary**: Fragment based on predicates on the relation's own attributes
- **Derived**: Fragment based on predicates on a **related** relation (follows the fragmentation of another table via FK)

### Data Allocation Strategies

| Strategy | Description | Trade-off |
|----------|------------|-----------|
| **Non-replicated** | Each fragment at exactly one site | No redundancy, single point of failure |
| **Partially replicated** | Some fragments replicated at selected sites | Balance of availability and update cost |
| **Fully replicated** | Every fragment at every site | Max availability, highest update cost |

### Replication Strategies

| Type | How | Pros | Cons |
|------|-----|------|------|
| **Synchronous** | All replicas updated in same transaction | Strong consistency | High latency, reduced availability |
| **Asynchronous** | Replicas updated after commit | Low latency | Temporary inconsistency |

| Topology | Write | Read | Conflict |
|----------|-------|------|----------|
| **Leader-Follower** | Single leader | Any replica | No write conflicts |
| **Multi-Leader** | Multiple leaders | Any replica | Write conflicts possible |
| **Leaderless** | Any node (quorum) | Any node (quorum) | Resolved via versioning |

### Quorum-Based Protocol

- **N** = total replicas, **W** = write quorum, **R** = read quorum
- **Strong consistency**: **W + R > N**
- **Read-optimised**: W = N, R = 1
- **Write-optimised**: W = 1, R = N
- **Balanced**: W = R = (N/2) + 1

### Replication Strategy Comparison (10 Dimensions)

| Dimension | Leader-Follower | Multi-Leader | Leaderless |
|-----------|:--------------:|:------------:|:----------:|
| Write throughput | Low (single writer) | Medium | High |
| Read throughput | High | High | High |
| Write latency | Low (one node) | Medium | Depends on W |
| Read latency | Low | Low | Depends on R |
| Consistency | Strong (sync) / Eventual (async) | Eventual | Tunable (quorum) |
| Availability | Medium | High | High |
| Conflict handling | None needed | Conflict resolution needed | Version vectors |
| Failover complexity | Leader election needed | Less impacted | No leader to fail |
| Implementation | Simple | Complex | Complex |
| Use case | Most common | Multi-datacenter writes | Highly available stores |

### Distributed Concurrency Control

| Approach | Description |
|----------|------------|
| **Centralised locking** | One site manages all locks — simple but SPOF |
| **Primary-copy locking** | Each data item has a primary site that manages its lock |
| **Distributed locking** | Lock managed by the site where the data resides — no SPOF |

### Distributed Deadlock Detection

| Method | How | Issue |
|--------|-----|-------|
| **Centralised** | One coordinator builds global wait-for graph | SPOF, phantom deadlocks |
| **Distributed** | Each site detects locally, cooperates for global detection | Complex message passing |
| **Timeout** | Assume deadlock if transaction waits too long → abort | May abort non-deadlocked transactions |

---

## Session 4: Distributed Transactions & CAP

### Distributed Transaction Anatomy

- **Coordinator**: The site that initiates and manages the global transaction
- **Participating Sites**: Sites that execute sub-transactions locally
- **Local Transaction Manager (TM)**: Manages local operations, locking, recovery at each site

### Challenges of Distributed Transactions

- **Atomicity across sites** — all sites commit or all abort
- **Site failures** — a participating site may crash mid-transaction
- **Communication failures** — network partition or message loss
- **Concurrency** — coordination of locks across sites
- **Partial failures** — some sites succeed, others fail

### Two-Phase Commit Protocol (2PC)

**Phase 1 — Prepare (Voting)**:
1. Coordinator sends `PREPARE` to all participants
2. Each participant:
   - If ready → writes changes to log, sends **VOTE-COMMIT**
   - If not → sends **VOTE-ABORT**

**Phase 2 — Commit/Abort (Decision)**:
1. If **all** participants voted COMMIT → Coordinator sends `GLOBAL-COMMIT`
2. If **any** participant voted ABORT → Coordinator sends `GLOBAL-ABORT`
3. Participants execute the decision and send **ACK**

```
Coordinator                    Participants
    |--- PREPARE ------------------>|
    |<-- VOTE-COMMIT / VOTE-ABORT --|
    |--- GLOBAL-COMMIT / ABORT ---->|
    |<-- ACK -----------------------|
```

### 2PC Limitations

| Limitation | Description |
|-----------|------------|
| **Blocking** | Participants block while waiting for coordinator's decision |
| **Coordinator SPOF** | If coordinator fails after Phase 1, participants are stuck |
| **Communication overhead** | 4n messages for n participants (PREPARE + VOTE + DECISION + ACK) |
| **Scalability** | Performance degrades as number of participants increases |
| **Latency** | Two round-trip delays before commit |

### Query Execution Strategies

| Strategy | How | When to Use |
|----------|-----|-------------|
| **Data Shipping** | Move data to the query site, process locally | Small data, powerful query site |
| **Query Shipping** | Send query to the data site, return results | Large data, processing near data |
| **Hybrid** | Combine both — move some data, process some remotely | Optimise based on cost model |

### CAP Theorem

> In a distributed system, you can guarantee at most **2 out of 3**:

| Property | Meaning |
|----------|---------|
| **C** — Consistency | Every read gets the most recent write (all nodes see same data) |
| **A** — Availability | Every request receives a response (no timeouts) |
| **P** — Partition Tolerance | System continues operating despite network partitions |

**In practice**: Network partitions **will** happen → must support **P** → choose between:
- **CP** — Consistent + Partition-tolerant (sacrifice availability during partitions) — e.g., HBase, MongoDB
- **AP** — Available + Partition-tolerant (sacrifice consistency during partitions) — e.g., Cassandra, DynamoDB
- **CA** — Only possible in non-distributed (single-node) systems — e.g., traditional RDBMS

### BASE Properties

| Property | Meaning |
|----------|---------|
| **B**asically **A**vailable | System guarantees availability (may return stale data) |
| **S**oft state | State may change over time even without input (due to async replication) |
| **E**ventual consistency | If no new updates, all replicas will eventually converge to the same value |

### ACID vs BASE Comparison

| Dimension | ACID | BASE |
|-----------|------|------|
| Consistency model | Strong (immediate) | Eventual |
| Availability | May sacrifice for consistency | Prioritises availability |
| Focus | Correctness | Performance & availability |
| Scaling | Vertical (scale up) | Horizontal (scale out) |
| Locking | Pessimistic (locks) | Optimistic (versioning) |
| Schema | Rigid | Flexible |
| Transactions | Distributed 2PC | Saga, compensation |
| Use when | Correctness critical (banking, inventory) | Availability critical (social media, IoT) |
| Examples | MySQL, PostgreSQL, Oracle | Cassandra, DynamoDB, MongoDB |
| Partition handling | Blocks or aborts | Continues with stale data |

### Decision Guide: ACID vs BASE

```
Choose ACID when:                    Choose BASE when:
✓ Financial transactions             ✓ Social media feeds
✓ Inventory management               ✓ IoT sensor data
✓ Healthcare records                  ✓ Shopping carts
✓ Booking systems                     ✓ Real-time analytics
✓ Correctness > availability          ✓ Availability > consistency
```

---

## Quick Reference — Key Comparisons

### All Replication Topologies at a Glance

| | Leader-Follower | Multi-Leader | Leaderless |
|-|:-:|:-:|:-:|
| Write node | 1 leader | Multiple leaders | Any (quorum) |
| Consistency | Strong/Eventual | Eventual | Tunable |
| Conflict | None | Yes (resolve) | Yes (version vectors) |
| Failover | Leader election | Less impact | No leader needed |

### Fragmentation Quick Check

| | Horizontal | Vertical | Mixed |
|-|:---:|:---:|:---:|
| Splits | Rows | Columns | Both |
| Operator | σ (selection) | π (projection) | σ + π |
| Reconstruct | UNION | JOIN (on PK) | UNION + JOIN |

### CAP in One Line

> **P is mandatory** → pick **CP** (consistency) or **AP** (availability)

---

*Good luck with your exam!* 🎯
