# Session 12: Vector Databases

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [12.1 Vector Representation and Embeddings](#121-vector-representation-and-embeddings)
- [12.2 Similarity Search](#122-similarity-search)
- [12.3 Distance Metrics](#123-distance-metrics)
- [12.4 Approximate Nearest Neighbor (ANN)](#124-approximate-nearest-neighbor-ann)
- [12.5 Indexing Techniques](#125-indexing-techniques)
- [12.6 Vector Database Architectures](#126-vector-database-architectures)
- [12.7 Applications in AI/ML](#127-applications-in-aiml)

---

## 12.1 Vector Representation and Embeddings

### What is a Vector Embedding?

A **vector embedding** (or embedding) is a numerical representation of data in multi-dimensional space.

**Key idea:** Convert unstructured data (text, images, audio) into structured vectors where:
- Similar items have similar vectors
- Distance between vectors correlates with difference
- Operations on vectors reveal semantic relationships

### How Embeddings Work

**Example: Word Embeddings**

```
Word: "king"      → Vector: [0.5, -0.2, 0.8, 0.1, 0.3, ...]
Word: "queen"     → Vector: [0.48, -0.19, 0.78, 0.12, 0.28, ...]
Word: "dog"       → Vector: [0.2, 0.9, -0.1, 0.5, 0.1, ...]

Property: vector("king") - vector("man") + vector("woman") ≈ vector("queen")
```

**Example: Sentence Embeddings**

```
Document: "The quick brown fox jumps"
Sentence Embedding: [0.12, 0.45, -0.33, 0.18, 0.67, ...] (384 or more dimensions)

Document: "A fast reddish fox leaps"
Sentence Embedding: [0.11, 0.44, -0.32, 0.19, 0.66, ...] (similar to first)
```

### Embedding Models

**Popular models:**

| Model | Type | Dimensions | Use Case |
|---|---|---|---|
| **Word2Vec** | Word-level | 300 | Word similarity |
| **GloVe** | Word-level | 300 | Word relationships |
| **BERT** | Sentence/text | 768-1024 | Text understanding |
| **GPT Embeddings** | General text | 1536 | General purpose |
| **Vision Transformer** | Images | 1024 | Image similarity |
| **OpenAI Embeddings** | General | 1536 | Production use |

### Key Properties of Embeddings

1. **Semantic Preservation:** Similar meanings have nearby vectors
2. **Dimensionality:** High-dimensional space captures nuances
3. **Composability:** Can combine/manipulate vectors mathematically
4. **Language Agnostic:** Some models work across languages
5. **Domain Specific:** Specialized models for domain knowledge

---

## 12.2 Similarity Search

### Core Problem

**Goal:** Given a query embedding, find the K most similar vectors in a large collection.

```
Query Vector (what user searches for)
    ↓
Compare to all vectors in database
    ↓
Find most similar (smallest distance)
    ↓
Return top K results
```

### Brute Force Similarity Search

**Algorithm:** Compare query to every vector

```python
def brute_force_search(query_vector, all_vectors, k=5):
    distances = []
    for i, vector in enumerate(all_vectors):
        distance = euclidean_distance(query_vector, vector)
        distances.append((distance, i))
    
    # Sort and return top-k
    distances.sort()
    return distances[:k]
```

**Characteristics:**
- Guarantees exact results
- O(n) time complexity
- Works for small datasets
- Impractical for millions/billions of vectors

### Approximate Nearest Neighbor (ANN) Search

**Idea:** Trade some accuracy for massive speed improvement

```
Brute Force: 1 second, exact results for 1M vectors
ANN:         1 millisecond, 99% accurate for 1M vectors
```

**Key insight:** Users want fast good results, not slow perfect results

---

## 12.3 Distance Metrics

### Euclidean Distance

**Formula:** √(Σ(ai - bi)²)

```
Vector A: [1, 2, 3]
Vector B: [4, 5, 6]

Distance = √((1-4)² + (2-5)² + (3-6)²) = √(9 + 9 + 9) = √27 ≈ 5.2
```

**Properties:**
- Geometric distance in space
- Magnitude-sensitive (longer vectors further away)
- Works for any dimensions
- Computationally more expensive

**Use case:** When magnitude matters (image coordinates)

### Cosine Similarity

**Formula:** (A · B) / (||A|| × ||B||)

Result ranges from -1 (opposite) to +1 (identical)

**Distance:** 1 - cosine_similarity

```
Vector A: [1, 2, 3]
Vector B: [2, 4, 6]  (direction same, magnitude different)

Cosine similarity = 1.0 (identical direction)
```

**Properties:**
- Direction-based, magnitude-independent
- Normalized (0 to 1 for similarity)
- Fast to compute
- Works well for text embeddings

**Use case:** Most common for text, NLP, semantic search

### Manhattan Distance

**Formula:** Σ|ai - bi|

```
Vector A: [1, 2, 3]
Vector B: [4, 5, 6]

Distance = |1-4| + |2-5| + |3-6| = 3 + 3 + 3 = 9
```

**Properties:**
- Sum of absolute differences
- Faster than Euclidean
- Works for sparse vectors

**Use case:** High-dimensional sparse data

### Dot Product

**Formula:** Σ(ai × bi)

```
Vector A: [1, 2, 3]
Vector B: [4, 5, 6]

Dot Product = (1×4) + (2×5) + (3×6) = 4 + 10 + 18 = 32
```

**Properties:**
- Magnitude-sensitive
- Fastest computation
- Used in neural networks
- Not normalized

**Use case:** When magnitude matters and speed critical

### Metric Comparison

| Metric | Speed | Normalized | Use Case |
|---|---|---|---|
| **Cosine** | Fast | Yes | Text, NLP |
| **Euclidean** | Medium | No | Spatial data |
| **Manhattan** | Fast | No | High-dim sparse |
| **Dot Product** | Fastest | No | Performance critical |

---

## 12.4 Approximate Nearest Neighbor (ANN)

### Why ANN Needed

Brute force search too slow for:
- Millions/billions of vectors
- Real-time queries
- Complex high-dimensional spaces
- Mobile/edge applications

ANN offers 100-1000x speedup at cost of ~1% accuracy loss.

### Trade-offs

```
Accuracy vs Speed

100% ├─ Brute Force (slow, exact)
      │      ╱
 95%  ├─────╱
      │    ╱
 90%  ├───╱─ ANN Algorithms
      │  ╱     (fast, approximate)
 85%  ├─╱
      │
      └────────────────────── Speed
      Slow          Fast
```

### Common ANN Algorithms

**HNSW (Hierarchical Navigable Small World):**
- Hierarchical graph structure
- Navigates from coarse to fine
- Excellent recall (~99%)
- Memory efficient
- Used in: Pinecone, Weaviate, Milvus

**IVF (Inverted File Index):**
- Cluster vectors into groups
- Search most relevant clusters first
- Faster but lower recall
- Good for very large datasets
- Used in: FAISS, Elasticsearch

**LSH (Locality Sensitive Hashing):**
- Hash similar vectors to same buckets
- Fast approximate search
- Lower precision/recall
- Good for real-time requirements
- Used in: Redis Search

**PQ (Product Quantization):**
- Compress vectors into compact form
- Reduces memory and computation
- Trade accuracy for speed
- Used in: FAISS, combined with IVF

---

## 12.5 Indexing Techniques

### HNSW Index

**Hierarchical Navigable Small World:**

```
Layer 2 (coarse):  ●─────●
                   │     │
Layer 1 (medium):  ●─●─●─●
                   │ │ │ │
Layer 0 (fine):    ●●●●●●●●

Search starts at top layer, navigates to fine level
```

**Advantages:**
- Fast search (logarithmic)
- High recall rates
- Practical for large datasets
- Incremental insertion
- No re-indexing needed

**Parameters:**
- M: Max connections per node
- ef_construction: Quality of index
- ef_search: Speed/accuracy trade-off

**Algorithm:**
1. Start at entry point in top layer
2. Find nearest neighbor at current layer
3. Move to next lower layer
4. Repeat until reaching base layer
5. Return k nearest neighbors

### IVF (Inverted File) Index

**Inverted File with Flat:**

```
Cluster Centers:
- Center 1: [0.1, 0.2, 0.3]
- Center 2: [0.5, 0.6, 0.7]
- Center 3: [0.9, 0.8, 0.7]

Inverted Lists:
- Cluster 1 → vectors [1, 5, 23, 56, ...]
- Cluster 2 → vectors [2, 7, 34, 67, ...]
- Cluster 3 → vectors [3, 8, 45, 78, ...]

Query Search:
1. Find closest cluster center to query
2. Search vectors in that cluster (and neighbors)
3. Return top-k
```

**Advantages:**
- Fast for large datasets
- Reasonable recall
- Scales well
- Parallelizable

**Disadvantages:**
- Needs re-clustering on inserts
- Cluster quality matters
- Lower recall than HNSW

---

## 12.6 Vector Database Architectures

### Dedicated Vector Databases

**Pinecone:**
- Fully managed cloud service
- Zero-shot semantic search
- Metadata filtering
- Real-time updates
- Multi-tenancy

**Architecture:**
```
Application API
      ↓
Index Servers (HNSW)
      ↓
Vector Storage
      ↓
Metadata Store
```

**Milvus:**
- Open-source vector database
- Flexible deployment (local, cloud, Kubernetes)
- Multiple index types (HNSW, IVF, etc.)
- Hybrid search (vector + traditional)

**Weaviate:**
- Open-source knowledge graph
- GraphQL API
- Semantic classification
- Cross-reference learning

### Embedding as a Service

**OpenAI Embeddings API:**
```
Text Input
    ↓
API Call
    ↓
OpenAI Model (text-embedding-3-large)
    ↓
Vector Output (1536 dimensions)
```

**Flow:**
1. Send text to API
2. Get back embedding
3. Store/query with vector DB

### Hybrid Search (Vector + Traditional)

**Combining vector and traditional database:**

```
Query: "affordable luxury watches"

Vector Search Path:
  → Embedding API: [0.12, 0.45, -0.33, ...]
  → Find semantically similar products
  → "premium timepieces under $5000"

Traditional Filter Path:
  → price < 5000
  → category = "watches"

Fusion:
  → Merge results from both
  → Rank-fusion algorithms
  → Return top-k
```

---

## 12.7 Applications in AI/ML

### Semantic Search

**Problem:** Keyword search misses synonyms

```
Query: "how to fix laptop screen"
Keyword search: Returns only exact matches
Semantic search: Also returns "monitor repair", "display replacement"
```

**Implementation:**
1. Embed query and documents
2. Vector similarity search
3. Return semantically related results

**Use cases:** Search engines, documentation search, product discovery

### Recommendation Systems

**Collaborative filtering with vectors:**

```
User Embeddings:
- User 1 interest vector: [0.2, 0.8, -0.3, ...]
- User 2 interest vector: [0.19, 0.82, -0.31, ...]  (similar user)

Product Embeddings:
- Product A: [0.15, 0.85, -0.25, ...]
- Product B: [0.9, -0.1, 0.5, ...]

Recommendation:
Find products similar to User 1 interests
Based on what similar users liked
```

**Advantages:**
- Captures subtle preferences
- Handles new products (cold-start)
- Fast inference

### Question Answering

**Retrieval-augmented generation (RAG):**

```
Question: "What is the ROI of marketing automation?"

Step 1: Embed question
Step 2: Search vector DB for related documents
Step 3: Retrieve top-k relevant documents
Step 4: Pass to LLM with context
Step 5: LLM generates answer using retrieved context

Result: Accurate, sourced answer
```

### Anomaly Detection

**Identify outliers in high-dimensional data:**

```
Normal transaction vectors cluster together
Fraudulent transactions far away (different embedding)

Distance-based detection:
- Calculate distance from normal cluster
- If distance > threshold → anomaly
- Flag for review
```

**Use cases:** Fraud detection, network intrusions, sensor anomalies

### Image Similarity

**Find visually similar images:**

```
Query Image
    ↓
Vision Model (ResNet, ViT)
    ↓
Image Embedding (1024 dims)
    ↓
Vector DB Search
    ↓
Similar Images Returned

Use cases: Duplicate detection, reverse image search, product matching
```

### NLP and Named Entity Recognition

**Embedding-based entity matching:**

```
"John Smith" in document A
"John Smith" in document B  → Are they same person?

Embedding-based approach:
- Context vectors for each mention
- Measure distance between contexts
- If similar contexts → likely same entity
- More robust than string matching
```

---

## Practical Vector Database Query Example

**Using Pinecone/Milvus:**

```python
import pinecone

# Initialize
pinecone.init(api_key="xxx", environment="us-west1-gcp")
index = pinecone.Index("products")

# Embed and store
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('all-MiniLM-L6-v2')

texts = ["luxury watch under 5000", "affordable laptop computer", ...]
embeddings = model.encode(texts)

# Insert vectors with metadata
index.upsert([
    ("id1", embedding1, {"price": 4500, "category": "watches"}),
    ("id2", embedding2, {"price": 1200, "category": "laptops"}),
])

# Search
query = "cheap watch"
query_embedding = model.encode([query])[0]
results = index.query(query_embedding, top_k=5, include_metadata=True)

# Results with metadata filtering
for result in results:
    if result["metadata"]["price"] < 5000:
        print(f"Match: {result['id']}, Score: {result['score']}")
```

---

## Summary

Vector databases enable semantic understanding of data:

1. **Vector embeddings** - Numerical representations preserving semantic meaning
2. **Similarity search** - Finding similar items based on distance
3. **Distance metrics** - Cosine similarity most common for text
4. **ANN algorithms** - HNSW and IVF for fast approximate search
5. **Indexing techniques** - Hierarchical structures for scalability
6. **Vector database architectures** - Dedicated systems like Pinecone and Milvus
7. **AI/ML applications** - Semantic search, recommendations, anomaly detection

Vector databases are essential infrastructure for modern AI applications requiring semantic understanding and similarity-based operations.
