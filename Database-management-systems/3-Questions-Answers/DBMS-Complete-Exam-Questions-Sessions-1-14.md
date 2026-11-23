# Database Management Systems - Complete Exam Questions
## SSZG507 - Sessions 1-14 Practical Scenario-Based Questions
## 30+ Questions for Complete Exam Preparation

---

## **SECTION A: SESSIONS 1-7 (Foundations to Administration)**

### **Q1. University Database Design & Normalization (8 marks)**

**Scenario:**
A university stores student information: StudentID, Name, Address, Phone, DepartmentID, DepartmentName, CourseID, CourseName, Grade, Semester.

Currently one table with repeating course information. Poor query performance. Data anomalies when departments change name.

**Problems:**
- Insert anomaly: Can't add new department without student
- Update anomaly: Changing "CS Department" name requires updating 10K rows
- Delete anomaly: Removing last student deletes department info
- Redundant storage: "CS Department" repeated 5000 times

**Questions:**

(a) **Identify all anomalies. How do they affect operations?** (2 marks)

(b) **Design ER model with proper entities and relationships.** (2 marks)

(c) **Normalize to BCNF. Show decomposition steps (1NF → 2NF → 3NF → BCNF).** (2 marks)

(d) **Write SQL CREATE TABLE statements for final schema.** (2 marks)

---

### **Q2. Transaction ACID Properties Under Failure (8 marks)**

**Scenario:**
Bank transfer: Account A (balance $1000) → Account B (balance $500).
Transfer $200. Process:
1. Read A balance: $1000
2. Deduct from A: $800
3. **System CRASHES here**
4. Add to B: $700
5. Write A: $800, Write B: $700

**After crash:**
- Account A: $800 (money disappeared!)
- Account B: $500 (didn't receive)
- Total money lost: $200

**Questions:**

(a) **Which ACID property violated? Explain impact.** (2 marks)

(b) **How would proper transaction handling prevent this?** (2 marks)

(c) **Implement using BEGIN/COMMIT/ROLLBACK. Show code.** (2 marks)

(d) **What if secondary crashes during commit? Design recovery mechanism.** (2 marks)

---

### **Q3. SQL Optimization - Join Performance Crisis (7 marks)**

**Scenario:**
Report: "Show all customers with orders > $1000, their products, and categories"

Query takes 45 seconds. Deadline: Must run in <2 seconds for dashboard.

```sql
SELECT c.name, o.order_id, p.product_name, cat.category_name
FROM customers c, orders o, order_items oi, products p, categories cat
WHERE c.customer_id = o.customer_id
  AND o.order_id = oi.order_id
  AND oi.product_id = p.product_id
  AND p.category_id = cat.category_id
  AND o.total_amount > 1000;
```

**Data:**
- 1M customers, 10M orders, 50M order_items, 100K products, 50 categories

**Questions:**

(a) **Why so slow? Analyze without indexes.** (2 marks)

(b) **What indexes to create? Show CREATE INDEX statements.** (2 marks)

(c) **Rewrite query optimized. Explain improvements.** (2 marks)

(d) **Alternative: Use materialized view. When beneficial?** (1 mark)

---

### **Q4. Distributed Transaction - 2PC Failure Scenarios (8 marks)**

**Scenario:**
Airline reservation system: Flight booked in US database, Payment processed in India database.
Two-Phase Commit protocol:
1. Prepare phase: Both databases vote YES
2. Commit phase: Both commit changes

**Scenario 1:** After US votes YES, coordinator crashes before sending COMMIT
**Scenario 2:** US commits, but India network fails before commit
**Scenario 3:** Both database crash during commit phase

**Questions:**

(a) **For each scenario, what goes wrong? Explain.** (3 marks)

(b) **How 2PC handles these using write-ahead logs (WAL).** (2 marks)

(c) **Design recovery mechanism for Scenario 3.** (2 marks)

(d) **Why not use eventual consistency instead? Trade-offs?** (1 mark)

---

### **Q5. Replication Consistency vs Performance (7 marks)**

**Scenario:**
E-commerce: Primary DB in US, Replicas in Europe, Asia for read-scaling.

Customer A: Buys laptop in US (primary updated instantly)
Milliseconds later: Checks inventory from Asia replica (shows OLD stock)
Thinks item still available, but stock was just sold.

**Current:** Asynchronous replication (100ms lag)
**Cost of sync replication:** 10x slower writes

**Questions:**

(a) **Analyze: Synchronous vs asynchronous trade-offs.** (2 marks)

(b) **Design solution: Hybrid approach minimizing both lag and latency.** (2 marks)

(c) **Implement read-your-writes consistency. Show logic.** (2 marks)

(d) **Monitor replication lag. What threshold to alert?** (1 mark)

---

### **Q6. Backup & Recovery Strategy (7 marks)**

**Scenario:**
Database: 500GB, 10K transactions/sec, RPO=1 hour (acceptable data loss), RTO=15 min (must recover in 15 min)

Current backup: Full backup weekly (takes 8 hours)

**Failure scenarios:**
1. Disk corruption (recoverable)
2. Accidental DROP TABLE (need to recover from yesterday)
3. Data center destroyed (need site elsewhere)

**Questions:**

(a) **Design backup strategy meeting RPO/RTO. Full vs incremental trade-offs.** (2 marks)

(b) **For DROP TABLE at 2 PM, recover data from noon backup. Show procedure.** (2 marks)

(c) **Implement point-in-time recovery using transaction logs.** (2 marks)

(d) **Multi-site disaster recovery. Show architecture.** (1 mark)

---

### **Q7. Database Indexing Strategy (6 marks)**

**Scenario:**
Customer table (10M rows): customer_id, name, email, phone, country, registration_date, last_login_date

Common queries:
- Q1: Find by customer_id (exact match)
- Q2: Find by email (exact match, used for login)
- Q3: Find customers in "USA" registered in "2024"
- Q4: Find by phone (prefix search: 555-*)

**Without indexes:** Each query scans 10M rows = slow

**Questions:**

(a) **Which columns to index? Justify each.** (2 marks)

(b) **For Q3, design composite index. Order matters?** (2 marks)

(c) **For Q4 (prefix search), what index type needed?** (1 mark)

(d) **Trade-off: Too many indexes causes problems. What?** (1 mark)

---

## **SECTION B: SESSIONS 8-10 (Search, Cloud, Warehousing)**

### **Q8. Full-Text Search Relevance Problem (8 marks)**

**Scenario:**
Book store with 1M books. Search for "best Python book":
- Returns 100K results
- Top results: "Python for Dummies" (5 stars, 10K reviews) ranked #1
- Missing: "Expert Python Programming" (5 stars, 100 reviews)

Problem: Common words ("best", "book", "Python") dominate ranking.

**Questions:**

(a) **Why simple frequency ranking fails. Use math examples.** (2 marks)

(b) **Apply BM25: Given df(best)=800K, df(Python)=50K, df(book)=200K out of 1M docs, calculate IDF for each.** (2 marks)

(c) **Document "Expert Python Programming" is 200 words (avg=300), "Python for Dummies" is 400 words. How BM25 handles length difference?** (2 marks)

(d) **Implement boosting: Increase weight for 5-star books with 50+ reviews. Show logic.** (2 marks)

---

### **Q9. Elasticsearch Cluster Disaster (8 marks)**

**Scenario:**
5-node Elasticsearch cluster (10 shards, 1 replica each) crashed in production:

- 100M documents across 10 shards
- Peak queries: 10K/sec
- Current: Response time 200ms
- After crash: Can only use 3 nodes initially

**Problems:**
- Remaining shards undersized (data unbalanced)
- Replica shards unavailable (data loss risk)
- Query latency spiked to 5 seconds
- Rebalancing taking 6 hours

**Questions:**

(a) **Analyze: Why performance degraded? Data distribution affected?** (2 marks)

(b) **Design recovery: Restore from backup, rebalance data, resume operations minimizing downtime.** (2 marks)

(c) **Reconfigure cluster: How many shards/replicas for 3-node? vs 5-node?** (2 marks)

(d) **Prevent future: Design monitoring/alerting strategy.** (2 marks)

---

### **Q10. Polyglot Architecture for SaaS Platform (8 marks)**

**Scenario:**
SaaS analytics platform serving 10K customers:

**Requirements:**
- Real-time dashboards (100K concurrent)
- Complex transactions: User signup, billing
- Full-text search: Logs, audit trails
- Time-series data: 1B metrics/day
- Geographic distribution: 50ms latency requirement globally

**Current:** PostgreSQL for everything = slow and can't scale

**Questions:**

(a) **Choose appropriate databases for each requirement. Justify 5 selections.** (3 marks)

(b) **Design architecture showing data flow. How sync between systems?** (2 marks)

(c) **Implement consistency: Billing transaction must succeed everywhere. Design mechanism.** (2 marks)

(d) **Cost analysis: Total TCO vs single PostgreSQL.** (1 mark)

---

### **Q11. Cloud Database Migration: On-Prem to AWS (8 marks)**

**Scenario:**
Company currently runs 100GB PostgreSQL on-premise:
- 500 reads/sec, 100 writes/sec
- Uses synchronous replication to standby for HA
- Global users (latency from India to US data center)

**Decision:** Migrate to AWS RDS Multi-AZ (redundancy) with Read Replicas (scaling)

**Migration challenges:**
- Zero downtime required
- Application continuously running
- Database growing 10GB/month

**Questions:**

(a) **Design multi-stage migration: Parallel run, cutover, rollback strategy.** (3 marks)

(b) **Implement using AWS DMS (Database Migration Service). Show configuration.** (2 marks)

(c) **Post-migration, users in Europe complain 300ms latency. Design multi-region: Use Aurora Global Database.** (2 marks)

(d) **Cost comparison: On-prem + self-managed HA vs AWS RDS Multi-AZ + Read Replicas.** (1 mark)

---

### **Q12. Dimensional Modeling for Retail BI (8 marks)**

**Scenario:**
Retail chain with 500 stores. CEO wants dashboard:
- "Show sales by store, product, region, day for last quarter"
- "Compare this quarter vs last year same quarter"
- "Top 100 products by profit"

**Data sources:**
- POS system: 1M transactions/day
- Inventory system: Stock levels
- Supplier system: Cost data
- Customer system: Demographic data

**Questions:**

(a) **Design star schema. Draw ER with Fact table + 5 Dimension tables.** (3 marks)

(b) **Define grain: What's one row in fact table? Transaction vs Daily aggregate?** (2 marks)

(c) **Implement SCD for slowly-changing dimensions: Product category renaming, store manager changes.** (2 marks)

(d) **Write SQL queries for all 3 CEO questions.** (1 mark)

---

### **Q13. ETL Pipeline for Data Integration (8 marks)**

**Scenario:**
Need daily ETL loading 50GB across 10 sources:

**Sources:**
- 3 SQL databases (Oracle, PostgreSQL, MySQL)
- 2 REST APIs (rate-limited to 100 req/min)
- 3 flat files (CSV, JSON, XML)
- 2 streaming sources (continuous, unbounded)

**Challenges:**
- Extracting from API slow (would take 8 hours) 
- Different schemas, data quality issues
- Need incremental (not full) load
- Previous pipeline crashed mid-load, corrupted data

**Questions:**

(a) **Design ETL architecture handling 10 heterogeneous sources.** (2 marks)

(b) **For API source: Rate-limited extraction. How to handle 8-hour requirement?** (2 marks)

(c) **Implement CDC (Change Data Capture) for incremental load from SQL databases.** (2 marks)

(d) **Idempotent design: Safe restart after failure at any stage. Show architecture.** (2 marks)

---

### **Q14. Data Quality & Slowly Changing Dimensions (7 marks)**

**Scenario:**
Insurance company data warehouse tracks customer addresses. Customer 1234:
- Jan 1: Lived in NYC (valid_from=Jan1, valid_to=Dec31)
- Mar 15: Moved to LA (valid_from=Mar16, valid_to=null)

**Problem:**
Quarterly report: "Top customers by state in Q1" - Should NYC customers show under NY or should they...?

**Current SCD Type 1 (overwrite):** Only LA address stored → Report shows under CA (wrong!)

**Better:** Need SCD Type 2 to track both addresses with dates

**Questions:**

(a) **Explain SCD Type 1 failure for this scenario with example.** (2 marks)

(b) **Design SCD Type 2 schema. Show rows and date fields.** (2 marks)

(c) **Write point-in-time query: Show customer state for each day in Q1.** (2 marks)

(d) **Performance trade-off: Type 2 creates more rows. How impacts storage/query speed?** (1 mark)

---

## **SECTION C: SESSIONS 11-14 (Modern Systems)**

### **Q15. Data Lake Implementation for ML (8 marks)**

**Scenario:**
ML team needs 500GB historical data for training recommendation model. Data scattered:
- User clickstream: Kafka (real-time)
- User profile: PostgreSQL (structured)
- Product info: JSON files in S3
- Images: Uncompressed TIFF (billion files)

**Problems:**
- Different formats & schemas
- No lineage tracking
- Data quality unknown
- Governance non-existent

**Questions:**

(a) **Design data lake (bronze/silver/gold zones). What goes where and why?** (2 marks)

(b) **Implement using Delta Lake: ACID transactions, schema enforcement, versioning.** (2 marks)

(c) **Governance: Data catalog, lineage, quality metrics. Show architecture.** (2 marks)

(d) **Handle 1B image files: Partitioning strategy, metadata management, query optimization.** (2 marks)

---

### **Q16. Delta Lake Time-Travel Investigation (7 marks)**

**Scenario:**
Data quality incident: Some metrics in warehouse wrong. Investigation finds:
- Error introduced on Mar 10 at 14:30
- Discovered on Mar 12
- Need to understand what went wrong

**Questions:**

(a) **Use Delta Lake time-travel: Query data as of Mar 10 13:00 (before error).** (1 mark)

(b) **Compare with current state to identify exact changes.** (1 mark)

(c) **Implement versioning: Show how to restore to Mar 10 13:00 state.** (2 marks)

(d) **Forensics: Query change log to see who modified what and when.** (2 marks)

(e) **Prevent future: Implement data quality tests in Delta Lake pipeline.** (1 mark)

---

### **Q17. Vector Embeddings for E-Commerce Search (8 marks)**

**Scenario:**
E-commerce with 10M products. Customer searches:
- "cheap laptop under $500"

**Desired results:**
- Laptops under $500 ✓
- Notebooks under $500 ✓
- Budget computers under $500 ✓

**Current lexical search misses notebooks/budget computers (different words)**

**Solution:** Use semantic embeddings

**Questions:**

(a) **Generate embeddings for 10M products. What features (title, description, price, category)? How combine?** (2 marks)

(b) **Design vector database: Store 10M × 1536-dim vectors. Indexing strategy (HNSW vs IVF)?** (2 marks)

(c) **Hybrid search: Combine lexical ranking with semantic similarity. Rank fusion algorithm?** (2 marks)

(d) **Handle price filter: "< $500" - How enforce exact filter on semantic results?** (2 marks)

---

### **Q18. Similarity Search for Duplicate Detection (7 marks)**

**Scenario:**
Platform has 100M user profiles. Issues:
- Fake accounts creating multiple profiles
- Legitimate users with duplicate profiles (forgot password)

**Solution:** Find similar profiles using embeddings

**Problem:** Exact algorithm for finding k-nearest neighbors to 100M vectors?

**Questions:**

(a) **Brute force (compare all pairs): How many comparisons? Why impractical?** (1 mark)

(b) **ANN algorithms: HNSW vs IVF. Which better for duplicate detection? Why?** (2 marks)

(c) **Implement: Build index, query for similar profiles, threshold similarity score.** (2 marks)

(d) **Performance: Time/space trade-offs. Accuracy vs speed.** (2 marks)

---

### **Q19. Stream Processing - Real-Time Analytics (8 marks)**

**Scenario:**
Stock exchange platform: 50K trades/second globally.

**Requirements:**
- Alert trader if stock price movement >5% in 1 minute
- Track portfolio value in real-time
- Detect unusual patterns (market manipulation)

**Challenges:**
- Real-time (can't batch overnight)
- Stateful (need portfolio state)
- Exactly-once (can't miss/duplicate trades)

**Questions:**

(a) **Batch vs stream? Why batch fails. Design streaming solution using Kafka + Flink.** (2 marks)

(b) **Windowing: Detect >5% move in 1-minute windows. Which window type (tumbling/sliding/session)?** (2 marks)

(c) **Stateful processing: Maintain portfolio value. How handle state on failure?** (2 marks)

(d) **Exactly-once semantics: Ensure each trade counted once despite failures. Implement checkpointing.** (2 marks)

---

### **Q20. Event-Driven Architecture (7 marks)**

**Scenario:**
Order processing system:

Event sequence:
1. Order Created → Stock updated, Notification sent, Analytics recorded
2. Payment Confirmed → Shipment created, Email sent
3. Shipment Created → Tracking updated, Notification sent
4. Delivered → Invoice created, Analytics recorded

**Problem:** If Payment service crashes, rest of pipeline blocks

**Solution:** Kafka-based event streaming with independent consumers

**Questions:**

(a) **Design event-driven architecture showing Kafka producers/consumers.** (2 marks)

(b) **Handle failures: One consumer crashes. How others continue? Dead-letter queues?** (2 marks)

(c) **Idempotence: Shipment service receives "Shipment Created" twice. How prevent duplicate shipments?** (2 marks)

(d) **Exactly-once: Ensure Invoice created exactly once despite failures.** (1 mark)

---

### **Q21. Temporal Database for Audit Trail (8 marks)**

**Scenario:**
Insurance system tracks policy changes. Policy #100:

- 2024-01-01: Premium $500/month (valid from Jan 1)
- 2024-03-15: Moved to LA (insurance rate changes) → Premium $600/month (valid from Mar 16)
- 2024-04-10: Error discovered! Premium should have been $550/month from Mar 16

**Questions:**

(a) **Design bi-temporal schema: Track both business time (valid_from/valid_to) and transaction time (recorded_from/recorded_to).** (3 marks)

(b) **Show rows before/after correction. How to query "What premium active on 2024-03-20?" vs "What did system have recorded on 2024-04-01?"** (2 marks)

(c) **Implement temporal versioning: Point-in-time queries, range queries, history reconstruction.** (2 marks)

(d) **Forensics: Show when correction was made and who made it.** (1 mark)

---

### **Q22. Time-Series Optimization (7 marks)**

**Scenario:**
IoT company: 100K sensors reporting temperature every 10 seconds.
1 year of data = 315 billion data points = 2TB raw storage

**Problem:**
- Storage exploding
- Queries slow ("Get daily average for sensor 1234 for last year")
- Need to keep raw data 1 month, then aggregate

**Questions:**

(a) **Storage design: Raw data (1 month), hourly aggregate (1 year), daily (forever). Partitioning strategy?** (2 marks)

(b) **Downsampling: Compress 1 month raw to daily summaries. What information preserved? Lost?** (2 marks)

(c) **Query optimization: "Average temp for sensor 1234 in January" - Use which tier (raw/hourly/daily)?** (2 marks)

(d) **Implement retention policy: Automatically archive/delete old data.** (1 mark)

---

### **Q23. Spatial Database for Location Services (8 marks)**

**Scenario:**
Delivery company with 50K daily delivery locations. Truck #42 at position (40.7128°N, -74.0060°W).

**Requirements:**
- Find all delivery points within 2km
- Optimize route: Visit 10 nearest points
- Avoid congested zones
- Real-time as truck moves every 10 seconds

**Without indexing:** Every query scans 50K locations (slow)

**Questions:**

(a) **Design PostGIS schema: Tables, geometry types, indexes (R-tree via GIST).** (2 marks)

(b) **Query: "Find all deliveries within 2km of truck." Write SQL using ST_DWithin.** (2 marks)

(c) **Route optimization: "Order 10 nearest deliveries by proximity." Write SQL with geometry.** (2 marks)

(d) **Spatial join: "Which customers in zone5 but assigned to zone3?" Show query.** (2 marks)

---

### **Q24. Multi-Region Spatial-Temporal Query (7 marks)**

**Scenario:**
Ride-sharing: Find eligible drivers for passenger pickup.

**Requirements:**
- Spatial: Driver within 2km of passenger
- Temporal: Driver was in that zone in last 1 hour (proves reliability)
- Recency: Driver data updated within 5 minutes

**Problem:** Combining spatial + temporal constraints

**Questions:**

(a) **Design schema: Driver locations with timestamps, zones as polygons.** (2 marks)

(b) **Query complexity: Spatial join (2km) + temporal filter (last 1 hour) + recency (5 min).** (2 marks)

(c) **Write SQL or pseudocode for the query.** (2 marks)

(d) **Performance: Indexes needed? Query optimization?** (1 mark)

---

### **Q25. CAP Theorem Trade-offs in Practice (7 marks)**

**Scenario:**
Global financial trading platform. Requirement: "Deposit $100 → Immediately available for trading in all regions"

**Problem:** Network partition between US and Europe data centers

**During partition:**
- US region sees $100 (available)
- Europe region doesn't see $100 (delayed)

**Tradeoff:**
- CP approach: Europe blocks trades (consistency)
- AP approach: Europe allows trades on delayed data (availability)

**Questions:**

(a) **Analyze: Why can't have both Consistency + Availability during partition?** (2 marks)

(b) **Design CP system: Quorum-based writes. What happens during partition?** (2 marks)

(c) **Design AP system: Both regions continue. How resolve conflicts after healing?** (2 marks)

(d) **Which for trading: CP or AP? Justify with business impact.** (1 mark)

---

### **Q26. Replication Lag Problem Solving (7 marks)**

**Scenario:**
E-commerce platform:
- Primary DB in US
- Read replicas in Europe, Asia
- Replication lag: 100ms average, 500ms worst case

**Customer A:**
1. Buys iPhone in US (primary updated instantly)
2. Checks inventory from Europe (replica shows old stock)
3. Sees item still available, tries to buy again
4. Fails: Stock already sold

**Problem:** Read-after-write inconsistency

**Questions:**

(a) **Explain replication lag and its causes.** (1 mark)

(b) **Solutions: Read from primary, sticky reads, replication monitoring. Compare.** (2 marks)

(c) **Implement: After write, next read from primary. For other operations, replica OK.** (2 marks)

(d) **Monitor: Set alert if lag > 1 second. Show metrics.** (2 marks)

---

### **Q27. Sharding Strategy for Global App (8 marks)**

**Scenario:**
App with 1B users, 10TB total data. Single database can't handle.
Solution: Shard by user_id across 100 database servers.

**Current:** Hash-based sharding
- Shard = hash(user_id) % 100

**Problem:** Need to add 50 more servers → 50% of data must move (expensive!)

**Questions:**

(a) **Explain hash-based sharding disadvantage when scaling.** (2 marks)

(b) **Alternative: Consistent hashing or directory-based. Compare.** (2 marks)

(c) **Implement directory-based: User table on special "shard directory" server.** (2 marks)

(d) **Handle shard-local transactions: Write updates affect only 1 shard. Cross-shard joins?** (2 marks)

---

### **Q28. Data Consistency in Distributed Systems (7 marks)**

**Scenario:**
Payment system. Transaction: Transfer $100 from Account A (Shard 1) to Account B (Shard 2)

**Without coordination:**
1. Deduct from A: $100 → 0 (Shard 1)
2. Network fails
3. Add to B: Failed (transaction incomplete)

**Result:** Money vanishes!

**Questions:**

(a) **Explain the problem: What could go wrong? Partial failure, network partition?** (2 marks)

(b) **Solution 1: Two-Phase Commit. Steps and failure handling?** (2 marks)

(c) **Solution 2: Saga pattern (eventual consistency). Steps?** (2 marks)

(d) **Which for payments? CP (2PC) or AP (Saga)? Justify.** (1 mark)

---

### **Q29. Performance Optimization End-to-End (8 marks)**

**Scenario:**
Report "Top 100 customers by spending in Q4": Currently takes 2 minutes

**Data:**
- Customers table: 10M rows
- Orders table: 100M rows  
- Order_items: 500M rows
- No indexes

**Requirements:** Must run in <10 seconds

**Questions:**

(a) **Analyze: Why slow? Estimate query cost (full table scans vs indexed).** (2 marks)

(b) **Indexing strategy: Which columns to index? Composite indexes?** (2 marks)

(c) **Query optimization: Rewrite for efficiency. Any schema changes?** (2 marks)

(d) **Alternative: Materialized view or summary table. When beneficial?** (2 marks)

---

### **Q30. Complete System Design: Netflix-Scale Architecture (10 marks)**

**Mega Scenario:** Design database architecture for Netflix-like platform:

**Scale:**
- 500M active users globally
- 10B hours watched/day
- 10M movies/shows
- 100K new videos/month
- Personalization: Each user different homepage

**Key Requirements:**
- Watch history (append-only)
- Real-time recommendations (ML scores)
- Payment/subscription (transactions)
- Search/browse (full-text + semantic)
- Analytics (1B events/day)
- Global: <100ms latency everywhere

**Questions:**

(a) **Design complete architecture: What databases for each function?** (3 marks)

(b) **Recommendation engine: Real-time ML serving 100 scores/user. Architecture?** (2 marks)

(c) **Consistency design: Payment consistency vs watch history availability. Trade-offs?** (2 marks)

(d) **Disaster recovery: Data center destroyed. Recovery plan?** (2 marks)

(e) **Cost: Estimate monthly cloud spend for this architecture.** (1 mark)

---

## **Summary Matrix: Topics Covered**

| Topic | Sessions | Questions |
|---|---|---|
| **Normalization/ER Model** | 1-2 | Q1 |
| **Transactions/ACID** | 2-3 | Q2, Q28 |
| **SQL Optimization** | 2-6 | Q3, Q29 |
| **Distributed Transactions** | 3-4 | Q4, Q28 |
| **Replication** | 3-4 | Q5, Q26 |
| **Backup/Recovery** | 7 | Q6 |
| **Indexing** | 6 | Q7, Q29 |
| **Full-Text Search** | 8 | Q8 |
| **Elasticsearch** | 8 | Q9 |
| **Polyglot Persistence** | 8-9 | Q10 |
| **Cloud Databases** | 9 | Q11 |
| **Dimensional Modeling** | 10 | Q12, Q14 |
| **ETL/ELT** | 10 | Q13 |
| **Data Lakes** | 11 | Q15 |
| **Delta Lake** | 11 | Q16 |
| **Vector Embeddings** | 12 | Q17, Q18 |
| **Stream Processing** | 13 | Q19, Q20 |
| **Temporal Databases** | 14 | Q21, Q22 |
| **Spatial Databases** | 14 | Q23, Q24 |
| **CAP Theorem** | 9 | Q25 |
| **System Design** | 1-14 | Q30 |

---

## **Exam Preparation Tips**

1. **Practice under time pressure:** 8-10 marks = 15-20 minutes
2. **Focus on "WHY":** Understand reasoning, not just memorizing
3. **Draw diagrams:** Schema, architecture, data flow
4. **Code matters:** Write actual SQL/pseudocode when asked
5. **Trade-offs:** Every decision has pros and cons
6. **Real systems:** Think Netflix/Uber scale while solving

**Total:** 30+ Practical scenario-based questions covering ALL 14 sessions
**Time to solve all:** 8-10 hours
**Recommended:** Solve 2-3 questions per study session

