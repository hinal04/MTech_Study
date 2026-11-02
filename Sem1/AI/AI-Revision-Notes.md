# AI Systems — Theory Revision Notes (Exam Ready)

> BITS Pilani — **SS ZG662: Introduction to AI Systems**
> Sessions 1–8 | Categories · Classifications · Types · Advantages · Disadvantages · Comparisons

---

## SESSION 1: FOUNDATIONS OF AI SYSTEMS

### AI vs ML vs DL — Classification

| | AI | ML | DL |
|---|---|---|---|
| **Definition** | Any system that mimics human intelligence | Subset of AI — learns patterns from data | Subset of ML — uses deep neural networks |
| **How it works** | Rules, heuristics, search, logic, OR learning | Algorithm finds rules from data automatically | Network discovers features AND rules from raw data |
| **Who writes logic?** | Human programmer | Algorithm learns from data | Network learns everything automatically |
| **Feature engineering** | Manual rules | Manual feature engineering needed | Automatic feature learning |
| **Data needed** | Little or none | Thousands–millions of examples | Millions–billions of examples |
| **Compute needed** | Low | Medium | High (GPUs/TPUs) |
| **Best when** | Rules are clear, data is scarce | Patterns exist but hard to write as rules | Data is abundant, features are complex (images, text) |
| **Example** | Chess engine, thermostat | Spam filter, fraud detection | Image recognition, ChatGPT |

### When to Use What — Decision Guide

| Situation | Approach |
|---|---|
| Rules are clear and simple | Traditional AI (rule-based) |
| Historical data available, patterns exist | Machine Learning |
| Complex data (images, text, audio) + lots of data | Deep Learning |
| Very little data available | Rule-based (not ML/DL) |
| Need explainability | Simple ML (decision trees) or rules |

### Three Levels of ML Software (Iceberg Metaphor)

| Level | What It Covers | Size |
|---|---|---|
| **Level 1: ML Code** | Model training, feature engineering | Smallest (~5%) |
| **Level 2: ML Infrastructure** | Data pipelines, serving, monitoring, feature store | Large |
| **Level 3: ML Operations (MLOps)** | CI/CD, testing, governance, team workflows | Largest |

> The model is the tip of the iceberg. Infrastructure and operations are the massive hidden base.

### Why AI Projects Fail — 85% Failure Rate

| Reason | Explanation |
|---|---|
| Wrong problem framing | Solving the wrong problem with AI |
| Poor data quality | Garbage in = garbage out |
| No clear success metric | Can't measure if AI is working |
| Trying AI where rules suffice | Over-engineering simple problems |
| Lack of MLOps infrastructure | Model works in notebook, fails in production |
| No business alignment | AI team builds what's cool, not what's needed |
| Ignoring the 80% | Focus on model, neglect pipelines/monitoring/deployment |

### Model = 20% Effort, System = 80%

```
Building the model:     20% of effort
Remaining 80%:
├── Data integration & pipelines
├── Feature engineering
├── Deployment infrastructure
├── Monitoring & alerting
├── Retraining automation
├── Business process integration
└── Governance & compliance
```

### AI System Lifecycle — 6 Stages (Circular)

| Stage | What Happens | Key Activity |
|---|---|---|
| **1. Problem Definition** | Translate business problem → ML problem | Define success metric |
| **2. Data Collection & Prep** | Gather, clean, label, split data | Handle missing values, bias |
| **3. Model Development** | Train, tune, evaluate models | Feature engineering, hyperparameter tuning |
| **4. Deployment** | Put model into production | API serving, batch/edge deployment |
| **5. Monitoring** | Track performance over time | Detect drift, degradation |
| **6. Iteration** | Improve based on feedback | Retrain with new data |

> **Why circular?** Unlike traditional software, ML models degrade over time (data drift, concept drift). Continuous retraining needed.

### 6 Components of an AI System

| Component | Purpose |
|---|---|
| **Data Pipeline** | Collect, clean, transform, store data |
| **Feature Pipeline** | Convert raw data → ML features |
| **Model Pipeline** | Train, evaluate, version, register models |
| **Serving Infrastructure** | Host model, serve predictions via API |
| **Monitoring** | Track accuracy, drift, latency, errors |
| **Application Layer** | UI/app that users interact with |

### ML Lifecycle Architecture — 9 Components

| Component | Role |
|---|---|
| **Ground-Truth Collector** | Collects correct answers for model comparison |
| **Data Labeller** | Creates labels for training data (manual/automated) |
| **Evaluator** | Measures model accuracy on test cases |
| **Performance Monitor** | Tracks metrics on production data over time |
| **Featurizer** | Computes features from raw data (batch + real-time) |
| **Orchestrator** | Coordinates the entire ML pipeline (ETL → train → deploy) |
| **Model Builder** | Trains and tunes models |
| **Model Server** | Hosts model API for predictions |
| **Front-End** | User-facing application |

### Explainable AI (XAI)

| Technique | What It Does |
|---|---|
| **SHAP** | Shows each feature's contribution to a prediction |
| **LIME** | Explains individual predictions by approximating locally |
| **Attention Visualization** | Shows which input tokens the model focused on |

| Trade-off | |
|---|---|
| High interpretability → | Lower performance (decision trees, logistic regression) |
| Low interpretability → | Higher performance (neural networks = black box) |

### Problem → Solution → ML Framing

```
Business Problem → Can clear rules solve it?
                     ├── YES → Rule-Based Solution
                     └── NO → Need to learn patterns from data?
                                  └── YES → Machine Learning
```

**Framing example:** "Reduce customer churn" → ML problem: "Predict which customers will churn in 30 days" → Binary classification

---

## SESSION 2: CATEGORIES OF AI SYSTEMS

### 6 Categories of AI Systems — Master Table

| Category | Input | Output | Technique | Example |
|---|---|---|---|---|
| **Predictive AI** | Historical structured data | Predicted number/label | Supervised ML | Swiggy delivery time prediction |
| **Generative AI** | Text prompt / seed | New text/image/code/audio | LLMs, Diffusion Models | ChatGPT writing an email |
| **Recommender System** | User behavior + item features | Ranked list of items | Collaborative/Content/Hybrid filtering | Netflix "Top Picks for You" |
| **Conversational AI** | Natural language message | Natural language response | NLU + Dialog Manager + LLMs | HDFC Bank chatbot |
| **Computer Vision** | Images / video | Labels, bounding boxes, segments | CNNs | Google Lens identifying a flower |
| **Autonomous System** | Sensor data (camera, LiDAR, radar) | Physical actions (steer, brake) | Sensor fusion + planning + control | Waymo self-driving car |

---

### PREDICTIVE AI

#### 4 Types of Prediction Tasks

| Type | Predicts | Output | Example |
|---|---|---|---|
| **Classification** | Which category? | Discrete label (spam/not-spam) | Email spam detection |
| **Regression** | What number? | Continuous value (₹85 lakhs) | House price prediction |
| **Time-series Forecasting** | What happens next? | Future values | Stock price, demand forecast |
| **Anomaly Detection** | Is this unusual? | Normal/Anomalous | Credit card fraud detection |

#### Classification vs Regression

| Aspect | Classification | Regression |
|---|---|---|
| Output | Discrete label (Yes/No, A/B/C) | Continuous number |
| Question | "Which group?" | "How much / how many?" |
| Metrics | Accuracy, Precision, Recall, F1, AUC-ROC | MAE, RMSE, R² |
| Example | Is this fraud? Will customer churn? | What will delivery time be? |

#### Predictive AI Metrics

| Model Metric | Measures |
|---|---|
| **Accuracy** | Overall correctness (correct / total) |
| **Precision** | Of predicted positives, how many were right? |
| **Recall** | Of actual positives, how many did we catch? |
| **F1-Score** | Harmonic mean of Precision and Recall |
| **AUC-ROC** | Model's ability to distinguish classes |

| Business KPI | Measures |
|---|---|
| Revenue uplift | Extra revenue from model |
| Cost saved | Money saved by automation |
| Customer retention rate | % at-risk customers retained |
| False positive cost | Cost of wrong positive predictions |

#### Uplift Modelling — 4 Groups

| Group | Buy without action? | Buy with action? | Strategy |
|---|---|---|---|
| **Persuadables** ✅ | No | Yes | TARGET these — action causes the sale |
| **Sure Things** | Yes | Yes | Don't waste coupons |
| **Lost Causes** | No | No | Don't bother |
| **Sleeping Dogs** ⚠️ | Yes | No | AVOID — action pushes them away |

#### Deployment Challenges

| Challenge | Description |
|---|---|
| **Data Drift** | Input data distribution changes over time |
| **Concept Drift** | Relationship between features and target changes |
| **Feature Availability** | Feature used in training not available at serving time |
| **Latency** | Model too slow for real-time use |
| **A/B Testing** | Need to prove new model is better than old |
| **Shadow Deployment** | Run new model silently alongside old one to compare |

---

### GENERATIVE AI

#### Types of Generative AI

| Type | Creates | Technology | Example |
|---|---|---|---|
| **Text generation** | Articles, code, emails | LLMs (predict next token) | ChatGPT, Claude, Gemini |
| **Image generation** | Photos, art, designs | Diffusion Models (noise → image) | DALL-E 3, Midjourney |
| **Code generation** | Code in any language | LLMs trained on code | GitHub Copilot |
| **Music/Audio** | Songs, voice cloning | Audio diffusion, neural codecs | Suno, ElevenLabs |
| **Video generation** | Video clips from text | Video diffusion | Sora, Runway Gen-3 |

#### Key GenAI Concepts

| Concept | Definition |
|---|---|
| **Prompt** | Instruction/question given to the AI |
| **Prompt Engineering** | Art of writing better prompts for better outputs |
| **Hallucination** | AI confidently generates WRONG information |
| **Temperature** | Controls creativity: 0 = safe/repetitive, 1 = creative/risky |
| **Fine-tuning** | Training a general model further on specific data |
| **Token** | Basic unit LLM works with (~3/4 of a word) |
| **Context Window** | How much text model can "remember" at once |
| **RLHF** | Reinforcement Learning from Human Feedback |

#### ChatGPT — 3 Steps

| Step | What Happens |
|---|---|
| **1. Pre-training** | Train on massive internet text → learns grammar, facts, reasoning |
| **2. RLHF** | Human evaluators rate responses → model fine-tuned to maximize helpfulness |
| **3. Serving** | User sends prompt → model generates word-by-word → safety filters → response |

#### Prompting vs RAG vs Fine-Tuning

| Approach | When | Data Needed | Cost | Time | Best For |
|---|---|---|---|---|---|
| **Prompting** | No data, quick start | None | Lowest | Minutes | Prototyping |
| **RAG** | Have documents | Documents (no labelling) | Low-Medium | Hours-Days | Factual Q&A, customer support |
| **Fine-Tuning** | Need custom behavior | Labelled examples | High | Days-Weeks | Domain adaptation, tone matching |

#### Productivity vs Automation

| Mode | Description | Human Role |
|---|---|---|
| **Productivity (Copilot)** | AI assists human, human decides | Reviews, edits, approves |
| **Automation (Autopilot)** | AI acts on its own | None |

> 90%+ of enterprise GenAI is productivity (human-in-loop), not full automation.

#### 3 Risks of Generative AI

| Risk | Description |
|---|---|
| **Hallucination** | Confidently wrong answers, fake sources/citations |
| **Bias** | Reproduces biases from training data (gender, race) |
| **Data/Privacy Risk** | Proprietary data leakage through prompts, copyright issues |

---

### RECOMMENDER SYSTEMS

#### 3 Types of Recommender Systems

| Type | How It Works | Strength | Weakness |
|---|---|---|---|
| **Collaborative Filtering** | "People like you liked X" | Discovers unexpected items | Cold start problem |
| **Content-Based Filtering** | "Items similar to what you liked" | Works for new items | Filter bubble (more of same) |
| **Hybrid** | Combines both | Best accuracy | More complex |

#### Cold Start Problem

| Scenario | Problem | Solution |
|---|---|---|
| New user | No history | Ask preferences at signup, show popular items |
| New item | No interactions | Use content features (genre, description) |

#### Recommendation Architecture (Production)

```
10M items → Candidate Generation (fast, ~1000) → Ranking (accurate, ~20) → Business Rules → Display
```

#### Recommendation Metrics

| Metric | Measures |
|---|---|
| **CTR (Click-Through Rate)** | % of recommended items clicked |
| **Conversion Rate** | % of clicks that lead to purchase |
| **NDCG** | Quality of ranking order |
| **Coverage** | % of catalog actually recommended |
| **Diversity** | How varied the recommendations are |

---

### CONVERSATIONAL AI

#### 4 Types (Simple → Advanced)

| Type | Smartness | Example |
|---|---|---|
| **Rule-based chatbot** | Low — fixed scripts, keyword matching | IRCTC IVR system |
| **Intent-based bot** | Medium — classifies intent, extracts entities | HDFC Bank's Eva |
| **LLM-based assistant** | High — handles open-ended conversations | ChatGPT, Claude |
| **RAG-based assistant** | High + Accurate — answers from real documents | Enterprise Q&A bots |

#### LLM Chatbots vs Traditional Chatbots

| Aspect | Traditional | LLM-based |
|---|---|---|
| Training | Manually define intents | Trained on massive text |
| Flexibility | Only pre-defined questions | Open-ended questions |
| Multi-turn context | Struggles | Naturally maintains context |
| Language support | Each language separately | 50+ languages with one model |
| Accuracy (known Qs) | Very high (scripted) | Good but may hallucinate |
| Maintenance | Manual updates per change | Update knowledge base (RAG) |

#### RAG — How It Works

```
User question → RETRIEVE relevant documents → AUGMENT prompt with docs → LLM GENERATES answer grounded in docs
```

> Without RAG: hallucination. With RAG: factual, citable answers.

#### Enterprise Conversational AI Architecture

```
User → Channel Adapter → NLU Engine → Dialog Manager → Knowledge Base/APIs → Response Generator → Channel Adapter → User
```

#### Human-in-the-Loop Patterns

| Pattern | How |
|---|---|
| AI drafts, human sends | AI generates draft → human reviews → sends |
| AI acts, human reviews | AI responds → human reviews afterward |
| AI escalates | AI tries → if uncertain → passes to human |

#### Escalation Triggers

- Low confidence (<70%)
- Emotional/angry user
- Complex multi-step issue
- Sensitive topics (legal, complaint)
- User explicitly requests human

---

### COMPUTER VISION

#### 6 Key Tasks

| Task | What It Does | Example |
|---|---|---|
| **Image Classification** | Labels entire image | Google Photos tagging |
| **Object Detection** | Finds + locates objects with bounding boxes | Self-driving cars |
| **Semantic Segmentation** | Labels every pixel | Medical imaging |
| **Face Recognition** | Identifies person from face | iPhone Face ID |
| **OCR** | Reads text from images | Google Lens translation |
| **Image Generation** | Creates images from text | DALL-E |

#### CNN — How It Works (Layer by Layer)

```
Pixels → Edges/Lines (Layer 1) → Shapes/Textures (Layer 2) → Parts (Layer 3) → Objects (Layer 4)
```

#### CV Enterprise Pipeline

```
Data Collection → Annotation → Training → Validation → Deployment → Monitoring
```

#### CV Deployment Challenges

| Challenge | Description |
|---|---|
| Lighting changes | Different lighting = different accuracy |
| Camera angle variations | Camera bumped = accuracy drops |
| Rare defects (class imbalance) | 99% good, 1% defective = model just says "good" |
| Edge computing constraints | Limited GPU on factory floor |
| Data privacy | Facial recognition regulation |
| Continuous annotation cost | New products = new labels needed |

---

### AUTONOMOUS SYSTEMS

#### Sense → Think → Act Loop

```
SENSE (cameras, LiDAR, radar) → THINK (AI decides what to do) → ACT (steer, brake, pick up)
         ↑                                                                    │
         └────────────────────── continuous feedback ─────────────────────────┘
```

#### SAE Levels of Autonomy (0-5)

| Level | Name | Who Drives? | Example |
|---|---|---|---|
| **0** | No Automation | Human | Old car |
| **1** | Driver Assistance | Human + 1 AI function | Adaptive cruise control |
| **2** | Partial Automation | Human + steering + speed | Tesla Autopilot |
| **3** | Conditional | AI in specific conditions | Mercedes Drive Pilot |
| **4** | High Automation | AI in specific areas, no human needed | Waymo robotaxis |
| **5** | Full Automation | AI everywhere | Does NOT exist yet |

#### Decision-Making, Planning, Control

| Component | Does What | Time Budget |
|---|---|---|
| **Decision-Making** | What to do (go/stop/yield) | <10ms |
| **Planning** | Path from A to B avoiding obstacles | <50ms |
| **Control** | Translate plan to steering/throttle/brake | <10ms |

> All three must complete in <100ms (humans: ~1500ms).

#### Waymo Sensors

| Sensor | Purpose |
|---|---|
| 29 cameras | 360° visual view |
| 4 LiDAR | 3D point cloud map (distance to objects) |
| 6 radar | Detect objects in fog/rain |
| GPS + IMU | Precise location and motion |
| Microphones | Detect emergency sirens |

---

### THREE CASE STUDIES — COMPARISON

| Case | Category | Key Lesson |
|---|---|---|
| **ChatGPT** | Generative + Conversational | RLHF + safety systems beyond the model. System matters as much as model. |
| **Netflix** | Recommender | Multiple models + personalisation at every touchpoint + A/B testing. 80% watched from recommendations. |
| **Waymo** | Autonomous | Sensor fusion + real-time <100ms + safety engineering is the hardest part. |

---

## SESSION 3: AI SYSTEM ARCHITECTURE

### 5-Layer Architecture

| Layer | Purpose | Key Tools |
|---|---|---|
| **Data Layer** | Collect, store, process, serve data | Kafka, S3, Spark, Airflow, Feast |
| **Model Layer** | Train, evaluate, version, serve models | MLflow, TensorFlow Serving, Triton |
| **Application Layer** | User-facing app, business logic, A/B testing | FastAPI, React, LaunchDarkly |
| **Infrastructure Layer** | Compute, storage, networking, containers | Kubernetes, Docker, Terraform, GPU |
| **Monitoring Layer** | Logs, metrics, drift detection, alerts | Prometheus, Grafana, Great Expectations |

### Data Layer Components

| Component | Purpose | Examples |
|---|---|---|
| Data Ingestion | Pull data from sources | Kafka, Kinesis, Airbyte |
| Data Storage | Store raw + processed data | S3 (lake), BigQuery (warehouse) |
| Data Processing | Clean, transform, aggregate | Spark, dbt, Airflow |
| Feature Store | Serve ML features consistently | Feast, Tecton, Hopsworks |
| Data Quality | Monitor completeness, freshness, anomalies | Great Expectations, Deequ |

### What to Monitor in Production

| Monitor | Why | Metric |
|---|---|---|
| **Model Performance** | Accuracy degrades over time | Accuracy, F1 over time |
| **Data Drift** | Input data changes vs training | KS test, PSI |
| **Prediction Drift** | Output distribution changes | Score distribution over time |
| **System Health** | Infra issues | Latency p50/p95/p99, error rate |
| **Business Metrics** | Does AI improve outcomes? | Revenue, retention, CTR |

### Zillow Failure Case Study

- Zillow built AI to predict house prices for home-buying business
- Model overpredicted prices (data drift from COVID market changes)
- No adequate monitoring to catch the drift early
- **Lost $881 million**, shut down business unit, laid off 2,000 employees
- **Lesson:** Without monitoring, AI silently serves wrong predictions

---

## SESSION 4: AI ECOSYSTEM & MODEL MARKETPLACES

### Foundation Models — Characteristics

| Characteristic | Description |
|---|---|
| Trained on massive data | Billions of tokens/images |
| General-purpose | Not task-specific — adapts to many tasks |
| Transfer learning | Knowledge transfers to new tasks |
| Emergent abilities | Capabilities not explicitly trained for |
| Very expensive to train | GPT-4: ~$100M+ |
| Accessible via APIs/fine-tuning | Regular devs can use without training from scratch |

### Notable Foundation Models

| Model | Company | Type | Key Fact |
|---|---|---|---|
| GPT-4/GPT-4o | OpenAI | Text + multimodal | Most capable general-purpose |
| Claude 3.5/4 | Anthropic | Text + vision | Safety focus, 200K context |
| Gemini | Google | Multimodal | Natively multimodal |
| LLaMA 3 | Meta | Text (open-source) | Free, huge community |
| Mistral | Mistral AI | Text (open-source) | Efficient for size |
| Stable Diffusion | Stability AI | Image (open-source) | Free image generation |

### Open-Source vs Proprietary Models

| Aspect | Open-Source | Proprietary (API) |
|---|---|---|
| Cost | Free weights, pay for compute | Pay per token/request |
| Data privacy | Data stays on your servers | Data sent to provider |
| Customization | Full fine-tuning possible | Prompt engineering only |
| Vendor lock-in | None | High |
| Maintenance | You manage everything | Provider handles |
| Best for | Production at scale, sensitive data | Prototyping, low volume |

### 5 Benefits of Open-Source Models

1. No API costs (run on own infrastructure)
2. Data privacy (data never leaves your servers)
3. Full customization (fine-tune, modify)
4. No vendor lock-in (switch freely)
5. Community innovation (500K+ models on Hugging Face)

### Cloud Model Marketplaces

| Platform | Provider | Key Benefit |
|---|---|---|
| **AWS Bedrock** | Amazon | Switch models without changing code |
| **Google Model Garden** | Google | Vertex AI integration |
| **Azure Model Catalog** | Microsoft | Only way to get GPT-4 with Azure compliance |
| **NVIDIA NGC** | NVIDIA | GPU-optimized models, 2-5x faster |

### License Types

| License | Freedom | Restriction | Example Models |
|---|---|---|---|
| **Apache 2.0** | Most free | None | Mistral, Whisper |
| **Llama License** | Mostly free | Restricted >700M MAU | LLaMA 2, LLaMA 3 |
| **Commercial** | Paid | Must purchase | Some medical/legal models |

### Build vs Buy vs Fine-Tune

| Aspect | API | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| Time to deploy | Hours-days | Days-weeks | Months-years |
| Upfront cost | Zero | Moderate | Very high |
| Data privacy | Data to 3rd party | Data stays local | Full control |
| Customization | Prompt engineering only | High | Maximum |
| Team needed | 1-2 developers | 2-5 ML engineers | 10-50 researchers |
| Best for | Prototyping, generic tasks | Domain-specific, privacy-sensitive | Core competitive advantage |

### Gateway/Router Pattern

```
App → Gateway → Simple queries → Small model (cheap, fast)
             → Complex queries → Large model (expensive, accurate)
```
**Benefit:** 78% cost reduction by routing 80% of simple queries to cheap models.

### Enterprise Procurement Scorecard

| Factor | Weight | What It Measures |
|---|---|---|
| Capability | 30% | Performance on your tasks |
| Cost | 25% | Total cost of ownership |
| Latency | 15% | Response time (p50/p95/p99) |
| Privacy | 15% | Data residency, compliance |
| Support | 10% | Enterprise support, SLAs |
| Lock-in | 5% | Ease of switching |

### Benchmarks (How Models Are Compared)

| Benchmark | Measures |
|---|---|
| **MMLU** | General knowledge (57 subjects) |
| **HumanEval** | Code generation ability |
| **MT-Bench** | Multi-turn conversation quality |
| **GSM8K** | Math reasoning |
| **TruthfulQA** | Factual accuracy / avoiding hallucinations |

> ⚠️ High benchmark ≠ good for YOUR task. Always evaluate on your own data.

---

## SESSION 5: DATA — TYPES, QUALITY, GOVERNANCE

### 3 Types of Data

| Type | Format | Storage | Query | % of Enterprise Data | ML Approach |
|---|---|---|---|---|---|
| **Structured** | Fixed rows & columns | SQL databases | SQL | ~20% | Tabular ML (XGBoost) |
| **Semi-Structured** | Flexible (JSON, XML) | Document DBs, Data Lakes | JSON queries | ~10% | Parse + tabular |
| **Unstructured** | No format (text, images) | Object storage (S3) | AI models, vector search | ~70% | Deep Learning |

### Event Data vs State Data

| Aspect | Event Data | State Data |
|---|---|---|
| Records | What HAPPENED | What IS (now) |
| Nature | Immutable (append-only) | Mutable (overwritten) |
| Example | "User clicked Buy at 10pm" | "User's balance is ₹5000" |
| ML use | Training (historical patterns) | Serving (current context) |

### 6 Dimensions of Data Quality

| Dimension | Definition |
|---|---|
| **Accuracy** | Values correctly represent reality |
| **Completeness** | No missing values where expected |
| **Consistency** | Same fact represented same way everywhere |
| **Timeliness** | Data reflects current state |
| **Validity** | Data follows expected format/rules |
| **Uniqueness** | No unintended duplicates |

### Common Data Quality Issues

| Issue | Problem | Fix |
|---|---|---|
| Missing values | Rows with NULLs | Impute (mean/median) or create "is_missing" feature |
| Outliers | Extreme values | Clip, log transform, or investigate |
| Duplicates | Same entity appears multiple times | Deduplicate by key fields |
| Label noise | Wrong labels in training | Manual audit, multiple annotators |
| Data leakage | Future info in training | Strict temporal train/test split |
| Class imbalance | One class dominates | SMOTE, class weights, threshold tuning |
| Schema drift | Data format changes over time | Schema validation + alerts |

### Data Governance — 7 Components

| Component | What It Covers |
|---|---|
| **Data Ownership** | Who is responsible for each data source |
| **Access Control** | Who can access what, for what purpose |
| **Data Lineage** | Where data came from, how transformed |
| **Data Cataloging** | Central inventory of all data assets |
| **Privacy & Compliance** | GDPR, DPDP Act, HIPAA compliance |
| **Data Retention** | How long data is kept, when deleted |
| **Quality Standards** | Defined thresholds (completeness >95%) |

### Centralized vs Federated Governance

| Model | How | Pro | Con |
|---|---|---|---|
| **Centralized** | One team controls all | Consistent | Bottleneck |
| **Federated** | Each domain governs own | Fast | Inconsistent |
| **Data Mesh** | Federated ownership + centralized standards | Best of both | Complex |

### GDPR vs India's DPDP Act

| | GDPR (EU) | DPDP Act (India) |
|---|---|---|
| Max fine | 4% of global revenue | ₹250 crore |
| Right to deletion | Yes | Yes |
| Consent required | Yes | Yes |
| Data Protection Officer | Required | Required |
| Applies to | EU data subjects | Indian data principals |

### Enterprise Data Maturity Model

| Level | Name | Description | AI Readiness |
|---|---|---|---|
| 1 | Ad-hoc | Spreadsheets, no governance | Not ready |
| 2 | Managed | Databases, some documentation | Simple ML possible |
| 3 | Defined | Data catalog, quality metrics | ML with effort |
| 4 | Quantified | Automated quality, SLAs, monitoring | ML at scale |
| 5 | Optimized | Data products, self-service, AI-ready | Full maturity |

### Synthetic Data — When and Why

| Use When | Benefit | Risk |
|---|---|---|
| Privacy restrictions | No real PII needed | Bias amplification |
| Rare events | Balance dataset | Distribution mismatch |
| Expensive to collect | Lower cost | Overfitting to generator |
| Dangerous scenarios | Safe simulation | May not capture real complexity |

### 5 Types of Bias in Data

| Bias Type | Description | Example |
|---|---|---|
| **Reporting Bias** | Unusual events overreported | News reports plane crashes, not safe landings |
| **Automation Bias** | Over-relying on AI output | Loan officer blindly follows AI recommendation |
| **Selection Bias** | Training data not representative | Model trained on urban users fails for rural |
| **Group Attribution Bias** | Group stat applied to individuals | "City X spends less" → penalise all from City X |
| **Implicit Bias** | Unconscious assumptions in data | "CEO" → images of men, "nurse" → images of women |

### Data Contracts

| Component | Specifies |
|---|---|
| Schema | Exact columns, data types, formats |
| Freshness SLA | Update frequency (every 4 hours) |
| Quality thresholds | <1% nulls, <0.1% duplicates |
| Ownership | Who is responsible |
| Access policies | Who can read/write |
| Change management | 14-day notice before breaking changes |

### Facebook/Cambridge Analytica — Governance Failure

- Quiz app harvested 87M users' data without consent
- Data used for targeted political ads
- No access control, no audit trail, no consent verification
- **$5 billion FTC fine** + global GDPR enforcement catalyst

---

## SESSION 6: DATA ENGINEERING PIPELINES

### ETL vs ELT

| Aspect | ETL | ELT |
|---|---|---|
| Order | Extract → Transform → Load | Extract → Load → Transform |
| Transform where | Separate processing server | Inside warehouse/lake |
| Raw data kept? | No | Yes |
| Flexibility | Low | High |
| Best for | On-premise, regulated | Cloud, big data, AI/ML |

### Batch vs Streaming

| Aspect | Batch | Streaming |
|---|---|---|
| Processing | Chunks on schedule | Each event as it arrives |
| Latency | Minutes–hours | Milliseconds–seconds |
| Complexity | Simpler | More complex |
| Cost | Lower | Higher |
| Use case | Reports, model training | Fraud detection, real-time recs |

> **Lambda Architecture** = Batch + Streaming together (most real-world systems).

### Data Lake vs Warehouse vs Lakehouse

| Aspect | Data Lake | Data Warehouse | Data Lakehouse |
|---|---|---|---|
| Data types | Any | Structured only | Any |
| Schema | Schema-on-read | Schema-on-write | Both |
| Cost | Very low | High | Low |
| Query speed | Slow | Very fast | Fast |
| Best for | ML/AI, raw archive | BI dashboards | ML + BI unified |

### Storage Types

| Type | What | Best For | Example |
|---|---|---|---|
| **File Storage** | Files in directories | Shared files, logs | NAS, NFS |
| **Block Storage** | Fixed-size blocks | Databases | EBS, SAN |
| **Object Storage** | Objects with metadata | Data lakes, ML data | S3, GCS |
| **Cache/Memory** | In-RAM storage | Real-time features | Redis, Memcached |
| **HDFS** | Distributed file system | Big data processing | Hadoop |
| **Streaming** | Append-only logs | Real-time events | Kafka, Kinesis |

### Streaming Concepts

| Concept | Definition |
|---|---|
| **Delivery Guarantees** | At-most-once / At-least-once / Exactly-once |
| **TTL (Time to Live)** | How long messages retained before deletion |
| **Dead-Letter Queue** | Queue for failed/unprocessable messages |
| **Replay** | Ability to re-read already consumed events |
| **Consumer Pull vs Push** | Pull: consumer polls. Push: broker sends. |

### Data Validation Tools

| Tool | Approach | Ecosystem |
|---|---|---|
| **Deequ** (Amazon) | Constraint-based (you write rules) | Apache Spark |
| **TFDV** (Google) | Schema-based (auto-generated) | TensorFlow |
| **Great Expectations** | Python-native, flexible | Any |

### Types of Data Drift

| Type | What Changes | Example |
|---|---|---|
| **Data Drift** | Input feature distribution | Average order value shifts |
| **Schema Skew** | Data schema/structure | New column added, type changed |
| **Distribution Skew** | Statistical distribution | Feature becomes bimodal |
| **Concept Drift** | Feature→target relationship | What "spam" looks like changes |
| **Training-Serving Skew** | Features differ train vs serve | Different code paths for same feature |

### Data Leakage — 2 Causes

| Cause | Description |
|---|---|
| **Feature hiding target** | Feature directly encodes the answer (e.g., "approved_date" in loan approval prediction) |
| **Feature from future** | Data not available at prediction time (e.g., using next-day volume to predict today's price) |

### Data Partitioning — 3 Types

| Type | Splits By | Reconstruct | Example |
|---|---|---|---|
| **Horizontal (Sharding)** | Rows | UNION | Orders split by region |
| **Vertical** | Columns | JOIN | Hot data (name) vs cold data (profile_pic) |
| **Functional** | Business function | Separate services | Orders DB vs Users DB vs Products DB |

---

## SESSION 7: FEATURE ENGINEERING

### What is a Feature?

A **feature** is a measurable property used as input to an ML model. Feature engineering converts raw data → features the model can understand.

> "Applied ML is basically feature engineering" — Andrew Ng

### Feature Extraction — By Data Type

| Data Type | Technique | Output |
|---|---|---|
| **Structured** | Direct use, aggregation, ratio, date extraction | Numbers/categories |
| **Text** | Bag of Words, TF-IDF, Word Embeddings, Sentence Embeddings | Vectors |
| **Image** | Pre-trained CNN features, object detection | Feature vectors |
| **Audio** | MFCCs, speech-to-text | Frequency features or text |

### Feature Transformation — Types

| Technique | What | When |
|---|---|---|
| **Min-Max Scaling** | Scale to [0,1] | Need bounded values |
| **Standard Scaling (Z-score)** | Mean=0, Std=1 | Normally distributed data |
| **Log Transformation** | log(x) | Highly skewed data |
| **One-Hot Encoding** | Binary columns per category | Nominal data (city, color) |
| **Label Encoding** | Number per category | Ordinal data (education level) |
| **Target Encoding** | Replace category with avg target | High-cardinality |
| **Binning** | Continuous → bins/categories | Non-linear relationships |

> ⚠️ Never use Label Encoding for nominal data — model thinks Delhi is "between" Mumbai and Bangalore.

### Feature Selection — 3 Methods

| Method | How | Speed | Accuracy |
|---|---|---|---|
| **Filter** | Statistical tests per feature (correlation, chi-squared) | Fast | Lower |
| **Wrapper** | Train model with different feature subsets | Slow | Higher |
| **Embedded** | Model learns importance during training (L1/Lasso, XGBoost importance) | Moderate | Good |

### Why Not All Features?

- **Overfitting** — irrelevant features add noise
- **Curse of dimensionality** — more features need exponentially more data
- **Slower** — more computation time
- **Harder to explain** — 5 features easier to audit than 500

### Embeddings

Convert high-dimensional sparse data → low-dimensional dense vectors. Similar items have similar vectors.

| Type | Embeds | Dimensions | Example |
|---|---|---|---|
| Word (Word2Vec) | Individual words | 100-300 | "king" ≈ "queen" in vector space |
| Sentence (BERT) | Sentences/paragraphs | 384-1024 | Semantic search |
| Image (ResNet) | Images | 512-2048 | Find visually similar products |
| User | Users | 64-256 | Netflix taste profile |
| Product | Products | 64-256 | Amazon similar items |

> **key - man + woman ≈ queen** (vector arithmetic captures meaning)

---

## SESSION 8: FEATURE STORES

### What is a Feature Store?

A centralized system that stores, manages, and serves ML features consistently for both training and serving.

### 5 Problems It Solves

| Problem | Without Feature Store | With Feature Store |
|---|---|---|
| Training-serving skew | Features computed differently | Single source of truth |
| Duplicate work | 5 teams build same feature | Compute once, share across teams |
| Slow serving | Compute on-the-fly (slow) | Pre-computed, cached (<1ms) |
| No discovery | Don't know what features exist | Searchable catalog |
| No versioning | Feature changes break models | Versioned features |

### Offline vs Online Store

| Aspect | Offline Store | Online Store |
|---|---|---|
| Computed | Batch (nightly/hourly) | Real-time (as events happen) |
| Represents | Historical aggregations | Current state |
| Latency | Minutes–hours OK | Must be <1-10ms |
| Storage | Data warehouse (BigQuery) | Key-value store (Redis) |
| Used for | Model training | Real-time predictions |
| Size | Very large (months of history) | Small (latest values only) |

### Training-Serving Skew — 4 Types

| Type | What Goes Wrong |
|---|---|
| **Feature computation skew** | Training computes feature one way, serving computes differently |
| **Data distribution skew** | Training data has different distribution than live data |
| **Feature availability skew** | Feature available in training is not available at serving time |
| **Time-travel skew** | Training accidentally uses future information |

### Two-Stage Recommendation Architecture

| Stage | Purpose | Input | Output | Speed |
|---|---|---|---|---|
| **Candidate Generation** | Quick rough filter | All items (millions) | ~1000 candidates | Very fast (ANN search) |
| **Ranking** | Accurate detailed scoring | ~1000 candidates | Top 20-50 items | Slower but precise |

> Why two stages? Running ranking model on all 10M items takes too long. Filter first, rank second.

### Feature Store Tools

| Tool | Type | Best For |
|---|---|---|
| **Feast** | Open-source | Startups, mid-size |
| **Tecton** | Commercial SaaS | Large enterprises |
| **Hopsworks** | Open-source + Commercial | Research + enterprise |
| **SageMaker Feature Store** | AWS managed | AWS users |

---

## QUICK-FIRE NUMBERS TO REMEMBER

| Fact | Number |
|---|---|
| Model code as % of AI system | **5%** |
| Model effort vs system effort | **20% vs 80%** |
| AI projects that fail | **85%** |
| Netflix content from recommendations | **80%** |
| Netflix savings from recommendations | **$1 billion/year** |
| Amazon revenue from recommendations | **35%** |
| Data collection/prep time in ML project | **80%** |
| Zillow loss from AI failure | **$881 million** |
| GPT-4 training cost | **~$100 million** |
| Cambridge Analytica FTC fine | **$5 billion** |
| DPDP Act max penalty | **₹250 crore** |
| Waymo autonomous miles driven | **20+ million** |
| Human reaction time | **~1500ms** |
| Autonomous system reaction time | **<100ms** |

---

## KEY COMPARISONS FOR EXAM

### AI vs ML vs DL
→ Nested: AI ⊃ ML ⊃ DL. AI is broadest, DL is most specialized.

### Supervised vs Unsupervised
→ Supervised: labelled data (classification, regression). Unsupervised: no labels (clustering, dimensionality reduction).

### Classification vs Regression
→ Classification: discrete labels. Regression: continuous numbers.

### Collaborative vs Content-Based Filtering
→ Collaborative: "similar users liked X." Content-based: "similar features to what you liked."

### RAG vs Fine-Tuning
→ RAG: search documents first, then answer (factual). Fine-tuning: retrain model on your data (behavioral change).

### ETL vs ELT
→ ETL: transform before loading (traditional). ELT: load raw then transform (modern, flexible).

### Batch vs Streaming
→ Batch: scheduled chunks (reports, training). Streaming: real-time per event (fraud, alerts).

### Data Lake vs Warehouse
→ Lake: any data, cheap, flexible, schema-on-read. Warehouse: structured, fast queries, schema-on-write.

### Offline vs Online Features
→ Offline: batch, historical, for training. Online: real-time, current, for serving.

### BFS vs DFS (if asked)
→ BFS: queue, level-by-level, shortest path. DFS: stack, go deep, cycle detection.

---

*Good luck with your exam!* 🎯
