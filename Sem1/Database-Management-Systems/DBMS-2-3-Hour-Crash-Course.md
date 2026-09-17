# DBMS — 2-3 Hour Crash Course (Exam Ready)

> BITS Pilani — SSZG507: Modern Database Systems
> You haven't attended lectures. Your exam is in 2 days.
> This covers everything from Sessions 1–4 in simple language with solved examples.
> 
> **Time Plan:**
> - **Hour 1 (Topics 1-4):** Normalisation, SQL Queries, Keys & Constraints, ACID/Transactions
> - **Hour 2 (Topics 5-8):** MongoDB Queries, Redis, SQL vs NoSQL Scenarios, Distributed DB
> - **Hour 3 (Topics 9-11):** 2PC & CAP Theorem, ER Model, Quick Revision
>
> **Exam Pattern:**
> 1. MongoDB/Redis Queries (1–2 questions)
> 2. Conceptual — SQL or NoSQL scenario-based (justify)
> 3. Relational model — Primary Key analysis
> 4. Normalisation — 1NF → 2NF → 3NF → BCNF
> 5. SQL Queries — 7–10 marks
> 6. Distributed DB — Fragmentation, placement across cluster

---

# ⏰ HOUR 1 — Highest Yield Topics

---

## TOPIC 1: Normalisation (⭐⭐⭐) — Most Tested

> **Time: ~25 minutes**
> This is the single most tested topic. If you learn one thing well, make it this.

### What is Normalisation?

Normalisation is the process of organizing a database to **remove redundancy** (duplicate data) and **prevent anomalies** (insert/update/delete problems). You start with one big messy table and break it into smaller, cleaner tables.

### The Memory Trick

> **"The key, the whole key, and nothing but the key — so help me Codd."**
> 
> - **1NF** → The key (atomic values, no comma-separated lists in a cell)
> - **2NF** → The whole key (every non-key column depends on the ENTIRE primary key, not just part of it)
> - **3NF** → Nothing but the key (non-key columns don't depend on OTHER non-key columns)
> - **BCNF** → Every determinant IS a candidate key (strictest form)

### What is a Functional Dependency (FD)?

An FD `A → B` means: "If you know the value of A, you can determine the value of B."

Example: `Student_ID → Student_Name` — if you know the ID, you know the name.

### Quick Identification Flowchart

```
START with your table
  │
  ├─ Does any cell have comma-separated values or repeating groups?
  │   YES → Not even in 1NF. Fix: Make each value atomic (one value per cell).
  │   NO ↓
  │
  ├─ Is the PK composite (multi-column)?
  │   YES → Does any non-key column depend on only PART of the PK?
  │          YES → In 1NF, not 2NF. Fix: Move partial dependencies to new table.
  │          NO ↓
  │   NO → Already in 2NF (partial dependency can't exist with single-column PK)
  │         ↓
  │
  ├─ Does any non-key column depend on ANOTHER non-key column?
  │   (A → B → C, where B is not a key)
  │   YES → In 2NF, not 3NF. Fix: Move transitive dependencies to new table.
  │   NO ↓
  │
  ├─ Is every determinant (left side of every FD) a candidate key?
  │   NO → In 3NF, not BCNF. Fix: Decompose so every determinant is a key.
  │   YES → In BCNF ✓
  │
  DONE
```

### Normal Forms — Rules Table

| Normal Form | Rule | Violation Example | Fix |
|---|---|---|---|
| **1NF** | Every cell has ONE atomic value. No repeating groups. | Phone: "9876,1234" | Split into separate rows or a new table |
| **2NF** | 1NF + No **partial dependency** (non-key depends on part of composite PK) | PK={StudentID, CourseID}, but StudentName depends only on StudentID | Move StudentName to STUDENT table |
| **3NF** | 2NF + No **transitive dependency** (non-key → non-key) | EmpID → DeptID → DeptName (DeptName depends on DeptID, not directly on EmpID) | Move DeptName to DEPARTMENT table |
| **BCNF** | For EVERY FD X→Y, X must be a superkey | CourseID,InstructorID → Room but InstructorID → Room (InstructorID is not a superkey) | Decompose into two tables |

### 3NF vs BCNF — When Do They Differ?

They differ ONLY when:
- There are multiple overlapping candidate keys, AND
- A non-candidate-key attribute determines part of a candidate key

> **Rule of thumb:** If your table has a single candidate key, 3NF = BCNF. They differ only with multiple overlapping candidate keys.

---

### ✅ Solved Example 1: STUDENT_COURSE Normalisation [Past Paper Pattern]

**Original Table: STUDENT_COURSE**

| StudentID | StudentName | CourseID | CourseName | InstructorName | Grade |
|---|---|---|---|---|---|
| S1 | Rahul | CS101 | DBMS | Prof. Narang | A |
| S1 | Rahul | CS102 | OS | Prof. Sharma | B+ |
| S2 | Priya | CS101 | DBMS | Prof. Narang | A+ |
| S2 | Priya | CS103 | Networks | Prof. Gupta | B |

**Step 1: Identify the Primary Key**

To uniquely identify each row, we need both StudentID AND CourseID.
- PK = {StudentID, CourseID} (composite key)

**Step 2: List All Functional Dependencies**

```
FD1: StudentID → StudentName
FD2: CourseID → CourseName, InstructorName
FD3: StudentID, CourseID → Grade
```

**Step 3: Check 1NF** ✅

All values are atomic (no commas, no lists). Already in 1NF.

**Step 4: Check 2NF** ❌ — Partial Dependencies Found!

PK is composite {StudentID, CourseID}. Check if any non-key attribute depends on only PART of the PK:

| FD | Depends on | Full PK? | Verdict |
|---|---|---|---|
| StudentID → StudentName | Part of PK (StudentID only) | ❌ Partial | **Violates 2NF** |
| CourseID → CourseName | Part of PK (CourseID only) | ❌ Partial | **Violates 2NF** |
| CourseID → InstructorName | Part of PK (CourseID only) | ❌ Partial | **Violates 2NF** |
| {StudentID,CourseID} → Grade | Full PK | ✅ Full | OK |

**Fix: Decompose to remove partial dependencies:**

**STUDENT** (StudentID PK, StudentName)
| StudentID | StudentName |
|---|---|
| S1 | Rahul |
| S2 | Priya |

**COURSE** (CourseID PK, CourseName, InstructorName)
| CourseID | CourseName | InstructorName |
|---|---|---|
| CS101 | DBMS | Prof. Narang |
| CS102 | OS | Prof. Sharma |
| CS103 | Networks | Prof. Gupta |

**ENROLLMENT** (StudentID FK, CourseID FK, Grade) — PK = {StudentID, CourseID}
| StudentID | CourseID | Grade |
|---|---|---|
| S1 | CS101 | A |
| S1 | CS102 | B+ |
| S2 | CS101 | A+ |
| S2 | CS103 | B |

**Step 5: Check 3NF** ✅

In each table, no non-key attribute depends on another non-key attribute. All good!

**Step 6: Check BCNF** ✅

In each table, the only determinants are the primary keys (which are superkeys). Done!

---

### ✅ Solved Example 2: BCNF Decomposition [Practice]

**Table: COURSE_ASSIGN(CourseID, InstructorID, Room, TimeSlot)**

**Constraints:**
- Each course is taught by one instructor at one time: {CourseID} → {InstructorID, TimeSlot}
- An instructor teaches in a specific room: {InstructorID} → {Room}
- A room at a time has one course: {Room, TimeSlot} → {CourseID}

**Functional Dependencies:**
```
FD1: CourseID → InstructorID, TimeSlot
FD2: InstructorID → Room
FD3: Room, TimeSlot → CourseID
```

**Candidate Keys:** {CourseID} and {Room, TimeSlot}

**Check 3NF:** For FD2 (InstructorID → Room):
- Is InstructorID a superkey? NO ❌
- Is Room a prime attribute (part of some candidate key)? YES ✅ (Room is part of candidate key {Room, TimeSlot})
- 3NF allows: if X is not a superkey, Y must be a prime attribute. Room IS prime. So **3NF is satisfied** ✅

**Check BCNF:** For FD2 (InstructorID → Room):
- Is InstructorID a superkey? NO ❌
- **BCNF violated!** (BCNF requires EVERY determinant to be a superkey)

**BCNF Decomposition:**

Split on the violating FD (InstructorID → Room):

**Table 1: INSTRUCTOR_ROOM** (InstructorID PK, Room)
| InstructorID | Room |
|---|---|
| I1 | R101 |
| I2 | R202 |

**Table 2: COURSE_SCHEDULE** (CourseID PK, InstructorID FK, TimeSlot)
| CourseID | InstructorID | TimeSlot |
|---|---|---|
| CS101 | I1 | Mon 9AM |
| CS102 | I2 | Tue 11AM |

> ⚠️ **Note:** BCNF decomposition may lose the FD {Room, TimeSlot} → CourseID (you can't check this from the two tables alone). This is the trade-off: **BCNF guarantees no redundancy but may lose some dependency preservation. 3NF always preserves dependencies.**

---

### 🔄 Practice Problem 1: INVOICE Normalisation

**Table: INVOICE**

| InvoiceNo | Date | CustomerID | CustomerName | CustomerCity | ItemCode | ItemName | Qty | UnitPrice |
|---|---|---|---|---|---|---|---|---|
| INV001 | 2024-01-15 | C1 | Flipkart | Bangalore | P1 | Laptop | 2 | 50000 |
| INV001 | 2024-01-15 | C1 | Flipkart | Bangalore | P2 | Mouse | 10 | 500 |
| INV002 | 2024-01-16 | C2 | Swiggy | Bangalore | P1 | Laptop | 1 | 50000 |

<details>
<summary>Answer</summary>

**Step 1: Identify PK** → {InvoiceNo, ItemCode} (need both to identify a unique line)

**Step 2: Functional Dependencies**
```
FD1: InvoiceNo → Date, CustomerID
FD2: CustomerID → CustomerName, CustomerCity
FD3: ItemCode → ItemName, UnitPrice
FD4: InvoiceNo, ItemCode → Qty
```

**Step 3: 1NF** ✅ — All values atomic.

**Step 4: 2NF** ❌ — Partial dependencies:
- InvoiceNo → Date, CustomerID (depends on part of PK)
- ItemCode → ItemName, UnitPrice (depends on part of PK)

**Decompose:**
- **INVOICE_HEADER**(InvoiceNo PK, Date, CustomerID FK)
- **ITEM**(ItemCode PK, ItemName, UnitPrice)
- **INVOICE_LINE**(InvoiceNo FK, ItemCode FK, Qty) — PK = {InvoiceNo, ItemCode}

**Step 5: 3NF** ❌ — In INVOICE_HEADER: CustomerID → CustomerName, CustomerCity (transitive: InvoiceNo → CustomerID → CustomerName)

**Decompose further:**
- **INVOICE_HEADER**(InvoiceNo PK, Date, CustomerID FK)
- **CUSTOMER**(CustomerID PK, CustomerName, CustomerCity)
- **ITEM**(ItemCode PK, ItemName, UnitPrice)
- **INVOICE_LINE**(InvoiceNo FK, ItemCode FK, Qty) — PK = {InvoiceNo, ItemCode}

**Final: 4 tables, all in 3NF and BCNF** ✅
</details>

---

### 🔄 Practice Problem 2: EMPLOYEE_PROJECT Normalisation

**Table: EMPLOYEE_PROJECT**

| EmpID | EmpName | DeptID | DeptName | DeptHead | ProjID | ProjName | Hours |
|---|---|---|---|---|---|---|---|
| E1 | Amit | D1 | Engineering | Ravi | P1 | Zomato App | 20 |
| E1 | Amit | D1 | Engineering | Ravi | P2 | Swiggy API | 15 |
| E2 | Priya | D2 | Marketing | Neha | P1 | Zomato App | 10 |

<details>
<summary>Answer</summary>

**PK:** {EmpID, ProjID}

**Functional Dependencies:**
```
FD1: EmpID → EmpName, DeptID
FD2: DeptID → DeptName, DeptHead
FD3: ProjID → ProjName
FD4: EmpID, ProjID → Hours
```

**1NF:** ✅ Atomic values.

**2NF:** ❌ Partial dependencies:
- EmpID → EmpName, DeptID (partial — only part of composite PK)
- ProjID → ProjName (partial)

**Decompose for 2NF:**
- **EMPLOYEE**(EmpID PK, EmpName, DeptID FK)
- **PROJECT**(ProjID PK, ProjName)
- **EMP_PROJECT**(EmpID FK, ProjID FK, Hours) — PK = {EmpID, ProjID}

**3NF:** ❌ In EMPLOYEE: EmpID → DeptID → DeptName, DeptHead (transitive)

**Decompose for 3NF:**
- **EMPLOYEE**(EmpID PK, EmpName, DeptID FK)
- **DEPARTMENT**(DeptID PK, DeptName, DeptHead)
- **PROJECT**(ProjID PK, ProjName)
- **EMP_PROJECT**(EmpID FK, ProjID FK, Hours) — PK = {EmpID, ProjID}

**All 4 tables are in BCNF** ✅
</details>

---

## TOPIC 2: SQL Queries (⭐⭐⭐) — 7-10 Marks

> **Time: ~25 minutes**
> Expect 2-3 SQL questions. Practice writing queries by hand.

### Schema for All Examples

```sql
EMPLOYEE (Emp_ID PK, First_Name, Last_Name, Salary, Dept_ID FK, Manager_ID FK)
DEPARTMENT (Dept_ID PK, Dept_Name, Location)
PROJECT (Proj_ID PK, Proj_Name, Budget)
WORKS_ON (Emp_ID FK, Proj_ID FK, Hours)  -- PK = {Emp_ID, Proj_ID}
```

### SQL Execution Order (Memorise This!)

```
FROM     → 1st (which tables?)
WHERE    → 2nd (filter rows)
GROUP BY → 3rd (group remaining rows)
HAVING   → 4th (filter groups)
SELECT   → 5th (choose columns)
ORDER BY → 6th (sort result)
LIMIT    → 7th (restrict output)
```

> **Key gotcha:** You CANNOT use a column alias (defined in SELECT) inside WHERE — because WHERE runs before SELECT.

### Pattern 1: Self Join — Employee Earning More Than Manager

```sql
SELECT E.First_Name AS Employee, E.Salary AS Emp_Salary,
       M.First_Name AS Manager, M.Salary AS Mgr_Salary
FROM EMPLOYEE E
JOIN EMPLOYEE M ON E.Manager_ID = M.Emp_ID
WHERE E.Salary > M.Salary;
```

**How it works:** The EMPLOYEE table is joined with itself. `E` is the employee copy, `M` is the manager copy. We match each employee to their manager using `Manager_ID = Emp_ID`, then filter where employee earns more.

### Pattern 2: GROUP BY + HAVING — Departments with >5 Employees and Avg Salary >50K

```sql
SELECT D.Dept_Name, COUNT(*) AS Emp_Count, AVG(E.Salary) AS Avg_Salary
FROM EMPLOYEE E
JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID
GROUP BY D.Dept_Name
HAVING COUNT(*) > 5 AND AVG(E.Salary) > 50000;
```

**Key difference:** WHERE filters individual rows BEFORE grouping. HAVING filters entire groups AFTER grouping. You need HAVING when the condition involves an aggregate (COUNT, AVG, SUM, etc.).

### Pattern 3: Window Functions — RANK, DENSE_RANK, ROW_NUMBER

```sql
SELECT First_Name, Dept_ID, Salary,
       ROW_NUMBER() OVER (ORDER BY Salary DESC) AS row_num,
       RANK()       OVER (ORDER BY Salary DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY Salary DESC) AS dense_rank
FROM EMPLOYEE;
```

**With tied salaries (60K, 60K, 50K):**

| Name | Salary | ROW_NUMBER | RANK | DENSE_RANK |
|---|---|---|---|---|
| Amit | 60000 | 1 | 1 | 1 |
| Priya | 60000 | 2 | 1 | 1 |
| Rahul | 50000 | 3 | 3 | 2 |

| Function | Ties? | Gaps? | Use When |
|---|---|---|---|
| ROW_NUMBER | Breaks ties arbitrarily (1,2,3) | No | Need unique sequential numbers |
| RANK | Same rank for ties (1,1,3) | Yes — skips next | "Who are the top 3?" (may return 4 if tied) |
| DENSE_RANK | Same rank for ties (1,1,2) | No — no skip | "What are the top 3 salary levels?" |

### Pattern 4: Correlated Subquery — Employees Above Department Average

```sql
SELECT E.First_Name, E.Salary, E.Dept_ID
FROM EMPLOYEE E
WHERE E.Salary > (
    SELECT AVG(E2.Salary)
    FROM EMPLOYEE E2
    WHERE E2.Dept_ID = E.Dept_ID   -- references outer query!
);
```

**How it works:** For EACH employee in the outer query, the inner query calculates that employee's department average. If their salary exceeds it, they're included. This re-runs for every row — that's what makes it "correlated."

### Pattern 5: Relational Division — "Find All Who Have EVERY"

> "Find employees who work on EVERY project."

This uses the double NOT EXISTS pattern:

```sql
SELECT E.First_Name
FROM EMPLOYEE E
WHERE NOT EXISTS (
    SELECT P.Proj_ID
    FROM PROJECT P
    WHERE NOT EXISTS (
        SELECT 1
        FROM WORKS_ON W
        WHERE W.Emp_ID = E.Emp_ID
        AND W.Proj_ID = P.Proj_ID
    )
);
```

**Reading it in English:** "Find employees where there does NOT EXIST a project that they do NOT work on" = they work on every project.

### Pattern 6: 2nd Highest Salary Per Department

```sql
-- Method 1: Using DENSE_RANK
SELECT Dept_ID, First_Name, Salary
FROM (
    SELECT Dept_ID, First_Name, Salary,
           DENSE_RANK() OVER (PARTITION BY Dept_ID ORDER BY Salary DESC) AS dr
    FROM EMPLOYEE
) ranked
WHERE dr = 2;

-- Method 2: Using Correlated Subquery (no window functions)
SELECT E.Dept_ID, E.First_Name, E.Salary
FROM EMPLOYEE E
WHERE 1 = (
    SELECT COUNT(DISTINCT E2.Salary)
    FROM EMPLOYEE E2
    WHERE E2.Dept_ID = E.Dept_ID
    AND E2.Salary > E.Salary
);
```

**Method 2 logic:** Count how many DISTINCT salaries are higher than mine in my department. If exactly 1 salary is higher → I have the 2nd highest.

---

### ✅ Solved: Employees in Departments Located in 'Mumbai' [Past Paper Pattern]

```sql
SELECT E.First_Name, E.Last_Name, D.Dept_Name
FROM EMPLOYEE E
JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID
WHERE D.Location = 'Mumbai';
```

### ✅ Solved: Department with the Highest Total Salary [Past Paper Pattern]

```sql
SELECT D.Dept_Name, SUM(E.Salary) AS Total_Salary
FROM EMPLOYEE E
JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID
GROUP BY D.Dept_Name
ORDER BY Total_Salary DESC
LIMIT 1;
```

---

### 🔄 Practice Problem 3: SQL Queries

**Q1:** Find employees who do NOT work on any project.

<details>
<summary>Answer</summary>

```sql
SELECT E.First_Name, E.Last_Name
FROM EMPLOYEE E
WHERE E.Emp_ID NOT IN (
    SELECT DISTINCT W.Emp_ID FROM WORKS_ON W
);

-- OR using LEFT JOIN:
SELECT E.First_Name, E.Last_Name
FROM EMPLOYEE E
LEFT JOIN WORKS_ON W ON E.Emp_ID = W.Emp_ID
WHERE W.Emp_ID IS NULL;
```
</details>

**Q2:** Find the department name and average salary for departments where the average salary is above the company-wide average.

<details>
<summary>Answer</summary>

```sql
SELECT D.Dept_Name, AVG(E.Salary) AS Avg_Salary
FROM EMPLOYEE E
JOIN DEPARTMENT D ON E.Dept_ID = D.Dept_ID
GROUP BY D.Dept_Name
HAVING AVG(E.Salary) > (SELECT AVG(Salary) FROM EMPLOYEE);
```
</details>

**Q3:** For each project, find the total hours worked and the number of employees assigned. Only show projects with more than 3 employees.

<details>
<summary>Answer</summary>

```sql
SELECT P.Proj_Name, SUM(W.Hours) AS Total_Hours, COUNT(W.Emp_ID) AS Num_Employees
FROM PROJECT P
JOIN WORKS_ON W ON P.Proj_ID = W.Proj_ID
GROUP BY P.Proj_Name
HAVING COUNT(W.Emp_ID) > 3;
```
</details>

**Q4:** Find the name and salary of employees who earn more than every employee in the 'Marketing' department.

<details>
<summary>Answer</summary>

```sql
SELECT E.First_Name, E.Salary
FROM EMPLOYEE E
WHERE E.Salary > ALL (
    SELECT E2.Salary
    FROM EMPLOYEE E2
    JOIN DEPARTMENT D ON E2.Dept_ID = D.Dept_ID
    WHERE D.Dept_Name = 'Marketing'
);
```
</details>

---

## TOPIC 3: Keys & Primary Key Analysis (⭐⭐)

> **Time: ~10 minutes**
> Exam often asks: "Is X a good primary key? Why or why not?"

### 6 Key Types — Quick Reference

| Key Type | Definition | Example (STUDENT table) |
|---|---|---|
| **Super Key** | Any set of columns that uniquely identifies a row | {StudentID}, {StudentID, Name}, {Email, Phone} |
| **Candidate Key** | A MINIMAL super key (can't remove any column and stay unique) | {StudentID}, {Email} |
| **Primary Key** | The ONE candidate key you CHOOSE as the main identifier | StudentID |
| **Alternate Key** | Candidate keys you DIDN'T choose as PK | Email |
| **Foreign Key** | A column that references another table's PK | DeptID → DEPARTMENT(DeptID) |
| **Composite Key** | A key made of 2+ columns | {StudentID, CourseID} in ENROLLMENT |

### Good Primary Key Test — The 3-Question Test

| Question | Required Answer | Why |
|---|---|---|
| **Unique?** | YES — no two rows share the same value | PK must identify each row uniquely |
| **Not Null?** | YES — always has a value | Entity integrity: PK cannot be NULL |
| **Stable?** | YES — rarely/never changes after creation | FK references would break if PK changes |

### Why Common Choices Are BAD Primary Keys

| Column | Unique? | Not Null? | Stable? | Verdict | Why It Fails |
|---|---|---|---|---|---|
| **Name** | ❌ | ✅ | ❌ | BAD | "Rahul Sharma" — duplicates guaranteed. Names also change (marriage). |
| **Phone** | ❌ | ❌ | ❌ | BAD | People change numbers, share numbers, have no number |
| **Email** | ✅ (usually) | ❌ | ❌ | RISKY | People change emails. Some people have no email. |
| **Aadhaar** | ✅ | ❌ | ✅ | RISKY | Not everyone has Aadhaar. Privacy concerns — sensitive data. |
| **Auto-increment INT** | ✅ | ✅ | ✅ | BEST | System-generated, never changes, guaranteed unique |
| **UUID** | ✅ | ✅ | ✅ | GOOD | Works across distributed systems, no collisions |

---

### ✅ Solved: ENROLLMENT Table Analysis [Past Paper Pattern]

**Table: ENROLLMENT(StudentID, CourseID, EnrollDate, Grade, StudentName, CourseName)**

**Question:** Identify the PK and any partial dependencies.

**Answer:**
- **PK = {StudentID, CourseID}** — need both to identify a unique enrollment
- **Partial Dependencies:**
  - StudentID → StudentName (depends on part of PK only) — violates 2NF
  - CourseID → CourseName (depends on part of PK only) — violates 2NF
- **Full Dependencies:**
  - {StudentID, CourseID} → Grade, EnrollDate — these are fine

---

### 🔄 Practice Problem 4: Is BookingRef a Good PK?

**Scenario:** An airline uses `BookingRef` (e.g., "PNR6X4K2") as PK for a BOOKING table. A single booking can have multiple passengers (a family booking for 4 people). Is BookingRef a good PK for a PASSENGER table?

<details>
<summary>Answer</summary>

**No, BookingRef is NOT a good PK for a PASSENGER table.**

| Test | Result | Reason |
|---|---|---|
| Unique? | ❌ FAIL | Multiple passengers share the same BookingRef (family booking) |
| Not Null? | ✅ | Every passenger has a booking |
| Stable? | ✅ | BookingRef doesn't change |

**Better PK options:**
1. **{BookingRef, PassengerSeqNo}** — composite key
2. **Ticket_Number** — unique per passenger (if airline assigns it)
3. **Auto-generated PassengerID** — system-generated surrogate key

BookingRef works as a PK for the BOOKING table (one ref per booking), but NOT for the PASSENGER table (one booking can have multiple passengers).
</details>

---

## TOPIC 4: ACID Properties & Transactions (⭐⭐)

> **Time: ~10 minutes**
> Know the 4 properties, transaction states, SQL command categories, and join types.

### ACID Properties — The Exam Table

| Property | Meaning | Real-World Analogy | How DBMS Enforces It |
|---|---|---|---|
| **Atomicity** | All or nothing — either ALL operations complete, or NONE do | ATM: Either money is debited AND dispensed, or neither happens | Undo log + transaction manager |
| **Consistency** | DB goes from one valid state to another valid state | Bank: Total money before = total money after a transfer | Constraint checking at commit |
| **Isolation** | Concurrent transactions don't interfere with each other | Two people booking the same last train ticket — only one gets it | Locks, MVCC |
| **Durability** | Once committed, changes survive any crash | Your Paytm payment goes through, even if server crashes 1 second later | Write-Ahead Log (WAL) flushed to disk |

### Transaction States Diagram

```
Active ──(all ops succeed)──→ Partially Committed ──(flush OK)──→ Committed ✅
  │                                │
(op fails)                    (flush fails)
  ↓                                ↓
Failed ────────────────────→ Aborted (rollback done) 🔄
                             [restart or kill transaction]
```

| State | Meaning |
|---|---|
| **Active** | Transaction is running |
| **Partially Committed** | Last operation done, waiting for disk confirmation |
| **Committed** | All changes permanent. Done. ✅ |
| **Failed** | Something went wrong (constraint violation, crash, deadlock) |
| **Aborted** | Rolled back. Database restored to before-transaction state |

### SQL Command Categories — Quick Table

| Category | Stands For | Commands | What They Do |
|---|---|---|---|
| **DDL** | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE | Define/modify table structure |
| **DML** | Data Manipulation Language | SELECT, INSERT, UPDATE, DELETE | Query/modify data |
| **DCL** | Data Control Language | GRANT, REVOKE | Manage permissions |
| **TCL** | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT | Manage transactions |

### DELETE vs TRUNCATE vs DROP

| Command | Removes | Table structure? | Can rollback? | Triggers? |
|---|---|---|---|---|
| DELETE | Specified rows | Kept | Yes | Yes |
| TRUNCATE | All rows (fast) | Kept | No | No |
| DROP | Everything | Gone | No | N/A |

### Join Types — One-Liner Each

| Join | Returns | Flipkart Example |
|---|---|---|
| **INNER JOIN** | Only matching rows from both tables | Products that have been ordered |
| **LEFT JOIN** | All left + matching right (NULL if no match) | All products, with order info if ordered |
| **RIGHT JOIN** | All right + matching left | All orders, with product info if available |
| **FULL OUTER JOIN** | All from both sides, NULL where no match | All products AND all orders |
| **CROSS JOIN** | Every combination (Cartesian product) | Every product × every colour = all combos |
| **SELF JOIN** | Table joined with itself | Employee and their manager (same table) |

---

# ⏰ HOUR 2 — Core Topics

---

## TOPIC 5: MongoDB Queries (⭐⭐⭐)

> **Time: ~20 minutes**
> Expect 1-2 MongoDB questions. Know find(), aggregate(), and updateMany().

### MongoDB ↔ SQL Mapping

| SQL | MongoDB | Example |
|---|---|---|
| Table | Collection | `db.orders` |
| Row | Document | `{ name: "Amit", age: 25 }` |
| Column | Field | `name`, `age` |
| SELECT | find() | `db.orders.find({})` |
| WHERE | find({condition}) | `db.orders.find({ status: "shipped" })` |
| INSERT | insertOne/insertMany | `db.orders.insertOne({ ... })` |
| UPDATE | updateOne/updateMany | `db.orders.updateMany({ ... }, { $set: { ... } })` |
| DELETE | deleteOne/deleteMany | `db.orders.deleteMany({ status: "cancelled" })` |
| GROUP BY | $group (aggregation) | `aggregate([{ $group: { _id: "$dept" } }])` |
| JOIN | $lookup (aggregation) | `aggregate([{ $lookup: { ... } }])` |

### find() — Query Operators

```javascript
// Comparison
db.orders.find({ price: { $gt: 500 } })       // greater than
db.orders.find({ price: { $lt: 100 } })       // less than
db.orders.find({ price: { $gte: 200, $lte: 1000 } })  // between 200 and 1000

// Set membership (like SQL IN)
db.orders.find({ status: { $in: ["shipped", "delivered"] } })

// Pattern matching (like SQL LIKE)
db.students.find({ name: { $regex: "^A" } })   // names starting with A

// Nested field query (dot notation)
db.students.find({ "address.city": "Mumbai" })

// Array field
db.students.find({ courses: { $size: 5 } })    // exactly 5 courses

// Logical operators
db.orders.find({ $or: [{ status: "shipped" }, { price: { $gt: 5000 } }] })
db.orders.find({ $and: [{ status: "pending" }, { qty: { $gt: 10 } }] })
```

### Aggregation Pipeline — The Power Tool

Think of it as a factory assembly line. Data flows through stages, each stage transforms it:

```
Collection → $match → $group → $sort → $limit → Result
             (WHERE)  (GROUP BY) (ORDER BY) (LIMIT)
```

| Stage | SQL Equivalent | What It Does |
|---|---|---|
| `$match` | WHERE | Filter documents |
| `$group` | GROUP BY | Group and aggregate |
| `$sort` | ORDER BY | Sort results |
| `$limit` | LIMIT | Restrict count |
| `$project` | SELECT | Choose/rename fields |
| `$unwind` | — | Flatten arrays (one doc per array element) |
| `$lookup` | JOIN | Join with another collection |

### updateMany — Modification Operators

```javascript
db.orders.updateMany(
  { status: "pending" },           // filter: which documents
  {
    $set: { status: "processing" }, // set field value
    $inc: { version: 1 },           // increment by 1
    $push: { history: "updated" },  // add to array
    $pull: { tags: "old" }          // remove from array
  }
)
```

---

### ✅ Solved: Orders Collection Queries [Past Paper]

**Collection: `orders`**
```json
{ "order_id": "O1", "customer_name": "Rahul", "product": "Laptop",
  "quantity": 2, "price": 50000, "date": ISODate("2024-01-15"), "status": "shipped" }
```

**(a) Find all shipped orders with quantity > 5:**
```javascript
db.orders.find({
  status: "shipped",
  quantity: { $gt: 5 }
})
```

**(b) Total revenue by product (sorted descending):**
```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$product",
      totalRevenue: { $sum: { $multiply: ["$quantity", "$price"] } }
    }
  },
  { $sort: { totalRevenue: -1 } }
])
```

---

### ✅ Solved: Average CGPA Per Department [Past Paper]

**Collection: `students`**
```json
{ "roll_no": "2023CS001", "name": "Amit", "department": "CSE", "cgpa": 8.5 }
```

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$department",
      avgCGPA: { $avg: "$cgpa" },
      count: { $sum: 1 }
    }
  },
  { $sort: { avgCGPA: -1 } }
])
```

**Output:**
```json
{ "_id": "CSE",  "avgCGPA": 8.2, "count": 150 }
{ "_id": "ECE",  "avgCGPA": 7.8, "count": 120 }
{ "_id": "MECH", "avgCGPA": 7.5, "count": 100 }
```

---

### 🔄 Practice Problem 5: MongoDB Queries

**Collection: `products`**
```json
{ "name": "iPhone 15", "category": "Electronics", "price": 79999,
  "stock": 50, "ratings": [4.5, 4.8, 3.9, 4.2], "seller": "Flipkart" }
```

**Q1:** Find all Electronics products priced between ₹10,000 and ₹50,000 with stock > 0.

<details>
<summary>Answer</summary>

```javascript
db.products.find({
  category: "Electronics",
  price: { $gte: 10000, $lte: 50000 },
  stock: { $gt: 0 }
})
```
</details>

**Q2:** Find the average price and total stock per category, sorted by average price descending. Only show categories with more than 10 products.

<details>
<summary>Answer</summary>

```javascript
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      avgPrice: { $avg: "$price" },
      totalStock: { $sum: "$stock" },
      productCount: { $sum: 1 }
    }
  },
  { $match: { productCount: { $gt: 10 } } },
  { $sort: { avgPrice: -1 } }
])
```

Note: `$match` AFTER `$group` filters groups (like HAVING in SQL). `$match` BEFORE `$group` filters documents (like WHERE).
</details>

---

## TOPIC 6: Redis Commands & Patterns (⭐⭐)

> **Time: ~15 minutes**
> Know the 5 data structures and 3-4 use case patterns.

### 5 Redis Data Structures

| Structure | Commands | Use Case | Example |
|---|---|---|---|
| **String** | `SET key val`, `GET key`, `INCR key` | Caching, counters | `SET user:1:name "Amit"` → `GET user:1:name` → "Amit" |
| **Hash** | `HSET key field val`, `HGET key field`, `HGETALL key` | Object storage (like a row) | `HSET user:1 name "Amit" age 25` |
| **List** | `LPUSH key val`, `RPUSH key val`, `LRANGE key 0 -1` | Queues, recent items | `LPUSH notifications "New order"` |
| **Set** | `SADD key val`, `SMEMBERS key`, `SINTER k1 k2` | Tags, unique items, intersections | `SADD user:1:skills "Python" "SQL"` |
| **Sorted Set** | `ZADD key score val`, `ZREVRANGE key 0 N`, `ZREVRANK key val` | Leaderboards, rankings | `ZADD leaderboard 100 "Amit"` |

### TTL (Time-To-Live) — Auto-Expiry

```
SET session:abc123 "user_data"    -- set a key
EXPIRE session:abc123 3600        -- expires in 3600 seconds (1 hour)

SETEX session:abc123 3600 "user_data"   -- SET + EXPIRE in one command

TTL session:abc123                -- check remaining time (-1 = no expiry, -2 = expired)
```

### Pattern 1: Session Management (Hash + EXPIRE)

```
-- User logs in → create session
HSET session:abc123 user_id "U1" name "Amit" role "admin" login_time "2024-01-15T10:00"
EXPIRE session:abc123 1800          -- 30 min timeout

-- Check session on each request
HGETALL session:abc123              -- returns all fields

-- User activity → reset timeout
EXPIRE session:abc123 1800          -- reset to 30 min

-- Logout → delete
DEL session:abc123
```

### Pattern 2: Leaderboard (Sorted Set)

```
-- Players score points
ZADD game:leaderboard 250 "Amit"
ZADD game:leaderboard 340 "Priya"
ZADD game:leaderboard 180 "Rahul"
ZADD game:leaderboard 340 "Neha"      -- same score as Priya

-- Top 3 players (descending by score)
ZREVRANGE game:leaderboard 0 2 WITHSCORES
-- Result: "Priya" 340, "Neha" 340, "Amit" 250

-- Amit's rank (0-indexed)
ZREVRANK game:leaderboard "Amit"
-- Result: 2

-- Update score (increment)
ZINCRBY game:leaderboard 200 "Rahul"
-- Rahul now has 380, jumps to rank 0
```

### Pattern 3: Rate Limiter (String + INCR + EXPIRE)

```
-- User makes API request
SET ratelimit:user:1 0 EX 60 NX     -- create counter, expires in 60s, only if not exists
INCR ratelimit:user:1                -- increment on each request
GET ratelimit:user:1                 -- check count

-- If count > 100 → reject request (100 requests/minute limit)
```

### Pattern 4: Caching (String + EX + DEL)

```
-- Cache a database query result
SET cache:product:42 '{"name":"Laptop","price":50000}' EX 300   -- cache for 5 min

-- Read: check cache first
GET cache:product:42
-- If exists → return cached value (cache HIT)
-- If NULL → query DB, then SET cache again (cache MISS)

-- On product update → invalidate cache
DEL cache:product:42
```

---

### ✅ Solved: Implement Leaderboard [Past Paper Pattern]

**Question:** Implement a gaming leaderboard using Redis. Show commands for: adding players, getting top 5, getting a player's rank, updating scores.

```
-- Add players with scores
ZADD leaderboard 1500 "Amit"
ZADD leaderboard 2200 "Priya"
ZADD leaderboard 1800 "Rahul"
ZADD leaderboard 2200 "Neha"
ZADD leaderboard 900  "Vikram"
ZADD leaderboard 3100 "Deepa"

-- Top 5 (descending)
ZREVRANGE leaderboard 0 4 WITHSCORES
-- "Deepa" 3100, "Priya" 2200, "Neha" 2200, "Rahul" 1800, "Amit" 1500

-- Amit's rank (0-indexed from top)
ZREVRANK leaderboard "Amit"
-- 4

-- Amit wins a match, add 500 points
ZINCRBY leaderboard 500 "Amit"
-- Amit now 2000

-- Amit's new rank
ZREVRANK leaderboard "Amit"
-- 3 (moved up!)

-- Total number of players
ZCARD leaderboard
-- 6
```

---

### 🔄 Practice Problem 6: Implement Session Management

**Question:** Using Redis, implement user session management for a Swiggy-like app. Show commands for: login (create session), checking if session is valid, extending session on activity, and logout.

<details>
<summary>Answer</summary>

```
-- LOGIN: Create session with user data, 30-minute timeout
HSET session:SwiggySession123 user_id "U5001" name "Priya" city "Mumbai" cart_count "3"
EXPIRE session:SwiggySession123 1800

-- CHECK SESSION: On each API request
EXISTS session:SwiggySession123       -- 1 = valid, 0 = expired
HGETALL session:SwiggySession123      -- get all session data
HGET session:SwiggySession123 city    -- get specific field → "Mumbai"

-- ACTIVITY: User browses menu → reset timeout
EXPIRE session:SwiggySession123 1800

-- UPDATE SESSION: User adds to cart
HSET session:SwiggySession123 cart_count "4"

-- LOGOUT: Delete session
DEL session:SwiggySession123

-- VERIFY LOGOUT:
EXISTS session:SwiggySession123       -- 0 (gone)
```

**Why Hash instead of String?**
- Hash stores multiple fields (user_id, name, city, cart) without serialising/deserialising JSON.
- You can read/update individual fields without fetching the entire session.
- More memory-efficient for structured data.
</details>

---

## TOPIC 7: SQL vs NoSQL Scenarios (⭐⭐⭐)

> **Time: ~15 minutes**
> Expect a scenario question: "Which database type would you recommend? Justify."

### The Decision Matrix — Memorise This Table

| Scenario | Recommend | Specific DB | 3-4 Justification Points |
|---|---|---|---|
| **Banking / Financial** (HDFC, SBI) | SQL (RDBMS) | PostgreSQL, Oracle | ① ACID guarantees (money can't disappear) ② Complex joins (account ↔ customer ↔ branch) ③ Regulatory compliance needs strict schema ④ Transactions must be atomic |
| **Social Media** (Instagram, Twitter) | Document DB | MongoDB | ① Flexible schema (posts, stories, reels all different) ② Horizontal scaling for billions of posts ③ Read-heavy, eventual consistency OK ④ Nested data (post → comments → likes) |
| **IoT / Sensor Data** | Wide-Column | Cassandra, InfluxDB | ① Massive write throughput (millions of sensors) ② Time-series data, append-mostly ③ Availability > consistency (AP in CAP) ④ Horizontal scaling across data centres |
| **Hospital / Healthcare** | SQL (RDBMS) | PostgreSQL, Oracle | ① Data integrity critical (wrong dose = death) ② ACID for prescriptions & billing ③ Regulatory compliance (HIPAA) ④ Complex relationships (patient → doctor → treatment → insurance) |
| **E-Commerce Catalog** (Flipkart) | Document DB | MongoDB | ① Products have varying attributes (phone: RAM/camera, shirt: size/colour) ② Schema-free (add new product types easily) ③ Read-heavy (browsing) ④ Nested data (product → reviews → images) |
| **Caching / Sessions** | In-Memory KV | Redis | ① Sub-millisecond response (in-memory) ② TTL support (sessions expire automatically) ③ Simple key-value access ④ Pub/Sub for real-time notifications |
| **Shopping Cart** | KV / Document | Redis / MongoDB | ① High availability (cart must always work) ② Eventually consistent OK (not financial) ③ TTL (abandoned carts expire) ④ Flexible structure |
| **Graph/Social Network** | Graph DB | Neo4j | ① "Friends of friends" queries are natural ② Graph traversal > multi-table joins ③ Relationship-first data model ④ Pattern matching |

### ACID vs BASE — Comparison Table

| Aspect | ACID | BASE |
|---|---|---|
| **Full Form** | Atomicity, Consistency, Isolation, Durability | Basically Available, Soft state, Eventually consistent |
| **Consistency** | Strong — read always gets latest write | Eventual — replicas converge over time |
| **Availability** | May sacrifice during partitions (CP) | Prioritises — always responds (AP) |
| **Focus** | Correctness first, then performance | Availability first, then consistency |
| **Scaling** | Vertical (scale up) | Horizontal (scale out) |
| **Schema** | Fixed, predefined | Flexible, schema-less |
| **Use Cases** | Banking, healthcare, inventory, payroll | Social media, IoT, shopping carts, analytics |
| **Examples** | PostgreSQL, MySQL, Oracle, SQL Server | MongoDB, Cassandra, DynamoDB, Redis |
| **Locking** | Pessimistic (lock before access) | Optimistic (resolve conflicts later) |
| **Partition Handling** | Block writes to maintain consistency | Accept writes, resolve conflicts later |

### SQL vs MongoDB vs Redis — Pros/Cons

| Database | Advantages | Disadvantages |
|---|---|---|
| **SQL (PostgreSQL)** | ACID compliance, complex queries, joins, mature ecosystem, data integrity | Rigid schema, hard to scale horizontally, expensive for read-heavy |
| **MongoDB** | Flexible schema, horizontal scaling, great for nested data, developer-friendly | No multi-document ACID (until v4.0), no joins (use $lookup), eventual consistency |
| **Redis** | Sub-millisecond speed, TTL, pub/sub, rich data structures | Data size limited by RAM, no complex queries, persistence is secondary |

---

### ✅ Solved: Banking System Scenario [Past Paper]

**Question:** A bank (HDFC) needs a database for managing customer accounts, transactions, and branch information. Should they use SQL or NoSQL? Justify with 5 points and show an example transaction.

**Answer: SQL (RDBMS) — specifically PostgreSQL or Oracle**

**5 Justifications:**

| # | Reason | Explanation |
|---|---|---|
| 1 | **ACID Transactions** | Money transfers MUST be atomic. If ₹10,000 is debited from Account A, it MUST be credited to Account B. Partial completion is unacceptable. |
| 2 | **Data Integrity** | Referential integrity ensures every transaction references a valid account. CHECK constraints ensure balance ≥ 0. |
| 3 | **Complex Queries** | Bank needs: "Monthly statement for customer X across all accounts," "Total deposits per branch," "Fraud detection across accounts" — all require JOINs. |
| 4 | **Regulatory Compliance** | RBI regulations require complete audit trails, strict data formats, and guaranteed consistency. Fixed schemas enforce this. |
| 5 | **Concurrency Control** | Two ATMs withdrawing from the same account simultaneously must be isolated. SQL provides isolation levels (SERIALIZABLE if needed). |

**Example SQL Transaction:**
```sql
BEGIN TRANSACTION;
  -- Check balance first
  SELECT Balance FROM ACCOUNT WHERE Acc_ID = 'A123' FOR UPDATE;
  -- If balance >= 10000:
  UPDATE ACCOUNT SET Balance = Balance - 10000 WHERE Acc_ID = 'A123';
  UPDATE ACCOUNT SET Balance = Balance + 10000 WHERE Acc_ID = 'B456';
  INSERT INTO TRANSACTION_LOG (From_Acc, To_Acc, Amount, Txn_Date)
    VALUES ('A123', 'B456', 10000, CURRENT_TIMESTAMP);
COMMIT;
-- If anything fails → ROLLBACK (all 3 operations undone)
```

---

### 🔄 Practice Problem 7: SQL vs NoSQL Scenarios

**Q1:** A hospital chain (Apollo) is building a new patient management system. They need to store patient records, doctor schedules, prescriptions, billing, and insurance claims. Should they use SQL or NoSQL?

<details>
<summary>Answer</summary>

**SQL (RDBMS) — PostgreSQL or Oracle**

| # | Reason |
|---|---|
| 1 | **Data integrity is life-critical** — wrong prescription dose or allergy info could kill a patient |
| 2 | **ACID transactions** — billing must be atomic (charge patient, update insurance, create invoice) |
| 3 | **Complex relationships** — patient → doctor → prescription → pharmacy → insurance = 5+ table joins |
| 4 | **Regulatory compliance** — HIPAA/Indian health regulations require audit trails and strict data formats |
| 5 | **Structured data** — patient demographics, vitals, lab results all have fixed formats |

**Exception:** For storing unstructured data like X-ray images, MRI scans, or doctor's notes, MongoDB or object storage (S3) can be used alongside the SQL database.
</details>

**Q2:** A startup is building an IoT platform to collect sensor data from 100,000 industrial machines. Each machine sends temperature, pressure, and vibration readings every second. Which database? Justify.

<details>
<summary>Answer</summary>

**NoSQL — Cassandra or a Time-Series DB (InfluxDB/TimescaleDB)**

| # | Reason |
|---|---|
| 1 | **Massive write throughput** — 100,000 machines × 1 reading/sec = 100K writes/sec. Cassandra handles millions of writes/sec |
| 2 | **Time-series data** — readings are append-only, ordered by timestamp. Cassandra/InfluxDB optimized for this pattern |
| 3 | **Horizontal scaling** — add nodes as machines grow. Cassandra scales linearly |
| 4 | **Availability > Consistency (AP)** — missing one reading is tolerable; system being down is not |
| 5 | **No complex joins** — queries are simple: "last 24 hours of machine X" or "average temperature per machine" |
| 6 | **TTL support** — old data auto-expires (keep only last 90 days) |
</details>

---

## TOPIC 8: Distributed DB & Fragmentation (⭐⭐⭐)

> **Time: ~20 minutes**
> Exam asks: "How would you distribute this DB across a cluster? Justify."

### Fragmentation Types — The Big Picture

| Type | What It Splits | Operation | Reconstruct With | When to Use |
|---|---|---|---|---|
| **Horizontal** | Rows (different rows at different sites) | σ (Selection) | UNION | Users in different regions access different rows |
| **Vertical** | Columns (different columns at different sites) | π (Projection) | JOIN on PK | Different departments need different columns |
| **Mixed/Hybrid** | Both rows and columns | σ then π (or vice versa) | UNION + JOIN | Complex distribution needs |

### Fragmentation Rules — Must Satisfy All 3

| Rule | Meaning | Violation Example |
|---|---|---|
| **Completeness** | Every row/column from the original table must appear in at least one fragment | If you forget to include Mumbai orders → data lost |
| **Reconstruction** | You must be able to rebuild the original table from all fragments | Horizontal: UNION all fragments. Vertical: JOIN on PK |
| **Disjointness** | No overlap between fragments (except PK must be in every vertical fragment for JOINs) | Same order appearing in both Mumbai and Delhi fragments |

### Data Allocation Strategies

| Strategy | Description | Pros | Cons |
|---|---|---|---|
| **Non-replicated** | Each fragment stored at exactly 1 site | No storage waste, no consistency issues | If that site fails, data unavailable |
| **Partially replicated** | Some fragments copied to selected sites | Balance of availability and cost | Need to keep replicas in sync |
| **Fully replicated** | Every fragment at every site | Maximum availability, fast local reads | Huge storage cost, slow writes (update everywhere) |

### Replication Topologies

| Topology | How It Works | Write | Read | Conflict | Best For |
|---|---|---|---|---|---|
| **Leader-Follower** | One leader accepts writes, followers replicate | Leader only | Any node | None (single writer) | Read-heavy: news, analytics |
| **Multi-Leader** | Multiple leaders accept writes, sync between them | Multiple leaders | Any node | Yes — need resolution | Multi-region writes: global apps |
| **Leaderless** | Any node accepts reads/writes, quorum-based | Any node | Any node | Yes — resolved by quorum | High availability: Cassandra, DynamoDB |

### Quorum Formula

For N replicas:
- **W** = number of nodes that must confirm a WRITE
- **R** = number of nodes that must confirm a READ
- **Strong consistency:** W + R > N (ensures overlap between read and write sets)

| Configuration | W | R | Trade-off |
|---|---|---|---|
| **Read-optimised** | N | 1 | Slow writes (wait for all), instant reads |
| **Write-optimised** | 1 | N | Instant writes, slow reads (must read all) |
| **Balanced** | (N/2)+1 | (N/2)+1 | E.g., N=3: W=2, R=2 |

**Example:** N=5 replicas. W=3, R=3. W+R=6>5 ✅ Strong consistency.

---

### ✅ Solved: Indian E-Commerce Across 3 Cities [Past Paper Pattern]

**Scenario:** Flipkart has data centres in Mumbai, Delhi, and Bangalore. Design the distributed database for their ORDER and PRODUCT tables.

**Schema:**
```
ORDER (OrderID, CustomerID, ProductID, Quantity, Price, OrderDate, DeliveryCity)
PRODUCT (ProductID, ProductName, Category, Price, Description)
```

**Solution:**

**1. ORDER Table — Horizontal Fragmentation by Region**

```
ORDER_MUMBAI = σ_{DeliveryCity IN ('Mumbai','Pune','Goa')} (ORDER)
  → Stored at Mumbai data centre

ORDER_DELHI = σ_{DeliveryCity IN ('Delhi','Noida','Jaipur')} (ORDER)
  → Stored at Delhi data centre

ORDER_BANGALORE = σ_{DeliveryCity IN ('Bangalore','Chennai','Hyderabad')} (ORDER)
  → Stored at Bangalore data centre
```

**Verify 3 Rules:**
| Rule | Satisfied? | How? |
|---|---|---|
| Completeness | ✅ | Every city falls into exactly one region |
| Reconstruction | ✅ | `ORDER = ORDER_MUMBAI ∪ ORDER_DELHI ∪ ORDER_BANGALORE` |
| Disjointness | ✅ | Each city mapped to exactly one fragment |

**Why horizontal?**
- Orders are accessed by region (Bangalore delivery team doesn't need Delhi orders)
- Reduces network traffic — 80% of queries are local
- Each data centre handles its own region's load

**2. PRODUCT Table — Full Replication**

```
PRODUCT → Fully replicated at ALL 3 sites (Mumbai, Delhi, Bangalore)
```

**Why full replication?**
- PRODUCT is read-heavy (every order lookup needs product info)
- Small table (~few million rows) — storage cost is low
- Rarely updated (product details don't change often)
- Avoids cross-site JOINs (ORDER JOIN PRODUCT is always local)

**3. Allocation Summary**

| Table | Fragmentation | Allocation | Rationale |
|---|---|---|---|
| ORDER | Horizontal (by DeliveryCity) | Non-replicated (each fragment at one site) | Region-specific access pattern |
| PRODUCT | None (keep whole) | Fully replicated (all 3 sites) | Read-heavy, small, rarely updated |

**4. Query Example — "Total revenue for Bangalore this month"**

```sql
-- Executes ONLY at Bangalore data centre (no network traffic!)
SELECT SUM(O.Quantity * O.Price) AS Revenue
FROM ORDER_BANGALORE O
WHERE O.OrderDate >= '2024-01-01'
AND O.OrderDate < '2024-02-01';
```

If PRODUCT wasn't replicated, this query would need to fetch product data from another city — adding network latency.

---

### 🔄 Practice Problem 8: Global Banking Distribution

**Scenario:** HDFC Bank has offices in India (Mumbai HQ), USA (New York), and UK (London). They need to distribute:
- CUSTOMER (CustID, Name, Email, Country, Branch)
- ACCOUNT (AccID, CustID, Balance, AccType, Currency)
- TRANSACTION (TxnID, FromAcc, ToAcc, Amount, TxnDate, TxnType)

Design the fragmentation and allocation strategy. Justify.

<details>
<summary>Answer</summary>

**1. CUSTOMER — Horizontal Fragmentation by Country**
```
CUSTOMER_INDIA  = σ_{Country='India'}(CUSTOMER)    → Mumbai
CUSTOMER_USA    = σ_{Country='USA'}(CUSTOMER)      → New York
CUSTOMER_UK     = σ_{Country='UK'}(CUSTOMER)       → London
```
**Why:** Customers are served by local branches. Indian customers rarely need UK data.

**2. ACCOUNT — Derived Horizontal Fragmentation (follows CUSTOMER)**
```
ACCOUNT_INDIA  = ACCOUNT ⋈ CUSTOMER_INDIA   → Mumbai
ACCOUNT_USA    = ACCOUNT ⋈ CUSTOMER_USA     → New York
ACCOUNT_UK     = ACCOUNT ⋈ CUSTOMER_UK      → London
```
**Why:** Accounts follow their customer's location. Mumbai branch manages Indian accounts.

**3. TRANSACTION — Horizontal Fragmentation by Date + Partial Replication**
```
-- Recent transactions (last 90 days): at all 3 sites for cross-border transfers
-- Historical transactions: partitioned by originating account's location
TRANSACTION_RECENT → Partially replicated (all sites)
TRANSACTION_ARCHIVE_INDIA → Mumbai only
TRANSACTION_ARCHIVE_USA   → New York only
TRANSACTION_ARCHIVE_UK    → London only
```
**Why:** Recent transactions needed globally (cross-border transfers). Old transactions needed only for audits at the originating country.

**4. Allocation Summary**
| Table | Fragmentation | Allocation |
|---|---|---|
| CUSTOMER | Horizontal (Country) | Non-replicated |
| ACCOUNT | Derived horizontal (follows CUSTOMER) | Non-replicated |
| TRANSACTION (recent) | None | Partially replicated (all sites) |
| TRANSACTION (archive) | Horizontal (origin country) | Non-replicated |

**5. Why this works for banking:**
- **ACID compliance** maintained at each site (local transactions)
- **Cross-border transfers** use 2PC protocol across sites
- **Regulatory compliance** — Indian customer data stays in India (data sovereignty)
- **Low latency** — Mumbai customers hit Mumbai server
- **Disaster recovery** — partially replicated recent transactions survive single-site failure
</details>

---

# ⏰ HOUR 3 — Remaining Topics + Revision

---

## TOPIC 9: 2PC Protocol & CAP Theorem (⭐⭐)

> **Time: ~20 minutes**
> Key exam topics: 2PC steps, CAP trade-offs, ACID vs BASE when-to-use.

### Two-Phase Commit (2PC) — Step by Step

**The Problem:** In a distributed transaction, Site A debits ₹10,000, Site B credits ₹10,000. What if Site A commits but Site B crashes? Money disappears! 2PC prevents this.

**Phase 1 — PREPARE (Voting)**
```
Coordinator → sends PREPARE to ALL participants
Each Participant:
  - Can I commit? (enough resources? constraints OK? log written?)
  - YES → writes to log, sends VOTE YES (promise to commit)
  - NO  → sends VOTE NO, aborts locally
```

**Phase 2 — DECISION (Commit/Abort)**
```
Coordinator collects all votes:
  ALL voted YES → sends GLOBAL COMMIT to all → each commits → sends ACK
  ANY voted NO  → sends GLOBAL ABORT to all  → each rolls back → sends ACK
```

**2PC Flow Diagram:**
```
                Coordinator                    Participants
                    │                              │
        Phase 1:    │──── PREPARE ────────────────→│
        (Voting)    │                              │ (check locally)
                    │←─── VOTE YES / VOTE NO ─────│
                    │                              │
        Phase 2:    │──── COMMIT or ABORT ────────→│
        (Decision)  │                              │ (apply decision)
                    │←─── ACK ────────────────────│
                    │                              │
                  DONE                           DONE
```

### 2PC Limitations — Exam Loves These

| Limitation | Explanation |
|---|---|
| **Blocking** | If coordinator crashes after Phase 1 (votes collected, decision not sent), participants are STUCK. They voted YES but don't know the decision. They hold locks and block other transactions. |
| **Coordinator = Single Point of Failure** | If coordinator dies, entire protocol stalls |
| **Communication overhead** | Requires 4N messages (N = participants): N PREPARE + N VOTE + N DECISION + N ACK |
| **Scalability** | More participants = more messages = slower. Doesn't scale well beyond a data centre |
| **Latency** | 2 network round-trips minimum (even when everything goes right) |

### CAP Theorem — The Trade-Off Triangle

**CAP = Consistency + Availability + Partition Tolerance**

You can guarantee **at most 2 out of 3** in a distributed system.

| Property | Meaning | Simple Explanation |
|---|---|---|
| **Consistency (C)** | Every read gets the latest write | All nodes see the same data at the same time |
| **Availability (A)** | Every request gets a response | System always responds, even if some nodes are down |
| **Partition Tolerance (P)** | System works despite network splits | Communication breaks between nodes, system keeps running |

### The Key Insight: P is Mandatory

In any real distributed system, network partitions WILL happen (cables break, routers fail). So **P is not optional**. You must choose:

```
         C
        / \
       /   \
      /     \
     CP     CA  ← only works on single server (not truly distributed)
    /         \
   P ───AP─── A
```

| Choice | Sacrifice | Behaviour During Partition | Examples |
|---|---|---|---|
| **CP** | Availability | Rejects requests to maintain consistency. "I'd rather give no answer than a wrong answer." | HBase, MongoDB (default), ZooKeeper |
| **AP** | Consistency | Accepts requests but may return stale data. "I'd rather give a potentially stale answer than no answer." | Cassandra, DynamoDB, CouchDB |
| **CA** | Partition Tolerance | Only possible on a single node (no distribution). Not relevant for distributed systems. | Traditional single-server PostgreSQL, MySQL |

### When to Choose ACID vs BASE

| Choose ACID When... | Choose BASE When... |
|---|---|
| Money is involved (banking, payments) | Social media (likes, feeds) |
| Inventory management (stock counts) | IoT sensor data |
| Healthcare (prescriptions, patient records) | Shopping carts |
| Correctness > availability | Availability > correctness |
| Regulatory compliance required | Scale is massive (millions of concurrent users) |
| Data relationships are complex (many joins) | Data is simple (key-value, documents) |

---

### ✅ Solved: Why Does Zomato's Order System Need 2PC? [Practice]

**Scenario:** When a Zomato order is placed:
1. Payment Service (Site A) charges the customer ₹500
2. Restaurant Service (Site B) confirms the order
3. Delivery Service (Site C) assigns a rider

**Why 2PC?**
- All 3 must succeed together. If payment succeeds but restaurant rejects (closed), customer must be refunded atomically.
- Without 2PC, you might charge the customer but not place the order → unhappy customer.

**2PC Trace:**
```
Phase 1: Coordinator sends PREPARE to Payment, Restaurant, Delivery
  - Payment: "Can charge ₹500" → VOTE YES
  - Restaurant: "Order accepted" → VOTE YES
  - Delivery: "Rider assigned" → VOTE YES

Phase 2: All YES → Coordinator sends GLOBAL COMMIT
  - Payment: ₹500 charged ✅
  - Restaurant: Order confirmed ✅
  - Delivery: Rider dispatched ✅
  - All send ACK → DONE

If Restaurant had voted NO (kitchen closed):
  Phase 2: Coordinator sends GLOBAL ABORT
  - Payment: Refund ₹500 ✅
  - Restaurant: Order cancelled ✅
  - Delivery: Release rider ✅
```

---

## TOPIC 10: ER Model (⭐)

> **Time: ~10 minutes**
> Quick overview — know the symbols and cardinality notation.

### ER Diagram Symbols — Quick Reference

| Concept | Symbol | Example |
|---|---|---|
| **Entity** | Rectangle | STUDENT, EMPLOYEE |
| **Weak Entity** | Double Rectangle | DEPENDENT (depends on EMPLOYEE) |
| **Attribute** | Oval | Name, Salary |
| **Key Attribute** | Underlined Oval | <u>StudentID</u> |
| **Multivalued Attribute** | Double Oval | Phone Numbers (a person has many) |
| **Derived Attribute** | Dashed Oval | Age (derived from DOB) |
| **Composite Attribute** | Oval with sub-ovals | Address → {Street, City, State, PIN} |
| **Relationship** | Diamond | "works_in", "enrolls_in" |
| **Total Participation** | Double Line | Every EMPLOYEE must be in a DEPARTMENT |
| **Partial Participation** | Single Line | Not every EMPLOYEE manages a DEPARTMENT |

### Cardinality Ratios

| Ratio | Meaning | Example |
|---|---|---|
| **1:1** | One to one | One EMPLOYEE manages one DEPARTMENT |
| **1:N** | One to many | One DEPARTMENT has many EMPLOYEEs |
| **M:N** | Many to many | A STUDENT takes many COURSEs; a COURSE has many STUDENTs |

### ER-to-Relational Mapping Rules

| ER Construct | Relational Table Rule |
|---|---|
| Strong entity | One table, PK = key attribute |
| Weak entity | One table, PK = own partial key + owner's PK, FK to owner |
| 1:1 Relationship | Add FK to either side (preferably the one with total participation) |
| 1:N Relationship | Add FK to the "N" side table |
| M:N Relationship | Create NEW junction table with PKs from both sides |
| Multivalued attribute | Create separate table with FK to original entity |

### Quick Example: University ER

```
STUDENT (StudentID PK, Name, DOB)
COURSE (CourseID PK, CourseName, Credits)
DEPARTMENT (DeptID PK, DeptName)

Relationships:
- STUDENT enrolls_in COURSE (M:N) → ENROLLMENT(StudentID FK, CourseID FK, Grade) PK={StudentID,CourseID}
- DEPARTMENT offers COURSE (1:N) → Add DeptID FK to COURSE table
- STUDENT belongs_to DEPARTMENT (N:1) → Add DeptID FK to STUDENT table
```

---

## TOPIC 11: Quick Revision — Last 15 Minutes

> **Time: ~15 minutes**
> Scan these tables right before entering the exam hall.

### 🔢 Key Numbers to Remember

| Number | What |
|---|---|
| 7 | File system problems (redundancy, inconsistency, access difficulty, isolation, integrity, atomicity, concurrency) |
| 7 | DBMS advantages (independence, reduced redundancy, sharing, integrity, security, recovery, concurrency) |
| 4 | ACID properties |
| 3 | BASE properties |
| 4 | SQL sub-languages (DDL, DML, DCL, TCL) |
| 6 | Key types (super, candidate, primary, alternate, foreign, composite) |
| 3 | Fragmentation types (horizontal, vertical, mixed) |
| 3 | Fragmentation rules (completeness, reconstruction, disjointness) |
| 3 | Allocation strategies (non-replicated, partially replicated, fully replicated) |
| 3 | Replication topologies (leader-follower, multi-leader, leaderless) |
| 5 | Transaction states (active, partially committed, committed, failed, aborted) |
| 5 | Aggregate functions (COUNT, SUM, AVG, MIN, MAX) |
| 6 | Join types (INNER, LEFT, RIGHT, FULL OUTER, CROSS, SELF) |
| 5 | Redis data structures (String, Hash, List, Set, Sorted Set) |

### 📋 Normalisation Quick Identification

| You See This... | It's This Violation | Normal Form Broken |
|---|---|---|
| Comma-separated values in a cell | Repeating groups / non-atomic | Not in 1NF |
| Non-key depends on PART of composite PK | Partial dependency | Not in 2NF |
| Non-key → another non-key | Transitive dependency | Not in 3NF |
| Determinant is not a candidate key | Non-key determinant | Not in BCNF |
| A →→ B (multivalued dependency) | Multi-valued dependency | Not in 4NF |

### 🎯 SQL Query Pattern Recognition

| Trigger Words in Question | SQL Pattern to Use |
|---|---|
| "more than their manager" | Self JOIN + WHERE comparison |
| "departments with more than N employees" | GROUP BY + HAVING COUNT(*) > N |
| "above average salary" | Subquery in WHERE: `> (SELECT AVG...)` or correlated subquery |
| "2nd highest", "3rd highest" | DENSE_RANK() OVER (ORDER BY ... DESC) or correlated subquery |
| "employees who work on EVERY project" | Double NOT EXISTS (relational division) |
| "not in any project" | LEFT JOIN + IS NULL or NOT IN / NOT EXISTS |
| "total/sum/average per group" | GROUP BY + aggregate function |
| "top N per category" | DENSE_RANK() OVER (PARTITION BY ... ORDER BY ...) |
| "employees and their department names" | INNER JOIN or LEFT JOIN |
| "all employees even without department" | LEFT JOIN |

### 🔄 SQL ↔ MongoDB Equivalents — Top 10

| SQL | MongoDB |
|---|---|
| `SELECT * FROM orders WHERE status='shipped'` | `db.orders.find({ status: "shipped" })` |
| `SELECT * FROM orders WHERE price > 500` | `db.orders.find({ price: { $gt: 500 } })` |
| `SELECT name, price FROM products` | `db.products.find({}, { name: 1, price: 1, _id: 0 })` |
| `SELECT COUNT(*) FROM orders GROUP BY product` | `db.orders.aggregate([{ $group: { _id: "$product", count: { $sum: 1 } } }])` |
| `SELECT * FROM orders ORDER BY date DESC LIMIT 5` | `db.orders.find().sort({ date: -1 }).limit(5)` |
| `SELECT AVG(salary) FROM employees GROUP BY dept` | `db.employees.aggregate([{ $group: { _id: "$dept", avg: { $avg: "$salary" } } }])` |
| `UPDATE orders SET status='shipped' WHERE order_id='O1'` | `db.orders.updateOne({ order_id: "O1" }, { $set: { status: "shipped" } })` |
| `DELETE FROM orders WHERE status='cancelled'` | `db.orders.deleteMany({ status: "cancelled" })` |
| `INSERT INTO orders VALUES (...)` | `db.orders.insertOne({ ... })` |
| `SELECT * FROM orders WHERE status IN ('shipped','delivered')` | `db.orders.find({ status: { $in: ["shipped", "delivered"] } })` |

### ✅❌ TRUE/FALSE Quick Facts

| # | Statement | T/F | Quick Reason |
|---|---|---|---|
| 1 | A table can have multiple candidate keys | TRUE | e.g., StudentID and Email |
| 2 | A primary key can contain NULL values | FALSE | Entity integrity: PK is always NOT NULL |
| 3 | Foreign key values must always match a PK in the referenced table | FALSE | FK can be NULL (if no match needed) |
| 4 | TRUNCATE can be rolled back | FALSE | TRUNCATE is DDL, not logged per-row |
| 5 | In 2PC, if coordinator crashes after voting, participants are blocked | TRUE | This is 2PC's main weakness |
| 6 | CAP theorem says you can have all 3 (C, A, P) | FALSE | At most 2 during a partition |
| 7 | MongoDB supports ACID transactions | TRUE (since v4.0) | Multi-document ACID added in MongoDB 4.0 |
| 8 | Redis stores data on disk by default | FALSE | Redis is in-memory (disk is for persistence/backup) |
| 9 | Every conflict-serializable schedule is view-serializable | TRUE | Conflict serializability is stricter |
| 10 | In BCNF, every determinant is a superkey | TRUE | That's the definition of BCNF |
| 11 | 3NF decomposition always preserves all FDs | TRUE | 3NF guarantees dependency preservation |
| 12 | BCNF decomposition always preserves all FDs | FALSE | BCNF may lose some dependencies |

### 📐 All Formulas in One Place

| Formula / Rule | Expression |
|---|---|
| **Quorum (Strong Consistency)** | W + R > N |
| **Read-optimised Quorum** | W = N, R = 1 |
| **Write-optimised Quorum** | W = 1, R = N |
| **Balanced Quorum** | W = R = ⌊N/2⌋ + 1 |
| **2PC Messages** | 4N messages total (N = number of participants) |
| **Binary Search (index)** | ⌈log₂(blocks)⌉ accesses |
| **B-Tree Data Pointers** | At every level (internal + leaf) |
| **B+ Tree Data Pointers** | Only at leaf level |
| **1NF** | Atomic values, no repeating groups |
| **2NF** | 1NF + no partial dependency |
| **3NF** | 2NF + no transitive dependency |
| **BCNF** | Every determinant is a superkey |
| **Horizontal Fragmentation** | σ (selection) → UNION to reconstruct |
| **Vertical Fragmentation** | π (projection) → JOIN on PK to reconstruct |
| **Armstrong's Axioms** | Reflexivity, Augmentation, Transitivity |
| **Derived Rules** | Decomposition, Union, Pseudotransitivity |
| **CAP During Partition** | Choose CP (consistency) or AP (availability) |
| **ACID** | Atomicity, Consistency, Isolation, Durability |
| **BASE** | Basically Available, Soft state, Eventually consistent |

### 🧠 Last-Minute Memory Hooks

| Topic | Memory Hook |
|---|---|
| Normalisation | "Key, whole key, nothing but the key — so help me Codd" |
| ACID | "All Changes In Database" — Atomicity, Consistency, Isolation, Durability |
| BASE | "BaSE" — Basically available, Soft state, Eventually consistent |
| CAP | "Choose 2 of 3, but P is mandatory → CP or AP" |
| 2PC | "PREPARE → VOTE → DECIDE → ACK" (ask, answer, decide, confirm) |
| Horizontal Frag | "Split ROWS (σ), rebuild with UNION" — like cutting a pizza horizontally |
| Vertical Frag | "Split COLUMNS (π), rebuild with JOIN" — like cutting a pizza vertically |
| Redis Sorted Set | "Leaderboard" — ZADD to add, ZREVRANGE for top-N |
| MongoDB Aggregation | "match-group-sort-limit" = "WHERE-GROUPBY-ORDERBY-LIMIT" |
| SQL vs NoSQL | "Money → SQL, Social Media → MongoDB, Speed → Redis" |
| Joins | "INNER = both match, LEFT = all left + match right, CROSS = everything × everything" |
| Window Functions | "ROW_NUMBER never ties, RANK skips, DENSE_RANK doesn't" |

---

> **You've covered everything in 2–3 hours. Take a deep breath. You've got this. 💪**
> 
> **Exam Strategy:**
> 1. Start with SQL queries (they're the most marks — 7-10)
> 2. Then do Normalisation (most tested)
> 3. Then MongoDB/Redis queries (practice the syntax)
> 4. Then Distributed DB (fragmentation + justify)
> 5. Leave scenario-based (SQL vs NoSQL) and PK analysis for last (these are opinion + logic)

---

*Created for SSZG507 Mid-Sem • Sessions 1–4 • Good luck!* 🎓
