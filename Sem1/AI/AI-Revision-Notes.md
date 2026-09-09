# AI Systems — Quick Revision Notes
> BITS Pilani | Sessions 1-8 | Last-Minute Reference

---

## Session 1: Introduction to AI

### AI vs ML vs DL (Nested Relationship)
```
AI (broadest) ⊃ ML ⊃ DL
```
- **AI** — machines mimicking human intelligence (reasoning, learning, problem-solving)
- **ML** — subset of AI; systems learn from data without explicit programming
- **DL** — subset of ML; neural networks with many layers; learns features automatically

| Aspect | AI | ML | DL |
|--------|----|----|-----|
| Data needed | Varies | Moderate | Large |
| Feature engineering | Manual | Manual | Automatic |
| Interpretability | Varies | Moderate | Low (black box) |
| Example | Expert systems, robotics | Random Forest, SVM | CNNs, Transformers |

### AI System Lifecycle (6 Stages — Circular)
1. **Problem Framing** — define business problem as ML problem
2. **Data Engineering** — collect, clean, transform data
3. **Model Development** — train, evaluate, select model
4. **Deployment** — serve model in production
5. **Monitoring** — track performance, detect drift
6. **Maintenance** — retrain, update, iterate
→ Circular: monitoring feeds back to problem framing

### 6 Components of an AI System
1. Data pipelines
2. Feature engineering
3. Model training
4. Model serving/inference
5. Monitoring & observability
6. Infrastructure & orchestration

### Model = 5% of Code
- ML model code is only ~5% of a production AI system
- Remaining 95% = data pipelines, feature stores, serving infra, monitoring, configuration, testing
- Key insight: AI engineering is mostly **systems engineering**, not model building

### Why AI Projects Fail
- **85% of AI projects fail** to reach production
- Common reasons: unclear problem framing, poor data quality, lack of MLOps, no monitoring, skill gaps, organizational silos, unrealistic expectations

### Explainable AI (XAI)

| Method | Type | How It Works |
|--------|------|-------------|
| **SHAP** | Model-agnostic | Shapley values from game theory; assigns contribution of each feature |
| **LIME** | Model-agnostic | Locally approximates model with interpretable model around a prediction |

- XAI matters for: trust, debugging, compliance (GDPR right to explanation), fairness audits

### ML Engineering vs Data Science

| Data Science | ML Engineering |
|-------------|---------------|
| Exploratory, research-oriented | Production-oriented |
| Notebooks, prototyping | Pipelines, APIs, CI/CD |
| Focus: model accuracy | Focus: reliability, scalability, latency |
| One-off analysis | Repeatable, automated systems |

### Problem → Solution → ML Framing Pipeline
1. **Business Problem** — "reduce customer churn"
2. **Solution Approach** — "predict which customers will churn next month"
3. **ML Framing** — binary classification, features = usage patterns, label = churned/not

### Three Levels of ML Software (Iceberg)
1. **Top (visible)** — ML algorithm/model code (~5%)
2. **Middle** — ML pipeline code (data processing, training, evaluation)
3. **Bottom (hidden, largest)** — infrastructure (serving, monitoring, data storage, orchestration)

---

## Session 2: Categories of AI Systems

### 6 Categories at a Glance

| Category | Core Task | Key Example |
|----------|-----------|------------|
| Predictive AI | Forecast outcomes from data | Fraud detection, demand forecasting |
| Generative AI | Create new content | ChatGPT, DALL-E, Midjourney |
| Recommender | Suggest relevant items | Netflix, Spotify, Amazon |
| Conversational | Human-like dialogue | Chatbots, voice assistants |
| Computer Vision | Understand images/video | Self-driving, medical imaging |
| Autonomous | Act independently in world | Waymo, drones, robotics |

### Predictive AI — Sub-types

| Type | Output | Example |
|------|--------|---------|
| **Classification** | Category/label | Spam or not spam |
| **Regression** | Continuous value | House price prediction |
| **Time-series** | Future values in sequence | Stock price, demand forecast |
| **Anomaly detection** | Normal vs abnormal | Fraud, network intrusion |
| **Uplift modelling** | Incremental impact of action | "Will this coupon change behavior?" |

### Generative AI — Key Concepts
- **LLMs** — Large Language Models (GPT-4, Claude, Gemini); trained on massive text corpora
- **Diffusion models** — generate images by denoising (Stable Diffusion, DALL-E)
- **Hallucination** — model generates plausible but factually incorrect content
- **Temperature** — controls randomness (0 = deterministic, 1+ = creative/random)
- **RLHF** — Reinforcement Learning from Human Feedback; aligns model to human preferences
- **Tokens** — basic units of text (word fragments); GPT ~1 token ≈ 4 characters
- **Context window** — max tokens model can process at once (e.g., GPT-4: 128K tokens)

### Recommender Systems

| Type | Approach | Limitation |
|------|----------|-----------|
| **Collaborative filtering** | "Users like you also liked..." (user-item interactions) | Cold start for new users/items |
| **Content-based** | Recommend items similar to what user liked (item features) | Limited diversity |
| **Hybrid** | Combine collaborative + content-based | Complex to implement |

- **Cold start problem** — can't recommend when no interaction data exists for new user/item

### Conversational AI — Evolution
1. **Rule-based** — hardcoded if-then rules; brittle
2. **Intent-based** — NLU classifies intent + extracts entities (Dialogflow, Lex)
3. **LLM-powered** — general-purpose language understanding (ChatGPT)
4. **RAG (Retrieval-Augmented Generation)** — LLM + external knowledge base for grounded answers

### Computer Vision — Tasks

| Task | What It Does | Output |
|------|-------------|--------|
| **Classification** | What is in the image? | Label (cat, dog) |
| **Object Detection** | Where are objects? | Bounding boxes + labels |
| **Segmentation** | Pixel-level object boundaries | Pixel masks |

- **CNNs** — Convolutional Neural Networks; standard architecture for vision tasks

### Autonomous Systems
- **SAE Levels of Driving Automation**:

| Level | Name | Description |
|-------|------|-------------|
| 0 | No Automation | Human does everything |
| 1 | Driver Assistance | Steering OR acceleration assist (not both) |
| 2 | Partial | Both steering and acceleration; human monitors |
| 3 | Conditional | System drives; human takes over on request |
| 4 | High | System drives in defined conditions; no human needed |
| 5 | Full | System drives everywhere, all conditions |

- **Sensor fusion** — combining LIDAR + camera + radar + GPS for robust perception
- **Waymo** — Level 4 autonomous taxi service (Alphabet/Google)

---

## Session 3: AI System Architecture

### 5 Layers of AI System Architecture

| Layer | Purpose | Key Tools |
|-------|---------|-----------|
| **Data** | Collect, store, process data | Kafka, Spark, Airflow, dbt |
| **Model** | Train, evaluate, version models | MLflow, Weights & Biases, SageMaker |
| **Application** | Serve predictions, build APIs | FastAPI, TensorFlow Serving, Triton |
| **Infrastructure** | Compute, storage, orchestration | Kubernetes, Docker, Terraform, GPUs |
| **Monitoring** | Track performance, detect issues | Prometheus, Grafana, Evidently, WhyLabs |

### Architecture Walkthrough — Swiggy ETA Example
- **Problem**: Predict delivery time for food orders
- **Data layer**: Real-time order data, restaurant prep times, traffic, weather (Kafka streaming)
- **Feature layer**: Distance, time of day, restaurant load, driver availability
- **Model layer**: Regression model trained on historical deliveries
- **Serving layer**: Real-time inference API (<100ms latency)
- **Monitoring**: Track prediction accuracy, data drift, model staleness

### Zillow Failure — $881M Loss
- **Zillow Offers**: used AI to predict home prices for automated buying/selling
- **What went wrong**: monitoring layer failure
  - Model overestimated home values during market shift
  - No effective drift detection or model retraining triggers
  - Bought homes at inflated prices
- **Result**: $881M write-down, 2,000 layoffs, program shut down
- **Lesson**: monitoring is not optional; models degrade over time

---

## Session 4: AI Ecosystem & Marketplaces

### Foundation Models
- **Definition**: large models trained on broad data; adapted to many downstream tasks
- **Characteristics**: massive scale, self-supervised pre-training, emergent abilities, fine-tunable

| Model | Provider | Type | Notable Trait |
|-------|----------|------|--------------|
| GPT-4 | OpenAI | Proprietary | Multimodal, strongest reasoning |
| Claude | Anthropic | Proprietary | Long context, safety-focused |
| Gemini | Google | Proprietary | Multimodal, integrated with Google |
| LLaMA | Meta | Open-weight | Popular open model family |
| Mistral | Mistral AI | Open-weight | Efficient, strong for size |

### Open-Source Benefits (5)
1. **No API cost** — run on your own infrastructure
2. **Privacy** — data stays in your environment
3. **Customization** — fine-tune for your domain
4. **No vendor lock-in** — switch models freely
5. **Community** — rapid innovation, shared improvements

### Model Hubs

| Hub | Provider | Specialty |
|-----|----------|-----------|
| **Hugging Face** | Independent | Largest open model hub; 500K+ models |
| **AWS Bedrock** | Amazon | Managed API for foundation models |
| **Model Garden** | Google Cloud | Vertex AI model catalog |
| **Azure AI** | Microsoft | OpenAI models + open models |
| **NGC** | NVIDIA | GPU-optimized models and containers |

### API vs Fine-tune vs Build — Decision Framework

| Approach | When to Use | Cost | Effort | Control |
|----------|------------|------|--------|---------|
| **API** | Generic tasks, quick start | Per-call | Low | Low |
| **Fine-tune** | Domain-specific, need customization | Medium | Medium | Medium |
| **Build** | Unique data, full control needed | High | High | Full |

### Build vs Buy Comparison

| Factor | Build | Buy (API) |
|--------|-------|-----------|
| Time to market | Slow (months) | Fast (days) |
| Cost upfront | High (compute, team) | Low (pay-per-use) |
| Customization | Full | Limited |
| Data privacy | Full control | Data sent to provider |
| Maintenance | Your responsibility | Provider handles |
| Expertise needed | High | Low |

### Benchmarks

| Benchmark | Measures |
|-----------|---------|
| **MMLU** | Massive Multitask Language Understanding (broad knowledge) |
| **HumanEval** | Code generation accuracy |
| **MT-Bench** | Multi-turn conversation quality |

### Gateway / Router Pattern
- AI Gateway sits between application and multiple model providers
- Routes requests to optimal model based on cost, latency, task type
- Benefits: fallback, load balancing, cost optimization, unified API

### Enterprise Procurement Scorecard
- Evaluate AI vendors on: accuracy, latency, cost, security, compliance, support, SLAs, data privacy, scalability, vendor stability

### License Terms

| License | Permissions | Restrictions |
|---------|------------|-------------|
| **Apache 2.0** | Commercial use, modify, distribute | Must include license |
| **Llama License** | Research + commercial (with limits) | >700M monthly users need Meta approval |
| **Commercial/Proprietary** | Use via API only | No modification, no self-hosting |

---

## Session 5: Data

### Data Types

| Type | Format | Examples |
|------|--------|---------|
| **Structured** | Tables, rows/columns | SQL databases, CSV |
| **Semi-structured** | Schema embedded in data | JSON, XML, logs |
| **Unstructured** | No predefined format | Images, text, audio, video |

### Internal vs External Data

| Source | Examples |
|--------|---------|
| **Internal** | Transaction logs, CRM, user events, operational DBs |
| **External** | Social media, public APIs, third-party data vendors, open datasets |

### 6 Dimensions of Data Quality
1. **Accuracy** — correct values
2. **Completeness** — no missing data
3. **Consistency** — same data across systems
4. **Timeliness** — up-to-date
5. **Validity** — conforms to format/rules
6. **Uniqueness** — no duplicates

### Data Governance — 7 Components
1. Data ownership
2. Data quality standards
3. Data access policies
4. Data lineage tracking
5. Data cataloging
6. Privacy & compliance
7. Data lifecycle management

### Key Regulations

| Regulation | Scope | Key Points |
|-----------|-------|------------|
| **GDPR** | EU citizens' data | Right to erasure, consent, DPO, fines up to 4% revenue |
| **India DPDP Act (2023)** | Indian citizens' data | Consent-based, data fiduciary obligations, Data Protection Board |

### Data Contracts, Data Products, SLAs
- **Data contract** — formal agreement on schema, quality, freshness, ownership between producer and consumer
- **Data product** — curated, documented, trustworthy dataset treated as a product
- **SLA** — service level agreement on data availability, latency, quality metrics

### Enterprise Data Maturity Model (5 Levels)
1. **Ad hoc** — no formal processes, data in silos
2. **Managed** — basic processes, some documentation
3. **Defined** — standardized processes across organization
4. **Measured** — metrics-driven, quality monitored
5. **Optimized** — continuous improvement, data-driven culture

### Synthetic Data
- Artificially generated data that mimics real data distributions
- Use cases: privacy preservation, augmenting small datasets, testing
- Caution: may not capture real-world edge cases

### Labels as Business Judgments
- Labels encode human decisions and biases
- Labeling = business judgment, not ground truth
- Different labelers → different labels → different models

### Event Data vs State Data

| Type | Description | Example |
|------|-------------|---------|
| **Event data** | Records something that happened (immutable) | "User clicked buy at 3:01 PM" |
| **State data** | Current snapshot (mutable, overwritten) | "User's cart has 3 items" |

### GenAI Governance Challenges
- Hallucination monitoring, IP/copyright concerns, prompt injection attacks
- Data leakage through prompts, bias amplification, lack of provenance
- Need: guardrails, content filtering, audit trails, human-in-the-loop

---

## Session 6: Data Engineering Pipelines

### Data Pipeline
```
Source → Extract → Transform → Load → Destination
```

### ETL vs ELT

| Aspect | ETL | ELT |
|--------|-----|-----|
| Transform | Before loading (staging area) | After loading (in warehouse) |
| Best for | Structured data, legacy systems | Big data, cloud warehouses |
| Tools | Informatica, Talend | dbt, Snowflake, BigQuery |
| Performance | Limited by staging compute | Leverages warehouse compute |

### Batch vs Streaming

| Aspect | Batch | Streaming |
|--------|-------|-----------|
| Latency | Minutes to hours | Milliseconds to seconds |
| Processing | Fixed data chunks | Continuous event flow |
| Tools | Spark, Airflow, Hadoop | Kafka, Flink, Kinesis |
| Use case | Reports, training data | Real-time alerts, fraud detection |

### Lambda Architecture
- **Batch layer** — processes all historical data (complete but slow)
- **Speed layer** — processes real-time data (fast but approximate)
- **Serving layer** — merges batch + speed views for queries
- Drawback: maintaining two separate pipelines (complexity)

### Data Lake vs Data Warehouse vs Data Lakehouse

| Feature | Data Lake | Data Warehouse | Data Lakehouse |
|---------|-----------|---------------|----------------|
| Data type | Raw, all formats | Structured, processed | All formats + structure |
| Schema | Schema-on-read | Schema-on-write | Schema enforcement optional |
| Cost | Low (cheap storage) | High (compute + storage) | Medium |
| Users | Data engineers, scientists | Business analysts | All users |
| Examples | S3, ADLS | Snowflake, Redshift | Databricks, Delta Lake |

### Storage Types

| Type | Description | Use Case |
|------|-------------|----------|
| **File storage** | Hierarchical directories | Shared drives, NFS |
| **Block storage** | Fixed-size blocks; low latency | Databases, VMs (EBS) |
| **Object storage** | Key-value; highly scalable | Data lakes, backups (S3) |
| **Cache** | In-memory; fastest | Real-time features (Redis) |
| **HDFS** | Distributed file system | Hadoop big data workloads |

### Data Validation

| Tool | Provider | Approach |
|------|----------|----------|
| **Deequ** | Amazon | Declarative data quality checks on Spark |
| **TFDV** | Google (TensorFlow) | Schema inference, anomaly detection |

### Data Drift, Schema Skew, Concept Drift

| Issue | Definition |
|-------|-----------|
| **Data drift** | Input data distribution changes over time (feature drift) |
| **Concept drift** | Relationship between features and target changes |
| **Schema skew** | Training and serving data have different schemas or feature types |

### Data Leakage (2 Causes)
1. **Target leakage** — features contain information from the future / derived from target
2. **Train-test contamination** — test data leaks into training set

### Fairness & Bias (5 Types)
1. **Historical bias** — real-world prejudices reflected in data
2. **Representation bias** — underrepresentation of certain groups
3. **Measurement bias** — inconsistent data collection across groups
4. **Aggregation bias** — one model for all subgroups (ignores differences)
5. **Evaluation bias** — benchmark doesn't represent deployment population

### Data Partitioning

| Type | How | Use Case |
|------|-----|----------|
| **Horizontal** | Split by rows | Distribute users across shards |
| **Vertical** | Split by columns | Separate frequently/rarely accessed columns |
| **Functional** | Split by domain/function | Orders DB vs Users DB |

---

## Session 7: Feature Engineering

### What is a Feature?
- A measurable property of data used as model input
- **Feature engineering** = transforming raw data into features that improve model performance
- Often the difference between a mediocre and great model

### Feature Extraction by Data Type

| Data Type | Extraction Methods |
|-----------|-------------------|
| **Structured** | Aggregations, ratios, rolling windows, time-based features |
| **Text** | TF-IDF, bag of words, n-grams, embeddings (BERT, Word2Vec) |
| **Image** | Pixel values, CNN features, edge detection, color histograms |
| **Audio** | MFCCs, spectrograms, pitch, energy |

### Feature Transformation

| Technique | Method | When to Use |
|-----------|--------|-------------|
| **Min-Max Scaling** | (x - min) / (max - min) → [0, 1] | Neural networks, distance-based |
| **Z-Score (Standardization)** | (x - μ) / σ → mean=0, std=1 | Linear models, SVM |
| **Log Transform** | log(x) | Skewed distributions |
| **One-Hot Encoding** | Binary column per category | Nominal categories (color, city) |
| **Label Encoding** | Integer per category | Ordinal categories (low/med/high) |
| **Target Encoding** | Replace category with target mean | High cardinality categories |
| **Missing Values** | Mean/median/mode imputation, flag column | Always handle before training |
| **Binning** | Group continuous values into buckets | Reduce noise, create categories |

### Feature Selection Methods

| Method | Approach | Examples |
|--------|----------|---------|
| **Filter** | Statistical tests, independent of model | Correlation, chi-squared, mutual info |
| **Wrapper** | Use model performance to select | Forward selection, backward elimination, RFE |
| **Embedded** | Feature selection built into model training | Lasso (L1), tree-based feature importance |

### Embeddings
- Dense vector representations that capture semantic meaning
- **Word embeddings** — Word2Vec, GloVe (similar words → close vectors)
- **Sentence embeddings** — BERT, Sentence-BERT (similar sentences → close vectors)
- **Image embeddings** — CNN feature maps (similar images → close vectors)
- **User/Product embeddings** — learned from interaction data (recommendations)
- Key: similar items have similar embeddings (measured by cosine similarity)

### Feature Design Examples

| Domain | Example Features |
|--------|-----------------|
| **Fraud detection** | Transaction amount deviation, frequency last 1hr, location mismatch, merchant risk score |
| **Recommendations** | User embedding, item embedding, interaction history, popularity score, time since last visit |
| **Delivery ETA** | Distance, time of day, traffic conditions, restaurant prep time, driver rating, weather |

---

## Session 8: Feature Stores

### What is a Feature Store?
- Centralized platform to store, manage, serve, and reuse ML features
- Single source of truth for features across training and serving

### 5 Problems Solved
1. **Feature reuse** — avoid re-engineering same features across teams
2. **Training-serving consistency** — same features in training and production
3. **Feature discovery** — catalog of available features with metadata
4. **Point-in-time correctness** — prevent data leakage in historical features
5. **Low-latency serving** — precomputed features for real-time inference

### Offline Store vs Online Store

| Feature | Offline Store | Online Store |
|---------|--------------|-------------|
| Purpose | Training data, batch scoring | Real-time inference |
| Latency | Seconds to minutes | Milliseconds (p99 < 10ms) |
| Storage | Data warehouse, data lake | Key-value store (Redis, DynamoDB) |
| Data volume | Months/years of history | Latest feature values |
| Access pattern | Batch read (full table scan) | Point lookup by entity key |

### Feature Pipelines

| Pipeline | Frequency | Source | Use Case |
|----------|-----------|--------|----------|
| **Batch** | Hourly/daily/weekly | Data warehouse | User lifetime value, 30-day averages |
| **Streaming** | Real-time | Kafka, Kinesis | Last-5-min transaction count, live location |

### Training-Serving Skew (4 Types)

| Type | Cause |
|------|-------|
| **Schema skew** | Different data schema in training vs serving |
| **Feature skew** | Different feature computation logic |
| **Distribution skew** | Serving data distribution differs from training |
| **Temporal skew** | Stale features served (not updated in time) |

- Training-serving skew = silent model performance degradation

### Two-Stage Recommendation System
1. **Candidate Generation** — narrow millions of items to ~100 candidates
   - Uses **ANN (Approximate Nearest Neighbor)** search on embeddings
   - Fast but approximate (trade accuracy for speed)
2. **Ranking** — score ~100 candidates with complex model, return top-K
   - Uses rich features from feature store
   - Slower but more accurate

### Zillow Case Study (Training-Serving Skew)
- Pricing model trained on historical data (stable market)
- Served in volatile market (COVID-era price swings)
- **Distribution skew**: serving data distribution ≠ training distribution
- No drift detection or retraining triggers
- Result: systematically overpriced home purchases → $881M loss

### Popular Feature Store Tools

| Tool | Type | Strengths |
|------|------|-----------|
| **Feast** | Open-source | Lightweight, cloud-agnostic, good for getting started |
| **Tecton** | Managed (commercial) | Real-time features, enterprise-grade, built by Feast creators |
| **Hopsworks** | Open-source + managed | Feature pipelines, great for streaming features |

---

## Quick-Fire Numbers & Key Facts

| Fact | Value |
|------|-------|
| AI project failure rate | **85%** |
| Model code in production AI system | **~5%** |
| Zillow loss | **$881M** |
| GPT-4 context window | **128K tokens** |
| 1 token ≈ | **4 characters** |
| SAE Level 4 | High automation (no human needed in defined conditions) |
| S3 durability | 11 nines |
| Data quality dimensions | 6 (accuracy, completeness, consistency, timeliness, validity, uniqueness) |
| Data governance components | 7 |
| Data maturity levels | 5 |
| Bias types | 5 (historical, representation, measurement, aggregation, evaluation) |
| Training-serving skew types | 4 (schema, feature, distribution, temporal) |
| Feature selection methods | 3 (filter, wrapper, embedded) |
| Recommendation stages | 2 (candidate generation → ranking) |

---

## Key Comparisons to Remember
- **ETL** = transform first, load second | **ELT** = load first, transform in warehouse
- **Batch** = high latency, high throughput | **Streaming** = low latency, continuous
- **Data Lake** = raw, cheap, schema-on-read | **Warehouse** = structured, schema-on-write
- **Offline store** = training (batch) | **Online store** = serving (real-time)
- **Filter** = independent of model | **Wrapper** = uses model | **Embedded** = during training
- **SHAP** = global + local explanations | **LIME** = local explanations only
- **Collaborative filtering** = user behavior | **Content-based** = item features

---

*Good luck with the exam!*
