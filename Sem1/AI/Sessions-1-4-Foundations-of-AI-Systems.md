# Sessions 1–4: Foundations of AI Systems

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **Textbooks:**
> - T1: Chip Huyen, *Designing Machine Learning Systems*, O'Reilly
> - T2: Emmanuel Ameisen, *Building Machine Learning Powered Applications*, O'Reilly
> - R1: Foster Provost & Tom Fawcett, *Data Science for Business*, O'Reilly
>
> **Contact Sessions:** 1–4 (Module 1)

---

## Table of Contents

- [Session 1: Introduction to AI, AI vs ML vs DL, Lifecycle, Components, Applications](#session-1-introduction-to-ai)
- [Session 2: Categories of AI Systems and Case Studies](#session-2-categories-of-ai-systems)
- [Session 3: AI System Architecture](#session-3-ai-system-architecture)
- [Session 4: AI Ecosystem and Model Marketplaces](#session-4-ai-ecosystem-and-model-marketplaces)

---
---

# Session 1: Introduction to AI

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

# Session 2: Categories of AI Systems

---

## 2.1 Predictive AI

**Predictive AI** systems analyse historical data to make predictions about future events or outcomes. They answer the question: "Given what has happened before, what is likely to happen next?"

### How it works

A predictive model is trained on historical data (features + known outcomes). Once trained, it takes new data (features without the outcome) and predicts the likely outcome.

```
Training:   Historical Data (features + labels) → Model learns patterns
Inference:  New Data (features only) → Model predicts label
```

### Types of Predictive Tasks

| Task | What it predicts | Output | Example |
|---|---|---|---|
| **Classification** | Which category does an input belong to? | Discrete label (spam/not-spam, cat/dog, fraud/legitimate) | Email spam detection: input = email features, output = spam or not. |
| **Regression** | What is the numerical value? | Continuous number (price, temperature, revenue) | House price prediction: input = house features, output = predicted price. |
| **Time-series forecasting** | What will happen next in a sequence? | Future values | Stock price prediction, demand forecasting, weather prediction. |
| **Anomaly detection** | Is this data point unusual? | Normal / anomalous | Credit card fraud: flag transactions that deviate from normal patterns. |

### Real-World Examples

- **Netflix:** Predicts which shows you'll enjoy (classification: will user watch? + regression: predicted rating).
- **Uber:** Predicts ride demand in each area for the next 15 minutes to pre-position drivers.
- **Healthcare:** Predicts patient readmission risk within 30 days based on medical history.

---

## 2.2 Generative AI

**Generative AI** creates new content — text, images, music, code, video — that didn't exist before. Instead of predicting a label, it generates entirely new data that resembles the training data.

### How it works

Generative models learn the underlying distribution of the training data, then sample from that distribution to create new, original content.

| Approach | How it generates | Example |
|---|---|---|
| **Large Language Models (LLMs)** | Predict the next token in a sequence, one at a time, producing coherent text. | GPT-4, Claude, Gemini, LLaMA. |
| **Diffusion Models** | Start with noise, iteratively denoise to produce an image. | DALL-E, Stable Diffusion, Midjourney. |
| **GANs (Generative Adversarial Networks)** | Two networks compete: Generator creates fake data, Discriminator tries to tell real from fake. They improve each other. | StyleGAN (face generation), DeepFake. |
| **VAEs (Variational Autoencoders)** | Encode data into a compressed latent space, then decode to generate new data. | Drug molecule generation, image synthesis. |

### Key Concepts in Generative AI

- **Prompt engineering:** Crafting the input (prompt) to get the desired output from an LLM.
- **Hallucinations:** When a generative model produces confident but factually incorrect content. This is a fundamental challenge — the model generates plausible text, not necessarily true text.
- **Temperature:** A parameter that controls randomness in generation. Low temperature = more deterministic/safe. High temperature = more creative/risky.
- **Fine-tuning:** Adapting a pre-trained generative model to a specific domain or task using domain-specific data.

### Case Study: ChatGPT

**What it is:** A conversational AI system built on GPT (Generative Pre-trained Transformer) architecture.

**How it works (simplified):**
1. **Pre-training:** GPT is trained on massive internet text to predict the next word. It learns grammar, facts, reasoning patterns — essentially compressing the internet's knowledge.
2. **Fine-tuning with RLHF (Reinforcement Learning from Human Feedback):** Human evaluators rate model responses. A reward model is trained on these ratings. The GPT model is then fine-tuned to maximise the reward model's score — making it more helpful, harmless, and honest.
3. **Serving:** User sends a prompt → the model generates a response token by token → the response is returned.

**System components beyond the model:** Content filtering, rate limiting, user session management, safety guardrails, monitoring for misuse, feedback collection, A/B testing of model versions.

---

## 2.3 Recommender Systems

**Recommender systems** predict what items (products, movies, songs, articles) a user would prefer, based on their past behaviour and the behaviour of similar users.

### Three Main Approaches

| Approach | How it works | Pros | Cons | Example |
|---|---|---|---|---|
| **Collaborative filtering** | "Users who liked what you liked also liked X." Find similar users and recommend what they enjoyed. | No need to understand the items themselves. Discovers unexpected recommendations. | Cold-start problem (new users/items have no history). Popularity bias. | Netflix: "Because users similar to you watched..." |
| **Content-based filtering** | "You liked items with features X, Y, Z. Here are other items with similar features." | No cold-start for items (can recommend new items with known features). Transparent recommendations. | Limited diversity — recommends more of the same. Needs good feature descriptions. | Spotify: "Based on the genre and tempo of songs you listen to..." |
| **Hybrid** | Combines collaborative + content-based approaches. | Best accuracy. Overcomes individual approach weaknesses. | More complex to build and maintain. | Amazon, YouTube, Netflix all use hybrid approaches. |

### Case Study: Netflix Recommendation System

Netflix estimates that 80% of what users watch comes from recommendations, not search. Their system:

1. **Data signals:** Viewing history, ratings, time of day, device, browse/scroll behaviour, what was started but abandoned.
2. **Multiple models:** Different models for different parts of the UI — "Top Picks," "Because You Watched," "Trending Now," homepage row ordering.
3. **Personalised thumbnails:** Even the movie thumbnail image is selected by an ML model based on what visuals appeal to each user.
4. **Offline + Online:** Heavy model training happens offline (batch). Real-time personalisation (reranking based on current session context) happens online.

---

## 2.4 Conversational AI

**Conversational AI** systems interact with users through natural language — understanding questions, maintaining context across a conversation, and generating appropriate responses.

### Types

| Type | Complexity | How it works | Example |
|---|---|---|---|
| **Rule-based chatbot** | Simple | Follows pre-defined decision trees and keyword matching. | "Press 1 for billing, 2 for support." IVR systems. |
| **Intent-based NLU bot** | Medium | Classifies user intent (e.g. "check balance," "transfer money") and extracts entities (amount, account). Routes to appropriate handler. | Bank chatbots, customer service bots (Dialogflow, Rasa). |
| **LLM-based assistant** | High | Uses a large language model to understand and generate responses. Can handle open-ended conversations. | ChatGPT, Claude, Google Bard, enterprise copilots. |
| **RAG-based assistant** | High | LLM + Retrieval: fetches relevant documents from a knowledge base, feeds them to the LLM as context for grounded answers. | Enterprise Q&A systems (answer questions from company documentation). |

### Key Concept: RAG (Retrieval-Augmented Generation)

RAG addresses the hallucination problem by giving the LLM access to a curated knowledge base:

```
User Query → Search Knowledge Base → Retrieve relevant documents
                                           ↓
                           LLM generates answer GROUNDED in retrieved documents
                                           ↓
                                    Response to user
```

This makes the AI's answers more factual and verifiable — it can cite its sources.

---

## 2.5 Computer Vision

**Computer vision** enables machines to interpret and understand visual information from the world — images, videos, live camera feeds.

### Key Tasks

| Task | What it does | Example |
|---|---|---|
| **Image classification** | Assign a label to an entire image. | "This is a cat." Google Photos auto-tagging. |
| **Object detection** | Identify and locate multiple objects within an image (bounding boxes). | Self-driving cars detecting pedestrians, cars, traffic signs. |
| **Semantic segmentation** | Classify every pixel in an image. | Medical imaging: segment tumour vs healthy tissue at pixel level. |
| **Image generation** | Create new images from text descriptions or other images. | DALL-E: "A cat riding a bicycle on Mars, oil painting style." |
| **Facial recognition** | Identify or verify a person from their face. | Phone unlock (Face ID), airport security. |
| **OCR (Optical Character Recognition)** | Extract text from images. | Scanning documents, reading license plates. |

### Underlying Technology: Convolutional Neural Networks (CNNs)

CNNs are the backbone of modern computer vision. They work by applying learnable filters (convolutions) across an image to detect features at increasing levels of abstraction:

```
Raw Pixels → Edges & Textures → Shapes & Patterns → Object Parts → Whole Objects
  (Layer 1)      (Layer 2)         (Layer 3)          (Layer 4)      (Layer 5)
```

---

## 2.6 Autonomous Systems

**Autonomous systems** operate in the physical world with minimal or no human intervention, making real-time decisions based on sensor input.

### Key Characteristics

- **Perception:** Sense the environment using cameras, LiDAR, radar, GPS, IMU sensors.
- **Decision-making:** Plan actions based on perceived environment and goals.
- **Action:** Execute physical actions (steer, accelerate, brake, pick up object).
- **Learning:** Improve performance over time from experience and feedback.

### Levels of Autonomy (SAE Levels for Vehicles)

| Level | Name | Description | Example |
|---|---|---|---|
| 0 | No Automation | Human does everything. | Standard car, no assistance. |
| 1 | Driver Assistance | System controls ONE function (steering OR speed). | Adaptive cruise control, lane-keeping assist. |
| 2 | Partial Automation | System controls steering AND speed. Human must monitor. | Tesla Autopilot, GM Super Cruise. |
| 3 | Conditional Automation | System handles driving in specific conditions. Human takes over when requested. | Mercedes Drive Pilot (highway only). |
| 4 | High Automation | System handles all driving in specific areas. No human needed in those areas. | Waymo robotaxis (geofenced areas). |
| 5 | Full Automation | System handles all driving everywhere. No steering wheel needed. | Doesn't exist yet. |

### Case Study: Autonomous Vehicles

An autonomous vehicle is one of the most complex AI systems ever built. It combines:

- **Computer vision** (cameras) — detect lanes, traffic lights, pedestrians, other vehicles.
- **LiDAR** — create 3D point cloud map of surroundings (distance to objects).
- **Sensor fusion** — combine camera, LiDAR, radar, GPS, IMU data into a unified world model.
- **Path planning** — decide where to go (route) and how to get there (trajectory).
- **Control** — execute the planned trajectory (steering, throttle, braking).
- **Prediction** — predict what other road users will do next (will that pedestrian step onto the road?).

All of this must happen in **real-time** (<100ms response), with **extreme reliability** (one failure = potential fatality), in **unpredictable environments** (weather, construction, erratic drivers).

---
---

# Session 3: AI System Architecture

---

## 3.1 The Five-Layer AI System Architecture

A modern AI system is structured in layers, each with distinct responsibilities. This layered architecture enables teams to work independently, replace components without affecting others, and scale each layer based on its specific needs.

```
┌─────────────────────────────────────────────────────────┐
│                   APPLICATION LAYER                       │
│   User interface, business logic, A/B testing, UX        │
├─────────────────────────────────────────────────────────┤
│                   MODEL LAYER                             │
│   Model serving, inference, model registry, versioning   │
├─────────────────────────────────────────────────────────┤
│                   DATA LAYER                              │
│   Data pipelines, feature store, data warehouse/lake     │
├─────────────────────────────────────────────────────────┤
│                   INFRASTRUCTURE LAYER                    │
│   Compute (GPUs/TPUs), storage, networking, orchestration│
├─────────────────────────────────────────────────────────┤
│                   MONITORING LAYER                        │
│   Logging, metrics, drift detection, alerting, feedback  │
└─────────────────────────────────────────────────────────┘
```

### 3.1.1 Data Layer

The foundation of every AI system. Responsible for collecting, storing, processing, and serving data.

| Component | Purpose | Examples |
|---|---|---|
| **Data ingestion** | Collect data from sources (databases, APIs, logs, streams). | Apache Kafka, AWS Kinesis, Airbyte. |
| **Data storage** | Store raw and processed data durably. | Data Lakes (S3, GCS), Data Warehouses (Snowflake, BigQuery). |
| **Data processing** | Clean, transform, aggregate data. ETL/ELT pipelines. | Apache Spark, dbt, Airflow. |
| **Feature store** | Manage and serve ML features consistently across training and serving. | Feast, Tecton, Hopsworks. |
| **Data quality** | Monitor data for completeness, freshness, schema changes, anomalies. | Great Expectations, Deequ. |

### 3.1.2 Model Layer

Handles everything related to the ML model — from training to serving predictions.

| Component | Purpose | Examples |
|---|---|---|
| **Experiment tracking** | Log experiments, hyperparameters, metrics, and artifacts. | MLflow, Weights & Biases, Neptune. |
| **Training pipeline** | Automate model training — data loading, preprocessing, training, evaluation. | Kubeflow, SageMaker Pipelines, Vertex AI. |
| **Model registry** | Store, version, and manage trained models. Track which model is in production. | MLflow Model Registry, SageMaker Model Registry. |
| **Model serving** | Host models and serve predictions via APIs. Handle batching, caching, scaling. | TensorFlow Serving, TorchServe, Triton, Seldon. |
| **Model evaluation** | Evaluate model performance on held-out test sets. Compare against baselines. | Custom evaluation pipelines, benchmarking suites. |

### 3.1.3 Application Layer

Where the model's predictions meet the real world — the user-facing layer.

| Component | Purpose | Examples |
|---|---|---|
| **API gateway** | Single entry point for all model requests. Rate limiting, authentication, routing. | Kong, AWS API Gateway, FastAPI. |
| **Business logic** | Transform raw model output into user-relevant actions (e.g. model says 0.87 → show "Recommended"). | Application code in Python/Java/Go. |
| **A/B testing** | Test different model versions on different user groups to measure impact. | LaunchDarkly, Optimizely, custom solutions. |
| **User interface** | Present results to users (web app, mobile app, dashboard). | React, Flutter, Streamlit (for prototyping). |
| **Feedback collection** | Capture user feedback (clicks, ratings, corrections) to improve the model. | Custom event logging, analytics pipelines. |

### 3.1.4 Infrastructure Layer

The compute and networking resources that run everything.

| Component | Purpose | Examples |
|---|---|---|
| **Compute** | CPU/GPU/TPU for training and inference. | AWS EC2 (GPU), Google TPU, Azure GPU VMs. |
| **Container orchestration** | Deploy and scale model-serving containers. | Kubernetes (EKS/GKE/AKS), Docker. |
| **Storage** | Object storage for data and models. | S3, GCS, Azure Blob. |
| **CI/CD** | Automated build, test, deploy pipelines for models and code. | GitHub Actions, GitLab CI, Jenkins. |
| **Infrastructure as Code** | Define infrastructure declaratively. | Terraform, Pulumi, CloudFormation. |

### 3.1.5 Monitoring Layer

Observes the entire system and alerts when things go wrong.

| What to monitor | Why | Metrics |
|---|---|---|
| **Model performance** | Model accuracy may degrade over time as data shifts. | Accuracy, precision, recall, F1, AUC over time. |
| **Data drift** | The distribution of incoming data may change (new user demographics, seasonal changes). | Statistical tests (KS test, PSI) comparing training data vs serving data distributions. |
| **Prediction drift** | The distribution of predictions may shift even if data looks similar. | Distribution of predicted classes/scores over time. |
| **System health** | Infrastructure may have issues (high latency, out-of-memory, disk full). | Latency (p50, p95, p99), error rate, throughput, memory/CPU utilization. |
| **Business metrics** | Ultimately, does the model improve business outcomes? | Click-through rate, conversion rate, revenue, user retention. |

---

## 3.2 Architecture Walkthrough: A Modern AI Application

**Example: An E-Commerce Product Recommendation System**

Let's trace how a recommendation flows through the architecture when a user opens the app:

```
1. USER opens app
      ↓
2. APPLICATION LAYER
   - API gateway receives request (user_id, context)
   - Business logic: "This user is on the homepage → need Top Picks"
      ↓
3. MODEL LAYER
   - Model serving endpoint receives user features
   - Model computes scores for candidate items
   - Returns top 20 ranked items
      ↓
4. DATA LAYER
   - Feature store provides user features (past purchases, browsing history)
   - Feature store provides item features (category, price, popularity)
   - All features served with <10ms latency (pre-computed)
      ↓
5. APPLICATION LAYER (continued)
   - Business logic filters: remove out-of-stock, apply diversity rules
   - A/B testing: user in experiment group → show new model's results
   - Render final 10 items in the UI
      ↓
6. USER sees recommendations, clicks on Item #3
      ↓
7. MONITORING LAYER
   - Log: user saw 10 items, clicked item #3 → click-through rate tracked
   - Data pipeline: user click event sent to data lake for future training
   - Model monitor: check if today's recommendation distribution looks normal
      ↓
8. FEEDBACK LOOP
   - Click data (what users engaged with) feeds back into training data
   - Next model retrain will include this new data
```

---
---

# Session 4: AI Ecosystem and Model Marketplaces

---

## 4.1 Foundation Models

A **foundation model** is a large AI model trained on broad, diverse data at scale, designed to be adapted (fine-tuned) for a wide range of downstream tasks. The term was coined by Stanford's HAI in 2021.

### Key Characteristics

| Characteristic | Explanation |
|---|---|
| **Trained on massive data** | Hundreds of billions of tokens (text), millions of images. Internet-scale data. |
| **General-purpose** | Not trained for a specific task — can be adapted to many tasks (translation, summarisation, coding, Q&A, image generation). |
| **Transfer learning** | Knowledge learned during pre-training transfers to downstream tasks, even with limited task-specific data. |
| **Emergent abilities** | Large models exhibit capabilities that weren't explicitly trained for — e.g. chain-of-thought reasoning, code generation in GPT-4. |
| **Expensive to train** | Training GPT-4 estimated at $100M+. Only a few organisations can train foundation models from scratch. |
| **Accessible via APIs or fine-tuning** | Most users access foundation models via APIs (OpenAI, Anthropic) or fine-tune open-source versions (LLaMA, Mistral). |

### Notable Foundation Models

| Model | Organisation | Type | Key feature |
|---|---|---|---|
| **GPT-4 / GPT-4o** | OpenAI | Text + multimodal | Most capable general-purpose LLM (as of 2024). |
| **Claude 3.5** | Anthropic | Text + vision | Focus on safety and helpfulness. Long context (200K tokens). |
| **Gemini** | Google DeepMind | Multimodal | Natively multimodal (text, image, audio, video). |
| **LLaMA 3** | Meta | Text (open-source) | Open-weights model enabling community fine-tuning. |
| **Mistral** | Mistral AI | Text (open-source) | Efficient, strong performance for its size. |
| **Stable Diffusion** | Stability AI | Image generation (open-source) | Open-source image generation. |
| **DALL-E 3** | OpenAI | Image generation | Text-to-image with high fidelity and prompt following. |
| **Whisper** | OpenAI | Speech-to-text (open-source) | Multilingual speech recognition. |

---

## 4.2 Open-Source Models

Open-source models provide model weights (and sometimes training code/data) that anyone can download, inspect, modify, and deploy.

### Why Open-Source Matters for AI

| Benefit | Explanation |
|---|---|
| **No API costs** | Run on your own infrastructure — no per-request charges. |
| **Data privacy** | Your data never leaves your servers. Critical for healthcare, finance, government. |
| **Customisation** | Fine-tune on your domain data. Modify architecture. Full control. |
| **No vendor lock-in** | Switch between models freely. Not dependent on one provider. |
| **Community innovation** | Community creates fine-tuned variants, tools, optimisations (quantisation, distillation). |

### Key Open-Source Hubs

| Hub | What it offers |
|---|---|
| **Hugging Face** | The "GitHub of ML." 500K+ models, 100K+ datasets, transformers library, inference API, Spaces for demos. The central hub for open-source AI. |
| **GitHub** | Model code, training scripts, tools. |
| **Kaggle** | Datasets, competitions, notebooks, some pre-trained models. |
| **TensorFlow Hub / PyTorch Hub** | Pre-trained models for specific frameworks. |

---

## 4.3 APIs and Cloud AI Services

For organisations that don't want to manage models themselves, cloud providers and AI companies offer **AI-as-a-Service** via APIs.

### Types of AI APIs

| Category | What it provides | Examples |
|---|---|---|
| **General-purpose LLM APIs** | Text generation, summarisation, translation, Q&A, code generation. | OpenAI API (GPT-4), Anthropic API (Claude), Google Gemini API. |
| **Embedding APIs** | Convert text/images into numerical vectors for search, similarity, clustering. | OpenAI Embeddings, Cohere Embed, Sentence Transformers. |
| **Image APIs** | Image generation, editing, analysis. | DALL-E, Stability AI, Google Vision AI. |
| **Speech APIs** | Speech-to-text, text-to-speech. | OpenAI Whisper, Google Cloud Speech, AWS Transcribe. |
| **Cloud ML Platforms** | End-to-end ML lifecycle — training, serving, monitoring. | AWS SageMaker, Google Vertex AI, Azure ML. |

### API Pricing Models

| Model | How you pay | Example |
|---|---|---|
| **Per token** | Pay per input + output token (text APIs). | OpenAI: $0.01/1K input tokens for GPT-4o. |
| **Per image** | Pay per image generated or analysed. | DALL-E: $0.04/image (standard). |
| **Per minute** | Pay per minute of audio processed. | Whisper API: $0.006/minute. |
| **Subscription** | Flat monthly fee for usage tiers. | ChatGPT Plus: $20/month. |

---

## 4.4 Model Hubs

A **model hub** is a centralised platform where models are shared, discovered, documented, and versioned.

### Hugging Face Hub — The Standard

Hugging Face has become the de facto hub for AI models. Key features:

| Feature | What it provides |
|---|---|
| **Model Cards** | Documentation for each model: what it does, how it was trained, limitations, intended uses, performance benchmarks. |
| **Datasets** | 100K+ datasets ready to use, with preview and streaming capabilities. |
| **Spaces** | Host interactive demos of models (Gradio, Streamlit apps). |
| **Inference API** | Run models directly via API without deploying your own infrastructure. |
| **Transformers Library** | Python library to load and use any model from the hub in a few lines of code. |
| **PEFT / LoRA** | Tools for parameter-efficient fine-tuning of large models. |

**Example — Using Hugging Face in 3 lines of Python:**
```python
from transformers import pipeline
classifier = pipeline("sentiment-analysis")
result = classifier("I love this product! It's amazing.")
# Output: [{'label': 'POSITIVE', 'score': 0.9998}]
```

---

## 4.5 Build vs Buy Decisions

One of the most important strategic decisions in AI: should you build your own model or use an existing one?

### Decision Framework

```
┌─────────────────────────────────────────────────┐
│    Is the task generic (translation, summary)?   │
│         YES → Use API (OpenAI, Claude)           │
│         NO  ↓                                    │
│    Is there an open-source model close enough?   │
│         YES → Fine-tune open-source model        │
│         NO  ↓                                    │
│    Is the task critical and differentiated?       │
│         YES → Build custom model from scratch     │
│         NO  → Re-evaluate if you need ML at all  │
└─────────────────────────────────────────────────┘
```

### Comparison Table

| Aspect | Use API | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| **Time to deploy** | Hours to days | Days to weeks | Months |
| **Cost (upfront)** | Zero | Moderate (compute for fine-tuning) | High (team + compute + data) |
| **Cost (ongoing)** | Per-request API fees | Infrastructure hosting costs | Team + infrastructure |
| **Data privacy** | Data sent to third party | Data stays on your servers | Full control |
| **Customisation** | Limited (prompt engineering) | High (adapt to your domain) | Maximum |
| **Maintenance** | Provider handles updates | You handle updates, retraining | You handle everything |
| **Vendor dependency** | High (API may change, price may increase) | Low (model is yours) | None |
| **Best for** | Prototyping, generic tasks, low volume | Domain-specific tasks, privacy-sensitive | Core competitive advantage |

### Real-World Examples

| Scenario | Recommended approach | Why |
|---|---|---|
| Startup building a customer support chatbot | **API (GPT-4)** | Fast to market, low upfront cost, generic task. |
| Bank building a fraud detection model | **Build from scratch** | Proprietary data, regulatory requirements, core business differentiator. |
| Hospital building a radiology AI assistant | **Fine-tune open-source** | Need domain adaptation (medical images), data privacy (patient data can't leave servers). |
| Marketing team generating ad copy | **API (Claude/GPT-4)** | Generic creative task, low volume, quality is "good enough." |
| Self-driving car company | **Build from scratch** | Safety-critical, massive proprietary data, core technology. |

---

*End of Sessions 1–4*
