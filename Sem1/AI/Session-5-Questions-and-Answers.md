# Session 5: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What are the three types of data? Compare them with real-world examples.

**Answer:**
All data in the world falls into three categories based on how it is organised:

| Aspect | Structured Data | Semi-Structured Data | Unstructured Data |
|---|---|---|---|
| **What it is** | Data in fixed rows and columns with a defined schema | Data with some organisation (tags/keys) but no rigid table structure | Data with no predefined format |
| **Storage** | Relational databases (MySQL, PostgreSQL) | Document databases (MongoDB), data lakes | Object storage (S3), media servers |
| **How to query** | SQL queries | JSON path queries, parsing | AI models, vector search |
| **% of enterprise data** | ~20% | ~10% | ~70% |
| **Example — Banking** | HDFC Bank customer table: Name, Account No, Balance | JSON transaction logs with nested payment details | Customer call recordings to the helpline |
| **Example — E-commerce** | Flipkart orders table: Order ID, Product, Price, Date | Product catalog in JSON with variable attributes | Customer review photos and videos |
| **Example — Healthcare** | Patient records: Age, Blood Group, Test Results | HL7/FHIR medical data exchange files | X-ray images, MRI scans, doctor's handwritten notes |

**Key insight for exams:** ~70% of all enterprise data is unstructured. This is why AI is so valuable — only AI models (CNNs, transformers) can extract useful information from unstructured data at scale. Traditional software cannot.

---

## Q2: What is Multimodal AI? Why is it important?

**Answer:**
Multimodal AI refers to AI systems that can understand, process, and combine multiple types of data (text, images, audio, video) simultaneously in a single model.

**Simple analogy:** A human doctor doesn't just read the blood report (structured data). They also look at the X-ray (image), listen to the patient describe symptoms (audio/text), and observe the patient's physical appearance (video). Multimodal AI does the same thing.

**Real examples:**

| Multimodal System | Data Types Combined | What It Does |
|---|---|---|
| **Google Gemini** | Text + Images + Audio + Video | Can watch a video and answer questions about it |
| **GPT-4o** | Text + Images + Audio | Can see a photo of a math problem and solve it |
| **Tesla Autopilot** | Camera images + LIDAR + GPS + Sensor data | Combines multiple sensor inputs to drive safely |
| **Apollo Hospitals AI** | Patient records (structured) + CT scans (images) + doctor notes (text) | Better diagnosis by combining all patient information |

**Why it matters:**
- Real-world problems are never single-type. A fraud detection system needs transaction data (structured) + user behaviour patterns (semi-structured) + voice recordings of customer calls (unstructured).
- Multimodal models outperform single-modal ones because they see the complete picture.

---

## Q3: Compare Internal vs External data sources with examples. Why do AI systems need both?

**Answer:**

**Internal Data Sources** — data generated within the organisation:

| Source | Example |
|---|---|
| Transactional databases | Swiggy's order history — what you ordered, when, from where |
| CRM systems | Salesforce records of customer interactions at HDFC Bank |
| Application logs | Zomato app logs — which restaurants you browsed, how long you spent on each |
| IoT/Sensor data | Tata Steel factory sensors — temperature, pressure, vibration readings |
| Employee data | HR systems at TCS — attendance, performance reviews |

**External Data Sources** — data from outside the organisation:

| Source | Example |
|---|---|
| Public datasets | Census of India data, government open data portal (data.gov.in) |
| Third-party APIs | Google Maps API for location data, AccuWeather for weather data |
| Social media | Twitter/X sentiment about a brand, Reddit discussions |
| Web scraping | Competitor pricing from Amazon/Flipkart |
| Purchased data | Nielsen market research data, credit bureau (CIBIL) scores |

**Why both are needed:**
- Swiggy uses internal data (your past orders) + external data (weather, traffic, local events) to predict delivery time. On a rainy day during an IPL match, delivery times go up — the model needs weather and event data (external) combined with restaurant preparation times (internal) to predict accurately.

---

## Q4: What are the 6 dimensions of data quality? Explain each with an example.

**Answer:**
Data quality is measured on 6 dimensions. Think of them as a health checkup for your data:

| Dimension | What It Means | Good Example | Bad Example |
|---|---|---|---|
| **Accuracy** | Data correctly represents reality | Customer age = 28 (actual age is 28) | Customer age = 280 (typo — clearly wrong) |
| **Completeness** | No missing values in critical fields | All Aadhaar records have name + address + photo | 30% of patient records missing blood group |
| **Consistency** | Same fact stored the same way everywhere | Customer name is "Rahul Sharma" in all systems | "Rahul Sharma" in CRM, "R. Sharma" in billing, "RAHUL" in support tickets |
| **Timeliness** | Data is up-to-date and available when needed | Stock prices updated every second on Zerodha | Using 3-month-old inventory data for demand prediction |
| **Validity** | Data follows defined rules and formats | Phone number has exactly 10 digits | Phone number field contains "call me later" |
| **Uniqueness** | No duplicate records | Each PAN card maps to one person | Same customer appears 5 times in database with slightly different spellings |

**Exam tip:** Remember with the mnemonic **"ACCTVU"** — Accuracy, Completeness, Consistency, Timeliness, Validity, Uniqueness.

---

## Q5: What are the common data quality issues in Machine Learning? Explain each.

**Answer:**

### 1. Missing Values
- **What:** Some fields in the dataset are empty.
- **Example:** In an HDFC Bank loan prediction dataset, 15% of applicants didn't provide their income. If you drop those rows, you lose data. If you fill with average, you introduce noise.
- **Impact:** Model either loses training data (if rows are dropped) or learns incorrect patterns (if imputed badly).

### 2. Outliers
- **What:** Extreme values that don't represent normal behaviour.
- **Example:** In a Zomato delivery time dataset, most deliveries take 20-45 minutes. But one entry shows 720 minutes (12 hours) because the rider's app crashed and the order was reassigned. This outlier will distort the model's average prediction.
- **Impact:** Models like linear regression are very sensitive to outliers — one extreme value can shift the entire prediction line.

### 3. Data Leakage
- **What:** Information from the future or from the target variable accidentally leaks into training features.
- **Example:** You're building a model to predict if a Flipkart customer will return a product. You accidentally include the feature "refund_amount" — but this field is only filled AFTER the return happens. The model gets 99% accuracy in training but fails completely in production because refund_amount won't be available at prediction time.
- **Impact:** Artificially inflated accuracy during training, complete failure in production. This is the most dangerous data quality issue.

### 4. Class Imbalance
- **What:** One category vastly outnumbers others in the dataset.
- **Example:** In HDFC Bank's fraud detection data, 99.5% of transactions are legitimate, only 0.5% are fraud. A lazy model that predicts "not fraud" for every transaction gets 99.5% accuracy but catches zero frauds.
- **Fixes:** Oversampling (SMOTE), undersampling, cost-sensitive learning, using metrics like F1-score instead of accuracy.

### 5. Label Noise
- **What:** Incorrect labels in supervised learning data.
- **Example:** In a product categorisation task for Amazon India, a "laptop bag" is mistakenly labelled as "laptop." The model learns that bags are laptops.
- **Impact:** Silently reduces model quality — doesn't crash, just makes the model subtly wrong.

---

## Q6: Scenario — A Swiggy data scientist notices their delivery time prediction model has 95% accuracy on test data but performs terribly in production. What could be wrong?

**Answer:**
This is a classic symptom of **data leakage**. Here's the likely diagnosis:

**Most probable cause — Data Leakage:**
- The training dataset likely includes features that are only available AFTER the delivery is complete. For example:
  - `actual_delivery_time` — this IS the answer, and a derivative of it may have leaked in
  - `rider_rating_for_this_order` — only available after delivery
  - `customer_feedback_score` — only available after delivery
- In training, the model uses this future information and gets 95% accuracy. In production, these features don't exist yet, so the model fails.

**Other possibilities:**
1. **Distribution shift:** Training data was from Bangalore (short distances, good roads) but production includes Tier-2 cities with different traffic patterns.
2. **Temporal leakage:** Model was trained on data sorted by time, and the train-test split wasn't done chronologically — so the model "saw" future patterns during training.
3. **Feature staleness:** Features like "restaurant_avg_prep_time" were calculated on old data but restaurant changed their menu/process.

**How to fix:**
- Audit every feature: "Would this feature be available at prediction time?"
- Use time-based train-test splits (not random splits)
- Monitor model performance in production using shadow deployment

---

## Q7: What is Data Governance? List its 7 key components.

**Answer:**
Data Governance is the set of policies, processes, and standards that ensure data is managed properly, securely, and in compliance with regulations throughout its lifecycle.

**Simple analogy:** Just like a company has HR policies for managing employees (hiring rules, leave policies, exit procedures), data governance is the "HR policy for data" — rules for how data is collected, stored, used, shared, and deleted.

**7 Components of Data Governance:**

| Component | What It Does | Real Example |
|---|---|---|
| **1. Data Ownership** | Assigns responsibility — who owns which data | At HDFC Bank, the "Customer Data" owner is the Head of Retail Banking. They decide who can access it. |
| **2. Data Stewardship** | Day-to-day management and quality monitoring | A data steward at Flipkart monitors product catalog quality — fixing wrong prices, duplicate listings. |
| **3. Data Quality Management** | Processes to measure and improve data quality | Aadhaar system validates biometric data quality — rejects blurry fingerprints, asks for re-scan. |
| **4. Data Security & Privacy** | Protects data from unauthorised access | PhonePe encrypts all UPI transaction data. Only authorised systems can decrypt. |
| **5. Data Lifecycle Management** | Manages data from creation to deletion | IRCTC deletes PNR data after 2 years. Old booking data is archived and eventually purged. |
| **6. Metadata Management** | Maintains information about data (data about data) | A data catalog at Infosys that tells you: this table has 50 columns, updated daily, owned by Finance team, contains PII. |
| **7. Regulatory Compliance** | Ensures all legal requirements are met | Ensuring Aadhaar data handling complies with India's DPDP Act 2023. |

---

## Q8: What is GDPR? List its key principles and rights.

**Answer:**
**GDPR** (General Data Protection Regulation) is the European Union's data protection law, effective since May 2018. It is considered the gold standard for data privacy worldwide and has influenced India's own DPDP Act.

**Key Principles:**

| Principle | What It Means | Example |
|---|---|---|
| **Lawfulness & Consent** | Must have legal basis to collect data; explicit consent required | Google must ask EU users "Do you consent to data collection?" — can't just bury it in terms. |
| **Purpose Limitation** | Data collected for one purpose can't be used for another | If Zara collects email for order updates, they can't use it for marketing without separate consent. |
| **Data Minimisation** | Collect only what you need | A food delivery app shouldn't ask for your Aadhaar number — it only needs your address and phone. |
| **Accuracy** | Keep data correct and up-to-date | If a customer changes their address, the company must update it. |
| **Storage Limitation** | Don't keep data longer than necessary | Delete customer data after account closure (within defined period). |
| **Integrity & Confidentiality** | Protect data with appropriate security | Encrypt personal data, use access controls. |

**Key Rights of Individuals:**
1. **Right to Access** — "Show me all data you have about me"
2. **Right to Rectification** — "Fix my incorrect data"
3. **Right to Erasure (Right to be Forgotten)** — "Delete all my data"
4. **Right to Data Portability** — "Give me my data in a downloadable format so I can move to a competitor"
5. **Right to Object** — "Stop processing my data for marketing"

**Penalty:** Up to €20 million or 4% of global annual revenue, whichever is higher.

---

## Q9: What is India's DPDP Act 2023? How is it different from GDPR?

**Answer:**
The **Digital Personal Data Protection Act (DPDP Act)** 2023 is India's first comprehensive data protection law. It was passed by Parliament in August 2023.

**Key features:**

| Feature | DPDP Act (India) | GDPR (EU) |
|---|---|---|
| **Scope** | Applies to processing of digital personal data in India | Applies to processing of personal data in the EU |
| **Consent** | Requires "free, specific, informed, and unambiguous" consent | Same — explicit consent required |
| **Data Principal** | The person whose data it is (called "Data Principal" in DPDP) | Called "Data Subject" in GDPR |
| **Data Fiduciary** | The organisation processing data (called "Data Fiduciary") | Called "Data Controller" in GDPR |
| **Right to Erasure** | Yes — you can ask any company to delete your data | Yes — Right to be Forgotten |
| **Children's data** | Extra protections — verifiable parental consent required for under-18 | Extra protections for under-16 |
| **Penalties** | Up to ₹250 crore per violation | Up to €20 million or 4% of global revenue |
| **Data Localisation** | Government can restrict transfer to certain countries (blacklist approach) | Can transfer to "adequate" countries (whitelist approach) |
| **Right to Data Portability** | Not explicitly included | Included |

**Impact on Indian companies:**
- Zomato, Swiggy, Flipkart must now get explicit consent before collecting personal data
- CRED must allow users to request deletion of all their financial data
- Byju's must get parental consent before collecting data from children under 18

---

## Q10: What are the AI-specific governance challenges? Explain consent, right to be forgotten, bias auditing, and machine unlearning.

**Answer:**
AI systems create unique governance challenges that traditional data governance wasn't designed to handle:

### 1. Consent in AI
- **Problem:** When you give consent for your data to be collected, did you consent for it to be used to train an AI model? Probably not.
- **Example:** You uploaded photos to Instagram. Meta used those photos to train their image generation AI (Stable Diffusion competitor). You consented to "sharing photos" — not to "training an AI model with your face."
- **Governance challenge:** Consent must be specific to AI training, not just data collection.

### 2. Right to be Forgotten in AI
- **Problem:** You can delete someone's data from a database. But if an AI model was already trained on that data, the knowledge is embedded in the model's weights. Deleting the original data doesn't remove it from the model's memory.
- **Example:** A person asks CRED to delete their financial data. CRED deletes from the database. But their credit score model was already trained on that person's data — the model still "remembers" the patterns.
- **Challenge:** How do you make an AI model "forget" specific data?

### 3. Machine Unlearning
- **What:** A technique to remove the influence of specific data points from a trained model WITHOUT retraining from scratch.
- **Why needed:** Retraining a large model costs crores of rupees. You can't retrain every time someone asks to be forgotten.
- **Current state:** This is an active research area. Google published research on approximate unlearning in 2023, but no perfect solution exists yet.
- **Practical workaround:** Companies maintain "do-not-use" lists and retrain periodically, excluding flagged data.

### 4. Bias Auditing
- **Problem:** AI models can learn biases from historical data and make discriminatory decisions.
- **Example:** Amazon built an AI hiring tool trained on past hiring data. Since the company had historically hired more men, the model learned to penalise resumes with the word "women's" (e.g., "women's chess club"). Amazon had to scrap the system.
- **Indian context:** If HDFC Bank's loan approval AI is trained on historical data where fewer loans were approved for certain communities/regions, the model may perpetuate that bias.
- **Governance requirement:** Regular bias audits — test the model on different demographic groups and ensure similar outcomes.

---

## Q11: Explain the Facebook/Cambridge Analytica data scandal. What governance failures occurred?

**Answer:**
This is the most important data governance case study in modern history.

**What happened (timeline):**

1. **2013:** A Cambridge University researcher named Aleksandr Kogan created a personality quiz app on Facebook called "thisisyourdigitallife."
2. **~270,000 people** took the quiz and gave the app permission to access their profile data.
3. **The loophole:** Facebook's API at the time also gave the app access to all the quiz-taker's FRIENDS' data — without the friends' consent.
4. **Result:** Data of **87 million users** was harvested — from just 270,000 who took the quiz.
5. **Kogan shared this data** with Cambridge Analytica (a political consulting firm) — violating Facebook's terms.
6. **Cambridge Analytica used the data** to build psychological profiles and target political ads during the 2016 US Presidential Election and the Brexit referendum.
7. **2018:** Whistleblower Christopher Wylie exposed the scandal. Facebook's stock dropped $120 billion.

**Governance failures:**

| Failure | What Went Wrong |
|---|---|
| **Consent violation** | 87M users never consented — only 270K did. Friends' data was collected without any consent. |
| **Purpose limitation breach** | Data collected for "academic research" was used for political advertising. |
| **Third-party data sharing** | Facebook allowed apps to harvest friends' data — massive over-sharing. |
| **No data audit** | Facebook never checked what Kogan did with the data. |
| **No enforcement** | Facebook asked Cambridge Analytica to "delete the data" — but never verified they actually did. |
| **Delayed disclosure** | Facebook knew about the breach in 2015 but didn't inform users until 2018. |

**Consequences:**
- Facebook fined **$5 billion** by the US FTC (largest privacy fine in history)
- Cambridge Analytica shut down
- Led to acceleration of GDPR enforcement and global privacy laws
- India's DPDP Act was partly influenced by this scandal

**Exam relevance:** This case study perfectly illustrates failures across ALL 7 governance components — ownership (who was responsible?), security (data leaked to third party), consent (friends never consented), purpose limitation (research → political ads), compliance (violated existing rules).

---

## Q12: Scenario — A healthcare startup collects patient data for diagnosis recommendations. They later want to use the same data to train an AI model and sell insights to pharma companies. What governance issues arise?

**Answer:**
Multiple governance violations are at play:

**1. Purpose Limitation Violation (GDPR Article 5, DPDP Act Section 4):**
- Data was collected for "diagnosis recommendations" — using it to train AI and sell to pharma companies is a completely different purpose.
- This requires fresh, explicit consent from every patient.

**2. Consent Issue:**
- Original consent was for "helping with MY diagnosis." Patients didn't agree to their data being used to train models or being shared with pharma companies.
- Under DPDP Act, this is a violation — consent must be "specific" to each purpose.

**3. Data Minimisation:**
- For diagnosis, you need the patient's relevant medical data. For pharma insights, you might be using ALL medical data — this exceeds what's necessary for the original purpose.

**4. Anonymisation Requirement:**
- If they must share with pharma companies, data MUST be anonymised (remove name, Aadhaar, phone number, etc.).
- But research shows that even "anonymised" health data can be re-identified using combinations of age + location + condition (k-anonymity attacks).

**5. DPDP Act Penalties:**
- If the startup operates in India, penalties can be up to ₹250 crore.

**What they should do:**
1. Go back to patients and get fresh consent specifically for AI training and pharma data sharing
2. Offer clear opt-out option
3. Anonymise data using differential privacy techniques before sharing
4. Conduct a Data Protection Impact Assessment (DPIA)
5. Appoint a Data Protection Officer (DPO)

---

## Q13: What is Data Leakage in ML? Give three different types with examples.

**Answer:**
Data leakage occurs when information that would not be available at prediction time is used during model training. It's the #1 reason models perform great in testing but fail in production.

**Type 1: Target Leakage (most common)**
- A feature is directly derived from or strongly correlated with the target variable.
- **Example:** Predicting loan default. Feature: "number_of_collection_calls." But collection calls only happen AFTER a person defaults. At prediction time (when the loan is being approved), this feature is zero for everyone.

**Type 2: Temporal Leakage**
- Future information leaks into past records because data isn't split by time.
- **Example:** Predicting Sensex movement. If you randomly split data into train/test, the model may train on October data and test on September data — it's learning from the future. Fix: always use time-based splits for time-series problems.

**Type 3: Train-Test Contamination**
- Test data leaks into the training process.
- **Example:** You normalise (scale) your entire dataset BEFORE splitting into train/test. The scaling parameters (mean, standard deviation) now include information from the test set. Fix: fit the scaler on training data only, then apply to test data.

**How to detect leakage:**
- If your model accuracy is "too good to be true" (99%+), suspect leakage
- Check if any feature has near-perfect correlation with the target
- Validate that every feature would be available in a real production scenario
- Use time-based cross-validation for temporal data

---

## Q14: A bank's credit scoring model rejects 80% of loan applications from women but only 40% from men. Is this necessarily bias? How should they investigate?

**Answer:**
This is NOT necessarily bias, but it absolutely requires investigation. Here's a systematic approach:

**Step 1: Check for legitimate explanations**
- Are the income levels, employment history, and credit scores actually different between the two groups in the data?
- If women applicants genuinely have lower incomes (due to societal factors), the model may be accurately reflecting the data — but it's still a fairness problem.

**Step 2: Check for proxy variables**
- Even if "gender" isn't a direct input, the model might use proxy features that correlate with gender:
  - Part-time employment status (more common for women)
  - Certain job titles
  - Age gaps in employment (maternity leave)
- These proxies effectively encode gender into the model.

**Step 3: Conduct a bias audit**
- Compare model predictions across demographic groups (this is what "disparate impact analysis" means)
- Use the **80% rule (four-fifths rule):** if the selection rate for a protected group is less than 80% of the rate for the most favoured group, there's evidence of adverse impact
- Here: Women approval rate = 20%, Men = 60%. 20/60 = 33%. This is well below 80% — clear adverse impact.

**Step 4: Remediation options**
1. **Remove proxy features** that encode gender
2. **Rebalance training data** to have equal representation
3. **Apply fairness constraints** during training (e.g., equalised odds)
4. **Use different thresholds** for different groups (controversial but legally used)
5. **Regular monitoring** — run bias reports monthly

**Regulatory requirement under DPDP Act:** While the Act doesn't explicitly mandate bias auditing, the principles of fairness and preventing harm apply. RBI guidelines for lending are also increasingly focusing on algorithmic fairness.

---

## Q15: Scenario — Zomato trains an AI model to predict restaurant ratings. Which data quality issues should they check for?

**Answer:**
Here's a systematic data quality audit for this use case:

| Quality Issue | What to Check | Example Problem |
|---|---|---|
| **Missing Values** | Are there restaurants with no ratings? Restaurants with missing cuisine type? | New restaurants have zero reviews — model can't predict for them (cold start). |
| **Outliers** | Extreme rating patterns | A restaurant has 10,000 five-star ratings in one day — likely fake/bot reviews. |
| **Class Imbalance** | Is the rating distribution balanced? | 70% of ratings are 4-5 stars (people only review when happy). Model under-predicts low ratings. |
| **Label Noise** | Are ratings accurate? | Competitor restaurants post fake 1-star reviews. Users rate 1-star because of late delivery (Zomato's fault, not restaurant's). |
| **Data Leakage** | Features that shouldn't be available at prediction time | Using "total_orders_this_month" to predict today's rating — future orders leak in. |
| **Consistency** | Same restaurant data across systems | Restaurant name is "Biryani House" in one table, "Biryani House - Koramangala" in another. |
| **Timeliness** | Is data fresh? | Restaurant changed its chef 3 months ago. Old ratings don't reflect current quality. |
| **Selection Bias** | Does data represent all users? | Only tech-savvy users who use the app rate restaurants. Older customers who order by phone are not represented. |

**Recommended approach:**
1. Profile the data first — check distributions, missing percentages, duplicates
2. Remove fake reviews using anomaly detection
3. Separate delivery experience from food quality in ratings
4. Use time-weighted ratings (recent ratings matter more)
5. Handle cold-start restaurants separately (use restaurant features, not past ratings)

---

## Q16: What is the difference between Data Privacy and Data Security? Why are both important for AI?

**Answer:**

| Aspect | Data Privacy | Data Security |
|---|---|---|
| **Focus** | WHO can access data and FOR WHAT purpose | HOW data is protected from unauthorised access |
| **Concern** | Rights of individuals over their personal data | Protecting data from breaches, theft, hacking |
| **Example** | Deciding that only the doctor (not the receptionist) should see patient diagnosis | Encrypting the patient database so hackers can't read it |
| **Regulation** | GDPR, DPDP Act | ISO 27001, PCI DSS |
| **Violation example** | PhonePe sharing your transaction data with advertisers without consent | PhonePe's database getting hacked and transaction data being leaked |

**Why both matter for AI:**
- **Privacy in AI:** If you train a language model on private emails, the model might memorise and reveal personal information. Samsung employees accidentally leaked company secrets through ChatGPT — a privacy failure.
- **Security in AI:** An attacker can perform "model inversion attacks" — querying the model repeatedly to reconstruct training data. If the model was trained on medical records, attackers could extract patient information.

**Key point:** You can have security without privacy (data is encrypted but shared with everyone) and privacy without security (access rules exist but data is unencrypted). You need BOTH.

---

## Q17: How does class imbalance affect different types of ML problems? Give Indian examples for each.

**Answer:**

### Fraud Detection (HDFC Bank)
- **Imbalance:** 99.5% legitimate, 0.5% fraud
- **Problem:** Model predicts "not fraud" for everything → 99.5% accuracy but zero fraud caught
- **Solution:** Use SMOTE oversampling, cost-sensitive learning, measure F1-score not accuracy

### Disease Detection (Apollo Hospitals)
- **Imbalance:** 98% healthy, 2% diseased (e.g., cancer screening)
- **Problem:** Model predicts "healthy" for everyone → misses critical cancer cases
- **Solution:** Use recall-focused metrics (catching all true positives matters more than precision)

### Spam Detection (Gmail/Indian email users)
- **Imbalance:** 80% spam, 20% legitimate
- **Problem:** Reverse imbalance — model might classify everything as spam, causing important emails to be missed
- **Solution:** Precision-focused metrics (not marking legitimate emails as spam is critical)

### Customer Churn (Jio)
- **Imbalance:** 95% stay, 5% churn
- **Problem:** Model predicts everyone stays → misses the 5% who leave
- **Solution:** Stratified sampling, ensemble methods, adjust decision threshold from 0.5 to a lower value

**General solutions for class imbalance:**
1. **Oversampling minority class** — SMOTE creates synthetic examples
2. **Undersampling majority class** — randomly remove majority samples
3. **Cost-sensitive learning** — penalise misclassification of minority class more
4. **Ensemble methods** — use balanced random forests
5. **Change the metric** — use F1, AUC-ROC, precision-recall instead of accuracy

---

## Q18: What is the "Right to be Forgotten" and why is it technically challenging for AI systems?

**Answer:**
The Right to be Forgotten (GDPR Article 17, also in DPDP Act) gives individuals the right to request that an organisation delete ALL their personal data.

**For traditional databases — simple:**
- Customer requests deletion from Flipkart
- Flipkart runs `DELETE FROM customers WHERE id = 12345` across all databases
- Done — data is removed

**For AI systems — extremely difficult:**
1. **Model memorisation:** Large language models memorise training data. Researchers have extracted phone numbers, email addresses, and code snippets from GPT-2's training data just by prompting it cleverly.
2. **Feature engineering:** The customer's data may have been used to create aggregate features ("average spending of customers in Mumbai"). Deleting the individual record doesn't remove their contribution to the aggregate.
3. **Model weights:** The model's neural network weights were shaped by this person's data during training. You can't simply "subtract" one person's influence.
4. **Cost of retraining:** Retraining GPT-4 costs ~$100M. You can't retrain every time someone requests deletion.

**Current approaches:**
- **Machine Unlearning:** Research area focused on surgically removing data influence without full retraining. Google and Apple are actively researching this.
- **Differential Privacy:** Add noise during training so no individual's data significantly influences the model — makes unlearning unnecessary.
- **Periodic retraining with exclusion lists:** Maintain a list of "do not use" data points, retrain the model periodically excluding this data.
- **Data sharding:** Train sub-models on different data shards. To "forget" a person, retrain only the shard containing their data.

---

## Q19: Compare data governance requirements for GDPR vs DPDP Act in a table format.

**Answer:**

| Aspect | GDPR (EU) | DPDP Act 2023 (India) |
|---|---|---|
| **Effective since** | May 2018 | Rules still being finalised (Act passed August 2023) |
| **Applies to** | Any organisation processing EU residents' data | Any organisation processing digital personal data in India |
| **Terminology** | Data Subject, Data Controller, Data Processor | Data Principal, Data Fiduciary, Data Processor |
| **Consent** | Explicit, freely given, specific, informed | Free, specific, informed, unambiguous |
| **Children's data** | Below 16 (member states can lower to 13) | Below 18 — parental consent required |
| **Right to Access** | Yes | Yes |
| **Right to Erasure** | Yes (Right to be Forgotten) | Yes |
| **Right to Portability** | Yes (can take data to competitor) | Not explicitly included |
| **DPO requirement** | Mandatory for certain organisations | Data Protection Board of India to be established |
| **Cross-border transfer** | Allowed to "adequate" countries (whitelist) | Government can block specific countries (blacklist) |
| **Max penalty** | €20M or 4% of global revenue | ₹250 crore (~€28M) |
| **Breach notification** | Within 72 hours | Timeline to be specified in rules |
| **Algorithmic decision-making** | Right to explanation of automated decisions (Article 22) | Not explicitly covered |

**Key exam takeaway:** DPDP Act is heavily inspired by GDPR but is simpler and less detailed. It lacks some GDPR provisions like data portability and algorithmic transparency rights.

---

## Q20: Scenario — An Indian ed-tech company collects children's learning data. What compliance steps must they follow under the DPDP Act?

**Answer:**
Under the DPDP Act 2023, children (below 18 years) receive special protection. Here's what the ed-tech company MUST do:

**1. Verifiable Parental Consent:**
- Cannot collect ANY data from a child without verifiable consent from a parent/guardian
- The "I agree" checkbox clicked by a 10-year-old is NOT valid consent
- Must implement age-gating mechanisms — verify the user's age before data collection

**2. No Behavioural Tracking of Children:**
- Section 9 prohibits tracking, behavioural monitoring, or targeted advertising directed at children
- This means: NO personalised ads based on a child's learning patterns
- A company like Byju's cannot use a child's usage data to show them targeted ads

**3. No Detrimental Processing:**
- Data processing must not cause harm to the child's well-being
- Gamification that creates addiction patterns could be argued as "detrimental processing"

**4. Data Minimisation:**
- Collect only what's needed for the educational service
- Don't collect parent's income, family details, or other irrelevant data

**5. Purpose Limitation:**
- Data collected for "education" cannot be used for marketing or sold to third parties

**6. Compliance Steps:**
1. Implement robust age verification
2. Build a parental consent flow (verified, not just a checkbox)
3. Audit all data collection points — remove anything not essential for education
4. Disable behavioural tracking and targeted ads for child users
5. Appoint a Data Protection Officer
6. Maintain records of all consent obtained
7. Build a mechanism for parents to access/delete their child's data
8. Conduct regular Data Protection Impact Assessments (DPIA)

**Penalties for non-compliance:** Up to ₹200 crore for processing children's data without proper safeguards.
