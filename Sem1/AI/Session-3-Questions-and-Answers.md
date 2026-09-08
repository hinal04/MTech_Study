# Session 3: AI System Architecture — Questions & Answers

> 4 questions covering Session 3 topics.

---

### Q1. Describe the five-layer AI system architecture. What does each layer do?

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

### Q2. Walk through how a recommendation request flows through the AI system architecture.

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

### Q3. What is data drift? Why is monitoring essential for AI systems?

**Answer:**

**Data drift** occurs when the distribution of incoming production data changes compared to the training data. The model was trained on one distribution but is now seeing a different one — its predictions degrade.

**Types of drift:**
- **Data drift (covariate shift):** Input feature distributions change. Example: A fraud model trained on weekday transactions starts seeing weekend patterns.
- **Concept drift:** The relationship between features and target changes. Example: What constitutes "fraud" changes as fraudsters develop new techniques.
- **Prediction drift:** Model output distribution shifts. Example: A spam filter suddenly classifies 50% of emails as spam (previously 5%).

**Why monitoring is essential:** Unlike traditional software bugs (which crash visibly), ML drift causes the model to **silently serve wrong predictions**. Without monitoring, you won't know until business metrics drop — which could be weeks later.

---

### Q4. What is a model card? Why is documentation important in the AI ecosystem?

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

---

*End of Session 3: AI System Architecture Questions & Answers*
