# AI Systems — Exam-Style Questions

> BITS Pilani — **SS ZG662: Introduction to AI Systems**
>
> Covers: Sessions 1–8 (Module 1: Foundations of AI Systems + Module 2: Data and Feature Engineering)

---

## Section A: Short Answer Questions (2-3 marks each)

### Session 1: Introduction to AI

**Q1.** Define Artificial Intelligence. How is it different from traditional software?

**Q2.** Draw the nested relationship between AI, Machine Learning, and Deep Learning. Give one example of each.

**Q3.** Why is the AI system lifecycle circular rather than linear? Give one real-world reason.

**Q4.** Name the 6 components of an AI system. Which component typically accounts for less than 5% of the total system?

**Q5.** What is "training-serving skew"? Why is it dangerous?

### Session 2: Categories of AI Systems

**Q6.** List the four types of predictive AI tasks with one example each.

**Q7.** What is a "hallucination" in the context of Generative AI? Why does it happen?

**Q8.** Explain the cold start problem in recommender systems. How does Netflix solve it for new users?

**Q9.** What is RAG (Retrieval-Augmented Generation)? How does it reduce hallucinations?

**Q10.** List the SAE levels of vehicle autonomy (0-5) with one real example for Level 2 and Level 4.

### Session 3: AI System Architecture

**Q11.** Name the five layers of an AI system architecture. Give one tool example for each layer.

**Q12.** What is "data drift"? Give an example of how data drift can silently degrade a fraud detection model.

**Q13.** Why did Zillow lose $881 million? Which architecture layer failed?

**Q14.** What is the difference between a Feature Store and a Data Warehouse in the context of AI systems?

### Session 4: AI Ecosystem and Marketplaces

**Q15.** Define "foundation model." List any three characteristics.

**Q16.** Name three benefits of open-source AI models over proprietary API-based models.

**Q17.** What is Hugging Face? Why is it called the "GitHub of AI"?

**Q18.** When should a company use an API vs fine-tune an open-source model vs build from scratch?

### Session 5: Data and Feature Engineering

**Q19.** Compare structured, semi-structured, and unstructured data with one example each.

**Q20.** List the six dimensions of data quality with a one-line definition of each.

**Q21.** What is "data leakage" in ML? Give an example.

**Q22.** Name four key components of data governance and explain why they matter for AI systems.

**Q23.** How does India's DPDP Act (2023) impact AI systems? Name two specific requirements.

### Session 6: Data Engineering Pipelines

**Q24.** What is a data pipeline? Draw its basic structure (Source → Extract → Transform → Load).

**Q25.** Compare ETL and ELT in a table with at least 4 differences.

**Q26.** When would you use batch processing vs streaming processing? Give one use case for each.

**Q27.** What is Lambda Architecture? Why do most real-world companies use both batch and streaming?

**Q28.** Compare Data Lake, Data Warehouse, and Data Lakehouse with at least 3 differences.

### Session 7: Feature Engineering

**Q29.** What is feature engineering? Why does it have a bigger impact on model accuracy than model selection?

**Q30.** Explain any three feature transformation techniques with examples.

**Q31.** What is the "curse of dimensionality"? How does feature selection help?

**Q32.** Compare filter, wrapper, and embedded methods of feature selection.

**Q33.** What is an embedding? Why is it useful for ML? Give one example.

### Session 8: Feature Stores

**Q34.** What is a feature store? Name any two problems it solves.

**Q35.** Compare offline features and online features in a table with at least 4 differences.

**Q36.** What is training-serving skew? Name its four types.

**Q37.** Explain the two-stage recommendation architecture (candidate generation + ranking). Why not use just one stage?

---

## Section B: Long Answer Questions (5-8 marks each)

### Session 1-2: AI Foundations

**Q38.** Compare AI, Machine Learning, and Deep Learning with respect to: (a) how they work, (b) data requirements, (c) feature engineering, (d) compute requirements, (e) examples. Use a table format. Then explain with a spam detection example how each approach would solve the problem differently. *[8 marks]*

**Q39.** Explain the 6 stages of the AI system lifecycle with a real-world example (e.g., Swiggy delivery time prediction or Netflix recommendations). For each stage, mention: what happens, key activities, and common pitfalls. *[8 marks]*

**Q40.** Describe any four categories of AI systems from the following: Predictive AI, Generative AI, Recommender Systems, Conversational AI, Computer Vision, Autonomous Systems. For each, explain: (a) what it does, (b) how it works, (c) one real-world case study with specific company name. *[8 marks]*

### Session 2: Case Studies

**Q41.** Explain how ChatGPT works in three stages (pre-training, RLHF fine-tuning, serving). What system components exist beyond the model itself? *[5 marks]*

**Q42.** Describe the Netflix recommendation system covering: (a) data signals used, (b) multiple models for different UI sections, (c) personalised thumbnails, (d) offline + online processing. Why does Netflix say 80% of content watched comes from recommendations? *[5 marks]*

**Q43.** Explain the Waymo autonomous vehicle system covering: (a) sensors used and their purpose, (b) AI tasks (detection, tracking, prediction, planning, control), (c) why Level 5 autonomy doesn't exist yet. *[5 marks]*

### Session 3: Architecture

**Q44.** Describe the five-layer AI system architecture. For each layer, explain: (a) its purpose, (b) key components, (c) one popular tool, (d) one live company example. *[8 marks]*

**Q45.** Trace a complete request through all 5 layers of an AI system for a food delivery ETA prediction (like Swiggy). Show what happens at each layer from "user places order" to "user sees estimated delivery time" to "system monitors prediction accuracy." *[8 marks]*

**Q46.** Explain why monitoring is critical for AI systems but less critical for traditional software. Use the Zillow case study ($881M loss) to illustrate what happens without proper monitoring. What metrics should be monitored? *[5 marks]*

### Session 4: Ecosystem

**Q47.** A startup CTO needs to decide between using an API (GPT-4), fine-tuning an open-source model (LLaMA), or building from scratch for the following three scenarios. Recommend and justify your answer for each:
(a) A customer support chatbot for a small e-commerce company
(b) A fraud detection system for a bank
(c) A medical imaging AI for a hospital chain
*[6 marks]*

**Q48.** Explain the concept of foundation models covering: (a) definition and key characteristics, (b) why they are expensive to train, (c) how they are made accessible (APIs + fine-tuning), (d) the trend from APIs to open-source. Name at least 4 notable foundation models. *[5 marks]*

### Session 5: Data Quality & Governance

**Q49.** A bank is building a credit scoring model and discovers these data quality issues:
(a) 15% of applicants have missing "employment_years"
(b) Training data includes "credit_bureau_score" which is updated after loan decisions
(c) Training data only covers urban professionals (not rural applicants)
(d) Only 2% of loans default

For each issue, identify: the data quality problem type, why it's harmful, and how to fix it. *[8 marks]*

**Q50.** Explain data governance for AI systems covering: (a) why it's more important for AI than traditional analytics, (b) at least 5 key components, (c) AI-specific challenges (consent for ML, right to be forgotten, bias auditing), (d) the Cambridge Analytica case as an example of governance failure. *[8 marks]*

### Session 6: Data Pipelines

**Q51.** Compare ETL and ELT approaches with respect to: (a) order of operations, (b) where transformation happens, (c) raw data retention, (d) flexibility, (e) best use cases, (f) example tools. Which approach is more common in modern AI systems and why? *[6 marks]*

**Q52.** Compare batch processing and streaming processing with respect to: (a) when data is processed, (b) latency, (c) complexity, (d) cost, (e) use cases. Then explain Lambda Architecture and why most companies use both approaches together. Give a live example. *[6 marks]*

**Q53.** Design a complete data pipeline for an e-commerce recommendation system (like Myntra). Cover: (a) data sources, (b) streaming pipeline components, (c) batch pipeline components, (d) storage layer (lake vs warehouse), (e) feature store integration, (f) monitoring. Name specific tools for each component. *[8 marks]*

### Session 7: Feature Engineering

**Q54.** You are building a fraud detection model for credit card transactions. The raw data includes: card_id, merchant_name, merchant_category, amount, timestamp, location_lat, location_lng, is_online.

Design at least 15 features across these categories:
(a) Transaction-level features
(b) Velocity features (speed of transactions)
(c) Location features
(d) User profile features

For each feature, explain what it captures and why it's useful for fraud detection. *[8 marks]*

**Q55.** Explain the following feature transformation techniques with examples: (a) Min-Max Scaling, (b) Standard Scaling, (c) Log Transformation, (d) One-Hot Encoding, (e) Label Encoding. For encoding, explain when to use one-hot vs label encoding and why using label encoding for nominal data is wrong. *[6 marks]*

**Q56.** Compare filter, wrapper, and embedded methods of feature selection. For each, explain: (a) how it works, (b) advantages and disadvantages, (c) one specific technique. Then explain why using all available features is not always better — use the "curse of dimensionality" concept. *[6 marks]*

**Q57.** What are embeddings? Explain with the example of word embeddings (Word2Vec). How do embeddings capture similarity? Show how "king - man + woman ≈ queen" works. List three types of embeddings used in AI systems with a live example for each. *[5 marks]*

### Session 8: Feature Stores & Real-Time Systems

**Q58.** Explain what a feature store is and why it's needed. Cover: (a) the 5 problems it solves, (b) architecture (offline store, online store, feature registry, feature pipelines), (c) how it prevents training-serving skew, (d) name 3 popular feature store tools. *[6 marks]*

**Q59.** Compare offline features and online features with respect to: (a) when they are computed, (b) what they represent, (c) latency requirements, (d) storage technology, (e) use cases. Then show with a Swiggy ETA prediction example how both offline and online features are combined for a single prediction. *[6 marks]*

**Q60.** Explain the four types of training-serving skew with one example each: (a) feature computation skew, (b) data distribution skew, (c) feature availability skew, (d) time-travel skew. How does a feature store prevent these? Use the Zillow case study to illustrate the real-world impact of skew. *[6 marks]*

**Q61.** Describe the two-stage architecture of a real-time recommendation system:
(a) Stage 1: Candidate Generation — what it does, how ANN search works, input/output sizes
(b) Stage 2: Ranking — what it does, what features it uses, why it's more accurate
(c) Why can't we just use the ranking model on all items?
(d) Trace a complete request through this architecture (user opens app → sees recommendations)
Use a real company example (Netflix, Spotify, or Amazon). *[8 marks]*

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

**Q64.** Compare the following pairs. For each pair, explain both concepts, their differences, and when to use which:

(a) ETL vs ELT *[3 marks]*
(b) Data Lake vs Data Warehouse *[3 marks]*
(c) Batch Processing vs Streaming Processing *[3 marks]*
(d) Offline Features vs Online Features *[3 marks]*
(e) Collaborative Filtering vs Content-Based Filtering *[3 marks]*

**Q65.** A healthcare startup wants to build an AI system that analyses chest X-rays to detect pneumonia. Answer:

(a) What type of AI system is this? What AI technique (CNN, NLP, etc.) would you use?
(b) What data do you need? What are the data quality concerns?
(c) Should you build from scratch, use API, or fine-tune open-source? Justify.
(d) What data governance challenges exist? (Patient privacy, consent, HIPAA/DPDP Act)
(e) Design the 5-layer architecture for this system
(f) What monitoring metrics are critical? (Consider: this is a safety-critical application)
(g) What could go wrong if the model has data drift? Give a concrete scenario.
*[10 marks]*

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
