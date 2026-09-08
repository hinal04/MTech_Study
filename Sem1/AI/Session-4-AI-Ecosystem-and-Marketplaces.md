# Session 4: AI Ecosystem and Model Marketplaces

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** Hugging Face, OpenAI APIs, Cloud AI Services
>
> **Contact Session:** 4 (Module 1: Foundations of AI Systems)

---

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

---

*End of Session 4*
