# DBMS — Topics Coverage Map (All 4 Sessions)

> BITS Pilani — **SSZG507: Modern Database Systems**
> Quick reference showing what each session covers — use this to check you haven't missed any topic.

---

## Session 1: Foundations of Structured Data Management (54 slides)

| Section | Topics Covered |
|---|---|
| **Data Fundamentals** | Data vs Information vs Database vs DBMS definitions |
| **File System Problems** | Data redundancy, inconsistency, no concurrent access, no security, data isolation, integrity problems, no atomicity |
| **DBMS Advantages** | 7 advantages: data independence, reduced redundancy, data sharing, integrity, security, backup/recovery, concurrent access |
| **Data Types** | Structured (SQL, tables), Semi-structured (JSON, XML), Unstructured (images, text) |
| **Relational Model** | Relation=table, tuple=row, attribute=column, domain, degree, cardinality |
| **Keys** | Super key, candidate key, primary key, alternate key, foreign key, composite key — each with definition and examples |
| **Constraints** | Domain constraint, key constraint, entity integrity (PK not null), referential integrity (FK matches PK) |
| **ER Model** | Entity (rectangle), attributes (simple/composite/multivalued/derived/key), relationships (diamond), cardinality (1:1, 1:M, M:N), participation (total/partial), weak entity (double rectangle + identifying relationship) |
| **Normalisation** | 1NF (atomic values, no repeating groups), 2NF (no partial dependency), 3NF (no transitive dependency — "the key, the whole key, nothing but the key"), BCNF (every determinant is a superkey) |
| **Functional Dependencies** | Definition, Armstrong's axioms, closure, canonical cover |
| **Decomposition** | Lossless join, dependency preservation |
| **Denormalisation** | Intentionally adding redundancy for read performance, pre-joined tables, materialized views |

---

## Session 2: SQL Fundamentals and Transactions (30 slides)

| Section | Topics Covered |
|---|---|
| **SQL Command Categories** | DDL (CREATE, ALTER, DROP, TRUNCATE), DML (INSERT, UPDATE, DELETE, SELECT), DCL (GRANT, REVOKE), TCL (COMMIT, ROLLBACK, SAVEPOINT) |
| **DDL Details** | CREATE TABLE with constraints, ALTER (ADD/DROP/MODIFY column), DROP TABLE, TRUNCATE vs DELETE |
| **DML Details** | INSERT (single/multiple rows), UPDATE with WHERE, DELETE with WHERE, SELECT with WHERE/ORDER BY/GROUP BY/HAVING |
| **Joins** | INNER JOIN, LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN, CROSS JOIN, SELF JOIN — each with one-line description and use case |
| **Subqueries** | Scalar (single value), Row (single row), Table (multiple rows), Correlated (depends on outer query), EXISTS |
| **Aggregate Functions** | COUNT, SUM, AVG, MIN, MAX, GROUP BY, HAVING |
| **ACID Properties** | Atomicity (all or nothing), Consistency (valid state to valid state), Isolation (concurrent transactions don't interfere), Durability (committed changes survive crashes) |
| **Transaction States** | Active → Partially Committed → Committed / Active → Failed → Aborted, state diagram |
| **Schema Evolution** | Modifying schema while preserving data, expand-contract pattern (add new → migrate → remove old) |
| **Schema Versioning** | Maintaining multiple schema versions simultaneously |
| **RDBMS Limitations** | Fixed schema rigidity, horizontal scaling difficulty, expensive joins at scale, not ideal for unstructured data → NoSQL motivation |

---

## Session 3: Distributed Database Foundations (27 slides)

| Section | Topics Covered |
|---|---|
| **DDB Definition** | Logically interrelated collection of shared data physically distributed over a network |
| **DDBMS Definition** | Software that manages a DDB, making distribution transparent to users |
| **7 Characteristics** | Data at multiple sites, connected by network, logically related, single logical database, common interface, independent site operation, transactions span sites |
| **6 Advantages** | Local autonomy, improved reliability/availability, improved performance, scalability, transparency, cost efficiency |
| **Centralised vs Distributed** | Comparison table: data location, single point of failure, scalability, network dependency, complexity, performance |
| **DDB Types** | Homogeneous (same DBMS everywhere), Heterogeneous (different DBMS), Multidatabase/Federated (autonomous DBs cooperating) |
| **Local vs Global Transactions** | Local = one site only, Global = multiple sites, requires distributed coordination |
| **Fragmentation** | Horizontal (split rows, σ selection, reconstruct with UNION), Vertical (split columns, π projection, reconstruct with JOIN on PK), Mixed/Hybrid (combination) |
| **Fragmentation Rules** | Completeness (every item in ≥1 fragment), Reconstruction (original can be rebuilt), Disjointness (no overlap, except PK in vertical) |
| **Primary vs Derived Fragmentation** | Primary: based on own attributes, Derived: based on related relation's attributes (follows FK fragmentation) |
| **Data Allocation** | Non-replicated (each fragment at exactly 1 site), Partially replicated (some fragments at selected sites), Fully replicated (every fragment at every site) |
| **Replication Strategies** | Synchronous (all replicas in same transaction) vs Asynchronous (replicas updated after commit) |
| **Replication Topologies** | Leader-Follower (single writer), Multi-Leader (multiple writers, conflict resolution), Leaderless (quorum-based) |
| **Quorum Protocol** | N = total replicas, W = write quorum, R = read quorum, strong consistency: W+R>N, read-optimised: W=N R=1, write-optimised: W=1 R=N, balanced: W=R=(N/2)+1 |
| **Replication Comparison** | 10-dimension table: write/read throughput, write/read latency, consistency, availability, conflict handling, failover, implementation complexity, use case |
| **Distributed Concurrency** | Centralised locking (one site manages all locks), Primary-copy locking (each item's primary site manages lock), Distributed locking (lock at data's site) |
| **Distributed Deadlock** | Centralised detection (one coordinator, SPOF, phantom deadlocks), Distributed detection (cooperative, complex), Timeout (assume deadlock if wait too long) |

---

## Session 4: Distributed Transactions & CAP Theorem (32 slides)

| Section | Topics Covered |
|---|---|
| **Distributed Transaction** | Transaction with operations at multiple sites, local vs distributed (global) transactions |
| **Transaction Anatomy** | Transaction Coordinator (manages global transaction), Participating Sites (execute sub-transactions), Local Transaction Manager (manages local execution) |
| **Challenges** | Atomicity across sites, site failures, communication failures, network partitions |
| **2PC Protocol** | Phase 1 — Prepare/Voting (coordinator sends PREPARE, participants vote YES/NO), Phase 2 — Commit/Abort (all YES → COMMIT, any NO → ABORT) |
| **2PC Example** | Bank transfer ₹5,000: Node 1 debit, Node 2 credit, coordinator ensures both commit or both abort |
| **2PC Limitations** | Blocking (participants wait if coordinator fails), Coordinator SPOF, Communication overhead (4n messages), Scalability degradation, Latency (2 round-trips) |
| **Distributed Query Processing** | Query decomposition (break into operations), Data localization (map to nodes/fragments) |
| **Query Execution Strategies** | Data Shipping (move data to query site), Query Shipping (send query to data site), Hybrid (combine both) — comparison table |
| **Query Optimization** | Select efficient execution plan, minimize network communication, operation location decisions |
| **CAP Theorem** | Consistency (every read gets latest write), Availability (every request gets response), Partition Tolerance (system works despite network splits) — can guarantee at most 2 of 3 |
| **CAP — Consistency** | All nodes see same data, even when replicas exist |
| **CAP — Availability** | Respond with success/failure in reasonable time, even during failures |
| **CAP — Partition Tolerance** | 3 options during partition: C+A (no P), A+P (no C), C+P (no A) — with diagrams |
| **CAP Trade-offs** | In practice P is mandatory → choose CP or AP. CA only possible in non-distributed (single node) |
| **Database Options** | CA: traditional RDBMS, CP: HBase/MongoDB, AP: Cassandra/DynamoDB |
| **BASE Properties** | Basically Available (always responds), Soft state (may be inconsistent during reads), Eventual consistency (converges if no new writes) |
| **BASE — Basically Available** | Database acknowledges every request (even with stale data) |
| **BASE — Soft State** | Database may return different results for same read if data is being replicated |
| **BASE — Eventual Consistency** | All replicas converge to same value once replication completes |
| **ACID vs BASE** | 10-dimension comparison: consistency model, availability, focus, scaling, locking, schema, transactions, use cases, examples, partition handling |
| **Choose ACID vs BASE** | ACID when: financial transactions, inventory, healthcare, correctness critical. BASE when: social media, IoT, shopping carts, availability critical |
| **Key Takeaways** | 2PC coordinates all-or-nothing, query optimization reduces data transfer, CAP highlights trade-offs, BASE favors availability |

---

*Use this file to verify you haven't missed any DBMS topic before the exam.*
