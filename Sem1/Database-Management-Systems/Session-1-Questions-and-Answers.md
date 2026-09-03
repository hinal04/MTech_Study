# Session 1: Foundations of Structured Data Management — Questions & Answers

> 20 questions covering: Introduction, Types of Data, File Systems vs DBMS, Three-Schema Architecture, Relational Model, ER Modelling, COMPANY Database Exercise, Normalisation, Indexing, Views, Triggers.

---

### Q1. What are the main problems of using a traditional file system for data management?

**Answer:**

1. **Data redundancy** — Same data duplicated across multiple files owned by different applications.
2. **Inconsistency** — Updating one copy but not others creates conflicting versions of the same fact.
3. **Difficulty in data access** — Each new query may require writing a new program from scratch; there is no general-purpose query language.
4. **Data isolation** — Data scattered across files in different formats makes consolidation extremely difficult.
5. **Integrity problems** — No central mechanism to enforce business rules (e.g. "balance ≥ 0"). Each application must enforce them independently, leading to inconsistent enforcement.
6. **Atomicity problems** — A crash midway through a multi-step update can leave data in a partial, inconsistent state with no mechanism to undo the partial work.
7. **Concurrent-access anomalies** — Two users modifying the same file simultaneously can overwrite each other's changes (lost update problem).
8. **Security issues** — File-level OS permissions are too coarse-grained. You cannot restrict access to specific fields within a file.

---

### Q2. Define DBMS. What are its key advantages over a file system?

**Answer:**

A **Database Management System (DBMS)** is system software that provides facilities to define, create, maintain, and control access to a database. It sits between applications and stored data, managing all interactions.

| Advantage | Explanation |
|---|---|
| **Program–data independence** | Schema stored in a catalog; changes to physical storage don't require rewriting application programs. |
| **Data abstraction** | Users work with a logical view (tables, rows, columns), not physical storage details (files, blocks, pages). |
| **Multiple views** | Different users see different subsets of the same underlying data, tailored to their needs and access rights. |
| **Reduced redundancy** | Centralised storage combined with normalisation eliminates or minimises duplicate data. |
| **Integrity enforcement** | Constraints (PK, FK, CHECK, NOT NULL) defined once in the schema and enforced automatically by the DBMS for every operation. |
| **Concurrency control** | The DBMS uses locking and MVCC to ensure multiple simultaneous users don't corrupt data. |
| **Backup & recovery** | Write-ahead logging and checkpoint mechanisms allow the DBMS to restore a consistent state after any failure. |
| **Fine-grained security** | Access control at the table, column, or even row level using GRANT/REVOKE. |

---

### Q3. Explain the Three-Schema Architecture and its importance.

**Answer:**

The **ANSI/SPARC Three-Schema Architecture** (1975) separates a database system into three levels of abstraction:

1. **External Level (User Views):** Each user or application sees only the data relevant to them. A student registration app sees student names and courses. A grade reporting app sees IDs and grades. These are different views of the same underlying data.

2. **Conceptual Level (Logical Schema):** The complete logical structure of the entire database — all entities, relationships, data types, and constraints. Describes **what** data is stored, independent of how it's physically organised. Designed by the DBA.

3. **Internal Level (Physical Schema):** How data is physically stored on disk — file organisations, indexing methods, compression, buffer management. Optimised for performance by the DBA/storage engine.

**Why it's important — Data Independence:**

- **Logical data independence:** The conceptual schema can change (e.g. add a table, split a table) without affecting external views or applications. The external-to-conceptual mapping absorbs the change.
- **Physical data independence:** The internal schema can change (e.g. add an index, switch from HDD to SSD, reorganise files) without affecting the conceptual schema. The conceptual-to-internal mapping absorbs the change.

This separation is what allows databases to evolve — new features, new storage optimisations, new user views — without breaking existing applications.

---

### Q4. Differentiate between schema and instance with an example.

**Answer:**

| Aspect | Schema (Intension) | Instance (Extension) |
|---|---|---|
| **What it is** | The structure and description of the database — table definitions, column types, constraints. | The actual data stored in the database at a particular moment in time. |
| **How often it changes** | Rarely — only when the design evolves (adding a column, changing a type). | Constantly — with every INSERT, UPDATE, DELETE operation. |
| **Analogy** | A spreadsheet template with column headers and validation rules. | The rows of data filled in by users. |
| **Example** | `STUDENT(ID INT PRIMARY KEY, Name VARCHAR(50), Age INT CHECK(Age>0))` | `(101, 'Alice', 21), (102, 'Bob', 23), (103, 'Carol', 20)` |

The schema defines the "shape" of the data; the instance is the data itself at any given moment.

---

### Q5. Explain the different types of keys with examples.

**Answer:**

Consider: `STUDENT(Student_ID, Email, Name, Dept_ID)`

| Key Type | Definition | Example |
|---|---|---|
| **Superkey** | Any set of attributes that uniquely identifies a tuple. Can have redundant attributes. | {Student_ID}, {Student_ID, Name}, {Email}, {Email, Name} |
| **Candidate key** | A minimal superkey — removing any attribute breaks uniqueness. No redundancy. | {Student_ID}, {Email} (both uniquely identify, both minimal) |
| **Primary key (PK)** | The candidate key chosen by the designer as the official identifier. | Student_ID (chosen because it's numeric and stable) |
| **Alternate key** | Any candidate key not chosen as the PK. | Email (it's unique but wasn't chosen as PK) |
| **Foreign key (FK)** | Attribute(s) in one relation referencing the PK of another relation. Enforces referential integrity. | Dept_ID references DEPARTMENT(Dept_ID) |

---

### Q6. What are integrity constraints? Explain each type.

**Answer:**

Integrity constraints are rules that the DBMS enforces to keep the database in a valid, consistent state.

1. **Entity integrity:** No attribute of a primary key can be NULL. Every row must be uniquely identifiable — a NULL PK would make it impossible to distinguish rows.

2. **Referential integrity:** A foreign key value must either be NULL (if the FK column allows NULLs) or must match an existing primary key value in the referenced relation. You cannot have an employee with Dept_ID = 99 if no department with Dept_ID = 99 exists.

3. **Domain constraints:** Every attribute value must come from the attribute's defined domain. If Age is defined as a positive integer, you cannot store -5 or "hello" in it.

4. **Key constraints:** No two tuples in a relation can have the same value for the primary key. This guarantees that the PK actually identifies rows uniquely.

---

### Q7. Describe the ER model. Explain entity types, attribute types, and relationship types.

**Answer:**

The **Entity-Relationship (ER) model** (Chen, 1976) is a conceptual data model that describes real-world data using three core concepts:

**Entity types:**
- **Strong entity:** Has its own key attribute. Exists independently. Example: EMPLOYEE(Emp_ID, Name, Salary).
- **Weak entity:** No key of its own. Depends on an owner entity via an identifying relationship. Uses a partial key + owner's PK. Example: DEPENDENT(Name, Relationship) depends on EMPLOYEE.

**Attribute types:**
- **Simple (Atomic):** Indivisible — FirstName.
- **Composite:** Divisible into sub-parts — Address → {Street, City, State, PIN}.
- **Single-valued:** One value per entity — DateOfBirth.
- **Multi-valued:** Multiple values — PhoneNumbers (a person may have 2-3).
- **Derived:** Computed from other attributes — Age (from DOB).
- **Key:** Uniquely identifies the entity — EmployeeID.

**Relationship types:**
- **Unary (recursive):** Entity relates to itself — EMPLOYEE supervises EMPLOYEE.
- **Binary:** Between two entity types — EMPLOYEE works-in DEPARTMENT.
- **Ternary:** Among three entity types — SUPPLIER supplies PART to PROJECT.

**Cardinality ratios:** 1:1, 1:N, M:N.
**Participation:** Total (mandatory — every entity must participate) or Partial (optional).

---

### Q8. Describe the ER-to-Relational mapping rules with examples.

**Answer:**

| ER Construct | Mapping Rule | Example |
|---|---|---|
| **Strong entity** | Create table; simple attributes → columns; key → PK. | EMPLOYEE(Emp_ID PK, Name, Salary) |
| **Weak entity** | Table with partial key + owner's PK as FK. Composite PK. | DEPENDENT(Emp_ID FK, Dep_Name, Relationship) PK=(Emp_ID, Dep_Name) |
| **1:1 relationship** | Add PK of one side as FK in the other (prefer total-participation side). | EMPLOYEE(Emp_ID, ..., Manages_Dept_ID FK) |
| **1:N relationship** | Add PK of "1" side as FK in the "N" side. | EMPLOYEE(Emp_ID, ..., Dept_ID FK) |
| **M:N relationship** | New junction table with both PKs as FKs; composite PK. | ENROLMENT(Student_ID FK, Course_ID FK, Grade) PK=(Student_ID, Course_ID) |
| **Multi-valued attribute** | Separate table: value + FK to owner's PK. | EMP_PHONE(Emp_ID FK, Phone) PK=(Emp_ID, Phone) |

---

### Q9. What is normalisation? Explain 1NF through BCNF with examples.

**Answer:**

**Normalisation** is the process of decomposing relations to eliminate redundancy and prevent update anomalies.

**1NF (First Normal Form):** All values must be atomic. No multi-valued or composite attributes in a cell.
- Violation: `STUDENT(ID, Name, Phones={9876, 1234})` — Phones is multi-valued.
- Fix: `STUDENT(ID, Name)` + `STUDENT_PHONE(ID, Phone)`.

**2NF (Second Normal Form):** 1NF + no partial dependencies on composite PK.
- Violation: `ENROLMENT(Student_ID, Course_ID, Student_Name, Grade)` — Student_Name depends only on Student_ID (partial dependency).
- Fix: `STUDENT(Student_ID, Student_Name)` + `ENROLMENT(Student_ID, Course_ID, Grade)`.

**3NF (Third Normal Form):** 2NF + no transitive dependencies among non-key attributes.
- Violation: `EMPLOYEE(Emp_ID, Dept_ID, Dept_Name)` — Dept_Name depends on Dept_ID (transitive: Emp_ID → Dept_ID → Dept_Name).
- Fix: `EMPLOYEE(Emp_ID, Dept_ID)` + `DEPARTMENT(Dept_ID, Dept_Name)`.

**BCNF (Boyce-Codd Normal Form):** For every non-trivial FD X → Y, X must be a superkey. Stricter than 3NF.
- Violation: `TEACHING(Student, Course, Instructor)` with FDs: {Student, Course} → Instructor and Instructor → Course. Instructor determines Course but Instructor is not a superkey.
- Fix: `INSTRUCTOR_COURSE(Instructor, Course)` + `STUDENT_INSTRUCTOR(Student, Instructor)`.

---

### Q10. What are the three types of update anomalies? Give examples.

**Answer:**

These occur in un-normalised or poorly normalised tables:

| Anomaly | Problem | Example |
|---|---|---|
| **Insertion anomaly** | Cannot insert data without unrelated data. | In a combined EMPLOYEE-DEPARTMENT table, you can't add a new department unless at least one employee exists in it — there's no employee row to hold the department info. |
| **Deletion anomaly** | Deleting data causes unintended loss. | Deleting the last employee in department "Research" also loses all information about the Research department (name, location, budget). |
| **Update anomaly** | The same fact must be updated in multiple rows. | If department "Sales" changes its name to "Revenue", every employee row with Dept_Name = 'Sales' must be updated. Missing one creates inconsistency. |

**Solution:** Normalise the table to separate independent facts into separate tables.

---

### Q11. Explain B+ Tree indexing. Why is it preferred in databases?

**Answer:**

A **B+ Tree** is a self-balancing tree structure where:
- All data pointers are in **leaf nodes** only (internal nodes have only keys for navigation).
- Leaf nodes are **linked** as a doubly-linked list for efficient sequential/range access.
- The tree is always balanced — all leaves at the same depth.

**Why preferred:**
1. **O(log n) search** — Even with billions of rows, only 3-4 disk reads are needed (each level = one disk read).
2. **Efficient range queries** — Follow the linked leaf nodes. `WHERE Salary BETWEEN 50000 AND 80000` starts at one leaf and walks the chain.
3. **Balanced** — Insertions and deletions maintain balance automatically. No worst-case O(n) like unbalanced BSTs.
4. **High fan-out** — Each node can hold hundreds of keys (sized to match disk block size), keeping the tree very shallow.
5. **Disk-optimised** — Nodes are sized to match disk pages (typically 4-16 KB), minimising I/O operations.

**Comparison with Hash Index:** Hash gives O(1) for equality but cannot support range queries, ORDER BY, or prefix matching. B+ Tree handles all of these.

---

### Q12. Compare clustered and non-clustered indexes.

**Answer:**

| Aspect | Clustered Index | Non-Clustered (Secondary) Index |
|---|---|---|
| **Physical order** | Table rows are physically stored in the order of the index key. | Index is a separate structure that stores (key, pointer-to-row) pairs. |
| **Number per table** | Only **one** — data can only be physically sorted one way. | **Many** can exist on the same table. |
| **Speed for range scans** | Fastest — rows with adjacent key values are stored adjacent on disk. | Slower — must follow pointers to scattered row locations (random I/O). |
| **Speed for point lookup** | Fast — binary search on sorted data. | Fast if covering (index-only scan); otherwise needs extra row fetch. |
| **Example** | Primary key index in MySQL InnoDB (data stored in PK order). | Index on Email column; index on (Dept_ID, Salary). |

**Covering index:** An index that includes all columns needed by a query. The DBMS answers entirely from the index without touching the main table (index-only scan).

---

### Q13. What is a view? Compare views and materialised views.

**Answer:**

A **view** is a virtual table defined by a SQL query. It stores no data — every time you query the view, the underlying SELECT executes.

| Aspect | View | Materialised View |
|---|---|---|
| **Storage** | No data stored; query runs each time. | Result physically stored on disk. |
| **Freshness** | Always current — runs against live data. | May be stale — must be refreshed. |
| **Performance** | Same as running the underlying query. | Faster reads (pre-computed). |
| **Use case** | Security (restrict visible columns), simplification, logical independence. | Expensive aggregations in reporting/data warehousing. |
| **Maintenance** | None — just a stored query definition. | Must be refreshed (manually, on schedule, or on commit). |

---

### Q14. Explain stored procedures and triggers. When would you use each?

**Answer:**

**Stored Procedure:** A named, reusable block of SQL statements stored in the database and executed on the server.
- **Use when:** Encapsulating business logic (e.g. "give all employees in department X a Y% raise"), reducing network traffic (one CALL instead of multiple statements), granting access via EXECUTE without direct table access.

**Trigger:** A stored procedure that automatically fires when a specific event (INSERT, UPDATE, DELETE) occurs on a table.
- **Use when:** Audit logging (record who changed what and when), enforcing complex constraints that CHECK can't express, maintaining derived/summary data, cascading business rules.

**Key difference:** You explicitly CALL a procedure. A trigger fires implicitly — you don't invoke it directly. This makes triggers powerful but harder to debug (hidden side effects, potential for cascading triggers).

---

### Q15. List and briefly describe the core relational algebra operations.

**Answer:**

| Operation | Symbol | Description | SQL Equivalent |
|---|---|---|---|
| **Selection** | σ | Filter rows by condition. σ_{Salary>50000}(EMPLOYEE) | `WHERE Salary > 50000` |
| **Projection** | π | Pick specific columns. π_{Name,Salary}(EMPLOYEE) | `SELECT Name, Salary` |
| **Union** | ∪ | Combine rows from two union-compatible relations (remove duplicates). | `UNION` |
| **Intersection** | ∩ | Rows present in both relations. | `INTERSECT` |
| **Difference** | − | Rows in first relation but not in second. | `EXCEPT` |
| **Cartesian Product** | × | Every combination of rows from two relations. | `FROM A, B` (no WHERE) |
| **Join** | ⋈ | Combine related rows based on a condition. Equivalent to σ over ×. | `JOIN ... ON` |
| **Rename** | ρ | Rename a relation or its attributes. | `AS` alias |

The five **fundamental** operations are: σ, π, ∪, −, ×. All others (∩, ⋈, ÷) can be derived from these five.

---

---

### Q16. Differentiate between structured, semi-structured, and unstructured data with examples.

**Answer:**

| Aspect | Structured | Semi-Structured | Unstructured |
|---|---|---|---|
| **Schema** | Fixed, predefined (tables with fixed columns and types). | Flexible, self-describing (tags/keys embedded in data). | None — no predefined format. |
| **Format** | Rows and columns (relational tables). | JSON, XML, YAML. | Text, images, audio, video, PDFs. |
| **Query method** | SQL. | JSON queries, XPath, XQuery. | Full-text search, AI/ML, NLP. |
| **Storage** | RDBMS (MySQL, PostgreSQL, Oracle). | Document databases (MongoDB, CouchDB). | Object storage (S3), data lakes. |
| **Examples** | Employee table, bank transactions, student records. | API responses, email (headers structured, body not), config files. | Word documents, photographs, videos, social media posts. |
| **% of global data** | ~10-20% | ~5-10% | ~80-90% |

**Key insight:** Structured data fits relational databases perfectly. Semi-structured data is the domain of document/NoSQL databases. Unstructured data requires specialised systems. Modern data platforms must handle all three.

---

### Q17. What are application-based (semantic) constraints? Give three examples.

**Answer:**

**Application-based constraints** are business rules that cannot be expressed through standard schema constraints (PRIMARY KEY, FOREIGN KEY, CHECK, NOT NULL). They require custom logic in application programs, triggers, or stored procedures.

**Examples from class:**
1. "Salary of an employee should not exceed the salary of the employee's supervisor." — This requires comparing values across different rows (employee and their supervisor), which a single-row CHECK constraint cannot do.
2. "Maximum number of hours an employee can work on a project per week is 56 hours." — This could be a CHECK constraint on a single column, but in practice the hours may be distributed across multiple projects and need a cross-row sum check.
3. "Salary of an employee should only increase (never decrease)." — This requires comparing the new value with the old value during an UPDATE, which needs a trigger (BEFORE UPDATE) to enforce.

---

### Q18. Design the COMPANY database: identify entities, relationships, and map to relational tables.

**Answer:**

**Entities:** Employee (strong, key=SSN), Department (strong, key=Dnumber), Project (strong, key=Pnumber), Dependent (weak, depends on Employee, partial key=Dependent_name).

**Key relationships:**

| Relationship | Type | Cardinality |
|---|---|---|
| Employee MANAGES Department | Binary | 1:1 (Employee partial, Department total) |
| Employee WORKS_FOR Department | Binary | N:1 (both total) |
| Department CONTROLS Project | Binary | 1:N (Dept partial, Project total) |
| Employee SUPERVISES Employee | Recursive | 1:N (both partial) |
| Employee WORKS_ON Project | Binary | M:N (both partial), has attribute Hours |
| Employee HAS Dependent | Identifying | 1:N (Employee partial, Dependent total) |

**Relational mapping:**
```sql
DEPARTMENT(Dname, Dnumber PK, Mgr_SSN FK→Employee, Mgr_start_date)
DEPT_LOCATIONS(Dnumber FK, Dlocation)  PK=(Dnumber, Dlocation)
EMPLOYEE(Fname, Minit, Lname, SSN PK, Bdate, Address, Sex, Salary,
         Super_SSN FK→Employee, Dno FK→Department)
PROJECT(Pname, Pnumber PK, Plocation, Dnum FK→Department)
WORKS_ON(ESSN FK→Employee, Pno FK→Project, Hours)  PK=(ESSN, Pno)
DEPENDENT(ESSN FK→Employee, Dependent_name, Sex, Bdate, Relationship)  PK=(ESSN, Dependent_name)
```

---

### Q19. What is the Database System Environment? What role does the DBMS play between users and data?

**Answer:**

The **Database System Environment** consists of:

1. **Users/Applications** — End users, application programs, and database administrators who interact with data.
2. **DBMS Software** — The middleware that sits between users and stored data. It handles:
   - **Defining** the database (DDL processing).
   - **Constructing** the database (creating objects, populating data).
   - **Manipulating** the database (INSERT, UPDATE, DELETE, SELECT).
   - **Supporting** multiple users and applications simultaneously.
3. **Stored Database** — The physical data files, index files, and log files on disk.
4. **Metadata/Catalog** — The data dictionary describing the schema, constraints, and storage details.

The DBMS interprets and optimises SQL statements before execution. It provides a **logical view** of the data, hiding low-level storage details. Applications communicate with the database using SQL; the DBMS translates this into physical operations on disk.

---

### Q20. What is the evolution of database systems from the 1960s to today?

**Answer:**

| Era | System | Characteristics |
|---|---|---|
| **1960s** | File Systems | Application-specific flat files. No sharing, no integrity, no concurrency control. |
| **1960s-70s** | Hierarchical DB (IMS) | Tree structure. Efficient for known access paths. Rigid, hard to reorganise. |
| **1970s** | Network DB (CODASYL) | Graph structure. More flexible than hierarchical but complex navigation. |
| **1970s-80s** | Relational DB (Codd, 1970) | Tables with SQL. Data independence. Mathematical foundation. Dominant model today. |
| **1990s** | Object-Oriented DB | Stored complex objects directly. Limited adoption. |
| **2000s** | NoSQL | Key-value, document, column-family, graph. Traded ACID for scalability. |
| **2010s** | NewSQL | Combined SQL + ACID + horizontal scalability (Spanner, CockroachDB). |
| **2020s** | Cloud-native / Lakehouse | Serverless databases, data lakes + warehouses unified (Databricks Lakehouse). |

*End of Session 1 Questions & Answers*
