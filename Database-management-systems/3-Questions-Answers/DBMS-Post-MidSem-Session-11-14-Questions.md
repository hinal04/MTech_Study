# Database Management Systems - Post-MidSem Questions
## Sessions 11-14: Data Lakes, Vector Databases, Streaming, Temporal/Spatial

---

## Q1. Data Lake Implementation for Healthcare (8 marks)

**Scenario:**
Hospital system collecting:
- 1TB/day of patient records, lab results, imaging (DICOM files)
- Unstructured notes from doctors
- Sensor data (ECG, heart rate, blood pressure)
- Billing/insurance documents (PDFs)
- Currently stored across 20+ different systems (silos)

**Challenge:**
Need to:
1. Consolidate all data for ML research
2. Maintain 7-year historical audit trail
3. Enable researchers to explore data
4. Comply with HIPAA privacy regulations

**Current Setup:** Data warehouse (too rigid for research exploration)

**Questions:**

(a) **Why is data lake better than data warehouse for this use case? Give 3 reasons.** (3 marks)

(b) **Design data lake architecture with zones (bronze/silver/gold). What goes where?** (3 marks)

(c) **Implement Delta Lake for ACID transactions. Why needed here?** (2 marks)

---

## Q2. Data Governance Crisis in Data Lake (7 marks)

**Scenario:**
Your company deployed data lake 6 months ago. Now facing:
- 50TB of data but no one knows what's what
- 100+ datasets created by different teams
- Data quality issues: duplicates, missing values, inconsistencies
- Compliance audit failure: Can't prove data lineage
- 3 teams accidental overwrite each other's work

**Problems:**
- "Data Swamp" - disorganized dump
- No metadata documentation
- No access controls
- No versioning

**Questions:**

(a) **List 3 governance failures and their business impact.** (3 marks)

(b) **Design governance framework including: metadata catalog, data lineage, access control.** (2 marks)

(c) **Implement versioning for accidental overwrites. Use Delta Lake features.** (2 marks)

---

## Q3. Vector Database for Recommendation Engine (8 marks)

**Scenario:**
Netflix-like streaming service with 100M movies/shows. Currently:
- User watches movie → stored in database
- Recommendation: "Users who watched X also watched Y" (collaborative filtering)
- Limitations: Can't understand content (doesn't know "Inception" is similar to "Interstellar" thematically)

**New Requirement:**
Better recommendations using semantic understanding:
- "Show me movies like Inception" → should find similar sci-fi/mind-bending films
- Not just based on who watched what, but actual content similarity

**Questions:**

(a) **How would you generate embeddings for 100M movies? What features to use?** (3 marks)

(b) **Design vector database architecture. How to store/query 100M 1536-dim vectors?** (3 marks)

(c) **Implement hybrid recommendation: collaborative filtering + semantic similarity.** (2 marks)

---

## Q4. Vector Search for Question Answering (7 marks)

**Scenario:**
Legal firm has 1M legal documents. Lawyers spend hours searching relevant cases.

**Goal:** 
Lawyer asks: "What's precedent for contract breach penalties?" 
System returns top 10 most relevant cases instantly.

**Current:** SQL keyword search too slow and misses semantic relevance

**Questions:**

(a) **Explain RAG (Retrieval Augmented Generation) approach.** (2 marks)

(b) **Design system: embed 1M documents + query → retrieve relevant + answer. Architecture?** (3 marks)

(c) **Why can't you just use keyword search? Why vectors needed?** (2 marks)

---

## Q5. Real-Time Fraud Detection with Streaming (8 marks)

**Scenario:**
Credit card company processing 100K transactions/second globally. Need real-time fraud detection.

**Current Issues:**
- Batch processing (overnight) finds fraud too late
- By next day, $10M+ already stolen
- Need to block within milliseconds

**Requirements:**
- Real-time scoring: flag suspicious transactions instantly
- Alert merchant/customer within 100ms
- Maintain transaction state: "Has this customer made 10 transactions in 1 hour?"

**Questions:**

(a) **Stream vs batch? Why batch fails here? Design streaming solution.** (3 marks)

(b) **Implement stateful stream processing: track customer transaction history. How maintain state on failure?** (3 marks)

(c) **Implement windowing: "If >5 transactions in 1 hour → suspicious". Which window type?** (2 marks)

---

## Q6. Stream Processing Exactly-Once Semantics (7 marks)

**Scenario:**
Financial system processing stock trades:
- 50K trades/second
- System crashed mid-processing
- After restart, some trades processed twice (data corruption)
- Duplicate trades caused incorrect account balances

**Problem:**
When system restarts, doesn't know which trades already processed.

**Questions:**

(a) **Explain the problem: At-most-once vs At-least-once vs Exactly-once.** (2 marks)

(b) **Why exactly-once critical for financial systems?** (1 mark)

(c) **Design exactly-once implementation using idempotence + checkpointing.** (2 marks)

(d) **Write pseudocode showing safe restart after failure.** (2 marks)

---

## Q7. Time-Series Data Management (7 marks)

**Scenario:**
IoT company monitoring 100K industrial sensors:
- Each sensor sends temperature/pressure reading every 10 seconds
- 1 year of data = 315 billion data points
- Need to query: "Show temperature trend for Sensor#1234 for last month"

**Problem:**
- Traditional SQL database can't handle query speed
- Storage costs exploding

**Questions:**

(a) **Why traditional database inadequate? Discuss scale.** (2 marks)

(b) **Design time-series optimization: partitioning, downsampling, retention.** (3 marks)

(c) **Implement: Keep raw data 1 month, aggregate to daily summaries after.** (2 marks)

---

## Q8. Temporal Database for Corrections (6 marks)

**Scenario:**
Insurance company:
- Policy #123: Premium $500/month (Jan 1 - Dec 31)
- Discovered error on Jan 15: Should have been $550/month
- Correction recorded Jan 16

**Question:** How to track this for audit trail?

**Problems:**
- If update record: Can't prove what was correct on Jan 15
- Simple versioning not enough

**Questions:**

(a) **Explain valid-time vs transaction-time needed here.** (2 marks)

(b) **Design bi-temporal schema for this scenario. Show rows before/after correction.** (2 marks)

(c) **Write SQL to query: "What premium was effective on Jan 10?" vs "What did system record on Jan 15?"** (2 marks)

---

## Q9. Spatial Database for Delivery Optimization (8 marks)

**Scenario:**
Delivery company with:
- 50K daily delivery locations
- 1000 delivery trucks
- Need to optimize routes in real-time
- Requirement: "What orders should Truck#42 deliver next?"

**Current Problem:**
- Calculating distances between all locations is slow
- Can't do spatial queries fast enough

**Questions:**

(a) **What spatial queries needed? Design PostGIS schema.** (3 marks)

(b) **Implement R-tree indexing. How to query "orders within 2km of truck"?** (3 marks)

(c) **Optimize route: Find shortest path visiting 5 delivery points. Use spatial functions.** (2 marks)

---

## Q10. Multi-Region Temporal-Spatial Query (6 marks)

**Scenario:**
Ride-sharing company:
- Driver location updated every 10 seconds
- Need to find: "Available drivers within 2km of passenger, who were also in that zone 1 hour ago (for eligibility)"

**Combined Requirement:**
- Spatial: "Within 2km" (R-tree query)
- Temporal: "Within last 1 hour" (time range)

**Questions:**

(a) **How is this different from pure spatial or pure temporal query?** (2 marks)

(b) **Design schema combining temporal + spatial. Schema and indexes?** (2 marks)

(c) **Write pseudocode/SQL for the query.** (2 marks)

---

## Q11. Event-Driven Architecture with Streaming (7 marks)

**Scenario:**
E-commerce platform:
1. Customer places order → Order Created event
2. Payment processed → Payment Confirmed event  
3. Inventory updated → Stock Decreased event
4. Shipment created → Shipment Created event
5. Delivery tracking → Location Updated events (100K per hour)

**All events flow through Kafka → Multiple consumers:**
- Consumer 1: Update order status (operational DB)
- Consumer 2: Send notifications
- Consumer 3: Update analytics

**Problem:**
If Consumer 1 fails, rest still processing. Causes inconsistency.

**Questions:**

(a) **Design event-driven architecture showing all components.** (3 marks)

(b) **How to ensure Consumer 1 (operational DB) processes events reliably?** (2 marks)

(c) **Implement exactly-once: Order state updated exactly once despite failures.** (2 marks)

---

## Q12. Stream-Batch Lambda Architecture (6 marks)

**Scenario:**
Rideshare company needs:
- **Speed Layer:** Real-time driver supply/demand (Kafka Streams)
- **Batch Layer:** Daily demand analysis, surge pricing optimization (Spark)
- **Serving Layer:** App needs unified view

**Current Problem:**
Speed layer says "50 drivers available" but batch layer (more accurate) says "30 drivers".

**Questions:**

(a) **Explain Lambda Architecture for this use case.** (2 marks)

(b) **How to reconcile speed and batch layer results?** (2 marks)

(c) **When would you use Lambda vs just Kappa (pure stream)?** (2 marks)

---

## Q13. Data Quality in Streaming (5 marks)

**Scenario:**
Sensor network sends temperature readings. Some issues:
- Sensor malfunction: Reading 999°C (impossible)
- Network delay: Sensor timestamp from yesterday arriving today
- Duplicate events: Same reading received twice

**Stream needs:**
- Filter invalid readings
- Handle late arrivals
- Deduplicate

**Questions:**

(a) **How to filter outliers in streaming? (Hint: Use state/history)** (2 marks)

(b) **Handle late data: Reading from yesterday arriving today. Use windows to fix?** (2 marks)

(c) **Deduplication implementation.** (1 mark)

---

## Q14. Combining All Technologies: Real-World Scenario (8 marks)

**Mega Scenario:** Uber-like Platform Architecture

Your company needs to build integrated system using:
- Search DB (find nearby drivers)
- Streaming (real-time location updates)
- Temporal DB (trip history with timestamps)
- Spatial DB (route optimization)
- Cloud DB (scalability)
- Data Lake (analytics)

**Requirement:**
"Find available drivers within 2km, check their history (never cancelled), show optimal route, store for analytics"

**Questions:**

(a) **Which technology for each part? Justify.** (3 marks)

(b) **Design complete architecture showing data flow.** (3 marks)

(c) **Identify consistency/latency trade-offs you're making.** (2 marks)

---

## Study Tips for Exam

1. **Think Practically:** Don't memorize definitions. Ask "When would I use this?"
2. **Design Problems:** Most questions ask you to design solutions, not explain concepts
3. **Trade-offs:** Every technology has trade-offs. Explain them
4. **Scenarios:** Read scenario carefully. It tells you what matters
5. **Implementation Details:** Be ready to write SQL/pseudocode when asked

