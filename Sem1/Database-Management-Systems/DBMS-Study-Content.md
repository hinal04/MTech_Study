# Structured Data Management & Distributed Databases — Complete Study Content

> **Textbooks Referenced:**
> - **T1:** Ramez Elmasri & Shamkant B. Navathe, *Fundamentals of Database Systems*, 7th Ed., Pearson, 2017.
> - **T2:** Martin Kleppmann, *Designing Data-Intensive Applications*, 1st Ed., O'Reilly, 2017.
>
> **Reference Books:**
> - **R1:** Paulraj Ponniah, *Data Warehousing Fundamentals for IT Professionals*, 2nd Ed., Wiley, 2010.
> - **R2:** Same as T2.

---
---

# SESSION 1 — Foundations of Structured Data Management

*References: T1 Ch.1 (Sec 1.2, 1.3, 1.6) | Ch.3 (Sec 3.3, 3.4, 3.6) | Ch.5 (Sec 5.1, 5.2) | Ch.6*

---

## 1.1 Introduction to Data Management Systems

A **Data Management System** is software that captures, stores, organises, and retrieves data so that users and applications can work with it efficiently and reliably.

### What is Data? What is Information?

Before discussing data management, it's important to distinguish between **data** and **information**. **Data** refers to raw, unprocessed facts — numbers, text, dates — without context. For example, the number `42000` by itself is just data. **Information** is data that has been processed, organised, and given context so that it becomes meaningful — for example, "Employee John's monthly salary is 42,000 INR." A database stores data; applications and queries transform it into information.

### Why do we need data management?

In any organisation — a university, a bank, a hospital, an e-commerce company — data is generated continuously: student enrolments, financial transactions, patient records, customer orders. Without a systematic way to manage this data, organisations face chaos. Consider a university with 10,000 students. If each department maintains its own Excel files for student records, you'll quickly encounter duplicate entries, contradictory information, and no way to answer cross-departmental questions like "How many students are enrolled in both Computer Science and Mathematics courses?"

Formal data management addresses five fundamental concerns:

### Why formal data management matters

| Concern | Explanation |
|---|---|
| **Data sharing** | Multiple users and applications need concurrent access to the same data. A bank's ATM system, mobile app, and branch counter software all read and write the same account balances simultaneously. Without coordination, these concurrent accesses can corrupt data. |
| **Integrity** | Rules (constraints) must keep data correct at all times. A student's age cannot be negative. An account balance cannot drop below the overdraft limit. These business rules must be enforced centrally, not scattered across individual applications. |
| **Efficiency** | As data grows, brute-force searching becomes impractical — indexed structures are essential. Searching through 100 million records one by one would take minutes. With a proper index (like a B+ tree), the same search takes milliseconds. |
| **Security** | Only authorised users should read or modify specific data. A payroll clerk should see salary information but not medical records. A doctor should see medical records but not salary data. Access control must be fine-grained, not all-or-nothing. |
| **Recovery** | After a crash, data must be restored to a consistent state. If a power failure occurs in the middle of a bank transfer (money debited from one account but not yet credited to the other), the system must be able to undo the partial operation and restore consistency. |

---

## 1.2 Evolution: File Systems → DBMS

### 1.2.1 The File System Era

In early computing every application managed its own flat files. This caused well-known problems:

| Problem | What goes wrong |
|---|---|
| **Data redundancy** | Same data (e.g. customer name) stored in many files across different programs. |
| **Inconsistency** | One copy updated, others not — conflicting versions. |
| **Difficulty in access** | Every new query may require writing a new program. |
| **Data isolation** | Data scattered across files in different formats. |
| **Integrity problems** | No central mechanism to enforce constraints like "balance ≥ 0". |
| **Atomicity problems** | A crash midway through a multi-step update leaves data in a partial state. |
| **Concurrent-access anomalies** | Two users updating the same file simultaneously can corrupt data. |
| **Security issues** | File-level permissions are too coarse for field-level control. |

### 1.2.2 The DBMS Solution

A **Database Management System (DBMS)** provides:

1. **Data definition** — DDL to specify schema, types, constraints.
2. **Data manipulation** — DML to insert, update, delete, and query.
3. **Controlled access** — Concurrency control, backup/recovery, authorisation, integrity enforcement.

**Key advantages over file systems:**

- **Program–data independence:** Schema stored in a catalog; storage changes don't force application rewrites.
- **Data abstraction:** Users see logical views, not physical storage details.
- **Multiple views:** Different users see different subsets of the same data.
- **Reduced redundancy:** Centralised storage + normalisation.
- **Transaction support:** ACID properties guarantee reliable processing.

### 1.2.3 The Three-Schema Architecture (ANSI/SPARC)

```
┌─────────────────────────────────┐
│  External Level (User Views)    │  ← View 1, View 2 …
├─────────────────────────────────┤
│  Conceptual Level (Logical)     │  ← Entities, relationships, constraints
├─────────────────────────────────┤
│  Internal Level (Physical)      │  ← Files, indexes, blocks on disk
└─────────────────────────────────┘
```

- **Logical data independence** — Change the conceptual schema without affecting external views.
- **Physical data independence** — Change internal storage (add index, reorganise files) without affecting the conceptual schema.

---

## 1.3 Structured Data Management Concepts

### 1.3.1 Data Models

A **data model** is a collection of concepts and rules for describing the structure of a database — what the data looks like, how it's organised, what constraints it must obey, and what operations can be performed on it. Think of a data model as a "blueprint language" for databases: just as architectural blueprints use walls, floors, and doors to describe a building, a data model uses entities, attributes, and relationships (or tables, rows, and columns) to describe a database.

Different data models operate at different levels of abstraction, each serving a different audience:

| Category | Examples | Purpose | Audience |
|---|---|---|---|
| **Conceptual (high-level)** | ER model, UML class diagrams | Describe data as users perceive it — what exists in the real world and how things relate. No concern for implementation. | Business analysts, end users, database designers during requirements gathering. |
| **Representational (logical)** | Relational, Network, Hierarchical, Object-Oriented | Provide a formal structure that a DBMS can work with. Concepts are understandable to end users but also precise enough for implementation. | Database designers, application developers. |
| **Physical (low-level)** | Indexes, hashing schemes, file structures, buffer management | Describe how data is physically stored on disk — which files, which block format, which indexing structures. | Database administrators, DBMS internals developers. |

The **relational model** (representational level) is by far the most widely used today. It represents data as tables (relations) with rows and columns. Its success comes from its simplicity — anyone can understand tables — combined with a solid mathematical foundation (relational algebra and calculus) that enables powerful query optimisation.

### 1.3.2 Schema vs. Instance

This distinction is fundamental and often tested in exams.

A **schema** (also called the **intension**) is the overall description or structure of the database. It defines what tables exist, what columns each table has, what data types those columns hold, and what constraints apply. The schema changes rarely — only when the database design itself evolves (adding a new column, creating a new table, changing a constraint).

An **instance** (also called the **extension** or **state**) is the actual data stored in the database at a particular moment in time. Every INSERT, UPDATE, or DELETE operation changes the instance. The instance changes constantly throughout the day as users interact with the database.

**Analogy:** Think of a spreadsheet template that defines column headers (Name, Age, Email, Department) and validation rules (Age must be a positive integer, Email must contain @). The template is the schema. The actual rows of data filled in by users constitute the instance. You can change the data freely (add/remove rows), but changing the template (adding a column) is a bigger decision that affects everyone.

**Why the distinction matters:** The three-schema architecture (external, conceptual, internal) relies on this separation. Each level has its own schema, and data independence means you can change one level's schema without affecting the others. The instance is always the same underlying data — just viewed through different schema "lenses."

### 1.3.3 Database Languages

A DBMS provides several specialised languages, each serving a distinct purpose. Together, they give users complete control over defining, populating, querying, securing, and managing the database.

| Language | Purpose | Examples | When used |
|---|---|---|---|
| **DDL** (Data Definition Language) | Define and modify the schema — create tables, alter structures, define constraints. | `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE` | During database design and schema evolution. |
| **DML** (Data Manipulation Language) | Query and update the data — retrieve, insert, modify, and delete rows. | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Every day, by applications and users. |
| **DCL** (Data Control Language) | Control who can access what data and what operations they can perform. | `GRANT`, `REVOKE` | By database administrators to manage security. |
| **TCL** (Transaction Control Language) | Manage transaction boundaries — commit changes or roll them back. | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | By applications that need atomic operations (e.g. bank transfers). |

In practice, SQL combines all four languages into a single unified language. When you write `CREATE TABLE`, you're using SQL's DDL subset. When you write `SELECT`, you're using the DML subset. But they're all part of the same SQL standard.

---

## 1.4 Relational Database Fundamentals

### 1.4.1 The Relational Model (Codd, 1970)

The relational model, proposed by **Edgar F. Codd** at IBM in 1970, was a revolutionary departure from the hierarchical and network models that preceded it. Instead of requiring programmers to navigate complex pointer structures to find data, Codd proposed a deceptively simple idea: **represent all data as tables** (which he called "relations" from set theory), and use **mathematical logic** to query them.

This simplicity was its greatest strength. A table is something anyone can understand — it's just rows and columns, like a spreadsheet. But underneath this simplicity lies a rigorous mathematical foundation (relational algebra and relational calculus) that enables the DBMS to automatically optimise queries, regardless of how the data is physically stored.

Today, nearly every major database system — Oracle, PostgreSQL, MySQL, SQL Server, SQLite — is based on the relational model. Understanding it is non-negotiable for any computer science professional.

**Core terminology:**

Data is represented as a collection of **relations** (tables).

| Term | Meaning |
|---|---|
| **Relation** | A table with rows and columns. |
| **Tuple** | A single row. |
| **Attribute** | A named column. |
| **Domain** | Set of allowed values for an attribute. |
| **Degree** | Number of attributes. |
| **Cardinality** | Number of tuples. |
| **Relation schema** | Relation name + attribute list, e.g. `STUDENT(ID, Name, Age)`. |

### 1.4.2 Properties of Relations

1. Each tuple is distinct (no duplicate rows).
2. Tuple ordering is insignificant.
3. Attribute ordering is insignificant (identified by name).
4. Every attribute value is **atomic** (indivisible) — **First Normal Form (1NF)**.

### 1.4.3 Keys

| Key Type | Definition |
|---|---|
| **Superkey** | Any set of attributes that uniquely identifies a tuple. |
| **Candidate key** | A *minimal* superkey (removing any attribute breaks uniqueness). |
| **Primary key (PK)** | The candidate key chosen by the designer. |
| **Foreign key (FK)** | Attribute(s) in one relation referencing the PK of another. |
| **Alternate key** | A candidate key not chosen as PK. |

### 1.4.4 Integrity Constraints

- **Entity integrity:** No PK attribute can be NULL.
- **Referential integrity:** FK value must be NULL or match an existing PK in the referenced relation.
- **Domain constraints:** Every value must belong to its attribute's defined domain.
- **Key constraints:** No two tuples share the same PK value.

### 1.4.5 Relational Algebra (Overview)

| Operation | Symbol | Description |
|---|---|---|
| **Selection** | σ | Pick tuples satisfying a condition. `σ_{age>20}(STUDENT)` |
| **Projection** | π | Pick specific columns. `π_{Name,Age}(STUDENT)` |
| **Union** | ∪ | Combine tuples from two union-compatible relations. |
| **Intersection** | ∩ | Tuples common to both. |
| **Difference** | − | Tuples in one but not the other. |
| **Cartesian product** | × | Every tuple of A combined with every tuple of B. |
| **Join** | ⋈ | Combine related tuples on a condition (selection over product). |

---

## 1.5 Data Modelling: Entities, Attributes, Relationships

### 1.5.1 The ER Model (Chen, 1976)

Represents the real world using:

- **Entities** — Distinguishable real-world objects.
- **Attributes** — Properties of entities.
- **Relationships** — Associations among entities.

### 1.5.2 Entity Types

- **Strong entity:** Has its own key attribute (e.g. EMPLOYEE with Emp_ID).
- **Weak entity:** No key of its own; depends on an **identifying (owner) entity** via an **identifying relationship**. Uses a **partial key** + owner's PK.
  - Example: DEPENDENT (weak) depends on EMPLOYEE (strong). Identified by (Emp_ID, Dependent_Name).

### 1.5.3 Attribute Types

| Type | Description | Example |
|---|---|---|
| **Simple** | Atomic, indivisible | FirstName |
| **Composite** | Divisible into sub-parts | FullName → {First, Middle, Last} |
| **Single-valued** | One value per entity | DateOfBirth |
| **Multi-valued** | Multiple values per entity | PhoneNumbers |
| **Derived** | Computed from other attributes | Age (from DOB) |
| **Key** | Uniquely identifies entity | EmployeeID |

### 1.5.4 Relationships

**Degree:**
- **Unary (recursive):** Entity relates to itself (EMPLOYEE supervises EMPLOYEE).
- **Binary:** Between two entity types (most common).
- **Ternary:** Among three entity types.

**Cardinality ratios (binary):**

| Ratio | Meaning | Example |
|---|---|---|
| **1:1** | Each side relates to at most one on the other. | EMPLOYEE manages DEPARTMENT |
| **1:N** | One on "1" side relates to many on "N" side. | DEPARTMENT has many EMPLOYEEs |
| **M:N** | Many-to-many. | STUDENT enrols in COURSEs |

**Participation:**
- **Total (mandatory):** Every entity must participate (double line in ER diagrams).
- **Partial (optional):** Some entities may not participate (single line).

### 1.5.5 ER-to-Relational Mapping Rules

| ER Construct | Relational Mapping |
|---|---|
| Strong entity | Table with all simple attributes; key attribute → PK. |
| Weak entity | Table with partial key + owner's PK as FK; composite PK = both. |
| 1:1 relationship | Add PK of one side as FK in the other (prefer the total-participation side). |
| 1:N relationship | Add PK of "1" side as FK in the "N" side table. |
| M:N relationship | New junction table; PKs of both sides as FKs; composite PK. |
| Multi-valued attribute | Separate table: attribute value + FK to owner's PK. |

---

## 1.6 Normalisation — Eliminating Redundancy Systematically

Normalisation is the process of decomposing relations to remove redundancy and avoid update anomalies. Each **normal form** builds on the previous one.

### 1.6.1 Functional Dependencies (FDs)

A functional dependency X → Y means: if two tuples have the same value for attribute set X, they must have the same value for Y.

Example in EMPLOYEE: `Emp_ID → {Name, Salary, Dept_ID}` — knowing the Emp_ID uniquely determines the other attributes.

**Types of FDs:**

| Type | Definition | Example |
|---|---|---|
| **Full FD** | Y is functionally dependent on X, and not on any proper subset of X. | {Student_ID, Course_ID} → Grade (neither Student_ID alone nor Course_ID alone determines Grade). |
| **Partial FD** | Y depends on a proper subset of a composite key. | {Student_ID, Course_ID} → Student_Name — Student_Name depends only on Student_ID. |
| **Transitive FD** | X → Y and Y → Z, so X → Z transitively (Y is not a candidate key). | Emp_ID → Dept_ID → Dept_Name — Dept_Name transitively depends on Emp_ID through Dept_ID. |

### 1.6.2 First Normal Form (1NF)

**Rule:** Every attribute value must be atomic (indivisible). No repeating groups or multi-valued attributes in a single cell.

**Violation example:**

```
STUDENT(ID, Name, PhoneNumbers)
──────────────────────────────────
101, Alice, {9876543210, 9123456789}   ← Multi-valued — violates 1NF
```

**Fix:** Create a separate row for each phone number, or move phone numbers to a separate table.

```
STUDENT(ID, Name)
STUDENT_PHONE(ID, Phone)    ← Each row has one phone number
```

### 1.6.3 Second Normal Form (2NF)

**Rule:** Must be in 1NF + no **partial dependencies** — every non-key attribute must depend on the **entire** primary key (relevant only when PK is composite).

**Violation example:**

```
ENROLMENT(Student_ID, Course_ID, Student_Name, Grade)
PK = {Student_ID, Course_ID}

Student_Name depends only on Student_ID (partial dependency) — violates 2NF.
```

**Fix:** Decompose:

```
STUDENT(Student_ID, Student_Name)
ENROLMENT(Student_ID, Course_ID, Grade)
```

### 1.6.4 Third Normal Form (3NF)

**Rule:** Must be in 2NF + no **transitive dependencies** — no non-key attribute depends on another non-key attribute.

**Violation example:**

```
EMPLOYEE(Emp_ID, Dept_ID, Dept_Name)
PK = Emp_ID

Emp_ID → Dept_ID → Dept_Name   ← Dept_Name transitively depends on Emp_ID — violates 3NF.
```

**Fix:** Decompose:

```
EMPLOYEE(Emp_ID, Dept_ID)
DEPARTMENT(Dept_ID, Dept_Name)
```

### 1.6.5 Boyce-Codd Normal Form (BCNF)

**Rule:** For every non-trivial functional dependency X → Y, X must be a **superkey**.

BCNF is stricter than 3NF. A relation can be in 3NF but not BCNF when a non-key attribute determines part of the candidate key.

**Example violation:**

```
TEACHING(Student, Course, Instructor)
FDs: {Student, Course} → Instructor
     Instructor → Course    ← Instructor is not a superkey — violates BCNF
```

**Fix:** Decompose:

```
INSTRUCTOR_COURSE(Instructor, Course)
STUDENT_INSTRUCTOR(Student, Instructor)
```

### 1.6.6 Normalisation Summary Table

| Normal Form | Requirement | Eliminates |
|---|---|---|
| **1NF** | Atomic values only | Repeating groups, multi-valued cells |
| **2NF** | 1NF + no partial dependencies | Partial dependencies on composite PK |
| **3NF** | 2NF + no transitive dependencies | Transitive dependencies among non-key attributes |
| **BCNF** | Every determinant is a superkey | All remaining anomalies from FDs |

### 1.6.7 Update Anomalies (Why Normalise?)

When a table is not properly normalised, three types of anomalies occur:

| Anomaly | Problem | Example (un-normalised EMPLOYEE with Dept_Name) |
|---|---|---|
| **Insertion anomaly** | Cannot insert data without unrelated data. | Can't add a new department unless at least one employee exists in it. |
| **Deletion anomaly** | Deleting data causes unintended loss. | Deleting the last employee in a department loses the department info. |
| **Update anomaly** | Must update the same fact in multiple rows. | Renaming a department requires updating every employee row in that department. |

---

## 1.7 DBMS Architecture — Internal Components

Understanding what happens inside a DBMS helps reason about performance and troubleshooting.

### 1.7.1 Major Components

```
                    ┌──────────────────┐
 User/Application → │  Query Interface  │  (SQL parser, API)
                    └────────┬─────────┘
                             ▼
                    ┌──────────────────┐
                    │  Query Processor  │
                    │  ┌──────────────┐│
                    │  │   Parser     ││  → Syntax check, parse tree
                    │  │   Optimizer  ││  → Choose best execution plan
                    │  │   Executor   ││  → Execute the plan
                    │  └──────────────┘│
                    └────────┬─────────┘
                             ▼
                    ┌──────────────────┐
                    │ Transaction Mgr   │  → ACID enforcement
                    │ Concurrency Ctrl  │  → Locks, MVCC
                    │ Recovery Mgr      │  → WAL, checkpoints
                    └────────┬─────────┘
                             ▼
                    ┌──────────────────┐
                    │ Storage Engine    │
                    │ ┌───────────────┐│
                    │ │ Buffer Manager││  → Cache pages in memory
                    │ │ Disk Manager  ││  → Read/write disk blocks
                    │ └───────────────┘│
                    └────────┬─────────┘
                             ▼
                    ┌──────────────────┐
                    │   Disk Storage    │  → Data files, index files, log files
                    └──────────────────┘
```

### 1.7.2 The Query Processor in Detail

1. **Parser** — Checks SQL syntax, verifies table/column names against the catalog, produces a parse tree.
2. **Query Optimizer** — Considers multiple execution plans (which index to use, join order, join algorithm) and picks the one with the lowest estimated cost. Uses statistics (table sizes, index selectivity, data distribution).
3. **Query Executor** — Runs the chosen plan, calling the storage engine to fetch/modify data.

### 1.7.3 The Catalog (Data Dictionary)

The catalog stores **metadata** about the database:

- Table names, column names, data types
- Constraints (PK, FK, CHECK, UNIQUE)
- Index definitions
- View definitions
- User privileges
- Statistics (row counts, value distributions) used by the optimizer

---

## 1.8 Indexing and File Structures

### 1.8.1 Why Indexes Matter

Without an index, finding a row requires a **full table scan** — reading every row. With millions of rows, this is extremely slow.

An index is a data structure that allows the DBMS to locate rows quickly based on the value of one or more columns — like a book's index that maps topics to page numbers.

### 1.8.2 Types of Indexes

#### B-Tree / B+ Tree (Most Common)

- Self-balancing tree structure.
- **B+ Tree:** All data pointers are in the leaf nodes; internal nodes contain only keys for navigation. Leaf nodes are linked for efficient range scans.
- Used by virtually all relational databases (PostgreSQL, MySQL, Oracle, SQL Server).
- Supports equality lookups (`=`), range queries (`BETWEEN`, `<`, `>`), and prefix searches (`LIKE 'abc%'`).

```
                    [30 | 60]                  ← Internal (root)
                   /    |     \
           [10|20]   [40|50]   [70|80]         ← Internal
           / | \     / | \     / | \
         [leaves linked → → → → → →]          ← Leaf nodes (contain data pointers)
```

**Time complexity:** O(log n) for search, insert, delete.

#### Hash Index

- Uses a hash function to map key values to bucket locations.
- Very fast for **equality lookups** (O(1) average).
- **Cannot** support range queries or sorting.
- Used for in-memory hash tables, hash joins.

#### Bitmap Index

- Uses a bit vector for each distinct value. Bit i is 1 if row i has that value.
- Very space-efficient for columns with low cardinality (few distinct values like Gender, Status).
- Excellent for complex AND/OR queries on multiple such columns.
- Common in data warehousing (Oracle, PostgreSQL).

### 1.8.3 Clustered vs. Non-Clustered Indexes

| Aspect | Clustered Index | Non-Clustered (Secondary) Index |
|---|---|---|
| Physical order | Table rows are physically stored in index order. | Index is a separate structure pointing to row locations. |
| Number per table | Only one (data can only be physically sorted one way). | Many can exist. |
| Speed | Fastest for range scans on the indexed column. | Requires an extra lookup to fetch the actual row (unless covering). |
| Example | Primary key index in MySQL InnoDB. | Index on Email column. |

### 1.8.4 Covering Index

A **covering index** includes all the columns needed by a query. The DBMS can answer the query entirely from the index without touching the main table — called an **index-only scan**.

```sql
-- If an index exists on (Dept_ID, Salary):
SELECT Dept_ID, AVG(Salary) FROM EMPLOYEE GROUP BY Dept_ID;
-- This can be answered entirely from the index.
```

### 1.8.5 When to Create Indexes

| Create an index when… | Avoid indexing when… |
|---|---|
| Column frequently appears in WHERE, JOIN, ORDER BY | Table is very small (full scan is faster) |
| Column has high selectivity (many distinct values) | Column is rarely used in queries |
| Read-heavy workload | Write-heavy workload (indexes slow down INSERT/UPDATE/DELETE) |
| | Column has very low cardinality (use bitmap instead or skip) |

### 1.8.6 File Organisation Methods

How rows are physically stored on disk:

| Method | Description | Best for |
|---|---|---|
| **Heap file** | Rows inserted in no particular order. | Bulk inserts; full table scans. |
| **Sorted (sequential) file** | Rows stored in order of a sort key. | Range queries on the sort key. |
| **Hash file** | Hash function determines which bucket stores the row. | Equality lookups. |
| **B+ tree file** | Rows stored in the leaf nodes of a B+ tree. | General purpose; balanced read/write. |

---

## 1.9 Views in SQL

### 1.9.1 What is a View?

A **view** is a virtual table defined by a SELECT query. It does not store data itself — every time you query a view, the underlying SELECT is executed.

```sql
CREATE VIEW High_Earners AS
SELECT Emp_ID, First_Name, Last_Name, Salary
FROM EMPLOYEE
WHERE Salary > 100000;

-- Query the view like a table
SELECT * FROM High_Earners WHERE Last_Name = 'Smith';
```

### 1.9.2 Benefits of Views

- **Simplification:** Complex queries wrapped in a view; users query a simple name.
- **Security:** Restrict access — users see only columns/rows exposed by the view.
- **Logical data independence:** If the underlying table changes, update the view definition instead of all applications.
- **Consistency:** A common business definition (e.g. "active customer") defined once and reused.

### 1.9.3 Updatable vs. Non-Updatable Views

A view is generally **updatable** (INSERT/UPDATE/DELETE through it) only if:
- It references exactly one base table.
- It includes the primary key.
- It does not use aggregates, DISTINCT, GROUP BY, HAVING, UNION, or subqueries.

Otherwise, the view is read-only.

### 1.9.4 Materialised Views

A **materialised view** physically stores the result of the query. It must be **refreshed** (manually or on a schedule) when the underlying data changes.

- Faster reads (pre-computed).
- Stale data risk if not refreshed.
- Useful for expensive aggregations in reporting/data warehousing.

```sql
CREATE MATERIALIZED VIEW Dept_Summary AS
SELECT Dept_ID, COUNT(*) AS Emp_Count, AVG(Salary) AS Avg_Salary
FROM EMPLOYEE
GROUP BY Dept_ID;

-- Refresh when needed
REFRESH MATERIALIZED VIEW Dept_Summary;
```

---

## 1.10 Stored Procedures, Functions, and Triggers

### 1.10.1 Stored Procedures

A **stored procedure** is a named block of SQL statements stored in the database and executed on the server.

```sql
CREATE PROCEDURE GiveRaise(IN dept INT, IN pct DECIMAL)
BEGIN
    UPDATE EMPLOYEE
    SET Salary = Salary * (1 + pct / 100)
    WHERE Dept_ID = dept;
END;

-- Call it
CALL GiveRaise(5, 10);  -- 10% raise for department 5
```

**Advantages:**
- Reduce network traffic (logic runs on the server).
- Encapsulate business logic.
- Security — grant EXECUTE permission without giving direct table access.
- Reusability.

### 1.10.2 Functions

Similar to procedures but **return a value** and can be used inside SQL expressions.

```sql
CREATE FUNCTION GetDeptName(d_id INT) RETURNS VARCHAR(100)
BEGIN
    DECLARE d_name VARCHAR(100);
    SELECT Dept_Name INTO d_name FROM DEPARTMENT WHERE Dept_ID = d_id;
    RETURN d_name;
END;

-- Use in a query
SELECT Emp_ID, First_Name, GetDeptName(Dept_ID) AS Department FROM EMPLOYEE;
```

### 1.10.3 Triggers

A **trigger** is a stored procedure that automatically fires when a specific event (INSERT, UPDATE, DELETE) occurs on a table.

```sql
CREATE TRIGGER salary_audit
AFTER UPDATE OF Salary ON EMPLOYEE
FOR EACH ROW
BEGIN
    INSERT INTO SALARY_AUDIT_LOG (Emp_ID, Old_Salary, New_Salary, Change_Date)
    VALUES (OLD.Emp_ID, OLD.Salary, NEW.Salary, CURRENT_TIMESTAMP);
END;
```

**Trigger timing:**
- `BEFORE` — Fires before the event; can modify or reject the operation.
- `AFTER` — Fires after the event; typically used for logging/auditing.
- `INSTEAD OF` — Replaces the event (commonly used with views to make non-updatable views updatable).

**Use cases:** Audit logging, enforcing complex constraints, maintaining derived/summary data, cascading business rules.

**Caution:** Overuse of triggers makes debugging difficult — they execute implicitly and can chain (one trigger firing another).

---
---

# SESSION 2 — SQL Fundamentals and Transaction Concepts

*References: T1 Ch.6 | Ch.7 (Sec 7.1)*

---

## 2.1 SQL Overview

**SQL (Structured Query Language)** is the standard language for creating, managing, and querying relational databases. It was originally developed at IBM in the early 1970s (called SEQUEL) and became an ANSI/ISO standard in 1986. Today, every major RDBMS supports SQL, though each vendor adds its own extensions.

SQL's most important characteristic is that it is **declarative**, not procedural. In a procedural language like C or Python, you tell the computer **how** to get the answer step by step: "open file, read line, compare with target, if match then add to result..." In SQL, you tell the database **what** you want: "give me all employees with salary > 70000." The DBMS's **query optimizer** figures out the best execution strategy — which indexes to use, which join algorithm, what order to process tables — entirely on its own.

This declarative nature has a profound consequence: the same SQL query can run on a tiny SQLite database on your phone or a massive distributed PostgreSQL cluster with billions of rows. The optimizer adapts the execution plan to the underlying storage and statistics. You never have to rewrite queries when data grows or storage changes.

SQL combines four sub-languages into one:
- **DDL** for schema definition (CREATE, ALTER, DROP)
- **DML** for data manipulation (SELECT, INSERT, UPDATE, DELETE)
- **DCL** for access control (GRANT, REVOKE)
- **TCL** for transaction management (COMMIT, ROLLBACK, SAVEPOINT)

---

## 2.2 DDL Statements

### 2.2.1 CREATE TABLE

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

**Common data types:**

| Category | Types |
|---|---|
| Numeric | `INT`, `SMALLINT`, `BIGINT`, `DECIMAL(p,s)`, `FLOAT`, `REAL` |
| Character | `CHAR(n)`, `VARCHAR(n)`, `TEXT` |
| Date/Time | `DATE`, `TIME`, `TIMESTAMP`, `INTERVAL` |
| Boolean | `BOOLEAN` |
| Binary | `BLOB`, `BYTEA` |

**Constraints:**

| Constraint | Purpose |
|---|---|
| `PRIMARY KEY` | Unique + NOT NULL identifier for each row. |
| `FOREIGN KEY … REFERENCES` | Enforces referential integrity. |
| `NOT NULL` | Attribute must have a value. |
| `UNIQUE` | No duplicate values (NULLs may be allowed). |
| `CHECK` | Boolean validation expression. |
| `DEFAULT` | Value used when none is supplied. |

**Referential triggered actions (ON DELETE / ON UPDATE):**

| Action | Behaviour |
|---|---|
| `CASCADE` | Propagate delete/update to referencing rows. |
| `SET NULL` | Set FK to NULL. |
| `SET DEFAULT` | Set FK to its default value. |
| `RESTRICT` / `NO ACTION` | Reject if referencing rows exist. |

### 2.2.2 ALTER TABLE

```sql
ALTER TABLE EMPLOYEE ADD COLUMN Phone VARCHAR(20);
ALTER TABLE EMPLOYEE DROP COLUMN Phone;
ALTER TABLE EMPLOYEE ADD CONSTRAINT chk_sal CHECK (Salary >= 15000);
```

### 2.2.3 DROP TABLE

```sql
DROP TABLE EMPLOYEE;          -- Fails if referenced
DROP TABLE EMPLOYEE CASCADE;  -- Drops dependent objects too
```

---

## 2.3 DML Statements

### 2.3.1 INSERT

```sql
-- Single row
INSERT INTO EMPLOYEE (Emp_ID, First_Name, Last_Name, Salary, Dept_ID)
VALUES (101, 'Alice', 'Johnson', 75000.00, 5);

-- Multiple rows
INSERT INTO EMPLOYEE (Emp_ID, First_Name, Last_Name, Salary, Dept_ID)
VALUES (102, 'Bob', 'Smith', 68000.00, 3),
       (103, 'Carol', 'Williams', 82000.00, 5);

-- Insert from query
INSERT INTO HIGH_EARNERS (Emp_ID, First_Name, Salary)
SELECT Emp_ID, First_Name, Salary FROM EMPLOYEE WHERE Salary > 100000;
```

### 2.3.2 UPDATE

```sql
UPDATE EMPLOYEE SET Salary = Salary * 1.10 WHERE Dept_ID = 5;

UPDATE EMPLOYEE
SET Dept_ID = (SELECT Dept_ID FROM DEPARTMENT WHERE Dept_Name = 'Engineering')
WHERE Emp_ID = 101;
```

### 2.3.3 DELETE

```sql
DELETE FROM EMPLOYEE WHERE Emp_ID = 103;
DELETE FROM EMPLOYEE;  -- all rows, structure remains
```

---

## 2.4 Retrieval Queries (SELECT)

### 2.4.1 Basic Syntax & Execution Order

```sql
SELECT   column_list          -- 5th
FROM     table(s)             -- 1st
WHERE    row_condition        -- 2nd
GROUP BY column_list          -- 3rd
HAVING   group_condition      -- 4th
ORDER BY column_list          -- 6th
LIMIT n  OFFSET m;            -- 7th
```

### 2.4.2 WHERE Clause Operators

```sql
-- Comparison: =, <>, <, >, <=, >=
SELECT * FROM EMPLOYEE WHERE Salary > 70000;

-- Logical: AND, OR, NOT
SELECT * FROM EMPLOYEE WHERE Dept_ID = 5 AND Salary > 60000;

-- Range
SELECT * FROM EMPLOYEE WHERE Salary BETWEEN 50000 AND 80000;

-- Set membership
SELECT * FROM EMPLOYEE WHERE Dept_ID IN (3, 5, 7);

-- Pattern matching
SELECT * FROM EMPLOYEE WHERE Last_Name LIKE 'J%';

-- NULL checks
SELECT * FROM EMPLOYEE WHERE Dept_ID IS NULL;
```

### 2.4.3 Aggregate Functions

| Function | Returns |
|---|---|
| `COUNT(*)` | Number of rows |
| `COUNT(col)` | Non-NULL values in col |
| `SUM(col)` | Sum of numeric col |
| `AVG(col)` | Average |
| `MIN(col)` | Minimum |
| `MAX(col)` | Maximum |

```sql
SELECT Dept_ID, COUNT(*) AS Emp_Count, AVG(Salary) AS Avg_Sal
FROM EMPLOYEE
GROUP BY Dept_ID
HAVING COUNT(*) > 5
ORDER BY Avg_Sal DESC;
```

### 2.4.4 DISTINCT and Aliases

```sql
SELECT DISTINCT Dept_ID FROM EMPLOYEE;

SELECT E.First_Name AS "Name", D.Dept_Name AS "Dept"
FROM EMPLOYEE E, DEPARTMENT D
WHERE E.Dept_ID = D.Dept_ID;
```

---

## 2.5 Joins

### Why Joins are Essential

In a properly normalised database, related data is spread across multiple tables. Student information is in the STUDENT table, department information is in the DEPARTMENT table, and the connection between them is a foreign key (Dept_ID). To answer a question like "What is the department name of each student?", you need to **combine** rows from both tables based on matching Dept_IDs. This operation is called a **join**.

Joins are the most powerful feature of SQL and the primary reason relational databases can maintain normalised data without redundancy while still answering complex cross-table questions efficiently. Understanding the different types of joins — and when each is appropriate — is essential for writing correct queries.

### How Joins Work Conceptually

A join takes two tables and produces a new result table by matching rows based on a condition (usually equality of a foreign key and a primary key). The type of join determines what happens when a row in one table has **no match** in the other.

| Join Type | What it returns | When to use |
|---|---|---|
| **INNER JOIN** | Only rows that have a match in both tables. Rows without a match in either table are excluded. | When you only want data that exists in both tables (e.g., "students who are assigned to a department"). |
| **LEFT (OUTER) JOIN** | All rows from the left table + matching rows from the right. Where there's no match, the right side columns are filled with NULL. | When you want all items from the left table regardless of whether they have a match (e.g., "all students, with their department name if they have one"). |
| **RIGHT (OUTER) JOIN** | All rows from the right table + matching rows from the left. Symmetric mirror of LEFT JOIN. | Same as LEFT JOIN but preserving the right table. In practice, most people just use LEFT JOIN and swap table order. |
| **FULL (OUTER) JOIN** | All rows from both tables. NULLs fill in where there's no match on either side. | When you want to see all data from both tables, including unmatched rows on both sides (e.g., "all students and all departments, showing the connection where it exists"). |
| **CROSS JOIN** | Every possible combination of rows (Cartesian product). If table A has m rows and B has n rows, the result has m×n rows. | Rare in practice. Used for generating combinations (e.g., all possible product-color pairs) or when the WHERE clause provides the join condition. |
| **NATURAL JOIN** | Automatically joins on all columns with the same name in both tables. No explicit ON clause needed. | Convenient but risky — if table structures change and a new column with the same name is added, the join condition silently changes. Avoid in production code. |
| **SELF JOIN** | A table joined with itself, using different aliases. | When a table has a self-referencing relationship (e.g., EMPLOYEE has a Manager_ID that references another EMPLOYEE's Emp_ID). |
| **INNER JOIN** | Only rows that match in both tables. |
| **LEFT JOIN** | All rows from left table + matching right (NULLs where no match). |
| **RIGHT JOIN** | All rows from right table + matching left. |
| **FULL OUTER JOIN** | All rows from both tables; NULLs where no match. |
| **CROSS JOIN** | Cartesian product (every combination). |
| **NATURAL JOIN** | Join on all columns with the same name (implicit). |
| **SELF JOIN** | A table joined with itself. |

```sql
-- Inner Join
SELECT E.First_Name, D.Dept_Name
FROM EMPLOYEE E INNER JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;

-- Left Join
SELECT E.First_Name, D.Dept_Name
FROM EMPLOYEE E LEFT JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID;

-- Self Join (find employees and their managers)
SELECT E.First_Name AS Employee, M.First_Name AS Manager
FROM EMPLOYEE E LEFT JOIN EMPLOYEE M ON E.Manager_ID = M.Emp_ID;
```

---

## 2.6 Subqueries

### What is a Subquery?

A **subquery** (also called a nested query or inner query) is a complete SELECT statement embedded inside another SQL statement. The outer statement can be a SELECT, INSERT, UPDATE, or DELETE. Subqueries allow you to use the result of one query as input to another, enabling multi-step reasoning within a single SQL statement.

Without subqueries, answering questions like "find employees who earn more than the company average" would require two separate queries: first compute the average, then use that value in a filter. Subqueries let you express this as a single, self-contained query.

### Why Subqueries Matter

Subqueries are essential for expressing conditions that depend on **aggregate values** or on the **existence** of related data. They're also the foundation of the **SQL division** pattern — answering "for all" questions like "find suppliers who supply every part."

### Types of Subqueries

A subquery is a SELECT inside another SQL statement. The type depends on what it returns and where it appears:

### Types of subqueries

| Type | Location | Returns |
|---|---|---|
| **Scalar subquery** | WHERE, SELECT | Single value |
| **Row subquery** | WHERE | Single row |
| **Table subquery** | FROM, WHERE (IN/EXISTS) | Multiple rows/columns |
| **Correlated subquery** | WHERE | Re-evaluated for each outer row |

```sql
-- Scalar (single value)
SELECT First_Name, Salary
FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE);

-- IN with subquery
SELECT First_Name FROM EMPLOYEE
WHERE Dept_ID IN (SELECT Dept_ID FROM DEPARTMENT WHERE Location = 'London');

-- EXISTS (correlated)
SELECT D.Dept_Name FROM DEPARTMENT D
WHERE EXISTS (SELECT 1 FROM EMPLOYEE E WHERE E.Dept_ID = D.Dept_ID);

-- Subquery in FROM (derived table)
SELECT Dept_ID, Avg_Sal
FROM (SELECT Dept_ID, AVG(Salary) AS Avg_Sal FROM EMPLOYEE GROUP BY Dept_ID) AS DeptAvg
WHERE Avg_Sal > 70000;
```

---

## 2.7 Transaction Basics & ACID Properties

### 2.7.1 What is a Transaction?

A **transaction** is a logical unit of work consisting of one or more database operations that must be executed as an indivisible whole.

Classic example — bank transfer:

```
BEGIN TRANSACTION;
  UPDATE ACCOUNT SET Balance = Balance - 500 WHERE Acc_ID = 'A';
  UPDATE ACCOUNT SET Balance = Balance + 500 WHERE Acc_ID = 'B';
COMMIT;
```

Both updates must succeed together or fail together.

### 2.7.2 ACID Properties

| Property | Meaning | How DBMS enforces it |
|---|---|---|
| **Atomicity** | All-or-nothing — either all operations of the transaction complete, or none do. | Transaction manager + undo log. If any operation fails, all changes are rolled back. |
| **Consistency** | A transaction takes the database from one consistent state to another. All integrity constraints are satisfied before and after. | Constraint checking at commit time. |
| **Isolation** | Concurrent transactions execute as if they were serial (one after another). Intermediate states are invisible to other transactions. | Concurrency control: locks, MVCC, timestamp ordering. |
| **Durability** | Once a transaction commits, its changes survive any subsequent failure (crash, power loss). | Write-ahead log (WAL) flushed to stable storage before commit is acknowledged. |

### 2.7.3 Transaction States

```
         ┌──────────┐
         │  Active   │ ──(all ops succeed)──→ Partially Committed ──(flush to disk)──→ Committed
         └──────────┘                                    │
              │                                          │ (flush fails)
         (operation fails)                               ▼
              ▼                                        Failed
           Failed ──→ Aborted (rollback applied) ──→ [restart or kill]
```

- **Active:** Transaction is executing.
- **Partially committed:** Final statement executed; awaiting confirmation that changes are durable.
- **Committed:** Changes are permanent.
- **Failed:** An error occurred; cannot proceed.
- **Aborted:** Rollback complete; database restored to state before the transaction started.

### 2.7.4 SQL Transaction Control

```sql
BEGIN TRANSACTION;     -- or just BEGIN
-- … DML operations …
SAVEPOINT sp1;         -- named checkpoint
-- … more operations …
ROLLBACK TO sp1;       -- undo back to savepoint
COMMIT;                -- make all changes permanent
-- or
ROLLBACK;              -- undo everything since BEGIN
```

---

## 2.8 Schema Evolution, Versioning, and Denormalisation

### 2.8.1 Schema Evolution

Real-world databases change over time. Schema evolution refers to modifying the database schema after it is in production.

**Common schema changes:**
- Adding/removing columns (`ALTER TABLE`)
- Changing data types
- Adding/dropping constraints
- Creating/dropping indexes
- Splitting or merging tables

**Challenges:**
- Existing data may need migration (backfilling new columns, transforming data types).
- Applications that depend on the old schema may break.
- Views and stored procedures may become invalid.

**Migration strategies:**
- **Expand-and-contract pattern:** First add the new column alongside the old one. Migrate data. Update applications. Finally drop the old column.
- **Schema versioning:** Maintain version numbers for the schema; applications specify which version they target.
- **Backward/forward compatibility:** Design changes so both old and new application versions can co-exist during rollout.

### 2.8.2 Denormalisation

**Normalisation** removes redundancy by decomposing tables (1NF → 2NF → 3NF → BCNF).

**Denormalisation** intentionally reintroduces redundancy for performance:

| When to denormalise | Technique | Trade-off |
|---|---|---|
| Frequent expensive joins | Store redundant columns to avoid the join. | Faster reads, slower writes, risk of inconsistency. |
| Aggregations computed repeatedly | Pre-computed summary tables or materialised views. | Stale data if not refreshed. |
| Read-heavy workloads | Duplicate data across tables optimised for read patterns. | Storage overhead + update anomalies. |

**Rule of thumb:** Normalise first for correctness, then denormalise selectively for measured performance problems.

---

## 2.9 Limits of Relational Systems — Motivation for NoSQL

The relational model has been the dominant paradigm since the 1980s, and for good reason — it provides a clean theoretical foundation, strong consistency guarantees, and a powerful query language (SQL). For structured data with well-defined schemas and moderate scale, relational databases remain the best choice.

However, the explosion of web-scale applications in the 2000s (Google, Amazon, Facebook, Twitter) exposed scenarios where relational databases struggle. These companies needed to handle millions of concurrent users, petabytes of data, and global distribution — requirements that pushed traditional RDBMS architectures to their limits.

| Limitation | Detailed explanation |
|---|---|
| **Rigid schema** | In a relational database, every row in a table must conform to the same schema (same columns, same types). Adding a column requires an `ALTER TABLE` operation that may lock the entire table for minutes or hours on large tables (billions of rows). In contrast, web applications evolve rapidly — new features require new data fields every sprint. The mismatch between slow schema evolution and fast feature development became a significant pain point. |
| **Impedance mismatch** | Modern applications use object-oriented programming (classes, inheritance, nested objects). Mapping these rich structures to flat relational tables (rows and columns) requires an **Object-Relational Mapping (ORM)** layer that adds complexity, performance overhead, and a constant source of bugs. A "User" object with a list of "Addresses" must be split across two tables and joined back together on every read. |
| **Horizontal scaling is hard** | Relational databases are designed for **vertical scaling** (buy a bigger, more powerful server). When one server isn't enough, you must **shard** (partition) the database across multiple servers. But sharding breaks many relational features: joins across shards become expensive cross-network operations, foreign key constraints can't span shards, and distributed transactions require complex two-phase commit protocols that add latency. |
| **Unstructured/semi-structured data** | Many modern data types don't fit neatly into tables: JSON documents with varying fields, graph relationships (social networks), time-series data (IoT sensors), key-value pairs (session stores), and full-text content (articles, logs). Forcing these into rows and columns requires awkward workarounds. |
| **High write throughput** | Relational databases use write-ahead logging (WAL) and enforce ACID properties on every write. This ensures correctness but limits write throughput. At millions of writes per second (typical for ad-tech, IoT, or logging), the WAL and lock management become bottlenecks. |
| **Global distribution** | Maintaining strong consistency across data centres on different continents requires synchronous replication — every write must be confirmed by all replicas before it's committed. The speed of light imposes a minimum network round-trip time of ~100-300ms between continents, making synchronous replication painfully slow. |

These limitations motivated the **NoSQL** movement (roughly 2005-2015), which introduced new database architectures optimised for specific access patterns:

- **Key-Value stores** (Redis, Amazon DynamoDB) — Extremely fast lookups by a single key. Ideal for caching, session management, and simple data models. No complex queries.
- **Document stores** (MongoDB, CouchDB) — Store JSON/BSON documents with flexible schemas. Each document can have different fields. Great for content management, user profiles, and catalog data.
- **Column-family stores** (Apache Cassandra, HBase) — Optimised for write-heavy workloads with wide rows. Data organised by column families rather than rows. Excellent for time-series data, event logging, and analytics.
- **Graph databases** (Neo4j, Amazon Neptune) — Relationships are first-class citizens, stored as explicit edges between nodes. Ideal for social networks, recommendation engines, and fraud detection where traversing relationships is the primary operation.

NoSQL systems typically trade some ACID guarantees for scalability and flexibility, adopting the BASE model (covered in Session 4). The choice between relational and NoSQL is not "one is better" — it's about matching the database architecture to your specific access patterns, consistency requirements, and scale needs.

---

## 2.10 Concurrency Control — Managing Simultaneous Transactions

When multiple transactions execute simultaneously, the DBMS must ensure correctness. Without concurrency control, several problems arise.

### 2.10.1 Problems of Concurrent Execution

| Problem | What happens | Example |
|---|---|---|
| **Lost update** | Two transactions read the same value and both update it — one update overwrites the other. | T1 reads balance=1000, T2 reads balance=1000. T1 sets balance=900, T2 sets balance=800. T1's deduction is lost. |
| **Dirty read** | A transaction reads data written by another transaction that has not yet committed (and might roll back). | T1 updates salary to 90000 but hasn't committed. T2 reads 90000. T1 rolls back. T2 used a value that never really existed. |
| **Non-repeatable read** | A transaction reads the same row twice and gets different values because another transaction modified it in between. | T1 reads salary=70000. T2 updates salary to 80000 and commits. T1 reads salary again and gets 80000. |
| **Phantom read** | A transaction re-executes a query and finds new rows that weren't there before because another transaction inserted them. | T1 counts employees in Dept 5 → 10. T2 inserts a new employee in Dept 5 and commits. T1 counts again → 11. |

### 2.10.2 Isolation Levels (SQL Standard)

The SQL standard defines four isolation levels, each preventing more anomalies at the cost of reduced concurrency:

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read | Concurrency |
|---|---|---|---|---|
| **READ UNCOMMITTED** | Possible | Possible | Possible | Highest |
| **READ COMMITTED** | Prevented | Possible | Possible | High |
| **REPEATABLE READ** | Prevented | Prevented | Possible | Medium |
| **SERIALIZABLE** | Prevented | Prevented | Prevented | Lowest |

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN;
-- … your queries …
COMMIT;
```

**Default levels:** PostgreSQL defaults to READ COMMITTED. MySQL InnoDB defaults to REPEATABLE READ.

### 2.10.3 Lock-Based Concurrency Control

**Lock types:**

| Lock | Also called | Allows |
|---|---|---|
| **Shared lock (S)** | Read lock | Multiple transactions can read simultaneously; no writing. |
| **Exclusive lock (X)** | Write lock | Only one transaction can hold it; no other reads or writes. |

**Lock compatibility matrix:**

```
             Requested
             S     X
Held    S    ✓     ✗
        X    ✗     ✗
```

**Two-Phase Locking (2PL):**
The most common protocol to ensure serializability:

1. **Growing phase:** Transaction acquires locks but never releases any.
2. **Shrinking phase:** Transaction releases locks but never acquires new ones.

If every transaction follows 2PL, the schedule is guaranteed to be **serializable** (equivalent to some serial execution).

**Strict 2PL:** All exclusive locks are held until the transaction commits or aborts. Prevents cascading rollbacks.

**Rigorous 2PL:** All locks (shared and exclusive) are held until commit/abort. Simplest to implement; used by most DBMSs.

### 2.10.4 Deadlocks

A **deadlock** occurs when two or more transactions are each waiting for a lock held by the other.

```
T1 holds lock on Row A, waits for lock on Row B
T2 holds lock on Row B, waits for lock on Row A
→ Neither can proceed — deadlock!
```

**Detection and resolution:**
- **Wait-for graph:** DBMS builds a directed graph of "transaction X waits for transaction Y". A cycle indicates a deadlock.
- **Victim selection:** DBMS aborts one transaction (the "victim") to break the cycle. Typically chooses the transaction that has done the least work.

**Prevention strategies:**
- **Wait-die (older waits, younger aborts):** If T1 is older than T2, T1 waits. If T1 is younger, T1 aborts and restarts.
- **Wound-wait (older wounds, younger waits):** If T1 is older, T2 is aborted (wounded). If T1 is younger, T1 waits.
- **Timeout:** If a transaction waits longer than a threshold, assume deadlock and abort.

### 2.10.5 Multi-Version Concurrency Control (MVCC)

**MVCC** is an alternative to locking. Instead of blocking readers when a writer is active, the DBMS keeps **multiple versions** of each row.

**How it works:**
1. Each write creates a new version of the row with a timestamp/transaction ID.
2. Readers see the version that was current at the time their transaction started — they never block on writers.
3. Old versions are garbage-collected after all transactions that could need them have finished.

**Advantages:**
- Readers never block writers, writers never block readers.
- Dramatically improves concurrency for read-heavy workloads.

**Used by:** PostgreSQL, MySQL InnoDB, Oracle, CockroachDB.

**Snapshot Isolation (SI):** An MVCC-based isolation level where each transaction sees a consistent **snapshot** of the database as of its start time. Prevents dirty reads and non-repeatable reads. May allow **write skew** (two transactions read overlapping data and make disjoint updates that together violate a constraint).

### 2.10.6 Optimistic vs. Pessimistic Concurrency Control

| Approach | Philosophy | How it works | Best for |
|---|---|---|---|
| **Pessimistic** (locking) | Assume conflicts are likely — prevent them upfront. | Acquire locks before accessing data. | Write-heavy, high-contention workloads. |
| **Optimistic** | Assume conflicts are rare — detect them at commit time. | Execute without locks; at commit, check if any other transaction modified the same data. If conflict → abort and retry. | Read-heavy, low-contention workloads. |

---

## 2.11 Database Recovery Techniques

Recovery ensures **atomicity** and **durability** — committed transactions survive crashes, and uncommitted transactions are rolled back.

### 2.11.1 Write-Ahead Logging (WAL)

The most widely used recovery mechanism.

**Rule:** Before any change is written to the database on disk, the corresponding **log record** must first be written to the **log file** on stable storage.

**Log records contain:**
- Transaction ID
- Data item identifier (which row/page)
- Old value (before image) — for UNDO
- New value (after image) — for REDO
- Operation type (INSERT, UPDATE, DELETE)
- COMMIT / ABORT markers

### 2.11.2 UNDO and REDO Recovery

After a crash, the recovery manager reads the log and:

| Action | When applied | What it does |
|---|---|---|
| **UNDO** | Transaction was active (not committed) at crash time. | Roll back its changes using the old values from the log. |
| **REDO** | Transaction had committed but its changes might not have been flushed to disk. | Re-apply its changes using the new values from the log. |

**Recovery steps (ARIES-style):**
1. **Analysis phase:** Scan the log to determine which transactions were active at crash time and which pages might be dirty.
2. **Redo phase:** Re-apply all changes from the log (both committed and uncommitted) to bring the database to the state just before the crash.
3. **Undo phase:** Roll back all uncommitted transactions.

### 2.11.3 Checkpoints

A **checkpoint** is a periodic operation that:
1. Flushes all dirty pages from the buffer to disk.
2. Writes a checkpoint record to the log.

**Purpose:** Limits the amount of log that must be scanned during recovery. Recovery only needs to start from the last checkpoint.

### 2.11.4 Types of Recovery

| Type | Description |
|---|---|
| **Transaction rollback** | A single transaction is aborted (due to error or deadlock). UNDO its changes using the log. |
| **Crash recovery** | System restart after a crash. REDO committed, UNDO uncommitted. |
| **Media recovery** | Disk failure. Restore from a backup and apply archived log records to bring the database up to date. |

---

## 2.12 Advanced SQL Concepts

### 2.12.1 Common Table Expressions (CTEs)

A CTE provides a temporary named result set within a single query. Improves readability for complex queries.

```sql
WITH DeptStats AS (
    SELECT Dept_ID, AVG(Salary) AS Avg_Sal, COUNT(*) AS Emp_Count
    FROM EMPLOYEE
    GROUP BY Dept_ID
)
SELECT D.Dept_Name, DS.Avg_Sal, DS.Emp_Count
FROM DeptStats DS
JOIN DEPARTMENT D ON DS.Dept_ID = D.Dept_ID
WHERE DS.Emp_Count > 10;
```

**Recursive CTE — Finding hierarchies (e.g. org chart):**

```sql
WITH RECURSIVE OrgChart AS (
    -- Base case: top-level manager (no manager)
    SELECT Emp_ID, First_Name, Manager_ID, 1 AS Level
    FROM EMPLOYEE
    WHERE Manager_ID IS NULL

    UNION ALL

    -- Recursive case: employees who report to someone in OrgChart
    SELECT E.Emp_ID, E.First_Name, E.Manager_ID, OC.Level + 1
    FROM EMPLOYEE E
    JOIN OrgChart OC ON E.Manager_ID = OC.Emp_ID
)
SELECT * FROM OrgChart ORDER BY Level, First_Name;
```

### 2.12.2 Window Functions

Window functions perform a calculation across a set of rows related to the current row, without collapsing the result (unlike GROUP BY).

```sql
-- Rank employees by salary within each department
SELECT Emp_ID, First_Name, Dept_ID, Salary,
       RANK() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS Salary_Rank,
       DENSE_RANK() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS Dense_Rank,
       ROW_NUMBER() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS Row_Num
FROM EMPLOYEE;

-- Running total of salary
SELECT Emp_ID, First_Name, Salary,
       SUM(Salary) OVER (ORDER BY Emp_ID) AS Running_Total
FROM EMPLOYEE;

-- Compare each employee's salary to the department average
SELECT Emp_ID, First_Name, Dept_ID, Salary,
       AVG(Salary) OVER (PARTITION BY Dept_ID) AS Dept_Avg,
       Salary - AVG(Salary) OVER (PARTITION BY Dept_ID) AS Diff_From_Avg
FROM EMPLOYEE;
```

**Common window functions:**

| Function | Description |
|---|---|
| `ROW_NUMBER()` | Sequential number for each row in the partition. |
| `RANK()` | Rank with gaps (tied values get same rank; next rank skips). |
| `DENSE_RANK()` | Rank without gaps. |
| `NTILE(n)` | Divides rows into n roughly equal groups. |
| `LAG(col, n)` | Value of col from n rows before the current row. |
| `LEAD(col, n)` | Value of col from n rows after the current row. |
| `SUM/AVG/COUNT OVER()` | Running or partitioned aggregates. |

### 2.12.3 SET Operations

```sql
-- UNION (removes duplicates)
SELECT City FROM CUSTOMER
UNION
SELECT City FROM SUPPLIER;

-- UNION ALL (keeps duplicates — faster)
SELECT City FROM CUSTOMER
UNION ALL
SELECT City FROM SUPPLIER;

-- INTERSECT (rows in both)
SELECT City FROM CUSTOMER
INTERSECT
SELECT City FROM SUPPLIER;

-- EXCEPT / MINUS (in first but not second)
SELECT City FROM CUSTOMER
EXCEPT
SELECT City FROM SUPPLIER;
```

**Requirement:** Both queries must have the same number of columns with compatible data types.

### 2.12.4 CASE Expressions

```sql
SELECT Emp_ID, First_Name, Salary,
    CASE
        WHEN Salary >= 100000 THEN 'Senior'
        WHEN Salary >= 60000  THEN 'Mid-Level'
        ELSE 'Junior'
    END AS Band
FROM EMPLOYEE;
```

### 2.12.5 GRANT and REVOKE (DCL)

```sql
-- Grant specific privileges
GRANT SELECT, INSERT ON EMPLOYEE TO user_report;
GRANT ALL PRIVILEGES ON DEPARTMENT TO admin_user;

-- Grant with ability to pass on
GRANT SELECT ON EMPLOYEE TO manager_user WITH GRANT OPTION;

-- Revoke privileges
REVOKE INSERT ON EMPLOYEE FROM user_report;
REVOKE ALL PRIVILEGES ON DEPARTMENT FROM admin_user;
```

**Privilege types:** SELECT, INSERT, UPDATE, DELETE, REFERENCES, TRIGGER, EXECUTE, USAGE.

---
---

# SESSION 3 — Distributed Database Foundations

*References: T1 Ch.23 (Sec 23.1, 23.2, 23.7)*

---

## 3.1 Need for Distributed Databases

### 3.1.1 Why Distribute?

As organisations grow, their computing needs inevitably outgrow what a single database server can provide. A multinational bank with branches in London, Mumbai, New York, and Tokyo cannot efficiently serve all users from a single server in one city — network latency would make transactions painfully slow for distant users. A social media platform with 2 billion users cannot store all data on one machine — no server has enough storage, memory, or CPU power.

**Distributed databases** address these challenges by spreading data across multiple computers (called **nodes** or **sites**) connected by a network. Each node stores a portion of the data and can process queries locally. The nodes cooperate to give users the illusion of a single, unified database.

The key drivers for distribution are:

| Driver | Explanation |
|---|---|
| **Organisational distribution** | Companies have offices in multiple locations, each generating and consuming local data. |
| **Improved performance** | Data placed close to where it is used reduces network latency. |
| **Scalability** | A single server has CPU, memory, and I/O limits. Adding more nodes increases capacity (horizontal scaling). |
| **Availability & reliability** | If one node fails, others continue serving requests; replicated data survives hardware failures. |
| **Autonomy** | Local sites can manage their own data while still participating in a global system. |

### 3.1.2 Centralised vs. Distributed Systems

| Aspect | Centralised | Distributed |
|---|---|---|
| **Data location** | Single site | Multiple sites connected by a network |
| **Single point of failure** | Yes — if the server goes down, everything stops | No — other nodes can continue |
| **Scalability** | Vertical (bigger machine) | Horizontal (more machines) |
| **Latency for remote users** | High | Low (data near the user) |
| **Complexity** | Simpler to manage | More complex (concurrency, partitioning, replication, failure handling) |
| **Cost** | Expensive high-end hardware | Commodity hardware, but more operational overhead |

---

## 3.2 Distributed Database Architecture

### 3.2.1 Definition

A **Distributed Database System (DDBS)** is a collection of multiple, logically interrelated databases distributed over a computer network, managed by a **Distributed DBMS (DDBMS)** that makes the distribution transparent to users.

### 3.2.2 Transparency Goals

| Transparency | User doesn't need to know… |
|---|---|
| **Location** | …where data is physically stored. |
| **Fragmentation** | …that a table is split into fragments across sites. |
| **Replication** | …that multiple copies of data exist. |
| **Concurrency** | …that other transactions are running at the same time. |
| **Failure** | …that a node or network link has failed (system handles it). |

### 3.2.3 Architecture Types

**1. Shared-Nothing Architecture**
- Each node has its own CPU, memory, and disk.
- Nodes communicate only via the network.
- Most common in modern distributed systems (e.g. Cassandra, CockroachDB).
- Scales well; a node failure affects only its local data.

**2. Shared-Disk Architecture**
- All nodes access a common storage layer (SAN/NAS).
- Easier data sharing, but the shared storage can become a bottleneck.
- Example: Oracle RAC.

**3. Shared-Memory (Shared-Everything) Architecture**
- All processors share the same memory and disk.
- Limited scalability; typical of SMP (symmetric multiprocessing) machines.

```
Shared-Nothing            Shared-Disk             Shared-Memory
┌─────┐  ┌─────┐        ┌─────┐  ┌─────┐        ┌─────────────┐
│CPU+M│  │CPU+M│        │ CPU │  │ CPU │        │CPU CPU CPU  │
│Disk │  │Disk │        └──┬──┘  └──┬──┘        │  Shared Mem │
└──┬──┘  └──┬──┘           │        │            │  Shared Disk│
   │  Network  │        ┌──┴────────┴──┐        └─────────────┘
   └─────┬─────┘        │ Shared Disk  │
         │              └──────────────┘
```

### 3.2.4 Homogeneous vs. Heterogeneous DDBS

| Type | Characteristics |
|---|---|
| **Homogeneous** | Same DBMS software at all sites; same data model and query language. Easier to manage. |
| **Heterogeneous** | Different DBMSs at different sites (e.g. Oracle + PostgreSQL + MySQL). Requires middleware or translation layers. |

---

## 3.3 Data Partitioning (Fragmentation)

Partitioning (also called fragmentation) splits a relation into smaller pieces distributed across sites.

### 3.3.1 Types of Fragmentation

#### Horizontal Fragmentation

Splits a table by **rows** — each fragment contains a subset of tuples selected by a condition.

```
EMPLOYEE table
┌──────┬──────────┬────────┐
│Emp_ID│ Name     │ Region │
├──────┼──────────┼────────┤
│  1   │ Alice    │ London │
│  2   │ Bob      │ Mumbai │
│  3   │ Carol    │ London │
│  4   │ Dave     │ Mumbai │
└──────┴──────────┴────────┘

Fragment 1 (London site): σ_{Region='London'}(EMPLOYEE) → rows 1, 3
Fragment 2 (Mumbai site): σ_{Region='Mumbai'}(EMPLOYEE) → rows 2, 4
```

**Requirements for correctness:**
- **Completeness:** Every tuple appears in at least one fragment.
- **Reconstruction:** The original table can be rebuilt by UNION of all fragments.
- **Disjointness:** (preferred) No tuple appears in more than one fragment.

#### Vertical Fragmentation

Splits a table by **columns** — each fragment contains a subset of attributes (always including the PK for reconstruction).

```
Fragment 1 (HR site):       Emp_ID, Name, Hire_Date
Fragment 2 (Payroll site):  Emp_ID, Salary, Bank_Account
```

Reconstruction: Natural JOIN on Emp_ID.

#### Hybrid (Mixed) Fragmentation

Applies both horizontal and vertical fragmentation. First split by columns, then by rows (or vice versa).

### 3.3.2 Partitioning Strategies in Practice

| Strategy | How it works | Pros | Cons |
|---|---|---|---|
| **Range partitioning** | Rows assigned based on a key range (e.g. date ranges). | Efficient range queries; simple. | Uneven data distribution (hot spots). |
| **Hash partitioning** | A hash function on the partition key determines the node. | Even distribution; good for point lookups. | Range queries require scanning all partitions. |
| **List partitioning** | Rows assigned based on explicit value lists (e.g. country codes). | Easy to understand; good for categorical data. | Imbalanced if some categories are much larger. |
| **Consistent hashing** | Hash ring; adding/removing nodes moves minimal data. | Elastic scaling; minimal data movement. | More complex implementation; potential for slight imbalance. |

---

## 3.4 Replication Strategies

Replication is the practice of maintaining **multiple copies** (replicas) of the same data at different sites. While partitioning (fragmentation) splits data so each piece lives at one site, replication **duplicates** data so the same piece lives at multiple sites.

Replication and partitioning are complementary strategies, and most real-world distributed systems use both. For example, a table might be horizontally partitioned by region (European customers at the EU site, Asian customers at the APAC site), and each partition might be replicated to 3 nodes within its region for fault tolerance.

### 3.4.1 Why Replicate?

The motivations for replication are compelling:

- **Availability:** If the only copy of data is on a single node and that node crashes, the data is unavailable until the node recovers. With 3 replicas on 3 different nodes, the data remains available even if 2 nodes crash simultaneously. This is the foundation of fault-tolerant systems.
- **Read performance:** If 1,000 users all want to read the same data, a single node becomes a bottleneck. With replicas, read requests can be distributed across multiple nodes — each node serves a fraction of the readers. This is called **read scaling**.
- **Latency reduction:** By placing replicas close to users geographically (one in Europe, one in Asia, one in the Americas), each user reads from the nearest replica, dramatically reducing network round-trip time.
- **Fault tolerance:** Even if an entire data centre goes offline (fire, natural disaster, network failure), replicas in other data centres ensure data survival.

### 3.4.2 Replication Approaches

| Approach | Description | Trade-off |
|---|---|---|
| **Full replication** | Every site has a complete copy of the entire database. | Maximum availability and read performance; very expensive for writes (must update all copies). |
| **Partial replication** | Some relations (or fragments) are replicated at selected sites. | Balances availability and write cost. |
| **No replication** | Each fragment stored at exactly one site. | Simplest writes; a node failure means that data is unavailable. |

### 3.4.3 Synchronous vs. Asynchronous Replication

| Synchronous (Eager) | Asynchronous (Lazy) |
|---|---|
| Write must be confirmed at all replicas before commit. | Write committed at the primary; replicas updated later. |
| **Strong consistency** — all replicas always agree. | **Eventual consistency** — replicas may temporarily diverge. |
| Higher write latency (wait for slowest replica). | Lower write latency. |
| If any replica is down, writes may block. | Writes succeed even if replicas are down; risk of data loss if primary fails before propagation. |

### 3.4.4 Replication Topologies

- **Single-leader (primary–replica):** One node accepts writes; replicas serve reads. Simple, but leader is a bottleneck/SPOF.
- **Multi-leader:** Multiple nodes accept writes; conflict resolution needed. Good for multi-region deployments.
- **Leaderless (peer-to-peer):** Any node accepts reads and writes; quorum-based reads/writes ensure consistency. E.g. Cassandra, DynamoDB.

### 3.4.5 Conflict Resolution (Multi-leader / Leaderless)

When two nodes independently update the same data:

| Strategy | How it works |
|---|---|
| **Last-writer-wins (LWW)** | Timestamp or version — latest write kept, earlier discarded. Simple but can lose data. |
| **Merge / CRDT** | Data structures designed so concurrent updates can be automatically merged without conflict. |
| **Application-level resolution** | App logic decides how to reconcile (e.g. show both versions to user). |

---

## 3.5 Distributed Concurrency Control

### 3.5.1 The Challenge

In a centralised system, one lock manager handles all concurrency. In a distributed system, locks may need to be acquired at multiple sites, and deadlocks can span multiple nodes.

### 3.5.2 Approaches

| Approach | How it works |
|---|---|
| **Centralised locking** | A single designated node manages all locks globally. Simple but creates a bottleneck and SPOF. |
| **Primary copy locking** | For each data item, one replica is designated the "primary." Locks are requested from the primary's node. Other replicas are updated after the lock is granted. |
| **Distributed locking** | Lock requests go to the node that stores the data item. A transaction may hold locks at multiple nodes. Requires distributed deadlock detection. |

### 3.5.3 Distributed Deadlock Detection

- **Centralised detector:** One node collects wait-for information from all sites and builds a global wait-for graph. Simple but single point of failure.
- **Distributed detector:** Each site maintains a local wait-for graph. Periodically share edges with other sites to detect global cycles.
- **Timeout-based:** If a transaction waits too long, assume deadlock and abort. Simple but may abort non-deadlocked transactions (false positives).

---

## 3.6 Distributed Catalog Management

The catalog (metadata) in a distributed system must track not only schema information but also:

- Which sites store which fragments
- Replication locations
- Fragmentation definitions (predicates for horizontal, attribute lists for vertical)

**Catalog placement strategies:**

| Strategy | Description | Trade-off |
|---|---|---|
| **Centralised catalog** | One site holds all metadata. | Simple; bottleneck and SPOF. |
| **Fully replicated catalog** | Every site has a complete copy. | Fast reads; expensive to update (must propagate changes). |
| **Partitioned catalog** | Each site stores metadata only for its local data. | No redundancy; requires remote calls for non-local queries. |

---

## 3.7 Design Considerations for Distributed Databases

When designing a distributed database, key decisions include:

### 3.7.1 Data Placement Strategy

**Questions to answer:**
1. **What to fragment?** — Which tables benefit from being split?
2. **How to fragment?** — Horizontal, vertical, or hybrid?
3. **Where to place fragments?** — Which site stores which fragment?
4. **What to replicate?** — Which fragments need copies for availability/performance?

### 3.7.2 Design Goals

- **Locality of reference:** Place data close to the applications that use it most.
- **Load balancing:** Distribute data evenly to avoid hotspots.
- **Minimise network traffic:** Reduce the amount of data transferred between sites for common queries.
- **Availability requirements:** Replicate critical data; accept that replication increases write cost.

### 3.7.3 Data Allocation — Tradeoffs

```
                   Read-heavy workload          Write-heavy workload
                   ──────────────────           ────────────────────
Full replication   Best (read from local)       Worst (write to all sites)
No replication     Worst (remote reads)         Best (write to one site)
Partial rep.       Balanced                     Balanced
```

---
---

# SESSION 4 — Distributed Transactions and Consistency Models

*References: T1 Ch.23 (Sec 23.4, 23.5) | Ch.24 (Sec 24.1)*

---

## 4.1 Distributed Transactions

A **distributed transaction** spans multiple nodes — parts of the transaction execute at different sites. The fundamental challenge: ensuring atomicity when multiple independent nodes must agree on commit or abort.

### 4.1.1 Two-Phase Commit (2PC)

The most common protocol for atomic distributed commits.

**Roles:**
- **Coordinator:** The node that initiates and manages the protocol.
- **Participants:** All other nodes involved in the transaction.

**Phase 1 — Voting (Prepare)**

```
Coordinator                     Participants
    │                               │
    │──── PREPARE ─────────────────→│  (Can you commit?)
    │                               │
    │←─── VOTE YES / VOTE NO ──────│  (Each participant votes)
    │                               │
```

- Each participant checks if it can commit its local part.
- If yes → writes changes to durable log, votes YES.
- If no → votes NO (aborts locally).

**Phase 2 — Decision (Commit/Abort)**

```
Coordinator                     Participants
    │                               │
    │  (If ALL votes = YES)         │
    │──── GLOBAL COMMIT ───────────→│
    │                               │
    │  (If ANY vote = NO)           │
    │──── GLOBAL ABORT ────────────→│
    │                               │
    │←─── ACK ─────────────────────│
    │                               │
```

- If all vote YES → coordinator sends GLOBAL COMMIT → participants make changes permanent.
- If any votes NO → coordinator sends GLOBAL ABORT → all participants roll back.

**Problem with 2PC — Blocking:**
If the coordinator crashes after Phase 1 (after participants voted YES but before sending the decision), participants are **blocked** — they cannot commit or abort until the coordinator recovers. They hold locks and wait.

### 4.1.2 Three-Phase Commit (3PC)

Adds an extra phase to reduce blocking:

1. **CanCommit?** — Coordinator asks if participants can commit.
2. **PreCommit** — If all agree, coordinator sends pre-commit (participants prepare but don't commit yet).
3. **DoCommit** — Coordinator sends final commit.

The extra phase ensures that if the coordinator crashes, participants can determine the outcome based on whether they received a pre-commit message.

**Trade-off:** 3PC reduces blocking but adds latency (extra round-trip) and does not fully handle network partitions.

---

## 4.2 Distributed Query Processing

### 4.2.1 The Challenge

In a distributed system, a single query may need data from multiple sites. The optimizer must decide:

- **Which fragments** contain relevant data?
- **Where** to execute each part of the query?
- **How** to transfer intermediate results between sites?

### 4.2.2 Query Processing Steps

```
SQL Query
    │
    ▼
┌──────────────────┐
│  Query Parsing    │  → Check syntax, validate tables/columns
└────────┬─────────┘
         ▼
┌──────────────────┐
│ Query Optimization│  → Consider data locations, network cost,
│ (Global + Local)  │     local processing cost
└────────┬─────────┘
         ▼
┌──────────────────┐
│ Distributed       │  → Execute sub-queries at relevant sites;
│ Execution         │     transfer intermediate results
└────────┬─────────┘
         ▼
┌──────────────────┐
│ Result Assembly   │  → Combine partial results at the query site
└──────────────────┘
```

### 4.2.3 Strategies for Distributed Joins

| Strategy | Description | When to use |
|---|---|---|
| **Ship-whole** | Send an entire table/fragment to the other site. | One table is small. |
| **Semi-join** | Send only the joining column values first to filter the remote table, then fetch matching rows. Reduces data transfer. | Tables are large; join selectivity is high. |
| **Bloom-filter join** | Send a compact Bloom filter of joining keys; remote site uses it to pre-filter. | Very large tables; approximate filtering acceptable. |
| **Parallel execution** | Break query into independent sub-queries executed simultaneously at different sites. | Multiple partitions can be scanned in parallel. |

### 4.2.4 Cost Factors in Distributed Queries

| Factor | Weight |
|---|---|
| **Network transfer cost** | Often dominant — moving data between nodes is expensive (latency + bandwidth). |
| **Local I/O cost** | Disk reads/writes at each site. |
| **Local CPU cost** | Processing at each site. |

A good distributed query optimizer minimises **total data transfer** while balancing local processing costs.

---

## 4.3 The CAP Theorem

### 4.3.1 Statement

The CAP Theorem is one of the most important theoretical results in distributed systems. It was proposed as a conjecture by **Eric Brewer** at the ACM PODC conference in 2000 and formally proved by **Seth Gilbert and Nancy Lynch** in 2002.

The theorem states: **In a distributed data store, it is impossible to simultaneously provide all three of the following guarantees. You can achieve at most two.**

The three properties are:

| Property | Definition | What it means in practice |
|---|---|---|
| **Consistency (C)** | Every read receives the most recent write (or an error). All nodes see the same data at the same time. This is **linearisability** — the strictest form of consistency. | If you write "balance = 500" and immediately read, you always see 500, no matter which node you read from. There is no "stale data" window. |
| **Availability (A)** | Every request (read or write) receives a non-error response, without guarantee that it contains the most recent write. The system is always responsive — no timeouts, no errors. | The system never refuses to answer. Every node that receives a request will return a result, even if it might not be the latest. No "service unavailable" responses. |
| **Partition tolerance (P)** | The system continues to operate despite arbitrary message loss or failure of part of the network. Nodes can't communicate with each other but still serve requests. | If the network cable between the London and Mumbai data centres is cut, both centres continue operating independently rather than shutting down. |

### 4.3.2 Why "pick 2 of 3" is misleading

The way CAP is often taught — "pick any 2" — is misleading because it suggests the three choices (CA, CP, AP) are equally valid. In reality, **network partitions are not optional**. In any real distributed system running on commodity hardware across networks, partitions **will** happen — switches fail, cables are cut, cloud regions disconnect, packets get dropped.

Since P is a fact of life, the real choice is: **When a partition occurs, which do you sacrifice — Consistency or Availability?**

Think of it this way: during a partition, the nodes can't communicate. A write arrives at Node A but can't be propagated to Node B because the network link is down. Now a read arrives at Node B. You have two options:

1. **Sacrifice Availability (CP):** Node B refuses to answer (returns an error or blocks) because it can't guarantee it has the latest data. The user gets an error, but they're never shown stale data.

2. **Sacrifice Consistency (AP):** Node B answers with whatever data it has, which might be stale. The user gets a response (system is available), but the response might be outdated.

Neither choice is universally "better" — it depends on your application. A banking system should probably choose CP (better to show an error than a wrong balance). A social media feed can choose AP (showing a slightly stale feed is fine; showing an error page is not).

| Choice | Behaviour during partition | Examples |
|---|---|---|
| **CP** (Consistency + Partition tolerance) | System returns an error or times out rather than serve stale data. Some requests fail, but no incorrect answers are given. | HBase, MongoDB (default config), Zookeeper, etcd |
| **AP** (Availability + Partition tolerance) | System returns the best available data, which may be stale. Every request gets a response, but the response might be outdated. | Cassandra, DynamoDB, CouchDB, DNS |
| **CA** (Consistency + Availability) | Only possible if there are no partitions — effectively a single-node system or a system where the network is 100% reliable (which doesn't exist in practice). | Traditional single-server RDBMS (PostgreSQL, MySQL on one server) |

### 4.3.3 CAP in Practice

- Most systems are not purely CP or AP — they allow **tunable consistency** (e.g. Cassandra lets you choose quorum levels per query).
- CAP only describes behaviour **during** a partition. Outside of partitions, a well-designed system can offer both consistency and availability.
- Daniel Abadi extended CAP with the **PACELC** model: if Partition → choose Availability or Consistency; Else (no partition) → choose Latency or Consistency.

---

## 4.4 ACID vs. BASE

### 4.4.1 ACID (Recap)

ACID is the cornerstone of traditional relational database reliability. These four properties together guarantee that transactions are processed correctly and completely, even in the face of system failures, power outages, and concurrent access by multiple users. Without ACID, databases would be unreliable for any serious application involving money, inventory, or critical records.

| Property | Description | Why it matters |
|---|---|---|
| **Atomicity** | All or nothing — either every operation in a transaction completes, or none do. | A bank transfer that debits one account but fails to credit another would lose money. Atomicity prevents this — the entire transfer succeeds or the entire transfer is rolled back. |
| **Consistency** | A transaction takes the database from one valid state to another. All integrity constraints are satisfied before and after. | If a constraint says "account balance ≥ 0", no transaction can violate this, even temporarily from another transaction's perspective. |
| **Isolation** | Concurrent transactions execute as if they were serial (one after another). Intermediate states are invisible to other transactions. | If two people simultaneously transfer money, neither should see the other's half-completed transfer. Each sees a clean, consistent snapshot. |
| **Durability** | Once a transaction commits, its changes survive any subsequent failure — power loss, crashes, disk failure. | After the bank confirms "transfer successful," the money must stay transferred even if the server crashes one millisecond later. |

ACID is the gold standard for traditional relational databases. It provides **strong guarantees** but can limit scalability in distributed settings (coordination overhead for distributed locks and 2PC).

### 4.4.2 BASE

The BASE model is the philosophical opposite of ACID, adopted by many NoSQL and distributed systems that prioritise **availability and scalability** over immediate consistency. The acronym was coined by **Dan Pritchett** of eBay as a deliberate contrast to ACID.

While ACID says "the database is always in a perfectly consistent state," BASE says "the database is always available and will *eventually* become consistent."

| Property | Description | What it means in practice |
|---|---|---|
| **Basically Available** | The system guarantees availability in the CAP sense — it will always return a response, even if some data is temporarily inconsistent or some nodes are down. | Amazon's shopping cart always works. If one data centre is unreachable, the system still accepts orders. Some data might be stale (your friend's latest review might not show up for a few seconds), but the system never goes offline. |
| **Soft state** | The state of the system may change over time even without new input, as data propagates and synchronises between replicas. | After you update your profile picture, different servers might show the old picture for a few seconds while the update propagates. The system state is "soft" — it drifts towards consistency but isn't locked into it. |
| **Eventually consistent** | If no new updates are made to a data item, all replicas will *eventually* converge to the same value. There is no guarantee about *when* — it could be milliseconds or seconds. | If you post a tweet and your friend in another country checks immediately, they might not see it yet. But within a few seconds (usually), all replicas sync up and everyone sees the same data. |

The key insight of BASE is that **perfect consistency at all times is expensive** in a distributed system. Maintaining it requires coordination between nodes (locks, two-phase commit), which adds latency and reduces availability. Many applications don't actually need perfect consistency — they just need "good enough" consistency with high availability.

### 4.4.3 ACID vs. BASE Comparison

| Aspect | ACID | BASE |
|---|---|---|
| **Consistency model** | Strong (immediate) | Eventual |
| **Availability** | May sacrifice availability for consistency | Prioritises availability |
| **Scalability** | Harder to scale horizontally | Designed for horizontal scale |
| **Use cases** | Financial transactions, inventory, bookings | Social media feeds, analytics, caching, IoT |
| **Complexity** | Simpler application logic (DB handles correctness) | Application must handle temporary inconsistency |
| **Performance** | Higher latency for writes (coordination) | Lower write latency |

### 4.4.4 Eventual Consistency — Deeper Look

Eventual consistency is a **spectrum**, not a single model. Variants include:

| Variant | Guarantee |
|---|---|
| **Causal consistency** | If operation A causally precedes B, everyone sees A before B. |
| **Read-your-writes** | After a write, the same client always sees its own update. |
| **Monotonic reads** | Once a client reads a value, it never sees an older value. |
| **Monotonic writes** | Writes by the same client are applied in order. |
| **Session consistency** | Within a session, read-your-writes + monotonic reads. |

Many systems offer **tunable consistency** — for example, Cassandra's consistency levels:

```
ONE         → Read/write acknowledged by 1 replica (fastest, weakest)
QUORUM      → Acknowledged by majority of replicas (balanced)
ALL         → Acknowledged by all replicas (strongest, slowest)
LOCAL_QUORUM → Quorum within the local data centre
```

**Formula for strong consistency in a quorum system:**

```
R + W > N
```

Where R = replicas read, W = replicas written, N = total replicas. If this holds, at least one node in every read set has the latest write.

---

## 4.5 Putting It All Together — A Decision Framework

When choosing between consistency models and architectures:

```
┌─────────────────────────────────────────────────────────────┐
│  Is your data safety-critical (money, health, inventory)?   │
│        YES → Strong consistency (CP / ACID)                 │
│        NO  ↓                                                │
│  Can your app tolerate temporary stale reads?               │
│        YES → Eventual consistency (AP / BASE) — scale out   │
│        NO  → Causal or session consistency (middle ground)  │
└─────────────────────────────────────────────────────────────┘
```

**Real-world systems often mix models:**
- A banking system uses ACID for account balances but eventual consistency for transaction history analytics.
- An e-commerce platform uses strong consistency for inventory counts but eventual consistency for product reviews.

---

## 4.6 Consensus Protocols — Paxos and Raft

### 4.6.1 The Consensus Problem

Consensus protocols solve a fundamental problem: getting multiple nodes to agree on a single value (e.g. who is the leader? what is the committed order of operations?) even when some nodes crash.

### 4.6.2 Paxos

Introduced by **Leslie Lamport (1990)**.

**Roles:**
- **Proposers** — Propose values.
- **Acceptors** — Vote on proposals.
- **Learners** — Learn the agreed-upon value.

**Phases:**
1. **Prepare:** Proposer sends a proposal number n to a majority of acceptors. Each acceptor promises not to accept any proposal with a number less than n.
2. **Accept:** If a majority respond with promises, the proposer sends an accept request with a value. Acceptors accept if they haven't promised a higher number.
3. **Learn:** Once a majority accept, the value is chosen.

**Properties:**
- **Safety:** Only a single value is chosen; a node only learns a value that was actually chosen.
- **Liveness:** Progress is guaranteed if a single proposer is active (in practice, a leader is elected).
- **Fault tolerance:** Tolerates failure of up to (N-1)/2 nodes out of N.

**Drawback:** Paxos is notoriously difficult to understand and implement correctly.

### 4.6.3 Raft

Introduced by **Ongaro & Ousterhout (2014)** as an understandable alternative to Paxos.

**Key idea:** Decompose consensus into three sub-problems:
1. **Leader election** — One node is elected leader; it manages all log replication.
2. **Log replication** — Leader receives client requests, appends them to its log, and replicates to followers. Once a majority acknowledge, the entry is committed.
3. **Safety** — Ensures only nodes with up-to-date logs can become leader.

**States a node can be in:**
- **Leader** — Handles all client requests; sends heartbeats.
- **Follower** — Passive; responds to leader and candidates.
- **Candidate** — Requesting votes for leader election (triggered by timeout).

```
Follower ──(election timeout)──→ Candidate ──(majority votes)──→ Leader
    ↑                                │                              │
    └────────(higher term seen)──────┘          (heartbeats)────────┘
```

**Raft is used in:** etcd, CockroachDB, TiKV, Consul, InfluxDB.

### 4.6.4 Paxos vs. Raft vs. 2PC

| Aspect | 2PC | Paxos | Raft |
|---|---|---|---|
| **Purpose** | Atomic commit (all-or-nothing) | Consensus on a value/log entry | Consensus (leader-based) |
| **Blocking** | Yes (coordinator failure) | No | No |
| **Partition tolerance** | No | Yes (majority quorum) | Yes (majority quorum) |
| **Fault tolerance** | Coordinator is SPOF | Tolerates < N/2 failures | Tolerates < N/2 failures |
| **Complexity** | Simple | Very complex | Moderate (designed to be understandable) |
| **Use in practice** | Traditional distributed DBs | Google Spanner (Multi-Paxos) | etcd, CockroachDB |

---

## 4.7 Distributed Recovery

### 4.7.1 Recovery in a Distributed Context

Recovery in a distributed system must handle:
- **Node crashes** — A node restarts and must recover its local state.
- **Network partitions** — Communication between nodes is lost temporarily.
- **Coordinator failures** — Especially in 2PC, where the coordinator holds the commit/abort decision.

### 4.7.2 Local Recovery (Per Node)

Each node runs its own WAL-based recovery (UNDO/REDO) independently — same as centralised recovery.

### 4.7.3 Handling 2PC Coordinator Failure

If a participant has voted YES but the coordinator crashed before sending the decision:
1. **Log-based recovery:** When the coordinator restarts, it reads its log to determine whether it had decided to commit or abort, and re-sends the decision.
2. **Participant timeout:** Participants can ask other participants what they voted. If any voted NO, they all abort. If all voted YES but no decision was received, they must wait (this is the blocking problem).
3. **Cooperative termination protocol:** Participants communicate with each other to determine the outcome without the coordinator.

---

## 4.8 NewSQL — Best of Both Worlds

**NewSQL** databases aim to provide the scalability of NoSQL with the ACID guarantees of traditional relational databases.

### 4.8.1 Characteristics

| Feature | Detail |
|---|---|
| **SQL interface** | Full SQL support (not just a subset). |
| **ACID transactions** | Including distributed transactions across nodes. |
| **Horizontal scalability** | Shared-nothing architecture; automatic sharding. |
| **High availability** | Replication with automatic failover. |

### 4.8.2 How They Achieve This

- **Distributed consensus (Raft/Paxos)** for leader election and log replication — replaces 2PC for many operations.
- **MVCC + distributed timestamps** (e.g. Google Spanner uses TrueTime, CockroachDB uses Hybrid Logical Clocks).
- **Automatic sharding and rebalancing** — data is partitioned across nodes and automatically rebalanced as nodes are added/removed.

### 4.8.3 Notable NewSQL Systems

| System | Key Feature |
|---|---|
| **Google Spanner** | Globally distributed; uses GPS and atomic clocks (TrueTime) for externally consistent timestamps. |
| **CockroachDB** | Open-source; Raft-based; PostgreSQL-compatible wire protocol. |
| **YugabyteDB** | Open-source; Raft-based; supports both SQL (PostgreSQL) and Cassandra APIs. |
| **TiDB** | Open-source; MySQL-compatible; uses TiKV (Raft-based) for storage. |
| **VoltDB** | In-memory; deterministic execution for extreme throughput. |

---

## 4.9 Data Warehousing Concepts (Brief Overview)

*Reference: R1 — Ponniah, Ch.1–2*

### 4.9.1 OLTP vs. OLAP

| Aspect | OLTP (Online Transaction Processing) | OLAP (Online Analytical Processing) |
|---|---|---|
| **Purpose** | Day-to-day operations | Business analysis and reporting |
| **Queries** | Short, simple, frequent (INSERT, UPDATE) | Complex, long-running (aggregations, joins) |
| **Data** | Current, detailed | Historical, summarised |
| **Schema** | Normalised (3NF) | Denormalised (star/snowflake schema) |
| **Users** | Clerks, app users | Analysts, managers |
| **Example** | Process a bank transaction | Quarterly sales report by region |

### 4.9.2 Data Warehouse Architecture

```
Data Sources → ETL (Extract, Transform, Load) → Data Warehouse → OLAP / BI Tools → Reports & Dashboards
```

- **ETL:** Extract data from operational systems, transform it (clean, deduplicate, aggregate), load into the warehouse.
- **Data Warehouse:** Central repository of integrated, historical data optimised for queries.

### 4.9.3 Star Schema vs. Snowflake Schema

**Star Schema:**
```
              ┌──────────┐
              │ DIM_TIME  │
              └─────┬────┘
                    │
┌──────────┐  ┌────┴─────┐  ┌──────────┐
│DIM_PRODUCT├──┤FACT_SALES├──┤DIM_STORE │
└──────────┘  └────┬─────┘  └──────────┘
                    │
              ┌─────┴────┐
              │DIM_CUST  │
              └──────────┘
```

- **Fact table** (centre): Contains measures (sales_amount, quantity) and foreign keys to dimension tables.
- **Dimension tables** (points of the star): Describe the who/what/when/where (product, time, customer, store).
- Denormalised dimensions — simple queries, fast joins.

**Snowflake Schema:**
- Dimension tables are normalised (e.g. DIM_PRODUCT references DIM_CATEGORY).
- Saves space but requires more joins.

### 4.9.4 ETL vs. ELT

| Approach | Where transformation happens | When to use |
|---|---|---|
| **ETL** | In a staging area before loading into the warehouse. | Traditional on-premises data warehouses. |
| **ELT** | Inside the warehouse after loading raw data. | Cloud data warehouses (BigQuery, Snowflake, Redshift) where compute is elastic. |

---

## Summary of All Sessions (Updated)

| Session | Core Concepts |
|---|---|
| **1 — Foundations** | File systems vs. DBMS, three-schema architecture, relational model, keys, constraints, ER modelling, ER-to-relational mapping, **normalisation (1NF–BCNF)**, **DBMS internal architecture**, **indexing (B+ tree, hash, bitmap)**, **views, stored procedures, triggers**. |
| **2 — SQL & Transactions** | DDL, DML, SELECT queries, joins, subqueries, ACID properties, transaction states, schema evolution, denormalisation, motivation for NoSQL, **concurrency control (locks, 2PL, MVCC, deadlocks)**, **isolation levels**, **database recovery (WAL, UNDO/REDO, checkpoints)**, **advanced SQL (CTEs, window functions, CASE, GRANT/REVOKE)**. |
| **3 — Distributed DB Foundations** | Centralised vs. distributed, DDBS architecture, transparency, fragmentation (horizontal/vertical), partitioning strategies, replication (sync/async, topologies, conflict resolution), **distributed concurrency control**, **distributed catalog management**, **data placement design**. |
| **4 — Distributed Transactions & Consistency** | 2PC/3PC, distributed query processing, CAP theorem, ACID vs. BASE, eventual consistency variants, tunable consistency, decision framework, **Paxos/Raft consensus**, **distributed recovery**, **NewSQL**, **OLTP vs. OLAP, data warehousing, star/snowflake schemas**. |

---

*End of Study Content*
