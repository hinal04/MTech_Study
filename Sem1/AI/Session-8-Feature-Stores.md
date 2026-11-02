# Session 8: Feature Stores (From Handout)

> BITS Pilani — SS ZG662 | Module 2: Data & Feature Engineering

---

## 8.1 What is a Feature Store?

**Definition:** Centralized system that stores, manages, and serves ML features consistently for both training and serving.

### 5 Problems Solved

1. **Training-serving skew** — same feature logic for training and production
2. **Feature duplication** — teams reuse features instead of rebuilding
3. **Slow serving** — pre-computed features served in milliseconds
4. **No discovery** — feature registry lets teams find existing features
5. **No versioning** — track feature definitions and values over time

### Architecture

```
┌─────────────────────────────────────────┐
│           FEATURE REGISTRY              │
│  (metadata, definitions, documentation) │
├──────────────────┬──────────────────────┤
│   OFFLINE STORE  │    ONLINE STORE      │
│  (historical)    │    (latest values)   │
│  Warehouse/S3    │    Redis/DynamoDB    │
│  For: training   │    For: serving      │
├──────────────────┴──────────────────────┤
│          FEATURE PIPELINES              │
│  Batch (Spark/Airflow) + Streaming      │
│  (Kafka/Flink)                          │
└─────────────────────────────────────────┘
```

### Feature Store Tools

| Tool | Type | Key Feature |
|---|---|---|
| **Feast** | Open-source | Most popular, vendor-neutral |
| **Tecton** | Commercial | Real-time features, enterprise support |
| **Hopsworks** | Open-source + commercial | Built-in ML pipelines |
| **SageMaker Feature Store** | AWS managed | Tight AWS integration |
| **Vertex AI Feature Store** | GCP managed | Tight GCP integration |

### Uber Michelangelo

- 10,000+ features in the store
- Serves ETA, surge pricing, fraud detection, driver-rider matching, restaurant ranking models
- Any team can browse and reuse features from other teams
- Single source of truth for all ML features across the company

---

## 8.2 Offline vs Online Features

### Comparison (6 Dimensions)

| Dimension | Offline Features | Online Features |
|---|---|---|
| When computed | Scheduled batch jobs | Real-time or near-real-time |
| Represents | Historical aggregations | Current state / recent activity |
| Latency | Hours (acceptable) | Milliseconds (required) |
| Storage | Data warehouse, S3, Parquet | Redis, DynamoDB, in-memory |
| Used for | Model training, batch predictions | Real-time serving |
| Size | Large (full history) | Small (latest values only) |

### Offline Features — Examples

| Feature | Computation | Schedule |
|---|---|---|
| avg_order_value_30d | Mean of order amounts in last 30 days | Nightly batch |
| customer_lifetime_value | Total revenue from customer | Weekly batch |
| product_popularity_score | Purchase count / view count | Daily batch |

### Online Features — Examples

| Feature | Computation | Update |
|---|---|---|
| cart_total_right_now | Sum of items currently in cart | On every cart change |
| txn_count_last_10min | Count of transactions in sliding window | Per transaction event |
| current_location | GPS coordinates | Real-time stream |

### Using Both Together (Swiggy ETA Example)

```
Offline (pre-computed nightly):
  restaurant_avg_prep_time, historical_route_time, driver_avg_speed

Online (real-time):
  current_traffic, weather_now, active_drivers_nearby, restaurant_queue_length

Combined → ETA Model → Prediction in <100ms
```

---

## 8.3 Feature Pipelines

### Batch Feature Pipeline

```
Source Data → Scheduled Job (Spark/SQL) → Compute Features → Write to Offline Store
```

- Runs on schedule (hourly/daily/weekly)
- Orchestrated by Airflow/Kubeflow
- High throughput, processes full datasets

**Flipkart nightly example:** Compute user purchase history, product popularity, seller ratings → store in offline feature store → ready for next day's model training.

### Streaming Feature Pipeline

```
Event Stream (Kafka) → Stream Processor (Flink) → Compute Features → Write to Online Store
```

- Runs 24/7, processes events as they arrive
- Low latency (ms to seconds)
- Windowed aggregations (last 5 min, last 1 hr)

**HDFC fraud detection example:** Each transaction → Kafka → Flink computes velocity features (txn_count_last_10min, amount_last_1hr) → writes to Redis → fraud model reads in <10ms.

### Batch + Streaming Combined

Most production systems use both:

```
Batch pipeline (nightly):  historical features → Offline Store
Streaming pipeline (24/7): real-time features → Online Store
Serving time: merge offline + online features → model input
```

---

## 8.4 Training-Serving Skew

### Definition

Features differ between training and production, causing model to perform worse than expected in production.

### 4 Types

| Type | What Goes Wrong |
|---|---|
| **Feature computation skew** | Different code computes the same feature in training vs serving |
| **Data distribution skew** | Production data looks different from training data |
| **Feature availability skew** | Feature available in training but not at serving time |
| **Time-travel skew** | Training features accidentally include future data |

### How Feature Stores Prevent Skew

- **Single feature definition** — one code path for training and serving
- **Point-in-time correctness** — historical feature values retrieved at exact timestamps
- **Offline + online stores** — same values, different access patterns
- **Feature validation** — compare training vs serving feature distributions

### Zillow Case Study ($881M Loss)

- Training-serving skew caused price overestimation
- Training features used data that wasn't available at serving time
- Model overestimated house prices → Zillow bought houses at inflated prices
- $881M loss, business unit shut down, 2,000 employees laid off

### Prevention Best Practices

1. Use a feature store (Feast/Tecton) — single source of truth
2. Point-in-time correctness for all historical features
3. Feature validation: compare training vs serving distributions
4. Share feature computation code between training and serving
5. Monitor feature distributions in production

---

## 8.5 Real-Time Recommendation Systems

### Architecture

```
User opens app
    ↓
Feature Store (user features + context)
    ↓
Candidate Generation (fast, narrow millions → ~1000)
    ↓
Ranking Model (accurate, score ~1000 → top 20)
    ↓
Business Logic (filter unavailable, apply promos)
    ↓
Display to user
    ↓
All in <100ms
```

### Two-Stage Architecture

| Stage | Purpose | Speed | Technique |
|---|---|---|---|
| **Candidate Generation** | Narrow millions of items to ~1000 candidates | Very fast | ANN search, embedding similarity |
| **Ranking** | Score and rank ~1000 candidates accurately | Accurate | Detailed ML model (XGBoost, neural ranker) |

**Why two stages?** Running the full ranking model on millions of items is too slow. Candidate generation quickly filters to a manageable set, then ranking refines.

### ANN (Approximate Nearest Neighbour) Search

Find items with similar embeddings quickly:

| Tool | Provider | Use |
|---|---|---|
| **FAISS** | Facebook | Fast similarity search on vectors |
| **ScaNN** | Google | Optimized for Google-scale |
| **Pinecone** | Pinecone | Managed vector DB |
| **Weaviate** | Weaviate | Open-source vector DB |

**How it works:** User embedding → search for nearest product embeddings → return top-k most similar products as candidates.

### Spotify Discover Weekly Example

**Batch pipeline (Monday 3AM):**
1. Compute user taste profile from last 7 days of listening
2. Collaborative filtering — find 1000 most similar users
3. Get songs those similar users loved, filter out known songs
4. Ranking model — score candidates, apply diversity
5. Select top 30 songs → store playlist

**Real-time pipeline (during listening):**
- User skips song → negative signal → update preferences via Kafka
- User saves song → positive signal → update preferences
- All signals feed into NEXT week's Discover Weekly

---
