# Session 5: Data and Feature Engineering

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 3 (Data Engineering and Feature Engineering)
>
> **Contact Session:** 5 (Module 2)

---

## Table of Contents

- [5.1 Structured and Unstructured Data](#51-structured-and-unstructured-data)
- [5.2 Data Sources for AI Systems](#52-data-sources-for-ai-systems)
- [5.3 Data Quality](#53-data-quality)
- [5.4 Data Governance](#54-data-governance)

---

## 5.1 Structured and Unstructured Data

### Why Data Matters for AI

In traditional software, the logic is in the **code** — you write rules, and the program executes them. In AI/ML systems, the logic is in the **data** — the model learns its behaviour from the data it's trained on. This means:

- **Better data almost always beats a better model.** A simple model trained on high-quality, relevant data will outperform a complex model trained on noisy, biased data.
- **Data is the hardest part.** Collecting, cleaning, labelling, and managing data consumes 80% of a typical ML project's time and effort.
- **Data quality issues silently degrade model performance.** Unlike code bugs (which crash), data bugs cause the model to produce subtly wrong predictions that are hard to detect.

### Types of Data in AI Systems

AI systems consume many types of data. The fundamental distinction is between structured and unstructured data, but semi-structured data is increasingly important too.

#### Structured Data

**Definition:** Data organised in a fixed, predefined schema — rows and columns with defined types and relationships. Every record has the same fields.

**Characteristics:**
- Fixed schema — every row has the same columns with the same data types.
- Easily queried using SQL.
- Stored in relational databases (PostgreSQL, MySQL) or columnar stores (Redshift, BigQuery).
- Typically represents **transactional or operational data** — customer records, orders, sensor readings, financial transactions.

**Examples in AI:**

| Data | Schema | AI use case |
|---|---|---|
| Customer transactions | customer_id, amount, timestamp, merchant, category | Fraud detection, spending prediction |
| User clickstream | user_id, page_url, action, timestamp, session_id | Recommendation engines, conversion prediction |
| IoT sensor readings | sensor_id, temperature, pressure, timestamp | Predictive maintenance, anomaly detection |
| Electronic health records | patient_id, diagnosis_code, medication, lab_results | Disease prediction, treatment recommendation |

**Strengths for ML:** Easy to process, well-understood tools (SQL, Pandas), straightforward feature engineering (aggregations, ratios, time-based features).

**Limitations for ML:** Doesn't capture unstructured information (images, text, audio). Many real-world signals are not structured.

#### Unstructured Data

**Definition:** Data without a predefined schema or organisation. Each data item may have a different format, length, and content. Requires specialised processing to extract useful information.

**Characteristics:**
- No fixed schema — each item is unique in structure.
- Cannot be queried with SQL directly.
- Stored in object storage (S3, GCS), document stores, or media servers.
- Requires specialised AI techniques to process (NLP for text, CNNs for images, speech recognition for audio).
- Estimated to be **80-90% of all data generated globally**.

**Examples in AI:**

| Data type | Examples | AI technique | AI use case |
|---|---|---|---|
| **Text** | Emails, reviews, documents, social media posts, chat logs | NLP, LLMs, transformers | Sentiment analysis, summarisation, Q&A, chatbots |
| **Images** | Photos, medical scans, satellite imagery, product images | CNNs, vision transformers | Object detection, medical diagnosis, quality inspection |
| **Audio** | Speech recordings, music, call centre recordings | Speech recognition, audio classification | Transcription, voice assistants, emotion detection |
| **Video** | Surveillance footage, sports broadcasts, user-generated content | Video understanding, action recognition | Security, content moderation, sports analytics |
| **Documents** | PDFs, scanned forms, invoices, contracts | OCR + NLP | Document extraction, contract analysis |

**Strengths for ML:** Rich signal — images, text, and audio contain information that structured data can't capture (tone, visual context, spatial relationships).

**Limitations for ML:** Harder to process, requires more compute (GPUs), larger models, more data for training, harder to label.

#### Semi-Structured Data

**Definition:** Data that has some organisational structure (tags, keys, nesting) but doesn't conform to a rigid tabular schema. Each record can have different fields.

**Examples:** JSON, XML, YAML, HTML, log files, email (headers are structured, body is unstructured).

**Relevance for AI:** API responses (JSON), event logs, user behaviour data, IoT device messages. Often the bridge between structured and unstructured.

**Example — Semi-structured JSON event:**
```json
{
  "user_id": "u12345",
  "event": "product_view",
  "timestamp": "2025-09-07T10:30:00Z",
  "product": {
    "id": "p789",
    "name": "Wireless Headphones",
    "category": "Electronics",
    "price": 2999.00
  },
  "context": {
    "device": "mobile",
    "referrer": "search",
    "search_query": "best wireless headphones under 3000"
  }
}
```

This event has structured fields (user_id, timestamp, price) but also nested objects and variable fields (search_query only exists for search-referred views). AI systems must flatten, extract, and transform this into features.

### Comparison Table

| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Schema** | Fixed, predefined | Flexible, self-describing | None |
| **Storage** | RDBMS, columnar stores | Document DBs, data lakes | Object storage, media servers |
| **Query** | SQL | JSON queries, path expressions | Full-text search, vector search, AI models |
| **Feature engineering** | Straightforward (SQL, aggregations) | Requires parsing and flattening | Requires AI models to extract features |
| **% of enterprise data** | ~20% | ~10% | ~70% |
| **ML examples** | Tabular models (XGBoost, logistic regression) | Event-based models, graph models | Deep learning (CNNs, transformers) |

### The Multimodal Reality

Modern AI systems increasingly work with **multiple data types simultaneously** (multimodal AI):

- **E-commerce search:** Text query + product images + user purchase history (structured) + reviews (text).
- **Healthcare:** Patient records (structured) + medical images (unstructured) + doctor's notes (text).
- **Autonomous vehicles:** Camera images (unstructured) + LiDAR point clouds (semi-structured) + GPS/speed (structured).

The trend in AI is toward **multimodal foundation models** (like GPT-4o, Gemini) that can process text, images, and audio in a single model.

---

## 5.2 Data Sources for AI Systems

AI systems consume data from many sources. Understanding these sources — their characteristics, strengths, and limitations — is essential for designing robust data pipelines.

### Internal Data Sources

Data generated within the organisation's own systems.

| Source | What it provides | Example |
|---|---|---|
| **Transactional databases** | Core business data — orders, payments, customer records, inventory. | PostgreSQL, MySQL, Oracle databases powering the application. |
| **Application logs** | User behaviour, errors, performance metrics. | Web server logs, API access logs, application event logs. |
| **CRM systems** | Customer interactions, support tickets, sales pipeline. | Salesforce, HubSpot data. |
| **Data warehouse / Data lake** | Historical, aggregated, and transformed data for analytics. | Snowflake, BigQuery, Databricks Lakehouse. |
| **User-generated content** | Reviews, ratings, comments, uploads. | Product reviews on an e-commerce site, user-uploaded photos. |
| **IoT / Sensor data** | Real-time readings from connected devices. | Temperature sensors in a factory, GPS data from delivery trucks. |

### External Data Sources

Data acquired from outside the organisation.

| Source | What it provides | Example |
|---|---|---|
| **Public datasets** | Openly available data for research and development. | ImageNet (images), Common Crawl (web text), Wikipedia, government open data portals. |
| **Third-party data providers** | Commercial data — demographics, financial, weather, geospatial. | Experian (credit data), AccuWeather (weather API), Foursquare (location data). |
| **APIs** | Real-time data from external services. | Twitter/X API (social media), Google Maps API (geolocation), stock market APIs. |
| **Web scraping** | Data extracted from public websites. | Product prices from competitor sites, job postings, news articles. (Legal and ethical considerations apply.) |
| **Pre-trained model outputs** | Embeddings, classifications, or annotations from existing AI models. | Using GPT-4 to generate synthetic training data, using CLIP embeddings for image features. |

### Data Collection Considerations

| Consideration | Why it matters | Example |
|---|---|---|
| **Sampling bias** | If training data isn't representative of the real population, the model will perform poorly on underrepresented groups. | A facial recognition model trained mostly on light-skinned faces performs worse on dark-skinned faces. |
| **Label quality** | If labels (ground truth) are wrong, the model learns wrong patterns. | A medical imaging dataset where 5% of "healthy" scans actually contain undetected tumours. |
| **Privacy** | Personal data (PII) requires consent, anonymisation, and compliance with regulations (GDPR, HIPAA). | Using customer purchase history requires explicit consent and careful handling. |
| **Freshness** | Stale data may not reflect current reality. | A fraud detection model trained on 2020 data may miss new fraud patterns in 2025. |
| **Volume vs Quality** | More data isn't always better — 10,000 high-quality labelled examples often beat 1 million noisy examples. | Carefully curated medical images with expert annotations vs mass-scraped internet images. |

---

## 5.3 Data Quality

Data quality is arguably the most impactful factor in ML system performance. A study by Google found that fixing data quality issues improved model performance more than switching to a more complex model architecture.

### The Six Dimensions of Data Quality

| Dimension | Definition | Example of poor quality | Impact on ML |
|---|---|---|---|
| **Accuracy** | Data values correctly represent real-world entities and events. | A customer's age recorded as 150 (data entry error). | Model learns wrong patterns. Predictions based on incorrect facts. |
| **Completeness** | No missing values where values should exist. | 30% of rows missing the "income" column. | Model either ignores these rows (losing training data) or imputes values (introducing noise). |
| **Consistency** | Same fact represented the same way everywhere. | "United States" in one table, "US" in another, "USA" in a third. | Features disagree, model confused. Join failures. |
| **Timeliness (Freshness)** | Data reflects the current state of the world. | Inventory data is 24 hours old in a real-time recommendation system. | Model recommends out-of-stock products. |
| **Validity** | Data conforms to defined formats, types, and constraints. | A "phone_number" field containing email addresses. | Feature parsing fails or produces garbage. |
| **Uniqueness** | No unintended duplicate records. | Same customer appears 3 times with slightly different names. | Model over-weights this customer's behaviour. Inflated training data. |

### Common Data Quality Issues in ML

| Issue | What happens | Detection method | Fix |
|---|---|---|---|
| **Missing values** | Rows with NULL or empty fields. | `df.isnull().sum()` in Pandas. | Impute (mean/median/mode), drop rows, or use models that handle nulls (XGBoost). |
| **Outliers** | Extreme values far from the distribution. | Box plots, z-score > 3, IQR method. | Clip, transform (log), or investigate (might be real signal — fraud IS an outlier). |
| **Duplicate records** | Same entity appears multiple times. | Deduplication by key fields, fuzzy matching. | Deduplicate. But be careful — some "duplicates" are legitimate (same customer buying same item twice). |
| **Label noise** | Incorrect labels in training data. | Manual audit of random sample. Cross-labelling (multiple annotators per sample). | Clean labels, use noise-robust training methods. |
| **Data leakage** | Information from the test set or future "leaks" into training. | Check temporal ordering. Ensure no test-time features in training. | Strict train/test split. Use proper cross-validation. |
| **Class imbalance** | One class has far more examples than others. | `value_counts()` on target column. | Oversampling (SMOTE), undersampling, class weights, threshold tuning. |
| **Schema drift** | Data format changes over time (new columns, changed types). | Schema validation in pipeline (Great Expectations). | Schema enforcement, alerts on drift. |

### Data Quality in Practice — A Real Example

**Scenario:** A bank builds a credit scoring model.

```
Training data issues discovered:
1. MISSING VALUES: 15% of applicants have no "employment_years" 
   → Imputed with median (5 years). But missing likely means 
   unemployed → should be 0, not 5. Model now underestimates 
   risk for unemployed applicants.

2. LABEL LEAKAGE: Training data includes "credit_bureau_score" 
   as a feature. But credit_bureau_score is updated AFTER 
   the loan decision is made. This leaks future info into 
   training → model looks amazing in testing but fails in 
   production (doesn't have this feature at prediction time).

3. SAMPLING BIAS: Training data is from 2018-2023. But the 
   bank only served urban professionals until 2022, then 
   expanded to rural areas. Rural applicants are 
   underrepresented → model performs poorly for rural customers.

4. CLASS IMBALANCE: Only 2% of loans default. Model learns 
   to predict "no default" for everyone → 98% accuracy but 
   useless for catching actual defaults.
```

Each of these issues would silently degrade the model in production without obvious errors.

---

## 5.4 Data Governance

**Data governance** is the framework of policies, processes, standards, and metrics that ensures data is managed securely, consistently, and in compliance with regulations throughout its lifecycle.

### Why Data Governance Matters for AI

In traditional analytics, poor governance means wrong reports. In AI systems, poor governance means:

1. **Models trained on unauthorized data** → Legal liability, regulatory fines (GDPR: up to 4% of global revenue).
2. **Models reproducing biases in data** → Discriminatory predictions (denying loans based on race proxies).
3. **No audit trail** → Can't explain or defend model decisions to regulators.
4. **Inconsistent data across teams** → Different teams train on different versions of "truth," producing contradictory models.

### Key Components of Data Governance

| Component | What it covers | Why it matters for AI | Example |
|---|---|---|---|
| **Data ownership** | Who is responsible for each data asset. | Clear accountability for data quality. Know who to ask when questions arise. | "Customer data is owned by the CRM team. They approve access and ensure quality." |
| **Data access control** | Who can access what data, for what purpose. | Prevent unauthorized use of sensitive data for model training. | PII data requires approval from the privacy team before use in ML. |
| **Data lineage** | Tracking where data came from, how it was transformed, where it's used. | If a model prediction is wrong, trace back to the data that caused it. Regulatory requirement in finance/healthcare. | "This feature was computed from table X, joined with table Y, aggregated by Z." |
| **Data cataloging** | Central inventory of all data assets with metadata (descriptions, schemas, owners, quality scores). | Data scientists can discover what data exists without asking 10 people. | Platforms like Alation, DataHub, Amundsen. |
| **Data privacy & compliance** | Ensure data handling complies with regulations (GDPR, HIPAA, CCPA, India's DPDP Act). | ML models can memorise and leak personal data. Regulations require consent, right to deletion. | Anonymise PII before using in ML training. Implement "right to be forgotten" in training data. |
| **Data retention** | How long data is kept and when it's deleted. | Regulatory requirements. Storage costs. Stale data degrades model quality. | "User clickstream data retained for 2 years, then archived. Personal data deleted after 1 year of account closure." |
| **Data quality standards** | Defined metrics and thresholds for data quality dimensions. | Prevent bad data from entering training pipelines. | "Completeness must be > 95%. Freshness must be < 1 hour for real-time features." |

### Data Governance for ML — Specific Challenges

| Challenge | Explanation | Mitigation |
|---|---|---|
| **Training data documentation** | What data was used to train a model? What version? When was it collected? | **Data cards** — standardised documentation for each dataset (similar to model cards for models). |
| **Consent for ML use** | Users may consent to data collection for the service but not for ML training. | Explicit consent for ML use. Separate consent for training vs serving. |
| **Right to be forgotten** | Under GDPR, users can request deletion of their data. But the model was already trained on it. | **Machine unlearning** — retrain the model without the deleted data, or use approximate unlearning techniques. |
| **Bias auditing** | Data may encode historical biases (gender, race, age). Models trained on this data will perpetuate biases. | Regular bias audits. Fairness metrics (equal opportunity, demographic parity). Bias-aware training. |
| **Model reproducibility** | To audit a model's decision, you need the exact data, code, and environment that produced it. | Data versioning (DVC, LakeFS), code versioning (Git), environment versioning (Docker). |

### Data Governance Frameworks in Practice

```
Data Governance Lifecycle for AI:

1. DISCOVER    → What data exists? Where? Who owns it? (Data Catalog)
2. CLASSIFY    → Is it PII? Sensitive? Regulated? (Data Classification)
3. PROTECT     → Encrypt, anonymise, control access. (Security)
4. COMPLY      → GDPR, HIPAA, DPDP Act. Consent management. (Compliance)
5. MONITOR     → Data quality checks, access audit logs, lineage tracking. (Observability)
6. GOVERN      → Policies, standards, ownership, stewardship. (People + Process)
```

### Real-World Example: Data Governance Failure

**Facebook/Cambridge Analytica (2018):**
- User data collected via a quiz app was shared with Cambridge Analytica without user consent.
- The data was used to build psychographic profiles and targeted political ads.
- **Governance failure:** No proper access control on third-party data access. No audit trail. No consent verification for secondary use.
- **Consequence:** $5 billion FTC fine. Massive reputational damage. Catalysed GDPR enforcement globally.

This case demonstrates why data governance isn't just compliance theatre — it has existential business implications.

---

*End of Session 5*
