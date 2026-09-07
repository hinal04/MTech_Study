# AI Systems — Exam-Style Questions (BITS Pilani Pattern)

> **Based on:** BITS Pilani SS ZG662 course structure and evaluation pattern.
>
> **Exam Pattern:**
> - **Mid-Semester (EC-2):** Closed Book, 2 hours, 30% weightage. Covers Sessions 1–8 (Modules 1–2).
> - **Comprehensive (EC-3):** Open Book, 2.5 hours, 30% weightage. All topics.
> - Topics 1-5 (Foundations + Data) are heavily tested in mid-semester.
>
> **Question styles:** Short answer, comparison, scenario-based design, case study analysis, MCQ/True-False.

---
---

## SECTION A — Short Answer (3-5 marks each)

---

### A1. Define AI, ML, and Deep Learning. Show their relationship and give one example of each. [5 marks]

**Answer:**

AI is the broad field of creating systems that perform tasks requiring human intelligence. ML is a subset where systems learn patterns from data. DL is a subset of ML using deep neural networks that automatically learn hierarchical features.

**Relationship:** AI ⊃ ML ⊃ DL (nested subsets).

| | Example |
|---|---|
| **AI (not ML)** | Chess engine using Minimax algorithm (hand-coded rules, no learning). |
| **ML (not DL)** | Spam filter using logistic regression on hand-crafted features (word counts). |
| **DL** | GPT-4 generating text using a transformer with billions of parameters, learning features automatically from raw text. |

**Key distinction:** Traditional AI uses explicit rules. ML learns rules from data. DL learns both features AND rules from raw data.

---

### A2. List the five essential layers of an AI system architecture and state the primary responsibility of each. [5 marks]

**Answer:**

| Layer | Primary Responsibility |
|---|---|
| **Data Layer** | Collect, store, process, and serve data. Feature store, data pipelines, data quality. |
| **Model Layer** | Train, evaluate, version, and serve ML models. Experiment tracking, model registry. |
| **Application Layer** | Integrate predictions into user experience. Business logic, A/B testing, APIs, feedback. |
| **Infrastructure Layer** | Provide compute (GPUs), storage, networking, CI/CD, container orchestration. |
| **Monitoring Layer** | Observe model performance, data drift, system health, business metrics. Alert on degradation. |

---

### A3. What is RLHF? Explain its role in building ChatGPT. [4 marks]

**Answer:**

**RLHF (Reinforcement Learning from Human Feedback)** is a technique to align language models with human preferences.

**Process in ChatGPT:**
1. Human labellers rank multiple model responses to the same prompt (e.g. response A is better than B).
2. A **reward model** is trained to predict human preference scores from these rankings.
3. The GPT model is fine-tuned using reinforcement learning (PPO algorithm) to maximise the reward model's score.

**Why it matters:** Pre-training alone produces a model that predicts likely text (which may be harmful, biased, or unhelpful). RLHF steers the model toward being helpful, harmless, and honest — bridging the gap between "what text is likely" and "what response is good."

---

### A4. Name and briefly explain the six dimensions of data quality. [3 marks]

**Answer:**

1. **Accuracy** — Values correctly represent reality. (Age = 150 is inaccurate.)
2. **Completeness** — No missing values where expected. (30% of rows missing income.)
3. **Consistency** — Same fact represented identically everywhere. ("US" vs "United States".)
4. **Timeliness** — Data reflects current state. (24-hour-old inventory in a real-time system.)
5. **Validity** — Data conforms to defined formats/types. (Phone field containing emails.)
6. **Uniqueness** — No unintended duplicates. (Same customer appearing 3 times.)

---

### A5. What is data leakage? Why is it dangerous in ML? Give one example. [3 marks]

**Answer:**

**Data leakage** occurs when information from the test set or the future "leaks" into training data, giving the model access to information unavailable at prediction time.

**Why dangerous:** Model appears to perform brilliantly during evaluation but completely fails in production — because the leaked feature doesn't exist when making real predictions. It's hard to detect because metrics look great.

**Example:** A credit scoring model uses "credit_bureau_score" as a feature. But the bureau updates this score AFTER the loan decision. During training, the model sees future information → 99% accuracy. In production, the score isn't available at decision time → model fails.

---

### A6. Distinguish between collaborative filtering and content-based filtering for recommender systems. [4 marks]

**Answer:**

| Aspect | Collaborative Filtering | Content-Based Filtering |
|---|---|---|
| **Approach** | "Users similar to you liked X" | "You liked items with features X, Y — here are similar items" |
| **Data needed** | User-item interaction data (ratings, clicks, purchases) | Item features (genre, price, description) + user preferences |
| **Cold-start** | Fails for new users/items (no interaction history) | Works for new items (features known). Fails for new users. |
| **Diversity** | Can discover unexpected items | Tends to recommend more of the same |
| **Example** | Netflix: "Users who watched Breaking Bad also watched..." | Spotify: "Based on the tempo and genre of songs you play..." |

Most production systems use **hybrid** approaches combining both.

---
---

## SECTION B — Comparison & Analysis (5-8 marks each)

---

### B1. Compare Predictive AI and Generative AI across at least 5 dimensions. Give two applications of each. [6 marks]

**Answer:**

| Dimension | Predictive AI | Generative AI |
|---|---|---|
| **Goal** | Predict an outcome (label, number, probability) | Create new content (text, images, code, audio) |
| **Output type** | Classification label, regression value, anomaly score | Text, images, music, video, code |
| **Training approach** | Supervised learning (labelled examples) | Self-supervised (predict next token) + RLHF |
| **Evaluation** | Accuracy, precision, recall, F1, AUC | Human evaluation, perplexity, BLEU, hallucination rate |
| **Data needs** | Thousands to millions of labelled examples | Billions of tokens/images (internet-scale) |

**Predictive AI applications:** (1) Fraud detection — classify transactions as fraud/legitimate. (2) Demand forecasting — predict next week's sales for inventory planning.

**Generative AI applications:** (1) ChatGPT — generate conversational responses. (2) DALL-E — create images from text descriptions.

---

### B2. A startup wants to build an AI-powered customer support chatbot. Compare three approaches: rule-based, intent-based NLU, and LLM-based. Recommend one with justification. [7 marks]

**Answer:**

| Aspect | Rule-Based | Intent-Based NLU | LLM-Based |
|---|---|---|---|
| **How it works** | Decision trees, keyword matching | Classifies intent + extracts entities → routes to handler | LLM understands and generates responses directly |
| **Flexibility** | Low — only handles pre-defined paths | Medium — handles trained intents | High — handles open-ended conversations |
| **Setup effort** | Low (write rules) | Medium (design intents, collect training data) | Low (API call) to Medium (fine-tune) |
| **Accuracy** | 100% for known paths, 0% otherwise | Good for trained intents, fails on unknown | Good for general queries, may hallucinate |
| **Cost** | Low | Medium | Higher (API fees or GPU hosting) |
| **Maintenance** | High (update rules for every new scenario) | Medium (retrain for new intents) | Low (model handles new queries naturally) |

**Recommendation for a startup: LLM-based (via API like GPT-4) with RAG.**

**Justification:**
1. **Fast time-to-market** — API integration in days, not months of rule/intent design.
2. **Handles diverse queries** — customers ask unpredictable questions. LLM handles open-ended conversations naturally.
3. **RAG for grounding** — Connect to company FAQ/knowledge base to reduce hallucinations and provide accurate answers.
4. **Low upfront cost** — No ML team needed initially. Pay per API call.
5. **Scalable** — As volume grows, can migrate from API to fine-tuned open-source model for cost efficiency.

---

### B3. Compare "Build from Scratch," "Fine-Tune Open-Source," and "Use API" for AI model deployment. For each, give a scenario where it's the best choice. [8 marks]

**Answer:**

| Aspect | Use API | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| **Time to deploy** | Hours to days | Days to weeks | Months |
| **Upfront cost** | Zero | Moderate (compute) | High (team + compute + data) |
| **Ongoing cost** | Per-request fees | Infrastructure hosting | Team + infrastructure |
| **Data privacy** | Data sent to third party | Data stays on your servers | Full control |
| **Customisation** | Limited (prompts only) | High (domain adaptation) | Maximum |
| **Maintenance** | Provider handles | You handle | You handle |
| **Vendor dependency** | High | Low | None |

**Best-choice scenarios:**

| Scenario | Best approach | Why |
|---|---|---|
| Marketing team generating ad copy | **Use API (GPT-4)** | Generic creative task, low volume, no sensitivity. Fast. |
| Hospital building radiology AI | **Fine-tune open-source** | Patient data can't leave servers. Need medical domain adaptation. |
| Self-driving car company | **Build from scratch** | Safety-critical. Massive proprietary data. Core technology differentiator. |

---
---

## SECTION C — Scenario & Design Questions (8-12 marks each)

---

### C1. You are designing an AI system for a large e-commerce company's product recommendation engine. Describe the system architecture using the five-layer model. For each layer, identify at least 2 specific components you would use. [10 marks]

**Answer:**

| Layer | Components | Specific technologies | Purpose in this system |
|---|---|---|---|
| **Data Layer** | Data lake, Feature store, Data pipeline | S3 (storage), Feast (feature store), Apache Kafka (streaming ingestion), Airflow (orchestration) | Store user clickstream, purchase history, product catalog. Serve user/item features with <10ms latency for real-time recommendations. |
| **Model Layer** | Training pipeline, Model registry, Model serving | SageMaker (training), MLflow (registry + experiment tracking), TorchServe (serving) | Train collaborative filtering + content-based hybrid model. A/B test model versions. Serve predictions via REST API. |
| **Application Layer** | API gateway, Business logic, A/B testing | FastAPI (gateway), Custom logic (filter out-of-stock, apply diversity rules), LaunchDarkly (A/B) | Transform model scores into ranked recommendations. Apply business rules. Test new models on 10% of users before full rollout. |
| **Infrastructure Layer** | Compute, Orchestration, CI/CD | AWS GPU instances (training), Kubernetes (serving), GitHub Actions (CI/CD) | GPU clusters for model training. Auto-scaling Kubernetes pods for serving. Automated deployment pipeline. |
| **Monitoring Layer** | Model metrics, Data drift, Business KPIs | Prometheus + Grafana (system health), custom drift detection (KS test on feature distributions), business dashboards (CTR, conversion) | Detect when recommendation quality degrades. Alert on feature drift. Track click-through rate and revenue impact. |

**Request flow:** User opens app → API gateway → feature store serves user+item features → model scores candidates → business logic filters/reranks → top 10 shown → user clicks → click logged → feeds back into training data.

---

### C2. A bank discovers that its loan approval AI model, deployed 6 months ago, is approving more high-risk loans than expected. Analyze potential causes and recommend fixes for each. [10 marks]

**Answer:**

| Potential cause | Explanation | Detection method | Fix |
|---|---|---|---|
| **1. Data drift** | Applicant demographics changed — bank expanded to rural areas not in training data. | Compare feature distributions (income, employment type) between training data and recent applications. KS test or PSI. | Retrain model with recent data including rural applicants. Monitor feature distributions continuously. |
| **2. Concept drift** | Economic conditions changed — what constituted "low risk" in 2023 may be "high risk" in 2025 (recession, job market shifts). | Track default rate over time. If model confidence is high but defaults are increasing → concept drift. | Retrain on recent labelled data. Shorten retraining cycle from 6 months to monthly. |
| **3. Label leakage in training** | Model was trained with a feature (e.g. credit bureau score) not available at prediction time, artificially inflating training accuracy. | Audit feature list — check each feature's availability at prediction time. | Remove leaked feature, retrain, accept lower (but honest) accuracy. |
| **4. Class imbalance not handled** | Only 2% of training loans defaulted. Model learned to approve everyone. | Check precision/recall on the "default" class. If recall is near 0 → imbalance issue. | Apply class weights, SMOTE oversampling, or adjust decision threshold. |
| **5. Missing monitoring** | No system in place to detect degradation. Problem discovered only after 6 months of damage. | (Retrospective — monitoring should have been there.) | Implement real-time monitoring: track approval rate, predicted risk distribution, and actual default rate. Set alerts for significant deviations. |

**Recommended action plan:**
1. **Immediate:** Increase human review threshold — require manual review for borderline approvals.
2. **Short-term (1-2 weeks):** Audit features for leakage. Retrain with recent data and proper class balancing.
3. **Medium-term (1 month):** Deploy monitoring for data drift, prediction drift, and business metric tracking.
4. **Long-term:** Set up automated retraining pipeline triggered by drift alerts.

---

### C3. A company wants to build an AI-powered document summarisation tool for internal use. Their documents contain confidential client information. Evaluate whether they should use an API (GPT-4), fine-tune an open-source model, or build from scratch. [8 marks]

**Answer:**

| Factor | Use API (GPT-4) | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| **Data privacy** | ❌ Confidential docs sent to OpenAI servers | ✅ Data stays on company servers | ✅ Full control |
| **Cost** | Low (per-request) | Medium (GPU for fine-tuning + hosting) | Very high (team + data + compute) |
| **Quality** | Excellent for general summarisation | Good — improved with domain fine-tuning | Takes months to match pre-trained quality |
| **Time to deploy** | Days | 2-4 weeks | 6-12 months |
| **Customisation** | Limited (prompts only) | High (learns company terminology, format) | Maximum but requires massive effort |
| **Maintenance** | Provider handles | Company handles updates | Company handles everything |

**Recommendation: Fine-tune an open-source model (e.g. LLaMA 3 or Mistral).**

**Justification:**
1. **Privacy is non-negotiable** — confidential client data cannot leave company servers. This eliminates the API option.
2. **Building from scratch is overkill** — summarisation is a well-solved problem. Pre-trained models already have strong capabilities. Fine-tuning adapts them to company-specific terminology and document formats.
3. **Practical path:** Download LLaMA 3 → fine-tune on 1,000 company documents with example summaries → deploy on company's GPU servers → iterate based on user feedback.
4. **Cost-effective:** One-time fine-tuning cost (a few hundred dollars of GPU time) + hosting cost. No per-request API fees. Scales better with volume.

---
---

## SECTION D — MCQ / True-False (1-2 marks each)

---

### D1. Which of the following is NOT a category of AI systems covered in this course?

(a) Predictive AI
(b) Generative AI
(c) Quantum AI
(d) Recommender Systems
(e) Conversational AI

**Answer: (c) Quantum AI.** The six categories covered are: Predictive AI, Generative AI, Recommender Systems, Conversational AI, Computer Vision, and Autonomous Systems.

---

### D2. True or False: In the AI system lifecycle, deployment is the final step.

**Answer: False.** Deployment is followed by Monitoring and then Iteration/Improvement. The lifecycle is circular — ML systems require continuous retraining and monitoring because the world changes (data drift, concept drift, evolving user behaviour). It's an infinite loop, not a waterfall.

---

### D3. Which layer of the AI system architecture is responsible for detecting data drift?

(a) Data Layer
(b) Model Layer
(c) Application Layer
(d) Monitoring Layer

**Answer: (d) Monitoring Layer.** The monitoring layer observes model performance, data distributions, prediction distributions, and system health. It detects drift by comparing incoming data distributions against training data distributions using statistical tests (KS test, PSI).

---

### D4. True or False: A foundation model is trained for a specific task and cannot be adapted to other tasks.

**Answer: False.** A foundation model is trained on broad, diverse data and is specifically designed to be **adapted (fine-tuned) for many downstream tasks** — translation, summarisation, coding, Q&A, image generation, etc. This generality is the defining characteristic.

---

### D5. What does the "cold-start problem" refer to in recommender systems?

(a) The system takes too long to start up
(b) New users or items have no interaction history for recommendations
(c) The recommendation model hasn't been trained yet
(d) The server temperature is too low for GPU computation

**Answer: (b).** The cold-start problem occurs when a new user (no purchase/click history) or new item (no ratings/interactions) joins the system. Collaborative filtering fails because it relies on interaction history. Content-based approaches partially mitigate this for new items (using item features).

---

### D6. In the Build vs Buy framework, which approach is best when data privacy is critical and the task requires domain-specific adaptation?

(a) Use a commercial API
(b) Fine-tune an open-source model
(c) Build a model from scratch
(d) Use a no-code AI platform

**Answer: (b) Fine-tune an open-source model.** Data stays on your servers (privacy ✓), and fine-tuning adapts the model to your domain (customisation ✓). Building from scratch is overkill if a good open-source base exists. APIs send data to third parties (privacy ✗).

---

### D7. True or False: In an AI system, "better data almost always beats a better model."

**Answer: True.** This is a widely accepted principle in ML engineering. A simple model on clean, relevant, representative data consistently outperforms a complex model on noisy, biased, incomplete data. Google's research confirmed that fixing data quality issues improved performance more than switching to more complex architectures.

---

### D8. Which data quality dimension is violated when training data from 2020 is used to predict 2025 patterns without updating?

(a) Accuracy
(b) Completeness
(c) Timeliness (Freshness)
(d) Uniqueness

**Answer: (c) Timeliness.** The data doesn't reflect the current state of the world. Patterns from 2020 (pre/during COVID) may not apply in 2025. The model will make predictions based on outdated distributions, leading to degraded performance.

---

### D9. What is the primary purpose of a feature store in an AI system?

(a) Store trained model weights
(b) Manage and serve ML features consistently across training and serving
(c) Store raw unprocessed data
(d) Monitor model performance in production

**Answer: (b).** A feature store manages computed features and ensures they are served consistently between training (offline, batch) and serving (online, real-time). This prevents **training-serving skew** — a common bug where features are computed differently during training vs production, causing silent model degradation.

---

### D10. A facial recognition model trained mostly on light-skinned faces performs poorly on dark-skinned faces. This is an example of:

(a) Data leakage
(b) Concept drift
(c) Sampling bias
(d) Class imbalance

**Answer: (c) Sampling bias.** The training data was not representative of the real-world population. Light-skinned faces were overrepresented, dark-skinned faces underrepresented. The model performs well on the majority group but poorly on underrepresented groups — a fairness and bias issue rooted in non-representative data collection.

---
---

## Exam Preparation Tips (BITS Pilani SS ZG662)

1. **AI vs ML vs DL** — Know the nested relationship. Be ready to give examples of each that are NOT the other.
2. **AI System Lifecycle** — Memorise all 6 stages and why it's circular (data drift, concept drift).
3. **5-Layer Architecture** — Draw it from memory. Know 2-3 components per layer.
4. **Categories of AI Systems** — Know all 6 categories with real-world case studies (ChatGPT, Netflix, Waymo).
5. **Build vs Buy** — The decision framework is a favourite exam question. Practice scenario-based recommendations with justification.
6. **Data Quality** — Know the 6 dimensions. Practice identifying quality issues in given scenarios (like the bank loan example).
7. **Data Governance** — Know why it's different for AI vs traditional systems. GDPR "right to be forgotten" is a common question.
8. **Foundation Models** — Know what they are, 4 characteristics, 3-4 examples.
9. **Scenario questions** — Practice designing AI systems for given business problems. Always structure your answer using the 5-layer architecture.
10. **Open book (comprehensive)** — Bookmark key tables (comparison tables, architecture diagrams, decision frameworks) in your notes for quick lookup.

---

*End of AI Exam-Style Questions*
