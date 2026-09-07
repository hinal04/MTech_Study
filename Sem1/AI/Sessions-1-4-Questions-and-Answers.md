# Sessions 1–4: Foundations of AI Systems — Questions & Answers

> 20 questions covering: AI vs ML vs DL, AI system lifecycle, system components, industry applications, categories of AI systems (predictive/generative/recommender/conversational/CV/autonomous), AI system architecture (5 layers), foundation models, open-source ecosystem, APIs, model hubs, build vs buy.

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

### Q5. Explain predictive AI. What are the four main predictive task types?

**Answer:**

**Predictive AI** analyses historical data to predict future outcomes: "Given what happened before, what happens next?"

| Task | Output | Example |
|---|---|---|
| **Classification** | Discrete label (yes/no, category) | Email spam detection, disease diagnosis. |
| **Regression** | Continuous number | House price prediction, revenue forecasting. |
| **Time-series forecasting** | Future values in a sequence | Stock prices, demand forecasting, weather. |
| **Anomaly detection** | Normal vs abnormal | Credit card fraud, network intrusion, equipment failure. |

A trained model takes new data and predicts the likely outcome based on patterns learned from historical examples.

---

### Q6. What is Generative AI? Explain hallucinations and temperature.

**Answer:**

**Generative AI** creates new content (text, images, music, code) that didn't exist before, by learning the underlying distribution of training data and sampling from it.

**Hallucinations:** When a generative model produces confident but factually incorrect content. The model generates *plausible* text, not necessarily *true* text. Example: An LLM confidently cites a research paper that doesn't exist.

**Temperature:** A parameter controlling randomness in generation.
- **Low temperature (0.1-0.3):** More deterministic, repetitive, "safe." Good for factual Q&A.
- **High temperature (0.7-1.0):** More creative, diverse, surprising. Good for brainstorming, creative writing.
- **Temperature = 0:** Always picks the most probable next token (greedy decoding).

---

### Q7. Explain how ChatGPT works at a high level. What is RLHF?

**Answer:**

**ChatGPT** is built in three stages:

1. **Pre-training:** GPT model is trained on massive internet text to predict the next token. Learns grammar, facts, reasoning patterns.
2. **Supervised Fine-tuning (SFT):** Human labellers write ideal responses to prompts. The model is fine-tuned on these examples.
3. **RLHF (Reinforcement Learning from Human Feedback):** Humans rank multiple model responses. A reward model is trained on these rankings. The GPT model is then fine-tuned to maximise the reward model's score — making responses more helpful, harmless, and honest.

**System beyond the model:** Content filtering, rate limiting, safety guardrails, session management, feedback collection, A/B testing, monitoring for misuse.

---

### Q8. Compare collaborative filtering, content-based filtering, and hybrid recommender systems.

**Answer:**

| Approach | How it works | Pros | Cons |
|---|---|---|---|
| **Collaborative** | "Users similar to you liked X" | Discovers unexpected items. No need to understand items. | Cold-start (new users/items). Popularity bias. |
| **Content-based** | "You liked items with features X, Y — here's similar" | No cold-start for items. Transparent. | Limited diversity (more of the same). |
| **Hybrid** | Combines both approaches | Best accuracy. Overcomes individual weaknesses. | More complex. |

**Netflix example:** Uses hybrid — collaborative filtering (similar users), content-based (genre/actor matching), contextual (time of day, device), even personalised thumbnails.

---

### Q9. What is RAG (Retrieval-Augmented Generation)? Why does it matter?

**Answer:**

**RAG** addresses the hallucination problem by grounding LLM responses in retrieved facts:

```
User query → Search knowledge base → Retrieve relevant documents
                                            ↓
                            LLM generates answer USING retrieved docs as context
                                            ↓
                                     Grounded response (can cite sources)
```

**Why it matters:**
- Reduces hallucinations — answers are based on actual documents, not just model memory.
- Keeps information current — update the knowledge base without retraining the model.
- Enables enterprise use — answer questions from company docs, policies, manuals.
- Provides citations — users can verify the answer against source documents.

---

### Q10. Describe the five-layer AI system architecture. What does each layer do?

**Answer:**

| Layer | Responsibility | Key components |
|---|---|---|
| **Data Layer** | Collect, store, process, serve data. Foundation of everything. | Data ingestion, data lake/warehouse, ETL pipelines, feature store, data quality checks. |
| **Model Layer** | Train, evaluate, version, serve models. | Experiment tracking (MLflow), training pipelines, model registry, model serving (TorchServe). |
| **Application Layer** | Integrate predictions into user experience. | API gateway, business logic, A/B testing, UI, feedback collection. |
| **Infrastructure Layer** | Compute and networking resources. | GPUs/TPUs, Kubernetes, storage, CI/CD, IaC. |
| **Monitoring Layer** | Observe everything, alert when things go wrong. | Model performance tracking, data drift detection, system health (latency, errors), business metrics. |

These layers enable independent team work, component replacement, and per-layer scaling.

---

### Q11. Walk through how a recommendation request flows through the AI system architecture.

**Answer:**

```
1. USER opens app homepage
2. APPLICATION LAYER: API gateway receives request (user_id, context)
3. DATA LAYER: Feature store serves user features (past purchases, browse 
   history) and item features (category, price) — pre-computed, <10ms
4. MODEL LAYER: Model scores candidate items using features → returns top 20
5. APPLICATION LAYER: Business logic filters (remove out-of-stock), applies 
   diversity rules, A/B testing assigns model version → renders top 10
6. USER sees recommendations, clicks Item #3
7. MONITORING: Click logged, CTR tracked, recommendation distribution checked
8. FEEDBACK LOOP: Click data flows to data lake → included in next retrain
```

The model itself is one step. The surrounding system (feature serving, filtering, A/B testing, monitoring, feedback loop) is the majority of the work.

---

### Q12. What is a foundation model? List 4 key characteristics.

**Answer:**

A **foundation model** is a large AI model trained on broad, diverse data at scale, designed to be adapted for many downstream tasks.

**4 key characteristics:**
1. **Massive scale:** Trained on billions of data points (text, images). Internet-scale data.
2. **General-purpose:** Not task-specific. Can be adapted to translation, summarisation, coding, Q&A, image generation.
3. **Transfer learning:** Knowledge transfers to downstream tasks even with limited task-specific data.
4. **Emergent abilities:** Larger models exhibit capabilities not explicitly trained for (chain-of-thought reasoning, in-context learning).

**Examples:** GPT-4 (OpenAI), Claude (Anthropic), LLaMA 3 (Meta), Gemini (Google), Stable Diffusion (Stability AI).

---

### Q13. Compare open-source models vs API-based models. When would you use each?

**Answer:**

| Aspect | Open-Source Models | API-Based Models |
|---|---|---|
| **Cost model** | Infrastructure hosting (your GPUs) | Per-request fees (pay per token) |
| **Data privacy** | Data stays on your servers | Data sent to third party |
| **Customisation** | Full — fine-tune, modify architecture | Limited — prompt engineering only |
| **Vendor lock-in** | None | High (provider can change API/pricing) |
| **Setup effort** | High (deploy, scale, manage) | Low (API call in minutes) |
| **Maintenance** | You handle updates, scaling | Provider handles everything |

**Use open-source when:** Data privacy is critical (healthcare, finance), need deep customisation, high-volume usage (API costs add up), want full control.

**Use API when:** Prototyping quickly, generic tasks (summarisation, translation), low volume, no ML infrastructure team.

---

### Q14. Explain the Build vs Buy decision framework for AI. Give 3 real-world examples.

**Answer:**

```
Is the task generic? → YES → Use API (GPT-4, Claude)
                     → NO  ↓
Is there a close open-source model? → YES → Fine-tune it
                                    → NO  ↓
Is it a core business differentiator? → YES → Build from scratch
                                      → NO  → Reconsider if you need ML
```

| Scenario | Decision | Why |
|---|---|---|
| Startup: customer support chatbot | **API (GPT-4)** | Fast to market, generic task, low volume. |
| Bank: fraud detection | **Build from scratch** | Proprietary data, regulatory requirements, core differentiator. |
| Hospital: radiology AI | **Fine-tune open-source** | Domain adaptation needed, patient data can't leave servers. |

---

### Q15. What is Hugging Face? Why is it called the "GitHub of ML"?

**Answer:**

**Hugging Face** is the central hub for open-source AI — a platform for sharing, discovering, and using ML models and datasets.

| Feature | What it provides |
|---|---|
| **Model Hub** | 500K+ pre-trained models with documentation (model cards). |
| **Datasets** | 100K+ datasets ready to use with preview and streaming. |
| **Transformers library** | Python library to load and use any model in a few lines. |
| **Spaces** | Host interactive model demos (Gradio, Streamlit). |
| **Inference API** | Run models via API without deploying infrastructure. |
| **PEFT/LoRA** | Tools for parameter-efficient fine-tuning. |

**"GitHub of ML"** because: just as GitHub hosts code, Hugging Face hosts models. You can browse, download, fork, contribute, and collaborate on ML models — with versioning, documentation, and community discussion.

---

### Q16. What are the SAE levels of vehicle autonomy? Give examples for Levels 2, 4, and 5.

**Answer:**

| Level | Name | Human role | Example |
|---|---|---|---|
| 0 | No Automation | Human does everything | Standard car |
| 1 | Driver Assistance | System controls ONE function | Adaptive cruise control |
| **2** | **Partial Automation** | System controls steering AND speed. Human monitors. | **Tesla Autopilot, GM Super Cruise** |
| 3 | Conditional Automation | System drives in specific conditions. Human takes over on request. | Mercedes Drive Pilot (highway) |
| **4** | **High Automation** | System drives in specific areas. No human needed there. | **Waymo robotaxis (geofenced)** |
| **5** | **Full Automation** | System drives everywhere. No steering wheel. | **Doesn't exist yet** |

---

### Q17. What systems does an autonomous vehicle combine? Why is it one of the most complex AI systems?

**Answer:**

An autonomous vehicle combines:
- **Computer vision** (cameras) — detect lanes, traffic lights, pedestrians, vehicles.
- **LiDAR** — 3D point cloud map of surroundings.
- **Sensor fusion** — combine camera + LiDAR + radar + GPS + IMU into unified world model.
- **Prediction** — predict what other road users will do next.
- **Path planning** — decide route and trajectory.
- **Control** — execute trajectory (steering, throttle, braking).

**Why most complex:** Must operate in real-time (<100ms), with extreme reliability (one failure = potential fatality), in completely unpredictable environments (weather, construction, erratic drivers), fusing data from 6+ sensor types simultaneously.

---

### Q18. Compare predictive AI and generative AI across 5 dimensions.

**Answer:**

| Dimension | Predictive AI | Generative AI |
|---|---|---|
| **Goal** | Predict an outcome (label, number) | Create new content (text, images) |
| **Output** | Classification label, regression value, probability | Text, images, code, music, video |
| **Training** | Supervised (labelled examples) | Self-supervised (predict next token) + RLHF |
| **Evaluation** | Accuracy, precision, recall, F1, AUC | Human evaluation, perplexity, BLEU, hallucination rate |
| **Example** | "Will this customer churn?" (yes/no) | "Write a marketing email for this customer" (new text) |

---

### Q19. What is data drift? Why is monitoring essential for AI systems?

**Answer:**

**Data drift** occurs when the distribution of incoming production data changes compared to the training data. The model was trained on one distribution but is now seeing a different one — its predictions degrade.

**Types of drift:**
- **Data drift (covariate shift):** Input feature distributions change. Example: A fraud model trained on weekday transactions starts seeing weekend patterns.
- **Concept drift:** The relationship between features and target changes. Example: What constitutes "fraud" changes as fraudsters develop new techniques.
- **Prediction drift:** Model output distribution shifts. Example: A spam filter suddenly classifies 50% of emails as spam (previously 5%).

**Why monitoring is essential:** Unlike traditional software bugs (which crash visibly), ML drift causes the model to **silently serve wrong predictions**. Without monitoring, you won't know until business metrics drop — which could be weeks later.

---

### Q20. What is a model card? Why is documentation important in the AI ecosystem?

**Answer:**

A **model card** is standardised documentation for an ML model, typically including:
- **Model description:** What it does, architecture, size.
- **Training data:** What data was used, how it was collected.
- **Intended uses:** What the model is designed for.
- **Limitations:** What it's NOT good at, known failure modes.
- **Performance:** Benchmarks, metrics on standard datasets.
- **Ethical considerations:** Bias analysis, fairness metrics.
- **Environmental impact:** Training compute, carbon footprint.

**Why documentation matters:**
1. **Responsible use:** Users understand what the model can and can't do.
2. **Reproducibility:** Others can replicate results.
3. **Accountability:** Clear record for auditing and compliance.
4. **Trust:** Transparent documentation builds trust in AI systems.

Hugging Face requires model cards for all models on its hub — it's becoming an industry standard.

---

*End of Sessions 1–4 Questions & Answers*
