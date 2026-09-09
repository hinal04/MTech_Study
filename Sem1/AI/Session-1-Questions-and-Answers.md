# Session 1: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is Artificial Intelligence? How is it different from traditional software?

**Answer:**
Artificial Intelligence (AI) means making computers do tasks that normally need human intelligence — like understanding language, recognising faces, making decisions, or learning from experience.

Traditional software follows fixed rules written by a programmer. For example, a calculator always adds 2 + 2 = 4 using a fixed formula. AI, on the other hand, can learn patterns from data and handle tasks where rules are not easy to write — like identifying whether a photo contains a dog or a cat.

Key difference: In traditional software, a human writes all the logic. In AI, the system learns its own logic from data.

Real-world example: A thermostat that turns on AC when temperature crosses 25°C is traditional software (fixed rule). Gmail's spam filter that learns from millions of emails to identify new types of spam is AI (learned from data).

---

## Q2: Explain the relationship between AI, Machine Learning, and Deep Learning with a diagram.

**Answer:**
AI, ML, and DL are nested concepts — each one fits inside the previous like Russian dolls:

```
┌──────────────────────────────────────────┐
│       Artificial Intelligence (AI)       │
│   (Any system that mimics human intel.)  │
│                                          │
│   ┌──────────────────────────────────┐   │
│   │      Machine Learning (ML)       │   │
│   │   (Systems that learn from data) │   │
│   │                                  │   │
│   │   ┌──────────────────────────┐   │   │
│   │   │    Deep Learning (DL)    │   │   │
│   │   │ (ML using deep neural    │   │   │
│   │   │  networks)               │   │   │
│   │   └──────────────────────────┘   │   │
│   └──────────────────────────────────┘   │
└──────────────────────────────────────────┘
```

- **AI** is the broadest term — the goal of making computers smart.
- **ML** is a subset of AI — machines learn patterns from data instead of being explicitly programmed.
- **DL** is a subset of ML — uses multi-layered neural networks to learn very complex patterns from raw data (images, text, audio).

All Deep Learning is Machine Learning, and all Machine Learning is AI. But not all AI is ML (rule-based expert systems are AI but not ML), and not all ML is DL (a decision tree is ML but not DL).

---

## Q3: Compare AI, Machine Learning, and Deep Learning across five key aspects.

**Answer:**

| Aspect | Traditional AI | Machine Learning | Deep Learning |
|---|---|---|---|
| **How it works** | Human writes rules ("if X then Y") | Algorithm learns rules from data | Neural network discovers features AND rules from raw data |
| **Who writes the logic?** | Human programmer | Algorithm learns automatically | Network learns everything on its own |
| **Data needed** | Little or none | Thousands to millions of examples | Millions to billions of examples |
| **Computing power** | Low | Medium | High (needs GPUs/TPUs) |
| **Best for** | Problems with clear, known rules | Problems where patterns exist but are hard to code | Complex data like images, text, and audio |

Example — Building a spam filter:
- **Traditional AI:** Human writes rules like "if email contains 'lottery' → spam." But spammers change words, so rules break.
- **ML:** Show the system 100,000 emails labelled spam/not-spam. It automatically discovers patterns (certain words, domains, link counts).
- **DL:** Feed the raw email (text + HTML + images + metadata) to a deep neural network. It figures out the features AND the rules by itself.

---

## Q4: When should you use Traditional AI, Machine Learning, or Deep Learning? Give examples.

**Answer:**

| Situation | Best Approach | Why | Example |
|---|---|---|---|
| Rules are clear and simple | Traditional AI (rules) | No need for ML — rules work fine | Age verification: if age > 18, allow entry |
| Patterns exist but hard to write as rules | Machine Learning | Let data reveal the patterns | Credit card fraud detection — too many patterns for a human to code |
| Data is complex (images, speech, text) | Deep Learning | Neural networks handle raw complexity well | Google Photos recognising your friends' faces |
| Very little data available | Traditional AI | ML and DL need lots of data to learn | A small clinic with only 50 patient records |
| Data is abundant and compute is available | Deep Learning | More data + more compute = better DL performance | ChatGPT trained on trillions of words from the internet |

Key insight: Always start with the simplest approach that works. Don't use Deep Learning if a simple rule-based system solves the problem. Using DL to check "is age > 18?" would be like using a rocket to go to the neighbourhood shop.

---

## Q5: What is an AI System? Why is the model "less than 5% of the total system"?

**Answer:**
An AI system is the complete end-to-end setup needed to deliver AI to real users. It is NOT just the model (algorithm). The model is like the engine of a car — important, but useless without wheels, brakes, fuel system, and dashboard.

An AI system includes:
- **Data pipelines** — collecting and cleaning data
- **Feature engineering** — preparing inputs for the model
- **Model training** — teaching the AI
- **Serving infrastructure** — making predictions available to users
- **Monitoring** — checking if the AI still works well
- **Application layer** — the app or website users interact with

The model (algorithm) is typically less than 5% of the total code in a production AI system. The other 95% is everything around it — data pipelines, feature engineering, serving, monitoring, CI/CD, logging, and A/B testing.

Restaurant analogy: The chef's recipe (model) is important, but the restaurant system includes sourcing ingredients (data), kitchen equipment (infrastructure), the menu (application), the waiter serving food (serving layer), and customer feedback cards (monitoring). The recipe alone doesn't feed anyone.

---

## Q6: Describe the six stages of the AI System Lifecycle. Why is it a cycle and not a straight line?

**Answer:**

The six stages are:

| Stage | What Happens | Example (Swiggy Delivery) |
|---|---|---|
| **1. Define Problem** | Figure out what you're solving. Define success metric. | "Predict delivery time accurately. Success = prediction within 5 min of actual." |
| **2. Collect & Prepare Data** | Gather data, clean it, label it, split into train/test. | Collect past deliveries (prep time, distance, traffic, weather). Remove cancelled orders. |
| **3. Build & Train Model** | Choose algorithm, train on data, tune parameters. | Try linear regression, XGBoost, neural network. XGBoost predicts best. |
| **4. Deploy & Serve** | Put model into production for real users. | Deploy as API. When user orders, app calls API → gets predicted time → shows on screen. |
| **5. Monitor & Evaluate** | Watch real-world performance over time. | Track: Are actual delivery times matching predictions? Is error increasing? |
| **6. Iterate & Improve** | Fix issues, add new data, retrain, deploy improved version. | Add Diwali traffic data. Retrain. New model is 15% more accurate during festivals. |

**Why it's a cycle:** Unlike traditional software (a tax calculator works correctly forever), AI models degrade over time because the real world changes:
- Customer preferences change with seasons
- New types of fraud appear that the model has never seen
- A pandemic changes all buying patterns overnight

So AI systems need continuous retraining and monitoring — it's an infinite loop, not a one-time project. Netflix runs this cycle continuously — their recommendation models are never "done."

---

## Q7: What are the six key components of an AI system? Explain each briefly.

**Answer:**

| Component | What It Does | Example (Amazon) |
|---|---|---|
| **Data Pipeline** | Collects raw data, cleans it, transforms it, stores it. "Garbage in, garbage out." | Amazon collects clicks, purchases, searches, reviews from millions of users every second. |
| **Feature Pipeline** | Converts raw data into features — the specific inputs the model needs. | Raw: "User clicked product X at 10pm." Feature: "user_click_count_last_7_days = 45, category_preference = electronics." |
| **Model Pipeline** | Trains, evaluates, and selects the best model. Tracks versions. | Amazon tests hundreds of recommendation models. The best one gets promoted to production. |
| **Serving Infrastructure** | Hosts the trained model and answers prediction requests fast. | When you open Amazon, a model server computes personalised recommendations in milliseconds. |
| **Monitoring** | Watches if the model is working well. Alerts when something goes wrong. | Amazon monitors if recommendation click-through rates drop. If yes, alert fires. |
| **Application Layer** | The app/website users interact with. Connects predictions to user experience. | Amazon's website takes model scores and displays products ranked with "Recommended" badges. |

---

## Q8: What is Training-Serving Skew? Why is it a serious problem in AI systems?

**Answer:**
Training-serving skew happens when the data or features used during training are different from what's available during serving (production). The model sees different data in production than what it trained on, which causes accuracy to drop.

**Analogy:** Imagine studying for an exam using last year's question paper (training). But this year's exam (serving) has completely different questions. You prepared for the wrong thing — your performance will suffer.

**Real-world example:** A fraud detection model is trained using a feature called "time since last transaction." During training, this is calculated exactly from historical data. But during serving, the feature calculation has a bug that rounds to the nearest hour. The model gets slightly different inputs in production → accuracy drops silently.

Why it's serious:
- It doesn't cause a crash or error — the model still runs but gives wrong predictions
- It's very hard to detect without proper monitoring
- It can go unnoticed for weeks or months before someone complains

Prevention: Use a Feature Store (like Feast or Tecton) to ensure the exact same feature computation logic is used in both training and serving.

---

## Q9: Explain overfitting with a simple analogy. How does it affect AI systems?

**Answer:**
**Overfitting** is when a model memorises the training data too well and fails on new, unseen data.

**Analogy:** A student who memorises every answer from past exam papers word-for-word. When a slightly different question appears in the actual exam, they can't answer it because they memorised instead of understanding the concepts.

In AI terms:
- An overfitted model scores 99% on training data but only 60% on new data
- It has learned the noise and quirks of the training set instead of the real patterns
- It's like remembering that "Question 5 answer is always C" instead of learning the subject

Impact on AI systems:
- Model performs great during development/testing but fails in production
- Wastes time and resources — the team thinks they have a good model but it doesn't work in the real world

How to prevent it:
- Use more training data
- Use simpler models (fewer parameters)
- Apply regularisation techniques
- Always evaluate on a separate test set that the model has never seen

---

## Q10: What is Data Drift? Give a real-world example of how it can break an AI model.

**Answer:**
Data drift occurs when the real-world data changes over time, making the model's training data outdated. The patterns the model learned no longer match what's happening in the current world.

**Example 1 — COVID impact:** A loan approval model trained on 2019 data learned that "restaurant owner with steady income" is a safe borrower. During COVID-19 in 2020, restaurants shut down, and restaurant owners defaulted on loans. The model's assumptions were no longer valid — data drift made it approve risky loans.

**Example 2 — Seasonal drift:** A clothing recommendation model trained on winter data recommends sweaters and jackets. When summer comes, users want kurtas and cotton clothes, but the model keeps recommending winter wear.

**Example 3 — Swiggy delivery:** A delivery time model trained on normal traffic patterns fails during IPL match days or Diwali when traffic patterns are completely different from the training data.

Why it matters: Unlike bugs in traditional software (which cause immediate errors), data drift causes silent degradation. The model doesn't crash — it just slowly becomes wrong. Without monitoring, you won't know until customers start complaining weeks later.

---

## Q11: List five real-world AI applications across different industries with specific company examples.

**Answer:**

| Industry | Application | Company Example | Impact |
|---|---|---|---|
| **Healthcare** | Medical image analysis | Google DeepMind detected 50+ eye diseases from retinal scans with accuracy matching specialist doctors. Used in NHS hospitals. | Faster and more consistent diagnosis |
| **Finance** | Fraud detection | PayPal uses ML to evaluate 10+ million transactions daily. Reduced false positives by 50% compared to rule-based systems. | Catches fraud in milliseconds |
| **E-commerce** | Product recommendations | Amazon's "Customers who bought this also bought..." drives 35% of total revenue (~$130 billion/year influenced by AI). | Massive revenue increase |
| **Manufacturing** | Predictive maintenance | Siemens uses AI on turbine sensor data to predict failures 20 hours before they happen. | Saves millions in unplanned downtime |
| **Transportation** | Self-driving vehicles | Waymo robotaxis in San Francisco — fully driverless, no safety driver, carrying paying passengers daily. | Safer autonomous transport |

Indian examples:
- **Jio** uses AI to manage network traffic across 400+ million subscribers
- **HDFC Bank** uses ML for real-time fraud detection on every transaction
- **Swiggy/Zomato** use AI for delivery time prediction and restaurant recommendations

---

## Q12: What is A/B Testing in the context of AI systems? Why is it important?

**Answer:**
A/B Testing means comparing two versions of something on real users to measure which performs better.

How it works in AI systems:
- **Version A (Control):** The current model (e.g., recommendation model v1)
- **Version B (Treatment):** The new model (e.g., recommendation model v2)
- Split users randomly — 50% see recommendations from v1, 50% from v2
- After 1-2 weeks, compare metrics: Which version led to more clicks? More purchases? Higher watch time?
- If v2 is better, promote it to 100% of users. If not, discard it.

**Example:** Google runs 10,000+ A/B tests per year on Search results. Each tiny change to the ranking algorithm is tested on a small percentage of users before being rolled out to everyone.

Why it's important:
- A model that performs well on test data might not perform well with real users
- A/B testing is the only way to measure real-world impact
- Prevents deploying a model that looks good on paper but actually hurts the user experience
- Allows data-driven decisions instead of guesswork

---

## Q13: Scenario — A local kirana store owner in Mumbai asks you: "I have 500 customers. Should I use AI for my business?" What would you advise?

**Answer:**
Short answer: Not ML/DL, but simple rule-based automation could help.

Detailed reasoning:
1. **Data volume is too low.** With only 500 customers, there isn't enough data to train meaningful ML models. ML needs thousands to millions of data points.

2. **Simple rules work better here.** For a small store:
   - Track which products sell most using a basic spreadsheet → stock accordingly
   - Send WhatsApp reminders to regular customers when their usual items arrive → simple rule, not ML
   - Track expiry dates using a basic alert system → no AI needed

3. **Where AI might help indirectly:**
   - Use a ready-made accounting app (like Khatabook) that uses AI internally
   - Use Google Translate or ChatGPT to communicate with suppliers in different languages
   - Use an API-based tool for generating marketing messages

Key principle: Don't use AI where a simple formula or rule works fine. AI adds complexity, cost, and maintenance burden. For a 500-customer business, the return on investment of building a custom AI model would be negative.

---

## Q14: What is the difference between Training and Inference (Serving) in AI?

**Answer:**

| Aspect | Training | Inference (Serving) |
|---|---|---|
| **What happens** | Model learns patterns from data | Trained model makes predictions on new data |
| **When it happens** | Before deployment (offline, can take hours/days) | After deployment (real-time, must be fast) |
| **Data used** | Historical labelled data (training set) | New, unseen data from real users |
| **Compute needed** | Very high (GPUs running for hours/days) | Lower (needs to respond in milliseconds) |
| **Frequency** | Done periodically (weekly, monthly, or on-demand) | Done continuously (every time a user makes a request) |

**Analogy:** Training is like studying for an exam (takes days, needs heavy effort). Inference is like writing the exam (must answer each question quickly, uses what you already learned).

**Example — Swiggy:**
- Training: Feed 2 years of delivery data to the model. It learns patterns over several hours on a GPU cluster.
- Inference: When you place an order, the trained model predicts "42 minutes" in under 100 milliseconds.

---

## Q15: Why do AI models degrade over time while traditional software doesn't? Explain with examples.

**Answer:**
Traditional software follows fixed rules that don't change unless a programmer changes them. A function that calculates GST at 18% will always calculate correctly — it doesn't degrade.

AI models, however, learn from historical data. When the real world changes, the patterns the model learned become outdated. This is called **model degradation** or **concept drift**.

Examples of why models degrade:

1. **Customer behaviour changes:** A movie recommendation model trained in 2019 wouldn't predict the sudden popularity of pandemic-themed movies in 2020.

2. **New fraud patterns:** A fraud detection model trained on 2022 data won't catch new scam methods invented in 2024 (like UPI phishing scams).

3. **Market changes:** A house price prediction model trained during stable times would give wildly wrong predictions during a real estate boom or crash.

4. **Seasonal shifts:** A fashion recommendation model trained on monsoon data would recommend raincoats during winter.

5. **Regulatory changes:** A loan approval model trained before new RBI guidelines might approve loans that should now be rejected.

This is why the AI lifecycle is a cycle — models must be continuously monitored and retrained to stay accurate.

---

## Q16: What does "Garbage In, Garbage Out" mean in the context of AI systems?

**Answer:**
"Garbage In, Garbage Out" (GIGO) means that if you feed bad data into an AI model, you will get bad predictions out — no matter how advanced the algorithm is.

**Example 1 — Biased data:** If a hiring AI is trained only on data from male employees (because historically only men were hired), it will learn that "male" is a positive signal and discriminate against female candidates. The algorithm isn't biased — the data is.

**Example 2 — Dirty data:** If a house price dataset has incorrect entries (a Rs 50 lakh house entered as Rs 5 crore), the model will learn wrong patterns and make absurd predictions.

**Example 3 — Incomplete data:** If a delivery time prediction model doesn't include weather data in training, it won't know that rain slows down deliveries. Its predictions will be wrong on rainy days.

This is why the Data Pipeline component is so critical in AI systems. Companies like Amazon and Netflix spend more time on data collection, cleaning, and preparation than on model building.

---

## Q17: Give two AI application examples each from Healthcare and Finance. Explain how AI helps in each case.

**Answer:**

**Healthcare:**

1. **Medical Image Analysis (Google DeepMind):** Deep learning models analyse retinal scans, X-rays, and MRIs to detect diseases. DeepMind's model detected 50+ eye diseases from retinal scans with accuracy matching specialist doctors. This helps in early detection, especially in areas with doctor shortages (like rural India where specialist availability is low).

2. **Drug Discovery (Insilico Medicine):** AI models predict how different chemical compounds will interact with human proteins. What traditionally took 4-5 years of lab work can now be narrowed down to months. This speeds up the creation of new medicines and reduces the cost from billions to millions of dollars.

**Finance:**

1. **Fraud Detection (HDFC Bank/PayPal):** ML models analyse every transaction in real-time — checking amount, location, time, merchant type — and flag unusual patterns. PayPal evaluates 10+ million transactions daily and catches fraud within milliseconds before the transaction completes.

2. **Credit Scoring (CRED):** AI analyses spending patterns, repayment history, and income to predict if a user will default on payments. This helps decide credit limits and personalised offers. Unlike traditional rule-based scoring, ML can find complex patterns that humans might miss.

---

## Q18: What is the role of the Monitoring component in an AI system? What happens if monitoring is missing?

**Answer:**
The Monitoring component watches the deployed model's performance in production and alerts when something goes wrong.

What monitoring tracks:
- **Model accuracy over time:** Is the model still making correct predictions?
- **Data drift:** Has the incoming data changed compared to what the model was trained on?
- **System health:** Is the model responding fast enough? Any server errors?
- **Business metrics:** Is the model actually improving business outcomes (revenue, user satisfaction)?

What happens without monitoring — the **Zillow disaster (2020):**
Zillow built an AI model to predict US house prices for their home-buying business. Initially it worked great. But:
- The housing market changed rapidly (COVID effect)
- The model's predictions drifted — it consistently overpredicted prices
- Without adequate monitoring, Zillow kept buying houses at inflated prices
- Lost $881 million and shut down the entire business unit
- 2,000 employees were laid off

This is the most expensive AI monitoring failure in history. Proper drift detection could have caught the problem months earlier and saved hundreds of millions.

---

## Q19: Scenario — Flipkart wants to build an AI system to predict which products a customer will buy next. Walk through the six lifecycle stages for this system.

**Answer:**

**Stage 1 — Define Problem:**
Goal: Predict the next product a customer is likely to purchase. Success metric: Increase purchase conversion rate by 10% through personalised "Recommended for You" suggestions.

**Stage 2 — Collect & Prepare Data:**
Collect: Past purchase history, browsing history, search queries, wishlist items, time of day, device type, customer demographics, festival/sale data. Clean: Remove bot traffic, handle missing values, remove returns/cancelled orders.

**Stage 3 — Build & Train Model:**
Create features like "number_of_purchases_last_30_days," "most_viewed_category," "days_since_last_purchase." Try collaborative filtering, XGBoost, and deep learning models. Evaluate each on held-out test data. Select the best-performing model.

**Stage 4 — Deploy & Serve:**
Deploy the model as an API on Flipkart's servers. When a user opens the app, the API is called with the user's features → model returns top 10 predicted products → shown as "Recommended for You."

**Stage 5 — Monitor & Evaluate:**
Track: Click-through rate on recommendations, conversion rate, revenue from recommended products. Check for data drift (are user patterns changing?). Alert if click-through rate drops below baseline.

**Stage 6 — Iterate & Improve:**
Add Big Billion Days sale data. Retrain model with festival shopping patterns. Add new features like "trending products in user's city." A/B test the new model vs old. If better, promote to production.

The cycle repeats continuously — especially before and after major sales events.

---

## Q20: Why does the course emphasise AI "Systems" rather than just AI "Algorithms"? Explain with the restaurant analogy.

**Answer:**
An AI algorithm (model) is just one piece of a much larger puzzle. Teaching only algorithms would be like teaching someone a recipe without teaching them how to run a restaurant.

The restaurant analogy:
- **Recipe (Algorithm/Model):** The chef knows how to cook biryani — this is the core skill
- **Ingredient sourcing (Data Pipeline):** Getting quality rice, spices, and meat from suppliers
- **Kitchen equipment (Infrastructure):** Ovens, stoves, refrigerators, vessels
- **Menu & service (Application Layer):** How the food reaches the customer's table
- **Waiter (Serving Layer):** Taking orders and delivering food quickly
- **Customer feedback cards (Monitoring):** Checking if customers are happy, if quality is declining

Without all of these, the recipe alone doesn't feed anyone. Similarly, a brilliant ML model sitting in a Jupyter notebook is useless if:
- There's no data pipeline to feed it fresh data
- There's no serving infrastructure to deliver predictions to users
- There's no monitoring to catch when it starts failing
- There's no application layer to turn raw predictions into useful actions

Google's research showed that the ML model code is typically less than 5% of a production AI system. The other 95% is the surrounding system — and that's what this course teaches.

---

*End of Session 1 Questions and Answers*
