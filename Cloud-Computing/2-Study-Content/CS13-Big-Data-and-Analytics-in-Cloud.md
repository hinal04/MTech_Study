# Big Data and Analytics in Cloud - Session 13

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**

---

## Table of Contents

1. [Big Data Challenges and Opportunities](#big-data-challenges-and-opportunities)
2. [Distributed Data Processing](#distributed-data-processing)
3. [Cloud Big Data Services](#cloud-big-data-services)
4. [Data Warehousing](#data-warehousing)
5. [Stream Processing](#stream-processing)
6. [Machine Learning in Cloud](#machine-learning-in-cloud)

---

## Big Data Challenges and Opportunities

### Volume, Velocity, Variety (The 3 Vs)

**Volume:** Data amount

```
Traditional Database: 1 TB
├─ Single server can store
├─ Single database query manageable
└─ Upload time: Hours

Big Data: 1 PB (1000 TB)
├─ Requires distributed storage (many servers)
├─ Query across cluster
└─ Upload time: Days

Scale: 1 million x larger
```

**Velocity:** Data generation rate

```
Traditional: Batch processing (overnight)
├─ Wait until EOD to run reports
├─ Results available next morning
├─ 24-hour old data

Big Data: Real-time processing
├─ Process as data arrives
├─ Results within seconds
├─ Fresh, actionable insights

Example: Stock market
- Batch: Overnight analysis (too late)
- Real-time: Immediate anomaly detection (trade triggers)
```

**Variety:** Data types

```
Traditional: Structured (SQL tables)
├─ Columns defined in advance
├─ Consistent format
├─ Easy to query

Big Data: Mixed types
├─ Structured: Sales database (CSV)
├─ Unstructured: Customer reviews (text)
├─ Semi-structured: JSON logs (nested)
├─ Binary: Images, videos
├─ Streams: Real-time sensor data

Challenge: Processing disparate sources
```

### Business Opportunities

**Personalization:**
```
Netflix recommendation engine:
├─ Analyze: Billions of viewing hours
├─ Identify: Patterns in what users watch
├─ Predict: What you'll want to watch
└─ Result: Engagement increases 50%
```

**Fraud Detection:**
```
Credit card company:
├─ Analyze: Transaction patterns
├─ Identify: Anomalies
├─ Alert: Suspicious transactions
└─ Result: Fraud loss reduced by 90%
```

**Supply Chain Optimization:**
```
Logistics company:
├─ Analyze: Route data, weather, traffic
├─ Identify: Optimal paths
├─ Predict: Delivery times accurately
└─ Result: Fuel cost reduced by 20%
```

---

## Distributed Data Processing

### The MapReduce Model

**Problem:** Process 1 TB of data
- Single server: 24 hours
- 100 servers: 15 minutes

**Solution:** Distribute computation

**MapReduce Phases:**

**1. Map Phase:** Distribute work

```
Input: Huge file (1 TB)
│
Mapper 1: Process chunk 1 (100 GB) → Intermediate output
Mapper 2: Process chunk 2 (100 GB) → Intermediate output
Mapper 3: Process chunk 3 (100 GB) → Intermediate output
...
Mapper 10: Process chunk 10 (100 GB) → Intermediate output

Parallelism: 10 mappers running simultaneously = 10x speedup
```

**2. Shuffle and Sort Phase:** Reorganize data

```
Intermediate output (key-value pairs):
Mapper 1: (word, 1), (word, 2), ...
Mapper 2: (word, 1), (word, 3), ...
Mapper 3: (word, 2), (word, 1), ...

Grouped by key:
word: [1, 2, 1, 3, 2, 1, ...]  ← All "word" values together
apple: [1, 1, 1, ...]           ← All "apple" values together
```

**3. Reduce Phase:** Aggregate results

```
Reducer 1:
Input: word: [1, 2, 1, 3, 2, 1]
Output: (word, 13)  ← sum of all 1s

Reducer 2:
Input: apple: [1, 1, 1]
Output: (apple, 3)
```

**Example: Word Count**

```
Input: 3 documents (1 MB each)

MAP:
├─ Doc1 → (hello, 1), (world, 1), (hello, 1), ...
├─ Doc2 → (hello, 1), (foo, 1), ...
└─ Doc3 → (bar, 1), (hello, 1), ...

SHUFFLE & SORT:
hello: [1, 1, 1, 1, 1]
world: [1]
foo: [1]
bar: [1]

REDUCE:
hello: 5
world: 1
foo: 1
bar: 1

RESULT: Frequency of each word
```

### Hadoop vs Spark

**Hadoop (MapReduce):**
```
├─ Disk-based: Write intermediate results to disk
├─ Latency: 1-10 seconds (IO overhead)
├─ Use case: Batch processing, one-time computations
├─ Setup: Complex, Hbase, Hive required
└─ Cost: Cheap, long-running fine
```

**Spark:**
```
├─ Memory-based: Keep intermediate results in RAM
├─ Latency: 10-100 milliseconds (memory speed)
├─ Use case: Iterative algorithms, interactive queries
├─ Setup: Simpler, SQL friendly
├─ Cost: Expensive, needs large cluster with RAM
```

**Performance Comparison:**
```
Sort 100 GB:
└─ Hadoop: 55 seconds
└─ Spark: 23 seconds (2.4x faster)

Iterative ML algorithm (1000 iterations):
└─ Hadoop: Would read/write disk 2000 times (terrible)
└─ Spark: Keeps data in memory (ideal)
```

---

## Cloud Big Data Services

### Amazon EMR (Elastic MapReduce)

**What it does:** Managed Hadoop/Spark cluster

**Setup:**
```
Traditional Hadoop:
├─ Provision 100 servers
├─ Install OS
├─ Install Java
├─ Install Hadoop
├─ Configure HDFS
├─ Time: 2-3 days
└─ Maintenance: Ongoing

EMR:
├─ Click: Create cluster
├─ Select: Size, type
├─ Time: 15 minutes
└─ Maintenance: Automatic (AWS handles)
```

**Use Case:**
```
Batch Processing:
1. Provision cluster (10 m5.2xlarge)
2. Submit Spark job
3. Wait for results
4. Terminate cluster
Cost: Pay only while running
```

**Pricing Example:**
```
100 m5.2xlarge instances for 2 hours:
├─ Instance cost: 100 × $0.342 × 2 = $68.40
├─ EMR service fee: 100 × 0.11 × 2 = $22
└─ Total: ~$90

vs. owning cluster:
├─ Capex: $500,000
├─ Deprecation: 5 years = $100,000/year
└─ Plus staff, power, cooling, etc.
```

### Amazon Redshift (Data Warehouse)

**What it does:** SQL analytics on massive data

```
Redshift Cluster:
├─ 20 ra3.4xlplus nodes (each 32 TB)
├─ Total capacity: 640 TB
├─ Compression: 4-10x (actual: 2-6 TB stored)
└─ Cost: $4.80/hour per node = $96/hour
```

**vs. Traditional Database:**

| Aspect | Traditional DB | Redshift |
|--------|---|---|
| Data Size | 100 GB | 1 PB+ |
| Query Speed | <1 second | 10-60 seconds |
| Concurrency | 1000s queries | 100s queries |
| Cost | $10,000/month | $20,000/month |

**Use Case:**
```
Query: "Show top 100 products by revenue, by region"

Data: 10 years of sales (5 million transactions/day)
└─ 18 billion transactions total

Traditional DB: Crashes (too much data)
Redshift: Completes in 30 seconds
```

---

## Data Warehousing

### Star Schema

**Structure:**

```
Fact Table (Sales)
├─ sales_id (primary key)
├─ product_id (foreign key)
├─ store_id (foreign key)
├─ date_id (foreign key)
├─ amount
└─ quantity

Dimension Tables:
├─ Product (product_id, name, category, price)
├─ Store (store_id, location, region, manager)
└─ Date (date_id, day, month, year, quarter)
```

**Query Example:**
```SQL
SELECT 
  d.year,
  s.region,
  SUM(f.amount) as total_sales
FROM fact_sales f
JOIN dim_product p ON f.product_id = p.product_id
JOIN dim_store s ON f.store_id = s.store_id
JOIN dim_date d ON f.date_id = d.date_id
WHERE p.category = 'Electronics'
  AND s.region = 'Europe'
  AND d.year = 2024
GROUP BY d.year, s.region
```

**Performance:**
```
Fact table: 10 billion rows
Dimension tables: 1000s rows each

Index on foreign keys: Joins fast
Compression: Multiple fact rows per block
Result: Sub-second query response
```

---

## Stream Processing

### Real-Time Processing Need

**Use Case: Netflix Watching Trends**

```
Every second:
├─ 100K users start watching
├─ 50K pause
├─ 30K resume
└─ 10K stop watching

Need: Trending shows RIGHT NOW

Batch approach (overnight):
└─ Results tomorrow (too late)

Stream processing approach:
└─ Results in real-time (useful)
```

### Amazon Kinesis

**How it works:**

```
Data Producers:
├─ Mobile app (user watched episode)
├─ Smart TV (started watching)
└─ Website (paused video)
     │
     │ Stream
     ▼
Kinesis Data Streams
├─ Shard 1: (user_id, action, timestamp)
├─ Shard 2: (user_id, action, timestamp)
└─ Shard 3: (user_id, action, timestamp)
     │
     │ Process
     ▼
Kinesis Analytics (or Lambda)
     │
     ├─ Calculate: Trending shows
     ├─ Update: Redis cache
     └─ Publish: Real-time metrics
     │
     ▼
Dashboard updates (live numbers)
```

**Streaming SQL:**
```SQL
SELECT 
  show_name,
  COUNT(*) as viewers_now,
  SUM(CASE WHEN action = 'play' THEN 1 ELSE 0 END) as starts,
  SUM(CASE WHEN action = 'pause' THEN 1 ELSE 0 END) as pauses
FROM viewing_stream
WHERE EVENT_TIME between CURRENT_TIMESTAMP - INTERVAL '5' MINUTE 
                     and CURRENT_TIMESTAMP
GROUP BY show_name
ORDER BY viewers_now DESC
LIMIT 10;

Result: Trending shows, updated every second
```

---

## Machine Learning in Cloud

### ML Pipeline

```
1. Data Collection
   └─ S3, Kinesis, databases
   
2. Data Preparation
   └─ Clean, transform, feature engineering
   
3. Model Training
   └─ SageMaker training job (distributed)
   
4. Model Evaluation
   └─ Accuracy, precision, recall metrics
   
5. Hyperparameter Tuning
   └─ SageMaker automatic tuning
   
6. Model Deployment
   └─ SageMaker endpoints (REST API)
   
7. Monitoring
   └─ Accuracy degradation detection
```

### Amazon SageMaker

**Fully Managed ML:**

```
Traditional ML:
├─ Provision GPU instances ($10/hour)
├─ Install TensorFlow, PyTorch
├─ Write training code
├─ Monitor training (manual)
├─ Deploy model
├─ Manage prediction endpoints
└─ Scale manually

SageMaker:
├─ Upload data to S3
├─ Create notebook (Jupyter)
├─ Write training code
├─ Click: Train
├─ Click: Deploy
└─ Automatic scaling
```

**Common Models:**

```
Computer Vision:
└─ Image classification (product recognition)

NLP:
└─ Sentiment analysis, topic modeling

Time Series:
└─ Forecasting (demand prediction)

Recommendation:
└─ Factorization machines (what to recommend)
```

---

## Summary

**Big Data Processing:**
- MapReduce: Distribute computation across machines
- Hadoop: Open-source, complex setup
- Spark: Modern, memory-based, faster

**Cloud Services:**
- EMR: Managed Hadoop/Spark cluster
- Redshift: Data warehouse for analytics
- Kinesis: Real-time streaming
- SageMaker: Machine learning managed service

**Use Cases:**
- Batch: Historical analysis (Redshift, EMR)
- Real-time: Live dashboards (Kinesis, Spark Streaming)
- ML: Predictions, recommendations (SageMaker)

**Cost Consideration:**
- Pay for data stored (S3: cheap, Redshift: expensive)
- Pay for compute (EMR: hourly, pay-per-job)
- Pay for processing (Kinesis: per shard)
- Optimize: Use right tool for workload

