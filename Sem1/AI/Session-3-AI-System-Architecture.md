# Session 3: AI System Architecture

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** Class Notes
>
> **Contact Session:** 3 (Module 1: Foundations of AI Systems)

---

---

## 3.1 The Five-Layer AI System Architecture

A modern AI system is structured in layers, each with distinct responsibilities. This layered architecture enables teams to work independently, replace components without affecting others, and scale each layer based on its specific needs.

```
┌─────────────────────────────────────────────────────────┐
│                   APPLICATION LAYER                       │
│   User interface, business logic, A/B testing, UX        │
├─────────────────────────────────────────────────────────┤
│                   MODEL LAYER                             │
│   Model serving, inference, model registry, versioning   │
├─────────────────────────────────────────────────────────┤
│                   DATA LAYER                              │
│   Data pipelines, feature store, data warehouse/lake     │
├─────────────────────────────────────────────────────────┤
│                   INFRASTRUCTURE LAYER                    │
│   Compute (GPUs/TPUs), storage, networking, orchestration│
├─────────────────────────────────────────────────────────┤
│                   MONITORING LAYER                        │
│   Logging, metrics, drift detection, alerting, feedback  │
└─────────────────────────────────────────────────────────┘
```

### 3.1.1 Data Layer

The foundation of every AI system. Responsible for collecting, storing, processing, and serving data.

| Component | Purpose | Examples |
|---|---|---|
| **Data ingestion** | Collect data from sources (databases, APIs, logs, streams). | Apache Kafka, AWS Kinesis, Airbyte. |
| **Data storage** | Store raw and processed data durably. | Data Lakes (S3, GCS), Data Warehouses (Snowflake, BigQuery). |
| **Data processing** | Clean, transform, aggregate data. ETL/ELT pipelines. | Apache Spark, dbt, Airflow. |
| **Feature store** | Manage and serve ML features consistently across training and serving. | Feast, Tecton, Hopsworks. |
| **Data quality** | Monitor data for completeness, freshness, schema changes, anomalies. | Great Expectations, Deequ. |

### 3.1.2 Model Layer

Handles everything related to the ML model — from training to serving predictions.

| Component | Purpose | Examples |
|---|---|---|
| **Experiment tracking** | Log experiments, hyperparameters, metrics, and artifacts. | MLflow, Weights & Biases, Neptune. |
| **Training pipeline** | Automate model training — data loading, preprocessing, training, evaluation. | Kubeflow, SageMaker Pipelines, Vertex AI. |
| **Model registry** | Store, version, and manage trained models. Track which model is in production. | MLflow Model Registry, SageMaker Model Registry. |
| **Model serving** | Host models and serve predictions via APIs. Handle batching, caching, scaling. | TensorFlow Serving, TorchServe, Triton, Seldon. |
| **Model evaluation** | Evaluate model performance on held-out test sets. Compare against baselines. | Custom evaluation pipelines, benchmarking suites. |

### 3.1.3 Application Layer

Where the model's predictions meet the real world — the user-facing layer.

| Component | Purpose | Examples |
|---|---|---|
| **API gateway** | Single entry point for all model requests. Rate limiting, authentication, routing. | Kong, AWS API Gateway, FastAPI. |
| **Business logic** | Transform raw model output into user-relevant actions (e.g. model says 0.87 → show "Recommended"). | Application code in Python/Java/Go. |
| **A/B testing** | Test different model versions on different user groups to measure impact. | LaunchDarkly, Optimizely, custom solutions. |
| **User interface** | Present results to users (web app, mobile app, dashboard). | React, Flutter, Streamlit (for prototyping). |
| **Feedback collection** | Capture user feedback (clicks, ratings, corrections) to improve the model. | Custom event logging, analytics pipelines. |

### 3.1.4 Infrastructure Layer

The compute and networking resources that run everything.

| Component | Purpose | Examples |
|---|---|---|
| **Compute** | CPU/GPU/TPU for training and inference. | AWS EC2 (GPU), Google TPU, Azure GPU VMs. |
| **Container orchestration** | Deploy and scale model-serving containers. | Kubernetes (EKS/GKE/AKS), Docker. |
| **Storage** | Object storage for data and models. | S3, GCS, Azure Blob. |
| **CI/CD** | Automated build, test, deploy pipelines for models and code. | GitHub Actions, GitLab CI, Jenkins. |
| **Infrastructure as Code** | Define infrastructure declaratively. | Terraform, Pulumi, CloudFormation. |

### 3.1.5 Monitoring Layer

Observes the entire system and alerts when things go wrong.

| What to monitor | Why | Metrics |
|---|---|---|
| **Model performance** | Model accuracy may degrade over time as data shifts. | Accuracy, precision, recall, F1, AUC over time. |
| **Data drift** | The distribution of incoming data may change (new user demographics, seasonal changes). | Statistical tests (KS test, PSI) comparing training data vs serving data distributions. |
| **Prediction drift** | The distribution of predictions may shift even if data looks similar. | Distribution of predicted classes/scores over time. |
| **System health** | Infrastructure may have issues (high latency, out-of-memory, disk full). | Latency (p50, p95, p99), error rate, throughput, memory/CPU utilization. |
| **Business metrics** | Ultimately, does the model improve business outcomes? | Click-through rate, conversion rate, revenue, user retention. |

---

## 3.2 Architecture Walkthrough: A Modern AI Application

**Example: An E-Commerce Product Recommendation System**

Let's trace how a recommendation flows through the architecture when a user opens the app:

```
1. USER opens app
      ↓
2. APPLICATION LAYER
   - API gateway receives request (user_id, context)
   - Business logic: "This user is on the homepage → need Top Picks"
      ↓
3. MODEL LAYER
   - Model serving endpoint receives user features
   - Model computes scores for candidate items
   - Returns top 20 ranked items
      ↓
4. DATA LAYER
   - Feature store provides user features (past purchases, browsing history)
   - Feature store provides item features (category, price, popularity)
   - All features served with <10ms latency (pre-computed)
      ↓
5. APPLICATION LAYER (continued)
   - Business logic filters: remove out-of-stock, apply diversity rules
   - A/B testing: user in experiment group → show new model's results
   - Render final 10 items in the UI
      ↓
6. USER sees recommendations, clicks on Item #3
      ↓
7. MONITORING LAYER
   - Log: user saw 10 items, clicked item #3 → click-through rate tracked
   - Data pipeline: user click event sent to data lake for future training
   - Model monitor: check if today's recommendation distribution looks normal
      ↓
8. FEEDBACK LOOP
   - Click data (what users engaged with) feeds back into training data
   - Next model retrain will include this new data
```

---
---


---

*End of Session 3*
