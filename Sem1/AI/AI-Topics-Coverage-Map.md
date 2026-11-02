# AI Systems — Topics Coverage Map (All 8 Sessions)

> BITS Pilani — **SS ZG662: Introduction to AI Systems**
> Quick reference showing what each session covers — use this to check you haven't missed any topic.

---

## Session 1: Foundations of AI Systems (71 slides)

| Section | Topics Covered |
|---|---|
| **AI vs ML vs DL** | Nested relationship, comparison table (6 dimensions), when to use what, enterprise context (banking/logistics/legal/search/manufacturing) |
| **Problem → Solution → ML Framing** | 3-step framing process, rule-based vs ML decision tree, when rules suffice vs ML needed, ML problem framing 8-step checklist |
| **Why AI Projects Fail** | 85% failure rate, 7 reasons, "Should we use AI?" checklist, failed vs successful project examples |
| **Model = 20% / System = 80%** | Credit risk prediction example, data integration, compliance, monitoring, workflow integration |
| **Three Levels of ML Software** | Iceberg metaphor: ML Code (5%) → ML Infrastructure (35%) → ML Operations (60%), Data/Model/Code as 3 assets |
| **Explainable AI (XAI)** | SHAP, LIME, Attention Visualization, interpretability vs performance trade-off, bank loan rejection example |
| **ML Engineering** | Data Scientist vs ML Engineer comparison, what ML Engineers build, workflow |
| **AI System Lifecycle** | 6 stages (circular), business goal → data → model → deploy → monitor → iterate, Netflix example |
| **Business Goal Phase** | 13 steps: requirements, feasibility, cost evaluation, POC, external data, pathways to production |
| **ML Lifecycle Architecture** | 12 components: ingestion, validation, transform, feature store, training, evaluation, validation, registry, serving, monitoring, ground truth, metadata |
| **Architecture Support** | Drift Feedback Loop, Alarm Manager, Scheduler, Lineage Tracker |
| **Data Engineering** | Gartner definition ("iterative and agile process"), data prep as most expensive step, propagation of errors |
| **Data Collection** | Label/Ingest/Aggregate, data sources (time-series, IoT, sensors, social networks), batch vs streaming ingestion, synthetic data generation, data enrichment |
| **Data Exploration** | Data profiling, metadata (min/max/avg/missing counts/unique values), understanding content and structure |
| **Data Validation** | Schema validation, range checks, null checks, distribution checks, referential integrity, automated error detection |
| **Data Wrangling** | Re-formatting attributes, correcting errors, missing values imputation |
| **Data Labelling** | Assigning categories for supervised learning, Label Studio, Figure Eight, Google Data Labeling Service |
| **Data Splitting** | Training (70-80%) / Validation (10-15%) / Test (10-15%), avoiding data leakage |
| **4 Functions of Data** | Defining goal, training algorithm, measuring performance, building monitoring baselines, same processing for training AND inference |
| **Data from Vendors** | Missing values, duplicates, schema mismatches, outliers, formats (ZIP/XML/CSV/JSON), ETL → data lake |
| **EDA** | Histograms, scatter plots, box plots, correlation heatmaps, missing value heatmaps, wrangler tools (AWS DataWrangler, Trifacta, OpenRefine) |
| **Data Preprocessing** | 8 strategies: clean, balance, replace, impute, partition, scale, augment, unbias |
| **Cleaning** | Remove outliers/duplicates, imputation methods (mean/median/mode/forward fill/model-based/drop) |
| **Partitioning** | Train/val/test split, leakage prevention (split first then preprocess), stratified/time-based sampling |
| **Scaling** | Normalize (min-max, 0-1 range) vs Standardize (z-score, mean=0 std=1), when each is better, algorithms that need scaling (K-Means, KNN, PCA, gradient descent) |
| **Unbias/Balance** | Class imbalance, representation bias, measurement bias, historical bias, SMOTE, class weights, Amazon hiring example |
| **Augmentation** | Image (rotation, flipping, cropping, color jitter), Text (synonym replacement, back-translation), Audio (speed perturbation) |
| **Feature Engineering** | 4 sub-steps: Creation (one-hot, binning, splitting, calculated), Transformation (Cartesian products, non-linear, domain-specific), Extraction (PCA, ICA, LDA), Selection (filter/wrapper/embedded) |
| **Model Training** | Algorithm selection, objective metric (loss function), hyperparameters (learning rate, epochs, batch size, max depth, regularization) |
| **Hidden Patterns** | Mapping function from inputs → target, correlation threshold (>60% → drop), dimensionality reduction (400 features → 50) |
| **Debugging/Profiling** | Loss not decreasing, loss exploding, overfitting, underfitting, GPU underutilization, memory overflow |
| **Model Evaluation** | Offline: hold-out test, k-fold cross-validation. Online: A/B testing, canary deployment, shadow mode |
| **Model Validation** | Generalization check, performance vs baseline, fairness check, robustness check |
| **Model Testing** | Latency testing (P99), throughput testing, infrastructure testing, operational testing |
| **Model Selection** | 6 criteria: accuracy, latency, model size, maintenance cost, explainability, business alignment → model registry |
| **Distributed Training** | Model parallelism (split model across GPUs), Data parallelism (split data into mini-batches across nodes) |
| **Imbalanced Data** | 85K no / 15K yes example, logistics regression, ensemble models (RF, XGB), model selection |
| **Model Deployment** | Real-time endpoints (API), batch transform, edge deployment, model serving (load balancing, auto-scaling, versioning, failover) |
| **Deployment Pipeline** | QA (unit testing) → Staging → UAT (User Acceptance Testing) → Production |
| **Retraining Triggers** | Scheduled (CI/CD pipeline at 10AM/4PM/10PM) and Event-based (data provider uploads → triggers retraining) |
| **Model Monitoring** | 6 areas: data ingestion issues, data drift, model degradation, concept drift, bias/fairness, feature attribution drift |
| **Performance Monitoring** | ML-specific signals (prediction deviation), triggers for re-training, dashboards, alert levels (warning/critical/emergency) |
| **Performance Logging** | Every inference request → log record (input features, prediction, timestamp, latency, model version) |
| **Rollback Strategy** | Automatic rollback if accuracy drops >10% or latency exceeds SLA |
| **9 ML System Components** | Ground-Truth Collector, Data Labeller, Evaluator, Performance Monitor, Featurizer (batch + real-time), Orchestrator (ETL→featurize→train→deploy), Model Builder, Model Server, Front-End |
| **Ground-Truth Collector** | Collects correct answers (sale price, churn event, spam label), delayed ground truth problem |
| **Data Labeller** | Manual labelling tools: Label Studio, Figure Eight/Appen, Google Data Labeling, SageMaker Ground Truth |
| **Evaluator** | Prediction accuracy + business impact + system metrics (lag, throughput), 2 objectives: comparing models + deployment safety |
| **Performance Monitor** | Production data → database → evaluator → dashboard showing performance evolution over time |
| **Featurizer** | customer_id → full feature vector, batch featurization (pre-computed) vs real-time featurization ("hot features"), microservice pattern |
| **Orchestrator** | ETL+split → featurize → prepare → model builder → evaluate → deploy, automation, parallelization, ML platforms (Kubeflow, SageMaker) |
| **AI Applications** | Healthcare (DeepMind), Finance (PayPal), E-commerce (Amazon 35%), Manufacturing (Siemens), Transport (Waymo), Customer Service (Bank of America Erica), Education (Khan Academy), Agriculture (John Deere), Legal (JPMorgan COIN), Entertainment (Spotify), Telecom (Jio) |

---

## Session 2: Categories of AI Systems (58 slides)

| Section | Topics Covered |
|---|---|
| **Choosing the Right AI** | Decision framework (5 steps), quick selection guide (6 categories), six AI categories comparative table |
| **Predictive AI** | 4 prediction types (classification, regression, time-series, anomaly detection), classification vs regression business comparison |
| **Uplift Modelling** | 4 groups (Persuadables, Sure Things, Lost Causes, Sleeping Dogs), Amazon/Jio examples |
| **Predictive Metrics** | Model metrics (Accuracy, Precision, Recall, F1, AUC-ROC), Business KPIs (revenue uplift, cost saved, retention rate, false positive cost) |
| **Deployment Challenges** | Data drift, concept drift, feature availability, latency, A/B testing, shadow deployment |
| **When to Use / Not** | Historical data, patterns exist, measurable outcome vs no data, constant change, simple rules |
| **Generative AI** | 5 types (text/image/code/music/video), LLM token-by-token generation, key concepts (prompt, hallucination, temperature, RLHF, tokens, context window) |
| **ChatGPT Case Study** | 3 steps: pre-training → RLHF → serving, system beyond model (safety, rate limiting, A/B testing) |
| **GenAI Enterprise Workflow** | Business need → select model → prompt/fine-tune → integrate data → safety guardrails → human review → test → deploy → monitor |
| **RAG vs Fine-Tune vs Prompting** | Comparison table (when/data/cost/time/best for), HR Policy Assistant RAG example |
| **Productivity vs Automation** | Copilot (AI assists) vs Autopilot (AI acts alone), 90%+ is productivity mode |
| **Hallucination/Bias/Data Risks** | Confident wrong answers, fake citations, bias reproduction, proprietary data leakage, copyright |
| **GenAI Enterprise Functions** | Marketing, Customer Support, Engineering, Legal, HR, Finance — with examples |
| **Enterprise Implementation** | Data privacy, cost management, hallucination mitigation, governance, workflow integration |
| **Recommender Systems** | 3 types (collaborative/content-based/hybrid), cold start problem (new user/new item solutions) |
| **Recommendation Architecture** | Candidate Generation → Ranking → Top-N → Business Rules → Display pipeline |
| **Recommendation Metrics** | CTR, Conversion Rate, NDCG, Revenue per User, Coverage, Diversity |
| **Netflix Case Study** | Data signals, multiple models, personalised thumbnails, offline+online, personalisation at every touchpoint, ranking model, feedback loop |
| **Netflix Business Value** | 80% from recommendations, $1B/year retention savings, challenges (filter bubble, popularity bias, cold start) |
| **Conversational AI** | 4 types (rule-based → intent-based → LLM → RAG), RAG flow (retrieve → augment → generate) |
| **LLM vs Traditional Chatbots** | 8-dimension comparison table |
| **Enterprise Conv AI Architecture** | Channel Adapter → NLU Engine → Dialog Manager → Knowledge Base → Response Generator → Channel Adapter |
| **Risks/Escalation/HITL** | Hallucination, inappropriate responses, data leakage, prompt injection, escalation triggers, 3 HITL patterns |
| **Customer Service vs Employee Support** | External (customer-facing) vs Internal (employee-facing) comparison |
| **Computer Vision** | 6 tasks (classification, detection, segmentation, face recognition, OCR, image generation), CNN layer-by-layer |
| **CV Enterprise Pipeline** | Data Collection → Annotation → Training → Validation → Deployment → Monitoring, edge deployment |
| **Manufacturing/Quality** | Defect detection, Tata Motors/Asian Paints/Amul examples, ROI (60% cost reduction, 99.5% accuracy) |
| **Retail/Healthcare/Logistics** | Shelf monitoring, Amazon Go, X-ray analysis, pathology, package sorting, warehouse robots |
| **CV Deployment Challenges** | Lighting, camera angle, class imbalance, edge computing, privacy, annotation cost |
| **Autonomous Systems** | Sense→Think→Act loop, SAE Levels 0-5, Decision-Making/Planning/Control (<100ms) |
| **Waymo Case Study** | 29 cameras, 4 LiDAR, 6 radar, GPS+IMU, sensor fusion, 20M+ autonomous miles |
| **Three Case Studies Summary** | ChatGPT (RLHF+safety), Netflix (multiple models+personalisation), Waymo (sensor fusion+safety engineering) |

---

## Session 3: AI System Architecture (no class slides — content from handout)

| Section | Topics Covered |
|---|---|
| **5-Layer Architecture** | Data Layer, Model Layer, Application Layer, Infrastructure Layer, Monitoring Layer |
| **Data Layer** | Ingestion (Kafka, Kinesis), Storage (S3, BigQuery), Processing (Spark, dbt, Airflow), Feature Store (Feast, Tecton), Data Quality (Great Expectations, Deequ) |
| **Model Layer** | Experiment Tracking (MLflow, W&B), Training Pipeline (Kubeflow, SageMaker), Model Registry, Model Serving (TensorFlow Serving, Triton), Evaluation |
| **Application Layer** | API Gateway, Business Logic, A/B Testing, User Interface, Feedback Collection |
| **Infrastructure Layer** | Compute (GPU/TPU), Container Orchestration (Kubernetes), Storage (S3), CI/CD, Infrastructure as Code (Terraform) |
| **Monitoring Layer** | Model performance, data drift, prediction drift, system health, business metrics |
| **Architecture Walkthrough** | Swiggy ETA prediction — traced through all 5 layers step by step |
| **Zillow Case Study** | $881M loss from monitoring failure, data drift from COVID market changes |

---

## Session 4: AI Ecosystem & Model Marketplaces (57 slides)

| Section | Topics Covered |
|---|---|
| **Foundation Models** | Definition, 6 characteristics (massive data, general-purpose, transfer learning, emergent abilities, expensive, accessible), notable models table (GPT-4, Claude, Gemini, LLaMA, Mistral, Stable Diffusion, Whisper) |
| **Multi-Layer Ecosystem** | Foundation Model Providers → Cloud Platforms → Model Hubs → Application Builders |
| **Model-Selection Question** | "Which model, deployed where, at what cost, with what governance, and what exit strategy?" 7 selection factors |
| **Model Discovery Channels** | Hugging Face, AWS Bedrock, Google Model Garden, Azure Model Catalog, NVIDIA NGC — comparison table |
| **Open-Source Models** | 5 benefits (no API cost, privacy, customization, no lock-in, community), Krutrim example |
| **Model Families** | Proprietary vs Open shortlist, GPT vs Claude vs Gemini comparison, LLaMA vs Mistral vs Qwen vs DeepSeek comparison |
| **Small/Task-Specific Models** | Size tiers (1B-100B+), why small models often win, BERT vs GPT-4 for sentiment |
| **Open-Weight Variants** | Original/fine-tuned/quantized/distilled versions, "LLaMA is not a procurement spec" |
| **Same Model Different Channels** | Direct download vs Managed API vs Cloud-hosted — pricing/SLA/compliance comparison |
| **License Terms** | Apache 2.0 (most free) vs Llama License (>700M MAU restricted) vs Commercial vs GPL |
| **API vs Open Model** | Direct comparison table (7 dimensions), pricing examples (Rs/1K tokens) |
| **AWS Bedrock** | Managed service, abstraction layer, model switching without code changes, Bedrock Marketplace |
| **Google Model Garden** | Vertex AI integration, one-click deployment, fine-tuning |
| **Azure Model Catalog** | Only way for enterprise-compliant GPT-4, Azure AI Studio |
| **NVIDIA NGC** | GPU-optimized models/containers, 2-5x faster on NVIDIA hardware |
| **Inference-as-a-Service** | Fireworks AI, Together AI, Groq (LPU hardware), Replicate |
| **Benchmarks** | MMLU (57 subjects), HumanEval (code), MT-Bench (conversation), GSM8K (math), TruthfulQA, warning about benchmark limitations |
| **Quality Scorecard** | Accuracy on YOUR data, latency (p50/p95/p99), throughput, cost per request, error rate, evaluate beyond output |
| **Total AI Cost** | Worked example: 1M requests — API ($30K) vs Managed Open ($8K) vs Self-Hosted ($8K incl. engineering) |
| **When to Self-Host** | 4 conditions: data privacy, cost at scale, custom modifications, regulatory requirements |
| **Hidden Costs** | GPU procurement, MLOps team, monitoring infra, security patching, model updates, on-call rotation |
| **Gateway/Router Pattern** | Route simple queries → cheap model, complex → expensive model, 78% cost reduction example |
| **Prompting vs RAG vs Fine-Tuning** | Comparison table (when/data/cost/time/best for), decision flow |
| **Build vs Buy** | Decision framework, comparison table (API vs Fine-tune vs Build), real-world examples (Razorpay, hospital, Swiggy, Google) |
| **Enterprise Procurement Scorecard** | Weighted scoring (Capability 30%, Cost 25%, Latency 15%, Privacy 15%, Support 10%, Lock-in 5%), worked calculation |
| **Enterprise Model Lifecycle** | Select → Integrate → Monitor → Evaluate → Replace/Upgrade, version control for prompts/data/models |

---

## Session 5: Data — Types, Quality, Governance (44 slides)

| Section | Topics Covered |
|---|---|
| **3 Types of Data** | Structured (SQL, 20%), Semi-structured (JSON, 10%), Unstructured (images/text, 70%), comparison table |
| **Hidden Structure** | Even unstructured data has structure (EXIF in images, headers in emails), Naukri.com example |
| **Temporal Data** | Point-in-time correctness, no future leakage, temporal train/test split, HDFC Bank loan example |
| **Event vs State Data** | Event (immutable, append-only, what HAPPENED) vs State (mutable, what IS now), Paytm example |
| **Data Sources** | Internal (databases, logs, CRM, warehouse, UGC, IoT) + External (public datasets, APIs, third-party, web scraping, pre-trained model outputs) |
| **Human-Generated Data** | Inter-annotator disagreement, Cohen's kappa (κ), Flipkart fashion labelling example |
| **Synthetic Data** | GANs, LLMs, simulation, rule-based, Aadhaar verification example, risks (bias amplification) |
| **Labels as Business Judgments** | Definition affects model (churn: 30/60/90 days), Hotstar/JioCinema example |
| **6 Data Quality Dimensions** | Accuracy, Completeness, Consistency, Timeliness, Validity, Uniqueness |
| **Common Quality Issues** | Missing values, outliers, duplicates, label noise, data leakage, class imbalance, schema drift |
| **Credit Scoring Example** | 4 issues: missing employment, data leakage (credit score), sampling bias, class imbalance |
| **Quality by Population Slice** | Aggregated metrics hide disparities, health-tech regional language example |
| **Missingness Is Data** | MCAR/MAR/MNAR types, "is_missing" indicator features, CIBIL example |
| **Data Governance** | 7 components (ownership, access control, lineage, cataloging, privacy/compliance, retention, quality standards) |
| **Centralized vs Federated** | Centralized (consistent but bottleneck), Federated (fast but inconsistent), Data Mesh (hybrid), Razorpay example |
| **Data Contracts** | Schema, freshness SLA, quality thresholds, ownership, access policies, change management, YAML example |
| **AI Data Contracts** | Traditional + label quality, distribution stability, bias metrics, drift thresholds, training/serving consistency |
| **Contract Enforcement** | Automated validation at pipeline boundaries |
| **AI Data Classification** | Public/Internal/Confidential/Restricted + AI usage categories (training/evaluation/production/feedback) |
| **GenAI Governance** | Training data copyright, generated content ownership, prompt injection, hallucination governance |
| **Data Incidents** | Source outage, schema change, corruption, late arrival, silent failure, blast radius mapping |
| **GDPR + DPDP Act** | Consent, right to deletion, DPO, penalties (4% revenue / Rs 250 crore) |
| **Cambridge Analytica** | 87M users, $5B fine, no access control/audit trail/consent verification |
| **Enterprise DM vs ML-Ready DM** | Gap: enterprise focuses on reporting, ML needs versioning/lineage/features/train-serve consistency, ICICI Bank example |
| **Data Maturity Model** | 5 levels: Ad-hoc → Managed → Defined → Quantified → Optimized, Indian companies mapped |
| **Traditional vs Modern Architecture** | OLTP→ETL→Warehouse→BI vs Streams+S3→ELT→Lakehouse→ML+BI, Zerodha example |
| **Data Products** | Datasets as products with owner/docs/quality/versioning/SLA, PhonePe example |
| **AI Data Readiness** | 9-area assessment checklist with scoring |
| **90-Day Transformation Roadmap** | Days 1-30 (foundation), 31-60 (infrastructure), 61-90 (first model to production), Lenskart example |
| **Target Architecture** | Unified data platform + Feature Store + Model Registry + ML Pipelines + Monitoring + Governance |

---

## Session 6: Data Engineering Pipelines (80 slides)

| Section | Topics Covered |
|---|---|
| **Data Pipeline** | Definition, Source→Extract→Transform→Load structure, Swiggy pipeline example |
| **ETL vs ELT** | Side-by-side comparison (7 dimensions), PhonePe migration example, industry trend toward ELT |
| **Batch Processing** | Scheduled chunks, tools (Spark, Airflow, dbt, Hadoop) |
| **Streaming Processing** | Real-time per event, tools (Kafka, Flink, Kinesis) |
| **Batch vs Streaming** | 7-dimension comparison, company examples (Netflix, Uber, HDFC, Flipkart, Zomato) |
| **Lambda Architecture** | Batch + Streaming together, Swiggy example |
| **Batch Ingestion** | Snapshot vs Differential extraction, file formats (CSV/JSON/Parquet/Avro/ORC), batch size optimization, data migration, schema evolution, late-arriving data |
| **Streaming Concepts** | Ordering, delivery guarantees (at-most/at-least/exactly-once), replay, message size, TTL, dead-letter queues, consumer pull vs push |
| **Data Ingestion Tools** | Ingestion vs Integration, 5 challenges (volume/velocity/variety/quality/schema), tool categories (batch/streaming/managed/CDC) |
| **Storage Types** | Raw ingredients (HDD/SSD/RAM/Network), File (NAS/NFS), Block (EBS/SAN), Object (S3/GCS), Cache (Redis/Memcached), HDFS, Streaming (Kafka), single vs distributed comparison |
| **Storage Abstractions** | Data Lake (any data, cheap, schema-on-read) vs Warehouse (structured, fast, schema-on-write) vs Lakehouse (best of both) |
| **Data Swamp** | Lake without governance, well-managed vs poorly managed examples |
| **Enterprise Pipeline Case Study** | Myntra recommendation pipeline — full architecture with tools for each component |
| **Data Validation** | Unit-test approach, Amazon Deequ (constraint-based), Google TFDV (schema-based), comparison table |
| **Data Drift & Skew** | Data drift, schema skew, distribution skew, concept drift, training-serving skew, detection methods |
| **Data Leakage** | Feature hiding target, feature from future, detection checklist |
| **Fairness & Bias** | 5 types: reporting, automation, selection, group attribution, implicit, identification signals, mitigation |
| **Data Partitioning** | Horizontal (sharding), Vertical (columns), Functional (by business domain), rebalancing strategies |

---

## Session 7: Feature Engineering (content from handout + textbook)

| Section | Topics Covered |
|---|---|
| **What is Feature Engineering** | Definition, "Applied ML is feature engineering" (Andrew Ng), raw data vs features example |
| **Feature Extraction** | From structured (aggregation, ratio, date), text (BoW, TF-IDF, word embeddings, sentence embeddings), image (CNN features, object detection), audio (MFCCs, speech-to-text) |
| **Feature Transformation** | Scaling (min-max, z-score, log), encoding (one-hot, label, target, embedding), missing values (5 imputation methods), binning, interaction features |
| **Feature Selection** | Filter (correlation, chi-squared, mutual info), Wrapper (forward/backward/RFE), Embedded (L1/Lasso, tree-based importance) |
| **Why Not All Features** | Overfitting, curse of dimensionality, slower training, harder to explain |
| **Embeddings** | Word (Word2Vec), Sentence (BERT), Image (ResNet/CLIP), User, Product embeddings, "king - man + woman ≈ queen" |
| **Feature Design Examples** | Fraud detection (HDFC Bank, 20+ features), Product recommendation (Amazon, user+product+interaction), Delivery ETA (Swiggy, restaurant+delivery+order+time features) |

---

## Session 8: Feature Stores (content from handout + textbook)

| Section | Topics Covered |
|---|---|
| **What is a Feature Store** | Definition, 5 problems solved (skew, duplication, slow serving, no discovery, no versioning) |
| **Architecture** | Offline Store + Online Store + Feature Registry + Feature Pipelines |
| **Uber Michelangelo** | 10,000+ features, serves ETA/surge/fraud/matching/restaurant models, any team can browse/reuse |
| **Offline vs Online Features** | 6-dimension comparison (when computed, represents, latency, storage, used for, size), Swiggy example combining both |
| **Batch Feature Pipeline** | Runs on schedule, Spark/SQL/Airflow, Flipkart nightly example |
| **Streaming Feature Pipeline** | Runs 24/7, Kafka/Flink, HDFC fraud detection example |
| **Training-Serving Skew** | 4 types: feature computation, data distribution, feature availability, time-travel |
| **Zillow Case Study** | Training-serving skew caused overestimation, $881M loss |
| **Prevention Best Practices** | Use feature store, point-in-time correctness, feature validation, share code, monitor distributions |
| **Two-Stage Recommendation** | Candidate Generation (ANN search, fast) → Ranking (detailed scoring, accurate), why two stages |
| **ANN Search** | Embedding similarity, FAISS (Facebook), ScaNN (Google), Pinecone, Weaviate |
| **Real-Time Recommendation Flow** | User opens app → feature store → candidate gen → ranking → business logic → display (all in <100ms) |
| **Spotify Discover Weekly** | Batch pipeline (Monday 3AM: user embeddings, collaborative filtering, ranking, diversity) + Real-time feedback (skip/like signals) |
| **Feature Store Tools** | Feast (open-source), Tecton (commercial), Hopsworks, SageMaker Feature Store, Vertex AI Feature Store |

---

## Quick Reference: What's in Each Session

| Session | Module | One-Line Summary |
|---|---|---|
| **S1** | Foundations | AI/ML/DL basics, lifecycle, architecture, data engineering, model development, deployment, monitoring |
| **S2** | Foundations | 6 AI categories with case studies (ChatGPT, Netflix, Waymo) |
| **S3** | Foundations | 5-layer system architecture (Data/Model/App/Infra/Monitoring) |
| **S4** | Foundations | Model marketplaces, foundation models, build vs buy, enterprise procurement |
| **S5** | Data & Feature Eng | Data types, quality, governance, maturity, contracts, bias |
| **S6** | Data & Feature Eng | Pipelines (ETL/ELT, batch/streaming), storage, validation, drift, partitioning |
| **S7** | Data & Feature Eng | Feature extraction, transformation, selection, embeddings |
| **S8** | Data & Feature Eng | Feature stores, offline/online, training-serving skew, real-time recommendations |

---

*Use this file to quickly check if you've studied every topic before the exam.*
