# Session 1: Introduction to AI

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 1, T2 Chapter 1, Class Notes
>
> **Contact Session:** 1 (Module 1: Foundations of AI Systems)

---

## Table of Contents

- [1.1 What is Artificial Intelligence?](#11-what-is-artificial-intelligence)
- [1.2 AI vs Machine Learning vs Deep Learning](#12-ai-vs-machine-learning-vs-deep-learning)
- [1.3 From Business Problem to ML Problem](#13-from-business-problem-to-ml-problem)
- [1.4 Why Do AI Projects Fail?](#14-why-do-ai-projects-fail)
- [1.5 The AI System Lifecycle](#15-the-ai-system-lifecycle)
- [1.6 Components of an AI System](#16-components-of-an-ai-system)
- [1.7 Explainable AI (XAI)](#17-explainable-ai-xai)
- [1.8 Machine Learning Engineering](#18-machine-learning-engineering)
- [1.9 Model Engineering](#19-model-engineering)
- [1.10 AI Applications Across Industries](#110-ai-applications-across-industries)

---

## 1.1 What is Artificial Intelligence?

### Simple Definition

**Artificial Intelligence (AI)** means making computers do things that normally need human brains — like understanding language, recognising faces, making decisions, or learning from past experience.

Think of it this way: a calculator can add numbers, but it can't look at a photo and tell you "that's a dog." AI is what lets computers do that kind of smart thinking.

### AI is Not One Thing — It's a Family of Techniques

AI is an **umbrella term** (a big category name) that covers many different approaches:

| Approach | What it does | Everyday Example |
|---|---|---|
| **Rule-based systems** | Follow human-written "if-then" rules | Thermostat: "If temperature > 25°C, turn on AC" |
| **Machine Learning** | Learn patterns from data automatically | Gmail separating spam from real emails |
| **Deep Learning** | Learn complex patterns using brain-inspired neural networks | Google Photos recognising your friends' faces |
| **Generative AI** | Create new content (text, images, code) | ChatGPT writing an essay for you |

### What Makes This Course Different: Focus on AI *Systems*

This course is not just about AI algorithms (the math behind AI). It's about **AI systems** — the complete setup needed to deliver AI to real users.

> **Analogy:** Think of a restaurant. The chef's recipe (algorithm) is important, but the restaurant *system* includes: sourcing ingredients (data pipeline), the kitchen equipment (infrastructure), the menu (application), the waiter serving food (serving layer), and customer feedback cards (monitoring). Without all of these, the recipe alone doesn't feed anyone.

An AI system includes:
- **Data pipelines** — collecting and cleaning data
- **Model training** — teaching the AI
- **Serving infrastructure** — making predictions available to users
- **Monitoring** — checking if the AI is still working well
- **Application layer** — the app or website users interact with

> The model (algorithm) is typically less than 5% of the total code in a production AI system. The other 95% is everything around it.

---

## 1.2 AI vs Machine Learning vs Deep Learning

These three terms are often used interchangeably, but they're actually **nested** — each one fits inside the previous:

```
┌─────────────────────────────────────────────────┐
│            Artificial Intelligence (AI)          │
│   Any system that mimics human intelligence      │
│                                                   │
│   ┌─────────────────────────────────────────┐   │
│   │         Machine Learning (ML)            │   │
│   │   Systems that learn from data           │   │
│   │                                           │   │
│   │   ┌─────────────────────────────────┐   │   │
│   │   │       Deep Learning (DL)         │   │   │
│   │   │   ML using deep neural networks  │   │   │
│   │   └─────────────────────────────────┘   │   │
│   └─────────────────────────────────────────┘   │
└─────────────────────────────────────────────────┘
```

**In plain English:**
- **AI** = the big goal (make computers smart)
- **ML** = one way to achieve AI (let computers learn from data instead of writing rules manually)
- **DL** = one type of ML (use large neural networks that can learn very complex patterns)

### Detailed Comparison

| Aspect | AI (Traditional) | Machine Learning | Deep Learning |
|---|---|---|---|
| **What it is** | Any technique that makes machines act smart | Machines learn patterns from data without being explicitly programmed | ML using multi-layered neural networks |
| **How it works** | Human writes rules ("if X then Y") | Algorithm finds rules automatically from examples | Neural network discovers features AND rules from raw data |
| **Who writes the logic?** | Human programmer | Algorithm learns from data | Network learns everything |
| **Data needed** | Little or none | Thousands to millions of examples | Millions to billions of examples |
| **Computing power** | Low | Medium | High (needs GPUs/TPUs) |
| **Best for** | Problems where rules are clear and known | Problems where patterns exist but are hard to write as rules | Problems with complex data (images, text, audio) |

### Understanding Through a Spam Detection Example

Let's see how each approach would build an email spam filter:

**Traditional AI (Rule-based):**
A human expert writes rules manually:
```
IF email contains "lottery" AND sender not in contacts → SPAM
IF email contains "buy now" AND has suspicious link → SPAM
IF email from known contact AND no suspicious words → NOT SPAM
```
Problem: Spammers change their words, so you constantly need new rules. This doesn't scale.

**Machine Learning:**
You show the system 100,000 emails labelled "spam" or "not spam." The ML algorithm automatically discovers patterns:
- Certain words appear more in spam ("free," "winner," "click here")
- Spam emails tend to come from certain domains
- Spam has more links and images

The system **learned the rules itself** from data — you didn't write them.

> **Live Example:** Gmail uses ML to filter 10 million spam emails every minute across all its users. It keeps getting better because it learns from the "Report Spam" button clicks of billions of users.

**Deep Learning:**
Same idea as ML, but the deep neural network can work with the **raw email** (text + HTML + images + metadata) without anyone telling it what features to look for. It automatically discovers that:
- Layer 1: detects character patterns
- Layer 2: detects word patterns
- Layer 3: detects sentence patterns
- Layer 4: detects overall email intent

It figures out the features AND the rules, all by itself.

### Key Insight: When to Use What

| Situation | Best Approach | Why |
|---|---|---|
| Rules are clear and simple (age > 18 → allow) | Traditional AI (rules) | No need for ML, rules work fine |
| Patterns exist but hard to write as rules | Machine Learning | Let the data reveal the patterns |
| Data is complex (images, speech, text) | Deep Learning | Neural networks handle complexity well |
| Very little data available | Traditional AI | ML/DL need lots of data to learn |
| Data is abundant and compute is available | Deep Learning | More data + more compute = better DL |

---

## 1.3 From Business Problem to ML Problem

Before jumping into models and algorithms, there's a critical thinking step most beginners skip: **framing the problem correctly**. This three-step process takes you from a vague business need to a concrete ML task.

### The Three-Step Framing Process

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. Product Goal  │────→│  2. Solution      │────→│  3. ML Framing    │
│  (Business Need)  │     │  Approach         │     │  (Task Type)      │
└──────────────────┘     └──────────────────┘     └──────────────────┘
 "What do we want?"       "Is ML the right       "What kind of ML
                           tool here?"            problem is this?"
```

### Step 1: Define the Product Goal

Start with what the business actually wants. Don't say "we need an ML model" — say what outcome matters.

| Vague Goal | Clear Product Goal |
|---|---|
| "Use AI for our app" | "Reduce customer support tickets by 30%" |
| "Build a recommendation engine" | "Increase average order value by ₹200" |
| "Do something with our data" | "Predict which machines will fail in the next 7 days" |

### Step 2: Choose the Solution Approach

ML is **not always the answer**. Before choosing ML, ask: can a simpler approach work?

| Approach | When to Use | Example |
|---|---|---|
| **Simple rules / heuristics** | Logic is clear and doesn't change often | "If order value > ₹10,000, require OTP verification" |
| **Traditional software** | Problem is well-defined with known formulas | Calculating EMI on a loan (fixed formula) |
| **Machine Learning** | Patterns exist in data but are too complex for manual rules | Predicting which Flipkart orders will be returned |

> **Rule of thumb:** If a team of humans can write down the rules in a few pages, you probably don't need ML. If the rules would fill a book and keep changing — that's when ML shines.

### Step 3: Frame as an ML Problem

Once you've decided ML is the right tool, you need to pick the **type** of ML task:

| ML Task Type | What It Predicts | Output Example | Real Example |
|---|---|---|---|
| **Binary Classification** | One of two categories | Yes/No, Spam/Not Spam | "Will this Flipkart order be returned?" (Yes/No) |
| **Multi-class Classification** | One of many categories | Cat/Dog/Bird/Fish | "What language is this text in?" (Hindi/English/Tamil/...) |
| **Regression** | A continuous number | Price, time, score | "What will be the delivery time?" (37 minutes) |
| **Ranking** | Ordered list of items | Top-10 products | "Which products should appear first in search results?" |
| **Clustering** | Groups of similar items | Segment A/B/C | "Group our customers into segments based on behaviour" |
| **Anomaly Detection** | Normal vs unusual | Normal/Anomalous | "Is this UPI transaction fraudulent?" |

### End-to-End Example: Flipkart Reducing Returns

Let's walk through all three steps with a real Indian e-commerce example:

**Step 1 — Product Goal:**
Flipkart loses ₹crores every year on product returns (shipping, restocking, customer dissatisfaction). Goal: **reduce return rate from 15% to 10%.**

**Step 2 — Solution Approach:**
- Simple rules? ("Block all returns" — bad idea, customers will leave.)
- Can we write rules? ("If clothing + size not selected → warn user" — helps a little, but doesn't catch all reasons.)
- ML? ✅ — Return patterns depend on dozens of factors (product category, seller rating, price, delivery time, customer history). Too complex for manual rules.

**Step 3 — ML Framing:**
- **Task:** Binary Classification (return / no-return)
- **Input features:** Product category, price, seller rating, customer return history, delivery time, size selected, number of product images, review score
- **Output:** Probability of return (0 to 1)
- **Action:** If probability > 0.7 → show warnings ("Size runs small — consider ordering one size up"), offer video reviews, highlight return policy clearly

> **Key insight:** The framing step is where most AI projects go wrong. If Flipkart framed this as "predict which products are bad" instead of "predict which orders will be returned," they'd build the wrong model entirely.

---

## 1.4 Why Do AI Projects Fail?

Here's a sobering reality: **85% of AI projects never make it to production.** That means for every 10 AI projects companies start, only 1-2 actually reach real users.

### The Top Reasons for Failure

```
┌─────────────────────────────────────────────────────────────┐
│              Why AI Projects Fail (Top 7 Reasons)            │
├──────┬──────────────────────────────────────────────────────┤
│  1   │ Wrong problem framing — solving a problem nobody has │
│  2   │ Poor data quality — garbage in, garbage out          │
│  3   │ No clear success metric — "make it better" is not    │
│      │ a metric                                              │
│  4   │ Using AI where simple rules would work               │
│  5   │ Lack of MLOps infrastructure — model works on laptop │
│      │ but can't be deployed                                │
│  6   │ Stakeholder misalignment — tech team and business    │
│      │ team want different things                           │
│  7   │ Ignoring data drift — model degrades but nobody      │
│      │ notices                                              │
└──────┴──────────────────────────────────────────────────────┘
```

### The "Should We Even Use AI?" Checklist

Before starting any AI project, ask these questions:

| Question | If "No" → | Example |
|---|---|---|
| Can you clearly define what "success" looks like? | Don't start the project | "Make our app smarter" ← not a success metric |
| Do you have enough quality data? | Collect data first, then start | "We have 50 labelled examples" ← not enough for ML |
| Is the problem too complex for manual rules? | Use rules, not ML | "If age > 18, allow access" ← a rule, not ML |
| Can you measure the model's impact on business? | Hard to justify the project | "Model is 95% accurate" but doesn't translate to revenue |
| Do you have infrastructure to deploy and monitor? | Invest in MLOps first | Model works in Jupyter notebook but no way to deploy it |

> **Golden rule:** "If you can't define what 'success' looks like in one sentence, don't start the AI project."

### Live Example: A Failed vs Successful AI Project

**Failed project (Indian bank):**
A bank spent 18 months building an AI chatbot for customer service. They never defined success metrics. The chatbot could answer questions, but customers still preferred calling humans. The project was shelved because nobody could prove it saved money or improved satisfaction.

**Successful project (Zomato):**
Zomato defined a clear goal: "Predict delivery time within 5 minutes of actual delivery for 90% of orders." They had data (millions of past deliveries), the problem was too complex for rules (traffic, weather, restaurant prep time, driver availability all matter), and they could measure success clearly. The model is now live and serving predictions to millions of users daily.

---

## 1.5 The AI System Lifecycle

Building an AI system is **not a one-time activity** — it's a repeating cycle. You build, deploy, monitor, learn, and improve continuously.

### It Starts with Business Goal → ML Problem Framing

Before the lifecycle even begins, you must **translate a business goal into an ML problem.** This is the single most important step — and the one most teams get wrong.

| Step | What You Define | Example (Reliance Jio) |
|---|---|---|
| **Business Goal** | What the company wants to achieve in business terms | "Reduce customer churn by 10% this quarter" |
| **ML Problem** | What the model should predict | "Predict which customers will leave in the next 30 days" |
| **Task Type** | Classification, regression, ranking, etc. | Binary classification (churn / no-churn) |
| **Success Metric** | How you measure if the model is actually helping | "Reduce churn rate from 5% to 4.5%" |
| **Action** | What happens when the model makes a prediction | If churn probability > 0.7 → send retention offer (₹50 cashback, free data pack) |

> **Analogy:** Think of it like going to the doctor. You don't say "use medicine on me." You describe your symptoms (business goal), the doctor diagnoses the problem (ML framing), picks the right treatment (model), and then monitors if you're getting better (success metric). Skip the diagnosis step and you might take the wrong medicine entirely.

### Common Framing Mistakes

| What Teams Say | What's Wrong | Better Framing |
|---|---|---|
| "We need an AI model" | No clear problem or metric | "We need to predict X to reduce Y by Z%" |
| "Predict customer satisfaction" | Too vague — what does satisfaction mean? | "Predict NPS score (1-10) from support call transcripts" |
| "Build a better search" | "Better" is not measurable | "Increase click-through rate on search results from 25% to 35%" |
| "Use deep learning" | Choosing solution before understanding problem | First define the problem, then pick the simplest model that works |

### Why is it a Cycle?

In traditional software, code doesn't degrade over time — a function that calculates tax will calculate tax correctly forever (unless tax rules change).

But AI models **degrade over time** because the real world changes:
- Customer preferences change with seasons (winter coats → summer dresses)
- New types of fraud appear that the model has never seen
- A pandemic changes all buying patterns overnight

So AI systems need **continuous retraining and monitoring** — it's an infinite loop, not a one-time project.

### The 6 Stages

```
┌──────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ 1. Define│──→│ 2. Collect &  │──→│ 3. Build &   │──→│ 4. Deploy &  │
│ Problem  │   │ Prepare Data  │   │ Train Model  │   │ Serve        │
└──────────┘   └──────────────┘   └──────────────┘   └──────┬───────┘
      ↑                                                       │
      │         ┌──────────────┐   ┌──────────────┐          │
      └─────────│ 6. Iterate   │←──│ 5. Monitor   │←─────────┘
                │ & Improve    │   │ & Evaluate   │
                └──────────────┘   └──────────────┘
```

| Stage | What Happens (Simple) | Live Example (Swiggy Food Delivery) | Common Mistakes |
|---|---|---|---|
| **1. Define Problem** | Figure out what you're trying to solve. What does "success" look like? | "We want to predict delivery time accurately so customers know when food arrives." Success = predicted time within 5 min of actual. | Solving the wrong problem. Using AI where a simple formula would work. |
| **2. Collect & Prepare Data** | Gather data, clean it, label it, split into training and testing sets. | Collect: past deliveries (restaurant prep time, distance, traffic, weather, driver speed). Clean: remove cancelled orders, fix missing values. | Dirty data, biased samples, not enough data. |
| **3. Build & Train Model** | Choose an algorithm, train it on the data, tune it until it works well. | Try different models (linear regression, XGBoost, neural network). XGBoost predicts best. Tune parameters. | Overfitting (model memorises training data but fails on new data). Not testing properly. |
| **4. Deploy & Serve** | Put the model into production so real users get predictions. | Deploy model as an API. When user places order, app calls API → gets predicted delivery time → shows on screen. | Model works in testing but crashes in production. Too slow for real-time use. |
| **5. Monitor & Evaluate** | Watch how the model performs with real users over time. | Track: Are actual delivery times matching predictions? Are error rates increasing? Did Diwali traffic mess up predictions? | Assuming the model stays accurate forever. Not detecting when it starts failing. |
| **6. Iterate & Improve** | Fix issues, add new data, retrain, deploy improved version. | Add Diwali/festival traffic data. Retrain model. New version predicts 15% more accurately during festivals. | Not collecting feedback. Never retraining. |

### Live Example: How Netflix Continuously Improves

Netflix's recommendation system follows this exact cycle:
1. **Problem:** Recommend shows users will enjoy (metric: watch time)
2. **Data:** Viewing history, ratings, what was started but abandoned, time of day, device
3. **Model:** Multiple ML models for different parts of the UI
4. **Deploy:** Models serve recommendations to 230+ million users
5. **Monitor:** Track click-through rate, watch time, user satisfaction surveys
6. **Iterate:** A/B test new models, retrain weekly with fresh data

Netflix runs this cycle **continuously** — their models are never "done."

### ML Lifecycle Architecture — The Complete Pipeline

The 6 stages above are what you *do*. The architecture below is *what you build* to support those stages. Think of it as the factory that makes the AI system run smoothly.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     ML Lifecycle Architecture                            │
│                                                                          │
│  ┌──────────┐   ┌──────────┐   ┌───────────┐   ┌──────────┐           │
│  │  Data     │──→│  Data     │──→│  Data      │──→│ Feature  │           │
│  │ Ingestion │   │ Validation│   │ Transform  │   │  Store   │           │
│  └──────────┘   └──────────┘   └───────────┘   └────┬─────┘           │
│                                                       │                  │
│                                                       ▼                  │
│  ┌──────────┐   ┌──────────┐   ┌───────────┐   ┌──────────┐           │
│  │  Model   │──→│  Model    │──→│  Model     │──→│  Model   │           │
│  │ Training │   │ Evaluation│   │ Validation │   │ Registry │           │
│  └──────────┘   └──────────┘   └───────────┘   └────┬─────┘           │
│                                                       │                  │
│                                                       ▼                  │
│  ┌──────────┐   ┌──────────┐   ┌───────────┐   ┌──────────┐           │
│  │  Model   │──→│  Model    │──→│  Ground    │──→│ Metadata │           │
│  │ Serving  │   │ Monitoring│   │  Truth     │   │  Store   │           │
│  └──────────┘   └──────────┘   │ Collector  │   └──────────┘           │
│                                 └───────────┘                            │
└─────────────────────────────────────────────────────────────────────────┘
```

| Component | What It Does | Why You Need It |
|---|---|---|
| **Data Ingestion** | Pulls in raw data from databases, APIs, files, streaming sources | You need a reliable, automated way to collect data — not manual downloads |
| **Data Validation** | Checks data for errors, missing values, schema changes, anomalies | Bad data = bad model. Catch problems early before they corrupt training |
| **Data Transformation** | Cleans, normalises, and formats data for model consumption | Raw data is messy — models need structured, consistent inputs |
| **Feature Store** | Stores pre-computed features so training and serving use the *same* features | Prevents training-serving skew (biggest source of production bugs) |
| **Model Training** | Runs the learning algorithm on prepared data | This is where the model actually "learns" from your data |
| **Model Evaluation** | Tests model accuracy on held-out test data, compares with previous versions | Ensures the new model is actually better before deploying it |
| **Model Validation** | Checks model for fairness, bias, latency, and compliance before deployment | A model can be accurate but still biased against certain groups |
| **Model Registry** | Stores all model versions with metadata (who trained it, when, what data, what accuracy) | Like Git for models — you need to track and roll back model versions |
| **Model Serving** | Hosts trained model and responds to prediction requests | The "waiter" that takes the model's predictions and serves them to users |
| **Model Monitoring** | Tracks accuracy, latency, data drift, and performance in production | Models degrade silently — monitoring catches problems before users complain |
| **Ground Truth Collector** | Collects the "correct answers" that model predictions are compared against | Without ground truth, you can't measure if the model is getting better or worse |
| **Metadata Store** | Records experiment details, pipeline runs, data lineage, model lineage | When something goes wrong, you need to trace back to find what changed |

> **Live Example (PhonePe):** PhonePe's fraud detection system has all these components. Data ingestion pulls in millions of UPI transactions per hour. Data validation flags if the transaction format changes. The feature store computes features like "number of transactions in last 10 minutes" consistently for both training and serving. The model registry tracks which fraud model version is live. Monitoring alerts the team if false positive rates spike after a festival season.

### Data Processing — Where 80% of Your Time Goes

If there's one thing every data scientist agrees on, it's this: **data preparation consumes 80% of project time.** The actual model training? That's the easy part.

Data processing has four major steps:

```
Raw Data ──→ Collection ──→ Preparation ──→ Preprocessing ──→ Feature Engineering ──→ Ready for Model
              (10%)          (30%)           (20%)              (20%)
```

#### Step 1: Data Collection

Where does the data come from?

| Source Type | Examples | Challenge |
|---|---|---|
| **Internal databases** | Customer records, transaction logs, CRM data | May be spread across many systems |
| **APIs** | Social media feeds, weather data, stock prices | Rate limits, format changes |
| **User-generated** | Reviews, clicks, search queries | Noisy, unstructured |
| **Third-party** | Census data, credit bureau data, map data | Cost, licensing, freshness |
| **Sensors/IoT** | Machine sensors, GPS trackers, cameras | Volume (millions of readings per day) |

**Sampling strategies:**
- **Random sampling:** Pick examples randomly (good default)
- **Stratified sampling:** Ensure each category is fairly represented (important when data is imbalanced — e.g., 99% non-fraud, 1% fraud)
- **Time-based sampling:** Use recent data for training, older data for validation (important when patterns change over time)

**Labelling approaches:**
- **Manual labelling:** Humans tag data (expensive but accurate). Example: doctors labelling X-rays as "pneumonia" / "normal."
- **Semi-supervised:** Label a small set, use the model to label the rest, have humans verify uncertain ones.
- **Weak supervision:** Use heuristics or rules to auto-label. Example: "Emails in spam folder → label as spam."

#### Step 2: Data Preparation (Cleaning)

Real-world data is **always messy.** Here's what you'll find and how to fix it:

| Problem | Example | Fix |
|---|---|---|
| **Missing values** | Customer age field is blank for 20% of records | Drop the row, fill with average, or use a "missing" category |
| **Duplicates** | Same order recorded twice due to a system glitch | Remove duplicates based on unique identifiers |
| **Outliers** | A customer's age is recorded as 350 | Cap at reasonable limits or investigate the data source |
| **Inconsistent formats** | Dates as "25/12/2024," "2024-12-25," "Dec 25, 2024" in the same column | Standardise to one format |
| **Wrong data types** | Phone number stored as an integer (leading zeros lost) | Convert to string |
| **Label errors** | A spam email manually labelled as "not spam" | Cross-check labels, use consensus labelling (multiple people label the same data) |

#### Step 3: Data Preprocessing

Transform data into a format that ML algorithms can work with:

| Technique | What It Does | When to Use | Example |
|---|---|---|---|
| **Normalisation** | Scales numbers to 0-1 range | When features have very different scales | Income (₹10,000–₹10,00,000) and age (18–80) → both scaled to 0–1 |
| **Standardisation** | Scales to mean=0, std=1 | When data follows a bell curve | Height in cm → z-score |
| **One-hot encoding** | Converts categories to binary columns | For categorical features | City: Mumbai → [1,0,0], Delhi → [0,1,0], Bangalore → [0,0,1] |
| **Label encoding** | Converts categories to numbers | For ordinal features (have order) | Education: High School=1, Bachelor's=2, Master's=3 |
| **Text tokenisation** | Splits text into words/tokens | For NLP tasks | "I love chai" → ["I", "love", "chai"] |

#### Step 4: Feature Engineering

This is the **creative** part — making new features from raw data that help the model learn better.

| Raw Data | Engineered Feature | Why It Helps |
|---|---|---|
| Transaction timestamp | Hour of day, day of week, is_weekend | Fraud patterns differ by time |
| Customer DOB | Age, age_group (teen/adult/senior) | Buying patterns differ by age |
| Lat/Long of delivery | Distance to restaurant (km) | Delivery time depends on distance |
| Product title text | Word count, has_brand_name, title_length | Helps predict product quality |
| Last 10 transactions | Average transaction amount, max, min, std | Captures spending behaviour |

> **Live Example (Ola):** Ola's ride pricing model doesn't just use "distance" as a feature. They engineer features like: time_since_last_ride, rides_in_last_7_days, is_airport_pickup, surge_zone_demand, driver_density_in_3km_radius, rain_intensity. These engineered features are what make their pricing model accurate — raw GPS coordinates alone wouldn't be enough.

---

## 1.6 Components of an AI System

An AI system has many parts working together, just like a car has an engine, wheels, brakes, fuel system, and dashboard — the engine alone doesn't make a car.

### The 6 Key Components

```
┌─────────────────────────────────────────────────────────────┐
│                    AI System Components                       │
├────────────┬──────────────┬───────────────┬─────────────────┤
│ Data       │ Model        │ Application   │ Infrastructure  │
│ Pipeline   │ Pipeline     │ Layer         │ & Operations    │
├────────────┼──────────────┼───────────────┼─────────────────┤
│ Collection │ Feature Eng  │ User Interface│ Compute (GPU)   │
│ Cleaning   │ Training     │ API/Serving   │ Storage         │
│ Labelling  │ Evaluation   │ Business Logic│ Networking      │
│ Storage    │ Selection    │ A/B Testing   │ CI/CD Pipeline  │
│ Versioning │ Registry     │ Feedback Loop │ Monitoring      │
│ Governance │ Fine-tuning  │ Orchestration │ Logging         │
└────────────┴──────────────┴───────────────┴─────────────────┘
```

| Component | What It Does (Simple) | Why It Matters | Live Example (Amazon) |
|---|---|---|---|
| **Data Pipeline** | Collects raw data, cleans it, transforms it, and stores it ready for use. | "Garbage in, garbage out" — if data is bad, the AI will be bad. | Amazon collects clicks, purchases, searches, reviews from millions of users every second. Data pipelines clean and organise this data. |
| **Feature Pipeline** | Converts raw data into **features** — the specific inputs the model needs. | The model can't use raw data directly. Features are the "ingredients" the model actually learns from. | Raw data: "User clicked product X at 10pm." Feature: "user_click_count_last_7_days = 45, time_of_day = evening, category_preference = electronics." |
| **Model Pipeline** | Trains, evaluates, and selects the best model. Keeps track of different versions. | Need to compare models, track experiments, and know which model is live. | Amazon tests hundreds of recommendation models. The best-performing one gets promoted to production. |
| **Serving Infrastructure** | Hosts the trained model and answers prediction requests fast. | Users expect instant results — Amazon product recommendations must load in <100ms. | When you open Amazon's homepage, a model server computes your personalised recommendations in milliseconds. |
| **Monitoring** | Watches if the model is still working well in production. Alerts when something goes wrong. | Without monitoring, a broken model silently serves wrong predictions — you won't know until customers complain. | Amazon monitors if recommendation click-through rates drop. If they do, an alert fires and the team investigates. |
| **Application Layer** | The app/website that users interact with. Connects model predictions to the user experience. | The model's raw output (a number like 0.87) needs to be turned into something useful ("Recommended for you"). | Amazon's website takes model scores and displays products in ranked order with "Recommended" badges. |

### The Three Levels of ML Software — The Iceberg

Most people think AI = the model. In reality, the model is the **tip of the iceberg.** Below the surface lies a massive amount of infrastructure and operational code.

```
                    ╱╲
                   ╱  ╲
                  ╱ ML ╲           ← Level 1: ML Code (~5%)
                 ╱ Code  ╲           Model training, feature engineering
                ╱──────────╲
               ╱            ╲
              ╱  ML Infra    ╲     ← Level 2: ML Infrastructure (~35%)
             ╱  Data pipelines╲      Serving, monitoring, feature store
            ╱  Model registry  ╲
           ╱────────────────────╲
          ╱                      ╲
         ╱    ML Operations       ╲  ← Level 3: ML Operations (~60%)
        ╱   CI/CD, testing,        ╲   Governance, team workflows,
       ╱   deployment, security     ╲   compliance, access control
      ╱──────────────────────────────╲
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  (water line)
```

| Level | What It Includes | Size | Who Builds It |
|---|---|---|---|
| **Level 1: ML Code** | Model training scripts, feature engineering, hyperparameter tuning, evaluation | ~5% of total code | Data Scientists, ML Researchers |
| **Level 2: ML Infrastructure** | Data pipelines, feature stores, serving systems, model registry, monitoring dashboards | ~35% of total code | ML Engineers, Data Engineers |
| **Level 3: ML Operations** | CI/CD for ML, automated testing, governance, compliance, access control, team workflows, documentation | ~60% of total code | MLOps Engineers, Platform Engineers |

> **Analogy:** Think of making a movie. The actors performing (ML code) is what audiences see. But behind the scenes there are cameras, lighting, sound equipment (infrastructure), and then production management, legal contracts, marketing, distribution (operations). The acting is 5% of the effort — the rest is what makes the movie actually reach theatres.

**Why this matters for your career:** If you only learn model training, you know 5% of what's needed. The industry desperately needs people who understand Levels 2 and 3 — that's where the jobs and salaries are.

### Ground-Truth Collector — Getting the "Right Answers"

A model makes predictions, but how do you know if those predictions were correct? You need **ground truth** — the actual outcome that happened in reality.

| Scenario | Model's Prediction | Ground Truth (Reality) | How Ground Truth Is Collected |
|---|---|---|---|
| Spam filter | "This email is spam" | User moved it back to inbox | User action (implicit feedback) |
| Fraud detection | "This transaction is fraud" | Investigation confirmed it was legitimate | Human investigator (explicit feedback) |
| Product recommendation | "User will like this product" | User bought it and gave 5 stars | Purchase + review data |
| Loan default prediction | "This borrower will default" | Borrower paid all EMIs for 12 months | Wait and observe (delayed ground truth) |

**The delayed ground truth problem:**

Some ground truth takes a long time to arrive:
- **Loan default:** You won't know if a borrower defaults until months or years later
- **Cancer detection:** A scan flagged as "suspicious" may take weeks of follow-up tests to confirm
- **Customer churn:** You predicted a customer would leave, but you need to wait 30-60 days to see if they actually did

> **Live Example (CRED):** CRED's credit score prediction model predicts if a user will pay their credit card bill on time. But ground truth (did they actually pay?) only arrives after the due date — sometimes 30 days later. CRED's ground-truth collector waits for payment data, then feeds it back to compare against the model's predictions and trigger retraining if accuracy drops.

### Performance Monitor — The AI System's Health Dashboard

Once your model is in production, you need to **watch it like a hawk.** Models don't crash like regular software — they silently start giving worse answers over time.

A performance monitor tracks four types of metrics:

| Metric Type | What It Measures | Example | Alert Threshold |
|---|---|---|---|
| **Accuracy metrics** | Is the model still predicting correctly? | Precision, recall, F1 score, AUC | F1 drops below 0.85 |
| **Latency metrics** | How fast are predictions returned? | P50, P95, P99 response time | P99 latency > 200ms |
| **Throughput metrics** | How many predictions per second? | Requests per second (RPS) | RPS capacity < expected traffic |
| **Data drift metrics** | Has the input data distribution changed? | Feature distribution shift, PSI score | PSI > 0.2 (significant drift) |

**What happens when the monitor detects a problem:**

```
Monitor detects issue
       │
       ▼
┌─────────────┐     ┌───────────────┐     ┌──────────────┐
│ Send alert  │────→│ Team           │────→│ Root cause   │
│ to on-call  │     │ investigates   │     │ analysis     │
└─────────────┘     └───────────────┘     └──────┬───────┘
                                                   │
                    ┌───────────────┐     ┌────────▼───────┐
                    │ Deploy fixed  │←────│ Retrain model  │
                    │ model         │     │ with new data  │
                    └───────────────┘     └────────────────┘
```

> **Live Example (Swiggy):** Swiggy's delivery time prediction model is monitored 24/7. During IPL season, delivery patterns changed dramatically — more evening orders, longer prep times at restaurants. The performance monitor detected that prediction errors increased by 40%. An alert fired, the team investigated, added IPL-match-day features, retrained the model, and deployed the updated version within 48 hours.

**Key triggers for automatic retraining:**
- Model accuracy drops below a set threshold for 3+ consecutive days
- Data drift score exceeds 0.2 (input distribution has shifted significantly)
- A new category of data appears that the model has never seen (e.g., a new payment method)
- Business rules change (e.g., new delivery zones added)

One of the biggest problems in AI systems is **training-serving skew** — when the data or features used during training are different from what's available during serving.

> **Analogy:** Imagine studying for an exam using last year's question paper (training). But this year's exam (serving) has completely different questions. You prepared for the wrong thing.

**Live Example:** A fraud detection model is trained using features including "time since last transaction." During training, this is calculated from historical data (exact). During serving, the feature calculation has a bug that rounds to the nearest hour. The model sees different data in production than what it trained on → accuracy drops.

---

## 1.7 Explainable AI (XAI)

### The Black Box Problem

Many ML models (especially deep learning) work like a **black box** — data goes in, a prediction comes out, but nobody can explain *why* the model made that decision.

```
        Input                Model              Output
   ┌────────────┐      ┌────────────┐      ┌────────────┐
   │ Customer    │─────→│  🔮 ???    │─────→│ Loan:      │
   │ data        │      │  (black    │      │ REJECTED   │
   │             │      │   box)     │      │            │
   └────────────┘      └────────────┘      └────────────┘
                                              ↑
                                   "But WHY was it rejected?"
```

This is a problem because:
- **Regulations demand it:** The EU's GDPR gives citizens the "right to explanation" — if an AI denies your loan, the bank must explain why.
- **Trust:** Doctors won't trust a diagnosis from an AI they can't understand.
- **Debugging:** If the model is wrong, you need to know *why* to fix it.
- **Fairness auditing:** You need to check if the model is discriminating based on gender, caste, religion, etc.

### XAI Techniques — Making the Black Box Transparent

| Technique | How It Works (Simple) | Output | Best For |
|---|---|---|---|
| **SHAP** (SHapley Additive exPlanations) | Calculates how much each feature contributed to the prediction. Based on game theory — each feature is a "player" and SHAP figures out each player's contribution. | Feature importance scores (positive = pushed towards YES, negative = pushed towards NO) | Tabular data (loans, fraud, churn prediction) |
| **LIME** (Local Interpretable Model-agnostic Explanations) | Creates a simple "mini-model" around one specific prediction to explain just that prediction. | "For THIS specific email, the words 'free money' and 'click here' caused the spam classification." | Explaining individual predictions |
| **Attention Visualization** | Shows which parts of the input the model "focused on" when making its decision. | Heatmap highlighting important words in text or regions in images. | NLP and computer vision tasks |
| **Feature Importance** | Ranks which features matter most overall (not per prediction, but for the whole model). | "Income is the #1 factor, followed by credit score, then age." | Understanding model behaviour globally |

### Example: Bank Loan Rejection with SHAP

A customer applies for a home loan. The model says: **REJECTED** (probability of default = 0.78).

Without XAI: "Your loan application was denied." (Customer is frustrated — no explanation.)

With SHAP explanation:

```
Feature                        Contribution to "Reject" decision
───────────────────────────────────────────────────────────────
Monthly income: ₹25,000        ████████████████  +0.35  (income too low for ₹50L loan)
Credit inquiries: 8 in 6 months ██████████████   +0.28  (too many recent inquiries)
Existing EMIs: 3 active        ████████████      +0.22  (high existing debt)
Credit score: 680              ████              +0.08  (slightly below preferred 720+)
Employment: 5 years            ██               -0.05  (stable job, slightly positive)
Age: 35                        █                -0.02  (neutral factor)
                               ────────────────────────
                               Net score → REJECT (0.78)
```

Now the bank can tell the customer: "Your application was declined primarily because your income of ₹25,000/month is insufficient for the requested ₹50 lakh loan, and you've had 8 credit inquiries in the last 6 months which signals risk. Consider: (1) applying for a smaller loan amount, (2) reducing existing EMIs first."

> **Live Example (Paytm):** Paytm's lending arm uses SHAP explanations to explain credit decisions to users. When a Paytm Postpaid application is declined, the app shows the top 3 reasons — making the process transparent and helping users improve their creditworthiness for future applications.

---

## 1.8 Machine Learning Engineering

### Data Scientists vs ML Engineers — What's the Difference?

These two roles are often confused, but they do very different jobs:

| Aspect | Data Scientist | ML Engineer |
|---|---|---|
| **Primary focus** | Experiment with models, find patterns, build prototypes | Build the systems that run models in production at scale |
| **Works with** | Jupyter notebooks, datasets, statistical analysis | APIs, pipelines, cloud infrastructure, deployment tools |
| **Output** | "This model achieves 92% accuracy on the test set" | "This model is now serving 10 million predictions/day with <50ms latency" |
| **Analogy** | The chef who creates a new recipe | The restaurant chain manager who ensures that recipe is served consistently in 500 restaurants |
| **Tools** | Python, R, scikit-learn, TensorFlow, pandas | Docker, Kubernetes, Airflow, MLflow, cloud services (AWS/GCP/Azure) |

> **Key insight:** A Data Scientist might build an amazing fraud detection model in a Jupyter notebook. But without an ML Engineer, that model sits in the notebook forever — it never reaches the PhonePe app where it actually catches fraud.

### What ML Engineers Build

ML Engineering bridges the gap between research and production. Here's what they build:

```
┌─────────────────────────────────────────────────────────────┐
│              ML Engineer's Responsibilities                   │
├─────────────┬─────────────┬──────────────┬──────────────────┤
│ Data        │ Feature     │ Training     │ Serving          │
│ Pipelines   │ Store       │ Infra        │ Systems          │
│             │             │              │                  │
│ Automate    │ Store &     │ Distributed  │ Deploy model     │
│ data flow   │ serve       │ training on  │ as API with      │
│ from source │ features    │ multiple     │ low latency      │
│ to model    │ consistently│ GPUs         │ and high         │
│             │             │              │ availability     │
├─────────────┼─────────────┼──────────────┼──────────────────┤
│ Monitoring  │ CI/CD       │ A/B Testing  │ Cost             │
│ Systems     │ for ML      │ Framework    │ Optimisation     │
│             │             │              │                  │
│ Track model │ Automate    │ Compare new  │ Right-size GPU   │
│ performance │ train-test- │ vs old model │ usage, optimise  │
│ in prod     │ deploy cycle│ on real users│ inference cost   │
└─────────────┴─────────────┴──────────────┴──────────────────┘
```

### The ML Engineering Workflow

```
Data Scientist hands over model
        │
        ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Package model│────→│ Build serving │────→│ Set up        │
│ & dependencies│    │ API           │     │ monitoring    │
└──────────────┘     └──────────────┘     └──────────────┘
        │                                         │
        ▼                                         ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Automate     │────→│ Deploy to    │────→│ Set up auto   │
│ retraining   │     │ production   │     │ scaling       │
│ pipeline     │     │              │     │               │
└──────────────┘     └──────────────┘     └──────────────┘
```

> **Live Example (Myntra):** Myntra's ML team has Data Scientists who experiment with fashion recommendation models. Once a model shows promise, ML Engineers take over: they containerise the model in Docker, deploy it on Kubernetes clusters, set up auto-scaling so the system handles Myntra's End of Reason Sale traffic (10x normal load), build feature pipelines that compute "trending_in_your_city" and "similar_body_type_users_bought" features in real-time, and set up monitoring dashboards to track recommendation click-through rates. Without ML Engineering, the recommendation model would just be a Jupyter notebook.

---

## 1.9 Model Engineering

### The Art and Science of Building Good Models

Model engineering is the process of selecting, training, tuning, and validating ML models. It's more structured than "just try different models and see what works."

### Step 1: Start Simple, Then Go Complex

A common beginner mistake is jumping straight to deep learning. Instead, follow the **complexity ladder:**

```
Start here
    │
    ▼
┌──────────────────┐
│ 1. Baseline       │  Simple rule or average (e.g., "predict most common class")
│    (2 minutes)    │  This is your minimum bar — the model MUST beat this.
└────────┬─────────┘
         ▼
┌──────────────────┐
│ 2. Simple model   │  Logistic Regression, Decision Tree, Linear Regression
│    (1 hour)       │  Often surprisingly good! Interpret easily.
└────────┬─────────┘
         ▼
┌──────────────────┐
│ 3. Moderate model │  Random Forest, XGBoost, LightGBM
│    (1 day)        │  Handles non-linear patterns. Industry workhorse.
└────────┬─────────┘
         ▼
┌──────────────────┐
│ 4. Complex model  │  Neural Networks, Transformers, Ensemble models
│    (1 week+)      │  Only if simpler models aren't good enough.
└──────────────────┘
```

> **Rule of thumb:** XGBoost wins ~80% of tabular data competitions on Kaggle. Don't use deep learning for spreadsheet-style data unless you have a very good reason.

### Step 2: Handle Common Challenges

| Challenge | What It Means | How to Handle |
|---|---|---|
| **Imbalanced data** | 99% of data is one class (e.g., 99% non-fraud, 1% fraud) | Use oversampling (SMOTE), undersampling, or class weights. Don't just use accuracy — use precision, recall, F1. |
| **Overfitting** | Model memorises training data but fails on new data (like memorising answers vs understanding concepts) | Use cross-validation, add regularisation, reduce model complexity, get more training data. |
| **Underfitting** | Model is too simple to capture patterns (like using a straight line to fit curved data) | Use a more complex model, add more features, reduce regularisation. |
| **Data leakage** | Training data accidentally contains information from the future that won't be available at prediction time | Careful feature engineering. Example: don't use "total_transactions_this_month" to predict something on the 5th of the month. |

### Step 3: Hyperparameter Tuning

Every ML model has **knobs you can adjust** (hyperparameters) that affect how it learns. Finding the best settings is called tuning.

| Method | How It Works | Speed | Quality |
|---|---|---|---|
| **Grid Search** | Try every combination of settings in a predefined grid | Slow (tries everything) | Thorough but wasteful |
| **Random Search** | Try random combinations | Faster than grid | Often finds good results faster than grid search |
| **Bayesian Optimisation** | Uses past results to intelligently pick next settings to try | Smart and fast | Best quality for limited budget |

> **Analogy:** Looking for the best biryani in Hyderabad. Grid search = visit every single restaurant systematically. Random search = visit random restaurants. Bayesian = ask locals which restaurants are good, visit those, then ask "anything better than this?" and follow the leads.

### Step 4: Cross-Validation — Estimating Real-World Performance

You can't just test on one train/test split — you might get lucky (or unlucky). **K-fold cross-validation** gives a more reliable estimate:

```
Full Dataset: [████████████████████████████████████████]

Fold 1: [TEST ][  Train  ][  Train  ][  Train  ][  Train  ]  → Accuracy: 91%
Fold 2: [Train][  TEST   ][  Train  ][  Train  ][  Train  ]  → Accuracy: 89%
Fold 3: [Train][  Train  ][  TEST   ][  Train  ][  Train  ]  → Accuracy: 92%
Fold 4: [Train][  Train  ][  Train  ][  TEST   ][  Train  ]  → Accuracy: 88%
Fold 5: [Train][  Train  ][  Train  ][  Train  ][  TEST   ]  → Accuracy: 90%

Average accuracy: 90% ± 1.4%  ← Much more reliable than a single test!
```

Each fold takes a turn being the test set. The average across all folds gives you a trustworthy estimate of how the model will perform on unseen data.

> **Live Example (Razorpay):** Razorpay's fraud detection team uses 5-fold cross-validation with time-based splits (older data for training, newer data for testing) to ensure their model generalises well. They found that a model with 95% accuracy on random splits only had 87% accuracy on time-based splits — because fraud patterns evolve over time. The time-based validation was more realistic.

---

## 1.10 AI Applications Across Industries

AI is being used in virtually every industry. Here are concrete, real-world examples you should know:

| Industry | Application | How AI Helps | Live Example |
|---|---|---|---|
| **Healthcare** | Medical image analysis | DL analyses X-rays, MRIs, CT scans to detect diseases faster and more consistently than humans alone | **Google DeepMind** detected 50+ eye diseases from retinal scans with accuracy matching specialist doctors. Used in NHS hospitals. |
| **Finance / Banking** | Fraud detection | ML models spot unusual transaction patterns in real-time, catching fraud that rule-based systems miss | **PayPal** uses ML to evaluate 10+ million transactions daily. Reduced false positives by 50% compared to rule-based systems. |
| **E-commerce** | Product recommendations | Predict what products you'll want based on your history and similar users' behaviour | **Amazon's** "Customers who bought this also bought..." drives 35% of total revenue (~$130 billion/year influenced by AI). |
| **Manufacturing** | Predictive maintenance | Sensor data from machines predicts failures before they happen, avoiding costly downtime | **Siemens** uses AI on turbine sensor data to predict failures 20 hours before they happen. Saves millions in unplanned downtime. |
| **Transportation** | Self-driving vehicles | Computer vision + sensor fusion + path planning enables autonomous driving | **Waymo** robotaxis in San Francisco and Phoenix — fully driverless, no safety driver, carrying paying passengers daily. |
| **Customer Service** | Chatbots / Virtual assistants | AI handles common customer questions 24/7, freeing humans for complex issues | **Bank of America's "Erica"** handles 1.5 billion customer interactions. Resolves 90% of queries without human agent. |
| **Education** | Personalised learning | AI adapts difficulty and content based on each student's performance | **Khan Academy's Khanmigo** (GPT-4 powered tutor) gives personalised explanations. Adjusts to each student's level. |
| **Agriculture** | Crop monitoring | Drones + computer vision detect crop diseases, assess yield, optimise irrigation | **John Deere's** See & Spray technology identifies individual weeds and sprays only them — reduces herbicide use by 77%. |
| **Legal** | Document review | NLP reads thousands of contracts/documents in minutes instead of weeks | **JPMorgan's COIN** (Contract Intelligence) reviews commercial loan agreements. Does in seconds what took lawyers 360,000 hours/year. |
| **Entertainment** | Content personalisation | AI recommends movies, songs, videos based on your taste | **Spotify's Discover Weekly** uses ML to create a personalised 30-song playlist every Monday for 400+ million users. |
| **Telecom** | Network optimisation | AI predicts network congestion and optimises traffic routing | **Jio** uses AI to manage network traffic across 400+ million subscribers, predicting congestion and rerouting automatically. |

### Why You Should Care About These Examples

For exams and interviews, knowing **specific company examples** with **concrete numbers** is far more impressive than vague statements like "AI is used in healthcare." The examples above give you ammunition for any question about AI applications.

---

## Key Terms Glossary (Session 1)

| Term | Simple Meaning |
|---|---|
| **AI (Artificial Intelligence)** | Making computers do tasks that normally need human intelligence |
| **ML (Machine Learning)** | A type of AI where computers learn patterns from data instead of being manually programmed |
| **DL (Deep Learning)** | A type of ML using deep neural networks (many layers) for complex tasks like image/text understanding |
| **Neural Network** | A computing system inspired by the brain — layers of connected nodes that process information |
| **Training** | The process of teaching a model by showing it many examples |
| **Inference / Serving** | Using a trained model to make predictions on new, unseen data |
| **Feature** | A measurable property used as input to a model (e.g., age, income, number of clicks) |
| **Feature Engineering** | Creating new, more useful features from raw data (e.g., extracting "hour of day" from a timestamp) |
| **Feature Store** | A centralised repository that stores and serves pre-computed features consistently for training and serving |
| **Overfitting** | When a model memorises training data too well and fails on new data (like memorising answers vs understanding concepts) |
| **Underfitting** | When a model is too simple to capture the patterns in data (like fitting a straight line to curved data) |
| **Data Drift** | When real-world data changes over time, making the model's training data outdated |
| **Training-Serving Skew** | When data or features differ between training and production, causing unexpected errors |
| **A/B Testing** | Comparing two versions (A and B) on real users to see which performs better |
| **ML Problem Framing** | Translating a business goal into a specific ML task type (classification, regression, ranking, etc.) |
| **Binary Classification** | Predicting one of two categories (yes/no, spam/not-spam, fraud/not-fraud) |
| **Regression** | Predicting a continuous number (price, delivery time, temperature) |
| **MLOps** | The practice of deploying, monitoring, and maintaining ML models in production — like DevOps for ML |
| **XAI (Explainable AI)** | Techniques that make AI decisions understandable to humans (e.g., SHAP, LIME) |
| **SHAP** | SHapley Additive exPlanations — calculates each feature's contribution to a prediction using game theory |
| **LIME** | Local Interpretable Model-agnostic Explanations — explains individual predictions using simple local models |
| **Ground Truth** | The actual correct outcome used to measure if a model's prediction was right |
| **Model Registry** | A versioned store for trained models — like Git but for ML models |
| **Hyperparameter Tuning** | Finding the best settings (knobs) for an ML algorithm to improve performance |
| **Cross-Validation** | Testing model performance across multiple train/test splits for a more reliable accuracy estimate |
| **Imbalanced Data** | When one class heavily outnumbers another (e.g., 99% non-fraud, 1% fraud) — requires special techniques |
| **Data Leakage** | When training data accidentally contains future information that won't be available during serving |

---

*End of Session 1*
