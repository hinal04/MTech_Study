# Session 5: Structured and Unstructured Data, Data Sources, Quality & Governance

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 3 (Data Engineering and Feature Engineering)
>
> **Contact Session:** 5 (Module 2: Data and Feature Engineering)

---

## Table of Contents

- [5.1 Structured and Unstructured Data](#51-structured-and-unstructured-data)
- [5.2 Data Sources for AI Systems](#52-data-sources-for-ai-systems)
- [5.3 Data Quality](#53-data-quality)
- [5.4 Data Governance](#54-data-governance)

---

## 5.1 Structured and Unstructured Data

### Why Does Data Matter So Much in AI?

In normal software, the logic is in the **code** — a programmer writes rules, and the computer follows them. In AI/ML, the logic is in the **data** — the model learns its behaviour from the data it sees.

> **Analogy:** In traditional software, you're writing a recipe book (code). In AI, you're showing the system 10,000 cooked dishes and letting it figure out the recipes itself. If you show it bad food, it learns to cook bad food. **Better data almost always beats a better model.**

**Key fact:** Collecting, cleaning, and managing data takes up **80% of a typical AI project's time**. The actual model training is only 20%.

### Three Types of Data

#### 1. Structured Data — "Neat rows and columns"

**What it is:** Data organised in a fixed, predefined format — like a spreadsheet with rows and columns. Every record has the same fields.

**Think of it as:** A neatly filled school attendance register — every row is a student, every column is a date, every cell is "Present" or "Absent."

| Characteristic | Explanation |
|---|---|
| Fixed schema | Every row has the same columns with the same data types |
| Easy to query | You can use SQL to search and filter |
| Stored in | Relational databases (MySQL, PostgreSQL) or columnar stores (BigQuery, Redshift) |
| Represents | Transactional data — orders, payments, sensor readings |

**Live Examples of Structured Data in AI:**

| Data | What It Looks Like | AI Use Case | Company |
|---|---|---|---|
| Customer transactions | customer_id, amount, merchant, timestamp | **Fraud detection** — spot unusual spending patterns | **HDFC Bank** flags suspicious transactions in milliseconds |
| Ride data | pickup_lat, pickup_lng, dropoff_lat, dropoff_lng, time, fare | **Surge pricing** — predict demand in each area | **Uber/Ola** dynamically prices rides based on predicted demand |
| Sensor readings | sensor_id, temperature, pressure, vibration, timestamp | **Predictive maintenance** — predict machine failures | **Tata Steel** predicts equipment failures from sensor data |
| User clickstream | user_id, page_url, action, timestamp, session_id | **Recommendation** — predict what user wants to buy | **Flipkart** personalises product recommendations |

#### 2. Unstructured Data — "No fixed format"

**What it is:** Data without a predefined structure — each item can be completely different in format, length, and content. A photo is unstructured. A customer review is unstructured. An audio recording is unstructured.

**Think of it as:** A box of random notes, photos, voice recordings, and doodles. There's valuable information in there, but it's not organised.

**Key fact:** **80-90% of all data generated globally is unstructured.** Most of the world's information is in text, images, audio, and video — not in spreadsheets.

| Data Type | Examples | AI Technique Used | Live Example |
|---|---|---|---|
| **Text** | Emails, reviews, documents, social media posts, chat messages | NLP (Natural Language Processing), LLMs | **Swiggy** analyses restaurant reviews using NLP to identify complaints ("food was cold," "late delivery") |
| **Images** | Photos, medical scans, satellite images, product photos | CNNs (Convolutional Neural Networks) | **Lenskart** uses AI to let you "try on" glasses virtually from your photo |
| **Audio** | Call centre recordings, voice messages, podcasts | Speech Recognition | **Jio** transcribes millions of customer service calls to identify common issues |
| **Video** | CCTV footage, sports broadcasts, user-uploaded content | Video Understanding | **Hotstar** uses AI to automatically generate highlights from cricket matches |
| **Documents** | PDFs, scanned forms, invoices, contracts | OCR + NLP | **ClearTax** uses OCR to read scanned invoices and auto-fill tax forms |

#### 3. Semi-Structured Data — "Partially organised"

**What it is:** Data that has some structure (tags, keys, nesting) but doesn't fit neatly into rows and columns. Each record can have different fields.

**Think of it as:** A resume — it has some standard sections (name, education, experience) but the content and format varies from person to person.

**Common formats:** JSON, XML, YAML, HTML, log files

**Example — A Swiggy order event (JSON):**
```json
{
  "order_id": "ORD-789456",
  "user_id": "USR-12345",
  "timestamp": "2025-09-07T19:30:00Z",
  "restaurant": {
    "id": "REST-456",
    "name": "Biryani Blues",
    "cuisine": "Hyderabadi"
  },
  "items": [
    {"name": "Chicken Biryani", "qty": 2, "price": 349},
    {"name": "Raita", "qty": 1, "price": 49}
  ],
  "delivery": {
    "address": "Koramangala, Bangalore",
    "distance_km": 3.5,
    "driver_id": "DRV-789"
  },
  "payment": {"method": "UPI", "amount": 747}
}
```

This has structured parts (order_id, price, amount) and variable parts (number of items varies, some orders have coupons, some don't). AI systems must **parse and flatten** this into features.

### Comparison Table

| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Format** | Fixed rows & columns | Flexible (JSON, XML) | No format (raw text, images) |
| **Schema** | Predefined, rigid | Self-describing, flexible | None |
| **Storage** | SQL databases (MySQL, PostgreSQL) | Document DBs (MongoDB), Data Lakes | Object storage (S3), Media servers |
| **How to query** | SQL queries | JSON path queries | AI models (NLP, CV), vector search |
| **% of enterprise data** | ~20% | ~10% | ~70% |
| **ML approach** | Tabular models (XGBoost, Random Forest) | Parse first, then tabular | Deep Learning (CNNs, Transformers) |
| **Feature engineering** | Easy (SQL aggregations) | Medium (parsing + flattening) | Hard (needs AI models to extract) |

### The Multimodal Reality

Modern AI increasingly works with **multiple data types at once** — this is called **multimodal AI**.

| Application | Data Types Combined | Example |
|---|---|---|
| E-commerce search | Text query + product images + purchase history (structured) + reviews (text) | You search "red running shoes" on Amazon → AI uses your text, product images, your past purchases, and review sentiments to rank results |
| Healthcare diagnosis | Patient records (structured) + X-ray images (unstructured) + doctor's notes (text) | AI combines lab results, X-ray, and doctor's notes to suggest a diagnosis |
| Self-driving cars | Camera images + LiDAR 3D data + GPS coordinates (structured) | Waymo fuses camera, LiDAR, and GPS to understand the road |

> **Trend:** Models like GPT-4o and Gemini are **multimodal foundation models** — they can process text, images, and audio in one model simultaneously.

---

## 5.2 Data Sources for AI Systems

AI systems consume data from many places. Knowing these sources helps you design good data pipelines.

### Internal Data Sources (From Within Your Company)

| Source | What Data It Has | AI Use Case Example |
|---|---|---|
| **Transactional databases** | Core business data — orders, payments, customer records | Flipkart's order database → product recommendation model |
| **Application logs** | User behaviour — what pages visited, what buttons clicked, errors | Hotstar's app logs → "Continue Watching" recommendations |
| **CRM systems** | Customer interactions — support tickets, sales pipeline | Freshworks' CRM data → predict which customers will churn |
| **Data warehouse / Data lake** | Historical, cleaned, aggregated data | Swiggy's data warehouse → weekly demand forecasting |
| **User-generated content** | Reviews, ratings, comments, uploads | Zomato's restaurant reviews → identify food quality issues |
| **IoT / Sensor data** | Real-time readings from devices | Tata Steel's factory sensors → predictive maintenance |

### External Data Sources (From Outside Your Company)

| Source | What Data It Has | AI Use Case Example |
|---|---|---|
| **Public datasets** | Free data for research | ImageNet (14M labelled images), Wikipedia, Government open data portals (data.gov.in) |
| **Third-party providers** | Commercial data — demographics, weather, financial | AccuWeather API for Swiggy's delivery time prediction (rain affects delivery) |
| **APIs** | Real-time data from external services | Google Maps API for distance/traffic data in ride-hailing apps |
| **Web scraping** | Data extracted from public websites (legal/ethical considerations apply) | Price comparison apps scraping product prices from multiple e-commerce sites |
| **Pre-trained model outputs** | Embeddings, classifications from existing AI models | Using GPT-4 to generate synthetic training data for a chatbot |

### Important Considerations When Collecting Data

| Consideration | Why It Matters | Real Example |
|---|---|---|
| **Sampling Bias** | If training data isn't representative, model performs poorly on underrepresented groups | A facial recognition system trained mostly on light-skinned faces **fails** on dark-skinned faces — happened with early commercial systems |
| **Label Quality** | Wrong labels = model learns wrong patterns | Medical dataset where 5% of "healthy" scans actually contain undetected tumours → model learns to miss tumours |
| **Privacy / Legal** | Personal data requires consent and compliance with laws (GDPR, India's DPDP Act) | Can't use customer purchase data for ML without explicit consent. Violation = heavy fines. |
| **Freshness** | Stale data may not reflect current reality | Fraud detection model trained on 2020 data misses new 2025 scam techniques |
| **Quality over Quantity** | 10,000 high-quality labelled examples often beat 1 million noisy ones | Carefully curated medical images with expert annotations >> mass-scraped internet images |

---

## 5.3 Data Quality

### Why Data Quality is the #1 Factor in AI Success

> **Simple truth:** A simple model on clean data will almost always beat a complex model on dirty data.

Google's own research found that **fixing data quality improved model performance more than switching to a fancier model**. Yet most teams spend their time tuning models instead of cleaning data.

### The Six Dimensions of Data Quality

| Dimension | What It Means (Simple) | Bad Quality Example | Impact on AI |
|---|---|---|---|
| **Accuracy** | Data values are correct | Customer age recorded as 150 (typo — should be 15) | Model learns wrong patterns from incorrect facts |
| **Completeness** | No missing values where they should exist | 30% of rows missing "income" field | Model either skips these rows (losing data) or guesses wrong values |
| **Consistency** | Same thing represented the same way everywhere | "India" in one table, "IN" in another, "Bharat" in a third | Joins fail. Model treats them as 3 different countries. |
| **Timeliness** | Data reflects current reality | Inventory data is 24 hours old in a real-time app | Swiggy recommends a restaurant that already closed for the night |
| **Validity** | Data follows expected format and rules | Phone number field contains "abc@email.com" | Feature parsing crashes or produces garbage |
| **Uniqueness** | No unintended duplicates | Same customer appears 3 times (Rahul, RAHUL, rahul sharma) | Model over-weights this customer. Training data is inflated. |

### Common Data Quality Problems in ML

| Problem | What Happens | How to Detect | How to Fix |
|---|---|---|---|
| **Missing values** | Rows with empty/NULL fields | `df.isnull().sum()` in Python | Fill with average (imputation), drop rows, or use models that handle NULLs (XGBoost) |
| **Outliers** | Extreme values far from normal | Box plots, z-score > 3 | Clip extremes, apply log transform, or investigate (fraud IS an outlier — it's real signal!) |
| **Duplicate records** | Same entity appears multiple times | Deduplicate by key fields, fuzzy matching for near-duplicates | Remove duplicates. But careful — same person buying same item twice is legitimate! |
| **Label noise** | Wrong labels in training data | Manual audit of random sample. Multiple annotators per sample. | Clean labels, use noise-tolerant training methods |
| **Data leakage** | Future information "leaks" into training data | Check that no test-time features exist in training | Strict train/test split. Ensure temporal ordering. |
| **Class imbalance** | One class has far more examples than others | Check `value_counts()` on target column | Oversampling (SMOTE), undersampling, or adjust class weights |
| **Schema drift** | Data format changes over time (new columns, changed types) | Schema validation tools | Automated schema enforcement + alerts |

### Live Example: Data Quality Issues in a Credit Scoring System

Imagine HDFC Bank is building a credit scoring model to decide loan approvals:

```
ISSUE 1: MISSING VALUES
─────────────────────
15% of applicants have no "employment_years" field.
Team imputes with median value (5 years).
BUT: Missing likely means UNEMPLOYED → should be 0, not 5.
Result: Model underestimates risk for unemployed applicants.
→ Bank approves risky loans → higher default rate.

ISSUE 2: DATA LEAKAGE
─────────────────────
Training data includes "credit_bureau_score" as a feature.
BUT: Credit bureau score is updated AFTER the loan decision.
This is future information leaking into training!
Model looks amazing in testing (99% accuracy) but fails
in production (doesn't have this feature at prediction time).

ISSUE 3: SAMPLING BIAS
─────────────────────
Training data is from 2018-2023. Bank only served urban
professionals until 2022, then expanded to rural areas.
Rural applicants are underrepresented in training data.
Result: Model performs poorly for rural customers.
→ Good rural applicants wrongly denied loans.

ISSUE 4: CLASS IMBALANCE
────────────────────────
Only 2% of loans default. 98% are paid back.
Model learns to predict "no default" for EVERYONE.
Result: 98% accuracy (sounds great!) but catches 0% of
actual defaults (completely useless!).
Fix: Use class weights, SMOTE oversampling, or
     change the decision threshold.
```

Each of these issues **silently degrades** the model — there's no crash or error message. This is why data quality is so important.

---

## 5.4 Data Governance

### What is Data Governance? (Simple)

**Data governance** is the set of **rules, processes, and responsibilities** that ensure data is managed properly — securely, consistently, and in compliance with laws.

> **Analogy:** Think of data governance like traffic rules. Without traffic rules, cars would crash everywhere. Similarly, without data governance, teams use data incorrectly, violate privacy laws, and build biased AI models. The rules keep everything safe and organised.

### Why Data Governance Matters for AI Specifically

In traditional analytics, poor governance = wrong reports (annoying but survivable).
In AI, poor governance = **serious consequences:**

| Problem | What Happens | Real Impact |
|---|---|---|
| Models trained on unauthorized data | Legal liability, regulatory fines | GDPR fine: up to **4% of global revenue**. India's DPDP Act: up to Rs 250 crore. |
| Models reproduce biases in data | Discriminatory predictions | Amazon had to scrap a hiring AI because it discriminated against women (trained on 10 years of male-dominated hiring data) |
| No audit trail | Can't explain model decisions to regulators | Banks required by RBI to explain why a loan was denied. If your AI can't explain, you're in trouble. |
| Inconsistent data across teams | Different teams get different "truth" | Marketing model says 1M active users, Finance model says 800K. Which is right? |

### Key Components of Data Governance

| Component | What It Means (Simple) | Why AI Needs It | Live Example |
|---|---|---|---|
| **Data Ownership** | Who is responsible for each data source? | Clear accountability. Know who to ask when questions arise. | "Customer data is owned by the CRM team. They approve access." |
| **Data Access Control** | Who can access what data, for what purpose? | Prevent unauthorized use of sensitive data in ML | PhonePe's payment data: only approved ML team can access, with audit logs |
| **Data Lineage** | Tracking where data came from and how it was transformed | If a model prediction is wrong, trace back to find the data that caused it | "This feature came from table X → joined with Y → aggregated by Z → fed to model" |
| **Data Cataloging** | Central inventory of all data assets | Data scientists can discover what data exists without asking 10 people | Tools: **Alation**, **DataHub**, **Apache Atlas** |
| **Privacy & Compliance** | Following laws — GDPR (EU), DPDP Act (India), HIPAA (US healthcare) | ML models can memorise and leak personal data | Anonymise PII before ML training. Implement "right to be forgotten." |
| **Data Retention** | How long data is kept and when deleted | Regulatory requirement + stale data hurts model quality | "Clickstream data: keep 2 years. Personal data: delete 1 year after account closure." |
| **Quality Standards** | Defined thresholds for data quality | Prevent bad data from entering training pipelines | "Completeness must be > 95%. Freshness must be < 1 hour for real-time features." |

### AI-Specific Governance Challenges

| Challenge | Why It's Hard | Solution |
|---|---|---|
| **What data trained the model?** | Need to know exactly which data, which version, when collected | **Data cards** — standardised documentation for each dataset |
| **Consent for ML use** | Users may consent to data collection for the service but not for ML training | Separate, explicit consent for ML use |
| **Right to be forgotten** | Under GDPR/DPDP, users can request data deletion. But model was already trained on it. | **Machine unlearning** — retrain without the deleted data |
| **Bias auditing** | Historical data encodes biases (gender, race, age). Models perpetuate these. | Regular bias audits. Fairness metrics. Bias-aware training. |
| **Reproducibility** | To audit a model, you need the exact data, code, and environment | **DVC** (data versioning), **Git** (code versioning), **Docker** (environment versioning) |

### The Data Governance Lifecycle

```
1. DISCOVER   → What data exists? Where? Who owns it?
                (Use a Data Catalog like Alation or DataHub)

2. CLASSIFY   → Is it PII? Sensitive? Regulated?
                (Tag data: "public", "internal", "confidential", "restricted")

3. PROTECT    → Encrypt sensitive data. Control who can access it.
                (Access control lists, encryption at rest and in transit)

4. COMPLY     → Follow GDPR, DPDP Act, HIPAA. Manage consent.
                (Consent management platform, privacy impact assessments)

5. MONITOR    → Data quality checks. Access audit logs. Lineage tracking.
                (Great Expectations for quality, audit logs for access)

6. GOVERN     → Policies, standards, ownership, stewardship.
                (People + Process + Technology)
```

### Live Example: Facebook/Cambridge Analytica (2018) — Governance Failure

This is the most famous data governance failure in history:

| What Happened | Governance Failure | Consequence |
|---|---|---|
| A quiz app on Facebook collected user data | No proper access control on third-party data access | 87 million users' data harvested without consent |
| Data shared with Cambridge Analytica | No audit trail for data sharing | Data used for targeted political ads in US and UK elections |
| Users never consented to political targeting | No consent verification for secondary use | Public outrage, congressional hearings |
| | | **$5 billion FTC fine** + massive reputation damage |
| | | Catalysed GDPR enforcement globally |

**Lesson:** Data governance isn't "nice to have" compliance paperwork — it has existential business implications.

### Live Example: India's DPDP Act (2023) — What It Means for AI

India's **Digital Personal Data Protection Act (DPDP, 2023)** directly impacts AI systems:

| DPDP Requirement | Impact on AI |
|---|---|
| Must get consent before collecting personal data | Can't scrape user data for ML training without consent |
| Users can request data deletion | Must be able to retrain models without a specific user's data |
| Must appoint a Data Protection Officer | Someone must be responsible for AI data governance |
| Penalties up to Rs 250 crore | Non-compliance is very expensive |
| Data can only be used for the stated purpose | If you collect data for "service delivery," you can't use it for "ML training" without separate consent |

---

## Key Terms Glossary (Session 5)

| Term | Simple Meaning |
|---|---|
| **Structured Data** | Data in fixed rows and columns (like a spreadsheet). Easy to query with SQL. |
| **Unstructured Data** | Data without fixed format — text, images, audio, video. Needs AI to process. |
| **Semi-Structured Data** | Partially organised data — JSON, XML, logs. Has some structure but flexible. |
| **Multimodal AI** | AI that processes multiple data types (text + images + audio) simultaneously |
| **Data Drift** | When real-world data changes compared to what the model was trained on |
| **Data Leakage** | When future or test information accidentally gets into training data |
| **Class Imbalance** | When one category has far more examples than others in training data |
| **Imputation** | Filling in missing values with estimated values (mean, median, etc.) |
| **PII (Personally Identifiable Information)** | Data that can identify a person — name, email, phone, Aadhaar number |
| **GDPR** | EU's data protection law. Fines up to 4% of global revenue. |
| **DPDP Act** | India's data protection law (2023). Fines up to Rs 250 crore. |
| **Data Lineage** | Tracking where data came from and how it was transformed |
| **Data Catalog** | A searchable inventory of all data assets in an organisation |
| **Machine Unlearning** | Removing the effect of specific data from a trained model |

---

*End of Session 5*
