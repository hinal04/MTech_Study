# Session 2: SQL Fundamentals and Transaction Concepts

> BITS Pilani — Structured Data Management & Distributed Databases
>
> **References:** T1 Ch.6 | Ch.7 (Sec 7.1)
>
> **Textbook:** Elmasri & Navathe, *Fundamentals of Database Systems*, 7th Ed., Pearson, 2017.

---

## Table of Contents

- [2.1 SQL Overview](#21-sql-overview)
- [2.2 DDL Statements](#22-ddl-statements)
- [2.3 DML Statements](#23-dml-statements)
- [2.4 Retrieval Queries (SELECT)](#24-retrieval-queries-select)
- [2.5 Joins](#25-joins)
- [2.6 Subqueries](#26-subqueries)
- [2.7 Transactions and ACID Properties](#27-transactions-and-acid-properties)
- [2.8 Concurrency Control](#28-concurrency-control)
- [2.9 Database Recovery](#29-database-recovery)
- [2.10 Schema Evolution and Denormalisation](#210-schema-evolution-and-denormalisation)
- [2.11 Limits of Relational Systems — Motivation for NoSQL](#211-limits-of-relational-systems)
- [2.12 Advanced SQL](#212-advanced-sql)

---

## 2.1 SQL Overview

**SQL (Structured Query Language)** is the standard language for creating, managing, and querying relational databases. It was originally developed at IBM in the early 1970s under the name SEQUEL (Structured English Query Language) and became an ANSI/ISO standard in 1986. Today, every major RDBMS — Oracle, PostgreSQL, MySQL, SQL Server, SQLite — supports SQL, though each vendor adds proprietary extensions.

### Why SQL is Declarative

SQL's most important characteristic is that it is **declarative**, not procedural. In a procedural language like C or Python, you tell the computer **how** to get the answer step by step: "open file, read line, compare with target, if match add to result." In SQL, you tell the database **what** you want: "give me all employees with salary > 70000." The DBMS's **query optimizer** determines the best execution strategy — which indexes to use, which join algorithm, what order to process tables.

This declarative nature has a profound practical consequence: the same SQL query can run on a tiny SQLite database on a phone or on a massive distributed PostgreSQL cluster with billions of rows. The optimizer adapts the execution plan to the data size, available indexes, and hardware. You never need to rewrite queries when data grows or storage changes.

### SQL Sub-Languages

SQL combines four sub-languages into one:

| Sub-Language | Purpose | Key Statements |
|---|---|---|
| **DDL** (Data Definition) | Define and modify schema objects. | `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE` |
| **DML** (Data Manipulation) | Query and modify data. | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL** (Data Control) | Manage access permissions. | `GRANT`, `REVOKE` |
| **TCL** (Transaction Control) | Manage transaction boundaries. | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

---

## 2.2 DDL Statements

DDL statements define the **structure** of the database — tables, columns, data types, and constraints. They modify the schema, not the data.

### 2.2.1 CREATE TABLE

The `CREATE TABLE` statement defines a new table with its columns, data types, and constraints. This is where you declare the "rules" for your data — what shape it must have.

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
        ON DELETE SET NULL
        ON UPDATE CASCADE
);
```

### Common Data Types

| Category | Types | When to use |
|---|---|---|
| **Numeric** | `INT`, `SMALLINT`, `BIGINT`, `DECIMAL(p,s)`, `FLOAT`, `REAL` | Exact numbers (DECIMAL for money), approximate numbers (FLOAT for science). |
| **Character** | `CHAR(n)`, `VARCHAR(n)`, `TEXT` | Fixed-length codes (CHAR), variable-length strings (VARCHAR), large text (TEXT). |
| **Date/Time** | `DATE`, `TIME`, `TIMESTAMP`, `INTERVAL` | Dates without time, times without date, both, and durations. |
| **Boolean** | `BOOLEAN` | True/false flags. |
| **Binary** | `BLOB`, `BYTEA` | Images, documents, binary data. |

### Constraints

Constraints are rules that the DBMS enforces automatically on every INSERT, UPDATE, and DELETE. They are the primary mechanism for maintaining data integrity.

| Constraint | What it enforces | Example |
|---|---|---|
| `PRIMARY KEY` | Uniquely identifies each row. Implies NOT NULL + UNIQUE. | `Emp_ID INT PRIMARY KEY` |
| `FOREIGN KEY ... REFERENCES` | Referential integrity — FK must match an existing PK or be NULL. | `FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID)` |
| `NOT NULL` | Column must always have a value — NULL is not allowed. | `First_Name VARCHAR(50) NOT NULL` |
| `UNIQUE` | No two rows can have the same value in this column (NULLs may be allowed). | `Email VARCHAR(100) UNIQUE` |
| `CHECK` | Value must satisfy a Boolean condition. | `CHECK (Salary > 0)` |
| `DEFAULT` | If no value is provided, use this default. | `Hire_Date DATE DEFAULT CURRENT_DATE` |

### Referential Triggered Actions

When a referenced row (parent) is deleted or updated, what happens to the rows that reference it (children)?

| Action | What happens to children |
|---|---|
| `CASCADE` | Children are automatically deleted/updated too. Useful for "if parent goes, children go." |
| `SET NULL` | Children's FK is set to NULL. Useful for "child can exist without parent." |
| `SET DEFAULT` | Children's FK is set to its default value. |
| `RESTRICT` / `NO ACTION` | The delete/update is rejected if children exist. Safest option — prevents accidental data loss. |

### 2.2.2 ALTER TABLE and DROP TABLE

```sql
-- Add a column
ALTER TABLE EMPLOYEE ADD COLUMN Phone VARCHAR(20);

-- Drop a column
ALTER TABLE EMPLOYEE DROP COLUMN Phone;

-- Add a constraint
ALTER TABLE EMPLOYEE ADD CONSTRAINT chk_sal CHECK (Salary >= 15000);

-- Drop an entire table
DROP TABLE EMPLOYEE;           -- Fails if other tables reference it
DROP TABLE EMPLOYEE CASCADE;   -- Drops dependent objects (FKs, views) too
```

---

## 2.3 DML Statements

DML statements modify the **data** inside tables, not the table structure.

### INSERT

```sql
-- Single row
INSERT INTO EMPLOYEE (Emp_ID, First_Name, Last_Name, Salary, Dept_ID)
VALUES (101, 'Alice', 'Johnson', 75000.00, 5);

-- Multiple rows
INSERT INTO EMPLOYEE (Emp_ID, First_Name, Last_Name, Salary, Dept_ID)
VALUES (102, 'Bob', 'Smith', 68000.00, 3),
       (103, 'Carol', 'Williams', 82000.00, 5);

-- Insert from a query (copy data between tables)
INSERT INTO HIGH_EARNERS (Emp_ID, First_Name, Salary)
SELECT Emp_ID, First_Name, Salary FROM EMPLOYEE WHERE Salary > 100000;
```

### UPDATE

```sql
-- Update specific rows
UPDATE EMPLOYEE SET Salary = Salary * 1.10 WHERE Dept_ID = 5;

-- Update using a subquery
UPDATE EMPLOYEE
SET Dept_ID = (SELECT Dept_ID FROM DEPARTMENT WHERE Dept_Name = 'Engineering')
WHERE Emp_ID = 101;
```

### DELETE

```sql
DELETE FROM EMPLOYEE WHERE Emp_ID = 103;    -- Delete specific row
DELETE FROM EMPLOYEE;                        -- Delete ALL rows (structure remains)
```

**Important distinction:** `DELETE` removes rows but the table still exists. `DROP TABLE` removes the entire table including its definition.

---

## 2.4 Retrieval Queries (SELECT)

The `SELECT` statement is the most powerful and most-used SQL statement. It retrieves data from one or more tables.

### Syntax and Logical Execution Order

The order you write clauses is NOT the order the DBMS executes them:

```sql
SELECT   column_list          -- 5th: Choose which columns to return
FROM     table(s)             -- 1st: Identify source tables, perform joins
WHERE    row_condition        -- 2nd: Filter individual rows
GROUP BY column_list          -- 3rd: Group remaining rows
HAVING   group_condition      -- 4th: Filter groups
ORDER BY column_list          -- 6th: Sort the result
LIMIT n  OFFSET m;            -- 7th: Restrict rows returned
```

**Why execution order matters:** You **cannot** use a column alias defined in SELECT inside a WHERE clause — because WHERE executes before SELECT. This is a common source of confusion.

### WHERE Clause Operators

```sql
-- Comparison: =, <>, <, >, <=, >=
SELECT * FROM EMPLOYEE WHERE Salary > 70000;

-- Logical: AND, OR, NOT
SELECT * FROM EMPLOYEE WHERE Dept_ID = 5 AND Salary > 60000;

-- Range (inclusive)
SELECT * FROM EMPLOYEE WHERE Salary BETWEEN 50000 AND 80000;

-- Set membership
SELECT * FROM EMPLOYEE WHERE Dept_ID IN (3, 5, 7);

-- Pattern matching (% = any chars, _ = one char)
SELECT * FROM EMPLOYEE WHERE Last_Name LIKE 'J%';

-- NULL check (cannot use = with NULL!)
SELECT * FROM EMPLOYEE WHERE Dept_ID IS NULL;
```

### Aggregate Functions

Aggregate functions compute a single value from a set of rows:

| Function | Returns |
|---|---|
| `COUNT(*)` | Number of rows (including NULLs). |
| `COUNT(col)` | Number of non-NULL values in col. |
| `SUM(col)` | Sum of numeric values. |
| `AVG(col)` | Average of numeric values. |
| `MIN(col)` / `MAX(col)` | Minimum / Maximum value. |

```sql
SELECT Dept_ID, COUNT(*) AS Emp_Count, AVG(Salary) AS Avg_Sal
FROM EMPLOYEE
GROUP BY Dept_ID
HAVING COUNT(*) > 5
ORDER BY Avg_Sal DESC;
```

### DISTINCT and Aliases

```sql
SELECT DISTINCT Dept_ID FROM EMPLOYEE;

SELECT E.First_Name AS "Name", D.Dept_Name AS "Department"
FROM EMPLOYEE E
JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;
```

---

## 2.5 Joins

In a normalised database, related data lives in separate tables. **Joins** combine rows from multiple tables based on a matching condition (typically FK = PK).

### Why Joins Are Essential

Without joins, a normalised database would be useless — you'd have student IDs in one table and department names in another, with no way to connect them in a single query. Joins are what make normalisation practical: store data without redundancy, then recombine it on the fly when needed.

### Join Types

| Join Type | What it returns | When to use |
|---|---|---|
| **INNER JOIN** | Only rows with a match in both tables. | "Students who have a department assigned." |
| **LEFT JOIN** | All rows from left + matching right (NULLs where no match). | "All students, with department name if assigned." |
| **RIGHT JOIN** | All rows from right + matching left. | Mirror of LEFT JOIN; rarely used in practice. |
| **FULL OUTER JOIN** | All rows from both; NULLs where no match on either side. | "All students and all departments, showing links where they exist." |
| **CROSS JOIN** | Cartesian product — every combination. | Generating all possible pairs (e.g. products × colours). |
| **SELF JOIN** | Table joined with itself using aliases. | "Employees and their managers" (Manager_ID references Emp_ID). |
| **NATURAL JOIN** | Auto-join on all same-named columns. | Convenient but risky — silent breakage if columns are added. |

### Examples

```sql
-- Inner Join
SELECT E.First_Name, D.Dept_Name
FROM EMPLOYEE E INNER JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;

-- Left Join (all employees, even those without a department)
SELECT E.First_Name, D.Dept_Name
FROM EMPLOYEE E LEFT JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;

-- Self Join (employees and their managers)
SELECT E.First_Name AS Employee, M.First_Name AS Manager
FROM EMPLOYEE E LEFT JOIN EMPLOYEE M ON E.Manager_ID = M.Emp_ID;
```

---

## 2.6 Subqueries

A **subquery** is a complete SELECT statement nested inside another SQL statement. It allows multi-step reasoning within a single query.

### Types of Subqueries

| Type | Returns | Example |
|---|---|---|
| **Scalar** | Single value | `WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE)` |
| **Row** | Single row | `WHERE (Dept_ID, Location) = (SELECT ...)` |
| **Table** | Multiple rows/columns | `FROM (SELECT ...) AS derived_table` |
| **Correlated** | Re-evaluated for each outer row | `WHERE EXISTS (SELECT 1 FROM ... WHERE inner.col = outer.col)` |

**Key distinction:** A regular subquery executes once and its result is reused. A **correlated subquery** references the outer query and re-executes for every row in the outer query — potentially much slower.

```sql
-- Scalar: employees above average salary
SELECT First_Name, Salary FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE);

-- IN: employees in London departments
SELECT First_Name FROM EMPLOYEE
WHERE Dept_ID IN (SELECT Dept_ID FROM DEPARTMENT WHERE Location = 'London');

-- EXISTS (correlated): departments that have employees
SELECT D.Dept_Name FROM DEPARTMENT D
WHERE EXISTS (SELECT 1 FROM EMPLOYEE E WHERE E.Dept_ID = D.Dept_ID);

-- Derived table: department averages above 70000
SELECT Dept_ID, Avg_Sal
FROM (SELECT Dept_ID, AVG(Salary) AS Avg_Sal FROM EMPLOYEE GROUP BY Dept_ID) AS DeptAvg
WHERE Avg_Sal > 70000;
```

---

## 2.7 Transactions and ACID Properties

### What is a Transaction?

A **transaction** is a logical unit of work consisting of one or more database operations that must succeed or fail as an **indivisible whole**. The classic example is a bank transfer:

```sql
BEGIN TRANSACTION;
  UPDATE ACCOUNT SET Balance = Balance - 500 WHERE Acc_ID = 'A';   -- Debit
  UPDATE ACCOUNT SET Balance = Balance + 500 WHERE Acc_ID = 'B';   -- Credit
COMMIT;
```

If the system crashes between the two UPDATEs, money has left account A but hasn't reached account B. Without transaction support, money would simply vanish. ACID properties prevent this.

### ACID Properties

| Property | Meaning | How the DBMS enforces it |
|---|---|---|
| **Atomicity** | All-or-nothing — either every operation completes, or none do. | Transaction manager + undo log. If any operation fails, all changes are rolled back using the undo log. |
| **Consistency** | A transaction takes the database from one valid state to another. All integrity constraints satisfied before and after. | Constraint checking at commit time. If any constraint is violated, the transaction is aborted. |
| **Isolation** | Concurrent transactions execute as if they were serial. Intermediate states are invisible to others. | Concurrency control: locks, MVCC (Multi-Version Concurrency Control), timestamp ordering. |
| **Durability** | Once committed, changes survive any failure — power loss, crashes, disk failure. | Write-Ahead Log (WAL) flushed to stable storage before commit is acknowledged. Even if the server crashes, the log can replay committed changes. |

### Transaction States

```
Active → (all ops succeed) → Partially Committed → (flush OK) → Committed
  |                                    |
(op fails)                        (flush fails)
  ↓                                    ↓
Failed → Aborted (rollback done) → [restart or kill]
```

| State | Description |
|---|---|
| **Active** | Transaction is executing operations. |
| **Partially committed** | Final statement executed; awaiting confirmation that changes are durable on disk. |
| **Committed** | All changes are permanent. Cannot be undone (except by a new compensating transaction). |
| **Failed** | An error occurred (constraint violation, deadlock, hardware failure). Cannot proceed. |
| **Aborted** | Rollback complete. Database restored to the state before the transaction started. Transaction may be restarted or abandoned. |

### SQL Transaction Control

```sql
BEGIN TRANSACTION;       -- Start a transaction
-- ... DML operations ...
SAVEPOINT sp1;           -- Named checkpoint within the transaction
-- ... more operations ...
ROLLBACK TO sp1;         -- Undo back to the savepoint (not the whole transaction)
COMMIT;                  -- Make ALL changes since BEGIN permanent
-- or
ROLLBACK;                -- Undo EVERYTHING since BEGIN
```

---

## 2.8 Concurrency Control

### Problems of Concurrent Execution

When multiple transactions run simultaneously without controls, four anomalies can occur:

| Problem | What happens | Example |
|---|---|---|
| **Lost update** | Two transactions read the same value, both update — one overwrites the other. | T1 reads balance=1000, T2 reads 1000. T1 writes 900, T2 writes 800. T1's deduction is lost. |
| **Dirty read** | Reading data from an uncommitted transaction that might roll back. | T1 updates salary to 90000 (uncommitted). T2 reads 90000. T1 rolls back. T2 used a phantom value. |
| **Non-repeatable read** | Reading the same row twice yields different values because another transaction modified it. | T1 reads salary=70000. T2 updates to 80000 and commits. T1 reads again: salary=80000. |
| **Phantom read** | Re-executing a query finds new/removed rows because another transaction inserted/deleted. | T1 counts 10 employees in Dept 5. T2 inserts one. T1 counts again: 11. |

### Isolation Levels

The SQL standard defines four isolation levels, each preventing more anomalies at the cost of reduced concurrency:

| Level | Dirty Read | Non-Repeatable | Phantom | Concurrency |
|---|---|---|---|---|
| **READ UNCOMMITTED** | Possible | Possible | Possible | Highest |
| **READ COMMITTED** | Prevented | Possible | Possible | High |
| **REPEATABLE READ** | Prevented | Prevented | Possible | Medium |
| **SERIALIZABLE** | Prevented | Prevented | Prevented | Lowest |

PostgreSQL defaults to READ COMMITTED. MySQL InnoDB defaults to REPEATABLE READ.

### Locking and Two-Phase Locking (2PL)

- **Shared lock (S):** Multiple readers can hold simultaneously. No writing allowed.
- **Exclusive lock (X):** Only one transaction can hold it. No other reads or writes.

**Two-Phase Locking (2PL):** Guarantees serializable schedules.
1. **Growing phase:** Acquire locks, never release any.
2. **Shrinking phase:** Release locks, never acquire new ones.

### MVCC (Multi-Version Concurrency Control)

Instead of blocking readers, MVCC keeps **multiple versions** of each row. Readers see the version current at their transaction's start time. **Readers never block writers, and writers never block readers.** Used by PostgreSQL, MySQL InnoDB, Oracle.

### Deadlocks

Two transactions each wait for a lock held by the other → neither can proceed. Detected via a **wait-for graph** (cycle = deadlock). Resolved by aborting one transaction (the "victim").

---

## 2.9 Database Recovery

Recovery ensures **atomicity** (uncommitted changes are undone) and **durability** (committed changes survive crashes).

### Write-Ahead Logging (WAL)

Before any change is written to the database on disk, the corresponding **log record** must first be written to the log file on stable storage. Log records contain: transaction ID, data item, old value (for UNDO), new value (for REDO), COMMIT/ABORT markers.

### UNDO and REDO

| Action | When applied | What it does |
|---|---|---|
| **UNDO** | Transaction was active (uncommitted) at crash time. | Roll back its changes using old values from the log. |
| **REDO** | Transaction committed but changes may not have been flushed to disk. | Re-apply changes using new values from the log. |

### Checkpoints

A checkpoint periodically flushes all dirty pages from memory to disk and writes a checkpoint record to the log. This limits how far back recovery must scan — only from the last checkpoint, not from the beginning of time.

---

## 2.10 Schema Evolution and Denormalisation

### Schema Evolution

Real-world databases change over time. Schema evolution means modifying the schema after it's in production.

**Common changes:** Adding/removing columns, changing data types, adding/dropping constraints, creating indexes.

**Challenges:** Existing data may need migration, applications may break, views and procedures may become invalid, large tables may lock during ALTER.

**Strategies:**
- **Expand-and-contract:** Add new column → migrate data → update apps → drop old column.
- **Schema versioning:** Tag each schema version; apps specify their target version.
- **Backward/forward compatibility:** Design changes so old and new app versions co-exist during rollout.

### Denormalisation

**Normalisation** removes redundancy (1NF → 2NF → 3NF → BCNF). **Denormalisation** intentionally reintroduces it for read performance.

| When to denormalise | Technique | Trade-off |
|---|---|---|
| Frequent expensive joins | Store redundant columns to avoid the join. | Faster reads; slower writes; inconsistency risk. |
| Repeated aggregations | Materialised views or summary tables. | Stale data if not refreshed. |
| Read-heavy workloads | Duplicate data for specific read patterns. | Storage overhead; update anomalies. |

**Rule:** Normalise first for correctness. Denormalise selectively for **measured** performance problems.

---

## 2.11 Limits of Relational Systems

The relational model excels for structured data with stable schemas, but faces challenges at web scale:

| Limitation | Explanation |
|---|---|
| **Rigid schema** | ALTER TABLE on large tables can lock for hours. Web apps evolve features every sprint. |
| **Impedance mismatch** | OO application objects don't map cleanly to flat tables; ORM complexity grows. |
| **Horizontal scaling is hard** | Sharding breaks joins, FK constraints, and distributed transactions. |
| **Unstructured data** | JSON, graphs, time-series, key-value pairs don't fit rows/columns naturally. |
| **High write throughput** | WAL + ACID can bottleneck at millions of writes/sec. |
| **Global distribution** | Strong consistency across continents is expensive (speed-of-light latency). |

**NoSQL alternatives:** Key-Value (Redis), Document (MongoDB), Column-Family (Cassandra), Graph (Neo4j). They trade ACID for scalability and flexibility (BASE model — Session 4).

---

## 2.12 Advanced SQL

### Common Table Expressions (CTEs)

```sql
WITH DeptStats AS (
    SELECT Dept_ID, AVG(Salary) AS Avg_Sal, COUNT(*) AS Cnt
    FROM EMPLOYEE GROUP BY Dept_ID
)
SELECT D.Dept_Name, DS.Avg_Sal
FROM DeptStats DS JOIN DEPARTMENT D ON DS.Dept_ID = D.Dept_ID
WHERE DS.Cnt > 10;
```

### Window Functions

Perform calculations across a set of rows related to the current row **without collapsing** the result.

```sql
SELECT Emp_ID, Dept_ID, Salary,
       RANK() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS Rank,
       AVG(Salary) OVER (PARTITION BY Dept_ID) AS Dept_Avg
FROM EMPLOYEE;
```

| Function | Description |
|---|---|
| `ROW_NUMBER()` | Sequential number per partition. |
| `RANK()` | Rank with gaps for ties. |
| `DENSE_RANK()` | Rank without gaps. |
| `LAG(col, n)` / `LEAD(col, n)` | Value n rows before/after current. |
| `SUM/AVG/COUNT OVER()` | Running or partitioned aggregates. |

### CASE Expressions

```sql
SELECT Emp_ID, Salary,
    CASE
        WHEN Salary >= 100000 THEN 'Senior'
        WHEN Salary >= 60000  THEN 'Mid-Level'
        ELSE 'Junior'
    END AS Band
FROM EMPLOYEE;
```

### GRANT and REVOKE

```sql
GRANT SELECT, INSERT ON EMPLOYEE TO user_report;
GRANT ALL PRIVILEGES ON DEPARTMENT TO admin_user WITH GRANT OPTION;
REVOKE INSERT ON EMPLOYEE FROM user_report;
```

---

*End of Session 2*
