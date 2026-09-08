# Session 1: Introduction to AI — Questions & Answers

> 4 questions covering Session 1 topics.

---

### Q1. Differentiate between AI, Machine Learning, and Deep Learning with examples.

**Answer:**

These are nested concepts — each is a subset of the previous:

| Aspect | AI | Machine Learning | Deep Learning |
|---|---|---|---|
| **Definition** | Systems that exhibit intelligent behaviour | Subset of AI that learns from data | Subset of ML using deep neural networks |
| **How it works** | Rules, logic, heuristics, or learning | Learns function f(x)→y from examples | Multi-layer networks learn hierarchical features |
| **Feature engineering** | Manual rules | Manual features needed | Automatic feature learning from raw data |
| **Data needs** | Varies | Moderate (thousands-millions) | Large (millions-billions) |
| **Example** | Chess engine (Minimax), expert systems | Spam filter, fraud detection | GPT-4, image recognition, speech recognition |

**Key distinction:** Traditional AI uses hand-coded rules. ML learns rules from data. Deep Learning learns both the rules AND the features from raw data.

---

### Q2. Describe the six stages of the AI system lifecycle. Why is it circular?

**Answer:**

| Stage | What happens | Key pitfall |
|---|---|---|
| **1. Problem Definition** | Translate business problem to ML task. Define metrics. | Solving the wrong problem. No clear success metric. |
| **2. Data Collection & Prep** | Gather, clean, label data. Train/test split. | Poor quality, bias, data leakage. |
| **3. Model Development** | Feature engineering, model selection, training, tuning. | Overfitting. Not establishing baseline first. |
| **4. Deployment** | Package model, create API, configure infrastructure. | Works in notebook, fails in production. |
| **5. Monitoring** | Track accuracy, drift, latency, errors in production. | Assuming model stays accurate forever. |
| **6. Iteration** | Retrain, fix bugs, update features from production feedback. | Not closing the feedback loop. |

**Why circular:** Unlike traditional software (code doesn't degrade), ML systems degrade over time because the world changes — user behaviour evolves, new patterns emerge, data distributions shift. Continuous retraining and monitoring is required. The lifecycle is an infinite loop.

---

### Q3. What are the key components of an AI system? Why is the model "less than 5% of the system"?

**Answer:**

| Component | Purpose |
|---|---|
| **Data Pipeline** | Collect, clean, transform, store data. "Garbage in, garbage out." |
| **Feature Pipeline** | Transform raw data into model-consumable features. Must be consistent between training and serving. |
| **Model Pipeline** | Train, evaluate, select, version models. Experiment tracking. |
| **Serving Infrastructure** | Host model, serve predictions via API, handle scaling and latency. |
| **Monitoring** | Track performance, data drift, prediction drift, system health. |
| **Application Layer** | Business logic, UI, A/B testing, feedback collection. |

**Why <5%:** The model is just one Python file. The surrounding system — data pipelines, feature engineering, serving, monitoring, CI/CD, logging, A/B testing — is 95% of the code and effort. Google's famous paper "Hidden Technical Debt in ML Systems" showed that the ML code is a tiny box surrounded by a massive infrastructure.

---

### Q4. List 5 AI applications across different industries with examples.

**Answer:**

| Industry | Application | Example |
|---|---|---|
| **Healthcare** | Medical imaging analysis | DeepMind detecting eye diseases from retinal scans. |
| **Finance** | Fraud detection | PayPal detecting fraud among 10M+ daily transactions. |
| **E-commerce** | Recommendation engines | Amazon's "Customers also bought" drives 35% of revenue. |
| **Manufacturing** | Predictive maintenance | Siemens predicting turbine failures 20 hours in advance. |
| **Transportation** | Autonomous driving | Waymo robotaxis operating in geofenced areas (Level 4). |

---


---

*End of Session 1: Introduction to AI Questions & Answers*
