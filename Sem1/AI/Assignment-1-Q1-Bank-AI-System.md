# Assignment 1 — Q1: AI-Powered Relationship Manager for a Large Bank

> **Course:** SS ZG662 — Introduction to AI Systems (BITS Pilani)

---

## Question Summary

A large bank wants to build an AI-powered relationship manager that can:
1. Predict customers likely to leave (churn)
2. Recommend products
3. Summarize customer interactions
4. Answer employee questions
5. Generate customer communication
6. Detect suspicious activity
7. Retrieve information from internal documents

**Available Data:** CRM + Transactions + Emails + Call Transcripts + Documents + Web Activity + Real-time Transactions

---

## A. AI Categories

Each component maps to one or more of the **six AI categories** covered in the course:

| # | Component | Primary AI Category | Secondary Category | Justification |
|---|-----------|--------------------|--------------------|---------------|
| 1 | Predict customers likely to leave | **Predictive AI — Classification** | — | Binary classification (churn / no-churn). Supervised learning on historical labelled data. |
| 2 | Recommend products | **Recommender System** | Predictive AI | Hybrid recommender: collaborative filtering (similar customers bought X) + content-based (product features match customer profile). |
| 3 | Summarize customer interactions | **Generative AI (NLP)** | — | Abstractive summarization using LLMs. Input = call transcripts + emails → Output = concise summary. |
| 4 | Answer employee questions | **Conversational AI** | Generative AI + RAG | Employee-facing chatbot using RAG (Retrieval-Augmented Generation) over internal knowledge base. |
| 5 | Generate customer communication | **Generative AI** | NLP | LLM generates personalized emails, SMS, and letters. Tone/compliance guardrails required. |
| 6 | Detect suspicious activity | **Predictive AI — Anomaly Detection** | — | Flags outlier transactions deviating from learned "normal" pattern. Isolation Forest / Autoencoders on real-time streams. |
| 7 | Retrieve information from internal documents | **Conversational AI + RAG** | NLP | Semantic search over document corpus → retrieve relevant chunks → generate answer. Not pure search — it synthesizes answers. |

### Key Insight
This system is **multi-modal** — it spans 4 of the 6 AI categories (Predictive, Generative, Recommender, Conversational). No single model can do everything; each component needs its own model strategy.

---

## B. Data Architecture

### B1. Data Sources and Ingestion

| Data Source | Type | Ingestion Mode | Format | Volume |
|-------------|------|---------------|--------|--------|
| CRM | Structured | Batch (daily) | Relational DB | Medium |
| Transactions | Structured | **Streaming** (real-time) + Batch | Event logs | Very High |
| Emails | Unstructured (text) | Batch (hourly) | Text/HTML | High |
| Call Transcripts | Unstructured (text) | Batch (after call) | Text/Audio | High |
| Internal Documents | Unstructured | Batch (on change) | PDF/DOCX | Medium |
| Web Activity | Semi-structured | Streaming (clickstream) | JSON event logs | Very High |
| Real-time Transactions | Structured | **Streaming** | Event stream | Very High |

### B2. Batch Pipeline

```
[CRM] ──────────┐
[Transactions]───┤                    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
[Emails]─────────┼──► ETL/ELT ──────►│  Data Lake    │───►│  Feature     │───►│  Data        │
[Call Transcripts]│   (Daily/Hourly)  │  (S3 / GCS)  │    │  Engineering │    │  Warehouse   │
[Documents]──────┤                    │  Raw Zone     │    │  Pipeline    │    │  (Snowflake/ │
[Web Activity]───┘                    └──────────────┘    └──────────────┘    │   BigQuery)  │
                                                                              └──────┬───────┘
                                                                                     │
                                                                              ┌──────▼───────┐
                                                                              │ Feature Store │
                                                                              │ (Offline)     │
                                                                              │ Feast/Tecton  │
                                                                              └──────────────┘
```

**Steps in Batch Pipeline:**
1. **Extract** — Pull data from source systems (CRM APIs, database replication, S3 drops)
2. **Validate** — Schema validation, null checks, range checks, distribution checks
3. **Clean** — Deduplicate, handle missing values, standardize formats
4. **Transform** — Feature engineering (aggregations, ratios, embeddings)
5. **Load** — Write to Feature Store (offline) and Data Warehouse

### B3. Streaming Pipeline

```
[Real-time Transactions] ──► Kafka / Kinesis ──► Stream Processor ──► Feature Store (Online)
[Web Clickstream] ─────────►                     (Flink / Spark       ──► Anomaly Detection
                                                  Streaming)               Model (Real-time)
```

**Steps in Streaming Pipeline:**
1. **Ingest** — Real-time events arrive via Kafka/Kinesis
2. **Process** — Compute streaming features (transaction velocity, session behaviour)
3. **Serve** — Write to online feature store (Redis/DynamoDB) for low-latency serving
4. **Alert** — Trigger anomaly detection model in real-time (<100ms)

### B4. Architecture Pattern: Lambda Architecture

The system uses **Lambda Architecture** — both batch and streaming pipelines running in parallel:
- **Batch layer** — Accuracy. Daily recompute of all features. Used for model training and offline analytics.
- **Speed layer** — Freshness. Real-time feature computation. Used for fraud detection and live recommendations.
- **Serving layer** — Feature Store merges batch + streaming features for model inference.

### B5. Text Data Pipeline (Emails, Transcripts, Documents)

```
[Raw Text] ──► OCR/Parser ──► Text Cleaning ──► Chunking ──► Embedding ──► Vector DB
                (Tika,          (remove PII,     (512-token    (OpenAI /     (Pinecone /
                 Textract)       normalize)       chunks)       sentence-     Weaviate /
                                                                transformers) pgvector)
```

This pipeline feeds the **RAG system** for components 4 and 7 (answering questions, retrieving documents).

---

## C. Feature Engineering

### C1. Features by Component

#### Component 1: Churn Prediction

| Feature | Source | Type | Engineering |
|---------|--------|------|-------------|
| Account tenure (months) | CRM | Numerical | `current_date - account_open_date` |
| Transaction frequency (last 30/60/90 days) | Transactions | Numerical | Count aggregation with time windows |
| Transaction amount trend | Transactions | Numerical | Slope of monthly avg amount over 6 months |
| Product count | CRM | Numerical | Count of active products |
| Complaint count (last 90 days) | CRM | Numerical | Count aggregation |
| Days since last login | Web Activity | Numerical | `current_date - last_login_date` |
| Service call frequency | Call Transcripts | Numerical | Count aggregation |
| Sentiment score of last 3 interactions | Emails + Calls | Numerical | NLP sentiment model output |
| Age, income bracket, geography | CRM | Categorical | One-hot encoding / binning |
| Channel usage ratio (mobile vs branch) | Web + CRM | Numerical | `mobile_logins / total_logins` |
| Balance velocity (rate of change) | Transactions | Numerical | `(balance_t - balance_t-30) / balance_t-30` |

#### Component 2: Product Recommendation

| Feature | Source | Type |
|---------|--------|------|
| Products currently held | CRM | Categorical (multi-label) |
| Life stage signals (salary credits, large expenses) | Transactions | Derived |
| Similar customer cluster ID | CRM + Transactions | Collaborative filtering |
| Product page views | Web Activity | Behavioural |
| Income-to-debt ratio | CRM + Transactions | Derived ratio |

#### Component 6: Anomaly/Fraud Detection

| Feature | Source | Type |
|---------|--------|------|
| Transaction amount vs historical average | Transactions | Numerical (z-score) |
| Transaction velocity (count in last 1hr) | Real-time Transactions | Streaming aggregation |
| Geo-location deviation | Transactions | Numerical (distance from usual) |
| New merchant flag | Transactions | Binary |
| Time-of-day deviation | Transactions | Categorical |
| Device fingerprint change | Web Activity | Binary |

### C2. Feature Leakage Risks

| Leakage Risk | Why It's Dangerous | Mitigation |
|--------------|-------------------|------------|
| Using "account_closed_date" for churn prediction | This is the **target itself** disguised as a feature. Only known after churn happens. | Exclude any post-event features. Define strict prediction point. |
| Using "retention_offer_received" | If bank sent offer because they already knew customer was at risk, this is **reverse causality** — the feature is caused by the target. | Exclude intervention features. |
| Future transaction data leaking into training | If feature window overlaps with label window, model sees the future. | Enforce temporal cutoff: features computed from `[t-365, t]`, label computed from `[t+1, t+90]`. |
| Using aggregated features without time-bounding | "Total lifetime transactions" computed on full history may include transactions after the prediction date during training. | Always compute features relative to a **snapshot date** per record. |

### C3. Feature Engineering Best Practices Applied

1. **Time-windowed aggregations** — Always specify the window (30d, 60d, 90d). Never use "all time" unless deliberately.
2. **Relative features over absolute** — "% change in balance" > "absolute balance" (captures trajectory).
3. **Interaction features** — `tenure × product_count` captures the interaction between loyalty and engagement.
4. **Feature Store** — Use Feast/Tecton to ensure **training-serving consistency** (same feature computation logic in both batch and real-time).
5. **PII Masking** — Remove/hash PII before feature engineering. Name, email, phone are NOT features.

---

## D. Model Strategy

| # | Component | Model Approach | Justification |
|---|-----------|---------------|---------------|
| 1 | Churn Prediction | **Traditional ML — XGBoost / LightGBM** | Structured tabular data. XGBoost excels on tabular data. Highly interpretable with SHAP. Fast training and inference. No need for deep learning. |
| 2 | Product Recommendation | **Hybrid: Collaborative Filtering + Content-Based + Rules** | CF for "customers like you" patterns. Content-based for new products (cold start). Business rules for eligibility filtering (credit score thresholds). |
| 3 | Summarize Interactions | **Foundation Model (LLM) via API or Fine-tuned** | Abstractive summarization requires language understanding. Use GPT-4/Claude API for quick start. Fine-tune open-source (LLaMA/Mistral) if data privacy is critical. |
| 4 | Answer Employee Questions | **RAG (Retrieval-Augmented Generation)** | Employees need answers grounded in bank's policies/documents. RAG = retrieve relevant document chunks + generate answer. Reduces hallucination vs pure LLM. No model training needed — just index documents. |
| 5 | Generate Customer Communication | **Foundation Model + Fine-tuning + Rules** | LLM generates draft communication. Fine-tune on bank's historical communications for tone/style. Rules layer for compliance (mandatory disclaimers, regulatory language). **Human-in-the-loop** for review before sending. |
| 6 | Detect Suspicious Activity | **Deep Learning (Autoencoder) + Traditional ML (Isolation Forest) + Rules** | Autoencoder learns "normal" transaction patterns; anomalies = high reconstruction error. Isolation Forest as ensemble. Rules for known fraud patterns (velocity limits, blacklisted merchants). Ensemble of all three for best detection. |
| 7 | Retrieve from Documents | **RAG** | Same as #4. Vector search over embedded document chunks → LLM generates synthesized answer with citations. |

### Model Strategy Decision Framework

```
Is data structured/tabular?
  ├── YES → Traditional ML (XGBoost, Random Forest)
  │         ├── Need real-time? → Pre-compute features, serve via API
  │         └── Need explainability? → SHAP / LIME
  └── NO (text, images, unstructured)
      ├── Need to generate new content? → Generative AI (LLM)
      │   ├── Need grounding in company data? → RAG
      │   ├── Need domain-specific style? → Fine-tune
      │   └── Generic task? → API (GPT-4, Claude)
      └── Need to detect patterns? → Deep Learning (Autoencoders, LSTM)
```

---

## E. System Architecture

### Five-Layer Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        5. MONITORING LAYER                          │
│  Model Performance │ Data Drift │ Prediction Distribution │ SLAs   │
│  Grafana + Prometheus + Custom ML Dashboards + Alert Manager        │
├─────────────────────────────────────────────────────────────────────┤
│                     4. INFRASTRUCTURE LAYER                         │
│  Cloud Platform (AWS/GCP/Azure) │ Kubernetes │ GPU Clusters         │
│  CI/CD Pipeline │ Model Registry │ Secret Management │ IAM          │
├─────────────────────────────────────────────────────────────────────┤
│                      3. APPLICATION LAYER                           │
│  RM Dashboard │ Employee Chatbot │ Customer Portal │ Alert Console  │
│  Communication Generator │ Document Search │ Risk Dashboard         │
├─────────────────────────────────────────────────────────────────────┤
│                        2. MODEL LAYER                               │
│  Churn Model │ Recommender │ Summarizer │ Q&A (RAG) │ Fraud Model  │
│  Communication Generator │ Document Retriever                       │
│  Model Serving: Real-time API (SageMaker/Vertex) + Batch Transform  │
├─────────────────────────────────────────────────────────────────────┤
│                         1. DATA LAYER                               │
│  Data Lake │ Data Warehouse │ Feature Store │ Vector DB │ Kafka     │
│  Batch Pipeline (Spark/Airflow) │ Stream Pipeline (Flink/Kinesis)   │
│  ETL/ELT │ Data Validation │ Data Catalog │ Lineage Tracker         │
└─────────────────────────────────────────────────────────────────────┘
```

### Layer Details

**Layer 1 — Data:**
- Raw data lands in **Data Lake** (S3/GCS) — schema-on-read, cheap storage
- Cleaned/transformed data in **Data Warehouse** (Snowflake/BigQuery) — for analytics and BI
- ML features in **Feature Store** (Feast/Tecton) — prevents training-serving skew
- Document embeddings in **Vector Database** (Pinecone/Weaviate) — for RAG
- Real-time events via **Kafka/Kinesis** — for streaming pipeline

**Layer 2 — Models:**
- Each component has its own model, independently trained, versioned, and deployed
- **Model Registry** (MLflow/SageMaker) stores all model versions with metadata
- **Real-time serving** for churn, fraud, recommendations (API endpoints, <100ms latency)
- **Batch serving** for summarization, communication generation (scheduled jobs)

**Layer 3 — Applications:**
- **RM Dashboard** — Unified view showing churn risk, recommended next actions, customer summary
- **Employee Chatbot** — RAG-powered Q&A for internal policies
- **Communication Generator** — Draft emails/letters with human review workflow
- **Alert Console** — Real-time fraud/suspicious activity alerts

**Layer 4 — Infrastructure:**
- Kubernetes for container orchestration (auto-scaling model endpoints)
- GPU clusters for LLM inference and training
- CI/CD for automated model retraining and deployment
- IAM + encryption for security

**Layer 5 — Monitoring:**
- **Data quality** — Missing values, schema changes, volume anomalies
- **Data drift** — Feature distribution shifts (PSI, KL divergence)
- **Model performance** — Accuracy, precision, recall tracked over time
- **Prediction distribution** — Alert if prediction distribution shifts (e.g., suddenly predicting 80% churn)
- **System health** — Latency (P50, P99), throughput, error rates
- **Business KPIs** — Actual churn rate, recommendation conversion rate, fraud detection rate

---

## F. Model Marketplace — Build vs Buy vs Open Source vs API

| # | Component | Decision | Specific Choice | Justification |
|---|-----------|----------|-----------------|---------------|
| 1 | Churn Prediction | **Build** (in-house) | XGBoost/LightGBM trained on bank's data | Core competitive advantage. Bank's own data is the moat. Proprietary customer behaviour patterns. Relatively simple to build with internal data science team. |
| 2 | Product Recommendation | **Build** + Open Source | Custom model on bank's data; use open-source libraries (Surprise, LightFM) | Recommendation logic is business-specific. Open-source frameworks reduce development time. |
| 3 | Summarize Interactions | **API** (short-term) → **Open Source Fine-tune** (long-term) | Start with GPT-4 API; migrate to fine-tuned LLaMA/Mistral | API for fast deployment (weeks). Migrate to fine-tuned open-source for data privacy and cost at scale. |
| 4 | Answer Employee Questions (RAG) | **Open Source** + API | LangChain + open-source embeddings + GPT-4/Claude API | RAG framework is open-source. LLM can be API or self-hosted. Vector DB is open-source (pgvector) or managed (Pinecone). |
| 5 | Generate Communication | **API** + Rules | GPT-4 API + custom compliance rules layer | Needs high-quality language generation. Rules layer is built in-house for regulatory compliance. Human-in-the-loop mandatory. |
| 6 | Fraud/Anomaly Detection | **Build** + **Buy** | Build custom model + buy vendor solution (Featurespace, NICE Actimize) | Ensemble approach: vendor provides baseline detection + custom model adds bank-specific patterns. Fraud detection is too critical to rely on one approach. |
| 7 | Document Retrieval (RAG) | **Open Source** + API | Same as #4 | Shared infrastructure with employee Q&A system. |

### Decision Framework Applied

```
Is it core competitive advantage?
  ├── YES → BUILD (Churn, Recommendations, Fraud custom model)
  └── NO
      ├── Is data privacy critical?
      │   ├── YES → OPEN SOURCE (self-hosted LLM, on-prem Vector DB)
      │   └── NO → API (GPT-4 for summarization, communication)
      └── Is it a solved industry problem?
          ├── YES → BUY (Fraud vendor, OCR vendor)
          └── NO → BUILD + OPEN SOURCE
```

---

## G. Production Risks — At Least 10 Failure Modes

| # | Failure Mode | Component Affected | Impact | Mitigation |
|---|-------------|-------------------|--------|------------|
| 1 | **Data drift** — Customer behaviour shifts (post-COVID, economic downturn) causing feature distributions to change | Churn, Recommendations | Model accuracy degrades silently. Predictions become unreliable. | Monitor feature distributions (PSI). Automated retraining triggers when drift exceeds threshold. |
| 2 | **Concept drift** — The relationship between features and churn changes (e.g., low activity no longer means churn because customers shifted to a competitor's app) | Churn | Model still runs but predictions are wrong. No data error to catch. | Monitor actual vs predicted churn rate. Periodic concept drift analysis. Scheduled retraining. |
| 3 | **LLM hallucination** — Chatbot invents policies, cites non-existent regulations, gives wrong product details | Q&A, Communication | Employee acts on wrong information. Regulatory risk. Reputational damage. | RAG with source citations. Confidence thresholds. Human-in-the-loop for customer-facing content. Guardrails (content filtering). |
| 4 | **Feature leakage in training** — Future data leaks into training features, giving inflated offline metrics that don't hold in production | Churn, Fraud | Model appears excellent in testing but fails in production. False confidence. | Strict temporal feature engineering. Feature Store with point-in-time correctness. Code reviews on feature pipelines. |
| 5 | **Training-serving skew** — Feature computation differs between batch training and real-time serving | All ML models | Model receives different feature values in production than training. Predictions are unpredictable. | Feature Store (Feast/Tecton) ensures same computation logic. Integration tests comparing training vs serving features. |
| 6 | **Data pipeline failure** — ETL job fails silently, serving stale features | All components | Model makes predictions on outdated data. Fraud misses recent transactions. | Data freshness monitoring. Alerts on pipeline failures. Circuit breaker pattern (stop predictions if data > X hours old). |
| 7 | **Adversarial attack on fraud model** — Fraudsters learn the model's patterns and adapt their behaviour | Fraud Detection | Fraud evolves to evade detection. Detection rate drops. | Regularly retrain. Ensemble models (harder to fool all). Adversarial testing. Rules as safety net (rules don't "drift"). |
| 8 | **Bias in recommendations** — Model recommends high-value products disproportionately to certain demographics | Recommendations | Regulatory violation (fair lending laws). Reputational damage. Discrimination. | Fairness metrics by demographic group. Bias audits. Disparate impact testing. Regulatory review before deployment. |
| 9 | **PII exposure via LLM** — LLM memorizes and regurgitates customer PII from training data or retrieved documents | Q&A, Summarization | Data breach. Privacy regulation violation (GDPR, DPDPA). | PII masking in retrieval pipeline. Output filtering. Never fine-tune on raw customer data. Data anonymization. |
| 10 | **Latency degradation under load** — Model serving latency spikes during peak hours (month-end, salary day) | Fraud Detection, Recommendations | Fraud transactions approved before model responds. Poor user experience. | Auto-scaling infrastructure. Load testing. Timeout + fallback logic (e.g., fall back to rules-based fraud detection if ML model times out). |
| 11 | **Model version mismatch** — Wrong model version deployed after update. Feature schema incompatible with new model. | All models | Crashes or silent wrong predictions. | Model Registry with versioning. Canary deployment. Automated integration tests before promotion. |
| 12 | **Feedback loop bias** — Recommender only shows products it already thinks customer wants → never explores new options → filter bubble | Recommendations | Revenue loss from missed opportunities. Customer dissatisfaction. | Exploration-exploitation balance (epsilon-greedy). Diversity constraints in recommendation pipeline. |
| 13 | **Prompt injection** — Malicious input to the employee chatbot extracts system prompts or causes unintended behaviour | Q&A Chatbot | Information leakage. Unintended actions. | Input sanitization. Prompt injection detection. Separate system vs user prompts. Rate limiting. |

---

## H. Governance

### H1. Privacy

| Control | Implementation |
|---------|---------------|
| Data minimization | Collect only data necessary for each model. Document justification for each data field. |
| PII handling | Mask/hash PII in feature pipelines. PII never stored in Feature Store or Vector DB. |
| Consent management | Track customer consent for data usage. Honour opt-out requests within 72 hours. |
| Regulatory compliance | GDPR (if EU customers), DPDPA (India), CCPA (US). Data retention policies. Right to erasure. |
| Access control | Role-based access (RBAC). Data scientists can't access raw PII — only anonymized features. |
| Data lineage | Track every data transformation from source to model input. Know which customers' data was used in which model. |

### H2. Bias

| Control | Implementation |
|---------|---------------|
| Pre-deployment bias audit | Test model predictions across demographic groups (age, gender, geography, income). |
| Disparate impact testing | Ensure churn risk scores don't systematically disadvantage protected groups. |
| Fair lending compliance | Product recommendations must not discriminate. Equal opportunity in credit product suggestions. |
| Training data audit | Check if historical data reflects existing biases (e.g., certain demographics historically denied products). |
| Ongoing monitoring | Track precision/recall by demographic group in production. Alert on significant disparities. |

### H3. Explainability

| Control | Implementation |
|---------|---------------|
| SHAP values | For churn and fraud models: show top 5 features driving each prediction. "This customer's churn risk is high because: balance dropped 40%, no login in 30 days, complaint filed." |
| RAG citations | For Q&A and document retrieval: always show source document and passage that generated the answer. |
| Model cards | For each model: document intended use, training data, performance metrics, known limitations, fairness metrics. |
| Decision audit trail | Log every prediction with input features, model version, output, and explanation. Queryable for regulatory audit. |

### H4. Security

| Control | Implementation |
|---------|---------------|
| Data encryption | At rest (AES-256) and in transit (TLS 1.3). |
| Model endpoint security | API authentication (OAuth 2.0). Rate limiting. Input validation. |
| LLM security | Prompt injection detection. Output filtering. No direct database access from LLM. |
| Network isolation | ML infrastructure in private VPC. No public internet access for model endpoints. |
| Secret management | API keys, credentials in vault (AWS Secrets Manager / HashiCorp Vault). Never in code. |

### H5. Auditability

| Control | Implementation |
|---------|---------------|
| Prediction logging | Every prediction logged with timestamp, model version, input features, output, explanation. |
| Model lineage | Track which data → which features → which model → which predictions. End-to-end traceability. |
| Change management | All model changes go through approval workflow. No direct deployment to production. |
| Regulatory reporting | Automated reports for compliance team: model performance, fairness metrics, incident log. |
| Immutable audit log | Append-only log. Cannot be tampered with. Retained for 7+ years (banking regulation). |

### H6. Model Drift

| Control | Implementation |
|---------|---------------|
| Data drift detection | Monitor feature distributions using PSI (Population Stability Index). Alert if PSI > 0.2. |
| Concept drift detection | Monitor actual vs predicted outcomes. Track metrics over time with sliding windows. |
| Automated retraining | Trigger retraining when drift exceeds threshold. Retrain on most recent data window. |
| Champion-challenger | Always keep current model (champion) and retrained model (challenger). Auto-promote challenger only if it beats champion on holdout set. |
| Scheduled retraining | Even without drift alerts: retrain monthly as baseline policy. |

### H7. Hallucination

| Control | Implementation |
|---------|---------------|
| RAG grounding | All LLM answers must be grounded in retrieved documents. If no relevant document found, say "I don't have information on this." |
| Confidence thresholds | If retrieval similarity score < threshold, don't generate answer. Route to human. |
| Fact-checking layer | Cross-reference generated content with structured data (product details, rates, policies). |
| Human-in-the-loop | All customer-facing generated content reviewed by human before sending. |
| Output guardrails | Block outputs containing financial advice, regulatory claims, or commitments without human approval. |
| Citation requirement | LLM must cite source document for every factual claim. No citation = flagged for review. |

---

## I. Business Case — KPIs

### Primary KPIs by Component

| # | Component | KPI | Measurement | Target |
|---|-----------|-----|-------------|--------|
| 1 | Churn Prediction | **Churn rate reduction** | (Churn rate before - after) / before | 15-25% reduction in churn |
| 1 | Churn Prediction | **Retention campaign ROI** | Revenue saved / cost of retention campaigns | > 3:1 |
| 1 | Churn Prediction | **Precision @ top 10%** | Of the top 10% riskiest customers, what % actually churned? | > 60% |
| 2 | Recommendation | **Product cross-sell rate** | Products per customer (before vs after) | 10-20% increase |
| 2 | Recommendation | **Recommendation conversion rate** | Accepted recommendations / shown recommendations | > 5% |
| 3 | Summarization | **RM time saved per interaction** | Average time spent reading vs using summary | 50% reduction |
| 4 | Employee Q&A | **Query resolution rate** | Questions answered without escalation to human | > 70% |
| 4 | Employee Q&A | **Employee satisfaction (NPS)** | Survey score for the chatbot | > 40 NPS |
| 5 | Communication | **Communication turnaround time** | Time from trigger to customer receiving message | 60% reduction |
| 6 | Fraud Detection | **Fraud detection rate (recall)** | Fraudulent transactions caught / total fraudulent | > 95% |
| 6 | Fraud Detection | **False positive rate** | Legitimate transactions flagged as fraud | < 2% |
| 6 | Fraud Detection | **Fraud losses** | Total monetary loss from undetected fraud | 30% reduction |
| 7 | Document Retrieval | **Search success rate** | Queries with relevant answer / total queries | > 80% |

### System-Level KPIs

| KPI | Measurement | Target |
|-----|-------------|--------|
| **System uptime** | Availability of all AI services | > 99.5% |
| **Prediction latency (P99)** | 99th percentile response time | < 200ms (real-time), < 5min (batch) |
| **Data freshness** | Time from event to feature availability | < 1 hour (batch), < 1 min (streaming) |
| **Model retraining frequency** | How often models are updated | Monthly (minimum), on-demand for drift |
| **Incident response time** | Time to detect and respond to model failures | < 30 minutes |

### Business Value Summary

| Metric | Estimated Annual Impact |
|--------|----------------------|
| Reduced churn (retaining high-value customers) | High — customer acquisition costs 5-7x more than retention |
| Increased cross-sell revenue | Medium-High — each additional product increases customer lifetime value |
| Fraud loss prevention | High — direct P&L impact |
| Employee productivity (summarization + Q&A) | Medium — time savings across thousands of RMs |
| Compliance cost avoidance | High — automated audit trails reduce manual compliance effort |

---

## Summary Diagram

```
                    ┌─────────────────────────────────┐
                    │    AI-POWERED RELATIONSHIP       │
                    │         MANAGER                  │
                    └──────────┬──────────────────────┘
                               │
        ┌──────────┬───────────┼───────────┬──────────┐
        ▼          ▼           ▼           ▼          ▼
   ┌─────────┐ ┌────────┐ ┌────────┐ ┌─────────┐ ┌────────┐
   │ Predict  │ │Recommend│ │Generate│ │  Detect  │ │Retrieve│
   │ (XGBoost)│ │(Hybrid) │ │ (LLM)  │ │(Ensemble)│ │ (RAG)  │
   └────┬─────┘ └───┬────┘ └───┬────┘ └────┬─────┘ └───┬────┘
        │           │          │           │           │
        └───────────┴──────────┴───────────┴───────────┘
                               │
                    ┌──────────▼──────────┐
                    │    FEATURE STORE     │
                    │  (Offline + Online)  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │  Batch   │   │ Streaming│   │  Vector  │
        │ Pipeline │   │ Pipeline │   │    DB    │
        └──────────┘   └──────────┘   └──────────┘
```
