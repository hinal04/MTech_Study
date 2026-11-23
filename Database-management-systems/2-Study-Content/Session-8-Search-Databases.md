# Session 8: Search Databases

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [8.1 Search Database Fundamentals](#81-search-database-fundamentals)
- [8.2 Full-Text Search and Text Analysis](#82-full-text-search-and-text-analysis)
- [8.3 Inverted Indexing](#83-inverted-indexing)
- [8.4 Relevance Scoring and Ranking](#84-relevance-scoring-and-ranking)
- [8.5 Elasticsearch and OpenSearch](#85-elasticsearch-and-opensearch)
- [8.6 Semantic and Hybrid Search](#86-semantic-and-hybrid-search)
- [8.7 Polyglot Persistence](#87-polyglot-persistence)

---

## 8.1 Search Database Fundamentals

### Why Search Databases?

Traditional OLTP and OLAP databases are optimized for structured queries and transactions. However, real-world applications often require:

1. **Full-text search capabilities** - Finding documents containing specific words/phrases
2. **Relevance ranking** - Ordering results by relevance, not just matching records
3. **Text analysis** - Processing natural language for meaningful retrieval
4. **Fast retrieval at scale** - Handling millions of documents with sub-second response times
5. **Text preprocessing** - Tokenization, stemming, synonym expansion

**Search Databases** are specialized systems designed from the ground up for these text-rich retrieval workloads.

### Search Database Characteristics

| Characteristic | Description |
|---|---|
| **Index Type** | Inverted indexes mapping terms to documents |
| **Query Model** | Full-text search with relevance scoring |
| **Data Model** | Semi-structured (typically JSON documents) |
| **Consistency** | Eventually consistent, optimized for search speed |
| **Scaling** | Horizontal scaling via sharding |
| **Use Cases** | Search engines, e-commerce, analytics, logs |

### Key Differences from Traditional Databases

| Aspect | Traditional DB | Search DB |
|---|---|---|
| **Primary Goal** | ACID transactions | Fast text retrieval |
| **Indexing** | B-tree indexes | Inverted indexes |
| **Query Type** | SQL predicates | Natural language keywords |
| **Result Ordering** | No ranking | Relevance-based ranking |
| **Consistency** | Strong | Eventually consistent |
| **Scalability** | Vertical primarily | Horizontal via sharding |

---

## 8.2 Full-Text Search and Text Analysis

### Text Analysis Pipeline

Full-text search systems process both indexed documents and query strings through a multi-step analysis pipeline:

```
Input Text
    ↓
Character Filtering (remove special chars, HTML)
    ↓
Tokenization (break into words)
    ↓
Lowercasing (normalize case)
    ↓
Stemming/Lemmatization (reduce to base form)
    ↓
Stop Word Removal (remove common words)
    ↓
Synonym Expansion (add related terms)
    ↓
Final Tokens
```

### Tokenization

**Tokenization** breaks text into individual searchable units (tokens/terms).

**Examples:**
- "The quick brown fox" → ["the", "quick", "brown", "fox"]
- "user@example.com" → ["user", "example", "com"] or ["user@example.com"]
- "O'Brien" → ["O", "Brien"] or ["O'Brien"]

**Considerations:**
- Handle punctuation (hyphens, apostrophes)
- Language-specific rules (contractions, compound words)
- Domain-specific requirements (email addresses, URLs, codes)

### Stemming and Lemmatization

**Stemming** removes prefixes/suffixes to get the word root.

**Examples:**
- running, runs, ran → run
- connection, connected, connecting → connect
- happily, happiness, happy → happi

**Lemmatization** maps words to their base form (lemma) using language knowledge.

**Examples:**
- ran, running, runs → run
- better → good
- was, were, is → be

**Trade-offs:**
- Stemming: Fast, language-independent, may over-normalize
- Lemmatization: More accurate, language-dependent, slower

### Stop Words

**Stop words** are common words providing little search value. Common removals:
- Articles: "the", "a", "an"
- Prepositions: "in", "on", "at", "to"
- Conjunctions: "and", "or", "but"
- Pronouns: "he", "she", "it", "this"

**Benefits:**
- Reduce index size (10-15% size reduction)
- Improve query performance
- Reduce noise in results

**Trade-offs:**
- May miss important phrase matches ("To be or not to be")
- Language-specific lists needed

### Synonym Expansion

**Synonym expansion** adds related terms during indexing or query time.

**Examples:**
- auto, automobile, car → all stored/searched together
- truck, lorry → mapped for English variants
- NYC, New York City, NY → all equivalent

**Implementation approaches:**
- Query-time expansion (slower, more comprehensive)
- Index-time expansion (faster searches, larger index)
- Synonym dictionaries or thesaurus management

---

## 8.3 Inverted Indexing

### Concept and Structure

An **inverted index** (or inverted file) is the reverse of a normal index. Instead of:

```
Document → Terms
Doc1 → [term1, term2, term3]
Doc2 → [term2, term3, term4]
```

An inverted index stores:

```
Term → Documents
term1 → [Doc1]
term2 → [Doc1, Doc2, Doc3]
term3 → [Doc1, Doc2]
term4 → [Doc2, Doc4]
```

### Why Inverted Indexes Work

**Problem:** Searching through all documents for a term is slow (O(n) complexity).

**Solution:** Pre-build index mapping terms to documents.

**Benefit:** Query becomes O(1) lookup, returning all matching documents instantly.

### Inverted Index Structure

```
Term Dictionary (sorted for binary search)
↓
"apple" → Posting List → [Doc1, Doc3, Doc5, ...]
"application" → Posting List → [Doc2, Doc4, ...]
"apply" → Posting List → [Doc1, Doc2, Doc3, ...]
```

**Posting List** for each term contains:
- Document IDs where term appears
- Term frequency (how many times in document)
- Position information (which field, position in text)
- Metadata (phrase info, term weights)

### Storage Optimization

**Techniques to reduce index size:**

1. **Variable-length encoding** - Use fewer bytes for small numbers
2. **Delta encoding** - Store differences between consecutive doc IDs
3. **Dictionary compression** - Compress term dictionary
4. **Bit-level compression** - Pack posting lists as bitfields

**Example Delta Encoding:**
```
Doc IDs: [1, 5, 7, 20, 25]
Deltas:  [1, 4, 2, 13, 5]  // Smaller numbers compress better
```

### Index Construction

**Single-pass indexing:**
1. Read document, tokenize, analyze
2. Add tokens to in-memory posting lists
3. When memory full, write to disk
4. Merge multiple disk segments into final index

**Incremental indexing:**
- Maintain in-memory buffer for new documents
- Periodically flush to disk and merge with existing segments
- Enables real-time index updates (near real-time search)

---

## 8.4 Relevance Scoring and Ranking

### Why Ranking Matters

Traditional SQL queries return all matching records unordered. Search databases rank results by relevance because:

1. Users care about top results, not all matches
2. Result quality varies (some documents more relevant than others)
3. Ranking enables search quality optimization
4. Personalization and business requirements affect ranking

### BM25 Algorithm

**BM25 (Best Matching 25)** is the most widely used ranking function. It considers:

1. **Term Frequency (TF)** - How often term appears in document
2. **Inverse Document Frequency (IDF)** - How rare the term is across corpus
3. **Document Length Normalization** - Avoid bias toward longer documents
4. **Field-specific weights** - Different importance for title vs. body

### BM25 Formula

```
score(D, Q) = Σ IDF(qi) * (f(qi, D) * (k1 + 1)) / (f(qi, D) + k1 * (1 - b + b * |D|/avgdl))
```

Where:
- **Q** = query terms
- **D** = document
- **f(qi, D)** = frequency of term qi in document D
- **|D|** = document length in words
- **avgdl** = average document length in corpus
- **k1** = parameter controlling TF saturation (typically 1.2)
- **b** = parameter controlling length normalization (typically 0.75)
- **IDF(qi)** = log((N - df(qi) + 0.5) / (df(qi) + 0.5))
- **N** = total documents in corpus
- **df(qi)** = documents containing term qi

### Understanding BM25 Components

**Term Frequency (TF) Saturation:**
- Not linear - second occurrence of term doesn't double relevance
- More common in relevant documents
- Plateaus to avoid boosting long documents unfairly

**Inverse Document Frequency (IDF):**
- Rare terms have higher weight
- "database" appears in many docs → lower IDF
- "BigTable" appears rarely → higher IDF
- Prevents common words from dominating

**Document Length Normalization:**
- Long documents naturally have more term occurrences
- BM25 normalizes to prevent bias
- Parameter **b** controls degree of normalization
  - b=0: No normalization (longer docs ranked higher)
  - b=1: Full normalization (term frequency only matters)
  - b=0.75: Typical compromise

### Field Weighting

Different fields have different importance:

```
Title query match: weight = 10
Meta description match: weight = 5
Body text match: weight = 1
```

BM25 extended with field-specific parameters:
- Document structure preserved in index
- Different IDF and length normalization per field
- Allows fine-tuning search quality

---

## 8.5 Elasticsearch and OpenSearch

### Architecture Overview

**Elasticsearch** (and open-source fork **OpenSearch**) are distributed search engines built for scalability.

```
┌─ Cluster ─────────────────────────────────┐
│                                           │
│  ┌─ Node 1 ─────┐  ┌─ Node 2 ─────┐    │
│  │ Index Shard  │  │ Index Shard  │    │
│  │ (Replica)    │  │ (Primary)    │    │
│  └──────────────┘  └──────────────┘    │
│                                           │
│  ┌─ Node 3 ─────────────────────────┐  │
│  │ Index Shard (Primary) + Replica  │  │
│  └───────────────────────────────────┘  │
│                                           │
└───────────────────────────────────────────┘
```

### Core Concepts

| Concept | Definition |
|---|---|
| **Cluster** | Collection of nodes sharing cluster name |
| **Node** | Single Elasticsearch instance |
| **Index** | Named collection of documents (like database table) |
| **Shard** | Horizontal partition of an index |
| **Replica** | Copy of a shard for redundancy and performance |
| **Document** | Individual searchable JSON object |
| **Mapping** | Schema defining field types and analysis |

### Data Model

```
Index
├─ Shard 0
│  ├─ Document 1: {user: "alice", email: "alice@example.com", ...}
│  ├─ Document 2: {user: "bob", email: "bob@example.com", ...}
│  └─ ...
├─ Shard 1
│  ├─ Document 3: ...
│  └─ ...
└─ Replicas (redundant copies of each shard)
```

### Mapping Definition

A **mapping** defines document schema and field analysis:

```json
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "standard"
      },
      "description": {
        "type": "text",
        "analyzer": "english"
      },
      "price": {
        "type": "float"
      },
      "created_at": {
        "type": "date",
        "format": "yyyy-MM-dd HH:mm:ss"
      },
      "tags": {
        "type": "keyword"
      }
    }
  }
}
```

### Common Field Types

| Type | Purpose | Searchability |
|---|---|---|
| **text** | Analyzed text search | Full-text search |
| **keyword** | Exact/partial matching | Filtering, sorting |
| **numeric** | Integer, float, etc. | Range queries |
| **date** | Timestamps | Date range queries |
| **boolean** | True/false values | Boolean filters |
| **geo_point** | Latitude/longitude | Distance queries |

### Query DSL

Elasticsearch uses **Query DSL** (Domain Specific Language) for complex queries:

```json
{
  "query": {
    "bool": {
      "must": [
        {"match": {"title": "python programming"}}
      ],
      "filter": [
        {"range": {"price": {"lte": 50}}}
      ],
      "should": [
        {"match": {"tags": "bestseller"}}
      ],
      "must_not": [
        {"term": {"status": "out_of_stock"}}
      ]
    }
  }
}
```

### Scaling via Sharding

**Horizontal Scaling:**
1. Single index too large for one node
2. Split into multiple **shards**
3. Each shard distributed to different nodes
4. Queries execute on all shards in parallel
5. Results aggregated from all shards

**Shard Strategy:**
- More shards = better parallelism but more overhead
- Typical: 1-5 shards per index depending on size/query volume

---

## 8.6 Semantic and Hybrid Search

### Traditional Lexical Search Limitations

**Lexical search** matches keywords exactly:

```
Query: "best laptop under 1000"
Matches: "best laptop" OR "laptop under 1000"
Misses: 
  - "top computer for less than $1000" (synonym issue)
  - "cheap notebook computer" (semantic meaning)
```

**Limitations:**
- Requires exact word matches
- Cannot understand meaning or intent
- Misses relevant documents with different vocabulary
- Subject to typos and language variations

### Semantic Search with Embeddings

**Semantic search** understands meaning using vector embeddings.

**Process:**
1. Convert query to embedding: "best laptop under 1000" → [0.2, -0.5, 0.8, ...]
2. Convert documents to embeddings: "great notebook computer cheap" → [0.19, -0.48, 0.82, ...]
3. Find similar vectors using distance metrics
4. Return documents with smallest distance

**Vector Representations:**
- Learned from large language models (BERT, GPT, etc.)
- Encode semantic meaning in multi-dimensional space
- Similar meanings have similar embeddings

**Advantages:**
- Understand synonyms and related concepts
- Handle typos and misspellings
- Capture semantic intent
- Language-independent (some models multilingual)

### Distance Metrics for Similarity

| Metric | Formula | Use Case |
|---|---|---|
| **Cosine Similarity** | 1 - cos(A,B) | Most common, normalized |
| **Euclidean Distance** | √Σ(ai-bi)² | Geometric space |
| **Manhattan Distance** | Σ\|ai-bi\| | Sparse vectors |
| **Dot Product** | Σ(ai*bi) | Performance optimized |

**Cosine Similarity** most popular because:
- Magnitude-independent (normalized)
- Fast computation
- Ranges from 0 to 1
- Works well with high-dimensional vectors

### Hybrid Search Architecture

**Hybrid search** combines lexical and semantic approaches:

```
Query
├─ Lexical Search (inverted index)
│  └─ Returns matches with keyword relevance
└─ Semantic Search (vector similarity)
   └─ Returns matches with semantic relevance

Results Fusion
├─ Rank Fusion (combine rankings)
└─ Score Normalization (make scores comparable)
```

**Benefits:**
- Lexical catches exact matches and factual queries
- Semantic catches intent and synonym matches
- Fusion gives best of both worlds

**Example Implementation:**

```python
# Lexical search score (BM25)
lexical_score = calculate_bm25(query, documents)

# Semantic search score (vector similarity)
query_embedding = model.encode(query)
semantic_scores = []
for doc in documents:
    doc_embedding = model.encode(doc)
    score = cosine_similarity(query_embedding, doc_embedding)
    semantic_scores.append(score)

# Normalize and combine
combined_score = 0.6 * normalize(lexical_score) + 0.4 * semantic_scores
# (weights tuned based on application)
```

---

## 8.7 Polyglot Persistence

### Single Database Limitations

A single database cannot optimally serve all application requirements:

| Requirement | Best System |
|---|---|
| **Transactions** | RDBMS (SQL) |
| **Search** | Search Engine (Elasticsearch) |
| **Cache** | Cache (Redis) |
| **Time Series** | Time-series DB (InfluxDB) |
| **Graphs** | Graph DB (Neo4j) |
| **Documents** | Document DB (MongoDB) |

### Polyglot Persistence Architecture

**Polyglot persistence** uses multiple specialized databases:

```
Application Layer
     ↓
┌────────────────────────────────────────┐
│  Write Operations (Transactions)      │
│  → PostgreSQL (ACID, consistency)     │
│                                        │
│  Search Operations (Full-text)        │
│  → Elasticsearch (relevance ranking)  │
│                                        │
│  Cache Layer (Performance)            │
│  → Redis (in-memory, fast access)     │
│                                        │
│  Analytics (Historical)               │
│  → Data Warehouse (aggregations)      │
│                                        │
│  Real-time Events (Streaming)         │
│  → Kafka (event stream)               │
└────────────────────────────────────────┘
```

### Implementation Patterns

**Synchronization Approaches:**

1. **Write-Through Pattern:**
   - Application writes to primary DB first
   - Then updates search index
   - Ensures consistency but slower

2. **Change Data Capture (CDC):**
   - Monitor transaction log of primary DB
   - Capture changes automatically
   - Asynchronously update secondary systems
   - Eventual consistency but decoupled

3. **Event Sourcing:**
   - Store all changes as events
   - Secondary systems subscribe to events
   - Rebuild any system state from event log
   - Maximum flexibility and auditability

### Trade-offs

| Benefit | Cost |
|---|---|
| **Optimal performance** for each workload | Operational complexity |
| **Scalability** without compromise | Data synchronization challenges |
| **Flexibility** to choose best tools | Distributed consistency issues |
| **Fault isolation** (one DB failure limited) | Monitoring multiple systems |

### Common Polyglot Stack Example

**E-commerce Platform:**
- **PostgreSQL**: Orders, inventory, user accounts (transactional consistency)
- **Elasticsearch**: Product search, catalog (relevance ranking)
- **Redis**: Shopping cart, session cache (performance)
- **Data Warehouse**: Sales analytics, reporting (historical analysis)
- **Kafka**: Order events, fulfillment workflow (real-time processing)

---

## Summary

Search databases are specialized systems optimized for text-rich retrieval workloads:

1. **Full-text search** processes text through analysis pipelines (tokenization, stemming, etc.)
2. **Inverted indexes** enable fast term-to-document lookup
3. **BM25 ranking** orders results by relevance considering term frequency, rarity, and document length
4. **Elasticsearch/OpenSearch** provide distributed search at scale
5. **Semantic search** understands meaning using vector embeddings
6. **Hybrid search** combines lexical and semantic for comprehensive retrieval
7. **Polyglot persistence** uses specialized databases for different workloads

Understanding these concepts enables building search-intensive applications that provide users with relevant, fast search experiences.
