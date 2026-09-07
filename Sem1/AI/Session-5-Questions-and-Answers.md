# Session 5: Data and Feature Engineering — Questions & Answers

> 12 questions covering: Structured/semi-structured/unstructured data, data sources, data quality (6 dimensions), common ML data issues, data governance, privacy, compliance.

---

### Q1. Compare structured, semi-structured, and unstructured data with examples relevant to AI.

**Answer:**

| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Schema** | Fixed, predefined (rows/columns) | Flexible, self-describing (tags/keys) | None |
| **Storage** | RDBMS, columnar stores | Document DBs, data lakes | Object storage, media servers |
| **Query method** | SQL | JSON queries, path expressions | AI models, vector search, full-text search |
| **Feature engineering** | Straightforward (aggregations, ratios) | Requires parsing and flattening | Requires AI to extract features |
| **% of enterprise data** | ~20% | ~10% | ~70% |
| **AI example** | Customer transactions → fraud detection (XGBoost) | JSON event logs → user behaviour prediction | Medical images → tumour detection (CNN) |

**Key insight:** Modern AI increasingly uses **multimodal data** — combining structured (patient records) + unstructured (medical images) + semi-structured (doctor's notes in JSON) in a single system.

---

### Q2. Why is data considered more important than the model in AI systems?

**Answer:**

"Better data almost always beats a better model." Here's why:

1. **Data defines behaviour:** In ML, the model learns FROM the data. The data IS the logic. A simple logistic regression on clean, relevant data will outperform a complex neural network on noisy, irrelevant data.

2. **80% rule:** Approximately 80% of a typical ML project's time goes to data collection, cleaning, labelling, and preparation — not model building.

3. **Google's finding:** Research at Google showed that fixing data quality issues improved model performance more than switching to more complex architectures.

4. **Silent failures:** Unlike code bugs (which crash), data bugs cause the model to produce subtly wrong predictions that are hard to detect. A training set with 5% wrong labels doesn't crash — it silently produces a model that's 5% less accurate.

5. **Data compounds:** More high-quality data continuously improves the model. A better algorithm with the same bad data hits a ceiling.

---

### Q3. List 5 internal and 5 external data sources for AI systems.

**Answer:**

**Internal sources (from within the organisation):**

| Source | What it provides |
|---|---|
| Transactional databases | Orders, payments, customer records, inventory |
| Application logs | User behaviour, errors, performance metrics |
| CRM systems | Customer interactions, support tickets, sales data |
| Data warehouse/lake | Historical, aggregated, transformed analytics data |
| IoT/sensor data | Real-time readings from connected devices |

**External sources (from outside):**

| Source | What it provides |
|---|---|
| Public datasets | ImageNet, Common Crawl, Wikipedia, government open data |
| Third-party providers | Demographics (Experian), weather (AccuWeather), location (Foursquare) |
| APIs | Social media (Twitter/X), maps (Google), stock markets |
| Web scraping | Competitor prices, job postings, news (legal/ethical considerations apply) |
| Pre-trained model outputs | Embeddings from CLIP, synthetic data from GPT-4 |

---

### Q4. Explain the six dimensions of data quality. Give an AI-specific example for each.

**Answer:**

| Dimension | Definition | AI-Specific Example |
|---|---|---|
| **Accuracy** | Values correctly represent reality. | Customer age recorded as 150 → model learns wrong demographic patterns. |
| **Completeness** | No missing values where they should exist. | 30% of rows missing "income" → model either drops these rows (losing data) or imputes incorrectly. |
| **Consistency** | Same fact represented the same way everywhere. | "United States" in one table, "US" in another → join failures, duplicate features. |
| **Timeliness** | Data reflects current reality. | Inventory data 24 hours old → recommendation model suggests out-of-stock items. |
| **Validity** | Data conforms to defined formats and constraints. | Phone number field containing email addresses → feature parsing produces garbage. |
| **Uniqueness** | No unintended duplicates. | Same customer appears 3 times → model over-weights their behaviour, training data inflated. |

---

### Q5. What is data leakage? Why is it one of the most dangerous data quality issues in ML?

**Answer:**

**Data leakage** occurs when information from the test set or from the future "leaks" into the training data, giving the model access to information it wouldn't have at prediction time.

**Why it's dangerous:**
- The model appears to perform amazingly during evaluation (high accuracy on test set).
- But it completely fails in production because the leaked feature doesn't exist at prediction time.
- It's hard to detect because metrics look great — you only discover it when the deployed model underperforms.

**Example:**

```
Credit scoring model:
Feature: "credit_bureau_score"

Problem: The credit bureau updates the score AFTER the loan 
decision is made. During training, this score reflects the 
outcome, not the input.

Training: Model learns "high bureau score → no default" → 99% accuracy!
Production: Bureau score not available at decision time → model fails.
```

**How to prevent:**
- Strict temporal ordering — only use features available at prediction time.
- Careful train/test splitting — split by time, not randomly, for temporal data.
- Feature audit — for each feature, ask "would this be available when making a real prediction?"

---

### Q6. What is class imbalance? How does it affect ML models and how can it be addressed?

**Answer:**

**Class imbalance** occurs when one class has far more examples than others in the training data.

**Example:** In fraud detection, 98% of transactions are legitimate and only 2% are fraudulent.

**How it affects models:**
- The model learns to predict the majority class for everything — "not fraud" for every transaction.
- This gives 98% accuracy but catches zero actual fraud — completely useless.
- Standard metrics (accuracy) are misleading. 98% accuracy sounds great but the model is worthless.

**Solutions:**

| Technique | How it works |
|---|---|
| **Oversampling (SMOTE)** | Synthetically generate more examples of the minority class. |
| **Undersampling** | Randomly remove examples from the majority class. |
| **Class weights** | Tell the model that misclassifying a fraud case costs 50x more than misclassifying a legitimate one. |
| **Threshold tuning** | Instead of using 0.5 as the classification threshold, use a lower value (e.g. 0.1) to catch more positives. |
| **Better metrics** | Use precision, recall, F1, AUC-PR instead of accuracy. |
| **Ensemble methods** | Train multiple models on balanced subsets. |

---

### Q7. What is data governance? Why does it matter specifically for AI systems?

**Answer:**

**Data governance** is the framework of policies, processes, and standards ensuring data is managed securely, consistently, and in compliance with regulations.

**Why it matters specifically for AI:**

| AI-Specific Concern | Why governance is needed |
|---|---|
| **Models trained on unauthorized data** | Legal liability. GDPR fines up to 4% of global revenue. |
| **Models reproducing biases** | Discriminatory predictions (denying loans based on race proxies). Reputational and legal risk. |
| **No audit trail** | Can't explain or defend model decisions to regulators. Required in finance (Basel) and healthcare (HIPAA). |
| **Inconsistent data across teams** | Different teams train on different "truth," producing contradictory models. |
| **Model memorises PII** | LLMs can memorise and regurgitate personal data from training. Privacy violation. |

In traditional analytics, poor governance means wrong reports. In AI, poor governance means wrong automated decisions affecting real people at scale.

---

### Q8. Explain the data governance lifecycle for AI systems.

**Answer:**

```
1. DISCOVER  → What data exists? Where? Who owns it?        (Data Catalog)
2. CLASSIFY  → Is it PII? Sensitive? Regulated?              (Classification)
3. PROTECT   → Encrypt, anonymise, control access.           (Security)
4. COMPLY    → GDPR, HIPAA, India's DPDP Act. Consent mgmt. (Compliance)
5. MONITOR   → Quality checks, access audit logs, lineage.   (Observability)
6. GOVERN    → Policies, standards, ownership, stewardship.  (People + Process)
```

**Key components:**

| Component | Purpose |
|---|---|
| **Data ownership** | Who is responsible for each dataset. Accountability for quality. |
| **Access control** | Who can access what, for what purpose. Prevent unauthorized ML training. |
| **Data lineage** | Where data came from, how transformed, where used. Trace bad predictions to root cause. |
| **Data cataloging** | Central inventory of all data assets. Data scientists discover data without asking 10 people. |
| **Privacy compliance** | GDPR, HIPAA, CCPA, DPDP Act. Consent, anonymisation, right to deletion. |
| **Retention policies** | How long data kept. Regulatory requirements. Stale data degrades models. |
| **Quality standards** | Defined thresholds: "Completeness > 95%, Freshness < 1 hour." |

---

### Q9. What is sampling bias? Give an example of how it affects AI systems.

**Answer:**

**Sampling bias** occurs when training data is not representative of the real-world population the model will serve.

**Classic example — Facial recognition:**
- Training data contained mostly light-skinned faces (majority from Western internet datasets).
- The model performed well on light-skinned faces (90%+ accuracy) but poorly on dark-skinned faces (65% accuracy).
- When deployed in a diverse population, the model systematically failed for underrepresented groups.

**Why it happens:**
- Data collected from convenience sources (internet scraping favours certain demographics).
- Historical biases in data (loan approval data from an era when certain groups were discriminated against).
- Survivorship bias (only successful cases are recorded, failures are lost).

**How to mitigate:**
- Audit training data for demographic representation.
- Collect data intentionally from underrepresented groups.
- Evaluate model performance per subgroup (not just overall accuracy).
- Use fairness metrics (equal opportunity, demographic parity).

---

### Q10. What is the "right to be forgotten" and how does it affect AI systems?

**Answer:**

Under **GDPR Article 17**, individuals can request that an organisation delete all their personal data. This is the "right to be forgotten" (right to erasure).

**The AI challenge:** If a user's data was used to train an ML model, the model has already "learned" from that data. Simply deleting the data from the database doesn't remove its influence from the model's weights.

**Solutions:**

| Approach | How it works | Trade-off |
|---|---|---|
| **Full retraining** | Delete the data, retrain the model from scratch without it. | Expensive (days/weeks of GPU compute for large models). But guaranteed. |
| **Machine unlearning** | Approximate techniques to remove a data point's influence without full retraining. | Faster but imperfect — influence may not be fully removed. Active research area. |
| **Data sharding** | Train on shards. To "forget" a user, retrain only their shard. | Faster retraining. But shard boundaries must be designed upfront. |
| **Differential privacy** | Add noise during training so no individual data point significantly influences the model. | Prevents memorisation proactively. But reduces model accuracy. |

---

### Q11. Compare the data needs of traditional software vs AI/ML systems.

**Answer:**

| Aspect | Traditional Software | AI/ML System |
|---|---|---|
| **Role of data** | Data is input to be processed by code. | Data IS the logic — model learns from data. |
| **Data quality impact** | Bad data → wrong output for that input. | Bad data → wrong model → wrong output for ALL inputs. |
| **Data volume** | Moderate — process what's needed. | Large — more data generally improves the model. |
| **Data freshness** | Real-time data needed for current transactions. | Training data can be historical. Serving data must be fresh. |
| **Data versioning** | Rarely version data (version code instead). | Critical — must version data AND code to reproduce results. |
| **Testing** | Test with known inputs and expected outputs. | Test with held-out data, but can't enumerate all edge cases. |
| **Failure mode** | Crashes, errors, wrong output for specific input. | Silently degrades — wrong predictions without obvious errors. |

---

### Q12. A bank is building a loan approval AI system. Identify 4 data quality issues that could arise and explain their impact.

**Answer:**

| Issue | What happens | Impact on loan model |
|---|---|---|
| **1. Missing values (completeness)** | 15% of applicants missing "employment_years." Imputed with median (5 years). | But missing likely means unemployed → should be 0. Model underestimates risk for unemployed applicants. Approves risky loans. |
| **2. Data leakage** | "credit_bureau_score" included as a feature. But it's updated AFTER the loan decision. | Model uses future information → looks amazing in testing (99% accuracy) → completely fails in production (feature unavailable at decision time). |
| **3. Sampling bias** | Training data from 2018-2023. Bank only served urban professionals until 2022, then expanded to rural areas. | Rural applicants underrepresented → model performs poorly for rural customers. Unfair denials or risky approvals for new segment. |
| **4. Class imbalance** | Only 2% of loans default. | Model predicts "no default" for everyone → 98% accuracy but catches zero actual defaults. Useless for risk management. Need to use class weights, oversampling, or precision/recall metrics. |

Each issue silently degrades the model without obvious errors — the model doesn't crash, it just makes wrong decisions that affect real people's lives.

---

*End of Session 5 Questions & Answers*
