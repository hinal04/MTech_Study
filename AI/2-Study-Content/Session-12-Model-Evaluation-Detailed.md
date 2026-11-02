# Session 12: Model Evaluation

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Dr. Minati Rath
>
> **References:** Class Slides, Contact Session 12
>
> **Contact Session:** 12 (Module 6: Validation and Deployment)

---

## Table of Contents

- [12.1 Evaluation as a Decision Problem](#121-evaluation-as-a-decision-problem)
- [12.2 Evaluation Contracts](#122-evaluation-contracts)
- [12.3 Validation Strategies and Leakage](#123-validation-strategies-and-leakage)
- [12.4 Classification Metrics](#124-classification-metrics)
- [12.5 Ranking and Regression Metrics](#125-ranking-and-regression-metrics)
- [12.6 Threshold Selection and Calibration](#126-threshold-selection-and-calibration)
- [12.7 Uncertainty and Confidence Intervals](#127-uncertainty-and-confidence-intervals)
- [12.8 Error Analysis and Slice Analysis](#128-error-analysis-and-slice-analysis)
- [12.9 ID vs. OOD Testing](#129-id-vs-ood-testing)
- [12.10 Benchmark Harness](#1210-benchmark-harness)
- [12.11 Model Acceptance Gate](#1211-model-acceptance-gate)
- [12.12 Advanced Decision Review](#1212-advanced-decision-review)
- [12.13 Key Takeaways](#1213-key-takeaways)

---

## Learning Objectives

At the end of this session, you should be able to:

- **Distinguish evaluation from model metrics** — Model accuracy ≠ business decision value
- **Design evaluation datasets that reflect deployment** — Avoid data leakage and invalid validation
- **Select metrics aligned with business costs** — Precision, recall, F1, AUC, business-specific metrics
- **Quantify model uncertainty** — Confidence intervals, statistical significance vs. business significance
- **Perform structured error and slice analysis** — Understand where and why models fail
- **Stress-test robustness** — ID vs. OOD testing, adversarial scenarios
- **Define acceptance gates** — Evidence needed to trust a model in production

---

## 12.1 Evaluation as a Decision Problem

### Model Metric ≠ Business Objective

A model achieving 95% accuracy does not automatically mean business success.

**Example: Credit Risk Prediction**
- Model predicts: P(Default | borrower) = 0.72
- Model has produced probability estimate
- Model has NOT decided: approve / request more info / deny

**Critical separation:**
```
Prediction layer: P(Default | borrower) = 0.72
    ↓
Decision layer: Approve / Request info / Deny (based on business rules)
    ↓
Business outcome: Approved loans / Default rate / Revenue / Customer satisfaction
```

These three layers must not collapse into one metric.

### Defining the Evaluation Contract

Before evaluating, explicitly define:

**1. Prediction Unit**
- Customer? Transaction? Device? Case? Document?
- Affects training data construction and evaluation split

**2. Label Definition and Maturity**
- What event constitutes positive label?
- When is label definitely known? (fraud confirmed after 30 days? 90 days?)
- How are ambiguous cases handled?

**3. Observation Window vs. Prediction Horizon**
- Observation window: Historical period for feature extraction
- Prediction horizon: Time gap between prediction and outcome
- Example: Use transactions from 2023 to predict defaults in Q1 2024

**4. Eligibility and Exclusions**
- Which examples included? (new customers? active accounts?)
- Which examples excluded? (high-value transactions? certain geographies?)
- Affects representativeness of evaluation

**5. Evaluation Population and Deployment Population**
- Is evaluation population representative of production?
- If populations differ, report performance on both
- Document gaps between evaluation and deployment

**6. Dataset and Version Identifiers**
- Which data version used? (dates, snapshots)
- Enables reproducibility and audit

### Population Representativeness

**Representativeness is a hypothesis, not an assumption.**

Check:
- **Prevalence**: Are positive/negative label rates similar?
- **Class mix**: Distribution of classes comparable?
- **Time distribution**: Are time periods balanced?
- **Geography**: Regional representation similar?
- **Product/channel**: Mix of business lines comparable?
- **Customer cohorts**: Similar distribution by size, age, segment?

Document inclusion/exclusion criteria explicitly.

---

## 12.2 Evaluation Contracts

### Subgroup Evaluation

Evaluate performance on critical **business and customer segments** separately.

**Example: Credit Scoring**
- Overall accuracy: 87%
- Segment: Small businesses: 82% (worse)
- Segment: Large enterprises: 92% (better)

Aggregate metric hides segment differences.

**Best practice:**
- Report performance by segment
- Include sample sizes
- Include uncertainty (confidence intervals)
- Examine false-positive and false-negative rates by segment
- Define acceptable performance gaps before reviewing results

**Material regressions escalate** even if aggregate improves:
- If small-business performance drops 5%, flag for investigation
- May indicate fairness concerns or data quality issues

### Validation Under Enterprise Constraints

**Real-world complications:**
- Repeated observations from same customer/device (not independent)
- Delayed labels with overlapping prediction horizons
- Geographic, business-unit, and population shifts
- Nested validation (hyperparameter selection itself can overfit)

**Solutions:**
- **Group split**: Prevent entity leakage by splitting by customer/device, not individual transactions
- **Temporal split**: Respect future deployment by using time-ordered splits
- **Embargo periods**: Define time ranges where leakage possible

---

## 12.3 Validation Strategies and Leakage

### Common Validation Strategies

**1. Random Split**
- Randomly partition data into train/validation/test
- Acceptable only when observations **sufficiently independent**
- Risk: If repeated customer observations, information may leak between sets

**2. Stratified Split**
- Preserve class proportions across splits
- Essential for imbalanced datasets
- Example: If 5% positive in full dataset, maintain 5% in each split

**3. Group Split**
- Prevent entity leakage
- If observations grouped by customer, split at customer level (all customer's data in one set)
- Example: Credit risk — all transactions from customer A in training, all from B in test

**4. Temporal Split**
- Respect time ordering; use past to predict future
- Train on data from months 1-12, test on months 13-14
- Respects deployment conditions (model predicts unseen future)

**5. Rolling/Forward Validation**
- For evolving systems, repeatedly advance time window
- Train on 1-12, test on 13; train on 2-13, test on 14; etc.
- Captures performance degradation over time
- Useful for detecting drift

**6. Nested Cross-Validation**
- Outer loop: Final evaluation
- Inner loop: Hyperparameter selection
- Prevents overfitting during model selection itself

### The Leakage Taxonomy

**Data leakage** undermines validity. Types:

**1. Target Leakage**
- Information about outcome used as feature
- Example: Predicting loan default with "flagged_for_investigation" (derived from outcome)

**2. Temporal Leakage**
- Using future information for prediction
- Example: Predicting Q1 performance with Q4 data collected Q2
- Information not available at prediction time

**3. Entity/Customer Leakage**
- Training and test sets contain same entity's data
- Model memorizes entity patterns rather than learning generalizable rules
- Solution: Group split by entity

**4. Aggregation Leakage**
- Aggregating before splitting causes information leakage
- Example: Compute customer-level average, then split — customer's data split across sets
- Solution: Split, then aggregate

**5. Preprocessing Leakage**
- Computing statistics on full dataset (train + test), then split
- Example: Normalize using min/max from full dataset
- Solution: Fit preprocessing on train, apply to test

**6. Feature-Store Leakage**
- Feature store includes post-outcome information
- Example: "churn_probability" computed after customer left
- Solution: Carefully version feature-store data, document observation windows

**7. Post-Outcome Leakage**
- Using information generated after outcome
- Example: Using intervention (customer called) as feature for predicting satisfaction
- Solution: Use only pre-outcome information

**8. Train/Test Contamination**
- Identical or near-identical records appear in both sets
- Solution: Deduplication and similarity checks

---

## 12.4 Classification Metrics

### Confusion Matrix Foundation

For binary classification:
- **True Positives (TP)**: Correctly predicted positive
- **True Negatives (TN)**: Correctly predicted negative
- **False Positives (FP)**: Incorrectly predicted positive
- **False Negatives (FN)**: Incorrectly predicted negative

### Key Metrics

**1. Accuracy**
```
Accuracy = (TP + TN) / (TP + TN + FP + FN)
```
Fraction of all predictions correct. **Misleading for imbalanced data.**

**2. Precision**
```
Precision = TP / (TP + FP)
```
Of predicted positives, fraction actually positive. Answer: "When model predicts positive, how often correct?"

**Use when**: False positives costly (e.g., spam detection — false positives annoy users)

**3. Recall (Sensitivity)**
```
Recall = TP / (TP + FN)
```
Of actual positives, fraction identified. Answer: "What fraction of real positives does model catch?"

**Use when**: False negatives costly (e.g., fraud detection — missing fraud causes financial loss)

**4. Specificity (True Negative Rate)**
```
Specificity = TN / (TN + FP)
```
Of actual negatives, fraction correctly identified.

**5. F1-Score**
```
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```
Harmonic mean of precision and recall. Balances both metrics. **Use when both types of errors costly.**

**6. ROC-AUC (Area Under Receiver Operating Characteristic Curve)**
- Plots True Positive Rate vs. False Positive Rate across thresholds
- AUC = 0.5: random model
- AUC = 1.0: perfect model
- AUC = 0.85: 85% probability model ranks random positive higher than random negative

**Advantage**: Threshold-independent; shows ranking quality

**Disadvantage**: Can hide poor performance at business operating point

**7. PR-AUC (Precision-Recall AUC)**
- Plots Precision vs. Recall across thresholds
- More informative than ROC-AUC for **rare positive classes**
- Directly reflects costs of false positives and false negatives

**8. Log Loss**
```
Log Loss = -1/n × Σ(y×log(p) + (1-y)×log(1-p))
```
Penalizes confident wrong predictions heavily. Measures **probability quality.**

**9. Brier Score**
```
Brier Score = 1/n × Σ(p - y)²
```
Mean squared error of predicted probabilities. Lower is better.

**10. Calibration**
Model prediction 0.8 should correspond to ~80% observed event rate among comparable cases.

Check with:
- **Reliability diagrams**: Plot predicted probability vs. observed frequency in bins
- **Calibration curves**: How predicted probability tracks observed outcomes
- **Brier score**: Combines calibration and discrimination

### Choosing Metrics

**Business context determines metric:**
- **Fraud detection**: Recall important (catch fraud); Precision important (minimize false reviews)
- **Medical diagnosis**: Recall critical (don't miss disease); FN cost >> FP cost
- **Spam filtering**: Precision critical (don't annoy users); FP cost >> FN cost
- **Search ranking**: AUC/NDCG (ranking quality); precision@K (visible results)

---

## 12.5 Ranking and Regression Metrics

### Ranking Metrics (for recommendation/search systems)

**1. Precision@K**
```
Precision@K = # relevant items in top K / K
```
Of top-K results, fraction relevant.

**2. Recall@K**
```
Recall@K = # relevant items in top K / total relevant items
```
What fraction of all relevant items appear in top K?

**3. MAP (Mean Average Precision)**
Averages precision across recall levels. Rewards getting relevant items early.

**4. NDCG (Normalized Discounted Cumulative Gain)**
Accounts for relevance grades and position discount (top positions valued more).
Normalized to 0-1 range for comparison.

**5. Lift and Cumulative Gain**
- **Cumulative Gain@K**: Sum of relevance scores for top K items
- **Lift**: Ratio vs. random baseline

**Critical**: Evaluate at actual business operating point (e.g., if display shows top-3, evaluate Precision@3, not top-100).

### Regression Metrics

**1. MAE (Mean Absolute Error)**
```
MAE = 1/n × Σ |y_actual - y_predicted|
```
Robust to outliers, interpretable (same units as target).

**2. RMSE (Root Mean Squared Error)**
```
RMSE = √(1/n × Σ (y_actual - y_predicted)²)
```
Emphasizes large errors (penalizes outliers more). More sensitive to outliers than MAE.

**3. MAPE (Mean Absolute Percentage Error)**
```
MAPE = 1/n × Σ |y_actual - y_predicted| / |y_actual| × 100%
```
**Problematic near zero** — division issues. Use with caution.

**4. Quantile Loss (Asymmetric)**
For forecasting with asymmetric costs:
- Overprediction costs different from underprediction
- Example: Inventory — stockout cost ≠ overstock cost
- Use quantile regression (predict conditional quantile, not mean)

**5. Business-Specific Loss**
Define loss matching business economics:
```
Loss = cost_overprediction × #overpredictions + cost_underprediction × #underpredictions
```

---

## 12.6 Threshold Selection and Calibration

### Threshold Determines Action

Threshold converts probability to decision:

```
If P(fraud) > 0.7: Block transaction
If 0.5 < P(fraud) ≤ 0.7: Review
If P(fraud) ≤ 0.5: Approve
```

Different thresholds produce different:
- True positive rates
- False positive rates
- Operating costs
- Business outcomes

### Threshold Selection Formula

**Expected cost** at threshold *t*:
```
Cost(t) = Cost_FP × #FP + Cost_FN × #FN + Cost_review × #reviewed
```

Threshold minimizing cost:
```
t* = Cost_FN / (Cost_FP + Cost_FN)
```

**Example:**
- Cost_FN (missed fraud) = ₹10,000
- Cost_FP (false decline) = ₹500
- t* = 10,000 / 10,500 ≈ 0.95

**Note**: This formula assumes costs dominate; capacity constraints may override.

### Capacity Constraints Override Math

Many threshold selection problems have hard constraints:

**Example: Manual review capacity**
- Investigators can review 100 transactions daily
- Model flags 200 suspicious transactions
- Even if mathematically optimal threshold suggests reviewing 180, capacity limits to 100

**Solution**: Use threshold that fills capacity:
- Sort by P(fraud) in descending order
- Take top 100 (fill capacity)
- Review those

Then evaluate precision@K, recall@K, expected loss given capacity constraint.

### Threshold Optimization Pitfall

**Never optimize threshold on test set without valid protocol.** Risk of overfitting to test examples.

**Valid approach:**
1. Train model on train set
2. Validate on separate validation set; select threshold there
3. Evaluate on held-out test set using validation-selected threshold
4. Report test-set performance

---

## 12.7 Uncertainty and Confidence Intervals

### Point Estimates Hide Variability

Model achieves 87% accuracy. Is this reliable?

**Questions:**
- How would accuracy change if different test set used?
- How different is 87% from 86%?
- Is improvement over baseline (85%) statistically significant?

### Bootstrap Confidence Intervals

**Bootstrap procedure:**
1. Resample test set WITH replacement
2. Compute metric on resampled data
3. Repeat 1000 times
4. Report percentiles (e.g., 2.5th and 97.5th for 95% CI)

**Result**: Confidence interval, e.g., 87% ± 2% (85%-89% at 95% confidence)

### Paired Bootstrap for Comparing Models

When comparing Model A vs. Model B on same test examples:

**Paired bootstrap:**
1. Resample examples WITH replacement
2. Compute accuracy for Model A and Model B on resampled set
3. Compute difference (A - B)
4. Repeat 1000 times
5. Report CI on difference

**Result**: Does CI include zero?
- If yes: No statistically significant difference
- If no: Significant difference, Model A likely better

### Statistical Significance ≠ Business Significance

**Example:**
- Model A: 87.0% accuracy
- Model B: 87.1% accuracy
- Sample size: 100,000 examples
- Difference: **statistically significant** (p < 0.001)
- But: 0.1% improvement = 100 more correct predictions
- Business value: $10,000 additional revenue
- Model B deployment cost: $50,000/year

**Conclusion**: Statistically significant ≠ business justifiable

**Always ask**: Does the improvement justify operational complexity?

---

## 12.8 Error Analysis and Slice Analysis

### Error Analysis: Understanding Failures

**When model makes wrong prediction, investigate why.**

**Categories of errors:**

**1. False Positives** (predicted positive, actually negative)
- Example: Predicting fraud on legitimate transaction
- Cost: Customer frustration, false decline

**2. False Negatives** (predicted negative, actually positive)
- Example: Missing actual fraud
- Cost: Financial loss

**3. High-Confidence Errors**
- Model very confident but wrong
- Often indicates systematic misunderstanding
- Example: Fraud model consistently misclassifies wire transfers as legitimate

**4. Boundary Cases**
- Predictions near threshold (e.g., 0.50-0.51 probability)
- Often ambiguous; manual review recommended

**5. Label Errors**
- Example shows inconsistent labels (multiple raters disagree)
- May indicate ambiguous case or labeling mistake

**6. Data-Quality Errors**
- Missing values, corrupted fields, outliers
- Example: Income = -1000 (data entry error)

**7. Feature Failures**
- Feature not available or meaningless in certain contexts
- Example: "months since last purchase" = NULL for new customers

**8. Model-Capacity Failures**
- Model architecture fundamentally limited
- Example: Linear model on nonlinear problem

**9. Long-Tail Failures**
- Model fails on rare but important examples
- Example: Medical model fails on rare disease presentation

### Error Analysis Workflow

1. **Categorize failures**: Bucket errors into types (FP, FN, etc.)
2. **Analyze patterns**: Are FNs concentrated in certain product category?
3. **Distinguish root causes**: Data problem vs. modeling problem
4. **Quantify severity**: How much does each type hurt business?
5. **Prioritize fixes**: Focus on highest-impact error types
6. **Develop solutions**: Better features, more data, different architecture?

### Slice Analysis

Evaluate performance across meaningful slices:

**By geography**: How does model perform in each region?
- North: 89% accuracy
- South: 82% accuracy (degraded)

**By product**: Performance by product line
- Smartphones: 91% accuracy
- Tablets: 85% accuracy (worse)

**By risk band**: Performance by predicted risk
- High risk (P>0.8): 95% precision
- Low risk (P<0.2): 88% precision

**By cohort**: Performance by customer segment
- New customers: 80% accuracy
- Loyal customers: 90% accuracy

**Best practices:**
- Compare both performance AND sample size
- Use confidence intervals (especially for small slices)
- Investigate systematic degradation (not just noise)
- Avoid declaring issues from noise alone

---

## 12.9 ID vs. OOD Testing

### In-Distribution (ID) Testing

**In-Distribution (ID)**: Test data from same distribution as training data.

Example: Train on 2023 Q1-Q2 transactions, test on 2023 Q2 transactions (similar distribution).

**Result**: Model's performance on "typical" data it was trained to handle.

### Out-of-Distribution (OOD) Testing

**Out-of-Distribution (OOD)**: Test data from different distribution than training.

Examples:
- Train on 2023 transactions, test on 2024 (temporal shift)
- Train on images from Camera V1, test on Camera V2 (sensor shift)
- Train on high-volume customers, test on rare customers (domain shift)

**Result**: Model's robustness to distribution shifts.

### Why OOD Testing Matters

Many models perform excellently on ID data but poorly on OOD data. Examples:

**Autonomous driving:**
- ID: Clear daylight, dry roads (training condition)
- OOD: Heavy rain, night (deployment reality)

**Fraud detection:**
- ID: Historical fraud patterns
- OOD: New fraud tactics (evolve over time)

**Medical imaging:**
- ID: High-quality images from training hospital
- OOD: Lower-quality images from different hospital

### Designing OOD Tests

**Identify likely distribution shifts:**
1. Temporal shifts (model deployed 6+ months after training)
2. Geographic shifts (trained in one region, deployed in another)
3. Population shifts (new customer segment introduced)
4. Sensor/equipment shifts (camera upgrade, system change)
5. Adversarial scenarios (intentional distribution shift)

**Create OOD test sets:**
- Collect actual OOD data if possible
- Simulate distribution shifts (brightness, rotation, corruption)
- Stress-test edge cases (extreme values, rare classes)

**Evaluate model:**
- Report ID and OOD performance separately
- Quantify performance drop
- Investigate causes of degradation

---

## 12.10 Benchmark Harness

### Purpose of Benchmark Harness

Benchmark harness enables **fair comparison** between candidate models.

Prevents hidden advantages for particular models due to different evaluation protocols.

### Components of Harness

**1. Meaningful Incumbent Baseline**
- Establish current production model performance
- OR meaningful heuristic baseline (e.g., always predicting majority class)
- Candidates must beat baseline to justify change

**2. Same Evaluation Data**
- Identical train/validation/test splits
- Same data preprocessing
- Same label definitions
- Same observation windows

**3. Same Preprocessing Contract**
- Identical feature engineering
- Same handling of missing values
- Same scaling/normalization
- Version control on preprocessing code

**4. Same Metric Implementation**
- Identical metric definitions
- Same metric computation code
- Tested for correctness

**5. Same Threshold Protocol**
- Select threshold on validation set
- Apply same threshold across all candidates
- OR use optimal threshold per candidate, but report both

**6. Version Control**
- Dataset versions with dates, snapshots
- Model versions
- Feature/config versions
- Enable reproducibility

### Measurement Best Practices

**Report comprehensive metrics:**
- Mean performance
- Fold variability (if cross-validation used)
- Inference latency
- Resource consumption (memory, CPU)
- Training time

**Control variables:**
- Same random seeds for reproducibility
- Same hardware for fair latency comparison
- Same batch sizes for consistent memory usage

**Prevent repeated holdout reuse:**
- Never train multiple models, evaluate on test, select best
- Use validation set for selection, test for evaluation
- Report held-out test performance only once per model

---

## 12.11 Model Acceptance Gate

### Decision Checklist Before Deployment

**1. Statistical Validity**
- [ ] Data quality meets standards (missing <5%, outliers handled)
- [ ] No data leakage detected
- [ ] Validation strategy appropriate (stratified, temporal, group splits as needed)
- [ ] Test set representative of deployment population
- [ ] Confidence intervals computed; improvement statistically significant

**2. Business Utility**
- [ ] Threshold justified (business cost-benefit or capacity constraints)
- [ ] Expected business impact quantified (revenue, cost reduction, risk)
- [ ] Justifies operational complexity and deployment cost
- [ ] Clear decision rule: when model predicts X, what action taken?

**3. Robustness and Calibration**
- [ ] Model well-calibrated (predicted probabilities match observed frequencies)
- [ ] Performance maintained across critical segments (no major regressions)
- [ ] ID and OOD performance both acceptable
- [ ] Sensitivity analysis: performance under reasonable assumption changes

**4. Reproducibility and Artifacts**
- [ ] Code reproducible (version control, dependency management)
- [ ] Model checkpoints versioned
- [ ] Training data snapshot reproducible
- [ ] Evaluation scripts version controlled
- [ ] Able to retrain from scratch deterministically

**5. Documented Limitations and Risks**
- [ ] Known failure modes documented (edge cases, segments where weak)
- [ ] Residual risks identified (assumptions that might fail)
- [ ] Monitoring plan for deployment (drift, performance degradation)
- [ ] Approval ownership clear (who approved this model for production?)

### Acceptance Criteria

Model approved for deployment only if:
1. Passes statistical validity checks
2. Demonstrates clear business utility
3. Meets robustness thresholds
4. Artifacts reproducible
5. Limitations documented
6. Appropriate stakeholders approve (data science, business, risk)

---

## 12.12 Advanced Decision Review

### Critical Questions Before Deployment

**Population Representativeness:**
- Is the evaluation population truly representative?
- What populations excluded? (e.g., new customers, rare regions)
- What would performance be on excluded populations?

**Leakage Risks:**
- What residual leakage might remain despite precautions?
- Are there post-outcome features used indirectly?
- Could deployment decisions indirectly cause leakage (creating feedback loops)?

**Metric-to-Action Mapping:**
- Which metric truly maps to the business decision?
- Does AUC capture business operating point?
- Should decision be threshold-based or ranking-based?

**Model Comparison Uncertainty:**
- How confident in comparison between candidates?
- Confidence intervals overlap? Benefit marginal?
- Is incremental improvement worth operational complexity?

**Segment Safety:**
- Which slices unsafe? (performance too low)
- Are fairness concerns addressed?
- Acceptable to deploy knowing certain populations underserved?

**Robustness Gaps:**
- What robustness evidence missing?
- Haven't tested: adversarial attacks? Seasonal shifts? New product types?
- Risk acceptable without that evidence?

**Monitoring Plan:**
- What monitoring must exist after deployment?
- What triggers model rollback?
- How quickly can degradation be detected and acted upon?
- Who owns monitoring and response?

---

## 12.13 Key Takeaways

1. **Evaluation is experimental design**, not final score calculation

2. **Validation strategy can matter more than model choice** — Prevent data leakage above all

3. **Metrics encode business assumptions** — Choose metrics reflecting business costs and decisions

4. **Error analysis explains why models succeed or fail** — Understand failure modes before deployment

5. **Uncertainty prevents false confidence** — Report confidence intervals, not point estimates alone

6. **Statistical significance ≠ business significance** — Improvement must justify complexity and cost

7. **Segment performance matters** — Regressions on important segments matter even if aggregate improves

8. **Representativeness is a hypothesis** — Test whether evaluation population matches deployment

9. **Threshold selection is a business decision** — Don't collapse prediction, decision, and outcome into one metric

10. **Robust models tested on ID and OOD data** — Know performance across expected shifts and edge cases

11. **Benchmark harness enables fair comparison** — Same data, same metrics, same protocol

12. **A model is only "good" relative to a decision** — Good for what? Under what conditions? For whom?

---
