# Session 7: Questions and Answers

> BITS Pilani — SS ZG662: Introduction to AI Systems

---

## Q1: What is Feature Engineering? Why is it considered the most important step in ML?

**Answer:**
Feature engineering is the process of transforming raw data into meaningful input variables (features) that help a machine learning model make better predictions.

**Simple analogy:** Raw data is like raw ingredients (flour, eggs, sugar). Features are like the prepared recipe components (dough, batter, frosting). The ML model is like the oven. You can have the best oven (model) in the world, but if your batter is badly prepared (poor features), the cake (predictions) will be terrible.

**Why it's the most important step:**

| Reason | Explanation |
|---|---|
| **Models learn from features, not raw data** | A model doesn't understand raw text "customer ordered biryani 5 times this week." It needs features like `order_count_last_7_days = 5`, `most_ordered_cuisine = biryani`. |
| **Good features > Complex models** | A simple logistic regression with well-engineered features often outperforms a deep neural network with raw features. Google has published research confirming this. |
| **80/20 rule** | Data scientists spend 80% of their time on data preparation and feature engineering, only 20% on model building. |
| **Domain knowledge encoded** | Features encode human understanding. A fraud detection expert knows that "transaction amount / average transaction amount" is a powerful feature — the model can't discover this ratio easily on its own. |
| **Reduces dimensionality** | Instead of feeding 1000 raw columns, you create 50 meaningful features — the model trains faster and generalises better. |

---

## Q2: What is the difference between Raw Data and Features? Give examples.

**Answer:**

| Aspect | Raw Data | Features |
|---|---|---|
| **What it is** | Original, unprocessed data as collected | Processed, meaningful variables derived from raw data |
| **Usable by ML models?** | Usually not directly | Yes — this is what models train on |
| **Format** | Can be anything — text, images, logs, tables | Numeric vectors (numbers that models understand) |

**Example 1 — Swiggy Delivery Time Prediction:**

| Raw Data | Engineered Features |
|---|---|
| Order timestamp: "2024-01-15 19:30:00" | `hour_of_day = 19`, `is_peak_hour = 1`, `is_weekend = 0`, `day_of_week = Monday` |
| Restaurant address: "BTM Layout, Bangalore" | `restaurant_lat = 12.91`, `restaurant_long = 77.61`, `area_zone = South Bangalore` |
| Customer address: "Koramangala, Bangalore" | `distance_km = 3.2`, `estimated_traffic_score = 7.5` |
| Weather: "Rainy, 24°C" | `is_raining = 1`, `temperature = 24` |
| Restaurant past data: 500 orders, avg prep time 22 min | `avg_prep_time = 22`, `order_volume_today = 45`, `restaurant_rating = 4.2` |

**Example 2 — HDFC Bank Loan Approval:**

| Raw Data | Engineered Features |
|---|---|
| Monthly salary: ₹80,000 | `annual_income = 960000` |
| Loan amount requested: ₹20,00,000 | `loan_to_income_ratio = 2.08` |
| Account opened: March 2018 | `account_age_years = 6`, `is_long_term_customer = 1` |
| Transaction history: 500 records | `avg_monthly_balance = 45000`, `min_balance_breaches = 2`, `salary_credited_regularity = 0.95` |
| CIBIL score: 750 | `cibil_score = 750`, `cibil_category = "good"` |

**Key insight:** The raw data "account opened March 2018" is useless to a model. But the engineered feature `account_age_years = 6` tells the model "this is a long-term customer" — very useful for loan decisions.

---

## Q3: Explain Feature Extraction from different data types — Structured, Text, Image, and Audio.

**Answer:**
Feature extraction is the first step — pulling useful signals out of raw data.

### From Structured Data (easiest)
- **Method:** Aggregations, ratios, date decomposition
- **Example (Flipkart):** From a customer's order history:
  - `total_orders_last_30_days = 12`
  - `avg_order_value = ₹1500`
  - `favourite_category = "Electronics"`
  - `days_since_last_order = 3`
  - `return_rate = 0.15` (15% of orders returned)

### From Text Data
- **Methods:** Bag of Words (BoW), TF-IDF, Word Embeddings (Word2Vec, BERT)
- **Example (Zomato restaurant reviews):**
  - Raw text: "Amazing biryani, but delivery was too late. The food was cold."
  - **BoW features:** `word_amazing = 1, word_biryani = 1, word_late = 1, word_cold = 1`
  - **Sentiment score:** `-0.3` (mixed — positive food, negative delivery)
  - **Topic extraction:** `food_quality = positive, delivery_experience = negative`
  - **BERT embedding:** A 768-dimensional vector that captures the full meaning of the sentence

### From Image Data
- **Methods:** Pixel features, CNN-extracted features, pre-trained model embeddings
- **Example (Myntra product images):**
  - Raw: A 1024x1024 pixel image of a red kurta
  - **Basic features:** `dominant_colour = red, brightness = 0.7, aspect_ratio = 0.75`
  - **CNN features:** Pass through ResNet-50 → get a 2048-dimensional feature vector that captures shape, texture, pattern, colour
  - **Object detection:** `garment_type = kurta, has_embroidery = true, neckline = V-neck`

### From Audio Data
- **Methods:** MFCCs (Mel-Frequency Cepstral Coefficients), spectrograms, speech-to-text + NLP
- **Example (Customer service call at HDFC Bank):**
  - Raw: 5-minute audio recording
  - **Audio features:** `pitch_variation = high, speaking_rate = fast, silence_ratio = 0.1`
  - **Emotion features:** `customer_sentiment = angry, agent_sentiment = calm`
  - **Speech-to-text → NLP features:** `complaint_category = "credit card charge", resolution_status = "unresolved"`
  - **MFCC features:** 13-dimensional vector per time frame — captures voice characteristics

---

## Q4: What are the main Feature Transformation techniques? Explain each with examples.

**Answer:**

### 1. Feature Scaling (Normalisation/Standardisation)
**Why needed:** Features have different scales. "Age" ranges from 0-100, "Income" ranges from 10,000 to 50,00,000. Models like KNN, SVM, and neural networks are sensitive to scale — income would dominate age in distance calculations.

| Technique | Formula | When to Use | Example |
|---|---|---|---|
| **Min-Max Scaling** | (x - min) / (max - min) → range [0, 1] | When you need bounded range | Scale customer age: (28 - 18) / (80 - 18) = 0.16 |
| **Standard Scaling (Z-score)** | (x - mean) / std_dev → mean=0, std=1 | When data is normally distributed | Scale salary: (80000 - 50000) / 15000 = 2.0 |
| **Robust Scaling** | (x - median) / IQR | When data has outliers | Income data with a few billionaires — median-based scaling isn't affected by outliers |

### 2. Encoding Categorical Variables
**Why needed:** ML models need numbers, not text. "City = Mumbai" must become a number.

| Technique | How It Works | When to Use | Example |
|---|---|---|---|
| **One-Hot Encoding** | Create binary column for each category | Few categories (<10), no ordinal relationship | City → `is_Mumbai=1, is_Delhi=0, is_Bangalore=0` |
| **Label Encoding** | Assign a number to each category | Ordinal categories (has order) | Education → `High School=1, Bachelor=2, Master=3, PhD=4` |
| **Target Encoding** | Replace category with mean of target variable | Many categories, tree-based models | Replace pin code with average delivery time for that pin code |
| **Frequency Encoding** | Replace category with its frequency | Many categories | Replace brand name with how often it appears in the dataset |

### 3. Handling Missing Values
**Why needed:** Real data always has gaps. You can't feed "NULL" to a model.

| Technique | When to Use | Example |
|---|---|---|
| **Mean/Median imputation** | Numerical, few missing values | Missing salary → fill with median salary (₹50,000) |
| **Mode imputation** | Categorical | Missing city → fill with most common city ("Bangalore") |
| **Forward/Backward fill** | Time-series data | Missing stock price → use yesterday's closing price |
| **Create a missing indicator** | When missingness itself is informative | `income_missing = 1` — maybe people who don't report income have something to hide (useful for fraud detection!) |
| **Drop the row/column** | >50% missing or feature isn't important | A column with 80% missing values — delete it |
| **Model-based imputation** | Complex patterns | Use KNN or regression to predict missing values from other features |

### 4. Binning (Discretisation)
**Why needed:** Sometimes converting continuous numbers to categories improves performance.

- **Example:** Age → Age groups: `[0-18: "Minor", 18-30: "Young Adult", 30-50: "Middle Age", 50+: "Senior"]`
- **When useful:** When the relationship isn't linear. A person's spending behaviour changes in steps (student → working → retired), not gradually.
- **Indian example:** CIBIL score binning → `[300-579: "Poor", 580-669: "Fair", 670-739: "Good", 740-799: "Very Good", 800-900: "Excellent"]`

### 5. Interaction Features
**Why needed:** Sometimes the combination of two features is more predictive than either alone.

- **Example:** For Swiggy delivery time:
  - `distance_km = 5` (not very informative alone)
  - `is_raining = 1` (not very informative alone)
  - `distance_when_raining = distance_km × is_raining = 5` (VERY informative — distance matters much more when it's raining!)
- **Another example:** For loan prediction:
  - `income = ₹80,000` and `loan_amount = ₹20,00,000`
  - `debt_to_income_ratio = loan_amount / (income × 12) = 2.08` — this RATIO is far more predictive than either number alone

---

## Q5: Explain the three approaches to Feature Selection — Filter, Wrapper, and Embedded methods.

**Answer:**
After creating many features, you need to select only the useful ones. Too many features cause overfitting, slow training, and wasted computation.

### 1. Filter Methods (Fast, simple)
- **How:** Evaluate each feature independently using statistical tests. No model is trained.
- **Analogy:** Screening resumes using basic criteria (degree, years of experience) before interviews.
- **Techniques:**
  - **Correlation:** Remove features highly correlated with each other (if temperature_celsius and temperature_fahrenheit are both features, keep only one)
  - **Chi-squared test:** For categorical features — is there a statistically significant relationship between this feature and the target?
  - **Mutual Information:** How much information about the target does this feature provide?
  - **Variance Threshold:** Remove features with very low variance (a feature that's the same value for 99% of records provides no information)

| Pros | Cons |
|---|---|
| Very fast — no model training | Doesn't consider feature interactions |
| Model-agnostic | May miss features that are weak individually but strong together |

### 2. Wrapper Methods (Accurate, slow)
- **How:** Actually train models with different subsets of features. Select the subset that gives the best performance.
- **Analogy:** Actually interviewing candidates in different team combinations to see which team performs best.
- **Techniques:**
  - **Forward Selection:** Start with 0 features. Add one at a time (the one that improves performance most). Stop when adding more doesn't help.
  - **Backward Elimination:** Start with ALL features. Remove one at a time (the one whose removal hurts least). Stop when removing more hurts performance.
  - **Recursive Feature Elimination (RFE):** Train a model, rank features by importance, remove the least important, repeat.

| Pros | Cons |
|---|---|
| Considers feature interactions | Very slow — trains many models |
| Finds the best subset for YOUR model | Computationally expensive for 100+ features |

### 3. Embedded Methods (Best balance)
- **How:** Feature selection happens DURING model training as part of the algorithm itself.
- **Analogy:** A cricket coach who evaluates players during practice matches, not in separate tryouts.
- **Techniques:**
  - **L1 Regularisation (Lasso):** Adds a penalty that automatically shrinks unimportant feature weights to exactly 0 — effectively removing them.
  - **Tree-based importance:** Random Forest and XGBoost provide feature importance scores as a byproduct of training. Features used for more splits = more important.
  - **Elastic Net:** Combines L1 (for selection) and L2 (for handling correlated features).

| Pros | Cons |
|---|---|
| Efficient — selection happens during training | Tied to specific model type |
| Considers interactions | L1 may randomly choose between correlated features |

**Quick comparison:**

| Aspect | Filter | Wrapper | Embedded |
|---|---|---|---|
| **Speed** | Fastest | Slowest | Medium |
| **Accuracy** | Lower | Highest | High |
| **Considers interactions** | No | Yes | Yes |
| **Example** | Correlation matrix, chi-squared | Forward selection, RFE | Lasso, XGBoost importance |
| **When to use** | Quick baseline, 1000+ features | Small feature set, need best accuracy | Production use, good balance |

---

## Q6: What are Feature Embeddings? Why are they important for modern AI?

**Answer:**
Feature embeddings are dense, lower-dimensional vector representations of data that capture semantic meaning. Instead of representing a word/item as a sparse one-hot vector, embeddings represent it as a compact vector of real numbers.

**The problem embeddings solve:**
- One-hot encoding of 50,000 words → 50,000-dimensional sparse vector (mostly zeros). Very wasteful.
- "Mumbai" and "Delhi" are one-hot encoded as completely different vectors — no notion of similarity.

**What embeddings do:**
- Represent "Mumbai" as [0.23, -0.15, 0.82, ...] (128 dimensions)
- Represent "Delhi" as [0.21, -0.13, 0.79, ...] (128 dimensions)
- These are SIMILAR vectors because Mumbai and Delhi are similar (both Indian cities)
- "Biryani" would be a very different vector — far away in embedding space

**Types of embeddings:**

| Embedding Type | What It Embeds | Technique | Example |
|---|---|---|---|
| **Word Embeddings** | Words/tokens | Word2Vec, GloVe, BERT | "king" - "man" + "woman" ≈ "queen" (captures relationships!) |
| **Sentence Embeddings** | Full sentences | Sentence-BERT | Find similar customer complaints |
| **Image Embeddings** | Images | ResNet, CLIP | Find visually similar products on Myntra |
| **User Embeddings** | Users | Collaborative filtering | Represent Flipkart users as vectors for recommendations |
| **Item Embeddings** | Products | Item2Vec | Represent products as vectors for "similar items" |
| **Graph Embeddings** | Nodes in a graph | Node2Vec, GraphSAGE | Represent people in a social network for friend recommendations |

**Real-world example — Spotify:**
- Each song is represented as a 128-dimensional embedding vector
- Songs that people listen to together end up with similar vectors
- "Shape of You" and "Perfect" (both Ed Sheeran pop songs) → vectors are close
- "Shape of You" and a death metal song → vectors are far apart
- Spotify's Discover Weekly uses these embeddings to find songs similar to what you like

**Why embeddings matter for AI:**
1. **Dimensionality reduction:** 50,000-dimensional one-hot → 128-dimensional embedding
2. **Capture semantics:** Similar items have similar vectors
3. **Transfer learning:** Pre-trained embeddings (BERT, ResNet) work across tasks
4. **Enable similarity search:** Find nearest neighbours in embedding space for recommendations

---

## Q7: Design features for a Fraud Detection system at HDFC Bank.

**Answer:**
This is a practical feature design question. Think about what signals indicate fraud.

**Transaction-Level Features (from the current transaction):**

| Feature | Why It's Useful |
|---|---|
| `transaction_amount` | Unusually large amounts are suspicious |
| `amount_ratio_to_avg = amount / user_avg_transaction` | ₹50,000 is normal for one person but suspicious for another |
| `is_international = 1/0` | International transactions have higher fraud rates |
| `is_online = 1/0` | Card-not-present (online) transactions are riskier |
| `merchant_category` | Certain categories (gambling, crypto) are higher risk |
| `hour_of_day` | Transactions at 3 AM are more suspicious than 3 PM |
| `is_weekend` | Spending patterns differ on weekends |

**User Behaviour Features (from historical patterns):**

| Feature | Why It's Useful |
|---|---|
| `avg_daily_transactions` | If someone usually makes 3 transactions/day and suddenly makes 20, flag it |
| `avg_transaction_amount_30d` | Baseline for what's "normal" for this user |
| `days_since_last_transaction` | A dormant card suddenly used = suspicious |
| `unique_merchants_7d` | If card is used at 15 different merchants in one day, likely stolen |
| `max_amount_ever` | Transaction exceeding all-time max is unusual |
| `velocity = transactions_last_1hr` | 5 transactions in 1 hour is unusual for most people |

**Location/Device Features:**

| Feature | Why It's Useful |
|---|---|
| `distance_from_home = km between transaction and home address` | Transaction 5000km away from home = suspicious |
| `distance_from_last_transaction` | Two transactions 2000km apart within 1 hour = physically impossible (velocity check) |
| `is_new_device = 1/0` | First time using this device to transact |
| `is_new_merchant = 1/0` | Never transacted with this merchant before |
| `country_of_transaction` | Sudden international use from a card never used abroad |

**Interaction Features (combinations):**

| Feature | Why It's Useful |
|---|---|
| `amount_x_is_international` | Large international transaction is riskier than small domestic |
| `hour_x_is_online` | Online transaction at 3 AM is more suspicious than in-store at 3 AM |
| `velocity_x_amount` | Many transactions AND high amounts = very suspicious |
| `new_device_x_new_merchant` | New device AND new merchant = double red flag |

**Aggregate Features (patterns over time):**

| Feature | Why It's Useful |
|---|---|
| `declined_transactions_24h` | Multiple declined transactions (testing stolen card) |
| `amount_increasing_pattern` | Amounts going ₹100 → ₹500 → ₹5000 → ₹50,000 (testing limits) |
| `unique_countries_7d` | Card used in 5 countries in 7 days = likely compromised |

---

## Q8: Design features for a Product Recommendation system at Flipkart.

**Answer:**

**User Features (who is the user?):**

| Feature | Source | Why |
|---|---|---|
| `user_avg_order_value` | Order history | Distinguish budget vs premium shoppers |
| `favourite_category` | Browsing + orders | Personalise by interest |
| `purchase_frequency` | Order history | Active vs occasional shopper |
| `avg_rating_given` | Reviews | Harsh reviewer vs easy rater |
| `preferred_brands` | Order history | Brand loyalty signals |
| `account_age_days` | Registration date | New vs established user |
| `city_tier = 1/2/3` | Address | Tier-1 city users have different preferences |
| `price_sensitivity_score` | % orders with discounts | High score = bargain hunter → recommend deals |

**Product Features (what is the product?):**

| Feature | Source | Why |
|---|---|---|
| `product_category` | Catalog | Obvious — Electronics, Fashion, etc. |
| `avg_rating` | Reviews | Quality signal |
| `num_reviews` | Reviews | Popular vs new product |
| `price_range = Low/Mid/High` | Catalog | Match user's spending habits |
| `discount_percentage` | Pricing | Important for price-sensitive users |
| `return_rate` | Returns data | High return rate = quality issue |
| `days_since_launch` | Catalog | New products need extra promotion |
| `product_embedding` | CNN on product image | Visual similarity for "similar products" section |

**Interaction Features (user × product):**

| Feature | Source | Why |
|---|---|---|
| `has_viewed = 1/0` | Clickstream | Viewed but didn't buy → retarget |
| `has_carted = 1/0` | Cart data | Carted = high intent → push notification |
| `view_count` | Clickstream | Viewed 5 times = very interested |
| `time_on_page_seconds` | Clickstream | Longer time = more interest |
| `user_category_affinity` | Historical orders | How much does this user like this product's category? |
| `brand_match = 1/0` | Orders + catalog | Does this product's brand match user's preferred brands? |
| `price_match_score` | Price vs user avg | How close is this product's price to user's typical spending? |

**Contextual Features (when and where?):**

| Feature | Source | Why |
|---|---|---|
| `hour_of_day` | Timestamp | Browsing patterns differ by time |
| `is_weekend` | Calendar | Weekend shopping vs weekday |
| `is_sale_period` | Business calendar | Behaviour changes during Big Billion Days |
| `device_type = mobile/desktop` | App/Web | Mobile users browse differently (shorter sessions, smaller screens) |
| `season` | Calendar | Winter → jackets, Summer → ACs |

---

## Q9: Design features for Delivery ETA prediction at Swiggy.

**Answer:**
Delivery ETA = Restaurant preparation time + Rider pickup time + Travel time. Each component needs features.

**Restaurant Features:**

| Feature | Why |
|---|---|
| `restaurant_avg_prep_time` | Historical baseline — Biryani place takes longer than chai shop |
| `restaurant_current_active_orders` | 20 active orders = kitchen overloaded = longer prep time |
| `restaurant_rating` | Higher-rated restaurants tend to be more consistent |
| `cuisine_type` | Biryani takes longer to prepare than sandwich |
| `order_complexity = number_of_items` | 5 items takes longer than 1 item |
| `is_peak_hour_for_restaurant` | Some restaurants are busier at lunch, others at dinner |

**Rider Features:**

| Feature | Why |
|---|---|
| `rider_avg_delivery_time` | Some riders are consistently faster |
| `rider_current_distance_to_restaurant` | How far is the nearest available rider |
| `rider_active_orders` | If rider has 3 orders, your order waits |
| `rider_vehicle_type = bike/cycle` | Bike is faster than bicycle |
| `rider_experience_months` | Experienced riders know shortcuts |

**Route Features:**

| Feature | Why |
|---|---|
| `distance_restaurant_to_customer_km` | Longer distance = longer time (obviously) |
| `current_traffic_score (1-10)` | From Google Maps API or historical patterns |
| `num_traffic_signals_on_route` | More signals = more stops |
| `is_same_area = 1/0` | Same locality = much faster |
| `elevation_difference` | Hilly areas (e.g., parts of Hyderabad/Pune) slow down riders |

**Temporal/Weather Features:**

| Feature | Why |
|---|---|
| `hour_of_day` | Rush hour (6-9 PM) = slower |
| `day_of_week` | Weekend traffic patterns differ from weekday |
| `is_raining = 1/0` | Rain significantly increases delivery time |
| `temperature` | Extreme heat = rider fatigue = slower |
| `is_holiday = 1/0` | Holidays have different patterns |
| `is_ipl_match = 1/0` | IPL match days = massive order surge + unusual traffic |

**Interaction Features:**

| Feature | Why |
|---|---|
| `distance × is_raining` | Distance matters much more when it's raining |
| `active_orders × is_peak_hour` | System overload during peak = longer wait |
| `prep_time × order_complexity` | Complex orders from slow restaurants = very long prep |

---

## Q10: What is the difference between Feature Extraction and Feature Selection? Why do we need both?

**Answer:**

| Aspect | Feature Extraction | Feature Selection |
|---|---|---|
| **What it does** | Creates NEW features from raw data | Chooses the BEST features from existing ones |
| **Input** | Raw data (text, images, numbers) | A set of already-created features |
| **Output** | New features that didn't exist before | A subset of existing features |
| **When in pipeline** | Early stage — transforming raw data to features | Later stage — reducing features before model training |
| **Example** | From order timestamp, extract `hour_of_day`, `is_weekend`, `day_of_week` | From 200 extracted features, select the 50 most important ones |
| **Changes data?** | Yes — creates new columns | No — just drops columns |
| **Methods** | Domain knowledge, PCA, embeddings, aggregations | Correlation, Lasso, RFE, tree importance |

**Why we need both (Flipkart example):**

1. **Feature Extraction:** From 5 raw data sources (orders, clicks, reviews, returns, demographics), create 500 features through aggregations, ratios, encodings, embeddings.

2. **Feature Selection:** From 500 features, select the 80 most predictive ones using XGBoost feature importance.

**Why not skip extraction?** → Model can't use raw data.
**Why not skip selection?** → 500 features cause overfitting, slow training, and many are redundant.

---

## Q11: Explain One-Hot Encoding vs Label Encoding. When should you use each?

**Answer:**

### One-Hot Encoding
- **How:** Create a separate binary (0/1) column for each category.
- **Example:** City = {Mumbai, Delhi, Bangalore}

| Original | is_Mumbai | is_Delhi | is_Bangalore |
|---|---|---|---|
| Mumbai | 1 | 0 | 0 |
| Delhi | 0 | 1 | 0 |
| Bangalore | 0 | 0 | 1 |

- **When to use:** When categories have no natural order (nominal data — cities, colours, product categories)
- **Problem:** If you have 1000 cities, you get 1000 new columns (high dimensionality). Use target encoding for high-cardinality features instead.

### Label Encoding
- **How:** Assign an integer to each category.
- **Example:** Education = {High School: 1, Bachelor: 2, Master: 3, PhD: 4}
- **When to use:** When categories have a natural order (ordinal data — education level, satisfaction rating, T-shirt size S/M/L/XL)
- **Problem:** If used for nominal data, the model thinks "Delhi (2) > Mumbai (1)" which is meaningless. This introduces a false ordinal relationship.

**Decision guide:**

| Data Type | Use | Example |
|---|---|---|
| Nominal (no order) + few categories | One-Hot Encoding | Gender, City (if <20 cities), Colour |
| Nominal + many categories (>20) | Target Encoding or Frequency Encoding | Pin code, Product ID, Brand name |
| Ordinal (has order) | Label Encoding | Education level, Rating (1-5), Income bracket (Low/Medium/High) |
| Binary (2 categories) | Simple 0/1 | Gender (M/F), Is_Active (Yes/No) |

---

## Q12: What is PCA (Principal Component Analysis)? When should you use it?

**Answer:**
PCA is a dimensionality reduction technique that transforms a large set of correlated features into a smaller set of uncorrelated features called "principal components."

**Simple analogy:** Imagine you have 100 photos of a person from different angles. PCA is like finding the 10 most informative photos that capture 95% of what the person looks like. You don't need all 100 — those 10 are enough.

**How it works (simplified):**
1. PCA finds the directions (axes) in the data with the MOST variance
2. The first principal component captures the most variance, the second captures the next most, and so on
3. You keep only the top K components that capture 95% of total variance
4. Discard the rest

**When to use PCA:**

| Situation | Example |
|---|---|
| Too many features | 500 features → reduce to 50 with 95% information retained |
| Multicollinearity | Many features are correlated (e.g., height_cm and height_inches). PCA combines them. |
| Visualisation | Reduce to 2-3 dimensions to plot data and see clusters |
| Preprocessing for models | Some models struggle with high-dimensional data — PCA helps |

**When NOT to use PCA:**

| Situation | Why |
|---|---|
| Interpretability needed | PCA components are combinations of features — "Component 1" has no business meaning |
| Tree-based models | Random Forest, XGBoost handle high dimensions well — PCA doesn't help much |
| Sparse data | PCA works best on dense, continuous data |

**Indian example:** CIBIL uses 50+ features to calculate a credit score. PCA could reduce these to 10-15 principal components for faster scoring, though they'd lose interpretability ("why was my score low?").

---

## Q13: Scenario — You're building a model to predict house prices in Bangalore. What features would you engineer?

**Answer:**

**Property Features:**

| Feature | Source | Why |
|---|---|---|
| `area_sqft` | Listing data | Primary price driver |
| `num_bedrooms` | Listing | 3BHK vs 1BHK |
| `num_bathrooms` | Listing | Luxury indicator |
| `floor_number` | Listing | Higher floors premium in apartments |
| `total_floors_in_building` | Listing | 2nd floor in a 3-storey vs 20-storey building |
| `age_of_property_years` | Listing | New construction premium |
| `property_type = Apartment/Villa/Plot` | Listing | Different pricing models |
| `furnishing_status = Furnished/Semi/Unfurnished` | Listing | Fully furnished commands premium |
| `has_parking = 1/0` | Listing | Important in Bangalore |
| `price_per_sqft_area_avg` | Derived | Average price in this locality |

**Location Features (extremely important — "location, location, location!"):**

| Feature | Source | Why |
|---|---|---|
| `locality` (Koramangala, Whitefield, etc.) | Address | Single most important feature |
| `distance_to_nearest_metro_km` | Google Maps | Metro proximity = premium |
| `distance_to_cbd_km` (MG Road/Indiranagar) | Maps | Closer to centre = expensive |
| `distance_to_nearest_school_km` | Maps | Families pay premium for school proximity |
| `distance_to_nearest_hospital_km` | Maps | Safety/convenience factor |
| `distance_to_tech_park_km` | Maps | Proximity to Manyata/ORR/Whitefield tech parks |
| `num_restaurants_within_2km` | Maps API | Proxy for developed/vibrant area |
| `flood_zone = 1/0` | Government data | Areas like Bellandur have flooding — depresses prices |

**Market Features:**

| Feature | Source | Why |
|---|---|---|
| `avg_price_trend_locality_6months` | Historical data | Rising area vs declining area |
| `num_listings_in_locality` | Current listings | More supply = lower prices |
| `nearby_sold_price_avg` | Transaction data | Comparable sales price |

**Interaction Features:**

| Feature | Why |
|---|---|
| `area_sqft × floor_number` | Large apartment on high floor = ultra premium |
| `bedrooms / area_sqft` | Compact vs spacious BHK design |
| `distance_to_metro × property_age` | New property near metro = highest premium |

---

## Q14: How do you handle a categorical feature with 10,000 unique values (high cardinality)? Give 4 approaches.

**Answer:**
High-cardinality categorical features are tricky. One-hot encoding would create 10,000 columns — unacceptable.

**Example:** Flipkart has 10,000 brands. `brand_name` is a feature.

### Approach 1: Target Encoding (Mean Encoding)
- Replace each category with the average target value for that category.
- Example: Replace "Samsung" with the average purchase probability for Samsung products (say 0.35).
- **Risk:** Can cause overfitting. Mitigate with regularisation or cross-validation-based encoding.

### Approach 2: Frequency Encoding
- Replace each category with how often it appears in the dataset.
- Example: "Samsung" appears 5000 times → replace with 5000 (or normalised frequency 0.05).
- **When useful:** When popularity/frequency correlates with the target. Popular brands might have different buying patterns.

### Approach 3: Embedding (Neural Network approach)
- Learn a dense vector representation for each category as part of model training.
- Example: Each of 10,000 brands gets a 16-dimensional embedding vector. The model learns these embeddings during training.
- **Best for:** Deep learning models. This is how Flipkart's recommendation system handles millions of product IDs.

### Approach 4: Grouping/Bucketing
- Group rare categories together.
- Example: Keep top 100 brands as individual categories. Group remaining 9,900 brands into "Other."
- **When useful:** When rare categories don't have enough data to learn patterns. "BrandXYZ" with only 3 orders — can't learn anything meaningful.

**Which to choose?**

| Approach | Best For | Risk |
|---|---|---|
| Target Encoding | Tree models (XGBoost, LightGBM) | Overfitting if not regularised |
| Frequency Encoding | Quick baseline, any model | Loses information beyond frequency |
| Embeddings | Neural networks, deep learning | Needs large data to learn good embeddings |
| Grouping | Simple models, small datasets | Loses information about grouped categories |

---

## Q15: What is the "Curse of Dimensionality"? How does feature engineering help address it?

**Answer:**
The Curse of Dimensionality means that as the number of features increases, the amount of data needed to train a good model increases exponentially.

**Simple analogy:**
- In 1 dimension (a line), 10 data points cover the space reasonably well.
- In 2 dimensions (a square), you need 10 × 10 = 100 points to cover the space equally well.
- In 3 dimensions (a cube), you need 10 × 10 × 10 = 1000 points.
- In 100 dimensions, you need 10^100 points — more than atoms in the universe!

**Practical problems with too many features:**

| Problem | What Happens | Example |
|---|---|---|
| **Data becomes sparse** | In high dimensions, all data points become equally far apart | Distance-based algorithms (KNN) stop working — "nearest neighbour" is meaningless when everyone is equidistant |
| **Overfitting** | Model memorises noise instead of patterns | With 1000 features and 500 rows, the model can find spurious correlations |
| **Slow training** | More features = more computation | Training time increases linearly or quadratically with number of features |
| **Diminishing returns** | Adding more features beyond a point hurts performance | After 100 useful features, the next 100 are noise that confuses the model |

**How feature engineering helps:**

1. **Feature Selection:** Remove redundant/useless features (500 → 50)
2. **PCA:** Reduce dimensions while preserving information (500 → 30 principal components)
3. **Feature Extraction:** Create fewer, more meaningful features instead of using all raw columns
4. **Embeddings:** Represent high-dimensional sparse vectors (50,000-dim one-hot) as dense low-dimensional vectors (128-dim embedding)
5. **Domain Knowledge:** An expert knows which 20 features matter most — no need for 500

---

## Q16: Explain Interaction Features and Polynomial Features with examples. When are they useful?

**Answer:**

### Interaction Features
Multiply two features together to capture their combined effect.

**Example — Swiggy delivery prediction:**
- Feature A: `distance_km = 8`
- Feature B: `is_raining = 1`
- Interaction: `distance_in_rain = distance_km × is_raining = 8`

**Why useful?** The effect of distance on delivery time changes when it's raining. An 8km delivery takes 25 minutes normally but 45 minutes in rain. The interaction feature captures this — the model learns that distance has a bigger coefficient when multiplied by rain.

### Polynomial Features
Create squared, cubed, or higher-power versions of features.

**Example — House prices in Bangalore:**
- Feature: `area_sqft = 1500`
- Polynomial: `area_sqft_squared = 2,250,000`

**Why useful?** Price doesn't increase linearly with area. A 3000 sqft apartment isn't just 2× the price of 1500 sqft — it's often 3-4× because very large apartments are luxury. The squared term captures this non-linear relationship.

**When to use:**

| Situation | Use |
|---|---|
| Two features interact (effect of one depends on the other) | Interaction features |
| Relationship between feature and target is non-linear (curved) | Polynomial features |
| Using linear models (linear regression, logistic regression) | Both — these models can't learn non-linearity without these features |
| Using tree-based models (XGBoost, Random Forest) | Less necessary — trees naturally capture interactions and non-linearity through splits |

**Warning:** Polynomial features can explode the feature count. 100 features with degree-2 polynomials = ~5000 features. Use selectively and combine with feature selection.

---

## Q17: Scenario — You're building a customer churn prediction model for Jio. Design the features.

**Answer:**

**Usage Features (how much do they use the service?):**

| Feature | Why |
|---|---|
| `monthly_data_usage_gb` | Declining usage = losing interest |
| `data_usage_trend_3months = increasing/stable/decreasing` | Trend matters more than current value |
| `daily_calls_count` | Low call usage might mean switching to WhatsApp/competitor |
| `monthly_recharge_amount` | Downgrading plan = cost-conscious = churn risk |
| `days_since_last_recharge` | Long gap = might have switched to another SIM |

**Engagement Features:**

| Feature | Why |
|---|---|
| `jio_cinema_usage_hours` | Engaged in ecosystem = less likely to churn |
| `jio_mart_orders` | Multiple Jio services used = sticky customer |
| `app_opens_per_week` | Declining app usage = disengagement |
| `customer_support_calls_30d` | Many support calls = frustrated customer |
| `complaint_count_90d` | Unresolved complaints drive churn |

**Account Features:**

| Feature | Why |
|---|---|
| `account_age_months` | New customers churn more than long-term ones |
| `plan_type = prepaid/postpaid` | Prepaid customers churn more easily |
| `num_family_connections` | Family plan users are stickier |
| `has_number_portability_inquiry = 1/0` | If they checked MNP, they're thinking of leaving! |
| `payment_failure_count` | Payment issues = involuntary churn risk |

**Network Quality Features:**

| Feature | Why |
|---|---|
| `avg_call_drop_rate` | Poor network = frustrated customer |
| `avg_download_speed_mbps` | Slow internet = reason to switch |
| `coverage_score_for_area` | Poor coverage in their area = churn |
| `network_complaints_in_area` | Area-level network issues |

**Interaction/Derived Features:**

| Feature | Why |
|---|---|
| `support_calls × complaint_unresolved` | Many calls + unresolved = very high churn risk |
| `usage_decrease_pct = (current - previous) / previous` | Quantifies decline |
| `competitor_offer_in_area = 1/0` | Airtel or Vi offering better plans in their area |
| `days_to_plan_expiry` | Churn happens at renewal points |

---

## Q18: What are the common mistakes in Feature Engineering? How to avoid them?

**Answer:**

### Mistake 1: Data Leakage in Features
- **What:** Using features that contain future information or target-derived information.
- **Example:** Using `total_purchases_next_month` to predict churn. This value is only known in the future.
- **Fix:** For every feature, ask: "Would I have this information at the time I need to make the prediction?"

### Mistake 2: Not Handling Missing Values Properly
- **What:** Dropping rows with missing values (losing data) or filling with the wrong method.
- **Example:** Filling missing income with mean income. But income is skewed (few very rich people pull mean up). Use median instead.
- **Fix:** Understand the distribution before choosing imputation. Sometimes missingness itself is a feature.

### Mistake 3: Encoding Nominal Features as Ordinal
- **What:** Using Label Encoding (1, 2, 3) for non-ordered categories.
- **Example:** Encoding City: Mumbai=1, Delhi=2, Bangalore=3. The model now thinks Bangalore > Mumbai, which is meaningless.
- **Fix:** Use One-Hot Encoding for nominal features, Label Encoding only for ordinal features.

### Mistake 4: Not Scaling Before Distance-Based Models
- **What:** Using features with different scales in KNN, SVM, or neural networks.
- **Example:** Age (0-100) and Income (10,000-50,00,000). KNN will completely ignore age because income differences are 1000x larger.
- **Fix:** Always scale features before KNN, SVM, or neural networks. Tree-based models don't need scaling.

### Mistake 5: Creating Too Many Features Without Selection
- **What:** Engineering 1000 features and feeding all of them to the model.
- **Problem:** Overfitting, slow training, curse of dimensionality.
- **Fix:** Always follow feature creation with feature selection. Use Lasso, tree importance, or correlation analysis to keep only useful features.

### Mistake 6: Ignoring Feature Interactions
- **What:** Only using individual features, missing important combinations.
- **Example:** For loan prediction, using `income` and `loan_amount` separately instead of creating `debt_to_income_ratio`.
- **Fix:** Think about business logic — which features should be combined? Ratios, products, and differences often create powerful features.

### Mistake 7: Fitting Transformations on Test Data
- **What:** Fitting a scaler or encoder on the entire dataset (including test data) before splitting.
- **Problem:** Test data information leaks into training through the scaler parameters.
- **Fix:** Fit transformations on training data only, then apply the fitted transformer to test data.

---

## Q19: How does Feature Engineering differ for Traditional ML vs Deep Learning?

**Answer:**

| Aspect | Traditional ML (XGBoost, Random Forest, SVM) | Deep Learning (Neural Networks, CNNs, Transformers) |
|---|---|---|
| **Feature engineering effort** | Very high — this is where you win or lose | Lower — model learns features automatically |
| **What you provide** | Manually engineered features (ratios, aggregations, encodings) | Raw or lightly processed data (pixels, tokens) |
| **Domain knowledge** | Essential — you encode business logic into features | Less critical (but still helps) |
| **Feature selection** | Required — too many features cause overfitting | Less critical — deep models handle high dimensions |
| **Scaling** | Required for some models (SVM, KNN), not for trees | Always required (neural networks need scaled inputs) |
| **Embedding learning** | You provide pre-computed embeddings as features | Model learns embeddings as part of training |
| **Data requirement** | Works with smaller datasets + good features | Needs large datasets to learn features automatically |

**Example — Sentiment Analysis of Zomato reviews:**

**Traditional ML approach:**
1. Manual feature extraction: TF-IDF vectors, word count, sentiment lexicon score, exclamation count, capital letter ratio
2. Feature selection: Select top 500 features using chi-squared test
3. Train SVM on these 500 features
4. Result: 85% accuracy

**Deep Learning approach:**
1. Tokenize text (minimal feature engineering)
2. Feed tokens into BERT (pre-trained transformer)
3. Fine-tune BERT on Zomato review labels
4. Result: 92% accuracy (model learned its own features)

**Key takeaway:** Deep learning reduces manual feature engineering but doesn't eliminate it. Even with deep learning, features like "time since last order" or "user's average rating" still need to be engineered manually because they come from structured data that the model can't "see" automatically.

---

## Q20: Scenario — You have 200 raw features for predicting Flipkart customer lifetime value. Walk through the complete feature engineering pipeline.

**Answer:**

**Step 1: Feature Extraction (create new features from raw data)**
- From timestamps: `account_age_days`, `days_since_last_order`, `orders_per_month`
- From transactions: `avg_order_value`, `total_spending`, `max_single_order`
- From browsing: `avg_session_duration`, `products_viewed_per_session`, `search_to_buy_ratio`
- From categories: `num_unique_categories_ordered`, `favourite_category`, `category_diversity_score`
- **Result:** 200 raw → 350 features

**Step 2: Handle Missing Values**
- For income: median imputation (income is skewed)
- For browsing data of new users: create `is_new_user = 1` indicator feature
- For rarely-missing features (< 5%): mean/mode imputation
- For heavily-missing features (> 50%): drop the feature
- **Result:** 350 → 320 features (dropped 30 with >50% missing)

**Step 3: Encode Categorical Variables**
- City (20 values): Target Encoding (replace with avg CLV per city)
- Product category (500 values): Embedding or Frequency Encoding
- Gender (2 values): Binary encoding (0/1)
- Payment method (5 values): One-Hot Encoding
- **Result:** 320 features (dimensions may change slightly)

**Step 4: Feature Scaling**
- Standard Scaling for numerical features
- Fit scaler on TRAINING data only
- Apply to test data using the same fitted scaler

**Step 5: Create Interaction Features**
- `order_value × frequency = spending_velocity`
- `recency × frequency = engagement_score`
- `discount_percentage × total_orders = discount_sensitivity`
- **Result:** 320 → 340 features

**Step 6: Feature Selection**
- Method 1: Remove features with correlation > 0.95 (redundant) → Remove 40 features
- Method 2: Train XGBoost and extract feature importance → Keep top 100
- Method 3: Verify with Lasso (L1) — check if same features are selected
- **Final result:** 100 selected features

**Step 7: Validate**
- Train model on selected features
- Compare performance with all 340 features vs selected 100
- If similar performance with 100 features → success (simpler model, same accuracy)
- If performance drops > 2% → revisit selection criteria

**Pipeline summary:**
```
200 raw → 350 extracted → 320 (missing handled) → 340 (interactions) → 100 (selected)
```
