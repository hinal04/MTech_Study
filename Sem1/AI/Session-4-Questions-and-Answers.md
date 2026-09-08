# Session 4: AI Ecosystem and Model Marketplaces — Questions & Answers

> 4 questions covering Session 4 topics.

---

### Q1. What is a foundation model? List 4 key characteristics.

**Answer:**

A **foundation model** is a large AI model trained on broad, diverse data at scale, designed to be adapted for many downstream tasks.

**4 key characteristics:**
1. **Massive scale:** Trained on billions of data points (text, images). Internet-scale data.
2. **General-purpose:** Not task-specific. Can be adapted to translation, summarisation, coding, Q&A, image generation.
3. **Transfer learning:** Knowledge transfers to downstream tasks even with limited task-specific data.
4. **Emergent abilities:** Larger models exhibit capabilities not explicitly trained for (chain-of-thought reasoning, in-context learning).

**Examples:** GPT-4 (OpenAI), Claude (Anthropic), LLaMA 3 (Meta), Gemini (Google), Stable Diffusion (Stability AI).

---

### Q2. Compare open-source models vs API-based models. When would you use each?

**Answer:**

| Aspect | Open-Source Models | API-Based Models |
|---|---|---|
| **Cost model** | Infrastructure hosting (your GPUs) | Per-request fees (pay per token) |
| **Data privacy** | Data stays on your servers | Data sent to third party |
| **Customisation** | Full — fine-tune, modify architecture | Limited — prompt engineering only |
| **Vendor lock-in** | None | High (provider can change API/pricing) |
| **Setup effort** | High (deploy, scale, manage) | Low (API call in minutes) |
| **Maintenance** | You handle updates, scaling | Provider handles everything |

**Use open-source when:** Data privacy is critical (healthcare, finance), need deep customisation, high-volume usage (API costs add up), want full control.

**Use API when:** Prototyping quickly, generic tasks (summarisation, translation), low volume, no ML infrastructure team.

---

### Q3. Explain the Build vs Buy decision framework for AI. Give 3 real-world examples.

**Answer:**

```
Is the task generic? → YES → Use API (GPT-4, Claude)
                     → NO  ↓
Is there a close open-source model? → YES → Fine-tune it
                                    → NO  ↓
Is it a core business differentiator? → YES → Build from scratch
                                      → NO  → Reconsider if you need ML
```

| Scenario | Decision | Why |
|---|---|---|
| Startup: customer support chatbot | **API (GPT-4)** | Fast to market, generic task, low volume. |
| Bank: fraud detection | **Build from scratch** | Proprietary data, regulatory requirements, core differentiator. |
| Hospital: radiology AI | **Fine-tune open-source** | Domain adaptation needed, patient data can't leave servers. |

---

### Q4. What is Hugging Face? Why is it called the "GitHub of ML"?

**Answer:**

**Hugging Face** is the central hub for open-source AI — a platform for sharing, discovering, and using ML models and datasets.

| Feature | What it provides |
|---|---|
| **Model Hub** | 500K+ pre-trained models with documentation (model cards). |
| **Datasets** | 100K+ datasets ready to use with preview and streaming. |
| **Transformers library** | Python library to load and use any model in a few lines. |
| **Spaces** | Host interactive model demos (Gradio, Streamlit). |
| **Inference API** | Run models via API without deploying infrastructure. |
| **PEFT/LoRA** | Tools for parameter-efficient fine-tuning. |

**"GitHub of ML"** because: just as GitHub hosts code, Hugging Face hosts models. You can browse, download, fork, contribute, and collaborate on ML models — with versioning, documentation, and community discussion.

---


---

*End of Session 4: AI Ecosystem and Model Marketplaces Questions & Answers*
