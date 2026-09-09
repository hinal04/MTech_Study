# Session 6: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is a Data Pipeline? Why is it essential for AI systems?

**Answer:**
A data pipeline is an automated series of steps that moves data from source systems, transforms it, and delivers it to a destination where it can be used for analysis or AI model training.

**Simple analogy:** Think of a water pipeline. Water (raw data) comes from a river (source systems), goes through a purification plant (transformations — cleaning, filtering), and reaches your home tap (destination — data warehouse or model training). The pipeline runs automatically — you don't manually carry water every day.

**Why essential for AI:**

| Without Pipelines | With Pipelines |
|---|---|
| Data scientists spend 80% time manually collecting and cleaning data | Automated — data arrives clean and ready |
| Data from different sources is in different formats | Pipeline standardises everything |
| No repeatability — done differently every time | Same process runs daily/hourly automatically |
| Impossible to scale | Can handle millions of records per second |
| No audit trail | Every step is logged and traceable |

**Structure of a typical data pipeline:**

```
Source → Ingestion → Processing/Transformation → Storage → Serving/Consumption
```

**Real example — Swiggy's pipeline:**
1. **Source:** App clicks, order events, GPS pings, restaurant data, payment data
2. **Ingestion:** Apache Kafka collects millions of events per second
3. **Processing:** Apache Spark cleans, joins, and transforms the data
4. **Storage:** Stored in a data lake (S3) and data warehouse (Redshift)
5. **Serving:** Delivery time prediction model reads from the warehouse; real-time recommendations read from the data lake

---

## Q2: Compare ETL and ELT. When should you use each approach?

**Answer:**
Both are approaches to moving and transforming data, but they differ in WHEN the transformation happens.

**ETL — Extract, Transform, Load:**
- Data is extracted from sources → transformed (cleaned, filtered, joined) in a separate processing engine → loaded into the destination (data warehouse)
- Transformation happens BEFORE loading

**ELT — Extract, Load, Transform:**
- Data is extracted from sources → loaded raw into the destination (data lake/cloud warehouse) → transformed inside the destination using its own computing power
- Transformation happens AFTER loading

| Aspect | ETL | ELT |
|---|---|---|
| **Order** | Extract → Transform → Load | Extract → Load → Transform |
| **Where transformation happens** | In a separate ETL tool/server | Inside the destination (data lake/warehouse) |
| **Data loaded** | Only clean, transformed data | All raw data (transform later) |
| **Speed** | Slower (transform before load) | Faster initial load (transform on-demand) |
| **Storage cost** | Lower (only store clean data) | Higher (store everything, including raw) |
| **Flexibility** | Low — must define transformations upfront | High — raw data is available for any future transformation |
| **Best for** | Traditional data warehousing, structured data | Cloud data lakes, big data, exploratory analysis |
| **Tools** | Informatica, Talend, SSIS | dbt, Spark SQL, BigQuery, Snowflake |
| **Indian example** | SBI processing daily branch transactions into reporting warehouse | Flipkart loading all clickstream data into S3, then running Spark jobs to analyse later |

**When to use ETL:**
- When you know exactly what transformations are needed
- When storage is expensive (on-premises)
- When data governance requires only clean data in the warehouse

**When to use ELT:**
- When you're on the cloud (storage is cheap)
- When you don't know all the use cases upfront — store raw, transform later
- When you have massive data volumes (big data)
- When different teams need different transformations of the same raw data

**Modern trend:** ELT is becoming the default because cloud storage is cheap and cloud warehouses (BigQuery, Snowflake, Redshift) are powerful enough to handle transformations.

---

## Q3: Compare Batch Processing and Stream Processing with examples.

**Answer:**

| Aspect | Batch Processing | Stream Processing |
|---|---|---|
| **What it is** | Process large volumes of data at scheduled intervals | Process data continuously as it arrives |
| **Latency** | High (minutes to hours) | Low (milliseconds to seconds) |
| **Data** | Processes accumulated historical data | Processes individual events in real-time |
| **Complexity** | Simpler to build and debug | More complex — must handle out-of-order events, failures |
| **Tools** | Apache Spark, Hadoop MapReduce, AWS Batch | Apache Kafka, Apache Flink, Apache Spark Streaming |
| **Cost** | Lower (runs periodically) | Higher (runs 24/7) |
| **Use case** | Monthly salary processing at TCS | Fraud detection at HDFC Bank during a UPI transaction |

**Indian examples:**

| Company | Batch Processing | Stream Processing |
|---|---|---|
| **HDFC Bank** | Monthly credit card statement generation | Real-time fraud detection on every UPI payment |
| **Swiggy** | Daily aggregation of delivery performance metrics | Real-time delivery tracking and ETA updates |
| **Flipkart** | Nightly product catalog update and search index rebuild | Real-time inventory update when someone buys an item |
| **IRCTC** | Daily report of bookings, cancellations, revenue | Real-time seat availability display during Tatkal booking |
| **Zerodha** | End-of-day portfolio P&L calculation | Real-time stock price updates and order matching |

**Key insight:** Most real-world systems use BOTH. Flipkart uses batch processing to rebuild search indexes nightly but stream processing to update "Only 2 left!" in real-time.

---

## Q4: What is Lambda Architecture? Why was it created?

**Answer:**
Lambda Architecture is a data processing pattern that combines both batch and stream processing to provide a complete and accurate picture of data.

**The problem it solves:**
- Batch processing is accurate but slow (processes data hours later)
- Stream processing is fast but can miss data or produce approximate results
- Lambda Architecture gives you BOTH: real-time speed AND batch-level accuracy

**Three layers:**

```
                    ┌─────────────────┐
   Raw Data ──────→ │   Batch Layer   │ ──→ Batch Views (accurate, slow)
        │           │ (Spark/Hadoop)  │              │
        │           └─────────────────┘              │
        │                                            ▼
        │                                   ┌──────────────┐
        │                                   │ Serving Layer │ ──→ Queries/Users
        │                                   └──────────────┘
        │                                            ▲
        │           ┌─────────────────┐              │
        └─────────→ │   Speed Layer   │ ──→ Real-time Views (fast, approximate)
                    │ (Kafka/Flink)   │
                    └─────────────────┘
```

| Layer | What It Does | Tools | Example (Swiggy) |
|---|---|---|---|
| **Batch Layer** | Processes ALL historical data periodically (e.g., every 6 hours) | Spark, Hadoop | Calculate accurate average delivery time per restaurant per area using all historical data |
| **Speed Layer** | Processes only the most recent data in real-time | Kafka, Flink | Calculate delivery time estimate based on current traffic, weather, and active orders |
| **Serving Layer** | Merges batch and speed layer results to answer queries | Druid, Cassandra | When you open the app, combine historical average (batch) with current conditions (speed) to show you accurate ETA |

**Limitation:** You write the same business logic twice — once for batch, once for streaming. This is called "code duplication" and makes maintenance harder. This led to the development of the **Kappa Architecture** (streaming-only approach using Kafka + Flink).

---

## Q5: Compare Data Lake, Data Warehouse, and Data Lakehouse.

**Answer:**

| Aspect | Data Lake | Data Warehouse | Data Lakehouse |
|---|---|---|---|
| **What it stores** | Everything — raw, unprocessed data in any format | Clean, structured, transformed data only | Both raw and processed data with management features |
| **Schema** | Schema-on-read (no structure until you query) | Schema-on-write (must define structure before loading) | Schema-on-read with optional schema enforcement |
| **Data types** | Structured + Semi-structured + Unstructured | Structured only | All types (like a lake) with warehouse-like management |
| **Cost** | Very low (cheap object storage like S3) | High (optimised columnar storage) | Medium (cheap storage + smart metadata layer) |
| **Performance** | Slow for analytical queries | Very fast for analytical queries | Fast (approaching warehouse speed) |
| **Users** | Data engineers, data scientists | Business analysts, BI teams | Everyone — engineers, scientists, analysts |
| **ACID transactions** | No | Yes | Yes (Delta Lake/Iceberg adds this) |
| **Data quality** | No guarantees (can become a "data swamp") | High quality enforced | Quality enforced via metadata layer |
| **Tools** | S3, HDFS, Azure Data Lake | Snowflake, BigQuery, Redshift | Databricks (Delta Lake), Apache Iceberg |

**Simple analogy:**
- **Data Lake** = A huge library where you dump all books, newspapers, handwritten notes, CDs — everything. No catalog, no shelf organisation. Finding something is hard.
- **Data Warehouse** = A well-organised library with only curated, cataloged textbooks. Easy to find information but expensive to maintain and can't store videos or art.
- **Data Lakehouse** = A huge library (like a lake) but WITH a proper catalog system, shelves, and librarian. Store everything but find anything quickly.

**Indian example — Myntra:**
- **Data Lake (S3):** Stores everything — raw clickstream data, product images, customer reviews, app logs, payment events
- **Data Warehouse (Redshift):** Stores clean, structured data — daily sales by category, customer segments, revenue metrics
- **Data Lakehouse (Databricks):** Combines both — data scientists can query raw clickstream data AND clean sales data in one place

---

## Q6: What is a Data Swamp? How does a Data Lake become a Data Swamp?

**Answer:**
A Data Swamp is a Data Lake that has become unusable because nobody can find, understand, or trust the data in it.

**Analogy:** A data lake is like a clean pond where you can see the fish. A data swamp is when you dump garbage, mud, and sewage into it — the pond is full but nothing in it is usable.

**How a Data Lake becomes a Data Swamp:**

| Problem | What Happens | Real Example |
|---|---|---|
| **No metadata** | Nobody knows what the data means, when it was created, or who owns it | A Flipkart engineer finds a table called "tmp_data_v2_final_FINAL" — nobody knows what's in it |
| **No data catalog** | Can't search or discover what data exists | 500 TB of data in S3, but no way to know which folder has customer data vs. server logs |
| **No ownership** | Nobody is responsible for data quality | A table hasn't been updated in 8 months but nobody noticed because nobody owns it |
| **No access controls** | Everyone dumps data, nobody cleans | Interns upload test data to the same bucket as production data |
| **No governance** | No policies on data retention or quality | Data from a discontinued product (2019) is still sitting there, consuming storage, confusing analysts |
| **Duplicates everywhere** | Same data loaded multiple ways | Customer data loaded by 3 different teams in 3 different formats — which one is correct? |

**How to prevent a Data Swamp:**
1. **Data Catalog** — tools like Apache Atlas, AWS Glue Catalog
2. **Metadata management** — document every dataset (owner, schema, freshness, description)
3. **Data quality checks** — automated validation on every data load
4. **Access controls** — not everyone can write to the lake
5. **Data lifecycle policies** — auto-archive data older than X months
6. **Use a Lakehouse instead** — built-in schema enforcement and metadata management

---

## Q7: Walk through an enterprise data pipeline for an e-commerce company like Myntra.

**Answer:**
Here's a complete end-to-end pipeline:

**Step 1: Data Sources (Ingestion)**

| Source | Data Type | Volume |
|---|---|---|
| Mobile app | Clickstream events (JSON) | 500M events/day |
| Website | Page views, search queries | 200M events/day |
| Order system | Transactions (structured) | 5M orders/day |
| Payment gateway | Payment events | 5M/day |
| Warehouse/logistics | Shipping, inventory updates | 10M events/day |
| Product catalog | Images, descriptions | 10M products |
| Customer service | Chat logs, call recordings | 500K interactions/day |

**Step 2: Ingestion Layer**
- **Apache Kafka** collects all events in real-time (acts as a message bus)
- Each data source publishes events to Kafka topics
- Kafka retains data for 7 days (buffer in case downstream processing fails)

**Step 3: Processing Layer**

| Processing Type | Tool | What It Does |
|---|---|---|
| Stream processing | Apache Flink | Real-time fraud detection, live inventory updates, real-time personalisation |
| Batch processing | Apache Spark | Daily aggregations, user segmentation, recommendation model training data prep |

**Step 4: Storage Layer**

| Storage | Tool | What's Stored |
|---|---|---|
| Data Lake | S3 + Delta Lake | All raw data + processed datasets (lakehouse approach) |
| Data Warehouse | Amazon Redshift | Clean, aggregated business metrics |
| Feature Store | Feast | Pre-computed features for ML models |
| Search Index | Elasticsearch | Product search data |

**Step 5: Consumption Layer**

| Consumer | Tool | What They Do |
|---|---|---|
| Business analysts | Tableau, Power BI | View dashboards — daily GMV, category performance |
| Data scientists | Jupyter, Databricks | Train recommendation models, demand forecasting |
| ML models (production) | Feature Store + Model Serving | Real-time product recommendations, search ranking |
| Operations team | Grafana | Monitor delivery performance, warehouse efficiency |

**Step 6: Orchestration & Monitoring**
- **Apache Airflow** schedules and orchestrates all batch jobs (the "conductor" of the pipeline)
- **Data quality checks** run after each pipeline step (Great Expectations, Deequ)
- **Alerting** if a pipeline fails or data quality drops below threshold

---

## Q8: Explain Apache Kafka, Spark, Airflow, and Flink. When do you use each?

**Answer:**

### Apache Kafka — The Message Highway
- **What:** A distributed event streaming platform — collects and delivers real-time data events.
- **Analogy:** Kafka is like India Post's sorting centre. Letters (events) arrive from millions of senders, get sorted by destination (topics), and are delivered to the right recipients (consumers).
- **Use when:** You need to collect high-volume real-time events — app clicks, sensor data, transactions.
- **Example:** Swiggy uses Kafka to collect every order event, GPS ping, and payment event in real-time. Millions of events per second flow through Kafka.

### Apache Spark — The Heavy Lifter
- **What:** A distributed data processing engine for large-scale batch and micro-batch processing.
- **Analogy:** Spark is like a team of 1000 accountants working in parallel. Each processes a chunk of data, then results are combined.
- **Use when:** You need to process large datasets — ETL jobs, aggregations, ML training data prep.
- **Example:** Flipkart runs nightly Spark jobs to aggregate all daily transactions, calculate seller performance, and prepare training data for recommendation models.

### Apache Airflow — The Scheduler/Orchestrator
- **What:** A workflow orchestration tool that schedules and manages data pipelines. Does NOT process data itself — it tells other tools (Spark, Python scripts) when and in what order to run.
- **Analogy:** Airflow is like a project manager. It doesn't write code — it decides: "First run the data extraction job, then the cleaning job, then the aggregation job. If extraction fails, alert the team and don't run the rest."
- **Use when:** You have multiple pipeline steps that must run in a specific order with dependencies.
- **Example:** Zomato's Airflow DAG (Directed Acyclic Graph):
  1. 2:00 AM — Extract restaurant data from production database
  2. 2:30 AM — Run Spark job to clean and transform
  3. 3:00 AM — Load into data warehouse
  4. 3:30 AM — Run data quality checks
  5. 4:00 AM — Trigger ML model retraining

### Apache Flink — The Real-Time Brain
- **What:** A stream processing engine for real-time event-by-event processing with very low latency.
- **Analogy:** Flink is like a quality inspector on an assembly line — checks EVERY item as it passes by, in real-time. Spark is like a warehouse inspector who checks items in batches at end of day.
- **Use when:** You need true real-time processing with millisecond latency and complex event processing (CEP).
- **Example:** HDFC Bank uses Flink-style streaming to detect fraud. Every UPI transaction is analysed in <100ms. If the pattern matches fraud (unusual amount, new device, different city), the transaction is flagged before it completes.

**Quick comparison:**

| Tool | Type | Latency | Primary Role |
|---|---|---|---|
| **Kafka** | Message broker | Milliseconds | Collect and distribute events |
| **Spark** | Processing engine | Minutes–hours | Batch processing, ETL, ML data prep |
| **Flink** | Processing engine | Milliseconds | Real-time stream processing |
| **Airflow** | Orchestrator | N/A (schedules others) | Schedule and manage pipeline workflows |

---

## Q9: Design a data pipeline architecture for Zomato's food delivery platform. What tools would you use and why?

**Answer:**
This is a system design question. Here's a complete architecture:

**Data Sources:**

| Source | Data | Format |
|---|---|---|
| Customer app | Clicks, searches, orders, reviews | JSON events |
| Restaurant partner app | Menu updates, order acceptance, prep time | JSON events |
| Delivery partner app | GPS location, delivery status | GPS + JSON |
| Payment system | Transaction records | Structured |
| Customer support | Chat logs, call recordings | Text + Audio |

**Architecture:**

```
┌──────────────┐     ┌─────────┐     ┌──────────────────────┐
│ Data Sources │────→│  Kafka  │────→│ Stream Processing    │──→ Real-time Actions
│ (Apps, APIs) │     │ (Ingest)│     │ (Flink)              │   (fraud alerts,
└──────────────┘     └────┬────┘     └──────────────────────┘    ETA updates,
                          │                                       live tracking)
                          │
                          ▼
                    ┌──────────────┐     ┌──────────────┐
                    │  Data Lake   │────→│  Spark       │──→ Data Warehouse
                    │  (S3/Delta)  │     │  (Batch ETL) │   (Redshift)
                    └──────────────┘     └──────────────┘        │
                                                                  ▼
                                                          ┌──────────────┐
                                                          │  Dashboards  │
                                                          │  ML Training │
                                                          │  Analytics   │
                                                          └──────────────┘
                    
                    Airflow orchestrates all batch jobs
```

**Tool Selection Rationale:**

| Component | Tool | Why This Tool |
|---|---|---|
| **Ingestion** | Apache Kafka | Handles millions of events/sec from all apps. Fault-tolerant. Decouples sources from processing. |
| **Stream Processing** | Apache Flink | Real-time fraud detection on payments, live ETA calculation, real-time GPS tracking on map. Sub-second latency required. |
| **Batch Processing** | Apache Spark | Nightly aggregation of daily metrics, ML training data preparation, restaurant performance scoring. |
| **Storage (Raw)** | S3 + Delta Lake | Cheap storage for all raw data. Delta Lake adds ACID transactions and schema enforcement (prevents data swamp). |
| **Storage (Clean)** | Amazon Redshift | Fast analytical queries for business dashboards. |
| **Orchestration** | Apache Airflow | Manages the nightly batch pipeline — schedule, dependency management, retry on failure, alerting. |
| **Feature Store** | Feast | Stores pre-computed ML features for delivery time prediction, restaurant ranking, user personalisation. |
| **Monitoring** | Grafana + Prometheus | Monitor pipeline health, data freshness, processing latency. |

---

## Q10: What is the difference between Kafka and Flink? Students often confuse them.

**Answer:**
This is a common confusion. Think of it this way:

- **Kafka = the highway** (transports data from point A to point B)
- **Flink = the factory on the highway** (processes data as it flows through)

| Aspect | Apache Kafka | Apache Flink |
|---|---|---|
| **Primary job** | Transport/deliver events between systems | Process/transform events in real-time |
| **Analogy** | Post office — receives letters, stores them, delivers them | Letter reader — opens, reads, analyses, and acts on letters |
| **Does it process data?** | Very basic (Kafka Streams for simple transforms) | Advanced processing — joins, aggregations, windowing, complex event patterns |
| **Does it store data?** | Yes — retains events for configurable period (hours to days) | No — processes events in flight, doesn't store |
| **Latency** | Milliseconds for delivery | Milliseconds for processing |
| **Use case** | Collecting all order events from Swiggy's app | Calculating running average delivery time per area using the last 30 minutes of orders from Kafka |

**How they work together:**
1. Swiggy's app sends order events → **Kafka** (receives and stores the events)
2. **Flink** reads from Kafka → calculates real-time metrics (delivery ETA, fraud score)
3. Flink writes results → **Kafka** (another topic) → consumed by the app, databases, or alerts

They're complementary, not competitive. Most real-time architectures use Kafka + Flink together.

---

## Q11: Scenario — IRCTC wants to show real-time seat availability during Tatkal booking. Design the pipeline.

**Answer:**
This is a high-concurrency, low-latency problem. Here's the design:

**Challenges:**
- 25 lakh+ tickets booked daily during peak season
- Tatkal booking opens at 10:00 AM — massive spike of concurrent users
- Seat availability must update in <1 second after each booking
- Must handle failures gracefully (what if a payment fails after seat was reserved?)

**Pipeline Design:**

| Component | Tool | Function |
|---|---|---|
| **Event Ingestion** | Apache Kafka | Every booking attempt, cancellation, and payment event published as a Kafka event |
| **Real-time Processing** | Apache Flink | Consumes booking events from Kafka. Maintains running count of available seats per train. Updates within milliseconds. |
| **Fast Storage** | Redis (in-memory cache) | Stores current seat availability for instant reads. When Flink updates counts, it writes to Redis. |
| **Primary Database** | PostgreSQL/Oracle | Source of truth for booking records. Batch-synced with Redis. |
| **Batch Reconciliation** | Apache Spark | Runs every 15 minutes to reconcile Redis counts with actual database records (catches any missed events). |
| **API Layer** | Load-balanced REST API | Reads from Redis to serve availability queries with <50ms response time. |
| **Orchestration** | Airflow | Manages daily batch jobs — seat allocation, waitlist processing, refund processing. |

**Data flow:**
1. User clicks "Book" → API creates booking event → **Kafka**
2. **Flink** reads event → decrements seat count → writes to **Redis**
3. Next user refreshes → API reads from **Redis** → shows updated availability (total latency: <1 second)
4. If payment fails → cancellation event → Kafka → Flink increments seat count → Redis updated

**Why Kafka + Flink + Redis (not just a database):**
- Direct database writes can't handle 50,000 concurrent writes at 10:00 AM
- Redis serves reads in <1ms (database takes 50-100ms)
- Kafka ensures no event is lost even if Flink or Redis temporarily fails
- Flink handles complex logic (reservation timeout, priority waitlist) in real-time

---

## Q12: When would you choose Batch processing over Streaming, and vice versa? Give a decision framework.

**Answer:**

**Choose Batch Processing when:**

| Criteria | Example |
|---|---|
| Results can wait hours/days | Generating monthly electricity bills for Tata Power |
| Need to process entire historical dataset | Retraining Flipkart's recommendation model on 6 months of data |
| Accuracy matters more than speed | Financial reconciliation at SBI — must be exact |
| Data volume is huge but infrequent | Processing annual income tax returns for all taxpayers |
| Cost is a priority | Batch compute is cheaper — runs for 2 hours, shuts down |

**Choose Stream Processing when:**

| Criteria | Example |
|---|---|
| Action needed in seconds | Blocking a fraudulent UPI transaction at Paytm |
| Users expect real-time updates | Showing live GPS tracking of Swiggy delivery |
| Event ordering matters | Processing stock trades at NSE in exact sequence |
| Continuous monitoring required | Monitoring Jio network health — alert if a tower goes down |
| Competitive advantage in speed | Zerodha showing stock prices faster than competitors |

**Decision flowchart:**

```
Is a delay of minutes/hours acceptable?
├── Yes → BATCH (cheaper, simpler)
│         Is the data volume > 1TB per run?
│         ├── Yes → Apache Spark
│         └── No → Simple Python/SQL scripts
│
└── No → STREAMING (real-time)
          Is the processing logic complex (joins, windows)?
          ├── Yes → Apache Flink
          └── No → Kafka Streams (lightweight)
```

**Most companies use both:** Zomato uses streaming for live order tracking and batch for daily business reports.

---

## Q13: What is an Airflow DAG? Explain with a real example.

**Answer:**
A DAG (Directed Acyclic Graph) is Airflow's way of defining a workflow — a set of tasks with dependencies between them.

- **Directed:** Tasks have a direction (Task A runs before Task B)
- **Acyclic:** No loops — the workflow moves forward, never goes back
- **Graph:** Tasks are nodes, dependencies are edges

**Real example — Flipkart's nightly recommendation pipeline:**

```
                    ┌─────────────────┐
                    │ extract_orders   │ (Task 1: Pull today's orders from production DB)
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │ extract_clicks   │ (Task 2: Pull today's clickstream from Kafka)
                    └────────┬────────┘
                             │
                ┌────────────┴────────────┐
                │                         │
       ┌────────▼────────┐      ┌────────▼────────┐
       │ clean_orders    │      │ clean_clicks     │ (Tasks 3a, 3b: Can run in PARALLEL)
       └────────┬────────┘      └────────┬────────┘
                │                         │
                └────────────┬────────────┘
                             │
                    ┌────────▼────────┐
                    │ join_datasets    │ (Task 4: Combine orders + clicks)
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │ compute_features │ (Task 5: Calculate user-product features)
                    └────────┬────────┘
                             │
                ┌────────────┴────────────┐
                │                         │
       ┌────────▼────────┐      ┌────────▼────────┐
       │ train_model     │      │ update_warehouse │ (Tasks 6a, 6b: Parallel)
       └────────┬────────┘      └────────┬────────┘
                │                         │
                └────────────┬────────────┘
                             │
                    ┌────────▼────────┐
                    │ quality_checks   │ (Task 7: Validate model and data quality)
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │ deploy_model     │ (Task 8: Push new model to production)
                    └─────────────────┘
```

**Key Airflow features:**
- **Scheduling:** This DAG runs daily at 2:00 AM
- **Dependencies:** "train_model" won't run until "compute_features" succeeds
- **Parallelism:** "clean_orders" and "clean_clicks" run simultaneously (no dependency between them)
- **Retries:** If "extract_orders" fails, Airflow retries 3 times before alerting
- **Alerting:** On failure, sends Slack message to the data engineering team
- **Backfilling:** If yesterday's run failed, Airflow can automatically re-run for the missed date

---

## Q14: Scenario — A retail company (like Reliance Retail) is moving from on-premises to cloud. Should they use ETL or ELT? Justify.

**Answer:**
**Recommendation: ELT.** Here's the justification:

**Current state (on-premises with ETL):**
- Traditional ETL using Informatica — extract from Oracle DB, transform in ETL server, load into data warehouse
- Storage is expensive (SAN/NAS arrays) — so they only store clean, transformed data
- ETL runs nightly, takes 6 hours, processes yesterday's data
- Only structured data (transactions, inventory) is processed

**Why ELT on cloud is better:**

| Factor | ETL (On-Premises) | ELT (Cloud) |
|---|---|---|
| **Storage cost** | ₹50 lakh/year for 50TB (SAN storage) | ₹2 lakh/year for 50TB (S3) — 25x cheaper |
| **Compute** | Fixed ETL server (expensive even when idle) | Pay only when processing (Spark on EMR) |
| **Flexibility** | Must define transforms upfront | Store raw data, transform when needed |
| **New use cases** | Adding a new report = weeks of ETL changes | New report = new SQL query on existing raw data |
| **Unstructured data** | Can't handle product images, customer call recordings | S3 stores everything — images, audio, JSON, CSV |

**Recommended ELT architecture for Reliance Retail:**

1. **Extract:** Pull data from all stores' POS systems, e-commerce platform, warehouse systems
2. **Load:** Load ALL raw data into S3 (Data Lake) — structured AND unstructured
3. **Transform:** Use Spark/dbt to transform data ON-DEMAND inside the cloud warehouse
4. **Serve:** Clean data in Redshift for dashboards; raw data in S3 for data scientists

**Key benefit:** When the marketing team wants a new analysis 6 months later (e.g., "correlation between weather and ice cream sales"), the raw weather data is already in S3. With ETL, they'd have to go back and rebuild the pipeline to include weather data.

---

## Q15: What are the key differences between a Data Engineer and a Data Scientist? How do their pipelines differ?

**Answer:**

| Aspect | Data Engineer | Data Scientist |
|---|---|---|
| **Primary role** | Build and maintain data infrastructure/pipelines | Analyse data and build ML models |
| **Analogy** | The person who builds roads, bridges, and water supply | The person who uses those roads to deliver goods |
| **Key skills** | SQL, Spark, Kafka, Airflow, cloud infra, Python/Scala | Python, statistics, ML algorithms, TensorFlow, pandas |
| **What they build** | ETL pipelines, data lakes, data warehouses | Predictive models, dashboards, insights |
| **Pipeline type** | Data pipelines (move and transform data) | ML pipelines (feature engineering → training → evaluation → deployment) |
| **Output** | Clean, reliable, accessible data | Trained models and predictions |
| **Metric of success** | Data freshness, pipeline uptime, data quality | Model accuracy, F1-score, business impact |

**How they collaborate (Flipkart example):**

```
Data Engineer builds:
  POS data → Kafka → Spark ETL → Data Warehouse → Feature Store

Data Scientist uses:
  Feature Store → Feature Engineering → Model Training → Model Evaluation → Deployment
```

The data engineer ensures clean data is available. The data scientist uses that data to build and train models. Without good data engineering, the data scientist spends 80% of their time cleaning data instead of building models.

---

## Q16: What are the common failure modes in data pipelines? How do you handle them?

**Answer:**

| Failure Mode | What Happens | Example | Solution |
|---|---|---|---|
| **Source unavailable** | Data source is down or unreachable | Swiggy's restaurant database is undergoing maintenance | Retry with exponential backoff. Use Kafka as buffer (data waits in Kafka until source is back). |
| **Schema change** | Source changes column names or types without notice | Flipkart's product catalog adds a new field — pipeline breaks because it expects old schema | Schema registry (Confluent Schema Registry). Schema evolution support. Alerts on schema changes. |
| **Data volume spike** | Sudden increase in data volume overwhelms pipeline | Flipkart Big Billion Days — 10x normal traffic | Auto-scaling (cloud). Kafka absorbs spike as buffer. Backpressure mechanisms in Flink. |
| **Late-arriving data** | Data arrives after the processing window closes | GPS pings arrive 5 minutes late due to poor network in rural areas | Watermarks and late-event handling in Flink. Allow grace period. |
| **Pipeline job failure** | A Spark job fails mid-execution | Out-of-memory error during a join operation | Airflow retries. Checkpointing (resume from last successful step, not from scratch). |
| **Data quality degradation** | Pipeline runs but produces bad data | A bug in the ETL script converts all prices to 0 | Data quality checks after each step (Great Expectations). Automated alerting. Circuit breaker pattern. |
| **Duplicate processing** | Same data processed twice | Kafka consumer restarts and reprocesses events | Idempotent processing. Exactly-once semantics in Kafka + Flink. Deduplication logic. |

**Best practices for resilient pipelines:**
1. **Idempotency:** Running the same pipeline twice should produce the same result
2. **Checkpointing:** Save progress so failures resume, not restart
3. **Dead letter queues:** Events that can't be processed go to a separate queue for investigation
4. **Monitoring and alerting:** Know when things break BEFORE users notice
5. **Circuit breaker:** If error rate exceeds threshold, pause the pipeline instead of producing bad data

---

## Q17: Scenario — Zerodha needs to process 10 million stock trades per day. Design the pipeline. Should they use batch or streaming?

**Answer:**
**Answer: Both — but streaming for the core trading flow.**

**Why streaming is essential:**
- Stock trades MUST be processed in real-time — a 5-second delay could mean lakhs of rupees in loss
- Regulatory requirement: trades must be reported to NSE/BSE within seconds
- Users expect to see their portfolio updated instantly after a trade

**Why batch is still needed:**
- End-of-day P&L calculations, margin calculations, regulatory reports
- Historical analysis for risk management
- Daily reconciliation with exchange records

**Architecture:**

| Component | Tool | Function |
|---|---|---|
| **Order Ingestion** | Kafka | Every buy/sell order published to Kafka. Handles 100K+ orders/second during market open. |
| **Order Matching** | Flink | Real-time order matching engine. Processes each order in <10ms. Handles complex order types (limit, market, stop-loss). |
| **Portfolio Update** | Flink + Redis | After trade execution, instantly update user's portfolio in Redis. User sees updated holdings immediately. |
| **Risk Monitoring** | Flink | Real-time margin calculation. If a user's margin falls below threshold, trigger margin call alert instantly. |
| **Market Data** | Kafka + Flink | Process real-time price feeds from NSE/BSE. Update stock prices on the app every second. |
| **End-of-Day Batch** | Spark + Airflow | After market close (3:30 PM): calculate final P&L, generate contract notes, prepare regulatory reports (SEBI submissions). |
| **Persistent Storage** | PostgreSQL + S3 | Trade records in PostgreSQL (ACID for financial data). Historical data archived to S3 for analytics. |

**Data flow for a single trade:**
1. User places buy order on Kite (Zerodha app) → API server → **Kafka** (< 1ms)
2. **Flink** reads order → validates margin → sends to exchange → receives confirmation (< 50ms)
3. **Flink** updates user's portfolio in **Redis** → user sees updated holdings (< 100ms total)
4. After market close → **Spark** batch job reconciles all trades, calculates taxes, generates reports
5. **Airflow** orchestrates: Reconciliation → P&L calculation → Contract note generation → Email to user

---

## Q18: Explain the concept of "Exactly-Once Processing" in streaming pipelines. Why is it important?

**Answer:**
In streaming systems, each event can be processed in three ways:

| Semantics | What It Means | Problem |
|---|---|---|
| **At-most-once** | Event processed 0 or 1 times. If processing fails, event is lost. | Might miss data — a Paytm transaction could be lost |
| **At-least-once** | Event processed 1 or more times. On failure, event is reprocessed. | Might duplicate — a Paytm transaction could be processed twice (₹500 deducted twice!) |
| **Exactly-once** | Event processed exactly 1 time, even on failures. | No loss, no duplication — ideal but hardest to achieve |

**Why exactly-once matters:**
- In financial systems (Paytm, HDFC Bank), processing a payment twice or losing a payment is unacceptable
- In Swiggy's order system, duplicating an order means the customer receives two orders and is charged twice

**How Kafka + Flink achieve exactly-once:**
1. **Kafka transactions:** Producer writes to Kafka in a transaction — either all messages are written or none (atomic)
2. **Flink checkpointing:** Flink periodically saves its state (checkpoint). On failure, it restores from the last checkpoint and reprocesses — but using Kafka offsets, it knows exactly where it left off
3. **Idempotent writes:** Even if an event is reprocessed, the output operation checks "has this already been done?" and skips duplicates

**Simple analogy:** Exactly-once is like sending an important courier. You want guaranteed delivery (not lost = at-least-once) but also want to ensure only one copy arrives (not duplicated = at-most-once). Exactly-once = guaranteed one delivery.

---

## Q19: A company stores 50TB of data in a data lake but nobody uses it. What went wrong, and how would you fix it?

**Answer:**
This company has a classic **Data Swamp** problem. Here's the diagnosis and fix:

**Diagnosis — What likely went wrong:**

| Symptom | Root Cause |
|---|---|
| Nobody can find relevant data | No data catalog or search functionality |
| Nobody trusts the data | No data quality monitoring — nobody knows if the data is accurate |
| Nobody understands the data | No documentation, no metadata, no column descriptions |
| Multiple versions of same data | No governance — different teams loaded the same data differently |
| Stale data mixed with fresh data | No lifecycle management — data from 2019 sits next to data from today |
| Nobody owns the data | No data ownership or stewardship assigned |

**Fix — The Data Swamp Recovery Plan:**

**Phase 1 (Week 1-2): Discover and Document**
1. Inventory all datasets — what's in the 50TB?
2. Deploy a data catalog tool (Apache Atlas or AWS Glue Catalog)
3. Auto-discover schemas and tag datasets
4. Identify "golden datasets" — the most important 20% that drive 80% of business value

**Phase 2 (Week 3-4): Assign Ownership and Quality**
1. Assign a data owner for each dataset (a person accountable)
2. Define data quality rules for each golden dataset
3. Implement automated quality checks (Great Expectations)
4. Delete or archive clearly outdated/useless data

**Phase 3 (Month 2): Build Access and Self-Service**
1. Build a searchable data catalog with descriptions, sample data, quality scores
2. Create "data products" — curated, documented, trusted datasets
3. Set up access controls (who can read what)
4. Create templates for common queries

**Phase 4 (Ongoing): Governance**
1. New data must have metadata, owner, and quality rules BEFORE loading
2. Monthly data quality reports
3. Automatic archiving of data not accessed in 6 months
4. Migrate to a Lakehouse architecture (Delta Lake/Iceberg) for built-in schema enforcement

---

## Q20: Compare all the data storage options (Data Lake, Data Warehouse, Data Lakehouse) with a decision framework for when to use each.

**Answer:**

**Decision Framework:**

```
What type of data do you primarily have?
│
├── Mostly Structured (SQL tables) + Known queries
│   └── DATA WAREHOUSE (Snowflake, BigQuery, Redshift)
│       Best for: BI dashboards, SQL analytics, reporting
│       Example: HDFC Bank's risk reporting — structured financial data, known queries
│
├── Mix of Structured + Unstructured + Don't know all use cases yet
│   └── DATA LAKEHOUSE (Databricks/Delta Lake, Apache Iceberg)
│       Best for: Modern data + AI stack, combines flexibility with reliability
│       Example: Flipkart — needs product images (unstructured) + sales data (structured)
│                + ML training + BI dashboards, all from one system
│
└── Everything, cheapest possible, primarily for data scientists
    └── DATA LAKE (S3 + Hive/Glue)
        Best for: Raw data storage, ML experimentation, cost-sensitive
        Example: A startup that needs to store tons of raw data cheaply and figure out use cases later
```

**Summary comparison:**

| Factor | Data Warehouse | Data Lake | Data Lakehouse |
|---|---|---|---|
| **Cost** | $$$ (expensive) | $ (cheapest) | $$ (moderate) |
| **Query speed** | Fastest | Slowest | Fast (approaching warehouse) |
| **Data types** | Structured only | All types | All types |
| **ACID transactions** | Yes | No | Yes |
| **Data governance** | Strong | Weak (swamp risk) | Strong |
| **ML support** | Poor (structured data only) | Good (all data, but messy) | Best (all data, well-managed) |
| **Maturity** | Very mature | Mature | Emerging but rapidly adopted |

**Industry trend (2024):** The Lakehouse is winning. Databricks (Delta Lake) and Snowflake are converging — Snowflake added unstructured data support, Databricks improved SQL performance. Most new projects at Indian companies like Myntra, CRED, and PhonePe use a Lakehouse approach.
