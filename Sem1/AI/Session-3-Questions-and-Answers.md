# Session 3: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What are the five layers of AI System Architecture? List each layer and its primary role.

**Answer:**

| Layer | Role | Analogy (McDonald's) |
|---|---|---|
| **Data Layer** | Collects, stores, cleans, and serves data. The fuel for everything. | Supply chain — getting ingredients to every restaurant |
| **Model Layer** | Trains, evaluates, versions, and serves ML models. The brain. | Kitchen — where food is prepared using recipes |
| **Application Layer** | User-facing part — app, website, API, business logic. | Counter/app — where customers order and receive food |
| **Infrastructure Layer** | Computing power, storage, networking, containers. The hardware foundation. | Building, electricity, gas, equipment |
| **Monitoring Layer** | Logs, metrics, alerts, drift detection. The nervous system. | Quality checks, customer feedback, health inspections |

The layers stack together:
```
┌─────────────────────────────────┐
│      APPLICATION LAYER          │  ← What users see
├─────────────────────────────────┤
│         MODEL LAYER             │  ← The "brain"
├─────────────────────────────────┤
│         DATA LAYER              │  ← The "fuel"
├─────────────────────────────────┤
│     INFRASTRUCTURE LAYER        │  ← The "hardware"
├─────────────────────────────────┤
│      MONITORING LAYER           │  ← The "nervous system"
└─────────────────────────────────┘
```

Key insight: Every AI system has all five layers. If any layer is weak or missing, the entire system suffers. A great model (brain) with poor data (fuel) and no monitoring (nervous system) will fail.

---

## Q2: Describe the Data Layer in detail. What are its five key components and which tools are used for each?

**Answer:**
The Data Layer is the foundation of any AI system. Without good data, nothing else matters — "Garbage In, Garbage Out."

| Component | What It Does | Tools | Example |
|---|---|---|---|
| **Data Ingestion** | Pulls data from different sources into one place. Like a funnel collecting water from multiple streams. | Apache Kafka (millions of events/sec, used by LinkedIn, Uber), AWS Kinesis | Uber collects GPS coordinates, payment data, ratings from millions of rides |
| **Data Storage** | Stores raw and processed data durably. | Data Lake (S3, Google Cloud Storage — store everything raw), Data Warehouse (Snowflake, BigQuery — cleaned, structured data) | Netflix stores all viewing history, ratings, and browsing behaviour |
| **Data Processing** | Cleans, transforms, and prepares data for ML use. | Apache Spark (processes terabytes in minutes), dbt (SQL transforms), Apache Airflow (orchestrates pipelines) | Flipkart cleans product data — removes duplicates, fixes missing prices, standardises categories |
| **Feature Store** | Stores pre-computed features that are consistent between training and serving. | Feast (open-source), Tecton, Hopsworks | Amazon pre-computes "user_click_count_last_7_days" and stores in feature store for instant access |
| **Data Quality** | Checks data for problems — missing values, wrong formats, unexpected changes. | Great Expectations, Deequ (by Amazon) | Automated checks: "Is any column more than 5% null? Has the data schema changed unexpectedly?" |

**Uber's Data Layer in action:**
Uber collects GPS coordinates every second from millions of drivers and riders, payment transactions, ratings, surge pricing data, plus weather and traffic feeds. All this flows through Kafka → stored in data lake → processed by Spark → features computed and stored in feature store → ready for ML models (ETA prediction, surge pricing, fraud detection).

---

## Q3: Explain the Model Layer. What are its five components and which tools are commonly used?

**Answer:**
The Model Layer handles everything about the ML model — training, evaluating, versioning, and serving predictions. It's the "brain" of the AI system.

| Component | What It Does | Tools |
|---|---|---|
| **Experiment Tracking** | Records every experiment — which data, settings, and results. Like a lab notebook. | MLflow (very popular, open-source), Weights & Biases (W&B), Neptune |
| **Training Pipeline** | Automates the model training process — load data, train, evaluate, save. | Kubeflow Pipelines, AWS SageMaker Pipelines, Google Vertex AI |
| **Model Registry** | Stores trained models with versions, metadata, and status (staging/production). Like Git but for models. | MLflow Model Registry, SageMaker Model Registry |
| **Model Serving** | Hosts the model and answers prediction requests fast via an API. | TensorFlow Serving, TorchServe, Triton (NVIDIA), Seldon |
| **Model Evaluation** | Tests the model on held-out data to measure accuracy before deployment. | Custom evaluation pipelines, benchmark suites |

**Spotify example — How a new recommendation model is shipped:**
1. Data scientist trains a new model using MLflow (experiment tracking)
2. Model is registered in MLflow Model Registry as "recommendation-v2.5"
3. Model is deployed to staging environment for A/B testing
4. 5% of users get the new model, 95% get the old one
5. After 2 weeks: new model increases listen time by 3%
6. New model is promoted to production → serves all 400M+ users

---

## Q4: What is the Application Layer? Explain its five components with a Zomato example.

**Answer:**
The Application Layer is where AI meets the real world — it's what users actually interact with. It takes raw model predictions and turns them into something useful.

| Component | What It Does | Tools |
|---|---|---|
| **API Gateway** | Front door — receives all requests, authenticates, rate-limits | Kong, AWS API Gateway, FastAPI |
| **Business Logic** | Transforms model output into user-relevant actions | Custom code (Model says score=0.87 → app shows "Highly Recommended") |
| **A/B Testing** | Tests two versions on different user groups | LaunchDarkly, Optimizely |
| **User Interface (UI)** | The app or website users interact with | React, Flutter, Streamlit (prototyping) |
| **Feedback Collection** | Captures user reactions — clicks, ratings, complaints | Custom event logging → feeds back into training data |

**Zomato example — how all components work together:**
1. You open Zomato → **API Gateway** receives your request, authenticates your session
2. **Business Logic:** "User is on homepage at 12:30 PM → show lunch recommendations"
3. Model returns: [Restaurant A: score 0.95, Restaurant B: score 0.88, ...]
4. **Business Logic** filters: Remove closed restaurants, apply "Free Delivery" promo
5. **A/B Test:** 50% of users see "Top Rated" first, 50% see "Fastest Delivery" first
6. **UI** displays personalised restaurant list on your phone
7. You tap a restaurant → click event logged by **Feedback Collection** → feeds back into the recommendation model

---

## Q5: Explain the Infrastructure Layer. Why are GPUs important for AI?

**Answer:**
The Infrastructure Layer provides the computing power, storage, and networking that everything else runs on.

| Component | What It Does | Tools |
|---|---|---|
| **Compute (CPU/GPU/TPU)** | Processing power for training and serving models | AWS P4d GPU instances, Google TPU v5 |
| **Container Orchestration** | Packages and runs code in containers, scales up/down automatically | Kubernetes (K8s), Docker |
| **Storage** | Stores data, models, logs, artifacts | S3 (AWS), GCS (Google), Azure Blob Storage |
| **CI/CD** | Automatically builds, tests, and deploys new code and models | GitHub Actions, GitLab CI, Jenkins |
| **Infrastructure as Code (IaC)** | Defines infrastructure in config files instead of manually | Terraform (most popular), Pulumi, AWS CloudFormation |

**Why GPUs matter for AI:**
- **CPU** = good at doing one task at a time, very fast (like a brilliant professor solving one problem)
- **GPU** = good at doing thousands of small tasks simultaneously (like 1000 students each solving a simple problem)
- AI training involves millions of simple math operations (matrix multiplications) → GPUs do this 10-100x faster than CPUs
- **TPU** = Google's custom chip, even faster than GPUs for specific AI workloads

Scale example: Training GPT-4 required approximately 25,000 GPUs running for months. Without GPUs, the same training would have taken decades on CPUs.

---

## Q6: Why is the Monitoring Layer critical for AI systems? How is it different from monitoring traditional software?

**Answer:**
In traditional software, bugs cause crashes or errors — they're obvious. A broken function throws an exception. In AI systems, a degraded model **silently gives wrong predictions** — it doesn't crash, it just quietly becomes wrong. Without monitoring, you won't know until customers complain weeks later.

What the Monitoring Layer tracks:

| What to Monitor | Why | Metrics |
|---|---|---|
| **Model Performance** | Accuracy degrades over time as data changes | Accuracy, Precision, Recall, F1 score over time |
| **Data Drift** | Incoming data may look different from training data | Statistical tests (KS test, PSI) comparing distributions |
| **Prediction Drift** | Distribution of predictions changes unexpectedly | Distribution of predicted scores/classes over time |
| **System Health** | Infrastructure issues — slow responses, crashes | Latency (p50, p95, p99), error rate, CPU/memory usage |
| **Business Metrics** | Does the AI actually improve business outcomes? | Revenue, conversion rate, user retention |

Key difference from traditional monitoring:
- Traditional monitoring asks: "Is the service running? Are there errors?"
- AI monitoring also asks: "Is the model still accurate? Has the data changed? Are predictions still reliable?"
- Traditional bugs crash the system. AI bugs produce wrong but plausible-looking outputs.

---

## Q7: Case Study — Explain the Zillow monitoring failure. What went wrong and what could have prevented it?

**Answer:**
Zillow (US real estate company) built an AI model called "Zestimate" to predict house prices for their home-buying business (iBuying program).

**What happened:**
1. The model worked great initially — accurately predicting house prices
2. The housing market changed rapidly due to COVID effects (2020-2021)
3. The model's predictions drifted — it consistently **overpredicted** house prices
4. Without adequate monitoring and drift detection, Zillow kept buying houses at inflated AI-predicted prices
5. When they tried to sell these houses, the actual market prices were much lower

**The damage:**
- Lost **$881 million** in just a few quarters
- Shut down the entire iBuying business unit
- **2,000 employees** were laid off
- This is the most expensive AI monitoring failure in history

**What could have prevented it:**
1. **Data drift detection:** Statistical tests comparing training data distributions with live data would have flagged that the housing market had changed significantly
2. **Prediction drift monitoring:** Tracking the distribution of predicted prices over time would have shown a suspicious upward trend
3. **Business metric monitoring:** Comparing predicted prices vs actual sale prices would have revealed growing gaps
4. **Human-in-the-loop safeguards:** Requiring human approval for purchases where the model's confidence was low
5. **Regular retraining:** Monthly retraining with recent sales data would have helped the model adapt to the changing market

Key lesson: No matter how good your model is at deployment, without continuous monitoring, it can silently become catastrophically wrong.

---

## Q8: What is Training-Serving Skew? How does a Feature Store help prevent it?

**Answer:**
Training-serving skew happens when features used during training are computed differently from features used during serving (production). The model sees different data in production than what it trained on.

**Example:**
A fraud detection model uses the feature "average transaction amount in last 7 days."
- During training: This is calculated from a complete historical database — exact and accurate
- During serving: This is calculated in real-time from a streaming pipeline that has a 5-minute delay — slightly different values
- Result: The model's accuracy drops because it's seeing slightly different feature values than what it learned from

**How a Feature Store prevents this:**
A Feature Store (like Feast, Tecton, or Hopsworks) stores pre-computed features in one centralised place and serves the exact same features for both training and serving.

```
Without Feature Store:
Training: feature = compute_from_batch_data()     → might be different
Serving:  feature = compute_from_streaming_data()  → might be different
→ SKEW!

With Feature Store:
Training: feature = feature_store.get("avg_txn_7d")  → same source
Serving:  feature = feature_store.get("avg_txn_7d")   → same source
→ NO SKEW!
```

The Feature Store ensures consistency — the same logic computes the same feature the same way, whether the model is being trained or making live predictions.

---

## Q9: Walk through the Swiggy ETA prediction system across all five architecture layers.

**Answer:**
When you order food on Swiggy, all five layers work together:

**Step 1 — Application Layer:**
Swiggy app sends request with your user_id, restaurant_id, items ordered, delivery address, and current time. API Gateway authenticates your session. Business logic says: "User placed an order → need ETA prediction."

**Step 2 — Data Layer:**
Feature Store serves pre-computed features instantly (in under 10 milliseconds):
- restaurant_avg_prep_time = 18 min
- current_traffic_level = HIGH (from Google Maps API)
- distance_km = 4.2
- current_weather = RAIN
- active_drivers_nearby = 12
- restaurant_current_order_queue = 5 pending orders

These features are pre-computed and cached — not calculated on-the-fly.

**Step 3 — Model Layer:**
ETA prediction model receives all features and computes: prep_time (20 min) + travel_time (18 min) + buffer_for_rain (5 min) = 43 minutes.

**Step 4 — Application Layer (continued):**
Business logic adds a buffer: 43 + 2 = 45 minutes (round up for safety). Displays: "Estimated delivery: 45 minutes."

**Step 5 — Infrastructure Layer (running silently):**
Model runs on Kubernetes cluster with auto-scaling. If Swiggy runs a sale → 10x more orders → Kubernetes automatically spins up more model servers. All logs stored in S3 for future training.

**Step 6 — Monitoring Layer:**
Logs prediction = 45 min, actual delivery = 42 min → error = 3 min (acceptable). Tracks average prediction error across all orders today. Alerts if error > 10 min for 5% of orders. Checks if today's traffic pattern differs from training data (e.g., a city-wide bandh). Stores actual delivery times as feedback for next model retraining.

**Key takeaways:**
- The model is just one step — the surrounding system does most of the work
- Speed matters — entire flow must happen in under 1 second
- Features are pre-computed (not computed on-the-fly)
- Monitoring closes the loop — actual times feed back into retraining

---

## Q10: "What layer handles X?" — Match each task to its correct architecture layer.

**Answer:**

| Task | Which Layer? | Why? |
|---|---|---|
| Storing 3 years of customer purchase data | **Data Layer** | Data storage is a Data Layer responsibility |
| Training a new recommendation model | **Model Layer** | Model training is the core function of the Model Layer |
| Showing "Recommended for You" products on the app | **Application Layer** | This is the user-facing UI and business logic |
| Auto-scaling servers during Diwali sale traffic | **Infrastructure Layer** | Kubernetes scaling is an Infrastructure function |
| Detecting that the model's accuracy dropped 10% this week | **Monitoring Layer** | Performance tracking and alerting is Monitoring |
| Computing "user_click_count_last_7_days" and storing it | **Data Layer** | Feature Store is part of the Data Layer |
| Deciding if 5% of users should see the new model (A/B test) | **Application Layer** | A/B testing is managed at the Application Layer |
| Checking if today's incoming data looks different from training data | **Monitoring Layer** | Data drift detection is a Monitoring function |
| Running model predictions on NVIDIA GPUs | **Infrastructure Layer** | GPU compute is an Infrastructure resource |
| Versioning model v1.0, v1.1, v2.0 in a registry | **Model Layer** | Model Registry is part of the Model Layer |

---

## Q11: What is Data Drift? How is it different from Prediction Drift? Give examples of each.

**Answer:**

**Data Drift:** The data coming into the system today looks different from what the model was trained on. The input distribution has changed.

Example: A fraud detection model was trained on 2023 transaction data. In 2025, new types of UPI scams appear that look completely different from 2023 fraud patterns. The incoming data has drifted — new patterns the model has never seen.

**Prediction Drift:** The distribution of the model's predictions changes over time, even if individual inputs might look normal. The output distribution shifts.

Example: A credit scoring model that usually approves 40% of applications suddenly starts approving 65%. Individual applications might look fine, but the overall pattern has shifted — something has changed that makes the model behave differently.

**Key differences:**

| Aspect | Data Drift | Prediction Drift |
|---|---|---|
| **What changes** | Input data distribution | Output prediction distribution |
| **What to monitor** | Compare training data vs live data using statistical tests | Compare prediction distributions over time |
| **Detection** | KS test, PSI (Population Stability Index) | Track prediction class/score distributions daily |
| **Cause** | Real world changed (new customer types, new fraud methods) | Could be data drift, model bug, or feature computation error |

Both types of drift require monitoring. Data drift tells you "the world changed." Prediction drift tells you "the model is behaving differently." Either one could mean trouble.

---

## Q12: What is Apache Kafka and why is it important for AI systems?

**Answer:**
Apache Kafka is a distributed event streaming platform that can collect and process millions of data events per second in real-time.

Think of it as a high-speed highway for data — events (clicks, transactions, sensor readings) flow through Kafka from various sources to various destinations at massive scale.

Why it matters for AI systems:
1. **Real-time data collection:** Captures user events (clicks, purchases, page views) the moment they happen — essential for real-time features
2. **Scale:** Processes millions of events per second — handles the data volume of companies like Uber, LinkedIn, and Netflix
3. **Decoupling:** Producers (apps that generate data) and consumers (ML pipelines that use data) don't need to talk directly — Kafka sits in between and manages the flow
4. **Reliability:** Messages are stored durably — if a consumer goes down, it can pick up where it left off when it comes back

**Example — Uber's use of Kafka:**
When a rider books a cab, the event flows through Kafka. From there it goes to:
- The ETA prediction model (needs real-time traffic and driver location)
- The surge pricing model (needs real-time demand)
- The fraud detection model (needs transaction patterns)
- The data warehouse (for historical analysis)

All these consumers read from Kafka independently, at their own speed, without blocking each other.

---

## Q13: What is Kubernetes (K8s)? Why is it essential for serving AI models in production?

**Answer:**
Kubernetes is a container orchestration platform that automatically manages, scales, and deploys containerised applications.

Simple analogy: If Docker is like a shipping container (packages your code and dependencies into a portable box), then Kubernetes is the shipping port that manages thousands of containers — loading, unloading, routing, and replacing damaged ones.

Why it's essential for AI serving:

1. **Auto-scaling:** During a Swiggy sale, orders might increase 10x. Kubernetes automatically spins up more model-serving containers to handle the load, then scales down when demand drops. No human intervention needed.

2. **Self-healing:** If a model-serving container crashes, Kubernetes automatically restarts it or replaces it with a new one. Users don't notice any downtime.

3. **Rolling updates:** When deploying a new model version, Kubernetes gradually replaces old containers with new ones — no downtime. If the new version has issues, it rolls back automatically.

4. **Resource management:** Kubernetes allocates GPUs, memory, and CPU to different model containers based on their needs. Training gets the big GPUs; serving gets the fast-response containers.

Used by: Netflix, Uber, Airbnb, Spotify, Amazon — basically every major tech company running AI at scale.

---

## Q14: Compare Data Lake and Data Warehouse. When would you use each in an AI system?

**Answer:**

| Aspect | Data Lake | Data Warehouse |
|---|---|---|
| **Data type** | Raw, unstructured (images, logs, JSON, CSV, videos) | Cleaned, structured (tables with defined schema) |
| **Schema** | Schema-on-read (figure out structure when you use it) | Schema-on-write (structure defined before storing) |
| **Purpose** | Store everything, figure out use later | Store cleaned data ready for analysis and ML |
| **Cost** | Cheap (stores raw data without processing) | More expensive (requires cleaning and structuring) |
| **Speed of queries** | Slower (data not optimised for queries) | Fast (data is pre-structured and indexed) |
| **Tools** | S3 (AWS), Google Cloud Storage, Azure Data Lake | Snowflake, BigQuery, Amazon Redshift |
| **Think of it as** | A big warehouse where you dump everything in boxes | A well-organised library with catalogued books |

**When to use each:**
- **Data Lake:** Store raw data that you might need later — all user clickstreams, raw sensor data, video recordings, server logs. ML training often needs raw data.
- **Data Warehouse:** Store cleaned, aggregated data for dashboards, reporting, and feature computation. Business analysts query the warehouse directly.

In practice, most AI systems use BOTH:
Raw data flows into the Data Lake → Spark/dbt processes it → Cleaned data stored in Data Warehouse → Features computed and stored in Feature Store.

---

## Q15: What is MLflow? How does it help manage the Model Layer?

**Answer:**
MLflow is the most popular open-source tool for managing the ML lifecycle. It handles experiment tracking, model packaging, and model versioning.

MLflow has three core components:

1. **MLflow Tracking:** Records every experiment — which dataset was used, which hyperparameters, what accuracy was achieved. Like a lab notebook that auto-records everything.

   Example: You try 50 different model configurations. MLflow records every single run — so you can compare all 50 and pick the best one, even weeks later.

2. **MLflow Models:** Packages trained models in a standard format that can be deployed anywhere (Docker, Kubernetes, AWS, etc.).

3. **MLflow Model Registry:** A versioned catalog of models. You can mark models as "staging" or "production" and track which version is currently live.

   Example: Your fraud detection model has versions v1.0 (old), v1.1 (staging, being A/B tested), and v2.0 (in development). The registry tracks all of this.

Why it matters: Without a tool like MLflow, ML teams lose track of experiments ("Which parameters gave 92% accuracy last month?"), can't reproduce results, and have no systematic way to promote models from testing to production.

---

## Q16: Scenario — You're designing an AI system for Indian Railways to predict train delays. Which components would you need at each layer?

**Answer:**

**Data Layer:**
- **Ingestion:** Real-time data feeds — GPS signals from trains, platform sensor data, weather APIs, signal system status. Use Kafka for streaming.
- **Storage:** Data Lake (S3) for raw GPS and sensor data. Data Warehouse (BigQuery) for cleaned historical delay records.
- **Processing:** Apache Spark to process millions of GPS data points daily. Apache Airflow to schedule daily data pipelines.
- **Feature Store:** Pre-compute features like "avg_delay_at_station_X_at_time_Y," "current_weather_on_route," "number_of_trains_on_same_track." Use Feast.
- **Data Quality:** Great Expectations to validate incoming data (GPS coordinates within India, speed values realistic).

**Model Layer:**
- **Experiment Tracking:** MLflow to record different model experiments
- **Training:** Train models using historical delay data — try XGBoost for structured features, LSTM neural network for time-series patterns
- **Model Registry:** MLflow Registry to version models (delay-predictor-v1, v2, etc.)
- **Serving:** TensorFlow Serving or FastAPI to serve predictions in real-time

**Application Layer:**
- **API Gateway:** Receives requests from the IRCTC app and station displays
- **Business Logic:** Convert model output (delay = 23 minutes) into user-friendly message: "Train 12302 Rajdhani is expected 25 minutes late at New Delhi"
- **UI:** IRCTC app and station display boards showing live predictions
- **Feedback Collection:** Collect actual arrival times to compare with predictions

**Infrastructure Layer:**
- **Compute:** GPU instances for training, CPU instances for serving (delay prediction doesn't need GPUs for inference)
- **Kubernetes:** Auto-scale during peak booking/travel periods (festivals, holidays)
- **CI/CD:** GitHub Actions for automated model deployment

**Monitoring Layer:**
- Track prediction accuracy (predicted delay vs actual delay)
- Monitor data drift (weather patterns changing, new routes added)
- Alert if average prediction error exceeds 15 minutes
- Business metric: Track if passenger satisfaction improves with better delay information

---

## Q17: What is CI/CD in the context of AI systems? How is it different from CI/CD in traditional software?

**Answer:**
CI/CD stands for Continuous Integration / Continuous Deployment. It automates the process of building, testing, and deploying code and models.

**In traditional software:**
Code change → Automated tests → Build → Deploy to production
- Focus: Does the new code pass all tests? Does it compile?

**In AI systems (often called ML CI/CD or MLOps):**
Code change OR data change OR model retraining → Automated tests → Model validation → Deploy to production
- Focus: Does the new model perform better? Are features consistent? Has data quality dropped?

Key differences:

| Aspect | Traditional CI/CD | AI/ML CI/CD |
|---|---|---|
| **What triggers deployment** | Code change | Code change, data change, or model retraining |
| **What's tested** | Code correctness (unit tests) | Code + model accuracy + data quality + feature consistency |
| **What's deployed** | Application code | Application code + model artifacts + feature pipelines |
| **Rollback reason** | Bug in code | Bug in code OR model accuracy drops OR data drift detected |

Tools: GitHub Actions, GitLab CI, Jenkins (same tools, but ML-specific tests added).

The key insight is that in AI systems, the model can break even without any code change — just because the data changed. So CI/CD pipelines must also monitor data quality and model performance, not just code correctness.

---

## Q18: Explain the difference between batch processing and real-time processing in AI systems. When would you use each?

**Answer:**

| Aspect | Batch Processing | Real-Time Processing |
|---|---|---|
| **How it works** | Processes large amounts of data at scheduled intervals | Processes data immediately as it arrives |
| **Latency** | Minutes to hours | Milliseconds to seconds |
| **When it runs** | Scheduled (daily, hourly, weekly) | Continuously, 24/7 |
| **Best for** | Training models, computing historical features, generating reports | Serving predictions, real-time fraud detection, live recommendations |
| **Tools** | Apache Spark, Apache Airflow, dbt | Apache Kafka, Apache Flink, Redis |

**When to use each:**

**Batch:** Model training (retrain weekly on accumulated data), computing features like "user's total purchases in last 30 days," generating daily recommendation lists for all users overnight.

**Real-time:** Fraud detection (must flag suspicious transaction before it completes), showing delivery ETA when user places order, updating stock recommendations when market conditions change.

**Netflix example — both used together:**
- **Batch (offline):** Every night, Netflix retrains its recommendation models using the previous day's viewing data. It pre-computes personalised recommendation lists for all 230M+ users.
- **Real-time (online):** When you start watching a show and stop after 10 minutes, the real-time system immediately adjusts your recommendations to de-prioritise similar shows.

Most production AI systems use BOTH — heavy training happens offline (batch), while serving and real-time adjustments happen online.

---

## Q19: What would happen to Swiggy's ETA system if the Feature Store failed? Explain the cascading impact across layers.

**Answer:**
If the Feature Store fails, it creates a cascading failure across multiple layers:

**Immediate impact — Data Layer:**
Pre-computed features (restaurant prep time, traffic level, driver availability) become unavailable. The system can't serve feature values in under 10ms as expected.

**Impact on Model Layer:**
The ETA model receives missing or stale features. Without "current_traffic_level = HIGH," the model might predict 25 minutes instead of 45 minutes. Predictions become inaccurate.

**Impact on Application Layer:**
Users see wildly incorrect ETAs. Someone orders food during peak rain and sees "Delivery in 20 min" when it actually takes 50 min. Customer satisfaction drops. Support calls spike.

**Impact on Infrastructure Layer:**
If the Feature Store failure causes the model-serving containers to timeout waiting for features, Kubernetes might start auto-scaling (spinning up more containers), wasting resources without fixing the root cause.

**Impact on Monitoring Layer:**
Monitoring should detect this quickly — prediction error spikes dramatically, latency increases, and feature availability drops to zero. If monitoring is set up correctly, an alert fires within minutes.

**Recovery path:**
1. Monitoring detects the failure and alerts the team
2. As a fallback, the system could use cached (stale) features or default values
3. Business logic could add a larger buffer to ETAs during the outage
4. Feature Store is restored, and the system recovers

This demonstrates why all five layers must work together — a failure in one layer ripples through the entire system.

---

## Q20: Scenario — A fintech startup in Bangalore is building a loan approval AI system. Design the architecture across all five layers, including specific tools.

**Answer:**

**Data Layer:**
- **Ingestion:** Kafka to stream real-time loan applications and repayment events
- **Storage:** Data Lake (S3) for raw application documents (Aadhaar scans, bank statements). Data Warehouse (BigQuery) for structured applicant data.
- **Processing:** Apache Spark for processing millions of historical loan records. dbt for transforming raw data into analysis-ready tables.
- **Feature Store:** Tecton or Feast. Pre-compute features: credit_score, income_to_debt_ratio, repayment_history_last_12_months, employer_stability_score, age, city_tier.
- **Data Quality:** Great Expectations — validate that Aadhaar numbers are 12 digits, income values are positive, no duplicate applications.

**Model Layer:**
- **Experiment Tracking:** MLflow — record all experiments comparing XGBoost, Random Forest, and neural network models
- **Training:** AWS SageMaker Pipelines for automated weekly retraining
- **Model Registry:** MLflow Model Registry — version models as loan-approval-v1, v2, etc. Track which is in production.
- **Serving:** FastAPI + TorchServe for real-time scoring (approve/reject within 2 seconds)
- **Evaluation:** Measure accuracy, precision, recall. Critically: monitor for bias across gender, religion, and geography (RBI fairness requirements).

**Application Layer:**
- **API Gateway:** AWS API Gateway — handles authentication, rate limiting
- **Business Logic:** Model returns probability (0.82). If probability > 0.7 → auto-approve. If 0.4-0.7 → manual review. If < 0.4 → auto-reject.
- **UI:** Web dashboard for loan officers showing application details + model recommendation
- **A/B Testing:** Test new model versions on 5% of applications before full rollout
- **Feedback Collection:** Track actual repayment/default for every approved loan → feeds back into retraining

**Infrastructure Layer:**
- **Compute:** CPU instances for serving (loan scoring doesn't need GPUs). GPU instances for weekly retraining.
- **Kubernetes:** Auto-scale during peak application hours (salary week, festival season)
- **CI/CD:** GitHub Actions — automated testing of model code + data validation + model accuracy checks before deployment
- **IaC:** Terraform for managing AWS resources

**Monitoring Layer:**
- **Model performance:** Track approval rate, default rate, accuracy monthly
- **Data drift:** Monitor if applicant demographics or income distributions are shifting
- **Fairness monitoring:** Ensure approval rates are not biased by gender, caste, or region
- **Prediction drift:** Alert if the model suddenly approves 30% more or fewer applications than baseline
- **Business metrics:** Track revenue from loans, default losses, customer satisfaction

**Special considerations for fintech:**
- Data must comply with RBI regulations — can't send to third-party APIs
- Must maintain audit trail for every model decision (explainability)
- Must regularly test for and report on bias/fairness

---

*End of Session 3 Questions and Answers*
