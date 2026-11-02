# Post-MidSem Practice Questions - Sessions 9, 11, 12
## SS ZG662 - Post Semester Exam Preparation
## Learning Systems, Transfer Learning, and Model Evaluation

---

## Q1. Learning Mechanism Selection for E-Commerce Recommendation System [9 Marks]

An e-commerce company has three AI problems to solve:

**Problem A**: Product recommendation to users based on browsing history
- Data available: 100 million user-product interactions (clicks, purchases, dwell time)
- Business need: Rank products to show most relevant items
- Learning signal: User clicks, purchases, cart additions (immediate feedback)

**Problem B**: Inventory demand forecasting for warehouses
- Data available: 5 years of sales history, external weather/event data
- Business need: Predict demand to optimize stock levels
- Learning signal: Actual sales 2 weeks later
- Complication: Forecast drives restocking decisions, which affects future sales

**Problem C**: Fraud detection in payment transactions
- Data available: Transaction history, merchant info, user profiles
- Business need: Flag suspicious transactions before approval
- Learning signal: Fraud confirmed 30-90 days later via chargeback or investigation
- Complication: Declined transactions never investigated (selective labels)

### Task:
For each problem, determine:

a) **Which learning mechanism is most appropriate** (Supervised, Unsupervised, Reinforcement, or Hybrid)? [3 marks total - 1 per problem]

b) **Justify your choice by specifying**: [6 marks total - 2 per problem]
   - What learning signal is available?
   - When does feedback become observable?
   - How does the system's decision influence future data?
   - What counterfactual challenges exist?
   - Why alternative mechanisms are less suitable?

### Answer:

---

**Problem A: Product Recommendation — Supervised Learning (with Ranking Objective) [3 marks]**

**Learning signal available:**
- User interactions: clicks (implicit positive), purchases (strong positive), dwell time (interest), non-clicks (implicit negative)
- Immediate feedback (user response observable within session)

**Mechanism choice: Supervised Learning with learning-to-rank objective**

**Justification [2 marks]:**

1. **Feedback availability**: Click/purchase feedback is immediate and abundant. Model can learn from millions of labeled examples (user saw result, clicked or didn't).

2. **Learning objective**: Not binary classification ("Is this product relevant?") but **ranking** ("Which products should appear first?"). Supervised learning naturally supports this through relative pairwise or listwise objectives.

3. **Why not unsupervised?**: Millions of labeled interactions available; unsupervised learning unnecessary. Explicit feedback signal (clicks) stronger than latent patterns.

4. **Why not reinforcement?**: Single-step decision (which products to show), not sequential optimization over time. RL unnecessary complexity.

5. **Counterfactual challenge**: Position bias (top positions clicked more regardless of relevance). **Solution**: Use randomized rankings during training to collect unbiased feedback, or use offline ranking learning methods.

6. **Selective labels**: Not all products shown to all users; feedback only for exposed products. **Solution**: Use exposure-aware learning or inverse propensity weighting to correct for exposure bias.

---

**Problem B: Demand Forecasting — Supervised Regression with Sequential Considerations [3 marks]**

**Learning signal available:**
- Historical sales (actual demand) observed after forecast period
- Feedback available but **delayed** (forecast for week 1, demand observed end of week 2)
- Complication: Forecast → restocking decision → future sales (feedback loop)

**Mechanism choice: Supervised Learning (Regression), with closed-loop monitoring for feedback**

**Justification [2 marks]:**

1. **Feedback availability**: Demand is observable (actual sales), making supervised regression appropriate. Delayed feedback manageable (2-week window predictable).

2. **Why supervised not unsupervised?**: Clear target variable (demand). Unsupervised clustering of time series patterns less useful than predicting actual demand.

3. **Why not reinforcement?**: Could frame as sequential inventory optimization, but simpler supervised approach sufficient. Forecasting → Business logic (ordering rules) → Outcomes. RL adds complexity without need.

4. **Feedback loop problem**: Model's forecast influences restocking → affects future sales. **Example**: Forecast high demand → restock heavily → high stock availability → sales higher than would naturally occur. Creates circular causality.

   **Solution**: Separate forecasting from inventory optimization. Train forecasting model on historical unbiased demand. Use business logic (restocking policy) as separate layer. Monitor for drift.

5. **Counterfactual**: Cannot know true demand if product went out of stock. Demand censored at stock level. **Solution**: Use survival analysis or censored regression to account for stockout events.

---

**Problem C: Fraud Detection — Supervised Learning with Offline Reinforcement Learning Considerations [3 marks]**

**Learning signal available:**
- Fraud labels obtained 30-90 days later via investigation/chargeback
- Delayed feedback (observation window before decision window)
- Complication: Selective labels (only investigated transactions get labeled)

**Mechanism choice: Supervised Learning (Classification) with policy-aware training**

**Justification [2 marks]:**

1. **Why supervised?**: Clear binary outcome (fraud confirmed or not). Abundant historical data with confirmed labels. Standard classification appropriate.

2. **Delayed feedback challenge**: 30-90 day delay acceptable for fraud (future prediction, not real-time). Model trained on historical outcomes; deployed on current transactions.

3. **Selective labels problem**: Transactions declined by model never reach investigators. **Example**: Model blocks 90% of flagged transactions; only 10% approved and later investigated. Training data biased toward approved transactions. **Solution**: 
   - Use only confirmed labels (exclude "unconfirmed" as ambiguous)
   - Track decision policy version; when policy changes, retraining distribution changes
   - Apply inverse propensity weighting (upweight underrepresented actions)
   - Consider offline RL methods (learn policy improvement from logged outcomes without live deployment)

4. **Why not reinforcement?**: Fraud not a sequential decision (each transaction independent). Supervised classification sufficient.

5. **Counterfactual**: Never observe what would happen if declined transaction approved. **Solution**: Careful monitoring post-deployment; slowly increase approval rate on borderline cases to learn their true outcomes.

---

## Q2. Transfer Learning Strategy for Multi-Factory Defect Detection [10 Marks]

A manufacturing company trains a defect detection model on Camera V1 at Factory A (10,000 labeled defective/non-defective images). The model achieves 92% accuracy on Factory A test set (ImageNet-pretrained ResNet-50, fine-tuned).

Now deploying to three new factories:
- **Factory B**: Has Camera V1 (same as A), but different product line (0 labeled images available)
- **Factory C**: Has Camera V2 (different color/resolution), same product as A (500 labeled images available)
- **Factory D**: Has Camera V3 (significantly different imaging technology), different product (50 labeled images available)

### Task:
For each factory, design a transfer-learning strategy:

a) **Strategy selection** [2 marks per factory]
b) **Justification** [2 marks per factory]
c) **Specific implementation** (layer unfreezing, learning rates, expected performance) [1 mark per factory]

### Answer:

---

**Factory B: Feature Extraction with Possible Retraining [5 marks]**

**Strategy:** Feature extraction initially; full fine-tuning if performance insufficient

**Justification [2 marks]:**
- Domain similarity: Same camera, same pretrained knowledge applicable
- Data scarcity: 0 labeled images → cannot fine-tune (risk of severe overfitting)
- Transfer potential: High (camera identical; visual primitives transfer)
- **Risk**: Different product line may require different defects to detect

**Implementation [1 mark]:**
```
Step 1: Use frozen ResNet-50 backbone (trained on Factory A)
Step 2: Train new classifier on... wait, no labels available
Step 3: Option A — Use inference on Factory B images; manually label ~500
Step 4: Option B — Use zero-shot: Apply Factory A model directly, monitor performance
Step 5: If performance >80%, deploy with monitoring
Step 6: If performance <80%, collect labels (500-1000 images) and fine-tune
```

**Expected performance**: 85-90% if product defects similar; may drop to 75-80% if products significantly different

---

**Factory C: Partial Fine-Tuning with Domain Adaptation [5 marks]**

**Strategy:** Partial fine-tuning (final ResNet block + classifier) with careful learning rates

**Justification [2 marks]:**
- Domain shift present: Camera V2 different color/resolution (moderate shift)
- Data availability: 500 labeled images sufficient for partial fine-tuning
- Transfer potential: Moderate (same product helps; camera difference hurts)
- Learning rate management critical to preserve Factory A knowledge while adapting to Camera V2

**Implementation [1 mark]:**
```
Step 1: Freeze ResNet backbone layers except final block
Step 2: Learning rates:
  - Final ResNet block: 0.0001 (low, preserve pretrained knowledge)
  - Classifier head: 0.001 (10× higher, learn new task)
Step 3: Use data augmentation (brightness, contrast variation) to simulate Camera V2 differences
Step 4: Early stopping on validation set (50 images held out from 500)
Step 5: Evaluate on remaining 450 images
Step 6: If performance >88%, deploy; if 80-88%, collect more data; if <80%, consider full fine-tuning
```

**Expected performance**: 86-91% (slight degradation from Factory A due to camera shift, but offset by targeted fine-tuning)

---

**Factory D: LoRA-Based PEFT with Unsupervised Domain Adaptation [5 marks]**

**Strategy:** Low-Rank Adaptation (LoRA) on classifier + unsupervised domain adaptation

**Justification [2 marks]:**
- Severe domain shift: Camera V3 fundamentally different + different product
- Extreme data scarcity: Only 50 labeled images (full fine-tuning will severely overfit)
- PEFT motivated: LoRA reduces trainable parameters, mitigates overfitting risk
- Unsupervised adaptation: Use unlabeled Factory D images (if available) to align representations

**Implementation [1 mark]:**
```
Step 1: Apply LoRA to classifier layers (freeze backbone)
  - Rank r = 4 (very conservative given 50 examples)
  - Trainable parameters: ~2,000 (vs. 1M+ for full fine-tuning)
Step 2: Train on 50 labeled images:
  - Learning rate: 0.001 (standard for LoRA)
  - Batch size: 16-32 (small due to limited data)
  - Early stopping after 5 epochs (prevent overfitting)
Step 3: Domain adaptation (if unlabeled Factory D images available):
  - Use self-supervised learning (rotation prediction, masked image modeling)
  - OR use test-time adaptation (gradually adapt model on unlabeled Factory D data)
Step 4: Expected baseline: 60-70% (severe domain shift + limited data)
Step 5: After domain adaptation: 70-75% (improvement from alignment)
Step 6: Consider collecting more labels (200-300) if business-critical
```

**Expected performance**: 65-75% (significant degradation, but best possible given constraints)

---

**Summary Table:**

| Factory | Strategy | Trainable Params | Expected Accuracy | Key Challenge |
|---------|----------|------------------|-------------------|----------------|
| B | Feature Extraction | Classifier only | 85-90% | No labels; manual labeling needed |
| C | Partial Fine-Tuning | Final block + classifier | 86-91% | Camera domain shift |
| D | LoRA + Domain Adaptation | ~2K (LoRA) | 65-75% | Severe shift + extreme scarcity |

---

## Q3. Defect Rate Classification Model Evaluation [10 Marks]

A manufacturing company builds a defect classifier: **P(Defective | image)**

Dataset: 10,000 images (9,500 non-defective, 500 defective)

Model A (Random Forest):
- Accuracy: 96.2%
- Precision: 78%
- Recall: 65%
- ROC-AUC: 0.82

Model B (ResNet-50 fine-tuned):
- Accuracy: 95.8%
- Precision: 72%
- Recall: 82%
- ROC-AUC: 0.88

Business context:
- Cost of missing a defect (False Negative): €500 (product shipped to customer)
- Cost of incorrectly flagging a good item (False Positive): €20 (additional inspection)
- Inspection capacity: Can inspect 200 items per day

### Task:

a) **Compare the two models holistically**. Which should be deployed and why? [4 marks]

b) **Determine the optimal threshold** for the deployed model given business costs. [3 marks]

c) **Design an evaluation and monitoring plan** for production deployment. [3 marks]

### Answer:

---

**a) Model Comparison [4 marks]**

**Metric Analysis [2 marks]:**

Both accuracy nearly identical (96.2% vs. 95.8%), but fundamentally different profiles:

- **Model A**: Higher precision (78%), lower recall (65%)
  - False Positives: 22% of positives are wrong (lots of good items flagged)
  - False Negatives: 35% of defects missed (dangerous)
  - AUC: 0.82 (moderate ranking quality)

- **Model B**: Lower precision (72%), higher recall (82%)
  - False Positives: 28% of positives are wrong (more inspections needed)
  - False Negatives: Only 18% of defects missed (safer)
  - AUC: 0.88 (better ranking)

**Business Cost Analysis [2 marks]:**

Expected cost per 10,000 images (estimate false positives/negatives):

**Model A:**
- TP: 0.65 × 500 = 325 defects caught
- FN: 0.35 × 500 = 175 defects missed → Cost: 175 × €500 = €87,500
- FP: 0.22 × 9,500 ≈ 2,090 good items flagged → Cost: 2,090 × €20 = €41,800
- **Total cost: €129,300**

**Model B:**
- TP: 0.82 × 500 = 410 defects caught
- FN: 0.18 × 500 = 90 defects missed → Cost: 90 × €500 = €45,000
- FP: 0.28 × 9,500 ≈ 2,660 good items flagged → Cost: 2,660 × €20 = €53,200
- **Total cost: €98,200**

**Conclusion**: Model B **€31,100 cheaper** per 10,000 items despite lower precision. **Deploy Model B.**

---

**b) Optimal Threshold for Model B [3 marks]**

**Business cost threshold formula [1 mark]:**
```
Optimal threshold = Cost_FN / (Cost_FP + Cost_FN)
                 = 500 / (20 + 500)
                 = 500 / 520
                 ≈ 0.96
```

Predict defective if P(Defective) > 0.96

**Constraint consideration [1 mark]:**

Inspection capacity: 200 items/day. Model B generates ~2,660 flagged items per 10,000 (26.6%).

Assuming 10,000 images processed over 13-14 days:
- Daily volume: ~700 images
- Daily flagged: ~186 items (within capacity ✓)

**Threshold flexibility [1 mark]:**

Pure cost formula suggests 0.96. In practice, could adjust:
- More conservative (higher threshold, e.g., 0.98): Fewer inspections, miss more defects
- More aggressive (lower threshold, e.g., 0.90): More inspections, catch more defects

**Recommendation**: Use threshold 0.96 based on cost optimization, monitor queue length. If queue builds (>250/day), raise threshold; if queue short (<150/day), lower threshold.

---

**c) Production Evaluation and Monitoring Plan [3 marks]**

**Phase 1: Baseline Establishment [0.75 marks]**
- Deploy Model B with threshold 0.96 to 10% of production (canary deployment)
- Run for 1 week, measure:
  - Actual defect rate (ground truth from customer returns, field failures)
  - Precision: Of flagged items, fraction actually defective
  - Recall: Of actual defects, fraction flagged
  - Business impact: Cost per defect, customer satisfaction
- Compare against baseline (manual inspection or previous model)

**Phase 2: Continuous Monitoring [1.25 marks]**

Daily metrics:
- **Accuracy**: Count flagged items later confirmed defective vs. falsely flagged
- **Precision/Recall**: Track on rolling basis (weekly updates)
- **Calibration**: Do predicted probabilities match actual defect rates? (Defects predicted 0.80 should ~80% be actually defective)
- **Data drift**: Monitor image feature distributions (color, texture, resolution). Alert if distribution shifts >10% KL-divergence
- **Concept drift**: Monitor defect types. If new defect pattern emerges, model may degrade

Thresholds for alerts:
- Precision drops <65% → investigate data quality
- Recall drops <75% → retraining needed
- Significant drift detected → retrain with recent data

**Phase 3: Error Analysis and Retraining [1 mark]**

Monthly deep-dive:
- Hard-case mining: Collect 20-30 false positives/false negatives
- Root cause analysis: Are errors due to new defect types? Image quality? Equipment change?
- Retraining trigger: If error patterns detected, collect 500-1000 new labeled examples, retrain
- Threshold adjustment: If business constraints change (inspection capacity, cost), reoptimize threshold

Scheduled retraining: Quarterly with accumulated new data

---

## Q4. Learning Mechanism and Validation Strategy Design - Warehouse Routing [9 Marks]

A warehouse robotic fleet uses a learned policy to decide routing (which aisle to visit next, when to recharge, where to park).

Current system: Rule-based heuristics + manual optimization. Result: 70% average task completion rate, 3.2 hours average fulfillment time.

Proposed AI system: Learn routing policy from historical operation logs and live interaction.

### Task:

a) **Why is reinforcement learning appropriate here?** (Not supervised or unsupervised) [2 marks]

b) **Design a learning framework** (what data to collect, reward structure, training approach) [4 marks]

c) **Propose a validation strategy** that prevents data leakage and ensures the AI system actually improves business metrics [3 marks]

### Answer:

---

**a) Why Reinforcement Learning [2 marks]**

**Sequential decision structure [1 mark]:**
Routing is inherently sequential:
```
State_t (current location, pending orders, battery, congestion) 
  → Action_t (next aisle to visit)
  → State_(t+1) (new location, updated state)
```

Each action changes environment. Next state depends on current action. Cannot solve with single prediction (supervised).

**Counterfactual nature [1 mark]:**
Historical data shows what happened with chosen actions. We don't know what would have happened with alternative routes. RL can estimate value of unexplored actions through exploration or offline policy learning.

**Why not supervised?** Supervised treats routing as independent predictions. Ignores sequential dependencies, long-term consequences of decisions.

**Why not unsupervised?** No inherent clusters or patterns to discover. Business needs optimal actions, not discovered structure.

---

**b) Learning Framework Design [4 marks]**

**1. State Representation [1 mark]:**

Define state space capturing relevant information:
```
State = {
  robot_location: (x, y, aisle_id),
  battery_level: percent,
  current_task: product_id,
  pending_orders: [order_1, order_2, ...],
  aisle_congestion: {aisle_A: 2_robots, aisle_B: 1_robot, ...},
  time_of_day: hour,
  estimated_completion_if_continue: minutes
}
```

**2. Action Space [0.75 marks]:**

```
Action = {
  move_to_aisle: aisle_id,
  recharge: boolean,
  wait: boolean,
  pick_item: item_id
}
```

Discrete actions; finite set enables tractable learning.

**3. Reward Structure [1.25 marks]:**

```
Reward_t = +10 * (items_completed_t)
         - 1 * (travel_distance_t / 100)  # penalize waste
         - 2 * (queue_length_waiting)      # encourage order fulfillment
         - 20 * (collision_event)          # safety constraint
         - 5 * (task_not_completed_t)      # opportunity cost

Constraints (hard):
  - No collisions (critical safety)
  - Minimum charge maintained
  - Restricted zones respected
```

Reward reflects business objectives: complete orders, minimize time, prioritize queue, avoid collisions.

**4. Data Collection [0.5 marks]:**

Offline learning from existing logs:
- State sequences: 1 year of warehouse operations logs
- Actions taken: Routing decisions made by controllers
- Outcomes: Time to complete, collisions, efficiency
- External data: Order volumes, seasonal patterns

Data structure for each trajectory:
```
(State_t, Action_t, Reward_t, State_(t+1), Done)
```

**5. Training Approach [0.5 marks]:**

**Offline RL** (safer than online):
1. Use historical logs to train policy via batch methods (Batch RL, Conservative Q-Learning)
2. Evaluate on historical data: Would policy have performed better than original actions?
3. Simulation validation: Replay policy in simulator
4. Safe deployment: Canary deployment to 5% of fleet, monitor vs. control group

Avoid live exploration initially (real collisions expensive).

---

**c) Validation Strategy Preventing Leakage [3 marks]**

**Temporal Validation [1 mark]:**

```
Period 1: Jan-Aug 2023 → Train
Period 2: Sep 2023    → Validate (select hyperparams, thresholds)
Period 3: Oct-Dec 2023 → Test (final evaluation)
```

Temporal splits prevent future information leaking into training. Tests deployment realism (model must generalize to unseen future).

**Why important**: Routing patterns seasonal. Winter (Sep-Dec) different from summer. Must validate on future distribution.

**Group Validation [1 mark]:**

```
Robot A, B, C, D → Training
Robot E, F     → Validation
Robot G, H     → Test
```

Prevents robot-specific patterns from leaking. Different robots, operators, wear, quirks. Group split tests generalization across robots.

**Alternatively, if robots in training data:**

Train on historical logs from all robots mixed. But validate:
- Separately on new robots (never seen during training) to test generalization
- In simulator before live deployment to test safety

**Leakage Prevention Specifics [1 mark]:**

**Watch for:**
- ✓ Don't use future order demand as feature (unavailable at decision time)
- ✓ Don't use outcome (collision, completion time) as feature
- ✓ Don't use previous policy's actions as features (introduces historical bias)
- ✓ Don't mix training and test trajectories from same robot during high-correlation periods (night shift, after maintenance)

**Counterfactual problem:**
Offline data only shows outcomes for actions taken. New policy may choose different actions. Address by:
- Importance sampling: weight trajectories by action probability ratio
- Simulation: Test policy in simulator before live deployment
- Conservative learning: Learn lower-bound policy quality to avoid overestimating

**Final Validation [0 marks embedded above]:**

Business metric comparison:
- **Baseline**: Current rule-based system (70% completion, 3.2h fulfillment)
- **AI policy**: Evaluate on test data
- **Improvement threshold**: Require ≥72% completion AND ≤3.0h fulfillment
- **Confidence**: 95% CI must not overlap baseline

---

## Q5. Transfer Learning Failure Analysis - Why Fine-Tuning Didn't Improve Performance [8 Marks]

A company has:
- **Source**: Pretrained ResNet-50 on ImageNet (1000 classes, millions of images)
- **Target task**: Classify medical X-ray images (2 classes: Normal vs. Fracture)
- **Target data**: 2,000 labeled X-ray images

**Experiment:**
1. Feature extraction (frozen backbone): Accuracy = 81%
2. Full fine-tuning (all layers trainable): Accuracy = 79%

**Puzzling result**: Full fine-tuning *decreased* performance!

### Task:

a) **Propose five hypotheses** explaining why fine-tuning degraded performance [5 marks]

b) **Design experiments to test each hypothesis** [2 marks]

c) **Recommend a solution** [1 mark]

### Answer:

---

**a) Five Hypotheses for Fine-Tuning Degradation [5 marks]**

**Hypothesis 1: Overfitting Due to Small Dataset [1 mark]**

Fine-tuning all 23+ million parameters with only 2,000 examples → severe overfitting risk.

Training accuracy ↑, validation accuracy ↓. Model memorizes training examples rather than learning generalizable features.

**Test**: Check training vs. validation accuracy. If training >>95%, validation <81%, this is culprit.

**Hypothesis 2: Incompatible Learning Rate [1 mark]**

Fine-tuning likely used default learning rate (e.g., 0.001) appropriate for small models/small datasets, not 23M parameters being updated.

High learning rate damages useful ImageNet representations. Model diverges from good pretrained knowledge.

**Test**: Retrain with lower learning rate (0.0001 or 10× lower). Compare final accuracy.

**Hypothesis 3: Lack of Discriminative Learning Rates [1 mark]**

Standard SGD uses same learning rate for all layers. Early ResNet layers capture low-level features (edges, textures); later layers capture high-level features.

Using high learning rate for early layers damages those general-purpose representations. Late layers need higher rates to adapt.

**Solution**: Use discriminative learning rates (higher for task-specific layers, lower for general layers).

**Test**: Retrain with 0.00001 for early layers, 0.0001 for mid, 0.001 for final classifier. Compare.

**Hypothesis 4: Domain Shift Too Severe (X-ray ≠ Natural Images) [1 mark]**

ImageNet images are natural photographs. X-ray images fundamentally different:
- Different imaging physics (X-ray vs. visible light)
- Different color patterns (grayscale)
- Different feature distributions (bone structures vs. objects)

Pretrained ImageNet features may be **negatively correlated** with X-ray task. Fine-tuning on negatively correlated features makes things worse.

**Test**: Compute feature correlation. Compare ImageNet pretrained features on X-ray vs. random initialization. If correlation weak/negative, domain shift is severe.

**Alternative**: Use medical imaging pretrained model instead of ImageNet.

**Hypothesis 5: Inadequate Regularization [1 mark]**

Fine-tuning all 23M parameters without strong regularization (weight decay, dropout) allows overfitting.

Model captures noise in 2,000 training examples instead of learning robust features.

**Test**: Retrain with:
- High weight decay (L2 regularization, lambda = 0.01 or 0.1)
- Dropout on fine-tuned layers
- Early stopping based on validation accuracy
- Data augmentation (rotation, crop, brightness variation)

Compare accuracy with and without regularization.

---

**b) Experimental Design to Test Hypotheses [2 marks]**

**Experiment 1: Overfit Detection [0.5 marks]**
```
Metric: Training accuracy vs. Validation accuracy
Setup: Train full model, plot per-epoch
Result interpretation:
  - If training >> validation: Overfitting confirmed
  - If training ≈ validation: Overfitting not primary issue
```

**Experiment 2: Learning Rate Ablation [0.5 marks]**
```
Test learning rates: [0.1, 0.01, 0.001, 0.0001, 0.00001]
Train full model with each rate
Plot: validation accuracy vs. learning rate
Result: Identify if very low LR restores performance
```

**Experiment 3: Discriminative Learning Rates [0.5 marks]**
```
Group layers by depth:
  - Early (layer 1-2): LR = 0.00001
  - Mid (layer 3): LR = 0.0001
  - Late (layer 4 + classifier): LR = 0.001
Train full model
Compare accuracy vs. uniform learning rate
Result: If discriminative LR > uniform, layer-wise issues confirmed
```

**Experiment 4: Domain Similarity Analysis [0.5 marks]**
```
Compare two models on X-rays:
  - ImageNet-pretrained ResNet-50
  - Untrained random ResNet-50 (same architecture)
Feature visualization: Inspect learned filters
Result interpretation:
  - If ImageNet features useful: Visualizations aligned with X-ray structures
  - If ImageNet features misaligned: Domain shift severe
```

---

**c) Recommended Solution [1 mark]**

**Multi-pronged approach:**

1. **Start with feature extraction baseline** (frozen backbone, train only classifier)
   - Confirmed working: 81% accuracy
   - Use as safety baseline

2. **Gradually unfreeze layers** rather than all at once
   - Unfreeze final classifier layer only
   - If improves, unfreeze final ResNet block
   - Each step, validate on hold-out set
   - Stop unfreezing if validation accuracy drops

3. **Use discriminative learning rates** for unfrozen layers
   - Low rate (0.00001-0.0001) for early layers
   - Higher rate (0.001) for task-specific head

4. **Add strong regularization**
   - Weight decay (L2): lambda = 0.01-0.1
   - Data augmentation: Random rotation ±10°, crop, brightness variation
   - Early stopping: Stop if validation acc plateaus

5. **Consider domain-adapted pretrained model**
   - If available, use medical-imaging pretrained model instead of ImageNet
   - Or use self-supervised pretraining on unlabeled X-rays first

6. **Monitor validation accuracy continuously**
   - Report train/val accuracy per epoch
   - Select model checkpoint with best validation accuracy
   - Evaluate on held-out test set

**Expected outcome**: Careful partial fine-tuning with discriminative rates + regularization likely achieves 83-86% accuracy (improvement over 81% feature extraction).

---

## Q6. Metric Selection for Loan Default Prediction Under Business Constraints [8 Marks]

Bank building default predictor. Context:
- **Prevalence**: 5% of loans default
- **Financial impact**: Default loss = €10,000 per loan
- **Operational capacity**: Can manually review 1,000 loans per day (out of 10,000 daily applications)
- **Cost of false decline**: Annoy customer, lose potential profit = €500 per declined application
- **Business goal**: Minimize total expected loss while maintaining customer satisfaction

Model outputs P(Default | loan_application)

### Task:

a) **Explain why AUC is insufficient** for this business problem [2 marks]

b) **Select appropriate metrics** to evaluate model [2 marks]

c) **Determine optimal threshold** using business constraints [2 marks]

d) **Design a monitoring plan** after deployment [2 marks]

### Answer:

---

**a) Why AUC Alone Insufficient [2 marks]**

**AUC measures ranking quality:**

AUC answers: "If I randomly pick a defaulter and a non-defaulter, what's the probability the model ranks defaulter higher?" (Discrimination power)

**Limitation for business decision [1 mark]:**

AUC ignores:
- **Specific operating point**: Doesn't care if threshold 0.1, 0.5, or 0.9. Same AUC.
- **Class imbalance consequences**: With 5% defaults, majority of predictions are negative. AUC doesn't reflect this imbalance's cost.
- **Decision policy**: Doesn't account for review capacity constraints or cost asymmetry.
- **Business outcome**: Good ranking (high AUC) ≠ good business outcome if threshold/policy poorly chosen.

**Example**: Model A (AUC 0.85) with high false positives may cost more than Model B (AUC 0.80) with better false positive/negative balance, depending on threshold chosen.

**What's needed instead [1 mark]:**

Metrics reflecting business reality:
- **Precision@K, Recall@K**: Performance at capacity constraint (review top-1,000 flagged loans)
- **Expected loss**: Quantify financial impact of errors at chosen threshold
- **Confusion matrix at business-relevant thresholds**: Show TP, FP, FN rates at thresholds matching operating points

---

**b) Appropriate Metrics Selection [2 marks]**

**Metric 1: Precision@K (K=1,000) [0.5 marks]**

Fraction of top-1,000 flagged loans that actually default.

```
Precision@1000 = # loans in top-1,000 that defaulted / 1,000
```

Reflects: "If we manually review top-1,000 flagged loans, what fraction are actually problematic?"

**Metric 2: Recall@K (K=1,000) [0.5 marks]**

Of all defaulting loans, what fraction appears in top-1,000?

```
Recall@1000 = # defaults in top-1,000 / # total defaults
```

Reflects: "How many actual defaults do we catch with our review capacity?"

**Metric 3: Expected Financial Loss at Threshold [0.5 marks]**

```
Expected Loss = (1-Precision@1000) × 500 + (1-Recall) × 10,000
               + operational_cost_per_review × 1,000
```

Quantifies total financial impact of chosen threshold and review policy.

**Metric 4: Calibration [0.5 marks]**

Among loans predicted 0.50 default probability, fraction actually defaulting should ≈50%.

Ensures predicted probabilities meaningful for decision-making (scoring loans by risk).

---

**c) Optimal Threshold Determination [2 marks]**

**Pure cost-based approach (ignoring capacity) [1 mark]:**

```
Optimal threshold = Cost_FN / (Cost_FP + Cost_FN)
                 = 10,000 / (500 + 10,000)
                 = 0.952
```

Predict default if P(Default) > 0.952

**Incorporating capacity constraint [1 mark]:**

Capacity: Review 1,000 loans/day. With 10,000 daily applications and 5% baseline default rate (500 expected defaults):

If threshold 0.952 is too high (flags <1,000), we're underutilizing capacity.

**Recommended approach:**

1. Compute false positive/negative rates at various thresholds
2. For each threshold, calculate:
   - # loans flagged (should ≈ 1,000)
   - Expected loss: FN_cost + FP_cost + review cost
3. Choose threshold that:
   - Fills capacity (1,000 flagged loans)
   - Minimizes expected loss
   - Maintains acceptable false positive rate (customer frustration)

**Example calculation:**

If threshold 0.85:
- Flags 1,500 loans (over capacity; must reduce or increase capacity)

If threshold 0.90:
- Flags 1,050 loans (within capacity)
- Expected defaults caught: 850 (if 1,050 flagged includes 850 true defaults)
- Expected loss: 150 × 10,000 + 200 × 500 + 1,050 × cost_review
- Compare against other thresholds

**Recommendation**: Use threshold that flags ~1,000 loans, minimizing expected loss. Likely around 0.88-0.92 depending on model calibration.

---

**d) Production Monitoring Plan [2 marks]**

**Daily Monitoring [1 mark]:**

```
Metrics tracked daily:
- # loans flagged (should ≈ 1,000)
- # reviews completed
- Observed default rate among reviewed loans
- Observed default rate among non-reviewed loans
- Total cost: loss from defaults + review costs + false declines

Alerts:
- # flagged deviates >20% from target (1,000) → investigate data drift
- Observed defaults in reviewed group <50% → threshold too low (false positives)
- Defaults in non-reviewed group >5% → threshold too high (false negatives, unacceptable risk)
```

**Weekly Reporting [1 mark]:**

```
Metrics calculated weekly:
- Precision@1,000: Of reviewed loans, fraction actually defaulted
- Recall: Of all defaults that occurred, fraction caught
- Calibration: For loans predicted 0.70, what fraction actually defaulted?
- ROC curve: Ranking quality

Triggers for retraining:
- Precision@1,000 drops <40% → data distribution shifted; retrain
- Recall drops <60% → missing defaults; retrain
- Calibration breaks (predicted 0.70 → actual 0.50) → concept drift; retrain
- Threshold needs adjustment: If policy changes (budget, risk tolerance), recalculate threshold

Timeline: Retrain monthly with accumulated new data
```

---

## Q7. Model Acceptance Decision - Challenger vs. Incumbent [9 Marks]

**Current model** (Incumbent): Predicting customer churn risk
- ROC-AUC: 0.82
- Inference time: 50ms per customer
- Operational cost: $5,000/month (server, maintenance)
- Training cost: $1,000/month

**Proposed model** (Challenger): Newer deep learning model
- ROC-AUC: 0.85
- Inference time: 200ms per customer
- Operational cost: $15,000/month (GPU server)
- Training cost: $2,000/month

Deployment context:
- 100,000 customers scored weekly
- Latency budget: 500ms per customer (can tolerate)
- False positive cost (false churn alerts): $50 per customer (intervention campaigns)
- False negative cost (missing churner): $500 per customer (lost revenue)
- Annual deployment window: 12 months

### Task:

a) **Identify critical evaluation dimensions beyond accuracy metrics** [2 marks]

b) **Quantify business trade-offs** between models [3 marks]

c) **Define an acceptance decision framework** [2 marks]

d) **Recommend: Deploy challenger, keep incumbent, or run pilot?** [2 marks]

### Answer:

---

**a) Critical Evaluation Dimensions [2 marks]**

**Dimension 1: Latency and Operational Constraints [1 mark]**

- Incumbent: 50ms (low, efficient)
- Challenger: 200ms (higher, but within 500ms budget)
- **Issue**: 200ms slower may impact:
  - Real-time scoring systems (if used interactively)
  - Batch scoring throughput (2M customers scored in parallel)
  - System responsiveness to campaign decisions

- **Test needed**: A/B test latency impact. Does +150ms latency degrade user experience? Batch scoring throughput?

**Dimension 2: Cost-Benefit Over 12 Months [1 mark]**

- Incumbent annual cost: $72,000
- Challenger annual cost: $204,000 (2.8× more expensive)
- **Question**: Is 3% AUC improvement worth $132,000/year?

- **Calculate ROI**: Model improvements must translate to revenue/cost gains exceeding $132,000/year

---

**b) Quantify Business Trade-Offs [3 marks]**

**ROC-AUC Improvement [1 mark]:**

0.82 vs. 0.85 = 3 percentage point improvement. 

In probability terms: If comparing random defaulter vs. non-defaulter, Challenger ranks them correctly 85% vs 82% of time.

**Translation to precision/recall [1 mark]:**

Assuming both models applied at same operating point (same threshold):

```
Incumbent at threshold T:
- Precision: 75%
- Recall: 60%

Challenger at same threshold T (with improved ranking):
- Precision: 78% (estimated based on AUC improvement)
- Recall: 65% (estimated)
```

Expected improvement: ~3-4 percentage points on precision and recall.

**Annual Financial Impact [1 mark]:**

Suppose 100,000 customers scored weekly = 5.2M customer-scores annually.

At ~2% churn rate, 104K expected churners.

- **Incumbent**: Catches 0.60 × 104K = 62,400 churners
  - Misses: 41,600 × $500 = $20.8M loss
  - False interventions: ~4M × 0.25 × $50 = $50M cost (25% of scored customers)

- **Challenger**: Catches 0.65 × 104K = 67,600 churners
  - Misses: 36,400 × $500 = $18.2M loss
  - False interventions: ~4M × 0.22 × $50 = $44M cost (improved precision)

**Improvement**: $2.6M loss reduction + $6M intervention cost reduction = **$8.6M annual benefit**

Challenger cost premium: $132,000

**ROI**: ($8.6M - $0.132M) / $0.132M = **6,412% return**

---

**c) Acceptance Decision Framework [2 marks]**

**Criteria [1 mark]:**

1. **Accuracy improvement**: ✓ Challenger AUC 0.85 vs incumbent 0.82 (meaningful improvement)
2. **Latency acceptable**: ✓ 200ms within budget; doesn't degrade user experience
3. **Financial ROI positive**: ✓ $8.6M benefit >> $132K cost
4. **Operational feasibility**: ✓ GPU infrastructure available; monitoring team trained
5. **Risk mitigation**: ? Require canary deployment + monitoring gates

**Gate conditions [1 mark]:**

- [ ] Pilot deployment on 10% of customers for 2 weeks
- [ ] Monitor precision@recall on pilot: must maintain ≥75% precision at same recall as incumbent
- [ ] Monitor latency: must remain <250ms in production
- [ ] Monitor data drift: input distributions stable; no unexpected shifts
- [ ] Stakeholder approval: Business, engineering, risk teams agree
- [ ] Rollback plan prepared: Can revert to incumbent within 1 hour if issues detected

---

**d) Recommendation [2 marks]**

**Recommended approach: Pilot then Deploy [1 mark]**

**Justification:**

Challenger shows strong promise:
- 3% AUC improvement significant and statistically substantial
- $8.6M annual benefit far exceeds $132K cost
- Latency acceptable for use case
- ROI overwhelmingly positive

But canary deployment prudent:
- Deep learning models less interpretable; harder to debug production failures
- Latency increase (~4× slower) risks cascade effects if not monitored
- $132K additional cost commitment; validate gains in real deployment first

**Pilot plan [1 mark]:**

```
Week 1-2: Canary deployment (10% of customers)
  - Route 10% of churn scores through Challenger
  - 90% still use Incumbent
  - Monitor precision, recall, latency, data quality

Week 2 decision gate:
  - If Challenger precision ≥75% at same recall: Proceed to 50%
  - If latency issues detected: Investigate; don't proceed unless fixed
  - If data drift detected: Investigate root cause

Week 3-4: 50% deployment
  - Half customers scored by Challenger, half by Incumbent
  - A/B test business metrics (churn rate, campaign ROI, customer satisfaction)

Week 5+: Full deployment
  - All customers scored by Challenger
  - Incumbent kept as fallback (24-hour revert capability)
  - Monitor continuously; monthly retraining

Exit criteria:
  - If Challenger precision drops <70% for 3 consecutive days: rollback to Incumbent
  - If latency exceeds 300ms: rollback
  - If unexpected churn increase observed: investigate before continuing
```

**Success metrics for pilot:**
- Precision@Recall ≥ incumbent baseline
- Latency <250ms p95
- No unexplained performance degradation
- Team confident in monitoring capabilities

---

## Q8. Unsupervised Learning for Enterprise: Clustering Customer Segments [8 Marks]

Bank has 500,000 customers. Business wants to segment customers by behavior to tailor offerings. 

Available data per customer:
- Account age, balance, transaction frequency, transaction size, savings rate, loan utilization, credit score, income, employment type, geography

**Proposed approach**: Unsupervised clustering (k-means) on 10 features.

### Task:

a) **Why unsupervised learning appropriate here?** [2 marks]

b) **Design the clustering solution** (preprocessing, algorithm, k selection) [3 marks]

c) **Validate and operationalize clusters** — how to ensure clusters meaningful for business [3 marks]

### Answer:

---

**a) Why Unsupervised Learning [2 marks]**

**No predefined labels [1 mark]:**

Business doesn't have labels like "Segment A", "Segment B" pre-assigned. Wants to discover natural customer groupings.

Unsupervised learning discovers structure from data without labels.

**Business value of discovery [1 mark]:**

Segments discovered may reveal unexpected patterns (e.g., high-balance, low-activity customers; young high-earners with small balances; stable mid-tier customers).

These discovered segments more actionable than arbitrary business-defined segments.

**Why not supervised?** No labels. Why not RL? Not a decision/optimization problem.

---

**b) Clustering Solution Design [3 marks]**

**Step 1: Feature Preprocessing [1 mark]**

```
1. Handle missing values:
   - Account age: Few missing; impute with median
   - Balance: Few missing; impute with median
   
2. Standardize features (critical for k-means):
   - z-score normalization: (x - mean) / std
   - Or min-max scaling: (x - min) / (max - min)
   - Reason: Features on different scales (e.g., balance ∈ [0, 1M] vs. credit_score ∈ [300, 800])
   
3. Handle outliers:
   - Visualize distributions; identify extreme values
   - Clip extreme values (e.g., balance > $1M clipped to $1M) or use robust scaling
   - Reason: Outliers pull cluster centers
```

**Step 2: Algorithm and K Selection [1 mark]**

```
Algorithm: K-means (interpretable, scalable)

K selection using elbow method:
1. Run k-means for k ∈ [2, 3, 4, ..., 10]
2. Plot within-cluster sum of squares (WCSS) vs. k
3. Elbow point (diminishing returns) ≈ optimal k
4. Expected: k ≈ 4-6 for customer segmentation

Alternative: Silhouette score (average cluster quality)
- Run k-means for each k
- Compute silhouette score (-1 to 1; higher better)
- Choose k with highest silhouette score
```

**Step 3: Stability and Validation [1 mark]**

```
Stability check:
1. Run k-means 10 times with different initializations
2. Compare cluster assignments:
   - If clusters stable, assignments mostly consistent
   - If unstable, try higher k or better features
   
Alternative initialization:
- Use k-means++ initialization (better convergence)
```

---

**c) Validate and Operationalize Clusters [3 marks]**

**Business Interpretation [1 mark]**

```
Once clusters found, interpret each:

Cluster 1:
- High balance, low transaction frequency, high savings rate
- Interpretation: "Conservative savers"
- Size: 15% of customers

Cluster 2:
- Low balance, high transaction frequency, small avg transaction
- Interpretation: "Active low-balance users"
- Size: 35% of customers

Cluster 3:
- High balance, frequent large transactions, credit score high, high income
- Interpretation: "Affluent active investors"
- Size: 20% of customers

Cluster 4:
- Mid balance, moderate activity, variable patterns
- Interpretation: "Moderate engagement"
- Size: 30% of customers

Validation: Do clusters make business sense?
- Can marketing team use these to tailor campaigns?
- Do clusters actionable? (e.g., can offer different products)
- Are clusters stable over time? (Not artifacts)
```

**Statistical Validation [1 mark]**

```
1. Silhouette score per cluster:
   - Score > 0.5: Well-separated cluster
   - Score 0.3-0.5: Borderline
   - Score < 0.3: Poorly defined
   - Action: If poor silhouette, reconsider cluster definition or k value

2. Entropy and purity:
   - If external labels available (e.g., known high-value vs. low-value customers),
     check if clusters align
   - High purity: Clusters match known labels
   - Low purity: Clusters capture different structure than expected

3. Stability testing:
   - Resample 80% of customers, retrain, compare cluster assignments
   - High stability: Same customers end up in same clusters
   - Low stability: Assignments noisy; clusters unreliable
```

**Operational Deployment [1 mark]**

```
1. Assign all customers to clusters:
   - Use learned cluster centers
   - Assign each customer to nearest center
   - Store cluster_id for each customer

2. Continuous monitoring:
   - Monthly: Rerun clustering on fresh data
   - Check if cluster distributions stable (# customers per cluster)
   - Check if center locations drift
   - Alert if major shifts detected

3. Actionable use cases:
   - Campaign targeting: Different offers per segment
   - Product recommendations: Cluster-specific products
   - Risk management: Allocate resources by cluster risk
   - Pricing: Segment-based pricing strategies

4. Scheduled retraining:
   - Quarterly: Incorporate new customers, update clusters
   - If major distribution shift: Retrain immediately
   - Version control clusters (date stamped)
```

---

## Summary of Questions by Topic

| Q# | Topic | Marks | Concepts |
|---|---|---|---|
| Q1 | Learning Mechanism Selection | 9 | Supervised vs. Unsupervised vs. RL, learning signals, feedback loops |
| Q2 | Transfer Learning Strategy | 10 | Fine-tuning, LoRA, PEFT, domain adaptation, factory scenarios |
| Q3 | Model Evaluation & Metrics | 10 | Precision, recall, ROC-AUC, business costs, threshold selection |
| Q4 | Reinforcement Learning Design | 9 | State/action/reward, offline RL, temporal validation |
| Q5 | Fine-Tuning Failure Analysis | 8 | Overfitting, learning rates, domain shift, regularization |
| Q6 | Metric Selection Business Constraints | 8 | Capacity constraints, expected loss, Precision@K |
| Q7 | Model Acceptance Decision | 9 | ROI analysis, canary deployment, pilot strategy |
| Q8 | Unsupervised Clustering | 8 | K-selection, validation, business interpretation |

**Total: 71 marks across 8 comprehensive questions**

**Coverage:**
- ✓ Learning paradigms (supervised, unsupervised, RL, hybrid)
- ✓ Transfer learning (feature extraction, fine-tuning, PEFT, LoRA, domain adaptation)
- ✓ Model evaluation (metrics, validation, leakage, monitoring)
- ✓ Practical enterprise scenarios
- ✓ Decision frameworks
- ✓ Real-world trade-offs and constraints

---
## Q9. Model Deployment Architecture and Monitoring [10 Marks]

A fintech company has trained a fraud detection model achieving 95% accuracy on validation data. Before deploying to production (processing 10,000 transactions/second), the team must design:

### Requirements:
1. **Model serving infrastructure** - predict in < 50ms per transaction
2. **Real-time monitoring** - detect model degradation within 24 hours
3. **Graceful fallback** - if model fails, use rule-based system
4. **A/B testing** - gradually roll out new model versions

### Design the complete system addressing:

a) **Model serving architecture** [3 marks]
   - How to achieve < 50ms latency at scale?
   - Batch vs. online serving trade-offs?
   - What preprocessing can/cannot be cached?
   - Handling model updates without service interruption?

b) **Monitoring strategy** [4 marks]
   - What metrics detect model degradation?
   - How to distinguish performance drop vs. data drift?
   - What's the minimum sample size to detect 5% AUC drop with 95% confidence?
   - Alert thresholds and manual review process?

c) **Deployment strategy** [3 marks]
   - How to validate model before production deployment?
   - Canary deployment plan (% traffic, duration, rollback criteria)?
   - How to maintain rule-based fallback accuracy?

### Answer:

**a) Model Serving Architecture [3 marks]**

**Latency budget (50ms total per transaction):**
- Feature retrieval: 10ms (cache lookups, database queries)
- Model inference: 20ms (single prediction pass)
- Post-processing: 5ms (score scaling, threshold application)
- Network overhead: 15ms (API calls, serialization)

**Serving approaches:**
1. **Real-time serving** (online):
   - Framework: TensorFlow Serving, Triton, vLLM
   - Batch size: 1 (single transaction per request)
   - Latency: 15-30ms typical
   - Throughput: ~300-600 requests/sec per server
   - Need 20-40 servers for 10k req/sec (with load balancing)
   
2. **Batch serving** (unsuitable):
   - Cannot batch 10k independent fraud decisions
   - Would delay decisions by minutes
   - Not viable for real-time fraud prevention

**Model optimization for latency:**
- Convert model to quantized format (FP16, INT8) - 30% speedup
- Use ONNX runtime - 25% faster than TensorFlow native
- Prune model (remove low-importance connections) - 40% faster
- Use model distillation (smaller model trained on large model) - 60% faster
- Trade-off: Monitor accuracy impact of optimizations

**Feature preprocessing caching:**
- Cannot cache: User behavior (current session data, real-time patterns)
- Can cache: User profile, historical statistics, reference data (merchant info, geographic data)
- Pre-compute and cache features every hour (batched job)
- Store in Redis/Memcached for sub-millisecond retrieval

**Model updates without interruption:**
- Blue-green deployment: Run two model servers (old and new)
- Route traffic to old while new fully starts
- Health check new model (predictions make sense, latency acceptable)
- Gradually shift traffic: 10% → 50% → 100% over hours
- Instant rollback if issues detected
- Canary: Route 1% initially, monitor for issues

**b) Monitoring Strategy [4 marks]**

**Metrics detecting degradation:**

1. **Performance metrics** (direct model quality):
   - Real-time precision/recall (transactions flagged as fraud, confirmed/not confirmed)
   - ROC-AUC estimated from prediction distribution
   - Expected loss (false positive cost vs. false negative cost)
   
2. **Data drift indicators** (input distribution changed):
   - Feature distribution comparison (KS test, Wasserstein distance)
   - Feature correlation changes (covariance matrix comparison)
   - Anomaly score: % transactions with unusual feature combinations
   
3. **Model behavior indicators** (prediction distribution changed):
   - Prediction distribution (histogram of fraud scores - any major shifts?)
   - Prediction stability (score variance, confidence distribution)

**Distinguishing performance drop vs. data drift:**
- Data drift alone: Performance unchanged, feature distributions different
  - Example: Transactions increased from urban to rural areas → different feature values, but model still accurate
  - Action: No retraining needed if model generalizes
  
- Performance drop due to drift: Both performance AND feature distributions changed
  - Example: New fraud patterns emerging → features similar but fraud incidence up
  - Action: Retrain with recent data
  
- Performance drop due to concept drift: Same features, different fraud patterns (fraudster tactics evolved)
  - Example: Card brands changed numbers (technically different features) but fraud methods same
  - Action: Retrain or adapt model

**Minimum sample for 5% AUC drop detection:**
- Statistical power analysis: Detect AUC drop from 0.95 → 0.90 with 95% confidence
- At true AUC=0.95: ~500-1000 transactions needed to get 95% CI around estimate
- At true AUC=0.90: Different distribution, ~1500-2000 transactions
- Conservative: Monitor every 1000 transactions (hourly at 10k/sec)
- Alert if AUC drops to < 0.92 (conservative threshold)

**Alert process:**
- Automated alert if: AUC < 0.92 OR false positive rate > 2% OR false negative rate > 0.5%
- Manual review: Data scientist examines drift indicators and recent performance
- Decision: Continue with current model, retrain, or rollback

**c) Deployment Strategy [3 marks]**

**Pre-deployment validation:**
1. **Offline validation** (historical data):
   - Split: 80% train, 10% validation, 10% test (time-ordered)
   - Evaluate: Precision, recall, ROC-AUC on test set
   - Check for leakage: Ensure test set doesn't influence feature engineering
   
2. **Shadow deployment** (production data, no decisions):
   - Run model in parallel to production for 1 week
   - Collect predictions on real transactions
   - Compare predictions to actual fraud labels (once known)
   - Identify any systematic differences from validation performance

3. **Staging environment** (production-like):
   - Same hardware, load patterns, feature latency
   - Test with 1% of real data volume
   - Verify latency, throughput, error rates

**Canary deployment:**
- Phase 1 (Days 1-2): Route 1% of traffic (100 trans/sec)
  - Alert thresholds: Very tight (e.g., AUC drop to 0.93)
  - Manual review of every alert
  
- Phase 2 (Days 3-5): Route 10% of traffic (1,000 trans/sec)
  - Alert thresholds: Normal (e.g., AUC drop to 0.92)
  - Auto-alert if latency > 100ms
  
- Phase 3 (Day 6+): Route 100% of traffic (10,000 trans/sec)
  - Continue monitoring
  - Rollback within 1 hour if major issues

**Rollback criteria (automatic):**
- Latency > 100ms (service degradation)
- Error rate > 0.1% (model crashes or timeouts)
- AUC drops below 0.90 (severe performance loss)

**Fallback rule-based system:**
- Use simple rules: Transaction amount > threshold, geographic anomaly, velocity checks
- Expected performance: ~70% precision (higher false positive rate)
- Used only when model unavailable
- Monitor accuracy to ensure rules still effective

---

## Q10. AI System Failure Analysis and Learning from Production Incidents [9 Marks]

A company deployed an AI-powered resume screening system that automatically ranks job applicants. After 3 months, HR discovers:
- System showed bias: Rejected 80% of applications with non-English names, 60% with college gaps
- System missed 5% of qualified candidates later identified by manual review
- System's confidence scores poorly calibrated: Claimed 90% confidence but actual accuracy only 60%

### Analyze:

a) **Root cause analysis** [3 marks]
   - What could cause these failures?
   - Were these predictable or unpredictable?
   - What signals should have triggered alerts?

b) **System-level design failures** [3 marks]
   - How would proper monitoring have prevented this?
   - What validation steps were likely skipped?
   - How to detect bias before deployment?

c) **Recovery and redesign** [3 marks]
   - How to fix the system?
   - How to rebuild trust with HR stakeholders?
   - What governance process prevents recurrence?

### Answer:

**a) Root Cause Analysis [3 marks]**

**Bias against non-English names:**
- **Root cause**: Training data likely had fewer non-English named candidates in "qualified" category (either because: hiring historical bias, certain demographics underrepresented in applicant pool, or quality of resumes different)
- **Model learning**: Learned proxy feature (name origin) → correlated with quality in training data
- **Predictable?**: Yes - Should have inspected training data demographics, tested for name bias specifically
- **Missing signal**: No analysis of feature importance (would show name/location as important), no fairness metrics tracked

**Missed qualified candidates (5% false negatives):**
- **Root cause**: Model optimized for precision (minimize false positives) or trained on outdated job descriptions
- **Predictable?**: Depends - If model designed for high precision (OK to miss candidates), then expected. If designed for recall (find all qualified), then failure
- **Missing signal**: Low recall on validation set should have been caught, but may not have been measured or monitored

**Poor calibration (90% confidence but 60% accuracy):**
- **Root cause**: Model trained on one distribution, applied to different candidate pool; or used classification confidence as calibrated probability without temperature scaling
- **Predictable?**: Yes - Calibration should be explicitly tested (Brier score, ECE - expected calibration error)
- **Missing signal**: No confidence histogram analysis, no comparison of predicted vs. actual outcomes

**b) System-Level Design Failures [3 marks]**

**Monitoring failures:**
1. **No fairness monitoring**:
   - Should track: Selection rate by demographic (name, gender, school prestige, employment gaps)
   - Alert if any demographic has < 80% of average selection rate (disparate impact test)
   - Should have: Audit of 100 resumes weekly for bias patterns

2. **No performance monitoring**:
   - Should track: Hiring managers' feedback on screened candidates
   - After hire: Compare "qualified by system" vs. "actually performed well"
   - Measure recall: % of hired employees was system had screened positive

3. **No calibration monitoring**:
   - Should track: Confidence score vs. actual accuracy
   - Example: Collect 100 resumes where system gave 90% confidence, see how many actually qualified
   - Alert if 90% confidence correlates with < 85% actual accuracy

**Validation steps likely skipped:**
1. **Fairness validation**: 
   - Test: Does model perform equally across demographic groups?
   - Method: Stratified testing (test separately on resumes with English names vs. non-English names)
   - Missing: No analysis of model performance by demographic group

2. **Drift detection**:
   - Test: How does model perform on new candidate pool vs. training pool?
   - Example: If trained on 2021 resumes, how does it perform on 2024 resumes (different job market, skills emphasis)?
   - Missing: Validation data likely not representative of actual candidate pool

3. **Human-in-the-loop validation**:
   - Process: HR should manually review system's decisions on sample of candidates before full deployment
   - Missing: Appears to have gone straight to full automation without human validation phase

**Detecting bias before deployment:**
- **Data audit**: Check training data for demographic imbalances
  - Count: How many resumes per demographic group?
  - Quality: Are all groups equally well-represented in "qualified" label?
  - Example: 90% of English-named candidates labeled "qualified" vs. 40% non-English = bias in data!

- **Model audit**: Check if name/demographics used as features
  - Inspect feature importance: Is applicant name or school a top predictor?
  - Red flag: Name shouldn't predict job suitability!

- **Fairness testing**: Compare model performance across groups
  - Metric: Precision, recall, ROC-AUC by demographic group
  - Alert if any group has materially different performance (e.g., recall 95% for English names, 75% for non-English)

**c) Recovery and Redesign [3 marks]**

**Fixing the system:**

1. **Immediate mitigation**:
   - Remove system from production (revert to manual review)
   - Audit all decisions made by system (ensure no candidates wrongly excluded)
   - Notify affected candidates (transparency, legal protection)

2. **Retrain with bias mitigation**:
   - **Data**: Re-label training data carefully (ensure consistent quality labels across demographics)
   - **Features**: Remove name, gender, school prestige (if not job-relevant)
   - **Algorithm**: Use fairness-aware learning:
     - Constraint: Require similar precision/recall across demographics
     - Method: Adversarial debiasing (train model to maximize performance while fooling demographic classifier)
   - **Validation**: Verify fairness achieved (< 10% difference in recall across groups)

3. **Improved monitoring**:
   - **Fairness dashboard**: Weekly report of selection rates by demographic
   - **Performance dashboard**: Manual review feedback (hired candidates' performance by system rank)
   - **Alerts**: Automatic alerts if selection rate drops by 5% for any group

**Rebuilding trust with HR:**

1. **Transparency**:
   - Explain what went wrong (training data bias, not monitored for fairness)
   - Share audit results (how many candidates affected, what populations harmed)
   - Show fix (new model, fairness constraints, monitoring)

2. **Human oversight**:
   - Propose limited deployment: System ranks top 100, HR manually reviews top 50
   - System supports decision-making, doesn't replace it
   - HR retains final decision authority

3. **Accountability**:
   - Establish fairness review board (HR, data science, legal)
   - Quarterly audits of system decisions
   - Governance process: Before deploying new AI systems, must pass fairness audit

**Governance process for prevention:**
- **AI Impact Assessment** (before deployment):
  - Who are affected stakeholders?
  - What fairness/bias risks exist?
  - How will system be monitored?
  
- **Testing checklist**:
  - [ ] Fairness validated across key demographic groups
  - [ ] Calibration verified (confidence matches accuracy)
  - [ ] Validation performance confirmed on representative data
  - [ ] Monitoring dashboards set up and tested
  - [ ] Human override process documented
  
- **Deployment gates**:
  - Shadow mode 1 week (compare system to human decisions)
  - Canary 10% traffic 1 week (monitor real-world performance)
  - Full deployment only after gates passed

---

## Summary of Post-MidSem Questions

| Q# | Topic | Marks | Focus |
|---|---|---|---|
| Q1 | Learning Mechanism Selection | 9 | Choosing appropriate paradigm (S/U/RL) for business problems |
| Q2 | Transfer Learning Strategy | 10 | Fine-tuning vs. PEFT, domain adaptation techniques |
| Q3 | Model Evaluation & Metrics | 10 | Metric selection, validation strategies, business impact |
| Q4 | RL System Design | 9 | State/action/reward definition, offline RL considerations |
| Q5 | Fine-Tuning Failure Analysis | 8 | Debugging training issues, regularization, domain shift |
| Q6 | Metric Selection & Constraints | 8 | Business constraints influence metric choice |
| Q7 | Model Acceptance Decision | 9 | ROI analysis, deployment strategy, pilot approach |
| Q8 | Unsupervised Learning | 8 | Clustering validation, business interpretation, monitoring |
| Q9 | Model Deployment & Monitoring | 10 | Serving architecture, monitoring strategy, deployment process |
| Q10 | AI System Failure Analysis | 9 | Root cause analysis, bias detection, governance |

**Total: 90 marks across 10 comprehensive questions**

**Coverage:**
- ✓ Learning paradigms (supervised, unsupervised, reinforcement, hybrid)
- ✓ Model development (training, fine-tuning, validation)
- ✓ Evaluation (metrics, bias, fairness, calibration)
- ✓ Deployment (serving, monitoring, governance)
- ✓ Real-world incidents and recovery
- ✓ Enterprise systems design
- ✓ Decision frameworks and trade-offs

