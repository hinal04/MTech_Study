# Session 2: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is Predictive AI? List and explain the four types of prediction tasks with examples.

**Answer:**
Predictive AI looks at past data to predict what will happen next. It answers: "Based on what happened before, what is likely to happen in the future?"

The four types of prediction tasks:

| Task | What It Predicts | Output Type | Example |
|---|---|---|---|
| **Classification** | Which category something belongs to | A label (Category A or B) | Is this email spam or not spam? Will this customer churn or stay? |
| **Regression** | A numerical value | A continuous number | What will this house sell for? (Rs 85 lakhs). What will tomorrow's temperature be? |
| **Time-series Forecasting** | What happens next in a sequence over time | Future values in a series | What will Reliance stock price be next week? How much electricity will Mumbai consume tomorrow? |
| **Anomaly Detection** | Whether a data point is unusual or normal | Normal or Anomalous | Is this credit card transaction fraudulent? Is this factory machine behaving abnormally? |

Indian examples:
- **Zomato/Swiggy** (Regression): Predicts delivery time using distance, traffic, weather, restaurant prep time
- **CRED** (Classification): Predicts if a user will default on credit card payment
- **Flipkart** (Time-series): Forecasts product demand to pre-stock warehouses
- **HDFC Bank** (Anomaly Detection): Flags unusual transactions as potential fraud in real-time

---

## Q2: Explain how a house price prediction model works step by step. What type of Predictive AI task is this?

**Answer:**
This is a **Regression** task because the output is a continuous number (price in rupees).

Step-by-step process:

**Step 1 — Collect Data:**
Gather past sales data with features like area (sq ft), number of bedrooms (BHK), location, age of building, floor number, and the actual sale price. Example: 10,000 past property sales in Mumbai.

**Step 2 — Train Model:**
Feed this data to an ML algorithm (e.g., XGBoost). The algorithm discovers patterns:
- Price increases roughly Rs 10,000 per sq ft
- Bandra adds Rs 30 lakh premium over Andheri
- Each year of age reduces price by Rs 2 lakh
- Higher floors command a 5% premium

**Step 3 — Predict:**
For a new house: 1100 sq ft, 2 BHK, Andheri, 3 years old — the model calculates all learned patterns together and predicts Rs 95 lakhs.

**Step 4 — Evaluate:**
Compare prediction with actual sale price. If the average error is less than 10%, the model is acceptable for deployment.

The model didn't memorise any rules — it discovered them from 10,000 examples of past data.

---

## Q3: What is Generative AI? How is it fundamentally different from Predictive AI?

**Answer:**
Generative AI creates new content — text, images, music, code, video — that didn't exist before. Instead of predicting a label or number from existing data, it generates entirely new things.

Key difference:
- **Predictive AI** is like a weather forecaster — tells you what will happen (classification, regression)
- **Generative AI** is like a painter — creates something new that never existed before

| Aspect | Predictive AI | Generative AI |
|---|---|---|
| **What it does** | Predicts an outcome from inputs | Creates new content |
| **Output** | A label, number, or category | Text, image, code, audio, video |
| **Example** | "This email is spam" (label) | "Write a professional email to the client" (new text) |
| **Key technology** | ML algorithms (XGBoost, Random Forest, etc.) | Large Language Models, Diffusion Models |

Types of Generative AI:

| Type | What It Creates | Examples |
|---|---|---|
| Text generation | Articles, emails, code, stories | ChatGPT, Claude, Gemini |
| Image generation | Photos, art, designs from text | DALL-E 3, Midjourney, Stable Diffusion |
| Code generation | Code in any programming language | GitHub Copilot, Kiro, Cursor |
| Music/Audio | Songs, sound effects, voice | Suno, ElevenLabs |
| Video generation | Video clips from text descriptions | Sora (OpenAI), Runway |

---

## Q4: How does a Large Language Model (LLM) generate text? Explain with an example.

**Answer:**
An LLM generates text by predicting one word (token) at a time. Given all the words so far, it predicts the most likely next word.

```
Input:  "The capital of India is"
                                    → Model predicts: "New"
Output: "The capital of India is New"
                                    → Model predicts: "Delhi"
Output: "The capital of India is New Delhi"
```

It works like a super-advanced autocomplete on your phone keyboard — but trained on trillions of words from the internet, so it can write essays, code, and stories.

Key details:
- The model doesn't "understand" like humans. It has learned statistical patterns of which words follow which
- It generates one token at a time (a token is roughly 3/4 of a word)
- The model has a **context window** — how much text it can "remember" at once (GPT-4o: 128K tokens, roughly 300 pages)
- Each word choice depends on all previous words, so it maintains coherence

This simple mechanism of "predict the next word" — repeated billions of times during training — is surprisingly powerful enough to write poetry, debug code, and explain quantum physics.

---

## Q5: Explain the following GenAI concepts: Hallucination, Temperature, and RLHF.

**Answer:**

**1. Hallucination:**
When an AI confidently generates wrong or made-up information. The output sounds correct and convincing, but it's factually false.

Example: You ask ChatGPT "How tall is the Eiffel Tower?" and it says "500 metres." It sounds confident, but the actual height is 330 metres. The model doesn't "know" facts — it generates statistically likely sequences, which sometimes happen to be wrong.

Why it's dangerous: In healthcare, legal, or financial contexts, a hallucinated answer could cause real harm.

**2. Temperature:**
A setting that controls how "creative" vs "safe" the model's output is.

- **Temperature 0:** Always picks the most likely word. Output is safe, repetitive, and predictable. Best for factual tasks (answering questions, coding).
- **Temperature 1:** More randomness in word choice. Output is creative, varied, but riskier. Best for creative tasks (writing stories, brainstorming).

Think of it as a dial: Low temperature = playing it safe (exam answers). High temperature = being creative (writing poetry).

**3. RLHF (Reinforcement Learning from Human Feedback):**
The process of training an AI to be helpful and safe using human ratings.

How it works:
1. The model generates multiple responses to a question
2. Human evaluators rate the responses ("this is helpful," "this is harmful," "this is better")
3. A reward model is trained on these human ratings
4. The main model is fine-tuned to maximise the reward model's score

This is how ChatGPT was trained to be helpful, harmless, and honest — not just by learning from internet text, but by learning from human preferences.

---

## Q6: Case Study — How was ChatGPT built? Describe the three main steps.

**Answer:**

**Step 1 — Pre-training (Learning Language):**
- Trained on massive internet text (books, websites, Wikipedia, code repositories)
- The model learns grammar, facts, reasoning patterns
- Like reading the entire internet and remembering patterns from it all
- Objective: Predict the next word, billions of times
- Cost: Estimated $100M+ in compute

**Step 2 — Fine-tuning with RLHF (Learning to Be Helpful):**
- Human evaluators rate model responses ("this answer is helpful," "this is harmful")
- A reward model is trained on these ratings
- ChatGPT is fine-tuned to maximise the reward model's score
- This step makes the model helpful, harmless, and honest
- Without RLHF, the raw model would sometimes generate toxic or unhelpful content

**Step 3 — Serving (Answering Users):**
- User sends a message (prompt)
- Model generates response token by token
- Safety filters check the response
- Response sent back to user — all in under 2 seconds

**Beyond the model — the system around ChatGPT:**
- Content safety filters (block harmful outputs)
- Rate limiting (prevent abuse)
- User session management (remember conversation context)
- A/B testing (test new model versions on small % of users)
- Usage monitoring and billing

Key takeaway: ChatGPT is not just a model — it's a complete AI system with data pipelines, safety layers, serving infrastructure, and monitoring.

---

## Q7: What are the three main approaches to building Recommender Systems? Compare them.

**Answer:**

| Approach | How It Works | Strength | Weakness | Example |
|---|---|---|---|---|
| **Collaborative Filtering** | "People similar to you liked X, so you'll probably like X too" | Discovers surprising recommendations you'd never search for | Cold start problem — can't recommend to new users (no history) | Netflix: "Users who watched Breaking Bad also watched Better Call Saul" |
| **Content-Based Filtering** | "You liked items with features A, B, C. Here are similar items" | Works for new items (just needs item features). No cold start for items. | Creates a filter bubble — recommends more of the same, no variety | Spotify: "You listen to Arijit Singh slow songs → here are similar Hindi slow songs" |
| **Hybrid** | Combines both collaborative and content-based approaches | Best accuracy. Avoids weaknesses of each approach. | More complex to build and maintain | Amazon, YouTube, Netflix — all use hybrid approaches |

Simple analogy:
- **Collaborative:** "Your friend Rahul has similar taste. He liked this book, so you probably will too."
- **Content-based:** "You like thriller novels by Indian authors. Here's another thriller by an Indian author."
- **Hybrid:** Uses both — "Rahul liked this book AND it matches your genre preferences."

---

## Q8: What is the Cold Start Problem in Recommender Systems? How do companies solve it?

**Answer:**
The cold start problem occurs when a recommender system cannot make good recommendations because it lacks data about a user or item.

Two types:

| Scenario | Problem | Solutions |
|---|---|---|
| **New user** (just signed up) | No history — don't know what they like | Ask preferences during signup. Use demographic info (age, location). Show popular items first. |
| **New item** (just added to catalog) | No one has interacted with it yet | Use content-based features (genre, description, tags). Boost new items in recommendations. |

**Netflix example:** When you create a new Netflix account, it asks you to pick 3 shows/movies you like. This "seed" data bootstraps the recommendation engine so it can start personalising immediately. Without this step, Netflix would show the same generic homepage to every new user.

**Amazon example:** For new products with zero reviews or purchases, Amazon uses content-based features (product category, description, price range) to place them in relevant recommendations. It also boosts visibility of new products to collect initial interaction data quickly.

**Spotify example:** New users are asked to select favourite artists during onboarding. Spotify also uses audio analysis (content features like tempo, energy, mood) of songs to recommend music to users who haven't built enough listening history.

---

## Q9: Case Study — How does Netflix's Recommendation System work? Why is it worth billions?

**Answer:**
Netflix says 80% of what users watch comes from recommendations, not search. Their recommendation system directly drives viewer engagement and retention.

**Data Collected:**
- What you watched and for how long
- What you rated
- What you searched for
- What time of day you watch
- What device you use (TV, phone, laptop)
- What you started but stopped watching (and at what point)
- Your browsing and scrolling behaviour

**Multiple Models (not just one):**
- "Top Picks for You" → personalised ranking model
- "Because You Watched Breaking Bad" → similar-items model
- "Trending Now" → popularity + personalisation model
- Row ordering on homepage → a separate model decides which ROW to show first
- Thumbnail selection → ML chooses which movie poster image appeals to YOU

**Fascinating detail — Personalised Thumbnails:**
The same movie shows different poster images to different users:
- If you watch romance → Netflix shows the romantic scene as thumbnail
- If you watch action → shows the action scene
- If you watch comedy → shows the funny moment
This increases the chance you'll click.

**Architecture:**
- Heavy model training happens offline (batch processing overnight)
- Real-time adjustments happen online (rerank based on current session behaviour)
- Models are retrained weekly with fresh data
- New models are A/B tested before full rollout

Netflix's recommendation system is estimated to save $1 billion per year by reducing churn (people cancelling subscriptions).

---

## Q10: What is Conversational AI? List the four types from simple to advanced.

**Answer:**
Conversational AI systems talk with users in natural language — understanding questions, maintaining context, and generating human-like responses.

| Type | How It Works | Smartness | Example |
|---|---|---|---|
| **Rule-based chatbot** | Follows a fixed script. Keyword matching. | Low — only handles pre-defined flows | IRCTC's "Press 1 for booking, Press 2 for cancellation" IVR system |
| **Intent-based bot** | Classifies user intent and extracts key entities | Medium — handles common questions well | HDFC Bank's Eva — understands "What's my balance?" (intent: check_balance) |
| **LLM-based assistant** | Uses a large language model for open-ended conversation | High — handles any topic | ChatGPT, Claude, Gemini |
| **RAG-based assistant** | LLM + searches a knowledge base for facts before answering | High + Accurate — grounded in real documents | Enterprise Q&A bots answering from company docs |

The progression is: fixed scripts → understanding intent → general intelligence → grounded intelligence.

---

## Q11: What is RAG (Retrieval-Augmented Generation)? How does it solve the hallucination problem?

**Answer:**
RAG combines an LLM with a document search system. Instead of relying on the LLM's memory (which can hallucinate), RAG forces the model to answer based on real, retrieved documents.

How RAG works — step by step:

```
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

**Without RAG:** LLM might hallucinate "Remote employees get 20 leaves" (made up from general training data).
**With RAG:** LLM cites the actual document with the correct answer.

Why RAG matters:
- Reduces hallucinations by grounding answers in real documents
- Keeps information up-to-date (search the latest documents, not the model's training data)
- Can cite sources (tell the user exactly which document the answer came from)

**Live Example:** Freshworks (Indian SaaS company) uses RAG-based bots for customer support. The bot searches product documentation and generates accurate answers. It resolved 60% of support tickets without human agents.

---

## Q12: What are the six key tasks in Computer Vision? Explain each with a real-world example.

**Answer:**

| Task | What It Does | Example |
|---|---|---|
| **Image Classification** | Labels the entire image with one category | Google Photos automatically tags photos: "beach," "wedding," "dog" |
| **Object Detection** | Finds and locates multiple objects with bounding boxes | Self-driving cars detecting pedestrians, other cars, and traffic signs simultaneously |
| **Semantic Segmentation** | Labels every single pixel in the image | Medical imaging: distinguishing tumour tissue from healthy tissue at pixel level |
| **Face Recognition** | Identifies who a person is from their face | iPhone Face ID unlocking your phone by recognising your face |
| **OCR (Optical Character Recognition)** | Reads text from images | Google Lens — point camera at a menu in Japanese → instant translation to English |
| **Image Generation** | Creates new images from text descriptions | DALL-E — "A photo of an astronaut riding a horse on Mars" → generates that image |

The complexity increases from top to bottom:
- Classification just says what's in the image (one label)
- Detection says what's in it AND where (bounding boxes)
- Segmentation labels every single pixel (most detailed)

---

## Q13: How do Convolutional Neural Networks (CNNs) work? Explain in simple terms.

**Answer:**
CNNs are the core technology behind most computer vision. They process images in layers, with each layer detecting increasingly complex things:

```
Layer 1: Simple features      → edges, lines, colours
Layer 2: Combinations         → corners, curves, textures
Layer 3: Parts of objects     → eyes, ears, wheels, windows
Layer 4: Whole objects        → "This is a cat" / "This is a car"
```

Think of it as: Letters → Words → Sentences → Meaning
Or in image terms: Pixels → Edges → Parts → Object

**Why "Convolutional"?**
The network slides a small window (called a filter or kernel) across the image, looking for patterns at each position. It's like running your finger across a page to find specific words — the sliding motion is the "convolution."

**Google Lens example:**
When you point Google Lens at a flower:
1. Camera captures the image (millions of pixels)
2. Layer 1 detects petal edges and colours
3. Layer 2 detects petal shapes and patterns
4. Layer 3 recognises flower structure
5. Layer 4 classifies: "Rose — Damask variety"
6. App displays result with Wikipedia info — all in under 1 second

The key power of CNNs is that they learn what features to look for automatically from data. You don't tell the CNN "look for petals" — it figures that out on its own during training.

---

## Q14: Explain the SAE Levels of vehicle autonomy (Level 0 to Level 5) with real-world examples.

**Answer:**

| Level | Name | Who Drives? | What AI Does | Real Example |
|---|---|---|---|---|
| **0** | No Automation | Human does everything | Nothing | Old car with no electronic assists |
| **1** | Driver Assistance | Human drives, AI helps with one thing | Either steering OR speed control (not both) | Adaptive cruise control — car maintains distance from car ahead |
| **2** | Partial Automation | Human drives, AI helps with two things | Controls steering AND speed. Human must watch the road. | Tesla Autopilot — keeps in lane + maintains speed. But YOU must watch. |
| **3** | Conditional Automation | AI drives in specific conditions, human takes over when asked | Full driving in certain situations (e.g., highway traffic jam) | Mercedes Drive Pilot — drives on highways up to 60 km/h in traffic |
| **4** | High Automation | AI drives in specific areas, no human needed | Full driving within a defined area (geofenced). No human backup needed. | Waymo robotaxis in San Francisco — no driver, no steering wheel |
| **5** | Full Automation | AI drives everywhere, all conditions | No steering wheel or pedals needed. Works everywhere. | Does NOT exist yet — nobody has achieved this |

Key points:
- Levels 0-2: Human is responsible for driving at all times
- Level 3: AI is responsible in specific conditions, but human must be ready to take over
- Levels 4-5: AI is fully responsible (Level 4 in specific areas, Level 5 everywhere)
- The jump from Level 2 to Level 3 is huge — it shifts legal responsibility from human to machine

---

## Q15: Case Study — How does Waymo's self-driving car work? Why can't we achieve Level 5 yet?

**Answer:**

**Waymo's system (Level 4):**

Sensors (Sense):
- 29 cameras — 360° view around the car
- 4 LiDAR sensors — create 3D map of surroundings (measures distance to every object using laser)
- 6 radar sensors — detect objects in fog/rain where cameras fail
- GPS + IMU — precise location and motion tracking
- Microphones — detect emergency vehicle sirens

AI Brain (Think):
- Object Detection — identifies every pedestrian, car, cyclist, traffic light
- Tracking — follows each object's movement over time
- Prediction — guesses what each object will do next (Will that pedestrian cross? Will that car change lanes?)
- Path Planning — decides the safest route and trajectory
- Decision Making — go/stop/turn/yield decisions 100+ times per second

Control (Act):
- Steering, acceleration, braking — all controlled by AI
- Response time: ~100 milliseconds (humans: ~1500 milliseconds)
- 20+ million miles driven autonomously as of 2024

**Why Level 5 doesn't exist yet:**
- Snow-covered roads with no visible lane markings
- Construction zones with human flaggers using hand signals
- Unpaved rural roads with no maps
- Extreme weather (heavy rain, fog, dust storms)
- Unusual situations (a mattress on the highway, a parade blocking the road, a cow sitting on an Indian road)

These "edge cases" make full autonomy incredibly hard — the real world has infinite variety. Waymo works in specific cities (geofenced areas) where it has detailed maps and favourable conditions. Achieving Level 5 means working everywhere — including chaotic Indian traffic, which is arguably the hardest driving environment in the world.

---

## Q16: What is Sensor Fusion? Why do autonomous vehicles need multiple types of sensors?

**Answer:**
Sensor fusion is the process of combining data from multiple sensors (cameras, LiDAR, radar, GPS) into one unified understanding of the environment.

Why multiple sensors are needed — each has strengths and weaknesses:

| Sensor | Strengths | Weaknesses |
|---|---|---|
| **Camera** | Reads signs, traffic lights, lane markings. Colour information. Cheap. | Fails in darkness, heavy rain, fog. Can't measure exact distance. |
| **LiDAR** | Precise 3D distance measurement. Works day and night. | Expensive. Fails in heavy rain/snow. Can't read text or colours. |
| **Radar** | Works in all weather (rain, fog, snow). Detects speed of objects. | Low resolution. Can't identify what an object is. |
| **GPS** | Knows the car's global position. | Not precise enough for lane-level decisions (error of 1-2 metres). |

By combining all four, the car gets a complete picture:
- Camera sees "that's a red traffic light"
- LiDAR measures "it's exactly 15 metres ahead"
- Radar confirms "it's stationary"
- GPS knows "we're at the intersection of MG Road and Park Street"

No single sensor can provide all this information alone. Fusion makes the system robust — if one sensor fails (camera blinded by sun), others compensate.

---

## Q17: Scenario — You're building a movie recommendation app for an Indian OTT platform. Which recommendation approach would you use and why?

**Answer:**
I would use a **Hybrid approach** combining Collaborative Filtering and Content-Based Filtering.

**Why not purely collaborative?**
- India has many regional languages (Hindi, Tamil, Telugu, Bengali, Malayalam, etc.)
- A pure collaborative filter might not work well for regional content — a Tamil user's preferences are very different from a Hindi user's
- Cold start problem: New users and new movies (many regional films are added daily) would have no data

**Why not purely content-based?**
- It would create a filter bubble — if a user watches one Telugu action movie, it keeps recommending only Telugu action movies
- Users wouldn't discover content from other languages or genres

**Hybrid approach — how it would work:**
1. **Content-based component:** Use movie features — language, genre, director, actors, runtime, ratings. For a new user who selects "Hindi" and "Action," immediately recommend popular Hindi action movies.
2. **Collaborative component:** "Users in your city who liked Pushpa also liked KGF and RRR" — discovers cross-language recommendations.
3. **Solve cold start:** Ask users to pick favourite genres and languages during signup. Use demographic data (age, location) for initial recommendations.
4. **Personalised thumbnails:** Like Netflix, show different poster images based on user preferences.

The hybrid approach gives the best of both worlds — handles India's language diversity while still discovering surprising recommendations.

---

## Q18: Compare Predictive AI and Generative AI across five dimensions.

**Answer:**

| Dimension | Predictive AI | Generative AI |
|---|---|---|
| **Purpose** | Predict outcomes from existing data | Create new content that didn't exist before |
| **Output** | Labels, numbers, categories | Text, images, code, audio, video |
| **Key question it answers** | "What will happen?" or "What category is this?" | "Can you create something new?" |
| **Examples** | Fraud detection, delivery ETA, weather forecast | ChatGPT, DALL-E, GitHub Copilot |
| **Data requirement** | Needs labelled historical data for training | Needs massive unlabelled data (internet text, images) |
| **Typical models** | XGBoost, Random Forest, Logistic Regression | LLMs (GPT, Claude), Diffusion Models |
| **Evaluation** | Measurable (accuracy, precision, recall) | Often subjective (is the essay good? is the image realistic?) |
| **Cost** | Generally lower compute cost | Very expensive to train (GPT-4: $100M+) |

Key insight: In practice, modern AI systems often combine both. For example, Swiggy uses Predictive AI to estimate delivery time and might use Generative AI (via an LLM API) to power its customer support chatbot.

---

## Q19: What are the key components of the system built AROUND ChatGPT (beyond the model itself)?

**Answer:**
ChatGPT is not just a model — it's a complete AI system. The GPT-4 model is the brain, but the system around it includes:

1. **Content Safety Filters:** Check every response for harmful, inappropriate, or dangerous content. Block outputs that violate safety policies.

2. **Rate Limiting:** Prevent abuse by limiting how many requests a user can send per minute/hour. Free users get fewer requests than paid users.

3. **User Session Management:** Maintain conversation context so the model remembers what you discussed earlier in the same chat. Store conversation history.

4. **A/B Testing:** Test new model versions on a small percentage of users before rolling out to everyone. Compare metrics like user satisfaction and response quality.

5. **Usage Monitoring and Billing:** Track token consumption per user. Calculate costs. Generate invoices for API users. Alert on unusual usage patterns.

6. **Serving Infrastructure:** Model deployed across thousands of GPUs globally. Load balancing across servers. Auto-scaling during peak hours. Response caching for common queries.

7. **Feedback Collection:** The thumbs up/down buttons on each response feed back into training data. User reports help identify model weaknesses.

This reinforces the lesson from Session 1 — the model is less than 5% of the system. The other 95% is what makes ChatGPT actually work for millions of users.

---

## Q20: Scenario — HDFC Bank wants to build an AI system to detect UPI fraud in real-time. Which type of Predictive AI task is this? What challenges would they face?

**Answer:**
This is primarily an **Anomaly Detection** task — the model must identify transactions that are unusual compared to normal behaviour. It also involves elements of **Classification** (fraud vs not-fraud).

**How it would work:**
The model analyses every UPI transaction in real-time — checking amount, sender, receiver, time, location, device, and frequency — and flags unusual patterns within milliseconds.

**Challenges:**

1. **Extreme class imbalance:** Only ~0.1% of transactions are fraudulent. The model sees 999 normal transactions for every 1 fraud. It could achieve 99.9% accuracy by simply predicting "not fraud" for everything — but that's useless.

2. **Real-time requirement:** India processes 10+ billion UPI transactions per month. The model must evaluate each transaction within milliseconds — before the transaction completes. Any delay frustrates users.

3. **Evolving fraud patterns (Data Drift):** Fraudsters constantly invent new methods — QR code scams, social engineering, fake UPI apps. The model must be retrained frequently to catch new patterns.

4. **False positives cost:** Blocking a legitimate transaction angers the customer. Too many false positives erode trust. But missing a fraud costs the bank money. There's a constant trade-off.

5. **Data privacy:** Transaction data is highly sensitive. The model and data must comply with RBI regulations. Sending transaction data to a third-party API (like OpenAI) is not an option — they'd need to build or fine-tune their own model.

6. **Cold start for new users:** A user who just opened an account has no transaction history. The model has nothing to compare against, making it harder to detect if their first few transactions are fraudulent.

---

*End of Session 2 Questions and Answers*
