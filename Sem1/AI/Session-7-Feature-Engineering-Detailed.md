# Session 7: Feature Engineering

> BITS Pilani — **SS ZG662: Introduction to AI Systems** — Instructor: Chandrasekhar Anantrama
>
> **References:** T1 Chapter 4 (Feature Engineering)
>
> **Contact Session:** 7 (Module 2: Data and Feature Engineering)

---

## Table of Contents

- [7.1 What is Feature Engineering?](#71-what-is-feature-engineering)
- [7.2 Feature Extraction](#72-feature-extraction)
- [7.3 Feature Transformation](#73-feature-transformation)
- [7.4 Feature Selection](#74-feature-selection)
- [7.5 Feature Representation](#75-feature-representation)
- [7.6 Feature Design Examples](#76-feature-design-examples)

---

## 7.1 What is Feature Engineering?

### Simple Definition

**Feature engineering** is the process of taking raw data and turning it into **features** — the specific inputs that an ML model uses to make predictions.

> **Analogy:** Imagine you're a cricket selector choosing the national team. The raw data is every ball played by every cricketer in domestic cricket. But you don't feed raw ball-by-ball data to your brain. Instead, you look at **features** like batting average, strike rate, centuries scored, economy rate, match-winning innings — these are engineered features that summarise the raw data into useful signals.

### Why Feature Engineering Matters

| Fact | Explanation |
|---|---|
| Features have the **biggest impact** on model accuracy | A good feature can improve model accuracy by 20-30%. Switching to a fancier model might improve by 2-3%. |
| Data scientists spend **60-70% of their time** on feature engineering | More than model building, tuning, or deployment |
| **"Applied ML is basically feature engineering"** (Andrew Ng) | The choice and quality of features often matters more than the choice of algorithm |

### Raw Data vs Features — Example

**Task:** Predict if a customer will buy a product on Flipkart.

**Raw data (what's in the database):**
```
user_id: USR-123
events: [
  {"action": "view", "product": "laptop-456", "time": "2025-09-01 10:00"},
  {"action": "view", "product": "laptop-789", "time": "2025-09-01 10:15"},
  {"action": "add_to_cart", "product": "laptop-456", "time": "2025-09-01 10:30"},
  {"action": "view", "product": "mouse-101", "time": "2025-09-02 14:00"},
  {"action": "purchase", "product": "laptop-456", "time": "2025-09-03 09:00"},
  ...hundreds more events
]
```

**Engineered features (what the model actually uses):**
```
user_id:                    USR-123
total_views_last_7_days:    45
total_purchases_last_30_days: 3
avg_session_duration_min:   12.5
cart_abandonment_rate:      0.4  (40% of carts not purchased)
days_since_last_purchase:   5
favourite_category:         "electronics"
price_sensitivity:          0.7  (buys mostly during sales)
device_type:                "mobile"
time_of_day_preference:     "morning"
```

The model can't understand raw event logs. But it can understand numbers and categories like "total_views = 45" and "favourite_category = electronics."

---

## 7.2 Feature Extraction

### What is Feature Extraction?

**Feature extraction** is pulling useful information out of raw data — especially from unstructured data (text, images, audio) where features aren't obvious.

> **Analogy:** When a doctor reads an X-ray, they extract features: "I see a shadow in the left lung, bone density looks normal, no fractures." They don't process every pixel — they extract meaningful features. Feature extraction teaches machines to do the same.

### Feature Extraction from Different Data Types

#### From Structured Data (Easy)

| Technique | What It Does | Example |
|---|---|---|
| **Direct use** | Use columns directly as features | Age, income, account_balance → use as-is |
| **Aggregation** | Summarise over time or groups | Total purchases in last 30 days, average order value |
| **Ratio/Difference** | Combine columns mathematically | Revenue per employee, price-to-earnings ratio |
| **Date/Time extraction** | Extract components from timestamps | Day of week, hour of day, is_weekend, days_since_event |

**Live Example — Uber's Features from Structured Ride Data:**
```
Raw: ride_id, pickup_time, dropoff_time, pickup_lat, pickup_lng, fare, rating

Extracted features:
├── ride_duration_minutes = dropoff_time - pickup_time
├── ride_distance_km = haversine(pickup_lat/lng, dropoff_lat/lng)
├── is_peak_hour = 1 if pickup_time between 8-10am or 5-8pm
├── is_weekend = 1 if Saturday/Sunday
├── fare_per_km = fare / ride_distance_km
└── driver_avg_rating_last_50_rides = rolling average
```

#### From Text Data (Medium)

| Technique | What It Does | Example |
|---|---|---|
| **Bag of Words (BoW)** | Count how many times each word appears | "I love this product love it" → {I:1, love:2, this:1, product:1, it:1} |
| **TF-IDF** | Weight words by importance (common words get low weight, rare words get high weight) | "the" gets low weight (appears everywhere), "turbocharged" gets high weight (rare, meaningful) |
| **Word Embeddings** | Convert each word to a vector of numbers that captures meaning | "king" → [0.2, 0.8, 0.1, ...], "queen" → [0.2, 0.7, 0.9, ...] (similar vectors = similar meaning) |
| **Sentence Embeddings** | Convert entire sentence/paragraph to one vector | "This phone has great battery life" → [0.1, 0.5, 0.3, ...768 numbers] |

**Live Example — Zomato Review Analysis:**
```
Raw review: "Food was amazing but delivery was very late and cold"

Extracted features using NLP:
├── sentiment_score: -0.2 (slightly negative overall)
├── food_sentiment: +0.9 (positive about food)
├── delivery_sentiment: -0.8 (negative about delivery)
├── mentioned_topics: ["food_quality", "delivery_speed", "food_temperature"]
├── word_count: 11
├── has_negative_keywords: True ("late", "cold")
└── sentence_embedding: [0.12, -0.34, 0.56, ...768 numbers]
```

#### From Image Data (Hard)

| Technique | What It Does | Example |
|---|---|---|
| **Pre-trained CNN features** | Pass image through a pre-trained CNN, use intermediate layer outputs as features | ImageNet-trained ResNet50 produces a 2048-number vector for any image |
| **Object detection features** | Detect objects in the image and use their counts/positions | "Image contains: 2 people, 1 car, 3 trees" |
| **Colour/texture histograms** | Summarise colour distribution and texture patterns | "Image is 40% blue (sky), 30% green (grass), 30% brown (dirt)" |

**Live Example — Myntra's Product Image Features:**
```
Product image: red_dress.jpg

Extracted features:
├── dominant_colours: ["red", "black"]
├── clothing_type: "dress" (detected by CNN)
├── pattern: "solid" (no prints)
├── style: "casual" (predicted by style classifier)
├── image_embedding: [0.23, -0.11, 0.78, ...2048 numbers]
└── similar_products: ["product-456", "product-789"] (by embedding similarity)
```

#### From Audio Data

| Technique | What It Does | Example |
|---|---|---|
| **MFCCs (Mel-Frequency Cepstral Coefficients)** | Capture audio frequency patterns that represent speech characteristics | 13-40 numbers per audio frame |
| **Speech-to-text** | Convert audio to text, then extract text features | Call centre recording → transcript → sentiment analysis |
| **Speaker embeddings** | Convert voice to a numerical fingerprint | Voice authentication: "Is this Rahul's voice?" |

---

## 7.3 Feature Transformation

### What is Feature Transformation?

**Feature transformation** modifies existing features to make them more useful for the model. Raw feature values may not be in the right form — transformations fix this.

> **Analogy:** Raw ingredients need preparation before cooking. You don't throw a whole onion into the pan — you peel it, chop it, maybe fry it. Similarly, raw features need transformations before feeding to a model.

### Common Transformations

#### 1. Scaling / Normalisation

**Problem:** Features have different scales. Age ranges 0-100, income ranges 10,000-10,00,000. Models like k-NN and neural networks are sensitive to scale — the feature with larger numbers dominates.

| Technique | Formula | When to Use | Example |
|---|---|---|---|
| **Min-Max Scaling** | (x - min) / (max - min) → range [0, 1] | When you need bounded values | Age: 25 → (25-0)/(100-0) = 0.25 |
| **Standard Scaling (Z-score)** | (x - mean) / std_dev → mean=0, std=1 | When data is roughly normally distributed | Income: Rs 50,000 → (50000 - 40000) / 15000 = 0.67 |
| **Log Transformation** | log(x) | When data is highly skewed (few very large values) | Revenue: [100, 200, 150, 50000] → log → [2.0, 2.3, 2.2, 4.7] (tames the outlier) |

**Live Example — Scaling in Ola's Surge Pricing Model:**
```
Before scaling:
├── demand_count: ranges 0 to 50,000 (huge numbers)
├── supply_count: ranges 0 to 5,000
├── temperature: ranges 10 to 45
└── rain_intensity: ranges 0 to 100

Without scaling, demand_count (50,000) dominates.
The model ignores rain_intensity (only goes up to 100).

After Standard Scaling:
├── demand_count: ranges -2.0 to +3.0
├── supply_count: ranges -2.0 to +3.0
├── temperature: ranges -2.0 to +3.0
└── rain_intensity: ranges -2.0 to +3.0

Now all features are on equal footing. Rain intensity gets fair weight.
```

#### 2. Encoding Categorical Variables

**Problem:** ML models work with numbers, not text. How do you convert "Mumbai", "Delhi", "Bangalore" into numbers?

| Technique | How It Works | When to Use | Example |
|---|---|---|---|
| **Label Encoding** | Assign a number to each category | Ordinal data (has natural order) | Education: High School=1, Bachelor=2, Master=3, PhD=4 |
| **One-Hot Encoding** | Create a separate binary column for each category | Nominal data (no natural order) | City: Mumbai → [1,0,0], Delhi → [0,1,0], Bangalore → [0,0,1] |
| **Target Encoding** | Replace category with the average target value for that category | High-cardinality (many unique values) | Pin code 400001 → avg_purchase = Rs 5,000 |
| **Embedding** | Learn a dense vector representation | Very high cardinality + deep learning | Product_ID → [0.2, 0.8, 0.1, 0.5] (learned during training) |

**Why you should NOT use Label Encoding for nominal data:**
If you encode City as Mumbai=1, Delhi=2, Bangalore=3 — the model thinks Delhi is "between" Mumbai and Bangalore, and Bangalore is "more than" Delhi. This is meaningless! Use One-Hot Encoding instead.

#### 3. Handling Missing Values

| Technique | How It Works | When to Use |
|---|---|---|
| **Drop rows** | Remove rows with missing values | Missing values are rare (<5%) and random |
| **Mean/Median imputation** | Fill missing with average or middle value | Numerical features, missing at random |
| **Mode imputation** | Fill with most frequent value | Categorical features |
| **Indicator column** | Add a new column: "is_income_missing" (0 or 1) | The fact that data is missing is itself informative |
| **Model-based imputation** | Use another ML model to predict the missing value | Complex cases, many missing values |

**Live Example — Why "Missing" Can Be a Feature:**
In a loan default prediction model:
- 15% of applicants have missing "employer_name"
- Missing employer often means self-employed or unemployed
- Adding a feature "is_employer_missing = 1" improved model accuracy by 4%
- The missingness itself was a strong signal!

#### 4. Binning / Discretisation

Convert continuous values into bins/categories:

```
Age (continuous) → Age Group (bins)
0-18   → "minor"
19-25  → "young_adult"
26-35  → "adult"
36-50  → "middle_aged"
51+    → "senior"
```

**When useful:** When the relationship between feature and target is non-linear, or when you want to reduce noise from exact values.

#### 5. Interaction Features

Create new features by combining existing ones:

```
price_per_sqft = price / area
bmi = weight / (height * height)
revenue_per_employee = revenue / num_employees
click_through_rate = clicks / impressions
```

These capture relationships that single features miss.

---

## 7.4 Feature Selection

### What is Feature Selection?

**Feature selection** means choosing which features to keep and which to drop. More features isn't always better.

> **Analogy:** When packing for a trip, you don't take everything you own. You select the most useful items. Taking too much makes your bag heavy and hard to manage. Similarly, too many features make the model slow, overfitting-prone, and hard to maintain.

### Why Not Just Use All Features?

| Problem | Explanation | Example |
|---|---|---|
| **Overfitting** | Model memorises noise in irrelevant features instead of learning real patterns | Adding "user's favourite colour" to a fraud detection model — it's noise, not signal |
| **Curse of dimensionality** | As features increase, the data becomes sparse and the model needs exponentially more data | With 100 features, you might need 10x more training data than with 10 features |
| **Slower training and inference** | More features = more computation | Model serving takes 200ms with 500 features, but only 50ms with 50 features |
| **Harder to understand** | Models with fewer features are easier to explain and debug | "The model uses 5 features" is easier to audit than "the model uses 500 features" |

### Three Approaches to Feature Selection

#### 1. Filter Methods (Quick, Independent of Model)

Evaluate each feature independently using statistical tests. Fast but may miss feature interactions.

| Test | When to Use | What It Measures |
|---|---|---|
| **Correlation** | Numerical feature vs numerical target | Linear relationship strength |
| **Chi-squared test** | Categorical feature vs categorical target | Association between categories |
| **Mutual Information** | Any feature vs any target | How much knowing the feature reduces uncertainty about the target |
| **Variance threshold** | Any feature | Remove features with near-zero variance (constant values) |

**Example:**
```python
# Remove features with less than 0.01 correlation with the target
correlations = df.corr()['target'].abs()
selected_features = correlations[correlations > 0.01].index
```

#### 2. Wrapper Methods (Best Accuracy, But Slow)

Train the model with different feature subsets and pick the best one.

| Method | How It Works |
|---|---|
| **Forward Selection** | Start with no features. Add one at a time (whichever improves accuracy most). Stop when adding more doesn't help. |
| **Backward Elimination** | Start with all features. Remove one at a time (whichever hurts accuracy least). Stop when removing more hurts accuracy. |
| **Recursive Feature Elimination (RFE)** | Train model, rank features by importance, remove the least important, repeat. |

These are accurate but slow — impractical with 1000+ features.

#### 3. Embedded Methods (Best Balance)

The model itself learns which features are important during training.

| Method | How It Works |
|---|---|
| **L1 Regularisation (Lasso)** | During training, the model automatically pushes unimportant feature weights to exactly zero. Effectively removes them. |
| **Tree-based importance** | Decision trees / Random Forests / XGBoost naturally rank features by how useful they were for splitting. |

**Live Example — Feature Selection at CRED:**
CRED predicts credit card payment defaults. Initial features: 200+ (income, spending patterns, app usage, location, device, etc.)

After feature selection:
```
Feature Importance (XGBoost):
1. payment_history_score         → 0.35  ← Most important
2. credit_utilisation_ratio      → 0.20
3. avg_monthly_spend             → 0.12
4. days_since_last_payment       → 0.10
5. num_missed_payments_6_months  → 0.08
...
195. phone_brand                 → 0.0001  ← Useless
196. app_theme_preference        → 0.0000  ← Zero importance
```

Keeping only top 20 features: same accuracy, 10x faster inference, much easier to explain to regulators.

---

## 7.5 Feature Representation

### What is Feature Representation?

**Feature representation** is how you encode/represent data so that the model can understand it effectively. The same information can be represented in different ways, and some representations work much better for ML.

### Embeddings — The Most Important Representation

An **embedding** converts a high-dimensional, sparse representation (like a word or product) into a low-dimensional, dense vector of numbers.

> **Analogy:** A map represents the real world in a compact form. Cities that are close together on the map are close in real life. Similarly, an embedding represents items as points in a space — items that are similar are close together in embedding space.

**Word Embedding Example:**
```
"king"  → [0.2, 0.8, 0.1, 0.5]     "man"   → [0.3, 0.7, 0.0, 0.4]
"queen" → [0.2, 0.7, 0.9, 0.5]     "woman" → [0.3, 0.6, 0.8, 0.4]

Notice: 
king - man + woman ≈ queen  (vector arithmetic captures meaning!)
```

**Why embeddings are powerful:**
- Capture similarity: similar items have similar vectors
- Compact: reduce thousands of categories to a few hundred numbers
- Learnable: the model discovers the best representation during training
- Composable: can combine embeddings of different types (user embedding + product embedding)

### Live Example: Spotify's Song Embeddings

Spotify represents every song as a 128-number vector:
```
"Tum Hi Ho" (Arijit Singh)  → [0.8, 0.2, 0.9, 0.1, ...128 numbers]
"Channa Mereya"             → [0.7, 0.3, 0.8, 0.2, ...128 numbers]  ← Similar!
"Thunderstruck" (AC/DC)     → [0.1, 0.9, 0.1, 0.8, ...128 numbers]  ← Very different

The two Hindi romantic songs are close in embedding space.
AC/DC is far away. 
Spotify uses this to recommend: "If you like Tum Hi Ho, try Channa Mereya."
```

### Types of Embeddings Used in AI

| Type | What It Embeds | Dimensions | Live Example |
|---|---|---|---|
| **Word embeddings** (Word2Vec, GloVe) | Individual words | 100-300 | "apple" the fruit vs "Apple" the company — different embeddings based on context |
| **Sentence embeddings** (BERT, Sentence-BERT) | Sentences/paragraphs | 384-1024 | Semantic search: find documents similar to a query |
| **Image embeddings** (ResNet, CLIP) | Images | 512-2048 | Google Lens: "find visually similar products" |
| **User embeddings** | Users | 64-256 | Netflix: represent each user's taste as a vector |
| **Product/Item embeddings** | Products/items | 64-256 | Amazon: represent each product, recommend similar ones |

---

## 7.6 Feature Design Examples

### Complete Feature Design for Three Real Scenarios

#### Example 1: Fraud Detection (HDFC Bank)

**Task:** Predict if a credit card transaction is fraudulent.

```
RAW DATA (per transaction):
card_id, merchant_name, merchant_category, amount, currency,
timestamp, location_lat, location_lng, is_online, card_present

ENGINEERED FEATURES:

Transaction-level features:
├── amount_zscore: how unusual is this amount vs user's history?
├── is_high_amount: 1 if amount > 2x user's average transaction
├── is_foreign_currency: 1 if currency ≠ INR
├── is_online: 1 if online transaction
├── hour_of_day: extracted from timestamp
├── is_night: 1 if between 12am-6am (fraud more common at night)
└── merchant_risk_score: historical fraud rate for this merchant category

Velocity features (speed of transactions):
├── txn_count_last_1_hour: how many transactions in last hour?
├── txn_count_last_24_hours: how many in last day?
├── unique_merchants_last_1_hour: buying at many different stores rapidly?
├── time_since_last_txn_seconds: 2 transactions 10 seconds apart = suspicious
└── amount_last_1_hour: total spend in last hour

Location features:
├── distance_from_last_txn_km: if last txn was in Mumbai and this in London 
│   1 hour later → physically impossible → fraud
├── distance_from_home_km: how far from user's usual location?
├── is_new_location: 1 if user never transacted here before
└── location_velocity_kmph: speed between consecutive txns

User profile features:
├── avg_daily_spend_30_days: typical daily spending
├── avg_txn_amount_30_days: typical transaction amount
├── preferred_merchant_categories: what they usually buy
├── num_cards: how many cards does this user have
└── account_age_days: new accounts are higher risk
```

#### Example 2: Product Recommendation (Amazon)

**Task:** Recommend products to users on the homepage.

```
USER FEATURES:
├── user_embedding: [learned 128-dim vector from purchase history]
├── purchase_count_30_days: 5
├── avg_order_value: Rs 2,500
├── favourite_categories: ["electronics", "books"]
├── browse_to_buy_ratio: 0.05 (buys 5% of what they view)
├── price_sensitivity: 0.8 (high — waits for discounts)
├── session_time_preference: "evening"
├── device: "mobile"
└── days_since_last_purchase: 3

PRODUCT FEATURES:
├── product_embedding: [learned 128-dim vector from user interactions]
├── category: "electronics"
├── price: Rs 1,499
├── avg_rating: 4.3
├── num_reviews: 2,456
├── return_rate: 0.05 (5% returned)
├── days_since_launch: 45
├── is_prime: True
└── discount_percentage: 15%

INTERACTION FEATURES (user × product):
├── user_viewed_similar_products: 3 (viewed 3 similar products recently)
├── user_category_affinity: 0.9 (user loves electronics)
├── price_match: 0.7 (product price matches user's typical range)
├── embedding_similarity: cosine(user_embedding, product_embedding) = 0.85
└── social_proof: 5 friends purchased this product
```

#### Example 3: Delivery Time Prediction (Swiggy)

**Task:** Predict delivery time when user places an order.

```
RESTAURANT FEATURES:
├── avg_prep_time_last_100_orders: 18 min
├── current_active_orders: 7 (restaurant is busy)
├── cuisine_type: "North Indian"
├── rating: 4.2
├── is_peak_hour: 1 (lunch time)
└── prep_time_consistency: 0.85 (how consistent is their prep time)

DELIVERY FEATURES:
├── distance_km: 4.2
├── current_traffic_level: "HIGH" (from Google Maps)
├── rain_intensity: 0 (no rain)
├── is_festival_day: 0
├── active_drivers_within_3km: 12
└── avg_driver_speed_current: 18 kmph

ORDER FEATURES:
├── num_items: 3
├── has_complex_items: 1 (biryani takes longer than naan)
├── order_value: Rs 750
└── is_first_order: 0 (repeat customer)

TIME FEATURES:
├── hour_of_day: 13 (1 PM — lunch rush)
├── day_of_week: "Tuesday"
├── is_weekend: 0
└── minutes_since_restaurant_opened: 240

HISTORICAL FEATURES:
├── avg_delivery_time_this_route_last_7_days: 35 min
├── avg_delivery_time_this_restaurant_last_7_days: 32 min
└── weather_impact_factor: 1.0 (no weather delay expected)
```

### Feature Design Best Practices

| Practice | Why | Example |
|---|---|---|
| **Start simple** | Simple features often work surprisingly well | "time_since_last_purchase" alone predicts churn well |
| **Domain knowledge matters** | Understanding the business helps create powerful features | Banker knows "multiple small ATM withdrawals" is suspicious → create "num_small_atm_txns_1hr" |
| **Temporal features are powerful** | "How recently" and "how often" are almost always useful | Recency: days_since_last_login. Frequency: logins_last_30_days. |
| **Interaction features capture relationships** | Combining features reveals patterns individual features miss | price_per_sqft = price/area reveals value better than price or area alone |
| **Test feature importance** | Not all features help — measure and remove useless ones | After building 200 features, keep only top 30 by XGBoost importance |

---

## Key Terms Glossary (Session 7)

| Term | Simple Meaning |
|---|---|
| **Feature** | A measurable property used as input to an ML model (like a column in a spreadsheet) |
| **Feature Engineering** | The process of creating useful features from raw data |
| **Feature Extraction** | Pulling useful information out of raw data (especially unstructured data) |
| **Feature Transformation** | Modifying features to make them more useful (scaling, encoding, binning) |
| **Feature Selection** | Choosing which features to keep and which to drop |
| **Embedding** | A compact numerical representation that captures meaning (similar items = similar numbers) |
| **One-Hot Encoding** | Converting categories to binary columns (Mumbai → [1,0,0], Delhi → [0,1,0]) |
| **Scaling** | Adjusting features to similar ranges so no single feature dominates |
| **TF-IDF** | A text feature technique that weights words by importance (rare words get high weight) |
| **Bag of Words** | Representing text as word frequency counts |
| **Word2Vec** | An algorithm that converts words to meaningful numerical vectors |
| **Curse of Dimensionality** | More features require exponentially more data — too many features hurts performance |
| **L1 Regularisation (Lasso)** | A technique that automatically removes unimportant features during training |
| **Interaction Features** | New features created by combining existing ones (price/area = price_per_sqft) |

---

*End of Session 7*
