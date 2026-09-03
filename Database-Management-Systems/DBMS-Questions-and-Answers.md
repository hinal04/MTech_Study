# Structured Data Management & Distributed Databases — Questions and Answers

> Covers all 4 sessions. Mix of conceptual, short-answer, comparison, and SQL-based questions.

---
---

# SESSION 1 — Foundations of Structured Data Management

---

### Q1. What are the main problems of using a traditional file system for data management?

**Answer:**

1. **Data redundancy** — Same data duplicated across multiple files.
2. **Inconsistency** — Updating one copy but not others creates conflicting versions.
3. **Difficulty in data access** — Each new query may require writing a new program.
4. **Data isolation** — Data spread across files in different formats is hard to consolidate.
5. **Integrity problems** — No central mechanism to enforce constraints (e.g. "balance ≥ 0").
6. **Atomicity problems** — A crash midway through an update can leave data in a partial state.
7. **Concurrent-access anomalies** — Multiple users modifying the same file can corrupt data.
8. **Security issues** — File-level permissions are too coarse for field-level access control.

---

### Q2. Define DBMS. What are its key advantages over a file system?

**Answer:**

A **Database Management System (DBMS)** is system software that provides facilities to define, create, maintain, and control access to a database.

**Key advantages:**

| Advantage | Explanation |
|---|---|
| Program–data independence | Schema stored in a catalog; storage changes don't require rewriting applications. |
| Data abstraction | Users work with a logical view, not physical storage details. |
| Multiple views | Different users can see different subsets of the same data. |
| Reduced redundancy | Centralised storage with normalisation minimises duplicate data. |
| Integrity enforcement | Constraints are defined centrally and enforced by the DBMS. |
| Concurrency control | Multiple users can safely access data simultaneously. |
| Backup & recovery | The DBMS can restore data to a consistent state after failures. |
| Security | Fine-grained access control at the table, column, or row level. |

---

### Q3. Explain the Three-Schema Architecture. Why is it important?

**Answer:**

The **ANSI/SPARC Three-Schema Architecture** separates a database system into three levels:

1. **External Level (User Views):** Individual user perspectives — each user or application sees only the data relevant to them.
2. **Conceptual Level (Logical Schema):** The complete logical structure of the entire database — entities, relationships, constraints — independent of physical storage.
3. **Internal Level (Physical Schema):** How data is physically stored on disk — file organisations, indexes, block structures.

**Importance — Data Independence:**

- **Logical data independence:** The conceptual schema can change (e.g. add a table) without affecting external views or applications.
- **Physical data independence:** The internal schema can change (e.g. add an index, move to SSD) without affecting the conceptual or external schemas.

This separation allows each level to evolve independently, reducing maintenance effort.

---

### Q4. Differentiate between schema and instance.

**Answer:**

| Aspect | Schema (Intension) | Instance (Extension) |
|---|---|---|
| **What it is** | The structure/description of the database. | The actual data stored at a particular moment. |
| **Changes** | Rarely (only when the database design evolves). | Frequently (with every INSERT, UPDATE, DELETE). |
| **Analogy** | Column headers and data types of a table. | The rows currently in that table. |
| **Example** | `STUDENT(ID INT, Name VARCHAR(50), Age INT)` | `(101, 'Alice', 21), (102, 'Bob', 23)` |

---

### Q5. What is the relational model? List its key terminology.

**Answer:**

The **relational model** (proposed by E.F. Codd, 1970) represents data as a collection of **relations** (tables) with rows and columns.

| Term | Meaning |
|---|---|
| **Relation** | A table. |
| **Tuple** | A row in a relation. |
| **Attribute** | A named column. |
| **Domain** | The set of allowable values for an attribute. |
| **Degree** | Number of attributes (columns). |
| **Cardinality** | Number of tuples (rows). |
| **Relation schema** | The relation name + attribute list, e.g. `STUDENT(ID, Name, Age)`. |

**Properties of relations:** No duplicate tuples, tuple order is insignificant, attribute order is insignificant, all attribute values are atomic (1NF).

---

### Q6. Explain the different types of keys with examples.

**Answer:**

Consider: `STUDENT(Student_ID, Email, Name, Dept_ID)`

| Key Type | Definition | Example |
|---|---|---|
| **Superkey** | Any set of attributes that uniquely identifies a tuple. | {Student_ID}, {Student_ID, Name}, {Email} |
| **Candidate key** | A minimal superkey — removing any attribute breaks uniqueness. | {Student_ID}, {Email} |
| **Primary key** | The candidate key chosen as the main identifier. | Student_ID |
| **Alternate key** | A candidate key not chosen as the PK. | Email |
| **Foreign key** | Attribute(s) referencing the PK of another relation. | Dept_ID references DEPARTMENT(Dept_ID) |

---

### Q7. What are integrity constraints in the relational model?

**Answer:**

1. **Entity integrity:** No attribute of a primary key can be NULL. (Every row must be uniquely identifiable.)
2. **Referential integrity:** A foreign key value must be NULL or must match an existing primary key value in the referenced relation.
3. **Domain constraints:** Every attribute value must belong to that attribute's defined domain (e.g. Age must be a positive integer).
4. **Key constraints:** No two tuples can have the same primary key value.

---

### Q8. Distinguish between strong and weak entity types.

**Answer:**

| Aspect | Strong Entity | Weak Entity |
|---|---|---|
| **Key** | Has its own key attribute. | No key of its own; depends on the owner entity. |
| **Existence** | Exists independently. | Cannot exist without its owner (identifying) entity. |
| **Identification** | Identified by its own PK. | Identified by the owner's PK + its partial key. |
| **ER notation** | Single-line rectangle. | Double-line rectangle. |
| **Example** | EMPLOYEE (Emp_ID) | DEPENDENT (Dependent_Name) — depends on EMPLOYEE |

A DEPENDENT is identified by the combination (Emp_ID, Dependent_Name).

---

### Q9. Describe the different attribute types in the ER model.

**Answer:**

| Type | Description | Example |
|---|---|---|
| **Simple (Atomic)** | Cannot be divided. | FirstName |
| **Composite** | Can be divided into sub-parts. | FullName → {First, Middle, Last} |
| **Single-valued** | One value per entity. | DateOfBirth |
| **Multi-valued** | Multiple values per entity. | PhoneNumbers (a person may have many) |
| **Derived** | Computed from other attributes. | Age (derived from DateOfBirth) |
| **Key** | Uniquely identifies an entity. | EmployeeID |

---

### Q10. Explain cardinality ratios and participation constraints with examples.

**Answer:**

**Cardinality ratios (for binary relationships):**

| Ratio | Meaning | Example |
|---|---|---|
| **1:1** | Each entity on both sides relates to at most one on the other. | One EMPLOYEE manages one DEPARTMENT. |
| **1:N** | One entity on the "1" side relates to many on the "N" side. | One DEPARTMENT has many EMPLOYEEs. |
| **M:N** | Many-to-many. | A STUDENT enrols in many COURSEs; a COURSE has many STUDENTs. |

**Participation constraints:**

- **Total (mandatory):** Every entity must participate in at least one relationship instance. (Double line in ER.) Example: Every EMPLOYEE *must* belong to a DEPARTMENT.
- **Partial (optional):** Some entities may not participate. (Single line.) Example: Not every EMPLOYEE manages a DEPARTMENT.

---

### Q11. Describe the ER-to-Relational mapping rules.

**Answer:**

| ER Construct | Mapping Rule |
|---|---|
| **Strong entity** | Create a table; simple attributes → columns; key attribute → PK. |
| **Weak entity** | Create a table; include partial key + owner's PK (as FK). Composite PK = owner PK + partial key. |
| **1:1 relationship** | Add PK of one side as FK in the other (prefer the total-participation side to avoid NULLs). |
| **1:N relationship** | Add PK of the "1" side as FK in the "N" side table. |
| **M:N relationship** | Create a new junction/bridge table with PKs of both sides as FKs; composite PK = both FKs. |
| **Multi-valued attribute** | Create a separate table: value + FK to owner entity's PK. |
| **Composite attribute** | Include only the simple component attributes in the table (not the composite itself). |
| **Derived attribute** | Usually not stored; computed at query time. (Sometimes stored for performance.) |

---

### Q12. List and briefly describe the core relational algebra operations.

**Answer:**

| Operation | Symbol | Description |
|---|---|---|
| **Selection** | σ | Picks tuples satisfying a condition. σ_{Salary>50000}(EMPLOYEE) |
| **Projection** | π | Picks specific columns. π_{Name, Salary}(EMPLOYEE) |
| **Union** | ∪ | Combines tuples from two union-compatible relations (removes duplicates). |
| **Intersection** | ∩ | Returns tuples present in both relations. |
| **Difference** | − | Returns tuples in one relation but not the other. |
| **Cartesian product** | × | Combines every tuple of A with every tuple of B. |
| **Join** | ⋈ | Combines related tuples from two relations based on a join condition. |
| **Rename** | ρ | Renames a relation or its attributes. |

---
---

# SESSION 2 — SQL Fundamentals and Transaction Concepts

---

### Q13. What is SQL? What makes it different from procedural languages?

**Answer:**

**SQL (Structured Query Language)** is the standard language for managing and querying relational databases. It combines DDL, DML, DCL, and TCL into a single language.

**Key difference:** SQL is **declarative** — you specify *what* data you want, not *how* to get it. The DBMS query optimizer chooses the most efficient execution plan. In contrast, procedural languages (like C or Python) require you to specify step-by-step instructions.

---

### Q14. Write SQL to create an EMPLOYEE table with appropriate constraints.

**Answer:**

```sql
CREATE TABLE EMPLOYEE (
    Emp_ID      INT            PRIMARY KEY,
    First_Name  VARCHAR(50)    NOT NULL,
    Last_Name   VARCHAR(50)    NOT NULL,
    Email       VARCHAR(100)   UNIQUE,
    Hire_Date   DATE           DEFAULT CURRENT_DATE,
    Salary      DECIMAL(10,2)  CHECK (Salary > 0),
    Dept_ID     INT,
    FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);
```

**Constraints used:**
- `PRIMARY KEY` — uniquely identifies each row; implies NOT NULL + UNIQUE.
- `NOT NULL` — column must have a value.
- `UNIQUE` — no duplicate values.
- `DEFAULT` — value used if none supplied.
- `CHECK` — validates a Boolean condition.
- `FOREIGN KEY … REFERENCES` — enforces referential integrity.
- `ON DELETE SET NULL` — if the referenced department is deleted, set Dept_ID to NULL.
- `ON UPDATE CASCADE` — if the department's PK changes, propagate the change.

---

### Q15. Explain the referential triggered actions (ON DELETE / ON UPDATE).

**Answer:**

| Action | Behaviour when referenced row is deleted/updated |
|---|---|
| `CASCADE` | Automatically delete/update the referencing rows too. |
| `SET NULL` | Set the FK column(s) in referencing rows to NULL. |
| `SET DEFAULT` | Set the FK column(s) to their default value. |
| `RESTRICT` / `NO ACTION` | Reject the delete/update if any referencing rows exist. |

**Example:** If `EMPLOYEE.Dept_ID` references `DEPARTMENT.Dept_ID` with `ON DELETE CASCADE`, deleting a department automatically deletes all employees in that department.

---

### Q16. Write SQL queries for the following scenarios.

*Given tables: EMPLOYEE(Emp_ID, First_Name, Last_Name, Salary, Dept_ID), DEPARTMENT(Dept_ID, Dept_Name, Location)*

**a) List all employees earning more than 70,000.**

```sql
SELECT * FROM EMPLOYEE WHERE Salary > 70000;
```

**b) Find the average salary per department, showing only departments with more than 5 employees, sorted by average salary descending.**

```sql
SELECT Dept_ID, COUNT(*) AS Emp_Count, AVG(Salary) AS Avg_Salary
FROM EMPLOYEE
GROUP BY Dept_ID
HAVING COUNT(*) > 5
ORDER BY Avg_Salary DESC;
```

**c) List employees who earn above the company average salary.**

```sql
SELECT First_Name, Last_Name, Salary
FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE);
```

**d) Find departments that have at least one employee (using EXISTS).**

```sql
SELECT D.Dept_Name
FROM DEPARTMENT D
WHERE EXISTS (
    SELECT 1 FROM EMPLOYEE E WHERE E.Dept_ID = D.Dept_ID
);
```

**e) List all employees with their department names, including employees not assigned to any department.**

```sql
SELECT E.First_Name, E.Last_Name, D.Dept_Name
FROM EMPLOYEE E
LEFT JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;
```

---

### Q17. Explain the different types of JOINs with examples.

**Answer:**

| Join Type | What it returns |
|---|---|
| **INNER JOIN** | Only rows where the join condition is satisfied in both tables. |
| **LEFT (OUTER) JOIN** | All rows from the left table + matching rows from the right; NULLs where no match. |
| **RIGHT (OUTER) JOIN** | All rows from the right table + matching rows from the left; NULLs where no match. |
| **FULL (OUTER) JOIN** | All rows from both tables; NULLs where no match on either side. |
| **CROSS JOIN** | Cartesian product — every row of A paired with every row of B. |
| **SELF JOIN** | A table joined to itself (e.g. to find employees and their managers). |
| **NATURAL JOIN** | Joins on all columns with the same name automatically (implicit condition). |

**Example — Self Join (employees and managers):**

```sql
SELECT E.First_Name AS Employee, M.First_Name AS Manager
FROM EMPLOYEE E
LEFT JOIN EMPLOYEE M ON E.Manager_ID = M.Emp_ID;
```

---

### Q18. What is a subquery? List the different types.

**Answer:**

A **subquery** (nested query) is a SELECT statement embedded inside another SQL statement.

| Type | Returns | Example use |
|---|---|---|
| **Scalar** | Single value | `WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE)` |
| **Row** | Single row | `WHERE (Dept_ID, Location) = (SELECT Dept_ID, Location FROM …)` |
| **Table** | Multiple rows/columns | `FROM (SELECT … ) AS derived_table` |
| **Correlated** | Re-evaluated for each outer row | `WHERE EXISTS (SELECT 1 FROM … WHERE inner.col = outer.col)` |

**Key difference:** A regular subquery is evaluated once; a **correlated subquery** references the outer query and is re-evaluated for every row in the outer query.

---

### Q19. Explain the logical order of execution of a SELECT query.

**Answer:**

```
1. FROM       — Identify source tables and perform joins.
2. WHERE      — Filter individual rows.
3. GROUP BY   — Group rows by specified columns.
4. HAVING     — Filter groups.
5. SELECT     — Choose which columns/expressions to return.
6. ORDER BY   — Sort the result.
7. LIMIT      — Restrict the number of rows returned.
```

This is why you **cannot** use a column alias defined in SELECT inside the WHERE clause — WHERE executes before SELECT.

---

### Q20. What is a transaction? Explain the ACID properties in detail.

**Answer:**

A **transaction** is a logical unit of work — one or more database operations that must be executed as an indivisible whole.

| Property | Meaning | How the DBMS enforces it |
|---|---|---|
| **Atomicity** | All operations complete or none do (all-or-nothing). | Transaction manager + undo log. If any operation fails, all changes are rolled back. |
| **Consistency** | The database moves from one valid state to another; all constraints are satisfied before and after. | Constraint checking at commit time. |
| **Isolation** | Concurrent transactions behave as if executed serially; intermediate states are invisible to others. | Concurrency control: locks, MVCC, timestamp ordering. |
| **Durability** | Once committed, changes survive any subsequent failure (crash, power loss). | Write-Ahead Log (WAL) flushed to stable storage before commit is acknowledged. |

**Classic example — Bank transfer:**

```sql
BEGIN TRANSACTION;
  UPDATE ACCOUNT SET Balance = Balance - 500 WHERE Acc_ID = 'A';
  UPDATE ACCOUNT SET Balance = Balance + 500 WHERE Acc_ID = 'B';
COMMIT;
```

Both updates must succeed together (atomicity). The total money in the system stays the same (consistency). No other transaction sees the intermediate state where money has left A but not yet arrived at B (isolation). Once committed, the transfer is permanent even if the server crashes immediately after (durability).

---

### Q21. Draw and explain the transaction state diagram.

**Answer:**

```
         ┌──────────┐
         │  Active   │──(all ops succeed)──→ Partially Committed ──(flush OK)──→ Committed
         └──────────┘                                │
              │                                      │ (flush fails)
         (operation fails)                           ▼
              ▼                                    Failed
           Failed ──→ Aborted (rollback done) ──→ [restart or kill]
```

| State | Description |
|---|---|
| **Active** | Transaction is executing operations. |
| **Partially committed** | Final operation executed; waiting for confirmation that changes are durable. |
| **Committed** | All changes are permanent; transaction is complete. |
| **Failed** | An error occurred; the transaction cannot proceed. |
| **Aborted** | Rollback is complete; database restored to the pre-transaction state. The transaction may be restarted or abandoned. |

---

### Q22. What is schema evolution? What challenges does it present?

**Answer:**

**Schema evolution** is the process of modifying a database schema after it is already in production (e.g. adding columns, changing types, adding constraints).

**Challenges:**
- Existing data may need **migration** (backfilling new columns, converting data types).
- Applications depending on the old schema may **break**.
- Views, stored procedures, and triggers may become **invalid**.
- Large tables may be **locked** during ALTER operations, causing downtime.

**Strategies:**
- **Expand-and-contract:** Add new column → migrate data → update applications → drop old column.
- **Schema versioning:** Tag each schema version; applications specify which version they target.
- **Backward/forward compatibility:** Design changes so old and new app versions co-exist during rollout.

---

### Q23. What is denormalisation? When and why would you use it?

**Answer:**

**Denormalisation** is the intentional reintroduction of redundancy into a normalised database to improve read performance.

| Scenario | Technique | Trade-off |
|---|---|---|
| Frequent expensive joins | Store redundant columns to avoid the join. | Faster reads; slower writes; risk of inconsistency. |
| Repeated aggregations | Pre-computed summary tables or materialised views. | Faster queries; data may become stale. |
| Read-heavy workloads | Duplicate data across tables optimised for different read patterns. | Storage overhead; update anomalies. |

**Rule:** Normalise first for correctness. Denormalise selectively only for measured performance problems.

---

### Q24. What are the limitations of relational databases that motivated NoSQL?

**Answer:**

| Limitation | Explanation |
|---|---|
| **Rigid schema** | ALTER TABLE on large tables can lock the table for extended periods. |
| **Impedance mismatch** | Application objects don't map cleanly to flat tables; ORM complexity. |
| **Horizontal scaling is hard** | Sharding introduces distributed joins, cross-shard transactions. |
| **Unstructured/semi-structured data** | JSON, graphs, time-series don't fit neatly into rows and columns. |
| **High write throughput** | WAL + strict ACID can bottleneck at millions of writes/second. |
| **Global distribution** | Strong consistency across continents is expensive. |

**NoSQL alternatives:** Key-Value (Redis, DynamoDB), Document (MongoDB), Column-Family (Cassandra), Graph (Neo4j).

---
---

# SESSION 3 — Distributed Database Foundations

---

### Q25. Why do we need distributed databases? List the key drivers.

**Answer:**

| Driver | Explanation |
|---|---|
| **Organisational distribution** | Companies operate in multiple locations, each generating and consuming local data. |
| **Improved performance** | Data placed close to users reduces network latency. |
| **Scalability** | Adding more nodes increases capacity beyond a single server's limits. |
| **Availability & reliability** | If one node fails, others continue serving requests; replicated data survives failures. |
| **Local autonomy** | Each site manages its own data while participating in a global system. |

---

### Q26. Compare centralised and distributed database systems.

**Answer:**

| Aspect | Centralised | Distributed |
|---|---|---|
| Data location | Single site | Multiple networked sites |
| Single point of failure | Yes | No (other nodes continue) |
| Scalability | Vertical (bigger machine) | Horizontal (more machines) |
| Latency for remote users | High | Low (data near the user) |
| Complexity | Simpler | Higher (partitioning, replication, failure handling) |
| Cost model | Expensive high-end hardware | Commodity hardware + operational overhead |

---

### Q27. What are the transparency goals of a Distributed DBMS?

**Answer:**

| Transparency | User doesn't need to know… |
|---|---|
| **Location** | …where data is physically stored. |
| **Fragmentation** | …that a table is split into fragments across sites. |
| **Replication** | …that multiple copies of data exist. |
| **Concurrency** | …that other transactions run simultaneously. |
| **Failure** | …that a node or network link has failed. |

The goal is to make the distributed system **appear** as a single, centralised database to the user.

---

### Q28. Explain the different distributed database architecture types.

**Answer:**

**1. Shared-Nothing:**
- Each node has its own CPU, memory, and disk.
- Nodes communicate only via the network.
- Scales well; failure of one node only affects its local data.
- Examples: Cassandra, CockroachDB, most modern distributed DBs.

**2. Shared-Disk:**
- All nodes access a common storage layer (SAN/NAS).
- Easier data sharing but shared storage can be a bottleneck.
- Example: Oracle RAC.

**3. Shared-Memory (Shared-Everything):**
- All processors share the same memory and disk.
- Limited scalability; typical of SMP machines.

**Most scalable → Shared-Nothing.** Most used in cloud-native distributed systems.

---

### Q29. What is data fragmentation? Explain horizontal and vertical fragmentation.

**Answer:**

**Fragmentation** (partitioning) splits a relation into smaller pieces distributed across sites.

**Horizontal fragmentation** — splits by **rows**:

```
EMPLOYEE: σ_{Region='London'}(EMPLOYEE) → Fragment at London site
          σ_{Region='Mumbai'}(EMPLOYEE) → Fragment at Mumbai site
```

Reconstruction: UNION of all fragments.

**Vertical fragmentation** — splits by **columns** (PK always included):

```
Fragment 1 (HR):      Emp_ID, Name, Hire_Date
Fragment 2 (Payroll): Emp_ID, Salary, Bank_Account
```

Reconstruction: Natural JOIN on Emp_ID.

**Hybrid (Mixed):** Applies both horizontal and vertical fragmentation.

**Correctness requirements:** Completeness (every tuple in at least one fragment), Reconstruction (original table can be rebuilt), Disjointness (preferred — no tuple in more than one fragment).

---

### Q30. Compare range, hash, and list partitioning strategies.

**Answer:**

| Strategy | How rows are assigned | Pros | Cons |
|---|---|---|---|
| **Range** | Based on key ranges (e.g. date ranges) | Efficient range queries; simple | Hot spots if data is skewed |
| **Hash** | Hash function on partition key | Even distribution; good for point lookups | Range queries must scan all partitions |
| **List** | Explicit value lists (e.g. country codes) | Easy to understand; good for categorical data | Imbalanced if some categories are much larger |
| **Consistent hashing** | Hash ring; minimal data movement when nodes change | Elastic scaling | More complex; slight imbalance possible |

---

### Q31. Why do we replicate data? Compare synchronous and asynchronous replication.

**Answer:**

**Why replicate:**
- **Availability** — if one replica fails, others serve requests.
- **Performance** — reads served by the nearest replica.
- **Fault tolerance** — data survives hardware failures.

| Aspect | Synchronous (Eager) | Asynchronous (Lazy) |
|---|---|---|
| Write confirmation | Must be confirmed at all replicas before commit | Committed at primary; replicas updated later |
| Consistency | Strong — all replicas always agree | Eventual — replicas may temporarily diverge |
| Write latency | Higher (wait for slowest replica) | Lower |
| Availability during failures | Writes may block if a replica is down | Writes succeed; risk of data loss if primary fails before propagation |

---

### Q32. Explain the different replication topologies.

**Answer:**

| Topology | How it works | Pros | Cons |
|---|---|---|---|
| **Single-leader (primary–replica)** | One node accepts writes; replicas serve reads. | Simple; no write conflicts. | Leader is a bottleneck/SPOF. |
| **Multi-leader** | Multiple nodes accept writes; changes replicated between leaders. | Good for multi-region; lower write latency. | Write conflicts must be resolved. |
| **Leaderless (peer-to-peer)** | Any node accepts reads and writes; quorum-based. | No SPOF; highly available. | More complex conflict resolution; quorum overhead. |

**Conflict resolution strategies (multi-leader/leaderless):**
- **Last-writer-wins (LWW):** Latest timestamp wins — simple but can lose data.
- **CRDTs:** Data structures designed for automatic conflict-free merging.
- **Application-level:** App logic decides (e.g. show both versions to the user).

---

### Q33. Differentiate between homogeneous and heterogeneous distributed databases.

**Answer:**

| Aspect | Homogeneous | Heterogeneous |
|---|---|---|
| DBMS software | Same at all sites | Different at different sites (e.g. Oracle + PostgreSQL) |
| Data model | Identical | May differ |
| Query language | Same | May need translation |
| Management | Easier — uniform configuration | Harder — requires middleware or translation layers |

---
---

# SESSION 4 — Distributed Transactions and Consistency Models

---

### Q34. Explain the Two-Phase Commit (2PC) protocol in detail.

**Answer:**

**2PC** ensures atomicity for distributed transactions — all participating nodes either commit or abort together.

**Roles:** Coordinator (initiates the protocol) and Participants (all other nodes in the transaction).

**Phase 1 — Voting (Prepare):**
1. Coordinator sends `PREPARE` to all participants.
2. Each participant checks if it can commit locally.
3. If yes → writes changes to durable log, sends `VOTE YES`.
4. If no → sends `VOTE NO` and aborts locally.

**Phase 2 — Decision (Commit/Abort):**
1. If **all** participants voted YES → Coordinator sends `GLOBAL COMMIT` → all participants make changes permanent.
2. If **any** participant voted NO → Coordinator sends `GLOBAL ABORT` → all participants roll back.
3. Participants send ACK to the coordinator.

**Blocking problem:** If the coordinator crashes after Phase 1 (after collecting YES votes but before sending the decision), participants are **stuck** — they hold locks and cannot decide to commit or abort until the coordinator recovers.

---

### Q35. How does Three-Phase Commit (3PC) improve on 2PC?

**Answer:**

3PC adds an intermediate phase to reduce blocking:

1. **CanCommit?** — Coordinator asks participants if they can commit.
2. **PreCommit** — If all agree, coordinator sends a pre-commit message (participants prepare but don't commit yet).
3. **DoCommit** — Coordinator sends the final commit.

**Improvement:** If the coordinator crashes, participants can determine the outcome based on whether they received a pre-commit message:
- Received pre-commit → likely the decision was to commit.
- Did not receive pre-commit → safe to abort.

**Limitation:** 3PC adds latency (extra round-trip) and does **not** fully handle network partitions. In practice, modern systems use alternative approaches (Paxos, Raft) for consensus.

---

### Q36. Describe the key steps and cost factors in distributed query processing.

**Answer:**

**Steps:**

1. **Query Parsing** — Check syntax, validate table/column names.
2. **Query Optimisation (Global + Local)** — Consider data locations, network cost, local processing cost, fragment locations.
3. **Distributed Execution** — Execute sub-queries at relevant sites; transfer intermediate results.
4. **Result Assembly** — Combine partial results at the query site.

**Cost factors (in order of typical importance):**

| Factor | Description |
|---|---|
| **Network transfer cost** | Often dominant — moving data between nodes is expensive. |
| **Local I/O cost** | Disk reads/writes at each site. |
| **Local CPU cost** | Processing (filtering, sorting, joining) at each site. |

**Join strategies for minimising data transfer:**
- **Ship-whole:** Send entire small table to the other site.
- **Semi-join:** Send only join-column values first to pre-filter the remote table.
- **Bloom-filter join:** Send a compact Bloom filter of keys to pre-filter remotely.
- **Parallel execution:** Break query into independent sub-queries across partitions.

---

### Q37. State and explain the CAP theorem.

**Answer:**

**CAP Theorem** (Brewer, 2000; proved by Gilbert & Lynch, 2002):

In a distributed data store, you can guarantee at most **two out of three**:

| Property | Definition |
|---|---|
| **Consistency (C)** | Every read returns the most recent write (linearisability). |
| **Availability (A)** | Every request gets a non-error response (system is always responsive). |
| **Partition Tolerance (P)** | System continues operating despite network partitions. |

**Key insight:** Network partitions **will** happen, so P is not optional. The real choice during a partition is:

- **CP:** Sacrifice availability — return errors rather than stale data. (HBase, MongoDB default, Zookeeper)
- **AP:** Sacrifice consistency — return best available (possibly stale) data. (Cassandra, DynamoDB, CouchDB)
- **CA:** Only possible without partitions — effectively a single-server system. (Traditional single-node RDBMS)

**Important nuance:** Most real systems offer **tunable consistency** — they aren't purely CP or AP but let you choose per-query.

---

### Q38. What is the PACELC model? How does it extend CAP?

**Answer:**

Daniel Abadi's **PACELC** model extends CAP:

- **If Partition (P):** choose between **Availability (A)** and **Consistency (C)**.
- **Else (E)** (no partition): choose between **Latency (L)** and **Consistency (C)**.

This captures the fact that even when the network is healthy, there is still a trade-off: achieving strong consistency requires coordination between nodes, which adds latency.

| System | During Partition | Normal Operation |
|---|---|---|
| Cassandra | PA (availability) | EL (low latency) |
| HBase | PC (consistency) | EC (consistency) |
| DynamoDB | PA (availability) | EL (low latency) |
| PostgreSQL (single node) | CA | EC |

---

### Q39. Compare ACID and BASE in detail.

**Answer:**

| Aspect | ACID | BASE |
|---|---|---|
| **Full form** | Atomicity, Consistency, Isolation, Durability | Basically Available, Soft state, Eventually consistent |
| **Consistency model** | Strong (immediate) | Eventual |
| **Availability** | May sacrifice availability for consistency | Prioritises availability |
| **Scalability** | Hard to scale horizontally | Built for horizontal scaling |
| **Use cases** | Financial transactions, inventory, bookings | Social feeds, analytics, caching, IoT |
| **Application complexity** | Simpler (DB handles correctness) | Higher (app must handle temporary inconsistency) |
| **Write performance** | Higher latency (coordination overhead) | Lower latency |

**ACID** is the gold standard for correctness. **BASE** trades immediate consistency for scalability and availability.

**Real-world systems often combine both:** e.g. ACID for bank account balances, BASE for analytics dashboards.

---

### Q40. Explain eventual consistency and its variants.

**Answer:**

**Eventual consistency:** If no new updates are made, all replicas will *eventually* converge to the same value. There is no guarantee about *when*.

**Variants (each adds a stronger guarantee):**

| Variant | Guarantee |
|---|---|
| **Causal consistency** | If operation A causally precedes B, everyone sees A before B. |
| **Read-your-writes** | After a write, the same client always sees its own update. |
| **Monotonic reads** | Once a client reads a value, it never sees an older value on subsequent reads. |
| **Monotonic writes** | Writes by the same client are applied in order. |
| **Session consistency** | Within a session: read-your-writes + monotonic reads. |

---

### Q41. What is tunable consistency? Give an example with Cassandra.

**Answer:**

**Tunable consistency** lets you choose the consistency level on a per-query basis, trading off between latency and freshness.

**Cassandra example:**

| Level | Meaning |
|---|---|
| `ONE` | Read/write acknowledged by 1 replica (fastest, weakest consistency). |
| `QUORUM` | Acknowledged by a majority of replicas (balanced). |
| `ALL` | Acknowledged by all replicas (strongest, slowest). |
| `LOCAL_QUORUM` | Quorum within the local data centre only. |

**Strong consistency formula:**

```
R + W > N
```

Where: R = replicas read, W = replicas written, N = total replicas.

If this holds, every read set overlaps with the latest write set, guaranteeing the latest value is seen.

**Example:** N=3, W=2, R=2 → 2+2=4 > 3 → strong consistency guaranteed.

---

### Q42. You are designing a system. How do you choose between strong and eventual consistency?

**Answer:**

**Decision framework:**

1. **Is the data safety-critical?** (money, health records, inventory counts)
   - **Yes → Strong consistency (ACID / CP).**
   - Losing or seeing stale data could cause financial loss or harm.

2. **Can the application tolerate temporary stale reads?**
   - **Yes → Eventual consistency (BASE / AP)** — and enjoy better scalability and lower latency.
   - Example: social media feeds, product reviews, analytics dashboards.

3. **Need a middle ground?**
   - **Causal or session consistency** — stronger than eventual but cheaper than full linearisability.
   - Example: a user sees their own writes immediately but may see slightly stale data from others.

**Best practice:** Mix models within the same system:
- ACID for the order/payment service.
- Eventual consistency for the recommendation engine.
- Session consistency for user profile reads.

---

### Q43. Explain the difference between Consistency in CAP and Consistency in ACID.

**Answer:**

These are **different concepts** despite sharing the same word:

| ACID Consistency | CAP Consistency |
|---|---|
| The database moves from one **valid state** to another — all integrity constraints (PK, FK, CHECK, domain) are satisfied before and after a transaction. | Every read receives the **most recent write** (or an error) — also called **linearisability**. All nodes see the same data at the same time. |
| About **correctness of data** (constraint satisfaction). | About **freshness/agreement** across distributed replicas. |
| Enforced by constraint checking at commit time. | Enforced by replication and consensus protocols. |
| Relevant even in a single-node database. | Only relevant in distributed systems with replicated data. |

---

### Q44. A distributed system has 5 replicas (N=5). If writes go to 3 replicas (W=3) and reads from 3 replicas (R=3), is strong consistency guaranteed? What if R=2?

**Answer:**

**Case 1: W=3, R=3, N=5**
- R + W = 3 + 3 = 6 > 5 (N) → **Yes, strong consistency is guaranteed.**
- At least one replica in the read set must have the latest write.

**Case 2: W=3, R=2, N=5**
- R + W = 2 + 3 = 5 = 5 (N) → **No, NOT guaranteed.**
- The condition requires R + W **strictly greater than** N. With equality, there could be a scenario where the read set doesn't overlap with the write set (due to timing).
- To guarantee consistency: increase R to 3, or increase W to 4.

---

### Q45. Compare 2PC, 3PC, Paxos/Raft — when would you use each?

**Answer:**

| Protocol | Purpose | Blocking? | Partition tolerant? | Use case |
|---|---|---|---|---|
| **2PC** | Atomic distributed commit | Yes (if coordinator fails) | No | Traditional distributed transactions within a trusted, low-latency network (e.g. databases within one data centre). |
| **3PC** | Reduce 2PC blocking | Partially (less blocking) | No (not fully) | Theoretical improvement; rarely used in practice. |
| **Paxos / Raft** | Distributed consensus | No (leader election handles failures) | Yes | Leader election, replicated state machines, metadata management. Used in CockroachDB, etcd, Zookeeper. |

Modern distributed databases often use **Raft/Paxos for consensus** and build transaction protocols on top, rather than relying on bare 2PC.

---
---

# BONUS — Mixed / Cross-Session Questions

---

### Q46. Trace the evolution from file systems → RDBMS → distributed databases → NoSQL. What problem did each stage solve?

**Answer:**

| Stage | Problem it solved |
|---|---|
| **File systems** | Basic data storage, but with redundancy, inconsistency, no concurrency control. |
| **RDBMS** | Structured storage with schemas, constraints, ACID transactions, SQL — solved redundancy, integrity, concurrency, and security problems. |
| **Distributed RDBMS** | Single-server limits on scalability, availability, and latency for geographically distributed users. Introduced partitioning and replication. |
| **NoSQL** | Rigid schemas, impedance mismatch, horizontal scaling limitations, and high write throughput needs of web-scale applications. Traded some ACID for flexibility and scale (BASE). |

Each stage didn't replace the previous one — all co-exist. The right choice depends on the workload.

---

### Q47. A company stores customer orders in a relational database. Orders have grown to billions of rows. Describe how you would evolve the architecture.

**Answer:**

**Step 1 — Optimise the existing RDBMS:**
- Add proper indexes on frequently queried columns (customer_id, order_date).
- Denormalise hot read paths (materialised views for order summaries).
- Archive old/cold orders to a separate table.

**Step 2 — Vertical scaling:**
- Upgrade to a bigger server (more RAM, faster SSDs).
- Effective up to a point but has a ceiling.

**Step 3 — Read replicas:**
- Set up primary–replica replication.
- Route read queries to replicas; writes to primary.
- Improves read throughput.

**Step 4 — Horizontal partitioning (sharding):**
- Shard the orders table by customer_id (hash partitioning) or order_date (range partitioning).
- Each shard on a different node.
- Cross-shard queries become more complex.

**Step 5 — Consider a distributed database:**
- Move to a distributed SQL system (CockroachDB, YugabyteDB) that handles sharding, replication, and distributed transactions transparently.

**Step 6 — Polyglot persistence (if needed):**
- Keep the relational DB for transactional order processing (ACID).
- Use a column-store or data warehouse (e.g. Redshift, BigQuery) for analytics on billions of rows.
- Use a cache layer (Redis) for hot data.

---

### Q48. Design an ER diagram for a university database, then map it to relational tables.

**Answer:**

**Entities and attributes:**
- **STUDENT** (Student_ID [PK], Name, Email, DOB)
- **COURSE** (Course_ID [PK], Course_Name, Credits)
- **INSTRUCTOR** (Instructor_ID [PK], Name, Email)
- **DEPARTMENT** (Dept_ID [PK], Dept_Name)

**Relationships:**
- STUDENT enrols in COURSE → M:N → ENROLMENT (Student_ID, Course_ID, Grade, Semester)
- INSTRUCTOR teaches COURSE → 1:N (one instructor teaches many courses)
- DEPARTMENT offers COURSE → 1:N
- INSTRUCTOR belongs to DEPARTMENT → N:1

**Relational tables after mapping:**

```sql
CREATE TABLE DEPARTMENT (
    Dept_ID    INT PRIMARY KEY,
    Dept_Name  VARCHAR(100) NOT NULL
);

CREATE TABLE INSTRUCTOR (
    Instructor_ID  INT PRIMARY KEY,
    Name           VARCHAR(100) NOT NULL,
    Email          VARCHAR(100) UNIQUE,
    Dept_ID        INT,
    FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID)
);

CREATE TABLE COURSE (
    Course_ID    INT PRIMARY KEY,
    Course_Name  VARCHAR(100) NOT NULL,
    Credits      INT CHECK (Credits > 0),
    Dept_ID      INT,
    Instructor_ID INT,
    FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID),
    FOREIGN KEY (Instructor_ID) REFERENCES INSTRUCTOR(Instructor_ID)
);

CREATE TABLE STUDENT (
    Student_ID  INT PRIMARY KEY,
    Name        VARCHAR(100) NOT NULL,
    Email       VARCHAR(100) UNIQUE,
    DOB         DATE
);

CREATE TABLE ENROLMENT (
    Student_ID  INT,
    Course_ID   INT,
    Grade       CHAR(2),
    Semester    VARCHAR(20),
    PRIMARY KEY (Student_ID, Course_ID, Semester),
    FOREIGN KEY (Student_ID) REFERENCES STUDENT(Student_ID),
    FOREIGN KEY (Course_ID) REFERENCES COURSE(Course_ID)
);
```

**Mapping rules applied:**
- Strong entities → Tables with PK.
- 1:N relationships (teaches, offers, belongs_to) → FK on the "N" side.
- M:N relationship (enrols) → Junction table ENROLMENT with composite PK.

---

### Q49. Compare the following pairs of concepts:

**a) Horizontal vs. Vertical fragmentation**

| Aspect | Horizontal | Vertical |
|---|---|---|
| Splits by | Rows (tuples) | Columns (attributes) |
| Selection condition | σ (selection predicates) | π (projection) |
| Reconstruction | UNION | Natural JOIN on PK |
| Use case | Data locality by region/category | Different departments need different columns |

**b) Synchronous vs. Asynchronous replication**

| Aspect | Synchronous | Asynchronous |
|---|---|---|
| Consistency | Strong | Eventual |
| Write latency | Higher | Lower |
| Risk | Writes blocked if replica down | Data loss if primary fails before propagation |

**c) 2PC vs. Paxos/Raft**

| Aspect | 2PC | Paxos/Raft |
|---|---|---|
| Purpose | Atomic commit | Consensus (agreement on a value) |
| Blocking | Yes (coordinator failure) | No (leader election) |
| Partition tolerance | No | Yes |

---

### Q50. Short-answer rapid-fire questions.

**1. Name the three levels of the ANSI/SPARC architecture.**
External (user views), Conceptual (logical), Internal (physical).

**2. What does 1NF require?**
All attribute values must be atomic (indivisible) — no multi-valued or composite attributes in a cell.

**3. What is the difference between a candidate key and a primary key?**
A candidate key is any minimal superkey. The primary key is the specific candidate key chosen by the designer.

**4. What does referential integrity mean?**
A foreign key value must be NULL or must match an existing primary key in the referenced table.

**5. What SQL clause filters groups (not individual rows)?**
`HAVING`.

**6. What is the difference between DELETE and DROP?**
`DELETE` removes rows from a table (table structure remains). `DROP` removes the entire table (structure + data).

**7. What does MVCC stand for?**
Multi-Version Concurrency Control — maintains multiple versions of data so readers don't block writers.

**8. In CAP, which property is always required in a real distributed system?**
Partition Tolerance (P) — network partitions are inevitable.

**9. What is the formula for strong consistency in a quorum system?**
R + W > N (where R = read replicas, W = write replicas, N = total replicas).

**10. Name three NoSQL database categories.**
Key-Value (Redis), Document (MongoDB), Column-Family (Cassandra), Graph (Neo4j) — any three.

---

*End of Questions and Answers*
