# Introduction to AI Systems - Mid-Semester Practice Questions
## SS ZG662 - Mid Semester Exam Preparation

---

## Q1. Predictive Maintenance for Retail Logistics Network [7 Marks]

A large retail company operates 500 distribution centers across multiple regions. It wants to predict transportation vehicle failures to minimize supply chain disruptions. The company has:
- 5 years of vehicle telemetry data (engine temperature, fuel consumption, vibration sensors)
- GPS tracking and route history
- Maintenance records and repair logs
- Driver behavior data (speed, acceleration patterns)

The management proposes: "Use Deep Learning on all available data to predict failures before they happen."

### Design an AI solution and determine:
a) Which parts require AI/ML/DL and which can be solved using traditional rules? (1.5 marks)
b) What data should be used for each component and why? (1.5 marks)
c) Where traditional ML might be preferable to deep learning? (1 mark)
d) How would you measure whether the AI system actually reduces disruptions? (1.5 marks)
e) What would make you reject the proposed deep-learning solution? (1.5 marks)

### Answer:

**a) Rules vs ML/DL vs. NLP [1.5 marks]**
- **Rules**: Threshold-based alerts (engine temperature > 90°C, fuel efficiency drops > 15%, brake wear exceeds limits), scheduled preventive maintenance reminders based on mileage.
- **Traditional ML**: Predict failure risk using structured telemetry and maintenance history (Random Forest, Gradient Boosting) with features like temperature trends, fuel efficiency degradation, repair frequency patterns.
- **Deep Learning**: Analyze complex time-series patterns in sensor streams, multi-dimensional vibration patterns, or multi-modal data (sensor + GPS + driver behavior) for early anomaly detection.
- **NLP**: Extract failure symptoms from maintenance notes and driver comments to identify recurring issues.

**b) Data required for each component [1.5 marks]**
- **Failure prediction model**: Historical telemetry (5 years), maintenance dates, repair types, vehicle age/model, mileage—to train on known failure patterns.
- **Real-time monitoring**: Live telemetry streams with timestamps for continuous anomaly detection.
- **Contextual factors**: Route difficulty, cargo weight, driver behavior, weather conditions—to explain failures and reduce false positives.
- **Driver/maintenance records**: Comments on observed issues, repair outcomes—to validate patterns and handle data quality issues.

**c) Traditional ML vs. DL [1 mark]**
- Traditional ML (Random Forest/Gradient Boosting) is preferable for:
  - Structured telemetry with limited labeled failures (few complete failure examples).
  - Better interpretability: maintenance teams can understand why a vehicle is flagged.
  - Lower computational cost and faster inference across 500 vehicles.
  - Sufficient for detecting anomalies in numerical sensor patterns.
- DL may be overkill without very large labeled failure datasets and insufficient improvement to justify complexity.

**d) Measure actual disruption reduction [1.5 marks]**
- **Establish baseline**: Current disruption rate, downtime cost, replacement cost, emergency repairs.
- **Pilot deployment**: Deploy on 50 vehicles, compare with control group of 50 vehicles using existing practices.
- **Metrics**:
  - Percentage reduction in unexpected breakdowns.
  - Reduction in emergency repairs vs. planned maintenance.
  - Cost per disruption event avoided.
  - False alarm rate (unnecessary maintenance triggered).
- **Control variables**: Vehicle age, route type, driver experience, seasonal patterns.
- **Duration**: Run pilot for at least 6 months to capture seasonal variations.

**e) Reasons to reject DL [1.5 marks]**
- No meaningful improvement over traditional ML or threshold-based rules.
- Insufficient representative failure data (very few complete failure examples).
- Excessive false alarm rates leading to unnecessary maintenance costs.
- Unacceptable latency for deployment across 500 vehicles.
- Model requires expensive GPUs, making total cost higher than savings.
- Lack of interpretability makes maintenance teams distrust predictions.
- Safety concerns if model misses critical failures or triggers false positives.

---

## Q2. Healthcare Analytics Platform - AI Architecture Design [10 Marks]

A hospital network wants to build an intelligent platform for patient care optimization. It needs to integrate:
- Electronic Health Records (EHRs) from 20 hospitals
- Real-time patient monitoring (ICU sensors, vital signs)
- Medical imaging data (X-rays, CT scans)
- Prescription and medication records
- Patient feedback and symptom logs
- Clinical guidelines and treatment protocols

The platform should support:
- Predicting patient deterioration risk
- Recommending treatment options
- Identifying potential drug interactions
- Analyzing imaging for anomalies

### Design the complete architecture using:
**Data Layer → Model Layer → Application Layer → Infrastructure Layer → Monitoring Layer**

For each layer, specify: Components, Data flow, Security controls, Scaling requirements, Failure handling.

Also explain where an ML/AI model should NOT be trusted in healthcare.

### Answer:

**Architecture Design [10 marks total]**

#### **1. Data Layer (2 marks)**

**Components:**
- Data ingestion connectors for each hospital EHR system (HL7/FHIR standards)
- Real-time streaming for vital signs and sensor data (Kafka/Pub-Sub)
- Batch ETL for imaging and historical records
- Data validation and schema enforcement
- De-identification and anonymization pipeline (HIPAA compliance)

**Data Flow:**
```
EHRs (batch) → Ingestion Layer → Validation → De-identification → Data Lake (raw)
Real-time Sensors → Streaming Ingestion → Transformation → Real-time Data Store
Images → Object Storage → Metadata Extraction → Indexed Repository
```

**Security Controls:**
- Encryption in transit (TLS) and at rest (AES-256)
- Role-based access control (RBAC) with audit logging
- De-identification of PII before processing
- Data retention policies compliant with healthcare regulations

**Scaling & Failure Handling:**
- Horizontal scaling with load balancing for ingestion
- Automated backups and disaster recovery (RTO < 4 hours, RPO < 1 hour)
- Dead letter queues for failed ingestion events
- Data quality checks with alerting on anomalies

---

#### **2. Model Layer (2 marks)**

**Components:**
- Deterioration risk model (LSTM/GBM on time-series vital signs + EHR features)
- Treatment recommendation engine (decision trees with clinical rules + ML)
- Drug interaction detector (rule-based + ML for novel interactions)
- Medical imaging analyzer (CNN for anomaly detection, radiologist augmentation)
- Feature store with pre-computed patient risk scores

**Data Flow:**
```
Validated Data → Feature Engineering → Feature Store
Feature Store → Model Serving → Predictions + Confidence Scores
Real-time Predictions → Application Layer with Explanations
```

**Security Controls:**
- Model versioning and audit trail
- Approval workflow for model updates
- Explainability tools for clinician review (SHAP, LIME)
- Fairness testing across demographics

**Scaling & Failure Handling:**
- Model serving with caching (Redis) for frequently accessed predictions
- A/B testing framework for new models
- Fallback to rule-based recommendations if ML model unavailable
- Shadow mode deployment before production use

---

#### **3. Application Layer (2 marks)**

**Components:**
- Clinical decision support dashboard for physicians
- Mobile app for patient monitoring and alerts
- Integration with hospital workflows and EHR systems
- Alert management system (risk notifications, triage)
- Audit trail and user action logging

**Data Flow:**
```
Model Predictions → Clinician Review → Action Decisions → EHR Updates
Patient Alerts → Mobile App → Patient Acknowledgment → Feedback Loop
```

**Security Controls:**
- Authentication (SSO with hospital credentials)
- Authorization (doctors see only their patients)
- Encrypted API communication
- All user actions logged for compliance

**Scaling & Failure Handling:**
- Stateless application servers with auto-scaling
- Database replication for high availability
- Cache layer for frequently accessed patient data
- Graceful degradation if prediction service unavailable

---

#### **4. Infrastructure Layer (2 marks)**

**Components:**
- Containerized services (Kubernetes) for orchestration
- Data warehouse (BigQuery, Snowflake) for analytics
- Message queue (Kafka) for real-time data
- Model serving platform (Seldon, KServe, or custom)
- Logging and observability stack (ELK, Prometheus, Grafana)

**Scaling & Failure Handling:**
- Multi-region deployment for redundancy
- Auto-scaling policies based on CPU/memory/prediction load
- Health checks and automatic restarts
- Load balancing across instances

---

#### **5. Monitoring Layer (1 mark)**

**Components:**
- Real-time dashboards for system health
- Model performance monitoring (accuracy, drift, calibration)
- Data quality monitoring (missing values, anomalies)
- Clinical outcome tracking (prediction accuracy vs. actual patient outcomes)
- Alert system for anomalies

**Metrics Tracked:**
- Prediction latency and throughput
- Model drift (input/output distribution changes)
- False positive/negative rates for deterioration alerts
- Clinical outcome correlation
- System uptime and error rates

---

#### **Where NOT to trust AI/ML [2 marks]**

**Critical decisions requiring human oversight:**
1. **Treatment authorization**: AI can recommend, but clinicians must approve all treatment plans. Never execute medications based solely on AI recommendations.

2. **Patient consent**: Never use AI to override patient preferences or autonomy, even if statistically beneficial.

3. **Diagnosis**: AI can flag potential issues, but only qualified radiologists/pathologists make final diagnostic decisions.

4. **Emergency decisions**: In critical moments (cardiac arrest, sepsis), experienced physicians override AI suggestions based on clinical judgment.

5. **Ethical/privacy concerns**: AI must not infer sensitive information (genetic disorders, mental health) beyond patient authorization.

6. **Palliative care decisions**: End-of-life decisions require human compassion and family consultation, not AI optimization.

7. **Medication changes**: Never change prescriptions without pharmacist and physician review, even if AI detects interactions.

8. **Liability**: The hospital remains liable for AI-assisted decisions; AI cannot replace professional accountability.

---

## Q3. Recommendation System Model Performance Degradation [7 Marks]

An e-commerce company deploys an ML model to recommend products to customers. The model performed well in development (precision: 0.85, recall: 0.78), but after four months in production, business metrics (revenue per user, conversion rate) have dropped significantly despite similar model accuracy scores.

### Identify at least seven possible causes across the AI lifecycle and design a production lifecycle that detects and handles these problems.

### Answer:

**A. Seven Possible Causes of Performance Degradation [3.5 marks]**

Each cause: 0.5 marks

1. **Data Drift**: Customer preferences have shifted (seasonal changes, new market segments, pandemic-driven behavior changes). Model was trained on historical data that no longer represents current population.

2. **Concept Drift**: Business context changed—competitors launched better products, user demographics shifted, or customer priorities evolved (e.g., price sensitivity increased).

3. **Feedback Loop/Selection Bias**: Model recommendations influence what users see, creating a feedback loop where recommendations only reflect model biases rather than true preferences.

4. **Training-Serving Skew**: Features computed in production differ from training features. E.g., training used historical price, but production uses real-time dynamic pricing.

5. **Incomplete Labels**: Recommendation feedback (clicks, purchases) is delayed or missing. User didn't purchase because recommendation was late, not because it was irrelevant.

6. **Feature Leakage**: Training data included information unavailable at prediction time (e.g., future demand, inventory levels known after purchase).

7. **Catalog Changes**: Product catalog expanded significantly; model has no embeddings or features for new products, leading to poor recommendations.

8. **Adversarial/Gaming**: Suppliers manipulate product reviews or use bots to inflate engagement, poisoning training data.

9. **External Factors**: Supply chain disruptions, stock-outs, shipping delays, or price changes affecting recommendations' relevance.

---

**B. Production Lifecycle to Detect and Handle Degradation [3.5 marks]**

**1. Data Ingestion & Validation (0.5 marks)**
- Validate recommendation requests: user_id, product_id, context (time, device).
- Check feature distributions against training baseline.
- Monitor for missing values, outliers, format changes.
- Alert if feature values deviate significantly (e.g., price distribution shifts 20%+).

**2. Prediction & Logging (0.5 marks)**
- Log all predictions: user_id, recommended products, scores, model version, timestamp.
- Store context: user history, session data, inventory status.
- Record serving latency and infrastructure metrics.
- Enable replay: re-score past requests if model changes.

**3. Outcome Tracking (0.5 marks)**
- Capture user actions: views, clicks, purchases, cart additions, returns.
- Delayed labels: Wait 30+ days for purchase labels to stabilize.
- Business metrics: Revenue per user, conversion rate, engagement time.
- Comparison: Track both with-model and without-model (A/B test control group).

**4. Monitoring & Alerting (0.5 marks)**
- **Performance metrics**: Precision, recall, AUC on recent held-out data.
- **Business metrics**: Revenue per recommendation, conversion rate, engagement.
- **Data drift**: Compare feature distributions (KL divergence) week-over-week.
- **Concept drift**: Monitor ranking changes (are top products different?).
- **Feedback**: Analyze user rejection rates, cart abandonment after recommendation.
- **Thresholds**: Alert if revenue drops > 10%, precision drops > 5%, or drift exceeds baseline.

**5. Diagnosis (0.5 marks)**
- Root cause analysis: Is it data quality, concept drift, feedback loop, or business change?
- Offline analysis: Re-score historical requests with current data to isolate issues.
- Segment analysis: Is degradation uniform or specific to regions, user cohorts, or product categories?
- Error analysis: Manually inspect failed recommendations to identify patterns.

**6. Retraining (0.5 marks)**
- **Frequency**: Retrain weekly or when drift detected.
- **Data selection**: Use only recent, verified labels (last 90 days).
- **Avoid leakage**: Exclude information unavailable at prediction time.
- **Catalog updates**: Re-embed new products before retraining.
- **Validation set**: Hold out data from most recent period to evaluate degradation.

**7. Validation & Testing (0.5 marks)**
- **Offline validation**: Compare new model against current model on recent test data.
- **Threshold checks**: New model must maintain precision > 0.80, no business metric regression.
- **Fairness testing**: Ensure recommendations don't discriminate by demographic factors.
- **A/B testing**: Shadow deploy or canary rollout before full deployment.

**8. Deployment & Rollback (0.5 marks)**
- **Gradual rollout**: Deploy to 5% of users, monitor for 1 week.
- **Predefined thresholds**: If revenue drops > 5%, trigger automatic rollback.
- **Manual gates**: Business approval before expanding beyond canary.
- **Rollback plan**: Revert to previous model version within 1 hour if issues detected.

---

## Q4. AI Transformation Project Selection - Decision Framework [6 Marks]

A mid-sized financial services company wants to identify top opportunities for AI transformation. Available budget supports only two pilot projects. Evaluate the following candidates using a structured framework:

| Candidate | Business Value | Data Availability | Complexity/Risk | Impact Timeline |
|-----------|----------------|-------------------|-----------------|-----------------|
| Fraud Detection | Very High | High | Medium | 2-3 months |
| Customer Lifetime Value (CLV) Prediction | High | High | Low | 1-2 months |
| Portfolio Risk Analysis | Very High | Medium | Very High | 6+ months |
| Document Processing Automation | High | Low | High | 4-5 months |
| Regulatory Compliance Monitoring | High | Medium | Very High | 6+ months |

### Your task:
a) Create a decision framework for evaluating AI projects (2 marks)
b) Select two projects and justify your selection (2 marks)
c) Defend your decision to the CEO in one paragraph (1 mark)
d) Discuss trade-offs and rejected projects (1 mark)

### Answer:

**a) Decision Framework for AI Project Evaluation [2 marks]**

**Framework Components:**

1. **Business Value Score** (Weight: 35%)
   - Revenue impact: Revenue increase, cost reduction, risk mitigation
   - Strategic fit: Alignment with company roadmap
   - Competitive advantage: Does it differentiate from competitors?
   - Scoring: Very High (100) → High (75) → Medium (50) → Low (25)

2. **Data Readiness Score** (Weight: 25%)
   - Data availability: Do we have sufficient historical/current data?
   - Data quality: Is data clean, well-structured, documented?
   - Time to data: How quickly can we prepare data?
   - Scoring: High (100) → Medium (70) → Low (40)

3. **Implementation Feasibility** (Weight: 25%)
   - Technical complexity: Model difficulty, infrastructure needs
   - Organizational readiness: Team skills, tools available
   - Timeline to MVP: Can we pilot in 1-3 months?
   - Reverse score: Low complexity (100) → Medium (70) → High/Very High (40)

4. **Risk & Reliability** (Weight: 15%)
   - Regulatory/compliance risk: Are there legal constraints?
   - Operational risk: Can we handle model failures gracefully?
   - Interpretability: Do stakeholders trust the model?
   - Scoring: Low Risk (100) → Medium (70) → High/Very High (40)

**Combined Score Formula:**
```
Project Score = (Business Value × 0.35) + (Data Readiness × 0.25) 
              + (Feasibility × 0.25) + (Risk × 0.15)
```

---

**b) Project Selection [2 marks]**

**Scores Calculated:**

| Project | Business (100) | Data (70) | Feasibility (100) | Risk (100) | **Final Score** |
|---------|---|---|---|---|---|
| **Fraud Detection** | 100 | 100 | 70 | 70 | **85.5** ✓ |
| **CLV Prediction** | 75 | 100 | 100 | 100 | **90.0** ✓ |
| Portfolio Risk | 100 | 40 | 40 | 40 | **61.0** |
| Document Processing | 75 | 40 | 40 | 60 | **57.5** |
| Compliance Monitoring | 75 | 70 | 40 | 50 | **60.3** |

**Selection: CLV Prediction (#1 score: 90.0) and Fraud Detection (#2 score: 85.5)**

**Justification:**
- **CLV Prediction**: Highest overall score. Data is abundant, implementation complexity is low, timeline is short (1-2 months), and business value is strong (enables targeted retention, cross-sell strategies). Low risk of model failures or regulatory issues.
- **Fraud Detection**: Very high business value (risk mitigation, compliance), good data availability, and manageable timeline (2-3 months). Though slightly more complex than CLV, the ROI justifies it. Both projects can start simultaneously without resource conflicts.
- **Why not others**: Portfolio Risk (6+ months timeline too long, data quality issues, very high complexity—high risk for first projects); Document Processing (limited data, long timeline, high uncertainty); Compliance Monitoring (regulatory complexity, slower ROI).

---

**c) CEO Defense (1 mark)**

"I recommend we pilot CLV Prediction and Fraud Detection simultaneously. CLV Prediction delivers quick wins in 1-2 months—our data is ready, the model is straightforward, and it directly enables revenue-focused retention strategies. Fraud Detection has the highest risk mitigation value and strong data availability, delivering measurable cost savings within 2-3 months. Together, they address both revenue growth and risk, demonstrate tangible business value, and build organizational confidence in AI. We avoid overambitious projects like Portfolio Risk (6+ months, very high complexity) until we've proven success on these foundational pilots. These two projects are low-risk, high-impact, and achievable within Q1."

---

**d) Trade-offs and Rejected Projects [1 mark]**

**Trade-offs:**
- **Speed vs. Impact**: CLV & Fraud Detection are fast but moderate impact. Portfolio Risk has higher individual impact but would delay results by 6+ months.
- **Data quality**: Fraud Detection trades off slightly higher complexity for very strong business value; portfolio risk would require significant data cleaning before starting.
- **Resource allocation**: Parallelizing both projects uses ~70% of AI team capacity, leaving 30% for support and infrastructure (acceptable).

**Rejected Projects Rationale:**
- **Portfolio Risk Analysis**: 6+ months to first results is too slow for building organizational buy-in; data quality issues require 2+ months preprocessing; regulatory complexity increases deployment risk. Candidate for phase 2 after proving CLV/Fraud models.
- **Document Processing**: Limited structured data available (data availability = Low); high uncertainty on ROI; 4-5 month timeline with risk of extended delays. Better suited after foundational projects succeed.
- **Compliance Monitoring**: High regulatory complexity and ambiguous business value compared to Fraud Detection; longer timeline (6+ months) without clear ROI proof.

---

## Q5. Feature Engineering for Loan Default Prediction [6 Marks]

A bank wants to predict loan default risk to improve credit decisions and reduce portfolio losses. Available data:

```
Borrower: Age, Salary, Employment_Years, Credit_Score, Loan_Amount, 
Loan_Term_Months, Interest_Rate, Marital_Status, Number_of_Dependents

Account: Account_Opening_Date, Previous_Loans_Count, 
Previous_Default_Count, Total_Paid_Amount, Total_Outstanding, 
Last_Payment_Date, Payment_Frequency
```

### Design at least 10 meaningful features for predicting default. For each feature, explain:
- How it is calculated
- Why it may predict default risk
- Whether it could introduce data leakage

### Answer:

**10 Feature Engineering Examples [6 marks total]**

**1. Debt-to-Income Ratio**
- **Calculation**: (Loan_Amount / Salary) × 100
- **Predictive Relevance**: Higher ratio indicates borrower is over-leveraged; they may struggle to meet payments if income drops or unexpected expenses arise.
- **Leakage Risk**: None—all data available at loan origination.

**2. Monthly Payment Burden**
- **Calculation**: (Loan_Amount × Interest_Rate / 12) / Salary
- **Predictive Relevance**: Shows percentage of monthly income needed for loan repayment. Ratios > 30% historically correlate with higher default rates.
- **Leakage Risk**: None—calculated from origination data.

**3. Employment Stability**
- **Calculation**: Employment_Years; Encoded as: 0-1 years (risky), 1-5 years (medium), 5+ years (stable)
- **Predictive Relevance**: Longer employment suggests stable income; frequent job changes increase default risk.
- **Leakage Risk**: None—known at application time.

**4. Credit History Performance**
- **Calculation**: (Previous_Loans_Count - Previous_Default_Count) / Previous_Loans_Count (if > 0, else 0)
- **Predictive Relevance**: Historical repayment success is the strongest predictor of future behavior.
- **Leakage Risk**: None—uses historical data available before loan decision.

**5. Recent Payment Delinquency Trend**
- **Calculation**: Days since Last_Payment_Date (relative to current date at observation)
- **Predictive Relevance**: Increasing delays indicate financial stress or behavioral change; early warning signal.
- **Leakage Risk**: **YES - LEAKAGE**: If using payment history after loan disbursement, this is post-outcome information. **Solution**: Only use data up to loan origination or observation date before default is known.

**6. Loan Age**
- **Calculation**: (Current_Date - Loan_Disbursement_Date) in months
- **Predictive Relevance**: Default risk varies by loan tenure. Early-stage defaults (first 6 months) vs. late defaults (after 3 years) have different causes.
- **Leakage Risk**: None—observed at prediction time. **Caution**: Use consistently in training and production.

**7. Outstanding-to-Loan Ratio**
- **Calculation**: Total_Outstanding / Loan_Amount
- **Predictive Relevance**: Shows repayment progress. Ratio close to 1.0 (early in loan) vs. 0.1 (nearly paid) suggests different risk profiles.
- **Leakage Risk**: **YES - POTENTIAL LEAKAGE**: This ratio depends on current payment status. If used post-default decision, it's outcome-dependent. **Solution**: Use only up to a fixed observation date before labels are determined.

**8. Income Volatility**
- **Calculation**: Coefficient of variation of salary over last 3 years (if available), else assume stable
- **Predictive Relevance**: Highly variable income increases default risk due to unpredictability.
- **Leakage Risk**: None—uses historical income data available before loan approval.

**9. Dependents-to-Income Burden**
- **Calculation**: (Number_of_Dependents × Estimated_Support_Cost) / Salary
- **Predictive Relevance**: More dependents = higher living expenses; reduces buffer for loan payments during hardship.
- **Leakage Risk**: None—known at application.

**10. Interest Rate Risk (Affordability)**
- **Calculation**: Interest_Rate relative to borrower's Credit_Score percentile; High_Rate_for_Score = Interest_Rate > 75th percentile for given credit band
- **Predictive Relevance**: Higher interest rates for similar creditworthiness suggest lender perceived additional risk; may indicate unobserved red flags.
- **Leakage Risk**: None—available at origination. **Note**: This is a market-based feature, not outcome-dependent.

**Alternative Features (Mention 2-3 more):**
- **Loan Purpose**: Personal loans default more than auto loans (0.5 mark).
- **Account Age**: Accounts older than 5 years show stability (0.5 mark).
- **Payment Frequency Consistency**: Borrowers with erratic payment history are riskier (0.5 mark).

---

## Q6. Data Pipeline Modernization with Streaming [8 Marks]

Your company currently processes customer transactions in a nightly batch ETL pipeline (daily at 11 PM). However, business stakeholders want to:
- Detect fraudulent transactions within seconds (real-time alerts)
- Update customer profiles in real-time for personalized recommendations
- Support both historical analytics and real-time AI/ML features

Current architecture: Transactional database → Batch ETL (nightly) → Data warehouse → Reports & ML features

### Design the modernized architecture to support both streaming and batch. Show:
a) How streaming and batch pipelines coexist (2 marks)
b) The flow of one transaction through both real-time and batch paths (2 marks)
c) Data consistency and eventual consistency handling (2 marks)
d) Monitoring and failure recovery (2 marks)

### Answer:

**a) Streaming + Batch Coexistence [2 marks]**

**Architecture:**

```
┌─────────────────────────────────────────────────────────────┐
│  Transaction Sources (POS, Online, Mobile)                 │
└──────────────────────┬──────────────────────────────────────┘
                       │
            ┌──────────┴──────────┐
            ▼                     ▼
      [Kafka Topic]          [Database]
    (Real-time Stream)      (System of Record)
            │
    ┌───────┴────────┬──────────────┐
    │                │              │
    ▼                ▼              ▼
[Stream Processing] [S3/Staging]  [Batch ETL]
(Spark Structured)  (Hourly flush) (Nightly)
    │                │              │
    ▼                ▼              ▼
[Real-time         [Data Lake]   [Data Warehouse]
 Feature Store]    (Raw Layer)    (Curated)
    │                              │
    ▼                              ▼
[ML Models]                     [Analytics]
(Fraud Detection)               (Reports)
```

**Key Components:**

1. **Event Sourcing (Kafka)**: All transactions published to immutable event log.
   - Partitioned by customer_id for ordering
   - Retention: 7 days for replay
   - Enables both real-time consumers and batch backfills

2. **Real-time Path**:
   - Stream processor (Spark Structured Streaming, Kafka Streams, or Flink)
   - Enriches transactions with customer profile, merchant data, real-time signals
   - Publishes to Redis/In-Memory Feature Store for low-latency access
   - Triggers fraud alerts within milliseconds

3. **Batch Path**:
   - Nightly ETL (11 PM): Reads raw transactions + events from S3 staging
   - Performs complex aggregations, historical joins, reconciliation
   - Writes to data warehouse (Snowflake, BigQuery)
   - Recomputes ML features using complete history

4. **Data Lake (S3/GCS)**:
   - Immutable transaction records (Parquet format)
   - Enables batch backfills and historical analysis
   - Hourly flush from Kafka to ensure no data loss

---

**b) Flow of One Transaction [2 marks]**

**Example: Customer "Alice" makes $500 online purchase at 3:15 PM**

**Real-time Path (Milliseconds):**
```
3:15:00.000 - Transaction initiated
           ↓
3:15:00.050 - Event published to Kafka (transaction_topic, partition: hash(alice_id))
           ↓
3:15:00.100 - Stream processor reads event
           ↓
3:15:00.200 - Enrichment (3 parallel lookups):
             • Alice's profile (age, income level, payment history) from Redis
             • Merchant category (online_retail) and risk score from DynamoDB
             • Real-time signals (last 5 transactions, velocity check) from Feature Store
           ↓
3:15:00.250 - Fraud detection model scores: 0.08 (low risk, threshold: 0.50)
           ↓
3:15:00.300 - Update Feature Store:
             • alice_last_transaction_amount = 500
             • alice_transaction_count_1h = 3
             • alice_merchant_category_frequency[online_retail] += 1
           ↓
3:15:00.400 - Return APPROVE to payment gateway
           ↓
3:15:01.000 - Recommendation engine uses updated profile:
             • Alice browsed laptops yesterday
             • Similar customers who bought from merchants X, Y, Z
             • Show personalized recommendation

**Batch Path (Night at 11:00 PM):**
```
11:00 PM - Nightly ETL triggers
         ↓
11:05 PM - Kafka flush: All events from past 24h written to S3/data lake
         ↓
11:10 PM - Read transactions from S3 + historical transactions from warehouse
         ↓
11:20 PM - Complex aggregations (Alice's transaction summary):
         • Total spending (daily, weekly, monthly)
         • Merchant diversity (how many different merchants)
         • Payment method changes
         • Geographic patterns (transaction locations)
         • Seasonal patterns (compared to same day last year)
         ↓
11:30 PM - Reconciliation:
         • Verify transaction count matches source
         • Match with cleared payments
         • Cross-check with customer service records
         ↓
11:45 PM - Write to Data Warehouse:
         • customer_daily_summary (alice, 500_today, 50_this_month)
         • transaction_detail (date, amount, merchant, category, status)
         • feature_batch (recalculated features for next day's training)
```

**Consistency Resolution:**
- Real-time predictions use fresh data (3s old)
- Batch uses complete history (reconciled, up to 24h delay)
- If real-time fraud threshold differs from batch audit, batch is source of truth for investigation

---

**c) Data Consistency & Eventual Consistency [2 marks]**

**Challenge**: Real-time system and batch system may temporarily disagree.

**Example Scenario**:
- 3:15 PM: Real-time system marks transaction as approved (fraud score: 0.08)
- 11:00 PM: Batch processing discovers Alice's profile was corrupted in stream (missing fraud flag from morning)
- Batch recalculates: fraud score should have been 0.55 (REJECT)

**Handling Consistency:**

**1. Consistency Model: Eventual Consistency with Audit Trail**
- Real-time system optimizes for latency (millisecond decisions)
- Batch system optimizes for accuracy (complete history, validated data)
- Both publish decisions to immutable audit log

**2. Conflict Resolution Strategy:**

| Scenario | Resolution | Owner |
|----------|-----------|-------|
| Real-time approves, batch disagrees | Batch is final authority; trigger audit investigation | Risk/Compliance |
| Real-time misses fraud, batch catches it | Flag transaction post-hoc; investigate feature store staleness | Fraud team |
| Data quality issue in stream | Pause stream, backfill from data lake, resume | Data Eng |

**3. Reconciliation Process (Nightly)**
```
Compare Real-time Decisions (Redis) vs. Batch Decisions (Warehouse)
├─ Matching: No action (97% of transactions)
├─ Diverging (High Value or Fraud):
│   ├─ Root cause analysis
│   ├─ Update feature definitions if needed
│   └─ Retrain model if systematic drift detected
└─ Missing data: Backfill from event log
```

**4. Handling Late/Out-of-Order Events**
```
Real-time Stream:
- Tumbling windows (5-minute windows)
- Grace period: Accept events 30 seconds late
- Watermark policy: Drop events > 1 minute late (publish summary anyway)
- Side output: Late events to DLQ for investigation

Batch:
- 24-hour retro window: Re-process last 24h if new events arrive
- State store: Maintains aggregate state; update if new events recalculate sums
```

---

**d) Monitoring & Failure Recovery [2 marks]**

**Monitoring:**

**1. Real-time Stream Health**
```
Metrics:
├─ Lag: Kafka consumer offset vs. producer offset (alert if > 30s)
├─ Throughput: Transactions/sec processed (alert if 20% drop)
├─ Error rate: % of transactions failing enrichment (alert if > 0.1%)
├─ Feature Store staleness: Max age of customer profile in cache (alert if > 5s)
└─ Fraud alert rate: % of transactions flagged (alert if deviation > ±30%)

Dashboard: Real-time display (Grafana)
Alerting: PagerDuty triggers on thresholds
```

**2. Batch Pipeline Health**
```
Metrics:
├─ Nightly completion time (alert if > 2 hours for 11 PM → 1 AM window)
├─ Data quality: Row counts vs. yesterday (alert if variance > 10%)
├─ Reconciliation mismatches: # real-time/batch conflicts (investigate if > 100)
├─ Feature drift: Distribution changes vs. baseline (alert if KL divergence > 0.1)
└─ Cost: $ spent on compute resources (budget alert if > threshold)

Dashboard: Post-run reports (emails)
Alerting: Slack notifications, escalate if recovery needed
```

**Failure Recovery:**

**Case 1: Real-time Stream Lag (Kafka accumulation)**
```
Detection: Lag > 30 seconds
Action:
├─ Auto-scale: Increase stream worker instances (1 → 5 min)
├─ Investigation: Check for slow queries (S3, Redis timeouts)
├─ Mitigation: Degrade features (use cached value if enrichment times out)
├─ Monitoring: Track recovery (lag should drop in 5 minutes)
└─ Post-mortem: Analyze root cause, tune batch size or parallelism
```

**Case 2: Feature Store Cache Failure**
```
Detection: Redis unavailable; enrichment latency > 1 second
Action:
├─ Fallback: Use batch-computed features (slightly stale, but complete)
├─ Alert: Page on-call; invoke failover Redis instance
├─ User experience: Fraud scoring uses last 24h snapshot instead of real-time (increased false negatives acceptable)
├─ Recovery: Restart Redis, repopulate from Feature Store database (10 min)
└─ Escalate: Page analytics team if incident > 30 minutes
```

**Case 3: Batch ETL Failure (Staging reads timeout)**
```
Detection: ETL doesn't complete by 1 AM
Action:
├─ Alert: Wake up on-call engineer
├─ Investigation: Check S3 connectivity, Kafka connectivity, staging area size
├─ Retry: Automatically retry with exponential backoff (3 attempts, up to 6 AM)
├─ Fallback: Use yesterday's warehouse state; flag as "incomplete" for manual review
├─ Notification: Alert BI/analytics teams that reports may be stale
└─ Recovery: Once fixed, backfill warehouse with missing transactions
```

**Case 4: Data Quality Issue (Corrupted transaction)**
```
Detection: Validation fails (amount < 0, customer_id missing)
Action:
├─ Quarantine: Send to dead-letter queue (DLQ) in S3
├─ Logging: Record transaction ID, error, timestamp
├─ Downside: Skip transaction in real-time fraud check (approval based on prior profile)
├─ Recovery: Data team investigates DLQ daily, fixes upstream, replays transactions
└─ Monitoring: Track DLQ size; alert if > 1% of daily transactions
```

**End-to-End Failure Scenario:**
- **4:30 PM**: Real-time stream lag detected (> 1 minute)
- **4:35 PM**: Auto-scaling kicks in; lag reduces to 10 seconds
- **11:00 PM**: Batch ETL starts; reads from S3 and reconciles with real-time decisions
- **11:15 PM**: Reconciliation identifies 50 transactions with > 0.10 fraud score difference
- **11:30 PM**: Root cause: Merchant database update at 3 PM wasn't reflected in stream feature store
- **11:45 PM**: Manual backfill: Re-process 50 flagged transactions, update feature store
- **12:00 AM**: ETL completes; DQ checks pass; warehouse updated
- **7:00 AM**: Post-mortem: Implement automated refresh of merchant reference data every hour

---

## Q7. True/False Conceptual Questions [8 Marks]

Answer True or False and provide a 2-3 sentence justification for each. (0.5 mark per choice + 0.5 mark per justification)

### 1. Increasing model complexity always improves production AI system reliability.
**Answer: False**
**Justification**: While complexity can improve model accuracy, it increases inference latency, computational cost, interpretability challenges, and debugging difficulty. A simpler model with acceptable accuracy is often more reliable in production than a complex model that fails to inference within SLA or produces unexplainable decisions. Trade-offs between accuracy and operational reliability must be carefully considered.

### 2. If model accuracy remains constant after deployment, the AI system is performing as expected.
**Answer: False**
**Justification**: Accuracy metrics alone don't capture production performance. Business metrics (revenue, engagement, fraud losses) might degrade due to data drift, feedback loops, training-serving skew, or delayed labels, even if reported accuracy stays stable. Model accuracy should always be validated against business outcomes and real-time monitoring of prediction impact.

### 3. A model trained on imbalanced data (90% class A, 10% class B) can still make good predictions if accuracy is 91%.
**Answer: False**
**Justification**: A 91% accuracy model might simply predict "always class A," achieving 90% accuracy without learning patterns for class B. For imbalanced data, precision, recall, F1-score, and AUC-ROC are more informative than accuracy. The model likely has terrible recall for the minority class, making it unreliable for practical use.

### 4. A model that works well on historical data will always generalize to future data.
**Answer: False**
**Justification**: Historical data may not represent future conditions due to data drift (changing distributions), concept drift (changing relationships between features and labels), or external shocks (market changes, new competitors). Continuous monitoring, retraining, and validation against recent held-out data are necessary to ensure future generalization.

### 5. If an AI system has explainable predictions, it doesn't need monitoring in production.
**Answer: False**
**Justification**: Explainability reveals how the model makes decisions but doesn't guarantee correctness or detect drift. A model might have clear feature importance scores but still produce systematically biased or outdated predictions due to changing data. Monitoring is still essential to track business metrics, data quality, and model performance even with explainability.

### 6. Deploying a model to production requires technical readiness only; business alignment is a separate concern.
**Answer: False**
**Justification**: Deployment requires both technical readiness AND business alignment. A technically robust model that doesn't address stakeholder needs, lacks clear success metrics, or conflicts with organizational priorities will fail adoption. Business stakeholders must define success criteria, risk tolerance, and governance before technical deployment.

### 7. An AI system that reduces false positives necessarily improves business outcomes.
**Answer: False**
**Justification**: Reducing false positives while increasing false negatives might harm business outcomes. For fraud detection, lowering false positives means fewer legitimate transactions blocked, but increasing false negatives means more fraud slips through. The business value depends on the asymmetric cost of FP vs. FN; sometimes accepting more false positives is better than missing fraud.

### 8. Real-time predictions with 5-second latency are always better than batch predictions with 1-day latency.
**Answer: False**
**Justification**: Latency alone doesn't determine value. Batch recommendations processed daily might have 99% accuracy due to complete historical data and careful validation, while real-time predictions might have lower accuracy due to stale features or limited context. The choice depends on use case requirements; real-time fraud detection needs low latency, but nightly recommendation batches are sufficient for email campaigns.

---

## Q8. Model Evaluation Framework for Text Classification [7 Marks]

A company wants to build an email classifier to automatically categorize support tickets as: "Bug Report," "Feature Request," "Billing Issue," or "General Inquiry."

Available data: 50,000 labeled emails (historical support tickets) with fairly balanced class distribution (12,000-13,000 per class).

### Design a comprehensive model evaluation framework covering:
a) Train/validation/test split strategy (1.5 marks)
b) Appropriate evaluation metrics and why (2 marks)
c) How to detect and prevent common issues (2 marks)
d) Approval criteria before production deployment (1.5 marks)

### Answer:

**a) Train/Validation/Test Split Strategy [1.5 marks]**

**Time-based Split (Recommended):**
```
Historical tickets ordered by date
├─ Training: Jan 2023 - Aug 2024 (80%, 40,000 emails)
├─ Validation: Sept 2024 (10%, 5,000 emails)  
└─ Test: Oct 2024 - Present (10%, 5,000 emails)
```

**Rationale:**
- **Prevents data leakage**: Future emails don't influence training
- **Simulates production**: Test set represents real unseen data the model will encounter
- **Drift detection**: Validation and test sets capture temporal changes in email patterns

**Stratification:**
- Maintain class distribution in each split (12-13% per class in train, val, test)
- Prevents class imbalance from skewing evaluation

**Cross-validation (Optional, for robustness):**
- 5-fold stratified cross-validation on training set to estimate generalization error
- Reduces variance in performance estimates
- Useful for hyperparameter tuning

---

**b) Evaluation Metrics & Justification [2 marks]**

**1. Primary Metrics:**

| Metric | Formula | Why Use |
|--------|---------|---------|
| **Macro F1-Score** | Average F1 across all classes | Balanced performance across all ticket types (including rare ones) |
| **Weighted F1-Score** | F1 weighted by class frequency | Accounts for slight class imbalance |
| **Confusion Matrix** | Pred vs. Actual for all classes | Identify systematic misclassifications (e.g., Feature Requests mislabeled as General) |

**2. Secondary Metrics by Use Case:**

| Scenario | Metric | Threshold |
|----------|--------|-----------|
| Bug Reports (critical) | Recall for "Bug Report" | ≥ 0.90 (catch 90% of bugs) |
| Feature Requests (low cost to misclassify) | Precision for "Feature Request" | ≥ 0.75 (reduce false alarms) |
| Billing Issues (high cost) | F1 for "Billing Issue" | ≥ 0.85 |
| General Inquiry (catch-all) | Low false positive rate | < 10% of non-bugs mislabeled as bugs |

**3. Business Metrics:**

- **Automation rate**: % of emails automatically classified without human review (target: 80%+)
- **Manual review workload reduction**: Reduction in human effort compared to baseline
- **User satisfaction**: Feedback on correct categorization from support team (target: 85%+)

**Why Not Accuracy Alone:**
- Accuracy hides class-specific performance (model might achieve 85% accuracy by always predicting majority class)
- For support tickets, correctly classifying rare "Bug Reports" is more valuable than "General Inquiry"

---

**c) Detecting & Preventing Common Issues [2 marks]**

**Issue 1: Class Imbalance (If Discovered)**
```
Detection:
└─ Check: Are class distributions actually balanced?
   If class counts differ > 20%, trigger balancing

Prevention:
├─ Use stratified sampling during train/test split
├─ Monitor confusion matrix for systematic bias
└─ If imbalanced in real data, use class weights or resampling
```

**Issue 2: Data Leakage**
```
Detection:
├─ Suspiciously high validation/test accuracy (> 95%)
├─ Check: Does test data have identical emails to training?
└─ Compare: Email IDs, exact content, customer names (might indicate duplicates)

Prevention:
├─ Deduplicate emails before splitting (same ticket replied multiple times)
├─ Remove customer/sender identity before training (avoid memorization)
├─ Time-based split ensures temporal separation
```

**Issue 3: Training-Serving Skew**
```
Detection:
├─ Production performance drops compared to test (>10% F1 decline)
├─ Monitor: Distribution shift in production emails
└─ Check: Are feature preprocessing steps identical in prod vs. testing?

Prevention:
├─ Document preprocessing steps (tokenization, case handling, special chars)
├─ Test on holdout set with same preprocessing
├─ Log production predictions and compare distributions monthly
└─ Trigger retraining if distribution shifts > threshold
```

**Issue 4: Overfitting**
```
Detection:
├─ Training F1: 0.95, Validation F1: 0.78 (large gap = overfitting)
├─ Excessive model complexity or memorization

Prevention:
├─ Use regularization (L1/L2 for LLMs, dropout for neural networks)
├─ Early stopping during training if validation loss increases
├─ Simpler baseline models (Naive Bayes, Logistic Regression) as sanity checks
└─ Cross-validation to detect variance in performance
```

**Issue 5: Class Confusion Pattern**
```
Detection:
├─ Confusion matrix shows "Feature Request" → "General Inquiry" (30% misclassification)
├─ These categories are semantically similar

Prevention:
├─ Error analysis: Manually review misclassified samples
├─ Combine categories or refine definitions
├─ Add feature engineering (highlight keywords: "I want," "request," "feature")
├─ Train category-specific sub-models if needed
└─ Communicate ambiguous examples to support team for clarification
```

---

**d) Approval Criteria Before Production [1.5 marks]**

**Approval Gates (All must pass):**

**Gate 1: Performance Threshold**
```
✓ Macro F1-Score ≥ 0.82 (overall balanced performance)
✓ Weighted F1-Score ≥ 0.84 (accounting for class weights)
✓ Recall for "Bug Report" ≥ 0.88 (catch most bugs)
✓ No class with F1 < 0.75 (avoid very poor categories)
✓ Confusion matrix shows no systematic class confusion pattern
```

**Gate 2: Generalization Check**
```
✓ Validation F1 within 5% of Test F1 (prevents overfitting)
✓ Cross-validation mean F1 within 3% of single-fold F1
✓ Performance stable across time-based splits (no temporal drift)
```

**Gate 3: Business Readiness**
```
✓ Support team reviews 100 sample predictions → 80%+ agree
✓ Automation achieves 75%+ throughput (leaving 25% for manual review)
✓ Model reduces manual triage time by ≥ 30%
✓ Cost savings justify deployment effort
```

**Gate 4: Technical & Operational Readiness**
```
✓ Inference latency < 100ms per email (acceptable for batch processing)
✓ Model size < 500MB (deployable to production servers)
✓ Monitoring & alert system configured (F1 drift detection, error tracking)
✓ Rollback procedure documented and tested
✓ Documentation complete (model card, training data description, limitations)
```

**Gate 5: Governance & Compliance**
```
✓ Bias analysis: Model performance equal across customer segments (±5%)
✓ Explainability: Can support team understand why an email was classified?
✓ Privacy: No sensitive customer data retained in model
✓ Licensing: Code/models comply with company policies
```

**Decision Tree:**
```
All gates pass?
├─ YES → APPROVED ✓
├─ PARTIALLY (1-2 gates failing) → Conditional Approval:
│   └─ Deploy to 10% of emails as pilot; monitor for 2 weeks
│   └─ If metrics hold, expand to 100%
│   └─ If issues arise, rollback and iterate
└─ NO (>2 gates failing) → REJECTED
    └─ Iterate: Improve model, gather more data, refine categories
    └─ Re-evaluate in 1 month
```

---

## Summary

This practice question set covers the same topics and difficulty levels as the mid-semester exams:
- Real-world AI system architecture and design decisions
- Production ML lifecycle and monitoring
- Data engineering and feature design
- Model evaluation and deployment criteria
- Business judgment and risk assessment
- Conceptual understanding of AI/ML limitations

**Study Tips:**
1. Focus on practical problem-solving, not just theory
2. Always justify design decisions with business context
3. Consider edge cases, failure modes, and trade-offs
4. Document assumptions clearly
5. Think about monitoring, governance, and long-term maintenance


---

## Q9. Foundation Models and Enterprise Model Selection [6 Marks]

A bank is building a customer service chatbot to handle account inquiries, complaints, and product recommendations. The CTO has three options:

**Option A**: Use GPT-4 API from OpenAI ($0.03/1K input tokens, $0.06/1K output tokens)
**Option B**: Use Claude 3 via AWS Bedrock ($15/1M input tokens, $45/1M output tokens)
**Option C**: Self-host open-source LLaMA 2 (70B) on infrastructure

The bank processes ~10M customer messages/month (avg 200 tokens each).

### Design a model selection framework:
a) Calculate monthly costs for each option (2 marks)
b) Identify non-cost factors for enterprise model selection (1.5 marks)
c) What licensing implications exist for each? (1 mark)
d) Recommend a choice with justification (1.5 marks)

### Answer:

**a) Monthly Cost Calculation [2 marks]**

**Assumptions**: 10M messages × 200 input tokens = 2B input tokens
- Average response: 150 output tokens
- Total output: 10M × 150 = 1.5B output tokens

| Option | Input Cost | Output Cost | Total Monthly |
|--------|-----------|-----------|---|
| **GPT-4 API** | 2B × $0.03/1M = $60K | 1.5B × $0.06/1M = $90K | **$150K/month** |
| **Claude/Bedrock** | 2B × $15/1M = $30K | 1.5B × $45/1M = $67.5K | **$97.5K/month** |
| **LLaMA 2 (Self-hosted)** | GPU: A100 × 3 = $30K/month + engineers | Support & ops | **$40-50K/month** (estimated) |

**Cost Winner**: LLaMA 2 is cheapest if infrastructure + ops are managed efficiently.

---

**b) Non-Cost Enterprise Selection Factors [1.5 marks]**

| Factor | Importance | Details |
|--------|-----------|---------|
| **Data Privacy** | Critical | Customer financial data stays on-premise with LLaMA; GPT-4 sends to OpenAI servers (data residency concerns) |
| **Latency** | High | LLaMA on-premise: 50-100ms; GPT-4 API: 500-2000ms via network calls |
| **Model Quality** | High | GPT-4 > Claude 3 >> LLaMA 2 for complex reasoning; LLaMA better for cost-sensitive use |
| **Support & SLA** | Medium | GPT-4/Claude: 99.95% SLA guaranteed; LLaMA: self-managed (variable uptime) |
| **Customization** | Medium | LLaMA: fine-tune on bank scripts; GPT-4: limited customization (prompt engineering only) |
| **Regulatory Compliance** | Critical | DPDP Act: customer data cannot leave India servers. **This eliminates GPT-4/Claude if data processing occurs remotely** |
| **Vendor Lock-in** | Medium | GPT-4: vendor-dependent; LLaMA: portable, can switch models later |

**Key Insight**: Regulatory constraint likely makes LLaMA or locally-hosted Claude mandatory.

---

**c) Licensing Implications [1 mark]**

| Model | License | Implications |
|-------|---------|---|
| **GPT-4** | Proprietary | ✓ No restrictions on output use; ✓ OpenAI owns model; ✗ You cannot modify; ✗ All data sent to OpenAI servers |
| **Claude 3 (via Bedrock)** | Proprietary (Anthropic) | ✓ No output restrictions; ✗ Proprietary model; ✗ Depends on AWS (vendor lock-in); ✓ Data stays on AWS (regional sovereignty possible) |
| **LLaMA 2** | Community License | ✓ Modify and fine-tune freely; ✓ Can be self-hosted (no vendor lock-in); ✓ Commercial use allowed up to 700M MAU; ✗ If bank has >700M users globally, need commercial license from Meta; ⚠ Open-source means community-maintained (support may be limited) |

**Licensing Winner**: LLaMA 2 has best flexibility, but check user scale.

---

**d) Recommendation with Justification [1.5 marks]**

**Recommended Choice: Self-Hosted LLaMA 2 + Prompt Engineering**

**Justification:**

1. **Cost**: ~$40-50K/month vs. $150K (GPT-4) or $97.5K (Claude) = 67% cost savings.

2. **Compliance**: DPDP Act mandates customer data stays in India. Self-hosted LLaMA satisfies this; GPT-4 API violates it.

3. **Latency**: <100ms responses critical for chat quality; on-premise wins over API.

4. **Customization**: Fine-tune LLaMA on bank's FAQs, policies, and conversation patterns for better domain accuracy.

5. **Fallback Plan**: If LLaMA performance insufficient for complex queries, use hybrid:
   - LLaMA for 80% of queries (routing, simple FAQs)
   - GPT-4 for 20% (complex reasoning, escalated cases) via secure private link if compliant

**Implementation Roadmap**:
- Month 1: Deploy LLaMA 2 70B on 3× A100 GPUs in India data center
- Month 1-2: Collect and annotate 5000 customer conversations for fine-tuning
- Month 2-3: Fine-tune LLaMA on bank conversations
- Month 3: A/B test LLaMA vs. GPT-4 on holdout queries; measure quality and cost
- Month 4+: Scale to production with monitoring and feedback loops

---

## Q10. Unstructured Data Feature Extraction & Multimodal ML [7 Marks]

An e-commerce company wants to improve product recommendations by analyzing:
- Product descriptions (text, ~100-300 words)
- Product images (high-resolution photos)
- Customer reviews (1-5 star ratings + text comments)
- Price and category metadata (structured)

The company sells 2M products and has 50M reviews.

### Design a feature extraction pipeline:
a) What features would you extract from each data type? (2 marks)
b) How would you handle the scale (2M products, 50M reviews)? (2 marks)
c) What are the risks of multimodal feature fusion? (1.5 marks)
d) How would you measure feature quality? (1.5 marks)

### Answer:

**a) Feature Extraction by Data Type [2 marks]**

**From Product Descriptions (Text)**:
```
Raw: "Premium stainless steel coffee maker with programmable timer..."

Features:
- Bag of Words (BoW): Count frequency of domain terms (brand, material, feature keywords)
- TF-IDF: Term frequency-inverse document frequency (what makes this description unique)
- Topic Modeling (LDA): Product is "kitchen appliance," "premium," "automated"
- Embeddings: Sentence embedding (BERT, MPNet) → 384-dim vector capturing semantic meaning
- Keyword extraction: "stainless steel," "coffee maker," "programmable timer" for matching
- Text length: Description length correlates with product quality
- Sentiment: Reviews mention "high quality" → positive sentiment feature
```

**From Product Images (Computer Vision)**:
```
Raw: Product photo

Features:
- Pre-trained CNN (ResNet, EfficientNet): Extract 2048-dim image embeddings
- Object detection (YOLO, R-CNN): Identify product features (size, color, design)
- Color histograms: RGB distribution (important for fashion items)
- Image quality: Sharpness, brightness, contrast (proxy for seller reliability)
- Background complexity: Does product stand out or blend with background?
- Multi-angle images: If available, extract features from all views and aggregate
```

**From Customer Reviews (Text + Ratings)**:
```
Raw: "Great product! Excellent build quality." (5 stars)

Features:
- Average rating per product (aggregate)
- Review count (popularity signal; more reviews = more trusted)
- Sentiment analysis (positive/negative/neutral from review text)
- Review embeddings: What do customers say about this product?
- Aspect-based sentiment: "Build quality" is positive, "Shipping time" is negative
- Review recency: Older reviews less relevant than recent ones
- Reviewer credibility: Do reviewers purchase frequently? Do their reviews match rating?
```

**From Structured Data (Price, Category)**:
```
- Price (normalized, log-scale)
- Price change velocity (rising/falling trend)
- Category ID (one-hot encoded)
- Sub-category (hierarchy)
- Stock availability (in-stock = 1, out-of-stock = 0)
- Return rate (products with high returns are risky)
```

---

**b) Handling Scale: 2M Products, 50M Reviews [2 marks]**

**Batch Processing (Offline)**:
```
Week 1: Compute embeddings for all 2M product descriptions
├─ Split 2M descriptions into 100 batches
├─ Use GPU cluster: Process 20K descriptions/batch with BERT
├─ Store 384-dim embeddings in feature store (vector DB: Pinecone, Weaviate)
├─ Cost: ~$500 (GPU hours)
└─ Time: ~8 hours with 50 GPUs

Week 1: Extract image features for 2M products
├─ Process images in batches with pre-trained ResNet
├─ Store 2048-dim image embeddings
├─ Time: ~12 hours with 20 GPUs
```

**Batch Processing (Recurring)**:
```
Monthly: Aggregate 50M new reviews
├─ Sample 1M reviews for sentiment analysis (vs. all 50M)
├─ Re-compute product-level aggregates (avg_rating, review_count, sentiment_score)
├─ Update feature store
├─ Time: ~2 hours
```

**Real-time Updates**:
```
When new review posted:
├─ Add 1 to review_count (quick)
├─ Recompute running average_rating (fast aggregation)
├─ Don't re-compute embeddings immediately (batch monthly)
├─ Store in Redis cache for serving
```

**Data Storage Architecture**:
```
Feature Store Layers:
├─ Offline store (Data Warehouse): Historical product embeddings, computed monthly
├─ Online store (Redis/DynamoDB): Latest features for real-time serving
│   - Product embeddings (lazy-loaded from warehouse if not in cache)
│   - Latest aggregates (review counts, ratings)
│   - Price, availability (updated hourly)
└─ Cold storage (S3): Raw images, full review texts (accessed for retraining)
```

---

**c) Risks of Multimodal Feature Fusion [1.5 marks]**

**Risk 1: Modality Imbalance**
```
Problem: Text features (2000-dim) dominate image features (2048-dim) in ML model
→ Model ignores images, learns mostly from text
Solution: Normalize embeddings, use attention mechanisms to weight modalities
```

**Risk 2: Missing Modalities**
```
Problem: Some products have no images (text-only), some reviews lack text (rating-only)
→ Creates feature vectors with NaN or zeros → biases predictions
Solution: Impute missing modalities or use modality-specific pathways in model
```

**Risk 3: Temporal Misalignment**
```
Problem: Product description written in 2020, customer reviews from 2024
→ Customer reviews reflect updated product perception, description outdated
→ Feature conflicts: description says "new," reviews say "worn out"
Solution: Use review recency as weight; down-weight old descriptions
```

**Risk 4: Semantic Redundancy**
```
Problem: Description "stainless steel coffee maker" + image of silver metal object = redundant
→ Model sees same signal twice → misleading importance
Solution: Use dimensionality reduction (PCA) or learn modality weights
```

**Risk 5: Scaling Computation**
```
Problem: Computing features for 2M products × 3 modalities × retraining = expensive
→ Can become computational bottleneck
Solution: Use efficient embeddings (DistilBERT), batch processing, caching
```

---

**d) Measuring Feature Quality [1.5 marks]**

**Test 1: Feature Variance & Information Content**
```
For each feature, compute variance:
├─ High variance: Feature discriminates between products ✓ (good)
├─ Low variance: Feature is constant → remove (bad)
│   Example: If 99% of products have same color, color feature has no variance
└─ Threshold: Keep features with variance > 10th percentile
```

**Test 2: Correlation with Target (Recommendation CTR)**
```
Compute Pearson correlation between each feature and click-through rate:
├─ High correlation (|r| > 0.3): Feature predicts user engagement ✓
├─ Zero correlation: Feature doesn't help prediction (consider removing)
├─ Negative correlation: Feature hurts predictions (might be redundant)
└─ Use this to rank feature importance
```

**Test 3: Embedding Quality via Nearest Neighbors**
```
For product embeddings, manually verify if similar products have similar embeddings:
├─ Take 10 random products
├─ Find 5 nearest neighbors in embedding space
├─ Expert review: "Are these neighbors good recommendations?"
├─ Success rate: If >80% neighbors are appropriate, embedding quality is high
└─ Example: "MacBook Pro 13" should have neighbors like "MacBook Pro 16", "Dell XPS", not "Toaster"
```

**Test 4: Multimodal Agreement**
```
For products, compare similarity rankings from different modalities:
├─ Text embedding: What products are similar based on description?
├─ Image embedding: What products look similar?
├─ Rating embedding: What products have similar reviews?
├─ Correlation: If all 3 modalities rank neighbors similarly → features are coherent ✓
├─ If they disagree → investigate which modality is wrong
```

**Test 5: Downstream Model Performance**
```
Train recommendation model with and without feature:
├─ With feature: AUC-ROC = 0.82
├─ Without feature: AUC-ROC = 0.81
├─ Gain: 0.01 AUC (1% improvement) → feature is valuable ✓
├─ If gain < 0.005: Feature might be redundant or noisy
└─ Use ablation study to rank feature importance
```

**Test 6: Feature Stability Over Time**
```
Compute same feature for same product on Day 1 vs. Day 30:
├─ Embeddings shouldn't change much (unless product updated)
├─ If embeddings drift significantly → suggests batch processing issues or data drift
├─ Cosine similarity between Day 1 and Day 30 embeddings: Should be > 0.95
```

---

## Q11. Data Quality, Fairness & Governance [6 Marks]

A bank's loan approval model shows concerning disparities:

```
Overall Accuracy: 85%
Accuracy for Urban Customers: 88%
Accuracy for Rural Customers: 71%

Loan Approval Rate (Urban): 60%
Loan Approval Rate (Rural): 35%
```

The same customers, when stratified by income, show similar disparities. The model was trained on 10 years of historical loan data.

### Diagnose and resolve:
a) What data quality issues could cause this disparity? (1.5 marks)
b) Is this model legally deployable under India's DPDP Act? (1 mark)
c) Design a fairness testing framework (1.5 marks)
d) How would you fix the model? (2 marks)

### Answer:

**a) Data Quality Issues Causing Disparity [1.5 marks]**

**Issue 1: Selection Bias in Historical Data**
```
Root Cause: Bank historically approved fewer rural loan applications
→ Training data: 90% urban loans, 10% rural loans
→ Model learns urban patterns better (more training examples)
→ Rural examples underrepresented → poor generalization

Evidence: "Loan Approval Rate (Rural) = 35%" suggests model learned historical bias

Fix: 
├─ Oversample rural examples during training (1:1 balance)
├─ Use stratified cross-validation ensuring each fold has balanced rural/urban
└─ Weight rural examples higher in loss function (2-5x higher)
```

**Issue 2: Feature Representation Bias**
```
Root Cause: Features correlated with geography but not causally related to creditworthiness
→ Example features:
   - "PIN code" (proxy for rural vs. urban) directly in model
   - "Branch location" (rural branches fewer, less training data)
   - "Collateral type" (rural uses land, urban uses property; different valuation rules)
→ Model uses geography as proxy for credit risk

Fix:
├─ Remove explicit geographic features (PIN code, branch location)
├─ Standardize feature definitions across regions
├─ Use fair feature representations (normalize collateral values consistently)
```

**Issue 3: Data Completeness Bias**
```
Root Cause: Rural customer records may be incomplete
→ Missing values handled differently:
   - Urban: Most customers have full credit history
   - Rural: Many customers have incomplete history (filled with imputed values)
→ Missing data imputation may introduce bias

Evidence: Higher variance in rural predictions (lower accuracy)

Fix:
├─ Analyze missing data patterns by region
├─ Don't impute missing values for rural customers using urban statistics
├─ Use region-specific imputation or "missing as indicator" approach
```

**Issue 4: Label Quality Bias**
```
Root Cause: Labels (loan default yes/no) may be inconsistent across regions
→ Urban default: Tracked meticulously through digital systems
→ Rural default: May be recorded inconsistently (no digital track record)
→ Rural "not defaulted" labels unreliable (actual defaults went unrecorded)

Fix:
├─ Validate label quality: Check if default tracking is consistent across regions
├─ If rural labels unreliable, reduce confidence weights for rural training examples
├─ Consider treating rural data as having higher label noise
```

---

**b) DPDP Act (India's Data Protection Law) Compliance [1 mark]**

**Legal Assessment: NOT Deployable in Current Form**

**DPDP Act Requirements**:
```
1. Algorithmic Transparency: Bank must disclose how algorithm makes decisions
   ✗ Current Model: Disparity detected but root cause unknown → violates transparency

2. Fairness & Non-discrimination: Algorithm cannot discriminate based on protected attributes
   ✗ Current Model: Rural customers disadvantaged (35% approval vs. 60% urban) → likely violates fairness

3. Consent & Explainability: Customer must consent to algorithmic decision
   ✗ Current Model: If customer denied loan due to algorithm, must explain why
   ✗ Problem: Model likely uses geography as proxy → customer explanation insufficient

4. Right to Correction: Individual can contest algorithmic decisions
   ✗ Current Model: Cannot fairly contest if geographic bias embedded

5. Impact Assessment: Organizations must conduct fairness impact assessments
   ✗ Current Model: None documented yet
```

**Legal Consequence**: Bank faces fines up to ₹5 crores or 2% of annual revenue under DPDP Act.

**To Become Compliant**:
```
1. Retrain model with fairness constraints (equal opportunity, demographic parity)
2. Remove geographic proxies
3. Document impact assessment (fairness testing results)
4. Implement explainability (SHAP values showing why each loan was approved/denied)
5. Create appeals process for customers to contest decisions
```

---

**c) Fairness Testing Framework [1.5 marks]**

**Fairness Metrics to Monitor**:

| Metric | Definition | Target | How to Compute |
|--------|-----------|--------|---|
| **Demographic Parity** | P(Approval \| Rural) = P(Approval \| Urban) | ≥ 90% parity (within 10%) | Approval rate ratio: 35% / 60% = 58% → **FAIL** |
| **Equal Opportunity** | P(Approval \| Rural, Qualified) = P(Approval \| Urban, Qualified) | ≥ 90% parity | Among customers with "acceptable credit," approval rates equal? |
| **Predictive Parity** | P(Actual Repay \| Approved, Rural) ≈ P(Actual Repay \| Approved, Urban) | ≥ 90% parity | Do approved rural loans actually repay at similar rates to urban? |
| **Calibration** | P(Default \| Score=0.7, Rural) = P(Default \| Score=0.7, Urban) | ≥ 95% | For same predicted risk score, actual default rate similar? |

**Testing Procedure**:
```
Step 1: Define protected groups (Rural/Urban, Income level, etc.)

Step 2: Hold-out test set with labeled outcomes (actual repayment, defaults)
├─ Stratify: Ensure test set has 50% rural, 50% urban loans
├─ Period: Use recent loans (2023-2024) to avoid historical bias

Step 3: Run model on test set
├─ Compute approval rates by group
├─ Compute accuracy, recall, precision by group
├─ Measure all fairness metrics above

Step 4: Statistical significance testing
├─ Use Chi-square test: Are disparities statistically significant or due to chance?
├─ If p-value < 0.05: Disparity is real, not random variation

Step 5: Decision rules
├─ If Demographic Parity < 80%: Model fails fairness test → REJECT
├─ If gaps exist but with business justification (e.g., rural = higher risk): CONDITIONAL APPROVAL
├─ If gaps eliminated and legal compliant: APPROVE
```

---

**d) How to Fix the Model [2 marks]**

**Fix 1: Balanced Training Data**
```
Action: Resample training set to balance rural/urban
├─ Original: 9000 urban loans, 1000 rural loans
├─ After balancing: 5000 urban loans, 5000 rural loans (stratified random sample)
├─ Or: Keep all, but up-weight rural loans (5x higher) in loss function
│   Loss = 1 * Urban_loss + 5 * Rural_loss

Result: Model sees rural patterns equally often → learns both distributions well
```

**Fix 2: Remove Geographic Proxies**
```
Before:
├─ Features: PIN code, branch location, collateral type (rural-specific)
└─ These act as proxies for rural/urban

After:
├─ Features: Income, credit history, debt-to-income ratio, collateral value
├─ Use standardized definitions (e.g., collateral value normalized consistently)
└─ No geographic identifiers in features

Result: Model decisions based on creditworthiness, not geography
```

**Fix 3: Fairness Constraints During Training**
```
Traditional Training:
└─ Minimize prediction error: min Σ (predicted - actual)²

Fairness-Aware Training:
├─ Minimize prediction error AND fairness loss:
│  min Σ (predicted - actual)² + λ * FairnessLoss
│
├─ Where FairnessLoss measures disparity between groups:
│  FairnessLoss = |Approval_rate_rural - Approval_rate_urban|
│
└─ Tune λ to balance accuracy vs. fairness:
   - λ = 0: Maximize accuracy (current biased model)
   - λ = 1.0: Force equal approval rates (might hurt accuracy)
   - λ = 0.5: Good balance (recommended)

Result: Model trades off small accuracy loss for large fairness gain
```

**Fix 4: Threshold Calibration by Group**
```
Idea: Use different decision thresholds for rural vs. urban

Method:
├─ Train single model (without group-specific training)
├─ For urban customers: Approve if model score > 0.6
├─ For rural customers: Approve if model score > 0.5 (lower threshold)
│
└─ Rationale: If same model score has different predictive power by group,
   adjust thresholds to equalize approval rates or outcomes

Result: Demographic parity can be achieved through threshold adjustment

Caution: Legal risk if explicitly using "rural" threshold
         → Instead frame as "for customers with limited credit history" (more inclusive)
```

**Fix 5: Validation & Monitoring**
```
Before Deployment (Offline):
├─ Test on 2023 loans: Verify fairness metrics pass
├─ A/B test: 50% new model (fair), 50% old model (biased)
│  - Track approval rates, default rates, customer satisfaction
│  - Verify fairness improvements don't hurt business metrics

After Deployment (Online Monitoring):
├─ Monthly: Compute fairness metrics on new loan decisions
├─ Alert if: Approval rate disparity > 10% (something drifted)
├─ Quarterly: Retrain on recent data to prevent new bias accumulation
└─ Yearly: Fairness audit with external auditors
```

**Expected Outcomes After Fixes**:
```
Before:
├─ Urban Approval: 60%
├─ Rural Approval: 35%
├─ Disparity: 25 percentage points ✗

After Fixes:
├─ Urban Approval: 55%
├─ Rural Approval: 52%
├─ Disparity: 3 percentage points ✓

Trade-off:
├─ Overall accuracy may drop by 2-3% (acceptable for fairness gain)
├─ More loans approved to rural customers (business impact: +5% portfolio)
└─ Legal compliance achieved (no DPDP violations)
```

---

## Q12. Temporal Data & Point-in-Time Correctness [7 Marks]

A fintech company wants to build a churn prediction model for bank customers. Available data:

```
Customer Features (current):
- account_age (years opened)
- total_transactions (lifetime count)
- average_transaction_amount
- last_login_date

Transaction History:
- transaction_id, customer_id, amount, date, type

Target Label:
- churned (1 = customer quit, 0 = still active) 
```

The company wants to train on historical data from 2021-2023 and deploy to predict 2024 churn.

### Design a temporally correct dataset:
a) Explain the risk of data leakage in temporal data (2 marks)
b) Design a correct train/validation/test split (2 marks)
c) What features are safe to use? Which create leakage? (2 marks)
d) How would you prevent training-serving skew? (1 mark)

### Answer:

**a) Temporal Data Leakage Risks [2 marks]**

**Leakage Risk 1: Using Future Information**
```
Scenario: Training set includes data from AFTER customer churned
├─ Example: Customer churned on 2023-05-01
│           Training uses total_transactions = 1000 (includes May-Dec 2023 activity)
│           But customer inactive after May → counts are inflated
└─ Result: Model learns "high transaction count → not churned"
         But high counts only exist before churn (not predictive)

Impact: Model overfits to training data, fails on new data where no future exists

Leakage Detection:
├─ Check: For each training example, is all data available BEFORE the observation date?
├─ For customers who churned, freeze data at churn date
├─ Don't use any activity after churn date
```

**Leakage Risk 2: Lookahead Bias**
```
Scenario: Features computed with knowledge of churn outcome

Example:
├─ If customer churned → may have stopped logging in (last_login_date was far ago)
├─ Using last_login_date in training set is OKAY
├─ But using post-churn inactivity period as feature = LEAKAGE

Correct approach:
├─ For each customer on date D, compute all features using data up to date D
├─ Check if customer churned on date D+1 to D+90 (this is the target)
└─ This creates a 90-day prediction window
```

**Leakage Risk 3: Data Available at Serving Time**
```
Scenario: Feature not available when making real-time prediction

Example:
├─ Training: Used "revenue_earned_in_next_quarter" (not available at prediction time!)
├─ Serving: Trying to predict churn TODAY, but next quarter revenue unknown

Why this happens:
├─ Analyst used data that doesn't exist yet during training
├─ Model can't reproduce predictions in production

Prevention:
├─ For every feature, ask: "Is this data available when we need to predict?"
├─ Build a feature "time travel" function: Given prediction_date, what data exists?
```

---

**b) Temporally Correct Train/Validation/Test Split [2 marks]**

**Goal**: Simulate production: train on past, predict near-future

```
Timeline:
├─ 2021-01-01: Training window start
├─ 2023-06-30: Training window end ✓ (18 months of data to train on)
├─ 2023-07-01: Validation observation date
├─ 2023-07-01 to 2023-09-30: Validation prediction window (90 days)
├─ 2023-10-01: Validation ends, test starts
├─ 2023-10-01 to 2023-12-31: Test observation & prediction window (90 days)

Splits:
└─ No overlap: Training data < Validation data < Test data

Split Assignment:
├─ Training Set: All features & activity from 2021-01-01 to 2023-06-30
│  │ Target: Did customer churn between 2023-07-01 and 2023-09-30?
│  └─ Example: If account_id=123, created 2021-06-15
│              ├─ Features computed as of 2023-06-30:
│              │  - account_age = 2.01 years
│              │  - total_transactions = 523 (up to 2023-06-30)
│              └─ Target = 1 if churned between 2023-07-01 and 2023-09-30
│
├─ Validation Set: All features & activity from 2021-01-01 to 2023-09-30
│  │ Target: Did customer churn between 2023-10-01 and 2023-12-31?
│  └─ (test model trained on training set)
│
└─ Test Set: All features & activity from 2021-01-01 to 2023-12-31
   │ Target: Did customer churn between 2024-01-01 and 2024-03-31?
   └─ (simulate 2024 predictions with 2023 data)
```

**Why This Split Works**:
```
✓ No information leakage: Each split uses only historical data
✓ Realistic simulation: Training on 2021-2023 to predict 2023-2024 (realistic prod scenario)
✓ Temporal ordering preserved: Can't train on future data
✓ Multiple observation windows: Capture seasonal variations (summer/winter churn rates)
```

**Code Example**:
```python
# Pseudocode for correct splitting

def create_temporal_splits(transactions_df, churn_labels_df):
    # Define time boundaries
    train_end = '2023-06-30'
    val_start = '2023-07-01'
    val_end = '2023-09-30'
    test_start = '2023-10-01'
    test_end = '2023-12-31'
    
    # Training set: features up to train_end, target = churn in [val_start, val_end]
    train_features = transactions_df[transactions_df['date'] <= train_end]
    train_targets = churn_labels_df[(churn_labels_df['churn_date'] >= val_start) 
                                     & (churn_labels_df['churn_date'] <= val_end)]
    train = merge(train_features, train_targets)
    
    # Validation set: features up to val_end, target = churn in [test_start, test_end]
    val_features = transactions_df[transactions_df['date'] <= val_end]
    val_targets = churn_labels_df[(churn_labels_df['churn_date'] >= test_start) 
                                   & (churn_labels_df['churn_date'] <= test_end)]
    val = merge(val_features, val_targets)
    
    # Test set: features up to test_end, target = churn in future
    test_features = transactions_df[transactions_df['date'] <= test_end]
    test_targets = churn_labels_df[churn_labels_df['churn_date'] >= '2024-01-01']
    test = merge(test_features, test_targets)
    
    return train, val, test
```

---

**c) Safe Features vs. Leakage [2 marks]**

| Feature | Safe? | Reason |
|---------|-------|--------|
| **account_age** | ✓ SAFE | Static (created date doesn't change); no future info |
| **total_transactions** | ⚠ RISKY | Aggregation of all history; depends on computation date |
| **average_transaction_amount** | ⚠ RISKY | Derived from transactions; changes over time |
| **last_login_date** | ⚠ RISKY | If customer churned, may not have recent login; correlated with target |
| **days_since_last_transaction** | ⚠ RISKY | Creates leakage: If computed after churn, will be large for churned customers |
| **account_balance** | ✓ SAFE | Point-in-time snapshot; if computed as of 2023-06-30, it's fixed |
| **signup_date** | ✓ SAFE | Static (never changes) |
| **customer_segment** | ⚠ RISKY | If segment assigned based on recent behavior, may leak |

**Correct Feature Definitions (Point-in-Time)**:
```
For each customer, at each observation date D:

✓ account_age_at_D = D - account_creation_date
✓ total_transactions_by_D = COUNT(transactions WHERE date <= D)
✓ avg_transaction_amount_by_D = AVERAGE(amount WHERE transaction_date <= D)
✓ last_login_by_D = MAX(login_date WHERE login_date <= D)
✓ days_since_last_transaction_by_D = D - MAX(transaction_date WHERE transaction_date <= D)
✓ balance_at_D = Balance as of close of day D
```

**Example Walkthrough: Customer #456**
```
Observation Date: 2023-06-30
Account Created: 2020-01-15

Safe Feature Computation:
├─ account_age = 2023-06-30 - 2020-01-15 = 3.45 years ✓
├─ total_transactions = COUNT(transactions WHERE date <= 2023-06-30) = 234 ✓
├─ avg_transaction_amount = AVERAGE(amount WHERE date <= 2023-06-30) = ₹5,234 ✓
├─ last_login = MAX(login_date WHERE date <= 2023-06-30) = 2023-06-15 (15 days ago) ✓
└─ days_since_last_transaction = 2023-06-30 - 2023-06-27 = 3 days ✓

Target (observed in future):
└─ churned = 1 (customer quit on 2023-08-15)

This is SAFE because all features computed BEFORE churn occurred.
```

---

**d) Prevent Training-Serving Skew [1 mark]**

**Training-Serving Skew**: Features computed differently in training vs. production

**Scenario**:
```
Training:
├─ Use SQL query: SELECT COUNT(*) FROM transactions WHERE customer_id=456 AND date <= '2023-06-30'
├─ Result: 234 transactions

Production (3 months later):
├─ Same query but on outdated database snapshot
├─ New transactions added; query returns 289 transactions (different!)
└─ Model sees different feature value → predictions change unpredictably
```

**Prevention Strategies**:

**Strategy 1: Feature Store with Offline + Online Consistency**
```
Feature Store:
├─ Offline Store (Warehouse): Computed features, versioned by date
│  └─ training_features_2023_06_30.parquet
│
├─ Online Store (Redis): Real-time features for production
│  ├─ Updated hourly from transactions
│  └─ Uses EXACT same computation logic as offline
│
└─ Consistency: Offline and online compute total_transactions identically
   └─ Same SQL, same aggregation window, same point-in-time logic
```

**Strategy 2: Version Feature Definitions**
```
For each feature, document:
├─ Name: "total_transactions"
├─ Computation: SELECT COUNT(*) FROM transactions 
               WHERE customer_id = ? AND date <= observation_date
├─ Last Updated: 2023-06-30
├─ Data Source: prod.transactions table, v2.3
└─ Owner: Data Eng Team

When deploying model:
├─ Lock feature version
├─ Both training & serving use EXACT same definition
└─ If feature definition changes, retrain model on new features
```

**Strategy 3: Regular Validation**
```
Every week:
├─ Compute features on historical data (2023-06-30) using production code
├─ Compare with training features
├─ If different: Fix the code before skew causes prediction drift

Monitoring:
├─ Track feature distributions in production
├─ Alert if feature distribution changes > 10% vs. training
├─ If drift detected: Retrain model
```

---

## Q13. Online vs. Offline Features & Real-Time Feature Stores [8 Marks]

A fintech company wants to build a real-time fraud detection system. Requirements:

- **Latency**: Decision within 50ms of transaction
- **Features needed**:
  - User's transaction history (last 10 transactions)
  - User's total spending (last 30 days)
  - User's location and device history
  - Merchant risk score (updated daily)
  - Account age

### Design a feature store architecture:
a) Classify each feature as offline or online (1 mark)
b) Design the offline + online pipelines (2.5 marks)
c) How to compute real-time aggregations efficiently? (2 marks)
d) Prevent training-serving skew in real-time system (2.5 marks)

### Answer:

**a) Feature Classification [1 mark]**

| Feature | Type | Why | Recompute Freq |
|---------|------|-----|---|
| Last 10 transactions | Online | Needed in 50ms; must be fresh | Real-time |
| Total spend (30-day) | Online | Aggregation changes constantly | Every minute |
| User location history | Offline | For training, historical locations; online for current device | Daily for offline |
| Device history | Online | Real-time fraud detection needs current device | Real-time |
| Merchant risk score | Offline | Computed once daily, doesn't need real-time updates | Daily |
| Account age | Offline | Static (never changes after account creation) | Never |

**Classification Logic**:
```
Online (Real-time store): 
├─ Features that change frequently (seconds/minutes)
├─ Features needed for production inference within latency SLA
├─ Examples: transaction counts, velocity, recent activity

Offline (Data warehouse):
├─ Features computed batch (nightly, daily)
├─ Used for training (historical snapshots)
├─ Static or slowly changing features
├─ Examples: merchant risk (daily), account age (static)
```

---

**b) Offline + Online Pipelines [2.5 marks]**

**Offline Pipeline (Nightly, Batch)**

```
Schedule: 11 PM daily

Step 1: Extract user profiles from data warehouse
├─ Query: Last 30 days of transactions per user
├─ Query: Account details (age, status, KYC info)
├─ Query: Historical locations, devices (past 90 days)
└─ Data volume: 10M users × 100KB = 1TB

Step 2: Compute offline features
├─ Group transactions by user_id
├─ Aggregate: Total spend (30-day), average transaction size
├─ Histogram: Transaction amounts (for percentile features)
├─ Join: Merchant risk scores (from daily merchant update)
└─ Result: Feature table (10M rows × 50 features)

Step 3: Store in offline feature store (Data Warehouse)
├─ Save as Parquet table: users_features_2024_01_15.parquet
├─ Include: user_id, feature_1, feature_2, ..., feature_50, timestamp
└─ Partition by: date, region (for fast queries)

Step 4: Train ML models
├─ Load features from offline store
├─ Join with fraud labels (transactions marked as fraud/legitimate)
├─ Train fraud detection model
└─ Save model to model registry
```

**Online Pipeline (Real-time, Streaming)**

```
Trigger: Every transaction (millions/second)

Step 1: Transaction received
├─ Timestamp: 2024-01-15 14:23:45.123
├─ User ID: 78910
├─ Amount: ₹5,000
├─ Merchant: Amazon

Step 2: Fetch online features (from Redis/DynamoDB)
├─ Query: user:78910:last_10_transactions
│  └─ Returns: [T1, T2, T3, ..., T10] (last 10 transaction objects)
│
├─ Query: user:78910:daily_spend
│  └─ Returns: ₹45,000 (as of last minute)
│
├─ Query: user:78910:current_location
│  └─ Returns: Delhi, IP=203.0.113.5
│
├─ Query: merchant:Amazon:risk_score
│  └─ Returns: 0.05 (low risk, loaded from offline daily)
│
└─ Query: user:78910:account_age
    └─ Returns: 5.5 years (static, loaded daily)

Step 3: Compute real-time aggregations
├─ Last 10 transactions feature vector:
│  ├─ Average amount: (T1.amt + ... + T10.amt) / 10
│  ├─ Max amount: MAX(T1.amt, ..., T10.amt)
│  ├─ Time between transactions: (now - T1.time) / 10
│  └─ Return: [5234, 9000, 30_seconds, ...]
│
├─ 30-day velocity:
│  ├─ Transactions today: 3
│  ├─ Transactions this week: 12
│  └─ Return: [3, 12, ...]
│
└─ Location anomaly:
    ├─ Current location: Delhi
    ├─ Last location: Mumbai
    ├─ Time since last transaction: 2 hours
    ├─ Is this plausible? Delhi→Mumbai in 2 hours? = SUSPICIOUS
    └─ Return: 1 (anomaly detected)

Step 4: Inference
├─ Combine all features into feature vector
├─ Load fraud detection model
├─ Score = model.predict(features)
│  └─ Score = 0.78 (78% probability of fraud)
│
└─ If score > threshold (0.7): BLOCK transaction, notify user

Step 5: Update online features (for next transaction)
├─ Add current transaction to: user:78910:last_10_transactions
│  └─ Keep only last 10 (pop oldest)
├─ Update: user:78910:daily_spend += 5000
├─ Update: user:78910:current_location = Delhi
└─ TTL: Keep data for 90 days, then expire

Latency Budget:
├─ Redis fetch: ~5ms (50 queries × 0.1ms each)
├─ Feature computation: ~10ms
├─ Model inference: ~20ms
├─ Write-back to Redis: ~5ms
└─ Total: ~40ms ✓ (within 50ms SLA)
```

---

**c) Computing Real-Time Aggregations Efficiently [2 marks]**

**Challenge**: Can't recompute totals on every transaction (too slow)

**Solution 1: Pre-computed Running Aggregates**
```
Instead of: COUNT(transactions WHERE date >= today - 30 days)
Use: Incrementally updated counters

Store in Redis:
├─ user:78910:spend_today = 45000 (updated every transaction)
├─ user:78910:spend_this_week = 125000
├─ user:78910:spend_this_month = 450000
└─ user:78910:transaction_count_today = 8

When new transaction:
├─ spend_today += 5000
├─ spend_this_week += 5000
├─ spend_this_month += 5000
└─ transaction_count_today += 1

Computation time: O(1) instead of O(N) where N = 1000 transactions

Reset logic:
├─ At midnight UTC: Reset daily counters (spend_today = 0)
├─ Every Monday: Reset weekly counters
└─ Event: Reset monthly counters on month boundary
```

**Solution 2: Sliding Window Aggregations**
```
For "transactions in last 2 hours":

Traditional (slow):
├─ Query: SELECT * FROM transactions WHERE user_id=78910 AND time >= now - 2hours
├─ For every transaction, scan millions of rows → 500ms (too slow!)

With streaming window:
├─ Keep in-memory deque: Last 2 hours of transactions
├─ New transaction arrives: Add to deque
├─ Old transaction (>2 hours old): Remove from deque
├─ Aggregate: Just sum values in deque (constant time)

Store:
└─ Redis list: user:78910:transactions_2h = [T1, T2, T3, ...]
   └─ Operations: LPUSH (add), LTRIM (keep only last 2h), LRANGE (read all)
   └─ Time complexity: O(1) amortized
```

**Solution 3: Probabilistic Data Structures (HyperLogLog, Bloom Filters)**
```
For "Did user visit 10+ different merchants this week?":

Traditional: Store SET of all merchants visited
└─ Storage: O(10) elements per user per week → Wasteful for billions of users

With HyperLogLog:
├─ Probabilistic set cardinality estimator
├─ Storage: ~12 bytes regardless of set size!
├─ Query: PFCOUNT user:78910:merchants_week
│  └─ Returns: ~11 (approximate number of unique merchants)
│  └─ Error rate: <2%
└─ Time: O(1)

For "Is this IP address in user's historical IPs?":

Traditional: Store SET of all historical IPs
└─ Storage: Megabytes per power user

With Bloom Filter:
├─ Probabilistic membership test
├─ Storage: Kilobytes
├─ Query: BF.EXISTS user:78910:ips 203.0.113.5
│  └─ Returns: YES (high probability) or NO (definite)
└─ Time: O(1)
```

**Solution 4: Time-Series Database (InfluxDB, TimescaleDB)**
```
For high-frequency numeric aggregations:

Store transaction amounts in time-series DB:
├─ Metric: transaction_amount
├─ Tags: user_id=78910, merchant_id=amazon
├─ Value: 5000
├─ Timestamp: 2024-01-15T14:23:45Z

Query (fast due to indexing):
├─ SELECT SUM(value) FROM transaction_amount 
│  WHERE user_id='78910' AND time >= now - 30d
└─ Response: <10ms (vs. 500ms with traditional query)

Why fast:
├─ Time-series DBs are optimized for time-range queries
├─ Data compressed; aggregations pre-computed
├─ Parallelized across time buckets
```

---

**d) Prevent Training-Serving Skew in Real-Time [2.5 marks]**

**Skew Risk 1: Different Computation Logic**

```
Training:
├─ Daily batch: Compute last_10_transactions feature
│  └─ SQL: SELECT * FROM transactions WHERE user_id=? ORDER BY time DESC LIMIT 10
│  └─ No time window; just last 10 chronologically

Serving (Real-time):
├─ When transaction arrives:
│  ├─ Query Redis: Get user:?:last_10_transactions
│  └─ This was last updated 5 minutes ago
│     → Real-time serving uses slightly stale data!

Problem: Training features ≠ Serving features
```

**Fix: Document & Enforce Exact Logic**

```
Feature Definition Document:

Feature Name: last_10_transactions
Training Computation:
├─ Time window: None (all history)
├─ SQL: SELECT transaction_id, amount, date FROM transactions 
│       WHERE user_id = ? AND date <= observation_date 
│       ORDER BY date DESC LIMIT 10
├─ Version: v2.1
└─ Last Updated: 2024-01-15

Real-time Computation:
├─ Location: Redis key user:?:last_10_transactions
├─ Update frequency: Every transaction
├─ Aggregation: Same SQL, but on streaming data
├─ Consistency: Both use identical averaging logic
└─ Verified: Weekly validation that offline & online match

**MUST MATCH**: If training uses DESC order, serving must too!
               If training aggregates mean, serving must use mean too!
```

**Skew Risk 2: Different Data Availability**

```
Training:
├─ Has complete 90-day history per user
├─ Computes: total_spend_90days = ₹100,000

Serving (Day 1):
├─ Only has 1 day of Redis data (new user)
├─ Computes: total_spend_available = ₹5,000 (only today's data)
└─ Feature differs dramatically!

Impact: Model trained on ₹100K values, now sees ₹5K → Poor predictions
```

**Fix: Warm-up Period**

```
Before launching real-time system:
├─ Pre-populate Redis with 90 days of historical data
│  └─ Run offline computation for all 10M users
│  └─ Load results into Redis
│
├─ Training: Built on same historical snapshot
├─ Serving: Starts with same warm Redis state
└─ Both have 90 days of data initially

Ongoing:
├─ New transactions continuously update Redis
├─ Old data (>90 days) automatically expires (TTL)
├─ Maintains sliding window of past 90 days
```

**Skew Risk 3: Time-Window Misalignment**

```
Training:
├─ Feature: daily_spend
├─ Computation: "Spend from start of today (midnight UTC)"
├─ Example: On 2024-01-15 at 14:00 UTC, sum transactions from 2024-01-15 00:00 UTC

Serving:
├─ Real-time: Incrementally adding to daily_spend
├─ At user's local time midnight, reset daily_spend
└─ Problem: User in IST (UTC+5:30), system uses UTC → 5.5 hour misalignment!

Impact: Training sees different "today" than serving
```

**Fix: Consistent Timezone**

```
Standardize on single timezone (UTC):

Training:
├─ All dates in UTC
├─ "today" = start of UTC day
└─ daily_spend = sum(transactions WHERE date >= today UTC)

Serving:
├─ Store timestamp in UTC
├─ Reset Redis counters at exactly UTC midnight (via cron job)
└─ All feature computations use UTC

Verification:
├─ Weekly: Compare training features (recomputed on same day) vs. Redis
└─ If mismatch > 2%, investigate and fix
```

**Skew Risk 4: Model Version Mismatch**

```
Training happened: 2024-01-10 with Model v1
├─ Features: [spend_30d, transaction_count, location_anomaly, ...]

Serving: 2024-01-15 with Model v2
├─ Features: [spend_30d, transaction_count, location_anomaly, merchant_risk, ...]
├─ Problem: Added merchant_risk feature; Model v1 doesn't know about it
│
└─ Result: Model v1 receives 5 features, expects 4 → ERROR

Fix: Version features with model
```

**Implementation**:
```
Model Registry:
├─ Model: fraud_detection_v2 
├─ Training Date: 2024-01-10
├─ Features Required: [spend_30d, trans_count, location_anomaly, merchant_risk]
│                     (ordered, specific)
├─ Feature Store Version: v2.1
└─ Serving Check:
   ├─ When loading model: Check feature versions match
   ├─ If mismatch: Refuse to load model (error before disaster)
   └─ Retrain on current features if they changed

Serving:
├─ Always load model + feature_requirements
├─ Compute exactly those features in exactly that order
├─ Pass to model_predict()
└─ No surprises!
```

**Automated Validation Pipeline**:
```
Daily (automated):
├─ 1. Compute offline features (training approach)
├─ 2. Compute online features (serving approach)
├─ 3. Compare:
│    ├─ For 1000 random users
│    ├─ Check if features match (within tolerance)
│    ├─ If match: All good ✓
│    └─ If mismatch: Alert and block serving until fixed
└─ Result: Email report to data team

Weekly (manual):
├─ Review 10 cases of training vs. serving features
├─ Spot-check: "Why did this user's spend_30d differ?"
├─ Update documentation if needed
└─ Sign-off on skew being minimal
```

---

## Q14. Embeddings & Semantic Search for Recommendations [7 Marks]

An e-commerce company wants to recommend similar products using embeddings. Currently using:

```
Product Features:
- Title: "Sony WH-1000XM5 Wireless Headphones"
- Category: Electronics / Audio
- Price: ₹24,999
- Description: 300 words
- Customer Reviews: 150 reviews totaling 2000 words

Need:
- Search: "noise cancelling headphones" → Show relevant products
- Recommendation: "Users who viewed Product A also viewed..."
- Personalization: "Based on your viewing history, you might like..."
```

### Design an embedding-based system:
a) How do embeddings work and why are they useful? (1.5 marks)
b) Design the embedding pipeline (2 marks)
c) Implement semantic search using embeddings (2 marks)
d) Address cold-start problem (1.5 marks)

### Answer:

**a) How Embeddings Work & Why Useful [1.5 marks]**

**Core Concept: From Sparse to Dense Representation**

```
Traditional Approach (Sparse):
├─ Product: "Sony WH-1000XM5 Wireless Headphones"
├─ Bag of Words encoding: [0,0,1,0,0,0,1,0,0,1,0,...,0] (100K dimensions)
│  └─ 1 = word present, 0 = not present
│  └─ Ultra-sparse: 99.9% zeros
├─ Problem: Many zeros; doesn't capture meaning
├─ Similarity: Hamming distance or Jaccard = surface-level

Embedding Approach (Dense):
├─ Same product encodes as: [0.32, -0.15, 0.89, 0.12, ..., -0.67] (384 dimensions)
│  └─ All dimensions have non-zero values
│  └─ 384-dimensional vector capturing semantic meaning
├─ Advantage: Meaning-dense; captures "what is this about?"
├─ Similarity: Cosine similarity = finds semantically similar products
```

**Why Embeddings Are Useful**:

```
1. Semantic Similarity:
├─ "Sony headphones" and "JBL headphones" → High cosine similarity (0.85)
├─ "Sony headphones" and "Toyota cars" → Low cosine similarity (0.15)
└─ Captures meaning, not just keywords

2. Reduces Dimensionality:
├─ 100K sparse dimensions → 384 dense dimensions
├─ 260× reduction in memory; faster search

3. Generalization:
├─ Training on "Sony WH-1000XM5" teaches model about "headphones" in general
├─ New product "JBL XB75" → Model understands it's similar even if no training data

4. Cross-modal Similarity:
├─ Embed product description (text) and product image (visual) in SAME space
├─ Can find "headphones similar to Sony" using either text OR image as query
└─ Enables multi-modal recommendations

5. Scalability:
├─ Query: Find 100 products most similar to a query
├─ Traditional: Compare against all 2M products = slow
├─ Embeddings + ANN (Approximate Nearest Neighbors) index = find top-100 in <50ms
```

---

**b) Embedding Pipeline Design [2 marks]**

**Architecture**:

```
┌──────────────────────────────────────────────────────┐
│         Product Data (2M products)                   │
│ ┌─ Title: "Sony WH-1000XM5..."                       │
│ ├─ Description: "Premium noise-cancelling..."        │
│ ├─ Reviews: "Amazing sound quality!"                 │
│ └─ Category: "Electronics/Audio"                     │
└──────────────────┬──────────────────────────────────┘
                   │
         ┌─────────▼─────────┐
         │ Embedding Models  │
         ├───────────────────┤
         │ 1. Text Embedding │ (Sentence-BERT)
         │ 2. Image Embedding│ (ResNet-50)
         │ 3. Aggregation    │ (Combine both)
         └─────────┬─────────┘
                   │
         ┌─────────▼──────────────┐
         │ Embedding Store        │
         │ (Vector Database)      │
         │ Pinecone / Weaviate    │
         └─────────┬──────────────┘
                   │
         ┌─────────▼──────────────┐
         │ ANN Index              │
         │ (Fast similarity search)│
         │ HNSW, Annoy            │
         └────────────────────────┘
```

**Step-by-Step Pipeline**:

**Step 1: Text Embedding (Offline, Monthly)**
```
For each product, concatenate text:
├─ Title: "Sony WH-1000XM5 Wireless Headphones"
├─ Category: "Electronics / Audio"
├─ Description: (300 words)
├─ Reviews: (2000 words from customer reviews, top 5 positive + negative)
└─ Combined text: ~2500 words per product

Encode using Sentence-BERT (all-MiniLM-L6-v2):
├─ Model: Trained on 215M+ sentence pairs
├─ Input: "Sony WH-1000XM5 wireless headphones for...professional audio..."
├─ Output: 384-dimensional vector
│  └─ Captures: Product type, brand, features, quality signals
└─ Time per product: ~50ms on CPU, parallelizable

Batch Process:
├─ 2M products / 50ms per product = 100K products/sec needed
├─ Use GPU: 20 GPUs × 50K products/GPU/sec = 1M products/sec
├─ Total time: 2M / 1M = 2 seconds (with batching)

Store:
├─ product_embeddings table:
│  ├─ product_id (primary key)
│  ├─ embedding (384-dim float32 vector, 1.5 KB)
│  └─ timestamp (when computed)
│
└─ Total storage: 2M × 1.5 KB = 3 GB
```

**Step 2: Image Embedding (Offline, Monthly)**
```
For each product with image:
├─ Load product image (high-res, 512×512px)
├─ Pass through ResNet-50 (pre-trained on ImageNet)
│  └─ Extracts 2048-dim feature vector
└─ Output: 2048-dimensional image embedding

Normalize to [0,1]:
├─ L2 normalization: ||v|| / ||v||_2 = normalized vector
└─ For cosine similarity later
```

**Step 3: Aggregation (Combine Text + Image)**
```
For each product:
├─ text_embedding (384-dim)
├─ image_embedding (2048-dim)
└─ Combine:
    ├─ Option 1: Concatenate = [384+2048] = 2432-dim (too high)
    ├─ Option 2: PCA reduce to 384-dim each, then concatenate = 768-dim ✓
    ├─ Option 3: Weighted avg = 0.6*text + 0.4*image = 384-dim (simple, works)
    └─ Option 4: Train a fusion network (complex, requires labeled data)

Use Option 3 (simple, effective):
└─ final_embedding = 0.6 * norm(text_embedding) + 0.4 * norm(image_embedding)
```

**Step 4: Index for Fast Search (ANN)**
```
Build Approximate Nearest Neighbors index:
├─ Algorithm: HNSW (Hierarchical Navigable Small World)
├─ Input: 2M product embeddings
├─ Output: Searchable index (~3-5GB in memory)
│
├─ Build time: ~30 minutes (one-time)
├─ Search time: Find 100 nearest neighbors in <50ms
└─ Stored: In-memory or in Pinecone/Weaviate cloud DB
```

**Step 5: Store in Vector Database**
```
Pinecone (cloud) or Weaviate (self-hosted):
├─ Store: product_id → 384-dim vector
├─ Index: HNSW for fast nearest neighbor search
├─ Query: Given product_id, find 100 similar products
└─ Response: <50ms
```

---

**c) Semantic Search Using Embeddings [2 marks]**

**Use Case 1: Text-Based Search**

```
User Query: "noise cancelling headphones under ₹30,000"

Step 1: Embed the query
├─ Query text: "noise cancelling headphones under ₹30,000"
├─ Encode using same BERT model: [0.23, -0.15, 0.78, ..., -0.32] (384-dim)
└─ This query embedding is in same space as product embeddings

Step 2: Find nearest neighbors
├─ Index query (HNSW): "What are the 1000 closest products to this embedding?"
├─ Pinecone returns: Top 1000 product_ids sorted by cosine similarity
│  └─ product_id=12345, similarity=0.92 (Sony WH-1000XM5)
│  └─ product_id=12346, similarity=0.89 (JBL XB75)
│  └─ product_id=12347, similarity=0.85 (Bose NC700)
│  └─ ...
└─ Time: <10ms

Step 3: Post-filter
├─ Filter results by price: Keep only < ₹30,000
├─ After filtering: 450 products remain
└─ Sort by rating/popularity

Step 4: Return top-10
├─ Display to user:
│  1. Sony WH-1000XM5 (₹24,999, ⭐4.8)
│  2. JBL XB75 (₹22,999, ⭐4.6)
│  3. Bose NC700 (₹28,999, ⭐4.7)
│  ... (7 more)
```

**Use Case 2: Image-Based Search (Visual Search)**

```
User Action: Uploads image of "Beats headphones"

Step 1: Embed the image
├─ Extract image features using ResNet-50
├─ Output: 2048-dim image embedding
└─ Convert to 384-dim (same as products)

Step 2: Find nearest neighbors
├─ Query vector database with image embedding
├─ Get top-100 visually similar products
│  └─ Include: Beats, Sony, JBL (all look like headphones)

Step 3: Return results
├─ "Products similar to the image you uploaded"
└─ Display 10 visually similar headphones
```

**Use Case 3: Personalized Recommendations**

```
User History: Viewed Sony WH-1000XM5, JBL XB75, Bose NC700

Step 1: Embed user history
├─ Average embeddings of viewed products:
│  user_vector = (embed_Sony + embed_JBL + embed_Bose) / 3
└─ Output: User's interest vector (384-dim)

Step 2: Find nearest neighbors
├─ Query: "What products are similar to user's interests?"
├─ Index returns: Top 100 products nearest to user_vector
│  └─ Sennheiser Momentum 4 (hasn't viewed yet)
│  └─ Philips Fidelio X2H (hasn't viewed yet)
│  └─ Audio-Technica M50x (hasn't viewed yet)
└─ Filter: Exclude already viewed products

Step 3: Return recommendations
├─ "Recommended for you based on your browsing history"
├─ Personalized ranking: Products similar to what user likes
└─ Display top-10
```

**Implementation (Pseudocode)**:
```python
def search_products(query_text, top_k=10, price_filter=30000):
    # Embed query
    query_embedding = bert_model.encode(query_text)
    
    # Search in vector DB
    results = pinecone_index.query(
        vector=query_embedding,
        top_k=1000,  # Get more, then filter
        namespace="products"
    )
    
    # Post-filter by price
    filtered = [r for r in results if products[r.id].price <= price_filter]
    
    # Sort by rating
    filtered = sorted(filtered, key=lambda x: products[x.id].rating, reverse=True)
    
    # Return top 10
    return filtered[:10]
```

---

**d) Address Cold-Start Problem [1.5 marks]**

**Cold-Start Challenge**:
```
Problem 1: New Product (No Embeddings)
├─ A new product is added to catalog
├─ No embedding computed yet
├─ Search returns: "Product not in index"
└─ Time to first recommendation: 1 week (wait for monthly re-embedding)

Problem 2: New User (No History)
├─ User just signed up
├─ No viewing history to average
├─ Can't compute user vector
└─ Personalized recommendations: Not available initially

Problem 3: Rare Products
├─ Niche product (only 5 views in 6 months)
├─ Few embeddings neighbors (only looks like 3 other products)
└─ Recommendations become stale
```

**Solution 1: Real-Time Embedding for New Products**

```
When new product added:
├─ 1. Trigger embedding pipeline immediately (don't wait for monthly)
├─ 2. Extract text: Title, category, description, initial reviews
├─ 3. Encode using BERT: Generate 384-dim embedding in <1 second
├─ 4. (Skip image embedding if not available yet; use text-only)
├─ 5. Insert into Pinecone immediately
├─ 6. Result: Product searchable within 5 minutes ✓

Pseudocode:
    on_product_added(product_id, title, description):
        # Immediately embed (not waiting for batch)
        embedding = fast_embed_text(title + description)
        pinecone_index.upsert(product_id, embedding)
        return "Product now searchable"
```

**Solution 2: Hybrid Recommendations for New Users**

```
Cold-start user (0 history):
├─ Can't use collaborative filtering (no history)
├─ Fall back to content-based:
│  ├─ Show trending products (trending_products query)
│  ├─ Show popular in user's inferred category
│  └─ Show recently added products
│
└─ As user browses, collect signal and personalize

After 5 views:
├─ Compute user_vector = avg(viewed products)
├─ Switch to personalized recommendations ✓
```

**Solution 3: Category-Based Fallback**

```
For rare products (few similar products):
├─ Direct search: Find similar headphones → Only 3 neighbors
├─ Fallback search: Find other products in "Audio" category → 500 products
├─ Blend:
│  ├─ 70% category matches (more variety)
│  ├─ 30% similar products (if available)
│  └─ Display mixed results
```

**Solution 4: Popularity & Engagement Signals**

```
When embeddings alone aren't enough:
├─ Mix similarity score with engagement signals:
│  final_score = 0.7 * embedding_similarity + 0.3 * popularity_score
│
├─ Popularity_score: Based on views, clicks, conversions
└─ New products ranked higher despite fewer embeddings neighbors

Example:
├─ Product A: 0.85 similarity, 0.2 popularity → final = 0.75 × 0.85 + 0.25 × 0.2 = 0.694
├─ Product B: 0.70 similarity, 0.8 popularity → final = 0.75 × 0.70 + 0.25 × 0.8 = 0.725 ✓ (ranked higher)
└─ Balances discovery with quality
```

**Solution 5: Contextual Embeddings**

```
Instead of global product embedding, compute contextual embeddings:
├─ Context: "What product is this relative to?"
│  
├─ Query 1: "headphones similar to Sony WH-1000XM5" 
│  └─ Compare in "premium headphones" space
│
├─ Query 2: "cheap headphones for gym"
│  └─ Compare in "budget sports headphones" space
│
└─ Dynamically compute embeddings for each context (fast, <100ms)
```

---

## Q15. LLMs, Generative AI & RAG in Enterprise [8 Marks]

A bank wants to deploy an LLM-based knowledge base Q&A system for employees. Requirements:

```
Use Case: Customer service reps ask questions about:
- Loan terms and conditions
- Complaint resolution procedures
- Fraud detection protocols
- KYC compliance rules

Data:
- 500 policy documents (10M words total)
- Policy updates every week
- Queries must return accurate, current answers
- Cannot tolerate hallucinations (must cite source)
```

### Design the system:
a) Explain LLM basics and hallucination risks (1.5 marks)
b) Compare: Prompt engineering vs. RAG vs. Fine-tuning (2 marks)
c) Implement RAG architecture (2.5 marks)
d) Prevent hallucinations and ensure accuracy (2 marks)

### Answer:

**a) LLM Basics & Hallucination Risks [1.5 marks]**

**How LLMs Work**:

```
LLMs are Prediction Machines:
├─ Input: "What is the loan interest rate for..."
├─ LLM thinks: "Based on patterns in training data, the next tokens are..."
├─ Output: Generates one token at a time
│  ├─ Token 1: "The"
│  ├─ Token 2: "current"
│  ├─ Token 3: "interest"
│  ├─ Token 4: "rate"
│  ├─ Token 5: "is"
│  ├─ Token 6: "7.5%"
│  └─ ...
│
└─ Process repeats until model generates [END] token

Temperature Parameter:
├─ Temperature = 0.1 (Deterministic): Always pick highest probability token
│  └─ Used for factual questions (e.g., loan rates)
│
├─ Temperature = 0.7 (Balanced): Mix randomness and probability
│  └─ Used for creative tasks
│
└─ Temperature = 1.5 (Creative): High randomness, explores alternatives
    └─ Used for brainstorming
```

**Hallucination: The Problem**

```
Query: "What is XYZ Bank's policy on joint account closures?"

Model might respond:
├─ "According to XYZ Bank policy doc-2024-03, joint accounts...
│  can be closed by either account holder after 30 days notice,
│  and the funds will be equally distributed..."
│
└─ Problem: Model NEVER SAW "doc-2024-03" or this policy!
            Model generated plausible-sounding but FALSE information

Why Hallucinations Happen:
├─ 1. Training Data Gap:
│    └─ Model trained on generic internet text, not bank-specific policies
│    └─ When asked about policies, must "guess" based on general knowledge
│
├─ 2. Confidence Mismatch:
│    └─ Model generates text with high confidence regardless of certainty
│    └─ Can't distinguish "I know this" from "I'm making it up"
│
├─ 3. Pattern Completion:
│    └─ Model sees "XYZ Bank policy says..." and completes the sentence
│    └─ Completion matches patterns in training data, but may be wrong
│
└─ 4. Combinatorial Explosion:
     └─ Model combines facts from different sources
     └─ "Bank A has 30-day closure + Bank B has equal distribution"
        → Generates hybrid policy that doesn't exist
```

**Cost of Hallucinations in Banking**:
```
Employee acts on hallucinated policy:
├─ Tells customer: "You can close your account in 30 days" (WRONG)
├─ Customer angry when told actual policy is 60 days
├─ Bank reputation damage
├─ Regulatory risk (if policy advice is false)
└─ Compliance violation
```

---

**b) Compare: Prompt Engineering vs. RAG vs. Fine-tuning [2 marks]**

| Approach | Mechanism | Pros | Cons | Use Case |
|----------|-----------|------|------|----------|
| **Prompt Engineering** | Add context directly in prompt: "Use this policy: ..." + query | Fast, no training, easy to update | Tokens limited (4K-100K); expensive for large docs; no learning | Simple Q&A with small context |
| **RAG (Retrieval-Augmented Generation)** | 1. Search relevant documents 2. Embed in prompt 3. Generate | Retrieves specific docs; factual; scalable to 1M docs; cites sources; updates automatic | Requires indexing pipeline; quality depends on retrieval | Bank Q&A, documentation lookup |
| **Fine-tuning** | Train LLM on domain-specific data (500 bank policies) | Specialized knowledge; model "understands" domain; efficient inference | Expensive ($1K-10K per fine-tune run); slow to update policies; needs many examples | General domain mastery (e.g., "become expert on banking") |

**Recommendation for Bank**: **RAG** ✓
```
Why RAG:
├─ Need to answer using actual current policies (not hallucinated)
├─ Policies update weekly (fine-tuning too slow)
├─ Must cite which policy document was used
├─ Can scale to 500 documents + future growth
└─ Cost-effective (no training required)
```

---

**c) Implement RAG Architecture [2.5 marks]**

**Architecture Overview**:

```
┌────────────────────────────────────────────────────┐
│         Policy Documents (500 files, 10M words)     │
│    - Loan Terms & Conditions (50 docs)              │
│    - Complaint Resolution (100 docs)                │
│    - Fraud Protocols (80 docs)                      │
│    - KYC Rules (270 docs)                           │
└────────────────────┬─────────────────────────────────┘
                     │
            ┌────────▼───────────┐
            │ Document Chunking  │ (Preprocess)
            │ & Embedding        │
            └────────┬───────────┘
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    Chunk 1      Chunk 2      Chunk 3
    "Loan         "For         "Interest
     rates        business     rates
     are..."      loans,..."   vary..."
        │            │            │
        ▼            ▼            ▼
    Embedding   Embedding   Embedding
    (384-dim)   (384-dim)   (384-dim)
        │            │            │
        └────────────┼────────────┘
                     │
            ┌────────▼─────────┐
            │  Vector Database  │
            │  (Pinecone/       │
            │   Weaviate)       │
            └────────┬──────────┘
                     │
        ┌────────────┼────────────┐
        │   Employee Query        │
        │ "What's the loan rate   │
        │  for business accounts?"│
        └────────────┬────────────┘
                     │
            ┌────────▼────────┐
            │ Embed Query     │
            │ (384-dim)       │
            └────────┬────────┘
                     │
            ┌────────▼───────────┐
            │ Retrieve Top-3      │
            │ Matching Chunks     │
            │ via Similarity      │
            └────────┬───────────┘
                     │
     ┌───────────────┼───────────────┐
     │  Construct Prompt:            │
     │  "Using this context:...      │
     │   Chunk 1: 'For business...'  │
     │   Chunk 2: 'Interest rates...'│
     │   Chunk 3: 'Rates vary...'    │
     │                               │
     │  Question: What's the loan    │
     │  rate for business accounts?" │
     └───────────────┬───────────────┘
                     │
            ┌────────▼────────┐
            │ LLM (GPT-4 or   │
            │  Claude)        │
            │ Generates Answer│
            └────────┬────────┘
                     │
     ┌───────────────▼───────────────┐
     │ Output:                       │
     │ "Based on our business loan   │
     │  policy (doc-2024-01):        │
     │                               │
     │  For business accounts, the   │
     │  current interest rate is     │
     │  8.5% - 12% depending on:     │
     │  - Loan amount                │
     │  - Business type              │
     │  - Credit score"              │
     │                               │
     │ Source: doc-2024-01.pdf       │
     └──────────────────────────────┘
```

**Detailed Implementation**:

**Step 1: Document Preparation (Offline, Weekly)**

```python
# When new policy documents arrive

def prepare_documents(policy_files):
    documents = []
    
    for file in policy_files:
        text = extract_text(file)  # PDF → text
        
        # Split into overlapping chunks
        chunks = chunk_text(
            text,
            chunk_size=512,           # ~100 words per chunk
            overlap=100               # 20% overlap
        )
        
        for i, chunk in enumerate(chunks):
            documents.append({
                "id": f"{file.name}-chunk-{i}",
                "text": chunk,
                "source": file.name,
                "date": datetime.now()
            })
    
    return documents

# Result: 500 files → ~50,000 chunks
```

**Step 2: Indexing (Offline, Weekly)**

```python
def index_documents(documents):
    embeddings = []
    
    # Batch embed all chunks
    for chunk in documents:
        embedding = embed_model.encode(chunk["text"])
        embeddings.append({
            "id": chunk["id"],
            "vector": embedding,
            "metadata": {
                "text": chunk["text"],
                "source": chunk["source"],
                "date": chunk["date"]
            }
        })
    
    # Upsert to vector DB
    pinecone_index.upsert(embeddings)
    
    return "Indexed 50K chunks"
```

**Step 3: Query & Retrieval (Real-time, <50ms)**

```python
def retrieve_context(query, top_k=3):
    # Embed user query
    query_embedding = embed_model.encode(query)
    
    # Search vector DB for similar chunks
    results = pinecone_index.query(
        vector=query_embedding,
        top_k=top_k,
        include_metadata=True
    )
    
    # Extract context
    context_chunks = []
    for result in results:
        context_chunks.append({
            "text": result.metadata["text"],
            "source": result.metadata["source"],
            "similarity": result.score
        })
    
    return context_chunks

# Example output:
# [
#   {"text": "For business loans, interest rates are 8.5%-12%...", 
#    "source": "BusinessLoanTerms.pdf", "similarity": 0.94},
#   {"text": "Interest rates vary based on...", 
#    "source": "RateFactors.pdf", "similarity": 0.87},
#   ...
# ]
```

**Step 4: Prompt Construction (Deterministic)**

```python
def construct_prompt(query, context_chunks):
    prompt = f"""
You are a helpful bank policy assistant. Use the following documents
to answer the employee's question. If the answer is not in the documents,
say "I don't have this information in current policies."

DOCUMENTS:
"""
    
    for i, chunk in enumerate(context_chunks):
        prompt += f"""
--- Document {i+1}: {chunk['source']} ---
{chunk['text']}
"""
    
    prompt += f"""
QUESTION: {query}

ANSWER: (Cite which document you're using)
"""
    
    return prompt
```

**Step 5: LLM Call (Deterministic)**

```python
def generate_answer(prompt):
    response = client.chat.completions.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "You are a bank policy expert. Be accurate. Cite sources."},
            {"role": "user", "content": prompt}
        ],
        temperature=0.2,  # Low temperature = deterministic, factual
        max_tokens=500
    )
    
    return response.choices[0].message.content
```

**Complete Flow (Pseudocode)**:

```python
def rag_query(employee_question):
    # 1. Retrieve
    context_chunks = retrieve_context(employee_question, top_k=3)
    
    # 2. Construct prompt
    prompt = construct_prompt(employee_question, context_chunks)
    
    # 3. Generate
    answer = generate_answer(prompt)
    
    # 4. Return with sources
    return {
        "answer": answer,
        "sources": [chunk["source"] for chunk in context_chunks],
        "confidence": "high" if top_result.similarity > 0.85 else "medium"
    }

# Example:
# Input: "What's the interest rate for business loans?"
# Output:
# {
#   "answer": "Based on BusinessLoanTerms.pdf, interest rates are 8.5-12% depending on...",
#   "sources": ["BusinessLoanTerms.pdf", "RateFactors.pdf"],
#   "confidence": "high"
# }
```

---

**d) Prevent Hallucinations & Ensure Accuracy [2 marks]**

**Strategy 1: Retrieval Quality Gating**

```python
def rag_query_with_gating(employee_question):
    context_chunks = retrieve_context(employee_question, top_k=3)
    
    # Check retrieval quality
    top_similarity = context_chunks[0]["similarity"]
    
    if top_similarity < 0.70:
        # No good match found
        return {
            "answer": "I couldn't find relevant policies for this question. "
                      "Please consult the policy database or ask an administrator.",
            "sources": [],
            "confidence": "low"
        }
    
    # Good match, proceed with generation
    prompt = construct_prompt(employee_question, context_chunks)
    answer = generate_answer(prompt)
    
    return {
        "answer": answer,
        "sources": [chunk["source"] for chunk in context_chunks],
        "confidence": "high"
    }
```

**Strategy 2: Constraint-Based Generation**

```python
def generate_answer_constrained(prompt):
    # Add explicit constraints to prompt
    constrained_prompt = prompt + """

STRICT RULES:
1. Only use information from the provided documents
2. If information is not in the documents, respond: "This is not covered in current policies."
3. Always cite the source document (e.g., "According to BusinessLoanTerms.pdf...")
4. Do NOT make up rates, dates, or procedures
5. If uncertain, ask for clarification rather than guess

Remember: Accuracy is more important than providing an answer.
"""
    
    response = client.chat.completions.create(
        model="gpt-4",
        messages=[...],
        temperature=0.0,  # Lowest temperature = most deterministic
        max_tokens=500
    )
    
    return response.choices[0].message.content
```

**Strategy 3: Source Verification (Post-Generation)**

```python
def verify_source_claims(answer, sources):
    # After generation, verify that answer claims match actual source content
    
    verification_results = []
    
    for source in sources:
        source_text = pinecone_index.get(source)["metadata"]["text"]
        
        # Check if main claims in answer appear in source
        claims = extract_claims(answer)  # NLP to extract factual claims
        
        for claim in claims:
            if claim_found_in_source(claim, source_text):
                verification_results.append({
                    "claim": claim,
                    "source": source,
                    "verified": True
                })
            else:
                # Claim not found in source → potential hallucination
                verification_results.append({
                    "claim": claim,
                    "source": source,
                    "verified": False  # ⚠️ Flag for review
                })
    
    if any(not v["verified"] for v in verification_results):
        return {
            "answer": answer,
            "verification": "⚠️ Some claims not verified against sources",
            "requires_review": True
        }
    
    return {
        "answer": answer,
        "verification": "✓ All claims verified against sources",
        "requires_review": False
    }
```

**Strategy 4: Human-in-the-Loop for High-Stakes Queries**

```python
def rag_query_with_escalation(employee_question, is_high_stakes=None):
    context_chunks = retrieve_context(employee_question)
    answer = generate_answer_constrained(...)
    verification = verify_source_claims(answer, ...)
    
    # Determine if escalation needed
    escalate = (
        verification.get("requires_review") or
        is_high_stakes or
        answer.contains("UNCERTAIN") or
        len(sources) == 0
    )
    
    if escalate:
        # Send to human expert for review
        send_to_queue(
            queue="policy_expert_review",
            content={
                "question": employee_question,
                "ai_answer": answer,
                "sources": context_chunks,
                "verification": verification,
                "priority": "HIGH"
            }
        )
        
        return {
            "answer": "This question has been escalated to a policy expert for accurate review. "
                      "You'll receive a verified answer within 1 hour.",
            "sources": [],
            "confidence": "pending_review"
        }
    
    return {
        "answer": answer,
        "sources": [chunk["source"] for chunk in context_chunks],
        "confidence": "high",
        "verification": verification
    }
```

**Strategy 5: Continuous Monitoring & Retraining**

```python
def monitor_accuracy(days=30):
    # Collect feedback from employees
    recent_queries = get_queries(since=days_ago(30))
    
    feedback_summary = {
        "total_queries": len(recent_queries),
        "helpful": sum(1 for q in recent_queries if q.feedback == "helpful"),
        "incorrect": sum(1 for q in recent_queries if q.feedback == "incorrect"),
        "outdated": sum(1 for q in recent_queries if q.feedback == "outdated_policy"),
    }
    
    accuracy_rate = feedback_summary["helpful"] / feedback_summary["total_queries"]
    
    if accuracy_rate < 0.90:  # Alert threshold
        alert("RAG accuracy dropped to {}%".format(accuracy_rate * 100))
        
        # Root cause analysis
        incorrect_cases = [q for q in recent_queries if q.feedback == "incorrect"]
        
        for case in incorrect_cases:
            # Analyze: Was retrieval bad? Was generation bad?
            # Re-index documents if policies changed
            # Retune system
            pass

# Weekly accuracy dashboard:
# - Week 1: 94% accuracy ✓
# - Week 2: 89% accuracy ⚠️ (investigate)
# - Week 3: 91% accuracy (policy updated, retrained)
```

---

## Summary: 15 Questions Total

| Q# | Topic | Marks | Difficulty |
|----|-------|-------|-----------|
| Q1 | Predictive Maintenance System | 7 | Medium |
| Q2 | Healthcare AI Architecture | 10 | Hard |
| Q3 | ML Model Degradation | 7 | Medium |
| Q4 | AI Project Selection | 6 | Medium |
| Q5 | Feature Engineering | 6 | Medium |
| Q6 | Data Pipelines | 8 | Hard |
| Q7 | **Foundation Models** ✓ | 6 | Medium |
| Q8 | **Unstructured Data** ✓ | 7 | Hard |
| Q9 | **Data Quality & Fairness** ✓ | 6 | Medium |
| Q10 | **Temporal Data & Leakage** ✓ | 7 | Hard |
| Q11 | **Real-Time Feature Stores** ✓ | 8 | Hard |
| Q12 | **Embeddings & Search** ✓ | 7 | Hard |
| Q13 | **LLMs & RAG** ✓ | 8 | Hard |
| Q14 | Q7 Renamed (Foundation Models) | - | - |
| Q15 | Q8 Renamed (Unstructured Data) | - | - |
| | **TOTAL** | **107 marks** | |

**Coverage**: All 8 sessions + topics beyond slides covered comprehensively
