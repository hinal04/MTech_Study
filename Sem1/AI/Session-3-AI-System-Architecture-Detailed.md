# Session 3: AI System Architecture

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** Class Notes
>
> **Contact Session:** 3 (Module 1: Foundations of AI Systems)

---

## Table of Contents

- [3.1 The Five-Layer AI System Architecture](#31-the-five-layer-ai-system-architecture)
- [3.2 Architecture Walkthrough: A Modern AI Application](#32-architecture-walkthrough-a-modern-ai-application)

---

## 3.1 The Five-Layer AI System Architecture

### Why Do We Need an Architecture?

An AI system is not just a model. The model is like the brain, but a body needs a heart, lungs, muscles, and a nervous system too. The **architecture** describes how all the parts fit together.

> **Analogy:** Think of a restaurant chain like McDonald's:
> - **Data Layer** = Supply chain (getting ingredients to every restaurant)
> - **Model Layer** = The kitchen (where food is prepared using recipes)
> - **Application Layer** = The counter/app (where customers order and receive food)
> - **Infrastructure Layer** = The building, electricity, gas, equipment
> - **Monitoring Layer** = Quality checks, customer feedback, health inspections

### The Five Layers

```
┌─────────────────────────────────────────────────────────┐
│                   APPLICATION LAYER                       │
│   What users see — app, website, API, business logic     │
├─────────────────────────────────────────────────────────┤
│                   MODEL LAYER                             │
│   The "brain" — training, serving predictions, versioning│
├─────────────────────────────────────────────────────────┤
│                   DATA LAYER                              │
│   The "fuel" — data pipelines, storage, feature store    │
├─────────────────────────────────────────────────────────┤
│                   INFRASTRUCTURE LAYER                    │
│   The "hardware" — GPUs, storage, networking, containers │
├─────────────────────────────────────────────────────────┤
│                   MONITORING LAYER                        │
│   The "nervous system" — logs, alerts, drift detection   │
└─────────────────────────────────────────────────────────┘
```

---

### 3.1.1 Data Layer — "Without good data, nothing else matters"

The Data Layer is the foundation. It handles **collecting, storing, processing, and serving data** to the rest of the system.

> **Simple rule:** An AI model is only as good as the data it's trained on. If your data is bad, your model will be bad — no matter how sophisticated the algorithm.

| Component | What It Does (Simple) | Think of It As... | Live Examples |
|---|---|---|---|
| **Data Ingestion** | Pulls data from different sources into one place | A funnel collecting water from multiple streams | **Apache Kafka** — collects millions of events/second (used by LinkedIn, Uber). **AWS Kinesis** — Amazon's data streaming service. |
| **Data Storage** | Stores raw and processed data durably | A warehouse with organised shelves | **Data Lake** (store everything raw: S3, Google Cloud Storage). **Data Warehouse** (store cleaned, structured data: Snowflake, BigQuery). |
| **Data Processing** | Cleans, transforms, and prepares data | A kitchen where raw ingredients are washed, cut, and prepared | **Apache Spark** — processes terabytes of data in minutes. **dbt** — transforms data using SQL. **Apache Airflow** — schedules and orchestrates data pipelines. |
| **Feature Store** | Stores pre-computed features (inputs to the model) that are consistent between training and serving | A prep station with pre-cut ingredients ready to use instantly | **Feast** (open-source), **Tecton**, **Hopsworks**. Amazon's feature store serves features for recommendations. |
| **Data Quality** | Checks data for problems — missing values, wrong formats, unexpected changes | Quality control on a production line | **Great Expectations** — runs automated data quality checks. **Deequ** (by Amazon) — validates data quality at scale. |

**Live Example — Uber's Data Layer:**
Uber collects data from millions of rides daily:
- GPS coordinates every second from drivers and riders
- Payment transactions
- Ratings and reviews
- Surge pricing data
- Weather and traffic feeds

All this flows through Kafka → gets stored in their data lake → gets processed by Spark → features are computed and stored in a feature store → ready for ML models (ETA prediction, surge pricing, fraud detection).

---

### 3.1.2 Model Layer — "The brain of the system"

The Model Layer handles everything about the ML model — training it, evaluating it, versioning it, and serving predictions.

| Component | What It Does (Simple) | Think of It As... | Live Examples |
|---|---|---|---|
| **Experiment Tracking** | Records every experiment — which data, which settings, what results | A lab notebook recording every experiment | **MLflow** (open-source, very popular), **Weights & Biases** (W&B), **Neptune** |
| **Training Pipeline** | Automates the model training process — load data, train, evaluate, save | An automated assembly line for models | **Kubeflow Pipelines**, **AWS SageMaker Pipelines**, **Google Vertex AI** |
| **Model Registry** | Stores trained models with versions, metadata, and status (staging/production) | A library catalog — stores, versions, and tracks which book is on display | **MLflow Model Registry**, **SageMaker Model Registry** |
| **Model Serving** | Hosts the model and answers prediction requests fast via an API | A restaurant's serving counter — takes orders, delivers food quickly | **TensorFlow Serving**, **TorchServe**, **Triton** (NVIDIA), **Seldon** |
| **Model Evaluation** | Tests the model on held-out data to measure accuracy before deployment | A final quality check before shipping a product | Custom evaluation pipelines, benchmark suites |

**Live Example — How Spotify Ships a New Recommendation Model:**
1. Data scientist trains a new model using MLflow (experiment tracking)
2. Model is registered in MLflow Model Registry as "recommendation-v2.5"
3. Model is deployed to a staging environment for A/B testing
4. 5% of users get the new model, 95% get the old one
5. After 2 weeks: new model increases listen time by 3%
6. New model promoted to production → serves all 400M+ users

---

### 3.1.3 Application Layer — "Where AI meets the real world"

The Application Layer is what users actually interact with. It takes raw model predictions and turns them into something useful.

| Component | What It Does (Simple) | Think of It As... | Live Examples |
|---|---|---|---|
| **API Gateway** | The front door — receives all requests, authenticates, rate-limits | The reception desk that checks your ID before letting you in | **Kong**, **AWS API Gateway**, **FastAPI** |
| **Business Logic** | Transforms model output into user-relevant actions | A translator converting technical jargon into plain language | Model says score=0.87 → app shows "Highly Recommended." Model says fraud_probability=0.95 → app blocks the transaction. |
| **A/B Testing** | Tests two versions on different user groups to see which performs better | A taste test — 50% of customers try new recipe, 50% get the old one | **LaunchDarkly**, **Optimizely**. Google runs 10,000+ A/B tests per year. |
| **User Interface (UI)** | The app or website users interact with | The dining room where customers sit and eat | Web apps (React), mobile apps (Flutter), dashboards (Streamlit for prototyping) |
| **Feedback Collection** | Captures user reactions — clicks, ratings, complaints, corrections | Comment cards at a restaurant | Custom event logging → feeds back into training data. |

**Live Example — Zomato's Application Layer:**
1. You open Zomato → API gateway receives your request
2. Business logic: "User is on homepage at 12:30 PM → show lunch recommendations"
3. Model returns: [Restaurant A: score 0.95, Restaurant B: score 0.88, ...]
4. Business logic filters: Remove closed restaurants, apply "Free delivery" promo
5. A/B test: 50% of users see "Top Rated" first, 50% see "Fastest Delivery" first
6. UI displays personalised restaurant list
7. You tap on a restaurant → click event logged → feeds back into recommendation model

---

### 3.1.4 Infrastructure Layer — "The foundation everything runs on"

The Infrastructure Layer provides the computing power, storage, and networking that everything else needs.

| Component | What It Does (Simple) | Think of It As... | Live Examples |
|---|---|---|---|
| **Compute (CPU/GPU/TPU)** | Processing power for training and serving models | The engine of a car | **GPU instances** (AWS P4d, Google TPU v5). Training GPT-4 required ~25,000 GPUs running for months. |
| **Container Orchestration** | Packages and runs model-serving code in containers, scales up/down automatically | Shipping containers on a cargo ship — standardised, stackable, portable | **Kubernetes** (K8s) — the standard. **Docker** for containerisation. Used by Netflix, Uber, Airbnb. |
| **Storage** | Stores data, models, logs, and artifacts | A warehouse | **S3** (AWS), **GCS** (Google), **Azure Blob Storage** |
| **CI/CD (Continuous Integration / Continuous Deployment)** | Automatically builds, tests, and deploys new code and models | An automated factory conveyor belt — code goes in, tested product comes out | **GitHub Actions**, **GitLab CI**, **Jenkins** |
| **Infrastructure as Code (IaC)** | Defines infrastructure in config files (instead of manually clicking in a cloud console) | A blueprint for a building — follow the blueprint, get the same building every time | **Terraform** (most popular), **Pulumi**, **AWS CloudFormation** |

**Why GPUs Matter for AI:**
- **CPU** (Central Processing Unit) = good at doing one task at a time, very fast
- **GPU** (Graphics Processing Unit) = good at doing thousands of small tasks simultaneously
- AI training involves millions of simple math operations (matrix multiplications) → GPUs do this 10-100x faster than CPUs
- **TPU** (Tensor Processing Unit) = Google's custom chip, even faster than GPUs for specific AI workloads

---

### 3.1.5 Monitoring Layer — "Without monitoring, you're flying blind"

The Monitoring Layer watches the entire system and alerts when something goes wrong.

> **Why monitoring is critical for AI:** In traditional software, bugs cause crashes (obvious). In AI systems, a degraded model **silently gives wrong predictions** — it doesn't crash, it just quietly becomes wrong. Without monitoring, you won't know until customers complain weeks later.

| What to Monitor | Why | What Metrics to Track | Live Example |
|---|---|---|---|
| **Model Performance** | Model accuracy degrades over time as data changes | Accuracy, Precision, Recall, F1 score over time | Netflix monitors if recommendation click-through rate drops below baseline |
| **Data Drift** | The data coming in today may look different from training data | Statistical tests (KS test, PSI) comparing training vs live data distributions | Fraud detection model trained on 2023 data. In 2025, new scam types appear → data drift → model misses new fraud |
| **Prediction Drift** | Distribution of predictions changes even if individual inputs look normal | Distribution of predicted scores/classes over time | Credit scoring model suddenly approves 30% more applications than usual → something changed |
| **System Health** | Infrastructure issues — slow responses, out of memory, server crashes | Latency (p50, p95, p99), error rate, throughput, CPU/memory utilisation | Amazon monitors that recommendation API responds in <100ms for 99% of requests |
| **Business Metrics** | Does the AI actually improve business outcomes? | Revenue, conversion rate, user retention, customer satisfaction | Spotify monitors if Discover Weekly increases total listen time per user |

**Live Example — What Happens Without Monitoring:**
In 2020, Zillow (US real estate company) built an AI model to predict house prices for their home-buying business. The model worked great initially, but:
- Housing market changed rapidly (COVID effect)
- Model's predictions drifted (data drift) — it overpredicted prices
- Without adequate monitoring, Zillow bought thousands of houses at inflated prices
- **Lost $881 million** and shut down the entire business unit
- 2,000 employees laid off

This is the most expensive AI monitoring failure in history. If they had proper drift detection, they could have caught the problem months earlier.

---

## 3.2 Architecture Walkthrough: A Modern AI Application

### Example: Swiggy Food Delivery Time Prediction

Let's trace what happens when you order food on Swiggy, through all 5 layers:

```
━━━ YOU OPEN SWIGGY AND PLACE AN ORDER ━━━

┌──────────────────────────────────────────────────────────────────┐
│ STEP 1: APPLICATION LAYER                                        │
│ ├── Swiggy app sends request: {user_id, restaurant_id, items,   │
│ │    delivery_address, current_time}                              │
│ ├── API Gateway authenticates your session                       │
│ └── Business logic: "User placed an order → need ETA prediction" │
└──────────────────────────────┬───────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────┐
│ STEP 2: DATA LAYER                                               │
│ ├── Feature Store serves pre-computed features:                  │
│ │   ├── restaurant_avg_prep_time = 18 min                        │
│ │   ├── current_traffic_level = HIGH (from Google Maps API)      │
│ │   ├── distance_km = 4.2                                        │
│ │   ├── current_weather = RAIN                                   │
│ │   ├── active_drivers_nearby = 12                               │
│ │   └── restaurant_current_order_queue = 5 pending orders        │
│ └── All features served in <10ms (pre-computed and cached)       │
└──────────────────────────────┬───────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────┐
│ STEP 3: MODEL LAYER                                              │
│ ├── ETA prediction model receives all features                   │
│ ├── Model computes: prep_time=20min + travel_time=18min          │
│ │   + buffer_for_rain=5min = 43 minutes                          │
│ └── Returns: predicted_eta = 43 minutes                          │
└──────────────────────────────┬───────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────┐
│ STEP 4: APPLICATION LAYER (continued)                            │
│ ├── Business logic adds buffer: 43 + 2 = 45 min (round up)      │
│ ├── Displays: "Estimated delivery: 45 minutes"                   │
│ └── User sees "Your food is on the way! ETA: 45 min"            │
└──────────────────────────────┬───────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────┐
│ STEP 5: INFRASTRUCTURE LAYER (running silently)                  │
│ ├── Model runs on Kubernetes cluster with auto-scaling           │
│ ├── If Swiggy has a sale → 10x more orders → K8s automatically  │
│ │   spins up more model servers                                  │
│ └── All logs stored in S3 for future training                    │
└──────────────────────────────┬───────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────┐
│ STEP 6: MONITORING LAYER                                         │
│ ├── Log: prediction=45min, actual_delivery=42min → error=3min ✓ │
│ ├── Track: average prediction error across all orders today      │
│ ├── Alert: if error > 10 min for 5% of orders → trigger alert   │
│ ├── Data drift check: is today's traffic pattern different from  │
│ │   training data? (e.g., city-wide bandh causing unusual traffic)│
│ └── Feedback: actual delivery times → stored → used for          │
│     next model retraining                                        │
└──────────────────────────────────────────────────────────────────┘
```

### Key Takeaways from the Walkthrough

1. **The model is just one step** — Steps 1, 2, 4, 5, and 6 are all "non-model" work that's essential
2. **Speed matters** — The entire flow (from order to showing ETA) must happen in under 1 second
3. **Features are pre-computed** — The feature store has restaurant prep times, traffic levels, etc. ready to go (not computed on-the-fly)
4. **Monitoring closes the loop** — Actual delivery times are compared with predictions and feed back into retraining
5. **Infrastructure scales automatically** — During sales or festivals, more servers spin up without human intervention

---

## Key Terms Glossary (Session 3)

| Term | Simple Meaning |
|---|---|
| **Data Layer** | The part of an AI system that collects, stores, cleans, and serves data |
| **Model Layer** | The part that trains, evaluates, versions, and serves ML models |
| **Application Layer** | The user-facing part — app, website, API, business logic |
| **Infrastructure Layer** | The hardware and cloud resources — GPUs, storage, networking, containers |
| **Monitoring Layer** | The observability part — logs, metrics, alerts, drift detection |
| **Feature Store** | A centralised store of pre-computed ML features, shared between training and serving |
| **Data Drift** | When live data looks different from the data the model was trained on |
| **Model Registry** | A versioned catalog of trained models (like Git for models) |
| **A/B Testing** | Testing two versions on different user groups to measure which performs better |
| **Kubernetes (K8s)** | Container orchestration platform — runs and scales containerised applications |
| **MLflow** | Popular open-source tool for experiment tracking and model registry |
| **CI/CD** | Continuous Integration / Continuous Deployment — automated build, test, deploy |

---

*End of Session 3*
