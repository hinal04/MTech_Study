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
- [1.3 The AI System Lifecycle](#13-the-ai-system-lifecycle)
- [1.4 Components of an AI System](#14-components-of-an-ai-system)
- [1.5 AI Applications Across Industries](#15-ai-applications-across-industries)

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

## 1.3 The AI System Lifecycle

Building an AI system is **not a one-time activity** — it's a repeating cycle. You build, deploy, monitor, learn, and improve continuously.

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

---

## 1.4 Components of an AI System

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

### Key Concept: Training-Serving Skew

One of the biggest problems in AI systems is **training-serving skew** — when the data or features used during training are different from what's available during serving.

> **Analogy:** Imagine studying for an exam using last year's question paper (training). But this year's exam (serving) has completely different questions. You prepared for the wrong thing.

**Live Example:** A fraud detection model is trained using features including "time since last transaction." During training, this is calculated from historical data (exact). During serving, the feature calculation has a bug that rounds to the nearest hour. The model sees different data in production than what it trained on → accuracy drops.

---

## 1.5 AI Applications Across Industries

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
| **Overfitting** | When a model memorises training data too well and fails on new data (like memorising answers vs understanding concepts) |
| **Data Drift** | When real-world data changes over time, making the model's training data outdated |
| **Training-Serving Skew** | When data or features differ between training and production, causing unexpected errors |
| **A/B Testing** | Comparing two versions (A and B) on real users to see which performs better |

---

*End of Session 1*
