# Session 6: Data Engineering Pipelines

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 3, Class Notes
>
> **Contact Session:** 6 (Module 2: Data and Feature Engineering)

---

## Table of Contents

- [6.1 What is a Data Pipeline?](#61-what-is-a-data-pipeline)
- [6.2 ETL vs ELT](#62-etl-vs-elt)
- [6.3 Batch vs Streaming Pipelines](#63-batch-vs-streaming-pipelines)
  - [Batch Ingestion Considerations](#batch-ingestion-considerations)
  - [Streaming Ingestion Concepts](#streaming-ingestion-concepts)
  - [Data Ingestion Tools and Challenges](#data-ingestion-tools-and-challenges)
- [6.4 Storage Types Deep Dive](#64-storage-types-deep-dive)
- [6.5 Data Lakes and Data Warehouses](#65-data-lakes-and-data-warehouses)
- [6.6 Enterprise Data Pipeline — Case Study](#66-enterprise-data-pipeline--case-study)
- [6.7 Data Validation](#67-data-validation)
- [6.8 Data Drift, Skew, and Monitoring](#68-data-drift-skew-and-monitoring)
- [6.9 Data Leakage](#69-data-leakage)
- [6.10 Fairness and Bias in Data](#610-fairness-and-bias-in-data)
- [6.11 Data Partitioning](#611-data-partitioning)

---

## 6.1 What is a Data Pipeline?

### Simple Definition

A **data pipeline** is a series of steps that moves data from where it is created (sources) to where it is needed (destinations), transforming it along the way.

> **Analogy:** Think of a water supply system. Water comes from rivers and lakes (sources), goes through treatment plants (cleaning/transformation), flows through pipes (transport), and arrives at your tap (destination) clean and ready to use. A data pipeline does the same thing, but with data instead of water.

### Why Do AI Systems Need Data Pipelines?

AI models can't just eat raw data. The data needs to be:
1. **Collected** from multiple sources (databases, APIs, logs, files)
2. **Cleaned** (remove errors, fill missing values, fix formats)
3. **Transformed** (combine tables, compute aggregations, create features)
4. **Loaded** into the right storage for the model to consume

Without pipelines, data scientists spend all their time manually downloading CSVs, cleaning them in Excel, and copying them around. Pipelines **automate** this entire process.

### Basic Pipeline Structure

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   SOURCES    │───→│   EXTRACT    │───→│  TRANSFORM   │───→│    LOAD      │
│              │    │              │    │              │    │              │
│ • Databases  │    │ Pull data    │    │ Clean, join, │    │ Store in     │
│ • APIs       │    │ from all     │    │ aggregate,   │    │ warehouse,   │
│ • Log files  │    │ sources      │    │ compute      │    │ data lake,   │
│ • Streams    │    │              │    │ features     │    │ feature store│
│ • CSV files  │    │              │    │              │    │              │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
```

### Live Example: Swiggy's Data Pipeline

Every time you place an order on Swiggy, data flows through their pipeline:

```
SOURCES:
├── User app → order details, location, payment
├── Restaurant app → prep time, menu, availability  
├── Driver app → GPS location, speed, route
├── Google Maps API → traffic conditions, distance
├── Weather API → current weather (rain = longer delivery)
└── Payment gateway → transaction status

EXTRACT:
├── Apache Kafka collects events in real-time
└── Millions of events per second

TRANSFORM:
├── Join order data with restaurant data
├── Calculate distance between restaurant and user
├── Compute average prep time for this restaurant (last 100 orders)
├── Flag if it's raining (affects delivery time)
└── Create feature: "driver_availability_radius_3km = 8 drivers"

LOAD:
├── Real-time features → Feature Store (for ETA prediction)
├── Raw events → Data Lake (for batch analytics)
└── Aggregated data → Data Warehouse (for business dashboards)
```

---

## 6.2 ETL vs ELT

**ETL vs ELT — the two dominant data pipeline patterns.** Every data pipeline must decide: do you transform data before loading it (ETL), or load it raw first and transform later (ELT)? This choice shapes your entire architecture.

### ETL — Extract, Transform, Load

**ETL** is the traditional approach: data is **transformed before** it's loaded into the destination.

```
Source → Extract → Transform (clean, filter, aggregate) → Load into warehouse
```

> **Analogy:** You go grocery shopping (extract), wash and cut vegetables at home (transform), then store them in the fridge (load). The prep work happens before storage.

**How ETL works:**
1. **Extract:** Pull data from source systems (databases, APIs, files)
2. **Transform:** Clean the data, fix formats, join tables, compute aggregates — all done in a separate processing system (like Apache Spark)
3. **Load:** Put the cleaned, transformed data into the data warehouse

**When to use ETL:**
- Data warehouse has limited storage or compute (expensive per TB)
- You only want clean, structured data in the warehouse
- Data volumes are moderate
- Traditional industries (banking, insurance) with well-defined schemas

### ELT — Extract, Load, Transform

**ELT** is the modern approach: data is loaded **raw** first, then transformed inside the destination.

```
Source → Extract → Load (raw data into data lake) → Transform (inside the lake/warehouse)
```

> **Analogy:** You buy groceries (extract), dump everything in the fridge raw (load), then wash and cut vegetables only when you're ready to cook (transform). Less upfront work, but you need a big fridge.

**How ELT works:**
1. **Extract:** Pull data from source systems
2. **Load:** Dump raw data as-is into a data lake (cheap storage like S3)
3. **Transform:** Transform data inside the data lake/warehouse using tools like dbt or Spark — only when needed

**When to use ELT:**
- Cloud data warehouses with cheap storage and powerful compute (Snowflake, BigQuery)
- You want to keep raw data for future use (you might need it later for new ML features)
- Data volumes are very large
- Modern tech companies, startups

### ETL vs ELT — Side-by-Side Comparison

| Aspect | ETL | ELT |
|---|---|---|
| **Order** | Extract → Transform → Load | Extract → Load → Transform |
| **Where transformation happens** | Separate processing server | Inside the data warehouse/lake |
| **Raw data kept?** | No (only transformed data is stored) | Yes (raw data is stored first) |
| **Storage cost** | Lower (only clean data stored) | Higher (raw + transformed data stored) |
| **Flexibility** | Low (if you need new transforms, re-extract from source) | High (raw data is always available, transform anytime) |
| **Speed of loading** | Slower (transform before load) | Faster (load immediately, transform later) |
| **Best for** | On-premise warehouses, structured data, regulated industries | Cloud warehouses, big data, AI/ML workloads |
| **Tools** | Informatica, Talend, SSIS | dbt, Fivetran + Snowflake, Airbyte + BigQuery |

### Live Example: ETL vs ELT at PhonePe

**ETL approach (what they used initially):**
- Extract transaction data from payment servers
- Transform: clean, validate, aggregate into daily summaries
- Load into data warehouse
- Problem: If data science team needed raw transaction-level data for a new fraud model, they couldn't get it — only aggregated data was stored

**ELT approach (what they switched to):**
- Extract transaction data
- Load raw data into data lake (Amazon S3)
- Transform as needed: daily summaries for dashboards, raw data for ML models
- Now fraud detection team can access raw transactions anytime for new model features
- Marketing team can do their own transformations for campaign analysis

> **Industry trend:** Most modern companies are moving from ETL to ELT because cloud storage is cheap, and keeping raw data gives more flexibility for AI/ML.

---

## 6.3 Batch vs Streaming Pipelines

### Batch Processing — "Process data in large chunks at scheduled times"

**Batch processing** collects data over a period and processes it all at once — hourly, daily, or weekly.

> **Analogy:** Doing laundry. You collect dirty clothes all week, then wash everything in one batch on Sunday. Efficient, but your clothes aren't clean instantly.

```
Events accumulate over time:
[event1] [event2] [event3] ... [event1000]
                    ↓
        At midnight, process ALL events
                    ↓
        Results available next morning
```

**When to use batch:**
- Reports and dashboards (daily sales report)
- Model training (retrain model every night with new data)
- Data warehouse updates
- Tasks where a few hours of delay is acceptable

**Tools:** Apache Spark, Apache Airflow (scheduling), dbt (transformations), Hadoop

### Streaming Processing — "Process data as it arrives, in real-time"

**Streaming processing** handles each event immediately as it arrives — with millisecond to second latency.

> **Analogy:** A live cricket scoreboard. Every ball bowled is immediately reflected on the score — there's no waiting for the end of the over to update.

```
Events arrive continuously:
[event1] → process immediately → result available in milliseconds
[event2] → process immediately → result available in milliseconds
[event3] → process immediately → result available in milliseconds
```

**When to use streaming:**
- Fraud detection (must block fraudulent transaction BEFORE it completes)
- Real-time recommendations (show results as user browses)
- Live monitoring and alerting
- Any task where delay = lost value

**Tools:** Apache Kafka, Apache Flink, Apache Spark Streaming, AWS Kinesis

### Batch vs Streaming — Comparison

| Aspect | Batch | Streaming |
|---|---|---|
| **Processing** | All data at once, on schedule | Each event as it arrives |
| **Latency** | Minutes to hours | Milliseconds to seconds |
| **Throughput** | Very high (process millions of records efficiently) | Moderate (limited by event rate) |
| **Complexity** | Simpler to build and debug | More complex (handle late events, out-of-order data) |
| **Cost** | Lower (run compute only when needed) | Higher (compute always running) |
| **Error handling** | Easy (reprocess the entire batch) | Hard (can't easily "undo" a real-time decision) |
| **Use cases** | Reports, model training, ETL | Fraud detection, real-time recommendations, alerts |

### Live Examples

| Company | Batch Pipeline | Streaming Pipeline |
|---|---|---|
| **Netflix** | Retrain recommendation model nightly with yesterday's viewing data | Real-time "Continue Watching" updates as you switch devices |
| **Uber** | Daily driver performance reports, weekly demand forecasting | Real-time surge pricing — recalculated every 2 minutes |
| **HDFC Bank** | Monthly customer risk scoring, quarterly regulatory reports | Real-time fraud detection — every card swipe analysed in <100ms |
| **Flipkart** | Nightly product catalog updates, weekly sales analytics | Real-time inventory updates — when item is sold, stock count decreases instantly |
| **Zomato** | Daily restaurant rating recalculation | Real-time order tracking — driver location updated every 5 seconds |

### Batch Ingestion Considerations

When building batch pipelines, there are several practical decisions you need to make about **how** you extract and load data in bulk.

#### Snapshot vs Differential Extraction

There are two ways to extract data from a source in batch mode:

| Approach | How It Works | Pros | Cons |
|---|---|---|---|
| **Snapshot (Full Extract)** | Copy the **entire** table/dataset every time | Simple, guaranteed complete data | Slow and expensive for large datasets |
| **Differential (Incremental Extract)** | Copy only **rows that changed** since the last extract | Fast, efficient, low resource usage | Complex — need to track what changed (timestamps, CDC logs) |

> **Analogy:** Snapshot is like photocopying an entire book every time someone edits one page. Differential is like only photocopying the pages that were edited.

**Live Example — Zerodha (stock trading):**
- **Snapshot:** Every night, export the complete holdings table for all users (~10 million users × their portfolios). Simple but takes 2+ hours.
- **Differential:** Only export trades that happened today (changes). Much faster (~15 minutes), but need to carefully merge with yesterday's data.

> **When to use which:** Start with snapshot for small datasets. Switch to differential when data grows large enough that full copies are too slow or expensive.

#### File-Based Export and Ingestion

In many batch pipelines, data is **serialized into files** (CSV, JSON, Parquet, Avro) and then those files are transferred between systems.

```
Source Database → Export to Parquet files → Upload to S3 → Load into Warehouse
```

Common file formats for batch ingestion:

| Format | Type | Best For |
|---|---|---|
| **CSV** | Text, row-based | Simple data, small files, human-readable |
| **JSON** | Text, semi-structured | Nested data, API responses |
| **Parquet** | Binary, columnar | Large analytics datasets, fast queries |
| **Avro** | Binary, row-based | Data with evolving schemas, Kafka integration |
| **ORC** | Binary, columnar | Hive/Hadoop ecosystem |

> **Tip:** For AI/ML workloads, **Parquet** is the go-to format — it's compressed, columnar (fast for analytics), and works with every major tool (Spark, Pandas, BigQuery).

#### Inserts, Updates, and Batch Size

When loading data in batches, **how much data you send in one go** matters a lot:

```
Slow (many small operations):
INSERT row 1 → commit
INSERT row 2 → commit        ← Each operation has overhead (network, disk, locking)
INSERT row 3 → commit
... × 1 million rows = very slow

Fast (large batch):
INSERT rows 1 to 10,000 → commit
INSERT rows 10,001 to 20,000 → commit    ← Fewer operations, less overhead
... × 100 batches = much faster
```

**Rule of thumb:** Large batches perform better than many small operations. But batches that are **too** large can cause memory issues or lock tables for too long.

| Batch Size | Tradeoff |
|---|---|
| Too small (1–100 rows) | High overhead per row, very slow |
| Sweet spot (1,000–100,000 rows) | Good balance of speed and memory |
| Too large (10M+ rows in one go) | May cause out-of-memory, long table locks |

#### Data Migration Considerations

Data migration is the one-time (or infrequent) process of moving data from an old system to a new one. It's different from regular pipelines because:

- **Volume is massive** — you're moving everything, not just incremental changes
- **Downtime matters** — the old system may need to be offline during migration
- **Schema differences** — old and new systems rarely have the same schema
- **Validation is critical** — you must verify every record made it across correctly

> **Live Example — ICICI Bank migrating from legacy mainframe to cloud:**
> 1. Map old schema → new schema (e.g., old system stores dates as YYYYMMDD integer, new system uses ISO date format)
> 2. Run migration in batches (accounts A-D first, then E-H, etc.)
> 3. Validate: row counts match, checksums match, sample records verified
> 4. Run old and new systems in parallel for a week (dual-write)
> 5. Cut over to new system only after validation passes

#### Schema Evolution and Late-Arriving Data

In the real world, data schemas **change over time**, and data doesn't always arrive on schedule.

**Schema Evolution:** The structure of your data changes as the application evolves.

```
Version 1 (Jan 2025): orders table has columns [order_id, user_id, amount, date]
Version 2 (Mar 2025): added column [delivery_type] — "express" or "standard"
Version 3 (Jun 2025): renamed [amount] to [total_amount], added [discount]
```

Your pipeline must handle all three versions without breaking. Solutions:
- Use **schema-flexible formats** like Avro (stores schema with data)
- Maintain a **schema registry** that tracks all versions
- Write transform logic that handles missing/renamed columns gracefully

**Late-Arriving Data:** Data that shows up after the batch window has closed.

> **Analogy:** Your class attendance register closes at 9:00 AM. A student walks in at 9:15 AM. Do you mark them absent, or reopen the register?

```
Batch window: Process all orders from September 7
Batch runs at: September 8, 2:00 AM
Late arrival: An order from September 7 shows up at September 8, 10:00 AM
              (maybe the payment gateway was slow to confirm)

Problem: This order is missing from yesterday's batch!
```

Solutions:
- **Reprocessing:** Re-run yesterday's batch to include late data (simple but expensive)
- **Late-arrival window:** Keep the batch open for an extra buffer period (e.g., 6 hours)
- **Correction batches:** Run a small "catch-up" batch that only processes late arrivals

---

### Lambda Architecture — "Use Both"

Most real-world systems use **both** batch and streaming. This is called **Lambda Architecture:**

```
                    ┌──────────────────────┐
                    │   STREAMING LAYER    │
    Events ────────→│   (real-time, fast,  │──→ Real-time View
       │            │    approximate)      │   (latest few hours)
       │            └──────────────────────┘
       │
       │            ┌──────────────────────┐
       └───────────→│    BATCH LAYER       │──→ Batch View
                    │   (slow, accurate,   │   (complete history)
                    │    all historical)   │
                    └──────────────────────┘
                              ↓
                    ┌──────────────────────┐
                    │    SERVING LAYER     │──→ User Query
                    │  (merge both views)  │   (best of both)
                    └──────────────────────┘
```

> **Live Example — Swiggy's Lambda Architecture:**
> - **Streaming:** ETA prediction uses real-time traffic, driver location, restaurant load
> - **Batch:** Nightly model retraining uses all historical delivery data
> - **Serving:** When you place an order, the system combines the real-time model with historical averages for the most accurate prediction

### Streaming Ingestion Concepts

When working with streaming pipelines, there are several important concepts beyond just "process events in real-time."

#### Ordering and Multiple Delivery

In a streaming system, **two things can go wrong with message delivery:**

**1. Out-of-Order Events:** Events may not arrive in the order they happened.

```
Real-world order:      What Kafka receives:
9:00:01 — User clicks  9:00:01 — User clicks
9:00:02 — User adds    9:00:03 — User pays     ← arrived before "add to cart"!
9:00:03 — User pays    9:00:02 — User adds      ← late arrival
```

> **Why?** Different events travel through different network paths, servers, or partitions. The payment event might travel through a faster server than the cart event.

**2. Multiple Delivery:** The same event may be delivered **more than once.**

```
Producer sends event → Broker receives it → Broker sends ACK back
                                            ↑
                            ACK gets lost in network!

Producer thinks: "Broker didn't get it, let me resend"
Broker now has: [event, event]  ← duplicate!
```

**Delivery guarantees** — three levels:

| Guarantee | Meaning | Risk |
|---|---|---|
| **At-most-once** | Send and forget. Message may be lost. | Missing data |
| **At-least-once** | Retry until acknowledged. Message may be duplicated. | Duplicate data |
| **Exactly-once** | Each message processed exactly one time. | No risk, but hardest to implement |

> **Live Example — Paytm:** For payment transactions, they use **exactly-once** semantics. If a Rs 500 payment event is duplicated, the user would be charged twice — unacceptable. Kafka's exactly-once feature + idempotent writes to the database prevent this.

#### Replay Capability

**Replay** is the ability to re-read events that were already consumed from the stream.

> **Analogy:** A TV show's "replay" button. Even though the show already aired, you can go back and watch it again. A stream with replay capability lets you go back and re-process events.

**Why is replay important?**
- **Bug fix:** You deployed a buggy consumer that miscounted transactions. After fixing the code, replay the last 7 days of events to recompute correct counts.
- **New consumer:** A new analytics team wants to process events from last month. With replay, they can read from the beginning.
- **Disaster recovery:** If your downstream database crashes, replay events to rebuild it.

```
Kafka topic: [event1] [event2] [event3] [event4] [event5]
                                          ↑
Consumer A: already read up to event4     │
Consumer B (new): starts from event1 ─────┘ (replay!)
```

> **Kafka supports replay** by retaining messages for a configurable duration (see TTL below). Consumers can "rewind" their read position to any point within the retention window.

#### Message Size Considerations

Streaming systems have limits on **how large each message can be:**

| System | Default Max Message Size |
|---|---|
| Apache Kafka | 1 MB per message |
| AWS Kinesis | 1 MB per record |
| Google Pub/Sub | 10 MB per message |
| RabbitMQ | No hard limit (but performance degrades above ~128 MB) |

**What if your data is larger?**
- **Compress** the message (gzip, snappy, lz4) — Kafka supports built-in compression
- **Split** the message into smaller chunks (but then you need to reassemble)
- **Reference pattern:** Store large data (image, video) in object storage (S3), send only the **reference URL** in the stream message

```
Instead of: { "event": "photo_uploaded", "image_data": "<5 MB binary>" }  ← Too big!
Do this:    { "event": "photo_uploaded", "image_url": "s3://bucket/photo123.jpg" }  ← Small!
```

#### TTL (Time to Live)

**TTL** determines **how long messages are retained** in the stream before being automatically deleted.

| System | Default Retention | Configurable? |
|---|---|---|
| Apache Kafka | 7 days | Yes — from hours to infinite |
| AWS Kinesis | 24 hours | Yes — up to 365 days |
| Google Pub/Sub | 7 days | Yes — up to 31 days |

> **Analogy:** TTL is like the expiry date on milk. After the TTL, the message is gone — no replay possible.

**Tradeoffs:**
- **Short TTL (hours):** Saves storage, but you lose the ability to replay old events
- **Long TTL (weeks/months):** Expensive storage, but great for replay and debugging
- **Infinite retention:** Kafka can keep messages forever — essentially turns a stream into a log database (used in event sourcing architectures)

#### Error Handling and Dead-Letter Queues (DLQ)

Not every message can be processed successfully. Some messages are **malformed, have invalid data, or trigger bugs** in your consumer code.

> **Analogy:** A postal service. If a letter has an invalid address, the postman doesn't throw it away — he sends it to a "dead letter office" where someone investigates it later.

```
Stream: [msg1] [msg2] [msg3-bad!] [msg4] [msg5]
                        ↓
Consumer tries to process msg3 → ERROR!

Without DLQ:
  Option A: Crash the consumer → all processing stops (terrible!)
  Option B: Skip the message → data is silently lost (bad!)

With DLQ:
  Send msg3 to Dead-Letter Queue → continue processing msg4, msg5
  Later: engineer investigates DLQ, fixes the issue, replays msg3
```

**DLQ workflow:**
1. Consumer attempts to process a message
2. If it fails after N retries (e.g., 3 attempts), send it to the DLQ
3. Set up alerts when DLQ receives messages
4. Engineer investigates, fixes the issue, replays failed messages

> **Live Example — Razorpay:** Payment webhook events that fail processing (e.g., merchant's server is down) are sent to a DLQ. An automated retry system re-attempts DLQ messages with exponential backoff (1 min, 5 min, 30 min). If still failing after 24 hours, an alert is raised for manual investigation.

#### Consumer Pull vs Push Model

There are two models for how consumers get messages from a stream:

| Model | How It Works | Pros | Cons |
|---|---|---|---|
| **Pull (Consumer Polls)** | Consumer asks: "Any new messages?" at regular intervals | Consumer controls its own pace, won't get overwhelmed | Slight delay (polling interval), wasted calls if no data |
| **Push (Broker Sends)** | Broker pushes messages to consumer as soon as they arrive | Lowest latency, no wasted calls | Can overwhelm slow consumers, harder to implement back-pressure |

```
PULL MODEL (Kafka uses this):
Consumer → "Any new messages?" → Broker → "Yes, here are 100 messages"
Consumer → processes them → "Any new messages?" → Broker → "Nothing new"

PUSH MODEL (Webhooks, Google Pub/Sub):
Broker → "Here's a message!" → Consumer
Broker → "Here's another!" → Consumer
Broker → "And another!" → Consumer (consumer getting overwhelmed? Too bad!)
```

> **Why Kafka chose Pull:** Consumers can read at their own speed. A slow consumer won't get buried under a flood of messages. Each consumer tracks its own position (offset) in the stream.

> **Why Push is useful:** For event-driven architectures where you want minimum latency. Google Pub/Sub supports push delivery to HTTP endpoints — great for serverless functions (Cloud Functions).

### Data Ingestion Tools and Challenges

#### Data Ingestion vs Data Integration

These two terms sound similar but mean different things:

| Concept | What It Means | Analogy |
|---|---|---|
| **Data Ingestion** | Moving data from point A to point B | Shipping boxes from a factory to a warehouse |
| **Data Integration** | Combining data from multiple sources into a unified, consistent view | Unpacking all boxes, sorting items, and organizing them on shelves so everything makes sense together |

```
Ingestion: Database A → copy data → Data Lake    (just moving)
Integration: Database A + API B + CSV C → clean, merge, resolve conflicts → Unified Dataset
```

> **Example:** Ingestion is downloading transaction data from HDFC Bank's API into your data lake. Integration is combining that with UPI data from PhonePe, credit card data from SBI, and resolving that "Hinal P" in one system is the same person as "Hinal Prajapati" in another.

#### Data Ingestion Challenges

| Challenge | What Goes Wrong | Example |
|---|---|---|
| **Volume** | Too much data to move efficiently | Hotstar streaming 500 TB of viewing data per day during IPL |
| **Velocity** | Data arriving too fast to keep up | Zerodha handling 15 million orders on a volatile market day |
| **Variety** | Data in many different formats | Flipkart ingesting structured product data + unstructured review text + semi-structured JSON logs |
| **Data Quality** | Source data has errors, duplicates, missing values | 15% of addresses in delivery data are incomplete or misspelled |
| **Schema Changes** | Source systems change their data format without warning | Payment gateway adds a new field "upi_id" — your pipeline breaks because it doesn't expect this column |

#### Types of Data Ingestion Tools

| Category | Tools | Best For |
|---|---|---|
| **Batch Ingestion** | Apache Sqoop, Airbyte, Apache NiFi | Moving large volumes on schedule (nightly, hourly) |
| **Streaming Ingestion** | Apache Kafka, AWS Kinesis, Apache Pulsar | Real-time event data, IoT sensor data |
| **Managed/SaaS** | Fivetran, Stitch, Hevo Data | Minimal setup — connect source, data flows automatically |
| **CDC (Change Data Capture)** | Debezium, AWS DMS, Oracle GoldenGate | Capturing real-time changes from databases without heavy queries |

> **Tip for choosing:**
> - **Startup with small data?** Use Fivetran or Airbyte — low effort, works out of the box
> - **Large scale, need control?** Use Kafka + custom consumers — maximum flexibility
> - **Need to replicate a database?** Use Debezium (CDC) — captures every INSERT, UPDATE, DELETE

#### Key Considerations for Data Ingestion

| Consideration | Why It Matters |
|---|---|
| **Reliability** | Data loss is unacceptable in payment/financial systems. Need at-least-once or exactly-once guarantees. |
| **Scalability** | Your pipeline should handle 10x traffic spikes (e.g., Diwali sale) without redesigning. |
| **Security** | Data in transit must be encrypted (TLS). Sensitive data (PII) needs masking or tokenization. |
| **Monitoring** | You need to know when a pipeline fails, when it's slow, or when data quality drops. Alerts are essential. |
| **Idempotency** | If you re-run the pipeline, it should produce the same result — no duplicates, no data corruption. |

---

## 6.4 Storage Types Deep Dive

Before we talk about data lakes and warehouses, let's understand the **fundamental storage types** that underpin all of them.

### Raw Ingredients of Data Storage

The raw ingredients underlying all storage systems are physical hardware components. Every database, data lake, and feature store ultimately reads and writes to these:

| Component | What It Does | Speed | Cost | Use In AI/ML |
|---|---|---|---|---|
| **HDD (Hard Disk Drive)** | Spinning magnetic disks. Stores data cheaply in bulk. | Slow (100-200 MB/s) | Very cheap (~$0.02/GB) | Bulk archival storage, cold data in data lakes |
| **SSD (Solid State Drive)** | Flash memory chips. No moving parts. | Fast (500-5000 MB/s) | Moderate (~$0.10/GB) | Databases, feature stores, model serving |
| **RAM (Memory)** | Volatile memory. Data lost when power is off. | Very fast (25-50 GB/s) | Expensive (~$5/GB) | Caching (Redis), in-memory computation (Spark), real-time features |
| **Networking** | Moves data between machines over Ethernet, fibre, or InfiniBand. | Variable (1-100 Gbps) | Depends on infrastructure | Distributed storage, streaming (Kafka), cloud storage access |

> **Key insight:** Higher-level systems (file storage, block storage, object storage, streaming storage) are all **abstractions built on top of these four physical components**. Understanding the raw layer helps you understand why some systems are fast but expensive (RAM-based caches) while others are slow but cheap (HDD-based data lakes).

### Data Storage Abstractions — Lake, Warehouse, Lakehouse

Storage abstractions sit above the raw storage types and provide higher-level ways to organise and query data:

| Abstraction | What It Does | Stores | Built On Top Of | Best For |
|---|---|---|---|---|
| **Data Lake** | Stores everything in raw format. No schema required when writing. | Any data — structured, semi-structured, unstructured | Object storage (S3, GCS, Azure Blob) | ML/AI workloads, keeping raw data for future use |
| **Data Warehouse** | Stores cleaned, structured data optimised for fast SQL queries. | Structured data only (tables with rows and columns) | Block storage + columnar formats | BI dashboards, business reports, analytics |
| **Data Lakehouse** | Combines the flexibility of a lake with the query speed of a warehouse. | Any data, with optional schema enforcement | Object storage + table formats (Delta Lake, Iceberg) | Unified ML + BI — one platform for data scientists and analysts |

> **Think of it this way:** Raw storage (HDD/SSD/RAM) → Storage types (file/block/object) → Storage abstractions (lake/warehouse/lakehouse). Each layer adds more structure and functionality on top of the layer below.

### Storage Layering Concept

Modern data systems use **multiple layers of storage**, each optimised for different access patterns:

```
┌─────────────────────────────────────┐
│          APPLICATION LAYER          │  ← Databases, Feature Stores
├─────────────────────────────────────┤
│         LOGICAL STORAGE LAYER       │  ← Tables, Collections, Topics
├─────────────────────────────────────┤
│        PHYSICAL STORAGE LAYER       │  ← Files, Blocks, Objects
├─────────────────────────────────────┤
│           HARDWARE LAYER            │  ← SSD, HDD, RAM, Network
└─────────────────────────────────────┘
```

> **Analogy:** Think of a library. The **hardware** is the building and shelves. The **physical layer** is how books are arranged on shelves (by size? by colour?). The **logical layer** is the cataloguing system (Dewey Decimal). The **application layer** is the library website where you search and borrow books.

### File Storage

Data is stored as **files organised in a directory hierarchy** — just like files and folders on your laptop.

```
/data/
├── raw/
│   ├── orders_2025_09_07.csv
│   └── orders_2025_09_08.csv
├── processed/
│   └── user_features.parquet
└── models/
    └── recommendation_v3.pkl
```

| Protocol | Full Name | Common Use |
|---|---|---|
| **NFS** | Network File System | Linux/Unix file sharing over network |
| **SMB** | Server Message Block | Windows file sharing |
| **NAS** | Network Attached Storage | Dedicated file server appliance |

**Best for:** Shared file access, legacy applications, log files, configuration files.

**Limitations:** Doesn't scale well to petabytes. Slow for random access to tiny pieces of data.

### Block Storage

Data is stored as **fixed-size blocks** (typically 512 bytes to 4 KB each). The storage system doesn't know or care about the content — it just stores and retrieves blocks by address.

> **Analogy:** A self-storage facility with identical numbered lockers. You can put anything in locker #47 — the facility doesn't know what's inside, it just lets you access locker #47 quickly.

| Technology | Provider/Type | Common Use |
|---|---|---|
| **EBS** | Amazon Elastic Block Store | Disk volumes for EC2 instances |
| **SAN** | Storage Area Network | Enterprise data centre storage |
| **iSCSI** | Internet SCSI protocol | Block storage over IP network |
| **Azure Managed Disks** | Microsoft Azure | Disk volumes for Azure VMs |

**Best for:** Databases (MySQL, PostgreSQL, MongoDB) — they need raw, fast block-level access to manage their own data layout.

**Why databases love block storage:** Databases need precise control over how data is written to disk. Block storage lets them write exactly where they want, enabling optimisations like B-tree indexes and write-ahead logs.

### Object Storage

Data is stored as **objects** — each object contains the data itself, metadata (tags, timestamps, custom properties), and a **unique identifier**.

```
Object:
├── Key (unique ID): "raw/orders/2025-09-07/chunk_001.parquet"
├── Data: <actual file content — 256 MB>
├── Metadata:
│   ├── Content-Type: application/parquet
│   ├── Created: 2025-09-07T02:00:00Z
│   ├── Owner: data-pipeline-prod
│   └── Retention: 90 days
└── No directory structure — flat namespace (folders are just key prefixes)
```

| Service | Provider | Free Tier |
|---|---|---|
| **Amazon S3** | AWS | 5 GB for 12 months |
| **Google Cloud Storage** | GCP | 5 GB always free |
| **Azure Blob Storage** | Microsoft | 5 GB for 12 months |
| **MinIO** | Open source | Self-hosted, free |

**Best for:** Data lakes, backups, ML training data, images, videos — anything large and unstructured.

**Why object storage dominates AI/ML:** It's incredibly cheap (~$0.023/GB/month for S3), virtually unlimited, and all major ML frameworks (Spark, PyTorch, TensorFlow) can read directly from it.

### Cache and Memory-Based Storage

Data is stored **in RAM** for ultra-fast access — typically microsecond to millisecond latency.

| Tool | Type | Typical Use |
|---|---|---|
| **Redis** | In-memory key-value store | Feature store, session cache, real-time counters |
| **Memcached** | In-memory key-value cache | Simple caching (query results, API responses) |
| **Apache Ignite** | In-memory computing platform | Distributed caching, in-memory SQL |

```
Without cache:
User request → Application → Query Database (50ms) → Return result
                              ↑ slow disk access

With cache (Redis):
User request → Application → Check Redis (0.5ms) → HIT! Return result
                              ↑ data is in RAM          ↑ 100x faster

Cache MISS:
User request → Application → Check Redis → MISS → Query Database → Store in Redis → Return
```

**Best for:** Feature stores serving real-time ML predictions, session data, leaderboards, rate limiting.

> **Live Example — Dream11:** During an IPL match, millions of users check their fantasy scores simultaneously. Player statistics and scores are cached in Redis. Without cache, the database would collapse under load.

### HDFS (Hadoop Distributed File System)

**HDFS** distributes files across a **cluster of commodity machines**, providing fault tolerance and high throughput for big data workloads.

```
File: user_activity.parquet (900 MB)

HDFS splits it into blocks (default 128 MB each):
Block 1 (128 MB) → stored on Node A + Node C (replica)
Block 2 (128 MB) → stored on Node B + Node D (replica)
Block 3 (128 MB) → stored on Node C + Node A (replica)
Block 4 (128 MB) → stored on Node D + Node B (replica)
...

NameNode (master): knows which blocks are on which nodes
DataNodes (workers): actually store the blocks
```

**Key properties:**
- **Fault tolerant:** Each block is replicated (default: 3 copies). If a node dies, data is still safe.
- **High throughput:** Read from multiple nodes in parallel — great for Spark/MapReduce jobs.
- **Not good for:** Small files, random reads, low-latency access.

> **Trend:** Many companies are moving from HDFS to cloud object storage (S3) because S3 is cheaper, requires no cluster management, and Spark can read from S3 directly.

### Streaming Storage

Streaming systems like Kafka and Kinesis have their **own storage** that's different from traditional storage.

| System | How It Stores Data | Retention |
|---|---|---|
| **Apache Kafka** | Append-only log files on disk, partitioned by topic | Configurable (hours to forever) |
| **AWS Kinesis** | Managed shards, each shard stores ordered sequence of records | 24 hours to 365 days |
| **Apache Pulsar** | Separated storage (Apache BookKeeper) from compute | Configurable, tiered storage support |

```
Kafka Topic: "order-events"
├── Partition 0: [msg0, msg1, msg2, msg3, ...]  → stored on Broker 1
├── Partition 1: [msg0, msg1, msg2, msg3, ...]  → stored on Broker 2
└── Partition 2: [msg0, msg1, msg2, msg3, ...]  → stored on Broker 3

Each partition is an ordered, append-only log.
Consumers read by tracking their "offset" (position) in each partition.
```

**Best for:** Real-time event data that needs to be consumed by multiple systems (analytics, ML, alerting) independently.

### Single Machine vs Distributed Storage

| Aspect | Single Machine | Distributed Storage |
|---|---|---|
| **Capacity** | Limited by one machine's disks (typically 1–16 TB) | Virtually unlimited (add more nodes) |
| **Fault tolerance** | If the disk fails, data is lost | Data replicated across nodes — survives failures |
| **Performance** | Limited by one machine's I/O | Parallel reads/writes across many nodes |
| **Cost** | Low upfront | Higher upfront, but cost-effective at scale |
| **Complexity** | Simple — it's just one machine | Complex — need to handle network partitions, replication, consistency |
| **Examples** | Local SSD, single PostgreSQL | HDFS, Amazon S3, Cassandra, Kafka |

> **When to go distributed:** When your data exceeds a single machine's capacity, when you need fault tolerance (production systems), or when you need high throughput (big data processing).

---

## 6.5 Data Lakes and Data Warehouses

### Data Warehouse — "A clean, organised library"

A **data warehouse** stores **cleaned, structured, and organised** data optimised for querying and reporting.

> **Analogy:** A library where every book is catalogued, shelved in the right section, and indexed. You can easily find any book. But you can only store books that follow the library's format — no random handwritten notes allowed.

| Characteristic | Details |
|---|---|
| **Data type** | Structured only (tables with rows and columns) |
| **Schema** | Schema-on-write (data must conform to schema BEFORE being loaded) |
| **Optimised for** | Fast SQL queries, analytics, dashboards |
| **Users** | Business analysts, data analysts |
| **Storage cost** | Higher (stored in optimised columnar format) |
| **Examples** | **Snowflake**, **Google BigQuery**, **Amazon Redshift**, **Azure Synapse** |

### Data Lake — "A huge storage dump for everything"

A **data lake** stores **raw data of any type** (structured, semi-structured, unstructured) without requiring a predefined schema.

> **Analogy:** A warehouse where you dump everything — boxes, loose items, furniture, documents. It's cheap and you can store anything. But finding something specific requires rummaging through piles of stuff.

| Characteristic | Details |
|---|---|
| **Data type** | Any — structured, semi-structured, unstructured |
| **Schema** | Schema-on-read (data is stored raw; schema applied when you query it) |
| **Optimised for** | Cheap storage, flexibility, ML/AI workloads |
| **Users** | Data engineers, data scientists, ML engineers |
| **Storage cost** | Very low (plain files in cloud storage) |
| **Examples** | **Amazon S3**, **Google Cloud Storage**, **Azure Data Lake Storage** |

### Data Lakehouse — "Best of both worlds" (Modern Approach)

A **data lakehouse** combines the cheap, flexible storage of a data lake with the query performance of a data warehouse.

> **Analogy:** An organised warehouse with a searchable catalog system — you can dump anything in, but you can also quickly find and query it.

| Characteristic | Details |
|---|---|
| **Data type** | Any type (like a lake) |
| **Query performance** | Fast SQL queries (like a warehouse) |
| **Schema** | Flexible — supports both schema-on-write and schema-on-read |
| **Examples** | **Databricks Lakehouse**, **Apache Iceberg + Spark**, **Delta Lake** |

### Comparison Table

| Aspect | Data Warehouse | Data Lake | Data Lakehouse |
|---|---|---|---|
| **Data types** | Structured only | Any type | Any type |
| **Schema** | Schema-on-write (rigid) | Schema-on-read (flexible) | Both |
| **Storage cost** | High | Very low | Low |
| **Query speed** | Very fast (optimised) | Slow (raw files) | Fast |
| **Best for** | BI dashboards, reports | ML/AI, raw data archive | ML + BI (unified) |
| **Users** | Business analysts | Data scientists | Everyone |
| **Risk** | Can't store unstructured data | "Data swamp" if not managed | Newer, less mature |

### Live Examples

| Company | Storage Approach | Why |
|---|---|---|
| **Flipkart** | Data Lake (S3) + Data Warehouse (Redshift) | Raw clickstream in S3 for ML. Aggregated sales in Redshift for dashboards. |
| **Netflix** | Data Lake (S3) + processing with Spark | Stores everything raw (viewing events, server logs, A/B test data). Processes with Spark for ML. |
| **CRED** | Data Lakehouse (Databricks) | Unified platform — data scientists and analysts both query the same data |
| **Uber** | Data Lake (HDFS/S3) + real-time (Apache Kafka/Flink) | Massive scale — millions of rides, GPS pings, transactions daily |

### What is a "Data Swamp"?

A data lake without governance becomes a **data swamp** — data is dumped in but nobody knows what's in there, who owns it, or whether it's still accurate.

```
Data Lake (well-managed):
├── /raw/orders/2025-09-07/       ← Dated, organized
├── /processed/user_features/      ← Clear naming
├── /models/recommendation_v3/     ← Versioned
└── README.md with data catalog    ← Documented

Data Swamp (poorly managed):
├── data_final.csv
├── data_final_v2.csv
├── data_final_FINAL.csv          ← Which one is actual final?
├── test123.parquet               ← What is this?
├── johns_experiment/             ← John left 2 years ago
└── (no documentation anywhere)
```

> **Lesson:** A data lake needs governance (catalog, ownership, retention policies) to avoid becoming a data swamp.

---

## 6.6 Enterprise Data Pipeline — Case Study

### Case Study: Building a Real-Time Recommendation Pipeline for Myntra

Let's design a complete data pipeline for Myntra (fashion e-commerce) to power real-time product recommendations.

### Requirements

- Show personalised "Recommended for You" products on the homepage
- Update recommendations in real-time as the user browses
- Train recommendation model daily with fresh data
- Handle 50 million daily active users

### Architecture

```
━━━ DATA SOURCES ━━━
├── User activity (clicks, views, purchases, wishlists)
├── Product catalog (images, descriptions, prices, categories)
├── Inventory system (stock levels per warehouse)
├── Weather API (seasonal fashion depends on weather!)
└── Social trends (trending styles from social media)

━━━ STREAMING PIPELINE (Real-time) ━━━
├── Apache Kafka ingests all user events in real-time
├── Apache Flink processes events:
│   ├── Compute: "user viewed 5 dresses in last 10 minutes"
│   ├── Compute: "user's price range today: Rs 500-2000"  
│   └── Update real-time user profile in Feature Store
├── Latency: < 500ms from event to feature update
└── Output: Real-time features in Redis (Feature Store)

━━━ BATCH PIPELINE (Nightly) ━━━
├── Apache Airflow orchestrates nightly jobs at 2 AM:
│   ├── Job 1: Export yesterday's events from Kafka to S3 (data lake)
│   ├── Job 2: Spark processes all historical data:
│   │   ├── Compute user-level aggregations (last 30 days)
│   │   ├── Compute product-level statistics (popularity, return rate)
│   │   └── Generate training dataset for recommendation model
│   ├── Job 3: Train new recommendation model
│   │   ├── Input: training dataset + product embeddings
│   │   ├── Model: two-tower neural network (user tower + item tower)
│   │   └── Output: trained model registered in MLflow
│   ├── Job 4: Evaluate model on test set
│   │   └── If accuracy > threshold → promote to production
│   └── Job 5: Update batch features in Feature Store
├── Duration: ~3 hours (2 AM to 5 AM)
└── Output: Fresh model + updated batch features

━━━ SERVING PIPELINE ━━━
├── User opens Myntra app
├── API Gateway receives request (user_id, context)
├── Feature Store serves:
│   ├── Batch features: user's last-30-day preferences
│   └── Real-time features: user's current session activity
├── Model Server computes recommendation scores
├── Business logic:
│   ├── Remove out-of-stock items
│   ├── Apply diversity rules (don't show 10 similar dresses)
│   ├── Boost items on sale
│   └── A/B test: 10% of users see experimental model
├── Return top 20 products to app
└── Total latency: < 200ms

━━━ MONITORING ━━━
├── Track: click-through rate on recommendations
├── Track: recommendation → purchase conversion rate
├── Alert: if click-through rate drops 20% below baseline
├── Alert: if model serving latency exceeds 500ms
├── Data quality: check daily for missing/corrupt data
└── Data drift: compare this week's user behaviour to training data
```

### Pipeline Tools Used

| Component | Tool | Why This Tool |
|---|---|---|
| Event streaming | Apache Kafka | Industry standard for real-time event streaming. Handles millions of events/second. |
| Stream processing | Apache Flink | Real-time computation with exactly-once processing guarantees. |
| Batch orchestration | Apache Airflow | Schedule and monitor complex batch workflows. Open-source, widely used. |
| Batch processing | Apache Spark | Process terabytes of data in the data lake efficiently. |
| Data lake | Amazon S3 | Cheap, durable, unlimited storage. |
| Feature store | Feast + Redis | Feast manages feature definitions. Redis serves features with <1ms latency. |
| Model training | PyTorch + MLflow | PyTorch for model code. MLflow for experiment tracking and model registry. |
| Model serving | TorchServe on Kubernetes | Serve model via API. K8s auto-scales based on traffic. |
| Monitoring | Prometheus + Grafana | Prometheus collects metrics. Grafana visualises dashboards. |
| Data quality | Great Expectations | Automated data quality checks with clear expectations. |

### Key Takeaways from This Case Study

1. **Two pipelines (batch + streaming)** — batch for thorough processing, streaming for real-time freshness
2. **Feature store bridges both** — batch features (historical) + real-time features (current session) served together
3. **Automation is key** — Airflow orchestrates nightly jobs without human intervention
4. **Monitoring closes the loop** — if recommendations degrade, alerts fire before users complain
5. **Business logic matters** — the model's output is just scores; business rules make them useful (remove out-of-stock, diversify, boost promotions)

---

## 6.7 Data Validation

### Why Data Validation Matters in ML Pipelines

Bad data in = bad model out. **Data validation** is the process of automatically checking incoming data for correctness before it enters your pipeline.

> **Analogy:** Airport security screening. Before passengers (data) board the plane (enter your ML pipeline), they go through metal detectors and baggage scans (validation checks). One bad passenger (corrupt data) can bring down the entire flight (model).

Without validation, these nightmares happen:
- A column of prices accidentally contains negative values → model learns that "negative price = high demand"
- A data source changes its date format from DD/MM/YYYY to MM/DD/YYYY → 5th January becomes "January is the 5th month"
- A CSV export has a missing header → all columns shift left by one

### Unit-Test Approach for Incoming Data

Just like software engineers write unit tests for code, data engineers write **"data unit tests"** for incoming data:

```python
# Data unit tests — run these on every new batch of data

def test_schema():
    """Check that the data has the expected columns"""
    assert set(df.columns) == {"user_id", "amount", "timestamp", "product_id"}

def test_no_nulls_in_key_columns():
    """Key columns should never be null"""
    assert df["user_id"].isnull().sum() == 0
    assert df["amount"].isnull().sum() == 0

def test_amount_range():
    """Order amounts should be between Rs 1 and Rs 10,00,000"""
    assert df["amount"].min() >= 1
    assert df["amount"].max() <= 1_000_000

def test_no_future_timestamps():
    """Timestamps should not be in the future"""
    assert df["timestamp"].max() <= datetime.now()

def test_row_count():
    """Daily batch should have at least 10,000 orders (sanity check)"""
    assert len(df) >= 10_000
```

### Amazon's Approach: Deequ Library

**Deequ** is an open-source data quality library built by Amazon, designed to run on **Apache Spark**.

**Philosophy:** Define constraints (rules) for your data, and Deequ checks every batch against these constraints.

```
Deequ Constraints (like unit tests for data):
├── "column 'user_id' is never NULL"
├── "column 'amount' is always positive"
├── "column 'email' matches regex pattern *@*.*"
├── "column 'country' values are in set {IN, US, UK, SG}"
├── "column 'amount' has mean between 500 and 5000"
└── "total row count is at least 10,000"
```

**How it works:**
1. Define constraints using Deequ's API
2. Run Deequ on each new batch of data (as a Spark job)
3. Deequ reports which constraints passed and which failed
4. If critical constraints fail → block the pipeline, send alert
5. If non-critical constraints fail → log warning, continue pipeline

> **Live Example — Amazon Retail:** Before new product catalog data enters the recommendation pipeline, Deequ checks: Are all product IDs unique? Are prices positive? Are all required fields (title, category, image_url) present? If checks fail, the pipeline halts and an engineer is alerted.

### Google's Approach: TensorFlow Data Validation (TFDV)

**TFDV** takes a different approach — instead of manually writing constraints, it **automatically generates a schema** from your training data, then validates new data against that schema.

```
Step 1: TFDV analyses your training data
        → Generates schema:
          {
            "user_age": { type: INT, min: 18, max: 120, not_null: true },
            "purchase_amount": { type: FLOAT, mean: 1500, std: 800 },
            "category": { type: STRING, values: ["electronics", "fashion", "food"] }
          }

Step 2: New data arrives
        → TFDV validates against schema
        → "ANOMALY: 'category' has new value 'automotive' not in schema!"
        → "ANOMALY: 'user_age' has minimum -5, expected >= 18!"
```

**Key feature: Automatic schema inference.** You don't need to manually specify every constraint — TFDV learns what "normal" looks like from your training data and flags anything that deviates.

### Deequ vs TFDV Comparison

| Aspect | Deequ (Amazon) | TFDV (Google) |
|---|---|---|
| **Approach** | Constraint-based (you write the rules) | Schema-based (auto-generated from data) |
| **Setup effort** | More effort (must define each constraint) | Less effort (auto-generates schema) |
| **Flexibility** | Very flexible — any custom check | Limited to schema-level checks |
| **Ecosystem** | Apache Spark | TensorFlow / TFX pipeline |
| **Language** | Scala / Java (with Python wrapper) | Python |
| **Best for** | General data quality in data warehouses | ML pipeline data validation |
| **Handles drift** | Manual (you set thresholds) | Automatic (detects distribution changes) |

> **Which to use?** If you're in a Spark-based data pipeline, use Deequ. If you're in a TensorFlow ML pipeline, use TFDV. For other ML frameworks (PyTorch), consider **Great Expectations** — a popular Python-native alternative.

---

## 6.8 Data Drift, Skew, and Monitoring

### Why Does Data Change?

You trained your ML model on data from January. It's now September. The world has changed — customer behavior has shifted, new products exist, economic conditions are different. **If your model still thinks it's January, its predictions will degrade.**

> **Analogy:** Imagine studying for an exam using last year's syllabus. The syllabus changed this year — some topics were removed, new ones were added. Your preparation (model) is based on outdated information (training data).

### Types of Data Drift and Skew

#### 1. Data Drift (Feature Drift)

**The distribution of your input features changes over time.**

```
Training data (Jan 2025):              Production data (Sep 2025):
Average order value: Rs 800            Average order value: Rs 1,200  ← shifted!
Age distribution: 60% are 18-30        Age distribution: 45% are 18-30  ← shifted!
Top category: Electronics (40%)        Top category: Fashion (35%)  ← shifted!
```

> **Live Example — Swiggy:** During COVID lockdowns, user behavior on Swiggy shifted dramatically — more grocery orders, fewer restaurant orders, different peak hours. Models trained on pre-COVID data gave terrible predictions.

#### 2. Schema Skew

**The schema (structure) of incoming data changes unexpectedly.**

```
Expected schema: [user_id: INT, amount: FLOAT, city: STRING]
Actual schema:   [user_id: STRING, amount: FLOAT, city: STRING, upi_id: STRING]
                  ↑ type changed!                                ↑ new column!
```

**Causes:** Source system upgrades, API version changes, new features added to the app.

**Impact:** Pipeline crashes, type conversion errors, missing columns in feature computation.

#### 3. Distribution Skew

**The statistical distribution of a specific feature changes significantly.**

```
Training:  "amount" follows normal distribution, mean=1000, std=300
Production: "amount" is bimodal — peak at 200 (small orders) and peak at 5000 (bulk orders)

The model never saw this bimodal pattern during training → predictions are unreliable
```

This can be detected using statistical tests:
- **KL Divergence** — measures how different two distributions are
- **KS Test (Kolmogorov-Smirnov)** — checks if two samples come from the same distribution
- **PSI (Population Stability Index)** — commonly used in banking/finance

#### 4. Concept Drift

**The relationship between input features and the target variable changes.** This is the most dangerous type of drift because your features might look the same, but the correct answer has changed.

```
Spam detection model:
Training (2023): spam emails = "Nigerian prince", "you won a lottery", "click here"
Production (2025): spam emails = AI-generated phishing that looks like genuine emails

The features (email text) look different now.
The concept of "what makes an email spam" has evolved.
Model accuracy drops from 98% to 75%.
```

> **Live Example — Credit scoring at Bajaj Finance:**
> - Training data (2023): income > Rs 50,000/month = low risk
> - Production (2025): A recession hits. People with Rs 50,000 income are now defaulting because expenses increased.
> - The **concept** of what "low risk" means has changed — same income, different risk.

#### 5. Training-Serving Skew

**Features computed during training differ from features computed during serving (production).**

```
TRAINING (offline, batch pipeline):
user_avg_spend = average of ALL historical orders (computed on full dataset)

SERVING (online, real-time):
user_avg_spend = average of last 30 days only (computed from feature store)

These are different values for the same feature!
The model was trained on "all-time average" but is serving predictions using "30-day average"
```

**Causes:**
- Different code paths for training vs serving
- Different data sources (training uses data warehouse, serving uses feature store)
- Timing differences (training data is stale, serving data is real-time)

**Solution:** Use a **feature store** (like Feast) that serves the same feature definitions for both training and serving.

### How to Monitor and Detect Drifts

| Method | What It Detects | How It Works |
|---|---|---|
| **Statistical tests** | Distribution drift | Compare production data distribution to training data distribution (KS test, PSI) |
| **Schema validation** | Schema skew | Check incoming data schema against expected schema (TFDV, Great Expectations) |
| **Model performance monitoring** | Concept drift | Track accuracy, precision, recall on production data. Drop = possible drift. |
| **Feature monitoring dashboards** | Feature drift | Plot feature distributions over time in Grafana. Visual anomalies = drift. |
| **Automated alerts** | All types | Set thresholds: "Alert if mean of 'amount' changes by more than 20% from training baseline" |

```
Monitoring Setup:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Production   │────→│  Monitoring  │────→│   Alerts     │
│ Predictions  │     │  Dashboard   │     │              │
│              │     │ (Grafana)    │     │ • Slack alert│
│ • features   │     │              │     │ • PagerDuty  │
│ • predictions│     │ • drift score│     │ • Email      │
│ • actuals    │     │ • accuracy   │     │              │
└──────────────┘     │ • latency    │     └──────────────┘
                     └──────────────┘
```

> **Best practice:** Don't just monitor model accuracy — monitor **input features** too. By the time accuracy drops, damage is already done. Feature monitoring catches drift early, before it impacts predictions.

---

## 6.9 Data Leakage

### What is Data Leakage?

**Data leakage** occurs when information from **outside the training dataset** is used to create the model — essentially, the model "cheats" by seeing information it shouldn't have access to.

> **Analogy:** Imagine a student who accidentally gets the answer key before the exam. They'll score 100% on the exam, but they haven't actually learned anything. When they face a new exam (production data), they'll fail miserably.

**Why it's dangerous:** A model with data leakage shows **amazing performance during training/testing** but **terrible performance in production**. It gives false confidence.

### Cause 1: Feature That Hides the Target

A feature in your training data **directly encodes the answer** you're trying to predict.

```
Task: Predict whether a loan will be approved

Training data:
| applicant_id | income | credit_score | approved_date | loan_approved |
|---|---|---|---|---|
| 101 | 50000 | 750 | 2025-03-15 | Yes |
| 102 | 30000 | 620 | NULL | No |
| 103 | 45000 | 700 | 2025-04-01 | Yes |

Problem: "approved_date" is only filled when the loan IS approved!
         The model learns: "if approved_date is not null → approve the loan"
         Accuracy on test set: 99.8% (amazing! ...but it's cheating)
```

> **The "approved_date" column IS the answer** — it only exists because the loan was approved. This information wouldn't be available at prediction time (you don't know the approval date before the loan is approved).

**More examples:**
- Predicting hospital readmission using "discharge_summary" — the summary mentions readmission risk
- Predicting customer churn using "cancellation_reason" — this field only exists after they've already churned
- Predicting fraud using "investigation_outcome" — this is filled in after the fraud investigation

### Cause 2: Feature From the Future

Using data that **wouldn't be available at the time of prediction** in production.

```
Task: Predict tomorrow's stock price

Training data:
| date | open_price | close_price | next_day_volume | tomorrow_price |
|---|---|---|---|---|
| Sep 7 | 100 | 105 | 50000 | 108 |
| Sep 8 | 105 | 108 | 60000 | 103 |

Problem: "next_day_volume" is data FROM THE FUTURE!
         On Sep 7, you don't know Sep 8's trading volume yet.
         But the training data has it because it was collected after the fact.
```

> **Rule of thumb:** For every feature, ask: "Would I have this data available at the exact moment I need to make a prediction?" If not, it's leakage.

**More examples:**
- Using average rating of a movie (which includes future reviews) to predict opening weekend performance
- Using a customer's lifetime value to predict whether they'll make their first purchase
- Using weather data from the same day to predict morning delivery times (morning prediction can't know afternoon weather)

### How to Detect and Prevent Data Leakage

| Strategy | How It Helps |
|---|---|
| **Temporal awareness** | Always split train/test by time. Never randomly shuffle time-series data. |
| **Feature audit** | For each feature, verify: "Is this available at prediction time?" |
| **Suspiciously high accuracy** | If your model suddenly achieves 99%+ accuracy, be suspicious — it might be cheaking. |
| **Feature importance analysis** | If one feature dominates (e.g., 90% importance), check if it's leaking the target. |
| **Simulate production** | Create a test set that exactly mimics production conditions — no future data, no target leakage. |
| **Peer review** | Have another data scientist review your feature engineering — fresh eyes catch leakage. |

```
Leakage Detection Checklist:
□ Is any feature derived from the target variable?
□ Is any feature unavailable at prediction time?
□ Is the train/test split respecting temporal order?
□ Are aggregations (averages, counts) computed only on past data?
□ Does any feature have suspiciously high correlation with the target?
```

> **Live Example — Jio Health:** A team built a patient readmission prediction model with 98% accuracy. On investigation, they found that one feature — "number_of_followup_appointments_scheduled" — was leaking. Patients who were going to be readmitted had more followups scheduled AFTER their condition worsened. Removing this feature dropped accuracy to 78% (real performance) — still useful, but honest.

---

## 6.10 Fairness and Bias in Data

### Why Fairness Matters for AI Systems

AI models learn patterns from data. If the data contains **biases** — historical discrimination, underrepresentation, or skewed sampling — the model will learn and **amplify** those biases.

> **This isn't a theoretical concern:** Amazon had to scrap an AI hiring tool because it penalised resumes containing the word "women's" (e.g., "women's chess club captain"). The model had been trained on 10 years of hiring data — a period when the tech industry hired predominantly men. The model learned that "male = hireable."

### Five Types of Bias (from Class Slides)

#### 1. Reporting Bias

**Data doesn't reflect real-world frequency** because unusual events are overreported and common events are underreported.

> **Analogy:** News channels report plane crashes but not safe landings. If you trained a model on news data, it would think most flights crash.

```
Example: Sentiment analysis model trained on product reviews
Reality: 90% of customers are satisfied, 10% are dissatisfied
Reviews: 30% positive, 70% negative  ← dissatisfied people are more likely to write reviews!

Model learns: "Most people dislike this product" (wrong — most people just didn't bother reviewing)
```

> **Live Example — Practo (health app):** If you train a symptom checker on online health forums, you'll overrepresent rare diseases (people with common colds don't post online, people with rare conditions do). The model would diagnose a headache as a brain tumor.

#### 2. Automation Bias

**Over-relying on AI output** without questioning it, even when it's wrong.

```
Scenario: A bank's loan approval AI recommends "reject" for a customer.
The loan officer sees the AI's recommendation and rejects it without reviewing the actual application.
The customer was actually creditworthy — the AI was wrong.

The human trusted the automation blindly → Automation Bias
```

> **This is a human bias, not a data bias** — but it makes data bias worse. If humans blindly accept AI decisions, biased AI decisions become biased real-world outcomes, which then become biased training data for the next model (feedback loop).

#### 3. Selection Bias

**Training data is not representative** of the real population the model will serve.

```
Training data: Customer behavior from Myntra app
Problem: Myntra's users skew urban, 18-35, English-speaking, smartphone users

The model doesn't know how to serve:
- Rural customers (different preferences, price sensitivity)
- Older customers (different fashion sense)
- Non-English speakers (different search patterns)
```

> **Live Example — Credit scoring in India:** If a credit scoring model is trained on data from people who already have bank accounts and credit cards, it can't fairly assess people who are new to formal banking (often rural populations, women, lower-income groups). The model has **never seen** their financial behaviour.

#### 4. Group Attribution Bias

**Assuming what's true of a group is true of every individual** in that group.

```
Data shows: "On average, customers from City X spend less than customers from City Y"
Model learns: Every individual customer from City X is a low spender

Reality: There are high spenders in City X and low spenders in City Y.
         Using city as a feature unfairly penalises individuals from City X.
```

This is closely related to **stereotyping** — applying group-level statistics to individuals.

#### 5. Implicit Bias

**Unconscious assumptions** baked into data collection, labelling, or feature engineering.

```
Example: Image labelling for a computer vision model
Labeller unconsciously associates:
- "CEO" → images of men in suits
- "nurse" → images of women
- "engineer" → images of young people

The model inherits these biases and reinforces them.
```

> **Live Example — Google Photos (2015):** Google's image recognition AI labelled photos of Black people as "gorillas" — a horrific failure caused by implicit bias in the training data (predominantly white faces in the training set, insufficient representation of darker skin tones).

### Identifying Bias in Your Data

| Signal | What It Means | How to Check |
|---|---|---|
| **Missing feature values** | Some groups may be underrepresented | Check null rates across demographic groups. If "income" is missing for 40% of rural users but only 5% of urban users, there's a problem. |
| **Unexpected feature values** | Data entry bias or measurement bias | Check if certain groups have systematically different data quality. |
| **Data skew** | Class imbalance across protected attributes | Check distribution: Is your training data 90% male, 10% female? 95% urban, 5% rural? |

```
Bias Detection Checklist:
□ What demographics are present in my training data? What's missing?
□ Do all groups have similar data quality (completeness, accuracy)?
□ Does model performance (accuracy, error rate) differ across groups?
□ Are any features correlated with protected attributes (gender, race, caste, religion)?
□ Would a human reviewer consider the model's decisions fair?
```

> **Mitigation strategies:**
> - **Balanced sampling:** Ensure training data represents all groups proportionally
> - **Bias auditing:** Test model performance separately for each demographic group
> - **Fairness constraints:** Add constraints during training (e.g., error rate must be similar across groups)
> - **Human-in-the-loop:** For high-stakes decisions (loans, hiring), require human review of AI recommendations

---

## 6.11 Data Partitioning

### Why Partition Data?

As your data grows, storing everything in a single table or file becomes a **bottleneck** — queries are slow, storage is hard to manage, and scaling is painful.

**Data partitioning** splits data into smaller, manageable pieces that can be stored and queried independently.

> **Analogy:** A library with one million books on a single shelf would be impossible to search. Instead, libraries partition books by floor → section → shelf → alphabetical order. Each level of partitioning makes finding a book faster.

**Benefits of partitioning:**

| Benefit | How |
|---|---|
| **Performance** | Queries only scan relevant partitions instead of the entire dataset |
| **Scalability** | Different partitions can live on different machines |
| **Manageability** | Easier to backup, archive, or delete specific partitions |
| **Availability** | If one partition's server fails, other partitions remain accessible |

### Horizontal Partitioning (Sharding)

**Split rows across partitions.** Each partition has the **same columns** but **different rows**.

```
Original table: orders (10 million rows)

Partition by region:
┌────────────────────┐  ┌────────────────────┐  ┌────────────────────┐
│ Partition: NORTH   │  │ Partition: SOUTH    │  │ Partition: WEST    │
│ order_id | amount  │  │ order_id | amount   │  │ order_id | amount  │
│ 1001     | 500     │  │ 2001     | 800      │  │ 3001     | 650     │
│ 1002     | 1200    │  │ 2002     | 350      │  │ 3002     | 900     │
│ ...      | ...     │  │ ...      | ...      │  │ ...      | ...     │
│ (3M rows)          │  │ (4M rows)           │  │ (3M rows)          │
└────────────────────┘  └────────────────────┘  └────────────────────┘
```

**Choosing a partition key** (very important):

| Partition Key | Good When | Risk |
|---|---|---|
| **Date** (e.g., order_date) | Queries always filter by date range | Old partitions are rarely queried |
| **Region** (e.g., city, state) | Queries always filter by region | Uneven distribution (Mumbai has 10x more data than Shimla) |
| **Hash of ID** (e.g., hash(user_id) % 10) | Need even distribution regardless of data patterns | Can't do range queries efficiently |
| **Customer tier** | Different access patterns per tier | Uneven sizes (more free users than premium) |

> **Best practice for partition key:** Choose a key that:
> 1. **Distributes data evenly** across partitions (no "hot" partition)
> 2. **Aligns with query patterns** (most queries filter on this key)
> 3. **Has low cardinality** — thousands or fewer distinct values, not millions

> **Live Example — BigBasket:**
> Orders table partitioned by `order_date`:
> - Query "show me today's orders" → scans only today's partition (fast!)
> - Query "show me all orders" → scans all partitions (slow, but rare)
> - Old partitions (>1 year) are moved to cheaper storage automatically

### Vertical Partitioning

**Split columns across partitions.** Each partition has the **same rows** but **different columns**.

```
Original table: users
| user_id | name | email | phone | profile_pic (5MB) | bio (text) | last_login |

Split into:
┌─────────────────────────┐  ┌──────────────────────────────┐
│ Partition A: Hot data    │  │ Partition B: Cold data        │
│ (frequently accessed)    │  │ (rarely accessed)             │
│ user_id | name | email   │  │ user_id | profile_pic | bio   │
│ Fast SSD storage         │  │ Cheap HDD/S3 storage          │
└─────────────────────────┘  └──────────────────────────────┘
```

**Why vertical partitioning?**
- **Frequently accessed columns** (name, email, last_login) are stored on fast storage
- **Large, rarely accessed columns** (profile_pic, bio) are stored on cheap storage
- Queries that only need name and email don't waste I/O reading a 5 MB profile picture

> **Live Example — LinkedIn:**
> - User profile **header data** (name, headline, current company) → hot partition, cached in memory → instant load
> - User **activity history** (all posts, comments, reactions) → cold partition, loaded on scroll → lazy load
> - User **media** (profile photos, shared documents) → object storage (S3) → loaded via CDN

### Functional Partitioning

**Partition by business function or bounded context.** Different types of data live in entirely separate storage systems.

```
E-commerce platform (like Meesho):

┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ ORDER SERVICE    │  │ USER SERVICE     │  │ PRODUCT SERVICE  │
│                  │  │                  │  │                  │
│ orders table     │  │ users table      │  │ products table   │
│ payments table   │  │ addresses table  │  │ inventory table  │
│ returns table    │  │ preferences      │  │ reviews table    │
│                  │  │                  │  │                  │
│ PostgreSQL       │  │ PostgreSQL       │  │ MongoDB          │
│ (Mumbai region)  │  │ (Delhi region)   │  │ (Bangalore)      │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

**Why functional partitioning?**
- Each service can **scale independently** (product catalog gets 100x more reads than orders)
- Each service can use the **best storage technology** (SQL for orders, NoSQL for product catalog)
- A failure in the order service **doesn't take down** the product catalog
- Teams can work independently without stepping on each other's data

> **This is the microservices approach** — each microservice owns its data, and services communicate via APIs or events.

### Rebalancing Partitions

Over time, data distribution changes, and partitions become **unbalanced:**

```
Initial (Jan 2025):
Partition A: 2M rows  |████████████████████|
Partition B: 2M rows  |████████████████████|
Partition C: 2M rows  |████████████████████|

After 6 months (Jul 2025):
Partition A: 8M rows  |████████████████████████████████████████████████████████████████████████████████|  ← hot!
Partition B: 2.5M rows |█████████████████████████|
Partition C: 1.5M rows |███████████████|  ← underused
```

**When to rebalance:**
- One partition has significantly more data than others (>2x the average)
- One partition is receiving much more traffic (hot partition)
- Query performance on one partition has degraded
- You're adding or removing nodes from your cluster

**Rebalancing strategies:**

| Strategy | How It Works | Pros | Cons |
|---|---|---|---|
| **Fixed partitions** | Create more partitions than nodes upfront (e.g., 1000 partitions for 10 nodes). When adding a node, move some partitions to it. | Simple, no re-hashing needed | Must choose partition count upfront |
| **Dynamic splitting** | When a partition grows too large, split it in half | Adapts to data growth | Temporarily slower during split |
| **Consistent hashing** | Use a hash ring. When adding a node, only nearby keys move | Minimal data movement | Uneven distribution possible |

**Migration during rebalancing:**

```
Step 1: Create new partition layout
Step 2: Copy data from old partitions to new partitions (background process)
Step 3: Once copy is complete, switch reads to new partitions
Step 4: Verify everything works
Step 5: Delete old partition data

During Step 2: Both old and new partitions serve reads → zero downtime
```

> **Live Example — Flipkart during Big Billion Days:**
> Before the sale, Flipkart pre-splits their database partitions to handle the expected load. During the sale, if one partition becomes hot (e.g., the "mobile phones" category), they dynamically split it further. After the sale, they rebalance back to normal distribution. This prevents any single database node from becoming the bottleneck during peak traffic.

---

## Key Terms Glossary (Session 6)

| Term | Simple Meaning |
|---|---|
| **Data Pipeline** | Automated flow that moves data from sources to destinations, transforming it along the way |
| **ETL** | Extract-Transform-Load — transform data before loading into warehouse |
| **ELT** | Extract-Load-Transform — load raw data first, transform inside the warehouse/lake |
| **Batch Processing** | Processing data in large chunks at scheduled times (hourly, daily) |
| **Streaming Processing** | Processing data immediately as each event arrives (real-time) |
| **Lambda Architecture** | Using both batch and streaming pipelines together |
| **Data Lake** | Cheap storage for raw data of any type. Schema applied when reading. |
| **Data Warehouse** | Optimised storage for clean, structured data. Schema required when loading. |
| **Data Lakehouse** | Modern approach combining data lake flexibility with warehouse query speed |
| **Data Swamp** | A poorly managed data lake where nobody knows what data exists |
| **Apache Kafka** | Popular tool for real-time event streaming (collects and distributes events) |
| **Apache Spark** | Popular tool for processing large amounts of data in batch |
| **Apache Airflow** | Tool for scheduling and orchestrating data pipeline workflows |
| **Apache Flink** | Tool for real-time stream processing |
| **dbt** | Tool for transforming data inside a warehouse using SQL |
| **Schema-on-Write** | Data must match the schema before it can be stored (warehouse approach) |
| **Schema-on-Read** | Data is stored raw; schema applied when you query it (lake approach) |
| **Snapshot Extraction** | Copying the entire dataset from source every time (full copy) |
| **Differential Extraction** | Copying only the rows that changed since the last extraction (incremental) |
| **Schema Evolution** | Changes to data structure over time (new columns, renamed fields, changed types) |
| **Late-Arriving Data** | Data that arrives after the expected batch processing window has closed |
| **Dead-Letter Queue (DLQ)** | A separate queue where failed/unprocessable messages are sent for investigation |
| **TTL (Time to Live)** | How long messages are retained in a stream before automatic deletion |
| **Exactly-Once Delivery** | Guarantee that each message is processed exactly one time — no loss, no duplicates |
| **Replay** | Ability to re-read and reprocess events that were already consumed from a stream |
| **Data Ingestion** | Moving data from source to destination (the transport step) |
| **Data Integration** | Combining data from multiple sources into a unified, consistent view |
| **File Storage** | Data stored as files in a directory hierarchy (NAS, NFS, SMB) |
| **Block Storage** | Data stored as fixed-size blocks, ideal for databases (EBS, SAN) |
| **Object Storage** | Data stored as objects with metadata and unique ID, ideal for large/unstructured data (S3, GCS) |
| **HDFS** | Hadoop Distributed File System — distributed file storage across cluster nodes for big data |
| **Deequ** | Amazon's open-source library for defining data quality constraints ("data unit tests") on Spark |
| **TFDV** | TensorFlow Data Validation — Google's tool that auto-generates schemas and validates data against them |
| **Data Drift** | Input data distribution changes over time compared to training data |
| **Concept Drift** | The relationship between features and target variable changes over time |
| **Schema Skew** | Schema of incoming data changes unexpectedly (new columns, type changes) |
| **Training-Serving Skew** | Features differ between training (offline) and production (online) environments |
| **Data Leakage** | When information from outside the training dataset leaks into model training, causing false accuracy |
| **Reporting Bias** | Data doesn't reflect real-world frequency (unusual events are overreported) |
| **Selection Bias** | Training data is not representative of the real population |
| **Automation Bias** | Over-relying on AI output without questioning it |
| **Group Attribution Bias** | Assuming what's true of a group applies to every individual in that group |
| **Implicit Bias** | Unconscious assumptions embedded in data collection or labelling |
| **Horizontal Partitioning (Sharding)** | Splitting rows across partitions — same columns, different rows |
| **Vertical Partitioning** | Splitting columns across partitions — same rows, different columns |
| **Functional Partitioning** | Partitioning by business function (e.g., orders data separate from user data) |
| **Rebalancing** | Redistributing data across partitions when load or data distribution becomes uneven |

---

*End of Session 6*
