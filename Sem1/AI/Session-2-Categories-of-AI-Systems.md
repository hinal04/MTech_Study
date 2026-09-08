# Session 2: Categories of AI Systems

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 1-2, T2 Chapter 1-2, Class Notes
>
> **Contact Session:** 2 (Module 1: Foundations of AI Systems)
>
> **Case Studies:** ChatGPT, Netflix Recommendations, Autonomous Vehicles

---

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


---

*End of Session 2*
