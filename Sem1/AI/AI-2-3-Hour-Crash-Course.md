# AI Systems — 2-3 Hour Crash Course (Exam Ready)

> BITS Pilani — SS ZG662: Introduction to AI Systems
> You haven't attended lectures. Your exam is in 2 days. This is a THEORETICAL exam.
> Focus on: categories, classifications, comparisons, advantages/disadvantages, case studies.
>
> **Time Plan:**
> - **Hour 1 (Topics 1-4):** 6 AI Categories, AI vs ML vs DL, AI Lifecycle, 5-Layer Architecture
> - **Hour 2 (Topics 5-8):** Data Engineering, Feature Engineering, Recommender/GenAI/Conversational, Case Studies
> - **Hour 3 (Topics 9-12):** Feature Stores, Data Quality/Governance, Model Marketplace, Quick Revision

---

# ⏱️ HOUR 1 — Highest Yield (Topics 1-4)

---

## TOPIC 1: 6 Categories of AI Systems ⭐⭐⭐

> This is the MOST important table. Learn it cold. Every design question maps to one of these 6 categories.

### Master Table — 6 AI Categories

| Category | Input | Output | Technique | Real Example |
|---|---|---|---|---|
| **Predictive AI** | Historical structured data | Number or label | Supervised ML (XGBoost, RF) | Swiggy delivery time, HDFC loan default |
| **Generative AI** | Text prompt / seed | New text/image/code/audio | LLMs, Diffusion Models | ChatGPT, DALL-E, GitHub Copilot |
| **Recommender Systems** | User behavior + item features | Ranked list of items | Collaborative / Content / Hybrid | Netflix "Top Picks", Amazon "You may like" |
| **Conversational AI** | Natural language message | Natural language response | NLU + Dialog Manager + LLM/RAG | HDFC Eva chatbot, ChatGPT |
| **Computer Vision** | Images / video | Labels, bounding boxes | CNNs | Google Lens, Tata Motors defect detection |
| **Autonomous Systems** | Sensor data (camera, LiDAR, radar) | Physical actions (steer, brake) | Sensor fusion + planning | Waymo self-driving car |

### Predictive AI — 4 Subtypes

| Type | What It Predicts | Output | Example |
|---|---|---|---|
| **Classification** | Which category? | Discrete label (spam/not-spam) | Flipkart review sentiment |
| **Regression** | What number? | Continuous value (₹85 lakhs) | House price prediction |
| **Time-series** | What happens next? | Future values over time | Jio network demand forecasting |
| **Anomaly Detection** | Is this unusual? | Normal/Anomalous | HDFC credit card fraud detection |

### Recommender Systems — 3 Subtypes

| Type | How It Works | Strength | Weakness |
|---|---|---|---|
| **Collaborative Filtering** | "Users like you liked X" | Discovers unexpected items (serendipity) | Cold start — fails for new users |
| **Content-Based** | "Items similar to what you liked" | Works for new items (just need metadata) | Filter bubble — keeps showing same type |
| **Hybrid** | Combines both approaches | Best accuracy overall | More complex to build and maintain |

> **Two-stage architecture in production:** Candidate Generation (fast, millions → ~1000) → Ranking (accurate, ~1000 → top 20)

### Generative AI — 5 Types

| Type | Creates | Technology | Example |
|---|---|---|---|
| Text generation | Articles, emails, code | LLMs (next token prediction) | ChatGPT, Claude |
| Image generation | Photos, art, designs | Diffusion Models (noise → image) | DALL-E 3, Midjourney |
| Code generation | Code in any language | LLMs trained on code | GitHub Copilot |
| Music/Audio | Songs, voice cloning | Audio diffusion, neural codecs | Suno, ElevenLabs |
| Video generation | Video clips from text | Video diffusion | Sora, Runway |

**Key GenAI concepts:** Hallucination (confident but WRONG), RLHF (human feedback to make models helpful), RAG (search documents first → answer grounded in facts), Temperature (0 = safe, 1 = creative)

### SAE Levels of Autonomous Vehicles (0-5)

| Level | Name | Who Drives? | Example |
|---|---|---|---|
| 0 | No Automation | Human only | Old car |
| 1 | Driver Assistance | Human + one AI function | Adaptive cruise control |
| 2 | Partial Automation | Human + steering + speed | Tesla Autopilot |
| 3 | Conditional Automation | AI in specific conditions | Mercedes Drive Pilot |
| 4 | High Automation | AI in specific areas, no human needed | **Waymo robotaxis** |
| 5 | Full Automation | AI everywhere | **Does NOT exist yet** ⚠️ |

> Level 5 doesn't exist because: infinite edge cases (construction, hand signals, animals), weather degrades sensors, no regulatory framework, cultural driving norms vary.

### ✅ Solved Exam Question — Q40: Describe 4 AI categories with case studies

**Predictive AI (Swiggy):** Predicts delivery time (regression) using distance, restaurant prep time, traffic, weather. Reduces customer complaints.

**Generative AI (ChatGPT):** Pre-trained on internet text → RLHF fine-tuned → served via API with safety filters. Model is <5% of system.

**Recommender Systems (Netflix):** Multiple models per UI section. Personalised thumbnails. 80% of content watched from recs. Saves $1B/year.

**Autonomous Systems (Waymo):** 29 cameras + 4 LiDAR + 6 radar. Sense→Think→Act in <100ms. Level 4 (geofenced). Level 5 doesn't exist.

### 📝 Practice: Match business problems to AI categories

<details><summary>A bank wants to detect unusual transactions in real-time → ?</summary>

**Predictive AI — Anomaly Detection.** Historical transaction data → flag outliers using Isolation Forest or Autoencoders.
</details>

<details><summary>Flipkart wants to show "Products you may like" → ?</summary>

**Recommender System — Hybrid.** Collaborative filtering (similar users) + content-based (similar product features). Two-stage: candidate gen → ranking.
</details>

<details><summary>Zomato wants a chatbot to answer "Where's my order?" → ?</summary>

**Conversational AI — RAG-based.** Retrieve real-time order data → feed to LLM → generate natural language response grounded in actual order status.
</details>

<details><summary>Tata Motors wants to detect paint defects on the assembly line → ?</summary>

**Computer Vision — Object Detection / Classification.** Camera images → CNN detects defective areas with bounding boxes.
</details>

---

## TOPIC 2: AI vs ML vs DL ⭐⭐⭐

> Almost certainly asked. Know the nested relationship and comparison table.

### Nested Relationship: AI ⊃ ML ⊃ DL

```
┌──────────────────────────────────────┐
│  AI (Broadest)                       │
│  Rule-based chess, thermostat, IRCTC │
│  ┌────────────────────────────────┐  │
│  │  ML (Subset of AI)            │  │
│  │  Spam filter, fraud detection │  │
│  │  ┌──────────────────────────┐ │  │
│  │  │  DL (Subset of ML)      │ │  │
│  │  │  ChatGPT, image recog.  │ │  │
│  │  └──────────────────────────┘ │  │
│  └────────────────────────────────┘  │
└──────────────────────────────────────┘
```

### Comparison Table (6 Dimensions)

| Dimension | AI | ML | DL |
|---|---|---|---|
| **What** | Any intelligent system | Learns patterns from data | Deep neural networks |
| **How** | Rules, heuristics, logic | Algorithm finds rules from data | Network discovers features AND rules |
| **Who writes logic?** | Human programmer | Algorithm learns | Network learns everything automatically |
| **Feature engineering** | Manual rules | Manual feature engineering needed | Automatic feature learning |
| **Data needed** | Little or none | Thousands–millions | Millions–billions |
| **Compute needed** | Low (CPU) | Medium (CPU/GPU) | High (GPUs/TPUs required) |

### When to Use What — Decision Guide

| Situation | Pick | Why |
|---|---|---|
| Rules are clear and simple | Traditional AI (rule-based) | No data needed, deterministic |
| Historical data + patterns exist | Machine Learning | Learns what humans can't code |
| Complex data (images, text) + lots of data | Deep Learning | Auto-discovers features |
| Very little data available | Rule-based AI | ML/DL need data to learn |
| Need explainability | Simple ML (decision tree) | Black-box DL can't explain |

### Three Levels of ML Software (Iceberg)

| Level | What It Covers | Size |
|---|---|---|
| **ML Code** | Model training, feature engineering | ~5% (tip of iceberg) |
| **ML Infrastructure** | Data pipelines, serving, monitoring, feature store | ~35% |
| **ML Operations** | CI/CD, testing, governance, team workflows | ~60% |

> **Key fact:** Model = 20% effort, System = 80% effort. The model is the tip of the iceberg.

### Why AI Projects Fail — 85% Failure Rate

| Reason | Explanation |
|---|---|
| Wrong problem framing | Using AI where simple rules work |
| Poor data quality | Garbage in = garbage out |
| No clear success metric | Can't measure if AI is working |
| Trying AI where rules suffice | Over-engineering simple problems |
| Lack of MLOps infrastructure | Model works in notebook, fails in production |
| No business alignment | AI team builds what's cool, not what's needed |
| Ignoring the 80% | Focus on model, neglect pipelines/monitoring |

### ✅ Solved Exam Questions — Q1-Q2

**Q1: Define AI. How is it different from traditional software?**
- AI: any system that mimics human intelligence — learning, reasoning, perception, decision-making.
- Traditional software: human writes rules (if-else), deterministic. AI: algorithm discovers rules from data, probabilistic.

**Q2: Draw the nested relationship. Give one example each.**
- AI ⊃ ML ⊃ DL (concentric circles). AI: rule-based chess. ML: spam filter (XGBoost). DL: ChatGPT.

### 📝 Practice

<details><summary>True or False: "DL always performs better than traditional ML"</summary>

**FALSE.** DL needs massive data + GPU compute. With small datasets, traditional ML (XGBoost, Random Forest) often outperforms DL. A logistic regression with great features can beat a neural network with poor features. DL shines only when you have millions of examples + complex data (images, text).
</details>

<details><summary>A Jio customer service team has 50 clear rules for handling complaints. Should they use DL?</summary>

**NO.** Rules are clear and simple → use rule-based AI. ML/DL add unnecessary complexity. Only use ML when patterns are too complex to write as rules, and DL when data is complex (images, text) + abundant.
</details>

---

## TOPIC 3: AI System Lifecycle — 6 Circular Stages ⭐⭐⭐

### The 6 Stages

| Stage | What Happens | Key Activity | Common Pitfall |
|---|---|---|---|
| **1. Problem Definition** | Translate business → ML problem | Define success metric (MAE < 5 min) | Solving wrong problem; AI where rules suffice |
| **2. Data Collection & Prep** | Gather, clean, label, split data | Handle missing values, check bias | Poor data quality; data leakage; selection bias |
| **3. Model Development** | Train, tune, evaluate models | Feature engineering, hyperparameter tuning | Overfitting; not evaluating on realistic test set |
| **4. Deployment** | Put model into production | API serving, batch/edge, set latency SLA | Works in notebook but fails in production |
| **5. Monitoring** | Track performance over time | Detect drift, alert on degradation | No monitoring → silent degradation (Zillow!) |
| **6. Iteration** | Improve from feedback | Retrain with new data, A/B test | Not closing the feedback loop |

### Why Circular (Not Linear)?

Unlike traditional software, ML models **degrade over time** due to:
- **Data drift:** Input data distribution changes (post-COVID transactions look different from pre-COVID)
- **Concept drift:** The relationship between features and target changes (what "spam" looks like evolves)

You must iterate continuously — train once and forget = guaranteed failure.

### ✅ Solved — Q39: Swiggy Delivery Time Through All 6 Stages

| Stage | Swiggy Example |
|---|---|
| 1. Problem Definition | "Reduce complaints about late deliveries" → "Predict delivery time (regression), MAE < 5 min" |
| 2. Data Collection | Historical orders, restaurant prep times, traffic, weather, rider locations. Clean + split. |
| 3. Model Development | Train XGBoost with features: distance, time_of_day, restaurant_avg_prep, rain_flag, active_riders |
| 4. Deployment | API serving via FastAPI. A/B test new vs old model. Latency SLA < 100ms. |
| 5. Monitoring | Track MAE over time. Alert if MAE > 7 min. Monitor data drift (monsoon patterns). |
| 6. Iteration | Retrain with monsoon data, new restaurants. A/B test improvements. Loop back. |

**Why circular for Swiggy:** Traffic patterns change seasonally, new restaurants open, rider fleet changes → must continuously retrain.

### 📝 Practice

<details><summary>Why can't you just train a model once and forget?</summary>

Because the world changes. Data drift (input distributions shift) and concept drift (relationships change) cause models to silently degrade. A fraud model trained on 2023 patterns misses new 2024 fraud tactics. Without retraining, accuracy drops and nobody notices — unlike traditional software that crashes visibly when broken.
</details>

---

## TOPIC 4: 5-Layer AI Architecture ⭐⭐⭐

> Use this framework to answer ANY system design question.

### The 5 Layers

| Layer | Purpose | Key Tools | Company Example |
|---|---|---|---|
| **Data Layer** | Collect, store, process, serve data | Kafka, S3, Spark, Airflow, Feast | Netflix ingests billions of events via Kafka |
| **Model Layer** | Train, evaluate, version, serve models | MLflow, TF Serving, Triton | Spotify trains & versions recommendation models |
| **Application Layer** | User-facing app, business logic, A/B testing | FastAPI, React, LaunchDarkly | Swiggy shows predicted ETA in the app |
| **Infrastructure Layer** | Compute, storage, networking, containers | Kubernetes, Docker, Terraform | Waymo uses GPU clusters + edge GPUs in vehicles |
| **Monitoring Layer** | Drift detection, alerts, dashboards | Prometheus, Grafana, Great Expectations | Zillow LACKED this → $881M loss |

### Zillow Case Study — $881M Loss (Monitoring Failure)

- Built AI to predict house prices for home-buying (iBuying).
- COVID caused unprecedented market shifts (data drift).
- Model **overpredicted prices** → bought homes at inflated prices.
- **No monitoring** to catch the drift. No alerts for distribution shift.
- **Result:** $881M loss, shutdown of iBuying, 2,000 layoffs.
- **Lesson:** A 5% investment in monitoring could have prevented an $881M loss.

### ✅ Solved — Q45: Swiggy Order Through All 5 Layers

**Scenario:** User places order → sees "Estimated delivery: 35 minutes"

1. **Application Layer:** User taps "Place Order" → API gateway receives request (user_id, restaurant_id, location)
2. **Data Layer:** Feature store (Redis) retrieves features in <1ms — restaurant_avg_prep_time, distance, active_riders, rain_flag, traffic_score
3. **Model Layer:** TF Serving receives feature vector → XGBoost predicts 35.2 minutes → logs prediction
4. **Infrastructure Layer:** Kubernetes auto-scales during peak dinner hours. Load balancer distributes. P99 < 100ms.
5. **Monitoring Layer:** Logs prediction (35 min) vs actual (38 min). Tracks MAE. Alerts if MAE > 7 min. Feeds back for retraining.

### 📝 Practice

<details><summary>Which layer failed in the Zillow case? What should they have done?</summary>

**Monitoring Layer failed.** They should have:
- Tracked data drift using KS test / PSI on input features (house prices, market velocity)
- Set alerts when prediction error exceeded thresholds
- Monitored business metrics (profit/loss per home purchased)
- Auto-paused buying when model confidence dropped
</details>

---

# ⏱️ HOUR 2 — Core Topics (Topics 5-8)

---

## TOPIC 5: Data Engineering & Pipelines ⭐⭐⭐

### ETL vs ELT Comparison

| Aspect | ETL | ELT |
|---|---|---|
| **Order** | Extract → Transform → Load | Extract → Load → Transform |
| **Transform where** | Separate processing server | Inside the warehouse/lake |
| **Raw data kept?** | No — only transformed data stored | Yes — raw data preserved |
| **Flexibility** | Low — must re-extract to transform differently | High — re-transform raw data anytime |
| **Best for** | On-premise, regulated, small data | Cloud, big data, AI/ML workloads |
| **Modern AI systems?** | Less common | **Dominant** (data scientists need raw data for experiments) |
| **Tools** | Informatica, Talend | dbt + BigQuery, Spark + S3 |

### Batch vs Streaming Comparison

| Aspect | Batch | Streaming |
|---|---|---|
| **Processing** | Scheduled chunks (hourly/daily) | Each event as it arrives |
| **Latency** | Minutes to hours | Milliseconds to seconds |
| **Complexity** | Simpler (easier debugging) | Complex (ordering, exactly-once) |
| **Cost** | Lower (bulk, spot instances) | Higher (always-on infrastructure) |
| **Use case** | Reports, model training, daily aggregations | Fraud detection, real-time recs, alerts |
| **Indian example** | Flipkart nightly product recommendations | HDFC real-time fraud scoring (<100ms) |
| **Tools** | Apache Spark, Airflow, dbt | Apache Kafka, Flink, Kinesis |

> **Lambda Architecture** = Batch + Streaming together. Most real-world systems use BOTH.

### Data Lake vs Warehouse vs Lakehouse

| Aspect | Data Lake | Data Warehouse | Data Lakehouse |
|---|---|---|---|
| **Data types** | Any (structured + unstructured) | Structured only | Any |
| **Schema** | Schema-on-read (flexible) | Schema-on-write (strict) | Both |
| **Cost** | Very low (object storage) | High (compute-optimized) | Low |
| **Query speed** | Slow (not optimized) | Very fast (indexed) | Fast |
| **Best for** | ML/AI, raw data archive | BI dashboards, analytics | ML + BI unified |
| **Risk** | Can become "data swamp" without governance | Rigid, hard to adapt | Newer, less mature |
| **Example** | S3, ADLS | BigQuery, Snowflake | Databricks, Delta Lake |

### Data Preprocessing — 8 Strategies

| Strategy | What | When to Use |
|---|---|---|
| **Clean** | Remove outliers, duplicates, impute missing | Always — first step |
| **Balance** | Equal class representation (SMOTE / undersample) | Imbalanced data (99% normal, 1% fraud) |
| **Replace** | Substitute invalid/corrupted values | Encoding errors, placeholder values |
| **Impute** | Fill missing (mean/median/mode/model-based) | Missing data — choose by distribution |
| **Partition** | Split into train/validation/test | Always — required for evaluation |
| **Scale** | Normalize (0-1) or Standardize (mean=0, std=1) | Features have different scales (age vs income) |
| **Augment** | Create synthetic examples (rotate/flip images) | Insufficient training data |
| **Unbias** | Detect and mitigate bias | Fairness-critical (hiring, lending) |

> **Memory trick:** "**C**lean, **B**alance, **R**eplace, **I**mpute, **P**artition, **S**cale, **A**ugment, **U**nbias"
> → **"Can Bees Really Improve Pollen Sorting And Utilization?"**

### Scaling: Min-Max vs Standard

| Method | Formula | Range | When to Use |
|---|---|---|---|
| **Min-Max (Normalize)** | (x - min) / (max - min) | [0, 1] | Need bounded values, no outliers |
| **Standard (Z-score)** | (x - mean) / std | Mean=0, Std=1 | Handle outliers, normal distribution |

### ✅ Solved — Q25-Q27: ETL vs ELT, Batch vs Streaming, Lambda

See tables above — these ARE the exam answers. Key points:
- Modern AI prefers **ELT** (raw data retention for feature experiments)
- **Lambda Architecture** = batch for accuracy + streaming for speed
- HDFC example: Batch retrains fraud model nightly + Streaming scores each transaction in <100ms

### 📝 Practice

<details><summary>Should HDFC use batch or streaming for real-time fraud detection?</summary>

**BOTH (Lambda Architecture).**
- **Streaming:** Score each transaction in real-time (<100ms) using Kafka + Flink. Compute live features: transactions in last 5 minutes, unusual merchant.
- **Batch:** Retrain fraud model nightly on all historical transactions using Spark. Pre-compute customer spending profiles (90-day averages).
- Both combined: Batch features (90-day avg spend) + streaming features (last 5 min activity) fed together to the model.
</details>

---

## TOPIC 6: Feature Engineering & Feature Stores ⭐⭐⭐

### What is a Feature?

A **feature** is a measurable property used as input to an ML model. Feature engineering converts raw data → features the model understands.

> "Applied ML is basically feature engineering" — Andrew Ng. 80% of ML project time is spent here.

### Transformation Techniques

| Technique | When to Use | Example |
|---|---|---|
| **Min-Max Scaling** | Need [0,1] range | (25-18)/(65-18) = 0.149 for age 25 |
| **Z-score Standardization** | Handle outliers, normal data | (x - mean) / std |
| **Log Transform** | Right-skewed data | log(income) compresses ₹10K–₹1Cr range |
| **One-Hot Encoding** | Nominal categories (NO order) | City → [1,0,0], [0,1,0], [0,0,1] |
| **Label Encoding** | Ordinal categories (HAS order) | Education: High School=1, Bachelor=2, Master=3 |

> ⚠️ **Never** use Label Encoding for nominal data — model thinks Delhi is "between" Mumbai and Bangalore!

### Feature Selection Methods

| Method | How | Speed | Accuracy | Technique |
|---|---|---|---|---|
| **Filter** | Statistical tests per feature | Fast | Lower | Correlation, chi-squared, mutual info |
| **Wrapper** | Train model with feature subsets | Slow | Higher | Forward selection, backward elimination |
| **Embedded** | Model learns importance during training | Moderate | Good balance | L1/Lasso, XGBoost feature importance |

### Embeddings — Dense Vector Representations

| Type | Dimensions | Use | Example |
|---|---|---|---|
| Word (Word2Vec) | 100-300 | NLP, search | "king" ≈ "queen" in vector space |
| Sentence (BERT) | 384-1024 | Semantic search | Find similar customer queries |
| Image (ResNet) | 512-2048 | Visual similarity | Myntra "visually similar products" |
| User/Item | 64-256 | Recommendations | Netflix user taste profiles |

> **Vector arithmetic captures meaning:** "king − man + woman ≈ queen"

### Feature Store: Offline vs Online

| Aspect | Offline Store | Online Store |
|---|---|---|
| **When computed** | Batch (nightly/hourly) | Real-time (as events happen) |
| **Represents** | Historical aggregations (avg spend 30 days) | Current state (items in cart now) |
| **Latency** | Minutes–hours acceptable | Must be <1-10ms |
| **Storage** | BigQuery, Hive (data warehouse) | Redis, DynamoDB (key-value) |
| **Used for** | Model training (need full history) | Real-time predictions/serving |

### 5 Problems Feature Store Solves

| Problem | Without Feature Store | With Feature Store |
|---|---|---|
| Training-serving skew | Features computed differently | Single source of truth |
| Duplicate work | 5 teams build same feature | Compute once, share across teams |
| Slow serving | Compute on-the-fly (slow) | Pre-computed, cached (<1ms) |
| No discovery | Don't know what features exist | Searchable catalog |
| No versioning | Feature changes break models | Versioned features |

### 4 Types of Training-Serving Skew

| Type | What Goes Wrong | Example |
|---|---|---|
| **Feature computation** | Same feature, different code paths | SQL in training vs Python in serving |
| **Data distribution** | Live data different from training | Trained on weekdays, serves weekends |
| **Feature availability** | Feature exists in training, not serving | credit_score in history but not real-time |
| **Time-travel** | Training uses future information | Using next-month sales to predict this month |

### ✅ Solved — Q54: Design 15 Fraud Detection Features (HDFC Bank)

**Transaction features:** amount, merchant_category, transaction_type (online/POS), time_of_day, day_of_week
**Velocity features:** num_transactions_last_1hr, num_transactions_last_24hr, total_amount_last_1hr
**Behavioral features:** avg_transaction_amount_30d, std_transaction_amount_30d, most_common_merchant, days_since_last_transaction
**Context features:** is_foreign_transaction, distance_from_home_city, is_new_merchant
**Derived features:** amount_vs_avg_ratio (current amount / 30-day average)

### 📝 Practice

<details><summary>For Swiggy delivery prediction: name 3 offline features and 3 online features</summary>

**Offline (batch, from history):**
1. restaurant_avg_prep_time_30d — average prep time over last 30 days
2. rider_avg_delivery_time — average delivery speed for this route
3. user_order_frequency — how often this user orders (affects priority)

**Online (real-time, current state):**
1. current_active_riders_nearby — how many riders are free right now
2. current_restaurant_queue_length — how many pending orders
3. is_raining_now — real-time weather affecting delivery
</details>

---

## TOPIC 7: GenAI, Recommender Systems & Conversational AI ⭐⭐

### ChatGPT — 3 Steps

| Step | What Happens | Key Detail |
|---|---|---|
| **1. Pre-training** | Train on massive internet text | Learns grammar, facts, reasoning. ~$100M for GPT-4 |
| **2. RLHF** | Human evaluators rate responses | Reward model trained → LLM fine-tuned to be helpful/safe |
| **3. Serving** | User sends prompt → word-by-word generation | Safety filters + rate limiting + monitoring. Model is <5% of system |

**System beyond the model:** Content filters, rate limiting, authentication, context window management, load balancing, GPU inference, monitoring, human feedback pipeline.

### RAG vs Fine-Tuning vs Prompting

| Approach | When | Data Needed | Cost | Time | Best For |
|---|---|---|---|---|---|
| **Prompting** | No data, quick start | None | Lowest | Minutes | Prototyping, simple tasks |
| **RAG** | Have documents | Documents (no labelling) | Low-Medium | Hours-Days | Factual Q&A, customer support |
| **Fine-Tuning** | Need custom behavior | Labelled examples | High | Days-Weeks | Domain adaptation, tone |

### Hallucination

- Model generates **confident but factually wrong** information (e.g., citing fake research papers)
- **Why:** LLMs predict the most probable next token — optimize for fluency, NOT truth
- **Fix:** RAG — ground answers in retrieved documents. Doesn't eliminate hallucination completely but reduces it significantly.

### Netflix Recommendation System — Key Facts

- **80% of content watched** comes from recommendations
- **$1 billion/year** saved in customer retention
- **Multiple models:** Different algorithm per UI section (Top Picks, Trending, Because You Watched)
- **Personalised thumbnails:** Comedy lover sees funny scene; action lover sees action scene from same movie
- **Two-stage:** Candidate generation (15K titles → ~500) → Ranking (→ top 40)
- **Offline + Online:** Batch (nightly retraining, Spark) + Streaming (real-time session signals)

### Cold Start Problem & Solutions

| Scenario | Problem | Solution |
|---|---|---|
| New user | No history to base recs on | Ask preferences at signup, show popular items, use content-based until history builds |
| New item | No interactions yet | Use item features (genre, description, tags) for content-based matching |

### Conversational AI — 4 Types (Simple → Advanced)

| Type | Intelligence | Example |
|---|---|---|
| **Rule-based chatbot** | Low — fixed scripts, keyword matching | IRCTC IVR system |
| **Intent-based bot** | Medium — classifies intent, extracts entities | HDFC Bank's Eva |
| **LLM-based assistant** | High — open-ended conversations | ChatGPT, Claude |
| **RAG-based assistant** | High + Accurate — answers from real documents | Enterprise Q&A bots |

### ✅ Solved — Q41: ChatGPT 3 Stages

Pre-training (learn language) → RLHF (learn helpfulness) → Serving (generate + filter). The model is <5% — safety, monitoring, infrastructure, and operations form the vast majority.

### 📝 Practice

<details><summary>Should an Indian bank (HDFC) use RAG or Fine-Tuning for a customer chatbot?</summary>

**RAG.** Because:
- Bank policies change frequently → RAG can update the document store without retraining
- Customer data is sensitive → RAG doesn't require sending training data to a provider
- Need factual, citable answers → RAG grounds responses in real policy documents
- Fine-tuning = expensive + needs labelled data + must retrain when policies change

**Fine-tuning** would be better only if HDFC needed the chatbot to adopt a specific tone/style that prompting can't achieve. For factual Q&A, RAG wins.
</details>

---

## TOPIC 8: Case Studies ⭐⭐⭐

> Case studies carry high marks. Know the key facts, failure reasons, and lessons.

### 📌 ChatGPT (OpenAI) — Generative + Conversational AI

| Aspect | Detail |
|---|---|
| **3 stages** | Pre-training → RLHF → Serving |
| **Key innovation** | RLHF makes model helpful, harmless, honest |
| **System insight** | Model is <5% of total system |
| **Beyond model** | Safety filters, rate limiting, load balancing, monitoring, feedback pipeline |
| **Cost** | GPT-4 training: ~$100 million |

### 📌 Netflix — Recommender System

| Aspect | Detail |
|---|---|
| **80% from recs** | Most viewing driven by recommendations |
| **$1B/year savings** | Retention from personalized content |
| **Multiple models** | Different algorithm per UI row (Top Picks, Trending, Because You Watched) |
| **Personalised thumbnails** | Different image shown to different users based on taste |
| **Offline + Online** | Batch retraining nightly + real-time session signals |
| **Cold start** | New users → popular content + signup preferences |

### 📌 Waymo — Autonomous System

| Aspect | Detail |
|---|---|
| **Sensors** | 29 cameras + 4 LiDAR + 6 radar + GPS + IMU + microphones |
| **AI tasks** | Detection → Tracking → Prediction → Planning → Control |
| **Speed** | All tasks in <100ms (humans: ~1500ms) |
| **SAE Level** | Level 4 (geofenced: Phoenix, San Francisco) |
| **Level 5** | Does NOT exist — infinite edge cases, weather, regulations |
| **Miles driven** | 20+ million autonomous miles |

### 📌 Zillow — Predictive AI Failure

| Aspect | Detail |
|---|---|
| **What happened** | AI predicted house prices for iBuying business |
| **Why failed** | COVID caused data drift → model overpredicted prices |
| **Root cause** | **No monitoring layer.** No drift detection. No alerts. |
| **Loss** | **$881 million** |
| **Consequence** | Shut down iBuying, laid off 2,000 employees |
| **Lesson** | Without monitoring, AI silently serves wrong predictions at scale |

### 📌 Cambridge Analytica — Governance Failure

| Aspect | Detail |
|---|---|
| **What happened** | Facebook quiz app harvested 87M users' data without consent |
| **Used for** | Targeted political advertising |
| **Governance failures** | No access control, no audit trail, no consent verification |
| **Fine** | **$5 billion** FTC fine |
| **Impact** | Catalyst for GDPR enforcement globally |
| **Lesson** | Without governance, data collected for one purpose gets weaponized for another |

### 📌 Amazon Hiring AI — Bias Failure

| Aspect | Detail |
|---|---|
| **What happened** | AI trained on 10 years of hiring data (male-dominated industry) |
| **Result** | Model penalized resumes containing "women's" (e.g., "women's chess club") |
| **Root cause** | **Selection bias** — historical data reflected gender imbalance, not merit |
| **Outcome** | Amazon scrapped the tool entirely |
| **Lesson** | Biased training data → biased model at scale. Must audit for fairness. |

### ✅ Solved — Q43: Waymo Sensors, AI Tasks, Why Level 5 Doesn't Exist

**Sensors:** 29 cameras (360° visual), 4 LiDAR (3D point cloud), 6 radar (works in fog/rain), GPS+IMU (location), microphones (emergency sirens). Sensor fusion combines all.

**AI tasks:** Detection (identify objects) → Tracking (follow across frames) → Prediction (what will object do next) → Planning (optimal path, <50ms) → Control (steer/brake, <10ms). All in <100ms.

**Why Level 5 doesn't exist:** Infinite edge cases (construction, hand signals, animals), weather degrades sensors, no regulatory framework, cultural driving norms vary globally.

### ✅ Solved — Q46: Zillow Failure & Required Monitoring

**What to monitor:** Data drift (KS test, PSI on house prices), prediction accuracy (MAE tracked over time), system health (latency p50/p95/p99), business metrics (profit/loss per home purchased). Auto-pause buying when model confidence drops.

---

# ⏱️ HOUR 3 — Remaining Topics (Topics 9-12)

---

## TOPIC 9: Data Quality & Governance ⭐⭐

### 6 Quality Dimensions

| Dimension | Definition | Example (Bad Quality) |
|---|---|---|
| **Accuracy** | Values correctly represent reality | Age = 250 instead of 25 |
| **Completeness** | No missing values where expected | 15% customers missing email |
| **Consistency** | Same fact, same way everywhere | "India" in one table, "IN" in another |
| **Timeliness** | Data reflects current state | Address not updated after move |
| **Validity** | Data follows expected format/rules | Phone number with 8 digits instead of 10 |
| **Uniqueness** | No unintended duplicates | Same customer appearing twice |

### 5 Types of Bias

| Bias | Description | Example |
|---|---|---|
| **Reporting Bias** | Unusual events overreported | News reports crashes, not safe flights |
| **Automation Bias** | Over-relying on AI output | Loan officer blindly follows AI |
| **Selection Bias** | Training data not representative | Urban-trained model fails for rural users |
| **Group Attribution** | Group stats applied to individuals | "City X spends less" → penalize all from X |
| **Implicit Bias** | Unconscious assumptions in data | "CEO" → images of men |

### 7 Governance Components

| Component | What It Covers |
|---|---|
| **Data Ownership** | Who is responsible for each data source |
| **Access Control** | Who can access what, for what purpose |
| **Data Lineage** | Where data came from, how it was transformed |
| **Data Cataloging** | Central inventory of all data assets |
| **Privacy & Compliance** | GDPR, DPDP Act, HIPAA |
| **Data Retention** | How long data kept, when deleted |
| **Quality Standards** | Thresholds (completeness > 95%) |

### GDPR + DPDP Act

| Aspect | GDPR (EU) | DPDP Act (India) |
|---|---|---|
| **Max fine** | 4% of global revenue | ₹250 crore |
| **Right to deletion** | Yes | Yes |
| **Consent required** | Yes | Yes |
| **Data Protection Officer** | Required | Required |
| **Applies to** | EU data subjects | Indian data principals |

### ✅ Solved — Q49: Credit Scoring Data Quality Issues

| Issue | Problem Type | Fix |
|---|---|---|
| 15% missing employment_years | **Completeness** | Impute with median or create "is_missing" feature |
| credit_bureau_score updated after loan | **Data leakage** | Use snapshot from BEFORE application date |
| Only urban professionals in training | **Selection bias** | Collect rural data, stratified sampling, monitor fairness |
| Only 2% default rate | **Class imbalance** | SMOTE, class weights, evaluate with F1/AUC not accuracy |

### 📝 Practice

<details><summary>True or False: "A data lake without governance is better than a data warehouse"</summary>

**FALSE.** A data lake without governance becomes a **data swamp** — nobody knows what data exists, where it came from, or if it's trustworthy. A well-governed warehouse, while less flexible, at least has schema enforcement, access control, and quality guarantees. The ideal is a **data lakehouse** with proper governance.
</details>

---

## TOPIC 10: Model Marketplace & Build vs Buy ⭐⭐

### Foundation Models — Key Players

| Model | Company | Type | Key Fact |
|---|---|---|---|
| GPT-4/4o | OpenAI | Multimodal | Most capable general-purpose |
| Claude 3.5/4 | Anthropic | Text + Vision | Safety focus, 200K context |
| Gemini | Google | Multimodal | Natively multimodal |
| LLaMA 3 | Meta | Open-source text | Free, huge community |
| Mistral | Mistral AI | Open-source text | Efficient for its size |

### Open-Source vs Proprietary (5 Benefits of Open-Source)

1. **No API costs** — run on your own infrastructure
2. **Data privacy** — data never leaves your servers
3. **Full customization** — fine-tune, modify architecture
4. **No vendor lock-in** — switch freely
5. **Community innovation** — 500K+ models on Hugging Face

### API vs Fine-Tune vs Build from Scratch

| Aspect | API (GPT-4) | Fine-Tune (LLaMA) | Build from Scratch |
|---|---|---|---|
| **Time to deploy** | Hours-days | Days-weeks | Months-years |
| **Upfront cost** | Zero | Moderate | Very high |
| **Data privacy** | Data to 3rd party | Data stays local | Full control |
| **Customization** | Prompt engineering only | High | Maximum |
| **Team needed** | 1-2 developers | 2-5 ML engineers | 10-50 researchers |
| **Best for** | Prototyping, generic tasks | Domain-specific, privacy-sensitive | Core competitive advantage |

### License Types

| License | Freedom | Example |
|---|---|---|
| **Apache 2.0** | Most free, no restrictions | Mistral, Whisper |
| **Llama License** | Free < 700M monthly active users | LLaMA 2, LLaMA 3 |
| **Commercial** | Must purchase | Specialized models |

### Benchmarks (How Models Are Compared)

| Benchmark | Measures |
|---|---|
| **MMLU** | General knowledge (57 subjects) |
| **HumanEval** | Code generation ability |
| **MT-Bench** | Multi-turn conversation quality |
| **GSM8K** | Math reasoning |
| **TruthfulQA** | Factual accuracy |

> ⚠️ High benchmark ≠ good for YOUR task. Always evaluate on your own data.

### Gateway/Router Pattern

```
App → Gateway → Simple queries → Small model (cheap, fast)
             → Complex queries → Large model (expensive, accurate)
```

**Result:** 78% cost reduction by routing 80% of simple queries to cheap models.

### Enterprise Procurement Scorecard

| Factor | Weight | What It Measures |
|---|---|---|
| **Capability** | 30% | Performance on YOUR specific tasks |
| **Cost** | 25% | Total cost of ownership |
| **Latency** | 15% | Response time (p50/p95/p99) |
| **Privacy** | 15% | Data residency, compliance |
| **Support** | 10% | Enterprise support, SLAs |
| **Lock-in** | 5% | Ease of switching providers |

### ✅ Solved — Q47: Recommend API/Fine-tune/Build

| Scenario | Recommendation | Why |
|---|---|---|
| Customer support chatbot (small e-commerce) | **API (GPT-4)** | Small team, generic task, quick deploy. Use RAG for product catalog. |
| Fraud detection (bank) | **Fine-tune open-source** | Bank data can't leave servers (RBI regulations). Fraud patterns are bank-specific. |
| Medical imaging (hospital chain) | **Build from scratch** (or fine-tune medical model) | Safety-critical, FDA approval needed, patient data can't leave hospital (HIPAA/DPDP). |

### 📝 Practice

<details><summary>A startup with 3 engineers wants to build a legal document summarizer. API, Fine-tune, or Build?</summary>

**Start with API (GPT-4/Claude) + RAG.**
- 3 engineers = too small for fine-tuning or building from scratch
- Legal docs can be fed via RAG for grounded summaries
- Quick to deploy (days, not months)
- If data sensitivity is a concern (client documents), consider fine-tuning an open-source model (LLaMA) as a Phase 2, once the product proves value
- Building from scratch is never justified for a startup with 3 engineers
</details>

---

## TOPIC 11: XAI, Monitoring & Deployment ⭐⭐

### Explainable AI (XAI)

| Technique | What It Does | Based On | Scope |
|---|---|---|---|
| **SHAP** | Feature contribution scores for predictions | Game theory (Shapley values) | Global + Local |
| **LIME** | Explains ONE prediction by local approximation | Perturb input, observe changes | Local only |

### Interpretability vs Performance Trade-off

| Model Type | Interpretability | Performance |
|---|---|---|
| Linear Regression, Decision Tree | **High** — can read rules | **Lower** — limited complexity |
| Random Forest, XGBoost | **Medium** — feature importance available | **Good** — handles non-linearity |
| Neural Networks, Deep Learning | **Low** — black box | **Highest** — captures complex patterns |

> When is XAI needed? Regulated industries (banking, healthcare), debugging wrong predictions, fairness auditing, building stakeholder trust.

### Model Deployment — 3 Options

| Option | How | When |
|---|---|---|
| **Real-time API** | One prediction per request (REST/gRPC) | Fraud detection, chatbots, search ranking |
| **Batch transform** | Process entire dataset on schedule | Monthly credit scoring, nightly reports |
| **Edge deployment** | Model on device (phone, IoT, camera) | Offline scenarios, ultra-low latency, privacy |

### Deployment Pipeline

**QA** (unit testing) → **Staging** (integration) → **UAT** (user acceptance) → **Production**

### Monitoring — What and Why

| What to Monitor | Why | Metric / Tool |
|---|---|---|
| **Model accuracy** | Models degrade over time | F1, AUC, MAE tracked weekly |
| **Data drift** | Input distributions shift | KS test, PSI |
| **Concept drift** | Input→output relationship changes | Accuracy on recent ground truth |
| **System health** | Latency spikes, errors | p50/p95/p99 latency, error rate |
| **Business metrics** | Does AI actually help the business? | Revenue, CTR, conversion, retention |

### Why Monitoring Is Critical for AI but Not Traditional Software

| Aspect | Traditional Software | AI Systems |
|---|---|---|
| **Behavior** | Deterministic (same input = same output) | Probabilistic (predictions degrade) |
| **Failure mode** | Crashes, visible errors | Silent accuracy degradation (invisible!) |
| **Degradation** | Doesn't change unless code changes | Degrades over time (drift) |
| **Testing** | Unit tests catch most bugs | No test can guarantee future accuracy |

> Traditional software either works or throws an error. AI returns "200 OK" with wrong predictions.

### Overfitting vs Underfitting

| Problem | Symptom | Fix |
|---|---|---|
| **Overfitting** | High training accuracy, LOW test accuracy | More data, regularization, dropout, simpler model |
| **Underfitting** | Low accuracy on BOTH training and test | More features, complex model, longer training |

### 📝 Practice

<details><summary>Zomato's delivery time model shows 95% accuracy in training but only 60% in production. What's wrong?</summary>

**Overfitting + possible training-serving skew.**
- 95% training / 60% production = classic overfitting (model memorized training data)
- Also check for training-serving skew: are features computed the same way in training vs production?
- Fixes: More diverse training data, regularization, cross-validation, check feature store for consistency, monitor data drift
</details>

---

## TOPIC 12: Quick Revision — Last 15 Minutes ⭐⭐⭐

### 🔢 Key Numbers (Memorize These)

| Fact | Number |
|---|---|
| Model code as % of AI system | **5%** |
| Model effort vs system effort | **20% vs 80%** |
| AI projects that fail | **85%** |
| Netflix content from recommendations | **80%** |
| Netflix savings from recs | **$1 billion/year** |
| Amazon revenue from recs | **35%** |
| Data prep time in ML project | **80%** |
| Zillow loss from AI failure | **$881 million** |
| GPT-4 training cost | **~$100 million** |
| Cambridge Analytica fine | **$5 billion** |
| DPDP Act max penalty | **₹250 crore** |
| Human reaction time | **~1500ms** |
| Autonomous system reaction | **<100ms** |

### 📊 6 AI Categories — One-Liner Each

| Category | One-Liner |
|---|---|
| Predictive | Historical data → predict number or label (Swiggy ETA) |
| Generative | Prompt → create new content (ChatGPT) |
| Recommender | User behavior → ranked item list (Netflix) |
| Conversational | Natural language → natural language response (HDFC Eva) |
| Computer Vision | Images → labels/boxes (Google Lens) |
| Autonomous | Sensors → physical actions (Waymo) |

### ⚡ One-Liner Comparisons

| Comparison | One-Liner |
|---|---|
| **ETL vs ELT** | ETL transforms before loading (old); ELT loads raw then transforms (modern, flexible) |
| **Batch vs Streaming** | Batch = scheduled chunks (cheap); Streaming = real-time per event (fast) |
| **Offline vs Online features** | Offline = batch, for training; Online = real-time, for serving |
| **Data Lake vs Warehouse** | Lake = any data, cheap, flexible; Warehouse = structured, fast queries |
| **SHAP vs LIME** | SHAP = feature contribution (global+local); LIME = explain one prediction (local) |
| **RAG vs Fine-Tuning** | RAG = search docs then answer (factual); Fine-tune = retrain model on your data (behavior) |
| **Overfitting vs Underfitting** | Overfit = high train/low test; Underfit = low everywhere |

### 🔧 Tool Map

| Task | Tool |
|---|---|
| Event streaming | Apache Kafka |
| Batch processing | Apache Spark |
| Pipeline orchestration | Apache Airflow |
| Stream processing | Apache Flink |
| Feature store | Feast, Tecton |
| Experiment tracking | MLflow, W&B |
| Model serving | TF Serving, Triton |
| Container orchestration | Kubernetes |
| Online feature serving | Redis |
| Data quality | Great Expectations, Deequ |
| Data transformation | dbt |
| Model marketplace | Hugging Face |

### ✅/❌ True/False — Quick Fire

| Statement | Answer |
|---|---|
| DL always beats traditional ML | ❌ — needs massive data; small data → ML wins |
| The model is the most important part of an AI system | ❌ — model is 5%; pipelines/monitoring/infra are 95% |
| 85% of AI projects fail | ✅ |
| Netflix gets 80% of views from recommendations | ✅ |
| Level 5 autonomous driving exists today | ❌ — does NOT exist |
| ETL is more common than ELT in modern AI systems | ❌ — ELT dominates in cloud/AI |
| Feature stores prevent training-serving skew | ✅ |
| RAG eliminates hallucination completely | ❌ — reduces it, doesn't eliminate |
| Label Encoding should be used for nominal data | ❌ — use One-Hot; Label implies false ordering |
| Data drift causes models to crash with errors | ❌ — models silently degrade (no error, just wrong) |
| Cambridge Analytica was fined $5 billion | ✅ |
| DPDP Act max penalty is ₹250 crore | ✅ |

### 🎯 Matching: Tool → Function

| Tool | Function |
|---|---|
| Kafka | Real-time event streaming/ingestion |
| Spark | Large-scale batch data processing |
| Airflow | Workflow/pipeline orchestration |
| Feast | Feature store (offline + online) |
| MLflow | Experiment tracking + model registry |
| Redis | Low-latency online feature serving |
| Kubernetes | Container orchestration + auto-scaling |
| Prometheus + Grafana | Monitoring + dashboards |
| dbt | Data transformation in warehouse (ELT) |
| Hugging Face | Model marketplace (500K+ models) |

### 🎯 Matching: Company → Application

| Company | AI Application | Category |
|---|---|---|
| Netflix | Movie recommendations, personalised thumbnails | Recommender |
| Swiggy | Delivery time prediction (ETA) | Predictive |
| ChatGPT | Text generation, conversational assistant | Generative + Conversational |
| Waymo | Self-driving robotaxis (Level 4) | Autonomous |
| HDFC Bank | Eva chatbot + fraud detection | Conversational + Predictive |
| Zillow | House price prediction (failed: $881M loss) | Predictive (failed) |
| Flipkart | Product recommendations | Recommender |
| Tata Motors | Paint defect detection on assembly line | Computer Vision |
| Amazon | "You may also like" (35% revenue from recs) + Hiring AI (bias failure) | Recommender + Predictive |

---

## 🏁 Final Exam Tips

1. **Every design question:** Map it to one of the 6 AI categories first, then use the 5-layer architecture as your framework.
2. **Case studies carry high marks.** Know ChatGPT (RLHF), Netflix (80% from recs), Waymo (sensors + Level 5 doesn't exist), Zillow ($881M, monitoring failure), Cambridge Analytica ($5B, governance failure), Amazon Hiring (bias).
3. **Always use tables** — examiners love structured answers.
4. **When asked "compare":** Use a table with 5+ dimensions. Never write paragraphs for comparisons.
5. **When asked "advantages/disadvantages":** Give at least 3 of each with one-line Indian company examples.
6. **The model is 5% of the system.** Mention this whenever relevant — it shows you understand real-world AI.
7. **Monitoring is the safety net.** Zillow = what happens without it. Every production AI system needs it.
8. **Data quality > model quality.** Garbage in = garbage out. 80% of time is data work.

---

*You've got this. 2-3 hours with this document = exam ready.* 🎯
