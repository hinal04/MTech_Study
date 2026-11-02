# DBMS — Exam Practice Questions with Answers

> BITS Pilani — SSZG507: Modern Database Systems
> Sessions 1–4 | Mid-Sem Pattern
> Topics: SQL/NoSQL queries, Relational model, Normalisation, Distributed DB

**Exam Pattern:**
1. Queries — MongoDB, Redis (1–2 questions)
2. Conceptual — Scenario-based (SQL or NoSQL? Justify)
3. Relational model — Primary key (good primary key or not?)
4. Normalisation — 1NF, 2NF, 3NF
5. SQL Queries — 7–10 marks
6. Distributed DB — How to place a DB across a cluster, justify

---

## PART 1: MongoDB & Redis Queries (1–2 questions per paper)

---

### Q1 — MongoDB: Orders Collection

**Collection: `orders`**
```json
{ "order_id": ..., "customer_name": ..., "product": ...,
  "quantity": ..., "price": ..., "date": ..., "status": ... }
```

**(a) Find all orders where status = "shipped" and quantity > 5**

```javascript
db.orders.find({
  status: "shipped",
  quantity: { $gt: 5 }
})
```

**Explanation:** `$gt` is the "greater than" operator. MongoDB uses implicit AND when multiple conditions are in the same document.

**(b) Find the total revenue (sum of quantity × price) grouped by product**

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$product",
      totalRevenue: {
        $sum: { $multiply: ["$quantity", "$price"] }
      }
    }
  },
  { $sort: { totalRevenue: -1 } }
])
```

**Explanation:** The aggregation pipeline first `$group`s by product, computing revenue using `$multiply` and `$sum`. Then `$sort` orders results by revenue descending.

**(c) Update all orders with status "pending" older than 30 days to "cancelled"**

```javascript
var thirtyDaysAgo = new Date();
thirtyDaysAgo.setDate(thirtyDaysAgo.getDate() - 30);

db.orders.updateMany(
  {
    status: "pending",
    date: { $lt: thirtyDaysAgo }
  },
  {
    $set: { status: "cancelled" }
  }
)
```

**Explanation:** `updateMany` modifies all matching documents. `$lt` selects dates before the threshold. `$set` changes only the specified field.

**(d) Find the top 3 customers by total spending**

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$customer_name",
      totalSpending: {
        $sum: { $multiply: ["$quantity", "$price"] }
      }
    }
  },
  { $sort: { totalSpending: -1 } },
  { $limit: 3 }
])
```

**Explanation:** Group by customer, sum their spending, sort descending, and limit to 3. This is the standard "Top-N" pattern in MongoDB aggregation.

---

### Q2 — MongoDB: Students Collection

**Collection: `students`**
```json
{
  "roll_no": "2023CS001", "name": "Amit Sharma",
  "department": "CSE", "cgpa": 8.5,
  "courses": [
    { "course_id": "CS101", "grade": "A" },
    { "course_id": "CS201", "grade": "B+" }
  ]
}
```

**(a) Find students in "CSE" department with CGPA > 8.0**

```javascript
db.students.find({
  department: "CSE",
  cgpa: { $gt: 8.0 }
})
```

**Explanation:** Simple compound query with implicit AND. MongoDB handles nested document queries natively.

**(b) Find students enrolled in more than 5 courses**

```javascript
db.students.find({
  "courses": { $exists: true },
  $expr: { $gt: [{ $size: "$courses" }, 5] }
})
```

**Alternative using `$where`:**
```javascript
db.students.find({
  $where: "this.courses.length > 5"
})
```

**Explanation:** `$expr` with `$size` counts the array length at query time. The `$where` alternative is simpler but slower (JavaScript execution).

**(c) Aggregate: Average CGPA per department, sorted descending**

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$department",
      avgCGPA: { $avg: "$cgpa" },
      studentCount: { $sum: 1 }
    }
  },
  { $sort: { avgCGPA: -1 } }
])
```

**Sample Output:**
```json
{ "_id": "CSE",  "avgCGPA": 8.2, "studentCount": 150 }
{ "_id": "ECE",  "avgCGPA": 7.8, "studentCount": 120 }
{ "_id": "MECH", "avgCGPA": 7.5, "studentCount": 100 }
```

**Explanation:** `$group` with `$avg` computes the mean. Adding `$sum: 1` gives count for context. `$sort: -1` = descending order.

---

### Q3 — MongoDB Schema Design: E-Commerce Platform

**Question:** Design a MongoDB schema for an e-commerce platform (products, users, orders). When would you embed vs reference? Give examples of both.

**Answer:**

#### Schema Design

**1. Users Collection (Reference pattern)**
```json
{
  "_id": ObjectId("..."),
  "user_id": "U1001",
  "name": "Priya Patel",
  "email": "priya@email.com",
  "address": {
    "street": "MG Road",
    "city": "Bangalore",
    "state": "Karnataka",
    "pin": "560001"
  },
  "created_at": ISODate("2024-01-15")
}
```

**2. Products Collection (Self-contained)**
```json
{
  "_id": ObjectId("..."),
  "product_id": "P5001",
  "name": "Wireless Headphones",
  "category": "Electronics",
  "price": 2499,
  "stock": 150,
  "specs": {
    "brand": "Sony",
    "battery_life": "30 hours",
    "connectivity": "Bluetooth 5.0"
  },
  "reviews": [
    { "user_id": "U1001", "rating": 4, "comment": "Great sound", "date": ISODate("...") }
  ]
}
```

**3. Orders Collection (Hybrid — embed + reference)**
```json
{
  "_id": ObjectId("..."),
  "order_id": "ORD-20240115-001",
  "user_id": "U1001",
  "items": [
    {
      "product_id": "P5001",
      "name": "Wireless Headphones",
      "price": 2499,
      "quantity": 1
    }
  ],
  "total_amount": 2499,
  "status": "delivered",
  "order_date": ISODate("2024-01-15"),
  "delivery_address": {
    "street": "MG Road",
    "city": "Bangalore",
    "state": "Karnataka",
    "pin": "560001"
  }
}
```

#### Embed vs Reference Decision Matrix

| Scenario | Strategy | Reason |
|----------|----------|--------|
| Address in User | **Embed** | 1:1 relationship, always accessed together |
| Reviews in Product | **Embed** (with limit) | 1:few, displayed with product. If reviews grow large, move to separate collection |
| Items in Order | **Embed** (snapshot) | Order items are immutable once placed; embed a snapshot of product name/price at order time |
| User in Order | **Reference** (user_id) | 1:many — one user has many orders. Embedding full user in every order wastes space |
| Product in Order | **Hybrid** | Reference product_id for lookups, embed name/price as snapshot (price may change later) |

#### Key Principles
- **Embed when:** data is accessed together, 1:1 or 1:few, data doesn't change often
- **Reference when:** 1:many or many:many, data changes frequently, document size would exceed 16MB limit
- **Snapshot pattern:** embed a copy of data at a point in time (e.g., product price at order time) — even if the product price changes later, the order retains the original price

---

### Q4 — Redis Data Structures & Commands

**Question:** Explain Redis data structures (String, List, Set, Sorted Set, Hash) with one use case each. Write Redis commands for the given scenarios.

**Answer:**

#### Redis Data Structures Overview

| Data Structure | Description | Use Case |
|---------------|-------------|----------|
| **String** | Simple key-value (text, number, binary) | Caching API responses, counters |
| **List** | Ordered collection (doubly linked list) | Message queues, activity feeds |
| **Set** | Unordered collection of unique elements | Tags, unique visitors, mutual friends |
| **Sorted Set (ZSet)** | Set with a score for each element | Leaderboards, priority queues |
| **Hash** | Map of field-value pairs under one key | User profiles, session data, objects |

**(a) Store and retrieve user session data**

```redis
# Store session data (Hash)
HSET session:user123 username "amit_sharma"
HSET session:user123 login_time "2024-01-15T10:30:00"
HSET session:user123 role "admin"

# Or set multiple fields at once
HMSET session:user123 username "amit_sharma" login_time "2024-01-15T10:30:00" role "admin"

# Set expiry (30 minutes = 1800 seconds)
EXPIRE session:user123 1800

# Retrieve all session data
HGETALL session:user123
# Returns: username "amit_sharma" login_time "2024-01-15T10:30:00" role "admin"

# Retrieve single field
HGET session:user123 role
# Returns: "admin"

# Check if session exists
EXISTS session:user123
# Returns: 1 (exists) or 0 (expired/not found)
```

**Why Hash?** Stores multiple fields under one key. More memory-efficient than separate String keys. Can read/write individual fields without loading the entire object.

**(b) Implement a leaderboard (Sorted Set)**

```redis
# Add players with scores
ZADD leaderboard 1500 "player:rajesh"
ZADD leaderboard 2300 "player:priya"
ZADD leaderboard 1800 "player:amit"
ZADD leaderboard 3100 "player:sneha"
ZADD leaderboard 2700 "player:vikram"

# Get top 3 players (highest scores first)
ZREVRANGE leaderboard 0 2 WITHSCORES
# Returns: sneha 3100, vikram 2700, priya 2300

# Get player rank (0-indexed, highest = rank 0)
ZREVRANK leaderboard "player:priya"
# Returns: 2 (3rd place)

# Increment score (player scored 200 more points)
ZINCRBY leaderboard 200 "player:amit"
# amit now has 2000

# Get players with scores between 2000-3000
ZRANGEBYSCORE leaderboard 2000 3000 WITHSCORES

# Get total number of players
ZCARD leaderboard
# Returns: 5
```

**Why Sorted Set?** Automatic ordering by score. O(log N) insertion and rank lookup. Perfect for real-time leaderboards.

**(c) Implement a rate limiter (max 100 requests/minute/user)**

```redis
# Sliding window approach using Sorted Set
# On each request from user123:

# 1. Get current timestamp (milliseconds)
# timestamp = current_time_ms

# 2. Remove entries older than 1 minute
ZREMRANGEBYSCORE rate:user123 0 (current_time_ms - 60000)

# 3. Count requests in current window
ZCARD rate:user123

# 4. If count < 100, allow and log the request
ZADD rate:user123 current_time_ms current_time_ms

# 5. Set expiry on the key (cleanup)
EXPIRE rate:user123 60
```

**Simple counter approach (fixed window):**
```redis
# On each request:
SET rate:user123 0 EX 60 NX    # Create counter if not exists, expires in 60s
INCR rate:user123               # Increment counter
# If INCR returns > 100 → REJECT request
# If INCR returns <= 100 → ALLOW request
```

**Comparison:**
- Fixed window: simpler, but allows burst at window boundary (up to 200 requests in 2 seconds straddling the boundary)
- Sliding window: more accurate, prevents boundary bursts, slightly more memory

---

### Q5 — Redis Caching: Food Delivery App

**Question:** A food delivery app needs to cache restaurant menus. Design a Redis caching strategy.

**Answer:**

**(a) What data structure would you use?**

**Hash** — each restaurant menu is stored as a Hash where:
- Key: `menu:{restaurant_id}`
- Fields: item names or item IDs
- Values: JSON strings with item details (price, description, availability)

**Why Hash over String?**
- Can update individual menu items without rewriting the entire menu
- Can retrieve specific items without loading the full menu
- Memory-efficient for objects with multiple fields

**(b) Write commands to set, get, and expire menu data**

```redis
# SET: Store menu items for restaurant R101
HSET menu:R101 "butter_chicken" '{"price":350,"category":"main","veg":false,"available":true}'
HSET menu:R101 "paneer_tikka" '{"price":280,"category":"starter","veg":true,"available":true}'
HSET menu:R101 "gulab_jamun" '{"price":120,"category":"dessert","veg":true,"available":true}'

# SET entire menu at once
HMSET menu:R101 "dal_makhani" '{"price":250,"veg":true,"available":true}' "naan" '{"price":60,"veg":true,"available":true}'

# GET: Retrieve full menu
HGETALL menu:R101

# GET: Retrieve single item
HGET menu:R101 "butter_chicken"

# GET: Retrieve multiple specific items
HMGET menu:R101 "butter_chicken" "paneer_tikka"

# EXPIRE: Set TTL of 1 hour (3600 seconds)
EXPIRE menu:R101 3600

# SET with expiry together (String approach for full menu JSON)
SET menu:R101:full '{"items":[...]}' EX 3600

# Check remaining TTL
TTL menu:R101
```

**(c) Cache invalidation strategy when menu updates**

**Strategy: Write-Through + Event-Based Invalidation**

```
┌──────────┐    update     ┌──────────┐    invalidate    ┌───────┐
│Restaurant│ ──────────── │  App DB  │ ────────────── │ Redis │
│  Admin   │              │ (MongoDB)│                  │ Cache │
└──────────┘              └──────────┘                  └───────┘
```

```redis
# Option 1: Delete on update (lazy loading)
# When restaurant updates menu in primary DB:
DEL menu:R101
# Next read will miss cache → fetch from DB → populate cache

# Option 2: Update cache immediately (write-through)
# When restaurant updates butter_chicken price:
HSET menu:R101 "butter_chicken" '{"price":400,"veg":false,"available":true}'
EXPIRE menu:R101 3600   # Reset TTL

# Option 3: Pub/Sub for real-time invalidation
PUBLISH menu_updates '{"restaurant_id":"R101","action":"update","item":"butter_chicken"}'
# Subscribers (app servers) receive notification and refresh their local cache
```

**Best Practice: TTL + Event-Based**
- Set a reasonable TTL (1 hour) as safety net
- Use Pub/Sub or message queue for immediate invalidation on updates
- This ensures stale data is never served for more than TTL duration even if event is missed

---

### Q6 — Redis vs MongoDB Comparison

**Question:** Compare Redis vs MongoDB for: (a) session management, (b) product catalog, (c) real-time analytics. When to use which?

**Answer:**

| Aspect | Redis | MongoDB |
|--------|-------|---------|
| **Storage** | In-memory (RAM) | Disk-based with memory-mapped files |
| **Speed** | Sub-millisecond reads/writes | Millisecond-level reads/writes |
| **Data size** | Limited by RAM | Can store terabytes on disk |
| **Persistence** | Optional (RDB/AOF) | Default (durable writes) |
| **Query language** | Simple key-based commands | Rich query language + aggregation |
| **Data model** | Key-value with data structures | Document (JSON-like BSON) |

**(a) Session Management → Redis wins**
- Sessions need sub-millisecond reads (every HTTP request checks session)
- Sessions are small (< 1KB typically)
- Sessions need automatic expiry (TTL) — Redis has native `EXPIRE`
- Sessions are temporary — no need for disk persistence
- Redis Hash stores session fields efficiently

**Verdict:** Redis. MongoDB is overkill — you don't need rich queries on session data.

**(b) Product Catalog → MongoDB wins**
- Products have complex, nested structures (specs, variants, reviews)
- Products need rich queries (find by category, price range, brand, full-text search)
- Product catalog can be large (millions of products, GBs of data)
- Products need durable storage (not volatile)
- MongoDB's flexible schema handles varying product attributes

**Verdict:** MongoDB. Redis can't efficiently search across product attributes or store large catalogs.

**(c) Real-time Analytics → Both, depending on use case**

| Sub-use case | Best choice | Reason |
|-------------|-------------|--------|
| Live counters (page views, active users) | **Redis** | Atomic INCR, sub-ms latency |
| Leaderboards, top-N rankings | **Redis** | Sorted Sets with O(log N) rank |
| Aggregated reports (daily sales, user cohorts) | **MongoDB** | Aggregation pipeline, disk storage |
| Time-series data (sensor readings over months) | **MongoDB** (or InfluxDB) | Large volume, needs disk |
| Real-time dashboards (last 5 min metrics) | **Redis** | Speed, TTL for auto-cleanup |

**Verdict:** Use Redis for hot, real-time counters and leaderboards. Use MongoDB for historical aggregation and complex analytics queries. In practice, use both together — Redis as the hot cache layer, MongoDB as the persistent analytical store.

---
---

## PART 2: Conceptual — SQL or NoSQL Scenarios (Justify Your Choice)

---

### Q7 — Banking System

**Question:** A banking system needs to handle account transfers between branches. Should they use SQL or NoSQL? Justify with ACID properties.

**Answer: SQL (Relational DB — e.g., PostgreSQL, Oracle)**

**Justification with ACID:**

| ACID Property | Why Critical for Banking | How SQL Provides It |
|--------------|-------------------------|-------------------|
| **Atomicity** | A transfer must debit Account A AND credit Account B — both or neither | `BEGIN TRANSACTION ... COMMIT/ROLLBACK` ensures all-or-nothing |
| **Consistency** | Total money in system must be conserved (sum of all accounts unchanged after transfer) | Constraints (`CHECK balance >= 0`), triggers enforce business rules |
| **Isolation** | Two concurrent transfers from the same account must not cause overdraft | Transaction isolation levels (SERIALIZABLE for banking) prevent dirty reads |
| **Durability** | Once a transfer is confirmed, it must survive server crash | Write-ahead logs (WAL) ensure committed transactions are permanent |

**Why NOT NoSQL?**
- NoSQL databases (MongoDB, Cassandra) offer **eventual consistency** — after a transfer, Account A might show debited but Account B not yet credited. This is unacceptable for financial data.
- NoSQL lacks native multi-document transactions (MongoDB added them in v4.0, but they're slower and limited compared to SQL)
- Banking requires complex JOIN queries for reporting (account statements, audit trails, cross-branch reconciliation)
- Regulatory compliance (RBI, SEBI) mandates strict audit trails — SQL's referential integrity ensures data correctness

**Example Transaction:**
```sql
BEGIN TRANSACTION;
  UPDATE accounts SET balance = balance - 10000 WHERE account_id = 'A001';
  UPDATE accounts SET balance = balance + 10000 WHERE account_id = 'B002';
  INSERT INTO transactions (from_acc, to_acc, amount, timestamp)
    VALUES ('A001', 'B002', 10000, CURRENT_TIMESTAMP);
COMMIT;
-- If any step fails → automatic ROLLBACK, no money is lost
```

---

### Q8 — Social Media Platform (100M Users, 1B Posts)

**Question:** A social media platform stores user posts, likes, comments, and follower relationships. 100M users, 1B posts. SQL or NoSQL? Justify.

**Answer: NoSQL (multiple databases for different needs — polyglot persistence)**

| Data Type | Recommended DB | Justification |
|-----------|---------------|---------------|
| User posts | **MongoDB** (Document DB) | Posts have varying structure (text-only, text+image, video, poll). Flexible schema handles this without NULL columns. |
| Likes, comments | **Cassandra** (Wide-column) | Massive write throughput (millions of likes/sec). Append-only pattern. Horizontal scaling across nodes. |
| Follower graph | **Neo4j** (Graph DB) | "Friends of friends", "people you may know" are graph traversals. SQL JOINs become impossibly slow at this scale. |
| News feed cache | **Redis** | Pre-computed feed stored as sorted list. Sub-ms reads for feed rendering. |
| Search (posts, users) | **Elasticsearch** | Full-text search, hashtag search, trending topics. |

**Why NOT SQL alone?**
1. **Scale:** 100M users × 10 posts avg = 1B posts. SQL vertical scaling has limits; NoSQL horizontal scaling is designed for this.
2. **Flexible schema:** A text post, image post, and video post have different fields. In SQL, you'd need many NULL columns or complex EAV patterns. MongoDB stores each as a natural document.
3. **Read/Write ratio:** Social media is read-heavy (1:100 write:read). NoSQL allows denormalized data for fast reads (embed user name in post, avoiding JOINs).
4. **Graph queries:** "Show me friends of friends who liked this post" is O(n²) in SQL with JOINs but efficient in graph DBs.
5. **Eventual consistency is acceptable:** If a like count shows 999 instead of 1000 for a brief moment, that's fine. No financial transaction at stake.

**Where SQL is still used:** User account management (registration, authentication, billing) — small scale, needs ACID.

---

### Q9 — Hospital Patient Records

**Question:** A hospital manages patient records, prescriptions, and appointments. SQL or NoSQL? Justify.

**Answer: SQL (Relational DB — e.g., PostgreSQL, MySQL)**

**Justification:**

1. **Structured, predictable schema:** Patient records have fixed fields (name, DOB, blood group, allergies, insurance ID). Prescriptions have (drug, dosage, frequency, duration). These don't vary — a relational schema fits perfectly.

2. **ACID compliance is critical:**
   - A prescription for "Drug A" must NOT be recorded as "Drug B" — consistency matters for patient safety
   - If a doctor prescribes and it fails mid-write, partial data could be life-threatening — atomicity needed
   - Two doctors accessing the same patient record simultaneously must not see stale data — isolation required

3. **Referential integrity:**
   - Patient → Doctor (FK ensures a valid doctor is assigned)
   - Prescription → Patient (FK ensures prescription belongs to a real patient)
   - Appointment → Patient + Doctor + Room (multiple FK constraints)
   - NoSQL doesn't enforce these relationships at the DB level

4. **Complex queries for reporting:**
   ```sql
   -- Find patients with drug interaction risk
   SELECT p.name, rx1.drug, rx2.drug
   FROM patients p
   JOIN prescriptions rx1 ON p.patient_id = rx1.patient_id
   JOIN prescriptions rx2 ON p.patient_id = rx2.patient_id
   JOIN drug_interactions di ON rx1.drug = di.drug_a AND rx2.drug = di.drug_b;
   ```
   This multi-table JOIN is natural in SQL but awkward in NoSQL.

5. **Regulatory compliance:** Healthcare regulations (HIPAA in US, DISHA in India) require audit trails, access logs, and data integrity — all strengths of SQL databases.

6. **Scale is manageable:** A hospital has thousands of patients, not billions. SQL handles this volume easily without horizontal scaling.

---

### Q10 — IoT Platform (1M Sensor Readings/Second)

**Question:** An IoT platform receives 1 million sensor readings per second from 50,000 devices. SQL or NoSQL? Justify.

**Answer: NoSQL (Time-Series DB like InfluxDB, or Wide-Column DB like Cassandra)**

**Justification:**

1. **Massive write throughput:** 1M writes/second is beyond what a single SQL server can handle. Cassandra can handle millions of writes/second by distributing across nodes. InfluxDB is optimized specifically for time-series ingestion.

2. **Simple data pattern:** Each reading is: `{device_id, timestamp, sensor_type, value}`. No complex relationships, no JOINs needed. This is an append-only workload — perfect for NoSQL.

3. **Horizontal scaling:** As devices grow from 50K to 500K, just add more nodes. SQL vertical scaling (bigger server) hits a ceiling. NoSQL distributes data across a cluster by partition key (device_id).

4. **Time-based queries:** "Get readings from device D001 between 10:00-11:00" is a range scan on (device_id, timestamp) — extremely efficient in Cassandra's sorted storage.

5. **Data model:**
   ```
   Partition Key: device_id
   Clustering Key: timestamp (descending)
   Columns: sensor_type, value, unit
   ```
   This stores all readings for a device together, sorted by time — optimal for time-range queries.

6. **Data lifecycle:** Sensor data older than 1 year can be automatically expired (TTL in Cassandra). SQL requires manual cleanup jobs.

**Why NOT SQL?**
- Single SQL server: ~10K writes/second (100x too slow)
- SQL sharding is complex and not designed for append-only time-series
- JOINs (SQL's strength) are not needed — each reading is self-contained
- Schema is simple and fixed — SQL's rich constraint system is unused overhead

**Architecture:**
```
50K Devices → Message Queue (Kafka) → NoSQL (Cassandra/InfluxDB) → Dashboard (Grafana)
                                            ↓
                                    Batch Analytics (Spark → PostgreSQL for reports)
```

Use NoSQL for ingestion, optionally pipe aggregated data to SQL for complex reporting.

---

### Q11 — E-Commerce Product Catalog (Varying Attributes)

**Question:** An e-commerce company needs a product catalog with varying attributes (clothes have size/color, electronics have specs). SQL or NoSQL?

**Answer: NoSQL (MongoDB — Document DB)**

**Justification:**

**The problem with SQL:**
```sql
-- Approach 1: Single table with many NULLs
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  name VARCHAR(100),
  price DECIMAL,
  -- Clothing attributes
  size VARCHAR(10),      -- NULL for electronics
  color VARCHAR(20),     -- NULL for electronics
  fabric VARCHAR(30),    -- NULL for electronics
  -- Electronics attributes
  ram VARCHAR(10),       -- NULL for clothing
  storage VARCHAR(10),   -- NULL for clothing
  battery VARCHAR(20),   -- NULL for clothing
  screen_size VARCHAR(10) -- NULL for clothing
);
-- Problem: Dozens of NULL columns, wastes space, hard to extend
```

```sql
-- Approach 2: EAV (Entity-Attribute-Value) pattern
CREATE TABLE product_attributes (
  product_id INT,
  attribute_name VARCHAR(50),
  attribute_value VARCHAR(200),
  PRIMARY KEY (product_id, attribute_name)
);
-- Problem: Complex queries, poor performance, no type safety
-- "Find products where RAM > 8GB" requires string comparison → broken
```

**The NoSQL solution (MongoDB):**
```json
// Clothing product
{
  "product_id": "P001",
  "name": "Cotton T-Shirt",
  "category": "clothing",
  "price": 599,
  "size": ["S", "M", "L", "XL"],
  "color": ["red", "blue", "black"],
  "fabric": "cotton",
  "wash_care": "machine washable"
}

// Electronics product
{
  "product_id": "P002",
  "name": "Laptop Pro 15",
  "category": "electronics",
  "price": 85000,
  "specs": {
    "ram": "16GB",
    "storage": "512GB SSD",
    "processor": "i7-12th Gen",
    "screen": "15.6 inch FHD"
  },
  "warranty": "2 years"
}

// Book product
{
  "product_id": "P003",
  "name": "DBMS Fundamentals",
  "category": "books",
  "price": 450,
  "author": "Navathe",
  "isbn": "978-0-13-...",
  "pages": 800
}
```

**Why MongoDB wins:**
- Each product type has its own natural structure — no NULLs, no EAV
- Adding a new product category (e.g., "furniture" with dimensions) requires zero schema changes
- Rich queries work naturally: `db.products.find({"specs.ram": "16GB"})`
- Arrays are first-class citizens: `db.products.find({"size": "XL"})`

---

### Q12 — Government Tax Filing System

**Question:** A government tax filing system processes annual returns. SQL or NoSQL?

**Answer: SQL (Relational DB — e.g., Oracle, PostgreSQL)**

**Justification:**

1. **Highly structured data:** Tax forms have fixed, government-mandated fields (PAN, income, deductions, tax liability). Every taxpayer fills the same form. A relational schema with well-defined columns is a natural fit.

2. **ACID for financial transactions:**
   - Filing a return involves multiple tables (taxpayer, income_sources, deductions, tax_computed, payments). Must be atomic — a half-filed return is dangerous.
   - If a payment is recorded, it MUST be durable and linked to the correct return.

3. **Complex reporting queries:**
   ```sql
   -- Total tax collected by state and income bracket
   SELECT t.state, 
          CASE WHEN t.total_income < 500000 THEN '< 5L'
               WHEN t.total_income < 1000000 THEN '5-10L'
               ELSE '> 10L' END AS bracket,
          SUM(t.tax_paid) as total_tax,
          COUNT(*) as taxpayer_count
   FROM taxpayers t
   JOIN returns r ON t.pan = r.pan
   WHERE r.assessment_year = '2024-25'
   GROUP BY t.state, bracket
   ORDER BY total_tax DESC;
   ```
   These aggregation + JOIN queries are SQL's bread and butter.

4. **Referential integrity:**
   - PAN number → must exist in taxpayer master table
   - Deduction claims → must reference valid section codes (80C, 80D, etc.)
   - Payment receipts → must link to valid return and bank account
   - SQL foreign keys enforce all of this; NoSQL would require application-level validation

5. **Regulatory audit trail:** Government systems require complete audit history — who filed what, when, any amendments. SQL's transaction logs and trigger-based audit tables handle this natively.

6. **Scale is predictable:** India has ~70M taxpayers filing annually. This is large but well within SQL capacity (with proper indexing and partitioning). It's not the "unlimited horizontal scale" scenario that demands NoSQL.

**Why NOT NoSQL?**
- Tax forms don't have varying schemas — everyone files the same form
- Eventual consistency is unacceptable — a taxpayer's liability must be accurate immediately
- Complex cross-table queries (income vs deductions vs payments) need JOINs
- Government compliance mandates relational integrity and audit

---
---

## PART 3: Relational Model — Primary Key Analysis

---

### Q13 — EMPLOYEE Table Primary Key

**Table:** `EMPLOYEE(EmpID, Name, Email, Phone, DeptID, Salary)`

**(a) Identify all candidate keys**

A candidate key is a minimal set of attributes that uniquely identifies each row.

| Attribute(s) | Unique? | Minimal? | Candidate Key? |
|-------------|---------|----------|----------------|
| {EmpID} | Yes (system-generated) | Yes | ✅ Yes |
| {Email} | Yes (unique per employee) | Yes | ✅ Yes |
| {Name} | No (two "Rahul Sharma" possible) | — | ❌ No |
| {Phone} | Possibly unique, but may change | Yes | ⚠️ Marginal (depends on policy) |
| {Name, Phone} | Likely unique | Not minimal if Phone alone is unique | ❌ Not ideal |

**Candidate Keys: {EmpID} and {Email}**

**(b) Which is the best primary key? Justify**

**Best PK: {EmpID}**

| Criteria | EmpID ✅ | Email ❌ |
|----------|---------|---------|
| **Immutable** | Never changes once assigned | Can change (marriage, company switch) |
| **Compact** | Small integer (4 bytes) | Variable-length string (20-50 bytes) |
| **System-generated** | No business meaning → stable | Business data → subject to change |
| **Indexing** | Integer comparisons are fast | String comparisons are slower |
| **Foreign keys** | Small FK in related tables | Large FK wastes space in child tables |
| **Privacy** | No privacy concern | Email is PII — showing in FK refs is a concern |

**Rule of thumb:** Always prefer a surrogate (system-generated, meaningless) key over a natural (business-meaning) key as the primary key.

**(c) Is {Name, Phone} a good primary key? Why or why not?**

**No. {Name, Phone} is a BAD primary key.**

| Problem | Explanation |
|---------|-------------|
| **Not immutable** | Names change (marriage: "Priya Sharma" → "Priya Patel"). Phone numbers change when switching providers. |
| **Not unique guaranteed** | Two employees named "Amit Kumar" with different phone numbers today could theoretically have same name+phone in future (e.g., one changes phone to match). |
| **Composite key overhead** | Every FK in child tables (WORKS_ON, ATTENDANCE) must store both Name AND Phone — wastes space and complicates JOINs |
| **String comparison** | Slower than integer comparison for lookups and JOINs |
| **Data quality** | Name formats vary ("R. Sharma" vs "Rahul Sharma") — comparison issues |

---

### Q14 — ENROLLMENT Table Dependencies

**Table:** `ENROLLMENT(StudentID, CourseID, Semester, Grade, InstructorID)`

**(a) What is the primary key?**

**PK: {StudentID, CourseID, Semester}**

**Reasoning:** A student can take the same course in different semesters (retake). So {StudentID, CourseID} alone isn't unique. Adding Semester makes it unique — a student takes a specific course exactly once in a given semester.

**(b) Are there any partial or transitive dependencies?**

**Functional Dependencies:**
- {StudentID, CourseID, Semester} → Grade (full dependency — grade depends on who took what course and when)
- {CourseID, Semester} → InstructorID (partial dependency — instructor is assigned per course per semester, regardless of which student)

**Analysis:**

| FD | Type | Problem |
|----|------|---------|
| {StudentID, CourseID, Semester} → Grade | Full dependency | ✅ No issue |
| {CourseID, Semester} → InstructorID | **Partial dependency** | ❌ InstructorID depends on only PART of the PK |

**This means the table is NOT in 2NF.**

**Anomalies caused by partial dependency:**
- **Insert anomaly:** Can't record that "Dr. Sharma teaches CS101 in Sem 1" until a student enrolls
- **Update anomaly:** If instructor changes, must update every row for that course-semester
- **Delete anomaly:** If all students drop the course, we lose the instructor assignment

**Decomposition to 2NF:**
```
ENROLLMENT(StudentID, CourseID, Semester, Grade)
    PK: {StudentID, CourseID, Semester}

COURSE_OFFERING(CourseID, Semester, InstructorID)
    PK: {CourseID, Semester}
```

Now every non-key attribute depends on the **full** primary key in its table.

---

### Q15 — Flight BOOKING Table

**Table:** `BOOKING(BookingRef, PassengerName, FlightNo, Date, Seat, Price)`

**Question:** Is BookingRef a good primary key? What if two passengers share a booking?

**Answer:**

**Scenario 1: One passenger per booking** → BookingRef IS a valid PK
- Each booking is for exactly one passenger
- BookingRef uniquely identifies the row
- This works fine

**Scenario 2: Multiple passengers per booking (e.g., family booking)** → BookingRef is NOT sufficient

**Problem demonstration:**

| BookingRef | PassengerName | FlightNo | Date | Seat | Price |
|-----------|--------------|----------|------|------|-------|
| BK001 | Rajesh Kumar | AI101 | 2024-03-15 | 12A | 5000 |
| BK001 | Priya Kumar | AI101 | 2024-03-15 | 12B | 5000 |

BookingRef = "BK001" returns TWO rows → it's not unique → **not a valid PK**.

**Solutions:**

**Solution 1: Composite PK**
```sql
CREATE TABLE BOOKING (
  BookingRef VARCHAR(10),
  PassengerName VARCHAR(100),
  FlightNo VARCHAR(10),
  Date DATE,
  Seat VARCHAR(5),
  Price DECIMAL,
  PRIMARY KEY (BookingRef, PassengerName)
);
```
**Problem:** PassengerName is not reliable (duplicates possible, name changes).

**Solution 2 (Better): Add PassengerID**
```sql
CREATE TABLE BOOKING (
  BookingRef VARCHAR(10),
  PassengerID INT,
  FlightNo VARCHAR(10),
  Date DATE,
  Seat VARCHAR(5),
  Price DECIMAL,
  PRIMARY KEY (BookingRef, PassengerID)
);
```
**Better** — PassengerID is a surrogate key, immutable and unique.

**Solution 3 (Best): Normalised design**
```sql
CREATE TABLE BOOKING (
  BookingRef VARCHAR(10) PRIMARY KEY,
  FlightNo VARCHAR(10),
  Date DATE,
  TotalPrice DECIMAL
);

CREATE TABLE BOOKING_PASSENGER (
  BookingRef VARCHAR(10),
  PassengerID INT,
  Seat VARCHAR(5),
  Price DECIMAL,
  PRIMARY KEY (BookingRef, PassengerID),
  FOREIGN KEY (BookingRef) REFERENCES BOOKING(BookingRef)
);
```
**Best approach** — separates booking-level data from passenger-level data. No redundancy (FlightNo, Date stored once per booking, not per passenger).

---

### Q16 — ORDER_ITEMS Normalisation

**Table:** `ORDER_ITEMS(OrderID, ProductID, Quantity, Price, ProductName, CategoryName)`

**(a) Identify the primary key**

**PK: {OrderID, ProductID}**

An order can contain multiple products, and a product can appear in multiple orders. The combination uniquely identifies each line item.

**(b) Identify functional dependencies**

| FD | Description |
|----|-------------|
| {OrderID, ProductID} → Quantity, Price | Full dependency — quantity and price depend on the specific order-product combination |
| ProductID → ProductName | Partial dependency — product name depends on only part of the PK |
| ProductID → CategoryName | Partial dependency — category depends on only part of the PK |

Note: Price depends on the full PK ({OrderID, ProductID}) because the price at order time may differ from current product price (discounts, time-based pricing).

**(c) Is this in 2NF? 3NF? If not, decompose.**

**Check 1NF:** ✅ Yes — all attributes are atomic, no repeating groups.

**Check 2NF:** ❌ No — Partial dependencies exist:
- ProductID → ProductName (depends on part of composite PK)
- ProductID → CategoryName (depends on part of composite PK)

**Decomposition to 2NF:**
```
ORDER_ITEMS(OrderID, ProductID, Quantity, Price)
    PK: {OrderID, ProductID}
    All non-key attributes depend on the FULL PK ✅

PRODUCT(ProductID, ProductName, CategoryName)
    PK: {ProductID}
    All non-key attributes depend on the full PK ✅
```

**Check 3NF:** Is there a transitive dependency in PRODUCT?
- ProductID → ProductName ✅ (direct)
- ProductID → CategoryName — is this transitive via ProductName?
  - ProductName → CategoryName? No — "Wireless Mouse" doesn't determine category (could be "Electronics" or "Accessories" depending on classification)
  - So NO transitive dependency

**Final decomposition (2NF = 3NF in this case):**
```
ORDER_ITEMS(OrderID, ProductID, Quantity, Price)
PRODUCT(ProductID, ProductName, CategoryName)
```

---

### Q17 — Aadhaar Number vs System-Generated CitizenID

**Question:** Evaluate if Aadhaar Number is a good primary key for an Indian citizen database vs a system-generated CitizenID.

**Answer:**

| Criteria | Aadhaar Number | System CitizenID |
|----------|---------------|-----------------|
| **Uniqueness** | ✅ Unique (12-digit, UIDAI assigned) | ✅ Unique (auto-increment or UUID) |
| **Immutability** | ⚠️ Can change — Aadhaar can be re-issued, deactivated, or replaced in case of fraud | ✅ Never changes once assigned |
| **Size** | ❌ 12 digits (stored as BIGINT or CHAR(12)) — large | ✅ Can be INT (4 bytes) or small BIGINT |
| **Privacy** | ❌ Aadhaar is sensitive PII. If used as PK, it appears in every FK reference, every log, every debug output | ✅ No privacy concern — meaningless number |
| **External dependency** | ❌ Controlled by UIDAI, not your system. Format/rules could change | ✅ Fully under your control |
| **Indexing performance** | ❌ Larger key → larger indexes → slower B-tree traversal | ✅ Compact integer → fast B-tree operations |
| **Foreign key overhead** | ❌ Every child table stores 12-digit Aadhaar as FK | ✅ Every child table stores small INT as FK |
| **Business meaning** | ❌ Natural key — ties your schema to a government system | ✅ Surrogate key — decoupled from business logic |

**Verdict: System-generated CitizenID is the better primary key.**

**Recommended schema:**
```sql
CREATE TABLE citizens (
  citizen_id INT PRIMARY KEY AUTO_INCREMENT,  -- System PK
  aadhaar_no CHAR(12) UNIQUE NOT NULL,        -- Natural key as UNIQUE constraint
  name VARCHAR(100),
  dob DATE,
  address TEXT
);
```

**Key design principle:**
- Aadhaar should be a **UNIQUE constraint** (for lookups and validation) but NOT the primary key
- The surrogate `citizen_id` is the PK used in all foreign key relationships
- This gives you the best of both worlds: Aadhaar-based lookups + efficient surrogate-key JOINs

**When might Aadhaar as PK be acceptable?**
- In a small, single-table system with no child tables (e.g., a simple verification lookup service)
- Even then, it's not recommended due to privacy concerns

---
---

## PART 4: Normalisation (1NF → 2NF → 3NF — Worked Examples)

---

### Q18 — STUDENT_COURSE Normalisation

**Un-normalised table:**
`STUDENT_COURSE(StudentID, StudentName, CourseID, CourseName, InstructorName, InstructorPhone, Grade)`

**Sample Data:**

| StudentID | StudentName | CourseID | CourseName | InstructorName | InstructorPhone | Grade |
|-----------|-------------|----------|------------|----------------|-----------------|-------|
| S001 | Amit | CS101 | DBMS | Dr. Sharma | 9876543210 | A |
| S001 | Amit | CS102 | OS | Dr. Gupta | 9876543211 | B+ |
| S002 | Priya | CS101 | DBMS | Dr. Sharma | 9876543210 | A+ |
| S003 | Rahul | CS102 | OS | Dr. Gupta | 9876543211 | B |

---

**Step 1: Check 1NF**

✅ Already in 1NF:
- All values are atomic (no multi-valued attributes)
- Each row is unique (StudentID + CourseID identifies each row)
- No repeating groups

**PK: {StudentID, CourseID}**

---

**Step 2: Identify Functional Dependencies**

| FD | Type |
|----|------|
| {StudentID, CourseID} → Grade | Full dependency ✅ |
| StudentID → StudentName | **Partial dependency** ❌ (depends on part of PK) |
| CourseID → CourseName | **Partial dependency** ❌ |
| CourseID → InstructorName | **Partial dependency** ❌ |
| CourseID → InstructorPhone | **Partial dependency** ❌ |

**Anomalies in current form:**
- **Insert:** Can't add a new course until a student enrolls
- **Update:** If Dr. Sharma changes phone, must update every row where she teaches
- **Delete:** If S003 drops CS102, we lose the info that Dr. Gupta teaches OS

---

**Step 3: Convert to 2NF (remove partial dependencies)**

Decompose into tables where every non-key attribute depends on the FULL primary key:

```
STUDENT(StudentID, StudentName)
    PK: {StudentID}
    FD: StudentID → StudentName ✅

COURSE(CourseID, CourseName, InstructorName, InstructorPhone)
    PK: {CourseID}
    FD: CourseID → CourseName, InstructorName, InstructorPhone ✅

ENROLLMENT(StudentID, CourseID, Grade)
    PK: {StudentID, CourseID}
    FD: {StudentID, CourseID} → Grade ✅
```

Now in 2NF — no partial dependencies.

---

**Step 4: Check for 3NF (remove transitive dependencies)**

Look at COURSE table:
- CourseID → InstructorName (direct) ✅
- CourseID → InstructorPhone — is this transitive?
  - CourseID → InstructorName → InstructorPhone
  - Yes! InstructorName → InstructorPhone (each instructor has one phone)
  - This is a **transitive dependency** ❌

**Decompose COURSE to remove transitive dependency:**

```
COURSE(CourseID, CourseName, InstructorName)
    PK: {CourseID}
    FK: InstructorName references INSTRUCTOR

INSTRUCTOR(InstructorName, InstructorPhone)
    PK: {InstructorName}
```

*(Note: In practice, InstructorID would be better as PK for INSTRUCTOR, but we use InstructorName here to match the given schema.)*

---

**Final 3NF Schema:**
```
STUDENT(StudentID, StudentName)
COURSE(CourseID, CourseName, InstructorName)
INSTRUCTOR(InstructorName, InstructorPhone)
ENROLLMENT(StudentID, CourseID, Grade)
```

**Verification:** No partial dependencies, no transitive dependencies. ✅

---

### Q19 — EMPLOYEE_PROJECT Normalisation

**Un-normalised table:**
`EMPLOYEE_PROJECT(EmpID, EmpName, DeptID, DeptName, DeptHead, ProjectID, ProjectName, Hours)`

**Sample Data:**

| EmpID | EmpName | DeptID | DeptName | DeptHead | ProjectID | ProjectName | Hours |
|-------|---------|--------|----------|----------|-----------|-------------|-------|
| E001 | Amit | D10 | IT | Rajesh | P100 | Website | 20 |
| E001 | Amit | D10 | IT | Rajesh | P200 | Mobile App | 15 |
| E002 | Priya | D10 | IT | Rajesh | P100 | Website | 30 |
| E003 | Sneha | D20 | HR | Kavita | P300 | Recruitment | 25 |

---

**Step 1: Identify anomalies**

| Anomaly | Example |
|---------|---------|
| **Redundancy** | "D10, IT, Rajesh" repeated for every employee-project combination in IT dept |
| **Insert anomaly** | Can't add Dept D30 (Marketing) until an employee is assigned to a project in it |
| **Update anomaly** | If DeptHead of D10 changes from Rajesh to Vikram, must update every row where DeptID = D10 |
| **Delete anomaly** | If E003 leaves P300, we lose that HR dept exists with head Kavita |

---

**Step 2: Identify FDs and PK**

**PK: {EmpID, ProjectID}** (one employee works specific hours on one project)

| FD | Dependency Type |
|----|----------------|
| {EmpID, ProjectID} → Hours | Full ✅ |
| EmpID → EmpName, DeptID | **Partial** ❌ |
| DeptID → DeptName, DeptHead | **Transitive** ❌ (via EmpID → DeptID → DeptName) |
| ProjectID → ProjectName | **Partial** ❌ |

---

**Step 3: Convert to 2NF (remove partial dependencies)**

```
EMPLOYEE(EmpID, EmpName, DeptID)
    PK: {EmpID}

PROJECT(ProjectID, ProjectName)
    PK: {ProjectID}

WORKS_ON(EmpID, ProjectID, Hours)
    PK: {EmpID, ProjectID}
```

But EMPLOYEE still has: EmpID → DeptID → DeptName, DeptHead (transitive).

---

**Step 4: Convert to 3NF (remove transitive dependencies)**

```
EMPLOYEE(EmpID, EmpName, DeptID)
    PK: {EmpID}
    FK: DeptID references DEPARTMENT

DEPARTMENT(DeptID, DeptName, DeptHead)
    PK: {DeptID}

PROJECT(ProjectID, ProjectName)
    PK: {ProjectID}

WORKS_ON(EmpID, ProjectID, Hours)
    PK: {EmpID, ProjectID}
    FK: EmpID references EMPLOYEE
    FK: ProjectID references PROJECT
```

---

**Verification:**
- ✅ 1NF: All atomic values
- ✅ 2NF: No partial dependencies (all non-key attributes depend on full PK)
- ✅ 3NF: No transitive dependencies (DeptName, DeptHead now in their own table)
- Redundancy eliminated: Department info stored once, not per employee-project

---

### Q20 — INVOICE Normalisation

**Un-normalised table:**
`INVOICE(InvoiceNo, Date, CustomerID, CustomerName, CustomerCity, ProductID, ProductName, Qty, UnitPrice, TotalAmount)`

**Sample Data:**

| InvoiceNo | Date | CustID | CustName | CustCity | ProdID | ProdName | Qty | UnitPrice | Total |
|-----------|------|--------|----------|----------|--------|----------|-----|-----------|-------|
| INV001 | 2024-01-15 | C10 | Amit | Mumbai | P01 | Laptop | 2 | 50000 | 100000 |
| INV001 | 2024-01-15 | C10 | Amit | Mumbai | P02 | Mouse | 2 | 500 | 1000 |
| INV002 | 2024-01-16 | C20 | Priya | Delhi | P01 | Laptop | 1 | 50000 | 50000 |

---

**Step 1: Identify all FDs**

| FD | Explanation |
|----|-------------|
| InvoiceNo → Date, CustomerID | Each invoice has one date and one customer |
| CustomerID → CustomerName, CustomerCity | Each customer has one name and city |
| ProductID → ProductName, UnitPrice | Each product has one name and standard price |
| {InvoiceNo, ProductID} → Qty | Quantity depends on which product in which invoice |
| {InvoiceNo, ProductID} → TotalAmount | Total = Qty × UnitPrice (derived attribute) |

**PK: {InvoiceNo, ProductID}** (composite — each invoice can have multiple products)

---

**Step 2: Check 1NF** ✅
All values are atomic, no repeating groups, PK identified.

---

**Step 3: Convert to 2NF (remove partial dependencies)**

Partial dependencies (depend on part of composite PK):
- InvoiceNo → Date, CustomerID (partial — depends on InvoiceNo only)
- ProductID → ProductName, UnitPrice (partial — depends on ProductID only)

**Decomposition:**
```
INVOICE(InvoiceNo, Date, CustomerID)
    PK: {InvoiceNo}

PRODUCT(ProductID, ProductName, UnitPrice)
    PK: {ProductID}

INVOICE_ITEM(InvoiceNo, ProductID, Qty, TotalAmount)
    PK: {InvoiceNo, ProductID}
```

Now in 2NF ✅

---

**Step 4: Convert to 3NF (remove transitive dependencies)**

Check INVOICE table:
- InvoiceNo → CustomerID → CustomerName, CustomerCity
- This is a **transitive dependency**: CustomerName and CustomerCity depend on CustomerID, not directly on InvoiceNo

**Decompose:**
```
INVOICE(InvoiceNo, Date, CustomerID)
    PK: {InvoiceNo}
    FK: CustomerID references CUSTOMER

CUSTOMER(CustomerID, CustomerName, CustomerCity)
    PK: {CustomerID}
```

Check INVOICE_ITEM:
- TotalAmount = Qty × UnitPrice — this is a **derived attribute**
- Best practice: don't store derived attributes (compute them in queries)
- Remove TotalAmount

---

**Final 3NF Schema:**
```
CUSTOMER(CustomerID, CustomerName, CustomerCity)
    PK: {CustomerID}

PRODUCT(ProductID, ProductName, UnitPrice)
    PK: {ProductID}

INVOICE(InvoiceNo, Date, CustomerID)
    PK: {InvoiceNo}
    FK: CustomerID references CUSTOMER

INVOICE_ITEM(InvoiceNo, ProductID, Qty)
    PK: {InvoiceNo, ProductID}
    FK: InvoiceNo references INVOICE
    FK: ProductID references PRODUCT
```

**To get TotalAmount:** `SELECT Qty * UnitPrice AS TotalAmount FROM INVOICE_ITEM NATURAL JOIN PRODUCT`

---

### Q21 — BCNF Decomposition

**Table:** `COURSE_ASSIGN(CourseID, InstructorID, Room, TimeSlot)`

**Given FDs:**
- {CourseID, TimeSlot} → InstructorID, Room
- InstructorID → Room (each instructor is assigned a fixed room)

**Question:** Is this in BCNF? If not, decompose.

**Answer:**

**Step 1: Identify candidate keys**

From {CourseID, TimeSlot} → InstructorID, Room:
- {CourseID, TimeSlot} determines all other attributes
- So **{CourseID, TimeSlot} is a candidate key** (and the PK)

**Step 2: Check BCNF condition**

BCNF requires: For every non-trivial FD X → Y, X must be a superkey.

| FD | Is LHS a superkey? | BCNF? |
|----|-------------------|-------|
| {CourseID, TimeSlot} → InstructorID, Room | Yes — it's the PK | ✅ |
| InstructorID → Room | Is InstructorID a superkey? **No** — InstructorID alone doesn't determine CourseID or TimeSlot | ❌ **BCNF violation** |

**The table is NOT in BCNF** because InstructorID → Room and InstructorID is not a superkey.

**Note:** It IS in 3NF (Room is not transitively dependent on the PK via a non-key attribute chain in the traditional sense, but BCNF is stricter than 3NF).

**Step 3: Decompose**

Split the violating FD into its own table:

```
INSTRUCTOR_ROOM(InstructorID, Room)
    PK: {InstructorID}
    FD: InstructorID → Room ✅ (InstructorID is now a superkey in this table)

COURSE_ASSIGN(CourseID, TimeSlot, InstructorID)
    PK: {CourseID, TimeSlot}
    FK: InstructorID references INSTRUCTOR_ROOM
    FD: {CourseID, TimeSlot} → InstructorID ✅
```

**Verification:**
- INSTRUCTOR_ROOM: InstructorID → Room, InstructorID is the PK (superkey) ✅ BCNF
- COURSE_ASSIGN: {CourseID, TimeSlot} → InstructorID, {CourseID, TimeSlot} is the PK (superkey) ✅ BCNF
- Room is derived via JOIN: `COURSE_ASSIGN JOIN INSTRUCTOR_ROOM ON InstructorID`

**Trade-off:** To find the room for a course, we now need a JOIN. This is the classic normalisation trade-off — reduced redundancy at the cost of more JOINs.

---
---

## PART 5: SQL Queries (7–10 marks)

**Schema:**
```sql
EMPLOYEE(EmpID, Name, Salary, DeptID, ManagerID, JoinDate)
DEPARTMENT(DeptID, DeptName, Location, Budget)
PROJECT(ProjectID, ProjectName, DeptID, StartDate, EndDate)
WORKS_ON(EmpID, ProjectID, Hours)
```

---

### Q22 (10 marks) — SQL Queries Set 1

**(a) Find employees earning more than their manager**

```sql
SELECT e.EmpID, e.Name, e.Salary AS EmpSalary, 
       m.Name AS ManagerName, m.Salary AS ManagerSalary
FROM EMPLOYEE e
JOIN EMPLOYEE m ON e.ManagerID = m.EmpID
WHERE e.Salary > m.Salary;
```

**Explanation:** Self-join — EMPLOYEE table joined to itself. Alias `e` = employee, `m` = manager. The `e.ManagerID = m.EmpID` link connects each employee to their manager.

---

**(b) Find departments with more than 5 employees AND average salary > 50000**

```sql
SELECT d.DeptID, d.DeptName, 
       COUNT(e.EmpID) AS EmpCount, 
       AVG(e.Salary) AS AvgSalary
FROM DEPARTMENT d
JOIN EMPLOYEE e ON d.DeptID = e.DeptID
GROUP BY d.DeptID, d.DeptName
HAVING COUNT(e.EmpID) > 5 AND AVG(e.Salary) > 50000;
```

**Explanation:** `GROUP BY` + `HAVING` filters groups after aggregation. `WHERE` filters rows before grouping; `HAVING` filters after.

---

**(c) Find employees who work on ALL projects in their department (division)**

```sql
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
WHERE NOT EXISTS (
    SELECT p.ProjectID
    FROM PROJECT p
    WHERE p.DeptID = e.DeptID
    AND NOT EXISTS (
        SELECT 1
        FROM WORKS_ON w
        WHERE w.EmpID = e.EmpID AND w.ProjectID = p.ProjectID
    )
);
```

**Explanation:** This is the **relational division** pattern — "for all" queries. Logic: find employees where there is NO project in their department that they DON'T work on. Double NOT EXISTS = universal quantifier.

---

**(d) Find the department with the highest total salary expenditure**

```sql
SELECT d.DeptID, d.DeptName, SUM(e.Salary) AS TotalSalary
FROM DEPARTMENT d
JOIN EMPLOYEE e ON d.DeptID = e.DeptID
GROUP BY d.DeptID, d.DeptName
ORDER BY TotalSalary DESC
LIMIT 1;
```

**Alternative (without LIMIT, works in all SQL dialects):**
```sql
SELECT d.DeptID, d.DeptName, SUM(e.Salary) AS TotalSalary
FROM DEPARTMENT d
JOIN EMPLOYEE e ON d.DeptID = e.DeptID
GROUP BY d.DeptID, d.DeptName
HAVING SUM(e.Salary) = (
    SELECT MAX(DeptTotal)
    FROM (
        SELECT SUM(Salary) AS DeptTotal
        FROM EMPLOYEE
        GROUP BY DeptID
    ) AS sub
);
```

---

**(e) Find employees who joined in the last 6 months and work on at least 2 projects**

```sql
SELECT e.EmpID, e.Name, e.JoinDate, COUNT(w.ProjectID) AS ProjectCount
FROM EMPLOYEE e
JOIN WORKS_ON w ON e.EmpID = w.EmpID
WHERE e.JoinDate >= DATE_SUB(CURRENT_DATE, INTERVAL 6 MONTH)
GROUP BY e.EmpID, e.Name, e.JoinDate
HAVING COUNT(w.ProjectID) >= 2;
```

**Explanation:** `WHERE` filters by join date first (reduces rows), then `GROUP BY` + `HAVING` filters employees by project count.

---

### Q23 (7 marks) — SQL Queries Set 2

**(a) Find the second highest salary in each department**

```sql
-- Using window function (modern SQL)
SELECT DeptID, Name, Salary
FROM (
    SELECT e.DeptID, e.Name, e.Salary,
           DENSE_RANK() OVER (PARTITION BY e.DeptID ORDER BY e.Salary DESC) AS rnk
    FROM EMPLOYEE e
) ranked
WHERE rnk = 2;
```

**Alternative (without window functions — works in older SQL):**
```sql
SELECT e.DeptID, e.Name, e.Salary
FROM EMPLOYEE e
WHERE e.Salary = (
    SELECT MAX(e2.Salary)
    FROM EMPLOYEE e2
    WHERE e2.DeptID = e.DeptID
    AND e2.Salary < (
        SELECT MAX(e3.Salary)
        FROM EMPLOYEE e3
        WHERE e3.DeptID = e.DeptID
    )
);
```

**Explanation:** `DENSE_RANK()` assigns rank within each department partition. Rank 2 = second highest. `DENSE_RANK` (not `RANK`) handles ties correctly — if two employees share the highest salary, the next one still gets rank 2.

---

**(b) List projects where total hours exceed 500**

```sql
SELECT p.ProjectID, p.ProjectName, SUM(w.Hours) AS TotalHours
FROM PROJECT p
JOIN WORKS_ON w ON p.ProjectID = w.ProjectID
GROUP BY p.ProjectID, p.ProjectName
HAVING SUM(w.Hours) > 500;
```

---

**(c) Find employees who don't work on any project**

```sql
-- Method 1: LEFT JOIN + NULL check (most efficient)
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
LEFT JOIN WORKS_ON w ON e.EmpID = w.EmpID
WHERE w.EmpID IS NULL;

-- Method 2: NOT EXISTS
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
WHERE NOT EXISTS (
    SELECT 1 FROM WORKS_ON w WHERE w.EmpID = e.EmpID
);

-- Method 3: NOT IN
SELECT EmpID, Name
FROM EMPLOYEE
WHERE EmpID NOT IN (SELECT DISTINCT EmpID FROM WORKS_ON);
```

**Comparison:**
- LEFT JOIN + NULL: Usually fastest — single pass, uses index
- NOT EXISTS: Good performance — stops at first match (short-circuit)
- NOT IN: Caution — if subquery returns any NULL, the entire result is empty (SQL three-valued logic trap)

---

### Q24 (10 marks) — SQL Queries Set 3

**(a) For each department, find the employee with the maximum salary (subquery)**

```sql
SELECT e.DeptID, d.DeptName, e.Name, e.Salary
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
WHERE e.Salary = (
    SELECT MAX(e2.Salary)
    FROM EMPLOYEE e2
    WHERE e2.DeptID = e.DeptID
);
```

**Explanation:** Correlated subquery — for each employee, the inner query finds the max salary in THAT department. If the employee's salary matches, they're included. Handles ties (multiple employees with same max salary both appear).

---

**(b) Find departments where ALL employees earn above 30000**

```sql
-- Method 1: NOT EXISTS with negation
SELECT d.DeptID, d.DeptName
FROM DEPARTMENT d
WHERE NOT EXISTS (
    SELECT 1
    FROM EMPLOYEE e
    WHERE e.DeptID = d.DeptID AND e.Salary <= 30000
);

-- Method 2: Using MIN
SELECT d.DeptID, d.DeptName
FROM DEPARTMENT d
JOIN EMPLOYEE e ON d.DeptID = e.DeptID
GROUP BY d.DeptID, d.DeptName
HAVING MIN(e.Salary) > 30000;
```

**Explanation:** "ALL employees earn above 30000" is equivalent to "NO employee earns 30000 or below". Method 2 uses the fact that if the minimum salary > 30000, then all salaries > 30000.

---

**(c) Find pairs of employees who work on the same project**

```sql
SELECT DISTINCT w1.EmpID AS Emp1, e1.Name AS Name1,
                w2.EmpID AS Emp2, e2.Name AS Name2,
                w1.ProjectID
FROM WORKS_ON w1
JOIN WORKS_ON w2 ON w1.ProjectID = w2.ProjectID AND w1.EmpID < w2.EmpID
JOIN EMPLOYEE e1 ON w1.EmpID = e1.EmpID
JOIN EMPLOYEE e2 ON w2.EmpID = e2.EmpID;
```

**Explanation:** Self-join on WORKS_ON with `w1.EmpID < w2.EmpID` ensures each pair appears only once (avoids duplicates like (A,B) and (B,A), and avoids self-pairs like (A,A)).

---

**(d) Calculate running total of salaries ordered by join date**

```sql
-- Window function approach
SELECT EmpID, Name, Salary, JoinDate,
       SUM(Salary) OVER (ORDER BY JoinDate 
                         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) 
       AS RunningTotal
FROM EMPLOYEE
ORDER BY JoinDate;
```

**Alternative (correlated subquery for older SQL):**
```sql
SELECT e1.EmpID, e1.Name, e1.Salary, e1.JoinDate,
       (SELECT SUM(e2.Salary)
        FROM EMPLOYEE e2
        WHERE e2.JoinDate <= e1.JoinDate) AS RunningTotal
FROM EMPLOYEE e1
ORDER BY e1.JoinDate;
```

**Explanation:** The window function `SUM() OVER (ORDER BY JoinDate ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)` computes a cumulative sum. For each row, it sums all salaries from the first row (by JoinDate) up to the current row.

---

### Q25 (7 marks) — SQL JOIN Types & Comparisons

**(a) INNER JOIN, LEFT JOIN, and SELF JOIN examples**

**INNER JOIN — Employees with their department names:**
```sql
SELECT e.EmpID, e.Name, d.DeptName
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
-- Only returns employees that HAVE a department (no NULLs)
```

**LEFT JOIN — All employees, including those without a department:**
```sql
SELECT e.EmpID, e.Name, d.DeptName
FROM EMPLOYEE e
LEFT JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
-- Returns ALL employees; DeptName = NULL for employees with no department
```

**SELF JOIN — Employee with their manager's name:**
```sql
SELECT e.EmpID, e.Name AS Employee, m.Name AS Manager
FROM EMPLOYEE e
LEFT JOIN EMPLOYEE m ON e.ManagerID = m.EmpID;
-- LEFT JOIN because the CEO has no manager (ManagerID = NULL)
```

**Summary:**

| JOIN Type | Returns | NULL handling |
|-----------|---------|---------------|
| INNER JOIN | Only matching rows from both tables | No NULLs from join |
| LEFT JOIN | All rows from left table + matching from right | NULL for non-matching right side |
| RIGHT JOIN | All rows from right table + matching from left | NULL for non-matching left side |
| FULL OUTER JOIN | All rows from both tables | NULLs on both sides for non-matches |
| SELF JOIN | Table joined to itself | Depends on INNER/LEFT used |

---

**(b) EXISTS vs IN — equivalent queries and performance**

**Find employees who work on at least one project:**

```sql
-- Using IN
SELECT EmpID, Name
FROM EMPLOYEE
WHERE EmpID IN (SELECT EmpID FROM WORKS_ON);

-- Using EXISTS (equivalent)
SELECT e.EmpID, e.Name
FROM EMPLOYEE e
WHERE EXISTS (SELECT 1 FROM WORKS_ON w WHERE w.EmpID = e.EmpID);
```

**Performance comparison:**

| Aspect | IN | EXISTS |
|--------|------|--------|
| **Mechanism** | Evaluates subquery once, creates a list, checks membership | For each outer row, checks if subquery returns any row |
| **Best when** | Subquery returns small result set | Subquery returns large result set |
| **NULL handling** | `NOT IN` fails with NULLs in subquery! | `NOT EXISTS` handles NULLs correctly |
| **Short-circuit** | No — evaluates full subquery | Yes — stops at first match |
| **Correlated** | Typically non-correlated | Typically correlated |

**Rule of thumb:**
- **Small subquery result → IN** is fine (and often more readable)
- **Large subquery result → EXISTS** is faster (short-circuits)
- **NOT IN with possible NULLs → Always use NOT EXISTS** (avoids the NULL trap)

---

**(c) GROUP BY with HAVING — departments where average hours > 40**

```sql
SELECT d.DeptID, d.DeptName, AVG(w.Hours) AS AvgHours
FROM DEPARTMENT d
JOIN EMPLOYEE e ON d.DeptID = e.DeptID
JOIN WORKS_ON w ON e.EmpID = w.EmpID
GROUP BY d.DeptID, d.DeptName
HAVING AVG(w.Hours) > 40;
```

**Execution order:**
1. `FROM` + `JOIN` — combine tables
2. `WHERE` — filter individual rows (none here)
3. `GROUP BY` — group rows by department
4. `HAVING` — filter groups (avg hours > 40)
5. `SELECT` — compute output columns
6. `ORDER BY` — sort (not used here)

---

### Q26 (10 marks) — Complex SQL Queries

**(a) Find employees who work on projects in departments other than their own**

```sql
SELECT DISTINCT e.EmpID, e.Name, e.DeptID AS EmpDept, 
                p.ProjectName, p.DeptID AS ProjectDept
FROM EMPLOYEE e
JOIN WORKS_ON w ON e.EmpID = w.EmpID
JOIN PROJECT p ON w.ProjectID = p.ProjectID
WHERE e.DeptID <> p.DeptID;
```

**Explanation:** Join EMPLOYEE → WORKS_ON → PROJECT, then filter where the employee's department differs from the project's department. `DISTINCT` avoids duplicates if an employee works on multiple projects in the same foreign department.

---

**(b) Rank employees by salary within each department**

**Method 1: Window function (modern SQL)**
```sql
SELECT EmpID, Name, DeptID, Salary,
       RANK() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS SalaryRank
FROM EMPLOYEE
ORDER BY DeptID, SalaryRank;
```

**Method 2: Correlated subquery (works in all SQL versions)**
```sql
SELECT e1.EmpID, e1.Name, e1.DeptID, e1.Salary,
       (SELECT COUNT(DISTINCT e2.Salary) 
        FROM EMPLOYEE e2 
        WHERE e2.DeptID = e1.DeptID AND e2.Salary > e1.Salary) + 1 AS SalaryRank
FROM EMPLOYEE e1
ORDER BY e1.DeptID, SalaryRank;
```

**RANK vs DENSE_RANK vs ROW_NUMBER:**
| Function | Salaries: 80K, 70K, 70K, 60K | Behavior |
|----------|-------------------------------|----------|
| RANK() | 1, 2, 2, 4 | Ties get same rank, next rank skipped |
| DENSE_RANK() | 1, 2, 2, 3 | Ties get same rank, next rank NOT skipped |
| ROW_NUMBER() | 1, 2, 3, 4 | No ties — arbitrary order for equal values |

---

**(c) Find the department with the most diverse project portfolio (most unique projects)**

```sql
SELECT d.DeptID, d.DeptName, COUNT(p.ProjectID) AS ProjectCount
FROM DEPARTMENT d
JOIN PROJECT p ON d.DeptID = p.DeptID
GROUP BY d.DeptID, d.DeptName
ORDER BY ProjectCount DESC
LIMIT 1;
```

**Alternative handling ties (all departments with max count):**
```sql
SELECT d.DeptID, d.DeptName, COUNT(p.ProjectID) AS ProjectCount
FROM DEPARTMENT d
JOIN PROJECT p ON d.DeptID = p.DeptID
GROUP BY d.DeptID, d.DeptName
HAVING COUNT(p.ProjectID) = (
    SELECT MAX(cnt)
    FROM (
        SELECT COUNT(ProjectID) AS cnt
        FROM PROJECT
        GROUP BY DeptID
    ) AS sub
);
```

**Explanation:** Count projects per department, then find the department(s) with the maximum count. The second query handles ties — if two departments both have 10 projects, both appear.

---
---

## PART 6: Distributed DB — Placing DB Across a Cluster (Justify)

---

### Q27 — Indian E-Commerce: Database Distribution Across 3 Cities

**Question:** An Indian e-commerce company (like Flipkart) has customers across India. They want to distribute their database across 3 cities: Mumbai, Delhi, Bangalore. Design the fragmentation and allocation strategy for CUSTOMER and ORDER tables. Justify.

**Answer:**

#### Step 1: Fragmentation Strategy

**CUSTOMER Table — Horizontal Fragmentation by Region:**

```
CUSTOMER_WEST   = σ(region = 'West')  (CUSTOMER)    → Mumbai
CUSTOMER_NORTH  = σ(region = 'North') (CUSTOMER)    → Delhi
CUSTOMER_SOUTH  = σ(region = 'South') (CUSTOMER)    → Bangalore
```

| Fragment | Site | Includes states |
|----------|------|----------------|
| CUSTOMER_WEST | Mumbai | Maharashtra, Gujarat, Rajasthan, Goa |
| CUSTOMER_NORTH | Delhi | Delhi, UP, Haryana, Punjab, MP |
| CUSTOMER_SOUTH | Bangalore | Karnataka, Tamil Nadu, Kerala, AP, Telangana |

**Justification:**
- **Data locality:** Customers mostly browse/order from their region → queries hit local site
- **Reduced latency:** A Mumbai customer's queries don't travel to Bangalore
- **Completeness:** CUSTOMER_WEST ∪ CUSTOMER_NORTH ∪ CUSTOMER_SOUTH = CUSTOMER (no data lost)
- **Disjointness:** Each customer belongs to exactly one region (no overlap)

**ORDER Table — Horizontal Fragmentation by customer region:**

```
ORDER_WEST   = ORDER ⋈ CUSTOMER_WEST   → Mumbai
ORDER_NORTH  = ORDER ⋈ CUSTOMER_NORTH  → Delhi
ORDER_SOUTH  = ORDER ⋈ CUSTOMER_SOUTH  → Bangalore
```

Orders are co-located with their customers — a JOIN between CUSTOMER and ORDER is a local operation (no network transfer needed).

#### Step 2: Allocation Strategy

**PRODUCT Table — Full Replication across all 3 sites:**

| Site | CUSTOMER | ORDER | PRODUCT |
|------|----------|-------|---------|
| Mumbai | CUSTOMER_WEST | ORDER_WEST | PRODUCT (full copy) |
| Delhi | CUSTOMER_NORTH | ORDER_NORTH | PRODUCT (full copy) |
| Bangalore | CUSTOMER_SOUTH | ORDER_SOUTH | PRODUCT (full copy) |

**Justification for PRODUCT replication:**
- Product catalog is **read-heavy** (millions of reads/day) vs **write-rare** (products added/updated occasionally)
- Every site needs product data for browsing/searching
- Replication cost: 3× storage for PRODUCT table (acceptable — catalog is not huge)
- Write cost: Product updates must propagate to 3 sites (acceptable — updates are infrequent)

#### Step 3: Trade-off Analysis

| Factor | Decision | Justification |
|--------|----------|---------------|
| **Read locality** | Horizontal fragmentation by region | 80% of reads are local (customer browsing their own orders) |
| **Cross-region orders** | Rare (< 5%) — handle via distributed query | A Delhi customer ordering from a Mumbai seller: query crosses sites but is rare |
| **Availability** | Partial replication of CUSTOMER to nearest neighbour | If Mumbai goes down, CUSTOMER_WEST is available at Delhi (backup) |
| **Consistency** | Eventual consistency for PRODUCT, strong consistency for ORDER | Product price changes can propagate asynchronously; order placement needs ACID |

---

### Q28 — Global Banking: India, US, UK

**Question:** A global banking system operates in India, US, and UK. How would you distribute the ACCOUNTS and TRANSACTIONS tables? Consider data sovereignty, latency, consistency.

**Answer:**

#### Step 1: Fragmentation — Horizontal by Country (Mandatory)

```
ACCOUNTS_INDIA = σ(country = 'India') (ACCOUNTS)  → Mumbai DC
ACCOUNTS_US    = σ(country = 'US')    (ACCOUNTS)  → New York DC
ACCOUNTS_UK    = σ(country = 'UK')    (ACCOUNTS)  → London DC

TRANSACTIONS_INDIA = TRANSACTIONS ⋈ ACCOUNTS_INDIA → Mumbai DC
TRANSACTIONS_US    = TRANSACTIONS ⋈ ACCOUNTS_US    → New York DC
TRANSACTIONS_UK    = TRANSACTIONS ⋈ ACCOUNTS_UK    → London DC
```

**Why country-based fragmentation is MANDATORY (not optional):**

| Regulation | Requirement |
|-----------|-------------|
| **India RBI** | Financial data of Indian customers must be stored on servers in India |
| **EU GDPR** | Personal data of EU citizens must be processed within EU-compliant jurisdictions |
| **US SOX** | Financial records must be accessible for US auditors on US systems |

Data sovereignty laws **legally require** that financial data stays in the customer's country. This is not a performance optimization — it's a legal requirement.

#### Step 2: Replication Strategy

**Within a country — Synchronous Replication:**
```
Mumbai DC ←── synchronous ──→ Pune DR (Disaster Recovery)
New York DC ←── synchronous ──→ Chicago DR
London DC ←── synchronous ──→ Dublin DR
```

**Why synchronous?** Banking needs ACID. If Mumbai DC crashes, Pune DR must have identical data — zero data loss (RPO = 0).

**Across countries — Asynchronous Replication (Read-Only Replicas):**
```
Mumbai DC ──── async ────→ London (read-only replica for analytics)
New York DC ── async ────→ Mumbai (read-only replica for global reports)
```

**Why async?** Cross-country replication over WAN is slow (100-200ms latency). Synchronous replication across continents would make every transaction wait for cross-Atlantic confirmation — unacceptable for user experience. Read-only replicas are sufficient for analytics/reporting.

#### Step 3: Cross-Border Transfers (the hard problem)

**Scenario:** An Indian customer sends ₹10,00,000 to a UK account.

```
Step 1: Debit ACCOUNTS_INDIA (Mumbai DC)
Step 2: Credit ACCOUNTS_UK (London DC)
Both must succeed or both must fail → Need Distributed Transaction
```

**Solution: Two-Phase Commit (2PC)**

```
         ┌───────────────┐
         │  Coordinator   │
         │  (Mumbai DC)   │
         └──────┬────────┘
                │
    Phase 1:    │   PREPARE
    ┌───────────┼───────────┐
    ▼           ▼           
  Mumbai      London        
  (VOTE YES)  (VOTE YES)    
    │           │           
    Phase 2:    │   COMMIT
    ┌───────────┼───────────┐
    ▼           ▼           
  Mumbai      London        
  (COMMIT)    (COMMIT)      
```

**Phase 1 (Prepare):** Coordinator asks both sites: "Can you commit?" Both vote YES/NO.
**Phase 2 (Commit):** If both vote YES → COMMIT at both sites. If any votes NO → ABORT at both.

#### Step 4: CAP Theorem Analysis

**Banking chooses CP (Consistency + Partition-tolerance, sacrifice Availability):**

| CAP Property | Banking Decision | Reason |
|-------------|-----------------|--------|
| **Consistency** | ✅ Must have | Account balance must be accurate at all times |
| **Partition Tolerance** | ✅ Must have | Network between India-UK can fail |
| **Availability** | ⚠️ Sacrificed if needed | If India-UK link is down, cross-border transfers are blocked (better than wrong balance) |

During a network partition: domestic transactions continue (local ACID), cross-border transfers queue until partition heals. This is acceptable — a customer can wait a few minutes for an international transfer but cannot tolerate a wrong balance.

---

### Q29 — Food Delivery App (50 Indian Cities)

**Question:** Design a distributed database for a food delivery app (like Swiggy) operating in 50 Indian cities. Tables: USERS, RESTAURANTS, ORDERS, DRIVERS. What fragmentation? What replication? Justify.

**Answer:**

#### Key Observation: Food delivery is inherently LOCAL

A user in Bangalore orders from Bangalore restaurants, delivered by Bangalore drivers. Cross-city queries are virtually non-existent (< 0.1%). This makes horizontal fragmentation by city the natural choice.

#### Step 1: Fragmentation Strategy

**All four tables — Horizontal Fragmentation by City:**

```
USERS_BLR       = σ(city = 'Bangalore') (USERS)
RESTAURANTS_BLR = σ(city = 'Bangalore') (RESTAURANTS)
ORDERS_BLR      = σ(city = 'Bangalore') (ORDERS)
DRIVERS_BLR     = σ(city = 'Bangalore') (DRIVERS)

(Same for all 50 cities)
```

**Justification:**
- A Bangalore user searches Bangalore restaurants, orders are local, delivered by local drivers
- All four tables JOIN within the same city — 100% local queries
- No cross-city JOINs needed in normal operations

#### Step 2: Cluster Design (not 50 separate servers)

Group 50 cities into **regional clusters** to avoid managing 50 individual databases:

| Cluster | Data Center | Cities (fragments) |
|---------|------------|-------------------|
| **South** | Bangalore DC | Bangalore, Chennai, Hyderabad, Kochi, Coimbatore, Mysuru, ... (15 cities) |
| **West** | Mumbai DC | Mumbai, Pune, Ahmedabad, Jaipur, Surat, ... (15 cities) |
| **North** | Delhi DC | Delhi, Noida, Gurugram, Lucknow, Chandigarh, ... (12 cities) |
| **East** | Kolkata DC | Kolkata, Bhubaneswar, Patna, Guwahati, ... (8 cities) |

Within each cluster, data is partitioned by city (hash or range partitioning on city_id).

#### Step 3: Replication Strategy

| Table | Replication | Justification |
|-------|------------|---------------|
| **USERS** | No cross-region replication | Users order only in their city; no need to replicate Delhi users to Bangalore |
| **RESTAURANTS** | No cross-region replication | Same reason — restaurants serve only their city |
| **ORDERS** | Within-region replication (2 copies) | For high availability — if one node fails, orders are still accessible |
| **DRIVERS** | No cross-region replication | Drivers operate in one city |
| **MENU (cache)** | Redis cache per city cluster | Menus are read-heavy; cache locally for low latency |
| **ANALYTICS (read replica)** | Aggregate to central analytics DB | For company-wide reports, ETL from all cities to a central data warehouse |

#### Step 4: Handling Specific Challenges

**Challenge 1: User travels to another city**
- User profile is in home city (Bangalore)
- User opens app in Mumbai
- Solution: Lookup user by ID from home cluster (cross-region read — rare, acceptable latency)
- Alternative: Replicate user auth data (user_id, token) to all clusters; full profile fetched on demand

**Challenge 2: Peak hour scalability (8-9 PM dinner rush)**
- 50 cities have staggered peaks (different time zones are minimal in India, but order patterns vary)
- Horizontal scaling: Add more read replicas per city cluster during peak
- Redis caching: Restaurant menus, driver locations served from cache

**Challenge 3: Consistency requirements**

| Operation | Consistency | Reason |
|-----------|------------|--------|
| Place order | **Strong** (ACID) | Must debit wallet, confirm restaurant, assign driver atomically |
| Driver location update | **Eventual** | GPS updates every 5 seconds; slight lag is fine |
| Restaurant menu browse | **Eventual** | Menu cached in Redis; stale by seconds is acceptable |
| Order status tracking | **Read-your-writes** | After placing order, user must see their own order immediately |

#### Architecture Diagram

```
User in Bangalore                    User in Delhi
       │                                    │
       ▼                                    ▼
  ┌─────────────┐                    ┌─────────────┐
  │  South DC   │                    │  North DC   │
  │ (Bangalore) │                    │  (Delhi)    │
  │             │                    │             │
  │ Redis Cache │                    │ Redis Cache │
  │ App Servers │                    │ App Servers │
  │ DB: BLR,CHN │                    │ DB: DEL,NOI │
  │   HYD,KOC   │                    │   GUR,LKO   │
  └──────┬──────┘                    └──────┬──────┘
         │          ┌───────────┐           │
         └──────── │ Analytics  │ ──────────┘
                    │   (Central │
                    │    DW)     │
                    └───────────┘
```

---

### Q30 — Replication Strategies Comparison (with Quorum Protocol)

**Question:** Compare these approaches for distributing a social media database:
(a) No replication (each fragment at one site)
(b) Partial replication
(c) Full replication
When would you choose each? Use the Quorum protocol (W+R>N) to explain consistency trade-offs.

**Answer:**

#### The Three Replication Strategies

**(a) No Replication — Each fragment at exactly one site**

```
Site 1: Fragment A
Site 2: Fragment B
Site 3: Fragment C
(N = 1 for each fragment)
```

| Aspect | Assessment |
|--------|-----------|
| **Storage cost** | ✅ Lowest — each data item stored once |
| **Write performance** | ✅ Best — write to one site, no propagation |
| **Read performance** | ⚠️ Variable — local reads fast, remote reads slow |
| **Availability** | ❌ Worst — if Site 1 fails, Fragment A is completely unavailable |
| **Consistency** | ✅ Trivial — only one copy, no conflicts |

**When to choose:** Development/testing environments. Data that is rarely accessed and has no availability requirement. Cost-constrained scenarios where data loss is acceptable.

---

**(b) Partial Replication — Each fragment at some (not all) sites**

```
Site 1: Fragment A, Fragment B (replica)
Site 2: Fragment B, Fragment C (replica)
Site 3: Fragment C, Fragment A (replica)
(N = 2 for each fragment)
```

| Aspect | Assessment |
|--------|-----------|
| **Storage cost** | ⚠️ Moderate — 2× storage (each fragment at 2 sites) |
| **Write performance** | ⚠️ Moderate — must write to 2 sites per fragment |
| **Read performance** | ✅ Good — can read from nearest replica |
| **Availability** | ✅ Good — survives 1 site failure per fragment |
| **Consistency** | ⚠️ Requires protocol (Quorum) |

**When to choose:** Most production systems. Balances availability, performance, and cost. Good for geographically distributed systems where each region needs access to some (not all) data.

---

**(c) Full Replication — Every fragment at every site**

```
Site 1: Fragment A, Fragment B, Fragment C
Site 2: Fragment A, Fragment B, Fragment C
Site 3: Fragment A, Fragment B, Fragment C
(N = 3 for each fragment)
```

| Aspect | Assessment |
|--------|-----------|
| **Storage cost** | ❌ Highest — 3× storage |
| **Write performance** | ❌ Worst — must propagate to all 3 sites |
| **Read performance** | ✅ Best — always read from local site |
| **Availability** | ✅ Best — survives N-1 site failures |
| **Consistency** | ⚠️ Hardest to maintain — more replicas = more conflict potential |

**When to choose:** Read-heavy data that rarely changes (product catalogs, configuration data, reference tables). Small tables where 3× storage is negligible.

---

#### Quorum Protocol: W + R > N

The Quorum protocol ensures consistency in replicated systems by requiring enough nodes to participate in reads and writes.

**Variables:**
- **N** = total number of replicas
- **W** = number of nodes that must acknowledge a WRITE
- **R** = number of nodes that must respond to a READ
- **Rule: W + R > N** → guarantees at least one node in any read set has the latest write

**Example with N = 3 (partial replication across 3 nodes):**

| Configuration | W | R | W+R | Consistent? | Character |
|--------------|---|---|-----|-------------|-----------|
| Strong consistency | 2 | 2 | 4 > 3 ✅ | Yes | Both reads and writes involve majority |
| Read-optimised | 3 | 1 | 4 > 3 ✅ | Yes | Reads are fast (1 node), writes are slow (all 3 nodes) |
| Write-optimised | 1 | 3 | 4 > 3 ✅ | Yes | Writes are fast (1 node), reads must check all 3 |
| Eventual consistency | 1 | 1 | 2 ≤ 3 ❌ | No | Fast but may read stale data |

**Visual: Why W + R > N works**

```
N = 3 nodes: [Node1] [Node2] [Node3]

Write (W=2): Write goes to Node1 ✓ and Node2 ✓
             Node3 has stale data ✗

Read  (R=2): Read from Node2 ✓ and Node3 ✗
             At least one node (Node2) has latest data!
             W + R = 2 + 2 = 4 > 3 = N ✅

If W=1, R=1: Write to Node1 only, Read from Node3 only
             Read misses the update! W + R = 2 ≤ 3 = N ❌
```

---

#### Applying Quorum to Social Media

**User posts (write-heavy, eventual consistency acceptable):**
- N = 3, W = 1, R = 1 (fast writes, fast reads, eventual consistency)
- A user posts a photo → written to 1 node, asynchronously replicated
- A follower might see the post 1-2 seconds late — acceptable

**User account/auth data (strong consistency required):**
- N = 3, W = 2, R = 2 (strong consistency via quorum)
- Password change must be immediately visible everywhere
- W + R = 4 > 3 → guaranteed to read latest data

**Like counts (read-heavy, approximate is fine):**
- N = 3, W = 1, R = 1 (eventual consistency)
- Showing "1.2K likes" vs "1.3K likes" for a brief moment is acceptable
- Optimised for speed, not precision

**Direct messages (strong consistency):**
- N = 3, W = 2, R = 2 (quorum)
- Messages must appear in order and not be lost
- W + R > N ensures every read sees the latest message

---

#### Summary: When to Choose Each Strategy

| Strategy | Best For | Example |
|----------|---------|---------|
| **No replication** | Development, disposable data | Test environments, temp data |
| **Partial replication** | Production systems balancing cost/availability | E-commerce orders, user profiles |
| **Full replication** | Read-heavy, rarely-changing data | Product catalogs, config, reference tables |

| Quorum Setting | Best For | Trade-off |
|---------------|---------|-----------|
| W=1, R=1 | Social feeds, likes, views | Fastest, but stale reads possible |
| W=2, R=2 (N=3) | Auth, payments, messages | Consistent, but slower |
| W=3, R=1 (N=3) | Read-heavy + consistent | Fast reads, but writes are slow |
| W=1, R=3 (N=3) | Write-heavy + consistent | Fast writes, but reads are slow |

---
---

## Quick Revision: Exam Answering Tips

| Question Type | Key Points to Include for Full Marks |
|--------------|--------------------------------------|
| **MongoDB/Redis** | Correct syntax + explanation of operators used + sample output |
| **SQL vs NoSQL** | Name specific DB + 3-4 justification points + mention ACID/CAP + address scale |
| **Primary Key** | Check: unique, minimal, immutable, compact, no business meaning, no privacy issue |
| **Normalisation** | Show FDs explicitly → state which NF is violated and why → decompose step by step → verify |
| **SQL Queries** | Use correct JOIN type + handle NULLs + show alternatives (subquery vs JOIN) + explain |
| **Distributed DB** | State fragmentation type + allocation table + justify with locality/latency/availability + mention CAP |

---

*End of Practice Paper*
