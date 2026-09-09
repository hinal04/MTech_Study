# Session 2: Categories of AI Systems

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 1-2, T2 Chapter 1-2, Class Notes
>
> **Contact Session:** 2 (Module 1: Foundations of AI Systems)
>
> **Case Studies:** ChatGPT, Netflix Recommendations, Autonomous Vehicles

---

## Table of Contents

- [2.1 Predictive AI](#21-predictive-ai)
- [2.2 Generative AI](#22-generative-ai)
- [2.3 Recommender Systems](#23-recommender-systems)
- [2.4 Conversational AI](#24-conversational-ai)
- [2.5 Computer Vision](#25-computer-vision)
- [2.6 Autonomous Systems](#26-autonomous-systems)

---

## 2.1 Predictive AI

### What is Predictive AI? (Simple)

Predictive AI looks at **past data** to guess **what will happen next**. It answers: "Based on what happened before, what is likely to happen in the future?"

> **Analogy:** You notice it rains every time dark clouds appear. So when you see dark clouds tomorrow, you predict rain and carry an umbrella. You used past observations to predict the future — that's exactly what Predictive AI does, but with millions of data points instead of just your memory.

### Types of Prediction Tasks

| Task | What It Predicts | Output Type | Easy Example |
|---|---|---|---|
| **Classification** | Which category does this belong to? | A label (Category A or B or C) | Is this email spam or not spam? |
| **Regression** | What is the number? | A continuous number | What will this house sell for? (Rs 85 lakhs) |
| **Time-series forecasting** | What happens next in a sequence? | Future values | What will tomorrow's stock price be? |
| **Anomaly detection** | Is this data point unusual? | Normal or Anomalous | Is this credit card transaction fraudulent? |

### Live Examples of Predictive AI

| Company | What They Predict | How It Works | Impact |
|---|---|---|---|
| **Zomato/Swiggy** | Delivery time | Uses distance, restaurant prep time, traffic, weather, driver availability to predict "Your food arrives in 32 min" | Accurate ETA keeps customers happy and reduces support calls |
| **CRED** | Credit score / risk | Analyses spending patterns, repayment history, income to predict if a user will default on credit card payment | Helps decide credit limits and cashback offers |
| **Flipkart** | Demand forecasting | Predicts how many units of each product will sell next week in each warehouse location | Pre-stocks warehouses → faster delivery → higher customer satisfaction |
| **HDFC Bank** | Fraud detection | ML model analyses every transaction in real-time — amount, location, time, merchant type — and flags unusual patterns | Catches fraud within milliseconds before the transaction completes |
| **Uber** | Surge pricing | Predicts demand in each area for the next 15 minutes. If demand > supply, prices increase to attract more drivers | Balances supply and demand dynamically |

### How a Predictive Model Works (Step by Step)

Let's walk through building a house price predictor:

```
Step 1: Collect Data
┌─────────┬──────┬──────────┬─────┬──────────┐
│ Area    │ BHK  │ Location │ Age │ Price    │
│ (sq ft) │      │          │(yrs)│ (lakhs)  │
├─────────┼──────┼──────────┼─────┼──────────┤
│ 1200    │ 2    │ Bandra   │ 5   │ 180      │
│ 800     │ 1    │ Andheri  │ 10  │ 75       │
│ 1500    │ 3    │ Powai    │ 2   │ 210      │
│ ...     │ ...  │ ...      │ ... │ ...      │
│ (10,000 rows of past sales)              │
└──────────────────────────────────────────┘

Step 2: Train Model
The ML algorithm finds patterns:
  - Price increases ~Rs 10,000 per sq ft
  - Bandra adds Rs 30 lakh premium over Andheri
  - Each year of age reduces price by Rs 2 lakh
  
Step 3: Predict
New house: 1100 sq ft, 2 BHK, Andheri, 3 years old
Model predicts: Rs 95 lakhs

Step 4: Evaluate
Compare prediction with actual sale price.
If error < 10%, model is acceptable.
```

---

## 2.2 Generative AI

### What is Generative AI? (Simple)

Generative AI **creates new content** — text, images, music, code, video — that didn't exist before. Instead of predicting a label or number, it generates entirely new things.

> **Analogy:** Predictive AI is like a weather forecaster (tells you what will happen). Generative AI is like a painter (creates something new that never existed before).

### Types of Generative AI

| Type | What It Creates | Technology Behind It | Live Examples |
|---|---|---|---|
| **Text generation** | Articles, emails, code, stories, summaries | Large Language Models (LLMs) — predict next word, one at a time | **ChatGPT** (OpenAI), **Claude** (Anthropic), **Gemini** (Google) |
| **Image generation** | Photos, art, designs from text descriptions | Diffusion Models — start from random noise, gradually refine into an image | **DALL-E 3** (OpenAI), **Midjourney**, **Stable Diffusion** |
| **Code generation** | Code in any programming language | LLMs trained on code repositories | **GitHub Copilot**, **Kiro**, **Cursor** |
| **Music/Audio** | Songs, sound effects, voice cloning | Audio diffusion models, neural audio codecs | **Suno**, **ElevenLabs** (voice), **MusicLM** (Google) |
| **Video generation** | Short video clips from text descriptions | Video diffusion models | **Sora** (OpenAI), **Runway Gen-3** |

### How Does an LLM Generate Text? (Simple Explanation)

An LLM works by **predicting one word at a time**. Given all the words so far, it predicts the most likely next word.

```
Input:  "The capital of India is"
                                    ↓ Model predicts
Output: "The capital of India is New"
                                    ↓ Model predicts again
Output: "The capital of India is New Delhi"
                                    ↓ continues...
```

It's like a super-advanced autocomplete on your phone keyboard — but trained on trillions of words from the internet, so it can write essays, code, and stories.

### Key Concepts You Must Know

| Concept | Simple Explanation | Example |
|---|---|---|
| **Prompt** | The instruction/question you give to the AI | "Write a Python function to sort a list" |
| **Prompt Engineering** | The art of writing better prompts to get better outputs | Instead of "summarise this," say "summarise this article in 3 bullet points for a 10-year-old" |
| **Hallucination** | When the AI confidently generates **wrong** information | ChatGPT says "The Eiffel Tower is 500m tall" (actual: 330m). It sounds right but is factually wrong. |
| **Temperature** | Controls how "creative" vs "safe" the output is | Temperature 0 = always picks the most likely word (safe, repetitive). Temperature 1 = more random (creative, risky). |
| **Fine-tuning** | Taking a general model and training it further on your specific data | Taking GPT and fine-tuning it on medical textbooks to create a medical AI assistant |
| **Token** | The basic unit an LLM works with — roughly 3/4 of a word | "Hello world" = 2 tokens. "Artificial intelligence" = 2-3 tokens. |
| **Context window** | How much text the model can "remember" at once | GPT-4o: 128K tokens (~300 pages). Claude 3.5: 200K tokens (~500 pages). |

### Case Study: ChatGPT — How It Works

ChatGPT is the most well-known generative AI. Here's how it was built, step by step:

```
Step 1: PRE-TRAINING (learning language)
├── Trained on massive internet text (books, websites, Wikipedia, code)
├── Learns grammar, facts, reasoning patterns
├── Like reading the entire internet and remembering it all
└── Cost: Estimated $100M+ in compute

Step 2: FINE-TUNING with RLHF (learning to be helpful)
├── RLHF = Reinforcement Learning from Human Feedback
├── Human evaluators rate responses ("this answer is helpful" / "this is harmful")
├── A reward model is trained on these ratings
├── ChatGPT is fine-tuned to maximise the reward model's score
└── Makes it more helpful, harmless, and honest

Step 3: SERVING (answering users)
├── User sends a message (prompt)
├── Model generates response word by word
├── Safety filters check the response
├── Response sent back to user
└── All in <2 seconds
```

**Beyond the model — the system around ChatGPT:**
- Content safety filters (block harmful outputs)
- Rate limiting (prevent abuse)
- User session management (remember conversation context)
- A/B testing (test new model versions on small % of users)
- Usage monitoring and billing

---

## 2.3 Recommender Systems

### What is a Recommender System? (Simple)

A recommender system predicts **what items you'll like** — products, movies, songs, restaurants — based on your past behaviour and what similar people liked.

> **Analogy:** Imagine a shopkeeper in your neighbourhood who knows you well. When you walk in, he says "I got new basmati rice — you'll love it, your neighbour Mrs. Sharma bought it last week and she has similar taste to you." That's a recommender system — but automated and working for millions of users simultaneously.

### Three Main Approaches

| Approach | How It Works (Simple) | Live Example | Strengths | Weaknesses |
|---|---|---|---|---|
| **Collaborative Filtering** | "People similar to you liked X, so you'll probably like X too" | **Netflix:** "Users who watched Breaking Bad also watched Better Call Saul" → recommends Better Call Saul to you | Discovers surprising recommendations you'd never search for | **Cold start problem:** Can't recommend to new users (no history). New items get no recommendations. |
| **Content-Based Filtering** | "You liked items with features A, B, C. Here are other items with similar features" | **Spotify:** "You listen to Arijit Singh Hindi songs at slow tempo. Here are similar Hindi slow songs by other artists" | Works for new items (just needs item features). No cold start for items. | Recommends more of the same. Creates a "filter bubble" — you never discover new genres. |
| **Hybrid** | Combines both approaches — uses user similarity AND item features | **Amazon, YouTube, Netflix** all use hybrid approaches | Best accuracy. Avoids weaknesses of each individual approach. | More complex to build and maintain |

### Case Study: Netflix Recommendation System

Netflix says **80% of what users watch comes from recommendations**, not search. Their system is worth billions of dollars.

```
How Netflix Recommends (Simplified):

1. DATA COLLECTED
   ├── What you watched (and for how long)
   ├── What you rated
   ├── What you searched for
   ├── What time of day you watch
   ├── What device you use (TV, phone, laptop)
   ├── What you started but stopped watching (and at what point)
   └── Your browsing/scrolling behaviour

2. MULTIPLE MODELS (not just one)
   ├── "Top Picks for You" → personalised ranking model
   ├── "Because You Watched Breaking Bad" → similar-items model
   ├── "Trending Now" → popularity + personalisation model
   ├── Row ordering on homepage → another model decides which ROW to show first
   └── Thumbnail selection → ML chooses which movie poster image appeals to YOU

3. PERSONALISED THUMBNAILS (fascinating detail)
   The same movie shows DIFFERENT poster images to different users!
   ├── If you watch romance → show the romantic scene
   ├── If you watch action → show the action scene
   └── If you watch comedy → show the funny scene
   All to increase the chance you'll click.

4. REAL-TIME + BATCH
   ├── Heavy model training: OFFLINE (batch processing overnight)
   └── Real-time adjustments: ONLINE (rerank based on current session)
```

### The Cold Start Problem — Explained Simply

The **cold start problem** is one of the biggest challenges in recommender systems:

| Scenario | Problem | How Companies Solve It |
|---|---|---|
| **New user** (just signed up) | No history — don't know what they like | Ask preferences during signup. Use demographic info. Show popular items first. |
| **New item** (just added to catalog) | No one has interacted with it yet | Use content-based features (genre, description). Boost new items in recommendations. |

> **Live Example:** When you create a new Netflix account, it asks you to pick 3 shows/movies you like. This "seed" data bootstraps the recommendation engine so it can start personalising immediately.

---

## 2.4 Conversational AI

### What is Conversational AI? (Simple)

Conversational AI systems **talk with users in natural language** — understanding questions, maintaining context across a conversation, and generating human-like responses.

> **Analogy:** Think of the difference between a vending machine (you press a button, get a fixed item) and a waiter at a restaurant (you can ask questions, change your mind, have a back-and-forth conversation). Conversational AI is the waiter, not the vending machine.

### Types of Conversational AI (From Simple to Advanced)

| Type | How It Works | Smartness Level | Live Example |
|---|---|---|---|
| **Rule-based chatbot** | Follows a fixed script. Keyword matching. | Low — can only handle pre-defined flows | IRCTC's "Press 1 for booking, Press 2 for cancellation" IVR system |
| **Intent-based bot** | Classifies what the user wants (intent) and extracts key info (entities) | Medium — handles common questions well | **HDFC Bank's Eva** — understands "What's my account balance?" (intent: check_balance, entity: account) |
| **LLM-based assistant** | Uses a large language model to understand and respond to any question | High — handles open-ended conversations | **ChatGPT**, **Claude**, **Google Gemini** |
| **RAG-based assistant** | LLM + searches a knowledge base for facts before answering | High + Accurate — answers grounded in real documents | Enterprise Q&A bots that answer from company documentation |

### Key Concept: RAG (Retrieval-Augmented Generation)

RAG solves the **hallucination problem** — LLMs sometimes make up facts. RAG forces the LLM to answer based on real documents.

```
How RAG Works:

User asks: "What is our company's leave policy for remote employees?"
                    ↓
Step 1: RETRIEVE — Search the company's HR document database
        → Finds: "HR Policy v2.3, Section 4.2: Remote employees 
           get 24 paid leaves per year..."
                    ↓
Step 2: AUGMENT — Feed the retrieved document + user question to the LLM
                    ↓
Step 3: GENERATE — LLM generates answer BASED ON the document
        → "Remote employees get 24 paid leaves per year as per 
           HR Policy v2.3, Section 4.2."
```

**Without RAG:** LLM might hallucinate "Remote employees get 20 leaves" (made up).
**With RAG:** LLM cites the actual document with the correct answer.

> **Live Example:** **Freshworks** (Indian SaaS company) uses RAG-based bots for customer support. The bot searches their product documentation and generates accurate answers. Resolved 60% of support tickets without human agents.

---

## 2.5 Computer Vision

### What is Computer Vision? (Simple)

Computer Vision gives machines the ability to **see and understand** images and videos — like giving eyes to a computer.

> **Analogy:** When you look at a photo of a dog, your brain instantly recognises it's a dog, identifies the breed, notices the background is a park. Computer Vision teaches machines to do the same thing — except the machine sees millions of tiny numbers (pixel values) and has to figure out what they mean.

### Key Tasks in Computer Vision

| Task | What It Does | How It Works (Simple) | Live Example |
|---|---|---|---|
| **Image Classification** | Labels the entire image with one category | "This image is a cat" | **Google Photos** automatically tags your photos: "beach," "wedding," "dog" |
| **Object Detection** | Finds and locates multiple objects with bounding boxes | "There's a car at position (100,200) and a person at position (300,150)" | **Self-driving cars** detecting pedestrians, other cars, traffic signs in real-time |
| **Semantic Segmentation** | Labels every single pixel in the image | "These pixels are road, these are sidewalk, these are sky" | **Medical imaging:** distinguishing tumour tissue from healthy tissue at pixel level |
| **Face Recognition** | Identifies who a person is from their face | Compares face features against a database of known faces | **iPhone Face ID** — unlocks your phone by recognising your face |
| **OCR (Optical Character Recognition)** | Reads text from images | Detects text regions → recognises characters | **Google Lens** — point camera at a menu in Japanese → instant translation to English |
| **Image Generation** | Creates new images from text descriptions | Diffusion models — start from noise, refine into image | **DALL-E:** "A photo of an astronaut riding a horse on Mars" → generates that image |

### How CNNs (Convolutional Neural Networks) Work — Simply

CNNs are the core technology behind most computer vision. They work in layers, each detecting increasingly complex things:

```
Layer 1: Detects simple features    → edges, lines, colours
Layer 2: Combines simple features   → corners, curves, textures  
Layer 3: Detects parts             → eyes, ears, wheels, windows
Layer 4: Detects whole objects     → "This is a cat" / "This is a car"

Think of it like: Letters → Words → Sentences → Meaning
                   Pixels → Edges → Parts → Object
```

> **Why "Convolutional"?** The network slides a small window (filter/kernel) across the image, looking for patterns. Like running your finger across a page to find specific words — the "sliding" motion is the convolution.

### Live Example: Google Lens

When you point Google Lens at a flower:
1. Camera captures the image (millions of pixels)
2. CNN processes the image layer by layer
3. Layer 1 detects petal edges and colours
4. Layer 2 detects petal shapes and patterns
5. Layer 3 recognises flower structure
6. Layer 4 classifies: "Rose — Damask variety"
7. App displays: "Damask Rose" with Wikipedia info

All in under 1 second, running on your phone.

---

## 2.6 Autonomous Systems

### What is an Autonomous System? (Simple)

An autonomous system is a machine that can **operate in the real world on its own** — sensing its environment, making decisions, and taking actions without (or with minimal) human control.

> **Analogy:** A remote-controlled car needs a human pressing buttons. An autonomous car drives itself — it sees the road, decides to turn, and turns the steering wheel on its own.

### The Core Loop: Sense → Think → Act

Every autonomous system follows this loop continuously:

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│  SENSE   │────→│  THINK   │────→│   ACT    │
│ (cameras,│     │ (AI      │     │ (steer,  │
│  LiDAR,  │     │  decides │     │  brake,  │
│  radar)  │     │  what to │     │  pick up │
│          │     │  do)     │     │  object) │
└──────────┘     └──────────┘     └──────────┘
      ↑                                │
      └────────────────────────────────┘
            (continuous feedback loop)
```

### Levels of Autonomy (SAE Levels for Vehicles)

| Level | Name | Who Drives? | What AI Does | Real Example |
|---|---|---|---|---|
| **0** | No Automation | Human does everything | Nothing | Old car with no electronic assists |
| **1** | Driver Assistance | Human drives, AI helps with ONE thing | Either steering OR speed (not both) | **Adaptive cruise control** — car maintains distance from car ahead |
| **2** | Partial Automation | Human drives, AI helps with TWO things | Controls steering AND speed. Human must watch road. | **Tesla Autopilot** — keeps in lane + maintains speed. But YOU must watch. |
| **3** | Conditional Automation | AI drives in specific conditions, human takes over when asked | Full driving in certain situations (e.g., highway). Alerts human to take over otherwise. | **Mercedes Drive Pilot** — drives on highways up to 60 km/h in traffic jams |
| **4** | High Automation | AI drives in specific areas, no human needed there | Full driving within a defined area (geofenced). No human backup needed in that area. | **Waymo** robotaxis in San Francisco — no driver, no steering wheel, carrying real passengers |
| **5** | Full Automation | AI drives everywhere, in all conditions | No steering wheel or pedals needed. Works everywhere. | **Does not exist yet** — nobody has achieved this |

### Case Study: Waymo Self-Driving Car

Waymo (Google's self-driving car company) is the most advanced autonomous vehicle system today:

```
SENSORS (Sense):
├── 29 cameras — 360° view around the car
├── 4 LiDAR sensors — create 3D map of surroundings (measures distance to every object)
├── 6 radar sensors — detect objects in fog/rain where cameras fail
├── GPS + IMU — precise location and motion tracking
└── Microphones — detect emergency vehicle sirens

AI BRAIN (Think):
├── Object Detection — identifies every pedestrian, car, cyclist, traffic light
├── Tracking — follows each object's movement over time
├── Prediction — guesses what each object will do next
│   (Will that pedestrian cross the road? Will that car change lanes?)
├── Path Planning — decides the safest route and trajectory
└── Decision Making — go/stop/turn/yield decisions 100+ times per second

CONTROL (Act):
├── Steering, acceleration, braking — all controlled by AI
├── Response time: ~100 milliseconds (humans: ~1500 milliseconds)
└── 20+ million miles driven autonomously (as of 2024)
```

**Why Level 5 doesn't exist yet:**
- Snow-covered roads with no lane markings
- Construction zones with human flaggers using hand signals
- Unpaved rural roads
- Extreme weather (heavy rain, fog, dust storms)
- Unusual situations (a mattress on the highway, a parade blocking the road)

These "edge cases" are what make full autonomy so hard — the real world has infinite variety.

### Beyond Cars: Other Autonomous Systems

| System | What It Does | Live Example |
|---|---|---|
| **Delivery drones** | Autonomous package delivery | **Amazon Prime Air** — drone delivers packages under 5 lbs within 60 minutes |
| **Warehouse robots** | Move and sort packages autonomously | **Amazon's Kiva robots** — 750,000 robots in warehouses moving shelves to workers |
| **Agricultural robots** | Autonomous farming (planting, spraying, harvesting) | **John Deere** autonomous tractors — farm without a human driver |
| **Surgical robots** | Assist surgeons with precision tasks | **Intuitive's da Vinci** — performs 1.2 million minimally invasive surgeries/year |
| **Underwater robots** | Explore oceans, inspect pipelines | **OceanOne** — humanoid diving robot that reaches depths humans can't |

---

## Key Terms Glossary (Session 2)

| Term | Simple Meaning |
|---|---|
| **Predictive AI** | AI that predicts future outcomes from past data |
| **Generative AI** | AI that creates new content (text, images, code, music) |
| **LLM (Large Language Model)** | A very large neural network trained on text data that can generate human-like text |
| **Hallucination** | When an AI confidently generates wrong or made-up information |
| **Prompt Engineering** | The skill of writing better instructions to get better AI outputs |
| **RLHF** | Reinforcement Learning from Human Feedback — training AI using human ratings |
| **Collaborative Filtering** | Recommending items based on what similar users liked |
| **Content-Based Filtering** | Recommending items based on similarity to items you already liked |
| **Cold Start Problem** | Can't recommend to new users (no history) or new items (no interactions) |
| **RAG** | Retrieval-Augmented Generation — LLM searches documents before answering to reduce hallucinations |
| **CNN** | Convolutional Neural Network — the core deep learning architecture for image understanding |
| **Sensor Fusion** | Combining data from multiple sensors (cameras, LiDAR, radar) into one unified view |
| **SAE Levels** | The 0-5 scale measuring how autonomous a vehicle is |

---

*End of Session 2*
