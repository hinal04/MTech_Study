# Session 2: Categories of AI Systems — Questions & Answers

> 8 questions covering Session 2 topics.

---

### Q1. Explain predictive AI. What are the four main predictive task types?

**Answer:**

**Predictive AI** analyses historical data to predict future outcomes: "Given what happened before, what happens next?"

| Task | Output | Example |
|---|---|---|
| **Classification** | Discrete label (yes/no, category) | Email spam detection, disease diagnosis. |
| **Regression** | Continuous number | House price prediction, revenue forecasting. |
| **Time-series forecasting** | Future values in a sequence | Stock prices, demand forecasting, weather. |
| **Anomaly detection** | Normal vs abnormal | Credit card fraud, network intrusion, equipment failure. |

A trained model takes new data and predicts the likely outcome based on patterns learned from historical examples.

---

### Q2. What is Generative AI? Explain hallucinations and temperature.

**Answer:**

**Generative AI** creates new content (text, images, music, code) that didn't exist before, by learning the underlying distribution of training data and sampling from it.

**Hallucinations:** When a generative model produces confident but factually incorrect content. The model generates *plausible* text, not necessarily *true* text. Example: An LLM confidently cites a research paper that doesn't exist.

**Temperature:** A parameter controlling randomness in generation.
- **Low temperature (0.1-0.3):** More deterministic, repetitive, "safe." Good for factual Q&A.
- **High temperature (0.7-1.0):** More creative, diverse, surprising. Good for brainstorming, creative writing.
- **Temperature = 0:** Always picks the most probable next token (greedy decoding).

---

### Q3. Explain how ChatGPT works at a high level. What is RLHF?

**Answer:**

**ChatGPT** is built in three stages:

1. **Pre-training:** GPT model is trained on massive internet text to predict the next token. Learns grammar, facts, reasoning patterns.
2. **Supervised Fine-tuning (SFT):** Human labellers write ideal responses to prompts. The model is fine-tuned on these examples.
3. **RLHF (Reinforcement Learning from Human Feedback):** Humans rank multiple model responses. A reward model is trained on these rankings. The GPT model is then fine-tuned to maximise the reward model's score — making responses more helpful, harmless, and honest.

**System beyond the model:** Content filtering, rate limiting, safety guardrails, session management, feedback collection, A/B testing, monitoring for misuse.

---

### Q4. Compare collaborative filtering, content-based filtering, and hybrid recommender systems.

**Answer:**

| Approach | How it works | Pros | Cons |
|---|---|---|---|
| **Collaborative** | "Users similar to you liked X" | Discovers unexpected items. No need to understand items. | Cold-start (new users/items). Popularity bias. |
| **Content-based** | "You liked items with features X, Y — here's similar" | No cold-start for items. Transparent. | Limited diversity (more of the same). |
| **Hybrid** | Combines both approaches | Best accuracy. Overcomes individual weaknesses. | More complex. |

**Netflix example:** Uses hybrid — collaborative filtering (similar users), content-based (genre/actor matching), contextual (time of day, device), even personalised thumbnails.

---

### Q5. What is RAG (Retrieval-Augmented Generation)? Why does it matter?

**Answer:**

**RAG** addresses the hallucination problem by grounding LLM responses in retrieved facts:

```
User query → Search knowledge base → Retrieve relevant documents
                                            ↓
                            LLM generates answer USING retrieved docs as context
                                            ↓
                                     Grounded response (can cite sources)
```

**Why it matters:**
- Reduces hallucinations — answers are based on actual documents, not just model memory.
- Keeps information current — update the knowledge base without retraining the model.
- Enables enterprise use — answer questions from company docs, policies, manuals.
- Provides citations — users can verify the answer against source documents.

---

### Q6. What are the SAE levels of vehicle autonomy? Give examples for Levels 2, 4, and 5.

**Answer:**

| Level | Name | Human role | Example |
|---|---|---|---|
| 0 | No Automation | Human does everything | Standard car |
| 1 | Driver Assistance | System controls ONE function | Adaptive cruise control |
| **2** | **Partial Automation** | System controls steering AND speed. Human monitors. | **Tesla Autopilot, GM Super Cruise** |
| 3 | Conditional Automation | System drives in specific conditions. Human takes over on request. | Mercedes Drive Pilot (highway) |
| **4** | **High Automation** | System drives in specific areas. No human needed there. | **Waymo robotaxis (geofenced)** |
| **5** | **Full Automation** | System drives everywhere. No steering wheel. | **Doesn't exist yet** |

---

### Q7. What systems does an autonomous vehicle combine? Why is it one of the most complex AI systems?

**Answer:**

An autonomous vehicle combines:
- **Computer vision** (cameras) — detect lanes, traffic lights, pedestrians, vehicles.
- **LiDAR** — 3D point cloud map of surroundings.
- **Sensor fusion** — combine camera + LiDAR + radar + GPS + IMU into unified world model.
- **Prediction** — predict what other road users will do next.
- **Path planning** — decide route and trajectory.
- **Control** — execute trajectory (steering, throttle, braking).

**Why most complex:** Must operate in real-time (<100ms), with extreme reliability (one failure = potential fatality), in completely unpredictable environments (weather, construction, erratic drivers), fusing data from 6+ sensor types simultaneously.

---

### Q8. Compare predictive AI and generative AI across 5 dimensions.

**Answer:**

| Dimension | Predictive AI | Generative AI |
|---|---|---|
| **Goal** | Predict an outcome (label, number) | Create new content (text, images) |
| **Output** | Classification label, regression value, probability | Text, images, code, music, video |
| **Training** | Supervised (labelled examples) | Self-supervised (predict next token) + RLHF |
| **Evaluation** | Accuracy, precision, recall, F1, AUC | Human evaluation, perplexity, BLEU, hallucination rate |
| **Example** | "Will this customer churn?" (yes/no) | "Write a marketing email for this customer" (new text) |

---


---

*End of Session 2: Categories of AI Systems Questions & Answers*
