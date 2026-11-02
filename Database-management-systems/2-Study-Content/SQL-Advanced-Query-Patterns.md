# SQL Advanced Query Patterns and Examples

> BITS Pilani — **SSZG507: Modern Database Systems**
>
> **Reference:** Class SQL Query Examples

---

## Table of Contents

- [1. Schema Design & Constraints](#1-schema-design--constraints)
- [2. Data Manipulation Fundamentals](#2-data-manipulation-fundamentals)
- [3. Basic SELECT Queries](#3-basic-select-queries)
- [4. Aggregate Functions](#4-aggregate-functions)
- [5. GROUP BY and HAVING](#5-group-by-and-having)
- [6. JOINs and Multiple Tables](#6-joins-and-multiple-tables)
- [7. Subqueries](#7-subqueries)
- [8. Correlated Subqueries](#8-correlated-subqueries)
- [9. Views](#9-views)
- [10. Advanced Patterns](#10-advanced-patterns)

---

## 1. Schema Design & Constraints

### Creating Tables with Constraints

**Primary Key Constraint:**
```sql
CREATE TABLE EMPLOYEE (
    Fname VARCHAR(30) NOT NULL,
    Minit VARCHAR(30),
    Lname VARCHAR(30),
    Ssn CHAR(9),
    Bdate DATE,
    Address VARCHAR(100),
    Gender CHAR(1),
    Salary DECIMAL(10,2) NOT NULL,
    Super_ssn CHAR(9),
    Dno INT,
    PRIMARY KEY(Ssn)
);
```

**Foreign Key Constraint:**
```sql
CREATE TABLE EMPLOYEE (
    Fname VARCHAR(30) NOT NULL,
    Lname VARCHAR(30),
    Ssn CHAR(9),
    Salary DECIMAL(10,2) NOT NULL,
    Dno INTEGER,
    PRIMARY KEY(Ssn),
    FOREIGN KEY(Dno) REFERENCES Department(Dnumber)
);

CREATE TABLE DEPARTMENT(
    Dname VARCHAR(15) NOT NULL,
    Dnumber INT PRIMARY KEY,
    Mgrssn CHAR(9) NOT NULL,
    Mgrstartdate DATE,
    FOREIGN KEY(Mgrssn) REFERENCES EMPLOYEE(SSN)
);
```

**CHECK Constraint:**
```sql
CREATE TABLE DEPARTMENT(
    Dname VARCHAR(15) NOT NULL,
    Dnumber INT CHECK (Dnumber > 0 AND Dnumber < 21),
    PRIMARY KEY(Dnumber)
);
```

**DEFAULT Values:**
```sql
CREATE TABLE EMPLOYEE (
    Fname VARCHAR(30) NOT NULL,
    Lname VARCHAR(30) DEFAULT 'abc',
    Ssn CHAR(9),
    Salary DECIMAL(10,2) NOT NULL,
    PRIMARY KEY(Ssn)
);
```

**Self-Referencing Foreign Key (Manager-Employee):**
```sql
CREATE TABLE EMPLOYEE (
    Fname VARCHAR(30) NOT NULL,
    Ssn CHAR(9),
    Super_ssn CHAR(9) NOT NULL,
    Dno INTEGER NOT NULL,
    PRIMARY KEY(Ssn),
    FOREIGN KEY(Super_ssn) REFERENCES EMPLOYEE(Ssn)
);
```

### Referential Integrity Actions

**ON DELETE SET NULL:**
```sql
CREATE TABLE EMPLOYEE (
    Ssn CHAR(9) PRIMARY KEY,
    Dno INTEGER,
    FOREIGN KEY(Dno) REFERENCES Department(Dnumber) 
        ON DELETE SET NULL
);
-- If department deleted, employee's Dno becomes NULL
```

**ON UPDATE CASCADE:**
```sql
CREATE TABLE EMPLOYEE (
    Ssn CHAR(9) PRIMARY KEY,
    Dno INTEGER,
    FOREIGN KEY(Dno) REFERENCES Department(Dnumber) 
        ON UPDATE CASCADE
);
-- If Dnumber updated, employee's Dno updates automatically
```

### Altering Tables

```sql
-- Add column
ALTER TABLE EMPLOYEE ADD AGE INT;

-- Drop column
ALTER TABLE EMPLOYEE DROP AGE;

-- Rename column
ALTER TABLE Department CHANGE Dno Dnumber INT;
ALTER TABLE Department CHANGE Dnumber Dno INT;

-- Add constraint
ALTER TABLE EMPLOYEE 
ADD CONSTRAINT EMPFK FOREIGN KEY (Dno) 
REFERENCES Department(Dnumber);

-- Drop constraint
ALTER TABLE DEPARTMENT 
DROP CONSTRAINT DEPTFK;

-- View constraints in schema
SELECT * FROM information_schema.table_constraints
WHERE constraint_schema = 'test6';
```

---

## 2. Data Manipulation Fundamentals

### INSERT Operations

**Single Row Insert:**
```sql
INSERT INTO Employee(Fname, Ssn, Bdate, Gender, Salary, Super_ssn, Dno)
VALUES ('Rohit', '111111111', '1987-07-01', 'M', 80000.20, '222222222', 10);
```

**Partial Insert (with defaults):**
```sql
INSERT INTO Employee(Fname, Ssn, Bdate, Gender, Salary, Super_ssn, Dno)
VALUES ('Alok', '333333333', '1987-07-02', 'M', 90000.20, '333333333', 10);
```

**Multiple Row Insert:**
```sql
INSERT INTO Employee VALUES
('Rohit', 'k', 'goel', '111111111', '1987-07-01', 'Noida, UP', 'M', 80000.20, '222222222', 10),
('Mohan', 'k', 'goel', '222222222', '1987-07-01', 'Noida, UP', 'M', 80000.20, '222222222', 10);
```

**Insert from SELECT:**
```sql
INSERT INTO Employee_Backup
SELECT * FROM Employee WHERE Dno = 5;
```

### UPDATE Operations

**Simple Update:**
```sql
UPDATE EMPLOYEE 
SET Lname = 'sehgal' 
WHERE Ssn = '111111111';
```

**Multiple Column Update:**
```sql
UPDATE EMPLOYEE 
SET Salary = 85000, Lname = 'Sharma' 
WHERE Ssn = '111111111';
```

**Conditional Update:**
```sql
UPDATE EMPLOYEE 
SET Salary = Salary * 1.10 
WHERE Dno = 5;
-- 10% salary increase for department 5
```

### DELETE Operations

**Delete Specific Rows:**
```sql
DELETE FROM EMPLOYEE 
WHERE Fname = 'Alok';

DELETE FROM EMPLOYEE 
WHERE Dno = 10;
```

**Delete All Rows:**
```sql
DELETE FROM EMPLOYEE;
-- Removes all data but keeps table structure
```

---

## 3. Basic SELECT Queries

### Simple SELECT

```sql
-- All columns, all rows
SELECT * FROM Employee;

-- Specific columns
SELECT Ssn, Fname FROM Employee;

-- With aliases
SELECT Fname, Lname FROM Employee;
```

### Filtering with WHERE

```sql
-- Equality
SELECT * FROM Employee WHERE Dno = 5;

-- Comparison operators
SELECT * FROM Employee WHERE Salary > 50000;
SELECT * FROM Employee WHERE Salary < 100000;
SELECT * FROM Employee WHERE Salary >= 80000.20;

-- Range
SELECT * FROM Employee 
WHERE Salary BETWEEN 50000.20 AND 80000.20;

-- Multiple conditions (AND)
SELECT * FROM Employee 
WHERE Salary > 50000 AND Salary < 100000;

-- OR conditions
SELECT * FROM Employee 
WHERE Dno = 5 OR Dno = 10;
```

### Text Matching (LIKE)

```sql
-- Starts with 'noida' (case-insensitive in most DBs)
SELECT Lname, Fname FROM Employee 
WHERE Address LIKE '%noida%';

-- Pattern with wildcards
-- % = any number of characters
-- _ = single character
SELECT * FROM Employee 
WHERE Bdate LIKE '__7_______';
-- Matches year starting with 7 (1970s)

-- Specific pattern
SELECT * FROM Employee 
WHERE Fname LIKE 'R%';
-- Names starting with R
```

### DISTINCT

```sql
-- Remove duplicates
SELECT DISTINCT Minit FROM Employee;

-- Count unique departments
SELECT COUNT(DISTINCT Dno) FROM Employee;
```

### ORDER BY

```sql
-- Ascending (default)
SELECT * FROM Employee 
ORDER BY Lname ASC;

-- Descending
SELECT * FROM Employee 
ORDER BY Salary DESC;

-- Multiple columns
SELECT * FROM Employee 
ORDER BY Dno ASC, Lname ASC;

-- Mixed order
SELECT * FROM Employee 
ORDER BY Dno ASC, Salary DESC;
```

### IN Operator

```sql
SELECT * FROM Employee 
WHERE Dno IN (5, 10, 15);
-- Equivalent to: Dno=5 OR Dno=10 OR Dno=15

SELECT * FROM Employee 
WHERE Fname IN ('Rohit', 'Mohan', 'Seema');
```

---

## 4. Aggregate Functions

### COUNT, SUM, AVG, MAX, MIN

```sql
-- Count total employees
SELECT COUNT(*) FROM Employee;

-- Count employees in department 5
SELECT COUNT(*) FROM Employee WHERE Dno = 5;

-- Sum of salaries
SELECT SUM(Salary) FROM Employee;

-- Average salary
SELECT AVG(Salary) FROM Employee;

-- Maximum salary
SELECT MAX(Salary) FROM Employee;

-- Minimum salary
SELECT MIN(Salary) FROM Employee;

-- Multiple aggregates
SELECT 
    COUNT(*) AS Total_Employees,
    AVG(Salary) AS Avg_Salary,
    MAX(Salary) AS Max_Salary,
    MIN(Salary) AS Min_Salary
FROM Employee;
```

### Aggregate with Conditions

```sql
-- Average salary of department 5
SELECT AVG(Salary) FROM Employee WHERE Dno = 5;

-- Count employees earning over 100000
SELECT COUNT(*) FROM Employee WHERE Salary > 100000;
```

---

## 5. GROUP BY and HAVING

### GROUP BY

```sql
-- Total employees per department
SELECT Dno, COUNT(*) AS Num_Employees
FROM Employee
GROUP BY Dno;

-- Average salary per department
SELECT Dno, AVG(Salary) AS Avg_Salary
FROM Employee
GROUP BY Dno;

-- Multiple grouping columns
SELECT Dno, Gender, COUNT(*) AS Num_Employees
FROM Employee
GROUP BY Dno, Gender;
```

### HAVING Clause

```sql
-- Departments with more than 2 employees
SELECT Dno, COUNT(*) AS Num_Employees
FROM Employee
GROUP BY Dno
HAVING COUNT(*) > 2;

-- Departments with average salary > 80000
SELECT Dno, AVG(Salary) AS Avg_Salary
FROM Employee
GROUP BY Dno
HAVING AVG(Salary) > 80000;
```

### Complex GROUP BY Example

```sql
-- For each department: dept number, num employees, avg salary
SELECT 
    Dno,
    COUNT(*) AS Num_Employees,
    AVG(Salary) AS Avg_Salary
FROM Employee
GROUP BY Dno
ORDER BY Dno;
```

---

## 6. JOINs and Multiple Tables

### INNER JOIN

```sql
-- Get fname, lname, dname of all employees
SELECT E.Fname, E.Lname, D.Dname
FROM Employee E
INNER JOIN Department D ON E.Dno = D.Dnumber;

-- Join with WHERE condition
SELECT E.Fname, E.Lname, D.Dname
FROM Employee E
JOIN Department D ON E.Dno = D.Dnumber
WHERE E.Dno = 5;
```

### LEFT JOIN

```sql
-- All employees and their departments (include unassigned employees)
SELECT E.Fname, E.Lname, D.Dname
FROM Employee E
LEFT JOIN Department D ON E.Dno = D.Dnumber;
```

### Three-Table JOIN

```sql
-- Employee, Department, and Projects they work on
SELECT E.Fname, E.Lname, D.Dname, P.Pname, W.working_hours
FROM Employee E
JOIN Department D ON E.Dno = D.Dnumber
JOIN Works_on W ON E.Ssn = W.Essn
JOIN Project P ON W.Pnum = P.Pnumber;
```

### Self-JOIN (Manager-Employee)

```sql
-- Employees and their managers
SELECT E.Fname AS Employee_Name, M.Fname AS Manager_Name
FROM Employee E
JOIN Employee M ON E.Super_ssn = M.Ssn;
```

### JOIN with Ordering

```sql
-- Fname, lname of employees and projects they work on
-- Ordered by project name, then by last name, then first name
SELECT E.Fname, E.Lname, P.Pname
FROM Employee E
JOIN Works_on W ON E.Ssn = W.Essn
JOIN Project P ON W.Pnum = P.Pnumber
ORDER BY P.Pname, E.Lname, E.Fname;
```

---

## 7. Subqueries

### Subquery in WHERE

```sql
-- Employees in Research department
SELECT Fname, Lname, Address
FROM Employee
WHERE Dno IN (
    SELECT Dnumber 
    FROM Department 
    WHERE Dname = 'Research'
);

-- Employees earning more than average
SELECT Fname, Lname, Salary
FROM Employee
WHERE Salary > (
    SELECT AVG(Salary) 
    FROM Employee
);

-- Employees who work on projects
SELECT Fname, Lname
FROM Employee
WHERE Ssn IN (
    SELECT Essn 
    FROM Works_on
);
```

### Nested Subqueries

```sql
-- Projects with more than one employee
SELECT P.Pnumber, P.Pname, COUNT(W.Essn) AS Num_Employees
FROM Project P
JOIN Works_on W ON P.Pnumber = W.Pnum
GROUP BY P.Pnumber, P.Pname
HAVING COUNT(W.Essn) > 1;
```

### EXISTS / NOT EXISTS

```sql
-- Employees who have dependents
SELECT E.Fname, E.Lname
FROM Employee E
WHERE EXISTS (
    SELECT 1 
    FROM Dependent D 
    WHERE D.Essn = E.Ssn
);

-- Employees who don't have dependents
SELECT E.Fname, E.Lname
FROM Employee E
WHERE NOT EXISTS (
    SELECT 1 
    FROM Dependent D 
    WHERE D.Essn = E.Ssn
);
```

---

## 8. Correlated Subqueries

### Basic Correlated Subquery

```sql
-- Employees whose name matches a dependent's name
SELECT E.Fname, E.Lname
FROM Employee E
WHERE E.Ssn IN (
    SELECT Essn 
    FROM Dependent 
    WHERE Essn = E.Ssn 
    AND Depname = E.Fname
);
```

### Comparison Operators

```sql
-- Employees with above-average salary in their department
SELECT E.Fname, E.Lname, E.Salary
FROM Employee E
WHERE E.Salary > (
    SELECT AVG(Salary)
    FROM Employee E2
    WHERE E2.Dno = E.Dno
);
```

---

## 9. Views

### Creating Views

```sql
-- Simple view
CREATE VIEW Works_On1 AS
SELECT Fname, Lname, Pname, working_hours
FROM Employee, Project, Works_on
WHERE Ssn = Essn AND Pnum = Pnumber;

-- Select from view
SELECT * FROM Works_On1;

-- Filter view
SELECT * FROM Works_On1 
WHERE working_hours > 5;
```

### Updating Through Views

```sql
-- Update via view (may have restrictions)
UPDATE Works_On1
SET Pname = 'ProductK'
WHERE Lname = 'goel' AND Fname = 'rohit' AND Pname = 'ProjectX';
```

### View Advantages

- **Simplification:** Hide complex joins
- **Security:** Control column visibility
- **Reusability:** Reference multiple queries
- **Logical Data Independence:** Change underlying tables without affecting applications

---

## 10. Advanced Patterns

### Common Table Expressions (CTE)

```sql
-- Define temporary result set
WITH DepartmentStats AS (
    SELECT 
        Dno,
        COUNT(*) AS Num_Employees,
        AVG(Salary) AS Avg_Salary
    FROM Employee
    GROUP BY Dno
)
SELECT * 
FROM DepartmentStats 
WHERE Avg_Salary > 80000;
```

### Window Functions (PostgreSQL, SQL Server)

```sql
-- Running total of salaries
SELECT 
    Fname, 
    Salary,
    SUM(Salary) OVER (ORDER BY Salary) AS Running_Total
FROM Employee;

-- Rank employees by salary
SELECT 
    Fname, 
    Salary,
    RANK() OVER (ORDER BY Salary DESC) AS Salary_Rank
FROM Employee;
```

### UNION

```sql
-- Combine results from two queries
SELECT Fname AS Name FROM Employee
UNION
SELECT Dname AS Name FROM Department;

-- UNION ALL keeps duplicates
SELECT Fname FROM Employee WHERE Dno = 5
UNION ALL
SELECT Dname FROM Department;
```

### Case Statements

```sql
-- Categorize employees
SELECT 
    Fname, 
    Salary,
    CASE 
        WHEN Salary > 100000 THEN 'High'
        WHEN Salary > 75000 THEN 'Medium'
        ELSE 'Low'
    END AS Salary_Category
FROM Employee;
```

---

## Summary

SQL provides powerful tools for querying data at scale. Key patterns include:
- **Constraints:** Enforce data integrity
- **Joins:** Combine data from multiple tables
- **Subqueries:** Reuse query results
- **Aggregation:** Summarize data
- **Views:** Abstract complexity

Mastering these patterns enables efficient, maintainable database applications.

