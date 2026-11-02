# Session 9: Learning Systems

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Dr. Minati Rath
>
> **References:** Class Slides, Contact Session 9
>
> **Contact Session:** 9 (Module 5: Learning Mechanisms and System Architecture)

---

## Table of Contents

- [9.1 Learning Mechanisms as Architecture Decisions](#91-learning-mechanisms-as-architecture-decisions)
- [9.2 Supervised Learning: System Design](#92-supervised-learning-system-design)
- [9.3 Unsupervised and Self-Supervised Learning](#93-unsupervised-and-self-supervised-learning)
- [9.4 Reinforcement Learning](#94-reinforcement-learning)
- [9.5 Hybrid Learning Systems](#95-hybrid-learning-systems)
- [9.6 System-Level Inductive Biases](#96-system-level-inductive-biases)
- [9.7 Learning System Failure Modes](#97-learning-system-failure-modes)
- [9.8 Practical Learning-System Design Framework](#98-practical-learning-system-design-framework)

---

## Learning Objectives

At the end of this session, you should be able to:

- **Understand learning mechanisms as system architecture decisions** — Choosing supervised, unsupervised, or reinforcement learning is fundamentally an architectural choice, not just an algorithm decision
- **Identify the role of learning signals in system design** — What learning signal does the system have access to?
- **Recognize the gap between models and systems** — Model accuracy ≠ business success
- **Detect and prevent common learning system failures** — Feedback loops, data drift, policy drift, counterfactual problems
- **Design integrated learning systems** — Combining multiple learning mechanisms appropriately

---

## 9.1 Learning Mechanisms as Architecture Decisions

### Why Learning Mechanism Choice Matters

Choosing supervised, unsupervised, or reinforcement learning is **not simply an algorithm-selection decision**. It's a fundamental **architecture decision** that determines:

- **What constitutes a training example** — Do we have labeled data, unlabeled data, or sequential interactions?
- **What constitutes feedback** — Is feedback explicit (labels), implicit (behavior), or sequential (rewards)?
- **When feedback becomes available** — Immediate feedback vs. delayed labels vs. eventual outcomes
- **What the model is allowed to optimize** — Prediction accuracy, representation quality, cumulative reward, or constraint satisfaction
- **How the system changes its future environment** — Are predictions passive observations, or do they influence future outcomes?

### The Learning Signal Question

**The first architectural question is always:** *What learning signal does the system actually have access to?*

Once you answer this, the model architecture should follow naturally.

**Examples:**
- **Fraud detection**: Transaction → investigator confirms fraud or legitimate → label available after 5-30 days. **Learning signal**: Delayed labels. **Mechanism**: Supervised learning.
- **Document intelligence**: Millions of unlabeled documents available; humans won't label them all. **Learning signal**: Raw text structure. **Mechanism**: Unsupervised or self-supervised learning.
- **Warehouse robotics**: Robot interacts with environment; today's action affects tomorrow's state. **Learning signal**: Sequential interaction and rewards. **Mechanism**: Reinforcement learning.

---

## 9.2 Supervised Learning: System Design

### The Target Variable Is a Design Decision

In supervised learning, **the target is often treated as an objective fact**, but in production systems, it's usually **a measurement constructed by the organisation**.

**Examples of measurement processes:**
- **Fraud detection**: Actual fraud → investigation → confirmation → fraud label (not direct observation of fraud)
- **Search ranking**: User intent → result exposure → click/dwell/reformulation → relevance signal (clicks may reflect position bias, not true preference)
- **Document classification**: Document → human annotation → assigned category (human judgment may be subjective)

> **Critical insight**: The model is not learning directly from reality. It's learning from a measurement process that approximates reality. A poor target definition can produce a highly accurate model that optimizes the wrong phenomenon.

### Designing the Learning Target: Fraud Detection Example

Consider transactions with three possible outcomes:
- Confirmed fraud
- Confirmed legitimate
- Unresolved / insufficient evidence

**Label policy decisions:**
- What event constitutes a positive label? (confirmed fraud only, or suspected fraud too?)
- How long do we wait before assigning the label? (30 days? 90 days?)
- How are unresolved cases handled? (exclude? treat as negative? treat as positive?)
- Which cases are excluded? (high-value transactions? certain regions?)
- Is the label generated independently of previous model decisions? (if model blocked a transaction, does it affect whether fraud is investigated?)

A mature production system requires an **explicit label policy** as part of the learning-system design.

### The Three Layers: Prediction → Decision → Business Outcome

These three layers should not be automatically collapsed into one model metric:

```
Model produces: P(Fraud | transaction) = 0.73
↓
Business must decide: Approve / Authenticate / Review / Block
↓
Business outcome: Financial loss + customer friction + review cost
```

**Example:**
- Fraud loss per missed fraud: ₹10,000
- False-decline cost per legitimate transaction blocked: ₹500
- Manual review cost: ₹50 per investigation

A model that reduces false negatives by increasing false positives may improve one component while damaging total economics.

**Production objective formula:**
```
Expected total cost = fraud_loss + decline_cost + review_cost
subject to:
  - review_volume ≤ operational_capacity
  - fraud_loss ≤ risk_tolerance
  - decline_rate ≤ acceptable_limit
```

### Class Imbalance as an Objective-Design Problem

A naive model that predicts every transaction as legitimate achieves 99.5% accuracy when 1 in 200 transactions is fraudulent. Yet it provides zero fraud protection.

**The deeper issue**: The natural frequency of an event does not determine its business importance. **Rare events can dominate economic risk.**

**Solution**: Training objectives, sampling strategy, evaluation design, and decision thresholds must reflect the cost and consequences of errors, not merely their frequency.

### Calibration and Risk Scores

**Model A**: Score 0.80 does not consistently correspond to 80% event rate  
**Model B**: Among transactions with score ≈ 0.80, approximately 80% eventually exhibit fraud

Model B is **calibrated**. Calibrated scores support threshold policies:
- < 0.20 → approve
- 0.20–0.60 → authenticate
- 0.60–0.85 → review
- > 0.85 → block

**Thresholds belong to the decision policy, not to the model itself.** Changing business economics or operational capacity changes the policy without retraining.

### Selective Labels and Policy-Induced Data Distribution

**Problem**: Fraud labels are often observed only after investigation, dispute, or chargeback.

```
Transaction → Model score → Decision → Customer response → Investigation → Label
```

The model's decision affects which outcomes become observable. Transactions blocked automatically may never generate the same evidence as transactions allowed through.

**Consequence**: Training data is **policy-dependent**. The model learns from outcomes observed under the *previous decision policy*.

**Solution**: Production systems should track:
- Model version
- Policy version
- Decision made
- Observed outcome

...as connected elements of learning history.

### Counterfactual Learning in Supervised Systems

**Challenge**: We observe what happened, but not what would have happened under alternative actions.

- A blocked transaction: We know it caused no fraudulent loss. But what if it had been approved?
- An approved transaction: Later labeled legitimate. But what would have happened under authentication?

Historical supervised data generally tells us what happened under the actions that were taken. It does not automatically tell us what would have happened under alternative actions.

> **This becomes critical when supervised models influence decisions.**

### Learning-to-Rank: A Different Objective

Search ranking provides a different supervised-learning problem:

**Not**: "Is this document relevant?"  
**But**: "Given several candidate documents, what ordering maximises useful results?"

Training focuses on relative ordering, not binary classification. A model may correctly identify that Documents A and B are both relevant, yet produce poor search experience if it places B above A when users consistently prefer A.

### Human Labels as Measurements

When multiple human reviewers evaluate search results:
- Reviewer 1: Highly relevant
- Reviewer 2: Relevant
- Reviewer 3: Partially relevant

This disagreement is information. It may indicate ambiguous intent, insufficient guidelines, genuine subjectivity, or different expertise.

> **Mature learning systems capture label uncertainty and annotator disagreement, rather than blindly converting every judgment into a binary truth.**

---

## 9.3 Unsupervised and Self-Supervised Learning

### When Labels Are the Bottleneck

Consider an organization with:
- 10 million documents
- 500,000 technical reports
- Millions of emails
- Large collections of contracts and policies

Only a small fraction may have high-quality human labels. **The problem is no longer "which algorithm?" but "how can we extract reusable structure from unlabeled information?"**

This motivates:
- Representation learning
- Self-supervised learning
- Clustering
- Similarity learning
- Anomaly detection

### Representation Learning Changes Architecture

**Traditional task-specific architecture:**
```
Document → Classifier → Document Type
Document → Classifier → Category
Document → Classifier → Sentiment
```
Separate model for every task.

**Representation-oriented architecture:**
```
Document → General Representation → Multiple Downstream Tasks
                ↓
         - Semantic retrieval
         - Classification
         - Clustering
         - Duplicate detection
         - Question answering
         - Recommendation
```

Same representation supports multiple tasks. This changes enterprise AI economics—create reusable representations, specialize downstream.

### Self-Supervised Learning

Self-supervised systems **construct a learning task from the raw data**, without human labels.

**For language:**
- Observed context → predict missing or future information
- Example: Predict next word in sentence

**For images:**
- Partial information → recover or identify missing structure
- Example: Predict masked regions in image

**For sequential systems:**
- Past observations → predict future observations
- Example: Predict next state given history

The critical design question: **Does the artificial training task force the model to learn representations useful for the real downstream task?**

### Pretraining vs. Downstream Task

A document representation model is pretrained on billions of text tokens. Its pretraining objective encourages language understanding.

But enterprise applications may require:
- Contract clause retrieval
- Legal obligation extraction
- Document similarity
- Risk classification

The pretrained representation becomes an intermediate asset. Engineering challenge: **Which properties transfer naturally? Which require adaptation? Which require task-specific supervision?**

This creates layered architecture:
```
General representation → Domain adaptation → Task-specific learning
```

### Embedding Spaces Reflect Training Objectives

An embedding model maps documents into vector space. Similarity between vectors becomes proxy for semantic similarity.

But geometry reflects training objective and training corpus:
- If corpus distinguishes technical terminology, documents cluster by concepts
- If corpus reflects templates, similarity may capture formatting
- If corpus emphasizes domain, it skews toward domain patterns

> **An embedding space does not represent "meaning" universally. It represents the relationships encouraged by the learning process.**

### Clustering Does Not Automatically Create Business Categories

Unsupervised model produces five clusters. Organization expects:
- Contracts / Invoices / Policies / Reports / Emails

Model might discover:
- Department / Writing style / Template / Language / Author

**The clustering is statistically correct. The business interpretation may still be wrong.**

**Solution**: Unsupervised systems require a second stage:
```
Discovered structure → Semantic interpretation → Validation → Operational use
```

### Stability in Unsupervised Learning

If clustering produces 5 clusters today and 6 clusters after retraining next month:

Possible causes:
- Changed data distribution
- Changed representation
- Random initialization
- Altered hyperparameters
- Genuinely new structure

Before operationalizing, examine:
- Cluster stability
- Membership stability
- Semantic coherence
- Business usefulness

> **Unsupervised models cannot be judged solely by producing visually distinct clusters. Question: Are discovered structures sufficiently stable and meaningful for downstream decisions?**

### Anomaly Detection

Cloud platform generates continuous signals: CPU, memory, latency, request rate, error rate, network traffic.

Anomaly detector learns normal behavior patterns. Sudden deviation triggers alert.

**But**: Statistical anomaly ≠ system failure
- Planned deployment creates unusual traffic
- Marketing campaign causes legitimate spike
- New workload creates previously unseen pattern

Learning detects novel statistical behavior. Operational systems determine if behavior indicates incident.

### Contextual Anomaly Detection

CPU utilization of 90% is:
- Normal during scheduled batch job
- Highly unusual for low-traffic service at 3 a.m.

Anomaly depends on context. Production systems condition on:
- Time
- Service
- Deployment state
- Traffic level
- Seasonality
- Dependency state

General principle: **Anomaly detection is learning what is unusual conditional on system context.**

### Unsupervised Learning Requires Downstream Contract

Don't deploy representation or anomaly detector because it produces useful-looking outputs. System needs **downstream contract**:
- What does representation enable?
- Who consumes output?
- What action follows?
- What constitutes useful signal?
- What happens when uncertain?

Value determined by how outputs participate in larger system.

---

## 9.4 Reinforcement Learning

### Prediction vs. Sequential Decision

**Fraud detection** predicts outcome (passive).

**Warehouse routing** requires sequence of decisions. At time *t*:
```
State_t → Action_t → State_(t+1)
```

Action changes environment. Next state depends on what system chose previously.

Learning problem is not "What is the correct label?" but "Which action should be selected now given its future consequences?"

This changes: training data, objective, evaluation, deployment architecture.

### Warehouse Robot: State Representation

Robot's state may include:
- Robot location
- Remaining orders
- Battery level
- Nearby robots
- Aisle congestion
- Current task
- Obstacles

Quality of state representation determines what policy can reason about.
- If congestion info absent, policy cannot learn to avoid it
- If future demand not represented, policy may optimize locally, harming overall fulfilment

State representation is **inductive bias for sequential decision-making.**

### Reward Engineering

Suppose robot receives:
- +10 for order completion
- −1 per unit of travel
- −20 for collision

Reward implicitly defines what system values.

Change to:
- +10 for order completion
- −0.1 per unit of travel
- −100 for collision

Produces very different behavior. **Reward design is not an implementation detail. It's formal representation of desired behavior.**

Poorly specified reward creates mathematically optimal but operationally unacceptable behavior.

### Reward Hacking

Suppose system rewarded for minimizing average delivery time.

Learning system discovers: aggressively prioritize short deliveries improves metric.

But it simultaneously:
- Starves difficult orders
- Increases congestion
- Deprioritises low-volume locations

Model has not failed. It has **optimized the objective it was given** (reward hacking / specification gaming).

**Response**: Redesign reward + constraints + monitoring + evaluation scenarios, not just increase complexity.

### Long-Term Credit Assignment

Robot chooses route causing congestion 15 steps later. Final performance deterioration caused by earlier action with positive immediate reward.

**Credit-assignment problem**: Which earlier actions deserve credit or blame?

Longer horizon = harder problem. Sequential learning needs mechanisms for propagating future-outcome information back to earlier decisions.

**Fundamentally different from one-step supervised prediction.**

### Exploration vs. Exploitation

Balance:
- **Exploitation**: Use actions already known to perform well
- **Exploration**: Test alternatives to discover better policies

In simulation, exploration is inexpensive. In physical warehouse or traffic, exploration creates real operational consequences.

**Solution**: Restrict exploration through:
- Simulation
- Safe action spaces
- Policy constraints
- Offline training
- Human approval

Acceptable exploration is a **system governance decision.**

### Offline Reinforcement Learning

When live experimentation expensive, use historical data:
```
State → Action → Reward → Next State
```

Learner attempts policy improvement without direct environment interaction.

**Limitation**: Historical data only contains outcomes for actions previously taken. If action rarely occurred, insufficient evidence about consequences.

**Cannot automatically infer quality of poorly-represented actions.**

### The Counterfactual Problem in RL

Historical data shows:
```
State S → Action A → Reward 10
```

But does not show:
```
State S → Action B → Reward ?
```

Unless Action B also observed under comparable conditions.

**Offline policy improvement involves counterfactual reasoning.** Estimate what might have happened under untaken action.

Additional risks:
- Distribution shift
- Extrapolation error
- Unsupported policy actions

---

## 9.5 Hybrid Learning Systems

### Why Production Systems Combine Mechanisms

Complex AI systems often contain different learning mechanisms for different responsibilities.

**Autonomous driving:**
- Self-supervised learning → general visual representation
- Supervised learning → object and lane detection
- Prediction models → trajectory estimation
- Sequential decision learning → planning
- Rule/constraint layer → safety boundaries

Forcing entire system into one paradigm often unnecessary.

**Architectural question**: Which learning mechanism is appropriate for each decision boundary?

### Learned Models + Explicit Constraints

Autonomous vehicle uses learned models to estimate:
- Pedestrian location
- Vehicle trajectories
- Road boundaries
- Traffic signals

But certain behaviors remain explicitly constrained:
- Maximum speed
- Collision avoidance
- Restricted manoeuvres
- Traffic rules

**Hybrid system:**
```
Learned perception + Learned prediction + Learned planning + Explicit constraints
```

**Design principle**: Not every requirement needs to be learned. Some behaviors better expressed as explicit constraints.

### Human Feedback as Learning Mechanism

For generative AI, correctness often not binary. Two responses both factually acceptable, but one is:
- Clearer
- More useful
- Better structured
- More aligned with user values

**Human feedback provides learning signal.** System learns from user preferences, not just task completion.

---

## 9.6 System-Level Inductive Biases

### What Is Inductive Bias?

Inductive bias: assumptions built into learning system that guide it toward particular solutions.

Examples of inductive biases encoded in architecture:
- Belief that nearby pixels are related (convolutional networks)
- Belief that language has hierarchical structure (transformers)
- Belief that important features are sparse (L1 regularization)

### Connectivity as Inductive Bias

Graph neural networks encode assumption: connectivity is informative.

Enables predictions based on:
- Entity attributes
- Relationship structure

Rather than attributes alone.

**Useful only when relationship structure itself has meaningful predictive information.**

### Objective Function as Inductive Bias

Two models produce equally good predictions:
- Model A: highly variable outputs
- Model B: smoother trajectories

If application assumes continuity, objective penalizing excessive changes expresses prior preference:

Among solutions fitting observed data, prefer solutions satisfying expected structural behavior.

Other examples of objectives expressing bias:
- Sparsity (prefer few active features)
- Smoothness (prefer gradual changes)
- Robustness (prefer solutions stable to perturbations)
- Fairness (prefer equitable across groups)
- Risk sensitivity (prefer conservative predictions)

### Constraints as Inductive Bias

Resource allocation model can technically allocate 100% to one customer. Business imposes:
- Maximum per-customer allocation
- Minimum service levels
- Regulatory constraints
- Fairness constraints
- Capacity constraints

**Feasible space restricted before learning chooses among remaining possibilities.**

Powerful form of system-level inductive bias: **Explicitly rule out classes of solutions.**

### Data Composition as Learned Bias

Model trained predominantly on technical manuals learns technical-manual language and concepts.

Same algorithm trained on legal contracts produces different learned behavior.

Algorithm identical. **Behavior differs because training distribution → learned representation → downstream behavior.**

> **"Same model architecture" does not imply "same learned system."**

---

## 9.7 Learning System Failure Modes

### Model Correctness ≠ System Correctness

Cloud anomaly model correctly identifies unusual traffic pattern (statistically anomalous).

Operational system nevertheless treats it as critical incident.

Model performed exactly as designed. **Failure occurred in translation: statistical anomaly → operational meaning → action.**

> **Model correctness and system correctness must be evaluated separately.**

### Feedback Loops Change Training Distribution

Consider search:
```
Model ranks result → User sees result → User clicks → Click becomes training signal
→ Model learns higher relevance → Result gets more exposure
```

Model participates in process generating future training data.

Standard IID (independent, identically distributed) assumptions become unrealistic.

**Learning system must be designed with its own influence on data-generating process in mind.**

### Three Types of Drift

1. **Model drift**: Model parameters or version change
2. **Data drift**: Input distribution changes (user behavior shifts, new market segment)
3. **Concept drift**: Relationship between inputs and outcomes changes (fraud tactics evolve)

Additionally:
4. **Policy drift**: Decision rules surrounding model change

Example: Changing fraud threshold from 0.80 to 0.65 alters observed population even if model unchanged.

**Monitoring must distinguish model changes from environment changes from policy changes.**

### Learning System as Closed-Loop Control

For interactive AI:
```
Environment → Observation → Model → Decision → Action → Environment Changes → New Observations
```

Fundamentally different from static prediction pipeline.

System's decisions influence future data. Production systems need explicit mechanisms for:
- Exploration
- Feedback measurement
- Policy versioning
- Counterfactual evaluation
- Safety constraints
- Continuous monitoring

> **This is where machine learning becomes AI systems engineering.**

---

## 9.8 Practical Learning-System Design Framework

### Before Choosing the Algorithm

**Specify these first:**

1. **Outcome** — What real-world outcome are we trying to influence?
2. **Learning signal** — Where does evidence about that outcome come from?
3. **Feedback delay** — How long before signal becomes observable?
4. **Decision influence** — Does model's action change future observations?
5. **Counterfactual availability** — Can we know what would have happened under alternative action?
6. **Constraints** — Which behaviors unacceptable even if objective improves?
7. **Evaluation unit** — Are we evaluating predictions, decisions, or end-to-end business outcomes?

This framework determines whether supervised, unsupervised, reinforcement, or hybrid learning is appropriate.

### From Learning Mechanism to Training System

Once mechanism selected, operationalize it:

```
Learning Objective 
→ Training Data / Interaction Data
→ Training Workflow
→ Experimentation
→ Hyperparameter Optimization
→ Candidate Model Generation
→ Validation
→ Model Selection
→ Deployment
```

Move from "How should the system learn?" to "How do we engineer, automate, reproduce and control learning at scale?"

This leads to model training workflows, training pipelines, and hyperparameter tuning.

---

## Key Takeaways

1. **Learning mechanism is an architecture decision**, not just algorithm selection
2. **What learning signal does the system have access to?** This question should drive mechanism choice
3. **Targets are measurements**, not objective facts. Measure design affects what model learns
4. **Three layers must be separated**: Prediction → Decision → Business outcome
5. **Class imbalance is an objective-design problem**, not an algorithm problem
6. **Unsupervised learning requires downstream contracts** — value determined by how outputs used
7. **Reinforcement learning solves constrained sequential decision problems**, with counterfactual challenges in offline settings
8. **Feedback loops change distributions**; systems must account for their own influence on data
9. **Model correctness ≠ system correctness**; evaluate separately
10. **Production systems often combine mechanisms** — not everything needs to be learned

---
