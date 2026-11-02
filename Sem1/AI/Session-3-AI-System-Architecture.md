# Session 3: AI System Architecture (From Handout)

> BITS Pilani — SS ZG662 | Module 1: Foundations of AI Systems

---

## 3.1 The Five-Layer AI System Architecture

```
┌─────────────────────────────────────────┐
│          APPLICATION LAYER              │
│  API Gateway, Business Logic, A/B Test  │
├─────────────────────────────────────────┤
│            MODEL LAYER                  │
│  Training, Serving, Registry, Eval      │
├─────────────────────────────────────────┤
│             DATA LAYER                  │
│  Pipelines, Storage, Feature Store, QA  │
├─────────────────────────────────────────┤
│        INFRASTRUCTURE LAYER             │
│  GPU/TPU, Kubernetes, CI/CD, IaC        │
├─────────────────────────────────────────┤
│         MONITORING LAYER                │
│  Model perf, Data drift, System health  │
└─────────────────────────────────────────┘
```

---

### 3.1.1 Data Layer

| Component | Function | Tools |
|---|---|---|
| Data Ingestion | Pull data from sources into one place | Kafka, Kinesis |
| Data Storage | Store raw + processed data | Data Lake (S3/GCS), Warehouse (Snowflake, BigQuery) |
| Data Processing | Clean, transform, prepare data | Spark, dbt, Airflow |
| Feature Store | Pre-computed features consistent between training and serving | Feast, Tecton, Hopsworks |
| Data Quality | Check for missing values, wrong formats, distribution shifts | Great Expectations, Deequ |

---

### 3.1.2 Model Layer

| Component | Function | Tools |
|---|---|---|
| Experiment Tracking | Record every experiment (data, settings, results) | MLflow, Weights & Biases |
| Training Pipeline | Automate: load data → train → evaluate → save | Kubeflow, SageMaker Pipelines, Vertex AI |
| Model Registry | Store model versions with metadata and status | MLflow Model Registry, SageMaker Registry |
| Model Serving | Host model, answer prediction requests via API | TensorFlow Serving, TorchServe, Triton, Seldon |
| Model Evaluation | Test on held-out data before deployment | Custom evaluation pipelines |

---

### 3.1.3 Application Layer

| Component | Function | Tools |
|---|---|---|
| API Gateway | Receives requests, authenticates, rate-limits | Kong, AWS API Gateway, FastAPI |
| Business Logic | Transforms model output into user actions | Custom code (score → "Recommended" / block transaction) |
| A/B Testing | Test two versions on different user groups | LaunchDarkly, Optimizely |
| User Interface | App/website users interact with | React, Flutter, Streamlit |
| Feedback Collection | Captures user reactions (clicks, ratings, corrections) | Custom event logging → feeds back to training data |

---

### 3.1.4 Infrastructure Layer

| Component | Function | Tools |
|---|---|---|
| Compute (CPU/GPU/TPU) | Processing power for training and serving | AWS P4d, Google TPU v5 |
| Container Orchestration | Package, run, scale model containers | Kubernetes, Docker |
| Storage | Store data, models, logs, artifacts | S3, GCS, Azure Blob |
| CI/CD | Automated build, test, deploy | GitHub Actions, GitLab CI, Jenkins |
| Infrastructure as Code | Define infra in config files | Terraform, Pulumi, CloudFormation |

---

### 3.1.5 Monitoring Layer

| What to Monitor | Metrics | Why |
|---|---|---|
| Model Performance | Accuracy, Precision, Recall, F1 over time | Models degrade silently |
| Data Drift | KS test, PSI — training vs live distributions | Input data changes over time |
| Prediction Drift | Distribution of predicted scores over time | Sudden shifts signal problems |
| System Health | Latency (p50/p95/p99), error rate, throughput, CPU/memory | Infrastructure issues |
| Business Metrics | Revenue, conversion, retention, satisfaction | Does AI actually improve outcomes? |

---

## 3.2 Architecture Walkthrough: Swiggy ETA Prediction

**Traced through all 5 layers:**

1. **Application Layer:** User places order → API gateway → "need ETA prediction"
2. **Data Layer:** Feature Store serves pre-computed features (restaurant_avg_prep_time, traffic_level, distance, weather, active_drivers, order_queue) in <10ms
3. **Model Layer:** ETA model receives features → predicts 43 minutes
4. **Application Layer:** Business logic adds buffer → displays "45 minutes"
5. **Infrastructure Layer:** Kubernetes auto-scales during peak (sales → 10x orders → more servers)
6. **Monitoring Layer:** Log prediction=45min, actual=42min, error=3min ✓. Alert if error > 10min. Data drift check. Actual times → next retraining.

---

## 3.3 Zillow Case Study — Monitoring Failure

- Zillow built AI to predict house prices for home-buying business
- Housing market changed rapidly (COVID)
- **Data drift** — model overpredicted prices
- Without adequate monitoring, bought thousands of houses at inflated prices
- **Lost $881 million**, shut down business unit, 2,000 laid off
- Lesson: Proper drift detection could have caught the problem months earlier

---
