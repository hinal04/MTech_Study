# Assignment 1 — Q2: Hospital 30-Day Readmission Prediction

> **Course:** SS ZG662 — Introduction to AI Systems (BITS Pilani)

---

## Context

A large hospital wants to predict whether a patient will be **readmitted within 30 days** after discharge. The data science team reports **97% accuracy** on the test set. During review, the feature **"Number of follow-up appointments scheduled after discharge"** is found in the model.

---

## Question 1: Why "Number of Follow-Up Appointments Scheduled After Discharge" Could Be Target Leakage

### Answer

**Target leakage** occurs when the model uses information during training that would **not be available at prediction time** in production.

The prediction is supposed to be made **at the moment of discharge**. However, "number of follow-up appointments scheduled after discharge" is problematic for two reasons:

**1. Temporal Leakage (Primary Issue):**
Follow-up appointments are typically scheduled **after** the discharge decision is made — sometimes hours or days later. At the exact moment the model needs to make its prediction (discharge time), this information does not yet exist. Including it means the model is using **future information** to predict the future.

**2. Causal / Label Leakage (Secondary Issue):**
The number of follow-up appointments is often **causally related to the target**. Doctors schedule **more follow-up appointments** for patients they believe are at **higher risk of readmission**. This means:
- High-risk patients → more follow-ups scheduled → model learns "many follow-ups = readmission likely"
- The feature is essentially a **proxy for the physician's own risk assessment**, not an independent predictor

The feature acts as a **leak of the physician's implicit prediction** into the model's inputs. The model isn't learning to predict readmission from clinical factors — it's learning to copy the doctor's judgment, which is already encoded in the follow-up count.

**Why the team's argument ("it's in the database") is wrong:**
Being "available in the database" does not mean it is available **at prediction time**. A database stores data across all time points. The model must only use data available **at the specific moment the prediction would be generated in production**.

---

## Question 2: At Least 8 Features That Could Introduce Leakage

| # | Feature | When Information Becomes Available | Why It Matters |
|---|---------|----------------------------------|----------------|
| 1 | **Follow-up appointments scheduled after discharge** | After discharge (hours/days later) | Not available at discharge. Number correlates with physician's risk assessment → proxy for target. |
| 2 | **Post-discharge phone call outcomes** | Days/weeks after discharge | Occurs after the prediction point. Outcome of call may indicate deterioration that leads to readmission. |
| 3 | **Readmission within 10 days** | 10 days after discharge | This is a **subset of the target variable itself**. If predicting 30-day readmission, any readmission data is the target, not a feature. |
| 4 | **Future emergency room visits** | After discharge (unknown time) | ER visits happen after discharge. A patient visiting ER is much more likely to be readmitted — this is almost the target itself. |
| 5 | **Billing information (post-discharge)** | Generated after discharge/readmission | Post-discharge billing codes may include readmission charges, new procedures, or ER visits — all future information. |
| 6 | **Discharge summary** (if generated after discharge) | Hours/days after formal discharge | Some discharge summaries are completed after the patient leaves. If the summary mentions "high readmission risk" or "unstable at discharge," it leaks physician judgment. Depends on workflow timing. |
| 7 | **Post-discharge medication changes** | After discharge | If a patient's medication was changed post-discharge (e.g., due to adverse reaction), this information is only available after the prediction point. |
| 8 | **Length of readmission stay** | Only if readmitted | Only exists for readmitted patients. Using it creates a feature that is zero for non-readmitted and non-zero for readmitted — a perfect predictor that is the target in disguise. |
| 9 | **Post-discharge lab results** | After discharge | Lab results collected after leaving the hospital. Abnormal post-discharge labs often precipitate readmission. |
| 10 | **Readmission diagnosis code** | Only upon readmission | This exists only for the positive class. Including it guarantees perfect separation — it IS the outcome. |

### Key Principle
**Any feature generated from data that becomes available AFTER the discharge moment is a potential leakage feature.** The test: "If I am standing at the patient's bedside at the moment of discharge, do I know this value?" If no → potential leakage.

---

## Question 3: Prediction Point Definition

### The Prediction Point

> **The prediction must be generated at the exact moment of discharge — specifically, when the discharge order is finalized in the hospital information system (HIS) but before the patient physically leaves the facility.**

### Information Available at the Prediction Point

| Category | Available at Discharge? | Examples |
|----------|----------------------|----------|
| **Patient demographics** | ✅ Yes | Age, gender, address, insurance type |
| **Admission information** | ✅ Yes | Admission date, admission type (emergency/elective), admission source |
| **Diagnosis codes (current stay)** | ✅ Yes | Primary and secondary ICD codes assigned during the stay |
| **Procedure codes (current stay)** | ✅ Yes | All procedures performed during this hospitalization |
| **Lab results (during stay)** | ✅ Yes | All lab results collected from admission to discharge |
| **Vital signs (during stay)** | ✅ Yes | All vital signs recorded during the stay |
| **Medications (during stay + discharge prescriptions)** | ✅ Yes | Medications given during stay + discharge medication list |
| **Length of current stay** | ✅ Yes | `discharge_date - admission_date` |
| **Historical data (before admission)** | ✅ Yes | All previous admissions, ER visits, diagnoses, procedures |
| **Number of previous admissions** | ✅ Yes | Count of prior admissions (before current stay) |
| **Physician notes (during stay)** | ✅ Yes | All notes written during current hospitalization |
| **Follow-up appointments (after discharge)** | ❌ No | Typically scheduled after discharge decision |
| **Post-discharge phone calls** | ❌ No | Occur days/weeks after discharge |
| **Post-discharge events (ER visits, readmission)** | ❌ No | These are the **outcome**, not inputs |
| **Post-discharge billing** | ❌ No | Generated after the fact |

### Formal Definition

```
Prediction Point: T_discharge (discharge order timestamp)

Valid features: Any data with timestamp ≤ T_discharge
Invalid features: Any data with timestamp > T_discharge

Label (target): readmitted = 1 if patient returns within 30 days after T_discharge, else 0
Label window: (T_discharge, T_discharge + 30 days]
```

---

## Question 4: Feature Classification Table

| Feature | Valid at Discharge? | Potential Leakage? | Justification |
|---------|-------------------|--------------------|---------------|
| **Number of previous admissions** | ✅ Yes | ❌ No | All previous admissions occurred **before** the current stay. This is historical data available at discharge. It is a legitimate predictor — patients with more prior admissions are at higher risk. |
| **Discharge diagnosis** | ✅ Yes | ❌ No | Discharge diagnosis is assigned **at or before** the moment of discharge. It is part of the discharge order. It describes the **current** stay and is known at prediction time. |
| **Follow-up appointment scheduled after discharge** | ❌ No | ✅ Yes — **Leakage** | Scheduled **after** discharge. Not available at prediction time. Also a proxy for physician's risk assessment (more follow-ups = doctor thinks patient is sicker). |
| **Readmission within 10 days** | ❌ No | ✅ Yes — **Leakage (severe)** | This is a **subset of the target variable**. If we are predicting 30-day readmission, knowing about a 10-day readmission is knowing the answer. This is the most extreme form of leakage. |
| **Discharge medication list** | ✅ Yes | ❌ No | The discharge medication list is prepared **at the time of discharge** as part of the discharge process. It is available at the prediction point. Valid feature. |
| **Post-discharge phone call** | ❌ No | ✅ Yes — **Leakage** | Phone calls occur **days after** discharge. Outcomes of these calls (e.g., "patient reports symptoms") happen after the prediction point. |
| **Length of current hospital stay** | ✅ Yes | ❌ No | Computed as `discharge_date - admission_date`. Both dates are known at discharge. This is a well-established predictor of readmission risk. |
| **Future emergency-room visit** | ❌ No | ✅ Yes — **Leakage (severe)** | ER visits after discharge are **future events**. An ER visit is often the pathway to readmission — it is nearly the target itself. This feature does not exist at prediction time. |

### Summary Pattern

- **Valid features:** Historical data (before admission) + data from the current stay (admission through discharge)
- **Leakage features:** Anything generated after the discharge timestamp, especially post-discharge events and outcomes

---

## Question 5: Redesign the Feature-Generation Process

### Redesigned Process

**Principle:** Every feature must be computed using **only data with timestamps ≤ T_discharge** (the prediction point).

### Technical Enforcement Mechanisms (Not Relying on Developer Memory)

**1. Point-in-Time Feature Store**
```
For each training example:
  patient_id = P
  prediction_time = T_discharge

  Feature query:
    SELECT features
    FROM feature_store
    WHERE patient_id = P
    AND event_timestamp <= T_discharge
```
The Feature Store enforces the temporal constraint **automatically**. Features are always retrieved relative to a specific timestamp. This prevents any future data from leaking in.

**2. Temporal Join Enforcement in the Data Pipeline**
```sql
-- CORRECT: Time-bounded aggregation
SELECT patient_id,
       COUNT(*) as num_previous_admissions
FROM admissions
WHERE patient_id = :patient_id
AND discharge_date < :current_admission_date  -- strictly before current stay
GROUP BY patient_id

-- WRONG: Unbounded aggregation (includes future)
SELECT patient_id,
       COUNT(*) as num_admissions
FROM admissions
WHERE patient_id = :patient_id
GROUP BY patient_id
```

**3. Automated Validation Layer**
Build an automated check that runs before model training:
```python
def validate_no_leakage(feature_df, label_df):
    """Verify all feature timestamps precede label timestamps."""
    for idx in feature_df.index:
        feature_time = feature_df.loc[idx, 'feature_timestamp']
        prediction_time = label_df.loc[idx, 'discharge_timestamp']
        assert feature_time <= prediction_time, \
            f"LEAKAGE: Feature at {feature_time} is after prediction at {prediction_time}"
```

**4. Schema-Level Column Tagging**
Tag every column in the data catalog with its temporal availability:
```yaml
columns:
  num_previous_admissions:
    available_at: "admission"
    temporal_type: "historical"
  follow_up_appointments:
    available_at: "post_discharge"
    temporal_type: "future"  # BLOCKED from feature pipeline
  discharge_diagnosis:
    available_at: "discharge"
    temporal_type: "current_stay"
```
The feature pipeline **rejects** any column tagged as `future` or `post_discharge`.

**5. Training-Serving Parity Test**
In production, the model only receives data available at discharge time. Create an integration test that:
1. Takes a real training example
2. Simulates the production feature pipeline (only data up to T_discharge)
3. Compares the feature vector from training vs simulation
4. Flags any discrepancy (indicates leakage in training)

**6. Code Review Checklist**
Every feature engineering PR must answer:
- [ ] Does this feature use only data available at prediction time?
- [ ] Is the time-window explicitly bounded?
- [ ] Has the feature been tested with the temporal validation pipeline?

### Key Insight
**Technical enforcement > developer discipline.** Humans forget rules; automated pipelines don't. The Feature Store with point-in-time correctness is the single most effective leakage prevention mechanism.

---

## Question 6: Temporal Train/Validation/Test Strategy

### Why Random Splitting Is Problematic

Random splitting of 5 years of data creates **temporal leakage** and gives an **overly optimistic performance estimate** for several reasons:

**1. Future data leaks into training:**
A random split may place a patient's 2023 record in training and their 2021 record in test. The model trains on future information about the same patient — it has seen what "happens next" for this patient.

**2. Temporal autocorrelation:**
Medical practices, treatment protocols, patient populations, and disease patterns **change over time**. A model trained on 2019-2023 data randomly mixed will learn an "average" of all these periods. In production, it must predict only the future. Random splitting hides the fact that the model may not generalize to future time periods.

**3. Same patient in both sets:**
Without patient-level splitting, the same patient may appear in both training and test. The model memorizes patient-specific patterns rather than learning generalizable clinical features.

**4. Seasonal/temporal patterns:**
Readmission rates may vary by season (flu season, holiday periods). Random splitting blurs these patterns, making offline metrics unrealistically stable.

### Proper Temporal Split Strategy

**Use a time-based split where training data is always BEFORE validation, and validation is always BEFORE test:**

```
Year 1   Year 2   Year 3   Year 4   Year 5
(2019)   (2020)   (2021)   (2022)   (2023)
├────────────────┤ ├──────┤ ├──────┤
    TRAINING          VAL      TEST
   (3 years)       (1 year)  (1 year)
```

| Set | Period | Purpose |
|-----|--------|---------|
| **Training** | Years 1-3 (2019-2021) | Model learns patterns from historical data |
| **Validation** | Year 4 (2022) | Hyperparameter tuning, model selection, threshold tuning |
| **Test** | Year 5 (2023) | Final unbiased performance estimate. **Never touched during development.** |

**Additional safeguards:**
1. **Patient-level split:** If a patient appears in the training period AND the test period, their test-period records go to test only. No patient straddles both sets.
2. **Gap period (optional):** Insert a small gap (e.g., 30 days) between training and validation to prevent any label window overlap.
3. **Walk-forward validation (advanced):** Train on expanding windows and validate on the next period. This simulates how the model would be retrained over time.

```
Fold 1: Train [Y1]       → Validate [Y2]
Fold 2: Train [Y1, Y2]   → Validate [Y3]
Fold 3: Train [Y1-Y3]    → Validate [Y4]
Final:  Train [Y1-Y4]    → Test [Y5]
```

This approach gives the **most realistic estimate** of how the model will perform when deployed on future, unseen patients.

---

## Question 7: How an Apparently Valid Feature Can Accidentally Include Future Information

### The Problem

The feature **"Number of admissions in previous year"** seems valid — it counts historical admissions. However, if computed carelessly, it can leak future information.

**How the accident happens:**

Suppose the prediction date for a patient is **March 15, 2022** (their discharge date). The intended feature is: "How many times was this patient admitted in the 12 months before March 15, 2022?"

The **correct** computation:
```sql
SELECT COUNT(*)
FROM admissions
WHERE patient_id = :pid
AND admission_date BETWEEN '2021-03-15' AND '2022-03-15'
AND admission_id != :current_admission_id
```

The **incorrect** (but common) computation:
```sql
-- WRONG: Uses full patient history table without time filtering
SELECT COUNT(*)
FROM admissions
WHERE patient_id = :pid
AND YEAR(admission_date) = 2022  -- calendar year, not relative
```

Or even worse:
```sql
-- WRONG: No time filter at all
SELECT COUNT(*)
FROM admissions
WHERE patient_id = :pid
```

**What goes wrong:**

The patient history table contains **all admissions**, including those that happen **after March 15, 2022**. If the patient is readmitted on April 10, 2022 (within 30 days — a positive case), and the query counts all 2022 admissions, it would count the readmission itself in the feature.

This means:
- Patients who were readmitted → higher admission count → model learns "high count = readmission"
- The model is literally using **the outcome** to predict the outcome

**This is especially insidious because:**
- The feature name sounds perfectly legitimate
- The SQL query looks reasonable
- The leakage is subtle — it inflates the count by just 1 for readmitted patients
- But that "+1" is a near-perfect predictor of the target

### Correct Computation

```sql
SELECT COUNT(*) as admissions_previous_year
FROM admissions
WHERE patient_id = :patient_id
AND admission_date >= (:prediction_date - INTERVAL '365 days')
AND admission_date < :current_admission_date  -- strictly before THIS admission
-- Excludes: current admission, any future admissions, any post-prediction admissions
```

**Rules for correct feature computation:**
1. Always filter by `timestamp < prediction_point`
2. Exclude the current admission from historical counts
3. Use **relative time windows** (365 days before prediction), not calendar years
4. Test: Does the feature value change if we change the label? If yes → leakage

---

## Question 8: Accuracy Drop from 97% to 82% — Is the Model "Worse"?

### Answer: No, I do NOT agree. The model has become more **honest**, not worse.

**What the performance change actually tells us:**

**1. The 97% was fake:**
The 97% accuracy was an artifact of data leakage. The model was using features that directly encoded the outcome (follow-up appointments, post-discharge events). It was essentially "cheating" — looking at the answer sheet while taking the test. This performance was **never achievable in production** because those features don't exist at prediction time.

**2. The 82% is the real performance:**
After removing leakage features, the model is forced to make predictions using only information available at discharge time — exactly what it would have in production. The 82% represents the model's **true generalization ability**.

**3. The gap (97% → 82%) measures the leakage magnitude:**
The 15-percentage-point drop quantifies how much the leaked features were inflating performance. This is a useful diagnostic — it shows the previous model was heavily reliant on cheating features.

**4. 82% may actually be good:**
Whether 82% is acceptable depends on:
- **Baseline comparison:** What does a naive model achieve? (See Q9 — a "predict no readmission for everyone" model gets 92% due to class imbalance)
- **Clinical utility:** Does the model meaningfully help identify at-risk patients compared to the current process?
- **Metric appropriateness:** Accuracy alone is misleading for imbalanced data (8% readmission rate). We need precision, recall, F1, AUC-ROC (see Q9).

**5. A lower-accuracy honest model is infinitely more valuable than a high-accuracy cheating model:**
The 97% model would **fail immediately in production** because the leakage features are unavailable. The 82% model will actually work.

### Analogy
A student who scores 97% by copying answers will score poorly in the real exam. A student who scores 82% through genuine study will perform reliably. The 82% student is **better prepared**, not worse.

---

## Question 9: Why Accuracy Is Inappropriate — Better Metrics

### Why Accuracy Fails

The hospital's readmission rate is **8%**. This means 92% of patients are NOT readmitted.

**A "dumb" model that predicts "No readmission" for EVERY patient achieves:**
- Accuracy = 92% (correct for all non-readmitted patients)
- But it **catches zero at-risk patients**
- It is completely useless clinically

This demonstrates the **accuracy paradox for imbalanced classes**: high accuracy can coexist with zero clinical value.

**The 82% accuracy model is actually WORSE than the naive 92% baseline** by accuracy alone. But this comparison is meaningless — accuracy is the wrong metric.

### Better Metrics

| Metric | Formula | Why It Matters Here |
|--------|---------|-------------------|
| **Recall (Sensitivity)** | TP / (TP + FN) | "Of all patients who WERE readmitted, what % did we catch?" This is the most important metric. Missing a high-risk patient (false negative) means they don't get intervention. |
| **Precision (PPV)** | TP / (TP + FP) | "Of all patients we flagged as high-risk, what % were actually readmitted?" Low precision means wasting resources on patients who didn't need intervention. |
| **F1 Score** | 2 × (Precision × Recall) / (Precision + Recall) | Harmonic mean of precision and recall. Balances both concerns. Better than accuracy for imbalanced data. |
| **AUC-ROC** | Area under Receiver Operating Characteristic curve | Measures the model's ability to **rank** patients by risk across all possible thresholds. Threshold-independent metric. A random model = 0.5, perfect model = 1.0. |
| **AUC-PR (Precision-Recall curve)** | Area under Precision-Recall curve | **Better than AUC-ROC for imbalanced data.** AUC-ROC can be misleadingly high when negatives dominate. AUC-PR focuses on performance for the minority (positive) class. |
| **Recall @ top K%** | Recall when selecting top K% highest-risk patients | Practical metric: "If we can only intervene on the top 20% riskiest patients, what % of actual readmissions do we catch?" Directly tied to resource constraints. |

### Recommended Primary Metrics for This Problem

1. **Primary:** Recall (must catch high-risk patients)
2. **Secondary:** Precision (resources are limited)
3. **Overall:** AUC-PR (single number summarizing performance on the minority class)
4. **Practical:** Recall @ top 20% (operational constraint)

---

## Question 10: Probability Threshold Selection

### Should the Threshold Be 0.5? NO.

A threshold of 0.5 is arbitrary. It assumes that:
- The classes are balanced (they are not — 8% vs 92%)
- The cost of a false positive equals the cost of a false negative (it does not)

### Asymmetric Cost Analysis

| Error Type | What Happens | Cost |
|-----------|-------------|------|
| **False Negative** (miss a high-risk patient) | Patient doesn't receive post-discharge support → gets readmitted → poor outcome, hospital penalty, higher cost | **HIGH** — patient harm, financial penalty (CMS readmission penalties in US), lost trust |
| **False Positive** (flag a low-risk patient) | Patient receives unnecessary follow-up care → extra phone call, nurse visit | **LOW** — additional resources spent, but patient is not harmed. May even benefit from extra attention. |

Since **false negatives are much more costly than false positives**, we should **lower the threshold below 0.5** to catch more true positives (at the cost of more false positives).

### How to Determine the Optimal Threshold

**Method 1: Cost-Sensitive Analysis**
```
Total Cost = (FN × Cost_FN) + (FP × Cost_FP)

Suppose:
  Cost of missing a readmission (FN): ₹50,000 (readmission cost + penalties)
  Cost of unnecessary follow-up (FP): ₹2,000 (nurse call + time)

  Cost ratio = 50,000 / 2,000 = 25:1

  → Set threshold such that we accept up to 25 false positives to catch 1 more true positive
```

**Method 2: Precision-Recall Curve Analysis**
1. Plot the precision-recall curve across all thresholds (0.0 to 1.0)
2. At each threshold, compute the operational cost
3. Select the threshold that minimizes total cost (or maximizes net clinical benefit)

**Method 3: Clinical Decision Curve Analysis**
1. Define the clinical action: "Provide post-discharge support program"
2. At each threshold, compute: Net Benefit = (TP/N) - (FP/N) × (threshold / (1 - threshold))
3. Select the threshold with highest net benefit

**Method 4: Stakeholder-Driven**
1. Ask clinicians: "How many patients are you willing to follow up unnecessarily to avoid missing one readmission?"
2. If answer is "10" → acceptable precision = 1/11 ≈ 9%
3. Find the threshold on the precision-recall curve that achieves this precision while maximizing recall

**Practical recommendation:** The threshold would likely be **0.15 - 0.30** for this problem, significantly below 0.5, because:
- The prevalence is low (8%)
- The cost of missing a patient is much higher than unnecessary follow-up
- The hospital can absorb some false positives if it means catching more at-risk patients

---

## Question 11: Intervention Changes Future Training Data (Feedback Loop)

### The Problem

**Before deployment:**
- High-risk patients receive no extra support → some get readmitted → labelled positive (readmitted=1)
- Model learns: patient features X → readmission likely

**After deployment:**
- Model identifies high-risk patients → hospital provides extra follow-up care → **some of these patients are NO longer readmitted** thanks to the intervention
- These patients now have label = 0 (not readmitted) in the new data
- But their features are identical to the previously readmitted patients

**What happens when we retrain:**
- The new training data contains patients with high-risk features but **negative outcomes** (because they were successfully treated)
- The model learns: "patients with these features don't get readmitted" → it lowers their risk scores
- Next cycle: these patients are no longer flagged → they don't receive intervention → they get readmitted again

This creates a **destructive feedback loop**:

```
Deploy model → Intervene on predicted high-risk →
Readmissions decrease → New training data shows lower readmission →
Retrained model thinks risk is lower → Stops flagging patients →
No intervention → Readmissions increase again → ...
```

This is called **performative prediction** or the **treatment effect problem**.

### Solutions

1. **Randomized holdout:** Randomly withhold intervention from a small percentage of flagged patients (ethical review required). This creates an unbiased estimate of the true readmission risk.

2. **Causal inference adjustment:** Use techniques like inverse propensity weighting (IPW) to adjust for the fact that treated patients have different outcomes than they would without treatment.

3. **Label the intervention:** Add a feature `received_intervention = True/False`. The model can learn: "Patients with features X who did NOT receive intervention → high readmission risk." Only train on non-intervened patients for risk estimation.

4. **Use pre-deployment data as anchor:** Keep the original (pre-intervention) dataset as a reference. Blend old and new data during retraining with appropriate weighting.

5. **Multi-armed bandit approach:** Occasionally explore (don't intervene on some predicted high-risk patients) to maintain data diversity.

---

## Question 12: Recall Decrease After 2 Years — Five Possible Explanations

The model's code and parameters haven't changed, but recall has dropped significantly. This means the issue is **not in the model itself** but in the **data, environment, or system around it**.

| # | Explanation | Type | Why It Causes Recall Drop |
|---|------------|------|--------------------------|
| 1 | **Data drift — patient population changed** | Data Drift | The hospital may now serve a different demographic (e.g., older patients, different insurance mix, new catchment area). Feature distributions have shifted away from what the model was trained on. The model's learned thresholds no longer match the current population. |
| 2 | **Concept drift — new treatment protocols** | Concept Drift | New treatments or care protocols may have changed the **relationship** between features and readmission. A condition that previously led to readmission now doesn't (or vice versa). The same features now mean different things. |
| 3 | **Upstream data pipeline change** | Data Quality | A change in the EHR system, data extraction logic, or coding practices (e.g., ICD-10 code updates, new lab test names) means the model receives different feature values than during training, even for similar patients. Schema drift. |
| 4 | **Feedback loop from intervention** (see Q11) | Performative Prediction | The model's own predictions changed the training data. Intervened patients didn't get readmitted → model underestimates risk for similar patients → recall drops for the actual high-risk group. |
| 5 | **Missing or stale features** | Data Quality / Operational | A data feed may have broken (e.g., lab system integration fails, a vendor stops supplying data). Missing features get imputed with defaults, leading to uniformly moderate risk scores instead of correctly high scores. |
| 6 | **New disease patterns or health events** | Concept Drift | A new disease (e.g., pandemic aftermath, new variant), seasonal shift, or public health change introduces readmission patterns the model has never seen. The model cannot flag risks it wasn't trained on. |
| 7 | **Label definition change** | Operational | If the hospital changed what counts as a "readmission" (e.g., observation stays now counted, transfers between facilities included), the ground truth labels have shifted. The model was trained on the old definition. |

---

## Question 13: Data Drift vs Concept Drift Classification

### Scenario A: Older patient population, different age and comorbidity distributions

**Classification: DATA DRIFT (Covariate Shift)**

**Reasoning:**
- The **input feature distributions** have changed (age is higher, comorbidity counts are different)
- The **relationship** between features and readmission has NOT changed (an 80-year-old with diabetes still has the same readmission risk as before — there are just MORE 80-year-olds now)
- P(X) has changed, but P(Y|X) has NOT changed
- The model may perform poorly because it's now operating in a region of the feature space with fewer training examples (extrapolation)

**Formally:**
- P_train(age, comorbidities) ≠ P_production(age, comorbidities) → **Data Drift**
- P(readmission | age, comorbidities) remains the same

### Scenario B: New treatment protocol reduces readmission probability for same patients

**Classification: CONCEPT DRIFT**

**Reasoning:**
- The **input feature distributions** may be the SAME (same patients, same characteristics)
- But the **relationship** between features and the outcome has changed (same features now lead to different readmission probabilities because of the new treatment)
- P(X) is unchanged, but P(Y|X) HAS changed
- The model's learned mapping from features → readmission risk is now wrong, even though the features look the same

**Formally:**
- P_train(age, comorbidities) = P_production(age, comorbidities)
- P_train(readmission | age, comorbidities) ≠ P_production(readmission | age, comorbidities) → **Concept Drift**

### Summary Table

| Aspect | Data Drift (Scenario A) | Concept Drift (Scenario B) |
|--------|------------------------|---------------------------|
| Feature distribution changes? | ✅ Yes | ❌ No |
| Feature-target relationship changes? | ❌ No | ✅ Yes |
| Root cause | Population shift | Treatment/protocol change |
| Fix | Retrain on new population | Retrain to learn new relationships |
| Detection | Monitor feature distributions (PSI) | Monitor predicted vs actual outcomes |

---

## Question 14: Missing Lab Values — Why Replacing with Zero Is Problematic

### Why Zero-Imputation Is Dangerous

**1. Zero is a meaningful value, not "missing":**
For many lab tests, zero is either impossible or clinically extreme. Replacing missing hemoglobin with 0 g/dL tells the model the patient has no blood. Replacing blood glucose with 0 tells the model the patient is in severe hypoglycemia. The model will treat these as **critically abnormal patients**, not as missing data.

**2. Systematic bias in who is missing:**
Lab values are not missing **at random**. They are missing because:
- The physician didn't order the test (perhaps the patient seemed healthy → Missing Not At Random / MNAR)
- The patient was discharged before results were available
- The test wasn't applicable to the patient's condition

This means "missing" carries clinical information. Zero-imputing destroys this information and introduces systematic bias.

**3. Feature distribution distortion:**
With 30% zeros injected, the model sees a bimodal distribution: a spike at zero and the actual clinical distribution. This distorts correlations, splits, and learned thresholds. Tree-based models (XGBoost) would create splits that separate the artificial zeros from real values, learning a "missing vs not-missing" pattern rather than the clinical pattern.

**4. Different features have different scales:**
Zero means different things for different tests. Heart rate = 0 means death. Creatinine = 0 is impossible. White blood cell count = 0 is severe. Blanket zero imputation ignores the clinical meaning of each variable.

### What to Investigate Before Choosing an Imputation Strategy

| Investigation | Question to Answer |
|--------------|-------------------|
| **Missingness pattern** | Is data Missing Completely At Random (MCAR), Missing At Random (MAR), or Missing Not At Random (MNAR)? |
| **Clinical reason for missingness** | Why was the test not ordered? Does missingness itself predict readmission? |
| **Percentage missing per feature** | Is it 5% (impute) or 80% (consider dropping the feature)? |
| **Correlation between missingness and target** | Are missing lab patients more/less likely to be readmitted? If yes, missingness is informative. |
| **Feature importance** | How important is this feature? High-importance features need careful imputation. |

### Better Approaches

| Approach | When to Use |
|----------|------------|
| **Median/mean imputation** | MCAR, low % missing. Use population or subgroup (by diagnosis) median. |
| **Missing indicator feature** | Add a binary column `lab_X_missing = 1/0`. Lets the model learn the missingness pattern. |
| **Model-based imputation (MICE, KNN)** | MAR, moderate % missing. Uses other features to estimate the missing value. |
| **Domain-specific defaults** | Consult clinicians: "What value would you assume if this test wasn't ordered?" |
| **Drop the feature** | If >50% missing and not clinically important, remove it entirely. |
| **Combination: Impute + missing indicator** | Best of both: impute with median for the model, but also tell the model the value was imputed. |

---

## Question 15: Disparate Recall Across Groups

### Can the Hospital Claim the Model Is "Performing Well"?

**No.** Overall recall of 86% masks a significant performance disparity between groups:

| Group | Recall |
|-------|--------|
| Group A | 90% |
| Group B | 72% |
| Overall | 86% |

If Group A and Group B represent different demographic or clinical groups (e.g., ethnic groups, age groups, insurance types), the **18-percentage-point gap** means the model is systematically **missing more high-risk patients in Group B**.

**Implications:**
- Group B patients who should receive intervention are being missed at a much higher rate
- If Group B is a protected demographic group, this is a **fairness violation**
- The overall 86% is a weighted average that hides the disparity (likely because Group A is larger)

### Additional Analysis Required

| Analysis | Purpose |
|----------|---------|
| **Subgroup performance breakdown** | Compute recall, precision, F1, AUC for ALL demographic groups (age, gender, ethnicity, insurance, diagnosis category). |
| **Equalized odds check** | Are TP and FP rates similar across groups? If not, the model discriminates. |
| **Training data representation** | Is Group B underrepresented in training data? If only 10% of training data is Group B, the model hasn't learned their patterns well. |
| **Feature distribution by group** | Do the features behave differently for Group A vs B? (e.g., different lab normal ranges, different comorbidity profiles). |
| **Error analysis** | For Group B false negatives: what do these missed patients look like? Are they a specific subtype the model consistently misses? |
| **Calibration by group** | When the model says 30% risk for Group A vs Group B, is the actual readmission rate 30% for both? Miscalibration = group-specific inaccuracy. |
| **Disparate impact ratio** | Recall_B / Recall_A = 72/90 = 0.80. The 4/5ths rule (used in employment) suggests ratios below 0.80 indicate adverse impact. This is right at the boundary. |

### Remediation Options
1. Collect more training data for Group B
2. Use stratified sampling to ensure Group B is well-represented
3. Apply group-specific thresholds (different probability cutoffs per group to equalize recall)
4. Use fairness-aware training objectives (penalize disparate recall during training)
5. Investigate if different features are needed for Group B

---

## Question 16: Explaining a Prediction to a Physician

### What the AI System Should Provide

When a physician asks "Why does the model think this patient is likely to be readmitted?", the system should present:

**1. Top Contributing Factors (SHAP or LIME explanation):**

```
Patient Risk Score: 0.73 (High Risk)

Top factors increasing risk:
  ↑ Number of admissions in past year: 4 (population average: 0.8)
  ↑ Length of current stay: 12 days (population average: 4.2 days)
  ↑ Number of active comorbidities: 6 (population average: 2.1)
  ↑ Discharge diagnosis: Heart failure (historically high readmission rate)
  ↑ Age: 78 (older patients have higher readmission risk)

Factors decreasing risk:
  ↓ Stable vital signs at discharge
  ↓ Medication adherence history: Good
```

**2. Patient context compared to similar patients:**
```
Among patients with similar characteristics:
  - 68% were readmitted within 30 days
  - Most common readmission reasons: medication non-compliance, symptom exacerbation
```

**3. Important caveats (non-causal language):**

The system should explicitly state:
> "These are **statistical associations** the model has learned from historical data, **not causal explanations**. The model identified patterns — it does not know **why** these factors are associated with readmission. Clinical judgment should consider factors the model cannot capture."

**4. What NOT to say:**
- ❌ "The patient WILL be readmitted because of heart failure" (causal claim)
- ✅ "Patients with similar characteristics have historically had a 68% readmission rate" (statistical association)
- ❌ "Reducing comorbidities will lower readmission risk" (causal intervention claim)
- ✅ "Fewer comorbidities is associated with lower readmission rates in the training data" (correlation)

**5. Actionable (but not prescriptive) information:**
- Suggest the physician **consider** additional post-discharge support
- Present the score as **one input** into the clinical decision, not the final decision
- Make it clear: **the physician makes the decision, not the model**

### Technical Implementation
- Use **SHAP (SHapley Additive exPlanations)** for feature attribution — it provides mathematically grounded, per-prediction feature importance
- Present in a visual dashboard: waterfall chart showing how each feature pushed the score up or down from the baseline
- Include model performance disclaimer: "This model correctly identifies 81% of readmitted patients. 34% of flagged patients are actually readmitted."

---

## Question 17: Production Architecture Design

```
Hospital Systems → Data Pipeline → Feature Generation → Model → Risk Score → Clinical App → Monitoring
```

### Stage-by-Stage Design

#### Stage 1: Hospital Systems (Data Sources)

| System | Data | Integration Method |
|--------|------|-------------------|
| EHR (Electronic Health Records) | Demographics, diagnoses, labs, vitals, medications, notes | HL7/FHIR API, database replication |
| ADT (Admission-Discharge-Transfer) | Admission/discharge events, timestamps | Real-time HL7 ADT messages |
| Laboratory Information System | Lab results, order timestamps | HL7 ORU messages |
| Pharmacy System | Medication history, discharge prescriptions | FHIR MedicationRequest |
| Scheduling System | Previous appointments (historical only) | Database query |

**Key action:** Ingest data from multiple hospital systems using standardized healthcare interoperability protocols (HL7 FHIR).

#### Stage 2: Data Pipeline

| Component | Purpose |
|-----------|---------|
| **Ingestion** | Receive data from hospital systems. Batch (nightly for historical) + event-driven (on discharge event). |
| **Validation** | Schema checks, null checks, range checks (e.g., age 0-120, heart rate 20-300), referential integrity (patient_id exists). |
| **Cleaning** | Deduplicate records. Handle missing values (per Q14 strategy). Standardize codes (ICD-10 mapping). |
| **Transformation** | Convert raw data to analysis-ready format. Date parsing, unit conversion, text normalization. |
| **PII Protection** | Hash/mask PII. Separate identifying data from clinical features. HIPAA compliance. |

**Trigger:** The pipeline is triggered by the **ADT discharge event** (when a discharge order is placed in the system).

#### Stage 3: Feature Generation

| Component | Purpose |
|-----------|---------|
| **Point-in-time feature computation** | Compute all features using ONLY data with timestamp ≤ T_discharge. |
| **Feature Store (offline)** | Pre-computed historical features (admission counts, lab averages). Updated nightly. |
| **Feature Store (online)** | Real-time features for the current stay (length of stay, current vitals). Sub-second retrieval. |
| **Feature vector assembly** | Combine offline + online features into a single vector for model input. |
| **Leakage guard** | Automated check: reject any feature with timestamp > T_discharge. |

#### Stage 4: Model Inference

| Component | Purpose |
|-----------|---------|
| **Model serving endpoint** | REST API hosting the trained model. Receives feature vector, returns probability. |
| **Model versioning** | Track which model version produced each prediction. Support rollback. |
| **Fallback logic** | If model unavailable → return "score unavailable" (never return a default score). |
| **Latency SLA** | P99 < 200ms. If exceeded → alert + fallback. |
| **Input validation** | Check feature vector completeness, flag if critical features are missing. |

#### Stage 5: Risk Score

| Component | Purpose |
|-----------|---------|
| **Score computation** | Model output (probability 0-1) → Risk category (Low / Medium / High) using calibrated threshold. |
| **Threshold application** | Apply the clinically-determined threshold (from Q10 analysis). E.g., ≥ 0.25 = High Risk. |
| **Explanation generation** | SHAP values computed for this prediction → top 5 contributing features. |
| **Score logging** | Log: patient_id (hashed), timestamp, model_version, feature_vector, raw_probability, risk_category, explanation. |

#### Stage 6: Clinical Application

| Component | Purpose |
|-----------|---------|
| **Dashboard integration** | Risk score displayed in the EHR discharge workflow. Physician sees score + explanation at discharge. |
| **Alert system** | High-risk patients trigger alert to care coordination team. |
| **Workflow integration** | High-risk flag → automatically enroll patient in post-discharge support program. |
| **Physician override** | Physician can override the model's recommendation with documented reasoning. |
| **Patient communication** | High-risk patients receive additional discharge instructions. |

#### Stage 7: Monitoring

| Component | Purpose |
|-----------|---------|
| **Data quality monitoring** | Track missing rates, distribution shifts, pipeline failures (see Q18). |
| **Model performance monitoring** | Track recall, precision over time. Compare predicted vs actual readmissions (with 30-day lag). |
| **System health monitoring** | Latency, throughput, error rates, uptime. |
| **Drift detection** | PSI for features, KL divergence for predictions. Alert on significant drift. |
| **Business outcome tracking** | Actual readmission rates, intervention effectiveness, cost savings. |
| **Retraining trigger** | Automated or manual trigger when performance degrades below threshold. |

---

## Question 18: At Least 10 Production Monitoring Metrics

### Data Quality Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 1 | **Missing value rate per feature** | % of predictions with missing values for each feature | > 10% for critical features (labs, vitals) |
| 2 | **Data freshness (pipeline latency)** | Time from discharge event to feature availability | > 30 minutes |
| 3 | **Schema validation failure rate** | % of records failing schema checks (wrong types, invalid codes) | > 1% |

### Data Drift Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 4 | **Population Stability Index (PSI) per feature** | Distribution shift in each input feature compared to training data | PSI > 0.2 (significant drift) |
| 5 | **Input feature summary statistics** | Mean, median, std of key features over rolling 7-day window vs training baseline | > 2 standard deviations from training mean |

### Model Performance Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 6 | **Recall (sensitivity)** — rolling 30-day window | Of patients who were readmitted, what % were flagged? (Requires 30-day label delay) | < 75% |
| 7 | **Precision** — rolling 30-day window | Of patients flagged as high-risk, what % were readmitted? | < 25% |
| 8 | **AUC-ROC / AUC-PR** — rolling 30-day window | Overall model discrimination ability | AUC-ROC < 0.70 |

### Prediction Distribution Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 9 | **Prediction distribution shift** | Distribution of risk scores (mean, percentiles) over time | Mean risk score shifts > 20% from baseline |
| 10 | **High-risk flag rate** | % of patients classified as high risk per week | Deviates > 30% from historical average |

### System Performance Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 11 | **Inference latency (P50, P95, P99)** | Time to generate a prediction | P99 > 200ms |
| 12 | **Error rate / availability** | % of requests that fail or time out | > 0.1% failure rate |
| 13 | **Prediction volume** | Number of predictions per day/hour | Sudden drop (indicates pipeline failure) or spike (indicates unusual activity) |

### Clinical / Business Outcome Metrics

| # | Metric | What It Measures | Alert Threshold |
|---|--------|-----------------|----------------|
| 14 | **Actual 30-day readmission rate** | Ground truth readmission rate over time | Significant change from historical 8% baseline |
| 15 | **Intervention effectiveness** | Readmission rate for intervened vs non-intervened high-risk patients | No difference → intervention not working |
| 16 | **Physician override rate** | % of high-risk predictions overridden by physician | > 50% → model may not be trusted or calibrated |

---

## Question 19: Predictions Unavailable During Peak Hours

### Is This a Model-Performance Problem?

**No.** Offline model performance is unchanged, meaning the model itself is working correctly. The issue is that predictions are **not reaching physicians**, not that the predictions are wrong.

### Where to Investigate: Infrastructure / System Layer

**Primary investigation: Application / Infrastructure Layer**

| Investigation Area | What to Check | Why |
|-------------------|--------------|-----|
| **Model serving endpoint** | CPU/memory utilization, request queue depth, connection pool exhaustion | Peak hours = more discharges = more concurrent prediction requests. The endpoint may not have enough capacity to handle the load. |
| **Auto-scaling configuration** | Is the endpoint configured to scale horizontally? What's the scale-up latency? | If auto-scaling is misconfigured or slow, the system can't handle traffic spikes. |
| **Data pipeline bottleneck** | Is the feature generation pipeline backing up during peak hours? | If features take too long to compute, the prediction request times out. |
| **Network / load balancer** | Is the load balancer dropping requests? Are there timeout configurations that are too aggressive? | Network-level issues cause requests to fail silently. |
| **EHR integration** | Is the EHR system slow during peak hours, causing the API call to the model to timeout? | The bottleneck may be in the hospital's own system, not the ML system. |
| **Database / Feature Store** | Is the online feature store (Redis/DynamoDB) under-provisioned? | Feature retrieval latency spikes → overall prediction latency exceeds timeout → prediction unavailable. |

### Diagnostic Approach

1. **Check system metrics during peak hours:** CPU, memory, request latency (P99), error rates, queue depth
2. **Identify the bottleneck:** Is it the feature retrieval, model inference, or API layer?
3. **Compare peak vs off-peak:** What changes? (Traffic volume, latency, error rates)
4. **Load test:** Simulate peak-hour traffic and observe where the system breaks

### Likely Solutions
- Increase compute resources / enable auto-scaling for the model endpoint
- Pre-compute features (batch) during off-peak hours
- Add caching for frequently accessed features
- Implement request queuing with priority (high-risk patients first)
- Add circuit breaker pattern: if prediction unavailable > 5 seconds, notify physician "score will be available shortly"

---

## Question 20: Production Approval Decision

### Given Results

| Metric | Value |
|--------|-------|
| Initial model accuracy | 97% |
| After leakage removal | 82% |
| Baseline model (predicts majority class) | 78% (actually a "never readmit" model would get 92%) |
| Readmission prevalence | 8% |
| Recall | 81% |
| Precision | 34% |
| Model latency | 150ms |
| Data freshness | 24 hours |
| Prediction generated at | Discharge |

### Structured Assessment

#### 1. Data Validity ⚠️ CONDITIONAL PASS

- **Positive:** 5 years of historical data provides substantial training volume
- **Concern:** 24-hour data freshness means features may be stale. Lab results from the last day of stay might not be reflected. For a discharge-time prediction, freshness should be **near real-time** (minutes, not hours)
- **Action required:** Reduce data freshness to <1 hour for clinical features. 24 hours is acceptable for historical aggregations only.

#### 2. Leakage ✅ PASS (resolved)

- The 97% → 82% drop confirms leakage was present and has been removed
- The 15-point gap indicates the model was heavily reliant on leaked features
- **Verification needed:** Has the team confirmed ALL leakage features are removed? Has the temporal feature validation pipeline been implemented?

#### 3. Evaluation Methodology ⚠️ NEEDS INVESTIGATION

- **Critical question:** Was the 82% accuracy measured with temporal splitting or random splitting?
- If random split → performance is still optimistic (see Q6). Must redo with temporal split.
- **Action required:** Confirm temporal train/val/test split was used. If not, re-evaluate.

#### 4. Model Performance ⚠️ MARGINAL

- **Accuracy (82%) vs baseline (78%):** Only 4 percentage points above baseline. This is a modest improvement. However, accuracy is the wrong metric for 8% prevalence (see Q9).
- **Recall (81%):** Good — catching 81% of readmitted patients. This means 19% of at-risk patients are still missed.
- **Precision (34%):** Low — for every 3 patients flagged as high-risk, only 1 is actually readmitted. This means 66% of flagged patients receive unnecessary intervention.
- **Is this acceptable?** Depends on the cost of intervention vs cost of missed readmission. If follow-up calls cost ₹2,000 and a readmission costs ₹50,000, the math works: spending ₹6,000 (3 calls) to prevent one ₹50,000 readmission is worthwhile.

| Assessment | Metric | Verdict |
|-----------|--------|---------|
| Recall | 81% | Acceptable for initial deployment |
| Precision | 34% | Low but potentially acceptable given cost asymmetry |
| **Missing:** AUC-ROC, AUC-PR | Not reported | **Must be computed before approval** |

#### 5. Threshold ⚠️ NOT ADDRESSED

- No information provided about threshold selection methodology
- Was the 0.5 threshold used? If so, the threshold should be optimized per Q10
- **Action required:** Conduct cost-sensitive threshold analysis. Report precision-recall curve. Determine optimal threshold based on clinical cost-benefit analysis.

#### 6. Clinical Usefulness ⚠️ NEEDS VALIDATION

- The model catches 81% of readmissions — meaningful clinical value
- But: has the model been tested with actual clinicians?
- **Action required:** 
  - Conduct a pilot study: shadow deployment (model runs, scores are logged, but not shown to physicians). Compare model predictions with actual outcomes.
  - Then: prospective pilot where physicians see scores. Measure if intervention based on scores reduces readmissions.

#### 7. Fairness ❌ NOT ADDRESSED

- No subgroup analysis reported (recall/precision by age, ethnicity, insurance type, diagnosis)
- This is a **blocking gap**. A model with 81% overall recall might have 90% for one group and 60% for another (see Q15)
- **Action required:** Compute performance metrics by all relevant demographic groups. Report disparate impact ratios. Must pass fairness review before production deployment.

#### 8. Monitoring ⚠️ NEEDS PLAN

- No monitoring plan described
- **Action required:** Implement the monitoring metrics from Q18 before deployment. At minimum: data quality, drift detection, performance tracking (with 30-day label lag), system health.

#### 9. Operational Readiness ⚠️ PARTIAL

- **Latency (150ms):** ✅ Good — well within acceptable range for a discharge-time prediction
- **Data freshness (24 hours):** ⚠️ Too slow for real-time clinical use
- **Missing:** Uptime SLA, fallback plan, rollback strategy, load testing results, integration testing with EHR
- **Action required:** Define SLA, test under peak load, implement fallback logic

#### 10. Business Value ⚠️ NEEDS QUANTIFICATION

- No cost-benefit analysis provided
- **Action required:** Calculate:
  - Expected readmissions prevented per month (81% recall × 8% readmission rate × patient volume)
  - Cost of interventions (34% precision means many unnecessary follow-ups)
  - Net savings (readmissions prevented × cost per readmission − unnecessary interventions × cost per intervention)
  - Break-even analysis

---

### Final Decision: ❌ DO NOT APPROVE FOR IMMEDIATE PRODUCTION DEPLOYMENT

### Recommendation: APPROVE FOR PILOT DEPLOYMENT WITH CONDITIONS

**Blocking issues that must be resolved:**
1. **Fairness analysis** — Complete subgroup performance evaluation (no deployment without this)
2. **Evaluation methodology** — Confirm temporal splitting was used
3. **AUC-ROC / AUC-PR** — Report these threshold-independent metrics

**Conditions for pilot deployment:**
1. Deploy in **shadow mode** first (model runs, scores logged, not shown to clinicians) — 30 days
2. During shadow mode: validate performance on live data, confirm no leakage, assess fairness
3. After shadow mode passes: limited pilot with 2-3 departments, clinicians see scores
4. Data freshness must be reduced to <1 hour for clinical features
5. Monitoring infrastructure (Q18 metrics) must be operational
6. Threshold must be optimized using clinical cost-benefit analysis
7. Physician feedback mechanism must be in place (override + feedback)
8. Rollback plan defined and tested

**Timeline:**
- Month 1: Shadow deployment + monitoring setup + fairness analysis
- Month 2-3: Pilot deployment in selected departments
- Month 4: Full production deployment (if pilot metrics are met)

**Justification for not deploying immediately:**
The model shows promise (81% recall is meaningful), but deploying a clinical decision support system without fairness analysis, proper evaluation methodology confirmation, and a monitoring plan puts patients at risk and exposes the hospital to regulatory and ethical liability. A structured pilot approach protects patients while moving toward production.

---

## Summary: Key Concepts Tested Across Q2

| Concept | Questions |
|---------|-----------|
| **Target leakage** | Q1, Q2, Q4, Q5, Q7 |
| **Feature engineering** | Q2, Q4, Q5, Q7 |
| **Temporal data handling** | Q3, Q5, Q6, Q7 |
| **Evaluation methodology** | Q6, Q8, Q9, Q10 |
| **Class imbalance** | Q9, Q10 |
| **Feedback loops** | Q11 |
| **Model drift (data + concept)** | Q12, Q13 |
| **Missing data handling** | Q14 |
| **Fairness / bias** | Q15 |
| **Explainability** | Q16 |
| **System architecture** | Q17, Q19 |
| **Production monitoring** | Q18, Q19 |
| **Governance / production readiness** | Q20 |
