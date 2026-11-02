# Session 6: Advanced Indexing and Query Optimization

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [6.1 Indexing Fundamentals](#61-indexing-fundamentals)
- [6.2 Index Types](#62-index-types)
- [6.3 Query Optimization](#63-query-optimization)
- [6.4 Execution Plans](#64-execution-plans)
- [6.5 Index Selection](#65-index-selection)
- [6.6 Performance Tuning](#66-performance-tuning)

---

## 6.1 Indexing Fundamentals

### Why Indexes?

Without index: Full table scan
```sql
SELECT * FROM employees WHERE salary > 100000;
-- Scans all 1M rows: O(n)
```

With index on salary: B-tree lookup
```sql
-- Uses index: O(log n)
```

### Trade-offs

**Advantages:**
- Fast lookups, range scans
- Optimized queries
- Sorted retrieval

**Disadvantages:**
- Storage overhead
- Insert/update/delete slower (index maintenance)
- Not always used by optimizer

---

## 6.2 Index Types

### B-Tree Index (Default)

- Balanced tree structure
- Maintains sorted order
- Efficient for range queries
- Operations: O(log n)

**When to use:**
- Equality: WHERE id = 101
- Range: WHERE salary BETWEEN 50K AND 100K
- Sorting: ORDER BY created_date

### Hash Index

- Hash function maps key to bucket
- O(1) for equality
- Not suitable for ranges

**When to use:**
- Exact match: WHERE id = 101
- Join conditions: WHERE user_id = 5

### Bitmap Index

- Compact for low-cardinality columns
- Columns with few unique values (gender, status)
- Efficient for analytical queries

### Composite Index

```sql
CREATE INDEX idx_dept_salary ON employees(dept_id, salary);
```

Can use for:
- WHERE dept_id = 5
- WHERE dept_id = 5 AND salary > 50000
- Cannot use for: WHERE salary > 50000 (must have leading column)

### Covering Index

Index includes all columns needed for query (no table lookup)

```sql
CREATE INDEX idx_covered ON employees(dept_id) INCLUDE (name, salary);

SELECT name, salary FROM employees WHERE dept_id = 5;
-- No table lookup needed; data in index
```

---

## 6.3 Query Optimization

### Query Optimization Process

```
Parse SQL → Validate → Optimize → Compile → Execute
           ↓
        Check syntax
           ↓
        Check tables/columns exist
           ↓
        Generate multiple execution plans
           ↓
        Estimate cost (I/O, CPU)
           ↓
        Choose cheapest plan
```

### Optimization Techniques

**Index Selection:**
- Use index on WHERE clause columns
- Use covering indexes to avoid table access
- Use indexes on JOIN columns

**Join Order:**
- Execute most selective conditions first
- Smaller intermediate results

**Predicate Pushdown:**
- Apply filters as early as possible
- Reduce data flowing through operators

```sql
-- Bad: JOIN first, filter later
SELECT * FROM orders JOIN customers
WHERE customers.country = 'USA';
-- Must join all rows

-- Good: Filter customers first
SELECT * FROM (SELECT * FROM customers WHERE country = 'USA') c
JOIN orders o ON c.id = o.customer_id;
-- Fewer rows joined
```

---

## 6.4 Execution Plans

### Reading Execution Plans

PostgreSQL EXPLAIN:
```sql
EXPLAIN SELECT * FROM employees WHERE salary > 100000;

-- Output:
Seq Scan on employees
  Filter: (salary > 100000)
  Rows: 500
  Cost: 0.00..50.00
```

With Index:
```sql
EXPLAIN SELECT * FROM employees WHERE salary > 100000;

-- Output:
Index Range Scan on idx_salary
  Filter: (salary > 100000)
  Rows: 500
  Cost: 0.42..25.50
```

### Plan Analysis

**Sequential Scan:** Full table scan (expensive for selective queries)
**Index Scan:** Use index (usually better)
**Index Only Scan:** Use index without table access (covering index)
**Hash Join:** Use hash table to join (good for medium-large)
**Sort-Merge Join:** Sort both inputs, merge (good for large, already sorted)
**Nested Loop:** For each row in outer, scan inner (slow for large)

---

## 6.5 Index Selection

### When to Index

Create index if:
1. **Frequent in WHERE clause:** Selective queries benefit
2. **Used in JOIN:** Foreign key columns
3. **Used in GROUP BY:** Aggregation optimization
4. **Selective:** Filters many rows (>10% selectivity)
5. **Write-infrequent:** Minimize index maintenance cost

### When NOT to Index

Avoid index if:
1. **Low cardinality:** Few unique values (bool, gender)
2. **Write-heavy:** INSERT/UPDATE/DELETE cost outweighs benefit
3. **Rarely queried:** Index maintenance cost not justified
4. **Already covered:** Another index covers query

### Index Maintenance Overhead

```sql
-- Each write must update index
INSERT: O(log n) for B-tree update
UPDATE: O(log n) to remove old, add new
DELETE: O(log n) to remove index entry
```

For write-heavy tables, fewer indexes better.

---

## 6.6 Performance Tuning

### Query Tuning Steps

1. **Identify slow queries:** Enable query log, use monitoring
2. **Analyze execution plan:** Check for sequential scans, high costs
3. **Check for indexes:** Are relevant indexes used?
4. **Rewrite query if needed:** Better predicate order
5. **Add indexes:** On frequently used columns
6. **Monitor results:** Compare before/after

### Common Bottlenecks

**Full Table Scan:**
- Problem: Sequential scan on large table
- Solution: Add index on WHERE column

**Bad Join Order:**
- Problem: Join largest table first
- Solution: Let optimizer reorder; use hints if needed

**Inefficient JOIN:**
- Problem: Nested loop join on large tables
- Solution: Use hash join (adjust work memory)

**Subquery not optimized:**
- Problem: Subquery materialized before joining
- Solution: Use EXPLAIN; rewrite if needed

---

## Summary

Indexing and query optimization are critical for database performance. Effective indexing requires understanding access patterns, query workloads, and trade-offs between read and write performance. Query optimization involves analyzing execution plans and making targeted improvements.

