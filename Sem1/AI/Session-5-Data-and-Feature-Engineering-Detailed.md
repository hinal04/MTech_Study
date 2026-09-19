# Session 5: Structured and Unstructured Data, Data Sources, Quality & Governance

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 3 (Data Engineering and Feature Engineering)
>
> **Contact Session:** 5 (Module 2: Data and Feature Engineering)

---

## Table of Contents

- [5.1 Structured and Unstructured Data](#51-structured-and-unstructured-data)
  - [Hidden Structure Inside Unstructured Data](#hidden-structure-inside-unstructured-data)
  - [Temporal Data — Time Changes Everything](#temporal-data--time-changes-everything)
  - [Event Data vs State Data](#event-data-vs-state-data)
- [5.2 Data Sources for AI Systems](#52-data-sources-for-ai-systems)
  - [Human-Generated Data and Label Quality](#human-generated-data-and-label-quality)
  - [Synthetic Data](#synthetic-data)
  - [Labels Are Business Judgments](#labels-are-business-judgments)
- [5.3 Data Quality](#53-data-quality)
  - [Data Quality by Population Slice](#data-quality-by-population-slice)
  - [Missingness Is Data](#missingness-is-data)
- [5.4 Data Governance](#54-data-governance)
  - [Centralized vs Federated Governance](#centralized-vs-federated-governance)
  - [Data Contracts](#data-contracts)
  - [AI Data Contracts vs Traditional Data Contracts](#ai-data-contracts-vs-traditional-data-contracts)
  - [GenAI Changes Data Governance](#genai-changes-data-governance)
  - [Data Incident Management and Blast Radius](#data-incident-management-and-blast-radius)
- [5.5 Enterprise Data Management for AI](#55-enterprise-data-management-for-ai)
  - [Enterprise Data Management vs ML-Ready Data Management](#enterprise-data-management-vs-ml-ready-data-management)
  - [Enterprise Data Maturity Model](#enterprise-data-maturity-model)
  - [Traditional vs Modern Enterprise Data Architecture](#traditional-vs-modern-enterprise-data-architecture)
  - [Data Products, Contracts, and SLAs](#data-products-contracts-and-slas)
- [5.6 AI Data Readiness and Transformation Roadmap](#56-ai-data-readiness-and-transformation-roadmap)

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

### Hidden Structure Inside Unstructured Data

Here's the twist — even "unstructured" data actually has **hidden structure** buried inside it. The data isn't as chaotic as it looks.

> **Analogy:** Think of a messy room. It looks unstructured, but there ARE patterns — clothes are near the wardrobe, books are near the desk, food is near the kitchen. You just need to look carefully. Similarly, unstructured data has patterns that AI can extract.

| Unstructured Data Type | Hidden Structure Inside It | How AI Extracts It |
|---|---|---|
| **Emails** | Sender, receiver, timestamp, subject, body, attachments | NLP parses header fields + extracts intent from body |
| **Images** | EXIF metadata (camera model, GPS location, date taken), pixel patterns | CV models extract objects, faces, text; EXIF gives context |
| **Audio files** | Sample rate, duration, channels, frequency patterns | Speech recognition extracts words; audio analysis extracts tone/emotion |
| **PDF documents** | Page structure, headings, tables, font sizes | OCR + layout analysis extracts structured fields from visual layout |
| **Social media posts** | Hashtags, mentions, timestamps, engagement metrics | NLP extracts entities, sentiment; metadata gives social context |

**Why this matters for AI:**
- Modern AI models are built to **automatically extract** this hidden structure
- NLP extracts entities (person names, locations, amounts) from raw text
- Computer Vision extracts objects (cars, people, defects) from raw images
- You don't always need to manually label unstructured data — AI can discover the structure

**Indian Example:** **Naukri.com** receives millions of resumes in PDF, DOCX, and image formats (all unstructured). Their AI automatically extracts structured fields — name, skills, experience years, current company, expected salary — from these messy documents. The hidden structure is there; AI just needs to find it.

---

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

### Temporal Data — Time Changes Everything

Much of the data AI systems work with **changes over time**. This creates special challenges that most beginners miss.

> **Analogy:** Imagine writing an exam and being allowed to use tomorrow's answer key. That's cheating! Similarly, when building ML training data, you must NOT use information that wasn't available at that historical point in time. This is called **point-in-time correctness**.

**The Core Problem:** When you're building training data TODAY using historical records, it's easy to accidentally include information that wasn't known at the time of the event.

**Example — Predicting Loan Default at HDFC Bank:**

```
WRONG APPROACH:
─────────────
Training row for a loan from January 2023:
- income: 50,000/month
- loan_amount: 5,00,000
- months_past_due: 3        ← THIS IS WRONG!
- credit_score_latest: 580  ← THIS IS WRONG!

"months_past_due" and latest credit score are known AFTER
the loan was given. At the time of loan approval (Jan 2023),
you didn't know the borrower would miss 3 EMIs!

CORRECT APPROACH:
─────────────────
Use ONLY features available at the time of loan decision:
- income: 50,000/month       ← known at application time
- loan_amount: 5,00,000      ← known at application time
- credit_score_at_application: 720  ← snapshot from Jan 2023
- existing_loan_count: 2     ← known at application time
```

**Key Rules for Temporal Data:**

| Rule | What It Means | Violation Example |
|---|---|---|
| **Point-in-time correctness** | Only use features available at the historical decision time | Using current address for a 2-year-old transaction |
| **No future leakage** | Training data must not contain information from after the event | Using "account_closed" flag to predict churn (closing IS churning!) |
| **Temporal train/test split** | Train on older data, test on newer data (not random split) | Randomly mixing 2024 and 2025 data in both train and test sets |
| **Feature staleness** | Features must be fresh enough to be useful at serving time | Using a "last_login" feature that's only updated weekly in a real-time system |

### Event Data vs State Data

AI systems work with two fundamentally different kinds of data, and mixing them up causes real problems.

| Aspect | Event Data | State Data |
|---|---|---|
| **What it records** | **What HAPPENED** | **What IS (right now)** |
| **Nature** | Immutable (can't change the past) | Mutable (gets overwritten) |
| **Storage pattern** | Append-only (keep adding new events) | Update-in-place (overwrite old value) |
| **Examples** | User clicked "Buy", order placed, payment of Rs 500 made, driver assigned | User's wallet balance is Rs 2,000, inventory count is 42, driver is "available" |
| **Time** | Always has a timestamp | Represents current snapshot |

> **Analogy:** Event data is like a diary — you record what happened each day and never erase it. State data is like your bank passbook's last page — it shows the current balance, overwriting the previous one.

**Why ML Needs Both:**

```
FOR TRAINING (Historical — "What happened?"):
──────────────────────────────────────────────
Use EVENT DATA — sequence of past actions.
Example: Train a fraud model on historical transactions:
  Event 1: Rs 200 at BigBazaar, Mumbai, 2pm
  Event 2: Rs 50,000 at electronics store, Delhi, 2:05pm  ← suspicious!
  Event 3: Rs 80,000 online purchase, 2:10pm               ← suspicious!

FOR SERVING (Real-time — "What's happening now?"):
──────────────────────────────────────────────────
Use STATE DATA — current snapshot of the world.
Example: When a new transaction comes in, check:
  - Current account balance: Rs 1,20,000
  - Last known location: Mumbai
  - Account status: Active
  - Number of transactions today: 5
```

**Indian Example:** **Paytm** uses event data (every UPI transaction, login, recharge) to train its fraud detection model. But when a new transaction comes in, it checks state data (current balance, account age, device info) to make the real-time fraud decision. Both are needed — events for learning patterns, state for current context.

---

## 5.2 Data Sources for AI Systems

AI systems consume data from many places. Knowing these sources helps you design good data pipelines.

**Enterprise data sources** include: transactional databases, CRM systems, ERP systems, data warehouses, application logs, IoT devices, and third-party data providers. Each source has different formats, freshness, and access patterns — understanding these is the first step to building any AI system.

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

### Human-Generated Data and Label Quality

In ML, humans create a lot of the training data — especially **labels** (the "correct answers" the model learns from). But human-generated labels are messier than you'd think.

> **Analogy:** Ask 10 people to rate a movie from 1-5 stars. You'll get different answers — not because they're wrong, but because taste is subjective. Similarly, when humans label data for ML, they disagree on borderline cases.

**The Problem: Inter-Annotator Disagreement**

| Labelling Task | Why Annotators Disagree | Disagreement Rate |
|---|---|---|
| "Is this product review positive or negative?" | "The food was okay but delivery was late" — is this positive or negative? | 15-25% disagreement on borderline reviews |
| "Is this email spam?" | Promotional emails — spam or just marketing? | 10-20% disagreement |
| "Does this X-ray show a fracture?" | Hairline fractures are hard to spot even for doctors | 5-15% disagreement among radiologists |
| "Is this social media post hate speech?" | Cultural context matters — same words mean different things | 20-40% disagreement |

**How to Measure Agreement: Cohen's Kappa**

Cohen's kappa (κ) measures how much two annotators agree **beyond random chance**:

| Kappa Value | What It Means |
|---|---|
| κ < 0.20 | Poor agreement — labels are almost random |
| 0.21 - 0.40 | Fair — significant disagreement |
| 0.41 - 0.60 | Moderate — acceptable for many tasks |
| 0.61 - 0.80 | Substantial — good quality |
| 0.81 - 1.00 | Almost perfect — very reliable labels |

**Best Practices for Human Labelling:**

1. **Multiple annotators per sample** — get 3-5 people to label each item, take majority vote
2. **Clear labelling guidelines** — write detailed instructions with examples of edge cases
3. **Regular calibration sessions** — have annotators discuss disagreements and align
4. **Track annotator quality** — identify annotators who consistently disagree with others

**Indian Example:** **Flipkart** uses human annotators to label product images (is this a "kurta" or a "kurti"? Is the color "maroon" or "wine red"?). They found that without clear guidelines, annotators disagreed 30% of the time on fashion categories. After creating detailed visual guides with examples, disagreement dropped to 8%.

### Synthetic Data

**Synthetic data** is artificially generated data — created by AI models, simulations, or mathematical rules rather than collected from the real world.

> **Analogy:** When Bollywood shoots a war scene, they don't start a real war. They create **synthetic** battle footage using VFX. Similarly, when you can't get enough real data, you create synthetic data that looks and behaves like real data.

**When to Use Synthetic Data:**

| Situation | Why Real Data Is Hard | How Synthetic Data Helps |
|---|---|---|
| **Privacy restrictions** | Can't share patient medical records | Generate fake-but-realistic patient records for model training |
| **Rare events** | Very few fraud cases to train on | Generate synthetic fraud patterns to balance the dataset |
| **Expensive to collect** | Labelling 100K images costs Rs 50 lakh | Use AI to generate labelled images automatically |
| **Dangerous scenarios** | Can't crash real self-driving cars to collect data | Simulate thousands of crash scenarios in a virtual environment |
| **New product categories** | No historical data exists yet | Generate synthetic user behavior for a new feature |

**How Synthetic Data Is Generated:**

| Method | How It Works | Example |
|---|---|---|
| **GANs (Generative Adversarial Networks)** | Two neural networks — one generates fake data, the other tries to catch fakes. They improve together. | Generate realistic face images for testing facial recognition |
| **LLMs (Large Language Models)** | Ask GPT-4/Gemini to generate training examples | "Generate 1000 customer support conversations about UPI failures" |
| **Simulation** | Mathematical/physics models create data | Simulate sensor readings from a factory machine under different conditions |
| **Rule-based generation** | Use known rules to create data | Generate valid Aadhaar-format numbers (without real people) for testing |

**Caution — Risks of Synthetic Data:**

- **Bias amplification:** If the generator model is biased, synthetic data amplifies those biases
- **Distribution mismatch:** Synthetic data may not capture the full complexity of real-world data
- **Overfitting to generator:** Model trained on synthetic data may learn the generator's quirks, not real patterns
- **Always validate:** Test synthetic-data-trained models on REAL data before deployment

**Indian Example:** **Aadhaar verification systems** can't use real Aadhaar data for testing (privacy laws). So teams generate **synthetic Aadhaar-like documents** — fake names, fake numbers, but realistic layouts — to train OCR models that read Aadhaar cards. The model learns the document structure without ever seeing real personal data.

### Labels Are Business Judgments

Here's something most textbooks don't tell you: **a label in ML is not objective truth — it's a business decision.** The same raw data can be labelled differently depending on how the business defines the problem.

Labels are business judgments — the definition of "churned customer" (30 days? 60 days? 90 days inactive?) is a business decision that directly affects model behavior. Change the definition and you change the model.

> **Analogy:** Is a student who scored 39/100 a "fail"? Depends on the passing mark! If it's 35, they passed. If it's 40, they failed. The score is the same — the LABEL changes based on the threshold YOU define.

**Example — "Has This Customer Churned?" at Hotstar/JioCinema:**

```
Same customer. Same data. Three different business definitions:

DEFINITION 1: Churned = No login for 30 days
→ Customer last logged in 35 days ago
→ Label: CHURNED ✓

DEFINITION 2: Churned = No login for 60 days
→ Customer last logged in 35 days ago
→ Label: NOT CHURNED ✗

DEFINITION 3: Churned = Cancelled subscription
→ Customer still has active subscription (just not watching)
→ Label: NOT CHURNED ✗

SAME customer, SAME data → THREE different labels!
Each definition gives you a DIFFERENT model with DIFFERENT predictions.
```

**Why This Matters:**

| Business Decision | Impact on Model |
|---|---|
| Stricter churn definition (30 days) | Model catches more potential churners but also has more false alarms |
| Lenient churn definition (90 days) | Model only catches severe churners, misses early warning signs |
| Revenue-based definition (spending dropped 50%) | Model focuses on high-value customers, ignores casual users |

**Key Takeaway:** Before building ANY ML model, align with business stakeholders on label definitions. Different definitions → different models → different business outcomes. There is no "correct" label without a business context.

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

### Data Quality by Population Slice

Here's a trap that catches even experienced teams: **overall data quality can look fine, while quality for specific groups is terrible.**

> **Analogy:** A school's average exam score is 80%. Sounds great! But Class A scored 95% and Class B scored 55%. The average hides the problem. Similarly, an ML model with "95% accuracy" might be 99% accurate for urban users and only 70% for rural users.

**The Problem: Aggregated Metrics Hide Disparities**

```
OVERALL MODEL PERFORMANCE:
──────────────────────────
Accuracy: 94%   ← Looks great!
Precision: 91%  ← Looks great!

BUT WHEN YOU SLICE BY POPULATION:
─────────────────────────────────
Urban customers:      Accuracy = 97%  ✓ Excellent
Rural customers:      Accuracy = 72%  ✗ Poor
Hindi-speaking users: Accuracy = 96%  ✓ Excellent
Tamil-speaking users: Accuracy = 68%  ✗ Terrible
Age 25-40:            Accuracy = 95%  ✓ Excellent
Age 60+:              Accuracy = 74%  ✗ Poor

WHY? These underrepresented groups have:
- Fewer training examples (data quantity)
- More missing features (data completeness)
- Different data patterns (data relevance)
```

**What to Check — Quality by Slice:**

| Quality Metric | Check by Slice | Example |
|---|---|---|
| **Completeness** | % missing values per demographic group | "income" field is missing for 40% of rural applicants but only 5% of urban |
| **Accuracy** | Error rate per geographic region | Address validation fails more often for tier-3 city addresses |
| **Freshness** | Data update lag per source | Rural branch data updates weekly, urban branch updates hourly |
| **Label quality** | Annotation accuracy per category | Annotators are less accurate labelling regional language content |

**Indian Example:** A leading **health-tech startup** built a symptom-checker AI. Overall accuracy was 90%. But when they sliced by language, they found: English queries → 94% accuracy, Hindi queries → 82%, Kannada queries → 61%. The training data had 10x more English examples than regional language examples. They had to specifically collect and label more regional language data.

**Rule:** Always check data quality AND model performance sliced by relevant demographic, geographic, and behavioral groups. National averages mean nothing if specific communities are poorly served.

### Missingness Is Data

Most beginners treat missing values as "garbage to be filled or deleted." But here's the insight: **the fact that data is missing is itself a signal.**

> **Analogy:** On a job application, if someone leaves the "Previous Employer" field blank, that IS information — they might be a fresher or self-employed. The blank itself tells you something.

**Examples Where Missing = Meaningful:**

| Feature | If It's Missing, It Likely Means | Signal Value |
|---|---|---|
| `employer_name` | Self-employed or unemployed | Higher loan risk |
| `annual_income` (in a survey) | Income is very high or very low (people at extremes skip this) | Non-random missingness! |
| `emergency_contact` | Person lives alone or is estranged from family | Social isolation indicator |
| `email_address` (in a rural application) | Applicant may not use email regularly | Digital literacy indicator |
| `delivery_instructions` (Swiggy/Zomato) | Standard delivery location, easy to find | Low delivery complexity |

**Best Practice — Create "Is Missing" Features:**

```python
# Instead of just imputing, also create indicator features!

# BAD: Just fill missing income with median
df['income'] = df['income'].fillna(df['income'].median())

# GOOD: Create a missing indicator FIRST, then impute
df['income_is_missing'] = df['income'].isnull().astype(int)
df['income'] = df['income'].fillna(df['income'].median())

# Now your model has TWO features:
# 1. income (filled with median for missing cases)
# 2. income_is_missing (1 = was missing, 0 = was present)
# The model can learn that "income was missing" itself is a signal!
```

**Three Types of Missingness:**

| Type | What It Means | Example | How to Handle |
|---|---|---|---|
| **MCAR** (Missing Completely at Random) | Missingness has no pattern — purely random | A server crash randomly lost 5% of records | Safe to drop or impute with mean/median |
| **MAR** (Missing at Random) | Missingness depends on OTHER observed features | Young people skip "home_loan" questions (no loan yet) | Impute using other features (age predicts missingness) |
| **MNAR** (Missing Not at Random) | Missingness depends on the MISSING VALUE itself | Rich people don't report income (privacy). Missing because income is HIGH. | Hardest to handle. Create indicator features. Use domain knowledge. |

**Indian Example:** In **CIBIL credit scoring**, when "number_of_credit_cards" is missing, it often means the person has NO credit cards (not that the data was lost). Treating this as "missing" and imputing with the average (say, 2 cards) would be wrong. The correct approach: create an `has_credit_card_info = 0` feature and set the count to 0.

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

> **Important principle:** Governance should be embedded into data pipelines from the start, not bolted on as an afterthought. Retroactive governance is 10x more expensive than built-in governance. Build quality checks, access controls, and lineage tracking into your pipelines from day one.

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

### Centralized vs Federated Governance

As organisations grow, a key question arises: **who controls data governance?** There are three models:

| Model | How It Works | Pros | Cons |
|---|---|---|---|
| **Centralized** | One central team (Chief Data Officer's team) owns ALL governance — policies, quality, access | Consistent standards. Clear accountability. Easy compliance. | Bottleneck — every team waits for central approval. Doesn't scale. |
| **Federated** | Each domain team (payments, logistics, marketing) governs their own data independently | Fast — no bottleneck. Domain experts make decisions. | Inconsistent standards. "Payments team" and "Finance team" define "revenue" differently. |
| **Data Mesh (Hybrid)** | Federated ownership + centralized standards. Each team owns their data as a "product" but follows company-wide rules. | Best of both — speed + consistency | Complex to implement. Needs cultural change. |

> **Analogy:** Think of India's governance — the Constitution (centralized standards) sets the rules, but states (federated) manage their own affairs within those rules. If every decision went to Delhi, nothing would get done. If every state made its own rules, chaos.

**Data Mesh Approach (Increasingly Popular):**

```
CENTRALIZED STANDARDS (company-wide):
─────────────────────────────────────
- Data quality thresholds (completeness > 95%)
- Privacy policies (DPDP compliance)
- Naming conventions (snake_case, standard date formats)
- Security standards (encryption, access controls)

FEDERATED OWNERSHIP (per domain team):
──────────────────────────────────────
Payments Team:
  → Owns: transaction data, refund data, payment methods
  → Publishes: "daily_transactions" data product
  → Responsible for: quality, freshness, documentation

Logistics Team:
  → Owns: delivery data, driver data, route data
  → Publishes: "delivery_performance" data product
  → Responsible for: quality, freshness, documentation
```

**Indian Example:** **Razorpay** follows a federated model — the payments team owns payment data, the banking team owns account data, the compliance team owns KYC data. But company-wide standards ensure everyone uses the same encryption, the same PII handling rules, and the same data catalog (so anyone can discover what data exists).

### Data Contracts

A **data contract** is a formal agreement between a data **producer** (team that creates/owns the data) and a data **consumer** (team that uses the data).

> **Analogy:** When you order on Swiggy, there's an implicit contract — the restaurant promises hot food within 30 minutes. If they send cold food after 2 hours, the contract is broken. A data contract is the same — the data producer promises specific quality, and the consumer depends on it.

**What's in a Data Contract:**

| Component | What It Specifies | Example |
|---|---|---|
| **Schema** | Exact columns, data types, allowed values | `customer_id: string (UUID format), amount: float (>0)` |
| **Freshness SLA** | How often data is updated | "Refreshed every 4 hours, max delay 6 hours" |
| **Quality thresholds** | Minimum acceptable quality | "< 1% null values in customer_id, < 0.1% duplicates" |
| **Ownership** | Who is responsible for this data | "Owned by Payments Team, contact: payments-data@company.com" |
| **Access policies** | Who can read/write this data | "Read: ML team, Analytics team. Write: Payments service only." |
| **Change management** | How schema changes are communicated | "14-day notice before any breaking schema change" |

**Example Data Contract:**

```yaml
# Data Contract: daily_transactions
contract:
  name: daily_transactions
  owner: payments-team
  version: 2.1
  
  schema:
    - field: transaction_id
      type: string
      format: "TXN-[0-9]{12}"
      nullable: false
      
    - field: customer_id
      type: string
      nullable: false
      quality: "< 0.5% null rate"
      
    - field: amount
      type: float
      range: [0.01, 10000000]
      
    - field: timestamp
      type: datetime
      format: ISO-8601
      
  sla:
    freshness: "every 4 hours"
    max_delay: "6 hours"
    uptime: "99.5%"
    
  consumers:
    - team: fraud-detection-ml
      purpose: "Training fraud model"
    - team: finance-analytics
      purpose: "Revenue dashboards"
```

**Why Data Contracts Matter for AI:**
- Without contracts, an upstream team changes a column name → your ML pipeline breaks silently
- Without quality SLAs, your model trains on garbage data without anyone noticing
- Contracts create **accountability** — when data quality drops, you know who to call

### AI Data Contracts vs Traditional Data Contracts

Traditional data contracts (for BI/analytics) focus on schema and freshness. But **AI systems need MORE** from their data contracts.

| Aspect | Traditional Contract | AI Contract (Adds) |
|---|---|---|
| **Schema** | Column names, types, formats | Same |
| **Freshness** | Update frequency, SLA | Same |
| **Completeness** | Max null % per field | Same |
| **Label quality** | Not applicable | Min inter-annotator agreement (κ > 0.7) |
| **Feature distribution** | Not tracked | Alert if distribution shifts (mean/variance changes > 10%) |
| **Bias metrics** | Not tracked | Demographic parity, equal opportunity metrics |
| **Drift thresholds** | Not tracked | Max allowed PSI (Population Stability Index) before retraining |
| **Training/Serving consistency** | Not applicable | Same features must be computable in both batch (training) and real-time (serving) |

**AI-Specific Contract Clauses:**

```
AI DATA CONTRACT ADDITIONS:
────────────────────────────
1. LABEL QUALITY
   - Inter-annotator agreement: κ > 0.75
   - Max label noise: < 5%
   - Label audit: random 2% sample reviewed monthly

2. DISTRIBUTION STABILITY
   - Feature distributions must not shift > 10% month-over-month
   - Alert if mean of "transaction_amount" changes > 15%
   - Automated PSI check weekly

3. BIAS MONITORING
   - Demographic parity across gender, region, age groups
   - Max accuracy gap between slices: 10%
   - Quarterly bias audit report

4. TRAINING-SERVING CONSISTENCY
   - All features must be computable within 100ms for real-time serving
   - Feature computation logic in training and serving must use same code
   - No features that require future data
```

**Contract Enforcement:** Modern teams use automated validation at pipeline boundaries — every time data flows from producer to consumer, automated checks verify the contract. If checks fail, the pipeline stops and alerts the producer.

### GenAI Changes Data Governance

Generative AI (ChatGPT, Gemini, Stable Diffusion) creates **entirely new governance challenges** that traditional frameworks weren't designed for.

GenAI changes data governance fundamentally: training data copyright concerns, generated content ownership questions, prompt injection risks, and the need to track what data was used for fine-tuning. Traditional governance frameworks cover none of these.

| Traditional AI Governance | NEW GenAI Governance Challenge |
|---|---|
| "What data trained this model?" | "What data trained this foundation model?" (often unknown — OpenAI doesn't reveal GPT-4's training data) |
| Data privacy for structured fields | **Training data copyright** — was copyrighted text/images used in training? |
| Model output is a number/category | **Generated content ownership** — who owns AI-generated text, images, code? |
| Input is a database query | **Prompt injection** — malicious prompts that make AI ignore safety rules |
| Bias in labels | **Hallucination governance** — AI confidently generates false information |

**New Governance Questions for GenAI:**

```
1. TRAINING DATA PROVENANCE
   ─────────────────────────
   Q: Was copyrighted material used to train/fine-tune our model?
   Q: Do we have licenses for all training data?
   Q: Can we prove our model wasn't trained on competitors' data?

2. GENERATED CONTENT OWNERSHIP
   ────────────────────────────
   Q: If our AI writes marketing copy, who owns it?
   Q: If AI generates code, can we use it in commercial products?
   Q: What if generated content is similar to existing copyrighted work?

3. PROMPT SECURITY
   ────────────────
   Q: Can users manipulate prompts to extract training data?
   Q: Can prompt injection bypass safety filters?
   Q: Are user prompts being logged and governed?

4. HALLUCINATION RISK
   ──────────────────
   Q: How do we handle AI-generated misinformation?
   Q: What's our liability if a customer acts on hallucinated advice?
   Q: How do we detect and flag uncertain outputs?
```

**Indian Example:** **Koo** (Indian social media platform) used AI to auto-translate posts between languages. Governance challenges: (1) AI-translated hate speech — who is responsible? (2) AI mistranslates sensitive political content — legal liability? (3) Users' posts used to improve translation model — do they consent? These are NEW problems that traditional data governance never had to handle.

### Data Incident Management and Blast Radius

A **data incident** is when data quality degrades unexpectedly — a source goes down, a schema changes without warning, data gets corrupted, or a pipeline breaks.

> **Analogy:** Think of a power outage. If the power goes out in one room, the blast radius is small (one room affected). But if the main transformer fails, the blast radius is huge (entire neighbourhood goes dark). Data incidents work the same way.

**Common Data Incidents:**

| Incident Type | What Happens | Example |
|---|---|---|
| **Source outage** | Data producer stops sending data | Payment gateway API goes down → no new transaction data |
| **Schema change** | Column names, types, or formats change without notice | Upstream team renames `cust_id` to `customer_id` → all downstream joins break |
| **Data corruption** | Invalid or garbage data enters the pipeline | A bug writes negative values in `order_amount` column |
| **Late arrival** | Data arrives hours or days late | Delivery partner data comes 12 hours late → real-time dashboards show stale info |
| **Silent failure** | Pipeline runs but produces wrong results (no error!) | Currency conversion uses stale exchange rate → all amounts are wrong |

**Blast Radius — How Far Does the Damage Spread?**

```
INCIDENT: "user_transactions" table is corrupted (negative amounts)

BLAST RADIUS MAPPING:
─────────────────────
user_transactions
    │
    ├── Fraud Detection Model
    │   └── AFFECTED: Model starts flagging legitimate transactions as fraud
    │
    ├── Recommendation Engine
    │   └── AFFECTED: Recommends wrong products (purchase history is wrong)
    │
    ├── Revenue Dashboard
    │   └── AFFECTED: CFO sees wrong revenue numbers
    │
    ├── Customer Lifetime Value Model
    │   └── AFFECTED: Customer value scores are incorrect
    │
    └── Tax Reporting System
        └── AFFECTED: Tax calculations are wrong (regulatory risk!)

BLAST RADIUS = 5 SYSTEMS
─────────────────────────
One corrupted table → five downstream systems broken.
```

**How to Manage Data Incidents:**

| Practice | What It Means | Example |
|---|---|---|
| **Dependency mapping** | Know which systems depend on which data sources | "If `user_transactions` fails, these 5 systems are affected" |
| **Automated monitoring** | Detect quality issues before they spread | Great Expectations checks → alert if null rate > 1% |
| **Circuit breakers** | Stop consuming data if quality checks fail | "If source data fails quality check, use last known good data instead" |
| **Runbooks** | Pre-written steps for common incidents | "If payment data is late: Step 1 — check API status. Step 2 — switch to backup source." |
| **Post-incident review** | Learn from incidents to prevent recurrence | "Why did no one notice corrupted data for 6 hours? → Add automated check." |

**Indian Example:** In 2023, a major **Indian e-commerce company** had a data incident — their product catalog service had a bug that set all product prices to Rs 0 for 2 hours. Blast radius: (1) pricing model served wrong prices, (2) recommendation model over-recommended Rs 0 products, (3) analytics showed massive "sales" spike, (4) customer service was flooded. Root cause: no circuit breaker on the pricing data pipeline. A simple "alert if average price drops > 50% in 1 hour" would have caught it immediately.

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

### AI Data Classification

AI data classification categorizes data by sensitivity and usage:

| Sensitivity Level | Description | Example |
|---|---|---|
| **Public** | Open to anyone | Published product catalog, public API docs |
| **Internal** | Visible within the company | Employee directory, internal dashboards |
| **Confidential** | Restricted to specific teams | Customer PII, financial records |
| **Restricted** | Highest sensitivity, strict access controls | Payment card data, health records, Aadhaar numbers |

For AI specifically, data also needs classification by **usage stage**:

| AI Usage Category | Governance Needs |
|---|---|
| **Training data** | Consent verification, bias auditing, version control |
| **Evaluation data** | Strict separation from training, representativeness checks |
| **Production inference data** | Real-time privacy compliance, logging policies |
| **Feedback data** | User consent for feedback loops, retention policies |

Each category has different governance needs — training data needs copyright clearance, evaluation data needs separation guarantees, and production data needs real-time compliance.

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

## 5.5 Enterprise Data Management for AI

### Enterprise Data Management vs ML-Ready Data Management

Most companies already have data management systems — databases, warehouses, dashboards. But **managing data for BI reports is very different from managing data for ML models.**

The gap between enterprise data management and AI data management is significant: enterprise DM focuses on reporting and compliance, while AI data management requires versioning, lineage, feature computation, and training/serving consistency. Most companies discover this gap only after their first ML project fails.

> **Analogy:** A library has books neatly organised on shelves (enterprise data management). But if you want to train a student, you don't just hand them the entire library — you need to select the right books, organise them in a curriculum, track which chapters they've studied, and test them on what they learned. That's ML-ready data management — it needs much more than just storage and retrieval.

| Aspect | Traditional Enterprise Data Mgmt | ML-Ready Data Management |
|---|---|---|
| **Primary goal** | Reporting, compliance, dashboards | Training models, feature computation, prediction serving |
| **Data versioning** | Maybe version schema, not data itself | Must version BOTH schema and data (need to reproduce training runs) |
| **Lineage** | "Where did this report number come from?" | "What exact data, with what transformations, trained this model?" |
| **Feature engineering** | Ad-hoc SQL queries | Reusable feature pipelines (feature store) |
| **Train/Test split** | Not applicable | Must maintain strict separation to avoid data leakage |
| **Label management** | Not applicable | Track labels, annotator quality, label versioning |
| **Data freshness** | Daily/weekly refresh is fine for reports | Real-time features need sub-second freshness |
| **Quality checks** | Schema validation, null checks | Plus distribution checks, drift detection, bias monitoring |

**The Gap — Clean for Reports ≠ Ready for ML:**

```
WHAT ENTERPRISE ALREADY HAS:
─────────────────────────────
✓ Clean customer data in a data warehouse
✓ Well-structured transaction tables
✓ Daily ETL pipelines
✓ BI dashboards showing revenue trends

WHAT ML ADDITIONALLY NEEDS:
────────────────────────────
✗ Labels (is this customer "churned"? Is this transaction "fraud"?)
✗ Feature versioning (which version of features trained which model?)
✗ Point-in-time feature computation (no future leakage)
✗ Feature store (reusable features across models)
✗ Data drift monitoring (is incoming data changing?)
✗ Training/serving consistency (same features in training and real-time)
```

**Indian Example:** **ICICI Bank** had excellent data warehousing for regulatory reports. But when they started building ML models for credit scoring, they discovered: (1) no labels — no one had systematically tagged which loans defaulted, (2) no versioning — couldn't reproduce last month's model, (3) features for reports (quarterly aggregates) were too coarse for ML (needed daily/weekly features). They had to build an ML data platform ON TOP of their existing enterprise data platform.

### Enterprise Data Maturity Model

Not all companies are equally ready for AI. The **data maturity model** describes where an organisation stands on its data journey.

| Level | Name | What It Looks Like | AI Readiness |
|---|---|---|---|
| **Level 1** | **Ad-hoc** | Data in spreadsheets and emails. No central storage. Teams hoard their own data. | Not AI-ready. Can't even do basic analytics. |
| **Level 2** | **Managed** | Databases exist. Some documentation. Basic ETL pipelines. Manual data quality checks. | Can do simple analytics. ML is possible but painful — lots of manual work. |
| **Level 3** | **Defined** | Data catalog exists. Quality metrics defined. Lineage tracked. Standard naming conventions. | ML projects can succeed with effort. Feature engineering is still manual. |
| **Level 4** | **Quantified** | Automated quality checks. SLAs on data freshness. Monitoring dashboards. Data contracts in place. | ML at scale. Feature stores, automated pipelines, reproducible training. |
| **Level 5** | **Optimized** | Data treated as a product. Self-service access. AI-ready data platform. Automated bias detection and drift monitoring. | Full AI/ML maturity. New models can be built and deployed in days, not months. |

**Where Are Indian Enterprises Today?**

```
LEVEL 1 (Ad-hoc):
  Small businesses, early-stage startups
  
LEVEL 2 (Managed):        ← MOST Indian enterprises are here
  Mid-size IT companies, traditional banks, government departments
  
LEVEL 3 (Defined):
  Progressive banks (HDFC, ICICI), large e-commerce (Flipkart)
  
LEVEL 4 (Quantified):
  Tech-forward companies (Razorpay, Swiggy, PhonePe)
  
LEVEL 5 (Optimized):
  Very few globally — Google, Netflix, Uber
  Some Indian unicorns are approaching this (Zerodha for trading data)
```

**Key Insight:** You can't jump from Level 1 to Level 5. Each level builds on the previous one. Trying to build advanced ML without Level 2-3 data infrastructure leads to failure.

### Traditional vs Modern Enterprise Data Architecture

The way companies store and process data has fundamentally changed in the last decade. Understanding both architectures helps you see where data for AI comes from.

**Traditional enterprise data architecture:** OLTP → ETL → Data Warehouse → BI dashboards. This is batch-only, structured-only, and has no native ML support.

**Modern enterprise data architecture:** Event Streams + Object Storage → ELT → Lakehouse → ML + BI + Real-time applications. This supports all data types, batch and streaming, and ML is a first-class citizen.

**Traditional Architecture (2000-2015):**

```
┌──────────────┐    ┌───────┐    ┌─────────────────┐    ┌──────────────┐
│ OLTP Database │───→│  ETL  │───→│  Data Warehouse  │───→│ BI Dashboards │
│ (MySQL, Oracle)│   │(nightly│    │ (Teradata,       │    │ (Tableau,     │
│               │    │ batch) │    │  Oracle DW)      │    │  Power BI)    │
└──────────────┘    └───────┘    └─────────────────┘    └──────────────┘
                     Extract       Structured only.         Reports only.
                     Transform     Expensive.               No ML.
                     Load          Fixed schema.            Batch only.
```

**Modern Architecture (2020+):**

```
┌───────────────┐
│  OLTP + APIs  │──┐
└───────────────┘  │    ┌────────────────┐    ┌───────────────────┐
                   ├───→│  Event Stream   │───→│    Lakehouse       │
┌───────────────┐  │    │  (Kafka)        │    │  (Databricks/      │───→ ML Models
│  IoT Sensors  │──┤    └────────────────┘    │   Snowflake)       │───→ BI Dashboards
└───────────────┘  │                          │                    │───→ Real-time Apps
                   │    ┌────────────────┐    │  Structured +      │───→ Feature Store
┌───────────────┐  ├───→│ Object Storage │───→│  Unstructured +    │
│  Logs, Files  │──┘    │  (S3)          │    │  Semi-structured   │
└───────────────┘       └────────────────┘    └───────────────────┘
                                                Open format.
                                                Batch + Streaming.
                                                ML + BI together.
```

**Key Differences:**

| Aspect | Traditional | Modern |
|---|---|---|
| **Data types** | Structured only | Structured + Unstructured + Semi-structured |
| **Processing** | Batch only (nightly ETL) | Batch + Real-time streaming |
| **Storage** | Expensive data warehouse | Cheap object storage (S3) + compute on demand |
| **Users** | BI analysts only | BI analysts + Data scientists + ML engineers |
| **Schema** | Fixed, rigid (schema-on-write) | Flexible (schema-on-read) |
| **ML support** | No native ML support | Built-in ML pipelines, feature stores |
| **Cost** | Very expensive (Oracle/Teradata licenses) | Pay-per-use cloud pricing |
| **Key tech** | Oracle, Teradata, Informatica | Kafka, Spark, Databricks, Snowflake, S3 |

**Supporting Multiple AI Consumers:** A modern data architecture must support multiple AI consumers simultaneously: batch ML training, real-time inference, analytics dashboards, and ad-hoc exploration — all from the same underlying data platform. If each team builds its own data pipeline, you get inconsistent data, duplicated effort, and conflicting results.

**Indian Example:** **Zerodha** (India's largest stock broker) moved from a traditional Oracle-based architecture to a modern event-streaming architecture (Kafka + ClickHouse). This allowed them to: (1) process millions of stock trades in real-time, (2) feed real-time data to ML models for risk management, (3) reduce infrastructure costs by 60%.

### Data Products, Contracts, and SLAs

A **data product** is a dataset treated like a product — it has an owner, documentation, quality metrics, versioning, and a service level agreement (SLA), just like a software API.

> **Analogy:** Think of data like a dish at a restaurant. A random home-cooked meal has no consistency — different every time. But a restaurant dish is a **product** — it has a recipe (schema), quality standards (taste), consistent portions (SLA), and a chef responsible (owner). Data products bring the same discipline to data.

**What Makes Data a "Product":**

A **data product contract** specifies: schema, freshness SLA, quality thresholds, access policies, ownership, and versioning rules. Without this contract, consumers have no guarantee about what they're getting.

| Property | Raw Data | Data Product |
|---|---|---|
| **Documentation** | "Ask Rahul, he knows the data" | Detailed data dictionary with field descriptions, examples, caveats |
| **Quality metrics** | "It's probably fine" | Published metrics: 99.2% complete, < 0.1% duplicates, refreshed every 2 hours |
| **Versioning** | Overwritten daily | v2.3 — schema changes documented, backward-compatible |
| **Owner** | Unclear (everyone's and no one's) | Payments Team owns this data product |
| **SLA** | "We'll try to update it" | 99.5% uptime, refreshed every 2 hours, max 4-hour delay |
| **Access** | Copy-paste from shared drive | API or SQL interface with access controls |
| **Discovery** | "I didn't know this data existed!" | Listed in data catalog, searchable |

**Data Product SLA Example:**

```
DATA PRODUCT: customer_360
──────────────────────────
Owner:       Customer Data Team
Version:     3.1
Refresh:     Every 2 hours
Uptime SLA:  99.5% (max 4 hours downtime/month)
Quality:
  - Completeness: > 98% (all required fields)
  - Freshness: < 2 hours for core fields
  - Null rate: < 0.1% for customer_id, < 2% for optional fields
  - Duplicate rate: < 0.05%
Consumers:
  - Recommendation ML model (reads every 6 hours)
  - Customer support dashboard (reads real-time)
  - Marketing segmentation (reads daily)
```

**Indian Example:** **PhonePe** treats their transaction data as a data product. The payments team publishes a `daily_upi_transactions` product with clear SLAs — 99.9% uptime, refreshed every hour, < 0.01% null rate on transaction_id. This allows downstream teams (fraud ML, analytics, compliance) to confidently build on it without worrying about data reliability.

---

## 5.6 AI Data Readiness and Transformation Roadmap

### AI Data Readiness Assessment

Before starting any AI/ML project, assess whether your organisation's data is actually ready. Many AI projects fail not because of model problems, but because the data wasn't ready.

**AI Data Readiness Checklist:**

| Area | Question to Ask | Red Flag If... |
|---|---|---|
| **Data Availability** | Does the data we need actually exist? | "We think someone has it somewhere..." |
| **Data Access** | Can the ML team access it legally and technically? | Need 6 approvals and 3 months to get access |
| **Data Volume** | Do we have enough data to train a model? | Less than a few thousand labelled examples |
| **Label Availability** | Do we have labels (ground truth) for supervised learning? | "We'll figure out labels later" |
| **Data Quality** | Is the data clean enough for ML? | > 20% missing values, no quality monitoring |
| **Data Freshness** | Is the data current enough? | Training data is 2+ years old, world has changed |
| **Data Infrastructure** | Can we build pipelines, version data, serve features? | Everything is in spreadsheets and manual SQL queries |
| **Data Governance** | Do we have permission to use this data for ML? | "Nobody has checked the legal stuff yet" |
| **Team Capability** | Do we have people who can engineer ML-ready data? | "Our DBA will figure it out" |

**Scoring Your Readiness:**

```
Score each area 1-5:

1 = Not started / Major gaps
2 = Aware but not addressed
3 = Partially addressed
4 = Mostly ready
5 = Fully ready

TOTAL SCORE INTERPRETATION:
───────────────────────────
9-18:   NOT READY — Fix data foundations first. Don't start ML yet.
19-27:  PARTIALLY READY — Can do simple ML with effort. Invest in infrastructure.
28-36:  MOSTLY READY — Can do ML at scale. Focus on optimisation.
37-45:  AI-READY — Full speed ahead. Focus on model quality and deployment.
```

### 90-Day Data Transformation Roadmap

For organisations at Level 1-2 maturity wanting to become AI-ready, here's a practical 90-day roadmap:

```
DAYS 1-30: FOUNDATION ("Know What You Have")
─────────────────────────────────────────────
Week 1-2:
  □ Inventory all data sources (databases, files, APIs, spreadsheets)
  □ Identify data owners for each source
  □ Document top 10 most valuable datasets

Week 3-4:
  □ Set up a basic data catalog (even a shared spreadsheet works to start)
  □ Define data quality metrics for top 5 datasets
  □ Run first quality assessment — measure completeness, accuracy, freshness
  □ Identify the #1 AI use case to pursue

DAYS 31-60: INFRASTRUCTURE ("Build the Pipes")
───────────────────────────────────────────────
Week 5-6:
  □ Set up a cloud data warehouse or lakehouse (BigQuery / Databricks)
  □ Build ETL pipelines for top 5 datasets
  □ Implement basic quality checks (null rates, schema validation)

Week 7-8:
  □ Create labelled dataset for the #1 use case
  □ Set up data versioning (DVC or similar)
  □ Define data contracts for ML-critical data sources
  □ Implement access controls and audit logging

DAYS 61-90: AI-READY ("First Model to Production")
───────────────────────────────────────────────────
Week 9-10:
  □ Build feature engineering pipeline for #1 use case
  □ Train first model on clean, versioned, labelled data
  □ Set up monitoring: data drift, model performance, quality dashboards

Week 11-12:
  □ Deploy model with automated retraining pipeline
  □ Run first bias audit on model predictions
  □ Document lessons learned, plan next 3 use cases
  □ Present results to leadership — show ROI of data investment
```

**Key Principle:** Start small, show results fast, then scale. Don't try to build a perfect data platform before training your first model. Pick one high-value use case, make it work end-to-end, then expand.

**Indian Example:** **Lenskart** followed this approach — they didn't build a massive data platform first. They picked ONE use case (virtual try-on using face images), built the data pipeline for just that use case, proved it worked, then used that success to get leadership buy-in for a broader data platform investment.

### What the Target Enterprise AI Architecture Should Look Like

The target enterprise AI architecture should include these integrated components:

```
┌──────────────────────────────────────────────────────────────────┐
│                    GOVERNANCE LAYER (across everything)          │
│  Access control · Lineage · Compliance · Bias monitoring         │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────┐  ┌──────────────┐  ┌────────────────────┐   │
│  │ Unified Data    │  │ Feature      │  │ Model Registry     │   │
│  │ Platform        │  │ Store        │  │ (MLflow, etc.)     │   │
│  │ (Lakehouse)     │→ │ (Feast, etc.)│→ │                    │   │
│  └────────────────┘  └──────────────┘  └────────────────────┘   │
│          ↓                   ↓                    ↓              │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │            Automated ML Pipelines (train → deploy)         │  │
│  └────────────────────────────────────────────────────────────┘  │
│          ↓                                                       │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │            Monitoring Dashboards (drift, quality, cost)     │  │
│  └────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

The key principle: **integrated, not siloed.** The data platform, feature store, model registry, ML pipelines, monitoring, and governance layer must work together as one system. Siloed tools create gaps where data quality, compliance, and reproducibility fall through.

---

## Key Terms Glossary (Session 5)

| Term | Simple Meaning |
|---|---|
| **Structured Data** | Data in fixed rows and columns (like a spreadsheet). Easy to query with SQL. |
| **Unstructured Data** | Data without fixed format — text, images, audio, video. Needs AI to process. |
| **Semi-Structured Data** | Partially organised data — JSON, XML, logs. Has some structure but flexible. |
| **Multimodal AI** | AI that processes multiple data types (text + images + audio) simultaneously |
| **Hidden Structure** | Structure embedded within unstructured data (e.g., EXIF in images, headers in emails) that AI can automatically extract |
| **Temporal Data** | Data that changes over time and requires point-in-time correctness for ML training |
| **Point-in-Time Correctness** | Using only features that were available at the historical moment when a decision was made — no future information allowed |
| **Event Data** | Immutable records of what happened (transactions, clicks, events). Append-only. Used for ML training. |
| **State Data** | Mutable records of current status (balance, inventory, status). Overwritten. Used for ML serving. |
| **Synthetic Data** | Artificially generated data (by AI or simulation) used when real data is scarce, expensive, or privacy-restricted |
| **Inter-Annotator Agreement** | How much human labellers agree on labels. Measured by Cohen's kappa (κ). Higher = more reliable labels. |
| **Cohen's Kappa (κ)** | A metric measuring annotator agreement beyond random chance. κ > 0.8 is excellent, κ < 0.4 is poor. |
| **Label (in ML)** | The "correct answer" a model learns from. Not objective truth — it's a business-defined judgment. |
| **Data Contract** | A formal agreement between data producer and consumer specifying schema, quality, freshness, and SLA |
| **Data Product** | A dataset treated like a product — with an owner, documentation, quality metrics, versioning, and SLA |
| **Data Product SLA** | Service Level Agreement for a data product — uptime, freshness, quality thresholds |
| **Data Drift** | When real-world data changes compared to what the model was trained on |
| **Data Leakage** | When future or test information accidentally gets into training data |
| **Class Imbalance** | When one category has far more examples than others in training data |
| **Imputation** | Filling in missing values with estimated values (mean, median, etc.) |
| **Missingness as Signal** | The fact that data is missing can itself be informative (e.g., missing employer = unemployed) |
| **MCAR / MAR / MNAR** | Three types of missing data: Missing Completely at Random, Missing at Random, Missing Not at Random |
| **Population Slice** | A specific segment of data/users (by geography, age, gender, etc.) — quality must be checked per slice |
| **PII (Personally Identifiable Information)** | Data that can identify a person — name, email, phone, Aadhaar number |
| **GDPR** | EU's data protection law. Fines up to 4% of global revenue. |
| **DPDP Act** | India's data protection law (2023). Fines up to Rs 250 crore. |
| **Data Lineage** | Tracking where data came from and how it was transformed |
| **Data Catalog** | A searchable inventory of all data assets in an organisation |
| **Machine Unlearning** | Removing the effect of specific data from a trained model |
| **Centralized Governance** | One central team controls all data governance. Consistent but can be a bottleneck. |
| **Federated Governance** | Each domain team governs their own data. Fast but can be inconsistent. |
| **Data Mesh** | A hybrid approach — federated data ownership with centralized governance standards |
| **Blast Radius** | How many downstream systems/models are affected when a single data source fails or degrades |
| **Data Incident** | An unexpected data quality degradation — source outage, schema change, corruption, or silent failure |
| **Circuit Breaker (Data)** | A mechanism that stops consuming data if quality checks fail, using last known good data instead |
| **Data Maturity Model** | A 5-level framework (Ad-hoc → Managed → Defined → Quantified → Optimized) describing an org's data readiness |
| **Feature Store** | A centralized repository for storing, versioning, and serving ML features for both training and real-time prediction |
| **Lakehouse** | A modern data architecture combining the low cost of data lakes with the quality of data warehouses |
| **ETL / ELT** | Extract-Transform-Load (traditional) vs Extract-Load-Transform (modern). ELT loads raw data first, transforms later. |
| **GenAI Governance** | New governance challenges from generative AI: training data copyright, generated content ownership, prompt injection |
| **AI Data Readiness** | Assessment of whether an organisation's data infrastructure, quality, and governance are sufficient for ML projects |

---

*End of Session 5*
