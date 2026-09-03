# Session 1: Foundations of Structured Data Management

> BITS Pilani — **SSZG507: Modern Database Systems** — Prof. Ashish Narang, CSIS
>
> **References:** T1 Ch.1 (Sec 1.2, 1.3, 1.6) | Ch.3 (Sec 3.3, 3.4, 3.6) | Ch.5 (Sec 5.1, 5.2) | Ch.6
>
> **Textbooks:**
> - T1: Elmasri & Navathe, *Fundamentals of Database Systems*, 7th Ed., Pearson, 2017.
> - T2: Martin Kleppmann, *Designing Data-Intensive Applications*, 1st Ed., O'Reilly, 2017.
> - R1: Paulraj Ponniah, *Data Warehousing Fundamentals for IT Professionals*, 2nd Ed., Wiley, 2010.
>
> **Course Modules (Full Course):**
> 1. Foundations of Structured Data Management ← *this session*
> 2. Distributed Database Architecture and Consistency Models
> 3. NoSQL Data Stores
> 4. Cloud Databases and NewSQL Systems
> 5. Data Warehouses, Data Lakes and Data Lakehouse
> 6. Specialized Databases for Modern Applications
> 7. Emerging Trends in Data Systems

---

## Table of Contents

- [1.1 Introduction to Data Management Systems](#11-introduction-to-data-management-systems)
- [1.2 Types of Data and Their Characteristics](#12-types-of-data-and-their-characteristics)
- [1.3 Evolution: File Systems to DBMS](#13-evolution-file-systems-to-dbms)
- [1.4 Structured Data Management Concepts](#14-structured-data-management-concepts)
- [1.5 Relational Database Fundamentals](#15-relational-database-fundamentals)
- [1.6 Data Modelling: ER Model](#16-data-modelling-er-model)
- [1.7 COMPANY Database — Worked ER Exercise](#17-company-database--worked-er-exercise)
- [1.8 Normalisation (1NF to BCNF)](#18-normalisation-1nf-to-bcnf)
- [1.9 DBMS Internal Architecture](#19-dbms-internal-architecture)
- [1.10 Indexing and File Structures](#110-indexing-and-file-structures)
- [1.11 Views in SQL](#111-views-in-sql)
- [1.12 Stored Procedures, Functions, and Triggers](#112-stored-procedures-functions-and-triggers)

---

## 1.1 Introduction to Data Management Systems

### What is Data? What is Information?

Before discussing data management, it's essential to understand the difference between **data** and **information**. **Data** refers to raw, unprocessed facts — numbers, text, dates — without context. The number `42000` by itself is just data. **Information** is data that has been processed, organised, and given context so that it becomes meaningful — for example, "Employee John's monthly salary is 42,000 INR." A database stores data; applications and queries transform it into information.

### What is a Database?

A **database** is an organized collection of logically related data, designed to efficiently store, retrieve, update, and manage information for a specific application or organization. It is not merely a file or a spreadsheet — it is a structured repository governed by rules, accessible by many users, and protected against failure.

**Examples:** University Database, Banking Database, Hospital Database, E-commerce Database.

### What is a DBMS?

A **Database Management System (DBMS)** is software that enables users to create, store, organize, manage, and retrieve data efficiently from a database. It sits between applications and the stored data.

A DBMS facilitates:
- **Defining** the database — specify the structure, tables, relationships, and constraints.
- **Constructing** the database — create database objects and populate them with data.
- **Manipulating** the database — insert, update, delete, and retrieve data. Support multiple users and applications simultaneously.

**Examples:** MySQL, PostgreSQL, MongoDB, Oracle, SQL Server, CockroachDB.

### Why Do We Need Formal Data Management?

In any organisation — a university, a bank, a hospital, an e-commerce company — data is generated continuously: student enrolments, financial transactions, patient records, customer orders. Without a systematic way to manage this data, organisations face chaos.

Consider a university with 10,000 students. If each department maintains its own Excel files for student records, you'll quickly encounter duplicate entries, contradictory information (one file says a student is in 3rd year, another says 4th year), and no way to answer cross-departmental questions like "How many students are enrolled in both Computer Science and Mathematics courses?"

Formal data management addresses five fundamental concerns:

| Concern | Explanation |
|---|---|
| **Data sharing** | Multiple users and applications need concurrent access to the same data. A bank's ATM system, mobile app, and branch counter all read and write the same account balances simultaneously. Without coordination, these concurrent accesses can corrupt data. |
| **Integrity** | Rules (constraints) must keep data correct at all times. A student's age cannot be negative. An account balance cannot drop below the overdraft limit. These business rules must be enforced centrally, not scattered across individual applications. |
| **Efficiency** | As data grows, brute-force searching becomes impractical. Searching 100 million records one by one would take minutes. With a proper index (like a B+ tree), the same search takes milliseconds. |
| **Security** | Only authorised users should read or modify specific data. A payroll clerk should see salary data but not medical records. A doctor should see medical records but not salary data. |
| **Recovery** | After a crash, data must be restored to a consistent state. If a power failure occurs during a bank transfer (money debited from one account but not credited to the other), the system must undo the partial operation. |

---

## 1.2 Types of Data and Their Characteristics

*(From class slides 15-19)*

Data can be classified based on how it is organised and represented. Different types require different storage and management techniques. Understanding these types is critical because modern database systems must handle all three.

### Structured Data

Structured data is organised according to a **predefined schema** (fixed format), making it easy to store, retrieve, and process. This is the traditional domain of relational databases.

**Characteristics:**
- Organised into rows and columns (tables).
- Follows a fixed, predefined schema — every row has the same columns.
- Each attribute has a defined data type (INT, VARCHAR, DATE, etc.).
- Easily queried using SQL.
- Ensures consistency and integrity through constraints.

**Examples:** Employee records (Emp_ID, Name, Salary, Dept_ID), bank account transactions, student enrolment data, inventory tables.

**Storage:** Relational databases (MySQL, PostgreSQL, Oracle, SQL Server).

### Semi-Structured Data

Semi-structured data does **not** follow a rigid tabular schema but contains **tags, keys, or metadata** that provide partial structure and organisation. It sits between structured and unstructured.

**Characteristics:**
- Does not follow a fixed schema — different records can have different fields.
- Self-describing using tags or key-value pairs (the structure is embedded in the data).
- Flexible and easily extensible — adding a new field doesn't require altering a schema.
- Suitable for exchanging and integrating data between systems.
- Commonly stored in **JSON, XML, and YAML** formats.

**Examples:** JSON API responses, XML configuration files, email (headers are structured, body is unstructured), web pages (HTML tags provide structure).

**Storage:** Document databases (MongoDB, CouchDB), XML databases.

### Unstructured Data

Unstructured data does **not** follow any predefined schema or organised format, making it difficult to store and analyse using traditional relational databases.

**Characteristics:**
- No predefined schema or fixed structure.
- Contains text, images, audio, video, and documents.
- Difficult to query using traditional SQL.
- Requires specialised storage and processing techniques (full-text search, image recognition, NLP).
- Forms the **majority of data generated today** (estimated 80-90% of all data).

**Examples:** Word documents, PDFs, images and photographs, audio files (podcasts, music), video files, social media posts, sensor data streams.

**Storage:** Object storage (Amazon S3), data lakes, NoSQL databases, specialised systems (Elasticsearch for text, media servers for video).

### Comparison Table

| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Schema** | Fixed, predefined | Flexible, self-describing | None |
| **Format** | Tables (rows/columns) | JSON, XML, YAML | Text, images, audio, video |
| **Query language** | SQL | JSON queries, XPath, XQuery | Full-text search, AI/ML |
| **Storage** | RDBMS | Document DB, XML DB | Object storage, data lakes |
| **Examples** | Employee table, bank transactions | API responses, config files | PDFs, videos, social media |
| **% of global data** | ~10-20% | ~5-10% | ~80-90% |

---

## 1.3 Evolution: File Systems to DBMS

### 1.3.1 The File System Era

In the 1960s and 1970s, each application managed its own flat files. The payroll application had its files, the HR application had separate files, and the inventory application had yet another set. There was no central coordination. This approach had severe problems:

| Problem | What goes wrong |
|---|---|
| **Data redundancy** | The same customer name, address, and phone number stored in multiple files across programs. If a customer moves, every file must be updated independently. |
| **Inconsistency** | One file is updated with a new address, others still have the old one. Now different applications show different addresses for the same customer. Which is correct? |
| **Difficulty in access** | Want a new report? You might need to write an entirely new program. There's no general-purpose query language — every data access path must be programmed individually. |
| **Data isolation** | Data scattered across files in different formats (some binary, some text, some fixed-width, some comma-delimited). Combining data from different files requires custom parsing code. |
| **Integrity problems** | No central mechanism to enforce business rules. If the rule is "account balance must not go below zero," each application must implement and enforce this independently — and they may do it inconsistently. |
| **Atomicity problems** | A crash midway through a multi-step update (debit one account, credit another) leaves the data in a partial, inconsistent state. There's no mechanism to "undo" the partial work. |
| **Concurrent-access anomalies** | Two clerks updating the same file simultaneously. One reads a balance of 1000, the other reads 1000. Both write back their modified values — one update is silently lost. |
| **Security issues** | File-level permissions (read/write/execute for owner/group/others) are far too coarse. You can't say "user X can see columns A and B but not column C." |

### 1.2.2 The DBMS Solution

A **Database Management System (DBMS)** emerged as the solution to all these problems. It is system software that sits between applications and the stored data, providing:

1. **Data definition** — A Data Definition Language (DDL) to specify the schema: what tables exist, what columns they have, what types and constraints apply.
2. **Data manipulation** — A Data Manipulation Language (DML) to insert, update, delete, and query data using a high-level language (SQL), without needing to know how data is physically stored.
3. **Controlled access** — Concurrency control (so multiple users don't corrupt data), backup and recovery (so crashes don't lose data), authorisation (so only permitted users access data), and integrity enforcement (so business rules are always satisfied).

**Key advantages over file systems:**

- **Program–data independence:** The schema is stored separately in a **catalog** (data dictionary). If you change the storage structure (add an index, reorganise files on disk), application programs don't need to be rewritten. They query the logical schema, not the physical storage.
- **Data abstraction:** Users interact with a logical view of the data (tables, rows, columns) without needing to understand how data is physically stored on disk (files, blocks, pages, indexes).
- **Multiple views:** Different users can see different subsets of the same underlying data. The HR department sees employee personal details; the finance department sees salary information; neither sees what the other sees — even though it's all in the same database.
- **Reduced redundancy:** Data is stored once in a centralised database. Normalisation techniques (covered in Section 1.6) systematically eliminate duplicate storage.
- **Transaction support:** The ACID properties (Atomicity, Consistency, Isolation, Durability) guarantee that every transaction either completes fully or has no effect at all — even if the system crashes midway.

### 1.2.3 The Three-Schema Architecture (ANSI/SPARC)

The ANSI/SPARC architecture (proposed in 1975) separates a database system into three levels of abstraction. This is one of the most important concepts in database theory because it enables **data independence** — the ability to change one level without affecting the others.

```
┌─────────────────────────────────┐
│  External Level (User Views)    │  ← Each user/app sees only relevant data
├─────────────────────────────────┤
│  Conceptual Level (Logical)     │  ← Complete logical structure of entire DB
├─────────────────────────────────┤
│  Internal Level (Physical)      │  ← How data is physically stored on disk
└─────────────────────────────────┘
```

**External level:** Individual user perspectives. A student registration system sees student names and course enrolments. A grade reporting system sees student IDs and grades. Both are views of the same underlying data, tailored to each application's needs.

**Conceptual level:** The complete logical description of the entire database — all entities, relationships, constraints, and security rules. It describes **what** data is stored, not **how** it's stored. This is the schema designed by the DBA.

**Internal level:** The physical storage structures — which files store which tables, what indexing methods are used (B+ tree, hash), how data is compressed, what buffer management strategy is employed. This level is optimised for performance.

**Data independence** is achieved through mappings between levels:

- **Logical data independence:** You can change the conceptual schema (e.g. add a new table, split a table) without affecting the external views or applications that use them. The external-to-conceptual mapping absorbs the change.
- **Physical data independence:** You can change the internal schema (e.g. add an index, move data to SSDs, change file organisation) without affecting the conceptual schema. The conceptual-to-internal mapping absorbs the change.

---

## 1.4 Structured Data Management Concepts

### 1.3.1 Data Models

A **data model** is a collection of concepts and rules for describing the structure of a database — what the data looks like, how it's organised, what constraints it must obey, and what operations can be performed. Think of a data model as a "blueprint language" for databases.

| Category | Examples | Purpose | Audience |
|---|---|---|---|
| **Conceptual (high-level)** | ER model, UML class diagrams | Describe data as users perceive it. No implementation details. | Business analysts, end users. |
| **Representational (logical)** | Relational, Network, Hierarchical | Formal structure a DBMS can implement. Understandable by both users and developers. | Database designers, developers. |
| **Physical (low-level)** | Indexes, hashing, file structures | How data is stored on disk. | DBAs, DBMS internals developers. |

The **relational model** dominates today because it combines simplicity (tables are intuitive) with mathematical rigour (relational algebra enables automatic query optimisation).

### 1.3.2 Schema vs. Instance

A **schema** (intension) is the structure of the database — table definitions, column types, constraints. Changes rarely.

An **instance** (extension) is the actual data at a point in time. Changes with every INSERT, UPDATE, DELETE.

**Analogy:** A spreadsheet template with column headers and validation rules is the schema. The rows of data filled in by users are the instance.

### 1.3.3 Database Languages

| Language | Purpose | Examples | When used |
|---|---|---|---|
| **DDL** | Define/modify schema | `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE` | Database design, schema evolution |
| **DML** | Query/update data | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Daily, by apps and users |
| **DCL** | Access control | `GRANT`, `REVOKE` | By DBAs for security |
| **TCL** | Transaction control | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | By apps needing atomic operations |

SQL unifies all four into a single language.

---

## 1.5 Relational Database Fundamentals

### 1.4.1 The Relational Model (Codd, 1970)

Proposed by **Edgar F. Codd** at IBM, the relational model was revolutionary: instead of navigating pointer structures (hierarchical/network models), represent all data as **tables** and use **mathematical logic** to query them.

| Term | Meaning |
|---|---|
| **Relation** | A table with rows and columns. |
| **Tuple** | A single row. |
| **Attribute** | A named column. |
| **Domain** | Set of allowed values for an attribute (e.g. positive integers for Age). |
| **Degree** | Number of attributes (columns). |
| **Cardinality** | Number of tuples (rows). |
| **Relation schema** | Name + attribute list: `STUDENT(ID, Name, Age)`. |

### 1.4.2 Properties of Relations

1. **No duplicate tuples** — every row is distinct.
2. **Tuple ordering is insignificant** — rows have no inherent order.
3. **Attribute ordering is insignificant** — columns are identified by name, not position.
4. **All values are atomic** — no multi-valued or composite attributes in a cell (**First Normal Form**).

### 1.4.3 Keys

| Key Type | Definition | Example (for STUDENT table) |
|---|---|---|
| **Superkey** | Any set of attributes that uniquely identifies a tuple. | {Student_ID}, {Student_ID, Name}, {Email} |
| **Candidate key** | A *minimal* superkey — removing any attribute breaks uniqueness. | {Student_ID}, {Email} |
| **Primary key (PK)** | The candidate key chosen by the designer as the main identifier. | Student_ID |
| **Foreign key (FK)** | Attribute(s) in one relation that reference the PK of another relation. | Dept_ID → DEPARTMENT(Dept_ID) |
| **Alternate key** | A candidate key that was not chosen as the PK. | Email (if Student_ID is PK) |

### 1.4.4 Integrity Constraints

These are rules the DBMS enforces to keep data correct:

- **Entity integrity:** No primary key attribute can be NULL. Every row must be uniquely identifiable.
- **Referential integrity:** A foreign key value must either be NULL or match an existing primary key in the referenced relation. You can't reference a department that doesn't exist.
- **Domain constraints:** Every attribute value must belong to its defined domain (e.g. Age must be a positive integer, not a string).
- **Key constraints:** No two tuples can share the same primary key value.

### Application-Based (Semantic) Constraints

*(From class slide 29)*

Beyond schema-based constraints (domain, key, entity integrity, referential integrity), real-world databases often need **business rules** that cannot be expressed purely through the schema:

- "Salary of an employee should not exceed the salary of the employee's supervisor."
- "Maximum number of hours an employee can work on a project per week is 56 hours."
- "Salary of an employee should only increase (never decrease)."

These **semantic constraints** are enforced through **application programs**, **triggers**, or **stored procedures** rather than through DDL constraints. They require custom logic because they involve cross-row or cross-table conditions that SQL CHECK constraints cannot handle.

### 1.4.5 Relational Algebra

Relational algebra provides the theoretical foundation for SQL. It defines a set of operations that take one or two relations as input and produce a new relation as output.

| Operation | Symbol | Description | SQL Equivalent |
|---|---|---|---|
| **Selection** | σ | Pick rows satisfying a condition | `WHERE` |
| **Projection** | π | Pick specific columns | `SELECT col1, col2` |
| **Union** | ∪ | Combine rows from two compatible relations | `UNION` |
| **Intersection** | ∩ | Rows common to both | `INTERSECT` |
| **Difference** | − | Rows in one but not the other | `EXCEPT` |
| **Cartesian product** | × | Every combination of rows from two relations | `FROM A, B` (no WHERE) |
| **Join** | ⋈ | Combine related rows on a condition | `JOIN ... ON` |

---

## 1.6 Data Modelling: ER Model

### 1.5.1 The Entity-Relationship Model (Chen, 1976)

The **ER model** is the most widely used conceptual data model. It describes real-world data in terms of:

- **Entities** — Distinguishable real-world objects (a specific employee, a specific course).
- **Attributes** — Properties that describe entities (Name, Salary, DOB).
- **Relationships** — Associations between entities (an employee "works in" a department).

### 1.5.2 Entity Types

- **Strong entity:** Has its own key attribute that uniquely identifies each entity (e.g. EMPLOYEE with Emp_ID). Exists independently.
- **Weak entity:** Cannot be uniquely identified by its own attributes alone. Depends on an **owner/identifying entity** via an **identifying relationship**. Uses a **partial key** combined with the owner's PK.
  - Example: DEPENDENT (Name, Relationship) depends on EMPLOYEE. A dependent is identified by (Emp_ID, Dependent_Name).

### 1.5.3 Attribute Types

| Type | Description | Example |
|---|---|---|
| **Simple (Atomic)** | Cannot be divided further. | FirstName |
| **Composite** | Can be divided into meaningful sub-parts. | Address → {Street, City, State, PIN} |
| **Single-valued** | Holds exactly one value per entity. | DateOfBirth |
| **Multi-valued** | Can hold multiple values per entity. | PhoneNumbers (a person may have 2-3) |
| **Derived** | Computed from other stored attributes. | Age (computed from DOB and current date) |
| **Key** | Uniquely identifies the entity. | EmployeeID |

### 1.5.4 Relationships

**Degree of a relationship:**
- **Unary (recursive):** Entity relates to itself. EMPLOYEE supervises EMPLOYEE.
- **Binary:** Between two entity types (most common). EMPLOYEE works-in DEPARTMENT.
- **Ternary:** Among three entity types. SUPPLIER supplies PART to PROJECT.

**Cardinality ratios (binary):**

| Ratio | Meaning | Example |
|---|---|---|
| **1:1** | Each entity on one side relates to at most one on the other. | One EMPLOYEE manages one DEPARTMENT. |
| **1:N** | One entity on the "1" side relates to many on the "N" side. | One DEPARTMENT has many EMPLOYEEs. |
| **M:N** | Many-to-many. | A STUDENT enrols in many COURSEs; each COURSE has many STUDENTs. |

**Participation constraints:**
- **Total (mandatory):** Every entity must participate in at least one relationship instance. Shown as a **double line** in ER diagrams. Example: Every EMPLOYEE **must** belong to a DEPARTMENT.
- **Partial (optional):** Some entities may not participate. Shown as a **single line**. Example: Not every EMPLOYEE manages a DEPARTMENT.

### 1.5.5 ER-to-Relational Mapping Rules

This is the standard algorithm for converting an ER diagram into relational tables:

| ER Construct | Mapping Rule | Rationale |
|---|---|---|
| Strong entity | Create a table with all simple attributes; key attribute → PK. | Each entity type becomes its own table. |
| Weak entity | Table with partial key + owner's PK (as FK). Composite PK = both. | Weak entity can't exist without owner, so owner's PK is part of the key. |
| 1:1 relationship | Add PK of one side as FK in the other (prefer total-participation side to avoid NULLs). | Only one FK needed since each side maps to at most one on the other. |
| 1:N relationship | Add PK of the "1" side as FK in the "N" side table. | Each entity on the "N" side is related to exactly one on the "1" side. |
| M:N relationship | Create a new junction/bridge table. PKs of both sides as FKs. Composite PK = both FKs. | No single table can hold the many-to-many mapping without a separate table. |
| Multi-valued attribute | Separate table: attribute value + FK to owner entity's PK. | Each value becomes a separate row, linked back to the owner. |
| Composite attribute | Include only the simple component sub-attributes (not the composite name). | The composite name is just a grouping; only leaf-level attributes are stored. |
| Derived attribute | Usually not stored; computed at query time (or materialised for performance). | Storing derived values risks inconsistency if the source changes. |

---

## 1.7 COMPANY Database — Worked ER Exercise

*(From class slides 40-47 — this is the classic textbook exercise from Elmasri & Navathe)*

### Problem Statement

The COMPANY database keeps track of a company's employees, departments, and projects.

**Requirements:**
- The company is organised into **departments**. Each department has a unique name, a unique number, and a particular employee who manages the department. We track the start date when that employee began managing. A department may have several locations.
- A department controls a number of **projects**, each with a unique name, a unique number, and a single location.
- We store each **employee's** name, Social Security number, address, salary, gender, and birth date. An employee is assigned to one department but may work on several projects (not necessarily controlled by the same department). We track hours per week on each project. We also track the direct supervisor of each employee.
- We want to track the **dependents** of each employee for insurance purposes: first name, gender, birth date, and relationship to the employee.

### Step 1: Identify Entities

| Entity | Type | Key Attribute |
|---|---|---|
| **Employee** | Strong | SSN |
| **Department** | Strong | Dnumber |
| **Project** | Strong | Pnumber |
| **Dependent** | Weak (depends on Employee) | Dependent_name (partial key) |

### Step 2: Identify Attributes

**Employee:** Name (Fname, Minit, Lname — composite), SSN, Address, Salary, Gender (Sex), Bdate
**Department:** Dname, Dnumber, {Dlocation} (multi-valued)
**Project:** Pname, Pnumber, Plocation
**Dependent:** Dependent_name, Sex, Bdate, Relationship

### Step 3: Identify Relationships

| Relationship | Entities | Cardinality | Participation |
|---|---|---|---|
| **MANAGES** | Employee manages Department | 1:1 | Employee: partial, Department: total |
| **WORKS_FOR** | Employee works for Department | N:1 | Employee: total, Department: total |
| **CONTROLS** | Department controls Project | 1:N | Department: partial, Project: total |
| **SUPERVISION** | Employee supervises Employee | 1:N (recursive) | Both partial |
| **WORKS_ON** | Employee works on Project | M:N | Both partial |
| **DEPENDENTS_OF** | Employee has Dependents | 1:N (identifying) | Employee: partial, Dependent: total |

**Relationship attributes:** MANAGES has Start_date. WORKS_ON has Hours.

### Step 4: ER-to-Relational Mapping

```sql
DEPARTMENT(Dname, Dnumber PK, Mgr_SSN FK→Employee, Mgr_start_date)
DEPT_LOCATIONS(Dnumber FK, Dlocation)  PK = (Dnumber, Dlocation)

EMPLOYEE(Fname, Minit, Lname, SSN PK, Bdate, Address, Sex, Salary,
         Super_SSN FK→Employee, Dno FK→Department)

PROJECT(Pname, Pnumber PK, Plocation, Dnum FK→Department)

WORKS_ON(ESSN FK→Employee, Pno FK→Project, Hours)  PK = (ESSN, Pno)

DEPENDENT(ESSN FK→Employee, Dependent_name, Sex, Bdate, Relationship)
          PK = (ESSN, Dependent_name)
```

---

## 1.8 Normalisation (1NF to BCNF)

Normalisation is the systematic process of decomposing relations to eliminate redundancy and prevent update anomalies (insertion, deletion, and update anomalies). Each normal form imposes additional constraints.

### 1.6.1 Functional Dependencies (FDs)

A functional dependency **X → Y** means: if two tuples agree on X, they must agree on Y. Knowing X uniquely determines Y.

| Type | Definition | Example |
|---|---|---|
| **Full FD** | Y depends on X entirely, not on any proper subset. | {Student_ID, Course_ID} → Grade |
| **Partial FD** | Y depends on a proper subset of a composite key. | {Student_ID, Course_ID} → Student_Name (Name depends only on Student_ID) |
| **Transitive FD** | X → Y → Z where Y is not a candidate key. | Emp_ID → Dept_ID → Dept_Name |

### 1.6.2 Normal Forms

| NF | Rule | What it eliminates | Fix |
|---|---|---|---|
| **1NF** | All attribute values must be atomic. No repeating groups. | Multi-valued cells, nested tables. | Move multi-valued attributes to a separate table. |
| **2NF** | 1NF + no partial dependencies on composite PK. | Non-key attributes depending on part of the key. | Decompose: move partially-dependent attributes to a new table with the partial key. |
| **3NF** | 2NF + no transitive dependencies among non-key attributes. | A → B → C where B is not a candidate key. | Decompose: create a separate table for the transitive chain. |
| **BCNF** | For every non-trivial FD X → Y, X must be a superkey. | Cases where a non-key determines part of a candidate key (3NF allows this if Y is prime). | Decompose on the violating FD. |

### 1.6.3 Update Anomalies (Why Normalise?)

| Anomaly | Problem | Example |
|---|---|---|
| **Insertion** | Cannot insert data without unrelated data. | Can't add a new department unless at least one employee exists in it. |
| **Deletion** | Deleting data causes unintended loss. | Deleting the last employee in a department loses the department info entirely. |
| **Update** | Must update the same fact in multiple rows. | Renaming a department requires updating every employee row in that department. |

---

## 1.9 DBMS Internal Architecture

Understanding what happens inside a DBMS helps reason about performance and troubleshooting.

```
 User/Application → Query Interface (SQL parser, API)
                          ↓
                    Query Processor
                    ├── Parser (syntax check, parse tree)
                    ├── Optimizer (choose best execution plan)
                    └── Executor (run the plan)
                          ↓
                    Transaction Manager + Concurrency Control + Recovery Manager
                          ↓
                    Storage Engine
                    ├── Buffer Manager (cache pages in memory)
                    └── Disk Manager (read/write disk blocks)
                          ↓
                    Disk Storage (data files, index files, log files)
```

**The Catalog (Data Dictionary)** stores metadata: table/column names, data types, constraints, index definitions, view definitions, user privileges, and statistics used by the optimizer.

---

## 1.10 Indexing and File Structures

### Why Indexes Matter

Without an index, finding a row requires a **full table scan** — reading every row. With millions of rows, this is extremely slow. An index maps column values to row locations, like a book's index maps topics to page numbers.

### Types of Indexes

| Index Type | How it works | Best for |
|---|---|---|
| **B+ Tree** | Self-balancing tree; leaf nodes linked for range scans. O(log n). | Equality, range, prefix queries. Used by all major RDBMSs. |
| **Hash** | Hash function maps keys to buckets. O(1) average. | Equality lookups only. Cannot sort or range scan. |
| **Bitmap** | Bit vector per distinct value. Bit i = 1 if row i has that value. | Low-cardinality columns (Gender, Status). Data warehousing. |

### Clustered vs. Non-Clustered

| Aspect | Clustered | Non-Clustered (Secondary) |
|---|---|---|
| Physical order | Rows stored in index order. | Separate structure pointing to row locations. |
| Number per table | Only one (data sorted one way). | Many. |
| Speed | Fastest for range scans. | Extra lookup unless covering. |

**Covering index:** Includes all columns needed by a query — DBMS answers entirely from the index (index-only scan).

### File Organisation

| Method | Description | Best for |
|---|---|---|
| **Heap file** | Rows in no order. | Bulk inserts, full scans. |
| **Sorted file** | Rows in sort-key order. | Range queries on sort key. |
| **Hash file** | Hash determines bucket. | Equality lookups. |
| **B+ tree file** | Rows in B+ tree leaves. | General purpose. |

---

## 1.11 Views in SQL

A **view** is a virtual table defined by a SELECT query. It doesn't store data — the underlying query runs each time the view is accessed.

```sql
CREATE VIEW High_Earners AS
SELECT Emp_ID, First_Name, Last_Name, Salary
FROM EMPLOYEE
WHERE Salary > 100000;
```

**Benefits:** Simplification (wrap complex queries), security (restrict visible columns/rows), logical data independence (change underlying tables, update view instead of all apps), consistency (one business definition reused everywhere).

**Materialised view:** Physically stores the result. Must be **refreshed** when data changes. Faster reads but stale-data risk.

---

## 1.12 Stored Procedures, Functions, and Triggers

### Stored Procedures

Named blocks of SQL stored in the database and executed on the server. Reduce network traffic, encapsulate business logic, and enable fine-grained security (EXECUTE permission without table access).

```sql
CREATE PROCEDURE GiveRaise(IN dept INT, IN pct DECIMAL)
BEGIN
    UPDATE EMPLOYEE SET Salary = Salary * (1 + pct/100) WHERE Dept_ID = dept;
END;

CALL GiveRaise(5, 10);  -- 10% raise for department 5
```

### Functions

Similar to procedures but **return a value** and can be used inside SQL expressions.

### Triggers

Automatically fire on INSERT, UPDATE, or DELETE events. Timing: `BEFORE`, `AFTER`, or `INSTEAD OF`.

```sql
CREATE TRIGGER salary_audit
AFTER UPDATE OF Salary ON EMPLOYEE
FOR EACH ROW
BEGIN
    INSERT INTO SALARY_AUDIT_LOG VALUES (OLD.Emp_ID, OLD.Salary, NEW.Salary, CURRENT_TIMESTAMP);
END;
```

**Use cases:** Audit logging, enforcing complex constraints, maintaining derived data.

**Caution:** Overuse makes debugging difficult — triggers execute implicitly and can chain.

---

*End of Session 1*
