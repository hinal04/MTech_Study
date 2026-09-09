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
- [6.4 Data Lakes and Data Warehouses](#64-data-lakes-and-data-warehouses)
- [6.5 Enterprise Data Pipeline — Case Study](#65-enterprise-data-pipeline--case-study)

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

---

## 6.4 Data Lakes and Data Warehouses

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

## 6.5 Enterprise Data Pipeline — Case Study

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

---

*End of Session 6*
