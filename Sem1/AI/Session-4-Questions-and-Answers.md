# Session 4: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is a Foundation Model? List its key characteristics.

**Answer:**
A Foundation Model is a very large AI model trained on massive amounts of data that can be used for many different tasks. The term was coined by Stanford University in 2021 because these models serve as the "foundation" on which many applications are built.

**Analogy:** A foundation model is like a Swiss Army knife — not designed for just one task. GPT-4 can write essays, translate languages, write code, answer questions, and summarise documents — all with the same model.

Key characteristics:

| Characteristic | Explanation |
|---|---|
| **Trained on massive data** | Billions of text documents, images, or code files. GPT-4 was trained on text equivalent to millions of books. |
| **General-purpose** | Not built for one task — handles many different tasks without retraining |
| **Transfer learning** | Knowledge from training transfers to new tasks. GPT-4 wasn't specifically taught Indian tax law but can answer tax questions from general legal reasoning. |
| **Emergent abilities** | Large models show abilities that weren't explicitly taught. GPT-4 can do chain-of-thought reasoning — nobody programmed this. |
| **Very expensive to train** | Costs millions of dollars. GPT-4 training: estimated $100M+. Only companies like OpenAI, Google, Meta can afford this. |
| **Accessible to everyone** | Regular developers use them via APIs or fine-tuning, paying a few rupees per call instead of $100M to train their own. |

---

## Q2: List the notable Foundation Models you should know. Who made them and what do they do?

**Answer:**

| Model | Company | Type | Key Fact |
|---|---|---|---|
| **GPT-4 / GPT-4o** | OpenAI | Text + images + audio | Most capable general-purpose model. Powers ChatGPT. |
| **Claude 3.5 / Claude 4** | Anthropic | Text + images | Focus on safety. 200K token context window (~500 pages). |
| **Gemini** | Google DeepMind | Text + images + audio + video | Natively multimodal — understands all media types together. |
| **LLaMA 3** | Meta | Text (open-source) | Free to download and modify. Thousands of community fine-tuned versions. |
| **Mistral** | Mistral AI (France) | Text (open-source) | Very efficient — strong performance with smaller model size. |
| **Stable Diffusion** | Stability AI | Image generation (open-source) | Free image generation. Anyone can run on their own GPU. |
| **DALL-E 3** | OpenAI | Image generation | Best text-to-image quality. Integrated into ChatGPT. |
| **Whisper** | OpenAI | Speech-to-text (open-source) | Understands 100+ languages. Free to use. |

For exams, remember: GPT-4 (OpenAI, most capable), Gemini (Google, multimodal), LLaMA (Meta, open-source), and Whisper (OpenAI, speech-to-text, open-source).

---

## Q3: What does "open-source" mean in the context of AI models? List the benefits of open-source models.

**Answer:**
An open-source model is one where the model files (called "weights") are freely available for anyone to download, inspect, modify, and run on their own computers.

**Analogy:** A proprietary model (like GPT-4) is like a restaurant — you pay for each meal and can't see the recipe. An open-source model (like LLaMA) is like a recipe shared online — you can cook it yourself, modify it, add your own spices, for free.

Benefits:

| Benefit | What It Means | Real Example |
|---|---|---|
| **No API costs** | Run on your own servers — no per-request charges | A startup making 1M API calls/month to GPT-4 pays ~Rs 25 lakh. Same with open-source on own GPU: ~Rs 1.5 lakh/month. |
| **Data privacy** | Your data never leaves your servers | A hospital can run a medical AI without sending patient data to OpenAI's servers (would violate privacy regulations). |
| **Full customisation** | You can fine-tune, modify, or strip down the model | Fine-tune LLaMA on Indian legal documents → create an Indian law AI that outperforms GPT-4 for this specific task. |
| **No vendor lock-in** | Not dependent on one company's pricing or availability | If OpenAI raises prices 5x tomorrow, companies using GPT-4 are stuck. Open-source users can switch to Mistral or LLaMA. |
| **Community innovation** | Thousands of developers improve and create variants | Hugging Face has 500K+ open-source models fine-tuned for every domain imaginable. |

---

## Q4: Case Study — How did Krutrim use open-source models to build India's first multilingual LLM?

**Answer:**
Krutrim (by Ola's Bhavish Aggarwal) built India's first multilingual large language model by leveraging open-source technology.

**What they did:**
1. Started with open-source LLaMA (Meta's model) as the base
2. Fine-tuned it on 22 Indian languages — Hindi, Tamil, Telugu, Bengali, Kannada, Malayalam, Marathi, Gujarati, and more
3. Result: An AI that understands Indian languages far better than GPT-4 (which was primarily trained on English)

**Why open-source was essential:**
- Didn't need $100M to train from scratch — used transfer learning from LLaMA's English knowledge
- LLaMA already understood language structure, grammar, and reasoning — Krutrim just needed to teach it Indian languages
- Could customise freely — added support for Devanagari, Tamil, Telugu scripts without asking Meta for permission

**Why GPT-4 wasn't enough:**
- GPT-4 is primarily trained on English text — its Hindi/Tamil understanding is limited
- Using GPT-4 API means sending all user data to OpenAI's servers (data sovereignty concern)
- API costs at scale would be enormous for a mass-market Indian product

**Key lesson:** Open-source models enable countries and companies to build AI in their own languages, on their own terms, without depending on US-based companies. This is the power of open-source: start from a strong foundation, adapt to your specific need.

---

## Q5: What is an AI API? What are the different types of AI APIs available?

**Answer:**
An API (Application Programming Interface) lets you use someone else's AI model by sending a request and getting back a response — like ordering food delivery instead of cooking yourself.

**Analogy:** You don't need to own a cow to drink milk. You buy milk from a dairy. Similarly, you don't need to train your own AI model — you call an API and get predictions back.

Types of AI APIs:

| Category | What You Get | Cost Model | Examples |
|---|---|---|---|
| **Text generation** | Send a prompt, get generated text back | Pay per token (word) | OpenAI API (GPT-4): ~Rs 0.80 per 1000 input tokens. Anthropic API (Claude). Google Gemini API. |
| **Embedding APIs** | Convert text/images into numbers (vectors) for search and similarity | Pay per token | OpenAI Embeddings, Cohere Embed. Used for semantic search. |
| **Image APIs** | Generate or analyse images | Pay per image | DALL-E 3: ~Rs 3 per image. Google Vision AI. |
| **Speech APIs** | Convert speech to text or text to speech | Pay per minute/character | OpenAI Whisper API: ~Rs 0.50/min. Google Cloud Speech. AWS Transcribe. |
| **Cloud ML Platforms** | Full ML lifecycle — train, deploy, monitor | Pay for compute + storage | AWS SageMaker, Google Vertex AI, Azure ML |

**Key pricing insight:** APIs are cheaper for low volume (under 100K requests/month). Self-hosted is cheaper for high volume (over 500K requests/month). The crossover point depends on the specific task.

---

## Q6: What is Hugging Face? Why is it called the "GitHub of AI"?

**Answer:**
Hugging Face is the most important platform in the AI ecosystem — an online hub where AI models are shared, documented, and versioned. Every ML engineer uses it.

It's called the "GitHub of AI" because just like GitHub hosts code repositories, Hugging Face hosts AI model repositories.

| Feature | What It Does | Why It Matters |
|---|---|---|
| **500K+ Models** | Pre-trained models for every task — text, image, audio, video | Whatever you need, someone probably already built it |
| **100K+ Datasets** | Ready-to-use datasets with preview and streaming | Don't need to collect your own data for many tasks |
| **Model Cards** | Documentation for each model — what it does, how to use it, limitations | You know exactly what you're getting before downloading |
| **Spaces** | Host interactive demos of models | Test a model in your browser before committing |
| **Inference API** | Run any model via API without setting up your own server | No GPU needed — just make an API call |
| **Transformers Library** | Python library to use any model in 3 lines of code | The most popular ML library in the world |

How easy it is to use:
```python
# Sentiment Analysis in 3 lines
from transformers import pipeline
classifier = pipeline("sentiment-analysis")
result = classifier("I love this product! Best purchase ever.")
# Output: [{'label': 'POSITIVE', 'score': 0.9998}]
```

**Real-world use:** When Flipkart wants to analyse millions of customer reviews to find common complaints, they can use Hugging Face's sentiment analysis pipeline — no model training needed.

---

## Q7: What is a Model Hub? What is a Model Card and why is it important?

**Answer:**
A **Model Hub** is an online platform where AI models are shared, documented, and versioned — like an app store, but for AI models. Examples: Hugging Face Hub, TensorFlow Hub, PyTorch Hub.

A **Model Card** is the documentation that accompanies each model on the hub. It describes:
- What the model does (task, capabilities)
- How it was trained (data, method, compute used)
- How to use it (code examples, input/output format)
- Performance metrics (accuracy, F1 score on benchmarks)
- Limitations and biases (what it's bad at, known failure cases)
- Ethical considerations (potential for misuse)

**Why Model Cards matter:**
1. **Transparency:** You know exactly what you're getting before downloading — trained on what data, how well it performs, what it can't do
2. **Reproducibility:** Other researchers can verify or reproduce the results
3. **Responsible AI:** Documents known biases and limitations so users can make informed decisions
4. **Trust:** A model without a Model Card is like a medicine without a label — you don't know what's inside

**Analogy:** A Model Card is like the nutrition label on a food packet. Before eating (using the model), you check: What's inside? Any allergens (biases)? Expiry date (when was it last updated)?

---

## Q8: Explain the Build vs Buy decision framework. When should a company build their own model vs use an API vs fine-tune open-source?

**Answer:**
This is the most important strategic question for any company building AI.

The decision framework (three questions):

```
Question 1: Is the task generic? (translation, summary, Q&A)
   YES → Use an API (GPT-4, Claude)
   NO  → Go to Question 2

Question 2: Is there an open-source model close to what you need?
   YES → Fine-tune it on your data
   NO  → Go to Question 3

Question 3: Is AI core to your business (competitive advantage)?
   YES → Build a custom model from scratch
   NO  → Reconsider if you even need AI
```

Comparison:

| Aspect | Use API | Fine-Tune Open-Source | Build from Scratch |
|---|---|---|---|
| **Time to deploy** | Hours to days | Days to weeks | Months to years |
| **Upfront cost** | Zero | Moderate (GPU compute) | Very high (team + compute + data) |
| **Ongoing cost** | Per-request fees (expensive at scale) | Server hosting | Team salaries + infrastructure |
| **Data privacy** | Data sent to third party | Data stays on your servers | Full control |
| **Customisation** | Limited (prompt engineering only) | High (adapt to your domain) | Maximum (you control everything) |
| **Team needed** | 1-2 developers | 2-5 ML engineers | 10-50 ML engineers + researchers |
| **Vendor lock-in** | High | Low (model is yours) | None |

---

## Q9: Scenario — A small e-commerce startup in India wants to generate product descriptions automatically. What approach should they take and why?

**Answer:**
**Recommended approach: Use an API (GPT-4 or Claude)**

Reasoning:

1. **Task is generic:** Product description generation is a general text generation task — foundation models like GPT-4 already excel at this without any special training.

2. **Low volume:** A small startup might have hundreds to a few thousand products. At this volume, API costs are minimal — perhaps Rs 500-1000 per month.

3. **No ML team needed:** The startup likely doesn't have ML engineers. Using an API requires just 1-2 developers who can write a simple script.

4. **Speed to market:** Can be built and deployed in 1-2 days using the OpenAI API. Building from scratch would take months.

5. **Good enough quality:** GPT-4 already writes excellent product descriptions. No fine-tuning needed for this generic task.

Implementation:
```python
response = openai.chat.completions.create(
    model="gpt-4o",
    messages=[{
        "role": "user", 
        "content": "Write a compelling product description for: Blue cotton kurta, size M, hand-embroidered, Jaipur collection"
    }]
)
```

When to reconsider: If the startup grows to millions of products and needs frequent regeneration, the API costs would become significant. At that point, switching to a fine-tuned open-source model (like LLaMA fine-tuned on their product catalog) would be more cost-effective.

---

## Q10: Scenario — Razorpay wants to build a fraud detection AI system. Should they use API, fine-tune, or build from scratch? Justify.

**Answer:**
**Recommended approach: Build from scratch**

Justification:

1. **Proprietary data is the key advantage:** Razorpay has unique transaction data from millions of Indian merchants — transaction patterns, merchant behaviours, UPI flows. No foundation model has this data. Their competitive advantage comes from training on this proprietary data.

2. **Data privacy and regulation:** Payment data is extremely sensitive. Sending transaction details to OpenAI or any third-party API would violate PCI-DSS compliance and RBI regulations. The model MUST run on Razorpay's own infrastructure.

3. **Core business need:** Fraud detection directly impacts revenue and trust. If fraud goes undetected, Razorpay loses money and merchant trust. This is too critical to depend on a third-party API that could have downtime or policy changes.

4. **Real-time requirement:** Fraud detection must evaluate each transaction within milliseconds. An external API call would add latency (network round-trip). A self-hosted model responds faster.

5. **Continuous customisation needed:** Fraud patterns change weekly in India (new UPI scams, QR code fraud, social engineering). Razorpay needs to retrain their model frequently with their own data — something an API can't do.

6. **Team capacity:** Razorpay is a well-funded fintech company that can afford a team of 10-20 ML engineers dedicated to fraud detection.

Build from scratch is justified because: the data is proprietary, the task is core to business, privacy regulations are strict, and the company has resources to maintain a custom system.

---

## Q11: Scenario — A hospital chain wants to build an AI for analysing X-ray images. What approach should they use?

**Answer:**
**Recommended approach: Fine-tune an open-source model**

Justification:

1. **Open-source medical imaging models exist:** Models like CheXNet (Stanford), BiomedCLIP, and various models on Hugging Face are already pre-trained on medical images. They provide a strong starting point.

2. **Patient data can't leave the hospital:** HIPAA/Indian health data regulations mean patient X-rays cannot be sent to third-party APIs like Google Vision AI. The model must run on the hospital's own servers.

3. **Domain adaptation is needed:** Generic image classification models (trained on everyday photos) don't understand medical images well. Fine-tuning on the hospital's own X-ray dataset adapts the model to detect specific conditions (tuberculosis, pneumonia, fractures).

4. **Cost-effective:** Building a medical imaging AI from scratch would require a massive team and millions of dollars. Fine-tuning an existing model needs 2-5 ML engineers and weeks of work.

5. **Regulatory requirements:** Medical AI must be explainable — doctors need to understand why the AI flagged a particular X-ray. Fine-tuned models with proper evaluation are easier to validate than black-box API calls.

Implementation plan:
- Download a pre-trained medical imaging model from Hugging Face
- Collect and label 50,000+ X-rays from the hospital's archives (with radiologist annotations)
- Fine-tune the model on this data
- Evaluate against a held-out test set validated by senior radiologists
- Deploy on the hospital's own GPU servers
- Use as a "second opinion" tool — the AI flags suspicious X-rays for the doctor to review

---

## Q12: What is Transfer Learning? Why is it the foundation of the modern AI ecosystem?

**Answer:**
Transfer learning is the technique of using knowledge learned from one task or dataset and applying it to a different but related task.

**Analogy:** A person who knows Hindi can learn Marathi faster because both languages share similar grammar and many common words. The "knowledge" of Hindi transfers to help with Marathi. Similarly, a model trained on English text can be fine-tuned for Hindi tasks much faster than training from scratch.

Why it's the foundation of modern AI:

1. **Saves massive training costs:** GPT-4 cost $100M+ to train. With transfer learning, you can fine-tune it for your specific task for just a few thousand dollars.

2. **Needs less data:** A model pre-trained on billions of words already understands language. Fine-tuning it on 10,000 legal documents is enough to create a legal AI — training from scratch would need millions of documents.

3. **Faster development:** Fine-tuning takes days or weeks. Training from scratch takes months or years.

4. **Democratises AI:** Small companies and startups can build sophisticated AI by fine-tuning foundation models, without needing the resources of Google or OpenAI.

Examples:
- **Krutrim:** Fine-tuned LLaMA on Indian languages → Indian multilingual AI
- **Hospital AI:** Fine-tune a medical imaging model on their X-rays → domain-specific diagnostic tool
- **Legal startup:** Fine-tune GPT on Indian court judgments → AI legal research assistant

Without transfer learning, only companies with billions of dollars could build AI. Transfer learning made AI accessible to everyone.

---

## Q13: Compare API pricing with self-hosted pricing for a chatbot processing 1 million requests per month.

**Answer:**

| Approach | Monthly Cost | Setup Time | Maintenance |
|---|---|---|---|
| **GPT-4o API** | ~Rs 2,50,000/month (at ~$0.03 per 1K tokens, assuming 500 tokens per request) | 1-2 days | Zero — OpenAI handles everything |
| **Self-hosted open-source (LLaMA on GPU)** | ~Rs 40,000-60,000/month (GPU server rental) | 2-4 weeks | You handle updates, scaling, monitoring |

**When API is better (low volume):**
- Under 100K requests/month → API is cheaper and easier
- No ML team available
- Need to launch quickly (prototype or MVP)
- Task is generic (translation, summarisation, Q&A)

**When self-hosted is better (high volume):**
- Over 500K requests/month → self-hosted is significantly cheaper
- Data privacy requirements (healthcare, finance, government)
- Need customisation beyond prompt engineering
- Want to avoid vendor lock-in

**The crossover point:**
Roughly at 200K-500K requests/month, self-hosting becomes cheaper. Below that, the infrastructure overhead (setup, maintenance, DevOps) makes APIs more cost-effective.

Think of it like renting vs buying a house: Renting (API) is easier and cheaper short-term. Buying (self-hosting) has a high upfront cost but is cheaper long-term for heavy use.

---

## Q14: What is Vendor Lock-in? Why is it a risk when using AI APIs?

**Answer:**
Vendor lock-in means being so dependent on one provider that switching to another becomes very difficult or expensive.

**How it happens with AI APIs:**
1. You build your entire product around GPT-4's API (specific prompt formats, output structures, features)
2. Your code, prompts, and workflows are all designed for OpenAI's specific API format
3. Now you're stuck — if OpenAI raises prices 5x, changes their terms of service, or discontinues a model, switching to Claude or Gemini requires rewriting significant code and re-engineering all prompts

**Real risks:**
- **Price increases:** OpenAI can raise API prices at any time. Companies with millions of API calls face sudden cost spikes.
- **Model deprecation:** OpenAI deprecated GPT-3.5, forcing users to migrate. Future model changes could break existing applications.
- **Policy changes:** If OpenAI changes content policies, your application might suddenly get blocked responses for previously allowed queries.
- **Downtime:** If OpenAI's servers go down, your entire product goes down. You have zero control.

**How to avoid vendor lock-in:**
1. **Abstract the API layer:** Write your code so switching from OpenAI to Claude requires changing one config file, not rewriting the entire application
2. **Use open-source models:** Your model, your servers, your rules
3. **Multi-provider strategy:** Use OpenAI for some tasks, Claude for others. If one fails, the other is a backup.
4. **Standardised prompts:** Keep prompts as model-agnostic as possible

---

## Q15: What are Emergent Abilities in Foundation Models? Give examples.

**Answer:**
Emergent abilities are capabilities that appear in large AI models that weren't explicitly programmed or trained for. They "emerge" spontaneously as the model gets larger.

**Analogy:** When you teach a child to read, you don't explicitly teach them to detect sarcasm. But after reading thousands of books, they develop the ability to understand sarcasm on their own. Similarly, LLMs develop abilities that nobody explicitly trained them for.

Examples of emergent abilities:

1. **Chain-of-thought reasoning:** GPT-4 can solve math problems step by step. Nobody programmed "think step by step" — this ability emerged from training on vast text that included step-by-step explanations.

2. **Code generation:** Models trained primarily on text (including some code) can write functional programs in languages they weren't specifically taught — they "figured out" programming logic from patterns.

3. **Translation between uncommon language pairs:** A model trained mainly on English-French and English-Hindi text can suddenly translate directly between French and Hindi, even though it saw very few French-Hindi examples.

4. **Analogical reasoning:** Asking "Japan is to Tokyo as India is to ___" and getting "New Delhi" — the model learned analogy patterns without explicit analogy training.

Why this matters: Emergent abilities mean that larger models aren't just "better at the same things" — they can do fundamentally new things that smaller models cannot. This is why the AI industry keeps building bigger models — new capabilities emerge at certain scale thresholds.

---

## Q16: What is the role of Ollama in the open-source AI ecosystem?

**Answer:**
Ollama is a tool that lets you run open-source LLMs on your own laptop or computer with a single command. Think of it as "Docker for AI models" — simple, lightweight, and local.

How easy it is:
```bash
# Install and run LLaMA 3 on your laptop
ollama run llama3
```
That's it. One command, and you have a ChatGPT-like AI running entirely on your computer — no internet needed, no API costs, complete privacy.

Why Ollama matters:

1. **Zero cost:** Once downloaded, the model runs locally. No per-request fees.
2. **Complete privacy:** Your conversations never leave your computer. Perfect for sensitive work (legal, medical, financial documents).
3. **Works offline:** No internet connection needed after initial download. Use AI on a flight or in areas with poor connectivity.
4. **Easy experimentation:** Try different models (LLaMA, Mistral, Phi, Gemma) instantly to find the best one for your use case.

**Limitations:**
- Requires a decent computer (8GB+ RAM for small models, 16GB+ for larger ones)
- Local models are generally less capable than GPT-4 or Claude
- No cloud scaling — limited to your machine's capacity

**Use case:** A developer working on a confidential project can use Ollama to get AI coding assistance without sending any proprietary code to external servers.

---

## Q17: The industry is shifting from "Use APIs" to "Fine-tune Open-Source." Explain this trend with a timeline.

**Answer:**
The AI industry has gone through distinct phases:

**2020-2022: The API Era**
- GPT-3 launched (2020) — the first powerful LLM available via API
- OpenAI was essentially the only option for good-quality text generation
- Companies had no choice but to use APIs
- Open-source alternatives were weak

**2023-2024: The Open-Source Revolution**
- Meta released LLaMA (2023) — first high-quality open-source LLM
- Mistral released efficient open-source models from France
- Google released Gemma (smaller open-source models)
- Open-source models reached 80-90% of GPT-4's quality
- Fine-tuning tools became easy to use (Hugging Face, LoRA, QLoRA)

**2025+: Fine-tune as Default**
- For serious production applications, fine-tuning open-source is becoming the standard
- APIs remain popular for prototyping and low-volume tasks
- Open-source is the pragmatic middle ground between API simplicity and build-from-scratch control

**Why this shift is happening:**

| Reason | Explanation |
|---|---|
| **Cost** | API costs at scale are enormous. Self-hosted fine-tuned models are 5-10x cheaper at 1M+ requests/month |
| **Privacy** | Enterprises don't want to send data to third-party servers |
| **Customisation** | Prompt engineering has limits. Fine-tuning gives deeper domain adaptation. |
| **Quality** | Open-source models are now "good enough" for most production tasks |
| **Vendor independence** | Companies don't want to depend on OpenAI's pricing and policies |

**Indian context:** Companies like Krutrim, Sarvam AI, and AI4Bharat are building on open-source models because Indian language support from proprietary APIs is inadequate. Fine-tuning open-source models with Indian language data is the only practical path.

---

## Q18: Scenario — You're an AI consultant. Three different companies ask you for advice. What would you recommend to each?

**Answer:**

**Company A: A marketing agency with 10 employees wants to generate social media posts for clients.**

Recommendation: **Use an API (GPT-4 or Claude)**
- Task is generic creative writing — foundation models excel at this
- Low volume (maybe 1000-5000 posts per month) — API cost is negligible (under Rs 5000/month)
- No ML engineers on team
- Can be built in a day with a simple script
- Quality is "good enough" without any fine-tuning

**Company B: A large Indian bank wants to build a customer support chatbot that answers questions about their 200+ financial products.**

Recommendation: **Fine-tune open-source model with RAG**
- Data privacy: Customer conversations and financial product details cannot be sent to OpenAI (RBI regulations)
- Domain specificity: The bot needs to answer accurately about specific bank products — a generic LLM would hallucinate details
- RAG approach: Fine-tune LLaMA + build a RAG system that retrieves relevant product documentation before answering
- Volume: Large bank with millions of customers → self-hosted is cheaper than API at this scale
- Team needed: 3-5 ML engineers — affordable for a large bank

**Company C: Google wants to improve its search ranking algorithm.**

Recommendation: **Build from scratch**
- This is THE core business — search ranking is Google's most important product
- Proprietary data: Billions of search queries and click patterns that no one else has
- Scale: Handles billions of queries daily — no API could handle this volume
- Competitive advantage: The ranking model IS the product. Using someone else's model would mean having the same quality as competitors.
- Team: Google has thousands of ML researchers
- No existing open-source model can handle this task at Google's scale and specificity

---

## Q19: What is Fine-tuning? How is it different from Prompt Engineering? When should you use each?

**Answer:**

**Prompt Engineering:** Writing better instructions (prompts) to get better outputs from a pre-trained model, without changing the model itself.

Example: Instead of asking "summarise this," you write "summarise this article in 3 bullet points for a 10-year-old, using simple Hindi."

**Fine-tuning:** Taking a pre-trained model and training it further on your specific dataset, which actually modifies the model's internal parameters.

Example: Taking LLaMA and training it on 100,000 medical Q&A pairs so it becomes better at answering medical questions.

| Aspect | Prompt Engineering | Fine-tuning |
|---|---|---|
| **What changes** | Only the input instructions | The model's internal weights/parameters |
| **Effort** | Minutes to hours | Days to weeks |
| **Cost** | Zero (just writing text) | Moderate (GPU compute for training) |
| **Skill needed** | Any developer can do it | Needs ML engineering expertise |
| **Customisation depth** | Surface-level (how the model responds) | Deep (what the model knows) |
| **Best for** | Generic tasks, formatting preferences | Domain-specific tasks, specialised knowledge |

**When to use each:**

Use **Prompt Engineering** when:
- The task is generic (summarisation, translation, general Q&A)
- You need quick results without any setup
- The base model already has the knowledge you need
- You want to control output format (JSON, bullet points, tables)

Use **Fine-tuning** when:
- The model needs domain-specific knowledge it doesn't have (Indian law, medical terminology, your company's products)
- Prompt engineering gives inconsistent results
- You need consistent style/tone across thousands of outputs
- Performance on your specific task needs significant improvement

---

## Q20: Scenario — An Indian edtech startup (like Byju's competitor) wants to build an AI tutor that explains concepts in Hindi. Walk through their Build vs Buy decision.

**Answer:**

**Step 1 — Evaluate the task:**
The AI tutor needs to explain NCERT concepts in simple Hindi, adapt to student level, and handle follow-up questions. This requires good Hindi language understanding + educational content knowledge.

**Step 2 — Can an API work?**
Partially. GPT-4 can explain concepts and understands Hindi reasonably well. For an MVP (minimum viable product), an API approach works.

| API Pros | API Cons |
|---|---|
| Quick to build (1 week) | Hindi quality is inconsistent — GPT-4 sometimes mixes English |
| No ML team needed initially | Can't control teaching style or adapt to NCERT curriculum |
| Good for validating the idea | At scale (millions of students), API costs become enormous |
| | Data privacy: Student learning data goes to OpenAI |

**Step 3 — Long-term recommendation: Fine-tune open-source**

**Why:** An Indian edtech company serving millions of Hindi-medium students needs:
- Excellent Hindi (not GPT-4's sometimes-broken Hindi)
- NCERT-aligned explanations (specific to Indian curriculum)
- Cost efficiency at scale (millions of daily interactions)
- Data control (student performance data stays in India)

**Implementation plan:**
1. **Phase 1 (Month 1-2):** Launch MVP using GPT-4 API. Validate that students find AI tutoring useful. Collect student interaction data.
2. **Phase 2 (Month 3-5):** Fine-tune LLaMA/Mistral on collected interaction data + NCERT textbook content + Hindi educational material.
3. **Phase 3 (Month 6+):** Deploy fine-tuned model on own servers. Use the GPT-4 API data as a benchmark — ensure the fine-tuned model matches quality. Switch over.

**Cost comparison:**
- Phase 1 (API): Rs 3-5 lakh/month for 500K student interactions
- Phase 3 (Self-hosted): Rs 50,000-80,000/month for the same 500K interactions
- Annual saving after switching: Rs 25-50 lakh

**Decision:** Start with API for speed, plan to migrate to fine-tuned open-source for cost and quality. This is the most common real-world strategy — "prototype with API, scale with open-source."

---

*End of Session 4 Questions and Answers*
