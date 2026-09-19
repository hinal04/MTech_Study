# Session 7: Feature Engineering (From Handout)

> BITS Pilani — SS ZG662 | Module 2: Data & Feature Engineering

---

## 7.1 What is Feature Engineering?

**Definition:** Process of creating useful input features from raw data for ML models.

> "Applied ML is basically feature engineering." — Andrew Ng

### Raw Data vs Features

Raw data (customer_id, transaction_log, timestamps) → Engineered features (avg_transaction_last_30d, days_since_last_purchase, purchase_frequency).

---

## 7.2 Feature Extraction

### From Structured Data

| Technique | Example |
|---|---|
| Aggregation | avg, sum, count, min, max over time windows |
| Ratio | revenue_per_customer = total_revenue / customer_count |
| Date extraction | day_of_week, month, hour, is_weekend, days_since_event |
| Difference | balance_change = current_balance - previous_balance |

### From Text

| Technique | Output | Use Case |
|---|---|---|
| Bag of Words (BoW) | Word frequency vector | Simple text classification |
| TF-IDF | Weighted word importance | Document search, keyword extraction |
| Word embeddings (Word2Vec) | Dense vector per word | Semantic similarity |
| Sentence embeddings (BERT) | Dense vector per sentence | Semantic search, classification |

### From Images

| Technique | Output |
|---|---|
| CNN features | Feature map from convolutional layers |
| Object detection features | Bounding boxes, object counts |
| Pre-trained embeddings (ResNet, CLIP) | Dense vector per image |

### From Audio

| Technique | Output |
|---|---|
| MFCCs | Mel-frequency cepstral coefficients (audio fingerprint) |
| Speech-to-text | Transcribed text (then apply text features) |
| Spectrograms | Visual representation of audio frequencies |

---

## 7.3 Feature Transformation

### Scaling

| Method | Formula | Output Range | When |
|---|---|---|---|
| Min-Max normalization | (x-min)/(max-min) | 0 to 1 | Known bounds, no outliers |
| Z-score standardization | (x-mean)/std | ~-3 to +3 | Outliers present, normal distribution assumed |
| Log transform | log(x) | Compressed | Skewed distributions (income, prices) |

### Encoding Categorical Variables

| Method | How | When |
|---|---|---|
| One-hot encoding | Create binary column per category | Nominal categories (city, color) |
| Label encoding | Map to integers (0, 1, 2...) | Ordinal categories (low/medium/high) |
| Target encoding | Replace category with mean of target | High-cardinality categories (zip code) |
| Embedding | Learned dense vector per category | Very high cardinality, deep learning |

### Missing Value Handling (5 Methods)

1. **Drop rows** — when few rows affected (<5%)
2. **Mean/median imputation** — numerical features
3. **Mode imputation** — categorical features
4. **Model-based imputation** — predict missing from other features
5. **Missing indicator** — add binary `is_missing` column

### Binning

Convert continuous → categorical. Example: age → [0-18, 18-30, 30-50, 50+]. Reduces noise, handles non-linear relationships.

### Interaction Features

Combine features to capture relationships: `price_per_sqft = price / area`, `bmi = weight / height²`.

---

## 7.4 Feature Selection

### Why Not Use All Features?

- **Overfitting** — model memorizes noise in irrelevant features
- **Curse of dimensionality** — more features need exponentially more data
- **Slower training** — more features = more computation
- **Harder to explain** — simpler models are more interpretable

### Three Approaches

| Approach | How | Methods | Speed |
|---|---|---|---|
| **Filter** | Score features independently, rank, select top-k | Correlation, chi-squared, mutual information | Fast |
| **Wrapper** | Try feature subsets, evaluate model each time | Forward selection, backward elimination, RFE | Slow |
| **Embedded** | Feature selection during model training | L1/Lasso (zeros out unimportant features), tree-based importance (XGBoost feature importance) | Medium |

---

## 7.5 Embeddings

### Definition

Compact numerical vectors that capture semantic meaning. Similar items → similar vectors.

**"king - man + woman ≈ queen"** — embeddings capture relationships.

### Types of Embeddings

| Type | What | Example |
|---|---|---|
| Word embeddings | Vector per word | Word2Vec, GloVe |
| Sentence embeddings | Vector per sentence/document | BERT, Sentence-BERT |
| Image embeddings | Vector per image | ResNet, CLIP |
| User embeddings | Vector per user (learned from behavior) | Netflix user profiles |
| Product embeddings | Vector per item | Amazon product vectors |

**Spotify example:** Songs represented as embeddings. Similar songs = close vectors. Used for Discover Weekly recommendations.

---

## 7.6 Feature Design Examples

### Example 1: Fraud Detection (HDFC Bank)

| Category | Features |
|---|---|
| Transaction | amount, amount_vs_avg, merchant_category, is_international |
| Velocity | txn_count_last_1hr, txn_count_last_24hr, unique_merchants_1hr |
| Behavioral | time_of_day, day_of_week, is_weekend, device_change |
| Historical | avg_txn_amount_30d, max_txn_ever, days_since_last_txn |
| Location | distance_from_home, is_new_city, geo_velocity (km/hr between txns) |

20+ features engineered from raw transaction logs.

### Example 2: Product Recommendation (Amazon)

| Category | Features |
|---|---|
| User | purchase_history_embedding, category_preferences, price_sensitivity |
| Product | category, price, avg_rating, review_count, seller_rating |
| Interaction | viewed_not_purchased, time_on_product_page, add_to_cart_rate |
| Context | time_of_day, device, session_duration, search_query |

### Example 3: Delivery ETA (Swiggy)

| Category | Features |
|---|---|
| Restaurant | avg_prep_time, current_order_queue, cuisine_type |
| Delivery | distance_km, traffic_level, weather, active_drivers_nearby |
| Order | item_count, total_amount, special_instructions |
| Time | hour, day_of_week, is_weekend, is_peak_hour, is_holiday |
| Historical | avg_delivery_time_this_route_7d, weather_impact_factor |

### Feature Design Best Practices

| Practice | Why |
|---|---|
| Start simple | Simple features often work surprisingly well |
| Domain knowledge matters | Business understanding creates powerful features |
| Temporal features are powerful | Recency and frequency almost always useful |
| Interaction features capture relationships | Combined features reveal hidden patterns |
| Test feature importance | Build many, keep only top-k by importance score |

---
