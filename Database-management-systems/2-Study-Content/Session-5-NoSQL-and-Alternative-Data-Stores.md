# Session 5: NoSQL and Alternative Data Stores

> BITS Pilani — **SSZG507: Modern Database Systems**
>
> **References:** T2: Martin Kleppmann, *Designing Data-Intensive Applications*, Ch.2-5

---

## Table of Contents

- [5.1 Why NoSQL?](#51-why-nosql)
- [5.2 NoSQL Types](#52-nosql-types)
- [5.3 Document Stores](#53-document-stores)
- [5.4 Key-Value Stores](#54-key-value-stores)
- [5.5 Column-Family Stores](#55-column-family-stores)
- [5.6 Graph Databases](#56-graph-databases)
- [5.7 NoSQL Trade-offs](#57-nosql-trade-offs)
- [5.8 Choosing the Right Database](#58-choosing-the-right-database)

---

## 5.1 Why NoSQL?

### Limitations of Traditional SQL

**SQL Databases Designed For:**
- Structured data with predefined schema
- ACID transactions
- Normalized data (avoiding redundancy)
- Strong consistency

**Problems at Scale:**
- **Schema Rigidity:** Hard to add new fields; migrations complex
- **Scaling:** Horizontal scaling (sharding) difficult
- **Unstructured Data:** JSON, images, videos fit poorly
- **Performance:** JOINs expensive with large datasets
- **Availability:** ACID transactions vs. availability trade-off

### When SQL Breaks Down

```
Traditional DBMS: 10 GB, ACID, normalized schema
Twitter: 300+ GB/day, billions of tweets, flexible schema
Instagram: Billions of images, petabytes storage
Netflix: Streaming recommendations, real-time personalization
```

### NoSQL Philosophy

**Trade-offs Embraced:**
- **Eventual Consistency:** Accept temporary inconsistency
- **Denormalization:** Accept redundancy for performance
- **Schema Flexibility:** Allow unstructured/semi-structured data
- **Horizontal Scaling:** Partition data across servers
- **Specialized Systems:** Different tool for different problem

---

## 5.2 NoSQL Types

### Document Stores (MongoDB, CouchDB, Firebase)

**Model:** Collections of self-describing documents (JSON/BSON)

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Alice",
  "email": "alice@example.com",
  "address": {
    "street": "123 Main St",
    "city": "Boston",
    "zip": "02101"
  },
  "tags": ["developer", "python"],
  "created": ISODate("2021-01-15")
}
```

**Strengths:**
- Flexible schema; documents in same collection can differ
- Natural JSON/BSON storage
- Expressive query language
- Embedded documents (array/object fields)

**Limitations:**
- No JOINs (query across collections inefficient)
- Transactions limited (single document or multi-document with overhead)
- Not ideal for highly connected data

### Key-Value Stores (Redis, Memcached, DynamoDB)

**Model:** Simple key → value mapping

```
SET user:101:name "Alice"
SET user:101:email "alice@example.com"
GET user:101:name
→ "Alice"
```

**Strengths:**
- Ultra-fast; in-memory access
- Simple; easy to scale
- Ideal for caching, sessions
- Support for data structures (lists, sets, hashes)

**Limitations:**
- No querying; must know key
- No relational queries
- Not suitable for complex data

**Use Cases:**
- Session storage
- Caching layer (cache-aside pattern)
- Leaderboards, counters
- Rate limiting, distributed locks

### Column-Family Stores (Cassandra, HBase)

**Model:** Data organized by columns (not rows); distributed across nodes

```
Row Key: user:101
Column Family: profile
  name: Alice
  email: alice@example.com
  created: 2021-01-15

Column Family: activity
  last_login: 2023-10-29
  posts: 150
```

**Strengths:**
- Massively scalable (petabytes)
- High availability; no single point of failure
- Efficient for analytics (query specific columns)
- Write-optimized

**Limitations:**
- Complex query model
- Not suitable for random access queries
- Transactions limited

**Use Cases:**
- Time-series data (metrics, logs)
- Analytics on large datasets
- Event logging
- Real-time analytics

### Graph Databases (Neo4j)

**Model:** Nodes, edges, properties; optimized for relationships

```
(Alice)-[:FRIEND]-(Bob)
(Alice)-[:FOLLOWS]->(Charlie)
(Bob)-[:MANAGER]->(Alice)
```

**Strengths:**
- Efficient traversal (finding shortest path, connections)
- Natural representation of relationships
- Fast recursive queries

**Limitations:**
- Not suitable for non-relational data
- Scaling challenges (graph problems inherently global)

**Use Cases:**
- Social networks (friends, recommendations)
- Knowledge graphs (taxonomy, ontologies)
- Fraud detection (tracing money flows)
- Identity and access management

---

## 5.3 Document Stores

### MongoDB Concepts

**Document:** Single JSON-like object (MongoDB uses BSON)

**Collection:** Set of documents (similar to table but schema-flexible)

**Database:** Set of collections

### Basic Operations

```javascript
// Insert
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com"
})

// Find
db.users.find({name: "Alice"})
db.users.find({age: {$gt: 18}})

// Update
db.users.updateOne(
  {name: "Alice"},
  {$set: {email: "new@example.com"}}
)

// Delete
db.users.deleteOne({name: "Alice"})
```

### Schema Validation

```javascript
// Define schema validation
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: {bsonType: "string"},
        email: {bsonType: "string"},
        age: {bsonType: "int"}
      }
    }
  }
})
```

### Indexing

```javascript
// Create index on name
db.users.createIndex({name: 1})

// Compound index
db.users.createIndex({name: 1, email: -1})

// Text index for search
db.posts.createIndex({title: "text", content: "text"})
db.posts.find({$text: {$search: "mongodb"}})
```

### Transactions (MongoDB 4.0+)

```javascript
session = db.getMongo().startSession()
session.startTransaction()

try {
  db.users.updateOne({_id: 1}, {$inc: {balance: -100}})
  db.users.updateOne({_id: 2}, {$inc: {balance: +100}})
  session.commitTransaction()
} catch(e) {
  session.abortTransaction()
}
```

---

## 5.4 Key-Value Stores

### Redis Data Structures

**Strings:**
```
SET key value
GET key
```

**Lists:**
```
LPUSH mylist "a"
RPUSH mylist "b", "c"
LRANGE mylist 0 -1
→ ["a", "b", "c"]
```

**Sets:**
```
SADD myset "a", "b", "c"
SMEMBERS myset
SINTER set1 set2
```

**Hashes:**
```
HSET user:101 name "Alice" email "alice@example.com"
HGETALL user:101
```

**Sorted Sets:**
```
ZADD leaderboard 100 "Alice" 200 "Bob" 150 "Charlie"
ZRANGE leaderboard 0 -1
ZREVRANGE leaderboard 0 2  // Top 3
```

### Redis Patterns

**Cache-Aside Pattern:**
```
1. Client requests data
2. Check Redis cache
3. If hit: return data
4. If miss: fetch from database, store in Redis, return
5. Set TTL on cache entries
```

**Session Storage:**
```
SET session:abc123 {user_id: 1, login_time: 2023-10-29}
EXPIRE session:abc123 3600  // 1 hour TTL
```

**Atomic Counters:**
```
INCR user:101:post_count
DECR inventory:item:5
```

---

## 5.5 Column-Family Stores

### Cassandra Concepts

**Keyspace:** Equivalent to database; contains column families

**Table (Column Family):** Rows with flexible columns

**Row Key:** Identifies row uniquely

**Partition Key:** First part of row key; determines which node stores data

### Cassandra Query Language (CQL)

```sql
-- Create keyspace
CREATE KEYSPACE user_ks WITH replication = 
  {'class': 'SimpleStrategy', 'replication_factor': 3};

-- Create table
CREATE TABLE user_ks.users (
  user_id INT PRIMARY KEY,
  name TEXT,
  email TEXT,
  created TIMESTAMP
);

-- Insert
INSERT INTO user_ks.users (user_id, name, email, created)
VALUES (1, 'Alice', 'alice@example.com', now());

-- Query
SELECT * FROM user_ks.users WHERE user_id = 1;
```

### Cassandra Architecture

**Ring Topology:**
- Nodes arranged in ring
- Each node owns token range
- Partition key determines token
- Replication factor: copies on multiple nodes

**Consistency Levels:**
- ONE: Read/write confirmed by 1 node (fast, risky)
- QUORUM: Read/write confirmed by majority
- ALL: All replicas (slow, safest)

---

## 5.6 Graph Databases

### Neo4j Cypher Query Language

```cypher
// Create nodes and relationships
CREATE (alice:Person {name: "Alice", age: 30})
CREATE (bob:Person {name: "Bob", age: 35})
CREATE (alice)-[:FRIEND]->(bob)

// Find friends of Alice
MATCH (alice:Person {name: "Alice"})-[:FRIEND]-(friend)
RETURN friend.name

// Find friends of friends (2nd degree connections)
MATCH (alice:Person {name: "Alice"})-[:FRIEND]*2-(friend)
RETURN friend.name

// Shortest path
MATCH path = shortestPath(
  (alice:Person {name: "Alice"})-[:FRIEND*]-(bob:Person {name: "Bob"})
)
RETURN path
```

### Graph Algorithms

**Shortest Path:** Find minimum distance between nodes
**Centrality:** Find most important nodes
**Community Detection:** Find clusters of connected nodes
**Recommendation:** Find similar/complementary products

---

## 5.7 NoSQL Trade-offs

### CAP Theorem Applied

**Consistency:** All replicas have same data
**Availability:** System responsive (no timeouts)
**Partition Tolerance:** Survive network partitions

Choose 2 of 3:

**Cassandra:** Chooses Availability + Partition (eventual consistency)
**HBase:** Chooses Consistency + Partition (sacrifices availability)
**MongoDB:** Tunable (can adjust consistency/availability)

### Comparison

| Aspect | SQL | Document | Key-Value | Column-Family | Graph |
|--------|-----|----------|-----------|---|---|
| **Consistency** | Strong | Eventual | Eventual | Eventual | Strong |
| **Schema** | Fixed | Flexible | N/A | Flexible | Flexible |
| **Querying** | SQL | Query language | Key lookup | CQL/HBase | Cypher |
| **Scaling** | Hard | Easy | Very easy | Easy | Hard |
| **Transactions** | ACID | Limited | Limited | Limited | ACID |
| **Use Case** | Structured, relational | Flexible docs | Fast caching | Massive scale, time-series | Relationships |

---

## 5.8 Choosing the Right Database

### Decision Framework

**Do you need complex transactions across multiple records?**
- YES → SQL or NewSQL
- NO → Continue below

**Is your schema stable and well-defined?**
- YES → SQL
- NO → Continue below

**Is your data primarily highly interconnected?**
- YES → Graph database
- NO → Continue below

**Do you need highest speed for key-based access?**
- YES → Key-value store
- NO → Continue below

**Do you have time-series or analytical workloads at massive scale?**
- YES → Column-family store
- NO → Continue below

**Is your data document-like (JSON)?**
- YES → Document store
- NO → SQL

### Real-World Polyglot Stack

```
User Data → PostgreSQL (relational, transactions)
User Sessions → Redis (fast cache, temporary)
Analytics Events → Cassandra (time-series, massive scale)
Social Graph → Neo4j (relationships)
Product Catalog → MongoDB (flexible documents)
Full-Text Search → Elasticsearch (search)
```

---

## Summary

NoSQL databases emerged to address limitations of traditional SQL databases at scale. Each type (document, key-value, column-family, graph) optimizes for different access patterns and trade-offs. Understanding these options enables choosing the right tool for each part of an application, leading to better performance, scalability, and maintainability.

