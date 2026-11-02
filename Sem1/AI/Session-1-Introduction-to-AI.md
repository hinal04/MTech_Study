# Session 1: Foundations of AI Systems (71 Slides)

> BITS Pilani — SS ZG662 | Module 1: Foundations of AI Systems

---

## 1.1 AI vs ML vs DL

### Nested Relationship

```
AI (broadest) ⊃ ML ⊃ DL
```

### Comparison Table (6 Dimensions)

| Dimension | AI | ML | DL |
|---|---|---|---|
| What | Any system mimicking human intelligence | Learns patterns from data | ML using deep neural networks |
| How | Human writes rules | Algorithm finds rules from data | Network discovers features + rules |
| Data needed | Little/none | Thousands–millions | Millions–billions |
| Feature engineering | Manual | Manual | Automatic |
| Compute | Low | Medium | High (GPU/TPU) |
| Example | Rule-based chess | Spam filter (Gmail) | ChatGPT, image recognition |

### When to Use What

- **Rules clear and simple** → Traditional AI (rule-based)
- **Patterns in data, hard to write rules** → ML
- **Complex data (images, text, audio)** → DL
- **Very little data** → Traditional AI
- **Abundant data + compute** → DL

### Enterprise Context

| Function | Use Case |
|---|---|
| Banking/Finance | Credit scoring — ML predicts default probability |
| Logistics | Route optimisation — ML finds fastest/cheapest routes |
| Legal | Intelligent document processing — NLP extracts clauses from contracts |
| Search | Enterprise search — ML ranks docs by relevance |
| Manufacturing | Predictive maintenance — sensor data → predict equipment failure |

---

## 1.2 Problem → Solution → ML Framing

### 3-Step Framing Process

```
Product Goal (Business Need) → Solution Approach (Is ML right?) → ML Framing (Task Type)
```

### Rule-Based vs ML Decision

| Use Rules When | Use ML When |
|---|---|
| Logic is clear, doesn't change | Patterns too complex for manual rules |
| Few conditions | Hundreds of interacting factors |
| Example: age > 18 → allow | Example: predict which orders will be returned |

### ML Problem Framing — 8-Step Checklist

1. Define criteria for successful outcome
2. Establish observable, quantifiable performance metric
3. Ensure stakeholders agree on the metric
4. Formulate ML question: inputs → outputs → metric
5. Evaluate whether ML is actually the right approach
6. Check if sufficient data exists
7. Create strategy for data sourcing and annotation
8. Start with a simple, interpretable model first

### ML Task Types

| Task | Output | Example |
|---|---|---|
| Binary Classification | Yes/No | Will this order be returned? |
| Multi-class Classification | One of N categories | What language is this text? |
| Regression | Continuous number | Delivery time = 37 minutes |
| Ranking | Ordered list | Search result ordering |
| Clustering | Groups | Customer segmentation |
| Anomaly Detection | Normal/Anomalous | Is this UPI transaction fraudulent? |

---

## 1.3 Why AI Projects Fail

**85% of AI projects never make it to production.**

### 7 Reasons

1. Wrong problem framing — solving a problem nobody has
2. Poor data quality — garbage in, garbage out
3. No clear success metric — "make it better" is not measurable
4. Using AI where simple rules would work
5. Lack of MLOps infrastructure — model works on laptop but can't deploy
6. Stakeholder misalignment — tech and business want different things
7. Ignoring data drift — model degrades but nobody notices

### "Should We Use AI?" Checklist

- Can you clearly define what "success" looks like?
- Do you have enough quality data?
- Is the problem too complex for manual rules?
- Can you measure the model's impact on business?
- Do you have infrastructure to deploy and monitor?

---

## 1.4 Model = 20% / System = 80%

The model is ~20% of total effort. Remaining 80%: data integration, pipeline automation, monitoring, deployment, retraining, business process integration.

**Credit risk prediction example:** Model training = 20%. The other 80% = sourcing credit bureau data, compliance checks, integration with loan origination system, monitoring for drift, quarterly retraining.

---

## 1.5 Three Levels of ML Software (Iceberg Metaphor)

| Level | % of System | What It Contains |
|---|---|---|
| ML Code | 5% | Model training, inference logic |
| ML Infrastructure | 35% | Data pipelines, feature store, serving, registry |
| ML Operations | 60% | Monitoring, retraining, deployment, CI/CD, orchestration |

**Three assets to manage:** Data, Model, Code

---

## 1.6 Explainable AI (XAI)

### Interpretability vs Performance Trade-off

Simple models (linear regression, decision trees) → easy to explain but less accurate.
Complex models (deep learning, ensembles) → more accurate but harder to explain.

### XAI Techniques

| Technique | How It Works |
|---|---|
| **SHAP** | Game-theory-based feature attribution. Shows each feature's contribution to prediction. |
| **LIME** | Perturbs input, fits local interpretable model. Per-prediction explanation. |
| **Attention Visualization** | Shows which parts of input the model focused on (for transformers/NLP). |

### Bank Loan Rejection Example

Model rejects a loan application. SHAP shows: debt-to-income ratio (-25%), credit score (-18%), employment tenure (+10%). Applicant can understand why they were rejected.

---

## 1.7 ML Engineering

### Data Scientist vs ML Engineer

| Aspect | Data Scientist | ML Engineer |
|---|---|---|
| Focus | Build and evaluate models | Deploy and maintain models in production |
| Tools | Jupyter, scikit-learn, pandas | Docker, Kubernetes, Airflow, MLflow |
| Output | Trained model + analysis | Production system serving predictions |
| Skills | Statistics, ML algorithms | Software engineering, DevOps, distributed systems |

### What ML Engineers Build

Data pipelines, feature stores, model serving endpoints, monitoring dashboards, retraining pipelines, CI/CD for ML.

---

## 1.8 AI System Lifecycle

### 6 Stages (Circular)

```
1. Define Problem → 2. Collect & Prepare Data → 3. Build & Train Model →
4. Deploy & Serve → 5. Monitor & Evaluate → 6. Iterate & Improve → back to 1
```

**Netflix example:** Problem (recommend shows) → Data (viewing history, ratings) → Model (multiple ML models) → Deploy (serve 230M+ users) → Monitor (CTR, watch time) → Iterate (A/B test, retrain weekly)

### Business Goal Phase — 13 Steps

1. Understand business requirements
2. Form a business question
3. Review ML feasibility
4. Evaluate costs (data, compute, inference, wrong predictions)
5. Review published work / Kaggle / industry case studies
6. Define key performance metrics
7. Define ML task type
8. Identify must-have features
9. Design small, focused POCs
10. Evaluate external data sources
11. Establish pathways to production

---

## 1.9 ML Lifecycle Architecture (12 Components)

```
Data Ingestion → Data Validation → Data Transformation → Feature Store →
Model Training → Model Evaluation → Model Validation → Model Registry →
Model Serving → Model Monitoring → Ground Truth Collector → Metadata Store
```

| Component | Purpose |
|---|---|
| Data Ingestion | Pull raw data from databases, APIs, streams |
| Data Validation | Schema checks, range checks, null checks, distribution checks |
| Data Transformation | Clean, normalise, format data |
| Feature Store | Store pre-computed features; prevent training-serving skew |
| Model Training | Run learning algorithm on prepared data |
| Model Evaluation | Test on held-out data, compare with previous versions |
| Model Validation | Check fairness, bias, latency, compliance |
| Model Registry | Store all model versions with metadata |
| Model Serving | Host model, respond to prediction requests |
| Model Monitoring | Track accuracy, latency, data drift in production |
| Ground Truth Collector | Collect correct answers for measuring model performance |
| Metadata Store | Record experiment details, pipeline runs, data lineage |

### Architecture Support Components

| Component | Function |
|---|---|
| **Drift Feedback Loop** | Monitoring detects drift → automatically triggers retraining pipeline |
| **Alarm Manager** | Publishes notifications, triggers retraining, escalates critical alerts |
| **Scheduler** | Initiates retraining at defined intervals (daily/weekly/monthly) |
| **Lineage Tracker** | Records data lineage, model lineage, infra lineage, environment lineage for reproducibility |

---

## 1.10 Data Engineering

**Gartner definition:** "An iterative and agile process for exploring, combining, cleaning, and transforming raw data into curated datasets."

- Data preparation is the **most expensive step** in the ML pipeline (60-80% of budget)
- Errors in data **propagate forward** into model training

### Data Collection

**Sources:** Internal databases, APIs, user-generated, third-party, sensors/IoT, time-series, event streams

**Ingestion modes:**

| Mode | How | When | Tools |
|---|---|---|---|
| Batch | Large volumes at scheduled intervals | Training, periodic reports | Spark, HDFS, Parquet |
| Streaming | Event-by-event, minimal delay | Real-time predictions (fraud) | Kafka, Flink, Kinesis |

Also includes: **Synthetic data generation** and **Data enrichment** (adding external context)

### Data Exploration (Profiling)

Compute metadata: min/max, mean, standard deviation, missing value count, unique value count, data types.

### Data Validation

Schema validation, range checks, null checks, distribution checks, referential integrity, automated error detection.

### Data Wrangling

Re-formatting attributes, correcting errors, missing values imputation.

### Data Labelling

Assigning categories for supervised learning. Tools: Label Studio, Figure Eight/Appen, Google Data Labeling Service, SageMaker Ground Truth.

### Data Splitting

| Set | % | Purpose |
|---|---|---|
| Training | 70-80% | Model learns patterns |
| Validation | 10-15% | Tune hyperparameters |
| Test | 10-15% | Final evaluation (never touch during training) |

### 4 Functions of Data

1. **Defining the goal** — input/output pairs define what system learns
2. **Training the algorithm** — teaching the model
3. **Measuring performance** — held-out test set evaluation
4. **Building monitoring baselines** — production baselines for drift detection

**Critical rule:** Same data processing steps for training AND inference. Violation → training-serving skew.

### Data from Vendors

Common issues: missing values, duplicates, schema mismatches, outliers. Formats: ZIP/XML/CSV/JSON. Raw vendor data → ETL pipeline → data lake.

### EDA (Exploratory Data Analysis)

Histograms, scatter plots, box plots, correlation heatmaps, missing value heatmaps.

**Wrangler tools:** AWS DataWrangler, Google Cloud Dataprep, Trifacta, OpenRefine, pandas Profiling.

---

## 1.11 Data Preprocessing — 8 Strategies

| Strategy | What It Does |
|---|---|
| Clean | Remove outliers/duplicates, impute missing data |
| Balance | Ensure classes are equally represented |
| Replace | Substitute invalid/corrupted values |
| Impute | Fill missing values (mean/median/mode/forward fill/model-based/drop) |
| Partition | Split into train/val/test sets |
| Scale | Normalize or standardize features |
| Augment | Artificially increase training data |
| Unbias | Detect and mitigate biases |

### Cleaning — Imputation Methods

Mean/median fill, mode fill, forward/backward fill, model-based imputation, drop rows.

### Partitioning — Avoiding Leakage

- **Never normalize before splitting** — test set statistics leak into training
- **Never shuffle time-series** — future data leaks into training
- **Split first, then preprocess each split independently**

### Scaling

| Technique | Formula | Output | Best When |
|---|---|---|---|
| Normalization (Min-Max) | (value-min)/(max-min) | 0 to 1 | Known bounds, no extreme outliers |
| Standardization (Z-score) | (value-mean)/std | ~-3 to +3 | Has outliers, algorithm assumes normal distribution |

**Algorithms that NEED scaling:** K-Means, KNN, PCA, gradient descent (neural networks, logistic regression).
**Algorithms that DON'T need scaling:** Decision trees, Random Forest, XGBoost.

### Unbias/Balance

| Bias Type | Example | Mitigation |
|---|---|---|
| Class imbalance | 99% non-fraud, 1% fraud | SMOTE, undersampling, class weights |
| Representation bias | Hiring model trained mostly on male resumes | Collect diverse data, fairness-aware algorithms |
| Measurement bias | Income self-reported for some groups, verified for others | Standardize collection methods |
| Historical bias | Past lending decisions reflect discrimination | Audit training data, remove proxy features |

**Amazon hiring example:** AI trained on historical resumes (mostly male) learned to penalize female applicants.

### Augmentation

- **Image:** rotation, flipping, cropping, color jitter
- **Text:** synonym replacement, back-translation
- **Audio:** speed perturbation, noise injection

---

## 1.12 Feature Engineering (4 Sub-steps)

| Step | Techniques |
|---|---|
| **Creation** | One-hot encoding, binning, splitting, calculated features |
| **Transformation** | Cartesian products, non-linear transforms, domain-specific |
| **Extraction** | PCA, ICA, LDA |
| **Selection** | Filter (correlation), Wrapper (forward/backward), Embedded (L1/tree importance) |

---

## 1.13 Model Training

### Key Concepts

- **Algorithm selection** — choose appropriate algorithm for task type
- **Objective metric (loss function)** — what the model optimizes
- **Hyperparameters:** learning rate, epochs, batch size, max depth, regularization

### Hidden Patterns

Model learns a mapping function from inputs → target. Correlation threshold > 60% → drop redundant features. Dimensionality reduction (400 features → 50).

### Debugging/Profiling

| Problem | Symptom |
|---|---|
| Loss not decreasing | Learning rate too low, data issue |
| Loss exploding | Learning rate too high |
| Overfitting | Train acc high, val acc low |
| Underfitting | Both train and val acc low |
| GPU underutilization | Batch size too small |
| Memory overflow | Batch size too large, model too big |

### Model Evaluation

**Offline:** Hold-out test set, k-fold cross-validation
**Online:** A/B testing, canary deployment, shadow mode

### Model Validation

Generalization check, performance vs baseline, fairness check, robustness check.

### Model Testing

Latency testing (P99), throughput testing, infrastructure testing, operational testing.

### Model Selection — 6 Criteria

Accuracy, latency, model size, maintenance cost, explainability, business alignment → best model goes to **model registry**.

### Distributed Training

| Type | How |
|---|---|
| Model parallelism | Split model across GPUs |
| Data parallelism | Split data into mini-batches across nodes |

### Imbalanced Data

Example: 85K no / 15K yes. Use logistic regression, ensemble models (Random Forest, XGBoost), adjust class weights.

---

## 1.14 Model Deployment

**Deployment modes:** Real-time endpoints (API), batch transform, edge deployment.

**Model serving:** Load balancing, auto-scaling, versioning, failover.

### Deployment Pipeline

```
QA (unit testing) → Staging → UAT (User Acceptance Testing) → Production
```

### Retraining Triggers

| Type | How |
|---|---|
| Scheduled | CI/CD pipeline at fixed times (10AM/4PM/10PM) |
| Event-based | Data provider uploads → triggers retraining |

---

## 1.15 Model Monitoring

### 6 Areas

1. Data ingestion issues
2. Data drift
3. Model degradation
4. Concept drift
5. Bias/fairness
6. Feature attribution drift

### Performance Monitoring

ML-specific signals (prediction deviation), triggers for retraining, dashboards, alert levels (warning/critical/emergency).

### Performance Logging

Every inference request → log: input features, prediction, timestamp, latency, model version.

### Rollback Strategy

Automatic rollback if accuracy drops >10% or latency exceeds SLA.

---

## 1.16 Nine ML System Components

| Component | Function |
|---|---|
| **Ground-Truth Collector** | Collects correct answers (sale price, churn event, spam label). Delayed ground truth problem. |
| **Data Labeller** | Manual labelling: Label Studio, Figure Eight/Appen, Google Data Labeling, SageMaker Ground Truth |
| **Evaluator** | Prediction accuracy + business impact + system metrics. Compares models + deployment safety. |
| **Performance Monitor** | Production data → database → evaluator → dashboard showing performance over time |
| **Featurizer** | customer_id → full feature vector. Batch (pre-computed) vs real-time ("hot features"). Microservice pattern. |
| **Orchestrator** | ETL+split → featurize → prepare → model builder → evaluate → deploy. Platforms: Kubeflow, SageMaker. |
| **Model Builder** | Trains models using prepared features and labels |
| **Model Server** | Hosts model, serves predictions via API |
| **Front-End** | User-facing application consuming model predictions |

---

## 1.17 AI Applications Across Industries

| Industry | Application | Example |
|---|---|---|
| Healthcare | Disease prediction, drug discovery | DeepMind AlphaFold |
| Finance | Fraud detection, credit scoring | PayPal fraud detection |
| E-commerce | Recommendations, demand forecasting | Amazon (35% revenue from recommendations) |
| Manufacturing | Predictive maintenance, quality control | Siemens |
| Transport | Autonomous driving, route optimization | Waymo |
| Customer Service | Virtual assistants | Bank of America Erica |
| Education | Personalized learning | Khan Academy + GPT-4 |
| Agriculture | Crop monitoring, precision farming | John Deere autonomous tractors |
| Legal | Contract analysis | JPMorgan COIN (seconds vs 360,000 hours) |
| Entertainment | Content recommendation | Spotify Discover Weekly |
| Telecom | Network optimization, churn prediction | Jio |

---
