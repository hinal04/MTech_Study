# Session 8: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is a Feature Store? Why do we need one?

**Answer:**
A Feature Store is a centralised platform that manages, stores, and serves machine learning features for both model training and production inference.

**Simple analogy:** A Feature Store is like a well-organised kitchen pantry. Instead of every cook (data scientist) going to the farm (raw data) to pick vegetables (extract features) every time they want to cook (train a model), the pantry (feature store) has pre-washed, pre-cut, ready-to-use ingredients (features) that any cook can grab.

**Problems without a Feature Store:**

| Problem | What Happens |
|---|---|
| **Duplicate work** | 5 data scientists at Flipkart independently write code to calculate `user_avg_order_value`. Each writes it slightly differently. |
| **Training-serving skew** | Features calculated one way in Python for training, but differently in Java for production → model performance degrades. |
| **No feature reuse** | Team A builds great features for fraud detection. Team B, building a recommendation system, doesn't know these features exist and builds from scratch. |
| **Inconsistent features** | Same feature defined differently by different teams. `avg_order_value` calculated over 30 days by one team, 90 days by another. |
| **Slow model deployment** | Data scientist creates features in Jupyter notebook → production engineer has to re-implement in Java/Scala → takes weeks. |

**What a Feature Store provides:**

| Capability | How It Helps |
|---|---|
| **Centralised feature repository** | All features stored in one place with documentation, owner, and version |
| **Feature reuse** | Any team can search and reuse existing features |
| **Consistent features** | Same code computes features for both training and serving |
| **Offline + Online serving** | Provides features for batch training (offline) AND real-time inference (online) |
| **Feature versioning** | Track changes to feature definitions over time |
| **Monitoring** | Detect data drift, feature quality issues, staleness |

---

## Q2: Explain the architecture of a Feature Store. What are its key components?

**Answer:**

```
                     ┌────────────────────────────┐
                     │      Feature Registry       │ (metadata, definitions, owners)
                     └────────────────────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         │                         │                         │
         ▼                         ▼                         ▼
┌──────────────┐         ┌──────────────┐         ┌──────────────────┐
│ Feature       │         │ Offline       │         │ Online            │
│ Pipelines     │         │ Store         │         │ Store             │
│ (Compute)     │────────→│ (Historical)  │         │ (Real-time)       │
│ Batch/Stream  │         │ S3, BigQuery  │────────→│ Redis, DynamoDB   │
└──────────────┘         └──────────────┘         └──────────────────┘
                                │                         │
                                ▼                         ▼
                         ┌──────────────┐         ┌──────────────────┐
                         │ Model         │         │ Model Serving     │
                         │ Training      │         │ (Real-time        │
                         │ (Batch)       │         │  Inference)       │
                         └──────────────┘         └──────────────────┘
```

**Key components:**

| Component | What It Does | Tools |
|---|---|---|
| **Feature Registry** | Catalog of all features — name, description, owner, version, data type, how it's computed | Part of Feast, Tecton, Hopsworks |
| **Feature Pipelines** | Code that computes features from raw data — can be batch (Spark) or streaming (Flink) | Spark, Flink, Python scripts |
| **Offline Store** | Stores historical feature values for model training. Optimised for bulk reads. | S3, BigQuery, Hive, Redshift |
| **Online Store** | Stores the latest feature values for real-time inference. Optimised for low-latency lookups. | Redis, DynamoDB, Bigtable |
| **Feature Serving API** | Provides features to models during training (offline) and inference (online) | REST API, gRPC |
| **Monitoring** | Tracks feature freshness, drift, quality, and usage | Grafana, custom dashboards |

---

## Q3: Compare Offline Features and Online Features. Give examples of each.

**Answer:**

| Aspect | Offline Features | Online Features |
|---|---|---|
| **What** | Historical feature values computed in batch | Latest feature values served in real-time |
| **Latency** | Minutes to hours (batch computation) | Milliseconds (must serve instantly) |
| **Storage** | Data lake/warehouse (S3, BigQuery) | In-memory stores (Redis, DynamoDB) |
| **Updated** | Periodically (hourly, daily, weekly) | Continuously or near-real-time |
| **Used for** | Model training | Model inference (production predictions) |
| **Volume** | Large — entire history (millions/billions of rows) | Small — only latest values (one row per entity) |

**Examples for Swiggy:**

| Feature | Type | Why |
|---|---|---|
| `user_total_orders_all_time = 250` | Offline | Doesn't change by the minute. Computed nightly. Used for training. |
| `user_avg_order_value_90d = ₹380` | Offline | Aggregated over 90 days. Updated daily. |
| `user_orders_last_30_days = 12` | Offline → Online | Computed daily (batch) and materialised to Redis for fast serving. |
| `restaurant_current_active_orders = 15` | Online | Changes every second. Must be real-time for accurate ETA. |
| `rider_current_location = (12.91, 77.61)` | Online | GPS updates every few seconds. |
| `current_weather_is_raining = 1` | Online | Must be real-time — weather affects delivery time NOW. |
| `user_favourite_cuisine = "Biryani"` | Offline | Computed weekly from order history. Doesn't change often. |

**Key insight:** Most systems use BOTH:
- Train the model using offline features (historical data with labels)
- Serve the model using online features (latest values for real-time prediction)
- The feature store ensures the SAME feature logic is used in both contexts → prevents training-serving skew.

---

## Q4: What are Batch and Streaming Feature Pipelines? Compare them.

**Answer:**

### Batch Feature Pipeline
- **What:** Computes features from historical data at scheduled intervals (hourly, daily, weekly).
- **How:** Spark/SQL job reads from data warehouse → computes features → writes to offline store (and optionally materialises to online store).
- **Example:** Every night at 2 AM, compute `user_avg_order_value_30d` for all Flipkart users from the orders table.

### Streaming Feature Pipeline
- **What:** Computes features in real-time as events arrive.
- **How:** Flink/Kafka Streams reads from Kafka → computes features on-the-fly → writes to online store.
- **Example:** Every time a new order event arrives on Kafka, update `user_orders_today_count` in Redis immediately.

| Aspect | Batch Pipeline | Streaming Pipeline |
|---|---|---|
| **Freshness** | Stale (hours/days old) | Fresh (seconds old) |
| **Complexity** | Simple — read, compute, write | Complex — handle late events, out-of-order data, failures |
| **Cost** | Lower — runs periodically | Higher — runs 24/7 |
| **Use case** | Features that don't change fast | Features that must be up-to-the-minute |
| **Tools** | Spark, Airflow, dbt | Flink, Kafka Streams, Spark Structured Streaming |
| **Example features** | `lifetime_order_count`, `avg_rating`, `favourite_category` | `orders_in_last_1_hour`, `current_cart_value`, `last_viewed_product` |

**In practice, most companies use both:**
- Batch pipeline computes 80% of features (historical aggregations)
- Streaming pipeline computes 20% of features (real-time context)
- Feature store serves both to the model seamlessly

---

## Q5: What is Training-Serving Skew? Why is it the biggest enemy of production ML?

**Answer:**
Training-Serving Skew occurs when the features a model receives during production inference are different from what it received during training. The model was trained on one "version" of reality but serves predictions on a different version.

**Simple analogy:** Imagine studying for a Hindi exam using an English textbook. Your preparation (training) was in a different language (feature set) than the actual test (serving). Your performance will be terrible — not because you're dumb, but because the input was different.

**Why it's so dangerous:**
- The model doesn't crash or throw errors — it silently produces bad predictions.
- Accuracy in offline testing (98%) hides the problem. Production accuracy might be 70%, but nobody notices until business metrics drop.
- It's the #1 cause of ML models failing in production.

**Real impact:** Google's paper "Hidden Technical Debt in Machine Learning Systems" identified training-serving skew as one of the most common and costly issues in production ML.

---

## Q6: Explain the 4 types of Training-Serving Skew with examples.

**Answer:**

### Type 1: Feature Computation Skew
- **What:** Same feature is computed differently during training vs serving.
- **Example:** During training (in Python/Pandas), `avg_order_value` is calculated as `mean(order_values)`, which handles NaN by ignoring them. During serving (in Java), the production code uses a different function that includes NaN as 0 → different average → different prediction.
- **How Feature Store prevents it:** Same feature computation code used for both training and serving.

### Type 2: Feature Freshness Skew
- **What:** Features used during training were fresh, but in production they're stale.
- **Example:** During training, `user_last_login_days_ago` was computed from the actual last login date. In production, this feature is only updated daily (batch pipeline), so a user who logged in 1 hour ago still has `last_login_days_ago = 1` instead of `0.04`.
- **Impact:** Model was trained with precise values but sees rounded/stale values in production.
- **How Feature Store prevents it:** Online store maintains fresh values. Streaming pipelines update real-time features.

### Type 3: Data Distribution Skew
- **What:** The statistical distribution of features changes between training and serving.
- **Example:** A Flipkart recommendation model was trained on data from January-June (no sale season). In production during Big Billion Days (October), user behaviour is completely different — higher spending, more electronics, bulk buying. The model's training data doesn't represent this distribution.
- **How Feature Store prevents it:** Feature monitoring detects distribution drift and triggers retraining alerts.

### Type 4: Feature Leakage Skew
- **What:** Features available during training are not available at prediction time.
- **Example:** Training data has `order_status = cancelled` as a feature. This is used to predict customer churn. But at prediction time, you're predicting churn for a customer who hasn't cancelled yet — `order_status` is unknown.
- **How Feature Store prevents it:** Feature registry documents which features are available at serving time. Point-in-time joins prevent leakage during training data creation.

**Summary table:**

| Skew Type | Cause | Symptom | Prevention |
|---|---|---|---|
| **Computation** | Different code in training vs serving | Subtle accuracy drop | Same feature code in Feature Store |
| **Freshness** | Stale features in production | Predictions lag reality | Online store + streaming pipelines |
| **Distribution** | World changes, model doesn't | Model works well sometimes, badly at other times | Drift monitoring + retraining triggers |
| **Leakage** | Using future information during training | Inflated training accuracy, poor production accuracy | Point-in-time joins, feature availability documentation |

---

## Q7: How does a Feature Store prevent Training-Serving Skew? Explain the mechanism.

**Answer:**
A Feature Store prevents skew through several mechanisms:

### 1. Single Feature Definition
- Feature is defined ONCE in the feature store.
- Same code computes the feature for both offline (training) and online (serving) stores.
- **Before feature store:** Training team computes `avg_order_value` in Python. Production team re-implements in Java. Subtle differences creep in.
- **With feature store:** `avg_order_value` is defined once. Feature pipeline computes it and writes to both stores.

### 2. Point-in-Time Joins
- **Problem:** When creating training data, you must ensure features represent what was known AT THAT TIME, not today's values.
- **Example:** Training to predict if a Flipkart order from January 15 would be returned. The feature `user_total_orders` should be the count AS OF January 15, not today's count.
- **Without feature store:** Data scientist queries the current database → gets today's count → future leakage.
- **With feature store:** The offline store has time-stamped feature values. Point-in-time join automatically retrieves features as they existed on January 15.

### 3. Feature Freshness Guarantees
- Feature store tracks when each feature was last updated.
- If an online feature hasn't been updated in 2 hours (SLA breach), an alert fires.
- This ensures the model never serves predictions on stale features without awareness.

### 4. Feature Monitoring and Drift Detection
- Feature store monitors the statistical distribution of each feature over time.
- If `avg_order_value` suddenly shifts from ₹500 to ₹2000 (Big Billion Days), the monitoring system detects drift.
- Teams can then decide: retrain the model or add this distribution to training data.

### 5. Feature Versioning
- When a feature definition changes (e.g., `avg_order_value` now includes cancelled orders), the feature store creates a new version.
- Models are pinned to specific feature versions → no surprise changes.

---

## Q8: What happened with Zillow's iBuying failure? Explain the case study.

**Answer:**
Zillow's iBuying program (Zillow Offers) is one of the most famous ML failure case studies. Zillow lost over **$500 million** and laid off 25% of its workforce.

**What Zillow Did:**
- Zillow Offers was an "instant buying" program where Zillow would buy your house directly (instead of you listing it and waiting for a buyer).
- They used an ML model called "Zestimate" to predict house prices and decide how much to offer.
- The idea: buy houses at predicted price → do minor renovations → sell at profit.

**What Went Wrong:**

### 1. Training-Serving Skew (Feature Freshness)
- The model was trained on historical housing data from 2019-2020 (relatively stable market).
- By 2021, the housing market was wildly different — prices were rising 15-20% per year due to low interest rates and COVID-era demand.
- **Freshness skew:** The model's training data didn't reflect current market dynamics.

### 2. Feature Quality Issues
- The model relied on features like `comparable_sale_prices` (recent sales of similar homes nearby).
- In fast-moving markets, "comparable" sales from even 3 months ago were outdated — prices had jumped significantly.
- The model consistently UNDERPREDICTED how much prices would rise → Zillow overpaid.

### 3. Distribution Shift
- Training data represented a stable market. Production data was an abnormally hot market.
- When the market started cooling in late 2021, the model was still calibrated for the hot market → now it was overpaying for houses in a declining market.

### 4. Feedback Loop Problem
- Zillow was buying so many houses (hundreds per week) that it was actually affecting local market prices.
- The model's predictions influenced the market it was trying to predict — a self-fulfilling prophecy that inflated prices.

**Financial Impact:**
- Zillow bought ~27,000 homes in 2021
- Had to sell many at a loss — sometimes $20,000-$50,000 below purchase price
- Total loss: **$569 million** in Q3 2021 alone
- Shut down iBuying division entirely in November 2021
- Laid off ~2,000 employees (25% of workforce)

**Lessons for ML practitioners:**

| Lesson | What to Do |
|---|---|
| **Feature freshness matters** | Features must reflect current reality, not last quarter |
| **Monitor for distribution shift** | Set up automated drift detection |
| **Don't trust offline metrics alone** | A model with 95% test accuracy can still fail in production |
| **Understand feedback loops** | Your model's predictions can change the world it's predicting |
| **Have human oversight** | Don't let a model make $500M decisions autonomously |
| **Use a feature store** | Would have detected feature staleness and distribution drift |

---

## Q9: List the major Feature Store tools and compare them.

**Answer:**

| Tool | Type | Best For | Key Feature |
|---|---|---|---|
| **Feast** | Open-source | Teams wanting flexibility and no vendor lock-in | Most popular open-source option. Works with any cloud. |
| **Tecton** | Managed (cloud) | Companies wanting a managed solution with real-time capabilities | Built by the creators of Uber's Michelangelo. Strong real-time features. |
| **Hopsworks** | Open-source + managed | Teams wanting both batch and streaming in one platform | Integrated ML platform with feature store, training, and serving. |
| **Databricks Feature Store** | Integrated with Databricks | Teams already using Databricks/Delta Lake | Native integration with Spark and MLflow. |
| **Amazon SageMaker Feature Store** | AWS managed | Teams on AWS ecosystem | Tight integration with SageMaker for training and deployment. |
| **Google Vertex AI Feature Store** | GCP managed | Teams on GCP ecosystem | Integration with BigQuery and Vertex AI. |

**Choosing a Feature Store:**

```
Are you on a single cloud (AWS/GCP)?
├── Yes, AWS → SageMaker Feature Store
├── Yes, GCP → Vertex AI Feature Store
└── No / Multi-cloud / On-prem
    ├── Want open-source? → Feast
    ├── Need real-time streaming features? → Tecton
    └── Already using Databricks? → Databricks Feature Store
```

**Indian company examples:**
- Flipkart, Swiggy (AWS-heavy) → SageMaker Feature Store or Feast on AWS
- Razorpay (multi-cloud) → Feast (open-source, cloud-agnostic)
- Large enterprises (TCS, Infosys building for clients) → Feast or Tecton depending on client's cloud

---

## Q10: What is a Real-Time Recommendation System? Explain the two-stage architecture.

**Answer:**
A real-time recommendation system generates personalised recommendations for each user within milliseconds when they open an app or browse a page.

**The challenge:** Flipkart has 100 million users and 10 million products. You can't score all 10 million products for every user in real-time — it would take hours. Solution: **two-stage architecture**.

### Stage 1: Candidate Generation (narrow down 10M → 1000)
- **Goal:** Quickly narrow down millions of items to a few hundred/thousand candidates.
- **Latency budget:** 10-50ms
- **Methods:**
  - **Collaborative Filtering:** "Users who bought what you bought also bought these" → retrieve 200 items
  - **Content-based:** "Items similar to what you recently viewed" → retrieve 200 items
  - **ANN (Approximate Nearest Neighbour) Search:** Convert all products to embeddings. Find 500 products closest to the user's embedding in vector space. → fast, millisecond-level
  - **Popularity-based:** Add top trending items as candidates → 100 items
- **Result:** ~1000 candidates from multiple sources

### Stage 2: Ranking (rank 1000 → top 20)
- **Goal:** Score and rank the 1000 candidates precisely. Show the top 20.
- **Latency budget:** 20-50ms
- **Method:** A sophisticated ML model (typically a neural network or gradient-boosted tree) scores each candidate.
- **Features used:**
  - User features (from feature store online): preferences, demographics, purchase history
  - Item features (from feature store): price, rating, category, recency
  - Context features: time of day, device, current session behaviour
  - User-item interaction features: has the user viewed this item? Has the user bought from this brand?
- **Result:** Top 20 items ranked by predicted relevance/purchase probability

**Architecture diagram:**

```
User opens Flipkart app
         │
         ▼
┌─────────────────────┐
│ Candidate Generation │ (10M items → 1000 candidates)
│   - ANN Search       │ (50ms)
│   - Collaborative    │
│   - Trending         │
└────────┬────────────┘
         │ 1000 items
         ▼
┌─────────────────────┐         ┌──────────────┐
│    Ranking Model     │←───────│ Feature Store │ (user + item features)
│  (Neural Network)    │         │  (Online)     │
│  Score each item     │         └──────────────┘
└────────┬────────────┘
         │ Top 20 items (ranked)
         ▼
    User's home page / "Recommended for you"
```

**Why two stages?**
- Stage 1 is fast but rough (uses simple signals)
- Stage 2 is precise but slow (uses rich features and complex model)
- Together: fast enough for real-time AND accurate enough for good recommendations

---

## Q11: What is ANN (Approximate Nearest Neighbour) Search? Why is it used in recommendations?

**Answer:**
ANN search finds the most similar items to a query in a large dataset without checking every single item.

**The problem:**
- Flipkart has 10 million products, each represented as a 128-dimensional embedding vector.
- To find the 100 most similar products to what a user likes, you'd need to calculate distance to ALL 10 million products → takes seconds (too slow for real-time).
- **Exact nearest neighbour** is O(n) — check every item. Not feasible in real-time.

**ANN solution:**
- Build an index structure that organises vectors into clusters/partitions.
- When a query comes in, only search the nearby clusters — check ~1% of items instead of 100%.
- Result is approximate (might miss a few truly nearest items) but 100-1000x faster.
- Accuracy: typically 95-99% (finds 95 of the true top 100).

**Popular ANN algorithms:**

| Algorithm | How It Works | Used By |
|---|---|---|
| **HNSW (Hierarchical Navigable Small World)** | Builds a graph where similar items are connected. Navigate the graph to find neighbours. | Spotify, Pinterest |
| **IVF (Inverted File Index)** | Clusters vectors using K-means. Only searches the nearest clusters. | Facebook (FAISS library) |
| **LSH (Locality Sensitive Hashing)** | Hashes similar vectors to the same bucket. Only compares within buckets. | Google |
| **ScaNN** | Google's algorithm combining quantisation and search. Very fast on large scale. | Google, YouTube |

**Libraries/Tools:**

| Tool | Creator | Key Feature |
|---|---|---|
| **FAISS** | Meta (Facebook) | Most popular. GPU-accelerated. Handles billions of vectors. |
| **Annoy** | Spotify | Lightweight. Used by Spotify for song recommendations. |
| **ScaNN** | Google | Optimised for Google-scale (billions of items). |
| **Pinecone** | Pinecone | Managed vector database (cloud service). |
| **Milvus** | Open-source | Full-featured vector database. |

**Real-world example — Myntra "Similar Products":**
1. All 10M products have 128-dimensional image embeddings (from CNN).
2. FAISS index built on all 10M embeddings.
3. User views a red kurta → get its embedding → query FAISS → find 100 visually similar products in <10ms.
4. Show these as "Similar Products You May Like."

---

## Q12: Explain the Spotify Discover Weekly case study. How does it work?

**Answer:**
Discover Weekly is Spotify's most successful recommendation feature — a personalised playlist of 30 songs delivered every Monday to each of its 500+ million users. It's responsible for billions of streams.

**How it works (simplified architecture):**

### Step 1: Build User Taste Profiles
- Represent each user as a vector based on their listening history.
- **Method:** Collaborative filtering using matrix factorisation.
- If User A and User B listen to 80% of the same songs, they have similar taste vectors.

### Step 2: Build Song Embeddings
- Each song is represented as a 128-dimensional embedding vector.
- **Method 1:** Collaborative filtering — songs listened to by similar users get similar vectors.
- **Method 2:** NLP on playlists — treat playlists like "sentences" and songs like "words." Use Word2Vec-style training to learn song embeddings. Songs that appear in the same playlists get similar embeddings.
- **Method 3:** Audio features — CNN on the raw audio waveform to capture tempo, key, energy, danceability.

### Step 3: Candidate Generation
- For each user, find songs that are:
  - Similar to songs they've listened to (ANN search on song embeddings)
  - Listened to by users with similar taste (collaborative filtering)
  - NOT songs they've already heard (filter out known songs)
- This generates ~1000 candidates per user.

### Step 4: Ranking and Filtering
- Rank candidates by predicted enjoyment score.
- Apply diversity rules: not all from the same artist, mix of genres, include some exploration.
- Select top 30 songs.

### Step 5: Feedback Loop
- Monday: Playlist delivered
- All week: Track which songs user plays, skips, saves, or adds to playlist
- This feedback becomes training data for next week's model
- Model continuously improves based on user reactions

**Key technical details:**

| Component | Technology |
|---|---|
| User embeddings | Collaborative filtering (matrix factorisation) |
| Song embeddings | Word2Vec-style on playlists + audio CNN |
| Candidate retrieval | ANN search using Annoy library |
| Ranking | Gradient-boosted trees / neural network |
| Feature store | Stores user preferences, listening history, song features |
| Scale | 500M+ users × 100M+ songs, 30 recommendations each, every Monday |

**Why it works so well:**
1. **Freshness:** Updated weekly with new songs
2. **Serendipity:** Surfaces songs the user wouldn't discover on their own
3. **Personalisation:** Every user gets a unique playlist
4. **Feedback loop:** Gets better every week based on user behaviour

---

## Q13: Scenario — Identify the type of Training-Serving Skew in each situation.

**Answer:**

### Situation 1: "Swiggy's delivery time model was 92% accurate last month but dropped to 75% this month without any code changes."
- **Skew type:** Distribution Shift
- **Why:** The real world changed (maybe monsoon season started, or a new festival week), but the model's training data doesn't include these patterns. The statistical distribution of features like `is_raining`, `order_volume` has shifted.
- **Fix:** Retrain with recent data including monsoon patterns. Set up drift monitoring.

### Situation 2: "HDFC Bank's fraud model catches 95% of fraud in offline testing but only 60% in production."
- **Skew type:** Feature Computation Skew
- **Why:** Most likely, features are computed differently in training (Python/Pandas with specific NaN handling) vs production (Java with different NaN handling). Also possible: feature leakage — training features include information not available at prediction time.
- **Fix:** Use a feature store to ensure same computation. Audit all features for availability at serving time.

### Situation 3: "Flipkart's recommendation model works great on weekdays but poorly during Big Billion Days."
- **Skew type:** Distribution Shift
- **Why:** User behaviour during sale events (higher spending, bulk buying, electronics-heavy) is completely different from normal weekdays. Training data is mostly from normal periods.
- **Fix:** Include sale-period data in training. Add `is_sale_period` as a feature. Consider a separate model for sale events.

### Situation 4: "A model to predict stock price direction was trained using yesterday's closing price as a feature, but in production, yesterday's price isn't available until 30 minutes after market open."
- **Skew type:** Feature Freshness Skew
- **Why:** During training, yesterday's close is always available (historical data). In production, there's a 30-minute delay in data pipeline → feature is stale or missing.
- **Fix:** Ensure feature pipeline delivers yesterday's close before market opens. Or use the feature as of the previous available point.

### Situation 5: "A customer churn model used 'number_of_complaints_resolved' as a feature, which was 98% accurate in testing but useless in production."
- **Skew type:** Feature Leakage
- **Why:** `complaints_resolved` can only be counted AFTER the resolution period. At prediction time (predicting if a customer will churn next month), you don't know how many complaints will be resolved.
- **Fix:** Remove this feature. Replace with `number_of_open_complaints` (available at prediction time).

---

## Q14: How does Point-in-Time Join work in a Feature Store? Why is it critical?

**Answer:**
Point-in-Time Join ensures that when creating training data, features reflect what was known at the TIME of the event — not what we know today.

**The problem without Point-in-Time Join:**

Imagine training a model to predict if a Swiggy order placed on **January 15 at 7 PM** would be delayed.

| Feature | Wrong way (no point-in-time) | Right way (point-in-time) |
|---|---|---|
| `restaurant_avg_prep_time` | 25 min (today's average, including post-January data) | 22 min (average as of January 15) |
| `user_total_orders` | 150 (total as of today) | 120 (total as of January 15) |
| `restaurant_rating` | 4.2 (today's rating) | 4.5 (rating as of January 15 — it dropped later) |

**Without point-in-time:** The training data contains future information. The model learns from data that wouldn't have been available on January 15 → **leakage** → inflated training accuracy → poor production performance.

**How Point-in-Time Join works:**

```
Training Examples:                    Feature Store (time-stamped values):

Order_ID | Event_Time           |    User_ID | Feature      | Timestamp      | Value
---------|------------------    |    --------|----------    |-------------   |------
1001     | Jan 15, 7:00 PM      |    U1      | total_orders | Jan 10         | 118
1002     | Jan 20, 8:30 PM      |    U1      | total_orders | Jan 15         | 120
1003     | Feb 1, 6:00 PM       |    U1      | total_orders | Jan 20         | 122
                                |    U1      | total_orders | Feb 1          | 125

Point-in-Time Join:
- Order 1001 (Jan 15) → gets total_orders = 118 (latest value BEFORE Jan 15)
- Order 1002 (Jan 20) → gets total_orders = 120 (latest value BEFORE Jan 20)
- Order 1003 (Feb 1) → gets total_orders = 122 (latest value BEFORE Feb 1)
```

**The feature store automatically matches each training example with feature values from BEFORE that example's timestamp.** This prevents any future leakage.

**Why it's critical:**
- Without it, training accuracy is inflated (maybe 95%)
- With it, training accuracy is honest (maybe 88%) — but this 88% actually holds in production
- Feature stores like Feast and Tecton have built-in point-in-time join functionality

---

## Q15: Design a real-time recommendation system for Swiggy (food delivery). What features go in the offline vs online store?

**Answer:**

### System Architecture

```
User opens Swiggy → API call with (user_id, location, time)
         │
         ▼
┌──────────────────────┐
│ Candidate Generation  │
│ - Nearby restaurants  │ (location-based filter)
│ - User's past orders  │ (collaborative filtering)
│ - Trending in area    │ (popularity)
│ → 200 restaurants     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐      ┌─────────────────────┐
│   Ranking Model      │←─────│    Feature Store     │
│ (Score each of 200)  │      │ Offline + Online     │
│ Neural network       │      └─────────────────────┘
└──────────┬───────────┘
           │
           ▼
    Top 20 restaurants on home screen
```

### Offline Store Features (updated daily/hourly)

| Feature | Update Frequency | Why Offline |
|---|---|---|
| `user_favourite_cuisine` | Daily | Doesn't change often |
| `user_avg_order_value` | Daily | Aggregation over history |
| `user_preferred_meal_time` | Weekly | Long-term preference |
| `user_dietary_preference = veg/non-veg` | Monthly | Rarely changes |
| `restaurant_avg_rating` | Hourly | Changes slowly |
| `restaurant_avg_prep_time` | Hourly | Historical average |
| `restaurant_cuisine_type` | Daily | Catalog data, rarely changes |
| `restaurant_price_range` | Daily | Menu pricing |
| `user_restaurant_order_count` (per restaurant) | Daily | How often user orders from each restaurant |

### Online Store Features (updated in real-time)

| Feature | Update Frequency | Why Online |
|---|---|---|
| `restaurant_is_open = 1/0` | Real-time | Can't show closed restaurants |
| `restaurant_current_active_orders` | Real-time | Affects prep time NOW |
| `estimated_delivery_time` | Real-time | Must reflect current traffic/weather |
| `rider_availability_in_area` | Real-time | No riders = longer delivery |
| `is_raining` | Real-time (weather API) | Affects delivery time and food choice |
| `current_hour_of_day` | Real-time | Lunch vs dinner recommendations |
| `user_current_location` | Real-time (GPS) | Determines nearby restaurants |
| `restaurant_current_offers` | Real-time | Discounts drive clicks |
| `user_last_session_viewed_restaurants` | Real-time (streaming) | What they just browsed (session-level personalisation) |

### Model Features for Ranking

The ranking model combines offline + online features:

```
Score(user, restaurant) = f(
    user_favourite_cuisine,            // offline
    user_avg_order_value,              // offline
    restaurant_avg_rating,             // offline
    restaurant_cuisine_type,           // offline
    estimated_delivery_time,           // online
    is_raining,                        // online
    restaurant_current_offers,         // online
    user_restaurant_order_count,       // offline
    distance_user_to_restaurant        // online (computed from GPS)
)
```

---

## Q16: What is the Cold Start problem in recommendation systems? How is it solved?

**Answer:**
The Cold Start problem occurs when the recommendation system has no data about a new user or a new item, making it impossible to generate personalised recommendations.

**Two types:**

### 1. New User Cold Start
- **Problem:** A new user just signed up on Flipkart. No purchase history, no browsing data, no preferences known.
- **Solutions:**

| Solution | How It Works | Example |
|---|---|---|
| **Ask preferences** | During onboarding, ask user to select interests | Spotify asks "Choose 3 artists you like" during signup |
| **Popularity-based** | Show globally popular items until personal data is available | Swiggy shows "Popular in your area" for new users |
| **Demographic-based** | Use age, location, gender for initial recommendations | 25-year-old in Bangalore → show trending cafes and biryani places |
| **Contextual** | Use time, location, device for initial signals | 12 PM in Koramangala → show lunch restaurants |
| **Explore-exploit** | Initially explore (show diverse items), then exploit (personalise as data accumulates) | First 10 sessions: diverse. After that: personalised. |

### 2. New Item Cold Start
- **Problem:** A new restaurant just joined Swiggy. No ratings, no order history.
- **Solutions:**

| Solution | How It Works | Example |
|---|---|---|
| **Content-based** | Use item attributes (cuisine, price range) to match with users | New pizza place → show to users who order pizza often |
| **Similar items** | Find items with similar features and bootstrap from their data | New Chinese restaurant → assume similar patterns to existing Chinese restaurants |
| **Boosted exposure** | Artificially promote new items to gather initial data | Swiggy gives new restaurants extra visibility for first 2 weeks |
| **Hybrid approach** | Combine content features with limited collaborative signals | As orders come in, blend content-based (attributes) with collaborative (user behaviour) |

---

## Q17: Explain the two-stage Candidate Generation + Ranking architecture with a Flipkart example.

**Answer:**

**The math problem:** Flipkart has 100M users and 10M products. Scoring every product for every user = 10^15 computations. Impossible in real-time.

**Solution: Two stages.**

### Stage 1: Candidate Generation (10M → 500 items, <50ms)

Multiple candidate sources run in parallel:

| Source | Method | Items Retrieved | Example |
|---|---|---|---|
| **Collaborative Filtering** | "Users like you bought these" | 100 | Users who bought iPhone cases also bought AirPods |
| **Content-Based** | "Similar to what you viewed" | 100 | You viewed Nike shoes → show similar Adidas shoes |
| **ANN Search** | Find nearest products in embedding space | 200 | User embedding → FAISS → 200 nearest products |
| **Trending** | Top sellers in user's category interest | 50 | Trending in Electronics this week |
| **Recently Viewed** | Items user saw but didn't buy | 50 | The laptop you viewed 3 times but didn't purchase |

**After deduplication: ~500 unique candidate items.**

### Stage 2: Ranking (500 → Top 20, <50ms)

A sophisticated ML model scores each of the 500 candidates:

```
For each candidate product:
    score = RankingModel(
        user_features,     // from Feature Store (online)
        item_features,     // from Feature Store (online)
        context_features,  // real-time: time, device, location
        interaction_features // user-item: has_viewed, has_carted
    )

Sort by score → return top 20
```

**Ranking model features:**

| Feature Type | Examples |
|---|---|
| **User features** | avg_order_value, favourite_category, price_sensitivity, account_age |
| **Item features** | price, rating, num_reviews, category, discount_pct, return_rate |
| **User-Item features** | has_viewed (1/0), view_count, has_carted (1/0), category_match_score |
| **Context features** | hour_of_day, is_weekend, device_type, is_sale_period |

**Final pipeline:**

```
Time breakdown for a single request:
  - Candidate Generation: 30ms (parallel sources)
  - Feature retrieval from online store: 5ms
  - Ranking model scoring (500 items): 15ms
  - Post-processing (diversity, business rules): 5ms
  - Total: ~55ms (well within 100ms SLA)
```

---

## Q18: Scenario — Design a Feature Store implementation for CRED's credit card bill payment platform.

**Answer:**

**CRED's ML use cases:**
1. Personalised reward recommendations
2. Credit card offer matching
3. Cashback prediction
4. User engagement scoring
5. Payment behaviour prediction

**Feature Store Design:**

### Feature Registry (what features exist)

| Feature Name | Entity | Type | Pipeline | Freshness |
|---|---|---|---|---|
| `user_credit_score_band` | User | Offline | Batch (monthly from CIBIL) | Monthly |
| `user_total_cards` | User | Offline | Batch (daily) | Daily |
| `user_avg_monthly_spend` | User | Offline | Batch (daily from transactions) | Daily |
| `user_reward_preference` | User | Offline | Batch (weekly ML model) | Weekly |
| `user_payment_streak` | User | Offline | Batch (daily) | Daily |
| `user_days_before_due_date` | User | Online | Streaming (real-time) | Real-time |
| `user_current_outstanding` | User | Online | Streaming (event-driven) | Real-time |
| `user_last_app_open` | User | Online | Streaming (app events) | Real-time |
| `offer_discount_pct` | Offer | Offline | Batch (daily from partners) | Daily |
| `offer_category` | Offer | Offline | Catalog data | Daily |
| `offer_popularity_score` | Offer | Offline | Batch (daily from clicks) | Daily |
| `user_offer_click_history` | User×Offer | Offline | Batch (daily) | Daily |
| `user_current_session_views` | User | Online | Streaming (Kafka) | Real-time |

### Architecture

| Component | Tool | Why |
|---|---|---|
| **Offline Store** | Amazon S3 + Delta Lake | Cost-effective for historical features. Delta Lake for ACID transactions. |
| **Online Store** | Amazon DynamoDB | Low-latency key-value lookups (<5ms). Scales automatically. |
| **Feature Pipeline (Batch)** | Apache Spark on EMR | Nightly computation of aggregated features from the data warehouse. |
| **Feature Pipeline (Streaming)** | Apache Flink on Kinesis | Real-time feature updates from app events (bill payments, app opens, offer clicks). |
| **Feature Registry** | Feast | Open-source. Manages feature definitions, versions, and lineage. |
| **Orchestration** | Apache Airflow | Schedules nightly batch feature computation. |
| **Monitoring** | Grafana + custom alerts | Monitor feature freshness, drift, and serving latency. |

### How it prevents Training-Serving Skew at CRED:

1. **Same feature definitions:** `user_avg_monthly_spend` computed by the same Spark job for both training data (offline store) and production (materialised to online store).
2. **Point-in-time joins:** When training the reward recommendation model, features are joined based on the timestamp of each user-reward interaction — not current values.
3. **Freshness monitoring:** Alert if `user_current_outstanding` in online store is >5 minutes stale.
4. **Drift detection:** If `user_avg_monthly_spend` distribution shifts significantly (e.g., festive season), trigger retraining alert.

---

## Q19: What is Feature Drift? How do you detect and handle it?

**Answer:**
Feature Drift (also called Data Drift) occurs when the statistical distribution of input features changes over time, causing model performance to degrade.

**Types of drift:**

| Type | What Changes | Example |
|---|---|---|
| **Feature Drift (Data Drift)** | Distribution of input features | During COVID, Swiggy's `avg_order_value` jumped 40% as people ordered more from home. |
| **Concept Drift** | Relationship between features and target | Before COVID, high `order_frequency` predicted loyalty. During COVID, even disloyal users ordered frequently (no choice). Same features, different meaning. |
| **Label Drift** | Distribution of the target variable | Fraud rate at HDFC Bank increases from 0.5% to 2% during a data breach — the baseline changes. |

**How to detect drift:**

| Method | How It Works | Tool |
|---|---|---|
| **Population Stability Index (PSI)** | Compare feature distribution between training data and current production data. PSI > 0.2 = significant drift. | Custom Python, Evidently AI |
| **Kolmogorov-Smirnov Test** | Statistical test comparing two distributions. Low p-value = distributions are different. | SciPy, Evidently AI |
| **Jensen-Shannon Divergence** | Measures how different two probability distributions are. Higher = more drift. | Custom code |
| **Performance monitoring** | Track model accuracy/F1 over time. Declining performance = likely drift. | MLflow, Grafana |
| **Feature monitoring dashboards** | Visualise feature distributions over time. Manual inspection. | Grafana, Evidently AI |

**How to handle drift:**

| Strategy | When to Use |
|---|---|
| **Scheduled retraining** | Retrain model every week/month with latest data. Simple and effective. |
| **Triggered retraining** | Automatically retrain when drift exceeds threshold (PSI > 0.2). |
| **Online learning** | Model continuously updates with new data (incremental learning). For rapidly changing environments. |
| **Feature store monitoring** | Feature store detects drift and alerts teams before model performance drops. |
| **Windowed training** | Only train on recent data (last 3 months) instead of all historical data. Recent data is more relevant. |
| **Model ensemble** | Run old model and new model in parallel. Gradually shift traffic to new model (A/B testing). |

**Indian example — Zomato during COVID:**
- Pre-COVID: Model trained on dine-in + delivery data. `is_dine_in` was a feature.
- During COVID: Dine-in dropped to zero. Distribution of multiple features shifted dramatically.
- Model performance degraded because it had never seen "all orders are delivery" patterns.
- Fix: Retrained with COVID-era data, removed dine-in features, added `is_lockdown` feature.

---

## Q20: Scenario — You're building a real-time fraud detection system for Paytm using a Feature Store. Design the complete system.

**Answer:**

### Requirements:
- Detect fraud within 100ms of transaction
- Handle 10,000+ transactions per second
- Use both historical patterns and real-time signals
- Minimise false positives (don't block legitimate transactions)

### Architecture:

```
User initiates payment on Paytm
         │
         ▼
    ┌─────────┐
    │  Kafka   │  (Transaction event published)
    └────┬────┘
         │
         ▼
┌──────────────────┐     ┌──────────────────────────┐
│  Fraud Detection  │←───│      Feature Store         │
│  Service (Flink)  │     │                            │
│                   │     │  Online Store (Redis):     │
│  1. Get features  │     │  - user_avg_txn_amount     │
│  2. Score with    │     │  - user_txn_last_1hr       │
│     model         │     │  - user_last_location      │
│  3. Decide        │     │  - device_fingerprint      │
│                   │     │  - merchant_fraud_rate      │
└────────┬─────────┘     │                            │
         │               │  Offline Store (S3):       │
         │               │  - historical patterns      │
    ┌────▼────┐          │  - (used for training)      │
    │ Decision │          └──────────────────────────┘
    │          │
    ├── APPROVE → Payment proceeds
    ├── DECLINE → Transaction blocked, user notified
    └── REVIEW → Sent to manual review queue
```

### Feature Store Implementation:

**Online Features (served in <5ms):**

| Feature | Pipeline | Update Frequency | Why Online |
|---|---|---|---|
| `user_txn_count_last_1hr` | Streaming (Flink) | Every transaction | Velocity check — 20 transactions in 1 hour is suspicious |
| `user_txn_amount_last_1hr` | Streaming (Flink) | Every transaction | Total amount spent recently |
| `user_last_transaction_location` | Streaming (Flink) | Every transaction | Distance check — Delhi then Mumbai in 1 hour = impossible |
| `user_last_transaction_time` | Streaming (Flink) | Every transaction | Time gap between transactions |
| `device_is_new = 1/0` | Streaming (Flink) | Every transaction | First time using this device |
| `merchant_fraud_rate_7d` | Batch → materialised to online | Hourly | Some merchants have higher fraud rates |

**Offline Features (used for training):**

| Feature | Pipeline | Update Frequency |
|---|---|---|
| `user_avg_txn_amount_90d` | Batch (Spark) | Daily |
| `user_max_single_txn_ever` | Batch (Spark) | Daily |
| `user_unique_merchants_30d` | Batch (Spark) | Daily |
| `user_international_txn_pct` | Batch (Spark) | Daily |
| `user_declined_txn_30d` | Batch (Spark) | Daily |
| `merchant_category_fraud_history` | Batch (Spark) | Weekly |
| `user_device_history` | Batch (Spark) | Daily |

### Model Scoring (within Flink):

```
For each transaction:
    1. Read online features from Redis (5ms)
    2. Compute real-time features in Flink:
       - distance_from_last_txn
       - time_since_last_txn
       - amount_ratio = txn_amount / user_avg_txn_amount
       - is_new_merchant = 1/0
    3. Score with ML model (XGBoost loaded in Flink): (5ms)
       fraud_score = model.predict(all_features)
    4. Decision:
       - fraud_score < 0.3 → APPROVE
       - fraud_score 0.3-0.7 → REVIEW (send to human analyst)
       - fraud_score > 0.7 → DECLINE
    Total time: ~15ms
```

### Preventing Training-Serving Skew:

| Skew Type | How Feature Store Prevents It |
|---|---|
| **Computation** | `user_avg_txn_amount_90d` computed by same Spark job for training and materialised to Redis for serving |
| **Freshness** | Real-time features updated via Flink streaming pipeline. Redis TTL ensures stale features are flagged. |
| **Leakage** | Point-in-time joins ensure training data doesn't use future transaction information |
| **Distribution** | Weekly drift report compares feature distributions. Alert if fraud rate or transaction patterns shift (e.g., festival season). |

### Monitoring:

| Metric | Threshold | Action |
|---|---|---|
| Feature serving latency | > 10ms | Alert engineering team |
| Feature freshness (online) | > 5 minutes stale | Alert on-call |
| Model accuracy (daily) | < 90% precision | Trigger retraining |
| False positive rate | > 5% | Review threshold settings |
| Feature drift (PSI) | > 0.2 | Alert data science team |
