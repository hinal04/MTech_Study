# Session 2: SQL Fundamentals and Transactions — Questions & Answers

> 15 questions covering: DDL, DML, SELECT, Joins, Subqueries, ACID, Concurrency, Recovery, Advanced SQL.

---

### Q1. What is SQL? Why is it called a declarative language?

**Answer:**

**SQL (Structured Query Language)** is the standard language for relational databases, combining DDL, DML, DCL, and TCL.

It is **declarative** because you specify **what** data you want, not **how** to get it. When you write `SELECT * FROM EMPLOYEE WHERE Salary > 70000`, you don't tell the DBMS which index to use, how to scan the table, or in what order to process rows. The **query optimizer** decides the most efficient execution plan based on available indexes, table statistics, and data distribution.

In contrast, a **procedural** language (C, Python) requires step-by-step instructions: open file, read line, compare, accumulate results.

---

### Q2. Write SQL to create an EMPLOYEE table with constraints. Explain each constraint.

**Answer:**

```sql
CREATE TABLE EMPLOYEE (
    Emp_ID      INT            PRIMARY KEY,
    First_Name  VARCHAR(50)    NOT NULL,
    Last_Name   VARCHAR(50)    NOT NULL,
    Email       VARCHAR(100)   UNIQUE,
    Hire_Date   DATE           DEFAULT CURRENT_DATE,
    Salary      DECIMAL(10,2)  CHECK (Salary > 0),
    Dept_ID     INT,
    FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID)
        ON DELETE SET NULL ON UPDATE CASCADE
);
```

| Constraint | Explanation |
|---|---|
| `PRIMARY KEY` | Uniquely identifies each row. Implies NOT NULL + UNIQUE. |
| `NOT NULL` | Column must always have a value — inserting NULL is rejected. |
| `UNIQUE` | No two rows can have the same Email. NULLs may be allowed (one per row in most DBMSs). |
| `DEFAULT` | If no Hire_Date is provided during INSERT, use the current date. |
| `CHECK` | Salary must be positive — any INSERT/UPDATE violating this is rejected. |
| `FOREIGN KEY` | Dept_ID must match an existing Dept_ID in DEPARTMENT (or be NULL). |
| `ON DELETE SET NULL` | If a department is deleted, employees' Dept_ID becomes NULL (not deleted). |
| `ON UPDATE CASCADE` | If a department's Dept_ID changes, the change propagates to all referencing employees. |

---

### Q3. Explain the logical execution order of a SELECT query. Why can't you use a SELECT alias in WHERE?

**Answer:**

```
1. FROM       — Identify source tables, perform joins.
2. WHERE      — Filter individual rows.
3. GROUP BY   — Group remaining rows by specified columns.
4. HAVING     — Filter groups (not individual rows).
5. SELECT     — Choose columns/expressions to return; compute aliases.
6. ORDER BY   — Sort the result.
7. LIMIT      — Restrict the number of rows returned.
```

**Why aliases fail in WHERE:** Aliases are created in step 5 (SELECT), but WHERE runs in step 2 — before the alias exists. The DBMS doesn't know what the alias refers to yet.

**Workaround:** Use a subquery or CTE, or repeat the expression in WHERE.

---

### Q4. Write SQL for: (a) employees earning above average, (b) department employee counts sorted, (c) departments with employees using EXISTS.

**Answer:**

**(a) Above-average salary (scalar subquery):**
```sql
SELECT First_Name, Salary FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE);
```

**(b) Employee count per department, only departments with >5, sorted descending:**
```sql
SELECT Dept_ID, COUNT(*) AS Emp_Count, AVG(Salary) AS Avg_Sal
FROM EMPLOYEE
GROUP BY Dept_ID
HAVING COUNT(*) > 5
ORDER BY Avg_Sal DESC;
```

**(c) Departments that have at least one employee (correlated EXISTS):**
```sql
SELECT D.Dept_Name FROM DEPARTMENT D
WHERE EXISTS (SELECT 1 FROM EMPLOYEE E WHERE E.Dept_ID = D.Dept_ID);
```

---

### Q5. Explain the different types of JOINs. When would you use each?

**Answer:**

| Join Type | Returns | Use when |
|---|---|---|
| **INNER JOIN** | Only rows matching in both tables. | You only want data that exists in both (e.g. students with assigned departments). |
| **LEFT JOIN** | All left rows + matching right (NULLs if no match). | You want all items from the left regardless of match (e.g. all employees, with dept name if assigned). |
| **RIGHT JOIN** | All right rows + matching left. | Mirror of LEFT JOIN. |
| **FULL OUTER JOIN** | All rows from both; NULLs where no match. | You want everything from both sides. |
| **CROSS JOIN** | Cartesian product (every combination). | Generate all possible pairs. |
| **SELF JOIN** | Table joined to itself. | Self-referencing relationships (employee → manager). |

**Example — Self Join:**
```sql
SELECT E.First_Name AS Employee, M.First_Name AS Manager
FROM EMPLOYEE E LEFT JOIN EMPLOYEE M ON E.Manager_ID = M.Emp_ID;
```

---

### Q6. What is a subquery? Compare regular vs correlated subqueries.

**Answer:**

A **subquery** is a SELECT inside another SQL statement.

| Aspect | Regular subquery | Correlated subquery |
|---|---|---|
| **Execution** | Runs **once**; result reused for all outer rows. | Re-runs **for each outer row**. |
| **References outer query?** | No. | Yes — uses columns from the outer query. |
| **Performance** | Generally faster (one execution). | Can be slower (N executions for N outer rows). |
| **Example** | `WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE)` | `WHERE EXISTS (SELECT 1 FROM EMPLOYEE E WHERE E.Dept_ID = D.Dept_ID)` |

The optimizer can sometimes convert correlated subqueries to joins for better performance.

---

### Q7. What is a transaction? Explain the ACID properties with a bank transfer example.

**Answer:**

A **transaction** is a logical unit of work — one or more operations that must all succeed or all fail.

**Bank transfer: Move 500 from Account A to Account B.**

| ACID Property | How it applies |
|---|---|
| **Atomicity** | Both the debit (A-500) and credit (B+500) happen, or neither does. If the system crashes between them, the debit is rolled back. |
| **Consistency** | Total money in the system stays the same. If a constraint says balance ≥ 0, a transfer that would make A negative is rejected. |
| **Isolation** | While the transfer is in progress, no other transaction sees the intermediate state (A debited, B not yet credited). They see either the before-state or the after-state. |
| **Durability** | Once the bank confirms "transfer successful," the change is permanent — even if the server crashes one millisecond later. The WAL ensures committed changes can be recovered. |

---

### Q8. Explain the four SQL isolation levels and what anomalies each prevents.

**Answer:**

| Level | Dirty Read | Non-Repeatable Read | Phantom Read | Concurrency |
|---|---|---|---|---|
| **READ UNCOMMITTED** | Possible | Possible | Possible | Highest (almost no locking) |
| **READ COMMITTED** | Prevented | Possible | Possible | High (default in PostgreSQL) |
| **REPEATABLE READ** | Prevented | Prevented | Possible | Medium (default in MySQL InnoDB) |
| **SERIALIZABLE** | Prevented | Prevented | Prevented | Lowest (full isolation, most locking) |

Higher isolation = fewer anomalies but lower concurrency (more blocking/waiting between transactions).

---

### Q9. What is MVCC? How does it improve concurrency?

**Answer:**

**Multi-Version Concurrency Control (MVCC)** keeps multiple versions of each row. When a transaction writes a row, it creates a **new version** rather than overwriting the old one. Readers see the version that was current when their transaction started (a "snapshot").

**Key benefit:** **Readers never block writers, and writers never block readers.** This dramatically improves concurrency for read-heavy workloads compared to pure locking, where a writer holding an exclusive lock blocks all readers.

**How it works:**
1. Each row version is tagged with the transaction ID that created it.
2. When a transaction reads, it sees only versions created by committed transactions that started before it.
3. Old versions are garbage-collected after all transactions that could see them have finished.

**Used by:** PostgreSQL, Oracle, MySQL InnoDB, CockroachDB.

---

### Q10. What is a deadlock? How is it detected and resolved?

**Answer:**

A **deadlock** occurs when two or more transactions are each waiting for a lock held by another — forming a circular wait. Neither can proceed.

**Example:**
```
T1 holds lock on Row A, waits for lock on Row B
T2 holds lock on Row B, waits for lock on Row A
→ Neither can proceed — deadlock!
```

**Detection:** The DBMS builds a **wait-for graph** — a directed graph where an edge T1→T2 means "T1 is waiting for a lock held by T2." A **cycle** in this graph indicates a deadlock.

**Resolution:** The DBMS selects a **victim** transaction (typically the one that has done the least work) and aborts it. The victim's locks are released, allowing the other transaction(s) to proceed. The victim can be restarted.

**Prevention strategies:** Wait-die (older waits, younger aborts), wound-wait (older kills younger), or timeout-based (abort after waiting too long).

---

### Q11. Explain Write-Ahead Logging (WAL) and its role in recovery.

**Answer:**

**WAL rule:** Before any change is written to the database on disk, the corresponding **log record** must first be written to the log file on stable storage.

**Log records contain:** Transaction ID, data item identifier, old value (before-image for UNDO), new value (after-image for REDO), COMMIT/ABORT markers.

**Recovery after crash:**
1. **REDO:** For committed transactions whose changes might not have reached disk — re-apply using new values from log.
2. **UNDO:** For active (uncommitted) transactions at crash time — roll back using old values from log.

**Checkpoints** periodically flush dirty pages to disk, limiting how far back recovery must scan.

WAL is the foundation of durability — it's how the DBMS guarantees that committed data survives any crash.

---

### Q12. What is schema evolution? What is the expand-and-contract pattern?

**Answer:**

**Schema evolution** is modifying the database schema after it's in production (adding columns, changing types, adding constraints).

**Expand-and-contract pattern** (safest approach for zero-downtime changes):

1. **Expand:** Add the new column alongside the old one. Both old and new app versions work.
2. **Migrate:** Backfill the new column with data from the old column (or a transformation of it).
3. **Switch:** Update applications to use the new column.
4. **Contract:** Drop the old column once all apps are migrated.

This avoids breaking existing applications during the transition period.

---

### Q13. What is denormalisation? When would you use it?

**Answer:**

**Denormalisation** intentionally reintroduces redundancy into a normalised database to improve **read performance**.

| Scenario | Technique | Trade-off |
|---|---|---|
| Frequent expensive joins | Store redundant columns to avoid the join. | Faster reads; slower writes; risk of data inconsistency. |
| Repeated aggregations | Materialised views or pre-computed summary tables. | Faster queries; data may be stale until refreshed. |
| Read-heavy workloads | Duplicate data optimised for specific read patterns. | More storage; update anomalies possible. |

**Rule:** Normalise first for correctness. Denormalise selectively only for **measured** performance problems — never as a default.

---

### Q14. Explain CTEs and window functions with examples.

**Answer:**

**CTE (Common Table Expression):** A temporary named result set within a single query.

```sql
WITH DeptStats AS (
    SELECT Dept_ID, AVG(Salary) AS Avg_Sal FROM EMPLOYEE GROUP BY Dept_ID
)
SELECT D.Dept_Name, DS.Avg_Sal
FROM DeptStats DS JOIN DEPARTMENT D ON DS.Dept_ID = D.Dept_ID
WHERE DS.Avg_Sal > 70000;
```

**Window function:** Calculates across a set of rows related to the current row **without collapsing** the result (unlike GROUP BY which collapses rows into groups).

```sql
SELECT Emp_ID, Dept_ID, Salary,
    RANK() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS Dept_Rank,
    AVG(Salary) OVER (PARTITION BY Dept_ID) AS Dept_Avg,
    Salary - AVG(Salary) OVER (PARTITION BY Dept_ID) AS Diff_From_Avg
FROM EMPLOYEE;
```

This returns every employee row with their rank within their department and how far they are from the department average — without losing individual rows.

---

### Q15. What are the limitations of relational databases that motivated NoSQL?

**Answer:**

| Limitation | Explanation |
|---|---|
| **Rigid schema** | ALTER TABLE on billions of rows can lock the table for hours. Web apps need to evolve schemas every sprint. |
| **Impedance mismatch** | Object-oriented app data (nested objects, lists) doesn't map cleanly to flat tables. ORMs add complexity. |
| **Horizontal scaling is hard** | Sharding breaks joins, foreign keys, and distributed transactions. |
| **Unstructured data** | JSON documents, social graphs, time-series, and key-value pairs don't fit rows/columns naturally. |
| **High write throughput** | WAL + ACID creates bottlenecks at millions of writes/second (ad-tech, IoT, logging). |
| **Global distribution** | Strong consistency across continents requires synchronous replication with 100-300ms latency per write. |

**NoSQL types:** Key-Value (Redis), Document (MongoDB), Column-Family (Cassandra), Graph (Neo4j). They trade ACID for scalability (BASE model).

---

*End of Session 2 Questions & Answers*
