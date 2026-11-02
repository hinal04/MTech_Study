# Database Management Systems - Post-Semester Questions & Answers
## SSZG507 - Chapters 1-3, 5-7 (Foundations, SQL, NoSQL, Data Warehouses, Specialized Databases)

---

## Q1. Database Fundamentals and DBMS Architecture [3 Marks]

### Question:
Explain the evolution from file systems to DBMS. Discuss: (i) limitations of file-based systems, (ii) advantages of DBMS, (iii) DBMS three-layer architecture (external, conceptual, internal), (iv) provide example of each layer for employee database.

### Answer:

**Limitations of File-Based Systems [0.75 marks]:**

- **Data Redundancy:** Same data stored in multiple files; inconsistency risk
- **Data Inconsistency:** Update in one file may miss related files; dangling references
- **Poor Security:** File access control limited; all users see all data
- **Limited Concurrency:** Multiple users accessing same file cause conflicts
- **No Data Independence:** Applications tightly coupled to file structure; changes break code
- **Recovery Difficulty:** No systematic backup/recovery; manual restoration

Example: Employee payroll system with separate files for salary, benefits, tax — duplicates, inconsistencies

**Advantages of DBMS [0.75 marks]:**

- **Data Centralization:** Single repository; reduced redundancy
- **Consistency:** Constraints ensure related data stays synchronized
- **Security:** Role-based access; users see only authorized data
- **Concurrency:** Lock management allows multiple users simultaneously
- **Data Independence:** Logical structure independent of physical storage
- **Recovery:** Automated backup; ACID properties ensure crash recovery
- **Query Language:** SQL provides flexible data access without programming

**Three-Layer Architecture [1 mark]:**

1. **External Layer (User View):**
   - Multiple external schemas; each user/application sees customized view
   - Example: Payroll manager sees salary data; HR sees benefits
   - Hides physical storage details

2. **Conceptual Layer (Logical Design):**
   - Single conceptual schema; complete logical view of database
   - Tables, relationships, constraints
   - Example: Employee(EmpID, Name, DeptID, Salary), Department(DeptID, Name)
   - Independent of physical storage location

3. **Internal Layer (Physical Storage):**
   - Physical storage on disk; how data actually stored
   - Indexes, file organization, compression
   - Example: Employee table stored in B-tree index on EmpID

**Data Independence Benefit:**
- Can change physical storage (optimize index) without touching external/conceptual layers
- Can add new external view without modifying storage

---

## Q2. Relational Data Model and Normalization [3.5 Marks]

### Question:
Explain the relational model and normalization. Discuss: (i) relational model concepts (tables, rows, columns, keys), (ii) functional dependencies, (iii) normal forms (1NF, 2NF, 3NF, BCNF), (iv) provide example of unnormalized data and normalized to 3NF, (v) why normalization matters.

### Answer:

**Relational Model Concepts [0.75 marks]:**

- **Relation (Table):** Named collection of tuples (rows)
- **Attribute (Column):** Named property of entities
- **Tuple (Row):** Single entity with values for all attributes
- **Primary Key:** Attribute(s) uniquely identifying each tuple
- **Foreign Key:** Attribute(s) referencing primary key in another relation
- **Candidate Key:** Any attribute(s) that could be primary key
- **Referential Integrity:** Foreign key values must exist in referenced table

**Example:**
```
Employee(EmpID, Name, Department, DeptManager)
- EmpID is primary key
- DeptManager is foreign key referencing Department table
```

**Functional Dependencies [0.5 marks]:**

Functional Dependency (FD): X → Y means if two tuples have same X value, they have same Y value

Example: In Employee table:
- EmpID → Name (each employee has one name)
- EmpID → Department (each employee in one department)
- Department → DeptManager (each department has one manager; not EmpID → Department)

**Normal Forms [1.5 marks]:**

| Normal Form | Definition | Ensures |
|-------------|-----------|---------|
| **1NF** | All values atomic (no multi-valued attributes) | Each cell contains single value, not array/list |
| **2NF** | 1NF + no non-key attribute depends on part of composite key | Eliminate partial dependencies |
| **3NF** | 2NF + no non-key attribute depends on another non-key attribute | Eliminate transitive dependencies |
| **BCNF** | Every determinant is candidate key | Stricter than 3NF; all FDs resolved |

**Normalization Example [0.75 marks]:**

**Unnormalized Data:**
```
StudentEnrollment:
StudentID | Name   | Courses                    | CourseInstructor
101       | Alice  | [Math, Physics, Chemistry] | [Prof.A, Prof.B, Prof.C]
```
- Violates 1NF (multi-valued attributes)

**1NF Normalization:**
```
StudentEnrollment:
StudentID | Name   | Course    | CourseInstructor
101       | Alice  | Math      | Prof.A
101       | Alice  | Physics   | Prof.B
101       | Alice  | Chemistry | Prof.C
```
- Atomic values; but StudentID not candidate key (StudentID + Course is)

**2NF Normalization:**
```
Student:
StudentID | Name
101       | Alice

Enrollment:
StudentID | CourseID
101       | M101

Course:
CourseID | CourseName | Instructor
M101     | Math       | Prof.A
P102     | Physics    | Prof.B
```
- No partial dependencies (no non-key depends on part of composite key)

**3NF Normalization:**
```
Add Department Table to avoid transitive dependency:

Professor:
ProfID  | Name    | DeptID
ProfA   | Prof.A  | Dept1

Department:
DeptID | DeptName  | DeptHead
Dept1  | Science   | Prof.X
```
- Eliminate: Course → Instructor → Department (transitive dependency)
- Now: Course → Instructor, Instructor → Department (no transitive from Course to Department through non-key)

**Why Normalization Matters [0.5 marks]:**

- **Eliminate Redundancy:** Reduce storage; no duplicate data
- **Prevent Anomalies:**
  - Insert: Can add course without instructor data
  - Update: Change instructor name once, not in multiple places
  - Delete: Remove student doesn't lose course information
- **Maintain Consistency:** Changes reflected everywhere
- **Flexibility:** Easy to add new courses, instructors

---

## Q3. SQL Transactions and Concurrency Control [3 Marks]

### Question:
Explain SQL transaction management. Discuss: (i) ACID properties with SQL examples, (ii) transaction states (active, committed, aborted), (iii) concurrency problems (dirty read, non-repeatable read, phantom), (iv) isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE), (v) which level prevents which problem.

### Answer:

**ACID Properties in SQL [0.75 marks]:**

```sql
-- Atomicity: Entire transaction succeeds or fails
BEGIN TRANSACTION;
UPDATE Accounts SET balance = balance - 100 WHERE id = 1;
UPDATE Accounts SET balance = balance + 100 WHERE id = 2;
COMMIT; -- Both succeed or both rollback

-- Consistency: Constraints maintained
-- Total money in system stays constant

-- Isolation: Concurrent transactions don't interfere
-- Transaction 1 doesn't see Transaction 2's uncommitted changes

-- Durability: After COMMIT, data persists
COMMIT;  -- Survives power loss, crashes
```

**Transaction States [0.5 marks]:**

```
Start
  ↓
Active (executing statements)
  ↓
  ├→ COMMIT → Committed (permanent; visible to others)
  │
  └→ ROLLBACK → Aborted (changes discarded)
```

**Concurrency Problems [1 mark]:**

1. **Dirty Read:** Transaction reads uncommitted changes from another transaction
   ```
   T1: UPDATE balance = 100 (not committed)
   T2: READ balance → sees 100 (dirty!)
   T1: ROLLBACK
   T2: Has invalid data from rolled-back transaction
   ```

2. **Non-Repeatable Read:** Transaction reads same row twice; gets different values
   ```
   T1: READ balance = $500
   T2: UPDATE balance = $600 (commits)
   T1: READ balance = $600 (different!)
   ```

3. **Phantom Read:** Transaction reads set of rows; another transaction inserts/deletes rows; re-read returns different set
   ```
   T1: SELECT COUNT(*) WHERE dept = 'Sales' → 10
   T2: INSERT new Sales employee (commits)
   T1: SELECT COUNT(*) WHERE dept = 'Sales' → 11 (phantom!)
   ```

**Isolation Levels and Problem Prevention [1 mark]:**

| Isolation Level | Dirty Read | Non-Repeatable | Phantom |
|----------------|-----------|----------------|---------|
| **READ UNCOMMITTED** | Possible ✗ | Possible ✗ | Possible ✗ |
| **READ COMMITTED** | Prevented ✓ | Possible ✗ | Possible ✗ |
| **REPEATABLE READ** | Prevented ✓ | Prevented ✓ | Possible ✗ |
| **SERIALIZABLE** | Prevented ✓ | Prevented ✓ | Prevented ✓ |

Implementation:
- READ UNCOMMITTED: No locks; fastest, least safe
- READ COMMITTED: Lock rows until commit
- REPEATABLE READ: Lock range; MySQL default
- SERIALIZABLE: Complete isolation; like sequential execution (slowest)

---

## Q4. NoSQL Data Stores [3.5 Marks]

### Question:
Explain NoSQL databases and compare with SQL. Discuss: (i) types of NoSQL (document, key-value, column-family, graph), (ii) advantages over SQL, (iii) disadvantages/trade-offs, (iv) CAP theorem applied to NoSQL, (v) provide example queries for each type, (vi) when to use each.

### Answer:

**Types of NoSQL [1.4 marks]:**

1. **Document Stores (MongoDB, CouchDB):**
   - Store semi-structured documents (JSON, BSON)
   - Flexible schema; documents in same collection can differ
   - Query language operates on document fields
   ```json
   db.employees.find({dept: "Sales", salary: {$gt: 50000}})
   // Finds employees in Sales earning >50k
   ```

2. **Key-Value Stores (Redis, Memcached, DynamoDB):**
   - Simple: key → value mapping
   - Ultra-fast access; used for caching
   - No querying; must know key
   ```
   SET user:101 '{"name": "Alice", "age": 30}'
   GET user:101  → {'name': 'Alice', 'age': 30}
   ```

3. **Column-Family Stores (HBase, Cassandra):**
   - Organized by columns not rows
   - Efficient for analytics (querying specific columns)
   - Distributed, high availability
   ```
   Row: employee:101
   Column families: personal, salary, benefits
   → huge scalability, not traditional SQL
   ```

4. **Graph Databases (Neo4j):**
   - Nodes, edges, properties
   - Efficient traversal (social networks, recommendations)
   ```
   MATCH (user:User)-[:FRIEND]->(friend:User)
   WHERE user.id = 101
   RETURN friend  → All friends of user 101
   ```

**Advantages over SQL [0.75 marks]:**

- **Scalability:** Horizontal; add nodes for more capacity
- **Flexibility:** Schema-less or flexible schema
- **Performance:** Optimized for specific access patterns (key-value very fast)
- **Availability:** Designed for high availability; CAP tradeoff explicitly handled
- **Handling Unstructured Data:** JSON, XML; natural fit
- **High Throughput:** Eventually consistent; lower latency

**Disadvantages/Trade-offs [0.75 marks]:**

- **Eventual Consistency:** Temporary inconsistency
- **No JOINs:** Must fetch from multiple places and combine in application
- **Limited Query Flexibility:** Optimized for specific patterns; hard to query unexpected ways
- **Learning Curve:** Different paradigm from SQL
- **Transactions:** Weak or no multi-document transactions (though improving)
- **Data Duplication:** Often duplicate data across stores (denormalization)

**CAP Theorem Applied [0.5 marks]:**

- **MongoDB:** AP (availability + partition tolerance) - eventually consistent
- **Cassandra:** AP - highly available, eventually consistent
- **HBase:** CP - consistent, reduced availability
- **Neo4j:** CP - consistency prioritized over availability

**When to Use Each:**

**Document Stores:** Content management, user profiles, flexible data
**Key-Value:** Caching, sessions, real-time analytics
**Column-Family:** Analytics, time-series, massive scale data
**Graph:** Social networks, recommendations, knowledge graphs

---

## Q5. Data Warehousing and OLAP [3 Marks]

### Question:
Explain data warehousing. Discuss: (i) data warehouse vs. OLTP database, (ii) dimensional modeling (facts, dimensions), (iii) star schema vs. snowflake schema, (iv) OLAP operations (slice, dice, drill-down, roll-up), (v) provide example data warehouse schema for sales.

### Answer:

**Data Warehouse vs. OLTP [0.75 marks]:**

| Aspect | OLTP | Data Warehouse |
|--------|------|-----------------|
| **Purpose** | Daily operations | Historical analysis |
| **Queries** | Simple, frequent inserts | Complex aggregations |
| **Data** | Current, detailed | Historical, summarized |
| **Design** | Normalized (reduce redundancy) | Denormalized (fast queries) |
| **Volume** | GB-TB | TB-PB |
| **Update** | Real-time, frequent | Batch, periodic |
| **Users** | Operational (many) | Analysts (few) |

Example:
- OLTP: Restaurant POS system recording each sale
- DW: Analyzing sales trends by region/time over years

**Dimensional Modeling [0.75 marks]:**

**Facts:** Measurable events; numerical data
- Sales amount, quantity sold, profit

**Dimensions:** Context for facts; attributes
- Time (day, month, year), Product, Region, Customer

**Measures:** Quantitative facts aggregated
- Total sales, average price, count of transactions

**Example Sales Facts & Dimensions:**
- Fact: Sale_ID, Product_ID, Customer_ID, Time_ID, Amount, Quantity
- Dimensions: Product(ID, Name, Category), Time(Date, Month, Quarter, Year), Customer(ID, Name, Region), Region(Region_Code, Country)

**Star vs. Snowflake Schema [0.75 marks]:**

**Star Schema:**
- Facts in center; dimensions radiating out
- Denormalized: dimension tables contain redundant data
- Query Efficiency: few joins needed
- Storage: More redundancy

```
              ┌─────────────┐
              │   Product   │
              └──────┬──────┘
                     │
     ┌───────────┬───┴────┬──────────┐
     │           │        │          │
┌────▼──┐    ┌───▼──┐ ┌──▼───┐  ┌──▼───┐
│ Time  │    │ Fact │ │Customer
 Customer│
└────────┘    └──────┘ └──────┘  └──────┘
```

**Snowflake Schema:**
- Normalized dimensions to 3NF
- Fewer redundancy
- More complex queries (more joins)
- Less storage

```
┌─────────────┐
│  Category   │
└──────┬──────┘
       │
┌──────▼──────┐
│   Product   │
└──────┬──────┘
       │
    ┌──┴────────────────┐
    │                   │
┌───▼──┐            ┌──▼───┐
│ Fact │            │ Time  │
└──────┘            └───────┘
```

**OLAP Operations [0.75 marks]:**

1. **Slice:** Select specific value from dimension
   - Sales for January only

2. **Dice:** Select ranges from multiple dimensions
   - Sales for Q1-Q2, Product = Electronics, Region = North

3. **Drill-Down:** Move to finer granularity
   - Sales by Year → Sales by Quarter → Sales by Month

4. **Roll-Up:** Move to coarser granularity
   - Sales by Day → Sales by Month → Sales by Year

5. **Pivot:** Rotate dimensions (row ↔ column)
   - Rows=Product, Columns=Region → Rows=Region, Columns=Product

---

## Q6. Indexing and Query Optimization [2.5 Marks]

### Question:
Explain indexing strategies in databases. Discuss: (i) why indexes needed, (ii) B-tree indexes, (iii) hash indexes, (iv) index selection criteria, (v) query optimization using indexes, (vi) when indexes hurt performance (write-heavy workloads).

### Answer:

**Why Indexes Needed [0.5 marks]:**

Without index:
```sql
SELECT * FROM employees WHERE salary > 100000;
-- Full table scan: examine all 1M rows: O(n)
```

With index on salary:
```sql
SELECT * FROM employees WHERE salary > 100000;
-- B-tree index lookup: O(log n) + output 10K rows
-- Much faster!
```

**B-Tree Indexes [0.75 marks]:**

- Balanced tree structure; maintains sorted order
- Each node has multiple keys and children
- Efficient for range queries: `WHERE salary BETWEEN 50K AND 100K`
- Typical in SQL databases (default choice)

Operations: Search O(log n), Insert O(log n), Range O(log n + output)

**Hash Indexes [0.75 marks]:**

- Hash function maps key to bucket
- Fast equality lookups: O(1) average
- NOT good for range queries (no ordering)
- Good for: `WHERE id = 101` (exact match)

Operations: Exact match O(1), Range O(n) (poor!)

**Trade-offs:**
- B-tree: Versatile; range and equality
- Hash: Super fast equality; terrible ranges

**Index Selection Criteria [0.5 marks]:**

Index on column if:
1. **Frequently used in WHERE clause:** `WHERE salary > 100K`
2. **Used for JOIN:** Foreign keys benefit from indexes
3. **Used for aggregation:** `GROUP BY dept_id` faster with index
4. **Selective:** Many rows filtered out

Avoid indexing:
- Low-cardinality columns (few unique values)
- Write-heavy tables (INSERT/UPDATE cost)
- Rarely queried columns

**Query Optimization [0.5 marks]:**

```sql
-- Without index: scans entire table
SELECT * FROM employees WHERE dept_id = 5 AND salary > 50000;

-- With indexes: can use index on dept_id to find candidates
-- Filter within those candidates by salary
-- Much faster if dept_id=5 is selective
```

**Index Maintenance Cost [0.5 marks]:**

Each INSERT/UPDATE/DELETE requires updating all indexes:
- 1 index: minimal overhead
- 20 indexes: significant INSERT cost

Write-heavy workloads may suffer from too many indexes.

---

## Q7. SQL vs. NoSQL Trade-offs and Selection [2.5 Marks]

### Question:
Compare SQL and NoSQL in modern systems. Discuss: (i) when to use SQL, (ii) when to use NoSQL, (iii) polyglot persistence (using both), (iv) migration challenges, (v) provide real-world examples of each choice.

### Answer:

**When to Use SQL [0.75 marks]:**

- **Complex Queries:** Multiple JOINs, aggregations
- **Transactions:** ACID across multiple tables
- **Consistency Critical:** Financial systems, inventory
- **Established Data Model:** Well-understood schema
- **Ad-hoc Queries:** Analysts need flexibility
- **Reporting/Analytics:** Complex report generation

**Examples:** Banking, E-commerce, HR systems

**When to Use NoSQL [0.75 marks]:**

- **Massive Scale:** Billions of records, petabytes data
- **High Availability:** Prioritize availability over consistency
- **Unstructured Data:** Images, videos, JSON documents
- **High Write Throughput:** Millions of writes/sec
- **Flexible Schema:** Data structure changes frequently
- **Real-time Analytics:** Cache, session data

**Examples:** Social media, IoT, content platforms, caching layer

**Polyglot Persistence (Using Both) [0.75 marks]:**

Modern systems often use both:
- **SQL:** Orders, inventory, user accounts (consistency matters)
- **NoSQL:** Caching, session data (fast access), analytics logs
- **Search:** Elasticsearch for full-text search
- **Graph:** Neo4j for social connections

**Example:**
```
E-commerce Platform:
- SQL Database: Orders, payments, inventory (ACID transactions)
- NoSQL Cache (Redis): Session data, shopping cart (fast)
- Document Store (MongoDB): Product reviews, user profiles (flexible)
- Search Engine: Elasticsearch for product search
- Time-series: InfluxDB for analytics and metrics
```

**Migration Challenges [0.75 marks]:**

From SQL to NoSQL:
- **Schema Design:** Denormalize; duplicate data as needed
- **JOIN Logic:** Move to application code
- **Transactions:** Split across documents; handle eventual consistency
- **Replication:** Data consistency across regions
- **Legacy Applications:** Update code to work with new model
- **Data Size:** Migration itself is complex for huge datasets

Real challenge: "We have SQL system working fine; why migrate?" — cost-benefit analysis needed

---

## Marks Summary
- Q1: 3 marks
- Q2: 3.5 marks
- Q3: 3 marks
- Q4: 3.5 marks
- Q5: 3 marks
- Q6: 2.5 marks
- Q7: 2.5 marks

**Total: 21.5 marks**



## Q8. NoSQL Document Design - MongoDB [3 Marks]

### Question:
Design MongoDB collections for an online bookstore with:
- Books (title, author, ISBN, category, price, reviews)
- Orders (order items, quantities, customer info, status)
- Users (profiles, preferences, purchase history)

For each: (a) collection schema, (b) embedding vs. referencing decision, (c) query examples

### Answer:

**Collection Design [1 mark]:**

**Books Collection:**
```json
db.books.insertOne({
  _id: ObjectId(),
  title: "MongoDB Guide",
  author: "John Doe",
  isbn: "123-456-789",
  category: "Database",
  price: 45.99,
  reviews: [
    { rating: 5, comment: "Excellent!", user: "Alice" },
    { rating: 4, comment: "Good coverage", user: "Bob" }
  ],
  inStock: 50,
  tags: ["database", "nosql", "programming"]
})
```

**Embedding vs. Referencing [1 mark]:**

**EMBED when:** Data accessed together, small, one-to-few, no update conflicts
**REFERENCE when:** Large documents, many-to-many, independent updates

**Query Examples [1 mark]:**

```javascript
// Find books with rating > 4
db.books.find({ reviews: { $elemMatch: { rating: { $gte: 4 } } } })

// Orders with total > $100
db.orders.find({ totalAmount: { $gt: 100 } })
```

---

## Q9. Database Backup and Recovery [3 Marks]

### Question:
Design backup/recovery for critical financial database:
- 500 GB, 10,000 tx/sec
- RPO: 15 minutes, RTO: 1 hour

For this: (a) backup approach, (b) frequency, (c) recovery procedure, (d) testing

### Answer:

**Backup Approach [1 mark]:**
- Weekly full backup (Sunday 2 AM)
- Daily incremental backups
- Every 15 min transaction log backups

**Recovery Procedure [1 mark]:**
- Restore full backup (1 hour)
- Apply incremental backups (30 min)
- Apply transaction logs (8 hours)
- Total: Too long! Need hot standby

**Better Strategy [1 mark]:**
- Hot standby with replication
- Failover time: < 2 minutes
- Data loss: 0-30 seconds
- Meets RTO and RPO

---

## Q10. High Availability Multi-Region [3.5 Marks]

### Question:
Design multi-region deployment (NA, Europe, Asia):
- Single points of failure: NONE
- Consistency: Eventual
- Latency: < 100ms p99

Design: (a) topology, (b) consistency, (c) conflict resolution, (d) failover

### Answer:

**Multi-Master Replication [1 mark]:**
- Each region primary (writes accepted)
- Asynchronous replication (100-500ms lag)
- No master election

**Consistency Strategy [1 mark]:**
- Eventual consistency accepted
- Replication lag 1-2 seconds
- Application handles read-after-write

**Conflict Resolution [1 mark]:**
- Last-Write-Wins (by timestamp)
- Or: Merge via CRDT
- Or: User resolution

**Failover [0.5 marks]:**
- Region failure detected (30 seconds)
- Traffic redirected to nearest healthy region
- Downtime: ~1 minute
- Data loss: 0 (asynchronous committed)

---

## Q11. Query Optimization [3 Marks]

### Question:
Query: Find customers in California with spend > $1000

Tables: CUSTOMERS(id, name, state), ORDERS(id, customer_id, amount)

(a) Write query (b) Execution plans with/without index (c) Optimization

### Answer:

**Query [0.5 marks]:**
```sql
SELECT c.id, c.name, SUM(o.amount) as total
FROM CUSTOMERS c
JOIN ORDERS o ON c.id = o.customer_id
WHERE c.state = 'CA'
GROUP BY c.id, c.name
HAVING SUM(o.amount) > 1000;
```

**Without Index [1 mark]:**
- Full scan CUSTOMERS (1M) + ORDERS (10M)
- Nested loop: O(n²) = hours

**With Index [1 mark]:**
```sql
CREATE INDEX idx_state ON CUSTOMERS(state);
CREATE INDEX idx_cust_id ON ORDERS(customer_id);
```
- Index scan state: 100k rows
- Hash join: O(10M) = seconds
- 1000x faster!

---

## Q12. Monitoring and Maintenance [2.5 Marks]

### Question:
Design database monitoring system:
- Alert conditions
- Metrics
- Troubleshooting
- Maintenance tasks

### Answer:

**Alert Conditions [0.75 marks]:**
- Disk space > 80%
- Slow queries > 1 sec
- Connections > 80% max
- Replication lag > 10 sec
- Backup failures
- Lock timeouts > 10/hour

**Metrics [0.75 marks]:**
- Query response time (p50, p95, p99)
- Transactions/sec
- Cache hit ratio
- CPU, memory, disk I/O
- Connection count
- Lock waits

**Maintenance [1 mark]:**
- Daily: Alert monitoring, backup verification
- Weekly: VACUUM/ANALYZE, index rebuild
- Monthly: Capacity planning, full backup test

---

## Summary

| Q# | Topic | Marks |
|---|---|---|
| Q1-Q7 | Earlier questions | 21.5 |
| Q8 | MongoDB Design | 3 |
| Q9 | Backup/Recovery | 3 |
| Q10 | HA Multi-Region | 3.5 |
| Q11 | Query Optimization | 3 |
| Q12 | Monitoring | 2.5 |

**Total: 40 marks across 12 questions**

