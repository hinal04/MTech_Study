# Database Management Systems - Post-MidSem Questions
## Sessions 8-10: Search Databases, Cloud Databases, Data Warehousing

---

## Q1. E-Commerce Search Optimization Scenario (8 marks)

**Scenario:**
You're designing search functionality for an e-commerce platform with 10 million products. Currently, customers search using simple keyword matching which is slow and returns irrelevant results. Products include: title, description, category, price, ratings, and seller info.

**Problems Observed:**
- Search for "laptop computer" misses results for "notebook device"
- Searching takes 3-5 seconds for each query
- Users complain about result quality and relevance
- System crashes during peak hours (Black Friday: 100K concurrent users)

**Questions:**

(a) **Why would a traditional SQL database be inadequate for this search requirement? Explain three limitations.** (3 marks)

(b) **Design a search architecture using Elasticsearch. Include: data model, indexing strategy, and how you'd handle the 10 million products.** (3 marks)

(c) **How would you improve search quality to handle "laptop computer" matching "notebook device"? What technology would you use?** (2 marks)

---

## Q2. BM25 Ranking Problem Solving (7 marks)

**Scenario:**
Your search engine returns too many irrelevant results. After analyzing queries:
- Query "best Python book" returns 100K results
- Python tutorial sites ranked higher than expert Python books
- Very common words like "best" and "book" dominate ranking

**Current Setup:**
- Simple TF ranking (just counting keyword occurrences)
- Database has 1 million book documents
- Average document length: 500 words

**Questions:**

(a) **Why does simple TF scoring fail here? Give an example with numbers.** (2 marks)

(b) **Explain how BM25 would solve this problem. Focus on IDF component.** (2 marks)

(c) **Given: df("best")=500K, df("Python")=50K, df("book")=200K out of 1M total docs**
**Calculate IDF for each term. Which term has highest weight and why?** (2 marks)

(d) **A document is 1500 words (3x average length). How does BM25 handle this vs simple TF?** (1 mark)

---

## Q3. Elasticsearch Scaling Crisis (8 marks)

**Scenario:**
Your Elasticsearch cluster (5 nodes, 3 shards, 1 replica) served well with 100M documents. But now:
- Dataset growing to 1B documents
- Query latency increasing from 200ms to 2000ms
- Node CPU at 85%, Memory at 90%
- Rebalancing taking 2 hours after node addition

**Questions:**

(a) **Why is performance degrading? Analyze 3 factors.** (3 marks)

(b) **Should you increase shards to 10? Or add more nodes? Justify your choice with trade-offs.** (3 marks)

(c) **Design a new cluster architecture (nodes, shards, replicas). How would you migrate without downtime?** (2 marks)

---

## Q4. Polyglot Persistence for Ride-Sharing App (8 marks)

**Scenario:**
You're designing a database architecture for Uber-like app. Requirements:
- Fast booking: Create order, update driver location, calculate fare (needs transactions)
- Driver search: "Find drivers within 2km", "Show top 50 drivers by rating"  
- Real-time notifications: Driver accepted order, arriving in 5 min
- Analytics: 100M daily transactions to analyze demand patterns

**Current Problem:**
- Single PostgreSQL causing bottlenecks
- Spatial queries slow (finding nearby drivers)
- Real-time features lagging

**Questions:**

(a) **Which database(s) would you choose for each requirement? Justify.** (4 marks)

(b) **How would you sync data between systems? Discuss trade-offs.** (2 marks)

(c) **Draw the system architecture showing data flow between databases.** (2 marks)

---

## Q5. Cloud Database Migration Challenge (7 marks)

**Scenario:**
Your company runs 50GB PostgreSQL database on-premise with:
- 10K reads/sec during peak
- 500 writes/sec constantly
- Replication to standby for HA
- Global users (latency issues from India-US-Europe)

**Decision:** Migrate to cloud (AWS RDS vs CockroachDB)

**Questions:**

(a) **What are advantages and disadvantages of each option for your workload?** (3 marks)

(b) **Design a migration strategy minimizing downtime. Include rollback plan.** (2 marks)

(c) **Post-migration, users in Europe complain of 500ms latency. How would you solve with multi-region?** (2 marks)

---

## Q6. Data Warehouse for Retail Analytics (8 marks)

**Scenario:**
Large retail chain with 500 stores. Need to analyze:
- Daily sales by store, product, region
- Customer purchase patterns
- Inventory levels
- Supplier performance

**Current State:**
- Sales data in POS system (1M transactions/day)
- Customer data in CRM
- Inventory in WMS
- Supplier data scattered across emails/files

**New Requirement:**
CFO wants: "What were top 10 products by profit in North region last quarter? Filter by suppliers with >95% on-time delivery"

**Questions:**

(a) **Design a dimensional model (fact & dimension tables). Draw the schema.** (4 marks)

(b) **What is grain of your fact table? How would you handle multiple grains (transaction vs daily aggregate)?** (2 marks)

(c) **Write SQL to answer CFO's question using your schema.** (2 marks)

---

## Q7. ETL Pipeline Failure Recovery (7 marks)

**Scenario:**
Your daily ETL pipeline:
1. Extracts 50GB from 10 source systems (6 PM)
2. Transforms and validates (8 PM)
3. Loads to warehouse (10 PM)

**Yesterday's Problem:**
- ETL started at 6 PM
- At 9 PM, one transform step failed (column type mismatch)
- 30% of data already loaded incorrectly
- Batch job restarted but entire pipeline ran again (expensive, 4 hours late)

**Questions:**

(a) **What went wrong? Why did restarting cause re-processing of already-loaded data?** (2 marks)

(b) **Design an idempotent pipeline. What changes needed to safely restart failed steps?** (3 marks)

(c) **Should you use ETL or ELT? Justify for this scenario.** (2 marks)

---

## Q8. Slowly Changing Dimension Dilemma (6 marks)

**Scenario:**
Customer address changes frequently:
- Customer 1234 moved from NYC to LA on March 15
- Sales report needed: "Top customers by region in Q1" 
- Q1 ends March 31

**Question:** Which SCD type to use? Why?

**Analysis:**

Customer lived in NYC for Q1 (Jan-Mar 15) = should report under NYC
But if using SCD Type 1 (overwrite), reporting shows LA

**Questions:**

(a) **Explain the problem with SCD Type 1 for this scenario.** (2 marks)

(b) **Show how SCD Type 2 would solve it. Include schema.** (2 marks)

(c) **What's the trade-off of using SCD Type 2 vs Type 1?** (2 marks)

---

## Q9. Product Search Hybridization (6 marks)

**Scenario:**
Your product search needs improvement:
- Keyword search works but misses synonyms
- Semantic search better but slower
- Example: "cheap watch" should find "affordable chronograph" and "budget-friendly timepiece"

**Current:** Only lexical (Elasticsearch inverted index)

**Question:** Implement hybrid search strategy

**Solution Requirements:**

(a) **Explain lexical vs semantic search and their limitations. Use example.** (2 marks)

(b) **Design hybrid architecture combining both. Include ranking fusion.** (3 marks)

(c) **Why can't you use pure semantic search? (Hint: cost, latency)** (1 mark)

---

## Q10. Text Analysis for Multi-Language Search (5 marks)

**Scenario:**
Your marketplace serves customers in 5 countries speaking different languages. Same product listing needs searchable in English, Spanish, French.

**Problem:** 
Text "LAPTOP COMPUTER" in English should match "portátil computadora" in Spanish and "ordinateur portable" in French.

**Questions:**

(a) **How would text analysis pipeline differ for each language?** (2 marks)

(b) **What additional processing needed beyond basic tokenization?** (2 marks)

(c) **How would you handle multi-language in single search query?** (1 mark)

