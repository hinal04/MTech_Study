# AI Systems — Exam-Style Questions

> BITS Pilani — **SS ZG662: Introduction to AI Systems**
>
> Covers: Sessions 1–8 (Module 1: Foundations of AI Systems + Module 2: Data and Feature Engineering)

---

## Section A: Short Answer Questions (2-3 marks each)

### Session 1: Introduction to AI

**Q1.** Define Artificial Intelligence. How is it different from traditional software?

**Answer:**
- **AI** is any system that mimics human intelligence — learning, reasoning, perception, and decision-making.
- **Traditional software:** Human writes explicit rules (if-else logic). Behavior is deterministic and predictable.
- **AI software:** System learns patterns from data automatically. Behavior is probabilistic and can improve over time.
- Key difference: In traditional software, humans encode logic; in AI, algorithms discover logic from data.

**Q2.** Draw the nested relationship between AI, Machine Learning, and Deep Learning. Give one example of each.

**Answer:**
- **Nested relationship:** AI ⊃ ML ⊃ DL (concentric circles — AI is outermost, DL is innermost)
- **AI** (broadest): Any system mimicking intelligence — e.g., rule-based chess engine
- **ML** (subset of AI): Learns patterns from data — e.g., spam email filter (XGBoost)
- **DL** (subset of ML): Uses deep neural networks — e.g., ChatGPT, image recognition
- Key: DL needs massive data + GPUs; ML needs moderate data; AI can work with rules and no data.

**Q3.** Why is the AI system lifecycle circular rather than linear? Give one real-world reason.

**Answer:**
- Unlike traditional software, ML models **degrade over time** due to data drift and concept drift.
- The 6 stages (Problem Definition → Data Collection → Model Development → Deployment → Monitoring → Iteration) form a continuous loop.
- **Real-world reason:** A fraud detection model trained on 2023 patterns will become inaccurate as fraudsters evolve new tactics in 2024 — requiring continuous retraining with new data (iteration back to Stage 2/3).

**Q4.** Name the 6 components of an AI system. Which component typically accounts for less than 5% of the total system?

**Answer:**
1. **Data Pipeline** — collect, clean, transform, store data
2. **Feature Pipeline** — convert raw data → ML features
3. **Model Pipeline** — train, evaluate, version, register models
4. **Serving Infrastructure** — host model, serve predictions via API
5. **Monitoring** — track accuracy, drift, latency, errors
6. **Application Layer** — UI/app that users interact with
- The **ML model code** accounts for less than 5% of the total system (the "tip of the iceberg"). Infrastructure and operations form the remaining 95%.

**Q5.** What is "training-serving skew"? Why is it dangerous?

**Answer:**
- **Training-serving skew** occurs when features are computed differently during training vs production serving, causing the model to receive different inputs than what it was trained on.
- **Dangerous because:** The model silently produces wrong predictions — there is no error or crash, just degraded accuracy that may go undetected.
- **Example:** A feature "avg_transaction_amount_30d" computed with SQL in training but with Python in serving may yield different results due to different null handling, causing the fraud model to miss real fraud.

### Session 2: Categories of AI Systems

**Q6.** List the four types of predictive AI tasks with one example each.

**Answer:**
| Type | What It Predicts | Example |
|---|---|---|
| **Classification** | Which category/label | Email spam detection (spam/not-spam) |
| **Regression** | A continuous number | House price prediction (₹85 lakhs) |
| **Time-series Forecasting** | Future values over time | Stock price / demand forecasting |
| **Anomaly Detection** | Whether something is unusual | Credit card fraud detection |

**Q7.** What is a "hallucination" in the context of Generative AI? Why does it happen?

**Answer:**
- **Hallucination:** When AI confidently generates factually incorrect or fabricated information (e.g., citing a research paper that doesn't exist).
- **Why it happens:** LLMs are trained to predict the most probable next token — they optimize for fluency, not factual accuracy. They have no mechanism to verify truth; they generate statistically plausible text even when wrong.
- **Mitigation:** RAG (Retrieval-Augmented Generation) grounds answers in real documents, reducing but not eliminating hallucinations.

**Q8.** Explain the cold start problem in recommender systems. How does Netflix solve it for new users?

**Answer:**
- **Cold start problem:** Collaborative filtering cannot recommend items to new users (no history) or new items (no interactions), because it relies on past behavior patterns.
- **Netflix's solution for new users:**
  - Asks preferences at signup (genre preferences, favorite shows)
  - Shows globally popular/trending content initially
  - Uses content-based filtering (recommend based on item features) until enough history accumulates
  - Rapidly learns from initial clicks/watches to switch to collaborative filtering

**Q9.** What is RAG (Retrieval-Augmented Generation)? How does it reduce hallucinations?

**Answer:**
- **RAG** is a technique that combines retrieval (searching relevant documents) with generation (LLM producing a response).
- **How it works:** User question → **Retrieve** relevant documents from a knowledge base → **Augment** the LLM prompt with these documents → LLM **Generates** an answer grounded in the retrieved content.
- **Reduces hallucinations because:** The LLM answers based on actual documents rather than relying solely on its parametric memory. Answers are factual and citable.
- **Note:** RAG does not eliminate hallucinations completely — the LLM can still misinterpret or incorrectly summarize retrieved content.

**Q10.** List the SAE levels of vehicle autonomy (0-5) with one real example for Level 2 and Level 4.

**Answer:**
| Level | Name | Who Drives? |
|---|---|---|
| 0 | No Automation | Human only |
| 1 | Driver Assistance | Human + one AI function (e.g., adaptive cruise control) |
| **2** | **Partial Automation** | **Human + steering + speed — e.g., Tesla Autopilot** |
| 3 | Conditional Automation | AI in specific conditions (Mercedes Drive Pilot) |
| **4** | **High Automation** | **AI in specific areas, no human needed — e.g., Waymo robotaxis** |
| 5 | Full Automation | AI everywhere — **does NOT exist yet** |

### Session 3: AI System Architecture

**Q11.** Name the five layers of an AI system architecture. Give one tool example for each layer.

**Answer:**
| Layer | Purpose | Tool Example |
|---|---|---|
| **Data Layer** | Collect, store, process, serve data | Apache Kafka, Feast |
| **Model Layer** | Train, evaluate, version, serve models | MLflow, TensorFlow Serving |
| **Application Layer** | User-facing app, business logic, A/B testing | FastAPI, React |
| **Infrastructure Layer** | Compute, storage, networking, containers | Kubernetes, Docker |
| **Monitoring Layer** | Logs, metrics, drift detection, alerts | Prometheus, Grafana |

**Q12.** What is "data drift"? Give an example of how data drift can silently degrade a fraud detection model.

**Answer:**
- **Data drift:** When the statistical distribution of live/production input data changes compared to the training data, causing model performance to degrade.
- **Example:** A fraud detection model trained on pre-COVID transaction data (mostly in-store purchases) sees a sudden shift to online transactions post-COVID. The model's feature distributions (transaction amounts, merchant types, locations) no longer match training data. The model silently misclassifies new fraud patterns, increasing false negatives — but no system error is raised.
- **Fix:** Continuous monitoring using statistical tests (KS test, PSI) to detect drift and trigger retraining.

**Q13.** Why did Zillow lose $881 million? Which architecture layer failed?

**Answer:**
- Zillow built an AI model (Zestimate) to predict house prices for its home-buying business (iBuying).
- The model **overpredicted prices** during COVID-era market volatility — buying homes for more than they were worth.
- **Architecture layer that failed: Monitoring Layer.** There was no adequate drift detection or alerting to catch that input data distributions had shifted dramatically.
- **Result:** $881 million loss, shutdown of the iBuying unit, 2,000 employees laid off.
- **Lesson:** Without monitoring, AI models silently serve wrong predictions at scale.

**Q14.** What is the difference between a Feature Store and a Data Warehouse in the context of AI systems?

**Answer:**
| Aspect | Feature Store | Data Warehouse |
|---|---|---|
| **Purpose** | Store & serve ML-ready features for training and inference | Store structured data for analytics and BI |
| **Users** | ML engineers, models | Data analysts, business users |
| **Serving** | Low-latency online store (Redis, <1ms) for real-time predictions | Batch queries (seconds–minutes) |
| **Key capability** | Prevents training-serving skew; single source of truth for features | OLAP queries, dashboards, reporting |
| **Example** | Feast, Tecton | BigQuery, Snowflake, Redshift |

### Session 4: AI Ecosystem and Marketplaces

**Q15.** Define "foundation model." List any three characteristics.

**Answer:**
- **Foundation model:** A large AI model trained on massive, broad datasets that serves as a general-purpose base, adaptable to many downstream tasks via fine-tuning or prompting.
- **Three characteristics:**
  1. **Trained on massive data** — billions of tokens/images (e.g., GPT-4 training cost ~$100M)
  2. **General-purpose** — not task-specific; can be adapted to translation, summarization, code generation, etc.
  3. **Transfer learning** — knowledge learned during pre-training transfers to new tasks with minimal additional data
- Examples: GPT-4, LLaMA 3, Gemini, Stable Diffusion

**Q16.** Name three benefits of open-source AI models over proprietary API-based models.

**Answer:**
1. **Data privacy** — Data never leaves your servers; with APIs, data is sent to the provider's servers.
2. **No vendor lock-in** — You can switch models freely, modify, and deploy anywhere. APIs tie you to one provider.
3. **Full customization** — You can fine-tune, modify architecture, and adapt the model to your domain. APIs only allow prompt engineering.
- Additional: No per-token API costs at scale (run on your own infrastructure).

**Q17.** What is Hugging Face? Why is it called the "GitHub of AI"?

**Answer:**
- **Hugging Face** is an open-source AI platform and community that hosts models, datasets, and ML applications.
- **Called "GitHub of AI" because:**
  - Hosts 500K+ pre-trained models that anyone can download and use
  - Provides version control for models and datasets (like Git for code)
  - Community-driven — researchers and developers share, collaborate, and improve models
  - Includes tools like Transformers library, Spaces (demo hosting), and Datasets library
- It democratizes AI by making state-of-the-art models accessible to all developers.

**Q18.** When should a company use an API vs fine-tune an open-source model vs build from scratch?

**Answer:**
| Approach | When to Use | Example |
|---|---|---|
| **API** (e.g., GPT-4) | Quick prototyping, generic tasks, small volume, small team (1-2 devs) | Customer support chatbot for a small startup |
| **Fine-tune open-source** (e.g., LLaMA) | Domain-specific needs, data privacy required, moderate team (2-5 ML engineers) | Legal document analysis for a law firm |
| **Build from scratch** | Core competitive advantage, unique data/problem, large team (10-50 researchers), massive budget | Google building its own search ranking model |
- **Key trade-off:** Time-to-deploy (hours → weeks → months), cost (low → moderate → very high), customization (low → high → maximum).

### Session 5: Data and Feature Engineering

**Q19.** Compare structured, semi-structured, and unstructured data with one example each.

**Answer:**
| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Format** | Fixed rows & columns | Flexible (self-describing) | No predefined format |
| **Storage** | SQL databases (MySQL, PostgreSQL) | Document DBs (MongoDB), Data Lakes | Object storage (S3) |
| **Query** | SQL queries | JSON/XML queries | AI models, vector search |
| **% of enterprise data** | ~20% | ~10% | ~70% |
| **Example** | Bank transaction table | JSON API logs, XML config | Emails, images, videos |
| **ML approach** | Tabular ML (XGBoost) | Parse → tabular | Deep Learning (CNNs, LLMs) |

**Q20.** List the six dimensions of data quality with a one-line definition of each.

**Answer:**
| Dimension | Definition |
|---|---|
| **Accuracy** | Values correctly represent reality (e.g., age = 25, not 250) |
| **Completeness** | No missing values where expected (e.g., all customers have an email) |
| **Consistency** | Same fact represented the same way everywhere (e.g., "India" not "IN" in one table and "India" in another) |
| **Timeliness** | Data reflects the current state (e.g., address updated after a move) |
| **Validity** | Data follows expected format/rules (e.g., phone number has 10 digits) |
| **Uniqueness** | No unintended duplicates (e.g., same customer not appearing twice) |

**Q21.** What is "data leakage" in ML? Give an example.

**Answer:**
- **Data leakage** occurs when information from outside the training dataset (typically future or target-derived information) is used to train the model, giving unrealistically high accuracy that fails in production.
- **Two causes:**
  - **Feature hiding target:** A feature directly encodes the answer
  - **Feature from the future:** Data not available at prediction time
- **Example:** In a loan default prediction model, including "approved_date" as a feature leaks the outcome — if the loan was approved, it means the decision was already made. The model learns to cheat rather than predict.

**Q22.** Name four key components of data governance and explain why they matter for AI systems.

**Answer:**
| Component | Why It Matters for AI |
|---|---|
| **Data Ownership** | AI models use data from multiple teams; clear ownership ensures accountability for data quality |
| **Access Control** | AI training data may contain PII; role-based access prevents unauthorized use of sensitive data |
| **Data Lineage** | Tracks where data came from and how it was transformed — critical for debugging model issues and audit trails |
| **Privacy & Compliance** | AI systems must comply with GDPR, DPDP Act, HIPAA — governs consent for ML training, right to be forgotten, bias auditing |
- Without governance, AI systems risk biased decisions, regulatory fines, and reputational damage (e.g., Cambridge Analytica: $5B fine).

**Q23.** How does India's DPDP Act (2023) impact AI systems? Name two specific requirements.

**Answer:**
- India's **Digital Personal Data Protection Act (2023)** regulates how organizations collect, process, and store personal data of Indian citizens ("data principals").
- **Two specific requirements impacting AI systems:**
  1. **Explicit consent required** — AI systems must obtain clear consent before using personal data for ML training; data collected for one purpose cannot be repurposed for model training without fresh consent.
  2. **Right to erasure** — Data principals can request deletion of their data, which means AI systems must be able to remove individual data from training sets and potentially retrain models (challenging for deep learning).
- **Max penalty:** ₹250 crore. Requires appointment of a Data Protection Officer.

### Session 6: Data Engineering Pipelines

**Q24.** What is a data pipeline? Draw its basic structure (Source → Extract → Transform → Load).

**Answer:**
- A **data pipeline** is an automated system that moves data from source systems through processing stages to a destination for analysis or ML.
- **Basic structure:**
```
Data Sources → Extract → Transform → Load → Destination
(DBs, APIs,    (Pull    (Clean,      (Write to    (Data Warehouse,
 Logs, Streams)  data)   aggregate,   storage)      Data Lake,
                         validate)                  Feature Store)
```
- **Key properties:** Automated, scheduled (or real-time), monitored, idempotent.
- **Tools:** Apache Airflow (orchestration), Apache Spark (processing), Kafka (streaming ingestion).

**Q25.** Compare ETL and ELT in a table with at least 4 differences.

**Answer:**
| Aspect | ETL | ELT |
|---|---|---|
| **Order** | Extract → Transform → Load | Extract → Load → Transform |
| **Where transformation happens** | Separate processing server (before loading) | Inside the warehouse/lake (after loading) |
| **Raw data retained?** | No — only transformed data is stored | Yes — raw data preserved in lake |
| **Flexibility** | Low — must re-extract to transform differently | High — can re-transform raw data anytime |
| **Best for** | On-premise, regulated environments, small data | Cloud, big data, AI/ML workloads |
| **Example tools** | Informatica, Talend | dbt + BigQuery, Spark + S3 |
- **Modern AI systems prefer ELT** because raw data retention allows experimentation with new features without re-extracting.

**Q26.** When would you use batch processing vs streaming processing? Give one use case for each.

**Answer:**
| Aspect | Batch Processing | Streaming Processing |
|---|---|---|
| **When** | Data processed in scheduled chunks (hourly/daily) | Each event processed as it arrives |
| **Latency** | Minutes to hours | Milliseconds to seconds |
| **Complexity** | Simpler to build and maintain | More complex (ordering, exactly-once) |
| **Cost** | Lower | Higher |
| **Use case** | **Model retraining** — retrain fraud model nightly on all new transactions | **Real-time fraud detection** — score each transaction as it happens in <100ms |

**Q27.** What is Lambda Architecture? Why do most real-world companies use both batch and streaming?

**Answer:**
- **Lambda Architecture** is a data processing pattern that combines both batch and streaming pipelines to handle all data needs.
- **Structure:** Data arrives → splits into two paths:
  - **Batch layer:** Processes all historical data (high accuracy, high latency) — e.g., Spark nightly jobs
  - **Speed layer:** Processes real-time events (low latency, approximate) — e.g., Kafka + Flink
  - **Serving layer:** Merges results from both for queries
- **Why both?** No single approach is sufficient:
  - Streaming alone is expensive and complex for training models on full history
  - Batch alone cannot provide real-time predictions (fraud, recommendations)
  - Example: Netflix uses batch for nightly model retraining + streaming for real-time session-based recommendations

**Q28.** Compare Data Lake, Data Warehouse, and Data Lakehouse with at least 3 differences.

**Answer:**
| Aspect | Data Lake | Data Warehouse | Data Lakehouse |
|---|---|---|---|
| **Data types** | Any (structured + unstructured) | Structured only | Any (structured + unstructured) |
| **Schema** | Schema-on-read (flexible) | Schema-on-write (strict) | Both (flexible + enforced) |
| **Cost** | Very low (object storage) | High (compute-optimized) | Low |
| **Query speed** | Slow (not optimized for queries) | Very fast (indexed, columnar) | Fast (optimized formats) |
| **Best for** | ML/AI, raw data archive | BI dashboards, analytics | ML + BI unified |
| **Risk** | Can become a "data swamp" without governance | Rigid, hard to adapt | Newer, less mature |
| **Example** | S3, ADLS | BigQuery, Snowflake, Redshift | Databricks Lakehouse, Delta Lake |

### Session 7: Feature Engineering

**Q29.** What is feature engineering? Why does it have a bigger impact on model accuracy than model selection?

**Answer:**
- **Feature engineering** is the process of converting raw data into meaningful features (inputs) that an ML model can understand and learn from.
- **Why bigger impact than model selection:**
  - "Applied ML is basically feature engineering" — Andrew Ng
  - A good feature directly encodes domain knowledge (e.g., "velocity of transactions in last hour" for fraud) that no model can discover from raw timestamps alone
  - A simple model (logistic regression) with great features often outperforms a complex model (deep learning) with poor features
  - Data scientists spend ~80% of their time on data collection and feature engineering, only ~20% on model selection/tuning

**Q30.** Explain any three feature transformation techniques with examples.

**Answer:**
1. **Min-Max Scaling** — Scales values to [0, 1] range. Formula: (x - min) / (max - min). Example: age 25 in range [18, 65] → (25-18)/(65-18) = 0.149. Use when you need bounded values.
2. **Log Transformation** — Applies log(x) to compress skewed distributions. Example: income values [₹10K, ₹50K, ₹1Cr] become [4, 4.7, 7] (log scale), reducing the impact of extreme outliers. Use for right-skewed data.
3. **One-Hot Encoding** — Converts categorical variables into binary columns. Example: city = {Delhi, Mumbai, Bangalore} → three columns: is_Delhi (1/0), is_Mumbai (1/0), is_Bangalore (1/0). Use for nominal (unordered) categories. Never use label encoding for nominal data — the model would incorrectly assume ordering.

**Q31.** What is the "curse of dimensionality"? How does feature selection help?

**Answer:**
- **Curse of dimensionality:** As the number of features increases, the data becomes increasingly sparse in high-dimensional space. More features require exponentially more data to maintain the same statistical significance.
- **Consequences:** Overfitting (model memorizes noise), slower training, harder to interpret, reduced generalization.
- **How feature selection helps:**
  - Removes irrelevant/redundant features that add noise
  - Reduces dimensionality so data density remains sufficient
  - Improves model accuracy by focusing on signal, not noise
  - Example: Selecting 15 important features from 500 raw features can improve both accuracy and speed.

**Q32.** Compare filter, wrapper, and embedded methods of feature selection.

**Answer:**
| Aspect | Filter | Wrapper | Embedded |
|---|---|---|---|
| **How it works** | Statistical tests per feature (independent of model) | Trains model with different feature subsets, picks best | Model learns feature importance during training |
| **Speed** | Fast | Slow (many model runs) | Moderate |
| **Accuracy** | Lower (ignores feature interactions) | Higher (evaluates actual model performance) | Good (balances both) |
| **Technique** | Correlation, chi-squared test, mutual information | Forward selection, backward elimination, RFE | L1/Lasso regularization, XGBoost feature importance |
| **When to use** | Initial screening of thousands of features | Small-medium feature sets, when accuracy is critical | When training the model anyway; good default choice |

**Q33.** What is an embedding? Why is it useful for ML? Give one example.

**Answer:**
- **Embedding:** A compact, dense numerical vector representation of high-dimensional data (words, images, users) where similar items are placed close together in vector space.
- **Why useful for ML:**
  - Converts sparse, categorical data into dense vectors that ML models can process
  - Captures semantic similarity (similar meanings = nearby vectors)
  - Enables vector arithmetic: "king − man + woman ≈ queen"
- **Example:** Word2Vec word embeddings — maps each word to a 100-300 dimensional vector. Words like "king" and "queen" are close in vector space because they appear in similar contexts. Used in NLP, search, and recommendation systems.

### Session 8: Feature Stores

**Q34.** What is a feature store? Name any two problems it solves.

**Answer:**
- A **feature store** is a centralized system that stores, manages, and serves ML features consistently for both model training and real-time serving.
- **Two problems it solves:**
  1. **Training-serving skew** — Without a feature store, features are computed differently in training (SQL/Spark) vs serving (Python/Java), causing silent accuracy degradation. A feature store provides a single source of truth.
  2. **Duplicate work** — Multiple teams independently build the same features (e.g., "user_avg_spend_30d"). A feature store computes once and shares across all teams via a searchable catalog.
- Tools: Feast (open-source), Tecton (commercial), Hopsworks.

**Q35.** Compare offline features and online features in a table with at least 4 differences.

**Answer:**
| Aspect | Offline Features | Online Features |
|---|---|---|
| **When computed** | Batch (nightly/hourly) | Real-time (as events happen) |
| **What they represent** | Historical aggregations (avg spend over 30 days) | Current state (items in cart right now) |
| **Latency requirement** | Minutes–hours acceptable | Must be <1-10ms |
| **Storage technology** | Data warehouse (BigQuery, Hive) | Key-value store (Redis, DynamoDB) |
| **Used for** | Model training (need full history) | Real-time predictions/serving |
| **Data size** | Very large (months of history) | Small (latest values only) |

**Q36.** What is training-serving skew? Name its four types.

**Answer:**
- **Training-serving skew** occurs when features used during model training differ from features available or computed during production serving, causing the model to make incorrect predictions.
- **Four types:**
  1. **Feature computation skew** — Same feature computed using different code paths (e.g., SQL in training vs Python in serving yields different results)
  2. **Data distribution skew** — Training data has a different statistical distribution than live production data (e.g., model trained on weekday data, serves weekend traffic)
  3. **Feature availability skew** — A feature available during training is not available at serving time (e.g., "credit_score" available in historical data but not at real-time inference)
  4. **Time-travel skew** — Training accidentally uses future information that wouldn't be available at prediction time (e.g., using next-month's sales to predict this month's demand)

**Q37.** Explain the two-stage recommendation architecture (candidate generation + ranking). Why not use just one stage?

**Answer:**
- **Stage 1: Candidate Generation** — Quickly filters millions of items to ~1,000 candidates using fast approximate nearest neighbor (ANN) search on item embeddings. Prioritizes speed over precision.
- **Stage 2: Ranking** — Scores the ~1,000 candidates using a complex model with rich features (user history, item attributes, context). Returns the top 20-50 items. Prioritizes accuracy.
- **Why not just one stage?** Running the full ranking model (with hundreds of features) on all 10 million items would take too long for real-time serving (<100ms budget). The two-stage design trades a small accuracy loss in filtering for massive speed gains.
- **Example:** Netflix → 15K titles → candidate generation selects ~500 → ranking model scores and orders top 40 for the user's homepage.

---

## Section B: Long Answer Questions (5-8 marks each)

### Session 1-2: AI Foundations

**Q38.** Compare AI, Machine Learning, and Deep Learning with respect to: (a) how they work, (b) data requirements, (c) feature engineering, (d) compute requirements, (e) examples. Use a table format. Then explain with a spam detection example how each approach would solve the problem differently. *[8 marks]*

**Answer:**

**Comparison Table:**

| Aspect | AI (Rule-Based) | Machine Learning | Deep Learning |
|---|---|---|---|
| **(a) How it works** | Human writes explicit rules, heuristics, search, logic | Algorithm learns patterns from data automatically | Deep neural networks discover features AND rules from raw data |
| **(b) Data requirements** | Little or none | Thousands–millions of examples | Millions–billions of examples |
| **(c) Feature engineering** | Manual rules by programmer | Manual feature engineering needed | Automatic feature learning (no manual engineering) |
| **(d) Compute requirements** | Low (CPU sufficient) | Medium (CPU/GPU) | High (GPUs/TPUs required) |
| **(e) Examples** | Chess engine, thermostat, IRCTC IVR | Spam filter (XGBoost), fraud detection | Image recognition, ChatGPT, language translation |

**Spam Detection — Three Approaches:**

1. **Rule-Based AI:** Human writes rules like "IF sender contains 'lottery' AND has attachment → SPAM." Fast to deploy but brittle — spammers change wording and rules break. Requires constant manual updates.

2. **Machine Learning (e.g., XGBoost):** Engineer manually creates features: word_count, has_link, sender_reputation, capital_letter_ratio, etc. Train a classifier on labeled spam/not-spam emails. Model learns which feature combinations indicate spam. Better than rules but relies on feature quality.

3. **Deep Learning (e.g., BERT):** Feed raw email text directly into a neural network. The model automatically learns which words, phrases, and patterns indicate spam — no manual features needed. Most accurate but needs millions of labeled emails and GPU training. Catches subtle patterns like sarcasm or context-dependent spam.

**Q39.** Explain the 6 stages of the AI system lifecycle with a real-world example (e.g., Swiggy delivery time prediction or Netflix recommendations). For each stage, mention: what happens, key activities, and common pitfalls. *[8 marks]*

**Answer:**

Using **Swiggy delivery time prediction** as the running example:

| Stage | What Happens | Key Activities | Common Pitfalls |
|---|---|---|---|
| **1. Problem Definition** | Translate business problem → ML problem. Swiggy: "Reduce customer complaints about late deliveries" → "Predict delivery time in minutes (regression)" | Define success metric (MAE < 5 mins), identify stakeholders, confirm ML is the right approach | Wrong problem framing; no clear metric; trying AI where simple rules suffice |
| **2. Data Collection & Prep** | Gather historical orders, restaurant prep times, traffic data, weather, rider locations. Clean and label data. | Handle missing values, remove duplicates, temporal train/test split, check for bias | Poor data quality (garbage in = garbage out); data leakage (using future info); selection bias (only urban data) |
| **3. Model Development** | Train regression models (XGBoost, neural net). Engineer features: distance, time_of_day, restaurant_avg_prep_time, rain_flag, active_riders_nearby. | Feature engineering, hyperparameter tuning, cross-validation, compare models | Overfitting to training data; not evaluating on realistic test set; ignoring feature importance |
| **4. Deployment** | Put model into production — serve predictions via API when user places an order. | API serving (FastAPI), A/B test new model vs old, shadow deployment, set latency SLA (<100ms) | Model works in notebook but fails in production; latency too high; no rollback plan |
| **5. Monitoring** | Track prediction accuracy over time. Alert if MAE increases. | Monitor data drift (traffic patterns change), prediction drift, system health (latency p95), business metrics (complaint rate) | No monitoring → silent degradation (Zillow lost $881M); not tracking business KPIs |
| **6. Iteration** | Improve based on feedback. Retrain with new data (monsoon patterns, new restaurants). | Collect ground truth (actual delivery times), retrain model, A/B test improvements | Infrequent retraining; ignoring concept drift; not closing the feedback loop |

**Why circular:** Swiggy's traffic patterns change with seasons, new restaurants open, rider fleet changes — the model must continuously retrain, making the lifecycle a loop, not a one-time project.

**Q40.** Describe any four categories of AI systems from the following: Predictive AI, Generative AI, Recommender Systems, Conversational AI, Computer Vision, Autonomous Systems. For each, explain: (a) what it does, (b) how it works, (c) one real-world case study with specific company name. *[8 marks]*

**Answer:**

**1. Predictive AI**
- **(a) What it does:** Takes historical structured data and predicts a number, label, or anomaly.
- **(b) How it works:** Supervised ML algorithms (XGBoost, Random Forest, neural nets) learn patterns from labeled training data. Four task types: classification, regression, time-series forecasting, anomaly detection.
- **(c) Case study — Swiggy:** Predicts delivery time (regression) using features like distance, restaurant prep time, traffic, weather, rider availability. Reduces customer complaints by setting accurate expectations.

**2. Generative AI**
- **(a) What it does:** Creates new content — text, images, code, music, video — from a prompt.
- **(b) How it works:** LLMs predict the next token based on massive pre-training data. Diffusion models start with noise and iteratively refine to create images. Fine-tuned with RLHF for helpfulness and safety.
- **(c) Case study — ChatGPT (OpenAI):** Pre-trained on internet text → RLHF fine-tuned with human feedback → served via API with safety filters. System components beyond the model include: content filters, rate limiting, context window management, and monitoring.

**3. Recommender Systems**
- **(a) What it does:** Suggests relevant items to users based on their behavior and preferences.
- **(b) How it works:** Three approaches — collaborative filtering ("similar users liked X"), content-based filtering ("items similar to what you liked"), and hybrid (combines both). Production systems use two-stage architecture: candidate generation (fast, ~1000) → ranking (accurate, ~20).
- **(c) Case study — Netflix:** Uses multiple models for different UI sections (Top Picks, Trending, Because You Watched). Even personalizes thumbnails per user. 80% of content watched comes from recommendations, saving $1B/year in retention.

**4. Autonomous Systems**
- **(a) What it does:** Perceives the environment through sensors and takes physical actions (steer, brake, pick up) without human intervention.
- **(b) How it works:** Sense → Think → Act loop. Sensors (cameras, LiDAR, radar) provide input → AI performs detection, tracking, prediction, planning → control systems execute steering/braking. Must complete in <100ms (vs human ~1500ms).
- **(c) Case study — Waymo:** Level 4 robotaxis using 29 cameras, 4 LiDAR, 6 radar, GPS+IMU, and microphones. Operates in specific geofenced areas (Phoenix, San Francisco). 20M+ autonomous miles driven. Level 5 (full automation everywhere) does not exist yet.

### Session 2: Case Studies

**Q41.** Explain how ChatGPT works in three stages (pre-training, RLHF fine-tuning, serving). What system components exist beyond the model itself? *[5 marks]*

**Answer:**

**Three Stages:**

| Stage | What Happens |
|---|---|
| **1. Pre-training** | Model trained on massive internet text corpus (books, websites, code). Learns grammar, facts, reasoning, and world knowledge by predicting the next token. Very expensive (~$100M for GPT-4). |
| **2. RLHF Fine-tuning** | Human evaluators rate model responses for helpfulness, truthfulness, and harmlessness. A reward model is trained on these ratings. The base model is then fine-tuned using Reinforcement Learning to maximize the reward score — making it helpful rather than just predictive. |
| **3. Serving** | User sends a prompt → model generates response word-by-word (auto-regressive) → safety/content filters check output → response returned to user. |

**System Components Beyond the Model:**
- **Content/safety filters** — Block harmful, toxic, or policy-violating outputs
- **Rate limiting & authentication** — Manage API access and prevent abuse
- **Context window management** — Handle conversation history within token limits
- **Load balancing & GPU inference infrastructure** — Serve millions of concurrent requests
- **Monitoring & logging** — Track latency, error rates, harmful content rates
- **Human feedback pipeline** — Continuously collect ratings for model improvement

The model is <5% of the total system — infrastructure, safety, and operations form the vast majority.

**Q42.** Describe the Netflix recommendation system covering: (a) data signals used, (b) multiple models for different UI sections, (c) personalised thumbnails, (d) offline + online processing. Why does Netflix say 80% of content watched comes from recommendations? *[5 marks]*

**Answer:**

**(a) Data signals used:**
- Explicit: ratings, thumbs up/down, search queries
- Implicit: watch history, watch duration, time of day, device, browse behavior, pause/rewind patterns
- Content metadata: genre, actors, director, language, release year

**(b) Multiple models for different UI sections:**
- "Top Picks for You" — personalized collaborative filtering model
- "Because You Watched X" — content-based similarity model
- "Trending Now" — popularity + recency model
- "New Releases" — content-based with freshness weighting
- Each row on the homepage uses a different algorithm optimized for that specific recommendation task.

**(c) Personalised thumbnails:**
- Netflix doesn't show the same thumbnail to all users. If a user watches many comedies, they see a funny scene thumbnail for a movie; if they watch action, they see an action scene from the same movie.
- Uses a multi-armed bandit approach to test and select the best-performing thumbnail per user segment.

**(d) Offline + online processing:**
- **Offline (batch):** Nightly model retraining on full viewing history, pre-compute user taste profiles and item embeddings using Spark
- **Online (real-time):** Session-based signals (what user is browsing right now), real-time re-ranking, A/B testing different algorithms

**Why 80%:** Netflix has ~15K titles — without recommendations, users would abandon the platform due to choice overload. Recommendations act as a personalized filter, matching content to taste so effectively that most viewing is driven by them, saving Netflix ~$1B/year in customer retention.

**Q43.** Explain the Waymo autonomous vehicle system covering: (a) sensors used and their purpose, (b) AI tasks (detection, tracking, prediction, planning, control), (c) why Level 5 autonomy doesn't exist yet. *[5 marks]*

**Answer:**

**(a) Sensors and their purpose:**
| Sensor | Count | Purpose |
|---|---|---|
| Cameras | 29 | 360° visual view — read signs, detect lane markings, identify objects |
| LiDAR | 4 | Creates 3D point cloud map — precise distance to objects |
| Radar | 6 | Detect objects in fog/rain/darkness (works where cameras fail) |
| GPS + IMU | 1 each | Precise location and motion tracking |
| Microphones | Multiple | Detect emergency vehicle sirens |

Sensor fusion combines all inputs for a comprehensive understanding of the environment.

**(b) AI tasks:**
| Task | What It Does | Time Budget |
|---|---|---|
| **Detection** | Identify objects (cars, pedestrians, cyclists, signs) in sensor data | Part of <100ms total |
| **Tracking** | Follow identified objects across frames, maintain identity | Part of <100ms total |
| **Prediction** | Predict what each object will do next (pedestrian will cross, car will turn) | Part of <100ms total |
| **Planning** | Determine optimal path from A to B avoiding all obstacles | <50ms |
| **Control** | Translate plan into physical actions (steering angle, throttle, brake pressure) | <10ms |

All tasks must complete within <100ms (humans take ~1500ms).

**(c) Why Level 5 doesn't exist yet:**
- **Edge cases are infinite:** Construction zones, emergency vehicles, hand signals from traffic police, unpredictable animal behavior — cannot be pre-programmed or fully learned
- **Weather limitations:** Heavy snow, fog, and rain degrade camera and LiDAR performance
- **Regulatory and liability:** No framework for fully autonomous driving on all roads
- **Cultural driving norms:** Driving behavior varies dramatically between cities and countries
- Waymo operates at Level 4 — full autonomy but only within specific geofenced areas (Phoenix, San Francisco).

### Session 3: Architecture

**Q44.** Describe the five-layer AI system architecture. For each layer, explain: (a) its purpose, (b) key components, (c) one popular tool, (d) one live company example. *[8 marks]*

**Answer:**

| Layer | (a) Purpose | (b) Key Components | (c) Tool | (d) Company Example |
|---|---|---|---|---|
| **Data Layer** | Collect, store, process, and serve data for training and inference | Data ingestion, storage (lake/warehouse), data processing, feature store, data quality monitoring | Apache Kafka (ingestion), Feast (feature store) | **Netflix** — ingests billions of viewing events daily via Kafka into S3 data lake |
| **Model Layer** | Train, evaluate, version, and serve ML models | Experiment tracking, model registry, model training, model serving (batch/real-time) | MLflow (experiment tracking), TensorFlow Serving | **Spotify** — trains recommendation models, versions them in a registry, serves via API |
| **Application Layer** | User-facing app, business logic, A/B testing of models | API gateway, frontend UI, A/B testing framework, business rules engine | FastAPI (API), LaunchDarkly (A/B testing) | **Swiggy** — shows predicted delivery time in the app, runs A/B tests on new models |
| **Infrastructure Layer** | Provide compute, storage, networking, and containerization | GPU/CPU clusters, container orchestration, auto-scaling, CI/CD pipelines | Kubernetes (orchestration), Docker, Terraform | **Waymo** — uses GPU clusters for model training and edge GPUs in vehicles for inference |
| **Monitoring Layer** | Track model performance, data drift, system health, and business metrics | Logging, alerting, drift detection, dashboards | Prometheus + Grafana (metrics), Great Expectations (data quality) | **Zillow** — lacked monitoring → $881M loss. Now an anti-pattern for skipping this layer. |

**Key insight:** All 5 layers must work together. The model layer (~5% of code) gets the most attention, but the other 4 layers determine real-world success.

**Q45.** Trace a complete request through all 5 layers of an AI system for a food delivery ETA prediction (like Swiggy). Show what happens at each layer from "user places order" to "user sees estimated delivery time" to "system monitors prediction accuracy." *[8 marks]*

**Answer:**

**Scenario:** User opens Swiggy app and places an order → sees "Estimated delivery: 35 minutes."

**1. Application Layer (Request Entry)**
- User taps "Place Order" in the Swiggy app (React Native frontend)
- API gateway (FastAPI) receives the request with: user_id, restaurant_id, order_items, current_time, user_location
- A/B testing framework decides which model version to use for this user

**2. Data Layer (Feature Assembly)**
- **Feature store online store (Redis)** retrieves pre-computed features in <1ms:
  - Offline features: restaurant_avg_prep_time (20 min), rider_avg_delivery_time, user_distance_to_restaurant (3.5 km)
  - Online features: current_active_riders_nearby (8), current_restaurant_queue_length (5 orders), is_raining (true), current_traffic_score (0.7)
- Features assembled into a feature vector and sent to the model layer

**3. Model Layer (Prediction)**
- Model serving endpoint (TensorFlow Serving / Triton) receives the feature vector
- Trained XGBoost regression model predicts: **35.2 minutes**
- Model version and prediction logged to experiment tracker (MLflow)

**4. Infrastructure Layer (Execution)**
- Kubernetes auto-scales serving pods during peak dinner hours
- GPU/CPU resources allocated for model inference
- Load balancer distributes requests across model replicas
- Entire inference completes in <100ms (p99 latency)

**5. Monitoring Layer (Continuous)**
- **Real-time:** Log prediction (35 min) and actual delivery time (38 min) → MAE tracked
- **Data drift detection:** Monitor if feature distributions are shifting (e.g., more rain than training data covered)
- **Business metrics:** Track customer complaints about delivery time accuracy
- **Alerts:** If MAE > 7 minutes for 1 hour → alert ML team → trigger model retraining pipeline
- **Feedback loop:** Actual delivery times become ground truth for next retraining cycle (lifecycle loops back)

**Q46.** Explain why monitoring is critical for AI systems but less critical for traditional software. Use the Zillow case study ($881M loss) to illustrate what happens without proper monitoring. What metrics should be monitored? *[5 marks]*

**Answer:**

**Why monitoring is critical for AI but less so for traditional software:**

| Aspect | Traditional Software | AI Systems |
|---|---|---|
| **Behavior** | Deterministic — same input = same output | Probabilistic — predictions can degrade silently |
| **Failure mode** | Crashes, errors (visible) | Silent accuracy degradation (invisible) |
| **Degradation** | Doesn't change unless code changes | Model degrades over time due to data drift / concept drift |
| **Testing** | Unit tests catch most bugs | No test can guarantee future accuracy |

Traditional software either works or throws an error. AI systems can return "200 OK" with wrong predictions — no error, no crash, just increasingly bad decisions.

**Zillow Case Study:**
- Zillow's Zestimate model predicted house prices for its iBuying business.
- COVID caused unprecedented housing market shifts (data drift) — prices spiked in unpredictable ways.
- The model overpredicted prices, and Zillow bought thousands of homes at inflated prices.
- **No monitoring caught the drift** — no alerts for distribution shift or prediction accuracy degradation.
- **Result:** $881 million loss, shutdown of iBuying, 2,000 layoffs.
- **Lesson:** A 5% monitoring investment could have prevented an $881M loss.

**Metrics to Monitor:**

| Category | Metrics |
|---|---|
| **Model Performance** | Accuracy, F1, MAE, RMSE tracked over time (not just at training) |
| **Data Drift** | KS test, PSI (Population Stability Index) on input features |
| **Prediction Drift** | Output score distribution changes over time |
| **System Health** | Latency (p50/p95/p99), error rate, throughput |
| **Business Metrics** | Revenue impact, customer complaints, conversion rate, CTR |

### Session 4: Ecosystem

**Q47.** A startup CTO needs to decide between using an API (GPT-4), fine-tuning an open-source model (LLaMA), or building from scratch for the following three scenarios. Recommend and justify your answer for each:
(a) A customer support chatbot for a small e-commerce company
(b) A fraud detection system for a bank
(c) A medical imaging AI for a hospital chain
*[6 marks]*

**Answer:**

**(a) Customer Support Chatbot — Small E-commerce → Use API (GPT-4)**
- **Justification:** Small team (1-2 devs), generic task (answering product/order questions), low volume. GPT-4 API gives excellent conversation quality out of the box. Use RAG to ground answers in the company's product catalog and policies.
- **Why not others:** Fine-tuning is overkill for a generic chatbot. Building from scratch requires 10-50 researchers — impossible for a small startup.
- **Trade-off:** Data sent to OpenAI servers, but customer queries are not highly sensitive.

**(b) Fraud Detection for a Bank → Fine-tune Open-Source Model**
- **Justification:** Banks have strict data privacy requirements (RBI regulations, DPDP Act) — transaction data cannot leave bank servers. Fine-tuning an open-source model (e.g., tabular ML models or domain-specific models) on proprietary transaction data gives high accuracy with full data control.
- **Why not API:** Bank transaction data is highly sensitive — cannot send to a third-party API. Also, fraud patterns are unique to each bank — generic models won't work.
- **Why not from scratch:** Banks have domain-specific data but not the resources of a research lab. Fine-tuning is sufficient.

**(c) Medical Imaging AI for Hospital Chain → Build from Scratch (or fine-tune specialized models)**
- **Justification:** Safety-critical application where errors cost lives. Requires FDA/regulatory approval, extensive validation on hospital-specific data, full control over model behavior. Hospital has enough X-ray/CT data and the budget (hospital chain) for a dedicated ML team.
- **Why not API:** No medical imaging API meets regulatory requirements for clinical use. Patient data cannot leave hospital servers (HIPAA/DPDP).
- **Pragmatic alternative:** Fine-tune an open-source medical imaging model (e.g., pre-trained on CheXpert) on the hospital's own labeled X-rays — faster than full from-scratch but still gives full control.

**Q48.** Explain the concept of foundation models covering: (a) definition and key characteristics, (b) why they are expensive to train, (c) how they are made accessible (APIs + fine-tuning), (d) the trend from APIs to open-source. Name at least 4 notable foundation models. *[5 marks]*

**Answer:**

**(a) Definition and Key Characteristics:**
A foundation model is a large AI model pre-trained on massive, diverse datasets that serves as a general-purpose base, adaptable to many tasks.
- **Characteristics:** Trained on billions of tokens/images; general-purpose (not task-specific); exhibits transfer learning; shows emergent abilities (capabilities not explicitly trained for); very expensive to train.

**(b) Why expensive to train:**
- Requires billions of data points (internet-scale text/images)
- Needs thousands of GPUs/TPUs running for weeks-months
- GPT-4 training cost: ~$100 million+
- Human annotation for RLHF adds labor cost
- Only well-funded labs (OpenAI, Google, Meta) can afford this

**(c) How made accessible:**
- **APIs:** Pay-per-token access (GPT-4 via OpenAI API, Claude via Anthropic API) — no infrastructure needed, just send requests. Best for quick prototyping and low-volume use.
- **Fine-tuning:** Download open-source model weights → retrain on your domain-specific data → deploy on your servers. Requires ML expertise and GPU infrastructure, but gives full control and privacy.

**(d) Trend from APIs to open-source:**
- Initially dominated by proprietary APIs (GPT-3, GPT-4)
- Meta released LLaMA (open-source) → community exploded
- Hugging Face hosts 500K+ models — "GitHub of AI"
- Companies increasingly prefer open-source for: cost savings at scale, data privacy, no vendor lock-in, community innovation
- Trend: Start with API for prototyping → move to open-source for production

**4 Notable Foundation Models:**
1. **GPT-4/GPT-4o** (OpenAI) — most capable general-purpose, multimodal
2. **LLaMA 3** (Meta) — leading open-source text model, free
3. **Gemini** (Google) — natively multimodal
4. **Stable Diffusion** (Stability AI) — open-source image generation

### Session 5: Data Quality & Governance

**Q49.** A bank is building a credit scoring model and discovers these data quality issues:
(a) 15% of applicants have missing "employment_years"
(b) Training data includes "credit_bureau_score" which is updated after loan decisions
(c) Training data only covers urban professionals (not rural applicants)
(d) Only 2% of loans default

For each issue, identify: the data quality problem type, why it's harmful, and how to fix it. *[8 marks]*

**Answer:**

| Issue | Problem Type | Why Harmful | How to Fix |
|---|---|---|---|
| **(a) 15% missing employment_years** | **Completeness** | Missing values cause model to either ignore these rows (losing 15% of data) or learn incorrect patterns. Employment tenure is a strong credit signal. | Impute with median/mean, or create a binary feature `is_employment_years_missing` (missingness itself may be informative — informal sector workers are more likely to have missing data). |
| **(b) credit_bureau_score updated after loan decision** | **Data leakage** (timeliness) | This is a future feature — the credit bureau score changes after the loan is granted/denied. Model sees information it wouldn't have at prediction time, giving inflated accuracy in training but failing in production. | Remove the feature or use the credit_bureau_score snapshot from *before* the loan application date. Enforce strict temporal splitting. |
| **(c) Only urban professionals in training data** | **Selection bias** (representativeness) | Model will perform poorly on rural applicants — different income patterns, employment types, credit behavior. May systematically deny loans to rural applicants (fairness violation). | Collect representative rural applicant data, use stratified sampling, monitor model fairness across demographic groups, apply bias mitigation techniques. |
| **(d) Only 2% default rate** | **Class imbalance** | A model predicting "no default" for everyone achieves 98% accuracy but catches zero actual defaults. Precision/recall will be terrible for the positive class. | Use techniques: SMOTE (synthetic oversampling), class weights (penalize missing defaults more), threshold tuning, evaluate using precision/recall/F1/AUC-ROC instead of accuracy. |

**Q50.** Explain data governance for AI systems covering: (a) why it's more important for AI than traditional analytics, (b) at least 5 key components, (c) AI-specific challenges (consent for ML, right to be forgotten, bias auditing), (d) the Cambridge Analytica case as an example of governance failure. *[8 marks]*

**Answer:**

**(a) Why more important for AI than traditional analytics:**
- AI systems make **automated decisions** that directly affect people (loan approvals, hiring, medical diagnoses) — bad data leads to discriminatory outcomes at scale
- AI models **memorize patterns** from training data — biased data produces biased models permanently
- AI systems are **harder to audit** — black-box models can't easily explain why a decision was made
- Traditional analytics produces reports for humans to interpret; AI systems act autonomously

**(b) 5 Key Components of Data Governance:**
| Component | What It Covers |
|---|---|
| **Data Ownership** | Who is responsible for each data source — ensures accountability |
| **Access Control** | Who can access what data, for what purpose — prevents unauthorized use |
| **Data Lineage** | Where data came from, how it was transformed — critical for debugging and auditing |
| **Privacy & Compliance** | GDPR, DPDP Act, HIPAA compliance — legal requirements |
| **Data Cataloging** | Central inventory of all data assets — enables discovery and prevents duplication |
| **Quality Standards** | Defined thresholds (completeness >95%, freshness <24hrs) — ensures reliable inputs |
| **Data Retention** | How long data is kept and when deleted — regulatory requirement |

**(c) AI-Specific Challenges:**
- **Consent for ML training:** Data collected for one purpose (e.g., service delivery) may not have consent for ML training. DPDP Act requires explicit consent for each purpose.
- **Right to be forgotten:** Users can request deletion of their data. For AI, this means potentially retraining models after removing specific user data — technically challenging for deep learning.
- **Bias auditing:** AI models must be regularly audited for fairness across protected groups (gender, caste, religion). Requires governance frameworks that mandate bias testing before deployment.

**(d) Cambridge Analytica Case Study:**
- Facebook quiz app harvested data from 87 million users without their consent
- Data was shared with Cambridge Analytica for targeted political advertising
- **Governance failures:** No access control (app could harvest friends' data), no audit trail, no consent verification, no data lineage tracking
- **Consequence:** $5 billion FTC fine for Facebook, catalyst for GDPR enforcement globally
- **Lesson:** Without governance, data collected for one purpose gets weaponized for another — causing massive regulatory, financial, and reputational damage.

### Session 6: Data Pipelines

**Q51.** Compare ETL and ELT approaches with respect to: (a) order of operations, (b) where transformation happens, (c) raw data retention, (d) flexibility, (e) best use cases, (f) example tools. Which approach is more common in modern AI systems and why? *[6 marks]*

**Answer:**

| Aspect | ETL | ELT |
|---|---|---|
| **(a) Order** | Extract → Transform → Load | Extract → Load → Transform |
| **(b) Transformation location** | Separate processing server (outside the warehouse) | Inside the warehouse/lake (using its compute) |
| **(c) Raw data retention** | No — only transformed data is loaded | Yes — raw data preserved in lake, transformed later |
| **(d) Flexibility** | Low — must re-extract from source to create new transformations | High — raw data in lake allows new transformations anytime |
| **(e) Best use cases** | On-premise, regulated environments, small data, traditional BI | Cloud, big data, AI/ML, data science experimentation |
| **(f) Example tools** | Informatica, Talend, SSIS | dbt + BigQuery, Apache Spark + S3, Snowflake |

**Which is more common in modern AI systems?**
**ELT** is dominant in modern AI systems because:
- AI/ML requires experimentation — data scientists need raw data to try different feature engineering approaches without re-extracting
- Cloud data warehouses (BigQuery, Snowflake) have massive compute, making in-warehouse transformation fast and cheap
- Raw data retention supports retraining models with new features from the same historical data
- Schema-on-read allows flexibility as ML requirements evolve

**Q52.** Compare batch processing and streaming processing with respect to: (a) when data is processed, (b) latency, (c) complexity, (d) cost, (e) use cases. Then explain Lambda Architecture and why most companies use both approaches together. Give a live example. *[6 marks]*

**Answer:**

| Aspect | Batch Processing | Streaming Processing |
|---|---|---|
| **(a) When processed** | In scheduled chunks (hourly/daily) | Each event as it arrives (continuous) |
| **(b) Latency** | Minutes to hours | Milliseconds to seconds |
| **(c) Complexity** | Simpler (well-understood, easier debugging) | More complex (ordering, exactly-once delivery, state management) |
| **(d) Cost** | Lower (process in bulk, use spot instances) | Higher (always-on infrastructure, dedicated compute) |
| **(e) Use cases** | Reports, model training, data warehouse loads, feature aggregations | Fraud detection, real-time recommendations, live dashboards, alerting |
| **Tools** | Apache Spark, Apache Airflow, dbt | Apache Kafka, Apache Flink, Spark Streaming |

**Lambda Architecture:**
A data processing pattern combining both batch and streaming:
```
                    ┌─ Batch Layer (Spark) ─── complete, accurate, slow ──┐
Raw Data → Kafka → ├                                                      ├→ Serving Layer → Query
                    └─ Speed Layer (Flink) ── approximate, fast ──────────┘
```
- **Batch layer:** Processes all historical data periodically — high accuracy, high latency
- **Speed layer:** Processes real-time events — low latency, approximate results
- **Serving layer:** Merges results from both layers for queries

**Why companies use both:**
Neither batch alone nor streaming alone is sufficient:
- Streaming alone: Too expensive for training models on full history; complex state management
- Batch alone: Cannot provide real-time predictions (fraud must be caught in <100ms)

**Live example — HDFC Bank fraud detection:**
- **Batch:** Nightly Spark job retrains the fraud model on all historical transactions and pre-computes customer spending profiles
- **Streaming:** Kafka ingests each transaction in real-time → Flink computes live features (transactions in last 5 minutes) → model scores in <100ms
- **Both combined:** Batch features (customer's 90-day avg spend) + streaming features (transactions in last 5 min) fed together to the model

**Q53.** Design a complete data pipeline for an e-commerce recommendation system (like Myntra). Cover: (a) data sources, (b) streaming pipeline components, (c) batch pipeline components, (d) storage layer (lake vs warehouse), (e) feature store integration, (f) monitoring. Name specific tools for each component. *[8 marks]*

**Answer:**

**(a) Data Sources:**
| Source | Data Type | Examples |
|---|---|---|
| User activity | Event/clickstream | Page views, searches, add-to-cart, purchases, time spent |
| User profiles | State data | Age, gender, location, preferences, size |
| Product catalog | Master data | SKU, category, brand, price, images, descriptions |
| Order history | Transactional | Past orders, returns, ratings, reviews |
| External | Contextual | Weather (seasonal fashion), trending styles, festival calendar |

**(b) Streaming Pipeline Components:**
```
User actions → Apache Kafka (event ingestion) → Apache Flink (real-time processing)
    → Compute: session features (items browsed in last 5 min, current cart contents)
    → Write to Redis (online feature store)
    → Real-time re-ranking trigger
```
- **Kafka:** Ingests clickstream events as they happen
- **Flink:** Processes events, computes real-time features, filters invalid events
- **Redis:** Stores latest user session context for <1ms serving

**(c) Batch Pipeline Components:**
```
All data sources → Apache Airflow (orchestration) → Apache Spark (processing)
    → Compute: user taste profiles, item embeddings, collaborative filtering scores
    → Retrain recommendation models nightly
    → Write to BigQuery (offline store) + update Feast (feature store)
```
- **Airflow:** Schedules nightly ETL jobs
- **Spark:** Processes full history, aggregates, computes offline features
- **MLflow:** Track model experiments, version models

**(d) Storage Layer:**
| Storage | Technology | Purpose |
|---|---|---|
| **Data Lake (S3)** | Raw event logs, images, unprocessed data | Archive, ML training data, schema-on-read |
| **Data Warehouse (BigQuery)** | Clean, structured user/product/order tables | BI analytics, reporting, offline feature computation |
| **Data Lakehouse (Delta Lake)** | Unified layer with ACID transactions | Combines ML flexibility with BI query speed |

**(e) Feature Store Integration (Feast):**
| Feature Type | Examples | Store |
|---|---|---|
| **Offline features** | user_avg_spend_90d, user_preferred_brands, item_popularity_score | BigQuery (offline store) |
| **Online features** | items_in_current_session, time_since_last_purchase, current_cart_value | Redis (online store) |
- Feature registry enables discovery and prevents duplication across teams
- Same feature definition used for training and serving → prevents training-serving skew

**(f) Monitoring:**
| Monitor | Tool | Metric |
|---|---|---|
| Data quality | Great Expectations | Completeness >99%, freshness <1hr |
| Pipeline health | Airflow + PagerDuty | Job failures, SLA breaches |
| Data drift | Custom + Prometheus | Feature distribution shifts (PSI) |
| Model performance | MLflow + Grafana | CTR, conversion rate, NDCG |
| System health | Prometheus + Grafana | Latency p95 <50ms, error rate <0.1% |

### Session 7: Feature Engineering

**Q54.** You are building a fraud detection model for credit card transactions. The raw data includes: card_id, merchant_name, merchant_category, amount, timestamp, location_lat, location_lng, is_online.

Design at least 15 features across these categories:
(a) Transaction-level features
(b) Velocity features (speed of transactions)
(c) Location features
(d) User profile features

For each feature, explain what it captures and why it's useful for fraud detection. *[8 marks]*

**Answer:**

**(a) Transaction-Level Features:**

| # | Feature | What It Captures | Why Useful |
|---|---|---|---|
| 1 | `amount_zscore` | How unusual this amount is vs user's history (standard-scaled) | Fraudsters often test with small amounts then go large |
| 2 | `is_round_amount` | Whether amount is round (₹1000, ₹5000) | Fraud often involves round amounts |
| 3 | `is_high_risk_category` | Whether merchant_category is gambling/crypto/gift_cards | Certain categories are fraud-prone |
| 4 | `hour_of_day` | Extracted from timestamp (0-23) | Fraud peaks at unusual hours (3-5 AM) |
| 5 | `is_weekend` | Whether transaction is on Saturday/Sunday | Different fraud patterns on weekends |

**(b) Velocity Features:**

| # | Feature | What It Captures | Why Useful |
|---|---|---|---|
| 6 | `txn_count_last_1hr` | Number of transactions in last 1 hour | Rapid multiple charges indicate card compromise |
| 7 | `txn_count_last_24hr` | Number of transactions in last 24 hours | Unusually high daily activity = red flag |
| 8 | `total_amount_last_1hr` | Sum of amounts in last 1 hour | Fraudsters drain accounts quickly |
| 9 | `unique_merchants_last_1hr` | Number of different merchants in last 1 hour | Multiple merchants in quick succession = suspicious |
| 10 | `time_since_last_txn_seconds` | Seconds since previous transaction | Very short gaps between transactions = automated fraud |

**(c) Location Features:**

| # | Feature | What It Captures | Why Useful |
|---|---|---|---|
| 11 | `distance_from_last_txn_km` | Geographical distance from previous transaction | If last txn was Delhi and this is London 5 min later → impossible travel |
| 12 | `distance_from_home` | Distance from user's usual location | Transactions far from home are riskier |
| 13 | `is_new_country` | First time transacting in this country | International fraud on new-country cards |
| 14 | `travel_speed_kmph` | distance_from_last_txn / time_since_last_txn | Speed > 900 kmph = physically impossible = likely fraud |

**(d) User Profile Features (Aggregated):**

| # | Feature | What It Captures | Why Useful |
|---|---|---|---|
| 15 | `user_avg_txn_amount_30d` | User's average transaction amount over 30 days | Baseline to detect anomalous amounts |
| 16 | `user_preferred_categories` | Most common merchant categories for this user | Transaction in an unusual category = suspicious |
| 17 | `user_typical_txn_hour` | Most common transaction hours | Transaction at 4 AM for a user who shops at 7 PM = risky |
| 18 | `days_since_card_issued` | Age of the card | Newly issued cards have higher fraud rates |

**Q55.** Explain the following feature transformation techniques with examples: (a) Min-Max Scaling, (b) Standard Scaling, (c) Log Transformation, (d) One-Hot Encoding, (e) Label Encoding. For encoding, explain when to use one-hot vs label encoding and why using label encoding for nominal data is wrong. *[6 marks]*

**Answer:**

**(a) Min-Max Scaling:**
- Scales values to [0, 1] range. Formula: `(x - min) / (max - min)`
- Example: Ages [18, 25, 65] → [0.0, 0.149, 1.0]
- Use when: Features need bounded values (e.g., neural networks work better with [0,1] inputs)

**(b) Standard Scaling (Z-score Normalization):**
- Transforms to mean=0, std=1. Formula: `(x - mean) / std`
- Example: If mean salary = ₹50K, std = ₹20K → salary ₹70K becomes (70K-50K)/20K = 1.0
- Use when: Data is normally distributed; algorithms assume standardized input (SVM, logistic regression)

**(c) Log Transformation:**
- Applies `log(x)` to compress right-skewed distributions.
- Example: Incomes [₹10K, ₹50K, ₹1Cr] → [4.0, 4.7, 7.0] — reduces impact of extreme outliers
- Use when: Data is heavily right-skewed (income, house prices, transaction amounts)

**(d) One-Hot Encoding:**
- Creates one binary column per category value.
- Example: City = {Delhi, Mumbai, Bangalore} →

| is_Delhi | is_Mumbai | is_Bangalore |
|---|---|---|
| 1 | 0 | 0 |
| 0 | 1 | 0 |

- Use when: **Nominal data** (categories with no natural ordering). Most common encoding for categorical features.

**(e) Label Encoding:**
- Assigns an integer to each category. Example: Education = {High School: 1, Bachelor's: 2, Master's: 3, PhD: 4}
- Use when: **Ordinal data** (categories with a natural order)

**When to use One-Hot vs Label Encoding:**
| Data Type | Encoding | Example |
|---|---|---|
| **Nominal** (no order) | One-Hot | City, color, merchant_name |
| **Ordinal** (has order) | Label | Education level, satisfaction rating |

**Why label encoding for nominal data is wrong:**
Label encoding assigns numbers (Delhi=1, Mumbai=2, Bangalore=3), which makes the model think Mumbai (2) is "between" Delhi (1) and Bangalore (3), and that Bangalore is "greater than" Delhi. This introduces a false ordinal relationship that doesn't exist, leading to incorrect model learning.

**Q56.** Compare filter, wrapper, and embedded methods of feature selection. For each, explain: (a) how it works, (b) advantages and disadvantages, (c) one specific technique. Then explain why using all available features is not always better — use the "curse of dimensionality" concept. *[6 marks]*

**Answer:**

| Aspect | Filter Method | Wrapper Method | Embedded Method |
|---|---|---|---|
| **(a) How it works** | Evaluates each feature independently using statistical tests (no model involved) | Trains model with different feature subsets, evaluates model performance to select best subset | Model automatically learns feature importance during its own training process |
| **(b) Advantages** | Fast, scalable to high dimensions, model-agnostic | Highest accuracy (considers feature interactions and actual model performance) | Good balance of speed and accuracy; no separate selection step needed |
| **(b) Disadvantages** | Ignores feature interactions; may miss useful feature combinations | Very slow (trains model many times); risk of overfitting to the selection process | Specific to one model type; may not transfer to another model |
| **(c) Technique** | **Correlation analysis** — remove features with >0.95 correlation (redundant); **Chi-squared test** for categorical features | **Recursive Feature Elimination (RFE)** — repeatedly remove least important feature and retrain | **L1/Lasso regularization** — shrinks unimportant feature weights to exactly zero; **XGBoost feature importance** |

**Why using all features is not always better — Curse of Dimensionality:**
- As features increase, the data becomes increasingly **sparse** in high-dimensional space
- The model needs **exponentially more data** to maintain statistical significance — 100 features might need millions of rows
- **Irrelevant features add noise** that the model overfits to — learning patterns in noise rather than signal
- More features = **slower training**, harder to interpret, more prone to overfitting
- Example: A model with 500 features (most irrelevant) performs worse than one with 15 carefully selected features, because the noise from 485 junk features overwhelms the signal from 15 good ones
- **Rule of thumb:** Start with many features, then use selection methods to find the most impactful subset

**Q57.** What are embeddings? Explain with the example of word embeddings (Word2Vec). How do embeddings capture similarity? Show how "king - man + woman ≈ queen" works. List three types of embeddings used in AI systems with a live example for each. *[5 marks]*

**Answer:**

**What are embeddings?**
Embeddings convert high-dimensional sparse data (words, images, users) into compact, dense numerical vectors (typically 64-2048 dimensions) where **similar items are placed close together** in vector space.

**Word2Vec Example:**
- Each word is mapped to a dense vector (e.g., 300 dimensions)
- Words appearing in similar contexts get similar vectors
- "king" and "queen" have nearby vectors because they appear in similar sentence patterns
- Training: Model learns to predict surrounding words given a target word (or vice versa), which forces it to learn semantic relationships

**How embeddings capture similarity:**
- Similarity is measured by cosine similarity or Euclidean distance between vectors
- Words used in similar contexts → similar vectors → high cosine similarity
- "apple" (fruit) is closer to "banana" than to "apple" (company) in well-trained embeddings

**"king − man + woman ≈ queen" — Vector Arithmetic:**
```
vector("king") - vector("man") + vector("woman") ≈ vector("queen")
```
- "king" - "man" isolates the concept of "royalty" by removing the "male" component
- Adding "woman" adds the "female" component to "royalty"
- The result lands near "queen" — showing that embeddings capture analogical relationships as linear vector operations

**Three Types of Embeddings:**

| Type | Dimensions | Captures | Live Example |
|---|---|---|---|
| **Word Embeddings** (Word2Vec, GloVe) | 100-300 | Word meaning and relationships | Google Search — understands "car" and "automobile" are similar |
| **Sentence Embeddings** (BERT, Sentence-BERT) | 384-1024 | Full sentence meaning | Semantic search — find documents with similar meaning, not just keywords |
| **User/Item Embeddings** | 64-256 | User taste profiles and item characteristics | **Netflix** — each user and movie is a 256-dim vector; nearby vectors = good match. **Spotify Discover Weekly** — user and song embeddings power playlist generation |

### Session 8: Feature Stores & Real-Time Systems

**Q58.** Explain what a feature store is and why it's needed. Cover: (a) the 5 problems it solves, (b) architecture (offline store, online store, feature registry, feature pipelines), (c) how it prevents training-serving skew, (d) name 3 popular feature store tools. *[6 marks]*

**Answer:**

A feature store is a centralized platform that stores, manages, and serves ML features consistently for both training and real-time inference.

**(a) 5 Problems It Solves:**

| Problem | Without Feature Store | With Feature Store |
|---|---|---|
| 1. Training-serving skew | Features computed differently in training vs serving | Single source of truth — same computation used everywhere |
| 2. Duplicate work | 5 teams independently build same feature (e.g., user_avg_spend) | Compute once, share across all teams |
| 3. Slow serving | Features computed on-the-fly at serving time (high latency) | Pre-computed, cached in online store (<1ms) |
| 4. No feature discovery | Teams don't know what features already exist | Searchable feature registry/catalog |
| 5. No versioning | Feature definition changes break existing models silently | Versioned features with backward compatibility |

**(b) Architecture:**
```
Data Sources → Feature Pipelines (Spark/Flink) → ┬─ Offline Store (BigQuery/Hive)
                                                   ├─ Online Store (Redis/DynamoDB)
                                                   └─ Feature Registry (Catalog + Metadata)
```
- **Offline Store:** Stores full historical feature values in a warehouse. Used for model training (reads months of data).
- **Online Store:** Stores only the latest feature values in a low-latency key-value store. Used for real-time serving (<1ms reads).
- **Feature Registry:** Central catalog of all features with metadata (name, description, owner, version, lineage). Enables discovery and reuse.
- **Feature Pipelines:** Batch (Spark) and streaming (Flink) jobs that compute features from raw data and write to both stores.

**(c) How it prevents training-serving skew:**
- Features are defined **once** in the feature store with a single computation logic
- The same feature definition is used to populate both the offline store (for training) and online store (for serving)
- This eliminates the root cause of skew: different code paths computing the "same" feature differently

**(d) 3 Popular Feature Store Tools:**
1. **Feast** — Open-source, best for startups and mid-size companies
2. **Tecton** — Commercial SaaS, best for large enterprises needing managed infrastructure
3. **Hopsworks** — Open-source + commercial, strong in research and enterprise

**Q59.** Compare offline features and online features with respect to: (a) when they are computed, (b) what they represent, (c) latency requirements, (d) storage technology, (e) use cases. Then show with a Swiggy ETA prediction example how both offline and online features are combined for a single prediction. *[6 marks]*

**Answer:**

| Aspect | Offline Features | Online Features |
|---|---|---|
| **(a) When computed** | Batch — nightly or hourly scheduled jobs | Real-time — as events happen (streaming) |
| **(b) What they represent** | Historical aggregations and long-term patterns | Current state and real-time context |
| **(c) Latency** | Minutes to hours acceptable | Must be <1-10ms |
| **(d) Storage** | Data warehouse (BigQuery, Hive) — optimized for large scans | Key-value store (Redis, DynamoDB) — optimized for point lookups |
| **(e) Use cases** | Model training (need full history), batch predictions | Real-time predictions, live scoring |

**Swiggy ETA Prediction — Combining Both Feature Types:**

When a user places an order, the model needs features from both stores:

| Feature | Type | Source | Value (Example) |
|---|---|---|---|
| `restaurant_avg_prep_time_30d` | Offline | BigQuery (computed nightly) | 22 minutes |
| `restaurant_avg_rating` | Offline | BigQuery | 4.2 stars |
| `rider_avg_delivery_time_30d` | Offline | BigQuery | 18 minutes |
| `user_distance_to_restaurant_km` | Offline | Pre-computed | 3.5 km |
| `current_active_riders_nearby` | **Online** | Redis (streaming from rider GPS) | 8 riders |
| `current_restaurant_queue_length` | **Online** | Redis (streaming from restaurant app) | 5 pending orders |
| `is_raining_now` | **Online** | Redis (weather API stream) | True |
| `current_traffic_score` | **Online** | Redis (Google Maps API, real-time) | 0.7 (high) |

**Flow:**
1. User taps "Place Order" → API request
2. Feature store online store fetches real-time features from Redis in <1ms
3. Feature store offline store fetches pre-computed features from cache
4. Both combined into a single feature vector
5. Model predicts: ETA = **38 minutes**
6. User sees "Estimated delivery: 38 minutes"

Offline features provide stable baseline context; online features capture the dynamic real-time situation.

**Q60.** Explain the four types of training-serving skew with one example each: (a) feature computation skew, (b) data distribution skew, (c) feature availability skew, (d) time-travel skew. How does a feature store prevent these? Use the Zillow case study to illustrate the real-world impact of skew. *[6 marks]*

**Answer:**

**(a) Feature Computation Skew:**
- **What:** Same feature computed using different code in training vs serving.
- **Example:** `avg_transaction_amount_30d` computed with SQL in training (handles NULLs one way) and Python in serving (handles NULLs differently). Values differ subtly, causing wrong predictions.
- **Feature store fix:** Feature defined once — same computation populates both offline (training) and online (serving) stores.

**(b) Data Distribution Skew:**
- **What:** Statistical distribution of live data differs from training data.
- **Example:** Fraud model trained on pre-COVID data (mostly in-store purchases). Post-COVID, 70% of transactions are online — a distribution the model never saw. Model misclassifies new fraud patterns.
- **Feature store fix:** Feature store + monitoring tracks distribution shifts. Alerts triggered when feature distributions diverge beyond thresholds.

**(c) Feature Availability Skew:**
- **What:** A feature used during training is not available at serving time.
- **Example:** Training data includes `credit_bureau_score` (available in historical data), but at serving time, the credit bureau API has 500ms latency — too slow for real-time inference. Model receives NULL instead.
- **Feature store fix:** Feature registry documents latency requirements and availability guarantees for each feature. Features marked as "online-available" are validated before inclusion in serving models.

**(d) Time-Travel Skew:**
- **What:** Training accidentally uses future information that wouldn't exist at prediction time.
- **Example:** Predicting loan default using `days_until_first_missed_payment` — this is a future feature that only becomes known after the loan is granted. Model achieves 99% training accuracy but 50% in production.
- **Feature store fix:** Feature store enforces point-in-time correctness — when generating training data, it only uses features that were available at the exact timestamp of each training example.

**Zillow Case Study — Real-World Impact:**
- Zillow's Zestimate model suffered primarily from **data distribution skew** — the COVID housing market had fundamentally different patterns than training data
- Also likely had **feature computation skew** — different teams computing housing features differently
- **No monitoring** caught these skews in time
- **Result:** Model overpredicted prices → Zillow overpaid for thousands of homes → **$881M loss**, 2,000 layoffs, business unit shut down
- **Key lesson:** A feature store with drift detection and point-in-time correctness could have prevented or caught these issues early

**Q61.** Describe the two-stage architecture of a real-time recommendation system:
(a) Stage 1: Candidate Generation — what it does, how ANN search works, input/output sizes
(b) Stage 2: Ranking — what it does, what features it uses, why it's more accurate
(c) Why can't we just use the ranking model on all items?
(d) Trace a complete request through this architecture (user opens app → sees recommendations)
Use a real company example (Netflix, Spotify, or Amazon). *[8 marks]*

**Answer:**

Using **Spotify Discover Weekly** as the running example:

**(a) Stage 1: Candidate Generation**
- **What it does:** Quickly filters millions of songs to ~1,000 candidates that are roughly relevant to the user.
- **How ANN (Approximate Nearest Neighbor) search works:**
  - Each user and song is represented as an embedding vector (64-256 dimensions)
  - ANN search finds the ~1,000 song vectors closest to the user's vector in embedding space
  - Uses optimized index structures (HNSW, FAISS) for sub-millisecond retrieval — doesn't compare all items linearly
- **Input:** All songs in catalog (~100M) + user embedding
- **Output:** ~1,000 candidate songs
- **Speed:** <10ms (must be extremely fast since it scans the full catalog)

**(b) Stage 2: Ranking**
- **What it does:** Scores the ~1,000 candidates using a rich feature set to produce the final ordered list.
- **Features used:**
  - User features: listening history, genre preferences, time-of-day patterns, skip rate
  - Song features: genre, tempo, popularity, artist, audio embeddings
  - User-song interaction features: has user listened to this artist before? Similar songs listened to?
  - Context features: time of day, day of week, device type
- **Why more accurate:** Uses 100+ features vs candidate generation which uses only embeddings. Can capture nuanced preferences (user likes jazz on weekday mornings but rock on weekends).
- **Output:** Top 30 songs ranked by predicted listen probability

**(c) Why not use ranking model on all items?**
- The ranking model uses 100+ features per song and runs a complex neural network
- Running it on 100M songs × 100+ features = **too slow** (would take minutes, not milliseconds)
- Two-stage design: candidate generation (fast, approximate) narrows to 1,000 → ranking (slow, accurate) picks the best 30
- **Trade-off:** Lose some potentially good songs in filtering (~1% recall loss) but gain massive speed (1000x faster)

**(d) Complete Request Trace — Spotify Discover Weekly:**

```
1. User opens Spotify app on Monday morning
   ↓
2. API request → Application Layer (user_id, timestamp, device)
   ↓
3. Feature Store (Online): Fetch user embedding from Redis (<1ms)
   ↓
4. CANDIDATE GENERATION:
   - ANN search over 100M song embeddings using FAISS
   - Finds 1,000 songs with nearest vectors to user embedding
   - Time: <10ms
   ↓
5. Feature Store: Fetch rich features for 1,000 candidates
   - Online features: user's recent listens, current mood playlist
   - Offline features: user genre preferences, song popularity scores
   ↓
6. RANKING:
   - Neural network scores each of 1,000 songs using 100+ features
   - Predicts P(user will listen fully and not skip)
   - Sorts by score → Top 30 songs selected
   - Time: <50ms
   ↓
7. Business Rules: Remove recently played songs, ensure artist diversity
   ↓
8. Response: 30-song "Discover Weekly" playlist displayed to user
   Total latency: <100ms
```

**Key insight:** The two-stage architecture enables personalized recommendations from 100M items in <100ms — impossible with a single model.

---

## Section C: Scenario-Based / Design Questions (8-10 marks each)

**Q62.** You are the Chief AI Officer at a large Indian bank (like HDFC or SBI). The bank wants to build an AI-powered fraud detection system. Answer the following:

(a) What category of AI system is this? (Predictive/Generative/etc.)
(b) What data sources would you use? (Internal and external)
(c) Design at least 10 features for the model
(d) Should you build from scratch, use an API, or fine-tune open-source? Justify.
(e) Describe the architecture — which of the 5 layers handle what?
(f) What data quality issues might you face?
(g) What data governance requirements apply? (DPDP Act, RBI regulations)
(h) How would you monitor the system in production?
*[10 marks]*

**Answer:**

**(a) Category:** **Predictive AI — Anomaly Detection.** Each transaction is classified as fraudulent or legitimate (binary classification). Real-time scoring is required (<100ms per transaction).

**(b) Data Sources:**

| Type | Source | Data |
|---|---|---|
| **Internal** | Core Banking System | Account balances, customer profiles, KYC data |
| **Internal** | Transaction System | Transaction history (amount, merchant, timestamp, location, channel) |
| **Internal** | Card Management | Card issuance date, card type, usage patterns |
| **Internal** | Customer Service | Past fraud reports, dispute history |
| **External** | Credit Bureau (CIBIL) | Credit scores, other bank defaults |
| **External** | Device/IP Intelligence | Device fingerprinting, IP geolocation, known fraud IPs |
| **External** | Merchant Risk Database | Merchant fraud history, category risk scores |

**(c) 10+ Features:**

| # | Feature | Type |
|---|---|---|
| 1 | `amount_vs_user_avg_ratio` | Transaction-level |
| 2 | `is_first_time_merchant` | Transaction-level |
| 3 | `hour_of_day` | Temporal |
| 4 | `txn_count_last_1hr` | Velocity |
| 5 | `total_amount_last_24hr` | Velocity |
| 6 | `unique_merchants_last_1hr` | Velocity |
| 7 | `distance_from_last_txn_km` | Location |
| 8 | `travel_speed_kmph` | Location (impossible travel detection) |
| 9 | `is_new_country` | Location |
| 10 | `days_since_card_issued` | User profile |
| 11 | `user_avg_txn_amount_30d` | User profile (offline) |
| 12 | `is_high_risk_merchant_category` | Merchant risk |

**(d) Build vs Buy vs Fine-tune → Fine-tune Open-Source**

| Option | Verdict | Reason |
|---|---|---|
| API (GPT-4) | **No** | Cannot send financial transaction data to third-party servers (RBI regulations, data sovereignty). Also, fraud detection is tabular ML, not an LLM task. |
| Build from scratch | **Partially** | Bank has proprietary data. Use established ML libraries (XGBoost, LightGBM) with custom feature engineering — not training a foundation model from scratch. |
| **Fine-tune / Custom ML** | **Yes** | Build custom tabular ML models (XGBoost/LightGBM) on bank's proprietary transaction data. Full data control, regulatory compliance, and domain-specific feature engineering. |

**(e) 5-Layer Architecture:**

| Layer | Components | Tools |
|---|---|---|
| **Data Layer** | Kafka ingests real-time transactions; Spark batch processes history; Feast serves features; S3 data lake | Kafka, Spark, Feast, S3 |
| **Model Layer** | XGBoost model trained on historical fraud labels; MLflow tracks experiments; model versioned and registered | XGBoost, MLflow, model registry |
| **Application Layer** | Real-time scoring API — every transaction hits the model in <100ms; rules engine for hard blocks (known fraud merchants); case management UI for investigators | FastAPI, React dashboard |
| **Infrastructure Layer** | Kubernetes cluster for model serving; auto-scaling during peak hours (salary days, festivals); on-premise or private cloud (data sovereignty) | Kubernetes, Docker, on-prem GPUs |
| **Monitoring Layer** | Track precision/recall over time; data drift detection; alert if fraud catch rate drops; business metrics (false positive rate, investigation backlog) | Prometheus, Grafana, Great Expectations |

**(f) Data Quality Issues:**

| Issue | Impact |
|---|---|
| **Class imbalance** — only ~0.1% of transactions are fraud | Model predicts "not fraud" for everything; need SMOTE, class weights |
| **Missing merchant location** for online transactions | Location features unavailable; use `is_online` flag + IP geolocation instead |
| **Label noise** — some fraud goes unreported for months | Training labels incomplete; use time-delayed label collection |
| **Data leakage** — including investigation outcome in features | Model sees future info; strict temporal splitting required |
| **Selection bias** — past model decisions bias training data | Transactions blocked by old model have no outcome; use causal inference techniques |

**(g) Data Governance Requirements:**

| Requirement | Application |
|---|---|
| **DPDP Act (India)** | Consent for using customer data for ML; right to erasure; data protection officer required; max penalty ₹250 crore |
| **RBI Data Localization** | All financial data must be stored on servers within India — cannot use foreign cloud regions |
| **Explainability** | RBI requires banks to explain why a transaction was blocked — use SHAP/LIME for model interpretability |
| **Bias auditing** | Ensure model doesn't discriminate by geography, gender, or income level; regular fairness audits |
| **Data retention** | Transaction data retention as per RBI norms; old data must be purged on schedule |
| **Access control** | Role-based access — not all employees can access transaction-level data |

**(h) Production Monitoring:**

| What to Monitor | Metric | Alert Threshold |
|---|---|---|
| **Model performance** | Precision, Recall, F1 (on labeled fraud) | Recall drops below 85% |
| **Data drift** | PSI on key features (amount distribution, channel mix) | PSI > 0.2 |
| **Prediction drift** | Fraud score distribution over time | Sudden shift in score distribution |
| **System health** | Latency p95, error rate | p95 > 100ms or error rate > 0.1% |
| **Business metrics** | False positive rate, fraud losses (₹), investigation backlog | Monthly fraud losses increase >20% |
| **Retraining trigger** | Automated retraining when drift detected | Weekly retraining minimum |

**Q63.** Design a complete AI system for Swiggy's restaurant recommendation. Cover:

(a) Problem definition and success metrics
(b) Data sources (user data, restaurant data, external data)
(c) Feature engineering — design 10 features each for users, restaurants, and user-restaurant interactions
(d) Pipeline architecture — batch and streaming components
(e) Feature store design — which features are offline vs online?
(f) Two-stage recommendation architecture (candidate generation + ranking)
(g) Monitoring — what metrics to track, what alerts to set
(h) How would recommendations update in real-time as the user browses?
*[10 marks]*

**Answer:**

**(a) Problem Definition and Success Metrics:**
- **Problem:** Given a user opening Swiggy app, recommend restaurants they are most likely to order from.
- **ML framing:** Ranking problem — score and rank all nearby restaurants by predicted order probability.
- **Success metrics:**
  - **CTR** (click-through rate on recommended restaurants) — primary online metric
  - **Conversion rate** (clicks → actual orders) — revenue metric
  - **NDCG** (ranking quality) — offline metric
  - **Coverage** (% of restaurants getting recommended) — fairness metric
  - **Business:** Average order value, orders per user per month

**(b) Data Sources:**

| Category | Source | Examples |
|---|---|---|
| **User data** | App activity | Order history, search queries, browse patterns, ratings, delivery addresses |
| **User data** | Profile | Location, preferred cuisines, dietary restrictions, average spend |
| **Restaurant data** | Restaurant system | Menu items, prices, cuisine type, ratings, prep time, operating hours |
| **Restaurant data** | Operational | Current queue length, active/inactive status, driver availability |
| **External data** | APIs | Weather (rain → comfort food), time of day, festivals/events, trending cuisines |

**(c) Feature Engineering — 30 Features:**

**User Features (10):**
| # | Feature | What It Captures |
|---|---|---|
| 1 | `user_order_count_30d` | Activity level |
| 2 | `user_avg_order_value` | Spending pattern |
| 3 | `user_preferred_cuisines` | Top-3 cuisine types by order count |
| 4 | `user_avg_rating_given` | How generous a rater they are |
| 5 | `user_order_frequency_weekday_vs_weekend` | Temporal ordering pattern |
| 6 | `user_veg_nonveg_ratio` | Dietary preference signal |
| 7 | `user_avg_delivery_distance` | How far they typically order from |
| 8 | `user_peak_order_hour` | Most common ordering time |
| 9 | `user_reorder_rate` | % of orders from previously ordered restaurants |
| 10 | `user_embedding` | 128-dim vector from collaborative filtering (captures latent taste) |

**Restaurant Features (10):**
| # | Feature | What It Captures |
|---|---|---|
| 1 | `restaurant_avg_rating` | Overall quality signal |
| 2 | `restaurant_total_orders_30d` | Popularity |
| 3 | `restaurant_avg_prep_time` | Speed of service |
| 4 | `restaurant_cuisine_type` | Cuisine category (one-hot encoded) |
| 5 | `restaurant_avg_order_value` | Price level |
| 6 | `restaurant_operating_hours_match` | Is restaurant open now? |
| 7 | `restaurant_distance_to_user_km` | Proximity |
| 8 | `restaurant_delivery_rating` | Delivery experience quality |
| 9 | `restaurant_menu_diversity` | Number of menu categories |
| 10 | `restaurant_embedding` | 128-dim vector from collaborative filtering |

**User-Restaurant Interaction Features (10):**
| # | Feature | What It Captures |
|---|---|---|
| 1 | `has_ordered_before` | Prior relationship (binary) |
| 2 | `times_ordered_from_restaurant` | Loyalty signal |
| 3 | `days_since_last_order_from_restaurant` | Recency |
| 4 | `user_rating_for_restaurant` | Personal satisfaction |
| 5 | `cosine_similarity_user_restaurant_embedding` | Taste match score |
| 6 | `cuisine_preference_match_score` | How well restaurant cuisine matches user preference |
| 7 | `price_match_score` | Restaurant avg price vs user avg spend |
| 8 | `user_clicked_but_not_ordered` | Interest but not conversion — why? |
| 9 | `similar_users_ordered_from_restaurant` | Collaborative signal |
| 10 | `current_session_cuisine_interest` | What cuisine user is browsing right now (real-time) |

**(d) Pipeline Architecture:**

**Batch Pipeline (Nightly — Apache Airflow + Spark):**
```
All data sources → Airflow schedules → Spark processes →
  ├── Compute offline features (user profiles, restaurant stats, aggregations)
  ├── Retrain candidate generation model (update embeddings)
  ├── Retrain ranking model on new order data
  ├── Write to BigQuery (offline feature store)
  └── Update model registry (MLflow)
```

**Streaming Pipeline (Real-time — Kafka + Flink):**
```
User app events → Kafka → Flink →
  ├── Compute online features (current browsing session, live restaurant status)
  ├── Update Redis (online feature store)
  └── Trigger real-time re-ranking when user context changes
```

**(e) Feature Store Design (Feast):**

| Feature | Store | Computed | Latency |
|---|---|---|---|
| `user_order_count_30d`, `user_avg_order_value` | **Offline** | Nightly batch | Hours |
| `restaurant_avg_rating`, `restaurant_total_orders_30d` | **Offline** | Nightly batch | Hours |
| `user_embedding`, `restaurant_embedding` | **Offline** | Nightly (model retrain) | Hours |
| `cosine_similarity`, `has_ordered_before` | **Offline** | Nightly batch | Hours |
| `current_restaurant_queue_length` | **Online** | Streaming (Flink) | <1ms |
| `current_session_cuisine_interest` | **Online** | Streaming | <1ms |
| `is_raining_now` | **Online** | Weather API stream | <1ms |
| `restaurant_is_currently_open` | **Online** | Streaming | <1ms |

**(f) Two-Stage Recommendation Architecture:**

```
User opens app
    ↓
Stage 1: CANDIDATE GENERATION
    - Input: user_embedding + location (nearby restaurants)
    - ANN search: find 200 restaurants whose embeddings are closest to user
    - Filter: remove closed, too far (>7km), user-blacklisted
    - Output: ~150 candidate restaurants
    - Latency: <10ms
    ↓
Stage 2: RANKING
    - Input: 150 candidates + full feature vectors (all 30 features)
    - Model: LightGBM/neural network predicts P(user will order)
    - Output: Top 25 restaurants sorted by predicted order probability
    - Latency: <50ms
    ↓
Business Rules:
    - Ensure restaurant diversity (not all same cuisine)
    - Boost restaurants with promotions/discounts
    - Apply Swiggy's commission/business logic
    ↓
Display: 25 restaurants on user's homepage
    Total: <100ms end-to-end
```

**(g) Monitoring:**

| Metric | Alert Threshold |
|---|---|
| **CTR** (click-through rate) | Drops >10% week-over-week |
| **Conversion rate** (order rate) | Drops >5% |
| **NDCG** (ranking quality) | Below 0.7 |
| **Coverage** (% restaurants recommended) | Below 60% |
| **Data drift** (feature distributions) | PSI > 0.2 on key features |
| **Latency p95** | > 100ms |
| **Feature freshness** | Online features stale >5 min |
| **A/B test significance** | New model must beat old with p<0.05 |

**(h) Real-Time Recommendation Updates:**
1. User opens app → sees initial recommendations based on offline features + location
2. User searches "biryani" → streaming pipeline captures this as `current_session_cuisine_interest = biryani`
3. Online feature store (Redis) updated in <100ms
4. Next scroll/refresh → re-ranking triggered with updated features → biryani restaurants boosted to top
5. User browses a restaurant but doesn't order → `user_clicked_but_not_ordered` updated → system shows similar but higher-rated restaurants
6. Continuous feedback loop: every interaction updates online features → recommendations adapt within the same session

**Q64.** Compare the following pairs. For each pair, explain both concepts, their differences, and when to use which:

(a) ETL vs ELT *[3 marks]*
(b) Data Lake vs Data Warehouse *[3 marks]*
(c) Batch Processing vs Streaming Processing *[3 marks]*
(d) Offline Features vs Online Features *[3 marks]*
(e) Collaborative Filtering vs Content-Based Filtering *[3 marks]*

**Answer:**

**(a) ETL vs ELT:**

| Aspect | ETL | ELT |
|---|---|---|
| **What** | Extract → Transform → Load. Data is cleaned/transformed on a separate server *before* loading into the warehouse. | Extract → Load → Transform. Raw data loaded into lake/warehouse *first*, then transformed inside it. |
| **Raw data** | Not retained (only transformed data stored) | Retained (raw data preserved for future use) |
| **Flexibility** | Low — must re-extract to try new transformations | High — raw data available for new transformations anytime |
| **When to use ETL** | Regulated industries (on-premise), small data, traditional BI |
| **When to use ELT** | Cloud environments, big data, AI/ML (need raw data for experimentation) |
| **Tools** | Informatica, Talend | dbt + BigQuery, Spark + S3 |

**(b) Data Lake vs Data Warehouse:**

| Aspect | Data Lake | Data Warehouse |
|---|---|---|
| **What** | Storage for any data type (structured + unstructured) with schema-on-read | Optimized storage for structured data with schema-on-write |
| **Data types** | Any (JSON, images, logs, CSVs, videos) | Structured only (tables with defined schema) |
| **Cost** | Very low (object storage like S3) | High (compute-optimized, indexed) |
| **Query speed** | Slow (not optimized for queries) | Very fast (columnar, indexed, partitioned) |
| **When to use Lake** | ML/AI training data, raw data archive, experimentation |
| **When to use Warehouse** | BI dashboards, analytics reports, business metrics |
| **Risk** | Can become "data swamp" without governance | Rigid — hard to adapt to new data types |

**(c) Batch Processing vs Streaming Processing:**

| Aspect | Batch Processing | Streaming Processing |
|---|---|---|
| **What** | Processes data in scheduled chunks (hourly/daily) | Processes each event as it arrives (continuous) |
| **Latency** | Minutes to hours | Milliseconds to seconds |
| **Complexity** | Simpler (well-understood patterns, easier debugging) | More complex (ordering, exactly-once delivery, state management) |
| **Cost** | Lower (process in bulk, use spot instances) | Higher (always-on infrastructure) |
| **When to use Batch** | Model training, daily reports, feature aggregations (user_avg_spend_30d) |
| **When to use Streaming** | Fraud detection (<100ms), real-time recommendations, live dashboards |
| **In practice** | Most companies use **both** (Lambda Architecture) |

**(d) Offline Features vs Online Features:**

| Aspect | Offline Features | Online Features |
|---|---|---|
| **What** | Features computed in batch from historical data | Features computed in real-time from live events |
| **When computed** | Nightly/hourly batch jobs | As events happen (streaming) |
| **What they capture** | Long-term patterns (avg spend over 30 days) | Current state (items in cart right now) |
| **Latency** | Minutes–hours acceptable | Must be <1-10ms |
| **Storage** | Data warehouse (BigQuery) | Key-value store (Redis) |
| **When to use Offline** | Model training, stable user profiles, historical aggregations |
| **When to use Online** | Real-time serving, session context, dynamic signals |
| **Example** | Swiggy: restaurant_avg_prep_time_30d | Swiggy: current_active_riders_nearby |

**(e) Collaborative Filtering vs Content-Based Filtering:**

| Aspect | Collaborative Filtering | Content-Based Filtering |
|---|---|---|
| **What** | "People like you liked X" — recommends based on similar users' behavior | "Items similar to what you liked" — recommends based on item features |
| **Data needed** | User-item interaction matrix (views, clicks, purchases) | Item attributes (genre, price, description) |
| **Strength** | Discovers unexpected items (serendipity); no need for item metadata | Works for new items (has features); no need for other users' data |
| **Weakness** | **Cold start problem** — can't recommend to new users with no history | **Filter bubble** — only recommends more of the same (no discovery) |
| **When to use Collaborative** | Mature platform with lots of user history (Netflix, Amazon) |
| **When to use Content-Based** | New platform, new items, or when item features are rich |
| **In practice** | Most production systems use **Hybrid** (combines both for best results) |

**Q65.** A healthcare startup wants to build an AI system that analyses chest X-rays to detect pneumonia. Answer:

(a) What type of AI system is this? What AI technique (CNN, NLP, etc.) would you use?
(b) What data do you need? What are the data quality concerns?
(c) Should you build from scratch, use API, or fine-tune open-source? Justify.
(d) What data governance challenges exist? (Patient privacy, consent, HIPAA/DPDP Act)
(e) Design the 5-layer architecture for this system
(f) What monitoring metrics are critical? (Consider: this is a safety-critical application)
(g) What could go wrong if the model has data drift? Give a concrete scenario.
*[10 marks]*

**Answer:**

**(a) Type and Technique:**
- **Category:** **Computer Vision — Image Classification** (binary: pneumonia vs normal, or multi-class: normal/bacterial pneumonia/viral pneumonia)
- **Technique:** **Convolutional Neural Network (CNN)** — the standard deep learning architecture for image analysis
  - CNNs learn hierarchical features: edges → textures → shapes → lung patterns → pneumonia indicators
  - Specific architecture: ResNet-50 or DenseNet-121 (proven for medical imaging)
  - This is also a **Predictive AI** system (classifying X-rays)

**(b) Data Requirements and Quality Concerns:**

**Data needed:**
| Data | Source | Purpose |
|---|---|---|
| Labeled chest X-rays | Hospital PACS systems, public datasets (CheXpert, NIH ChestX-ray14) | Training and validation |
| Radiologist annotations | Expert labeling | Ground truth labels |
| Patient metadata | Hospital records | Age, gender (for bias checking) |
| Clinical notes | EHR systems | Correlate imaging with clinical outcomes |

**Data quality concerns:**
| Concern | Impact |
|---|---|
| **Class imbalance** — healthy X-rays far outnumber pneumonia | Model biased toward "normal" prediction |
| **Label noise** — radiologists disagree on ~15-20% of X-rays | Wrong labels degrade model accuracy; need multiple annotators |
| **Selection bias** — training data from one hospital/demographics | Model fails on different patient populations, X-ray machines, imaging protocols |
| **Image quality variation** — different X-ray machines, exposure levels | Model overfits to machine artifacts instead of disease patterns |
| **Rare conditions** — atypical pneumonia presentations | Model misses unusual cases with highest mortality |

**(c) Build vs API vs Fine-tune → Fine-tune Open-Source:**

| Option | Verdict | Reason |
|---|---|---|
| **API** | **No** | No reliable medical imaging API exists for clinical use. Patient X-rays cannot be sent to third-party servers (HIPAA/DPDP). No regulatory approval for API-based clinical decisions. |
| **Build from scratch** | **No** (for most startups) | Training a CNN from scratch needs millions of labeled X-rays, massive GPU compute, and a large research team. Startup lacks these resources. |
| **Fine-tune open-source** | **Yes** | Fine-tune a pre-trained CNN (e.g., DenseNet-121 pre-trained on ImageNet or CheXpert) on the hospital's own labeled X-rays. Transfer learning means 10K-50K labeled images may suffice instead of millions. Data stays on hospital servers. Full model control for regulatory approval. |

**(d) Data Governance Challenges:**

| Challenge | Details |
|---|---|
| **Patient privacy** | X-rays are Protected Health Information (PHI). Must be de-identified before ML use — remove patient names, IDs from DICOM metadata. |
| **Consent for ML** | Patients consented to treatment, not to ML training. Need separate consent for using their data in AI development (DPDP Act requirement). |
| **HIPAA (US) / DPDP Act (India)** | Data must be stored within regulated jurisdictions; access controlled; audit trails maintained; breach notification required. Max penalty: ₹250 crore (DPDP) |
| **Right to erasure** | Patient can request deletion of their data — model may need retraining after removal. |
| **Bias auditing** | Must verify model performs equally across demographics (age, gender, ethnicity). A model that works well on young adults but fails on elderly patients is dangerous. |
| **Regulatory approval** | Medical AI needs FDA 510(k) (US) or CE marking (EU) or CDSCO approval (India). Requires clinical validation studies. |
| **Explainability** | Doctors need to understand *why* the model flagged an X-ray — use GradCAM/attention maps to highlight suspicious regions. |

**(e) 5-Layer Architecture:**

| Layer | Components | Tools |
|---|---|---|
| **Data Layer** | Secure X-ray storage (DICOM format), data ingestion from hospital PACS, de-identification pipeline, data quality checks, labeled dataset management | Hospital PACS → DICOM server → S3 (encrypted), Great Expectations for quality |
| **Model Layer** | DenseNet-121 fine-tuned on hospital X-rays; experiment tracking; model versioning; GradCAM for explainability | PyTorch, MLflow, DVC for data versioning |
| **Application Layer** | Radiologist-facing web app showing X-ray + AI prediction + confidence score + heatmap highlighting suspicious regions. **Human-in-the-loop** — AI flags, doctor decides. | FastAPI backend, React frontend with DICOM viewer |
| **Infrastructure Layer** | GPU servers for training (on-premise or private cloud for data sovereignty); model serving for inference; encrypted storage | NVIDIA DGX (training), Triton Inference Server, Docker, Kubernetes |
| **Monitoring Layer** | Continuous accuracy tracking against radiologist ground truth; data drift detection on image quality metrics; alert if sensitivity drops; bias monitoring across demographics | Prometheus, Grafana, custom clinical dashboard |

**(f) Critical Monitoring Metrics (Safety-Critical):**

| Metric | Why Critical | Alert Threshold |
|---|---|---|
| **Sensitivity (Recall)** | Missing a pneumonia case (false negative) can be fatal | Sensitivity drops below 95% |
| **Specificity** | False positives cause unnecessary treatment | Specificity drops below 90% |
| **Confidence calibration** | Model should be uncertain when unsure, not confidently wrong | Calibration error > 0.1 |
| **Subgroup performance** | Model accuracy across age groups, genders | Any subgroup accuracy < 85% |
| **Image quality drift** | New X-ray machine produces different image characteristics | PSI > 0.2 on image feature distributions |
| **Throughput / latency** | Doctors shouldn't wait — analysis should take seconds | Inference > 5 seconds |
| **Radiologist agreement rate** | How often doctors agree with AI (proxy for real-world usefulness) | Agreement drops below 80% |
| **Volume of "uncertain" predictions** | Model increasingly unsure = potential drift | Uncertain predictions > 15% |

**(g) Data Drift Scenario:**

**Concrete scenario:** The hospital upgrades its X-ray machines from Brand A to Brand B.
- Brand B produces slightly different image contrast, resolution, and positioning
- The model was trained entirely on Brand A images
- **What goes wrong:**
  - Image feature distributions shift (data drift) — pixel intensity histograms, edge patterns change
  - Model's sensitivity drops from 95% → 72% because it learned Brand A-specific artifacts as disease indicators
  - **No errors are raised** — model still produces predictions, just increasingly wrong ones
  - False negatives increase — pneumonia cases get missed
  - A patient with pneumonia receives a "normal" reading, gets discharged, condition worsens
- **How to prevent:**
  - Monitor image feature distributions for drift
  - Validate model on new machine images before clinical deployment
  - Retrain/fine-tune with mixed dataset from both machine brands
  - **Always have human-in-the-loop** for safety-critical medical AI

---

## Section D: True/False with Justification (1-2 marks each)

State whether the following are True or False. Justify your answer in one sentence.

**Q66.** Deep Learning always performs better than traditional Machine Learning.

**Answer:** False. DL needs large data and compute. For small structured datasets, traditional ML (XGBoost, Random Forest) often outperforms DL.

**Q67.** A foundation model can only be used for the task it was specifically trained for.

**Answer:** False. Foundation models are general-purpose — they can be adapted to many tasks via fine-tuning or prompt engineering.

**Q68.** ETL is more flexible than ELT because transformations happen before loading.

**Answer:** False. ELT is more flexible because raw data is preserved in the lake, allowing new transformations anytime without re-extracting.

**Q69.** In a recommender system, collaborative filtering can recommend items to brand-new users with no history.

**Answer:** False. Collaborative filtering suffers from the cold start problem — it needs user history to find similar users.

**Q70.** A data lake with no governance is better than a data warehouse because it stores more data types.

**Answer:** False. A data lake without governance becomes a "data swamp" — data is dumped but nobody knows what's there or if it's accurate.

**Q71.** Feature selection always improves model accuracy.

**Answer:** False. Removing important features hurts accuracy. Feature selection improves accuracy only when removing irrelevant/noisy features that cause overfitting.

**Q72.** The online store in a feature store contains all historical feature values.

**Answer:** False. The online store contains only the latest values for fast serving. The offline store contains historical values for training.

**Q73.** Streaming processing is always better than batch processing for ML pipelines.

**Answer:** False. Streaming is more complex and expensive. Batch is sufficient for model training (nightly retraining is fine for most use cases).

**Q74.** RAG eliminates hallucinations completely.

**Answer:** False. RAG reduces hallucinations by grounding answers in documents, but the LLM can still misinterpret or incorrectly summarise the retrieved content.

**Q75.** A model with 98% accuracy on imbalanced data (2% positive class) is a good model.

**Answer:** False. A model predicting "negative" for everything achieves 98% accuracy but catches zero positive cases. Need to check precision, recall, and F1 score.

---

## Section E: Quick-Fire Matching (1 mark each)

**Q76.** Match each tool with its primary function:

| Tool | Function |
|---|---|
| 1. Apache Kafka | A. Batch data processing |
| 2. Apache Spark | B. Real-time event streaming |
| 3. Apache Airflow | C. Feature store (open-source) |
| 4. Feast | D. Experiment tracking |
| 5. MLflow | E. Pipeline orchestration/scheduling |
| 6. Kubernetes | F. Container orchestration |
| 7. Redis | G. In-memory key-value store (online features) |
| 8. dbt | H. Data transformation in warehouse |

**Answers:** 1-B, 2-A, 3-E, 4-C, 5-D, 6-F, 7-G, 8-H

**Q77.** Match each concept with its definition:

| Concept | Definition |
|---|---|
| 1. Data Drift | A. Features differ between training and production |
| 2. Training-Serving Skew | B. Creating new features from raw data |
| 3. Feature Engineering | C. Live data distribution differs from training data |
| 4. Cold Start Problem | D. Compact numerical representation capturing meaning |
| 5. Embedding | E. Can't recommend to new users with no history |
| 6. Lambda Architecture | F. AI generates confident but wrong information |
| 7. Hallucination | G. Using both batch and streaming pipelines |
| 8. RLHF | H. Training AI using human feedback ratings |

**Answers:** 1-C, 2-A, 3-B, 4-E, 5-D, 6-G, 7-F, 8-H

**Q78.** Match each company example with the AI application:

| Company | AI Application |
|---|---|
| 1. Waymo | A. Credit card fraud detection |
| 2. Netflix | B. Personalised music playlists |
| 3. HDFC Bank | C. Self-driving robotaxis |
| 4. Spotify | D. Content recommendations + personalised thumbnails |
| 5. Zillow | E. Delivery time prediction |
| 6. Swiggy | F. AI home price prediction ($881M loss) |

**Answers:** 1-C, 2-D, 3-A, 4-B, 5-F, 6-E

---

## Exam Preparation Tips

1. **Know your case studies:** ChatGPT (Session 2), Netflix (Session 2), Waymo (Session 2), Zillow (Session 3), Cambridge Analytica (Session 5), Myntra pipeline (Session 6), Spotify Discover Weekly (Session 8)

2. **Know your comparisons:** AI vs ML vs DL, ETL vs ELT, Batch vs Streaming, Data Lake vs Warehouse vs Lakehouse, Offline vs Online features, Collaborative vs Content-based filtering, Build vs Buy vs Fine-tune

3. **Know your architectures:** 5-layer AI architecture, Lambda Architecture, Two-stage recommendation architecture, Feature store architecture

4. **Know your tools:** Kafka, Spark, Airflow, Flink, Feast, MLflow, Kubernetes, Redis, Hugging Face, dbt

5. **Know your numbers:** GPT-4 training ~$100M, Netflix 80% from recommendations, Amazon 35% revenue from recommendations, Zillow $881M loss, data quality = 80% of ML project time, model = 5% of AI system code

6. **For design questions:** Always structure your answer using the 5 layers (Data → Model → Application → Infrastructure → Monitoring). This shows systematic thinking.

---

*End of Exam-Style Questions*
