# DBMS — Exam Cheatsheet (Quick Reference Before Exam)

> All key decisions, query syntax, classifications, and comparisons in one place.
> Scan this 30 minutes before the exam.

---

## 1. WHEN TO USE WHAT — Decision Scenarios

### SQL vs NoSQL — Which to Pick?

| Scenario | Pick | Why |
|---|---|---|
| Banking / Financial transactions | **SQL** | ACID needed, consistency critical, audit trails |
| Social media (posts, likes, follows) | **NoSQL (MongoDB)** | Flexible schema, high write volume, horizontal scaling |
| Hospital / Patient records | **SQL** | Structured data, referential integrity, regulatory compliance |
| IoT sensor data (1M events/sec) | **NoSQL (Cassandra/InfluxDB)** | Massive write throughput, append-only, no complex joins |
| E-commerce product catalog | **NoSQL (MongoDB)** | Varying attributes per product type (clothes vs electronics) |
| Government tax system | **SQL** | Fixed forms, complex reporting, ACID for financial data |
| Session management / Caching | **Redis** | In-memory, sub-millisecond reads, TTL for auto-expiry |
| Real-time leaderboard | **Redis (Sorted Set)** | O(log n) insert/rank, real-time scoring |
| Shopping cart | **Redis** | Fast, TTL for abandoned carts |
| Complex reporting + joins | **SQL** | Powerful JOIN, GROUP BY, window functions |
| Chat messages | **NoSQL (MongoDB)** | Flexible, ordered, scalable |
| Recommendation engine | **NoSQL + Redis** | MongoDB for user profiles, Redis for real-time features |

### Replication Strategy — Which to Pick?

| Scenario | Strategy | Why |
|---|---|---|
| Read-heavy, few writes | **Leader-Follower** | Single writer, many read replicas |
| Multi-region writes needed | **Multi-Leader** | Each region has its own leader |
| Maximum availability | **Leaderless (Quorum)** | No single point of failure |
| Strong consistency required | **Synchronous replication** | All replicas updated in same transaction |
| Low latency writes needed | **Asynchronous replication** | Write returns immediately, replicas catch up |
| Banking across countries | **CP (Consistency + Partition tolerance)** | Correct data > availability during partition |
| Social media feed | **AP (Availability + Partition tolerance)** | Always respond, eventual consistency OK |

### Fragmentation — Which to Pick?

| Scenario | Type | Reconstruct |
|---|---|---|
| Split customers by region | **Horizontal** (rows) | UNION |
| Separate frequently accessed columns from large blobs | **Vertical** (columns) | JOIN on PK |
| Region-based rows + separate hot/cold columns | **Mixed/Hybrid** | UNION + JOIN |

### Normalisation vs Denormalisation

| Scenario | Pick | Why |
|---|---|---|
| OLTP (transactions, writes) | **Normalise** (3NF) | No anomalies, data integrity |
| OLAP (analytics, reads, dashboards) | **Denormalise** | Fewer joins = faster reads |
| Data warehouse | **Denormalise** (star schema) | Pre-joined for query speed |
| Frequently updated data | **Normalise** | Update in one place |
| Read-heavy, rarely updated | **Denormalise** | Pre-compute for speed |

---

## 2. CLASSIFICATIONS & CATEGORIES

### Types of Keys

| Key | Definition | Example |
|---|---|---|
| **Super Key** | Any set that uniquely identifies a row (may have extra attributes) | {EmpID, Name}, {EmpID} |
| **Candidate Key** | Minimal super key (remove any attribute → no longer unique) | {EmpID}, {Email} |
| **Primary Key** | Chosen candidate key (NOT NULL, unique, one per table) | EmpID |
| **Alternate Key** | Candidate keys NOT chosen as primary | Email |
| **Foreign Key** | References primary key of another table | DeptID in Employee → DeptID in Department |
| **Composite Key** | Key with 2+ attributes | {StudentID, CourseID} |

### Good Primary Key Checklist

| ✅ Good PK | ❌ Bad PK |
|---|---|
| System-generated (auto-increment, UUID) | Natural data (name, phone — can change) |
| Immutable (never changes) | Mutable (email changes, Aadhaar can be re-issued) |
| Short and numeric | Long strings (slower indexing) |
| No business meaning | Business-meaningful (ties to external system) |
| Single column preferred | Too many composite columns |

### Types of Constraints

| Constraint | Rule | Example |
|---|---|---|
| **Domain** | Value must be from defined domain/type | Age must be INTEGER, 0-150 |
| **Key** | Key values must be unique | No two employees with same EmpID |
| **Entity Integrity** | Primary key cannot be NULL | EmpID IS NOT NULL |
| **Referential Integrity** | FK must match existing PK or be NULL | DeptID in Employee must exist in Department |

### Normal Forms

| NF | Rule | Eliminates | Memory Trick |
|---|---|---|---|
| **1NF** | All values atomic, no repeating groups | Multi-valued attributes | "No lists in cells" |
| **2NF** | 1NF + no partial dependency | Partial dependencies | "Whole key, not part" |
| **3NF** | 2NF + no transitive dependency | Transitive dependencies | "Nothing but the key" |
| **BCNF** | Every determinant is a superkey | All remaining anomalies | "Determinant = superkey" |

> **3NF Memory:** "The key, the whole key, and nothing but the key — so help me Codd."

### Types of SQL Commands

| Category | Commands | Purpose |
|---|---|---|
| **DDL** | CREATE, ALTER, DROP, TRUNCATE | Define/modify schema |
| **DML** | INSERT, UPDATE, DELETE, SELECT | Manipulate data |
| **DCL** | GRANT, REVOKE | Access permissions |
| **TCL** | COMMIT, ROLLBACK, SAVEPOINT | Transaction control |

### Types of Joins

| Join | Returns | Use When |
|---|---|---|
| **INNER** | Only matching rows from both tables | Default — show related data |
| **LEFT** | All from left + matching from right (NULL if no match) | Find items WITH or WITHOUT matches |
| **RIGHT** | All from right + matching from left | Rarely used (swap table order + LEFT) |
| **FULL** | All from both (NULL where no match) | Find everything including unmatched |
| **CROSS** | Every row of A × every row of B (Cartesian product) | Generate all combinations |
| **SELF** | Table joined with itself | Employee-manager, find pairs |

### ACID vs BASE

| | ACID | BASE |
|---|---|---|
| **Focus** | Correctness | Availability |
| **Consistency** | Strong (immediate) | Eventual |
| **Scaling** | Vertical (scale up) | Horizontal (scale out) |
| **Locking** | Pessimistic (locks) | Optimistic (versioning) |
| **Use** | Banking, inventory, healthcare | Social media, IoT, shopping carts |
| **Examples** | MySQL, PostgreSQL | Cassandra, DynamoDB, MongoDB |

### CAP Theorem — Quick Decision

```
Network Partition WILL happen → must support P
                    ↓
        Choose: CP or AP

CP (Consistency + Partition): Data always correct, may be unavailable during partition
   → Banking, inventory → HBase, MongoDB (strong consistency mode)

AP (Availability + Partition): Always responds, may serve stale data during partition
   → Social media, IoT → Cassandra, DynamoDB
```

### Quorum Protocol

```
N = total replicas, W = write quorum, R = read quorum

Strong consistency:    W + R > N
Read-optimised:        W = N, R = 1 (write slow, read fast)
Write-optimised:       W = 1, R = N (write fast, read slow)
Balanced:              W = R = (N/2) + 1
```

---

## 3. QUERY SYNTAX CHEATSHEET

### SQL Quick Syntax

```sql
-- DDL
CREATE TABLE emp (id INT PRIMARY KEY, name VARCHAR(50) NOT NULL, 
                  salary DECIMAL, dept_id INT REFERENCES dept(id));
ALTER TABLE emp ADD email VARCHAR(100) UNIQUE;
DROP TABLE emp;
TRUNCATE TABLE emp;  -- Deletes ALL rows (no WHERE), faster than DELETE, cannot rollback

-- DML
INSERT INTO emp VALUES (1, 'Rahul', 50000, 10);
INSERT INTO emp (name, salary) VALUES ('Priya', 60000), ('Amit', 55000);
UPDATE emp SET salary = salary * 1.10 WHERE dept_id = 10;
DELETE FROM emp WHERE salary < 30000;

-- SELECT
SELECT name, salary FROM emp WHERE dept_id = 10 ORDER BY salary DESC LIMIT 5;
SELECT DISTINCT dept_id FROM emp;

-- Aggregation
SELECT dept_id, COUNT(*) AS cnt, AVG(salary) AS avg_sal
FROM emp GROUP BY dept_id HAVING AVG(salary) > 50000;

-- Joins
SELECT e.name, d.dept_name 
FROM emp e INNER JOIN dept d ON e.dept_id = d.dept_id;

SELECT e.name, m.name AS manager
FROM emp e LEFT JOIN emp m ON e.manager_id = m.id;  -- Self join

-- Subqueries
SELECT * FROM emp WHERE salary > (SELECT AVG(salary) FROM emp);  -- Scalar
SELECT * FROM emp WHERE dept_id IN (SELECT dept_id FROM dept WHERE location = 'Mumbai');
SELECT * FROM emp e WHERE salary > (SELECT AVG(salary) FROM emp e2 WHERE e2.dept_id = e.dept_id);  -- Correlated
SELECT * FROM dept d WHERE EXISTS (SELECT 1 FROM emp e WHERE e.dept_id = d.dept_id);

-- Window Functions
SELECT name, salary, dept_id, 
       RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dept_rank
FROM emp;

-- CASE
SELECT name, salary,
       CASE WHEN salary > 80000 THEN 'High'
            WHEN salary > 50000 THEN 'Medium'
            ELSE 'Low' END AS salary_band
FROM emp;
```

### MongoDB Quick Syntax

```javascript
// INSERT
db.emp.insertOne({name: "Rahul", salary: 50000, dept: "Engineering", skills: ["Python", "SQL"]})
db.emp.insertMany([{name: "Priya", salary: 60000}, {name: "Amit", salary: 55000}])

// FIND (SELECT equivalent)
db.emp.find({})                                          // SELECT * FROM emp
db.emp.find({dept: "Engineering"})                       // WHERE dept = 'Engineering'
db.emp.find({salary: {$gt: 50000}})                     // WHERE salary > 50000
db.emp.find({salary: {$gte: 40000, $lte: 80000}})      // WHERE salary BETWEEN 40000 AND 80000
db.emp.find({dept: {$in: ["Engineering", "HR"]}})       // WHERE dept IN ('Engineering', 'HR')
db.emp.find({name: {$regex: /^R/}})                     // WHERE name LIKE 'R%'
db.emp.find({skills: {$all: ["Python", "SQL"]}})        // Has BOTH skills
db.emp.find({skills: {$size: 3}})                       // Has exactly 3 skills
db.emp.find({"address.city": "Mumbai"})                 // Nested field query
db.emp.find({email: {$exists: true}})                   // WHERE email IS NOT NULL

// FIND with projection, sort, limit
db.emp.find({}, {name: 1, salary: 1, _id: 0})          // SELECT name, salary
db.emp.find({}).sort({salary: -1}).limit(5)              // ORDER BY salary DESC LIMIT 5
db.emp.find({}).skip(10).limit(5)                       // OFFSET 10 LIMIT 5 (pagination)

// UPDATE
db.emp.updateOne({name: "Rahul"}, {$set: {salary: 60000}})          // SET salary = 60000
db.emp.updateMany({dept: "HR"}, {$inc: {salary: 5000}})             // salary = salary + 5000
db.emp.updateOne({name: "Rahul"}, {$push: {skills: "Docker"}})      // Add to array
db.emp.updateOne({name: "Rahul"}, {$pull: {skills: "SQL"}})         // Remove from array
db.emp.updateOne({name: "Rahul"}, {$unset: {email: ""}})            // Remove field

// DELETE
db.emp.deleteOne({name: "Rahul"})
db.emp.deleteMany({salary: {$lt: 30000}})

// AGGREGATION PIPELINE
db.emp.aggregate([
  {$match: {dept: "Engineering"}},                        // WHERE
  {$group: {_id: "$dept", avgSalary: {$avg: "$salary"}, count: {$sum: 1}}},  // GROUP BY
  {$sort: {avgSalary: -1}},                              // ORDER BY
  {$limit: 3}                                            // LIMIT
])

// UNWIND (flatten arrays)
db.emp.aggregate([
  {$unwind: "$skills"},                                   // One doc per skill
  {$group: {_id: "$skills", count: {$sum: 1}}},          // Count per skill
  {$sort: {count: -1}}
])

// LOOKUP (JOIN equivalent)
db.emp.aggregate([
  {$lookup: {
    from: "departments",                                  // Other collection
    localField: "dept_id",                               // Field in emp
    foreignField: "_id",                                 // Field in departments
    as: "dept_info"                                      // Output array field
  }}
])

// INDEX
db.emp.createIndex({salary: 1})                          // Ascending index
db.emp.createIndex({dept: 1, salary: -1})                // Compound index
db.emp.createIndex({name: "text"})                       // Text search index
```

### Redis Quick Syntax

```redis
# STRINGS (key-value)
SET user:1:name "Rahul"                      # Store string
GET user:1:name                               # Retrieve → "Rahul"
SETEX session:abc 1800 "user_data_json"       # Set with 30-min TTL
INCR page:views                               # Atomic counter increment
MSET user:1:name "Rahul" user:1:age "28"     # Set multiple
MGET user:1:name user:1:age                   # Get multiple

# HASHES (like objects/maps)
HSET user:1 name "Rahul" age 28 dept "Eng"   # Store hash fields
HGET user:1 name                              # Get one field → "Rahul"
HGETALL user:1                                # Get all fields
HINCRBY user:1 age 1                          # Increment field
HDEL user:1 dept                              # Delete field

# LISTS (ordered, duplicates allowed)
LPUSH recent:orders "ORD-101"                 # Push to front
RPUSH recent:orders "ORD-102"                 # Push to back
LRANGE recent:orders 0 9                      # Get first 10 items
LPOP recent:orders                            # Remove from front
LLEN recent:orders                            # Count items

# SETS (unique, unordered)
SADD user:1:skills "Python" "SQL" "Docker"    # Add members
SMEMBERS user:1:skills                        # Get all members
SISMEMBER user:1:skills "Python"              # Check membership → 1 (true)
SINTER user:1:skills user:2:skills            # Common skills (intersection)
SUNION user:1:skills user:2:skills            # All skills (union)
SCARD user:1:skills                           # Count members → 3

# SORTED SETS (unique, scored, ordered by score)
ZADD leaderboard 100 "player:A"              # Add with score
ZADD leaderboard 250 "player:B" 180 "player:C"
ZREVRANGE leaderboard 0 2 WITHSCORES         # Top 3 (highest first)
ZRANK leaderboard "player:A"                  # Rank of player (0-based)
ZREVRANK leaderboard "player:B"               # Rank from top
ZSCORE leaderboard "player:B"                 # Get score → 250
ZINCRBY leaderboard 50 "player:A"             # Add 50 to score

# EXPIRY
EXPIRE user:1 3600                            # Expire in 1 hour
TTL user:1                                    # Check remaining time
PERSIST user:1                                # Remove expiry

# PUB/SUB
SUBSCRIBE news:sports                         # Listen for messages
PUBLISH news:sports "India won the match!"   # Send message to all subscribers
```

### Redis Patterns — Common Exam Scenarios

```redis
# SESSION MANAGEMENT
HSET session:xyz user_id "U123" role "admin" login_time "2025-09-13T10:00"
EXPIRE session:xyz 1800                       # 30-min session timeout
HGETALL session:xyz                           # Retrieve session
DEL session:xyz                               # Logout (destroy session)

# RATE LIMITER (max 100 requests/minute)
INCR rate:user:123                            # Count request
EXPIRE rate:user:123 60                       # Reset counter after 60 sec
# If GET rate:user:123 > 100 → reject request

# LEADERBOARD
ZADD game:leaderboard 1500 "Rahul" 2200 "Priya" 1800 "Amit"
ZREVRANGE game:leaderboard 0 9 WITHSCORES    # Top 10 players
ZREVRANK game:leaderboard "Rahul"             # Rahul's rank

# CACHING (cache DB query result)
SET cache:product:123 '{"name":"Laptop","price":50000}' EX 300  # Cache 5 min
GET cache:product:123                         # Check cache first
# If NULL → query DB → SET cache → return
# On product update → DEL cache:product:123   # Invalidate
```

---

## 4. SQL ↔ MongoDB EQUIVALENTS (Quick Map)

| Operation | SQL | MongoDB |
|---|---|---|
| Select all | `SELECT * FROM emp` | `db.emp.find({})` |
| Where | `WHERE salary > 50000` | `{salary: {$gt: 50000}}` |
| AND | `WHERE a=1 AND b=2` | `{a: 1, b: 2}` or `{$and: [{a:1},{b:2}]}` |
| OR | `WHERE a=1 OR b=2` | `{$or: [{a:1}, {b:2}]}` |
| IN | `WHERE dept IN ('A','B')` | `{dept: {$in: ["A","B"]}}` |
| LIKE | `WHERE name LIKE 'R%'` | `{name: {$regex: /^R/}}` |
| IS NULL | `WHERE email IS NULL` | `{email: {$exists: false}}` |
| Count | `SELECT COUNT(*)` | `db.emp.countDocuments({})` |
| Distinct | `SELECT DISTINCT dept` | `db.emp.distinct("dept")` |
| Sort | `ORDER BY salary DESC` | `.sort({salary: -1})` |
| Limit | `LIMIT 5` | `.limit(5)` |
| Skip | `OFFSET 10` | `.skip(10)` |
| Group By | `GROUP BY dept` | `{$group: {_id: "$dept"}}` |
| Having | `HAVING COUNT > 5` | `{$match: {count: {$gt: 5}}}` after $group |
| Join | `JOIN dept ON ...` | `{$lookup: {from: "dept", ...}}` |
| Insert | `INSERT INTO emp VALUES(...)` | `db.emp.insertOne({...})` |
| Update | `UPDATE emp SET x=1 WHERE...` | `db.emp.updateOne({...}, {$set: {x:1}})` |
| Delete | `DELETE FROM emp WHERE...` | `db.emp.deleteMany({...})` |
| Create | `CREATE TABLE emp(...)` | `db.createCollection("emp")` (or auto) |
| Drop | `DROP TABLE emp` | `db.emp.drop()` |
| Index | `CREATE INDEX idx ON emp(salary)` | `db.emp.createIndex({salary: 1})` |

---

## 5. ADVANTAGES & DISADVANTAGES

### SQL (Relational Databases)

| Advantages | Disadvantages |
|---|---|
| ✅ ACID transactions — data always consistent | ❌ Fixed schema — schema changes are expensive |
| ✅ Powerful query language (JOINs, subqueries) | ❌ Vertical scaling only (scale up, not out) |
| ✅ Data integrity (constraints, FK, normalization) | ❌ Expensive JOINs at massive scale |
| ✅ Mature tooling, huge community | ❌ Not ideal for unstructured data |
| ✅ Standardized (SQL is universal) | ❌ Object-relational impedance mismatch |

### MongoDB (Document Database)

| Advantages | Disadvantages |
|---|---|
| ✅ Flexible schema — no migration needed | ❌ No JOIN natively (must use $lookup or denormalize) |
| ✅ Horizontal scaling (sharding built-in) | ❌ Eventual consistency by default |
| ✅ Nested documents — model real objects naturally | ❌ Larger storage (data duplication from denormalization) |
| ✅ Fast reads with denormalized data | ❌ No ACID across multiple documents (until v4.0+) |
| ✅ JSON/BSON — developer-friendly | ❌ Complex aggregation pipeline syntax |

### Redis (In-Memory Store)

| Advantages | Disadvantages |
|---|---|
| ✅ Sub-millisecond latency (in-memory) | ❌ Data size limited by RAM |
| ✅ Rich data structures (sorted sets, hashes) | ❌ Not suitable for complex queries/joins |
| ✅ Built-in TTL (auto-expiry) | ❌ Persistence is optional (data loss risk) |
| ✅ Pub/Sub for real-time messaging | ❌ Single-threaded (one core only) |
| ✅ Atomic operations | ❌ Not a primary database — use as cache/session store |

### Distributed DB: Replication Strategies

| Strategy | Advantages | Disadvantages |
|---|---|---|
| **Leader-Follower** | Simple, no write conflicts, consistent reads from leader | Leader is SPOF, failover needed, writes limited to one node |
| **Multi-Leader** | Multi-region writes, better write availability | Write conflicts need resolution, complex |
| **Leaderless** | No SPOF, highly available, tunable consistency | Complex (version vectors), repair needed, eventual consistency |
| **Synchronous** | Strong consistency guaranteed | High latency, reduced availability during network issues |
| **Asynchronous** | Low write latency, high availability | Temporary inconsistency, possible data loss if leader fails |

---

## 6. 2PC PROTOCOL — Quick Recall

```
Phase 1: PREPARE (Voting)
  Coordinator → PREPARE → All Participants
  Each Participant → VOTE YES (can commit) or VOTE NO (abort)

Phase 2: DECISION
  All YES → Coordinator → GLOBAL COMMIT → All Participants
  Any NO  → Coordinator → GLOBAL ABORT  → All Participants
```

**Limitations:** Blocking (participants wait if coordinator fails), Coordinator SPOF, 4N messages overhead, doesn't scale well.

---

## 7. ER MODEL — Quick Symbols

| Symbol | Represents |
|---|---|
| Rectangle | Entity |
| Double Rectangle | Weak Entity |
| Oval | Attribute |
| Double Oval | Multi-valued Attribute |
| Dashed Oval | Derived Attribute |
| Underline in Oval | Key Attribute |
| Diamond | Relationship |
| Double Diamond | Identifying Relationship |
| Single Line | Partial Participation |
| Double Line | Total Participation |
| 1, M, N | Cardinality |

---

## 8. ANOMALIES & DECOMPOSITION — Quick Recall

**Update Anomaly:** Change dept name → must update EVERY employee row in that dept
**Insertion Anomaly:** Can't add new department until at least 1 employee exists
**Deletion Anomaly:** Delete last employee in dept → lose department info

**Good Decomposition must be:**
1. **Lossless Join** — original table reconstructable by JOIN
2. **Dependency Preserving** — all FDs checkable without joining

---

## 9. KEY NUMBERS TO REMEMBER

| Fact | Value |
|---|---|
| Max file system problems | 7 (redundancy, inconsistency, access, sharing, security, backup, integrity) |
| DBMS advantages | 7 (redundancy control, access, integrity, security, concurrency, backup, sharing) |
| ACID properties | 4 (Atomicity, Consistency, Isolation, Durability) |
| BASE properties | 3 (Basically Available, Soft State, Eventual Consistency) |
| CAP — can guarantee max | 2 out of 3 (usually CP or AP, since P is mandatory) |
| Normal forms sequence | 1NF → 2NF → 3NF → BCNF |
| Quorum strong consistency | W + R > N |
| 2PC phases | 2 (Prepare/Vote, Commit/Abort) |
| 2PC messages per participant | 4 (PREPARE, VOTE, DECISION, ACK) |
| DDB fragmentation types | 3 (Horizontal, Vertical, Mixed) |
| Replication topologies | 3 (Leader-Follower, Multi-Leader, Leaderless) |

---

*Good luck with your exam!* 🎯
