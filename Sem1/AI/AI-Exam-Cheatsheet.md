# AI Systems — Exam Cheatsheet (Quick Reference Before Exam)

> Scan this 30 minutes before the exam. All decisions, classifications, and key facts.

---

## 1. WHEN TO PICK WHAT — AI Category

| Business Problem | Pick | Why |
|---|---|---|
| Predict a number from historical data (house price, delivery time) | **Predictive AI — Regression** | Learn input→output mapping from past data. Supervised learning. XGBoost, Linear Regression. |
| Predict a category (spam/not-spam, fraud/legit, churn/no-churn) | **Predictive AI — Classification** | Binary or multi-class labels. Logistic Regression, Random Forest, XGBoost. |
| Detect unusual patterns (fraud, anomaly, intrusion) | **Predictive AI — Anomaly Detection** | Flag outliers that deviate from learned "normal" pattern. Isolation Forest, Autoencoders. |
| Generate new text, images, code, music | **Generative AI** | LLMs (next-token prediction), Diffusion Models (noise→image). ChatGPT, DALL-E, Stable Diffusion. |
| Show users items they'll like (movies, products, songs) | **Recommender System** | Collaborative filtering (similar users), Content-based (similar items), or Hybrid. Netflix, Amazon, Spotify. |
| Let users ask questions in natural language | **Conversational AI** | NLU + Dialog Manager + LLM/RAG. Chatbots, virtual assistants. HDFC Eva, ChatGPT. |
| Understand images/video (detect, classify, segment) | **Computer Vision** | CNNs for classification/detection/segmentation. Google Lens, self-driving cars, medical imaging. |
| Physical-world autonomous action (drive, fly, pick) | **Autonomous Systems** | Sense→Think→Act loop. Cameras+LiDAR→AI→Steer/Brake. Waymo, drones, warehouse robots. |

---

## 2. WHEN TO PICK WHAT — Model Approach

| Scenario | Pick | Why |
|---|---|---|
| Quick prototype, generic task, small team | **API (GPT-4, Claude)** | Hours to deploy. Zero upfront cost. No ML engineers needed. Trade-off: data sent to provider, per-token cost at scale. |
| Domain-specific needs, data privacy required | **Fine-tune Open-Source (LLaMA, Mistral)** | Data stays on your servers. Customize for your domain. 2-5 ML engineers needed. Trade-off: GPU compute cost, maintenance. |
| Core competitive advantage, massive proprietary data | **Build from Scratch** | Maximum control. Full customization. 10-50 researchers. Trade-off: months-years, very expensive. **Only Google, Tesla, etc. do this.** |
| Need factual answers from company documents | **RAG (Retrieval-Augmented Generation)** | Search docs first → feed to LLM → grounded answer. Reduces hallucination. No model training needed. |
| No training data, just need better prompts | **Prompt Engineering** | Write clear instructions. Zero cost beyond API. Minutes to deploy. Start here, escalate to RAG/fine-tune if insufficient. |

---

## 3. WHEN TO PICK WHAT — Data & Feature Decisions

| Scenario | Pick | Why |
|---|---|---|
| Transform before loading | **ETL** | Traditional, on-premise, regulated. Only clean data in warehouse. Less flexible. |
| Load raw, transform later | **ELT** | Modern, cloud, AI/ML. Keep raw data for future features. More flexible. |
| Scheduled bulk processing | **Batch** | Reports, model training, daily aggregations. Cheaper, simpler. |
| Real-time event processing | **Streaming** | Fraud detection, real-time recommendations. Faster but complex + expensive. |
| Both batch + streaming | **Lambda Architecture** | Most production systems. Batch for accuracy, streaming for speed. |
| Store any data cheaply | **Data Lake** | Raw data archive, ML training data. Schema-on-read. S3/GCS. |
| Fast structured queries | **Data Warehouse** | BI dashboards, reports. Schema-on-write. BigQuery, Snowflake. |
| Serve ML features consistently | **Feature Store** | Prevents training-serving skew. Single source of truth. Feast, Tecton. |

---

## 4. CLASSIFICATIONS & CATEGORIES

### AI vs ML vs DL

| | AI | ML | DL |
|---|---|---|---|
| What | Any intelligent system | Learns from data | Deep neural networks |
| Data needed | Little/none | Thousands-millions | Millions-billions |
| Feature engineering | Manual | Manual | Automatic |
| Compute | Low | Medium | High (GPU/TPU) |
| Example | Rule-based chess | Spam filter | ChatGPT, image recognition |

### 6 AI Categories

| Category | Input | Output | Technique | Example |
|---|---|---|---|---|
| Predictive | Structured data | Number/label | Supervised ML | Delivery time prediction |
| Generative | Text prompt | New content | LLMs, Diffusion | ChatGPT, DALL-E |
| Recommender | User behavior | Ranked items | CF/CB/Hybrid | Netflix, Amazon |
| Conversational | Natural language | NL response | NLU+LLM+RAG | Chatbots |
| Computer Vision | Images/video | Labels/boxes | CNNs | Google Lens |
| Autonomous | Sensor data | Physical actions | Sensor fusion | Waymo |

### 4 Types of Prediction

| Type | Output | Example |
|---|---|---|
| Classification | Category (spam/not-spam) | Email filtering |
| Regression | Number (₹85 lakhs) | House price prediction |
| Time-series | Future values | Stock price forecasting |
| Anomaly Detection | Normal/Anomalous | Fraud detection |

### Recommender Types

| Type | How | Strength | Weakness |
|---|---|---|---|
| Collaborative | "Users like you liked X" | Discovers unexpected items | Cold start (new users) |
| Content-Based | "Items similar to what you liked" | Works for new items | Filter bubble (same type) |
| Hybrid | Combines both | Best accuracy | More complex |

### SAE Levels (Autonomous Vehicles)

| Level | Name | Example |
|---|---|---|
| 0 | No Automation | Old car |
| 1 | Driver Assistance | Adaptive cruise control |
| 2 | Partial | Tesla Autopilot |
| 3 | Conditional | Mercedes Drive Pilot |
| 4 | High (geofenced) | **Waymo robotaxis** |
| 5 | Full (everywhere) | **Does NOT exist** |

### Foundation Models

| Model | Company | Type | Key Fact |
|---|---|---|---|
| GPT-4/4o | OpenAI | Multimodal | Most capable |
| Claude 3.5/4 | Anthropic | Text+Vision | Safety + 200K context |
| Gemini | Google | Multimodal | Natively multimodal |
| LLaMA 3 | Meta | Open-source | Free, huge community |
| Mistral | Mistral AI | Open-source | Efficient for size |

### License Types

| License | Freedom | Example |
|---|---|---|
| Apache 2.0 | Most free, no restrictions | Mistral, Whisper |
| Llama License | Free < 700M MAU | LLaMA 2, LLaMA 3 |
| Commercial | Must purchase | Specialized models |

---

## 5. ARCHITECTURE — 5 Layers

| Layer | Purpose | Tools |
|---|---|---|
| **Data** | Collect, store, process, serve data | Kafka, S3, Spark, Airflow, Feast |
| **Model** | Train, evaluate, version, serve | MLflow, TF Serving, Triton |
| **Application** | User-facing, business logic, A/B testing | FastAPI, React, LaunchDarkly |
| **Infrastructure** | Compute, storage, networking, containers | Kubernetes, Docker, Terraform |
| **Monitoring** | Drift detection, alerts, dashboards | Prometheus, Grafana, Great Expectations |

---

## 6. DATA QUALITY — 6 Dimensions

| Dimension | Definition |
|---|---|
| Accuracy | Values correctly represent reality |
| Completeness | No missing values where expected |
| Consistency | Same fact same way everywhere |
| Timeliness | Data reflects current state |
| Validity | Data follows format/rules |
| Uniqueness | No unintended duplicates |

### 5 Types of Bias

| Bias | Description | Example |
|---|---|---|
| Reporting | Unusual events overreported | News reports crashes, not safe flights |
| Automation | Over-relying on AI output | Loan officer blindly follows AI recommendation |
| Selection | Training data not representative | Model trained on urban users fails for rural |
| Group Attribution | Group stats applied to individuals | "City X spends less" → penalize all from X |
| Implicit | Unconscious assumptions in data | "CEO" → images of men |

### 4 Types of Training-Serving Skew

| Type | What Goes Wrong |
|---|---|
| Feature computation | Same feature computed differently in training vs serving |
| Data distribution | Live data has different distribution than training data |
| Feature availability | Feature available in training not available at serving time |
| Time-travel | Training uses future information not available at prediction time |

---

## 7. FEATURE ENGINEERING — Quick Reference

### Transformation Techniques

| Technique | When | Example |
|---|---|---|
| Min-Max Scaling | Need [0,1] range | (x-min)/(max-min) |
| Z-score Standardization | Handle outliers | (x-mean)/std |
| Log Transform | Right-skewed data | log(income) |
| One-Hot Encoding | Nominal categories (no order) | City → [1,0,0], [0,1,0] |
| Label Encoding | Ordinal categories (has order) | Education: 1,2,3,4 |
| Binning | Non-linear relationships | Age → teen/adult/senior |

### Selection Methods

| Method | How | Speed |
|---|---|---|
| Filter | Statistical tests per feature | Fast |
| Wrapper | Train model with subsets | Slow, accurate |
| Embedded | Model learns importance (L1/XGBoost) | Good balance |

### Embeddings

| Type | Dimensions | Use |
|---|---|---|
| Word (Word2Vec) | 100-300 | NLP, search |
| Sentence (BERT) | 384-1024 | Semantic search |
| Image (ResNet) | 512-2048 | Visual similarity |
| User/Item | 64-256 | Recommendations |

---

## 8. KEY NUMBERS

| Fact | Number |
|---|---|
| Model code as % of AI system | **5%** |
| Model effort vs system effort | **20% vs 80%** |
| AI projects that fail | **85%** |
| Netflix content from recommendations | **80%** |
| Netflix savings from recs | **$1 billion/year** |
| Amazon revenue from recs | **35%** |
| Data prep time in ML project | **80%** |
| Zillow AI failure loss | **$881 million** |
| GPT-4 training cost | **~$100 million** |
| Cambridge Analytica fine | **$5 billion** |
| DPDP Act max penalty | **₹250 crore** |
| Human reaction time | **~1500ms** |
| Autonomous system reaction | **<100ms** |

---

## 9. CASE STUDIES — What to Remember

| Case | Category | Key Lesson |
|---|---|---|
| **ChatGPT** | Generative + Conversational | RLHF + safety systems. Model is <5% of the system. |
| **Netflix** | Recommender | Multiple models + personalised thumbnails. 80% from recs. |
| **Waymo** | Autonomous | Sensor fusion (camera+LiDAR+radar). <100ms decisions. Level 5 doesn't exist. |
| **Zillow** | Predictive | $881M loss from no monitoring. Data drift during COVID. |
| **Cambridge Analytica** | Governance failure | $5B fine. No access control, no audit trail, no consent. |
| **Amazon Hiring AI** | Bias | Trained on 10 years male-dominated data → discriminated against women → scrapped. |

---

## 10. TOOL MAP

| Task | Tool |
|---|---|
| Event streaming | Apache Kafka |
| Batch processing | Apache Spark |
| Pipeline orchestration | Apache Airflow |
| Stream processing | Apache Flink |
| Feature store | Feast, Tecton |
| Experiment tracking | MLflow, W&B |
| Model serving | TF Serving, Triton |
| Container orchestration | Kubernetes |
| Online feature serving | Redis |
| Data quality | Great Expectations, Deequ |
| Data transformation | dbt |
| Model marketplace | Hugging Face |

---

*Good luck with your exam!* 🎯
