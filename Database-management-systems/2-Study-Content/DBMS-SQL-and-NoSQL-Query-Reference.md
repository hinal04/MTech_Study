# DBMS — SQL & NoSQL Query Reference

> **Complete reference for exam preparation**
> SQL (DDL / DML / Joins / Subqueries / Aggregation) + MongoDB + Redis

---

## Sample Schema (Used Throughout This Document)

```
┌──────────────────────────────────────────────────────────┐
│  EMPLOYEE                                                │
│  EmpID INT PK | Name VARCHAR | Salary DECIMAL            │
│  DeptID INT FK → DEPARTMENT | ManagerID INT | JoinDate   │
├──────────────────────────────────────────────────────────┤
│  DEPARTMENT                                              │
│  DeptID INT PK | DeptName VARCHAR | Location VARCHAR     │
│  Budget DECIMAL                                          │
├──────────────────────────────────────────────────────────┤
│  PROJECT                                                 │
│  ProjectID INT PK | ProjectName VARCHAR                  │
│  DeptID INT FK → DEPARTMENT | StartDate DATE             │
├──────────────────────────────────────────────────────────┤
│  WORKS_ON                                                │
│  EmpID INT FK → EMPLOYEE | ProjectID INT FK → PROJECT    │
│  Hours INT                                               │
└──────────────────────────────────────────────────────────┘
```

**Relationships:**
- EMPLOYEE.DeptID → DEPARTMENT.DeptID (many-to-one)
- EMPLOYEE.ManagerID → EMPLOYEE.EmpID (self-referencing)
- WORKS_ON is a junction table linking EMPLOYEE ↔ PROJECT (many-to-many)
- PROJECT.DeptID → DEPARTMENT.DeptID (many-to-one)

---

# PART A: SQL QUERIES

---

## 1. DDL (Data Definition Language)

DDL commands define and modify the **structure** of database objects. They **auto-commit** — changes cannot be rolled back.

### 1.1 CREATE TABLE

```sql
-- DEPARTMENT table (parent — create first)
CREATE TABLE DEPARTMENT (
    DeptID      INT          PRIMARY KEY,
    DeptName    VARCHAR(50)  NOT NULL UNIQUE,
    Location    VARCHAR(100) DEFAULT 'Head Office',
    Budget      DECIMAL(12,2) CHECK (Budget >= 0)
);

-- EMPLOYEE table (child — references DEPARTMENT)
CREATE TABLE EMPLOYEE (
    EmpID       INT          PRIMARY KEY,
    Name        VARCHAR(100) NOT NULL,
    Salary      DECIMAL(10,2) CHECK (Salary > 0),
    DeptID      INT,
    ManagerID   INT,
    JoinDate    DATE         DEFAULT CURRENT_DATE,
    FOREIGN KEY (DeptID)     REFERENCES DEPARTMENT(DeptID)
        ON DELETE SET NULL ON UPDATE CASCADE,
    FOREIGN KEY (ManagerID)  REFERENCES EMPLOYEE(EmpID)
        ON DELETE SET NULL
);

-- PROJECT table
CREATE TABLE PROJECT (
    ProjectID   INT          PRIMARY KEY,
    ProjectName VARCHAR(100) NOT NULL,
    DeptID      INT,
    StartDate   DATE,
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)
        ON DELETE CASCADE
);

-- WORKS_ON junction table (composite primary key)
CREATE TABLE WORKS_ON (
    EmpID       INT,
    ProjectID   INT,
    Hours       INT          CHECK (Hours >= 0),
    PRIMARY KEY (EmpID, ProjectID),
    FOREIGN KEY (EmpID)     REFERENCES EMPLOYEE(EmpID) ON DELETE CASCADE,
    FOREIGN KEY (ProjectID) REFERENCES PROJECT(ProjectID) ON DELETE CASCADE
);
```

**Constraint Summary:**

| Constraint | Purpose | Example |
|---|---|---|
| `PRIMARY KEY` | Uniquely identifies each row, NOT NULL + UNIQUE | `EmpID INT PRIMARY KEY` |
| `FOREIGN KEY` | Enforces referential integrity between tables | `FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)` |
| `NOT NULL` | Column cannot have NULL values | `Name VARCHAR(100) NOT NULL` |
| `UNIQUE` | All values in column must be distinct (allows one NULL) | `DeptName VARCHAR(50) UNIQUE` |
| `CHECK` | Validates data against a condition | `CHECK (Salary > 0)` |
| `DEFAULT` | Sets a default value when none is provided | `DEFAULT 'Head Office'` |

### 1.2 ALTER TABLE

```sql
-- ADD a new column
ALTER TABLE EMPLOYEE ADD Email VARCHAR(100);

-- DROP a column
ALTER TABLE EMPLOYEE DROP COLUMN Email;

-- MODIFY column data type / constraints
ALTER TABLE EMPLOYEE MODIFY COLUMN Name VARCHAR(150) NOT NULL;
-- (MySQL syntax; PostgreSQL uses ALTER COLUMN ... TYPE ...)

-- ADD a new constraint
ALTER TABLE EMPLOYEE ADD CONSTRAINT chk_salary CHECK (Salary <= 500000);

-- DROP a constraint
ALTER TABLE EMPLOYEE DROP CONSTRAINT chk_salary;

-- RENAME a table
ALTER TABLE EMPLOYEE RENAME TO EMP;

-- RENAME a column (MySQL 8+)
ALTER TABLE EMPLOYEE RENAME COLUMN Name TO FullName;
```

### 1.3 DROP TABLE & TRUNCATE TABLE

```sql
-- DROP TABLE — removes table structure + data permanently
DROP TABLE WORKS_ON;              -- fails if other tables reference it
DROP TABLE IF EXISTS WORKS_ON;    -- safe version, no error if absent

-- TRUNCATE TABLE — removes ALL rows, keeps structure
TRUNCATE TABLE WORKS_ON;
```

**DROP vs TRUNCATE vs DELETE:**

| Feature | DROP | TRUNCATE | DELETE |
|---|---|---|---|
| Removes structure? | ✅ Yes | ❌ No | ❌ No |
| Removes all data? | ✅ Yes | ✅ Yes | Can filter with WHERE |
| Can rollback? | ❌ No (DDL) | ❌ No (DDL) | ✅ Yes (DML) |
| Resets auto-increment? | N/A | ✅ Yes | ❌ No |
| Fires triggers? | ❌ No | ❌ No | ✅ Yes |
| Speed | Fastest | Fast | Slowest (row-by-row) |

### 1.4 CREATE INDEX

```sql
-- Single-column index
CREATE INDEX idx_emp_salary ON EMPLOYEE(Salary);

-- Composite index
CREATE INDEX idx_emp_dept_salary ON EMPLOYEE(DeptID, Salary);

-- Unique index
CREATE UNIQUE INDEX idx_dept_name ON DEPARTMENT(DeptName);

-- Drop an index
DROP INDEX idx_emp_salary;
```

> **When to use indexes:** Columns in WHERE, JOIN, ORDER BY, GROUP BY. Avoid over-indexing — each index slows down INSERT/UPDATE/DELETE.

---

## 2. DML (Data Manipulation Language)

DML commands manipulate **data within tables**. They can be rolled back inside a transaction.

### 2.1 INSERT

```sql
-- Single row
INSERT INTO DEPARTMENT (DeptID, DeptName, Location, Budget)
VALUES (1, 'Engineering', 'Building A', 500000.00);

-- Multiple rows
INSERT INTO DEPARTMENT (DeptID, DeptName, Location, Budget)
VALUES
    (2, 'Marketing',  'Building B', 300000.00),
    (3, 'HR',         'Building C', 200000.00),
    (4, 'Finance',    'Building D', 400000.00);

-- INSERT from SELECT (copy data from another table)
INSERT INTO DEPARTMENT (DeptID, DeptName, Location, Budget)
SELECT DeptID + 100, DeptName, Location, Budget * 0.8
FROM DEPARTMENT
WHERE Budget > 300000;
```

```sql
-- Sample employees
INSERT INTO EMPLOYEE (EmpID, Name, Salary, DeptID, ManagerID, JoinDate)
VALUES
    (101, 'Rahul Sharma',   75000, 1, NULL,  '2020-01-15'),
    (102, 'Priya Singh',    82000, 1, 101,   '2020-06-20'),
    (103, 'Amit Patel',     55000, 2, 101,   '2021-03-10'),
    (104, 'Neha Gupta',     90000, 1, 101,   '2019-08-01'),
    (105, 'Suresh Kumar',   45000, 3, NULL,  '2022-01-05'),
    (106, 'Deepa Iyer',     68000, 2, 103,   '2021-07-15'),
    (107, 'Vijay Reddy',    72000, 4, NULL,  '2020-11-20'),
    (108, 'Anita Desai',    58000, NULL, 107, '2023-02-28');

-- Sample projects
INSERT INTO PROJECT VALUES
    (1001, 'Project Alpha', 1, '2023-01-01'),
    (1002, 'Project Beta',  1, '2023-06-15'),
    (1003, 'Campaign X',    2, '2023-03-01'),
    (1004, 'HR Portal',     3, '2023-09-01');

-- Sample WORKS_ON
INSERT INTO WORKS_ON VALUES
    (101, 1001, 20), (101, 1002, 15),
    (102, 1001, 30), (102, 1002, 10),
    (103, 1003, 25), (104, 1001, 35),
    (104, 1002, 5),  (105, 1004, 40),
    (106, 1003, 20), (107, 1001, 10);
```

### 2.2 UPDATE

```sql
-- Simple UPDATE
UPDATE EMPLOYEE SET Salary = 80000 WHERE EmpID = 103;

-- UPDATE with calculation
UPDATE EMPLOYEE SET Salary = Salary * 1.10 WHERE DeptID = 1;

-- UPDATE with subquery
UPDATE EMPLOYEE
SET Salary = Salary * 1.15
WHERE DeptID = (SELECT DeptID FROM DEPARTMENT WHERE DeptName = 'Engineering');

-- UPDATE multiple columns
UPDATE EMPLOYEE
SET Salary = 95000, DeptID = 2
WHERE EmpID = 104;
```

### 2.3 DELETE

```sql
-- DELETE with WHERE
DELETE FROM EMPLOYEE WHERE EmpID = 108;

-- DELETE with subquery
DELETE FROM EMPLOYEE
WHERE DeptID IN (SELECT DeptID FROM DEPARTMENT WHERE Budget < 250000);

-- DELETE all rows (but keep table)
DELETE FROM WORKS_ON;
```

### 2.4 SELECT Basics

```sql
-- All columns
SELECT * FROM EMPLOYEE;

-- Specific columns with alias
SELECT EmpID AS "Employee ID", Name AS "Full Name", Salary
FROM EMPLOYEE;

-- DISTINCT
SELECT DISTINCT DeptID FROM EMPLOYEE;

-- WHERE filter
SELECT * FROM EMPLOYEE WHERE Salary > 70000;

-- ORDER BY
SELECT * FROM EMPLOYEE ORDER BY Salary DESC;

-- LIMIT / OFFSET
SELECT * FROM EMPLOYEE ORDER BY Salary DESC LIMIT 3;          -- top 3
SELECT * FROM EMPLOYEE ORDER BY Salary DESC LIMIT 3 OFFSET 2; -- skip 2, get 3

-- SQL Server equivalent
SELECT TOP 3 * FROM EMPLOYEE ORDER BY Salary DESC;

-- Calculated columns
SELECT Name, Salary, Salary * 12 AS AnnualSalary FROM EMPLOYEE;
```

---

## 3. Filtering & Sorting

### 3.1 WHERE with Operators

```sql
-- Equality / Inequality
SELECT * FROM EMPLOYEE WHERE DeptID = 1;
SELECT * FROM EMPLOYEE WHERE DeptID <> 1;    -- or !=

-- Comparison
SELECT * FROM EMPLOYEE WHERE Salary > 70000;
SELECT * FROM EMPLOYEE WHERE Salary <= 60000;

-- BETWEEN (inclusive on both ends)
SELECT * FROM EMPLOYEE WHERE Salary BETWEEN 50000 AND 80000;

-- IN
SELECT * FROM EMPLOYEE WHERE DeptID IN (1, 2);

-- LIKE (pattern matching)
SELECT * FROM EMPLOYEE WHERE Name LIKE 'R%';       -- starts with R
SELECT * FROM EMPLOYEE WHERE Name LIKE '%Singh';    -- ends with Singh
SELECT * FROM EMPLOYEE WHERE Name LIKE '%it%';      -- contains 'it'
SELECT * FROM EMPLOYEE WHERE Name LIKE '_____';     -- exactly 5 characters
SELECT * FROM EMPLOYEE WHERE Name LIKE 'A__t%';     -- A, 2 chars, t, then anything

-- IS NULL / IS NOT NULL
SELECT * FROM EMPLOYEE WHERE ManagerID IS NULL;
SELECT * FROM EMPLOYEE WHERE DeptID IS NOT NULL;
```

### 3.2 Logical Operators

```sql
-- AND
SELECT * FROM EMPLOYEE WHERE DeptID = 1 AND Salary > 70000;

-- OR
SELECT * FROM EMPLOYEE WHERE DeptID = 1 OR DeptID = 2;

-- NOT
SELECT * FROM EMPLOYEE WHERE NOT DeptID = 1;
SELECT * FROM EMPLOYEE WHERE DeptID NOT IN (1, 3);
SELECT * FROM EMPLOYEE WHERE Salary NOT BETWEEN 50000 AND 80000;
SELECT * FROM EMPLOYEE WHERE Name NOT LIKE 'A%';

-- Combined (use parentheses for clarity!)
SELECT * FROM EMPLOYEE
WHERE (DeptID = 1 OR DeptID = 2) AND Salary > 60000;
```

### 3.3 ORDER BY

```sql
-- Ascending (default)
SELECT * FROM EMPLOYEE ORDER BY Name ASC;

-- Descending
SELECT * FROM EMPLOYEE ORDER BY Salary DESC;

-- Multiple columns (sort by dept first, then salary within each dept)
SELECT * FROM EMPLOYEE ORDER BY DeptID ASC, Salary DESC;

-- Order by column position (not recommended but valid)
SELECT Name, Salary FROM EMPLOYEE ORDER BY 2 DESC;  -- order by Salary
```

### 3.4 LIMIT / OFFSET for Pagination

```sql
-- Page 1 (rows 1-5)
SELECT * FROM EMPLOYEE ORDER BY EmpID LIMIT 5 OFFSET 0;

-- Page 2 (rows 6-10)
SELECT * FROM EMPLOYEE ORDER BY EmpID LIMIT 5 OFFSET 5;

-- Page N formula: LIMIT page_size OFFSET (page_number - 1) * page_size
```

---

## 4. Aggregate Functions & GROUP BY

### 4.1 Aggregate Functions

```sql
SELECT COUNT(*)          AS TotalEmployees   FROM EMPLOYEE;  -- counts all rows
SELECT COUNT(DeptID)     AS WithDepartment   FROM EMPLOYEE;  -- ignores NULLs
SELECT COUNT(DISTINCT DeptID) AS UniqueDepts FROM EMPLOYEE;

SELECT SUM(Salary) AS TotalSalary FROM EMPLOYEE;
SELECT AVG(Salary) AS AvgSalary   FROM EMPLOYEE;
SELECT MIN(Salary) AS MinSalary   FROM EMPLOYEE;
SELECT MAX(Salary) AS MaxSalary   FROM EMPLOYEE;
```

### 4.2 GROUP BY

```sql
-- Basic GROUP BY
SELECT DeptID, COUNT(*) AS EmpCount
FROM EMPLOYEE
GROUP BY DeptID;

-- GROUP BY with multiple columns
SELECT DeptID, ManagerID, AVG(Salary) AS AvgSalary
FROM EMPLOYEE
GROUP BY DeptID, ManagerID;
```

### 4.3 HAVING (Filter After Grouping)

```sql
SELECT DeptID, AVG(Salary) AS AvgSalary
FROM EMPLOYEE
GROUP BY DeptID
HAVING AVG(Salary) > 60000;
```

### 4.4 WHERE vs HAVING

| Feature | WHERE | HAVING |
|---|---|---|
| Filters | Individual rows **before** grouping | Groups **after** grouping |
| Can use aggregates? | ❌ No | ✅ Yes |
| Execution order | Before GROUP BY | After GROUP BY |
| Example | `WHERE Salary > 50000` | `HAVING AVG(Salary) > 50000` |

**SQL Execution Order:**
```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

### 4.5 Worked Examples

**Example 1: Count employees per department**
```sql
SELECT d.DeptName, COUNT(e.EmpID) AS EmployeeCount
FROM DEPARTMENT d
LEFT JOIN EMPLOYEE e ON d.DeptID = e.DeptID
GROUP BY d.DeptName
ORDER BY EmployeeCount DESC;
```
| DeptName | EmployeeCount |
|---|---|
| Engineering | 3 |
| Marketing | 2 |
| Finance | 1 |
| HR | 1 |

**Example 2: Average salary per department (only departments with avg > 50000)**
```sql
SELECT d.DeptName, ROUND(AVG(e.Salary), 2) AS AvgSalary
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
GROUP BY d.DeptName
HAVING AVG(e.Salary) > 50000
ORDER BY AvgSalary DESC;
```
| DeptName | AvgSalary |
|---|---|
| Engineering | 82333.33 |
| Finance | 72000.00 |
| Marketing | 61500.00 |

**Example 3: Find department with maximum total salary**
```sql
SELECT d.DeptName, SUM(e.Salary) AS TotalSalary
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
GROUP BY d.DeptName
ORDER BY TotalSalary DESC
LIMIT 1;
```
| DeptName | TotalSalary |
|---|---|
| Engineering | 247000 |

**Example 4: Count projects per employee**
```sql
SELECT e.Name, COUNT(w.ProjectID) AS ProjectCount
FROM EMPLOYEE e
LEFT JOIN WORKS_ON w ON e.EmpID = w.EmpID
GROUP BY e.EmpID, e.Name
ORDER BY ProjectCount DESC;
```
| Name | ProjectCount |
|---|---|
| Rahul Sharma | 2 |
| Priya Singh | 2 |
| Neha Gupta | 2 |
| Amit Patel | 1 |
| Suresh Kumar | 1 |
| Deepa Iyer | 1 |
| Vijay Reddy | 1 |
| Anita Desai | 0 |

**Example 5: Find employees working more than average hours**
```sql
SELECT e.Name, SUM(w.Hours) AS TotalHours
FROM EMPLOYEE e
JOIN WORKS_ON w ON e.EmpID = w.EmpID
GROUP BY e.EmpID, e.Name
HAVING SUM(w.Hours) > (SELECT AVG(TotalHrs) FROM (
    SELECT SUM(Hours) AS TotalHrs
    FROM WORKS_ON
    GROUP BY EmpID
) AS sub)
ORDER BY TotalHours DESC;
```
| Name | TotalHours |
|---|---|
| Neha Gupta | 40 |
| Suresh Kumar | 40 |
| Rahul Sharma | 35 |
| Priya Singh | 40 |

---

## 5. Joins

### 5.1 INNER JOIN

**Definition:** Returns only rows that have matching values in **both** tables.

```
Table A        Table B
 ┌───┐         ┌───┐
 │   │▓▓▓▓▓▓▓▓▓│   │   ▓▓▓ = Result (intersection)
 │   │▓▓▓▓▓▓▓▓▓│   │
 └───┘         └───┘
```

```sql
SELECT e.Name, d.DeptName
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
```
| Name | DeptName |
|---|---|
| Rahul Sharma | Engineering |
| Priya Singh | Engineering |
| Amit Patel | Marketing |
| Neha Gupta | Engineering |
| Suresh Kumar | HR |
| Deepa Iyer | Marketing |
| Vijay Reddy | Finance |

> Anita Desai (DeptID = NULL) is excluded because there is no match.

### 5.2 LEFT (OUTER) JOIN

**Definition:** Returns **all rows from the left table** and matching rows from the right table. Non-matching right-side columns are NULL.

```
Table A        Table B
 ┌───┐         ┌───┐
 │▓▓▓│▓▓▓▓▓▓▓▓▓│   │   ▓▓▓ = Result (all of A + matched B)
 │▓▓▓│▓▓▓▓▓▓▓▓▓│   │
 └───┘         └───┘
```

```sql
SELECT e.Name, d.DeptName
FROM EMPLOYEE e
LEFT JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
```
| Name | DeptName |
|---|---|
| Rahul Sharma | Engineering |
| Priya Singh | Engineering |
| Neha Gupta | Engineering |
| Amit Patel | Marketing |
| Deepa Iyer | Marketing |
| Suresh Kumar | HR |
| Vijay Reddy | Finance |
| **Anita Desai** | **NULL** |

### 5.3 RIGHT (OUTER) JOIN

**Definition:** Returns **all rows from the right table** and matching rows from the left table.

```
Table A        Table B
 ┌───┐         ┌───┐
 │   │▓▓▓▓▓▓▓▓▓│▓▓▓│   ▓▓▓ = Result (matched A + all of B)
 │   │▓▓▓▓▓▓▓▓▓│▓▓▓│
 └───┘         └───┘
```

```sql
SELECT e.Name, d.DeptName
FROM EMPLOYEE e
RIGHT JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
```

### 5.4 FULL (OUTER) JOIN

**Definition:** Returns **all rows from both tables**. Non-matching sides are NULL.

```
Table A        Table B
 ┌───┐         ┌───┐
 │▓▓▓│▓▓▓▓▓▓▓▓▓│▓▓▓│   ▓▓▓ = Result (everything from both)
 │▓▓▓│▓▓▓▓▓▓▓▓▓│▓▓▓│
 └───┘         └───┘
```

```sql
SELECT e.Name, d.DeptName
FROM EMPLOYEE e
FULL OUTER JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
```

> **Note:** MySQL does not support FULL OUTER JOIN. Emulate with UNION of LEFT and RIGHT JOIN.

```sql
-- MySQL workaround
SELECT e.Name, d.DeptName FROM EMPLOYEE e LEFT JOIN DEPARTMENT d ON e.DeptID = d.DeptID
UNION
SELECT e.Name, d.DeptName FROM EMPLOYEE e RIGHT JOIN DEPARTMENT d ON e.DeptID = d.DeptID;
```

### 5.5 CROSS JOIN

**Definition:** Returns the **Cartesian product** — every row from A paired with every row from B. If A has m rows and B has n rows, result has m × n rows.

```sql
SELECT e.Name, p.ProjectName
FROM EMPLOYEE e
CROSS JOIN PROJECT p;
-- Returns 8 × 4 = 32 rows
```

### 5.6 SELF JOIN

**Definition:** A table joined with **itself**. Requires table aliases to distinguish the two copies.

```sql
-- Find employees and their managers
SELECT
    emp.Name  AS Employee,
    mgr.Name  AS Manager
FROM EMPLOYEE emp
LEFT JOIN EMPLOYEE mgr ON emp.ManagerID = mgr.EmpID;
```
| Employee | Manager |
|---|---|
| Rahul Sharma | NULL |
| Priya Singh | Rahul Sharma |
| Amit Patel | Rahul Sharma |
| Neha Gupta | Rahul Sharma |
| Suresh Kumar | NULL |
| Deepa Iyer | Amit Patel |
| Vijay Reddy | NULL |
| Anita Desai | Vijay Reddy |

### 5.7 Natural Join

**Definition:** Automatically joins on **all columns with the same name** in both tables. Risky — may join on unintended columns.

```sql
SELECT * FROM EMPLOYEE NATURAL JOIN DEPARTMENT;
-- Joins on DeptID (the common column)
```

> **Caution:** Avoid NATURAL JOIN in production. If both tables later get a column with the same name (e.g., `Name`), the query silently changes behavior.

### 5.8 Join Worked Examples

**Example 1: List employees with their department names**
```sql
SELECT e.EmpID, e.Name, e.Salary, d.DeptName, d.Location
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d ON e.DeptID = d.DeptID
ORDER BY d.DeptName, e.Name;
```

**Example 2: Find employees with no department (LEFT JOIN + IS NULL)**
```sql
SELECT e.Name
FROM EMPLOYEE e
LEFT JOIN DEPARTMENT d ON e.DeptID = d.DeptID
WHERE d.DeptID IS NULL;
```
| Name |
|---|
| Anita Desai |

**Example 3: Find employees and their managers (SELF JOIN)**
```sql
SELECT
    emp.Name   AS Employee,
    emp.Salary AS EmpSalary,
    mgr.Name   AS Manager,
    mgr.Salary AS MgrSalary
FROM EMPLOYEE emp
LEFT JOIN EMPLOYEE mgr ON emp.ManagerID = mgr.EmpID
ORDER BY mgr.Name, emp.Name;
```

**Example 4: Find all employee-project combinations (CROSS JOIN)**
```sql
SELECT e.Name, p.ProjectName
FROM EMPLOYEE e
CROSS JOIN PROJECT p
ORDER BY e.Name, p.ProjectName;
-- 8 employees × 4 projects = 32 rows
```

**Example 5: Find departments with no employees**
```sql
SELECT d.DeptName
FROM DEPARTMENT d
LEFT JOIN EMPLOYEE e ON d.DeptID = e.DeptID
WHERE e.EmpID IS NULL;
```

> In our sample data, all departments have at least one employee, so this returns no rows. If a department existed with no employees, it would appear here.

---

## 6. Subqueries

### 6.1 Types of Subqueries

#### Scalar Subquery (returns single value)
```sql
SELECT Name, Salary
FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE);
```

#### Row Subquery (returns single row)
```sql
SELECT * FROM EMPLOYEE
WHERE (DeptID, Salary) = (SELECT DeptID, MAX(Salary)
                           FROM EMPLOYEE
                           WHERE DeptID = 1
                           GROUP BY DeptID);
```

#### Table Subquery (returns a table, used with IN, EXISTS, or FROM)
```sql
SELECT * FROM EMPLOYEE
WHERE DeptID IN (SELECT DeptID FROM DEPARTMENT WHERE Location = 'Building A');
```

#### Correlated Subquery (depends on outer query — executes once per outer row)
```sql
-- Find employees earning above their department average
SELECT e.Name, e.Salary, e.DeptID
FROM EMPLOYEE e
WHERE e.Salary > (
    SELECT AVG(e2.Salary)
    FROM EMPLOYEE e2
    WHERE e2.DeptID = e.DeptID   -- references outer query
);
```

### 6.2 EXISTS / NOT EXISTS

```sql
-- Departments that have at least one employee
SELECT d.DeptName
FROM DEPARTMENT d
WHERE EXISTS (
    SELECT 1 FROM EMPLOYEE e WHERE e.DeptID = d.DeptID
);

-- Departments with no employees
SELECT d.DeptName
FROM DEPARTMENT d
WHERE NOT EXISTS (
    SELECT 1 FROM EMPLOYEE e WHERE e.DeptID = d.DeptID
);
```

### 6.3 IN / NOT IN

```sql
-- Employees in departments located in Building A
SELECT Name FROM EMPLOYEE
WHERE DeptID IN (SELECT DeptID FROM DEPARTMENT WHERE Location = 'Building A');

-- Employees NOT assigned to any project
SELECT Name FROM EMPLOYEE
WHERE EmpID NOT IN (SELECT DISTINCT EmpID FROM WORKS_ON);
```

> **Warning:** `NOT IN` fails silently if the subquery returns any NULL. Prefer `NOT EXISTS` for safety.

### 6.4 ANY / ALL

```sql
-- Salary greater than ANY employee in department 2 (i.e., greater than the MIN)
SELECT Name, Salary FROM EMPLOYEE
WHERE Salary > ANY (SELECT Salary FROM EMPLOYEE WHERE DeptID = 2);

-- Salary greater than ALL employees in department 2 (i.e., greater than the MAX)
SELECT Name, Salary FROM EMPLOYEE
WHERE Salary > ALL (SELECT Salary FROM EMPLOYEE WHERE DeptID = 2);
```

### 6.5 Subquery Worked Examples

**Example 1: Find employees earning more than average salary**
```sql
SELECT Name, Salary
FROM EMPLOYEE
WHERE Salary > (SELECT AVG(Salary) FROM EMPLOYEE)
ORDER BY Salary DESC;
```
| Name | Salary |
|---|---|
| Neha Gupta | 90000 |
| Priya Singh | 82000 |
| Rahul Sharma | 75000 |
| Vijay Reddy | 72000 |

**Example 2: Employees earning more than their department average (correlated)**
```sql
SELECT e.Name, e.Salary, d.DeptName
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
WHERE e.Salary > (
    SELECT AVG(e2.Salary)
    FROM EMPLOYEE e2
    WHERE e2.DeptID = e.DeptID
)
ORDER BY d.DeptName;
```
| Name | Salary | DeptName |
|---|---|---|
| Neha Gupta | 90000 | Engineering |
| Priya Singh | 82000 | Engineering |
| Deepa Iyer | 68000 | Marketing |

**Example 3: Find departments that have at least one employee (EXISTS)**
```sql
SELECT d.DeptID, d.DeptName
FROM DEPARTMENT d
WHERE EXISTS (
    SELECT 1 FROM EMPLOYEE e WHERE e.DeptID = d.DeptID
);
```

**Example 4: Find the second highest salary**
```sql
-- Method 1: Using subquery
SELECT MAX(Salary) AS SecondHighest
FROM EMPLOYEE
WHERE Salary < (SELECT MAX(Salary) FROM EMPLOYEE);

-- Method 2: Using LIMIT/OFFSET
SELECT DISTINCT Salary
FROM EMPLOYEE
ORDER BY Salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using DENSE_RANK
SELECT Salary FROM (
    SELECT Salary, DENSE_RANK() OVER (ORDER BY Salary DESC) AS rnk
    FROM EMPLOYEE
) ranked
WHERE rnk = 2;
```

**Example 5: Employees who work on ALL projects in their department (Relational Division)**
```sql
-- "For each employee, check if there is no project in their department
--  that they do NOT work on."
SELECT e.Name
FROM EMPLOYEE e
WHERE NOT EXISTS (
    SELECT p.ProjectID
    FROM PROJECT p
    WHERE p.DeptID = e.DeptID
      AND NOT EXISTS (
          SELECT 1
          FROM WORKS_ON w
          WHERE w.EmpID = e.EmpID AND w.ProjectID = p.ProjectID
      )
)
AND e.DeptID IS NOT NULL;
```

> This is the classic **double-NOT-EXISTS** pattern for relational division. Read it as: "There does not exist a project in this employee's department such that the employee does not work on it."

---

## 7. Advanced SQL

### 7.1 Window Functions

Window functions perform calculations across a **set of rows related to the current row** without collapsing them (unlike GROUP BY).

```sql
-- Syntax
function_name() OVER (
    [PARTITION BY column_list]
    [ORDER BY column_list]
    [frame_clause]
)
```

**Ranking Functions:**

```sql
-- ROW_NUMBER: unique sequential number (no ties)
-- RANK:       same rank for ties, then skips (1, 2, 2, 4)
-- DENSE_RANK: same rank for ties, no skip (1, 2, 2, 3)

SELECT
    Name,
    DeptID,
    Salary,
    ROW_NUMBER() OVER (ORDER BY Salary DESC)                AS RowNum,
    RANK()       OVER (ORDER BY Salary DESC)                AS Rnk,
    DENSE_RANK() OVER (ORDER BY Salary DESC)                AS DenseRnk,
    ROW_NUMBER() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS DeptRank
FROM EMPLOYEE;
```

| Name | DeptID | Salary | RowNum | Rnk | DenseRnk | DeptRank |
|---|---|---|---|---|---|---|
| Neha Gupta | 1 | 90000 | 1 | 1 | 1 | 1 |
| Priya Singh | 1 | 82000 | 2 | 2 | 2 | 2 |
| Rahul Sharma | 1 | 75000 | 3 | 3 | 3 | 3 |
| Vijay Reddy | 4 | 72000 | 4 | 4 | 4 | 1 |
| Deepa Iyer | 2 | 68000 | 5 | 5 | 5 | 1 |
| Anita Desai | NULL | 58000 | 6 | 6 | 6 | 1 |
| Amit Patel | 2 | 55000 | 7 | 7 | 7 | 2 |
| Suresh Kumar | 3 | 45000 | 8 | 8 | 8 | 1 |

### 7.2 Running Totals

```sql
SELECT
    Name,
    JoinDate,
    Salary,
    SUM(Salary) OVER (ORDER BY JoinDate) AS RunningTotal
FROM EMPLOYEE
ORDER BY JoinDate;
```

| Name | JoinDate | Salary | RunningTotal |
|---|---|---|---|
| Neha Gupta | 2019-08-01 | 90000 | 90000 |
| Rahul Sharma | 2020-01-15 | 75000 | 165000 |
| Priya Singh | 2020-06-20 | 82000 | 247000 |
| Vijay Reddy | 2020-11-20 | 72000 | 319000 |
| Amit Patel | 2021-03-10 | 55000 | 374000 |
| Deepa Iyer | 2021-07-15 | 68000 | 442000 |
| Suresh Kumar | 2022-01-05 | 45000 | 487000 |
| Anita Desai | 2023-02-28 | 58000 | 545000 |

### 7.3 Views

```sql
-- CREATE VIEW
CREATE VIEW HighEarners AS
SELECT e.EmpID, e.Name, e.Salary, d.DeptName
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
WHERE e.Salary > 70000;

-- Use the view like a table
SELECT * FROM HighEarners WHERE DeptName = 'Engineering';

-- DROP VIEW
DROP VIEW IF EXISTS HighEarners;
```

> **Views** are virtual tables — they store the query, not the data. Useful for security (restrict column access) and simplifying complex queries.

### 7.4 CASE WHEN

```sql
SELECT Name, Salary,
    CASE
        WHEN Salary >= 80000 THEN 'Senior'
        WHEN Salary >= 60000 THEN 'Mid-Level'
        WHEN Salary >= 40000 THEN 'Junior'
        ELSE 'Intern'
    END AS SalaryBand
FROM EMPLOYEE
ORDER BY Salary DESC;
```
| Name | Salary | SalaryBand |
|---|---|---|
| Neha Gupta | 90000 | Senior |
| Priya Singh | 82000 | Senior |
| Rahul Sharma | 75000 | Mid-Level |
| Vijay Reddy | 72000 | Mid-Level |
| Deepa Iyer | 68000 | Mid-Level |
| Anita Desai | 58000 | Junior |
| Amit Patel | 55000 | Junior |
| Suresh Kumar | 45000 | Junior |

### 7.5 Set Operations

```sql
-- UNION (combines results, removes duplicates)
SELECT Name FROM EMPLOYEE WHERE DeptID = 1
UNION
SELECT Name FROM EMPLOYEE WHERE Salary > 70000;

-- UNION ALL (keeps duplicates — faster)
SELECT Name FROM EMPLOYEE WHERE DeptID = 1
UNION ALL
SELECT Name FROM EMPLOYEE WHERE Salary > 70000;

-- INTERSECT (rows in both queries)
SELECT Name FROM EMPLOYEE WHERE DeptID = 1
INTERSECT
SELECT Name FROM EMPLOYEE WHERE Salary > 70000;

-- EXCEPT / MINUS (rows in first but not second)
SELECT Name FROM EMPLOYEE WHERE DeptID = 1
EXCEPT
SELECT Name FROM EMPLOYEE WHERE Salary > 80000;
```

> All set operations require the same number of columns with compatible data types.

### 7.6 String Functions

```sql
SELECT
    CONCAT(Name, ' - ', DeptID)         AS ConcatExample,
    UPPER(Name)                          AS UpperName,
    LOWER(Name)                          AS LowerName,
    LENGTH(Name)                         AS NameLength,
    SUBSTRING(Name, 1, 5)               AS FirstFive,
    TRIM('  hello  ')                    AS Trimmed,
    REPLACE(Name, 'Sharma', 'S.')        AS Replaced,
    LEFT(Name, 3)                        AS LeftThree,
    RIGHT(Name, 5)                       AS RightFive
FROM EMPLOYEE;
```

### 7.7 Advanced Worked Examples

**Example 1: Rank employees by salary within each department**
```sql
SELECT
    d.DeptName,
    e.Name,
    e.Salary,
    RANK() OVER (PARTITION BY e.DeptID ORDER BY e.Salary DESC) AS DeptSalaryRank
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.DeptID = d.DeptID
ORDER BY d.DeptName, DeptSalaryRank;
```
| DeptName | Name | Salary | DeptSalaryRank |
|---|---|---|---|
| Engineering | Neha Gupta | 90000 | 1 |
| Engineering | Priya Singh | 82000 | 2 |
| Engineering | Rahul Sharma | 75000 | 3 |
| Finance | Vijay Reddy | 72000 | 1 |
| HR | Suresh Kumar | 45000 | 1 |
| Marketing | Deepa Iyer | 68000 | 1 |
| Marketing | Amit Patel | 55000 | 2 |

**Example 2: Running total of salaries ordered by join date**
```sql
SELECT
    e.Name,
    e.JoinDate,
    e.Salary,
    SUM(e.Salary) OVER (ORDER BY e.JoinDate ROWS UNBOUNDED PRECEDING) AS RunningTotal,
    COUNT(*)      OVER (ORDER BY e.JoinDate ROWS UNBOUNDED PRECEDING) AS EmployeesSoFar
FROM EMPLOYEE e
ORDER BY e.JoinDate;
```

**Example 3: Categorize employees by salary range using CASE**
```sql
SELECT
    CASE
        WHEN Salary >= 80000 THEN 'A: High (80k+)'
        WHEN Salary >= 60000 THEN 'B: Medium (60k-79k)'
        ELSE 'C: Entry (<60k)'
    END AS SalaryCategory,
    COUNT(*) AS EmployeeCount,
    ROUND(AVG(Salary), 2) AS AvgSalary,
    MIN(Salary) AS MinSalary,
    MAX(Salary) AS MaxSalary
FROM EMPLOYEE
GROUP BY
    CASE
        WHEN Salary >= 80000 THEN 'A: High (80k+)'
        WHEN Salary >= 60000 THEN 'B: Medium (60k-79k)'
        ELSE 'C: Entry (<60k)'
    END
ORDER BY SalaryCategory;
```
| SalaryCategory | EmployeeCount | AvgSalary | MinSalary | MaxSalary |
|---|---|---|---|---|
| A: High (80k+) | 2 | 86000.00 | 82000 | 90000 |
| B: Medium (60k-79k) | 3 | 71666.67 | 68000 | 75000 |
| C: Entry (<60k) | 3 | 52666.67 | 45000 | 58000 |

---

## 8. Transaction Control

### 8.1 ACID Properties

| Property | Meaning |
|---|---|
| **Atomicity** | All operations in a transaction succeed or all fail — no partial commits |
| **Consistency** | Database moves from one valid state to another |
| **Isolation** | Concurrent transactions don't interfere with each other |
| **Durability** | Once committed, data survives crashes |

### 8.2 Transaction Syntax

```sql
-- Basic transaction
BEGIN TRANSACTION;   -- or just BEGIN; or START TRANSACTION;

UPDATE EMPLOYEE SET Salary = Salary + 5000 WHERE EmpID = 101;
UPDATE DEPARTMENT SET Budget = Budget - 5000 WHERE DeptID = 1;

COMMIT;   -- makes changes permanent

-- Rollback on error
BEGIN TRANSACTION;

UPDATE EMPLOYEE SET Salary = Salary + 5000 WHERE EmpID = 101;
-- Oops, wrong amount!
ROLLBACK;  -- undoes all changes since BEGIN
```

### 8.3 SAVEPOINT

```sql
BEGIN TRANSACTION;

UPDATE EMPLOYEE SET Salary = 80000 WHERE EmpID = 101;
SAVEPOINT sp1;

UPDATE EMPLOYEE SET Salary = 90000 WHERE EmpID = 102;
SAVEPOINT sp2;

UPDATE EMPLOYEE SET Salary = 100000 WHERE EmpID = 103;
-- Want to undo only EmpID 103's change
ROLLBACK TO sp2;

-- EmpID 101 = 80000 ✅, EmpID 102 = 90000 ✅, EmpID 103 = unchanged ✅
COMMIT;
```

### 8.4 Isolation Levels

| Level | Dirty Read | Non-repeatable Read | Phantom Read | Performance |
|---|---|---|---|---|
| READ UNCOMMITTED | ✅ Possible | ✅ Possible | ✅ Possible | Fastest |
| READ COMMITTED | ❌ Prevented | ✅ Possible | ✅ Possible | Fast |
| REPEATABLE READ | ❌ Prevented | ❌ Prevented | ✅ Possible | Moderate |
| SERIALIZABLE | ❌ Prevented | ❌ Prevented | ❌ Prevented | Slowest |

```sql
-- Set isolation level
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN TRANSACTION;
-- ... operations ...
COMMIT;
```

**Concurrency problems explained:**
- **Dirty Read:** Reading data written by an uncommitted transaction
- **Non-repeatable Read:** Reading the same row twice gets different values (another transaction committed an update between reads)
- **Phantom Read:** Re-executing a query returns different rows (another transaction inserted/deleted rows)

---

# PART B: MongoDB Queries

---

## 1. MongoDB Basics

### 1.1 Core Concepts

| SQL Concept | MongoDB Equivalent |
|---|---|
| Database | Database |
| Table | Collection |
| Row | Document (JSON/BSON) |
| Column | Field |
| Primary Key | `_id` (auto-generated ObjectId if not specified) |
| Index | Index |
| JOIN | `$lookup` (aggregation) or embedding |
| Schema | Schema-free (flexible, per-document) |

### 1.2 Document Structure (JSON/BSON)

```json
{
  "_id": ObjectId("64a7f2e8c1234567890abcde"),
  "name": "Rahul Sharma",
  "age": 28,
  "department": "Engineering",
  "salary": 75000,
  "skills": ["Python", "MongoDB", "Docker"],
  "address": {
    "city": "Mumbai",
    "state": "Maharashtra"
  },
  "joinDate": ISODate("2023-01-15"),
  "isActive": true
}
```

### 1.3 Data Types

| Type | Example |
|---|---|
| String | `"Rahul Sharma"` |
| Number (int/double) | `28`, `75000.50` |
| Boolean | `true`, `false` |
| Array | `["Python", "MongoDB"]` |
| Object (embedded doc) | `{ "city": "Mumbai" }` |
| Date | `ISODate("2023-01-15")` |
| ObjectId | `ObjectId("64a7f2...")` |
| Null | `null` |

### 1.4 When to Use MongoDB vs SQL

| Use MongoDB When | Use SQL When |
|---|---|
| Schema varies across records | Schema is fixed and well-defined |
| Nested/hierarchical data | Complex relationships and joins |
| Horizontal scaling needed | Strong ACID transactions required |
| Rapid development / prototyping | Reporting and analytics |
| High write throughput | Financial / regulatory data |
| Document-oriented data (blogs, catalogs) | Relational data (orders, invoices) |

---

## 2. CRUD Operations

### Sample Collection: `employees`

```javascript
// Sample data for all examples below
db.employees.insertMany([
  {
    name: "Rahul Sharma", age: 28, department: "Engineering",
    salary: 75000, skills: ["Python", "MongoDB", "Docker"],
    address: { city: "Mumbai", state: "Maharashtra" },
    joinDate: ISODate("2023-01-15"), isActive: true
  },
  {
    name: "Priya Singh", age: 32, department: "Engineering",
    salary: 82000, skills: ["Java", "AWS", "Docker"],
    address: { city: "Bangalore", state: "Karnataka" },
    joinDate: ISODate("2022-06-20"), isActive: true
  },
  {
    name: "Amit Patel", age: 25, department: "Marketing",
    salary: 55000, skills: ["SEO", "Analytics"],
    address: { city: "Delhi", state: "Delhi" },
    joinDate: ISODate("2023-08-10"), isActive: true
  },
  {
    name: "Neha Gupta", age: 35, department: "Engineering",
    salary: 95000, skills: ["Python", "ML", "Docker", "Kubernetes"],
    address: { city: "Mumbai", state: "Maharashtra" },
    joinDate: ISODate("2020-03-01"), isActive: true
  },
  {
    name: "Suresh Kumar", age: 30, department: "HR",
    salary: 48000, skills: ["Recruitment", "Training"],
    address: { city: "Chennai", state: "Tamil Nadu" },
    joinDate: ISODate("2023-11-01"), isActive: false
  },
  {
    name: "Riya Desai", age: 27, department: "Marketing",
    salary: 62000, skills: ["Content", "SEO", "Analytics"],
    address: { city: "Delhi", state: "Delhi" },
    joinDate: ISODate("2022-12-15"), isActive: true
  }
]);
```

### 2.1 Insert

```javascript
// insertOne — single document
db.employees.insertOne({
  name: "Deepa Iyer",
  age: 29,
  department: "Finance",
  salary: 68000,
  skills: ["Excel", "SAP"],
  address: { city: "Hyderabad", state: "Telangana" },
  joinDate: ISODate("2024-01-10"),
  isActive: true
});

// insertMany — multiple documents
db.employees.insertMany([
  { name: "Vijay Reddy", age: 33, department: "Finance", salary: 72000, skills: ["Accounting"], address: { city: "Pune", state: "Maharashtra" }, joinDate: ISODate("2023-05-20"), isActive: true },
  { name: "Kavita Joshi", age: 26, department: "HR", salary: 45000, skills: ["Onboarding"], address: { city: "Mumbai", state: "Maharashtra" }, joinDate: ISODate("2024-02-01"), isActive: true }
]);
```

### 2.2 Find (Read)

```javascript
// Find all
db.employees.find({});

// Find with filter
db.employees.find({ department: "Engineering" });

// Projection (include specific fields; _id included by default)
db.employees.find(
  { department: "Engineering" },
  { name: 1, salary: 1, _id: 0 }
);

// Sort (1 = ascending, -1 = descending)
db.employees.find({}).sort({ salary: -1 });

// Limit
db.employees.find({}).sort({ salary: -1 }).limit(3);

// Skip + Limit (pagination)
db.employees.find({}).sort({ name: 1 }).skip(2).limit(2);

// Count
db.employees.countDocuments({ department: "Engineering" });

// Distinct
db.employees.distinct("department");

// findOne (returns first matching document)
db.employees.findOne({ name: "Rahul Sharma" });
```

### 2.3 Update

```javascript
// updateOne — update first matching document
db.employees.updateOne(
  { name: "Rahul Sharma" },
  { $set: { salary: 80000 } }
);

// updateMany — update all matching documents
db.employees.updateMany(
  { department: "Engineering" },
  { $inc: { salary: 5000 } }       // increment salary by 5000
);

// $set — set/add a field
db.employees.updateOne(
  { name: "Amit Patel" },
  { $set: { "address.city": "Noida", title: "Senior Analyst" } }
);

// $unset — remove a field
db.employees.updateOne(
  { name: "Amit Patel" },
  { $unset: { title: "" } }
);

// $push — add to array
db.employees.updateOne(
  { name: "Rahul Sharma" },
  { $push: { skills: "Kubernetes" } }
);

// $pull — remove from array
db.employees.updateOne(
  { name: "Rahul Sharma" },
  { $pull: { skills: "Docker" } }
);

// $addToSet — add to array only if not already present
db.employees.updateOne(
  { name: "Rahul Sharma" },
  { $addToSet: { skills: "AWS" } }
);

// Upsert (update if exists, insert if not)
db.employees.updateOne(
  { name: "New Person" },
  { $set: { department: "Sales", salary: 50000 } },
  { upsert: true }
);
```

### 2.4 Delete

```javascript
// deleteOne — delete first matching document
db.employees.deleteOne({ name: "Suresh Kumar" });

// deleteMany — delete all matching documents
db.employees.deleteMany({ isActive: false });

// Delete all documents in collection (keep collection)
db.employees.deleteMany({});

// Drop entire collection (like DROP TABLE)
db.employees.drop();
```

### 2.5 findOneAndUpdate / findOneAndDelete

```javascript
// Returns the document BEFORE the update (or AFTER with returnNewDocument)
db.employees.findOneAndUpdate(
  { name: "Rahul Sharma" },
  { $inc: { salary: 5000 } },
  { returnNewDocument: true }     // return updated version
);

// Returns the deleted document
db.employees.findOneAndDelete(
  { salary: { $lt: 40000 } }
);
```

---

## 3. Query Operators

### 3.1 Comparison Operators

```javascript
// $eq (equal) — implicit when you use { field: value }
db.employees.find({ age: { $eq: 28 } });
db.employees.find({ age: 28 });              // shorthand

// $ne (not equal)
db.employees.find({ department: { $ne: "HR" } });

// $gt, $gte (greater than, greater than or equal)
db.employees.find({ salary: { $gt: 60000 } });
db.employees.find({ salary: { $gte: 60000 } });

// $lt, $lte (less than, less than or equal)
db.employees.find({ age: { $lt: 30 } });

// $in (value in a set)
db.employees.find({ department: { $in: ["Engineering", "Marketing"] } });

// $nin (value NOT in a set)
db.employees.find({ department: { $nin: ["HR", "Finance"] } });
```

### 3.2 Logical Operators

```javascript
// $and (all conditions must match)
db.employees.find({
  $and: [
    { department: "Engineering" },
    { salary: { $gt: 70000 } }
  ]
});
// Shorthand (implicit AND when using multiple fields):
db.employees.find({ department: "Engineering", salary: { $gt: 70000 } });

// $or (at least one condition must match)
db.employees.find({
  $or: [
    { department: "Engineering" },
    { salary: { $gt: 80000 } }
  ]
});

// $not (negate a condition)
db.employees.find({ salary: { $not: { $gt: 70000 } } });

// $nor (none of the conditions must match)
db.employees.find({
  $nor: [
    { department: "HR" },
    { salary: { $lt: 50000 } }
  ]
});
```

### 3.3 Element Operators

```javascript
// $exists — check if a field exists
db.employees.find({ title: { $exists: true } });
db.employees.find({ title: { $exists: false } });

// $type — check field's BSON type
db.employees.find({ salary: { $type: "number" } });
db.employees.find({ name: { $type: "string" } });
```

### 3.4 Array Operators

```javascript
// $all — array contains ALL specified elements
db.employees.find({ skills: { $all: ["Python", "Docker"] } });

// $elemMatch — at least one array element matches all conditions
// (useful for arrays of objects)
db.employees.find({
  projects: { $elemMatch: { name: "Alpha", hours: { $gt: 20 } } }
});

// $size — array has exactly N elements
db.employees.find({ skills: { $size: 3 } });
```

### 3.5 Regular Expressions

```javascript
// Names starting with "R"
db.employees.find({ name: { $regex: /^R/ } });

// Names containing "sh" (case-insensitive)
db.employees.find({ name: { $regex: /sh/i } });

// Department ending with "ing"
db.employees.find({ department: { $regex: /ing$/ } });
```

### 3.6 Worked Examples (8 Examples)

**Example 1: Find employees in Engineering with salary > 60000**
```javascript
db.employees.find({
  department: "Engineering",
  salary: { $gt: 60000 }
});
```

**Example 2: Find employees who know both Python AND Docker**
```javascript
db.employees.find({
  skills: { $all: ["Python", "Docker"] }
});
// Returns: Rahul Sharma, Neha Gupta
```

**Example 3: Find employees in Mumbai OR Delhi**
```javascript
db.employees.find({
  $or: [
    { "address.city": "Mumbai" },
    { "address.city": "Delhi" }
  ]
});
// Or more concisely:
db.employees.find({
  "address.city": { $in: ["Mumbai", "Delhi"] }
});
```

**Example 4: Find employees with more than 3 skills**
```javascript
db.employees.find({
  $expr: { $gt: [{ $size: "$skills" }, 3] }
});
// Returns: Neha Gupta (4 skills)
```

> Note: `$size` in a query filter only matches exact counts. For comparisons (>, <), use `$expr` with `$size` aggregation operator.

**Example 5: Find employees whose address.city is "Bangalore"**
```javascript
db.employees.find({ "address.city": "Bangalore" });
// Dot notation accesses nested fields
```

**Example 6: Find employees who joined after 2023**
```javascript
db.employees.find({
  joinDate: { $gte: ISODate("2023-01-01") }
});
```

**Example 7: Find employees with salary between 50000 and 100000**
```javascript
db.employees.find({
  salary: { $gte: 50000, $lte: 100000 }
});
```

**Example 8: Find employees whose name starts with "R"**
```javascript
db.employees.find({
  name: { $regex: /^R/ }
});
// Returns: Rahul Sharma, Riya Desai
```

---

## 4. Aggregation Pipeline

The aggregation pipeline processes documents through a sequence of **stages**. Each stage transforms the data and passes results to the next stage.

```
documents → $match → $group → $sort → $project → results
```

### 4.1 Pipeline Stages Reference

| Stage | Purpose | SQL Equivalent |
|---|---|---|
| `$match` | Filter documents | `WHERE` |
| `$group` | Group and aggregate | `GROUP BY` |
| `$sort` | Sort results | `ORDER BY` |
| `$project` | Reshape / select fields | `SELECT` |
| `$unwind` | Flatten an array (one doc per element) | (no direct equivalent) |
| `$lookup` | Join with another collection | `JOIN` |
| `$limit` | Limit results | `LIMIT` |
| `$skip` | Skip results | `OFFSET` |
| `$addFields` | Add computed fields | `SELECT col, expr AS alias` |
| `$count` | Count documents | `COUNT(*)` |

### 4.2 Group Accumulators

| Accumulator | Purpose |
|---|---|
| `$sum` | Sum of values (use `$sum: 1` for count) |
| `$avg` | Average |
| `$min` | Minimum |
| `$max` | Maximum |
| `$push` | Collect values into array |
| `$addToSet` | Collect unique values into array |
| `$first` | First value in group |
| `$last` | Last value in group |
| `$count` | Count of documents in group |

### 4.3 Worked Examples (6 Examples)

**Example 1: Average salary by department**
```javascript
db.employees.aggregate([
  {
    $group: {
      _id: "$department",
      avgSalary: { $avg: "$salary" },
      count: { $sum: 1 }
    }
  },
  { $sort: { avgSalary: -1 } }
]);
```
Result:
```json
{ "_id": "Engineering", "avgSalary": 84000, "count": 3 }
{ "_id": "Finance",     "avgSalary": 70000, "count": 2 }
{ "_id": "Marketing",   "avgSalary": 58500, "count": 2 }
{ "_id": "HR",          "avgSalary": 46500, "count": 2 }
```

**Example 2: Top 3 highest paid departments**
```javascript
db.employees.aggregate([
  {
    $group: {
      _id: "$department",
      totalSalary: { $sum: "$salary" },
      avgSalary: { $avg: "$salary" },
      count: { $sum: 1 }
    }
  },
  { $sort: { totalSalary: -1 } },
  { $limit: 3 },
  {
    $project: {
      department: "$_id",
      totalSalary: 1,
      avgSalary: { $round: ["$avgSalary", 2] },
      count: 1,
      _id: 0
    }
  }
]);
```

**Example 3: Count employees per skill (unwind skills array)**
```javascript
db.employees.aggregate([
  { $unwind: "$skills" },           // one doc per skill
  {
    $group: {
      _id: "$skills",
      count: { $sum: 1 },
      employees: { $push: "$name" }  // collect names
    }
  },
  { $sort: { count: -1 } }
]);
```
Result:
```json
{ "_id": "Docker",     "count": 3, "employees": ["Rahul Sharma", "Priya Singh", "Neha Gupta"] }
{ "_id": "Python",     "count": 2, "employees": ["Rahul Sharma", "Neha Gupta"] }
{ "_id": "SEO",        "count": 2, "employees": ["Amit Patel", "Riya Desai"] }
{ "_id": "Analytics",  "count": 2, "employees": ["Amit Patel", "Riya Desai"] }
...
```

**Example 4: Find department with most employees**
```javascript
db.employees.aggregate([
  {
    $group: {
      _id: "$department",
      employeeCount: { $sum: 1 }
    }
  },
  { $sort: { employeeCount: -1 } },
  { $limit: 1 }
]);
// Result: { "_id": "Engineering", "employeeCount": 3 }
```

**Example 5: Join employees with a "projects" collection using $lookup**
```javascript
// Assuming a "projects" collection exists:
// { projectName: "Alpha", department: "Engineering", members: [empId1, ...] }

db.employees.aggregate([
  {
    $lookup: {
      from: "projects",            // foreign collection
      localField: "department",     // field in employees
      foreignField: "department",   // field in projects
      as: "departmentProjects"      // output array field
    }
  },
  {
    $project: {
      name: 1,
      department: 1,
      projectCount: { $size: "$departmentProjects" },
      projectNames: "$departmentProjects.projectName"
    }
  }
]);
```

**Example 6: Monthly hiring trends (group by year-month from joinDate)**
```javascript
db.employees.aggregate([
  {
    $group: {
      _id: {
        year: { $year: "$joinDate" },
        month: { $month: "$joinDate" }
      },
      hires: { $sum: 1 },
      names: { $push: "$name" }
    }
  },
  {
    $sort: { "_id.year": 1, "_id.month": 1 }
  },
  {
    $project: {
      _id: 0,
      period: {
        $concat: [
          { $toString: "$_id.year" }, "-",
          {
            $cond: {
              if: { $lt: ["$_id.month", 10] },
              then: { $concat: ["0", { $toString: "$_id.month" }] },
              else: { $toString: "$_id.month" }
            }
          }
        ]
      },
      hires: 1,
      names: 1
    }
  }
]);
```
Result:
```json
{ "period": "2020-03", "hires": 1, "names": ["Neha Gupta"] }
{ "period": "2022-06", "hires": 1, "names": ["Priya Singh"] }
{ "period": "2022-12", "hires": 1, "names": ["Riya Desai"] }
{ "period": "2023-01", "hires": 1, "names": ["Rahul Sharma"] }
{ "period": "2023-08", "hires": 1, "names": ["Amit Patel"] }
{ "period": "2023-11", "hires": 1, "names": ["Suresh Kumar"] }
```

---

## 5. Schema Design

### 5.1 Embedding vs Referencing

| Approach | Description | When to Use |
|---|---|---|
| **Embedding** | Nest related data inside the parent document | Data is always read together; 1:1 or 1:few relationships |
| **Referencing** | Store related document's `_id` and look it up separately | Data accessed independently; 1:many or many:many; large/growing sub-data |

**Embedding Example (1:1 — Address inside Employee):**
```json
{
  "name": "Rahul Sharma",
  "address": {
    "street": "123 MG Road",
    "city": "Mumbai",
    "state": "Maharashtra",
    "zip": "400001"
  }
}
```
✅ Single read fetches everything. No joins needed.

**Referencing Example (1:Many — Department with many Employees):**
```json
// departments collection
{ "_id": ObjectId("dept1"), "name": "Engineering", "budget": 500000 }

// employees collection
{ "name": "Rahul Sharma", "departmentId": ObjectId("dept1"), "salary": 75000 }
```
✅ Department data not duplicated. Employees can be queried independently.

### 5.2 Relationship Patterns

**One-to-One:** Embed (unless subdocument is very large).
```json
{ "name": "Rahul", "passport": { "number": "A1234", "expiry": "2030-01-01" } }
```

**One-to-Many (Few):** Embed the "many" side as an array.
```json
{ "name": "Rahul", "phoneNumbers": ["+91-9876543210", "+91-1234567890"] }
```

**One-to-Many (Thousands):** Reference from the "many" side.
```json
// blog_posts
{ "_id": "post1", "title": "MongoDB Guide", "author": "Rahul" }

// comments (separate collection, referencing post)
{ "postId": "post1", "text": "Great article!", "author": "Priya" }
```

**Many-to-Many:** Use array of references.
```json
// students
{ "_id": "s1", "name": "Rahul", "courseIds": ["c1", "c2"] }

// courses
{ "_id": "c1", "name": "DBMS", "studentIds": ["s1", "s3"] }
```

### 5.3 Denormalization

MongoDB often **denormalizes** data (stores redundant copies) to avoid joins:

```json
// Instead of joining orders with products:
{
  "orderId": 1001,
  "customer": "Rahul",
  "items": [
    { "productName": "Laptop", "price": 50000, "qty": 1 },
    { "productName": "Mouse",  "price": 500,   "qty": 2 }
  ]
}
```

**Trade-off:** Faster reads, but updates require changing data in multiple places.

---

## 6. Indexes

### 6.1 Creating Indexes

```javascript
// Single-field index
db.employees.createIndex({ salary: 1 });          // ascending
db.employees.createIndex({ salary: -1 });         // descending

// Compound index (order matters!)
db.employees.createIndex({ department: 1, salary: -1 });

// Unique index
db.employees.createIndex({ email: 1 }, { unique: true });

// Text index (for full-text search)
db.employees.createIndex({ name: "text", skills: "text" });
db.employees.find({ $text: { $search: "Python Docker" } });

// TTL index (auto-delete after N seconds — for expiring data)
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 });

// List all indexes
db.employees.getIndexes();

// Drop an index
db.employees.dropIndex("salary_1");
```

### 6.2 Query Performance with explain()

```javascript
db.employees.find({ salary: { $gt: 70000 } }).explain("executionStats");

// Key fields to check:
// - executionStats.nReturned — number of documents returned
// - executionStats.totalDocsExamined — documents scanned
// - executionStats.executionTimeMillis — time taken
// - winningPlan.inputStage.stage — "IXSCAN" (index) vs "COLLSCAN" (full scan)
```

> **Goal:** `totalDocsExamined` should be close to `nReturned`. A `COLLSCAN` means no index was used.

---

# PART C: Redis Commands

---

## 1. Redis Basics

### 1.1 What is Redis?

Redis is an **in-memory key-value data store** that supports multiple data structures. Data lives in RAM for sub-millisecond access, with optional persistence to disk.

**Key Characteristics:**
- **In-memory:** All data stored in RAM → extremely fast
- **Data structures:** Strings, Lists, Sets, Sorted Sets, Hashes, Streams
- **Persistence:** RDB (snapshots) and AOF (append-only file)
- **Single-threaded:** One command at a time → no race conditions
- **TTL support:** Keys can auto-expire

### 1.2 Persistence Options

| Method | How It Works | Pros | Cons |
|---|---|---|---|
| **RDB** (Snapshots) | Periodic point-in-time snapshots to disk | Fast recovery, compact files | Data loss between snapshots |
| **AOF** (Append Only File) | Logs every write operation | Minimal data loss | Larger file size, slower recovery |
| **RDB + AOF** | Both combined | Best durability | More disk I/O |

### 1.3 Common Use Cases

| Use Case | Why Redis? |
|---|---|
| **Caching** | Sub-millisecond reads, TTL for auto-expiry |
| **Session Management** | Fast read/write, TTL for session timeout |
| **Rate Limiting** | Atomic INCR + EXPIRE per user/IP |
| **Leaderboards** | Sorted Sets with O(log n) rank operations |
| **Pub/Sub Messaging** | Real-time message broadcasting |
| **Job Queues** | Lists with LPUSH/BRPOP for reliable queuing |
| **Real-time Analytics** | HyperLogLog for unique counts, Bitmaps for flags |

---

## 2. Data Structures & Commands

### 2.1 Strings

The simplest Redis type. A key maps to a string value (can be text, number, or serialized data up to 512 MB).

```redis
# SET and GET
SET user:1:name "Rahul Sharma"
GET user:1:name                    # "Rahul Sharma"

# SET with expiry
SET session:abc123 "user_data" EX 1800    # expires in 1800 seconds (30 min)
SETEX session:abc123 1800 "user_data"     # equivalent

# SET only if key does NOT exist (useful for locks)
SETNX lock:resource1 "locked"

# Multiple SET and GET
MSET user:1:name "Rahul" user:1:age "28" user:1:dept "Engineering"
MGET user:1:name user:1:age user:1:dept   # ["Rahul", "28", "Engineering"]

# Numeric operations (atomic)
SET page:home:views 0
INCR page:home:views               # 1
INCR page:home:views               # 2
INCRBY page:home:views 10          # 12
DECR page:home:views               # 11
DECRBY page:home:views 5           # 6

# String operations
APPEND user:1:name " (Admin)"      # "Rahul Sharma (Admin)"
STRLEN user:1:name                  # 21

# Check TTL and manage expiry
TTL session:abc123                  # remaining seconds (-1 = no expiry, -2 = expired/missing)
PERSIST session:abc123              # remove expiry (make permanent)
EXPIRE user:1:name 3600            # set 1-hour expiry on existing key

# Delete
DEL user:1:name                     # removes the key
```

**Use Cases:** Session tokens, page view counters, cached query results, distributed locks.

### 2.2 Lists

Ordered collection of strings. Implemented as a linked list → fast push/pop at both ends.

```redis
# Push elements
LPUSH notifications:user1 "New message from Priya"
LPUSH notifications:user1 "Order shipped"           # pushes to left (head)
RPUSH notifications:user1 "Payment received"         # pushes to right (tail)

# Current list: ["Order shipped", "New message from Priya", "Payment received"]

# Pop elements
LPOP notifications:user1            # "Order shipped" (removes from head)
RPOP notifications:user1            # "Payment received" (removes from tail)

# Range (0-based index, -1 = last element)
RPUSH mylist "a" "b" "c" "d" "e"
LRANGE mylist 0 -1                  # ["a", "b", "c", "d", "e"] (all)
LRANGE mylist 0 2                   # ["a", "b", "c"] (first 3)
LRANGE mylist -3 -1                 # ["c", "d", "e"] (last 3)

# Other operations
LLEN mylist                         # 5 (length)
LINDEX mylist 2                     # "c" (element at index 2)
LTRIM mylist 0 99                   # keep only first 100 elements (trim rest)

# Blocking pop (waits up to N seconds for an element — great for queues)
BRPOP queue:jobs 30                 # blocks for 30 sec if queue is empty
```

**Use Cases:** Message queues (LPUSH + BRPOP), activity feeds, recent items list, undo history.

### 2.3 Sets

Unordered collection of **unique** strings. O(1) add, remove, and membership check.

```redis
# Add members
SADD user:1:skills "Python" "MongoDB" "Docker"
SADD user:2:skills "Java" "Docker" "AWS"
SADD user:3:skills "Python" "AWS" "ML"

# Remove
SREM user:1:skills "Docker"

# Check membership
SISMEMBER user:1:skills "Python"    # 1 (true)
SISMEMBER user:1:skills "Java"      # 0 (false)

# All members
SMEMBERS user:1:skills              # {"Python", "MongoDB"}

# Count
SCARD user:1:skills                 # 2

# Set operations
SUNION user:1:skills user:2:skills           # all skills from both users
SINTER user:1:skills user:2:skills           # common skills
SDIFF  user:1:skills user:2:skills           # skills user1 has but user2 doesn't

# Store result in a new key
SINTERSTORE common:skills user:1:skills user:2:skills

# Random member
SRANDMEMBER user:1:skills           # random skill
SPOP user:1:skills                  # random skill (removes it)
```

**Use Cases:** Tags, unique visitors (per day), mutual friends, online users, de-duplication.

### 2.4 Sorted Sets (ZSets)

Like Sets, but each member has a **score** (float). Members are ordered by score. O(log n) for most operations.

```redis
# Add members with scores
ZADD leaderboard 1500 "player:alice"
ZADD leaderboard 2300 "player:bob"
ZADD leaderboard 1800 "player:charlie"
ZADD leaderboard 2100 "player:diana"
ZADD leaderboard 900  "player:eve"

# Range by rank (0-based, ascending by score)
ZRANGE leaderboard 0 -1                # all, lowest to highest score
ZRANGE leaderboard 0 -1 WITHSCORES     # include scores

# Reverse range (highest to lowest)
ZREVRANGE leaderboard 0 2              # top 3 players
ZREVRANGE leaderboard 0 2 WITHSCORES

# Range by score
ZRANGEBYSCORE leaderboard 1000 2000              # scores between 1000-2000
ZRANGEBYSCORE leaderboard -inf +inf               # all
ZRANGEBYSCORE leaderboard 1500 +inf LIMIT 0 3    # top 3 above 1500

# Rank (0-based)
ZRANK leaderboard "player:bob"         # rank from lowest (0 = lowest score)
ZREVRANK leaderboard "player:bob"      # rank from highest (0 = highest score)

# Score
ZSCORE leaderboard "player:bob"        # 2300

# Update score
ZINCRBY leaderboard 200 "player:eve"   # eve's score: 900 + 200 = 1100

# Count and remove
ZCARD leaderboard                      # 5 (total members)
ZCOUNT leaderboard 1000 2000           # members with score 1000-2000
ZREM leaderboard "player:eve"          # remove a member
ZREMRANGEBYSCORE leaderboard 0 1000    # remove all with score 0-1000
```

**Use Cases:** Leaderboards, priority queues, time-based event scheduling, rate limiting windows.

### 2.5 Hashes

A map of field-value pairs under a single key. Perfect for representing objects.

```redis
# Set fields
HSET user:1 name "Rahul Sharma" age 28 department "Engineering" salary 75000

# Get single field
HGET user:1 name                       # "Rahul Sharma"

# Get multiple fields
HMGET user:1 name salary               # ["Rahul Sharma", "75000"]

# Get all fields and values
HGETALL user:1
# Returns: name "Rahul Sharma" age "28" department "Engineering" salary "75000"

# Check if field exists
HEXISTS user:1 email                   # 0 (false)

# Increment numeric field
HINCRBY user:1 salary 5000             # 80000
HINCRBYFLOAT user:1 rating 0.5

# Delete a field
HDEL user:1 department

# Get all field names or all values
HKEYS user:1                           # ["name", "age", "salary"]
HVALS user:1                           # ["Rahul Sharma", "28", "80000"]
HLEN user:1                            # 3 (number of fields)
```

**Use Cases:** User profiles, session data, configuration settings, object storage.

---

## 3. Practical Patterns

### 3.1 Session Management

```redis
# 1. Create session when user logs in
HSET session:abc123 userId "user:1" name "Rahul" role "admin" loginTime "2024-01-15T10:30:00Z"
EXPIRE session:abc123 1800             # 30-minute expiry

# 2. Check session on each request
HGETALL session:abc123                 # returns all session data
TTL session:abc123                     # check remaining time

# 3. Extend session on activity (sliding window)
EXPIRE session:abc123 1800             # reset to 30 minutes

# 4. Logout — destroy session
DEL session:abc123
```

### 3.2 Leaderboard

```redis
# 1. Add players with scores
ZADD game:leaderboard 1500 "alice"
ZADD game:leaderboard 2300 "bob"
ZADD game:leaderboard 1800 "charlie"
ZADD game:leaderboard 2100 "diana"
ZADD game:leaderboard 3500 "eve"
ZADD game:leaderboard 1200 "frank"
ZADD game:leaderboard 2800 "grace"
ZADD game:leaderboard 1900 "hank"
ZADD game:leaderboard 2600 "ivy"
ZADD game:leaderboard 3100 "jack"
ZADD game:leaderboard 1700 "kate"
ZADD game:leaderboard 2400 "leo"

# 2. Get top 10 players
ZREVRANGE game:leaderboard 0 9 WITHSCORES
# Result: eve(3500), jack(3100), grace(2800), ivy(2600), leo(2400),
#         bob(2300), diana(2100), hank(1900), charlie(1800), kate(1700)

# 3. Get a player's rank (0-indexed from top)
ZREVRANK game:leaderboard "diana"      # 5 (6th place)

# 4. Update score after a game
ZINCRBY game:leaderboard 500 "frank"   # frank: 1200 + 500 = 1700

# 5. Get player's score
ZSCORE game:leaderboard "bob"          # 2300

# 6. Get players ranked 5th to 10th
ZREVRANGE game:leaderboard 4 9 WITHSCORES

# 7. Count players with score above 2000
ZCOUNT game:leaderboard 2000 +inf
```

### 3.3 Rate Limiter (Sliding Window)

```redis
# Allow max 100 requests per minute per user
# Using a sorted set with timestamps as scores

# On each request from user:42:
# 1. Get current timestamp (e.g., 1705312800)
# 2. Remove entries older than 1 minute
ZREMRANGEBYSCORE ratelimit:user:42 0 1705312740    # remove entries > 60s ago

# 3. Count requests in the current window
ZCARD ratelimit:user:42

# 4. If count < 100, allow and log the request
ZADD ratelimit:user:42 1705312800 "req:1705312800:abc"
EXPIRE ratelimit:user:42 60           # auto-cleanup

# 5. If count >= 100, reject with 429 Too Many Requests
```

**Simpler approach using INCR + EXPIRE (fixed window):**
```redis
# Each request:
INCR ratelimit:user:42:minute:202401151030   # key = user + minute bucket
# If result of INCR = 1 (first request), set expiry:
EXPIRE ratelimit:user:42:minute:202401151030 60

# Check: if value > 100, reject
GET ratelimit:user:42:minute:202401151030
```

### 3.4 Caching

```redis
# 1. Check cache before querying DB
GET cache:products:electronics

# 2. Cache miss — query DB, then store result
SET cache:products:electronics '{"products":[...]}' EX 300   # 5-minute TTL

# 3. Cache hit — return cached data
GET cache:products:electronics

# 4. Invalidate on update
DEL cache:products:electronics         # delete when product catalog changes

# 5. Cache-aside pattern (pseudocode):
#    result = redis.GET(key)
#    if result is None:
#        result = db.query(...)
#        redis.SETEX(key, 300, serialize(result))
#    return result
```

### 3.5 Pub/Sub

```redis
# Terminal 1 — Subscriber
SUBSCRIBE notifications:user1
# Waits for messages...

# Terminal 2 — Publisher
PUBLISH notifications:user1 "You have a new message from Priya"
PUBLISH notifications:user1 "Your order has shipped"

# Terminal 1 output:
# 1) "message"
# 2) "notifications:user1"
# 3) "You have a new message from Priya"

# Pattern subscribe (wildcard)
PSUBSCRIBE notifications:*            # receives messages from all notification channels
```

> **Note:** Pub/Sub is fire-and-forget. If a subscriber is offline, it misses the message. For reliable messaging, use Redis Streams instead.

---

## 4. Redis vs MongoDB vs SQL Comparison

| Use Case | Best Choice | Why |
|---|---|---|
| User sessions | **Redis** | In-memory, fast R/W, TTL for auto-expiry |
| Product catalog | **MongoDB** | Flexible schema, nested data, rich queries |
| Financial transactions | **SQL (PostgreSQL)** | ACID compliance, referential integrity, audit trails |
| Real-time leaderboard | **Redis** | Sorted Sets, O(log n) rank operations |
| IoT sensor data | **MongoDB** | Write-heavy, flexible schema, time-series support |
| Shopping cart | **Redis** | Fast, TTL for abandoned carts, Hashes for items |
| User profiles (complex) | **MongoDB** | Nested data, flexible queries, schema evolution |
| Reporting / Analytics | **SQL** | Complex joins, window functions, aggregations |
| Cache layer | **Redis** | In-memory, sub-millisecond reads, TTL |
| Chat messages | **MongoDB** | Flexible schema, ordered data, scalable |
| Inventory management | **SQL** | Transactions prevent overselling, constraints |
| Real-time notifications | **Redis** | Pub/Sub or Streams for real-time delivery |
| Blog / CMS content | **MongoDB** | Variable structure, embedded comments, rich text |
| Rate limiting | **Redis** | Atomic INCR + EXPIRE, sliding window patterns |
| Social graph (friends) | **Redis / Graph DB** | Sets for mutual friends, fast intersection |
| Event logging | **MongoDB** | Append-heavy, flexible fields, capped collections |
| Multi-step workflows | **SQL** | ACID transactions, savepoints, rollback |
| Feature flags | **Redis** | Fast lookups, Hashes for flag configuration |
| Geospatial queries | **MongoDB / Redis** | Both support geo indexes and radius searches |
| Data warehouse | **SQL** | Star/snowflake schema, complex analytics |

---

# PART D: SQL vs MongoDB Query Equivalents

| # | Operation | SQL | MongoDB |
|---|---|---|---|
| 1 | **Select all** | `SELECT * FROM employees` | `db.employees.find({})` |
| 2 | **Select specific columns** | `SELECT name, salary FROM employees` | `db.employees.find({}, {name:1, salary:1, _id:0})` |
| 3 | **Filter (equality)** | `SELECT * FROM employees WHERE dept='Eng'` | `db.employees.find({dept:"Eng"})` |
| 4 | **Filter (comparison)** | `SELECT * FROM employees WHERE salary > 50000` | `db.employees.find({salary:{$gt:50000}})` |
| 5 | **AND** | `WHERE dept='Eng' AND salary>50000` | `{dept:"Eng", salary:{$gt:50000}}` |
| 6 | **OR** | `WHERE dept='Eng' OR dept='HR'` | `{$or:[{dept:"Eng"},{dept:"HR"}]}` |
| 7 | **IN** | `WHERE dept IN ('Eng','HR','Finance')` | `{dept:{$in:["Eng","HR","Finance"]}}` |
| 8 | **NOT** | `WHERE NOT dept='HR'` | `{dept:{$ne:"HR"}}` |
| 9 | **LIKE (starts with)** | `WHERE name LIKE 'R%'` | `{name:{$regex:/^R/}}` |
| 10 | **LIKE (contains)** | `WHERE name LIKE '%kumar%'` | `{name:{$regex:/kumar/i}}` |
| 11 | **BETWEEN** | `WHERE salary BETWEEN 50000 AND 80000` | `{salary:{$gte:50000,$lte:80000}}` |
| 12 | **IS NULL** | `WHERE dept IS NULL` | `{dept:null}` or `{dept:{$exists:false}}` |
| 13 | **IS NOT NULL** | `WHERE dept IS NOT NULL` | `{dept:{$ne:null}}` or `{dept:{$exists:true}}` |
| 14 | **Sort ascending** | `ORDER BY name ASC` | `.sort({name:1})` |
| 15 | **Sort descending** | `ORDER BY salary DESC` | `.sort({salary:-1})` |
| 16 | **Limit** | `LIMIT 5` / `SELECT TOP 5` | `.limit(5)` |
| 17 | **Skip + Limit** | `LIMIT 5 OFFSET 10` | `.skip(10).limit(5)` |
| 18 | **Count** | `SELECT COUNT(*) FROM employees` | `db.employees.countDocuments({})` |
| 19 | **Count with filter** | `SELECT COUNT(*) FROM emp WHERE dept='Eng'` | `db.employees.countDocuments({dept:"Eng"})` |
| 20 | **Distinct** | `SELECT DISTINCT dept FROM employees` | `db.employees.distinct("dept")` |
| 21 | **GROUP BY + COUNT** | `SELECT dept, COUNT(*) FROM emp GROUP BY dept` | `aggregate([{$group:{_id:"$dept", count:{$sum:1}}}])` |
| 22 | **GROUP BY + AVG** | `SELECT dept, AVG(salary) FROM emp GROUP BY dept` | `aggregate([{$group:{_id:"$dept", avg:{$avg:"$salary"}}}])` |
| 23 | **GROUP BY + SUM** | `SELECT dept, SUM(salary) FROM emp GROUP BY dept` | `aggregate([{$group:{_id:"$dept", total:{$sum:"$salary"}}}])` |
| 24 | **HAVING** | `GROUP BY dept HAVING COUNT(*)>2` | `aggregate([{$group:{_id:"$dept",c:{$sum:1}}},{$match:{c:{$gt:2}}}])` |
| 25 | **JOIN** | `SELECT * FROM emp JOIN dept ON emp.deptID=dept.deptID` | `aggregate([{$lookup:{from:"dept",localField:"deptID",foreignField:"deptID",as:"deptInfo"}}])` |
| 26 | **LEFT JOIN** | `SELECT * FROM emp LEFT JOIN dept ON ...` | `$lookup` + `{$unwind:{path:"$deptInfo",preserveNullAndEmptyArrays:true}}` |
| 27 | **INSERT one** | `INSERT INTO emp(name,salary) VALUES('Rahul',75000)` | `db.emp.insertOne({name:"Rahul",salary:75000})` |
| 28 | **INSERT many** | `INSERT INTO emp VALUES (...), (...), (...)` | `db.emp.insertMany([{...},{...},{...}])` |
| 29 | **UPDATE** | `UPDATE emp SET salary=60000 WHERE name='Rahul'` | `db.emp.updateOne({name:"Rahul"},{$set:{salary:60000}})` |
| 30 | **UPDATE increment** | `UPDATE emp SET salary=salary+5000 WHERE dept='Eng'` | `db.emp.updateMany({dept:"Eng"},{$inc:{salary:5000}})` |
| 31 | **DELETE** | `DELETE FROM emp WHERE salary<30000` | `db.emp.deleteMany({salary:{$lt:30000}})` |
| 32 | **DELETE all** | `DELETE FROM emp` / `TRUNCATE TABLE emp` | `db.emp.deleteMany({})` |
| 33 | **Create table / collection** | `CREATE TABLE emp (id INT PK, ...)` | `db.createCollection("emp")` (or auto-created on insert) |
| 34 | **Drop table / collection** | `DROP TABLE emp` | `db.emp.drop()` |
| 35 | **Create index** | `CREATE INDEX idx ON emp(salary)` | `db.emp.createIndex({salary:1})` |
| 36 | **Explain query** | `EXPLAIN SELECT * FROM emp WHERE salary>50000` | `db.emp.find({salary:{$gt:50000}}).explain()` |
| 37 | **MAX** | `SELECT MAX(salary) FROM emp` | `db.emp.find().sort({salary:-1}).limit(1)` or `aggregate $group $max` |
| 38 | **MIN** | `SELECT MIN(salary) FROM emp` | `db.emp.find().sort({salary:1}).limit(1)` or `aggregate $group $min` |
| 39 | **Update nested** | N/A (normalized) | `db.emp.updateOne({name:"Rahul"},{$set:{"address.city":"Pune"}})` |
| 40 | **Array push** | N/A (separate table) | `db.emp.updateOne({name:"Rahul"},{$push:{skills:"K8s"}})` |

---

## Quick-Reference Cheat Sheet

### SQL Order of Execution
```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

### MongoDB Aggregation Pipeline Order
```
$match → $unwind → $group → $sort → $project → $limit/$skip
```
(Place `$match` early to reduce documents processed.)

### Redis Key Naming Convention
```
entity:id:field
Examples: user:1:name, session:abc123, cache:products:electronics, ratelimit:user:42
```

### Exam Tips
1. **DDL vs DML:** DDL = structure (CREATE, ALTER, DROP, TRUNCATE), DML = data (INSERT, UPDATE, DELETE, SELECT)
2. **WHERE vs HAVING:** WHERE filters rows before grouping; HAVING filters groups after aggregation
3. **JOIN types:** Know the Venn diagram for each. INNER = intersection, LEFT = all left + matched right, FULL = everything
4. **Correlated subquery:** References outer query — re-executes for each outer row (slower than non-correlated)
5. **NOT IN pitfall:** If subquery returns NULL, entire NOT IN returns empty. Use NOT EXISTS instead
6. **Window functions:** Don't collapse rows (unlike GROUP BY). RANK skips, DENSE_RANK doesn't
7. **MongoDB $unwind:** Converts one document with an array of N elements into N documents
8. **MongoDB $lookup:** The NoSQL "JOIN" — always returns an array field
9. **Redis sorted sets:** The go-to structure for leaderboards and priority queues
10. **Embedding vs Referencing:** Embed for read-together data; reference for independent, large, or many-to-many data

---

*End of Reference*
