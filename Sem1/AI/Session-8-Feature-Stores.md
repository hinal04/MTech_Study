# Session 8: Feature Stores

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 4, Class Notes
>
> **Contact Session:** 8 (Module 2: Data and Feature Engineering)

---

## Table of Contents

- [8.1 What is a Feature Store?](#81-what-is-a-feature-store)
- [8.2 Offline vs Online Features](#82-offline-vs-online-features)
- [8.3 Feature Pipelines](#83-feature-pipelines)
- [8.4 Data Consistency — The Training-Serving Skew Problem](#84-data-consistency--the-training-serving-skew-problem)
- [8.5 Real-Time Recommendation Systems](#85-real-time-recommendation-systems)

---

## 8.1 What is a Feature Store?

### Simple Definition

A **feature store** is a centralised system that stores, manages, and serves ML features. It ensures that the same features used during model training are also available during model serving (prediction time).

> **Analogy:** Think of a feature store like a **central kitchen** in a restaurant chain. Every branch gets the same pre-cut vegetables, same sauces, same marinades — prepared centrally and delivered fresh. Without the central kitchen, each branch would prepare things differently, leading to inconsistent food.
>
> In the same way, without a feature store, the training pipeline computes features one way and the serving pipeline computes them differently — causing bugs and accuracy drops.

### Why Do We Need Feature Stores?

| Problem Without Feature Store | How Feature Store Solves It |
|---|---|
| **Training-serving skew:** Features computed differently in training vs serving | Feature store is the single source of truth — same feature definition, same computation |
| **Duplicate work:** 5 teams each building the same "user_avg_spend" feature | Feature store shares features across teams. Compute once, use everywhere. |
| **Slow feature serving:** Computing features on-the-fly takes too long for real-time predictions | Feature store pre-computes and caches features for instant retrieval (<1ms) |
| **No feature discovery:** Data scientists don't know what features exist | Feature store has a catalog — browse, search, and reuse features |
| **No versioning:** Feature definitions change, breaking models | Feature store versions features. Model v1 uses feature_v1, model v2 uses feature_v2. |

### Architecture of a Feature Store

```
┌──────────────────────────────────────────────────────────────┐
│                     FEATURE STORE                             │
│                                                               │
│  ┌─────────────────────┐     ┌─────────────────────────┐    │
│  │   OFFLINE STORE     │     │    ONLINE STORE          │    │
│  │   (for training)    │     │    (for serving)         │    │
│  │                     │     │                          │    │
│  │  • All historical   │     │  • Latest values only    │    │
│  │    feature values   │     │  • Ultra-fast reads      │    │
│  │  • Stored in data   │     │    (<1ms latency)        │    │
│  │    warehouse/lake   │     │  • Stored in Redis/      │    │
│  │  • Used for batch   │     │    DynamoDB              │    │
│  │    model training   │     │  • Used for real-time    │    │
│  │                     │     │    predictions           │    │
│  └─────────────────────┘     └─────────────────────────┘    │
│                                                               │
│  ┌─────────────────────────────────────────────────────┐     │
│  │              FEATURE REGISTRY                        │     │
│  │  • Feature definitions (name, type, description)    │     │
│  │  • Data source and transformation logic             │     │
│  │  • Owner, version, creation date                    │     │
│  │  • Feature statistics (mean, min, max, distribution)│     │
│  └─────────────────────────────────────────────────────┘     │
│                                                               │
│  ┌─────────────────────────────────────────────────────┐     │
│  │              FEATURE PIPELINES                       │     │
│  │  • Batch pipelines (compute features from historical│     │
│  │    data, run nightly)                               │     │
│  │  • Streaming pipelines (compute features from real- │     │
│  │    time events, update continuously)                 │     │
│  └─────────────────────────────────────────────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Popular Feature Store Tools

| Tool | Type | Who Uses It | Key Feature |
|---|---|---|---|
| **Feast** | Open-source | Startups, mid-size companies | Simple, works with any data source. Most popular open-source option. |
| **Tecton** | Commercial (SaaS) | Large enterprises | Built by creators of Uber's Michelangelo. Real-time feature computation. |
| **Hopsworks** | Open-source + Commercial | Research, enterprises | Full ML platform. Strong Python integration. |
| **Amazon SageMaker Feature Store** | AWS service | AWS users | Integrated with SageMaker ML platform. |
| **Google Vertex AI Feature Store** | GCP service | GCP users | Integrated with Vertex AI ML platform. |
| **Databricks Feature Store** | Databricks platform | Databricks users | Integrated with Databricks Lakehouse. |

### Live Example: How Uber Uses Their Feature Store (Michelangelo)

Uber's "Michelangelo" platform has one of the most sophisticated feature stores:

```
UBER'S FEATURE STORE:
├── Stores 10,000+ features used across the company
├── Serves features for: ETA prediction, surge pricing, fraud detection,
│   driver matching, restaurant recommendations
├── 
├── Feature examples:
│   ├── user_avg_trip_distance_30d (batch — updated daily)
│   ├── driver_current_location (streaming — updated every 5 seconds)
│   ├── area_demand_last_15min (streaming — updated every minute)
│   ├── restaurant_avg_prep_time_7d (batch — updated daily)
│   └── surge_multiplier_current (streaming — computed in real-time)
├── 
├── Any ML team at Uber can:
│   ├── Browse the feature catalog to find existing features
│   ├── Reuse features (no duplicate computation)
│   ├── Request features via API for training or serving
│   └── Create new features that others can also use
└──
└── Impact: Reduced feature development time from weeks to hours
```

---

## 8.2 Offline vs Online Features

### The Two Modes of Feature Serving

| Aspect | Offline Features | Online Features |
|---|---|---|
| **When computed** | In batch (nightly, hourly) | In real-time (as events happen) |
| **What they represent** | Historical aggregations | Current state |
| **Latency requirement** | Minutes to hours is fine | Must be served in <1-10ms |
| **Storage** | Data warehouse (BigQuery, Snowflake) or data lake (S3) | Key-value store (Redis, DynamoDB) |
| **Used for** | Model training, batch predictions | Real-time predictions |
| **Size** | Can be very large (months/years of history) | Small (only latest values) |

### Offline Features — Explained

Offline features are computed from historical data in batch jobs, typically running nightly.

> **Analogy:** Your bank statement at the end of the month — it summarises all your transactions over 30 days. Computed once, valid for a while.

**Examples of offline features:**

```
Feature: user_total_purchases_last_30_days
├── Computed: Every night at 2 AM
├── Logic: SELECT COUNT(*) FROM orders WHERE user_id = ? AND date > now() - 30 days
├── Storage: Written to offline feature store (BigQuery table)
├── Used by: Recommendation model training, customer segmentation
└── Freshness: Up to 24 hours stale (computed last night)

Feature: restaurant_avg_rating
├── Computed: Every night
├── Logic: AVG(rating) FROM reviews WHERE restaurant_id = ?
├── Storage: Feature store offline table
├── Used by: Restaurant ranking model
└── Freshness: Up to 24 hours stale
```

### Online Features — Explained

Online features are computed or updated in real-time as events happen, and must be served instantly.

> **Analogy:** The "currently playing" status on Spotify — it changes the moment you press play or pause. It reflects the current state, not yesterday's state.

**Examples of online features:**

```
Feature: user_clicks_last_10_minutes
├── Computed: Continuously (streaming pipeline)
├── Logic: Count user click events in sliding 10-minute window
├── Storage: Redis (in-memory key-value store)
├── Used by: Real-time recommendation model (what to show next)
├── Latency: Must be served in <1ms
└── Freshness: Updated within seconds of each click

Feature: driver_current_location
├── Computed: Every 5 seconds (GPS ping from driver app)
├── Logic: Latest GPS coordinates from driver
├── Storage: Redis
├── Used by: Driver matching model (assign nearest driver to order)
├── Latency: <1ms
└── Freshness: 5 seconds old at most
```

### Using Both Together

Most real-world models use **both** offline and online features for the best predictions:

```
SWIGGY ETA PREDICTION — Feature Request:

Offline features (from batch store):
├── restaurant_avg_prep_time_30d:     18 min    (computed last night)
├── user_avg_order_frequency:          3/week    (computed last night)
├── route_avg_delivery_time_7d:        25 min    (computed last night)
└── driver_avg_speed_7d:               22 kmph   (computed last night)

Online features (from real-time store):
├── restaurant_current_active_orders:  7         (updated 30 seconds ago)
├── driver_current_location:           12.97,77.59 (updated 5 seconds ago)
├── current_traffic_level:             HIGH       (updated 1 minute ago)
└── current_weather:                   RAIN        (updated 5 minutes ago)

Combined → fed to model → predicted ETA: 42 minutes
```

The offline features provide stable baselines (how this restaurant usually performs). The online features provide current context (the restaurant is busy right now, it's raining). Together, they give the most accurate prediction.

---

## 8.3 Feature Pipelines

### What is a Feature Pipeline?

A **feature pipeline** is the automated process that computes features from raw data and loads them into the feature store. There are two types:

### Batch Feature Pipeline

Runs on a schedule (nightly, hourly) to compute features from historical data.

```
BATCH FEATURE PIPELINE (runs every night at 2 AM):

┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ Raw Data │───→│ Read from    │───→│ Compute      │───→│ Write to     │
│ (Data    │    │ data lake/   │    │ features     │    │ feature      │
│  Lake)   │    │ warehouse    │    │ (Spark/SQL)  │    │ store        │
└──────────┘    └──────────────┘    └──────────────┘    └──────────────┘

Example (Flipkart nightly pipeline):
├── Read: All yesterday's order and browse events from S3
├── Compute: For each user:
│   ├── total_orders_last_30_days
│   ├── avg_order_value_last_30_days
│   ├── favourite_category (most purchased)
│   ├── days_since_last_order
│   └── cart_abandonment_rate_last_30_days
├── Write: Updated features to offline feature store (BigQuery)
└── Duration: ~2 hours for 100M users
```

**Tools:** Apache Spark, Apache Airflow, dbt, SQL

### Streaming Feature Pipeline

Runs continuously, computing features from real-time events as they arrive.

```
STREAMING FEATURE PIPELINE (runs 24/7):

┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ Events   │───→│ Read from    │───→│ Compute      │───→│ Write to     │
│ (Real-   │    │ event stream │    │ features in  │    │ online       │
│  time)   │    │ (Kafka)      │    │ real-time    │    │ feature      │
│          │    │              │    │ (Flink)      │    │ store(Redis) │
└──────────┘    └──────────────┘    └──────────────┘    └──────────────┘

Example (HDFC fraud detection):
├── Read: Every card transaction from Kafka stream
├── Compute: For each card in real-time:
│   ├── txn_count_last_1_hour (sliding window)
│   ├── total_amount_last_1_hour
│   ├── unique_merchants_last_1_hour
│   ├── distance_from_last_txn_km
│   └── time_since_last_txn_seconds
├── Write: Updated features to online store (Redis)
└── Latency: Feature available within 100ms of transaction
```

**Tools:** Apache Kafka, Apache Flink, Spark Streaming, AWS Kinesis

### Batch + Streaming Combined Pipeline

```
                    ┌─────────────────────────────────┐
                    │        RAW DATA SOURCES          │
                    │   (databases, APIs, events)      │
                    └───────────────┬──────────────────┘
                                    │
                    ┌───────────────┴──────────────────┐
                    │                                   │
              ┌─────▼──────┐                    ┌──────▼─────┐
              │   BATCH    │                    │  STREAMING │
              │  PIPELINE  │                    │  PIPELINE  │
              │ (Airflow + │                    │ (Kafka +   │
              │  Spark)    │                    │  Flink)    │
              │ Runs: 2 AM │                    │ Runs: 24/7 │
              └─────┬──────┘                    └──────┬─────┘
                    │                                   │
              ┌─────▼──────┐                    ┌──────▼─────┐
              │  OFFLINE   │                    │   ONLINE   │
              │  STORE     │                    │   STORE    │
              │ (BigQuery) │                    │  (Redis)   │
              └─────┬──────┘                    └──────┬─────┘
                    │                                   │
                    │     ┌──────────────────┐          │
                    └────→│  MODEL SERVING   │←─────────┘
                          │  (combines both) │
                          └──────────────────┘
```

---

## 8.4 Data Consistency — The Training-Serving Skew Problem

### What is Training-Serving Skew?

**Training-serving skew** happens when features used during model training are different from features available during prediction (serving). This is one of the most common and dangerous bugs in ML systems.

> **Analogy:** You study for a Hindi exam but the actual exam is in English. You prepared (trained) on different data than what you're tested (served) on. No wonder you fail.

### Types of Training-Serving Skew

| Type | What Goes Wrong | Example |
|---|---|---|
| **Feature computation skew** | Training computes features one way, serving computes differently | Training: age = current_year - birth_year. Serving: age = current_date - birth_date (different formula → different values) |
| **Data distribution skew** | Training data has different distribution than live data | Training on 2023 data, but 2025 user demographics are different (more young users) |
| **Feature availability skew** | A feature available during training is not available at serving time | Training uses "credit_score" (available in historical data). At serving time, credit score hasn't been fetched yet → NULL → model breaks. |
| **Time-travel skew** | Training accidentally uses future information | Training data includes "days_until_cancellation" — but at prediction time, you don't know when (or if) the user will cancel! |

### How Feature Stores Prevent Skew

```
WITHOUT FEATURE STORE:

Training pipeline (Python):
  user_avg_spend = df.groupby('user_id')['amount'].mean()
  # Uses pandas, includes all historical data

Serving pipeline (Java):
  user_avg_spend = redis.get("user:" + userId + ":avg_spend")  
  # Different code, different language, might compute differently
  
RESULT: Skew! Training and serving compute features differently.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WITH FEATURE STORE:

Feature definition (defined ONCE in feature store):
  name: user_avg_spend_30d
  type: float
  description: "Average transaction amount in last 30 days"
  computation: SQL("SELECT AVG(amount) FROM transactions 
                    WHERE user_id = ? AND date > now() - 30")

Training: reads from feature store offline table
Serving:  reads from feature store online table
Both use the SAME definition → NO SKEW!
```

### Live Example: Training-Serving Skew at Zillow

Zillow's "Zestimate" home price prediction model suffered from training-serving skew:

```
WHAT HAPPENED:
├── Training: Model trained on features including "days_on_market"
│   (how long the house has been listed)
├── Serving: When predicting price for a NEW listing, 
│   "days_on_market" = 0 (just listed)
├── But in training data, "days_on_market" was always > 0
│   (historical listings that had been on market for a while)
├── The model never saw days_on_market = 0 during training
├── This caused systematic overestimation for new listings
│   
├── RESULT: Zillow bought houses at inflated prices
├── LOSS: $881 million
└── Zillow shut down their home-buying business entirely
```

### Best Practices for Preventing Skew

| Practice | How It Helps |
|---|---|
| **Use a feature store** | Single source of truth for feature definitions |
| **Point-in-time correctness** | When creating training data, only use features that were available at that point in time (no future data) |
| **Feature validation** | Automatically compare feature distributions between training and serving. Alert on significant differences. |
| **Share code** | Training and serving should use the same feature computation code (or the same feature store) |
| **Monitor feature values** | Track feature distributions in production. Alert when they deviate from training distribution. |

---

## 8.5 Real-Time Recommendation Systems

### How Real-Time Recommendations Work

A real-time recommendation system combines everything we've learned — feature stores, online/offline features, streaming pipelines — to deliver personalised recommendations in milliseconds.

### Architecture of a Real-Time Recommendation System

```
━━━ WHEN USER OPENS THE APP ━━━

1. User opens Myntra app
   └── Request: "Show recommendations for user USR-12345"

2. Feature Store serves features instantly (<5ms total):
   
   OFFLINE features (pre-computed nightly):
   ├── purchase_history_embedding: [0.23, 0.45, ...128 dims]
   ├── favourite_brands: ["Zara", "H&M", "Allen Solly"]
   ├── size_preference: "M"
   ├── price_range: Rs 500-3000
   └── style_preference: "casual"
   
   ONLINE features (real-time):
   ├── current_session_clicks: ["dress-456", "top-789"]
   ├── session_duration_seconds: 120
   ├── search_query: "red dress for party"
   └── current_time: "evening"

3. Candidate Generation (first filter — fast but rough):
   ├── From 10 million products → filter to 1000 candidates
   ├── Method: ANN (Approximate Nearest Neighbour) search
   │   using user_embedding vs product_embeddings
   └── Takes: ~10ms

4. Ranking Model (second filter — accurate but slower):
   ├── Score each of 1000 candidates
   ├── Model uses ALL features (offline + online + product features)
   ├── Outputs: relevance score for each product
   └── Takes: ~20ms

5. Business Logic (final filter):
   ├── Remove out-of-stock items
   ├── Apply diversity rules (not all from same brand)
   ├── Boost items on sale (business priority)
   ├── Boost items matching search query "red dress for party"
   └── Apply A/B test: 10% of users see experimental ranking

6. Return top 30 products to app
   └── Total time: <100ms (user doesn't notice any delay)

7. User clicks on "Red Party Dress" →
   ├── Click event sent to Kafka
   ├── Streaming pipeline updates online features
   │   (session_clicks now includes "red-dress-123")
   ├── Next recommendation request uses updated features
   └── Recommendations become more relevant with each click!
```

### The Two-Stage Recommendation Architecture

Almost all large-scale recommendation systems use two stages:

| Stage | Purpose | Speed | Accuracy | Input Size | Output Size |
|---|---|---|---|---|---|
| **Stage 1: Candidate Generation** | Quickly find a rough set of relevant items | Very fast (ANN search) | Lower (approximate) | Millions of items | ~1000 candidates |
| **Stage 2: Ranking** | Accurately rank the candidates | Slower (full model) | Higher (precise) | ~1000 candidates | Top 20-50 items |

**Why two stages?**
- Running the full ranking model on 10 million products would take too long (seconds)
- Candidate generation narrows to 1000 in milliseconds using cheap approximate methods
- Then the expensive ranking model only scores 1000 items (fast enough)

> **Analogy:** When buying a house, you first filter by city, budget, and BHK (candidate generation — eliminates 99% of options). Then you personally visit the shortlisted 10 houses and evaluate each carefully (ranking).

### Key Concept: Approximate Nearest Neighbour (ANN) Search

ANN search is how candidate generation works at scale:

```
Every user and product has an embedding (vector of numbers):
  User USR-123:    [0.8, 0.2, 0.5, 0.1, ...]
  Product PROD-A:  [0.7, 0.3, 0.5, 0.2, ...]  ← Similar! (close vectors)
  Product PROD-B:  [0.1, 0.9, 0.1, 0.8, ...]  ← Different (far vectors)

ANN search finds the 1000 products whose embeddings are 
closest to the user's embedding — in milliseconds, even with
10 million products.

Tools: FAISS (Facebook), ScaNN (Google), Pinecone, Weaviate, Milvus
```

### Live Examples of Real-Time Recommendation Systems

| Company | What's Recommended | Scale | Refresh Speed |
|---|---|---|---|
| **Netflix** | Movies and TV shows | 230M users, 15,000+ titles | Thumbnails change in real-time, rankings updated every few hours |
| **YouTube** | Videos | 2B users, 800M videos | Recommendations update with every video watched |
| **Amazon** | Products | 300M users, 350M products | "Frequently bought together" updates in real-time |
| **Spotify** | Songs and playlists | 600M users, 100M tracks | "Discover Weekly" (batch, weekly). Radio (real-time, adjusts as you skip) |
| **Swiggy** | Restaurants and dishes | 50M users, 200K restaurants | Updates based on current time, location, weather, and browsing |
| **LinkedIn** | Job postings, connections | 900M users, 60M companies | "Jobs you might be interested in" updates as you browse profiles |

### End-to-End Example: Building Spotify's "Discover Weekly"

```
BATCH PIPELINE (runs every Monday at 3 AM):
├── Input: Last 7 days of listening data for all 600M users
├── Compute user taste profile:
│   ├── User embedding from listening history
│   ├── Genre preferences (30% Bollywood, 25% pop, 20% rock, ...)
│   ├── Tempo preferences (prefers 100-130 BPM)
│   └── Artist affinity scores
├── Collaborative filtering:
│   ├── Find 1000 most similar users
│   ├── Get songs those similar users loved
│   └── Filter out songs user already knows
├── Ranking model:
│   ├── Score remaining candidates
│   ├── Apply diversity (mix genres, mix artists)
│   └── Select top 30 songs
├── Store playlist in feature store / playlist database
└── User opens Spotify Monday morning → sees personalised 30-song playlist

REAL-TIME PIPELINE (runs during listening):
├── User skips a song in Discover Weekly → negative signal
│   └── Skip event → Kafka → update user preferences in real-time
├── User adds a song to their library → positive signal
│   └── Like event → Kafka → update user preferences
├── User's "Radio" feature adjusts in real-time based on skips/likes
└── All signals feed into NEXT week's Discover Weekly generation
```

---

## Key Terms Glossary (Session 8)

| Term | Simple Meaning |
|---|---|
| **Feature Store** | A centralised system that stores, manages, and serves ML features consistently for training and serving |
| **Offline Store** | Part of feature store that holds historical feature values for training (stored in warehouse) |
| **Online Store** | Part of feature store that holds latest feature values for real-time serving (stored in Redis/DynamoDB) |
| **Feature Pipeline** | Automated process that computes features from raw data and loads them into the feature store |
| **Training-Serving Skew** | When features differ between training and production, causing model to perform worse than expected |
| **Point-in-Time Correctness** | Ensuring training data only uses features that were actually available at that historical time |
| **Candidate Generation** | First stage of recommendation: quickly narrow millions of items to ~1000 candidates |
| **Ranking Model** | Second stage: accurately score and rank the candidate items |
| **ANN (Approximate Nearest Neighbour)** | Fast search that finds similar items in a large collection using embedding vectors |
| **Embedding Similarity** | Measuring how similar two items are by comparing their embedding vectors (cosine similarity) |
| **Feast** | Most popular open-source feature store |
| **Redis** | In-memory key-value store used for ultra-fast online feature serving |
| **FAISS** | Facebook's library for fast similarity search on embedding vectors |
| **Sliding Window** | A moving time frame for computing real-time features (e.g., "last 10 minutes") |

---

*End of Session 8*
