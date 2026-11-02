# Assignment 1 — Similar Practice Questions and Answers

> **Course:** SS ZG662 — Introduction to AI Systems (BITS Pilani)
> These practice questions mirror the style and depth of Assignment 1 (Q1 and Q2). Use them for exam preparation.

---

# PRACTICE QUESTION 1: AI System Design (Similar to Q1)

## E-Commerce Platform — AI-Powered Shopping Experience

A large e-commerce company wants to build a comprehensive AI system. The system should:

1. **Predict delivery time** for each order
2. **Recommend products** to users
3. **Detect fraudulent orders** in real-time
4. **Generate product descriptions** from images and specifications
5. **Power a customer support chatbot**
6. **Predict inventory demand** for warehouse planning
7. **Classify customer reviews** as positive/negative/neutral

**Available data:** User profiles, Purchase history, Clickstream data, Product catalog, Customer reviews, Delivery logs, Warehouse inventory, Supplier data, Payment transactions, Customer support tickets, Product images

Design the complete AI system covering: AI categories, data architecture, feature engineering, model strategy, system architecture, build vs buy, production risks, governance, and KPIs.

---

### Answer

---

### A. AI Categories

| # | Component | Primary AI Category | Secondary | Justification |
|---|-----------|-------------------|-----------|---------------|
| 1 | Predict delivery time | **Predictive AI — Regression** | — | Continuous numerical output (hours/days). Supervised learning on historical delivery data. |
| 2 | Recommend products | **Recommender System** | Predictive AI | Hybrid: Collaborative filtering (similar users) + Content-based (product features) + Contextual (time, session). |
| 3 | Detect fraudulent orders | **Predictive AI — Anomaly Detection** | Classification | Real-time classification. Isolation Forest / XGBoost for known patterns, Autoencoders for unknown patterns. |
| 4 | Generate product descriptions | **Generative AI** | Computer Vision | Multi-modal: CV extracts image features → LLM generates text description. |
| 5 | Customer support chatbot | **Conversational AI** | Generative AI + RAG | RAG over FAQ/policy documents. Escalation to human for complex issues. |
| 6 | Predict inventory demand | **Predictive AI — Time Series** | — | Forecast future demand per product per warehouse. ARIMA, Prophet, or DeepAR. |
| 7 | Classify customer reviews | **Predictive AI — Classification** (NLP) | — | Multi-class text classification (positive/negative/neutral). Can use fine-tuned BERT or even traditional ML with TF-IDF. |

---

### B. Data Architecture

#### Batch Pipeline
```
[User Profiles]──────┐
[Purchase History]────┤     ┌──────────┐    ┌──────────────┐    ┌──────────────┐
[Product Catalog]─────┼──►  │ Data Lake │───►│  Feature      │───►│   Feature    │
[Delivery Logs]───────┤     │ (S3/GCS) │    │  Engineering  │    │   Store      │
[Reviews]─────────────┤     └──────────┘    └──────────────┘    │  (Offline)   │
[Inventory]───────────┤                                         └──────────────┘
[Support Tickets]─────┘
        ↕ Scheduled: Daily/Hourly via Airflow/Spark
```

#### Streaming Pipeline
```
[Clickstream]────────┐
[Payment Txns]───────┼──► Kafka ──► Flink ──► Online Feature Store (Redis)
[Cart Events]────────┘                    ──► Fraud Detection (Real-time)
                                          ──► Recommendation Engine (Live)
```

#### Text/Image Pipeline
```
[Product Images] ──► Image Processing ──► Feature Extraction (ResNet/CLIP) ──► Embedding Store
[Reviews/Tickets] ──► Text Cleaning ──► Chunking ──► Embedding ──► Vector DB (for RAG)
[Product Specs] ──► Structured Parsing ──► Product Knowledge Graph
```

**Architecture Pattern:** Lambda Architecture (batch for training/analytics + streaming for real-time serving)

---

### C. Feature Engineering

#### Delivery Time Prediction
| Feature | Source | Engineering |
|---------|--------|-------------|
| Distance (warehouse to customer) | User profile + warehouse location | Haversine distance calculation |
| Historical avg delivery time for this route | Delivery logs | Time-windowed aggregation by (origin, destination_region) |
| Order weight/volume | Product catalog + cart | Sum of product dimensions |
| Day of week, time of day | Order timestamp | Cyclical encoding (sin/cos) |
| Weather at destination | External API | Real-time integration |
| Carrier performance score | Delivery logs | Rolling average delivery speed per carrier |
| Holiday flag | Calendar | Binary feature |
| Warehouse backlog | Inventory system | Count of pending orders at order time |

#### Product Recommendation
| Feature | Source | Engineering |
|---------|--------|-------------|
| User purchase history embedding | Purchase history | Sequence embedding (user's last 20 purchases) |
| Product co-purchase matrix | Purchase history | Items frequently bought together |
| Category affinity scores | Clickstream + purchases | Time-weighted category visit/purchase ratio |
| Price sensitivity | Purchase + clickstream | Ratio of purchases at discount vs full price |
| Session context | Clickstream | Current session viewed/carted items |
| Recency-frequency-monetary (RFM) | Purchases | Standard RFM segmentation features |

#### Fraud Detection
| Feature | Source | Engineering |
|---------|--------|-------------|
| Order amount vs user's average | Purchase history | Z-score of current order |
| Shipping address vs billing address mismatch | Order data | Binary + distance between addresses |
| New shipping address flag | User profile | Binary (first time this address used) |
| Device fingerprint change | Clickstream | Binary (different device than usual) |
| Velocity (orders in last 1 hour) | Payment transactions | Streaming count aggregation |
| Time since account creation | User profile | Numerical (new accounts = higher risk) |

#### Leakage Risks
| Risk | Why | Mitigation |
|------|-----|------------|
| Using actual delivery date for delivery prediction | Target is the delivery time; actual date is the answer | Only use features available at order placement time |
| Using chargeback flag for fraud detection | Chargebacks happen weeks after order — future data | Define prediction point as order placement; chargeback is the label, not a feature |
| Using return rate for review sentiment | Returns happen after review; also conflates two different signals | Temporal ordering: only use data available before review timestamp |

---

### D. Model Strategy

| # | Component | Approach | Why |
|---|-----------|---------|-----|
| 1 | Delivery prediction | **Traditional ML — XGBoost** | Tabular data, clear features. XGBoost handles non-linear relationships. SHAP for explainability ("your order is slow because of warehouse backlog"). |
| 2 | Recommendations | **Hybrid: Two-Tower Neural Network + Business Rules** | Two-tower model (user embedding tower + product embedding tower) for candidate generation. Business rules for filtering (in-stock, eligibility). Re-ranking with XGBoost for personalization. |
| 3 | Fraud detection | **Ensemble: XGBoost + Autoencoder + Rules** | XGBoost for known fraud patterns. Autoencoder for novel/unknown fraud. Rules as safety net (hard limits). Real-time serving required (<50ms). |
| 4 | Product descriptions | **Foundation Model (LLM) via API** | Multi-modal LLM (GPT-4V, Claude) takes image + specs → generates description. Fine-tune if specific brand voice needed. |
| 5 | Customer chatbot | **RAG** | Retrieve from FAQ/policy knowledge base → LLM generates answer. Escalation to human for complex/emotional cases. |
| 6 | Demand forecasting | **Deep Learning — DeepAR / Temporal Fusion Transformer** | Time-series with multiple products, external factors (holidays, promotions). Deep learning handles complex seasonality better than ARIMA at scale. |
| 7 | Review classification | **Fine-tuned BERT or Traditional ML (TF-IDF + Logistic Regression)** | BERT for high accuracy. TF-IDF + LR if latency/cost is a concern. Start with TF-IDF baseline, upgrade if needed. |

---

### E. System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      MONITORING LAYER                            │
│  Data Drift │ Model Perf │ A/B Testing │ Business KPIs │ Alerts │
├─────────────────────────────────────────────────────────────────┤
│                    INFRASTRUCTURE LAYER                          │
│  Kubernetes │ GPU Cluster │ CI/CD │ Model Registry │ IAM        │
├─────────────────────────────────────────────────────────────────┤
│                     APPLICATION LAYER                            │
│  Product Page (recs + desc) │ Checkout (delivery ETA + fraud)   │
│  Support Chat │ Inventory Dashboard │ Review Analytics           │
├─────────────────────────────────────────────────────────────────┤
│                       MODEL LAYER                                │
│  Delivery Model │ Recommender │ Fraud Model │ LLM (desc+chat)  │
│  Demand Model │ Sentiment Model │ Real-time + Batch Serving     │
├─────────────────────────────────────────────────────────────────┤
│                        DATA LAYER                                │
│  Data Lake │ Warehouse │ Feature Store │ Vector DB │ Kafka      │
│  Batch (Spark/Airflow) │ Streaming (Flink) │ Image Store        │
└─────────────────────────────────────────────────────────────────┘
```

---

### F. Build vs Buy vs Open Source vs API

| # | Component | Decision | Justification |
|---|-----------|----------|---------------|
| 1 | Delivery prediction | **Build** | Proprietary logistics data is the competitive advantage. Unique to this company's network. |
| 2 | Recommendations | **Build** + Open Source | Core competitive advantage (Amazon attributes 35% of revenue to recommendations). Use open-source frameworks (Merlin, LightFM). |
| 3 | Fraud detection | **Build + Buy** | Build custom model for company-specific patterns. Buy vendor solution (Stripe Radar, Signifyd) as baseline/ensemble. |
| 4 | Product descriptions | **API** | GPT-4V API for generation. Not a differentiator — quality of description matters less than speed to market. |
| 5 | Customer chatbot | **Open Source + API** | LangChain (open source) + LLM API. RAG framework is commodity. |
| 6 | Demand forecasting | **Build** | Proprietary: depends on company's specific SKUs, promotions, seasonal patterns. Use open-source frameworks (GluonTS, Darts). |
| 7 | Review classification | **Open Source** | Fine-tune open-source BERT. Well-solved problem with available pre-trained models. |

---

### G. Production Risks (10+)

| # | Risk | Impact | Mitigation |
|---|------|--------|------------|
| 1 | **Recommendation filter bubble** | Users only see similar products → reduced discovery → lower basket size | Add diversity constraints, exploration (epsilon-greedy), "surprise" recommendations |
| 2 | **Delivery prediction overconfidence** | Promising "next day" but delivering in 3 days → customer dissatisfaction, refunds | Add prediction intervals (not just point estimates). Under-promise by adding buffer to P75 estimate. |
| 3 | **Fraud model blocks legitimate orders** | High false positive rate → lost revenue, frustrated customers | Tiered response: low risk → approve, medium → 3D Secure, high → manual review. Never hard-block without human review for high-value orders. |
| 4 | **Demand forecast error → stockouts or overstock** | Underpredict → lost sales. Overpredict → excess inventory costs. | Ensemble forecasts. Safety stock buffers. Human override for promotional events. |
| 5 | **LLM generates inaccurate product descriptions** | Wrong specs, misleading claims → returns, legal liability | Human review workflow. Fact-check generated text against structured product data. Never generate safety or compliance claims via LLM. |
| 6 | **Chatbot hallucination** | Invents return policies, promises refunds it shouldn't | RAG with strict retrieval. Confidence threshold. Escalation to human. |
| 7 | **Data pipeline failure (silent)** | Stale features → wrong predictions → bad recommendations, missed fraud | Data freshness monitoring. Circuit breakers. Alerts on pipeline latency. |
| 8 | **Concept drift — COVID/pandemic-type event** | Buying patterns shift dramatically. All models trained on old patterns become wrong. | Drift detection (PSI). Rapid retraining capability. Fallback to rules/heuristics during crisis. |
| 9 | **Price manipulation via recommendations** | If the recommender is biased toward high-margin products, customers lose trust | Separate relevance ranking from business optimization. Transparency in "sponsored" vs "organic" recommendations. |
| 10 | **Privacy violation — clickstream tracking** | Excessive tracking without consent → GDPR/DPDPA violation | Consent management. Data minimization. Anonymize clickstream data. Clear privacy policy. |
| 11 | **Adversarial reviews** | Fake positive/negative reviews fool the sentiment model | Anomaly detection on review patterns. Verified purchase filter. Cross-reference reviewer behaviour. |

---

### H. Governance

| Area | Controls |
|------|----------|
| **Privacy** | Consent management for data collection. PII anonymization in ML pipelines. GDPR/DPDPA compliance. Right to erasure. Data retention policies (delete clickstream after 2 years). |
| **Bias** | Recommendation fairness: ensure products from small sellers get fair exposure. Fraud model: check for bias against specific demographics or geographies. Demand forecast: no systematic under-forecasting for certain regions. |
| **Explainability** | Delivery: "Your estimated time is 3 days because [warehouse distance + carrier speed]". Fraud: log rejection reasons for compliance audit. Recommendations: "Recommended because you bought X" (transparency). |
| **Security** | Encrypt all data in transit and at rest. API authentication for model endpoints. Rate limiting. Input validation (prevent prompt injection on chatbot). DDoS protection. |
| **Auditability** | Log every prediction (input, output, model version, timestamp). Immutable audit trail. Compliance reports on demand. Model cards for each model. |
| **Model Drift** | PSI monitoring on all features. Performance tracking with ground truth (delivery actual vs predicted, fraud confirmed vs predicted). Automated retraining triggers. Champion-challenger model promotion. |
| **Hallucination** | RAG grounding for chatbot. Fact-checking layer for product descriptions. Human review for customer-facing generated content. Confidence thresholds — don't generate if retrieval score is low. |

---

### I. KPIs

| Component | KPI | Target |
|-----------|-----|--------|
| Delivery prediction | Mean Absolute Error (MAE) in hours | < 4 hours |
| Delivery prediction | % orders delivered within predicted window | > 90% |
| Recommendations | Click-through rate (CTR) on recommended products | > 3% |
| Recommendations | Revenue from recommended products / total revenue | > 25% |
| Fraud detection | Fraud detection rate (recall) | > 95% |
| Fraud detection | False positive rate (legitimate orders blocked) | < 0.5% |
| Product descriptions | Human quality rating (1-5) | > 4.0 |
| Customer chatbot | Resolution rate (resolved without human) | > 65% |
| Customer chatbot | Customer satisfaction (CSAT) | > 4.0/5.0 |
| Demand forecasting | Weighted MAPE (Mean Absolute Percentage Error) | < 15% |
| Review classification | F1 Score | > 0.85 |
| System-wide | Model serving latency (P99) | < 100ms (real-time), < 5min (batch) |
| System-wide | System uptime | > 99.9% |

---

---

# PRACTICE QUESTION 2: ML Operations Deep Dive (Similar to Q2)

## Insurance Claim Fraud Detection

An insurance company builds an ML model to predict whether a newly filed insurance claim is **fraudulent**. The company has 3 years of historical claims data containing:

- Policyholder demographics
- Policy details (type, premium, coverage)
- Claim details (amount, type, description)
- Claim filing method (online, phone, agent)
- Time between policy purchase and claim
- Claimant's claim history
- Investigation notes
- Adjuster assessment
- Payout amount
- Fraud determination (final label from investigation)

The data science team reports **99.2% accuracy**. The fraud rate in the dataset is **2.5%**.

---

### Practice Question 2.1: A feature called "Investigation outcome" is included. Why is this target leakage?

**Answer:**

"Investigation outcome" is the **process by which the label is determined**. The fraud label itself comes from the investigation. Using the investigation outcome as a feature is literally using the answer to predict the answer.

**The prediction must be made at the time a claim is filed** — before any investigation begins. The investigation happens weeks or months later, specifically to determine if the claim is fraudulent. Including its outcome means the model has access to the final determination at prediction time, which is impossible in production.

This is the most severe form of leakage: the feature is a **direct proxy for the target variable**.

---

### Practice Question 2.2: Identify 8 features that could introduce leakage

| # | Feature | When Available | Why It's Leakage |
|---|---------|---------------|------------------|
| 1 | **Investigation outcome** | After investigation (weeks/months) | Direct proxy for target. Investigation determines fraud. |
| 2 | **Adjuster assessment** | During/after investigation | Adjuster's judgment is informed by investigation findings. Available after prediction point. |
| 3 | **Payout amount** | After claim resolution | Fraudulent claims have $0 payout. This perfectly separates classes. |
| 4 | **Investigation notes** | During investigation | Written by investigators looking for fraud. Contains evidence of fraud determination. |
| 5 | **Special Investigation Unit (SIU) referral flag** | After initial screening | SIU referral happens because someone suspected fraud. It's a consequence of suspected fraud, not a cause. |
| 6 | **Claim denial reason** | After claim resolution | If denied for fraud, this is the target itself. |
| 7 | **Number of document re-submissions** | During claim processing | Fraudulent claims often require re-submissions during investigation. This happens after filing. |
| 8 | **Legal proceedings flag** | After investigation | Legal action is taken because fraud was confirmed. Post-determination data. |
| 9 | **Surveillance results** | During investigation | Insurance companies sometimes surveil suspicious claimants. This data exists only because fraud was suspected. |

---

### Practice Question 2.3: Define the prediction point and valid features

**Prediction Point:** The moment the claim is filed and registered in the system.

**Valid features (available at filing time):**

| Feature | Why Valid |
|---------|----------|
| Policyholder age, gender, occupation | Demographics known when policy was issued |
| Policy type, premium, coverage amount | Known from the policy contract |
| Claim amount requested | Provided at filing time |
| Claim type (auto, health, property) | Stated in the claim form |
| Time between policy purchase and first claim | Computable from policy start date and claim date |
| Number of previous claims by this policyholder | Historical data, available at filing |
| Previous claim amounts (average, max) | Historical aggregation, bounded by filing timestamp |
| Filing method (online/phone/agent) | Known at filing |
| Time of filing (hour, day of week) | Known at filing |
| Geographic region | From policyholder address (known at policy issuance) |

---

### Practice Question 2.4: The 99.2% accuracy — is it meaningful?

**No. With a 2.5% fraud rate, accuracy is meaningless.**

A model that predicts **"not fraud" for every claim** achieves:
- Accuracy = 97.5%
- Recall = 0% (catches no fraud)
- Completely useless

The reported 99.2% is only 1.7 points above this naive baseline. Moreover, given that leakage features (investigation outcome) are included, even this modest improvement is inflated.

**Proper metrics:**
- **Recall:** What % of fraudulent claims are caught? (Most important — missed fraud = direct financial loss)
- **Precision:** What % of flagged claims are actually fraudulent? (Important — false positives waste investigation resources)
- **AUC-PR:** Best single metric for highly imbalanced fraud detection
- **Precision @ top K:** "Of the top 100 claims we flag per week, how many are actual fraud?" (Operational metric tied to investigation capacity)

---

### Practice Question 2.5: After removing leakage features, accuracy drops to 91%. Team says model is worse. Respond.

**The model is not worse — it's now honest.**

| Metric | Before (with leakage) | After (cleaned) | Interpretation |
|--------|----------------------|-----------------|----------------|
| Accuracy | 99.2% | 91% | 8.2-point drop measures leakage magnitude |
| What it means | Model was copying investigation results | Model is learning from filing-time features | Cleaned model reflects real production capability |

**Key points:**
1. The 99.2% was impossible to achieve in production (investigation results don't exist at filing time)
2. 91% accuracy might actually be below the 97.5% naive baseline — we need to check recall and precision
3. The 8-point drop confirms heavy reliance on leaked features
4. After removing leakage, the team must evaluate using **recall, precision, F1, AUC-PR** — not accuracy

---

### Practice Question 2.6: Design a temporal train/test split strategy

**Why random split is wrong:**
- Fraud patterns evolve (new fraud schemes appear over time)
- Random split mixes time periods, allowing the model to "memorize" specific fraud patterns that no longer apply
- Same policyholder may appear in both training and test sets

**Temporal split:**
```
Year 1 (2021)   Year 2 (2022)   Year 3 (2023)
├──────────────┤ ├────────────┤ ├────────────┤
   TRAINING         VALIDATION      TEST
```

**Additional safeguards:**
1. **Policyholder-level split:** If a policyholder has claims in both training and test periods, all their claims go to the set corresponding to their **most recent claim's period**
2. **Gap period:** 30-day gap between training and validation to prevent label window overlap
3. **Walk-forward validation:** Train on [Y1] → validate [Y2], train on [Y1-Y2] → validate [Y3]

---

### Practice Question 2.7: The model is deployed. Flagged claims get investigated first. How does this affect retraining?

**Feedback loop problem:**

1. Model flags claim as suspicious → investigated quickly → fraud confirmed or denied → ground truth available
2. Model does NOT flag claim → investigation deprioritized or never happens → **ground truth is missing or delayed**
3. Over time, the training data is biased: we have confirmed fraud labels mainly for claims the model already flagged

**This creates a self-reinforcing loop:**
- The model only learns about fraud patterns it already knows (because those are the ones investigated)
- Novel fraud patterns are never investigated → never labelled → never learned
- The model becomes increasingly blind to new fraud schemes

**Solutions:**
1. **Random audit sampling:** Investigate a random 5% of unflagged claims to discover fraud the model missed
2. **Label all claims eventually:** Even unflagged claims should be reviewed (even if after a longer delay)
3. **Use multiple fraud signals:** Cross-reference with external fraud databases, not just internal investigation labels
4. **Active learning:** Periodically select uncertain predictions (near the threshold) for human investigation

---

### Practice Question 2.8: Fraud rate drops from 2.5% to 1.8% after deployment. Is the model working?

**Maybe, but there are multiple explanations. Do not assume the model caused the drop.**

| Explanation | Type | How to Verify |
|-------------|------|--------------|
| Model-driven deterrence: fraudsters avoid filing because they know the company has detection | ✅ Desired outcome | Check if claim volume for fraud-prone categories declined |
| Model catches fraud → fraudulent claims denied → fewer successful frauds in the data | ✅ Partial success | Separate "attempted fraud" from "successful fraud" in metrics |
| Fraud shifted to channels/types the model doesn't cover | ❌ Problem | Monitor fraud patterns across ALL claim types, not just model-covered ones |
| Fraud still occurring but investigation backlog means labels are delayed | ❌ Data issue | Check label completeness and investigation queue length |
| Economic conditions reduced fraud motivation | External factor | Compare with industry-wide fraud rate trends |
| Model is causing false negatives (missing fraud that goes undetected) | ❌ Problem | Random audit sampling (see Q2.7) |

**Correct approach:** Compare flagged-and-confirmed fraud rate vs unflagged-and-audited fraud rate. If the random audit finds significant undetected fraud, the model has blind spots.

---

### Practice Question 2.9: Data drift vs concept drift — classify these scenarios

**Scenario A:** After a major natural disaster, the company receives a surge of property damage claims. Claim amounts are much higher than normal, and the geographic distribution shifts heavily toward the disaster zone.

**Classification: DATA DRIFT**
- P(X) changed — claim amounts, locations, and volumes are different
- P(fraud | X) has NOT fundamentally changed — the relationship between claim features and fraud likelihood is the same
- The model may underperform because it's operating in a rare region of the feature space (extreme claim amounts it hasn't seen)
- Fix: Monitor feature distributions, possibly retrain with disaster-period data

**Scenario B:** A new state regulation makes it harder to prosecute insurance fraud, leading to an increase in fraudulent claims that follow the same patterns as before but are now more frequent.

**Classification: CONCEPT DRIFT**
- P(X) may be similar — same types of claims, same characteristics
- P(fraud | X) has changed — the same features now have a higher probability of being fraud because the risk-reward calculus for fraudsters changed
- The model's learned thresholds are now too high (fraud is more prevalent than before)
- Fix: Retrain to learn the new fraud prevalence, adjust thresholds

---

### Practice Question 2.10: Production approval — would you deploy?

**Given:**
- Fraud rate: 2.5%
- Model recall: 72% (catches 72% of fraud)
- Precision: 18% (of flagged claims, 18% are actual fraud)
- AUC-PR: 0.45
- Latency: 80ms
- No fairness analysis done
- No monitoring plan

**Assessment:**

| Dimension | Verdict | Reasoning |
|-----------|---------|-----------|
| Recall (72%) | ⚠️ Moderate | Misses 28% of fraud. Every missed fraudulent claim is a direct financial loss. Target should be >85%. |
| Precision (18%) | ⚠️ Low but context-dependent | 82% of flagged claims are NOT fraud. This means investigators spend 82% of their time on legitimate claims. Whether this is acceptable depends on investigation capacity and cost. |
| AUC-PR (0.45) | ⚠️ Moderate for 2.5% prevalence | Not great, but significantly above the 0.025 baseline (random model on 2.5% prevalence). Room for improvement. |
| Fairness | ❌ Blocking | No fairness analysis. Model could be biased against certain demographics (age, region, claim type). Must not deploy without this analysis — could lead to discrimination lawsuits. |
| Monitoring | ❌ Blocking | No monitoring plan. Cannot deploy a fraud model without drift detection and performance tracking. |
| Latency | ✅ Good | 80ms is fast enough for real-time claim screening. |

**Decision: DO NOT DEPLOY TO PRODUCTION. Approve for pilot with conditions:**
1. Complete fairness analysis across demographics and regions
2. Implement monitoring (data quality, drift, recall/precision over time)
3. Deploy in **shadow mode** first — model scores claims but doesn't block them
4. Run shadow mode for 60 days to validate real-world performance
5. After shadow mode: deploy as a **triage tool** (flag for human investigation, never auto-deny)
6. Work on improving recall (investigate feature engineering, model architecture)

---

---

# PRACTICE QUESTION 3: Quick-Fire Concept Questions (Exam Style)

These are shorter questions testing individual concepts from the assignment.

---

### Q3.1: Feature Store — Why is it important?

**Question:** A model performs well during offline evaluation but poorly in production. Investigation reveals that the feature "average transaction amount in last 30 days" is computed differently during training (using Spark) and serving (using a Python script). What is this problem called, and how would you prevent it?

**Answer:**

This is called **training-serving skew** (or **online-offline skew**). The same feature has different computational logic in the training pipeline vs the serving pipeline, so the model receives different feature values in production than what it learned from.

**Prevention: Use a Feature Store (e.g., Feast, Tecton)**

A Feature Store ensures:
1. **Single feature definition:** One computation logic used by both training and serving
2. **Offline store:** Pre-computed features for training (batch, Spark)
3. **Online store:** Low-latency feature retrieval for serving (Redis, DynamoDB)
4. **Both stores produce identical values** because they use the same transformation logic
5. **Point-in-time correctness:** Historical feature values are retrievable for training without leakage

Without a Feature Store, developers independently implement the same feature twice (once for training, once for serving), and subtle differences (rounding, time zone handling, null treatment) cause divergence.

---

### Q3.2: Lambda Architecture

**Question:** An e-commerce company needs fraud detection that responds in <100ms AND a nightly customer segmentation model. Explain why you would use Lambda Architecture.

**Answer:**

**Lambda Architecture** runs **two parallel pipelines:**

| Layer | Purpose | Latency | Use Case |
|-------|---------|---------|----------|
| **Batch layer** | Accuracy. Processes all historical data nightly. | Hours | Customer segmentation model training, feature aggregation, daily reports |
| **Speed layer (streaming)** | Freshness. Processes real-time events. | Milliseconds | Fraud detection, real-time transaction scoring |
| **Serving layer** | Merges results from both layers. | — | Feature Store combines batch features + streaming features for model inference |

**Why both are needed:**
- Fraud detection can't wait for a nightly batch job — by then, the fraudulent transaction is already processed
- Customer segmentation doesn't need millisecond updates — nightly recomputation is sufficient
- Some features combine both: "average transaction amount last 30 days" (batch) + "transactions in last 10 minutes" (streaming) — both are used by the fraud model

---

### Q3.3: Concept Drift vs Data Drift

**Question:** A ride-sharing company's demand prediction model was trained pre-pandemic. After COVID lockdowns end:
- (a) People return to offices but with hybrid work patterns (3 days office, 2 days home)
- (b) A new competitor launches and captures 30% of riders

Classify each as data drift or concept drift.

**Answer:**

**(a) Hybrid work patterns — CONCEPT DRIFT**
- The **features** (day of week, time, location) have the SAME distributions as before
- But the **relationship** between these features and demand has changed: Tuesday at 8 AM no longer means the same demand as before, because only 60% of workers commute on Tuesday now
- P(X) similar, P(Y|X) changed → Concept drift
- The model must relearn what "Tuesday morning" means in the new normal

**(b) New competitor captures 30% of riders — DATA DRIFT + CONCEPT DRIFT (both)**
- **Data drift:** The company's rider population has changed (the 30% who left may be a specific demographic — e.g., price-sensitive riders). Feature distributions shift.
- **Concept drift:** For the remaining 70% of riders, their behaviour may not change. But demand is 30% lower for the same conditions. P(demand | features) has changed because overall market supply/demand shifted.
- Both types of drift are present simultaneously.

---

### Q3.4: Class Imbalance

**Question:** A cybersecurity company builds an intrusion detection system. Normal traffic = 99.99%, attacks = 0.01%. The model achieves 99.99% accuracy. Is this good?

**Answer:**

**No. This is the accuracy paradox at its most extreme.**

A model that predicts "normal" for every packet achieves 99.99% accuracy while catching **zero attacks**. It is completely useless.

**Why accuracy fails:**
- With 0.01% positive rate, even a random model gets >99.99% accuracy
- The 0.01% is the class the company actually cares about

**Better metrics:**
- **Recall (detection rate):** Of all attacks, what % were caught? Must be >99%
- **Precision:** Of flagged traffic, what % is actually an attack? (Acceptable to be low — better to investigate false positives than miss an attack)
- **F2 Score:** Weighted F-score that values recall more than precision (appropriate when missing a positive is very costly)
- **Alert volume:** Total number of alerts per day. Even high recall is useless if it generates 10,000 false alerts — analysts can't review them all.

**Handling class imbalance:**
1. **SMOTE** — Synthetic Minority Oversampling Technique
2. **Class weights** — Tell the model that missing an attack is 10,000x worse than a false alarm
3. **Anomaly detection approach** — Train only on normal traffic (Autoencoders, Isolation Forest). Any deviation from "normal" is flagged.
4. **Stratified sampling** — Ensure training batches contain sufficient attack examples
5. **Ensemble methods** — Random Forest, XGBoost handle imbalance better than logistic regression

---

### Q3.5: Explainability — SHAP vs LIME

**Question:** A loan approval model rejects an applicant. The bank is required by regulation to explain why. Compare SHAP and LIME for this purpose.

**Answer:**

| Aspect | SHAP (SHapley Additive exPlanations) | LIME (Local Interpretable Model-agnostic Explanations) |
|--------|------|------|
| **Approach** | Uses game theory (Shapley values) to compute each feature's contribution to the prediction | Perturbs the input, observes prediction changes, fits a simple interpretable model locally |
| **Scope** | Per-prediction and global | Per-prediction only |
| **Consistency** | Mathematically consistent — SHAP values always sum to the prediction | Can vary between runs (due to random perturbation) |
| **Speed** | Slower (especially kernel SHAP) | Faster |
| **Output** | Feature contribution values (positive/negative) that add up to the prediction | Feature importance weights for a local linear model |
| **Best for** | Regulatory compliance (consistent, auditable) | Quick exploration, prototyping |

**For the loan rejection, SHAP is preferred because:**
1. Regulatory compliance requires **consistent** explanations — the same input must always produce the same explanation
2. SHAP values are **additive** — you can show: "Base approval probability: 60%. Income (-15%), debt-to-income ratio (-20%), employment tenure (+8%), credit score (-12%) → Final: 21% → Rejected"
3. SHAP has **theoretical guarantees** (fairness, consistency, local accuracy) that LIME lacks
4. SHAP supports **global analysis** — the bank can also show regulators which features matter most across all decisions

**Example explanation for the applicant:**
```
Your loan application was not approved. The main factors were:
  ↓ Debt-to-income ratio: 0.65 (our limit is 0.45) — strongest negative factor
  ↓ Credit score: 620 (average approved applicant: 710)
  ↑ Employment tenure: 8 years (positive factor)
  ↓ Requested amount relative to income: high
```

---

### Q3.6: RAG vs Fine-Tuning

**Question:** A law firm wants to build an AI assistant. Lawyers ask questions about specific case law and internal legal memos. Should they use RAG or fine-tuning?

**Answer:**

**RAG (Retrieval-Augmented Generation) is the clear choice.**

| Criterion | RAG | Fine-Tuning |
|-----------|-----|-------------|
| **Data freshness** | ✅ New cases/memos available immediately (just add to index) | ❌ Must retrain to incorporate new data (hours/days) |
| **Hallucination risk** | ✅ Lower — answers grounded in retrieved documents with citations | ⚠️ Higher — model may fabricate case law or citations |
| **Traceability** | ✅ Every answer cites the source document and passage | ❌ No attribution — model "just knows" |
| **Data privacy** | ✅ Documents stay in the firm's secure vector database | ⚠️ Fine-tuning requires sending data to model provider (or self-hosting) |
| **Cost** | ✅ No training compute, just indexing | ❌ GPU compute for fine-tuning, regular retraining |
| **Accuracy on specific facts** | ✅ Retrieves exact text from the document | ⚠️ Model may paraphrase incorrectly or conflate cases |

**Why RAG works here:**
1. Legal questions need **exact citations** — RAG provides them
2. New case law is published constantly — RAG can index new documents in minutes
3. Lawyers need to **verify** answers against source material — RAG shows the source
4. The firm's internal memos are confidential — RAG keeps them local
5. Hallucinating a legal precedent could be malpractice — RAG reduces this risk

**When fine-tuning WOULD be better:**
- If the goal is to adopt a specific legal writing style (not factual recall)
- If you need the model to understand domain-specific terminology deeply
- If the task is generation (writing contracts) rather than retrieval (answering questions)

**Best approach: RAG for factual Q&A + Fine-tuning for writing style (if needed)**

---

### Q3.7: Build vs Buy Decision Framework

**Question:** For each scenario, recommend Build, Buy, Open Source, or API:

| Scenario | Answer | Reasoning |
|----------|--------|-----------|
| A hospital needs OCR to digitize handwritten prescriptions | **Buy** (Google Document AI, AWS Textract) | Specialized problem. Vendors have trained on millions of handwritten documents. Building this in-house would require massive annotated datasets. Not a competitive advantage. |
| A hedge fund wants to predict stock prices from financial data | **Build** | Core competitive advantage. Proprietary data and models are the fund's entire business. Any edge must be kept secret. |
| A startup needs a chatbot for their customer FAQ page | **API** (ChatGPT API + RAG) | Speed to market is critical for a startup. API is cheapest and fastest to implement. Not a differentiator. |
| A car manufacturer needs autonomous driving computer vision | **Build** | Core product feature. Safety-critical. Must have full control over model behaviour, training data, and testing. Tesla, Waymo all build in-house. |
| A retail chain wants to forecast demand for 50,000 SKUs | **Build + Open Source** | Business-specific (their stores, their products). Use open-source frameworks (GluonTS, Prophet). No vendor has their data. |
| A bank needs to monitor transactions for money laundering | **Buy + Build** | Buy a vendor solution (Actimize, Featurespace) for baseline AML detection. Build custom models for bank-specific patterns. Regulatory requirement means the bank must have capability regardless. |

---

### Q3.8: Monitoring — What Goes Wrong in Production

**Question:** List 6 things that can go wrong with an ML model in production that would NOT be caught by standard software monitoring (uptime, CPU, error rates).

**Answer:**

Standard software monitoring only checks if the system is running. These ML-specific failures are **silent** — the system returns HTTP 200 with a valid prediction, but the prediction is wrong.

| # | Silent Failure | Why Standard Monitoring Misses It | How to Detect |
|---|---------------|----------------------------------|--------------|
| 1 | **Data drift** — Input feature distributions shift | System receives valid data, just different from training | Monitor feature distributions with PSI. Compare rolling statistics to training baseline. |
| 2 | **Concept drift** — Same features, different outcomes | System performs normally. No data errors. But the real-world relationship has changed. | Monitor predicted vs actual outcomes over time (requires ground truth with lag). |
| 3 | **Stale features** — Data pipeline delivers old data | Feature values are valid numbers, just from yesterday. No error raised. | Monitor data freshness timestamps. Alert if feature_timestamp - current_time > threshold. |
| 4 | **Prediction distribution shift** — Model suddenly predicts 50% positive instead of usual 5% | Every individual prediction looks valid. But the aggregate pattern is wrong. | Monitor prediction distribution (mean, percentiles) over rolling windows. Alert on significant shifts. |
| 5 | **Training-serving skew** — Feature computation differs between training and serving | System returns a number. The number is just computed differently than in training. | Compare feature values from training pipeline vs serving pipeline on the same input. Log and compare periodically. |
| 6 | **Feedback loop degradation** — Model influences its own training data (see Q2.7) | Performance degrades gradually. No error. No drift in inputs. Model just gets worse over time. | Monitor actual outcomes for model-influenced vs non-influenced groups. Random audits. |

---

### Q3.9: ETL vs ELT

**Question:** A company is migrating from an on-premise data warehouse to a cloud data lake for their ML pipeline. Should they use ETL or ELT? Why?

**Answer:**

**ELT (Extract-Load-Transform) is the better choice for cloud + ML.**

| Aspect | ETL (Extract-Transform-Load) | ELT (Extract-Load-Transform) |
|--------|-----|-----|
| **Process** | Transform BEFORE loading to warehouse | Load raw data first, transform LATER |
| **Best for** | On-premise, structured data, traditional BI | Cloud, ML/AI, flexible exploration |
| **Raw data preserved?** | ❌ No — only transformed data is stored | ✅ Yes — raw data always available |
| **Schema** | Schema-on-write (must define schema upfront) | Schema-on-read (define schema when querying) |
| **Flexibility** | Low — if you need a new feature, re-run ETL from source | High — raw data is available for any future transformation |
| **Cost** | Higher compute for transformation before loading | Cheaper storage (cloud), compute only when querying |
| **ML benefit** | Must know features in advance | Can experiment with new features from raw data later |

**Why ELT for ML:**
1. **Feature engineering is iterative** — data scientists constantly create new features. With ETL, they'd need to re-run the pipeline from source every time. With ELT, raw data is in the lake ready for new transformations.
2. **Cloud storage is cheap** — storing raw data in S3/GCS costs very little
3. **Cloud compute is elastic** — spin up Spark/BigQuery to transform on demand
4. **Reproducibility** — raw data is preserved, so you can reconstruct any feature at any point in time

---

### Q3.10: A/B Testing for ML Models

**Question:** You've trained a new recommendation model (Model B) that outperforms the current model (Model A) in offline evaluation. Before full deployment, you want to A/B test. Design the A/B test.

**Answer:**

**A/B Test Design:**

| Component | Design |
|-----------|--------|
| **Control (A)** | Current production recommendation model |
| **Treatment (B)** | New candidate recommendation model |
| **Randomization unit** | User-level (each user consistently sees one model throughout the test — not request-level) |
| **Split ratio** | 90% Model A / 10% Model B (start small, increase if B looks safe) |
| **Duration** | Minimum 2 weeks (captures weekday/weekend patterns + sufficient sample size) |
| **Primary metric** | Revenue per user (captures both engagement and purchase quality) |
| **Secondary metrics** | CTR, conversion rate, average order value, items per session, return rate |
| **Guardrail metrics** | Page load time (ensure Model B isn't slower), bounce rate (ensure recommendations aren't driving users away), customer complaints |
| **Statistical significance** | p < 0.05 with power = 0.80. Pre-compute required sample size based on expected effect size. |
| **Novelty effect** | Wait at least 1 week before analyzing — users may initially engage with new recommendations out of curiosity, not true preference |

**Important safeguards:**
1. **No peeking** — Don't check results daily and stop when they look good (p-hacking). Pre-commit to the test duration.
2. **Segment analysis** — Check if Model B helps some users but hurts others (e.g., better for frequent shoppers but worse for new users)
3. **Cannibalization check** — Is Model B increasing revenue or just shifting it between products?
4. **Long-term effects** — Short-term CTR improvement may not translate to long-term customer satisfaction. Consider running a longer holdout for a subset.

---

## Summary: Key Concepts Across All Practice Questions

| Concept | Where Tested |
|---------|-------------|
| AI Categories (6 types) | PQ1-A |
| Lambda Architecture (Batch + Streaming) | PQ1-B, Q3.2 |
| Feature Engineering & Leakage | PQ1-C, PQ2.1-2.3 |
| Model Strategy (ML vs DL vs LLM vs RAG) | PQ1-D, Q3.6 |
| System Architecture (5 layers) | PQ1-E |
| Build vs Buy vs API | PQ1-F, Q3.7 |
| Production Risks | PQ1-G, PQ2.7-2.8 |
| Governance (Privacy, Bias, Explainability) | PQ1-H, Q3.5 |
| KPIs and Metrics | PQ1-I, PQ2.4 |
| Accuracy Paradox / Class Imbalance | PQ2.4, Q3.4 |
| Temporal Splitting | PQ2.6 |
| Data Drift vs Concept Drift | PQ2.9, Q3.3 |
| Feature Store & Training-Serving Skew | Q3.1 |
| ETL vs ELT | Q3.9 |
| RAG vs Fine-Tuning | Q3.6 |
| A/B Testing | Q3.10 |
| Production Monitoring (ML-specific) | Q3.8 |
| Production Readiness Assessment | PQ2.10 |
