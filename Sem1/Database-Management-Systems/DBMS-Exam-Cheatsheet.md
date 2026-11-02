# DBMS — Exam Cheatsheet (Quick Reference Before Exam)

> All key decisions, query syntax, classifications, and comparisons in one place.
> Scan this 30 minutes before the exam.

---

## 1. WHEN TO USE WHAT — Decision Scenarios

### SQL vs NoSQL — Which to Pick?

| Scenario | Pick | Why (write this in exam) |
|---|---|---|
| Banking / Financial transactions | **SQL (PostgreSQL)** | **ACID is mandatory.** A bank transfer must be atomic (debit+credit both succeed or both fail). Consistency ensures balances never go negative. Isolation prevents double-spending. Durability means committed transactions survive crashes. NoSQL's eventual consistency is unacceptable — a customer seeing wrong balance even for 1 second is a regulatory failure. Also needs complex joins (customer → accounts → transactions → branches). |
| Social media (posts, likes, follows) | **NoSQL (MongoDB + Redis)** | **Flexible schema:** Posts can be text, image, video, poll — each with different fields. Fixed SQL schema would need many NULL columns or complex EAV pattern. **Horizontal scaling:** 100M users generating billions of posts — must shard across servers. **Denormalized reads:** Show a post with author name, like count, comments inline — embedding avoids expensive joins. Redis caches hot feeds for sub-millisecond reads. |
| Hospital / Patient records | **SQL (PostgreSQL)** | **Data integrity is life-critical.** Wrong drug dosage from data error can kill a patient. FK constraints ensure prescriptions reference real patients/doctors. ACID needed for concurrent access (multiple nurses updating same patient). **Regulatory:** HIPAA/DPDP require audit trails, access logging — SQL triggers and stored procedures support this. Structured schema (patient always has name, DOB, blood group) fits relational model perfectly. |
| IoT sensor data (1M events/sec) | **NoSQL (Cassandra / InfluxDB)** | **Write throughput:** 1M inserts/sec — SQL databases choke at this volume. Cassandra is designed for massive writes across distributed nodes. **Simple data model:** Each reading is {device_id, timestamp, value} — no joins needed. **Time-series optimized:** InfluxDB/TimescaleDB handle time-bucketed queries (avg temp last hour) natively. **Horizontal scaling:** Add nodes as devices grow. SQL vertical scaling hits hardware limits. |
| E-commerce product catalog | **NoSQL (MongoDB)** | **Varying attributes:** A shirt has {size, color, fabric}. A laptop has {RAM, CPU, screen_size, weight}. In SQL, you'd need one giant table with 100+ columns (mostly NULL) or a complex EAV table. MongoDB stores each product as a document with only its relevant fields — clean, simple, no NULLs. **Nested data:** Product variants (size S/M/L × color Red/Blue) stored as nested arrays naturally. |
| Government tax system | **SQL (Oracle/PostgreSQL)** | **Fixed schema:** Tax forms have predetermined fields (PAN, income, deductions) — perfect for relational tables. **Complex reporting:** "Total tax collected by state, by income bracket, by quarter" requires multi-table JOINs and GROUP BY — SQL excels at this. **ACID for financial accuracy:** Tax calculations must be 100% correct. **Audit trail:** Every change must be logged — SQL triggers and transaction logs provide this. |
| Session management / Caching | **Redis** | **In-memory = sub-millisecond reads.** Sessions are accessed on every page load — must be instant. **TTL (Time to Live):** Set session to auto-expire after 30 minutes — `SETEX session:abc 1800 data`. No cron job needed. **Key-value simplicity:** Session is just a key (session_id) → value (user_data). Redis HSET stores structured session data (user_id, role, login_time). |
| Real-time leaderboard | **Redis (Sorted Set)** | **Sorted Set:** `ZADD leaderboard score player` inserts in O(log n). `ZREVRANGE leaderboard 0 9` returns top 10 instantly. `ZREVRANK leaderboard "Rahul"` returns rank in O(log n). No other database can do rank queries this fast. In SQL, ranking requires sorting entire table (O(n log n)) on every query. |
| Shopping cart | **Redis (Hash)** | **Speed:** Cart is read/updated on every product add/remove — must be instant. **TTL:** Abandoned carts auto-expire after 24 hours. **Hash structure:** `HSET cart:user123 product:456 2` (quantity). `HGETALL cart:user123` retrieves full cart. **Ephemeral data:** Cart doesn't need ACID or durability — if Redis restarts, customer rebuilds cart (acceptable UX). |
| Complex reporting + joins | **SQL** | **JOINs:** "Revenue by region by product category for Q3" requires joining orders → products → regions → time_periods. SQL handles multi-table JOINs elegantly. **Window functions:** Running totals, rankings, moving averages — built into SQL. **GROUP BY + HAVING:** Aggregation with filtering. MongoDB's $lookup is limited to simple joins and can't match SQL's expressive power for analytics. |
| Chat messages | **NoSQL (MongoDB)** | **Flexible message types:** Text, image, file, location, reaction — each has different fields. **Ordered by timestamp:** MongoDB's natural insertion order + index on timestamp gives efficient message retrieval. **Scalable:** Millions of chat rooms with billions of messages — sharding by chat_room_id distributes load. **Embedded reactions/replies:** Nested within message document — no joins needed. |
| Recommendation engine features | **MongoDB + Redis** | **MongoDB:** Store user profiles (purchase history, preferences, demographics) as rich documents. Complex queries for batch feature computation. **Redis:** Store real-time features (items viewed in last 5 min, current cart) in sorted sets/hashes for <1ms serving. The combination gives both rich querying (MongoDB) and ultra-fast serving (Redis). |

### Replication Strategy — Which to Pick?

| Scenario | Strategy | Why (write this in exam) |
|---|---|---|
| Read-heavy, few writes (e-commerce product pages) | **Leader-Follower** | Single leader handles all writes (simple, no conflicts). Multiple follower replicas serve reads — distribute read load across nodes. If leader fails, promote a follower. **Example:** Flipkart product catalog — millions of reads/sec but products updated a few times/day. |
| Multi-region writes (global app, offices in India + US + UK) | **Multi-Leader** | Each region has its own leader — users write to nearest leader (low latency). Leaders sync asynchronously. **Trade-off:** Write conflicts possible (two leaders update same record). Need conflict resolution (last-write-wins, or application-level merge). **Example:** Google Docs — edits in India and US simultaneously, conflicts merged. |
| Maximum availability, no single point of failure | **Leaderless (Quorum)** | Any node accepts reads/writes. No leader to fail. Use quorum: W+R>N ensures reads see latest write. **Example:** Cassandra, DynamoDB — designed for "always available" systems. |
| Strong consistency required (banking, inventory) | **Synchronous replication** | All replicas updated within the SAME transaction. Write only succeeds when ALL replicas confirm. **Guarantees:** Every read from any replica returns the latest data. **Cost:** Higher write latency (must wait for all replicas). **When:** Financial transactions where "you have ₹50,000" must be correct on every read, every replica. |
| Low latency writes (social media posts, IoT) | **Asynchronous replication** | Write returns immediately after leader confirms. Replicas catch up in the background. **Fast** but temporarily inconsistent — a read from a follower may return stale data for a few milliseconds. **When:** Social media feed — if a "like" appears 500ms late on another device, that's acceptable. |
| Banking across countries | **CP (Consistency + Partition tolerance)** | During a network partition between India and US, the system **refuses writes** rather than risk inconsistent data. A bank transfer must either fully succeed or fully fail — never partially applied. **Example:** If Mumbai-Delhi network breaks, transactions from Delhi to Mumbai are blocked until network recovers. Better to be unavailable for 5 minutes than to process a wrong transfer. |
| Social media feed | **AP (Availability + Partition tolerance)** | During a network partition, the system **keeps serving requests** even if data is slightly stale. If a user posts in Delhi and the post takes 2 seconds to appear in Mumbai, that's fine. Users would rather see a slightly stale feed than get an error page. **Example:** Instagram — you always see your feed, even if a new post from someone else is delayed by a few seconds. |

### Fragmentation — Which to Pick?

| Scenario | Type | How It Works | Reconstruct | When to Pick |
|---|---|---|---|---|
| Split customers by region (North/South/West) | **Horizontal** (split rows) | Each fragment contains rows for one region. Fragment 1: all North customers. Fragment 2: all South customers. Same columns in every fragment. | **UNION** all fragments | Data is naturally partitioned by a geographic, temporal, or categorical attribute. Queries usually filter by that attribute. **Example:** Swiggy orders — Delhi orders stay in Delhi DB, Mumbai orders in Mumbai DB. Most queries are regional ("show my orders" — user is in one city). |
| Separate frequently accessed columns from large blobs | **Vertical** (split columns) | Fragment 1: {EmpID, Name, Email, Phone} — accessed 100x/day. Fragment 2: {EmpID, ProfilePhoto, Resume, Bio} — accessed 1x/month. Both share PK (EmpID) for joining. | **JOIN** on PK | Some columns are queried constantly (hot data) while others are rarely needed (cold data). Separating them means hot queries don't waste I/O reading large cold columns. **Example:** LinkedIn — profile header (name, headline) loaded instantly; activity history loaded on scroll. |
| Region-based rows + hot/cold columns | **Mixed/Hybrid** | First horizontally fragment by region, then vertically fragment each piece by access frequency. | **UNION + JOIN** | Large-scale systems where BOTH row-level distribution AND column-level optimization are needed. **Example:** A global bank — accounts split by country (horizontal), then within each country, split into core data (balance, name) and archive data (10-year history). |

### Normalisation vs Denormalisation — Which to Pick?

| Scenario | Pick | Why (write this in exam) |
|---|---|---|
| OLTP (online transactions — banking, inventory, orders) | **Normalise (3NF)** | Transactions involve frequent writes (INSERT, UPDATE, DELETE). Normalisation eliminates redundancy → updates happen in ONE place. No anomalies: no risk of updating a department name in one row but missing it in another. **Data integrity** is paramount — constraints, FK, and normalization enforce correctness. |
| OLAP (analytics — dashboards, reports, BI) | **Denormalise** | Analytics involves complex read queries with many JOINs. Denormalisation pre-joins data → fewer runtime JOINs → faster queries. Reports are read-only — update anomalies don't apply. **Star schema** (fact + dimension tables) is the standard denormalized pattern for data warehouses. |
| Data warehouse (Snowflake, BigQuery, Redshift) | **Denormalise (Star Schema)** | Warehouses are optimized for reads, not writes. Pre-computed joins in fact tables make dashboard queries fast. ETL pipeline handles data loading (writes are batched, not real-time). **Example:** A sales dashboard showing "revenue by region by quarter" — pre-join sales+products+regions into one fact table for instant queries. |
| Frequently updated data (employee records, prices) | **Normalise** | If product price changes, update it in ONE table (Products). All orders reference it via FK. With denormalization, you'd need to update price in every order row — slow and error-prone. |
| Read-heavy, rarely updated (product catalog, reference data) | **Denormalise** | Product details rarely change but are read millions of times. Pre-joining product+category+brand into one document (MongoDB) or one wide table eliminates JOIN overhead on every read. Cache-friendly. |

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

**Easy Memory Trick — Ask 3 Questions:**

```
1. Unique?      → Can two rows have the same value?
2. Not Null?    → Can it ever be empty/blank?
3. Stable?      → Will it ever change after creation?

If ALL THREE = YES → ✅ Good Primary Key
If ANY = NO       → ❌ Bad Primary Key
```

| ✅ Good PK | ❌ Bad PK | Why Bad? |
|---|---|---|
| System-generated (auto-increment, UUID) | Name | Not unique (two "Rahul Sharma"), changes (marriage) |
| EmpID (numeric, auto-generated) | Phone number | Changes when user switches carrier |
| OrderID (system-assigned) | Email | Changes (switches provider), privacy concerns |
| StudentRollNo (institution-assigned) | Aadhaar | 12 digits (long), can be re-issued, privacy issues |
| Immutable, short, numeric | Business-meaningful data | Ties to external system, may change |

### Normal Forms — Easy Memory Trick

```
STEP 1: Check for 1NF violation
─────────────────────────────────
❓ Comma in a field? Multiple values in one cell?
   Example: Skills = "Python, SQL, Java"
→ VIOLATION of 1NF!
→ FIX: Split into separate rows (one skill per row)
   OR create a separate SKILLS table

STEP 2: Check for 2NF violation (only if composite PK)
───────────────────────────────────────────────────────
❓ Part of composite key determines a non-key attribute?
   Example: PK = {StudentID, CourseID}
            But CourseName depends ONLY on CourseID (not full PK)
→ VIOLATION of 2NF! (Partial dependency)
→ FIX: Create separate table — COURSE(CourseID, CourseName)

STEP 3: Check for 3NF violation
─────────────────────────────────
❓ Non-key attribute determines another non-key attribute?
   Rule: Non-key should NOT determine non-key
   Example: DeptID → DeptName (both are non-key, DeptName depends
            on DeptID, not on the primary key directly)
→ VIOLATION of 3NF! (Transitive dependency)
→ FIX: Create separate table — DEPARTMENT(DeptID, DeptName)

STEP 4: Check for BCNF violation
─────────────────────────────────
❓ Is the LEFT SIDE of EVERY functional dependency a candidate key or super key?
   Rule: Left side of EVERY FD must be a candidate/super key
   Example: PK = {CourseID, TimeSlot}
            FD: InstructorID → Room
            InstructorID is NOT a candidate key → BCNF violation!
→ VIOLATION of BCNF!
→ FIX: Split table using the violating FD
        Original: SCHEDULE(CourseID, InstructorID, Room, TimeSlot)
        Split into:
          INSTRUCTOR_ROOM(InstructorID, Room)        ← violating FD becomes its own table
          SCHEDULE(CourseID, InstructorID, TimeSlot)  ← remaining attributes
```

**One-line memory for 3NF:** *"The key, the whole key, and nothing but the key — so help me Codd."*

- **1NF** = "the key" (every row uniquely identified, atomic values)
- **2NF** = "the WHOLE key" (no partial dependencies — depend on full PK)
- **3NF** = "NOTHING BUT the key" (no transitive dependencies — non-keys don't determine other non-keys)
- **BCNF** = "EVERY determinant is a key" (left side of every FD must be a superkey)

**Key Difference — 3NF vs BCNF:**
```
3NF:  Non-key should not determine non-key
      (only checks non-key → non-key dependencies)

BCNF: Left side of EVERY FD must be a candidate/super key
      (checks ALL FDs, including key → non-key)

BCNF is STRICTER than 3NF. Every BCNF table is in 3NF, but not every 3NF table is in BCNF.
```

**Quick Decision Flowchart:**

```
Is there a comma/list in any cell?
  YES → Not in 1NF → Split rows or create child table
  NO  ↓

Is PK composite AND does part of PK determine a non-key?
  YES → Not in 2NF → Move partial dependency to new table
  NO  ↓

Does any non-key attribute determine another non-key?
  YES → Not in 3NF → Move transitive dependency to new table
  NO  ↓

Is the left side of EVERY FD a candidate/super key?
  NO  → Not in BCNF → Split table using the violating FD
  YES → ✅ In BCNF!
```

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

### Relational Division Pattern — ALL / EVERY / EACH

**When to use:** Anytime the question says "find X that has ALL / EVERY / EACH of Y."

**English logic:** *"There does NOT EXIST a required item that the entity does NOT have."*

```
Keyword Spotted         What It Means
─────────────────       ─────────────────────────────────────
ALL / EVERY / EACH  →   You need Relational Division
                    →   Double NOT EXISTS pattern
```

**The Thought Process (step by step):**

```
ALL / EVERY / EACH
       ↓
Find Missing Items          ← "Is there ANY required item this entity DOESN'T have?"
       ↓
Inner NOT EXISTS            ← Check: entity doesn't have this particular item
       ↓
No Missing Items            ← "If NO item is missing..."
       ↓
Outer NOT EXISTS            ← "...then this entity qualifies"
       ↓
Relational Division ✓
```

**Generic SQL Template:**

```sql
-- "Find all X that have EVERY Y"
SELECT x.X_id
FROM Entity x
WHERE NOT EXISTS (
    -- For each required item...
    SELECT 1
    FROM RequiredItems y
    WHERE NOT EXISTS (
        -- ...check if this entity has it
        SELECT 1
        FROM Relationship r
        WHERE r.X_id = x.X_id
        AND   r.Y_id = y.Y_id
    )
);
```

**Concrete Example: "Find students who have enrolled in ALL courses"**

```sql
SELECT s.StudentName
FROM Student s
WHERE NOT EXISTS (
    -- Is there ANY course...
    SELECT 1
    FROM Course c
    WHERE NOT EXISTS (
        -- ...that this student has NOT enrolled in?
        SELECT 1
        FROM Enrollment e
        WHERE e.StudentID = s.StudentID
        AND   e.CourseID  = c.CourseID
    )
);
-- If no such course exists → student has enrolled in ALL courses ✓
```

**Another Example: "Find suppliers who supply ALL red parts"**

```sql
SELECT sp.SupplierName
FROM Supplier sp
WHERE NOT EXISTS (
    SELECT 1
    FROM Part p
    WHERE p.Color = 'Red'
    AND NOT EXISTS (
        SELECT 1
        FROM Supply s
        WHERE s.SupplierID = sp.SupplierID
        AND   s.PartID     = p.PartID
    )
);
```

> **Exam Tip:** Whenever you see ALL/EVERY/EACH in a query question, immediately write the double NOT EXISTS skeleton. It's the only reliable way to express relational division in standard SQL.

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

## 10. SQL QUERY PATTERN RECOGNITION — Trigger Words → Solution

| Question Type | Trigger Words | Memory Trick | Pattern |
|---|---|---|---|
| Highest Salary | Highest, Maximum | `MAX()` | `MAX(Salary)` |
| 2nd Highest Salary | Second Highest | `MAX(< MAX())` | `MAX(Salary WHERE Salary < MAX(Salary))` |
| Nth Highest Salary | 3rd, 4th, Nth | Sort Descending | `ORDER BY Salary DESC LIMIT 1 OFFSET N-1` |
| Highest per Department | Per Department | `GROUP BY + MAX` | `GROUP BY DeptID` |
| Duplicates | Duplicate Names/Emails | `COUNT(*) > 1` | `GROUP BY ... HAVING COUNT(*) > 1` |
| More than N Employees | More than, At least | `GROUP BY + HAVING` | `HAVING COUNT(*) > N` |
| Above Department Average | Average per Dept | Correlated Subquery | `Salary > AVG(Dept Salary)` |
| No Matching Records | Not assigned, No project | `NOT EXISTS` | Missing Relationship |
| At Least One Match | Exists | `EXISTS` | Match Found |
| ALL / EVERY / EACH | All courses, Every dept | Double `NOT EXISTS` | Relational Division |

---

## 11. EXISTS vs NOT EXISTS — Quick Meaning

| Clause | Meaning |
|---|---|
| `EXISTS` | At least one row found |
| `NOT EXISTS` | No row found |
| Inner `NOT EXISTS` | Missing item (entity doesn't have this one) |
| Outer `NOT EXISTS` | No missing item (entity has ALL of them) |

---

## 12. NORMALISATION — Quick Identification Table

| NF | How to Identify | Problem | Fix |
|---|---|---|---|
| **1NF** | Comma / Multiple values in one field | Non-atomic values | Split into multiple rows |
| **2NF** | Part of composite key → attribute | Partial Dependency | Move dependent attributes to new table |
| **3NF** | Non-key → Non-key | Transitive Dependency | Create separate table |
| **BCNF** | Determinant is not a Candidate Key | BCNF Violation | Decompose using violating FD |

### Spot the Violation — Example Patterns

| Dependency Pattern | NF Violation |
|---|---|
| `StudentID → Subjects(DBMS, OS, CN)` | **1NF** — multi-valued field |
| `(StudentID, CourseID) → Grade` and `StudentID → StudentName` | **2NF** — partial dependency (StudentName depends only on StudentID, not full PK) |
| `EmpID → DeptID → DeptName` | **3NF** — transitive dependency (DeptName depends on DeptID, not on EmpID directly) |
| `InstructorID → Room` where InstructorID is not a key | **BCNF** — determinant is not a candidate key |

---

## 13. LAST-MINUTE RECALL — One Line Each

| Concept | Remember |
|---|---|
| Highest Salary | `MAX()` |
| 2nd Highest Salary | `MAX(< MAX())` |
| Per Department | `GROUP BY` |
| Group Filter | `HAVING` |
| Duplicate Rows | `COUNT(*) > 1` |
| Missing Records | `NOT EXISTS` |
| ALL / EVERY | Relational Division (Double NOT EXISTS) |
| ALL | No Missing Items |
| EXISTS | Match Found |
| NOT EXISTS | Match Missing |
| 1NF | Atomic Values (no commas in a field) |
| 2NF | Whole Key Dependency (no partial) |
| 3NF | No Transitive Dependency (non-key ✗→ non-key) |
| BCNF | Every Determinant is a Key |

---

*Good luck with your exam!* 🎯
