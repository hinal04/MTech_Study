# Database Management Systems - Mid-Semester Questions & Answers
## SSZG507 - Sessions 1-4 (Foundations, SQL, Distributed Databases, Transactions)

---

## Q1. Distributed Transactions - Concepts and Challenges [3 Marks]

### Question:
What is a distributed transaction? Explain: (i) definition, (ii) why it's fundamentally different from single-server transactions, (iii) the distributed commit problem, (iv) three key components involved in coordinating a distributed transaction, (v) why ensuring atomicity is challenging.

### Answer:

**Definition of Distributed Transaction [0.5 marks]:**
A distributed transaction spans multiple nodes/databases. Parts of the transaction execute at different sites, accessing data stored at different locations. The transaction must maintain ACID properties across all participating sites.

Example: Bank transfer from Account A (Database 1) to Account B (Database 2) must be atomic across both databases.

**Why Different from Single-Server Transactions [0.75 marks]:**

Single-server transactions:
- One transaction manager makes all decisions
- Commit or abort decided by single entity
- Atomicity enforced locally; no coordination needed
- Failures affect only one site

Distributed transactions:
- Multiple independent transaction managers at different sites
- All must reach same commit/abort decision despite failures
- Network can lose messages; nodes can crash
- Impossible to guarantee instantaneous synchronization
- Partial failures possible (one site commits, another aborts)

**Distributed Commit Problem [0.5 marks]:**
How do multiple independent sites reach same commit/abort decision when:
- Network can fail (messages lost)
- Sites can crash (unavailable)
- Messages can be delayed
- Sites cannot tell if other sites failed or are just slow

**Three Key Components [0.75 marks]:**

1. **Transaction Coordinator:** Central orchestrator
   - Splits global transaction into sub-transactions for each site
   - Collects votes from participating sites
   - Makes final commit/abort decision
   - Communicates decision to all sites

2. **Participating Sites:** Hold data needed by transaction
   - Execute their local sub-transaction portion
   - Report local success/failure to coordinator
   - Obey coordinator's final decision (commit or abort)

3. **Local Transaction Manager:** Manages at individual site
   - Executes local concurrency control and locking
   - Maintains write-ahead log
   - Responds to coordinator's prepare/commit/abort requests
   - Handles local recovery on crashes

**Why Atomicity is Challenging [0.5 marks]:**
Must guarantee either ALL sites commit or ALL sites abort, even with:
- Network partitions (sites can't communicate)
- Node crashes at any moment
- Messages arriving out of order
- Long delays making it impossible to know if site is crashed or slow

---

## Q2. Two-Phase Commit (2PC) Protocol [3.5 Marks]

### Question:
Explain the Two-Phase Commit (2PC) protocol in detail. Include: (i) phase 1 (voting/prepare phase), (ii) phase 2 (commit/abort phase), (iii) scenarios where 2PC succeeds and fails, (iv) failure handling at each phase, (v) blocking problem and limitations, (vi) provide example with 3 sites.

### Answer:

**Two-Phase Commit Protocol Overview [0.5 marks]:**
2PC ensures atomicity in distributed transactions through two phases: vote-to-commit and commit-or-abort.

**Phase 1: Prepare/Voting Phase [1 mark]:**

Coordinator sends "prepare to commit" to all participating sites:
```
Coordinator → All Sites: "Can you commit this transaction?"

Each Site Response:
- YES: "I can commit" (locks resources, writes undo logs, ready to commit)
- NO: "I cannot commit" (due to constraint violation, deadlock, etc.)
```

Site actions:
1. Execute transaction locally
2. If successful: lock all modified data, write undo/redo logs to disk
3. Send YES vote to coordinator if ready to commit
4. If any error: send NO vote and release locks

**Phase 2: Commit/Abort Phase [1 mark]:**

Coordinator decides based on votes:
```
If ALL sites vote YES:
    Coordinator → All Sites: "COMMIT"
    Each site:
        - Writes commit record to log
        - Releases all locks
        - Acknowledges commit complete

If ANY site votes NO:
    Coordinator → All Sites: "ABORT"
    Each site:
        - Uses undo logs to rollback changes
        - Releases all locks
        - Acknowledges abort complete
```

**Success Scenario [0.75 marks]:**

```
Transaction: Transfer $100 from Bank1 to Bank2

Coordinator (Bank3) sends: "Prepare"

PHASE 1:
Bank1: Can debit $100? YES → vote YES
Bank2: Can credit $100? YES → vote YES
Bank3 (coordinator): Can record? YES → vote YES

Coordinator receives: [YES, YES, YES]

PHASE 2:
Coordinator: All YES → send COMMIT
Bank1: Debit $100, commit, unlock
Bank2: Credit $100, commit, unlock
Bank3: Record, commit, unlock

Result: Transfer complete; atomicity maintained
```

**Failure at Phase 1 [0.5 marks]:**

```
Scenario: Bank2 cannot credit (insufficient space)

Bank1: vote YES
Bank2: vote NO (database full)
Bank3: vote YES

Coordinator: Received a NO → send ABORT to all

PHASE 2 - ABORT:
Bank1: Undo debit using undo log, release locks
Bank2: No changes to undo (already rejected)
Bank3: Undo record, release locks

Result: Transaction aborted; atomic rollback across all sites
```

**Blocking Problem and Limitations [0.75 marks]:**

**Blocking Problem:** After Phase 1, sites lock resources waiting for coordinator's final decision in Phase 2. If coordinator fails:
- Sites remain locked indefinitely
- Other transactions cannot access locked resources
- System becomes blocked until coordinator recovers

**Other Limitations:**
- Requires multiple round-trips (high latency)
- Not suitable for geographically distributed systems (high latency)
- Does not handle network partitions well (coordinator unreachable)
- Assumes reliable communication eventually (not true in real networks)

---

## Q3. CAP Theorem [3 Marks]

### Question:
State and explain the CAP Theorem. Define: (i) Consistency, (ii) Availability, (iii) Partition Tolerance. Explain: (iv) why only 2 of 3 are achievable, (v) real-world implications, (vi) provide practical examples of CP, AP, and CA systems.

### Answer:

**CAP Theorem Statement [0.75 marks]:**

In a distributed system, you can guarantee only 2 of these 3 properties:

1. **Consistency (C):** Every read returns the most recent write (linearizable). All nodes see same data at same time.

2. **Availability (A):** System always responds to requests (even if some nodes fail). Every request gets answer (success or failure), never times out.

3. **Partition Tolerance (P):** System continues operating despite network partitions (some nodes unreachable due to network failure).

**Why Only 2 of 3? [0.75 marks]:**

When network partition occurs (P), a choice must be made:

**Choose C (Consistency) over A:**
- Refuse requests to maintain consistency
- System unavailable during partition
- Example: Traditional databases, 2PC

```
Network Partition: Node A ↔ ↔ Node B (split brain)

If Consistency is priority:
- Block all writes until partition heals
- Refuse reads from partition that can't verify consistency
- Result: Unavailable but consistent
```

**Choose A (Availability) over C:**
- Accept requests; data may be inconsistent temporarily
- System available during partition
- Eventually consistent when partition heals
- Example: NoSQL databases (Cassandra, DynamoDB)

```
Network Partition: Node A ↔ ↔ Node B

If Availability is priority:
- Both sides accept writes independently
- Data diverges during partition
- Reconcile when partition heals
- Result: Available but eventually consistent
```

**Without Partition Tolerance (P):**
- Assume network never partitions (unrealistic)
- Achieve both C and A
- Example: Single-datacenter systems (assuming no partition)

**Real-World Implications [0.75 marks]:**

| System | Choose | Reason | Trade-off |
|--------|--------|--------|-----------|
| **Traditional RDBMS** | CP | Strong consistency crucial for data integrity | Reduced availability; blocks on partition |
| **NoSQL (Cassandra)** | AP | Availability and scale more important | Eventual consistency; temporary conflicts |
| **Google Spanner** | CP + High Availability | Uses atomic clocks for consistency | Very expensive infrastructure |
| **E-commerce Checkout** | CP | Money transactions can't be inconsistent | Lose availability if network issues |
| **Social Media Feeds** | AP | Perfect consistency not critical | Users see slightly outdated data |

**Practical Examples:**

**CP System - Banking:**
- If partition occurs: refuse transactions until partition heals
- Consistency maintained; availability reduced

**AP System - Social Media Likes:**
- If partition occurs: both sides accept like incrementally
- When partition heals: reconcile like counts
- Temporarily inconsistent but always available

**CA System - Single Datacenter (no partition):**
- All nodes in same datacenter; no network partition risk
- Both consistent and available (in normal operation)

---

## Q4. ACID vs. BASE [2.5 Marks]

### Question:
Compare ACID and BASE properties. For each property in both models, explain: (i) ACID definition and guarantee, (ii) BASE interpretation, (iii) when to use each, (iv) create comparison table, (v) provide practical examples.

### Answer:

**ACID Properties [0.5 marks]:**

- **Atomicity:** Transaction all-or-nothing; commit entire or rollback entirely
- **Consistency:** Database moves from valid state to valid state; constraints maintained
- **Isolation:** Concurrent transactions don't interfere; as if executed sequentially
- **Durability:** Committed data survives failures (power loss, crashes, disasters)

**BASE Properties [0.5 marks]:**

- **Basically Available:** System responds, even if some nodes/data temporarily unavailable
- **Soft State:** State of system can change without external input (due to replication delays)
- **Eventually Consistent:** After period with no updates, all nodes converge to consistent state

**When to Use Each [0.75 marks]:**

**Use ACID:**
- Financial transactions (banking, payments)
- Inventory management (prevent overselling)
- Medical records (data integrity critical)
- E-commerce orders (consistency crucial)

**Use BASE:**
- Social media (likes, comments)
- Real-time analytics
- Content distribution networks
- Recommendation systems
- High-scale, high-availability systems (millions of users)

**Comparison Table [0.75 marks]:**

| Aspect | ACID | BASE |
|--------|------|------|
| **Atomicity** | Guaranteed | Best effort; eventual atomicity |
| **Consistency** | Immediate; always consistent | Eventual; temporarily inconsistent |
| **Isolation** | Strict; no interference | Relaxed; some visibility of uncommitted |
| **Durability** | Guaranteed on commit | Not guaranteed initially |
| **Availability** | Reduced (consistency prioritized) | High (availability prioritized) |
| **Partition Tolerance** | Low (CA or CP; struggles with P) | High (designed for partitions) |
| **Latency** | Higher (due to sync replication) | Lower (async replication) |
| **Scalability** | Limited (vertical scaling) | High (horizontal scaling) |
| **Example Systems** | PostgreSQL, Oracle, SQL Server | Cassandra, DynamoDB, MongoDB |

**Practical Examples:**

**ACID - Bank Transfer:**
```
Account A: $1000, Account B: $500
Transaction: Transfer $200 from A to B

ACID guarantee:
1. Atomicity: Both debit and credit happen or nothing happens
2. Consistency: Sum remains $1500 before and after
3. Isolation: No one sees intermediate state
4. Durability: After confirmation, $200 moved permanently

Result: A=$800, B=$700 (guaranteed)
```

**BASE - Social Media Likes:**
```
Post has 100 likes on Server 1
User views from Server 2; might see 95-105 likes (inconsistency)
After few seconds: all servers sync; consistent

BASE guarantees:
- Basically Available: User always sees post
- Soft State: Like count may change due to replication
- Eventually Consistent: All servers reach same like count

Result: Always available; eventually consistent
```

---

## Q5. Consistency Models [3.5 Marks]

### Question:
Explain different consistency models: (i) strong consistency, (ii) weak consistency, (iii) eventual consistency, (iv) causal consistency. For each: define, explain guarantees, discuss latency implications, and provide practical scenarios.

### Answer:

**Strong Consistency [0.9 marks]:**

**Definition:** After write completes, all subsequent reads see the written value (linearizable).

**Guarantee:** 
- Write happens at moment in time T
- All reads after T see new value
- All reads before T see old value

**Latency:** HIGH
- Must synchronously replicate to all nodes before acknowledging write
- 2PC, Paxos consensus needed

**Example:** Banking system
```
Write: Set balance to $500
All subsequent reads: $500
No one sees intermediate values
```

**Use cases:** Financial transactions, inventory management

**Weak Consistency [0.9 marks]:**

**Definition:** Reads after write may not see new value immediately.

**Guarantee:**
- Inconsistency window exists
- Eventually consistency restored but no time bound

**Latency:** LOW
- Writes return immediately; replication asynchronous
- No synchronization overhead

**Example:** Social media post likes
```
User A posts
Like count on User B's screen: possibly not updated yet
After seconds/minutes: all see same count
```

**Use cases:** Non-critical data, high-traffic systems

**Eventual Consistency [0.9 marks]:**

**Definition:** If no new writes, all nodes eventually see same value.

**Guarantee:**
- After period with no updates
- All nodes converge to same state
- Conflict resolution when partition heals

**Latency:** LOW (async replication)

**Implementation:** Quorum reads/writes, vector clocks, conflict resolution

**Example:** Cassandra, DynamoDB
```
User A writes: name = "Alice"
User B reads immediately: might see "Bob" (old value) or "Alice" (new)
After seconds: both see "Alice"
```

**Use cases:** Cloud storage, caches, message queues

**Causal Consistency [0.7 marks]:**

**Definition:** If write A happened-before write B, all nodes see A before B. Causally related writes are ordered.

**Guarantee:**
- Maintains causal ordering
- Independent writes can be in any order
- Uses version vectors or logical timestamps

**Latency:** MEDIUM (more overhead than eventual, less than strong)

**Example:** Conversation thread
```
User A writes: "What's 2+2?"
User B writes: "It's 4"
User C will see A's message before B's response (causal ordering)
```

**Use cases:** Messaging systems, collaborative tools, social networks

**Comparison Table [0.5 marks]:**

| Model | Update Visibility | Latency | Consistency Window | Use Case |
|-------|-----------------|---------|-------------------|----------|
| **Strong** | Immediate | High | None | Banking, inventory |
| **Weak** | Delayed | Low | Unbounded | High-traffic web |
| **Eventual** | Delayed | Low | Bounded (seconds) | Cloud storage, caches |
| **Causal** | Delayed (respect order) | Medium | Preserves causality | Messaging, social |

---

## Q6. Paxos and Raft Consensus Protocols [3 Marks]

### Question:
Explain consensus protocols for distributed systems. Compare Paxos and Raft: (i) what problem they solve, (ii) how each achieves consensus, (iii) key differences, (iv) why Raft is easier to understand, (v) when to use each.

### Answer:

**The Consensus Problem [0.5 marks]:**

How do multiple independent nodes reach agreement on a value despite:
- Node failures
- Network delays
- Messages lost/reordered
- Asynchronous communication

Solution: Consensus protocol ensures all non-failed nodes agree on same value.

**Paxos Protocol [1 mark]:**

**Roles:**
- Proposers: propose values
- Acceptors: accept proposals (majority needed)
- Learners: learn accepted values

**Phases:**
1. **Prepare Phase:** Proposer requests permission to propose (higher number than seen)
2. **Accept Phase:** Proposers propose values; acceptors accept if highest-numbered seen
3. **Learn Phase:** Acceptors inform learners of accepted value

**Guarantee:** If majority of acceptors accept same value, all nodes learn it

**Challenge:** Complex protocol; multiple rounds needed; "Paxos Made Simple" paper itself is complex

**Raft Protocol [1 mark]:**

**Simpler alternative designed for understandability**

**Key Concepts:**
- **Leader:** Elected leader accepts all proposals; simplifies protocol
- **Terms:** Each term has at most one leader; terms have monotonically increasing numbers
- **Log Replication:** Leader replicates log entries to followers; majority needed for commit

**Phases:**
1. **Election:** Leader elected; followers vote for highest term candidate
2. **Log Replication:** Leader appends entries; followers acknowledge
3. **Safety:** Ensure leader has all previously committed entries

**Why Simpler:**
- Single leader simplifies logic (vs. Paxos multiple proposers)
- Clear role separation (leader/follower)
- Stronger leader claims avoid subtle bugs
- Easier to implement correctly

**Comparison [1.5 marks]:**

| Aspect | Paxos | Raft |
|--------|-------|------|
| **Understandability** | Complex | Designed for clarity |
| **Roles** | Multiple (proposer, acceptor, learner) | Simpler (leader, follower) |
| **Leader** | No leader; any node can propose | Single leader per term |
| **Messages** | 2 phases + multiple rounds | Append entries + elections |
| **Implementation** | Subtle bugs common | Clearer implementation |
| **Production Use** | Used in Google Chubby, Zookeeper | Used in etcd, Consul, etc. |
| **Time Complexity** | Multiple rounds possible | Simpler; fewer messages |

**When to Use:**
- **Paxos:** Historical systems, Byzantine tolerance needed, academic study
- **Raft:** New systems, production use, clarity and correctness important

---

## Q7. NewSQL Databases [2.5 Marks]

### Question:
What are NewSQL databases? Explain: (i) how they differ from traditional SQL databases, (ii) how they differ from NoSQL, (iii) what advantages they provide, (iv) challenges in achieving strong consistency at scale, (v) provide examples (Google Spanner, CockroachDB, etc.).

### Answer:

**NewSQL Definition [0.5 marks]:**

NewSQL databases combine:
- **SQL interface & ACID transactions** (like traditional RDBMS)
- **Horizontal scalability** (like NoSQL)
- **Distributed architecture** (across multiple nodes/data centers)

**Differences from Traditional SQL [0.75 marks]:**

Traditional SQL (PostgreSQL, MySQL):
- Single server or replicated for backup
- Strong consistency, full ACID
- Limited horizontal scalability
- Consistent but not always available

NewSQL:
- Distributed across multiple nodes
- Strong consistency + high availability
- Horizontal scaling capability
- Maintains ACID across distributed system

**Differences from NoSQL [0.75 marks]:**

NoSQL (MongoDB, Cassandra):
- Eventual consistency (sacrifice strong consistency)
- High availability
- Easy horizontal scaling
- No JOIN support (usually)
- Weak transactional guarantees

NewSQL:
- Strong consistency (like SQL)
- Higher latency (due to consensus overhead)
- Horizontal scaling achieved differently (not simple sharding)
- Full SQL with JOINs
- ACID transactions across nodes

**Advantages of NewSQL [0.5 marks]:**
- **Consistency + Scale:** ACID transactions at scale (unlike NoSQL)
- **SQL Familiarity:** Developers know SQL; lower learning curve
- **Horizontal scaling:** Handle more data than single-server SQL
- **Fault tolerance:** Continue operating if nodes fail
- **Global distribution:** Data in multiple regions with strong consistency

**Technical Challenges [0.5 marks]:**

To achieve strong consistency at scale:
- **Distributed consensus:** Use Paxos/Raft → overhead, latency
- **Atomic clocks:** Google Spanner uses atomic clocks for ordering
- **Two-phase commit:** Coordinate across nodes → blocking, slow
- **Trade-off:** Latency increases due to synchronization needed

**Examples:**

**Google Spanner:**
- Uses atomic clocks (TrueTime) for global consistency
- Achieves CP in CAP (Consistency + Partition tolerance)
- Very expensive infrastructure (atomic clock hardware)
- Strong consistency globally; high availability within regions

**CockroachDB:**
- Distributed SQL database
- Horizontal scaling, no single point of failure
- Uses Raft for consensus
- ACID transactions across nodes

**Vitess:**
- Middleware for MySQL scaling
- Sharding support; horizontal scaling
- Maintains SQL interface

---

## Q8. Distributed Query Processing [2 Marks]

### Question:
Explain distributed query processing. Discuss: (i) what happens when query accesses data across multiple sites, (ii) query optimization for distributed systems, (iii) data transfer costs, (iv) strategies: push selection/projection to source vs. transfer all data, (v) provide example showing cost difference.

### Answer:

**Distributed Query Processing Overview [0.5 marks]:**

When query accesses data on multiple sites:
```
Query: SELECT name, salary FROM employees JOIN departments
WHERE dept_id = 5

If employees on Server A, departments on Server B:
- Possible strategies differ in data transfer cost
```

**Cost Considerations [0.75 marks]:**

In distributed systems, data transfer is expensive (network bandwidth).

**Example Query Cost:**
- Employees table: 1 million rows, 100 bytes each = 100 MB
- Departments table: 1,000 rows, 50 bytes each = 50 KB

**Strategy 1: Transfer all to one site and join**
- Send employees to Server B: 100 MB
- Join locally
- Total transfer: 100 MB

**Strategy 2: Push selection/join to source**
- Select WHERE dept_id=5: 100 rows (1000 bytes from departments)
- Send selection to employees server
- Join locally; send result (100 rows × 100 bytes = 10 KB)
- Total transfer: 11 KB

**Result: Strategy 2 is 10,000× more efficient!**

**Query Optimization Strategies [0.75 marks]:**

1. **Push Selection/Projection Down:** Apply WHERE clauses and SELECT columns at source to reduce data transferred

2. **Reduce Join Size:** 
   - Evaluate selective predicates first (most filtering)
   - Send only needed columns

3. **Local Aggregation:** 
   - COUNT, SUM at each site before sending results
   - Send only aggregate results

4. **Materialized Views:** Pre-compute frequent joins; update incrementally

**Example Strategy Comparison:**
```
Query: SELECT AVG(salary) FROM employees WHERE dept_id = 5

Strategy A (Transfer all):
- Send all 1M employees (100 MB) to central location
- Filter dept_id = 5, calculate average

Strategy B (Push aggregation):
- Each employee server filters locally: 100 rows (10 KB)
- Calculate local average
- Send sum and count to central (2 numbers)
- Central calculates global average

Result: Strategy B transfers 10 KB vs. 100 MB (10,000× better)
```

---

## Marks Summary
- Q1: 3 marks
- Q2: 3.5 marks
- Q3: 3 marks
- Q4: 2.5 marks
- Q5: 3.5 marks
- Q6: 3 marks
- Q7: 2.5 marks
- Q8: 2 marks

**Total: 23 marks**

## Q9. Data Modeling and ER Diagrams [3 Marks]

### Question:
Design an ER diagram for a university database with:
- Students (taking courses)
- Courses (taught by instructors)
- Instructors (teaching multiple courses)
- Departments (offering courses, employing instructors)

Include: entities, attributes, relationships (cardinality), identifying keys, weak entities if any.

### Answer:

**Entities and Attributes [1 mark]:**

**STUDENT**
- Attributes: StudentID (PK), StudentName, Address, Email, DOB, EnrollmentDate
- Identifying key: StudentID

**COURSE**
- Attributes: CourseID (PK), CourseName, Credits, Description, DeptID (FK)
- Identifying key: CourseID

**INSTRUCTOR**
- Attributes: InstructorID (PK), InstructorName, Email, DeptID (FK), Specialization
- Identifying key: InstructorID

**DEPARTMENT**
- Attributes: DeptID (PK), DeptName, Building, Phone
- Identifying key: DeptID

**Relationships and Cardinality [1.5 marks]:**

```
STUDENT ─── ENROLLS ─── COURSE
   (1)         (M:N)       (M)
   
COURSE ─── TAUGHT_BY ─── INSTRUCTOR
   (1)        (M:N)         (1)
   
INSTRUCTOR ─── WORKS_IN ─── DEPARTMENT
      (M)         (M:1)        (1)
      
DEPARTMENT ─── OFFERS ─── COURSE
      (1)        (1:M)       (M)
```

**Cardinality Explanations:**
- Student enrolls in many courses; each course has many students (M:N)
- One course taught by one instructor; instructor teaches many courses (1:M)
- Many instructors in one department; each instructor in one department (M:1)
- One department offers many courses; each course in one department (1:M)

**Weak Entities [0.5 marks]:**

**ENROLLMENT** (Weak entity, depends on STUDENT and COURSE):
- Attributes: StudentID (FK, partial key), CourseID (FK, partial key), Grade, EnrollmentDate, Semester
- Composite identifying key: (StudentID, CourseID)
- Justifies: Can identify uniqueness of enrollment by student-course pair

---

## Q10. Normalization and Normal Forms [3.5 Marks]

### Question:
Given a STUDENT_COURSES table with attributes:
- StudentID, StudentName, Address, CourseID, CourseName, Credits, Grade, InstructorID, InstructorName

a) Identify functional dependencies and data anomalies
b) Normalize to BCNF, showing decomposition steps

### Answer:

**Functional Dependencies [1 mark]:**

```
StudentID → StudentName, Address
CourseID → CourseName, Credits, InstructorID
InstructorID → InstructorName
(StudentID, CourseID) → Grade
```

**Data Anomalies [1 mark]:**

**Insertion anomaly:**
- Cannot add new instructor without enrolling a student (InstructorName depends on InstructorID which exists only with CourseID)

**Update anomaly:**
- If instructor changes name, must update all rows with that instructor (redundancy)

**Deletion anomaly:**
- If remove last student taking a course, lose information about course instructor

**Normalization Steps [1.5 marks]:**

**Step 1: Remove non-key dependencies → 2NF**
```
STUDENT(StudentID, StudentName, Address)
  PK: StudentID

COURSE(CourseID, CourseName, Credits, InstructorID)
  PK: CourseID
  FK: InstructorID

ENROLLMENT(StudentID, CourseID, Grade)
  PK: (StudentID, CourseID)
  FK: StudentID, CourseID

INSTRUCTOR(InstructorID, InstructorName)
  PK: InstructorID
```

**Step 2: Remove transitive dependencies → BCNF**
```
No transitive dependencies remain (already in BCNF)
- No non-key attribute determines another non-key attribute
```

---

## Q11. SQL Joins and Set Operations [3 Marks]

### Question:
Given tables:
- EMPLOYEE(EmpID, Name, DeptID, Salary)
- DEPARTMENT(DeptID, DeptName)
- PROJECT(ProjectID, ProjectName, DeptID)
- WORKS_ON(EmpID, ProjectID, Hours)

Write SQL queries:
a) Find employees NOT working on any project
b) Find departments where all employees earn > $50K
c) Find the top 3 highest-paid employees in each department

### Answer:

**a) Employees NOT working on any project [1 mark]:**

```sql
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
WHERE e.EmpID NOT IN (SELECT DISTINCT EmpID FROM WORKS_ON);

-- Alternative using LEFT JOIN:
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
LEFT JOIN WORKS_ON w ON e.EmpID = w.EmpID
WHERE w.EmpID IS NULL;
```

**b) Departments where ALL employees earn > $50K [1 mark]:**

```sql
SELECT d.DeptID, d.DeptName
FROM DEPARTMENT d
WHERE NOT EXISTS (
  SELECT 1 FROM EMPLOYEE e
  WHERE e.DeptID = d.DeptID AND e.Salary <= 50000
);

-- Alternative using GROUP BY HAVING:
SELECT d.DeptID, d.DeptName
FROM DEPARTMENT d
WHERE d.DeptID NOT IN (
  SELECT DeptID FROM EMPLOYEE
  WHERE Salary <= 50000
  GROUP BY DeptID
);
```

**c) Top 3 highest-paid employees in each department [1 mark]:**

```sql
SELECT e.DeptID, e.Name, e.Salary,
       ROW_NUMBER() OVER (PARTITION BY e.DeptID ORDER BY e.Salary DESC) as Rank
FROM EMPLOYEE e
WHERE ROW_NUMBER() OVER (PARTITION BY e.DeptID ORDER BY e.Salary DESC) <= 3;

-- Alternative using subquery (pre-window function SQL):
SELECT e.DeptID, e.Name, e.Salary
FROM EMPLOYEE e
WHERE (
  SELECT COUNT(*)
  FROM EMPLOYEE e2
  WHERE e2.DeptID = e.DeptID AND e2.Salary >= e.Salary
) <= 3
ORDER BY e.DeptID, e.Salary DESC;
```

---

## Q12. ACID Properties and Transaction Isolation [3 Marks]

### Question:
Explain dirty reads, non-repeatable reads, and phantom reads with examples. For each, describe:
a) What can go wrong
b) Which isolation level prevents it
c) Trade-off in performance

### Answer:

**Dirty Read [1 mark]:**

**What happens:**
```
T1: BEGIN
T1: UPDATE Account SET balance = 100 WHERE id = 1
T2: BEGIN
T2: SELECT balance FROM Account WHERE id = 1  → Sees 100 (uncommitted!)
T1: ROLLBACK (balance should be 50, not 100)
T2: Used wrong data
```

T2 read an uncommitted change from T1.

**Prevention:**
- Isolation level: READ COMMITTED or higher
- Locking: S-locks (read locks) held until commit

**Trade-off:**
- Higher isolation = more locks = less concurrency = lower throughput

**Non-Repeatable Read [1 mark]:**

**What happens:**
```
T1: BEGIN
T1: SELECT balance FROM Account WHERE id = 1 → 100
T2: BEGIN
T2: UPDATE Account SET balance = 200 WHERE id = 1
T2: COMMIT
T1: SELECT balance FROM Account WHERE id = 1 → 200 (different!)
```

Same query in same transaction returns different results.

**Prevention:**
- Isolation level: REPEATABLE READ or higher
- Locking: Read locks held until transaction commit

**Trade-off:**
- Must hold locks longer → reduced concurrency

**Phantom Read [1 mark]:**

**What happens:**
```
T1: BEGIN
T1: SELECT COUNT(*) FROM Account WHERE balance > 100 → 5 rows
T2: BEGIN
T2: INSERT INTO Account VALUES (6, 150)  // balance > 100
T2: COMMIT
T1: SELECT COUNT(*) FROM Account WHERE balance > 100 → 6 rows (phantom!)
```

New rows match query predicate, appearing like "phantoms".

**Prevention:**
- Isolation level: SERIALIZABLE
- Locking: Predicate locks (lock all rows matching WHERE clause)

**Trade-off:**
- Predicate locks expensive (must check all matching rows)
- Severely reduces concurrency

---

## Q13. Indexing Strategies and Trade-offs [2.5 Marks]

### Question:
Design indexes for:
- Query A: SELECT * FROM Employees WHERE Salary > 50000
- Query B: SELECT * FROM Employees WHERE DeptID = 5 AND Salary > 50000
- Query C: Frequent full table scans on small table (100 rows)

For each: recommend index type, justify, and explain trade-offs.

### Answer:

**Query A: Salary > 50000 [0.8 marks]**

**Recommended index:**
```sql
CREATE INDEX idx_salary ON Employees(Salary);
```

**Why:**
- B-tree index on Salary enables range scan (> 50000)
- Query: 50000 → scan to end of index → get matching rows

**Trade-off:**
- Benefit: Query faster (O(log n) + scan matching)
- Cost: Slower INSERT/UPDATE (must update index), extra storage

**Query B: DeptID = 5 AND Salary > 50000 [0.8 marks]**

**Recommended index:**
```sql
CREATE INDEX idx_dept_salary ON Employees(DeptID, Salary);
```

**Why:**
- Composite index: First filter by DeptID (equality), then Salary (range)
- Index can answer entire query without table access (covering index if only need ID/Dept/Salary)

**Trade-off:**
- Benefit: Most selective (DeptID) first, then range scan (Salary)
- Cost: Index larger; fragmentation as Salary updates; order matters (DeptID, Salary not Salary, DeptID)

**Query C: Full table scans on small table [0.9 marks]**

**Recommended index:** NONE

**Why:**
- Table has 100 rows → fits in few pages
- Index lookup (binary search) adds overhead compared to sequential scan
- Full scan reads 1-2 pages from disk; index adds extra page reads

**Trade-off:**
- Benefit of no index: Faster INSERT/UPDATE, no maintenance overhead
- Cost: Table scans slower, but for 100 rows acceptable
- Threshold: Generally, index beneficial when table > 1000 rows

---

## Q14. Denormalization and Data Warehouse Design [3 Marks]

### Question:
A retail company has normalized database (OLTP):
- CUSTOMER(CustID, Name, Address)
- ORDER(OrderID, CustID, OrderDate, TotalAmount)
- ORDERLINE(OrderLineID, OrderID, ProductID, Qty, UnitPrice)
- PRODUCT(ProductID, ProductName, CategoryID, Price)

They want data warehouse for analytics. Design denormalized schema:
a) What tables needed for star schema?
b) Justify denormalization choices
c) Example: Query for "Total sales by product category by month"

### Answer:

**Star Schema Design [1 mark]:**

**Fact Table - SALES**
```
SalesID (PK)
OrderDateID (FK)
ProductID (FK)
CustomerID (FK)
Quantity
Amount
```

**Dimension Tables:**

**DIM_DATE**
- DateID (PK)
- Date, Month, Quarter, Year, DayOfWeek, MonthName

**DIM_PRODUCT**
- ProductID (PK)
- ProductName, Category, Price

**DIM_CUSTOMER**
- CustomerID (PK)
- CustomerName, Address, City, Country

**Justification [1 mark]:**

**Why denormalize:**
1. **Faster queries:** Joins reduced (4 simple joins vs. complex normalized joins)
2. **Pre-aggregated:** Fact table stores grain (per order line) ready for aggregation
3. **Dimension tables:** Include all descriptive attributes (CategoryName) avoiding joins to Product-Category tables
4. **Time dimension:** Date attributes pre-computed (no need to extract/calculate)

**Trade-offs accepted:**
- Redundancy: CategoryName stored in every Product row (acceptable; small tables)
- Maintenance: Dimension tables updated periodically (batch ETL), not real-time (acceptable for analytics)

**Example Query [1 mark]:**

```sql
-- OLTP Query (normalized):
SELECT p.Category, MONTH(o.OrderDate) as Month, SUM(ol.Qty * ol.UnitPrice) as Total
FROM ORDERLINE ol
JOIN ORDER o ON ol.OrderID = o.OrderID
JOIN PRODUCT p ON ol.ProductID = p.ProductID
GROUP BY p.Category, MONTH(o.OrderDate)
ORDER BY p.Category, MONTH(o.OrderDate);

-- Data Warehouse Query (denormalized):
SELECT dp.Category, dd.MonthName, SUM(s.Amount) as Total
FROM SALES s
JOIN DIM_PRODUCT dp ON s.ProductID = dp.ProductID
JOIN DIM_DATE dd ON s.OrderDateID = dd.DateID
GROUP BY dp.Category, dd.MonthName
ORDER BY dp.Category, dd.MonthName;

-- DW query simpler: 2 joins vs. 3, no need for date extraction
```

---

## Q15. Concurrency Control and Deadlock Prevention [3 Marks]

### Question:
Two transactions:
- T1: Transfer $100 from Account A to Account B
- T2: Transfer $50 from Account B to Account A

Design locking strategy to:
a) Prevent race conditions
b) Avoid deadlock
c) Maintain serializability

### Answer:

**Locking Strategy [1 mark]:**

**Without proper locking (RACE CONDITION):**
```
T1: READ Account A: 1000
T2: READ Account B: 500
T1: WRITE Account A: 900
T2: WRITE Account B: 550
    (T1 sees old balance, writes wrong value)
```

**With locks (2-Phase Locking):**
```
T1: LOCK Account A (exclusive)
T1: READ Account A: 1000
T1: LOCK Account B (exclusive)
T1: UPDATE: A=900, B=600
T1: UNLOCK Account A, B

T2: LOCK Account B (exclusive)    -- Must wait, T1 has it
T2: (waits for T1 to release)
```

**Prevention [1 mark]:**

**Deadlock Scenario (WRONG):**
```
T1: LOCK Account A
T2: LOCK Account B
T1: REQUEST LOCK Account B      (waits for T2)
T2: REQUEST LOCK Account A      (waits for T1)
    DEADLOCK!
```

**Deadlock Prevention (CORRECT):**

**Method: Lock Ordering**
- Define global order: Account ID order (A before B)
- Always lock in order: Lock A first, then B

```
T1: LOCK A (A < B)
T1: LOCK B
T1: Do work, unlock in reverse: B, A

T2: LOCK A (always first, even for B→A transfer)
T2: LOCK B
T2: Do work, unlock in reverse: B, A
```

**Serializability [1 mark]:**

**Ensures:**
- One transaction completes before another starts (or doesn't overlap)
- Final state equivalent to serial execution

**Guarantee:**
- 2-Phase Locking (two phases: acquisition and release) ensures conflict-serializability
- All locks held until commit prevents concurrent access
- Order matters: Both T1 and T2 use same lock order (A before B)

**Result:**
```
Legal interleaving: T1 completes lock-hold-release cycle
                    Then T2 acquires locks (no conflict)
                    
OR:                 T2 acquires locks first
                    Then T1 acquires locks
```

Both orderings are serializable; 2PL prevents interleaving that violates serializability.

---

## Summary of Mid-Sem Questions

| Q# | Topic | Marks | Concepts |
|---|---|---|---|
| Q1 | Distributed Transactions | 3 | 2PC, commit protocol, atomicity, ACID |
| Q2 | Consistency Models | 3.5 | Eventual vs. strong consistency, serializability |
| Q3 | Partitioning Strategies | 3 | Horizontal/vertical, tradeoffs, consistency |
| Q4 | Replication | 2.5 | Master-slave, multi-master, consistency |
| Q5 | CAP Theorem | 3.5 | Consistency/Availability/Partition tolerance tradeoffs |
| Q6 | Query Optimization | 3 | Distributed query cost, join strategies, optimization |
| Q7 | Entity-Relationship Model | 3 | ER diagrams, entities, relationships, cardinality |
| Q8 | Normalization | 3 | Functional dependencies, BCNF, anomalies |
| Q9 | Data Modeling | 3 | ER design for university database |
| Q10 | Normal Forms | 3.5 | 2NF, BCNF normalization steps |
| Q11 | SQL Joins | 3 | Complex queries, set operations, window functions |
| Q12 | Isolation Levels | 3 | Dirty reads, repeatable reads, phantom reads |
| Q13 | Indexing | 2.5 | Index selection, B-trees, trade-offs |
| Q14 | Denormalization | 3 | Star schema, data warehouse, analytical queries |
| Q15 | Concurrency Control | 3 | Locking, deadlock prevention, 2PL, serializability |

**Total: 45 marks across 15 comprehensive questions**

**Coverage:**
- ✓ Distributed database fundamentals (2PC, replication, partitioning)
- ✓ Consistency models and CAP theorem
- ✓ Data modeling (ER, normalization, BCNF)
- ✓ SQL and query optimization
- ✓ Transaction management (ACID, isolation, concurrency control)
- ✓ Data warehouse design
- ✓ Index strategies and performance tuning

