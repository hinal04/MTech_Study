# Session 4: AI Ecosystem & Model Marketplaces (57 Slides)

> BITS Pilani — SS ZG662 | Module 1: Foundations of AI Systems

---

## 4.1 Foundation Models

### Definition

Large, general-purpose AI models trained on massive data that can be adapted for many downstream tasks.

### 6 Characteristics

1. **Massive data** — trained on internet-scale datasets
2. **General-purpose** — not built for one task
3. **Transfer learning** — knowledge transfers to new tasks via fine-tuning/prompting
4. **Emergent abilities** — capabilities that appear only at large scale (not explicitly trained for)
5. **Expensive to train** — millions of dollars (GPT-4 ~$100M)
6. **Accessible** — available via API or download

### Notable Foundation Models

| Model | Provider | Type | Access |
|---|---|---|---|
| GPT-4 / GPT-4o | OpenAI | Text + Vision | API only |
| Claude 3.5 | Anthropic | Text + Vision | API only |
| Gemini 1.5 | Google | Text + Vision + Audio | API + Cloud |
| LLaMA 3 | Meta | Text | Open-weight download |
| Mistral Large | Mistral AI | Text | Open-weight + API |
| Stable Diffusion | Stability AI | Image generation | Open-source |
| Whisper | OpenAI | Speech-to-text | Open-source |

---

## 4.2 Multi-Layer Ecosystem

```
Foundation Model Providers (OpenAI, Meta, Google, Anthropic)
        ↓
Cloud Platforms (AWS Bedrock, Google Vertex, Azure AI)
        ↓
Model Hubs (Hugging Face, NVIDIA NGC)
        ↓
Application Builders (enterprises building on top)
```

---

## 4.3 Model Selection

### The Enterprise Question

"Which model, deployed where, at what cost, with what governance, and what exit strategy?"

### 7 Selection Factors

Capability, cost, latency, privacy/compliance, support/SLA, lock-in risk, customizability.

### Model Discovery Channels

| Platform | What It Offers | Best For |
|---|---|---|
| **Hugging Face** | 500K+ models, open community, free | Discovery, open-source models |
| **AWS Bedrock** | Managed service, multiple models (Claude, LLaMA, Mistral) via single API | Enterprise AWS users, model switching without code change |
| **Google Model Garden** | Vertex AI integration, one-click deploy, fine-tuning | Google Cloud users |
| **Azure Model Catalog** | Only way for enterprise-compliant GPT-4, Azure AI Studio | Enterprise OpenAI access |
| **NVIDIA NGC** | GPU-optimized models/containers, 2-5x faster on NVIDIA hardware | Performance-critical deployments |

---

## 4.4 Open-Source Models

### 5 Benefits

1. No API cost (run on own infrastructure)
2. Data privacy (data never leaves your servers)
3. Full customization (fine-tune, modify architecture)
4. No vendor lock-in
5. Community improvements and auditing

### Model Families

**Proprietary:** GPT-4, Claude, Gemini
**Open-weight:** LLaMA, Mistral, Qwen, DeepSeek

### GPT vs Claude vs Gemini (Proprietary Comparison)

| Aspect | GPT-4 | Claude 3.5 | Gemini 1.5 |
|---|---|---|---|
| Strength | Broadest capability | Safety, long context | Multimodal, 1M token context |
| Access | API | API | API + Google Cloud |
| Best for | General-purpose | Enterprise safety-critical | Google ecosystem |

### LLaMA vs Mistral vs Qwen vs DeepSeek (Open Comparison)

| Aspect | LLaMA 3 | Mistral | Qwen | DeepSeek |
|---|---|---|---|---|
| Provider | Meta | Mistral AI | Alibaba | DeepSeek |
| Sizes | 8B, 70B | 7B, 8x7B (Mixtral) | 7B, 72B | 7B, 67B |
| Strength | Most popular | Efficient MoE | Multilingual | Cost-efficient |

### Small/Task-Specific Models

| Size Tier | Parameters | When to Use |
|---|---|---|
| Small | 1-7B | Single specific task, edge deployment, low latency |
| Medium | 7-30B | Multiple tasks, good quality/cost balance |
| Large | 30-100B+ | Complex reasoning, general-purpose |

**Why small models often win:** BERT (110M params) beats GPT-4 on sentiment classification — faster, cheaper, more accurate for that specific task.

### Open-Weight Variants

Same base model has multiple versions: original, fine-tuned (for chat/code/domain), quantized (compressed for speed), distilled (smaller student model).

**"LLaMA is not a procurement spec"** — specify which variant, which quantization, which fine-tune.

### Same Model, Different Channels

| Channel | Example | Cost | Control | Compliance |
|---|---|---|---|---|
| Direct download | Hugging Face | Free + GPU cost | Full | Self-managed |
| Managed API | AWS Bedrock | Per token | Medium | Cloud provider SLA |
| Cloud-hosted | Vertex AI | Per token + hosting | Medium | Enterprise compliance |

### License Terms

| License | Permissions | Restrictions |
|---|---|---|
| Apache 2.0 | Commercial use, modification, redistribution | None |
| Llama License | Free for most uses | Restricted for apps >700M MAU |
| Commercial/Proprietary | As per contract | No modification, no redistribution |
| GPL | Use and modify | Must open-source derivatives |

---

## 4.5 API vs Open Model

| Dimension | API (GPT-4, Claude) | Open Model (LLaMA, Mistral) |
|---|---|---|
| Setup time | Hours | Days-weeks |
| Data privacy | Data sent to provider | Data stays on your servers |
| Cost at scale | High (per-token) | Lower (GPU fixed cost) |
| Customization | Limited (prompt only) | Full (fine-tune, modify) |
| Maintenance | Zero (provider handles) | You handle updates, patches |
| Lock-in | High | Low |
| Compliance | Provider's terms | Your control |

**Pricing examples:** ~₹0.5-3 per 1K tokens (API) vs fixed GPU cost (self-hosted).

---

## 4.6 Inference-as-a-Service

| Provider | Differentiator |
|---|---|
| Fireworks AI | Fast inference, competitive pricing |
| Together AI | Wide model selection, fine-tuning support |
| Groq | Custom LPU hardware — ultra-fast inference |
| Replicate | Easy deployment, pay per second |

---

## 4.7 Benchmarks

| Benchmark | What It Measures |
|---|---|
| MMLU | General knowledge across 57 academic subjects |
| HumanEval | Code generation (does generated code actually work?) |
| MT-Bench | Multi-turn conversation quality |
| GSM8K | Math reasoning |
| TruthfulQA | Factual accuracy, hallucination resistance |

**Warning:** Benchmark scores don't always reflect real-world performance. Models can overfit to benchmarks. Always test on YOUR data.

### Quality Scorecard — What to Actually Measure

| Metric | What |
|---|---|
| Accuracy on YOUR data | Not benchmarks — test on your actual use case |
| Latency (p50/p95/p99) | Response time distribution |
| Throughput | Requests per second |
| Cost per request | Total cost including infra |
| Error rate | Failed/invalid responses |

---

## 4.8 Total AI Cost — Worked Example (1M requests)

| Approach | Compute | Engineering | Total |
|---|---|---|---|
| API (GPT-4) | ~$30K/month | Minimal | ~$30K |
| Managed Open (Bedrock + LLaMA) | ~$8K/month | Low | ~$8K |
| Self-Hosted Open | ~$5K/month | $3K (team time) | ~$8K |

### When to Self-Host (4 Conditions)

1. Data privacy/regulatory requirements
2. Cost optimization at scale
3. Need custom modifications (fine-tuning, architecture changes)
4. Regulatory requirements (data residency)

### Hidden Costs of Self-Hosting

GPU procurement, MLOps team salaries, monitoring infrastructure, security patching, model updates, on-call rotation.

---

## 4.9 Gateway/Router Pattern

Route requests to different models based on complexity/cost:

```
Simple queries → Cheap/fast model (Mistral 7B)
Complex queries → Expensive/powerful model (GPT-4)
```

**Result:** 78% cost reduction in one example while maintaining quality.

---

## 4.10 Prompting vs RAG vs Fine-Tuning

| Approach | When | Data | Cost | Time | Best For |
|---|---|---|---|---|---|
| Prompting | Quick start, generic task | None | API cost only | Minutes | Prototypes, simple tasks |
| RAG | Need answers from company docs | Documents (no training) | Low | Days | Knowledge Q&A, grounded answers |
| Fine-Tuning | Domain-specific style/knowledge | Thousands of examples | Medium-High | Days-weeks | Domain adaptation, custom style |

### Decision Flow

```
Can prompting solve it? → YES → Use prompting
                        → NO → Need factual grounding from docs? → YES → RAG
                                                                  → NO → Fine-tune
```

---

## 4.11 Build vs Buy

### Decision Framework

| Factor | Build | Buy/API |
|---|---|---|
| Core competitive advantage | ✅ Build | |
| Speed to market critical | | ✅ Buy |
| Data privacy critical | ✅ Build (self-host) | |
| Small team, no ML expertise | | ✅ Buy |
| Scale >1M requests/month | ✅ Build (cost) | |

### Comparison — Three Approaches

| Aspect | API | Fine-tune Open-Source | Build from Scratch |
|---|---|---|---|
| Time | Hours-days | Weeks | Months-years |
| Team | 1-2 developers | 2-5 ML engineers | 10-50 researchers |
| Cost | Per-token | GPU + engineering | Massive |
| Control | Low | High | Full |
| When | Prototype, non-core | Domain-specific, privacy | Core product, massive data |

**Real-world examples:** Razorpay (API for support), Hospital (fine-tune for medical), Swiggy (build for ETA), Google (build from scratch for search).

### Enterprise Procurement Scorecard

| Factor | Weight |
|---|---|
| Capability | 30% |
| Cost | 25% |
| Latency | 15% |
| Privacy/Compliance | 15% |
| Support/SLA | 10% |
| Lock-in Risk | 5% |

Score each model candidate on 1-5 per factor. Weighted total determines winner.

---

## 4.12 Enterprise Model Lifecycle

```
Select → Integrate → Monitor → Evaluate → Replace/Upgrade → (repeat)
```

**Version control for:** Prompts, fine-tuning data, model versions, evaluation results.

**Key insight:** The model you pick today will likely be replaced within 6-12 months. Design architecture (gateway pattern, abstraction layers) to make switching easy.

---
