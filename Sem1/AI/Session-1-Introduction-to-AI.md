# Session 1: Introduction to AI

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 1, T2 Chapter 1, Class Notes
>
> **Contact Session:** 1 (Module 1: Foundations of AI Systems)

---

---

## 1.1 What is Artificial Intelligence?

**Artificial Intelligence (AI)** is the broad field of computer science focused on creating systems that can perform tasks that normally require human intelligence — understanding language, recognising images, making decisions, learning from experience, and solving complex problems.

AI is not a single technology but an **umbrella term** covering many techniques, from simple rule-based systems ("if temperature > 100, raise alarm") to complex deep neural networks that generate human-like text and images.

### A Practical Definition for This Course

In the context of this course (AI *Systems*), we focus not on the algorithms alone but on the **end-to-end systems** that deliver AI capabilities to users. An AI system includes data pipelines, model training infrastructure, serving infrastructure, monitoring, and the application layer — not just the model.

> *"A machine learning model is only a small part of an ML system. The system also includes the data pipeline, the serving infrastructure, the monitoring, and the business logic."* — Chip Huyen (paraphrased)

---

## 1.2 AI vs Machine Learning vs Deep Learning

These three terms are often confused. They are **nested concepts** — each is a subset of the previous:

```
┌─────────────────────────────────────────────────┐
│            Artificial Intelligence (AI)          │
│   Systems that mimic human intelligence          │
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

### Detailed Comparison

| Aspect | AI | Machine Learning | Deep Learning |
|---|---|---|---|
| **Definition** | Any system that exhibits intelligent behaviour — reasoning, learning, perception, language. | A subset of AI where systems learn patterns from data without being explicitly programmed. | A subset of ML using multi-layered neural networks to learn hierarchical representations. |
| **How it works** | Rules, heuristics, search, logic, or learning algorithms. | Algorithms learn a function f(x) → y from training data (examples of input-output pairs). | Deep neural networks with many hidden layers automatically extract features from raw data. |
| **Feature engineering** | Manual rules or manual features. | Requires manual feature engineering (human selects what features to extract from data). | **Automatic feature learning** — the network discovers useful features from raw data. |
| **Data requirements** | Varies — rule-based systems need little data. | Moderate — thousands to millions of labelled examples. | Large — millions to billions of data points. Benefits enormously from scale. |
| **Compute requirements** | Low to moderate. | Moderate. | High — requires GPUs/TPUs for training. |
| **Examples** | Chess engines (Minimax), expert systems, chatbots. | Spam filters, recommendation engines, fraud detection. | Image recognition (CNNs), language models (GPT, BERT), speech recognition. |
| **When to use** | When rules are known and data is scarce. | When data is available and patterns can be learned. | When data is abundant and features are complex (images, text, audio). |

### Key Insight: Traditional AI vs ML

**Traditional AI (rule-based):** A human expert writes rules. Example: "If email contains 'lottery' AND sender not in contacts → spam." The rules are explicit and handcrafted.

**Machine Learning:** The system learns rules from data. Example: Show 100,000 emails labelled "spam" or "not spam." The ML algorithm discovers patterns (certain words, sender patterns, link structures) that distinguish spam. No human explicitly writes the rules.

**Deep Learning:** Same as ML but the system also learns what features to extract. Example: Show 1 million images labelled "cat" or "dog." A deep neural network learns to detect edges, then shapes, then ears/tails, then whole animals — all automatically from raw pixels. No human tells it "look for pointy ears."

---

## 1.3 The AI System Lifecycle

Building an AI system is not a one-time process — it's an iterative lifecycle. Understanding this lifecycle is critical because most real-world AI projects fail not due to bad models but due to poor execution of the surrounding system.

### The Lifecycle Stages

```
┌──────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ 1. Define│──→│ 2. Data      │──→│ 3. Model     │──→│ 4. Deploy    │
│ Problem  │   │ Collection & │   │ Development  │   │ & Serve      │
│          │   │ Preparation  │   │ & Training   │   │              │
└──────────┘   └──────────────┘   └──────────────┘   └──────┬───────┘
      ↑                                                       │
      │         ┌──────────────┐   ┌──────────────┐          │
      └─────────│ 6. Iterate   │←──│ 5. Monitor   │←─────────┘
                │ & Improve    │   │ & Evaluate   │
                └──────────────┘   └──────────────┘
```

| Stage | What happens | Key activities | Common pitfalls |
|---|---|---|---|
| **1. Problem Definition** | Translate a business problem into an ML problem. Define success metrics. | Identify stakeholders, define the task (classification? regression? generation?), set baselines, define what "good enough" means. | Solving the wrong problem. No clear success metric. Trying ML when a simple rule-based solution would suffice. |
| **2. Data Collection & Preparation** | Gather, clean, label, and prepare the data. | Source data, handle missing values, remove duplicates, label data (if supervised), split into train/validation/test sets. | Poor data quality, insufficient labels, biased data, data leakage (test data seen during training). |
| **3. Model Development & Training** | Select algorithms, engineer features, train models, tune hyperparameters. | Feature engineering, model selection, training, hyperparameter tuning, cross-validation. | Overfitting, underfitting, choosing overly complex models, not establishing a baseline first. |
| **4. Deployment & Serving** | Put the model into production to serve real users. | Package model, create serving API, configure infrastructure, set up load balancing. | Model works in notebook but fails in production. Latency too high. Memory/cost constraints. |
| **5. Monitoring & Evaluation** | Track model performance in production over time. | Monitor accuracy, latency, data drift, model drift, error rates, user feedback. | Assuming the model stays accurate forever. Not detecting when data distribution changes. |
| **6. Iteration & Improvement** | Improve the model based on production feedback. | Retrain with new data, fix discovered bugs, update features, handle edge cases. | Not closing the feedback loop. Not versioning data and models. |

### Why the Lifecycle is Circular

In traditional software, once you deploy, the code doesn't degrade over time (a function that adds two numbers will always add correctly). In ML systems, **the world changes**:

- User behaviour evolves (what people search for changes over seasons).
- Data distribution shifts (new types of fraud appear, new diseases emerge).
- Business requirements change (new product categories, new markets).

This means ML systems need **continuous retraining and monitoring** — the lifecycle is an infinite loop, not a one-time waterfall.

---

## 1.4 Components of an AI System

An AI system is much more than just a model. The model is typically less than 5% of the total code and effort in a production AI system. The remaining 95% includes:

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

| Component | What it does | Why it matters |
|---|---|---|
| **Data Pipeline** | Collects, cleans, transforms, and stores data. Ensures data quality and consistency. | "Garbage in, garbage out." The model is only as good as the data it's trained on. |
| **Feature Pipeline** | Transforms raw data into features the model can consume. Manages feature store. | Feature quality directly impacts model accuracy. Feature computation must be consistent between training and serving. |
| **Model Pipeline** | Trains, evaluates, selects, and registers models. Manages model versions. | Reproducibility, experiment tracking, model comparison. |
| **Serving Infrastructure** | Hosts the model and serves predictions via APIs. Handles load balancing and scaling. | Must meet latency SLAs (e.g. <100ms for real-time recommendations). |
| **Monitoring** | Tracks model performance, data drift, prediction distribution, latency, errors. | Without monitoring, you won't know when your model degrades — it silently serves wrong predictions. |
| **Application Layer** | Integrates model predictions into the user experience. Implements business logic. | The model's output must be transformed into something useful for the user (ranked recommendations, yes/no decisions, generated text). |

---

## 1.5 AI Applications Across Industries

AI is no longer confined to tech companies. It's transforming every industry:

| Industry | Application | How AI is used | Example |
|---|---|---|---|
| **Healthcare** | Medical imaging | Deep learning analyses X-rays, MRIs, CT scans to detect tumours, fractures, diseases. | Google's DeepMind detected eye diseases from retinal scans with ophthalmologist-level accuracy. |
| **Finance** | Fraud detection | ML models detect unusual transaction patterns in real-time, flagging potential fraud. | PayPal uses ML to detect fraud among 10+ million transactions daily. |
| **Retail / E-commerce** | Recommendation engines | Predict what products a user might want based on browsing/purchase history and similar users. | Amazon's "Customers who bought this also bought..." drives 35% of its revenue. |
| **Manufacturing** | Predictive maintenance | Sensor data from machines is analysed to predict failures before they occur. | Siemens uses AI to predict turbine failures 20 hours in advance. |
| **Transportation** | Autonomous vehicles | Computer vision, sensor fusion, and reinforcement learning enable self-driving. | Tesla Autopilot, Waymo robotaxis. |
| **Customer Service** | Conversational AI | Chatbots and virtual assistants handle customer queries 24/7. | Bank of America's "Erica" handles 10+ million customer interactions monthly. |
| **Education** | Adaptive learning | AI personalises curriculum difficulty based on student performance. | Khan Academy's AI tutor adapts explanations to each student's level. |
| **Agriculture** | Crop monitoring | Drones + computer vision detect crop diseases, assess yield, and optimise irrigation. | John Deere's AI-powered tractors identify and spray weeds individually. |
| **Legal** | Document analysis | NLP analyses contracts, discovers relevant case law, predicts litigation outcomes. | ROSS Intelligence analyses millions of legal documents in seconds. |
| **Entertainment** | Content generation | Generative AI creates music, art, scripts, and personalised content. | Netflix uses AI for content recommendations, thumbnail personalisation, and script analysis. |

---
---


---

*End of Session 1*
