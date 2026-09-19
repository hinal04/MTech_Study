# Session 4: AI Ecosystem and Model Marketplaces

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** Hugging Face, OpenAI APIs, Cloud AI Services
>
> **Contact Session:** 4 (Module 1: Foundations of AI Systems)

---

## Table of Contents

- [4.1 Foundation Models](#41-foundation-models)
  - [The Enterprise Model-Selection Question](#the-enterprise-model-selection-question)
- [4.2 Open-Source Models](#42-open-source-models)
  - [License Terms](#understanding-license-terms--important-for-enterprise-use)
- [4.3 APIs and Cloud AI Services](#43-apis-and-cloud-ai-services)
  - [Cloud Model Marketplaces (Bedrock, Model Garden, Azure, NGC)](#cloud-model-marketplaces--where-enterprises-get-models)
  - [Inference-as-a-Service](#inference-as-a-service--a-new-category)
- [4.4 Model Hubs](#44-model-hubs)
  - [Small and Task-Specific Models](#small-and-task-specific-models--you-dont-always-need-gpt-4)
- [4.5 Evaluating Models — Benchmarks and Quality](#45-evaluating-models--benchmarks-and-quality)
  - [Standard Benchmarks](#standard-benchmarks--how-models-are-compared)
  - [Quality Scorecard](#quality-scorecard--what-to-actually-measure)
- [4.6 Architecture Patterns](#46-architecture-patterns)
  - [Gateway/Router Pattern](#gatewayrouter-pattern--dont-call-models-directly)
- [4.7 Build vs Buy Decisions](#47-build-vs-buy-decisions)
  - [Enterprise Procurement Scorecard](#enterprise-procurement-scorecard--how-companies-actually-decide)
  - [Enterprise Model Lifecycle](#enterprise-model-lifecycle--models-are-not-permanent)

---

## 4.1 Foundation Models

### What is a Foundation Model? (Simple)

A **foundation model** is a very large AI model trained on massive amounts of data that can be used for many different tasks. It's like a well-educated person who studied everything — you can ask them to write, translate, summarise, code, or answer questions, and they can do it all reasonably well.

> **Analogy:** Think of a foundation model like a Swiss Army knife — it wasn't designed for just one task. It can cut, open bottles, tighten screws, and more. Similarly, GPT-4 can write essays, translate languages, write code, answer questions, and summarise documents — all with the same model.

The term "foundation model" was coined by Stanford University in 2021 because these models serve as the **foundation** on which many applications are built.

### Key Characteristics — Explained Simply

| Characteristic | What It Means | Simple Example |
|---|---|---|
| **Trained on massive data** | Learned from billions of text documents, images, or code files from the internet | GPT-4 was trained on text equivalent to millions of books |
| **General-purpose** | Not built for one specific task — can handle many different tasks | Same GPT-4 model can write poetry, debug code, and explain physics |
| **Transfer learning** | Knowledge from training can be applied to new tasks it wasn't specifically trained for | GPT-4 was never specifically taught Indian tax law, but it can answer tax questions because it learned general legal reasoning |
| **Emergent abilities** | Large models suddenly show abilities that weren't explicitly taught | GPT-4 can do chain-of-thought reasoning (thinking step by step) — nobody programmed this |
| **Very expensive to train** | Training costs millions of dollars (compute, data, engineering) | GPT-4 training cost: estimated $100M+. Only companies like OpenAI, Google, Meta can afford this. |
| **Accessible to everyone** | Regular developers can use these via APIs or fine-tuning, without training from scratch | You pay OpenAI a few rupees per API call instead of spending $100M to train your own |

### Notable Foundation Models — Know These for Exams

| Model | Company | What It Does | Key Fact |
|---|---|---|---|
| **GPT-4 / GPT-4o** | OpenAI | Text + images + audio | Most capable general-purpose model. Powers ChatGPT. |
| **Claude 3.5 / Claude 4** | Anthropic | Text + images | Focus on safety. 200K token context (can read ~500 pages at once). |
| **Gemini** | Google DeepMind | Text + images + audio + video | Natively multimodal — understands all types of media together. |
| **LLaMA 3** | Meta | Text (open-source) | Free to download and modify. Community creates thousands of fine-tuned versions. |
| **Mistral** | Mistral AI | Text (open-source) | French company. Very efficient — strong performance with smaller model size. |
| **Stable Diffusion** | Stability AI | Image generation (open-source) | Free image generation. Anyone can run it on their own GPU. |
| **DALL-E 3** | OpenAI | Image generation | Best text-to-image quality. Integrated into ChatGPT. |
| **Whisper** | OpenAI | Speech-to-text (open-source) | Understands 100+ languages. Free to use. |

> **Live Example:** When you use ChatGPT, you're using GPT-4o (a foundation model). When you use Google's AI in Search, you're using Gemini. When Zomato's AI chatbot answers your query, it likely uses an API to one of these foundation models behind the scenes.

### The Enterprise AI Market as a Multi-Layer Ecosystem

The enterprise AI market has become a multi-layer ecosystem. Each layer builds on the one below it:

```
Foundation Model Providers (OpenAI, Anthropic, Meta)
        ↓
Cloud Platforms (AWS, Azure, GCP)
        ↓
Model Hubs (Hugging Face, NGC)
        ↓
Application Builders (enterprises)
```

No enterprise builds from scratch anymore — they pick a layer to enter and assemble from what's above and below.

### The Enterprise Model-Selection Question

In 2023, the question was simply: "Which AI model should we use?" In 2025, the question has become much harder:

> **The real question is not "which model?" but "which model, deployed where, at what cost, with what governance, and what exit strategy?"**

### Where Enterprises Discover Models

Enterprises discover models through several channels, each with different tradeoffs:

| Channel | Example | Best For |
|---|---|---|
| **Hugging Face Hub** | 500K+ open-source models | Browsing, experimenting, community models |
| **AWS Bedrock** | Managed API for Claude, LLaMA, Mistral | AWS-centric enterprises, managed deployments |
| **Google Model Garden** | Gemini + open-source on Vertex AI | Google Cloud users, multimodal tasks |
| **Azure Model Catalog** | GPT-4 + open-source on Azure AI Studio | Microsoft-centric enterprises, GPT-4 with compliance |
| **NVIDIA NGC** | GPU-optimized models and containers | Self-hosted deployments on NVIDIA GPUs |

When an enterprise picks an AI model, they must think about **seven factors** together — not just capability:

| Factor | What It Means | Example |
|---|---|---|
| **Capability** | Can the model actually do what you need? | GPT-4 is great at writing, but a specialised medical model may be better for diagnosing X-rays |
| **Cost** | How much per request, per month, per year? | GPT-4 costs ~₹0.80/1K tokens. A fine-tuned Mistral on your own GPU might cost ₹0.05/1K tokens |
| **Latency** | How fast does it respond? | A chatbot needs < 500ms. A batch report generator can tolerate 10 seconds. |
| **Privacy** | Where does your data go? | A bank can't send customer data to OpenAI's servers. They need a self-hosted or private deployment. |
| **License** | Are you legally allowed to use it this way? | Some open-source models restrict commercial use above a certain user count |
| **Support** | Who helps when things break? | OpenAI offers enterprise support. A random Hugging Face model has community support only. |
| **Lock-in** | How hard is it to switch later? | If you build everything around GPT-4's API format, switching to Claude means rewriting code |

> **Analogy:** Choosing an AI model is like choosing a car for a company fleet. You don't just ask "which car is fastest?" You ask: What's the fuel cost? Does it fit our roads? Can we service it locally? What's the insurance? Can we switch brands later?

> **Key Exam Point:** Enterprise AI is not just a technology decision — it's a business decision that involves engineering, legal, finance, and compliance teams.

---

## 4.2 Open-Source Models

### What Does "Open-Source" Mean Here?

An **open-source model** is one where the model files (called "weights") are freely available for anyone to download, inspect, modify, and run on their own computers.

> **Analogy:** A proprietary model (like GPT-4) is like a restaurant — you pay for each meal, you can't see the recipe. An open-source model (like LLaMA) is like a recipe shared online — you can cook it yourself at home, modify it, add your own spices, for free.

### Why Open-Source Models Matter

| Benefit | What It Means | Why You'd Care |
|---|---|---|
| **No API costs** | Run on your own servers — no per-request charges | A startup making 1 million API calls/month to GPT-4 pays ~$30,000. Same task with open-source model on their own GPU: ~$2,000/month. |
| **Data privacy** | Your data never leaves your servers | A hospital can run a medical AI without sending patient data to OpenAI's servers (which would violate HIPAA). |
| **Full customisation** | You can fine-tune, modify, or strip down the model | Fine-tune LLaMA on Indian legal documents → create an Indian law AI assistant that GPT-4 can't match for this specific task. |
| **No vendor lock-in** | Not dependent on one company's pricing or availability | If OpenAI raises prices 5x tomorrow, companies using GPT-4 are stuck. Companies using open-source can switch to Mistral or LLaMA. |
| **Community innovation** | Thousands of developers improve and create variants | Hugging Face has 500K+ open-source models — fine-tuned for every domain imaginable. |

### Live Example: How an Indian Startup Uses Open-Source

**Krutrim** (by Ola's Bhavish Aggarwal) built India's first multilingual LLM:
- Started with open-source LLaMA as the base
- Fine-tuned it on 22 Indian languages (Hindi, Tamil, Telugu, Bengali, etc.)
- Result: An AI that understands Indian languages far better than GPT-4
- Didn't need $100M — used transfer learning from LLaMA's English knowledge

This is the power of open-source: start from a strong foundation, adapt to your specific need.

### Model Families on the Enterprise Shortlist

The model families on a typical enterprise shortlist fall into two camps:

| Category | Models | Key Trait |
|---|---|---|
| **Proprietary** | GPT-4, Claude, Gemini | Higher capability, pay-per-use, data leaves your network |
| **Open** | LLaMA, Mistral, Qwen, DeepSeek | Free weights, self-hostable, full control |

Selection depends on the specific task, privacy requirements, and cost tolerance. Most enterprises end up using a mix of both.

### GPT vs Claude vs Gemini — Proprietary Model Comparison

| Model | Strengths | Best For |
|---|---|---|
| **GPT-4 / GPT-4o** | Broadest capability, largest ecosystem, best tool use | General-purpose tasks, code generation, API integrations |
| **Claude 3.5 / Claude 4** | Safety-focused, 200K token context window, strong reasoning | Long document analysis, regulated industries, safety-critical apps |
| **Gemini** | Natively multimodal (text + image + audio + video), deep Google integration | Multimodal tasks, Google Cloud users, search-related applications |

> **Key takeaway:** GPT-4 is the safe default. Claude excels when you need long context or extra safety. Gemini wins when you need multimodal input or are already on Google Cloud.

### LLaMA vs Mistral vs Qwen vs DeepSeek — Open Model Comparison

| Model | Parameters | License | Strength |
|---|---|---|---|
| **LLaMA 3** | 8B, 70B, 405B | Llama License (free < 700M MAU) | Largest open model family, huge community, many fine-tuned variants |
| **Mistral** | 7B, 8x7B (Mixtral) | Apache 2.0 | Very efficient — strong performance relative to size, no usage restrictions |
| **Qwen** | 7B, 14B, 72B | Apache 2.0 | Strong multilingual support (especially CJK languages), good coding ability |
| **DeepSeek** | 7B, 67B, MoE variants | Permissive | Excellent at math and code, competitive with much larger models |

> **Key takeaway:** LLaMA has the biggest ecosystem. Mistral is the efficiency champion. Qwen is strong for Asian languages. DeepSeek punches above its weight on technical tasks.

### Understanding License Terms — Important for Enterprise Use

When you download an open-source model, it comes with a **license** — a legal document that tells you what you can and cannot do with it. Not all "open-source" models are equally free.

| License | How Free? | What You Can Do | Who Uses It | Example Models |
|---|---|---|---|---|
| **Apache 2.0** | Most permissive | Use for anything — commercial, modify, redistribute, no restrictions | Companies that want maximum adoption | Mistral 7B, some LLaMA variants, Whisper |
| **Llama License** | Mostly free | Free for most uses, but restricted if you have **>700 million monthly active users** | Meta (to prevent big competitors from free-riding) | LLaMA 2, LLaMA 3 |
| **Commercial License** | Paid | Must purchase a license for commercial use | Companies monetizing their models | Some specialised medical/legal AI models |
| **GPL** | Free but viral | Must share your modifications publicly if you distribute | Academic/research community | Some older ML frameworks |

**Four questions to ask before using any model:**

1. **Can you use it commercially?** (Apache 2.0: yes. Some research-only models: no.)
2. **Can you modify it?** (Most open-source: yes. Some proprietary: no.)
3. **Must you share your modifications?** (Apache 2.0: no. GPL: yes.)
4. **Is there a usage cap?** (Llama License: restricted above 700M MAU. Apache 2.0: no cap.)

> **Real Example:** A startup building a chatbot with 10,000 users can freely use LLaMA 3 (way under the 700M limit). But if Meta's competitor like Google wanted to use LLaMA 3 in Google Search (billions of users), they would need a special agreement with Meta.

> **Exam Tip:** If a question asks "which license is most suitable for commercial use with no restrictions?" — the answer is **Apache 2.0**.

### Open-Weight Variants — "LLaMA" Is Not One Thing

"LLaMA" is not a procurement specification — the same base model has many variants:

| Variant Type | What It Is | Example |
|---|---|---|
| **Original release** | Meta's base model as published | LLaMA 3 70B base |
| **Fine-tuned versions** | Adapted for specific tasks | Code Llama (coding), Llama-Chat (conversation) |
| **Quantized versions** | Compressed for smaller hardware | GGUF format (for laptops), GPTQ format (for smaller GPUs) |
| **Distilled versions** | Smaller models trained to mimic the large one | Community distillations on Hugging Face |

When someone says "we use LLaMA," always ask: which variant, which quantization, from which source?

### Same Model, Different Channels

The same open model (e.g., LLaMA 3) is available through multiple channels, each with different tradeoffs:

| Channel | Example | Pricing | SLA | Compliance |
|---|---|---|---|---|
| **Direct download** | Hugging Face | Free (you pay for your own GPU) | None — you manage everything | Full control |
| **Managed API** | AWS Bedrock, Together AI | Per-token fee | Provider SLA (99.9% uptime) | Provider's compliance certs |
| **Cloud-hosted** | SageMaker, Vertex AI | Compute + hosting fee | Cloud provider SLA | Cloud compliance (SOC2, ISO, etc.) |

> **Key insight:** The model is the same, but the channel changes the pricing, SLAs, and compliance guarantees. Pick the channel that matches your operational needs, not just the model that matches your accuracy needs.

### Key Open-Source Platforms

| Platform | What It Is | Think of It As... |
|---|---|---|
| **Hugging Face** | The "GitHub of AI" — 500K+ models, 100K+ datasets, tools, and demos | App Store for AI models — browse, download, try before you install |
| **GitHub** | Source code for models, training scripts, and tools | Where the actual code lives |
| **Kaggle** | Datasets, competitions, and notebooks for practicing ML | A playground for learning ML with real data |
| **Ollama** | Run open-source LLMs on your laptop with one command | Docker for AI models — `ollama run llama3` and it just works |

---

## 4.3 APIs and Cloud AI Services

### What is an AI API? (Simple)

An **API** (Application Programming Interface) lets you use someone else's AI model by sending a request and getting back a response — like ordering food delivery instead of cooking yourself.

> **Analogy:** You don't need to own a cow to drink milk. You buy milk from a dairy. Similarly, you don't need to train your own AI model — you call an API and get predictions back.

### Types of AI APIs

| Category | What You Get | Cost Model | Live Examples |
|---|---|---|---|
| **Text generation APIs** | Send a prompt, get generated text back | Pay per token (word) | **OpenAI API** (GPT-4): ~Rs 0.80 per 1000 input tokens. **Anthropic API** (Claude). **Google Gemini API**. |
| **Embedding APIs** | Convert text/images into numbers (vectors) for search and similarity | Pay per token | **OpenAI Embeddings**, **Cohere Embed**. Used to build semantic search ("find documents similar to this query"). |
| **Image APIs** | Generate or analyse images | Pay per image | **DALL-E 3**: ~Rs 3 per image. **Google Vision AI**: detects objects, faces, text in images. |
| **Speech APIs** | Convert speech to text or text to speech | Pay per minute/character | **OpenAI Whisper API**: ~Rs 0.50/minute. **Google Cloud Speech**. **AWS Transcribe**. |
| **Cloud ML Platforms** | Full ML lifecycle — train, deploy, monitor | Pay for compute + storage | **AWS SageMaker**, **Google Vertex AI**, **Azure ML**. |

### Understanding API Pricing — Real Numbers

| Use Case | API Used | Monthly Cost (1M requests) | Self-Hosted Cost |
|---|---|---|---|
| Customer support chatbot | GPT-4o API | ~Rs 2,50,000/month | ~Rs 40,000/month (open-source on GPU) |
| Image classification | Google Vision AI | ~Rs 1,25,000/month | ~Rs 15,000/month (own model on GPU) |
| Speech transcription | Whisper API | ~Rs 5,000/month (for 10K min) | ~Rs 8,000/month (self-hosted Whisper) |

> **Key insight:** API is cheaper for low volume (< 100K requests/month). Self-hosted is cheaper for high volume (> 500K requests/month). The crossover point depends on the specific task.

### Cloud Model Marketplaces — Where Enterprises Get Models

Instead of going directly to OpenAI or Hugging Face, large companies use **cloud model marketplaces** — platforms offered by AWS, Google, and Microsoft that let you access many models from one place.

> **Analogy:** Think of it like a shopping mall vs individual shops. You could visit Nike, Adidas, and Puma stores separately — or you could go to one mall that has all of them. Cloud model marketplaces put all the models in one place with unified billing, security, and deployment.

#### AWS Bedrock

**AWS Bedrock** is Amazon's fully managed service that gives you access to multiple foundation models through a single API.

| Aspect | Details |
|---|---|
| **What it is** | A managed service to use foundation models (Claude, LLaMA, Mistral, Stable Diffusion) via API — all through AWS |
| **Key models available** | Anthropic Claude, Meta LLaMA 3, Mistral, Stability AI, Amazon Titan |
| **Why it matters** | Changes the architecture from `App → Model` to `App → Bedrock → Model`. Bedrock acts as an **abstraction layer**. |
| **Biggest benefit** | **Switch models without changing your application code.** If Claude gets expensive, switch to LLaMA in Bedrock — your app doesn't change. |
| **Bedrock Marketplace** | Extends the catalog with third-party and specialised models (like medical, legal, or finance-specific models) |

```
Traditional approach:      App → OpenAI API (locked in)

Bedrock approach:          App → AWS Bedrock → Claude
                                             → LLaMA
                                             → Mistral
                                             → (swap anytime)
```

> **Real Example:** An Indian insurance company uses Bedrock with Claude for policy Q&A. When Meta releases a better LLaMA version, they can switch in minutes without touching their application code. They also keep all data within their AWS region (Mumbai) for compliance.

#### Google Model Garden

**Google Model Garden** is Google's model discovery and deployment platform, built inside Vertex AI.

| Aspect | Details |
|---|---|
| **What it is** | A catalog of 100+ models inside Google Cloud's Vertex AI platform |
| **Models available** | Google's own (Gemini, PaLM) + open-source (LLaMA, Mistral, Falcon) |
| **Key features** | One-click deployment, built-in fine-tuning, model evaluation tools |
| **Best for** | Companies already using Google Cloud |

> **Think of it as:** Google's version of Bedrock — browse models, deploy them, fine-tune them, all within Google Cloud.

#### Azure Model Catalog

**Azure Model Catalog** is Microsoft's model marketplace inside Azure AI Studio.

| Aspect | Details |
|---|---|
| **What it is** | A catalog of models within Microsoft Azure, including OpenAI's models |
| **Models available** | OpenAI models (GPT-4, DALL-E) + open-source models (LLaMA, Mistral, Phi) |
| **Unique advantage** | **Only place to get OpenAI models with enterprise Azure compliance** (data residency, private networking) |
| **Best for** | Microsoft/Azure-centric enterprises, companies needing GPT-4 with enterprise security |

> **Why Azure stands out:** If you want GPT-4 but also need your data to stay within India and never leave your private network, Azure OpenAI Service is the only option. Direct OpenAI API sends data to OpenAI's servers.

#### NVIDIA NGC (NVIDIA GPU Cloud)

**NVIDIA NGC** is NVIDIA's catalog of GPU-optimized models, containers, and deployment tools.

| Aspect | Details |
|---|---|
| **What it is** | A catalog of models and tools **optimized specifically for NVIDIA GPUs** |
| **What it offers** | Pre-optimized containers, model weights, frameworks (TensorRT, Triton Inference Server) |
| **When to use** | When your enterprise operates its own GPU infrastructure (data centres with NVIDIA GPUs) |
| **Key benefit** | Models from NGC run **2-5x faster** on NVIDIA GPUs compared to unoptimized versions |

> **Analogy:** NGC is like buying tyres specifically made for your car brand — they fit perfectly and perform better than generic tyres. NGC models are specifically tuned for NVIDIA hardware.

> **Exam Tip:** Remember the four major cloud model marketplaces: **Bedrock** (AWS), **Model Garden** (Google), **Model Catalog** (Azure), **NGC** (NVIDIA). Each serves enterprises already in that cloud ecosystem.

### Inference-as-a-Service — A New Category

A new type of provider has emerged: companies that **only do inference** (running models), not training. They host popular open-source models with heavily optimized infrastructure, and you pay per token.

| Provider | What's Special | Key Benefit |
|---|---|---|
| **Fireworks AI** | Optimized inference for open-source models | Fast, cheap inference for LLaMA, Mistral, etc. |
| **Together AI** | Hosts 100+ open-source models with one API | Easy switching between models |
| **Groq** | Uses custom **LPU (Language Processing Unit)** hardware instead of GPUs | **Ultra-fast inference** — 10x faster than GPU-based providers |
| **Replicate** | Run any model via API, pay per second | Great for image/video models |

> **Why this matters:** You get the benefits of open-source models (cheaper, private) **without** managing GPU servers yourself. It's the middle ground between "pay OpenAI per token" and "buy your own GPUs."

```
Spectrum of options:

Full API (OpenAI)          → Easiest, most expensive, least control
Inference-as-a-Service     → Easy, cheaper, open-source models
Self-hosted (your GPUs)    → Hardest, cheapest at scale, full control
```

> **Real Example:** A startup uses Together AI to serve LLaMA 3 to their users. They pay ~₹0.10 per 1K tokens (vs ₹0.80 for GPT-4), get similar quality for their use case, and don't need to rent or manage any GPUs.

### API Model vs Open Model — Direct Comparison

| Aspect | API Model (GPT-4, Claude) | Open Model (LLaMA, Mistral) |
|---|---|---|
| **Control** | Provider controls updates, downtime, and deprecation | You control everything — version, deployment, timing |
| **Cost** | Pay per token; expensive at high volume | Free weights; you pay for compute |
| **Privacy** | Data sent to provider's servers | Data stays on your servers |
| **Customization** | Prompt engineering only (no weight access) | Full fine-tuning, quantization, distillation |
| **Maintenance** | Provider handles updates and scaling | You handle ops, patching, and scaling |
| **Time to start** | Minutes (just call the API) | Days to weeks (setup infrastructure) |
| **Best for** | Prototyping, low volume, non-sensitive data | Production at scale, sensitive data, custom needs |

> **Rule of thumb:** Start with an API model to prove the idea works. Switch to an open model when volume grows, privacy matters, or you need deep customization.

### Live Example: Building a Customer Support Bot

**Option A: API approach (quick & easy)**
```python
import openai

response = openai.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful customer support agent for Flipkart."},
        {"role": "user", "content": "Where is my order #12345?"}
    ]
)
print(response.choices[0].message.content)
# "Let me check your order #12345. It was shipped on Sep 5 
#  and is expected to arrive by Sep 8. Here's your tracking link..."
```
Time to build: 1 day. Cost: Pay per API call.

**Option B: Self-hosted approach (more control)**
- Download LLaMA 3 from Hugging Face
- Fine-tune on Flipkart's customer support data (past 1 million conversations)
- Deploy on your own GPU servers
- Time to build: 2-4 weeks. Cost: GPU server rental.

---

## 4.4 Model Hubs

### What is a Model Hub? (Simple)

A **model hub** is an online platform where AI models are shared, documented, and versioned — like an app store, but for AI models instead of apps.

### Hugging Face Hub — The Standard

Hugging Face is the **most important platform** in the AI ecosystem. Every ML engineer uses it.

| Feature | What It Does | Why It Matters |
|---|---|---|
| **Model Cards** | Documentation for each model — what it does, how to use it, limitations, performance scores | You know exactly what you're getting before downloading |
| **500K+ Models** | Pre-trained models for every task — text, image, audio, video | Whatever you need, someone probably already built it |
| **100K+ Datasets** | Ready-to-use datasets with preview and streaming | Don't need to collect your own data for many tasks |
| **Spaces** | Host interactive demos of models (try before download) | Test a model in your browser before committing to use it |
| **Inference API** | Run any model via API without setting up your own server | `curl` command → get predictions. No GPU needed. |
| **Transformers Library** | Python library to use any model in 3 lines of code | The most popular ML library in the world |

### Using Hugging Face — It's This Easy

```python
# Sentiment Analysis in 3 lines
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
result = classifier("I love this product! Best purchase ever.")
# Output: [{'label': 'POSITIVE', 'score': 0.9998}]
```

```python
# Text Summarisation in 3 lines
summariser = pipeline("summarization")
result = summariser("Long article text here...", max_length=50)
# Output: [{'summary_text': 'Concise summary of the article.'}]
```

```python
# Image Classification in 3 lines
classifier = pipeline("image-classification")
result = classifier("photo_of_cat.jpg")
# Output: [{'label': 'Egyptian cat', 'score': 0.95}]
```

> **Live Example:** When Flipkart analyses customer reviews to identify common complaints, they can use Hugging Face's sentiment analysis pipeline to classify millions of reviews in hours — without training any model themselves.

### Small and Task-Specific Models — You Don't Always Need GPT-4

A common mistake is thinking you need a frontier model (GPT-4, Claude, Gemini) for every AI task. In reality, **small, task-specific models** are often better.

| Model Size | Parameter Count | Good For | Example |
|---|---|---|---|
| **Tiny** | 1-3B parameters | Simple classification, entity extraction, on-device AI | Phi-3 Mini, TinyLlama |
| **Small** | 3-7B parameters | Summarisation, Q&A, chatbots for specific domains | Mistral 7B, LLaMA 3 8B |
| **Medium** | 7-30B parameters | Complex reasoning, code generation, multi-step tasks | LLaMA 3 70B, Mixtral |
| **Large (Frontier)** | 100B+ parameters | The hardest tasks — novel research, complex creative work | GPT-4, Claude 3.5, Gemini Ultra |

**Why use small models?**

| Benefit | Explanation | Numbers |
|---|---|---|
| **Cheaper** | Fewer parameters = less compute per request | GPT-4: ₹0.80/1K tokens. Mistral 7B (self-hosted): ₹0.02/1K tokens |
| **Faster** | Smaller model = quicker response | GPT-4: ~2 seconds. Mistral 7B: ~200ms |
| **On-device** | Can run on phones, laptops, edge devices (no internet needed) | Phi-3 Mini runs on an iPhone |
| **Easier to fine-tune** | Less data and compute needed for fine-tuning | Fine-tune BERT: 1 GPU, 2 hours. Fine-tune GPT-4: not even possible. |

> **Key Example:** A sentiment classifier doesn't need GPT-4. A fine-tuned **BERT** (110 million parameters) works **better** at sentiment classification and is **100x cheaper** than calling GPT-4 for the same task. Why? Because BERT was fine-tuned specifically for that task, while GPT-4 is a general-purpose model doing that task as one of thousands.

> **Analogy:** You don't drive a truck to buy groceries. A scooter is faster, cheaper, and more efficient for short trips. Similarly, you don't need a 1 trillion parameter model to classify emails as "spam" or "not spam."

> **Rule of Thumb:** Start with the smallest model that works. Only move to a bigger model if the small one can't handle the task.

---

## 4.5 Evaluating Models — Benchmarks and Quality

### Standard Benchmarks — How Models Are Compared

When someone claims "our model is better than GPT-4," they're usually talking about **benchmark scores**. Benchmarks are standardised tests that measure different capabilities.

| Benchmark | What It Measures | How It Works | Example Score |
|---|---|---|---|
| **MMLU** (Massive Multitask Language Understanding) | General knowledge across **57 subjects** (math, history, law, medicine, etc.) | Multiple-choice questions from university exams | GPT-4: ~86%. LLaMA 3 70B: ~79%. |
| **HumanEval** | **Code generation** ability | Given a function description, model must write working Python code | GPT-4: ~67%. Claude 3.5: ~64%. |
| **MT-Bench** | **Multi-turn conversation** quality | Model has a back-and-forth conversation; humans rate quality | Tests if model remembers context and stays coherent across multiple messages |
| **GSM8K** | **Math reasoning** | Grade school math word problems | Tests step-by-step mathematical thinking |
| **TruthfulQA** | **Factual accuracy** / avoiding hallucinations | Questions designed to trick the model into giving wrong answers | Measures how often the model makes things up |

**⚠️ The Big Warning About Benchmarks:**

> **A model that ranks #1 on benchmarks may perform poorly on YOUR specific task.**

Why?
- Benchmarks test **general ability**. Your task is **specific**.
- Models can be "trained on the test" (benchmark contamination) — they memorise answers without truly understanding.
- Benchmarks don't measure latency, cost, or reliability — only output quality.

> **Analogy:** A student who scores 99% in board exams might struggle in a job interview. Board marks measure textbook knowledge. Job interviews measure practical ability. Similarly, benchmark scores measure general ability, not real-world task performance.

> **Exam Point:** Always evaluate models on **your own data and tasks**, not just public benchmarks.

### Quality Scorecard — What to Actually Measure

Instead of only looking at benchmark scores, enterprises build a **quality scorecard** that measures the complete system:

| Metric | What It Measures | Why It Matters |
|---|---|---|
| **Accuracy on YOUR test set** | How well the model performs on your actual data | A model might score 90% on MMLU but only 60% on your specific legal documents |
| **Latency — p50** | Median response time (50% of requests are faster than this) | Your chatbot needs to feel responsive |
| **Latency — p95** | 95th percentile response time (only 5% of requests are slower) | Catches "occasional slowness" that frustrates users |
| **Latency — p99** | 99th percentile (worst-case response time for most users) | Important for SLA (Service Level Agreements) |
| **Throughput** | Requests per second the system can handle | Can it handle your peak traffic? |
| **Cost per request** | Total cost including compute, API fees, infrastructure | Directly impacts your business economics |
| **Error rate** | How often does the system fail or return garbage? | Reliability matters more than raw accuracy |

> **Key Insight:** Don't evaluate only model output quality — evaluate the **entire system end-to-end**. A model that's 95% accurate but takes 10 seconds per request is useless for a real-time chatbot. A model that's 85% accurate but responds in 200ms might be the better choice.

**What to Evaluate Beyond Output Quality:**

| Dimension | Metric | Why It Matters |
|---|---|---|
| **End-to-end latency** | Total time from request to response (including network, preprocessing, model, postprocessing) | Users feel the total latency, not just model inference time |
| **Error rate** | % of requests that fail, timeout, or return garbage | A 2% error rate at 1M requests/day = 20,000 failed requests |
| **Cost per request** | API fee + compute + infrastructure overhead | Determines whether the use case is economically viable |
| **Throughput under load** | Requests per second at peak traffic | Black Friday traffic is 10x normal — can your system handle it? |
| **Failure modes** | What happens when the model fails? Graceful fallback or crash? | A good system degrades gracefully (shows cached response) instead of crashing |

> **Analogy:** When buying a car, you don't just check the top speed. You check mileage, maintenance cost, comfort, resale value — the whole picture. Same with AI models.

---

## 4.6 Architecture Patterns

### Gateway/Router Pattern — Don't Call Models Directly

A common mistake in enterprise AI: the application calls a specific model directly. A better approach is to put a **gateway (router)** between your application and the models.

```
❌ Bad approach:
   App → GPT-4 API (locked in, one model for everything)

✅ Good approach:
   App → Gateway/Router → GPT-4 (complex queries)
                        → Mistral 7B (simple queries)
                        → Claude (long-document tasks)
```

**How it works:**

| Step | What Happens | Example |
|---|---|---|
| 1. App sends request | User asks a question | "What is the return policy?" |
| 2. Gateway classifies | Router determines complexity | Simple question → route to small model |
| 3. Route to best model | Based on task, cost, and latency needs | Mistral 7B responds (fast, cheap) |
| 4. Return response | App gets the answer | User sees the response in <200ms |

For a complex query like "Analyze this 50-page contract and identify all liability clauses," the gateway routes to GPT-4 or Claude (expensive, but capable).

**Benefits of the Gateway/Router Pattern:**

| Benefit | How |
|---|---|
| **Cost savings** | Route 80% of simple queries to cheap models, only 20% to expensive models |
| **Swap models easily** | Change from GPT-4 to Claude in the gateway config — app code doesn't change |
| **Redundancy** | If one model provider goes down, gateway routes to another |
| **A/B testing** | Send 50% of traffic to Model A, 50% to Model B, compare results |
| **Compliance** | Route sensitive data queries to self-hosted models, non-sensitive to API models |

> **Real Example:** An e-commerce company routes "What are your business hours?" to a tiny model (₹0.01/query). But routes "Help me compare these 5 laptops based on my requirements" to GPT-4 (₹0.50/query). Result: 70% cost reduction compared to sending everything to GPT-4.

> **Exam Tip:** The Gateway/Router Pattern is an example of the **abstraction layer** concept — decouple your application from specific model providers.

### Total Cost of AI — A Worked Example

Understanding the real cost of AI means looking beyond the API price tag:

```
SCENARIO: 1 million requests/month for a customer support chatbot

OPTION 1: API (GPT-4o)
─────────────────────
  API cost:              ≈ $30,000/month
  Engineering time:      Minimal (1-2 developers)
  Total:                 ≈ $30,000/month

OPTION 2: Managed Open (Bedrock + LLaMA)
─────────────────────────────────────────
  Managed hosting cost:  ≈ $8,000/month
  Engineering time:      Moderate (2-3 developers)
  Total:                 ≈ $8,000/month

OPTION 3: Self-Hosted (own GPU + LLaMA)
───────────────────────────────────────
  GPU compute cost:      ≈ $3,000/month
  Engineering time:      ≈ $5,000/month (MLOps team to manage infra)
  Total:                 ≈ $8,000/month
```

> **Key insight:** Self-hosted looks cheapest on compute, but add engineering time and it costs the same as managed. The "cheapest" option depends on your team size and expertise.

### When to Self-Host

Self-host when:
- **Data cannot leave your network** — healthcare, defense, financial institutions with strict data residency rules
- **Request volume makes API cost prohibitive** — millions of daily requests where per-token fees add up fast
- **You need custom model modifications** — fine-tuning, custom tokenizers, specialized architectures
- **Regulatory requirements demand on-premise** — certain government and banking regulations require on-premise processing

If none of these apply, managed services (Bedrock, Together AI) are usually the better choice.

### Hidden Costs of Self-Hosting

Before choosing self-hosting, account for costs that don't show up in GPU pricing:

| Hidden Cost | What It Involves | Typical Monthly Cost |
|---|---|---|
| **GPU procurement/rental** | A100/H100 GPUs are expensive and hard to get | $3,000-15,000 per GPU |
| **MLOps team salaries** | Engineers to manage infrastructure, deployments, monitoring | $5,000-15,000 (share of team salary) |
| **Monitoring infrastructure** | Prometheus, Grafana, logging, alerting | $500-2,000 |
| **Security patching** | OS updates, container security, vulnerability scanning | Engineering time |
| **Model update management** | Testing new model versions, rollback procedures | Engineering time |
| **On-call rotation** | Someone must respond when the GPU cluster goes down at 3am | Team burden + retention risk |

> **Reality check:** A company that saves $20,000/month on API costs but spends $25,000/month on MLOps team costs hasn't saved anything.

### Cost Optimization — Smart Routing

The biggest cost optimization in AI is not picking the cheapest model — it's routing each request to the right model:

```
STRATEGY: Route simple queries to small model, complex queries to large model

Simple query cost:   $0.001/request  (Mistral 7B)
Complex query cost:  $0.05/request   (GPT-4)

If 80% of queries are simple:
  Blended cost = (0.8 × $0.001) + (0.2 × $0.05)
               = $0.0008 + $0.01
               = $0.0108/request

Compared to sending everything to GPT-4:
  Flat cost = $0.05/request

SAVINGS: $0.0108 vs $0.05 = 78% cost reduction
```

This is exactly what the Gateway/Router pattern enables — classify the query first, then route to the right model.

---

## 4.7 Build vs Buy Decisions

### The Most Important Strategic Question

Every company building AI must answer: **Should we build our own model, use an API, or fine-tune an open-source model?**

### Decision Framework (Simple)

```
Question 1: Is the task generic? (translation, summary, Q&A)
   YES → Use an API (GPT-4, Claude)
   NO  → Go to Question 2

Question 2: Is there an open-source model close to what you need?
   YES → Fine-tune it on your data
   NO  → Go to Question 3

Question 3: Is AI core to your business (competitive advantage)?
   YES → Build a custom model from scratch
   NO  → Reconsider if you even need AI (maybe rules/heuristics work)
```

### Comparison — Three Approaches

| Aspect | Use API | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| **Time to deploy** | Hours to days | Days to weeks | Months to years |
| **Upfront cost** | Zero | Moderate (GPU compute) | Very high (team + compute + data) |
| **Ongoing cost** | Per-request fees (can get expensive at scale) | Server hosting | Team salaries + infrastructure |
| **Data privacy** | Data sent to third party | Data stays on your servers | Full control |
| **Customisation** | Limited (prompt engineering only) | High (adapt to your domain) | Maximum (you control everything) |
| **Maintenance** | Provider handles updates | You handle retraining | You handle everything |
| **Vendor lock-in** | High (if API changes or price increases, you're stuck) | Low (model is yours) | None |
| **Team needed** | 1-2 developers | 2-5 ML engineers | 10-50 ML engineers + researchers |

### Real-World Decision Examples

| Company/Scenario | Decision | Why |
|---|---|---|
| **Razorpay** — fraud detection | **Build from scratch** | Proprietary transaction data. Core business need. Regulatory requirements. Can't send payment data to third-party APIs. |
| **A small e-commerce startup** — product description generation | **API (GPT-4)** | Generic task. Low volume. Fast to market. Don't have ML engineers on team. |
| **A hospital chain** — radiology AI (X-ray analysis) | **Fine-tune open-source** | Need domain adaptation (medical images). Patient data can't leave servers (HIPAA). Open-source medical imaging models exist. |
| **Swiggy** — delivery time prediction | **Build from scratch** | Massive proprietary data (millions of deliveries). Core competitive advantage. Need real-time performance. |
| **A marketing agency** — generating social media posts | **API (Claude/GPT-4)** | Generic creative task. Low volume. Quality is "good enough." No ML team needed. |
| **Google** — Search ranking | **Build from scratch** | Core business. Billions of queries. Proprietary data. The most important model in the company. |
| **Krutrim** — Indian language AI | **Fine-tune open-source (LLaMA)** | Needed Indian language support (not well served by GPT-4). Started from LLaMA's base, added Indian language fine-tuning. |

### Key Insight: The Trend is Toward "Fine-Tune Open-Source"

### Prompting vs RAG vs Fine-Tuning — When to Use What

Before choosing build vs buy, understand the three ways to adapt a model to your needs:

| Approach | When to Use | Data Needed | Cost | Time | Best For |
|---|---|---|---|---|---|
| **Prompting** | No training data available, exploring whether AI fits the task | None — just write good instructions | Lowest (API costs only) | Minutes | Prototyping, simple tasks, quick experiments |
| **RAG (Retrieval-Augmented Generation)** | You have documents/knowledge base the model should reference | Documents, FAQs, knowledge articles | Low-Medium (embedding + retrieval infra) | Hours to days | Factual Q&A, customer support, internal knowledge bots |
| **Fine-Tuning** | You have labelled examples and need domain-specific behavior | Hundreds to thousands of labelled examples | Medium-High (GPU compute) | Days to weeks | Domain adaptation (medical, legal), tone/style matching, specialized classification |

```
DECISION FLOW:
─────────────
No data? → Prompting
Have documents? → RAG
Have labelled examples and need specialized behavior? → Fine-Tuning
```

> **Key insight:** Most enterprise use cases are best served by RAG (80% of cases). Fine-tuning is only needed when the model needs to learn new behavior, not just access new information.

The AI industry is shifting:
- **2020-2022:** Most companies used APIs (OpenAI was the only option)
- **2023-2024:** Open-source models (LLaMA, Mistral) became good enough for most tasks
- **2025+:** Fine-tuning open-source is becoming the default for serious applications

Why? APIs are great for prototyping, but at scale:
- API costs add up fast
- Data privacy concerns are real
- Customisation through prompt engineering has limits
- Open-source models are now 80-90% as good as proprietary ones

### Enterprise Procurement Scorecard — How Companies Actually Decide

In real enterprises, model selection isn't a gut feeling — it's done with a **weighted scorecard**. Different factors get different weights based on the use case.

**Standard Scorecard Weights:**

| Factor | Weight | What You Score (1-10) |
|---|---|---|
| **Capability** | 30% | How well does it perform on your specific tasks? |
| **Cost** | 25% | Total cost of ownership (API fees, compute, engineering time) |
| **Latency** | 15% | Response time (p50, p95, p99) |
| **Privacy** | 15% | Data residency, compliance, where data is processed |
| **Support** | 10% | Enterprise support, SLAs, documentation quality |
| **Lock-in** | 5% | How easy is it to switch to another model/provider later? |

**Example Comparison:**

| Factor | Weight | GPT-4 (API) | Claude (API) | LLaMA 3 (Self-Hosted) |
|---|---|---|---|---|
| Capability | 30% | 9/10 | 8/10 | 7/10 |
| Cost | 25% | 5/10 | 6/10 | 9/10 |
| Latency | 15% | 7/10 | 7/10 | 9/10 |
| Privacy | 15% | 4/10 | 5/10 | 10/10 |
| Support | 10% | 9/10 | 8/10 | 3/10 |
| Lock-in | 5% | 3/10 | 4/10 | 10/10 |
| **Weighted Score** | | **6.6** | **6.5** | **7.9** |

In this example, self-hosted LLaMA wins — but only because the use case values privacy and cost heavily. For a different use case, the weights (and winner) would be different.

### Worked Example — Customer Support Chatbot Selection

Evaluate GPT-4, Claude 3.5, and self-hosted LLaMA 3 for a customer support chatbot:

```
WEIGHTS:
  Capability: 30%  |  Cost: 25%  |  Latency: 15%
  Privacy: 15%     |  Support: 10%  |  Lock-in: 5%

SCORES (1-5, where 5 = best):

              Capability  Cost  Latency  Privacy  Support  Lock-in
GPT-4             5        2       3        2        5        2
Claude 3.5        4        3       3        3        4        3
LLaMA 3 (self)    3        5       4        5        2        5

WEIGHTED TOTALS:
  GPT-4:     (5×0.30)+(2×0.25)+(3×0.15)+(2×0.15)+(5×0.10)+(2×0.05)
           = 1.50 + 0.50 + 0.45 + 0.30 + 0.50 + 0.10 = 3.35

  Claude:    (4×0.30)+(3×0.25)+(3×0.15)+(3×0.15)+(4×0.10)+(3×0.05)
           = 1.20 + 0.75 + 0.45 + 0.45 + 0.40 + 0.15 = 3.40

  LLaMA 3:  (3×0.30)+(5×0.25)+(4×0.15)+(5×0.15)+(2×0.10)+(5×0.05)
           = 0.90 + 1.25 + 0.60 + 0.75 + 0.20 + 0.25 = 3.95

WINNER: Self-hosted LLaMA 3 (3.95) — because cost and privacy
dominate this use case.
```

> **Key takeaway:** The scorecard makes the decision objective and auditable. Different weights for a different use case (say, a medical assistant where capability matters most) would produce a different winner.

**Weights change based on use case:**

| Use Case | Highest Weight | Why |
|---|---|---|
| **Healthcare AI** | Privacy: 30% | Patient data regulations (HIPAA, DPDP) |
| **Marketing content** | Cost: 30% | High volume, "good enough" quality is fine |
| **Financial trading** | Latency: 30% | Milliseconds matter in trading |
| **Customer support** | Capability: 35% | Answer quality directly impacts customer satisfaction |

> **Exam Tip:** If asked "how should an enterprise choose between models?" — describe the weighted scorecard approach with factors and weights, not just "pick the best one."

### Enterprise Model Lifecycle — Models Are Not Permanent

AI models are not a "set and forget" decision. Better models emerge every few months. Enterprises need a **model lifecycle** — a process for continuously managing their AI models.

```
Select → Integrate → Monitor → Evaluate → Replace/Upgrade
  ↑                                              |
  └──────────── (cycle repeats) ────────────────┘
```

| Phase | What Happens | Example |
|---|---|---|
| **Select** | Use the procurement scorecard to pick the best model for your task | "We evaluated 5 models. Claude 3.5 scored highest for our legal Q&A bot." |
| **Integrate** | Connect the model to your application (via API, gateway, or self-hosted deployment) | "Integrated Claude via AWS Bedrock with our existing backend." |
| **Monitor** | Track performance in production — accuracy, latency, cost, errors | "Our dashboard shows accuracy dropped from 92% to 85% this month." |
| **Evaluate** | Periodically test new models against your current one | "LLaMA 4 just released. Let's benchmark it against our current Claude setup." |
| **Replace/Upgrade** | If a new model is better, swap it in (easy with Gateway/Router pattern) | "LLaMA 4 scores higher, costs less. Switching via Bedrock — zero code changes." |

**What to version-control (track changes over time):**

| What | Why | Tool |
|---|---|---|
| **Prompts** | Small prompt changes can dramatically change output quality | Git, prompt management tools |
| **Fine-tuning data** | If you re-fine-tune, you need to know what data was used | Dataset versioning (DVC, Hugging Face Datasets) |
| **Model versions** | Know exactly which model version is in production | Model registry (MLflow, Hugging Face Hub) |
| **Evaluation results** | Compare performance across model versions over time | Experiment tracking (Weights & Biases, MLflow) |

> **Real Example:** A bank uses GPT-4 for customer support in January 2025. In March, Claude 3.5 becomes cheaper with similar quality. In June, LLaMA 4 is released and fine-tuned on banking data performs even better. Because they use a Gateway/Router pattern and version everything, they swap models smoothly each time — like changing a light bulb, not rewiring the house.

> **Key Takeaway:** Plan for model rotation from day one. The model you pick today will likely be replaced within 6-12 months. Design your architecture (gateway pattern, abstraction layers) to make this easy.

---

## Key Terms Glossary (Session 4)

| Term | Simple Meaning |
|---|---|
| **Foundation Model** | A large, general-purpose AI model trained on massive data that can be adapted for many tasks |
| **Transfer Learning** | Using knowledge from one task/domain to help with another task/domain |
| **Fine-tuning** | Taking a pre-trained model and training it further on your specific data |
| **Open-source Model** | An AI model whose weights (learned parameters) are freely available to download and use |
| **API (Application Programming Interface)** | A way to use someone else's AI by sending requests and getting responses |
| **Hugging Face** | The most popular platform for sharing and discovering AI models (the "GitHub of AI") |
| **Model Hub** | An online platform where AI models are shared, documented, and versioned |
| **Model Card** | Documentation describing a model — what it does, how it was trained, its limitations |
| **Prompt Engineering** | Writing better instructions to get better outputs from an LLM |
| **Vendor Lock-in** | Being dependent on one provider, making it hard/expensive to switch |
| **Emergent Abilities** | Capabilities that appear in large models that weren't explicitly trained for |
| **Token** | The basic unit an LLM processes — roughly 3/4 of a word |
| **AWS Bedrock** | Amazon's managed service to access multiple foundation models (Claude, LLaMA, Mistral) through a single API with an abstraction layer |
| **Google Model Garden** | Google's model discovery and deployment platform within Vertex AI, hosting Google and open-source models |
| **Azure Model Catalog** | Microsoft's model marketplace within Azure AI Studio, the only way to get OpenAI models with enterprise Azure compliance |
| **NVIDIA NGC** | NVIDIA's catalog of GPU-optimized models, containers, and deployment tools for NVIDIA hardware |
| **Inference-as-a-Service** | Providers (Fireworks AI, Together AI, Groq) that host open-source models with optimized inference — pay per token, no GPU management |
| **Groq LPU** | A custom hardware chip (Language Processing Unit) designed specifically for ultra-fast LLM inference, used by Groq |
| **Apache 2.0 License** | The most permissive open-source license — allows commercial use, modification, and redistribution with no restrictions |
| **Llama License** | Meta's custom license for LLaMA models — free for most uses, but restricted for apps with more than 700 million monthly active users |
| **MMLU Benchmark** | Massive Multitask Language Understanding — measures AI general knowledge across 57 academic subjects |
| **HumanEval Benchmark** | A benchmark that measures code generation ability by testing if model-written code actually works |
| **MT-Bench** | A benchmark that measures multi-turn conversation quality — how well a model handles back-and-forth dialogue |
| **Quality Scorecard** | A structured evaluation of a model's accuracy, latency (p50/p95/p99), throughput, cost, and error rate on your actual tasks |
| **Gateway/Router Pattern** | An architecture where the application calls a gateway that routes requests to different models based on task complexity, cost, and latency needs |
| **Procurement Scorecard** | A weighted scoring system (capability, cost, latency, privacy, support, lock-in) used by enterprises to objectively compare and select AI models |
| **Model Lifecycle** | The ongoing cycle of Select → Integrate → Monitor → Evaluate → Replace/Upgrade for managing AI models in production |
| **Small/Task-Specific Models** | Models with 1-7B parameters that are cheaper, faster, and often better than large frontier models for specific narrow tasks |

---

*End of Session 4*
