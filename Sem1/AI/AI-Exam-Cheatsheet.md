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

## 11. AI SYSTEM LIFECYCLE — 6 Stages Details

> Why circular? Models degrade over time due to **data drift** (input distribution changes) and **concept drift** (input→output relationship changes). You must iterate continuously.

### Stage 1: Problem Definition
- **What happens:** Translate a business problem into an ML problem. Define what success looks like.
- **Key activities:** Identify stakeholders, define success metric (F1, revenue lift, latency target), determine if ML is even needed.
- **Common pitfall:** Solving the wrong problem — building a sophisticated model when a simple rule-based system would suffice.

### Stage 2: Data Collection & Preparation
- **What happens:** Gather, clean, label, and split the data needed for training.
- **Key activities:** Source internal/external data, handle missing values, remove duplicates, label data for supervised learning, split into train/validation/test.
- **Common pitfall:** Poor data quality or bias in training data → biased model (Amazon hiring AI trained on male-dominated data).

### Stage 3: Model Development
- **What happens:** Select algorithms, engineer features, train models, tune hyperparameters, evaluate.
- **Key activities:** Feature engineering, algorithm selection (start simple → complex), hyperparameter tuning, cross-validation, offline evaluation.
- **Common pitfall:** Overfitting — model memorizes training data, performs great in training but fails on new data.

### Stage 4: Deployment
- **What happens:** Put the trained model into production where it serves real predictions.
- **Key activities:** Choose deployment mode (API/batch/edge), set up CI/CD pipeline, QA → Staging → UAT → Production.
- **Common pitfall:** Latency issues, training-serving skew (features computed differently in training vs production).

### Stage 5: Monitoring
- **What happens:** Continuously track model performance, data quality, and system health in production.
- **Key activities:** Monitor accuracy, data drift (KS test, PSI), concept drift, latency (p50/p95/p99), business metrics.
- **Common pitfall:** No monitoring → silent degradation. Zillow lost $881M because models degraded during COVID and nobody caught it.

### Stage 6: Iteration
- **What happens:** Retrain the model with new data, fix issues, improve performance.
- **Key activities:** Collect new labeled data, retrain on schedule or on trigger, A/B test new vs old model, update features.
- **Common pitfall:** Not closing the feedback loop — deploying once and never updating.

---

## 12. DATA ENGINEERING — Steps & Details

| Step | Description | Tools / Details |
|---|---|---|
| **Data Ingestion** | Bring data into the system | Batch: Spark, HDFS. Streaming: Kafka, Kinesis. May include synthetic data generation. |
| **Data Exploration** | Understand what you have | Profiling: min/max/avg/nulls. EDA with visualization: histograms, scatter plots, heatmaps. |
| **Data Validation** | Check data meets expectations | Schema checks, range checks, null checks, distribution checks. Tools: **Deequ**, **TFDV** (TensorFlow Data Validation). |
| **Data Wrangling** | Re-format and clean | Re-formatting, cleaning, missing value imputation, type conversion. |
| **Data Labelling** | Assign categories for supervised learning | Human annotators or crowd-sourcing. Tools: **Label Studio**, **Appen**. |
| **Data Splitting** | Divide into train/val/test | Train (70-80%) / Validation (10-15%) / Test (10-15%). **Never leak test data!** |

---

## 13. DATA PREPROCESSING — 8 Strategies

| Strategy | What | When |
|---|---|---|
| **Clean** | Remove outliers, duplicates, impute missing values | Always — first step in any pipeline |
| **Balance** | Ensure equal class representation (oversample minority / undersample majority) | Imbalanced data (e.g., 99% normal vs 1% fraud) |
| **Replace** | Substitute invalid or corrupted values | Encoding errors, corrupted data, placeholder values |
| **Impute** | Fill missing values using mean/median/mode/model-based methods | Missing data — choose method based on data type and distribution |
| **Partition** | Split into train/validation/test sets | Always — required for unbiased evaluation |
| **Scale** | Normalize (0-1) or Standardize (mean=0, std=1) | When features have different scales (age vs income) |
| **Augment** | Create synthetic examples (rotate/flip images, paraphrase text) | Insufficient training data |
| **Unbias** | Detect and mitigate bias in data | Fairness-critical applications (hiring, lending, healthcare) |

> **Memory trick:** "**C**lean, **B**alance, **R**eplace, **I**mpute, **P**artition, **S**cale, **A**ugment, **U**nbias" → **"Can Bees Really Improve Pollen Sorting And Utilization?"**

---

## 14. MODEL DEVELOPMENT — Key Concepts

### Algorithm Selection
Start simple → moderate → complex. Only increase complexity if simpler models don't meet the success metric.
- **Simple:** Logistic Regression, Decision Tree
- **Moderate:** Random Forest, XGBoost, SVM
- **Complex:** Neural Networks, Transformers

### Hyperparameters
Parameters set BEFORE training (not learned from data):
- **Learning rate** — step size for gradient descent
- **Epochs** — number of full passes through training data
- **Batch size** — samples processed before updating weights
- **Max depth** — tree depth (Random Forest, XGBoost)
- **Regularization** — penalty to prevent overfitting (L1/L2)

### Overfitting vs Underfitting

| Problem | Symptom | Fix |
|---|---|---|
| **Overfitting** | High training accuracy, low test accuracy | More data, regularization, dropout, cross-validation, simpler model |
| **Underfitting** | Low accuracy on both training and test | More features, more complex model, longer training, less regularization |

### Evaluation Strategy
- **Offline:** Hold-out test set, k-fold cross-validation
- **Online:** A/B testing, canary deployment (5% traffic → 50% → 100%), shadow deployment (run in parallel, compare)

### Validation Checks
Generalization (works on unseen data), Fairness (no bias across groups), Robustness (handles edge cases and adversarial inputs)

### Model Selection — 6 Criteria
1. **Accuracy** — meets the success metric
2. **Latency** — fast enough for the use case
3. **Size** — fits deployment target (edge vs cloud)
4. **Maintenance cost** — team can maintain and retrain
5. **Explainability** — can you explain predictions? (required in regulated industries)
6. **Business alignment** — solves the actual business problem

---

## 15. DEPLOYMENT — Options & Details

| Option | How | When to Use |
|---|---|---|
| **Real-time endpoint (API)** | Model serves one prediction per request via REST/gRPC | Fraud detection, chatbots, search ranking — need instant response |
| **Batch transform** | Model processes entire dataset at once on a schedule | Monthly credit scoring, nightly reports — latency tolerance |
| **Edge deployment** | Model runs on device (phone, IoT sensor, camera) | Offline scenarios, ultra-low latency, privacy-sensitive data |

### Deployment Pipeline
**QA → Staging → UAT → Production**

### Retraining Strategy
- **Scheduled:** Daily/weekly via CI/CD pipeline (most common)
- **Event-based:** New data arrival or drift detection triggers retraining

### Rollback Strategy
If accuracy drops >10%, **auto-revert to previous model version**. Always keep the last known-good model available.

---

## 16. MONITORING — What & Why

| What to Monitor | Why | Metric / Tool |
|---|---|---|
| **Model accuracy** | Models degrade over time as world changes | F1, AUC, MAE — tracked weekly |
| **Data drift** | Input data distribution shifts from training data | KS test, PSI (Population Stability Index) |
| **Concept drift** | The relationship between input and output changes | Accuracy on recent ground truth labels |
| **System health** | Latency spikes and errors hurt user experience | p50/p95/p99 latency, error rate |
| **Business metrics** | Does the AI actually help the business? | Revenue, CTR, conversion, retention |

> **Memory trick:** "Monitor **DMASB** — **D**ata drift, **M**odel accuracy, **A** (concept drift = relationship changes), **S**ystem health, **B**usiness metrics"

---

## 17. FEATURE ENGINEERING — Advantages & Disadvantages

### Advantages
- **Biggest impact on accuracy** — more impactful than model selection. Good features make simple models powerful.
- **Captures domain knowledge** — encodes expert understanding into the model (e.g., "days since last purchase" for churn prediction).
- **Makes simple models powerful** — a well-engineered feature set with logistic regression can beat a neural network with raw features.

### Disadvantages
- **Time-consuming** — typically **80% of ML project time** is spent on data and feature work.
- **Requires domain expertise** — need to understand the business to create meaningful features.
- **Risk of data leakage** — if done incorrectly, future information leaks into training features, giving unrealistically high accuracy that fails in production.

---

## 18. RECOMMENDER SYSTEMS — Detailed Comparison

### Collaborative Filtering
- **How:** "Users like you liked X" — finds similar users or similar items based on interaction patterns.
- **Advantages:** Discovers unexpected/serendipitous items. No item metadata needed.
- **Disadvantages:** Cold start (can't recommend for new users/items with no history), popularity bias, sparsity (most users rate very few items).

### Content-Based Filtering
- **How:** "Items similar to what you liked" — uses item features (genre, tags, description) to find matches.
- **Advantages:** No cold start for new items (just need item features). Transparent recommendations (can explain why).
- **Disadvantages:** Filter bubble (keeps recommending same type of content). Needs good item feature engineering.

### Hybrid Approach
- **How:** Combines collaborative and content-based signals.
- **Advantages:** Best accuracy overall. Overcomes individual weaknesses of each approach.
- **Disadvantages:** More complex to build and maintain.

### Two-Stage Architecture (Production Systems)
1. **Candidate Generation** (fast, approximate) — ANN search over millions of items → narrows to ~1000 candidates
2. **Ranking** (accurate, full features) — applies full feature set and complex model → selects top ~20 items

> **Why 2 stages?** You can't run an expensive ranking model on millions of items within <100ms latency budget. Candidate generation acts as a fast filter.

---

## 19. GENERATIVE AI — Key Details

### How LLMs Work
- Predict the **next token**, one at a time (autoregressive generation).
- Trained on internet-scale text data (books, websites, code).
- Learn patterns and relationships, not facts — this is why they hallucinate.

### Key Concepts

| Concept | Details |
|---|---|
| **Hallucination** | Model generates confident but factually wrong output. LLMs optimize for fluency, not truth. **Fix:** RAG (ground responses in retrieved documents). |
| **Temperature** | Controls randomness. **0 = deterministic/safe** (best for factual tasks). **1 = creative/risky** (best for brainstorming, writing). |
| **RLHF** | Reinforcement Learning from Human Feedback. Human evaluators rate responses → train a reward model → fine-tune LLM to maximize reward. Makes models helpful, harmless, honest. |
| **Context window** | How much text the model can "see" at once. GPT-4: 128K tokens. Claude: 200K tokens. Longer = more expensive. |
| **RAG** | Retrieval-Augmented Generation. Search documents first → feed relevant chunks to LLM → grounded, factual answer. No model retraining needed. |

### Advantages
- Content creation at scale (marketing, reports, summaries)
- Code generation and debugging
- Translation and localization
- Summarization of long documents

### Disadvantages
- **Hallucinations** — generates plausible but false information
- **Bias** — reflects biases in training data
- **Data privacy risk** — sensitive data may be sent to API providers
- **Copyright concerns** — trained on copyrighted content, legal status unclear
- **Expensive inference** — large models cost significant compute per request

---

## 20. EXPLAINABLE AI (XAI) — Details

### SHAP (SHapley Additive exPlanations)
- Based on **game theory** (Shapley values).
- Shows each feature's **contribution score** — how much each feature pushes the prediction higher or lower.
- Works for **any model** (model-agnostic).
- Good for **global** (overall feature importance) and **local** (single prediction) explanations.

### LIME (Local Interpretable Model-agnostic Explanations)
- Explains **ONE prediction** at a time.
- Creates a **simple local model** (e.g., linear regression) around the specific data point.
- Perturbs the input and observes how predictions change.
- Good for debugging individual predictions.

### Interpretability vs Performance Trade-off

| Model Type | Interpretability | Performance |
|---|---|---|
| Linear Regression, Decision Tree | **High** — can read and explain rules | **Lower** — limited complexity |
| Random Forest, XGBoost | **Medium** — feature importance available | **Good** — handles non-linearity |
| Neural Networks, Deep Learning | **Low** — black box | **Highest** — captures complex patterns |

### When is XAI Needed?
- **Regulated industries** — banking (loan decisions), healthcare (diagnoses), insurance (claims)
- **Debugging** — understanding why a model made a wrong prediction
- **Fairness auditing** — checking if the model discriminates against protected groups
- **Building trust** — stakeholders need to understand and trust AI decisions

---

*Good luck with your exam!* 🎯
