# Session 11: Transfer Learning

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Dr. Minati Rath
>
> **References:** Class Slides, Contact Session 11
>
> **Contact Session:** 11 (Module 6: Advanced Learning Strategies)

---

## Table of Contents

- [11.1 Why Transfer Learning](#111-why-transfer-learning)
- [11.2 Source and Target Domains](#112-source-and-target-domains)
- [11.3 Feature Extraction vs. Fine-Tuning](#113-feature-extraction-vs-fine-tuning)
- [11.4 Partial and Full Fine-Tuning](#114-partial-and-full-fine-tuning)
- [11.5 Learning Rates and Overfitting in Transfer](#115-learning-rates-and-overfitting-in-transfer)
- [11.6 Parameter-Efficient Fine-Tuning (PEFT)](#116-parameter-efficient-fine-tuning-peft)
- [11.7 Low-Rank Adaptation (LoRA)](#117-low-rank-adaptation-lora)
- [11.8 Domain Shift and Domain Adaptation](#118-domain-shift-and-domain-adaptation)
- [11.9 Transfer Learning in Enterprise](#119-transfer-learning-in-enterprise)
- [11.10 Key Takeaways](#1110-key-takeaways)

---

## Learning Objectives

At the end of this session, you should be able to:

- **Understand the motivation for transfer learning** — Why reuse pretrained models rather than training from scratch
- **Distinguish between feature extraction and fine-tuning** — When to freeze vs. unfreeze layers
- **Apply partial and full fine-tuning strategies** — Balance adaptation capacity against training cost
- **Implement parameter-efficient methods** — Adapt models with minimal parameter updates
- **Detect and address domain shift** — Adapt models when deployment domain differs from training
- **Design enterprise transfer-learning systems** — Integrate transfer learning with monitoring and governance

---

## 11.1 Why Transfer Learning

### The Cost of Training from Scratch

Training a deep learning model from scratch requires:
- **Sufficient data**: Thousands to millions of labeled examples
- **Computational resources**: GPUs for weeks or months
- **Training time**: Potentially weeks to converge
- **Expertise**: Understanding of architecture and tuning

For many organizations, this is prohibitive.

### Pretrained Models as Knowledge Transfer

**Pretrained model** already contains representations learned from large dataset (e.g., ImageNet for vision, Wikipedia for NLP).

These representations capture general patterns:
- Low-level features (edges, textures in images)
- Mid-level features (shapes, objects)
- High-level features (semantic concepts)

**Transfer learning reuses these representations for new prediction task.**

### Effectiveness Depends on Relevance

Transfer learning's effectiveness depends on **relevance of pretrained knowledge to target problem**.

**Strong transfer**:
- Pretrained on ImageNet (millions of natural images)
- Target task: Identify manufacturing defects in product images
- Visual primitives overlap; transfer works well

**Weak transfer**:
- Pretrained on ImageNet (natural photos)
- Target task: Classify X-ray images for medical diagnosis
- Visual characteristics differ significantly; transfer less effective

**Enterprise example**: Manufacturing company reuses pretrained image model to identify defects, rather than collecting and labeling thousands of defective products from scratch.

---

## 11.2 Source and Target Domains

### Defining Source and Target

**Source domain**: Domain represented by original training data (what model was pretrained on)

**Target domain**: Domain of intended application (where we want to deploy)

**Source task**: Original learning objective (e.g., classify 1000 ImageNet categories)

**Target task**: New prediction objective (e.g., classify 10 manufacturing defect types)

### Domain vs. Task Mismatch

Transfer effectiveness depends on two dimensions:

| Scenario | Source-Target Similarity | Transfer Success |
|----------|--------------------------|------------------|
| Same domain, same task | Very high | Excellent (usually direct reuse) |
| Same domain, different task | High | Good (representations transfer naturally) |
| Different domain, same task | Medium | Moderate (task overlap helps) |
| Different domain, different task | Low | Limited (requires significant adaptation) |

**Key insight**: A successful transfer requires learned representations to be **useful for the target problem.**

---

## 11.3 Feature Extraction vs. Fine-Tuning

### Feature Extraction: Frozen Backbone

**Feature extraction** keeps pretrained backbone frozen; only new prediction head trained.

**Process:**
```
Input → Frozen Backbone → Feature Vector → New Classifier (trainable) → Output
```

**Advantages:**
- Low training cost (only classifier parameters updated)
- Fast training (fewer parameters to update)
- Suitable when pretrained features sufficient
- Lower overfitting risk on small target datasets
- Computationally efficient deployment

**Disadvantages:**
- Limited adaptation capacity (representations fixed)
- May underfit if target task requires very different features
- Cannot capture target-domain-specific patterns

**When to use:**
- Limited target-domain labels available
- Target domain similar to source domain
- Computational resources constrained
- Quick prototyping needed

### Fine-Tuning: Updating Pretrained Parameters

**Fine-tuning** allows selected or all pretrained layers to update using target-task data.

**Process:**
```
Input → Backbone (trainable) → Feature Vector → Classifier (trainable) → Output
```

**Advantages:**
- Higher adaptation capacity (representations update)
- Can adapt to target domain–specific patterns
- Often achieves better performance than frozen backbone

**Disadvantages:**
- Higher training cost (more parameters updated)
- Longer training time
- Greater risk of overfitting on small datasets
- Requires careful regularization

**When to use:**
- Substantial target-domain labeled data available
- Target domain significantly different from source
- Adaptation capacity needed to improve performance
- Resources available for extended training

### Key Distinction

Both start with pretrained model. Main difference: **whether pretrained parameters allowed to change during training.**

---

## 11.4 Partial and Full Fine-Tuning

### Layer Freezing Strategy

Neural networks organized in layers. Layers closer to input capture low-level features; deeper layers capture high-level semantics.

**Strategy 1: Feature Extraction**
```
Freeze all backbone layers
Train only new classifier
Trainable layers: 1 (classifier only)
```

**Strategy 2: Partial Fine-Tuning**
```
Freeze early layers (low-level features generally transferable)
Unfreeze final backbone block (mid-to-high level features)
Train classifier and final block
Trainable layers: 2 (final block + classifier)
```

**Strategy 3: Full Fine-Tuning**
```
Unfreeze all layers
All parameters updatable
Trainable layers: all (entire network)
```

### Balancing Adaptation and Cost

**Partial fine-tuning** balances:
- **Adaptation capacity**: Can update high-level features for target task
- **Computational cost**: Fewer trainable parameters than full fine-tuning
- **Generalization**: Early layers kept fixed prevent overfitting

**Example: ResNet-18 on CIFAR-10**
- Feature extraction: Update ~1,000 parameters (final classifier)
- Partial fine-tuning: Update ~130,000 parameters (final block + classifier)
- Full fine-tuning: Update ~11,000,000 parameters (entire network)

Trade-off visible in training time and convergence:
- Feature extraction: Fastest training, may underfit
- Partial: Balanced training time and performance
- Full: Longest training, best performance if data and resources permit

### Practical Guidance

**Choose based on:**
1. **Target data size**: More data supports full fine-tuning
2. **Domain similarity**: Similar domains work with feature extraction; distant domains need more unfreezing
3. **Available resources**: Limited compute suggests partial fine-tuning
4. **Performance requirements**: Compare validation performance across approaches

---

## 11.5 Learning Rates and Overfitting in Transfer

### Learning Rate in Transfer Learning

Learning rate controls size of parameter updates during optimization.

**Two competing considerations:**

**Preserving pretrained knowledge:**
- High learning rate can damage useful pretrained representations
- Rapid updates may erase learned patterns
- → Use small learning rate for pretrained layers

**Enabling adaptation:**
- Very low rate may prevent meaningful adaptation
- Learning slows to irrelevant levels
- → Use appropriate (moderate) rate for new classifier

### Discriminative Learning Rates

**Solution**: Use different learning rates for different layer groups.

```
Learning rate for new classifier: 0.001 (normal)
Learning rate for pretrained layers: 0.0001 (10× lower)
```

Pretrained parameters update slowly, preserving useful knowledge. New classifier updates faster, learning task-specific patterns.

### Overfitting Risk

Large model trained on limited target data easily overfits:
- High training accuracy
- Weak validation and test performance
- Model memorizes training examples rather than generalizing

**Overfitting risk highest with full fine-tuning on small datasets.**

### Mitigation Strategies

1. **Early stopping**: Stop training when validation performance plateaus
2. **Weight decay (L2 regularization)**: Penalize large parameter values, prefer smaller updates
3. **Data augmentation**: Artificially increase training dataset size (image rotations, crops, etc.)
4. **Selective layer unfreezing**: Freeze more layers, unfreeze fewer, reducing trainable parameters
5. **Dropout**: Randomly disable neurons during training, forcing more robust learning

### Practical Approach

Start with feature extraction (minimal overfitting risk). If performance insufficient:
1. Add L2 regularization
2. Selectively unfreeze layers (final layer first, then final block)
3. Use smaller learning rate for unfrozen pretrained layers
4. Monitor validation performance to detect overfitting

---

## 11.6 Parameter-Efficient Fine-Tuning (PEFT)

### Motivation for PEFT

**Challenge**: Large pretrained models (billions of parameters) require substantial memory and computation when all parameters updated.

**Scale problem:**
- Storing separate full model copy for every task increases storage
- Fine-tuning all parameters for each new task expensive
- Deployment infrastructure complex when managing multiple full models

### PEFT Concept

**Parameter-Efficient Fine-Tuning (PEFT)** adapts model by training **only small subset of parameters** or **adding compact trainable components**.

Pretrained model's main parameters commonly **kept frozen**.

**Goal**: Reduce adaptation costs while retaining sufficient task-specific learning capacity.

### PEFT Categories

1. **Adapter methods**: Insert small trainable modules between frozen layers
   - Original layers frozen
   - Small dense layers added, trained on downstream task
   - Negligible overhead; can be removed from deployment

2. **Prompt/prefix methods**: Prepend learnable embeddings (soft prompts)
   - For language models, learn small prompt vectors
   - Frozen model processes extended input
   - Minimal trainable parameters

3. **Low-rank methods**: Learn low-rank updates to weight matrices
   - Original weights frozen
   - Small matrices added representing weight updates
   - Dramatic parameter reduction

### When to Use PEFT

- Multiple tasks with shared backbone (efficient multitask learning)
- Storage-constrained deployments (edge devices, mobile)
- Need to adapt model frequently (new tasks regularly)
- Large models with parameter budgets
- When speed and efficiency critical

### When Full Fine-Tuning Better

- Single task, ample data and resources
- Performance more important than efficiency
- Model relatively small (fine-tuning costs acceptable)
- High accuracy requirements

---

## 11.7 Low-Rank Adaptation (LoRA)

### LoRA Concept

**LoRA** (Low-Rank Adaptation) freezes pretrained weight matrix and learns low-rank update to adapt it.

### Mathematical Formulation

Let **W₀** = original weight matrix

Adapted matrix: **W = W₀ + ΔW**

LoRA represents update as: **ΔW = BA**

- **A** and **B** are trainable matrices
- **W₀** remains frozen
- Rank (*r*) small relative to original matrix dimensions

### Parameter Reduction Example

Consider linear layer with weight matrix 1000 × 1000:

**Full fine-tuning:**
- Update 1,000,000 weights

**LoRA with rank r = 8:**
- Matrix A: 8 × 1000 = 8,000 parameters
- Matrix B: 1000 × 8 = 8,000 parameters
- Total: 16,000 parameters

**Reduction**: 16,000 / 1,000,000 = **1.6% of original**

### Why Low-Rank Works

Assumption: Weight update **ΔW** has low intrinsic dimensionality.

Most task-specific adaptation concentrated in few directions of weight space. Full matrix adaptation unnecessary.

Empirically supported: Models can achieve 90-95% of full fine-tuning performance with 1-10% of trainable parameters.

### LoRA Implementation

Forward pass:
```
y = W₀x + BAx
  = (W₀ + BA)x
```

Training updates only **A** and **B**. Gradients for **W₀** are zero (frozen).

Deployment option 1: Store **B, A** separately; apply update at inference
Deployment option 2: Merge into single weight matrix W = W₀ + BA (no inference overhead)

### LoRA Trade-offs

**Advantages:**
- Dramatic parameter reduction (1-10% of full)
- Fast training
- Low memory overhead
- Multiple LoRA matrices can be combined (multitask scenarios)

**Disadvantages:**
- Slightly lower maximum performance than full fine-tuning
- Requires rank selection (hyperparameter tuning)
- Less studied than standard fine-tuning
- May be insufficient for very different target domains

### Practical LoRA Design

**Rank selection:**
- Start with rank 8 or 16
- Increase if target task requires more adaptation
- Relationship is empirical, not theoretical

**Application:**
- Common in large language models (LLMs) for efficiency
- Increasingly used in vision transfer tasks
- Particularly effective when combining multiple task adaptations

---

## 11.8 Domain Shift and Domain Adaptation

### What Is Domain Shift?

**Domain shift** occurs when data encountered during deployment **differs from training data distribution**.

Changes may arise from:
- Lighting conditions (photos taken indoors vs. outdoors)
- Camera system changes (upgrade from old to new camera)
- Operating conditions (factory equipment wear)
- Customer behavior (preferences shift over time)
- Data collection procedures (new annotators, different protocols)

### Impact on Model Performance

Model can perform well on original test set but **poorly under changed conditions**.

Domain shift may involve changes in:
- **Covariate shift**: Input distribution changes, output stays related
- **Label shift**: Class proportions change
- **Concept drift**: Relationship between inputs and outputs changes

### Enterprise Example

Factory's defect detection system trained on images from Camera V1. Factory upgrades to Camera V2 (different color grading, resolution).

Model performance drops 15-20% without retraining. Camera change alone causes failure, despite labels remaining valid.

### Detecting Domain Shift

**Signals of domain shift:**
1. **Sudden performance drop** in production
2. **Statistical tests** on input feature distributions (Kolmogorov-Smirnov test, Maximum Mean Discrepancy)
3. **Per-class performance divergence** (model performs well on some classes, poorly on others)
4. **Confidence distribution changes** (model increasingly uncertain)
5. **Human feedback** (increased customer complaints)

### Demonstrating Domain Shift: Darkened Images

Experimental setup:
- Train model on clean CIFAR-10 images
- Test on original clean images (baseline performance)
- Test on artificially darkened images (shifted distribution)

Results: Performance drops 20-30% on darkened images despite identical label set.

Demonstrates models' **sensitivity to input distribution changes**.

### Supervised Domain Adaptation

**Approach**: Use small labeled target-domain dataset to adapt model.

**Process:**
1. Keep pretrained backbone frozen
2. Retrain classifier on target-domain examples
3. Evaluate on held-out target-domain test set
4. Compare performance before and after adaptation

**Advantages:**
- Simple to implement
- Usually improves performance if target labels available
- Low additional training cost

**Limitations:**
- Requires labeled target data
- Limited adaptation (only classifier updates)
- May not address deep distribution shifts

**When to use:**
- Target-domain labels obtainable
- Shift moderate (not fundamental difference)
- Quick adaptation needed

### Unsupervised Domain Adaptation

**Approach**: Align representations using unlabeled target data and labeled source data.

**Methods:**
- Feature distribution alignment (Maximum Mean Discrepancy)
- Domain-adversarial learning (discriminator distinguishes source vs. target features)
- Pseudo-labeling (model labels unlabeled target data, selectively retrains)
- Self-supervised pretraining on target domain

**Advantages:**
- Works without target labels
- Can address deeper distribution shifts
- Leverages large unlabeled datasets

**Limitations:**
- More complex to implement
- Performance often lower than supervised adaptation
- Requires careful hyperparameter tuning

### Semi-Supervised Domain Adaptation

**Approach**: Combine small labeled target dataset with large unlabeled target dataset.

**Methods:**
- Supervised adaptation + semi-supervised learning
- Mix target labels with pseudo-labels from model predictions
- Consistency regularization on unlabeled data

**Balance**: Leverages available labels while using abundant unlabeled data.

---

## 11.9 Transfer Learning in Enterprise

### Case Study: Manufacturing Across Multiple Factories

**Scenario:**
Manufacturing company uses pretrained vision model to identify product defects.

- Factory A: Model performs well (80% accuracy)
- Factory B: Model performs poorly (55% accuracy) — new factory, large labeled dataset available
- Factory C: Model performs poorly (52% accuracy) — different camera system, few labeled examples

### Selecting Adaptation Strategy by Scenario

**Factory A (No intervention needed):**
- Establish baseline performance
- Monitor drift over time
- Retrain quarterly on accumulated data

**Factory B (Supervised adaptation):**
- Abundant labeled target data (thousands of examples)
- Use full fine-tuning or partial fine-tuning
- Expect 20-25% improvement to reach 75-80% accuracy
- Cost-benefit: High ROI given data availability

**Factory C (PEFT or domain adaptation):**
- Few labeled examples (hundreds)
- Different camera system (significant domain shift)
- Option 1: PEFT (LoRA) for low-cost adaptation
- Option 2: Unsupervised domain adaptation using unlabeled images
- Option 3: Hybrid (label subset of images + pseudo-labeling remainder)

### Enterprise Decision Framework

**Before adapting, define:**
1. Current performance baseline (accuracy, precision, recall)
2. Target performance (acceptable for deployment)
3. Available resources (labeled data, computation, time)
4. Cost of poor performance (business impact)
5. Adaptation cost budget

**Choose approach:**
- Feature extraction if domain similar, computation limited
- Full fine-tuning if substantial labeled target data, strong accuracy required
- PEFT if efficiency critical, multiple tasks, frequent retraining
- Domain adaptation if domain shift significant, labels limited

### Monitoring After Deployment

Continuous monitoring essential:
- Performance metrics (accuracy, precision, recall, F1)
- Data drift (input distribution changes)
- Concept drift (relationship changes)
- Business metrics (revenue, customer satisfaction)

Trigger retraining if:
- Performance drops below threshold
- Significant drift detected
- New product variants introduced
- Camera system or operating conditions change

---

## 11.10 Key Takeaways

1. **Transfer learning reuses pretrained knowledge** — Avoid training from scratch when relevant pretrained models exist

2. **Feature extraction vs. fine-tuning trade-off** — Choose based on domain similarity, data availability, and resources

3. **Partial fine-tuning balances cost and adaptation** — Often better than both feature extraction and full fine-tuning in practice

4. **Learning rates matter** — Use discriminative rates: lower for pretrained layers, normal for new layers

5. **Parameter-efficient methods enable scale** — LoRA and similar techniques enable efficient multitask and personalization scenarios

6. **Domain shift degrades performance** — Monitor and adapt when deployment domain differs from training

7. **Supervised adaptation simple and effective** — When target labels available, retraining classifier often sufficient

8. **Unsupervised adaptation needed for label scarcity** — Alignment methods work without target labels but more complex

9. **Enterprise systems require governance** — Version models, track performance, define adaptation triggers

10. **Regular retraining prevents drift** — Production transfer-learning systems need continuous monitoring and periodic retraining

---
