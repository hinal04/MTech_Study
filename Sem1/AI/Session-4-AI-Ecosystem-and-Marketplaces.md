# Session 4: AI Ecosystem and Model Marketplaces

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** Hugging Face, OpenAI APIs, Cloud AI Services
>
> **Contact Session:** 4 (Module 1: Foundations of AI Systems)

---

## Table of Contents

- [4.1 Foundation Models](#41-foundation-models)
- [4.2 Open-Source Models](#42-open-source-models)
- [4.3 APIs and Cloud AI Services](#43-apis-and-cloud-ai-services)
- [4.4 Model Hubs](#44-model-hubs)
- [4.5 Build vs Buy Decisions](#45-build-vs-buy-decisions)

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

---

## 4.5 Build vs Buy Decisions

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

The AI industry is shifting:
- **2020-2022:** Most companies used APIs (OpenAI was the only option)
- **2023-2024:** Open-source models (LLaMA, Mistral) became good enough for most tasks
- **2025+:** Fine-tuning open-source is becoming the default for serious applications

Why? APIs are great for prototyping, but at scale:
- API costs add up fast
- Data privacy concerns are real
- Customisation through prompt engineering has limits
- Open-source models are now 80-90% as good as proprietary ones

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

---

*End of Session 4*
