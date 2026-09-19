# Session 6: Data Engineering Pipelines (80 Slides)

> BITS Pilani — SS ZG662 | Module 2: Data & Feature Engineering

---

## 6.1 Data Pipeline

### Definition

Automated sequence: Source → Extract → Transform → Load → Serve

**Swiggy pipeline example:** Restaurant data + order data + driver data + weather → ETL → Feature Store → ML models (ETA, matching, pricing)

---

## 6.2 ETL vs ELT

### ETL — Extract, Transform, Load

```
Source → Extract → Transform (clean/filter BEFORE loading) → Load to Warehouse
```

Best for: On-premise, regulated environments, structured data, traditional BI.

### ELT — Extract, Load, Transform

```
Source → Extract → Load raw to Lake → Transform WHEN NEEDED
```

Best for: Cloud, ML/AI workloads, flexible exploration, schema-on-read.

### Comparison (7 Dimensions)

| Dimension | ETL | ELT |
|---|---|---|
| Transform timing | Before loading | After loading |
| Raw data preserved | No | Yes |
| Schema | Schema-on-write | Schema-on-read |
| Flexibility | Low (must know transforms upfront) | High (transform later) |
| Cost | Higher compute upfront | Cheaper storage, compute on demand |
| Best for | Regulated, structured, BI | Cloud, ML, exploration |
| Trend | Legacy | Modern (industry moving here) |

**PhonePe migration example:** Moved from ETL to ELT — raw data preserved in lake, transform on demand for different ML use cases.

---

## 6.3 Batch vs Streaming

### Batch Processing

- Scheduled chunks (hourly/daily/weekly)
- **Tools:** Spark, Airflow, dbt, Hadoop
- High throughput, high latency

### Streaming Processing

- Real-time, per event, minimal delay
- **Tools:** Kafka, Flink, Kinesis
- Low latency, complex infrastructure

### Comparison (7 Dimensions)

| Dimension | Batch | Streaming |
|---|---|---|
| Latency | Minutes to hours | Milliseconds to seconds |
| Throughput | Very high | Lower per event |
| Complexity | Simple | Complex |
| Cost | Lower | Higher |
| Use case | Training, reports, aggregations | Fraud detection, real-time recs |
| Data completeness | Full dataset | Partial/windowed |
| Error handling | Reprocess entire batch | Per-event retry |

**Company examples:** Netflix (batch for training), Uber (streaming for pricing), HDFC (streaming for fraud), Flipkart (batch for recommendations), Zomato (both for ETA)

### Batch Ingestion Details

**Snapshot vs Differential:**
- Snapshot: Full copy of source data each time
- Differential: Only new/changed records since last extraction

**File formats:**

| Format | Type | Best For |
|---|---|---|
| CSV | Row-based, text | Simple exchange, small data |
| JSON | Row-based, text | APIs, semi-structured |
| Parquet | Columnar, binary | Analytics, ML (fast column reads) |
| Avro | Row-based, binary | Streaming, schema evolution |
| ORC | Columnar, binary | Hive/Hadoop ecosystem |

**Other considerations:** Batch size optimization, data migration strategies, schema evolution handling, late-arriving data management.

### Lambda Architecture (Batch + Streaming)

```
                    ┌─── Batch Layer (accuracy) ───┐
Source Data ────────┤                               ├──→ Serving Layer (merged)
                    └─── Speed Layer (freshness) ───┘
```

Batch layer: Daily recompute for accuracy. Speed layer: Real-time for freshness. Serving layer: Merges both.

**Swiggy example:** Batch (historical delivery patterns, restaurant stats) + Streaming (current traffic, driver location, live orders).

### Streaming Ingestion Concepts

| Concept | Details |
|---|---|
| Ordering | Events may arrive out of order — need timestamp-based ordering |
| Delivery guarantees | At-most-once, at-least-once, exactly-once |
| Replay | Ability to re-read past events (Kafka retains events) |
| Message size | Typically small (<1MB per message) |
| TTL | Time-to-live — how long messages are retained |
| Dead-letter queues | Failed messages routed here for investigation |
| Consumer pull vs push | Pull (consumer requests) vs Push (broker sends) |

### Data Ingestion Tools & Challenges

**5 Challenges:** Volume, velocity, variety, quality, schema changes.

**Tool categories:** Batch tools (Spark, Sqoop), streaming tools (Kafka, Flink), managed services (Fivetran, Airbyte), CDC tools (Debezium).

---

## 6.4 Storage Types

### Raw Storage Types

| Type | What | Speed | Cost | Example |
|---|---|---|---|---|
| HDD | Magnetic disk | Slow | Cheapest | Archive storage |
| SSD | Flash memory | Fast | Medium | Database storage |
| RAM | In-memory | Fastest | Expensive | Cache, real-time features |
| Network | Remote storage | Variable | Variable | Cloud storage |

### Storage Abstractions

| Type | How | Best For | Tools |
|---|---|---|---|
| File Storage | Hierarchical (folders/files) | Shared documents, NFS | NAS, NFS, EFS |
| Block Storage | Fixed-size blocks, direct attach | Databases, VMs | EBS, SAN |
| Object Storage | Flat namespace, key-value | Data lakes, ML data, any scale | S3, GCS, Azure Blob |
| Cache | In-memory key-value | Real-time features, hot data | Redis, Memcached |
| HDFS | Distributed file system | Big data processing | Hadoop HDFS |
| Streaming Storage | Append-only log | Event streams | Kafka |

### Single vs Distributed Storage

| Aspect | Single Machine | Distributed |
|---|---|---|
| Capacity | Limited by one machine | Scales horizontally |
| Fault tolerance | Single point of failure | Replicated across nodes |
| Throughput | Limited | Parallel reads/writes |
| Complexity | Simple | Complex (consensus, replication) |

---

## 6.5 Data Lake vs Warehouse vs Lakehouse

| Aspect | Data Warehouse | Data Lake | Lakehouse |
|---|---|---|---|
| Data type | Structured only | Any (structured + unstructured) | Any |
| Schema | Schema-on-write | Schema-on-read | Schema-on-read + enforcement |
| Cost | Expensive | Cheap | Medium |
| Query speed | Fast (optimized) | Slow (raw data) | Fast (with indexing) |
| Best for | BI, reporting | ML training, raw archive | Both BI + ML |
| Tools | Snowflake, BigQuery, Redshift | S3, GCS, ADLS | Delta Lake, Iceberg, Hudi |

### Data Swamp

A data lake without governance. Symptoms: No catalog, no quality checks, nobody knows what data exists or its meaning.

**Well-managed lake:** Cataloged, governed, quality-checked, discoverable.
**Data swamp:** Dumping ground, no metadata, unusable.

---

## 6.6 Enterprise Pipeline Case Study: Myntra Recommendations

### Architecture

```
Data Sources → Ingestion (Kafka + batch) → Storage (S3 + Warehouse) →
Processing (Spark) → Feature Store → ML Models →
Recommendation API → Mobile App → Monitoring
```

**Tools used per component:**
- Ingestion: Kafka (streaming), Sqoop (batch)
- Storage: S3 (lake), Redshift (warehouse)
- Processing: Spark, Airflow (orchestration)
- Feature Store: Custom (user features, product features, interaction features)
- Serving: Real-time API (<100ms)
- Monitoring: Custom dashboards, drift detection

---

## 6.7 Data Validation

### Unit-Test Approach

Treat incoming data like code — write tests that run automatically.

| Test Type | What It Checks |
|---|---|
| Completeness | Are all expected columns present? |
| Freshness | Is data from today (not stale)? |
| Volume | Expected number of rows (±threshold)? |
| Distribution | Column statistics within expected range? |
| Referential integrity | Foreign keys valid? |

### Amazon Deequ (Constraint-Based)

Define constraints in code: `hasSize(>1000)`, `isComplete("email")`, `isContainedIn("status", ["active","inactive"])`. Runs checks, produces metrics, alerts on violations.

### Google TFDV (Schema-Based)

Automatically infers schema from training data. Compares new data against schema. Flags anomalies: new categories, distribution shifts, missing values.

### Deequ vs TFDV

| Aspect | Deequ | TFDV |
|---|---|---|
| Approach | Constraint-based (you define rules) | Schema-based (auto-inferred) |
| Best for | Custom business rules | ML pipeline data validation |
| Language | Scala/Spark | Python/TensorFlow |
| Integration | Spark pipelines | TFX pipelines |

---

## 6.8 Data Drift, Skew, and Monitoring

### Types of Drift and Skew

| Type | What Changes | Detection |
|---|---|---|
| **Data drift** | Input feature distributions shift | PSI, KS test, compare train vs live distributions |
| **Schema skew** | Column names, types, or counts change | Schema validation |
| **Distribution skew** | Feature value distributions change | Statistical tests on feature distributions |
| **Concept drift** | Relationship between features and target changes | Monitor predicted vs actual outcomes |
| **Training-serving skew** | Features computed differently in training vs serving | Compare feature values between pipelines |

### Detection Methods

- **PSI (Population Stability Index):** PSI > 0.2 = significant drift
- **KS test:** Compare two distributions statistically
- **Feature statistics monitoring:** Track mean, std, min, max over time
- **Prediction distribution monitoring:** Track score distribution shifts

---

## 6.9 Data Leakage

### Definition

Model uses information during training that would not be available at prediction time.

### Cause 1: Feature That Hides the Target

Feature directly encodes the outcome. Example: "loan_status" as feature when predicting loan default — it IS the target.

### Cause 2: Feature From the Future

Feature uses data from after the prediction point. Example: "post-discharge follow-up count" when predicting readmission at discharge time.

### Detection Checklist

- Is any feature computed using data from after the prediction point?
- Does any feature have suspiciously high correlation with the target?
- Does removing one feature cause a large accuracy drop?
- Is the model performing "too well" (e.g., 99%+ accuracy)?

---

## 6.10 Fairness and Bias in Data

### 5 Types of Bias

| Bias Type | Description | Example |
|---|---|---|
| **Reporting bias** | Data over-represents certain events | News datasets over-represent crime vs daily life |
| **Automation bias** | Over-relying on automated systems | Trusting model output without human review |
| **Selection bias** | Non-representative sampling | Training on Uber data only in cities → fails in rural areas |
| **Group attribution bias** | Stereotyping entire groups from individuals | "All elderly patients are high risk" |
| **Implicit bias** | Unconscious assumptions in data collection/labeling | Resume screening penalizing non-Western names |

### Identification Signals

- Performance varies significantly across demographic groups
- Training data demographics don't match production population
- Proxy features (zip code proxying for race)
- Historical decisions embedded as labels

### Mitigation

- Audit training data for representation
- Test model performance per demographic group
- Remove proxy features
- Use fairness-aware algorithms
- Regular bias audits post-deployment

---

## 6.11 Data Partitioning

### Horizontal Partitioning (Sharding)

Split rows across multiple machines. Each shard has same schema, different rows.

| Strategy | How | Best When |
|---|---|---|
| Range-based | Rows 1-1M on shard 1, 1M-2M on shard 2 | Sequential access patterns |
| Hash-based | hash(key) % N shards | Even distribution needed |
| Directory-based | Lookup table maps keys to shards | Complex routing rules |

### Vertical Partitioning

Split columns across different stores. Frequently accessed columns together, rarely accessed separately.

Example: User profile (name, email) in fast DB, user preferences (100+ columns) in separate store.

### Functional Partitioning

Split by business domain. Payments data in one store, user data in another, product data in third.

### Rebalancing Partitions

When shards become uneven (hotspots), need to redistribute:
- Fixed number of partitions (pre-allocate more than needed)
- Dynamic partitioning (split when too large)
- Partition by node (each node owns N partitions)

---
