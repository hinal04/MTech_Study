# Session 2: Categories of AI Systems (58 Slides)

> BITS Pilani — SS ZG662 | Module 1: Foundations of AI Systems
> Case Studies: ChatGPT, Netflix, Waymo

---

## 2.0 Choosing the Right AI

### Decision Framework (5 Steps)

1. Define the business problem
2. Check what data is available
3. Latency requirements (ms? minutes? hours?)
4. Accuracy needs
5. Choose AI category

### Quick Selection Guide

| Problem Type | Best Category |
|---|---|
| Predict a number or category from historical data | Predictive AI |
| Create new text, images, content | Generative AI |
| Show users items they'll like | Recommender System |
| Let users ask questions in natural language | Conversational AI |
| Understand images or video | Computer Vision |
| Real-time decisions in the physical world | Autonomous System |

### Six AI Categories — Comparative Table

| Category | Input | Output | Technique | Example |
|---|---|---|---|---|
| Predictive AI | Structured historical data | Number/label | Supervised ML | Swiggy delivery time prediction |
| Generative AI | Text prompt/seed | New content | LLMs, Diffusion Models | ChatGPT, DALL-E |
| Recommender | User behaviour + item features | Ranked item list | CF/CB/Hybrid | Netflix "Top Picks" |
| Conversational AI | Natural language message | NL response | NLU + Dialog + LLM + RAG | HDFC Bank chatbot |
| Computer Vision | Images/video | Labels, boxes, segments | CNNs | Google Lens |
| Autonomous Systems | Sensor data (camera, LiDAR, radar) | Physical actions | Sensor fusion + planning | Waymo self-driving |

---

## 2.1 Predictive AI

### 4 Prediction Types

| Type | Output | Example |
|---|---|---|
| Classification | Category (binary/multi-class) | Spam/not-spam, churn/no-churn |
| Regression | Continuous number | House price, delivery time |
| Time-series | Future values over time | Stock price, demand forecast |
| Anomaly Detection | Normal/Anomalous | Fraud detection, intrusion detection |

### Classification vs Regression — Business Comparison

| Aspect | Classification | Regression |
|---|---|---|
| Output | Discrete category | Continuous value |
| Metric | Accuracy, Precision, Recall, F1 | MAE, RMSE, MAPE |
| Example | Will customer churn? (Yes/No) | How much will they spend? (₹) |

### Uplift Modelling

Predicts the **incremental impact** of an action. Segments customers into 4 groups:

| Group | Without Action | With Action | Strategy |
|---|---|---|---|
| **Persuadables** | Won't buy | Will buy | Target these — highest ROI |
| **Sure Things** | Will buy | Will buy | Don't waste money — they'll buy anyway |
| **Lost Causes** | Won't buy | Won't buy | Don't target — nothing helps |
| **Sleeping Dogs** | Will buy | Won't buy | Avoid — action actually hurts |

**Examples:** Amazon (send coupons only to Persuadables), Jio (retention offers only where effective)

### Predictive Metrics

**Model metrics:** Accuracy, Precision, Recall, F1, AUC-ROC
**Business KPIs:** Revenue uplift, cost saved, retention rate, false positive cost

### Deployment Challenges

Data drift, concept drift, feature availability at serving time, latency constraints, A/B testing, shadow deployment.

### When to Use / When Not to Use

| Use When | Don't Use When |
|---|---|
| Historical data available | No historical data |
| Patterns exist in data | Environment constantly changes |
| Measurable outcome defined | Simple rules suffice |
| Sufficient labelled data | Outcome not measurable |

---

## 2.2 Generative AI

### 5 Types

Text generation, image generation, code generation, music generation, video generation.

### LLM — Token-by-Token Generation

LLMs predict the next token in a sequence. Input → tokenize → model → probability distribution → sample next token → repeat.

### Key Concepts

| Concept | Definition |
|---|---|
| **Prompt** | Input instruction to the model |
| **Hallucination** | Model confidently generates wrong/made-up information |
| **Temperature** | Controls randomness (0 = deterministic, 1 = creative) |
| **RLHF** | Reinforcement Learning from Human Feedback — aligns model with human preferences |
| **Tokens** | Basic unit LLM processes (~¾ of a word) |
| **Context window** | Max tokens model can process at once (e.g., 128K for GPT-4) |

### Case Study: ChatGPT

**3 Steps:**
1. **Pre-training** — Train on internet text (next-token prediction)
2. **RLHF** — Human raters rank outputs → model learns to produce preferred responses
3. **Serving** — Safety filters, rate limiting, A/B testing, content moderation

**Key lesson:** The system beyond the model (safety, monitoring, scaling) matters as much as the model itself.

### GenAI Enterprise Workflow

```
Business Need → Select Model → Prompt/Fine-tune → Integrate Data →
Safety Guardrails → Human Review → Test → Deploy → Monitor
```

### RAG vs Fine-Tune vs Prompting

| Approach | When | Data Needed | Cost | Time | Best For |
|---|---|---|---|---|---|
| Prompt Engineering | Generic tasks, quick start | None | Free (API cost only) | Minutes | Simple tasks, prototypes |
| RAG | Need answers from company docs | Documents (no training) | Low | Days | Q&A over knowledge base |
| Fine-Tuning | Domain-specific style/knowledge | Thousands of examples | Medium-High | Days-weeks | Domain adaptation |

**HR Policy Assistant (RAG example):** Employee asks question → retrieve relevant policy doc chunks → feed to LLM → generate grounded answer with citations.

### Productivity vs Automation

| Mode | How | % of Enterprise Use |
|---|---|---|
| **Copilot** (Productivity) | AI assists, human decides | 90%+ |
| **Autopilot** (Automation) | AI acts alone | <10% |

### Hallucination/Bias/Data Risks

- Confident wrong answers, fake citations
- Reproduces biases from training data
- Proprietary data leakage if sent to external APIs
- Copyright issues with generated content

### GenAI Across Enterprise Functions

| Function | Use Case |
|---|---|
| Marketing | Content generation, ad copy, personalization |
| Customer Support | Chatbots, ticket summarization |
| Engineering | Code generation, documentation |
| Legal | Contract analysis, summarization |
| HR | Job descriptions, policy Q&A |
| Finance | Report generation, anomaly explanation |

### Enterprise Implementation Considerations

Data privacy, cost management (token costs at scale), hallucination mitigation, governance, workflow integration.

---

## 2.3 Recommender Systems

### 3 Types

| Type | How | Strength | Weakness |
|---|---|---|---|
| **Collaborative Filtering** | "Users like you liked X" | Discovers unexpected items | Cold start (new users) |
| **Content-Based** | "Items similar to what you liked" | Works for new items | Filter bubble |
| **Hybrid** | Combines both | Best accuracy | More complex |

### Cold Start Problem

- **New user:** No history → use demographics, popular items, onboarding quiz
- **New item:** No interactions → use item features, content similarity

### Recommendation Architecture (Pipeline)

```
Candidate Generation → Ranking → Top-N → Business Rules → Display
```

### Recommendation Metrics

CTR, Conversion Rate, NDCG, Revenue per User, Coverage, Diversity.

### Case Study: Netflix

**Data signals:** Viewing history, ratings, what was started but abandoned, time of day, device.

**Multiple models:** Ranking model, similarity model, thumbnail selection, row ordering.

**Personalisation at every touchpoint:** Homepage, search results, thumbnails, "Because you watched X" rows.

**Business value:**
- 80% of content watched comes from recommendations
- $1B/year saved in retention
- **Challenges:** Filter bubble, popularity bias, cold start

---

## 2.4 Conversational AI

### 4 Types (Evolution)

| Type | How | Example |
|---|---|---|
| Rule-based | Fixed decision trees | "Press 1 for billing" |
| Intent-based | NLU extracts intent + entities | Alexa, Google Assistant |
| LLM-based | Foundation model generates responses | ChatGPT |
| RAG-based | Retrieve docs + LLM generates grounded answer | Enterprise knowledge bots |

### RAG Flow

```
User question → Search knowledge base → Retrieve relevant chunks →
Augment prompt with context → LLM generates answer → Return with citations
```

### LLM vs Traditional Chatbots (8 Dimensions)

| Dimension | Traditional | LLM-based |
|---|---|---|
| Understanding | Pattern matching | Deep contextual understanding |
| Flexibility | Fixed intents only | Handles any topic |
| Training data | Thousands of labeled examples | Pre-trained, few-shot capable |
| Response | Template-based | Dynamically generated |
| Context | Short-term only | Long conversation memory |
| Setup time | Weeks-months | Days |
| Accuracy | High for trained intents | Variable, may hallucinate |
| Cost | Low per interaction | Higher (token costs) |

### Enterprise Architecture

```
Channel Adapter → NLU Engine → Dialog Manager → Knowledge Base →
Response Generator → Channel Adapter
```

### Risks, Escalation, HITL

**Risks:** Hallucination, inappropriate responses, data leakage, prompt injection.

**Escalation triggers:** Low confidence, sensitive topics, repeated failures, user requests human.

**3 HITL patterns:**
1. Human reviews before response sent
2. Human reviews flagged responses
3. Human available for escalation

### Customer Service vs Employee Support

| Aspect | Customer-facing (External) | Employee-facing (Internal) |
|---|---|---|
| Tone | Formal, brand-aligned | Casual, direct |
| Data sensitivity | High (PII) | Medium (internal policies) |
| Escalation | To human agent | To subject matter expert |
| Risk tolerance | Low (brand damage) | Higher (internal use) |

---

## 2.5 Computer Vision

### 6 Tasks

| Task | Output | Example |
|---|---|---|
| Classification | Label for whole image | "This is a cat" |
| Object Detection | Bounding boxes + labels | Self-driving car detecting pedestrians |
| Segmentation | Pixel-level classification | Medical image analysis |
| Face Recognition | Identity match | Phone unlock, attendance |
| OCR | Text from images | Document digitization |
| Image Generation | New images | DALL-E, Stable Diffusion |

### CNN Layer-by-Layer

```
Input Image → Convolutional Layers (detect edges, textures, shapes) →
Pooling Layers (reduce dimensions) → Fully Connected Layers → Output (classification)
```

### Enterprise Pipeline

```
Data Collection → Annotation → Training → Validation → Deployment → Monitoring
```

Edge deployment: Run models on cameras/devices instead of cloud.

### Manufacturing/Quality Inspection

- Defect detection on production lines
- Examples: Tata Motors, Asian Paints, Amul
- ROI: 60% cost reduction, 99.5% accuracy

### Retail/Healthcare/Logistics

| Domain | Use Case |
|---|---|
| Retail | Shelf monitoring (Amazon Go), cashier-less checkout |
| Healthcare | X-ray analysis, pathology (cancer detection) |
| Logistics | Package sorting, warehouse robots |

### Deployment Challenges

Lighting variations, camera angle changes, class imbalance, edge computing constraints, privacy concerns, annotation cost.

---

## 2.6 Autonomous Systems

### Core Loop: Sense → Think → Act

### SAE Levels (0–5)

| Level | Name | Example |
|---|---|---|
| 0 | No Automation | Old car |
| 1 | Driver Assistance | Adaptive cruise control |
| 2 | Partial | Tesla Autopilot |
| 3 | Conditional | Mercedes Drive Pilot |
| 4 | High (geofenced) | Waymo robotaxis |
| 5 | Full (everywhere) | **Does NOT exist yet** |

### Decision-Making/Planning/Control (<100ms)

Perception → Prediction (will that pedestrian cross?) → Path Planning → Decision Making (go/stop/turn) → Control (steering, braking) — all within ~100ms.

### Case Study: Waymo

- **Sensors:** 29 cameras, 4 LiDAR, 6 radar, GPS+IMU
- **Sensor fusion:** Combine all sensor data into unified 3D model
- **20M+ autonomous miles driven**
- **Key lesson:** Safety engineering (handling edge cases, fail-safe behaviours) is the hardest part

---

## 2.7 Three Case Studies — Summary

| Case Study | Category | Key Lesson |
|---|---|---|
| **ChatGPT** | Generative AI | RLHF + safety filters + monitoring — system beyond model matters |
| **Netflix** | Recommender | Multiple models + personalisation at every touchpoint + A/B testing |
| **Waymo** | Autonomous | Sensor fusion + real-time <100ms decisions + safety engineering |

---
