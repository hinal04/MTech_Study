# Session 5: Data — Types, Quality, Governance (44 Slides)

> BITS Pilani — SS ZG662 | Module 2: Data & Feature Engineering

---

## 5.1 Three Types of Data

| Type | Structure | % of Enterprise Data | Storage | Example |
|---|---|---|---|---|
| **Structured** | Rows & columns, fixed schema | ~20% | SQL databases | Customer table, transactions |
| **Semi-structured** | Self-describing, flexible schema | ~10% | JSON/XML stores | API responses, log files |
| **Unstructured** | No predefined schema | ~70% | Object storage, file systems | Images, text, audio, video |

### Hidden Structure in Unstructured Data

Even unstructured data contains extractable structure:
- **Images:** EXIF metadata (camera, GPS, timestamp)
- **Emails:** Headers (sender, date, subject), body structure
- **PDFs:** Title, sections, tables

**Naukri.com example:** Resumes (unstructured) contain structured fields: name, skills, experience, education — extractable via NLP.

### Temporal Data

- **Point-in-time correctness:** Features must only use data available at prediction time
- **No future leakage:** Never include data from after the prediction point in features
- **Temporal train/test split:** Train on past, test on future (never random split for time-dependent data)

**HDFC Bank loan example:** Feature "credit score" must be the score at application time, not the current score.

### Event vs State Data

| Aspect | Event Data | State Data |
|---|---|---|
| Nature | Immutable, append-only | Mutable, overwritten |
| Represents | What HAPPENED | What IS now |
| Example | "Customer purchased item X at time T" | "Customer balance = ₹50,000" |
| Storage | Event log, Kafka | Database row |

**Paytm example:** Transaction events (immutable log) vs current wallet balance (mutable state).

---

## 5.2 Data Sources

### Internal Sources

Databases, application logs, CRM systems, data warehouses, user-generated content, IoT sensors.

### External Sources

Public datasets (government, research), paid APIs (weather, maps), third-party vendors (credit bureaus), web scraping, pre-trained model outputs.

### Human-Generated Data & Label Quality

- **Inter-annotator disagreement:** Different humans label same data differently
- **Cohen's kappa (κ):** Measures agreement between annotators (1.0 = perfect, 0 = random chance)
- **Flipkart fashion labelling example:** Annotators disagreed on "casual" vs "semi-formal" — label definition needed standardization

### Synthetic Data

| Method | How |
|---|---|
| GANs | Generate realistic fake data |
| LLMs | Generate text data from prompts |
| Simulation | Rule-based data generation |
| Rule-based | Apply transformations to existing data |

**Aadhaar verification example:** Synthetic face images for testing without real PII.
**Risk:** Synthetic data can amplify biases present in the generation process.

### Labels as Business Judgments

Label definition directly affects model behavior. **Churn example:** Defining churn as 30 days vs 60 days vs 90 days of inactivity produces completely different models.

**Hotstar/JioCinema example:** "Engaged user" defined differently by each platform — impacts what the recommendation model optimizes for.

---

## 5.3 Data Quality

### 6 Dimensions

| Dimension | Meaning |
|---|---|
| **Accuracy** | Data correctly represents real-world values |
| **Completeness** | No missing values where expected |
| **Consistency** | Same data doesn't contradict across sources |
| **Timeliness** | Data is current and available when needed |
| **Validity** | Data conforms to defined rules/formats |
| **Uniqueness** | No duplicate records |

### Common Quality Issues in ML

Missing values, outliers, duplicates, label noise, data leakage, class imbalance, schema drift.

### Credit Scoring Example — 4 Issues

1. **Missing employment data** — incomplete features
2. **Data leakage** — using credit score (which encodes the outcome) as a feature
3. **Sampling bias** — training only on approved applicants (never see rejected ones)
4. **Class imbalance** — 95% non-default, 5% default

### Quality by Population Slice

Aggregated metrics hide disparities. Overall accuracy 90% could be:
- Group A: 95% accuracy
- Group B: 75% accuracy

**Health-tech example:** Speech recognition works well for English but poorly for regional Indian languages — quality varies by population slice.

### Missingness Is Data

| Type | Meaning | Example |
|---|---|---|
| **MCAR** | Missing Completely At Random | Random sensor failures |
| **MAR** | Missing At Random (depends on observed data) | Younger patients less likely to have cholesterol test |
| **MNAR** | Missing Not At Random (depends on missing value itself) | High-income people don't report income |

**"is_missing" indicator features:** Create binary column indicating missingness — the pattern of missing data itself can be predictive.

**CIBIL example:** Missing credit history doesn't mean "no history" — it means CIBIL doesn't have records, which is itself a risk signal.

---

## 5.4 Data Governance

### 7 Components

1. **Ownership** — who is responsible for each dataset
2. **Access control** — who can read/write/delete
3. **Lineage** — tracking data from source through transformations
4. **Cataloging** — metadata registry for discoverability
5. **Privacy/Compliance** — GDPR, DPDP Act adherence
6. **Retention** — how long data is kept, when deleted
7. **Quality standards** — defined thresholds and monitoring

### Centralized vs Federated Governance

| Aspect | Centralized | Federated | Data Mesh (Hybrid) |
|---|---|---|---|
| Control | Single team owns all governance | Each domain owns its data | Domain ownership + central standards |
| Speed | Slow (bottleneck) | Fast (autonomous) | Balanced |
| Consistency | High | Low (may diverge) | Medium-high |

**Razorpay example:** Moved from centralized to data mesh — each team (payments, lending, banking) owns their data with shared quality standards.

### Data Contracts

Agreement between data producer and consumer:

| Element | What It Specifies |
|---|---|
| Schema | Exact fields, types, formats |
| Freshness SLA | Data available within X hours |
| Quality thresholds | Max missing rate, min accuracy |
| Ownership | Who produces, who consumes |
| Access policies | Who can access, under what conditions |
| Change management | How schema changes are communicated |

### AI Data Contracts (extend traditional contracts)

Additional requirements beyond traditional contracts:
- Label quality metrics
- Distribution stability guarantees
- Bias metrics per demographic group
- Drift thresholds
- Training/serving consistency guarantees

**Enforcement:** Automated validation at pipeline boundaries — reject data that violates contract.

### AI Data Classification

| Sensitivity | + AI Usage |
|---|---|
| Public / Internal / Confidential / Restricted | Training / Evaluation / Production / Feedback |

Each combination has different governance rules.

### GenAI Governance

- **Training data copyright** — was model trained on copyrighted content?
- **Generated content ownership** — who owns AI-generated output?
- **Prompt injection** — malicious inputs bypassing safety
- **Hallucination governance** — processes for catching/preventing false outputs

### Data Incidents

Types: Source outage, schema change, data corruption, late-arriving data, silent failure.

**Blast radius mapping:** Understand which models/applications break when a data source fails.

### GDPR + DPDP Act

| Regulation | Key Requirements | Penalty |
|---|---|---|
| GDPR (EU) | Consent, right to deletion, DPO, data portability | 4% of global revenue |
| DPDP Act (India) | Consent, purpose limitation, data localization | ₹250 crore |

### Cambridge Analytica Case Study

- 87 million Facebook users' data harvested without consent
- $5 billion fine for Facebook
- **Root cause:** No access control, no audit trail, no consent verification

---

## 5.5 Enterprise Data Management for AI

### Enterprise DM vs ML-Ready DM (Gap)

| Enterprise DM (traditional) | ML-Ready DM (needed for AI) |
|---|---|
| Focus on reporting/BI | Focus on training/inference |
| Schema stability | Feature versioning |
| Point-in-time optional | Point-in-time mandatory |
| Lineage nice-to-have | Lineage critical |
| No feature store | Feature store essential |
| No train-serve consistency | Train-serve consistency required |

**ICICI Bank example:** Had excellent enterprise data management for reporting, but needed significant upgrades for ML — feature store, versioning, lineage tracking.

### Data Maturity Model (5 Levels)

| Level | Name | Description |
|---|---|---|
| 1 | Ad-hoc | No formal data management |
| 2 | Managed | Basic processes, some documentation |
| 3 | Defined | Standardized processes across org |
| 4 | Quantified | Measured quality, automated monitoring |
| 5 | Optimized | Continuous improvement, ML-ready |

### Traditional vs Modern Architecture

| Traditional | Modern |
|---|---|
| OLTP → ETL → Warehouse → BI | Streams + S3 → ELT → Lakehouse → ML + BI |
| Batch only | Batch + streaming |
| Schema-on-write | Schema-on-read |

**Zerodha example:** Modern architecture — real-time streams for market data, lakehouse for analytics + ML.

### Data Products

Treat datasets as products with: owner, documentation, quality metrics, versioning, SLA.

**PhonePe example:** Each team publishes data products with guaranteed quality and freshness SLAs.

---

## 5.6 AI Data Readiness

### 9-Area Assessment Checklist

Data availability, data quality, data labeling, data governance, data infrastructure, feature engineering, data pipelines, monitoring, team capability.

### 90-Day Transformation Roadmap

| Phase | Days | Focus |
|---|---|---|
| Foundation | 1-30 | Data audit, governance setup, quality baseline |
| Infrastructure | 31-60 | Pipeline automation, feature store, monitoring |
| First Model | 61-90 | End-to-end ML pipeline, model to production |

**Lenskart example:** Followed this roadmap — from ad-hoc data to production ML in 90 days.

### Target Architecture

Unified data platform + Feature Store + Model Registry + ML Pipelines + Monitoring + Governance — all integrated.

---
