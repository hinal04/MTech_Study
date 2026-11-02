# DBMS Design & Normalisation — Worked Examples and Practice

> ER Diagram Design Exercises + Normalisation Step-by-Step Examples
>
> This file focuses on **doing** — each problem walks through the complete design process with solutions.

---
---

# PART A — ER Design Exercises

---

## Exercise 1: University Database

### Problem Statement

Design an ER model for a university with these requirements:

- The university has many **departments**. Each department has a unique name, a unique department number, a phone number, and an office location.
- Each department is headed by one **faculty** member. A faculty member can head at most one department.
- The university offers **courses**. Each course has a unique course code, title, and credits. A course is offered by exactly one department, but a department can offer many courses.
- **Students** have a unique student ID, name, email, date of birth, and programme. A student belongs to one department.
- Students **enrol** in courses. For each enrolment, we record the semester and the grade obtained. A student can enrol in many courses, and a course can have many students.
- Faculty members have a unique faculty ID, name, email, designation, and salary. A faculty member belongs to one department.
- A faculty member **teaches** courses. A faculty can teach many courses in a semester, and a course section is taught by one faculty.
- Each student has a **faculty advisor** from their department.

### Step 1: Identify Entities and Attributes

| Entity | Type | Attributes | Key |
|---|---|---|---|
| **DEPARTMENT** | Strong | Dept_No, Dept_Name, Phone, Office | Dept_No |
| **FACULTY** | Strong | Faculty_ID, Name, Email, Designation, Salary | Faculty_ID |
| **COURSE** | Strong | Course_Code, Title, Credits | Course_Code |
| **STUDENT** | Strong | Student_ID, Name, Email, DOB, Programme | Student_ID |

### Step 2: Identify Relationships

| Relationship | Entities | Cardinality | Participation | Attributes |
|---|---|---|---|---|
| **HEADS** | Faculty heads Department | 1:1 | Faculty: partial, Dept: total | — |
| **BELONGS_TO (Faculty)** | Faculty belongs to Department | N:1 | Faculty: total, Dept: total | — |
| **OFFERS** | Department offers Course | 1:N | Dept: partial, Course: total | — |
| **BELONGS_TO (Student)** | Student belongs to Department | N:1 | Student: total, Dept: total | — |
| **ENROLS** | Student enrols in Course | M:N | Both partial | Semester, Grade |
| **TEACHES** | Faculty teaches Course | 1:N per semester | Faculty: partial, Course: total | Semester |
| **ADVISES** | Faculty advises Student | 1:N | Faculty: partial, Student: total | — |

### Step 3: ER-to-Relational Mapping

```sql
-- Strong entities → Tables with PK
DEPARTMENT (
    Dept_No     INT PRIMARY KEY,
    Dept_Name   VARCHAR(100) UNIQUE NOT NULL,
    Phone       VARCHAR(15),
    Office      VARCHAR(50),
    Head_ID     INT UNIQUE,                    -- 1:1 (HEADS): FK on total-participation side
    FOREIGN KEY (Head_ID) REFERENCES FACULTY(Faculty_ID)
);

FACULTY (
    Faculty_ID  INT PRIMARY KEY,
    Name        VARCHAR(100) NOT NULL,
    Email       VARCHAR(100) UNIQUE,
    Designation VARCHAR(50),
    Salary      DECIMAL(10,2),
    Dept_No     INT NOT NULL,                  -- N:1 (BELONGS_TO): FK on "N" side
    FOREIGN KEY (Dept_No) REFERENCES DEPARTMENT(Dept_No)
);

COURSE (
    Course_Code VARCHAR(10) PRIMARY KEY,
    Title       VARCHAR(200) NOT NULL,
    Credits     INT CHECK (Credits > 0),
    Dept_No     INT NOT NULL,                  -- 1:N (OFFERS): FK on "N" side
    FOREIGN KEY (Dept_No) REFERENCES DEPARTMENT(Dept_No)
);

STUDENT (
    Student_ID  INT PRIMARY KEY,
    Name        VARCHAR(100) NOT NULL,
    Email       VARCHAR(100) UNIQUE,
    DOB         DATE,
    Programme   VARCHAR(50),
    Dept_No     INT NOT NULL,                  -- N:1 (BELONGS_TO): FK on "N" side
    Advisor_ID  INT,                           -- 1:N (ADVISES): FK on "N" side
    FOREIGN KEY (Dept_No) REFERENCES DEPARTMENT(Dept_No),
    FOREIGN KEY (Advisor_ID) REFERENCES FACULTY(Faculty_ID)
);

-- M:N relationship → Junction table
ENROLMENT (
    Student_ID  INT,
    Course_Code VARCHAR(10),
    Semester    VARCHAR(20),
    Grade       CHAR(2),
    PRIMARY KEY (Student_ID, Course_Code, Semester),
    FOREIGN KEY (Student_ID) REFERENCES STUDENT(Student_ID),
    FOREIGN KEY (Course_Code) REFERENCES COURSE(Course_Code)
);

-- 1:N with attribute → Can go in COURSE or separate table
TEACHES (
    Faculty_ID  INT,
    Course_Code VARCHAR(10),
    Semester    VARCHAR(20),
    PRIMARY KEY (Faculty_ID, Course_Code, Semester),
    FOREIGN KEY (Faculty_ID) REFERENCES FACULTY(Faculty_ID),
    FOREIGN KEY (Course_Code) REFERENCES COURSE(Course_Code)
);
```

### Mapping Rules Applied

| ER Construct | Mapping Applied |
|---|---|
| Strong entities (4) | Each → table with its own PK. |
| HEADS (1:1) | Head_ID as FK + UNIQUE in DEPARTMENT (total-participation side). |
| BELONGS_TO Faculty (N:1) | Dept_No FK in FACULTY (the "N" side). |
| OFFERS (1:N) | Dept_No FK in COURSE (the "N" side). |
| BELONGS_TO Student (N:1) | Dept_No FK in STUDENT. |
| ADVISES (1:N) | Advisor_ID FK in STUDENT (the "N" side). |
| ENROLS (M:N) | Junction table ENROLMENT with composite PK + relationship attributes (Semester, Grade). |
| TEACHES (1:N with attribute) | Separate table TEACHES (since it has a Semester attribute making it effectively M:N per semester). |

---

## Exercise 2: Hospital Management System

### Problem Statement

A hospital needs a database with these requirements:

- **Patients** have a unique Patient_ID, name, phone, address, date of birth, and blood group.
- **Doctors** have a unique Doctor_ID, name, specialisation, phone, and consultation fee.
- A doctor **treats** patients. For each treatment, we record the date, diagnosis, and prescription. A patient can be treated by many doctors, and a doctor treats many patients.
- The hospital has **wards**. Each ward has a unique ward number, name, type (general/ICU/private), and capacity.
- A patient is **admitted** to a ward. For each admission, we record the admit date, discharge date, and bed number. A patient may have multiple admissions over time.
- Doctors work in **departments** (Cardiology, Neurology, etc.). Each department has a unique name and a head doctor.
- Each patient has a list of **allergies** (multi-valued attribute).

### Solution: Entities, Relationships, and Relational Mapping

**Entities:**

| Entity | Key | Other Attributes |
|---|---|---|
| PATIENT | Patient_ID | Name, Phone, Address (composite: Street, City, PIN), DOB, Blood_Group |
| DOCTOR | Doctor_ID | Name, Specialisation, Phone, Fee |
| WARD | Ward_No | Ward_Name, Type, Capacity |
| DEPARTMENT | Dept_Name (PK) | Head_Doctor_ID (FK) |

**Relationships:**

| Relationship | Type | Attributes |
|---|---|---|
| TREATS | M:N (Doctor–Patient) | Date, Diagnosis, Prescription |
| ADMITTED_TO | M:N (Patient–Ward, over time) | Admit_Date, Discharge_Date, Bed_No |
| WORKS_IN | N:1 (Doctor–Department) | — |
| HEADS | 1:1 (Doctor–Department) | — |

**Multi-valued attribute:** Patient allergies → separate table.

**Relational schema:**

```sql
PATIENT (
    Patient_ID  INT PRIMARY KEY,
    Name        VARCHAR(100) NOT NULL,
    Phone       VARCHAR(15),
    Street      VARCHAR(100),           -- Composite: Address broken into parts
    City        VARCHAR(50),
    PIN         VARCHAR(10),
    DOB         DATE,
    Blood_Group VARCHAR(5)
);

-- Multi-valued attribute → Separate table
PATIENT_ALLERGY (
    Patient_ID  INT,
    Allergy     VARCHAR(100),
    PRIMARY KEY (Patient_ID, Allergy),
    FOREIGN KEY (Patient_ID) REFERENCES PATIENT(Patient_ID) ON DELETE CASCADE
);

DEPARTMENT (
    Dept_Name       VARCHAR(50) PRIMARY KEY,
    Head_Doctor_ID  INT UNIQUE,
    FOREIGN KEY (Head_Doctor_ID) REFERENCES DOCTOR(Doctor_ID)
);

DOCTOR (
    Doctor_ID      INT PRIMARY KEY,
    Name           VARCHAR(100) NOT NULL,
    Specialisation VARCHAR(50),
    Phone          VARCHAR(15),
    Fee            DECIMAL(8,2),
    Dept_Name      VARCHAR(50),                -- N:1 WORKS_IN
    FOREIGN KEY (Dept_Name) REFERENCES DEPARTMENT(Dept_Name)
);

WARD (
    Ward_No    INT PRIMARY KEY,
    Ward_Name  VARCHAR(50),
    Type       VARCHAR(20) CHECK (Type IN ('General', 'ICU', 'Private')),
    Capacity   INT
);

-- M:N: TREATS
TREATMENT (
    Doctor_ID   INT,
    Patient_ID  INT,
    Treat_Date  DATE,
    Diagnosis   VARCHAR(500),
    Prescription VARCHAR(500),
    PRIMARY KEY (Doctor_ID, Patient_ID, Treat_Date),
    FOREIGN KEY (Doctor_ID) REFERENCES DOCTOR(Doctor_ID),
    FOREIGN KEY (Patient_ID) REFERENCES PATIENT(Patient_ID)
);

-- M:N: ADMITTED_TO (patient can be admitted multiple times)
ADMISSION (
    Patient_ID     INT,
    Ward_No        INT,
    Admit_Date     DATE,
    Discharge_Date DATE,
    Bed_No         INT,
    PRIMARY KEY (Patient_ID, Ward_No, Admit_Date),
    FOREIGN KEY (Patient_ID) REFERENCES PATIENT(Patient_ID),
    FOREIGN KEY (Ward_No) REFERENCES WARD(Ward_No)
);
```

### Key Design Decisions Explained

1. **Composite attribute (Address):** Broken into simple components (Street, City, PIN) — stored directly in PATIENT table, not as a separate table.
2. **Multi-valued attribute (Allergies):** Separate PATIENT_ALLERGY table with composite PK (Patient_ID, Allergy).
3. **TREATS (M:N with attributes):** Junction table TREATMENT. Composite PK includes Date because the same doctor-patient pair can have multiple treatments on different dates.
4. **ADMITTED_TO (M:N over time):** Junction table ADMISSION. PK includes Admit_Date since the same patient can be re-admitted to the same ward on different dates.
5. **HEADS (1:1):** Head_Doctor_ID as UNIQUE FK in DEPARTMENT (total-participation side — every department must have a head).

---

## Exercise 3: E-Commerce Platform

### Problem Statement

Design an ER model for an e-commerce platform:

- **Customers** have Customer_ID, name, email, phone, and multiple addresses (shipping addresses).
- **Products** have Product_ID, name, description, price, and stock quantity. Each product belongs to a **category**.
- **Categories** have Category_ID, name, and optional parent category (for hierarchical categories like Electronics > Phones > Smartphones).
- Customers place **orders**. Each order has Order_ID, order date, total amount, and status (pending/shipped/delivered/cancelled).
- Each order contains one or more **order items**. For each item, we record the product, quantity, and unit price at the time of ordering.
- **Sellers** have Seller_ID, name, rating. A seller sells many products. A product can be sold by multiple sellers (different sellers may offer the same product at different prices).
- Each order is shipped to one of the customer's addresses.

### Solution

```sql
CUSTOMER (
    Customer_ID  INT PRIMARY KEY,
    Name         VARCHAR(100) NOT NULL,
    Email        VARCHAR(100) UNIQUE NOT NULL,
    Phone        VARCHAR(15)
);

-- Multi-valued: Customer addresses
CUSTOMER_ADDRESS (
    Address_ID   INT PRIMARY KEY,           -- Surrogate key
    Customer_ID  INT NOT NULL,
    Label        VARCHAR(20),               -- 'Home', 'Office', 'Other'
    Street       VARCHAR(200),
    City         VARCHAR(50),
    State        VARCHAR(50),
    PIN          VARCHAR(10),
    FOREIGN KEY (Customer_ID) REFERENCES CUSTOMER(Customer_ID)
);

-- Recursive relationship: Category hierarchy
CATEGORY (
    Category_ID    INT PRIMARY KEY,
    Category_Name  VARCHAR(100) NOT NULL,
    Parent_ID      INT,                     -- Self-referencing FK (recursive 1:N)
    FOREIGN KEY (Parent_ID) REFERENCES CATEGORY(Category_ID)
);

PRODUCT (
    Product_ID   INT PRIMARY KEY,
    Name         VARCHAR(200) NOT NULL,
    Description  TEXT,
    Price        DECIMAL(10,2) NOT NULL,
    Stock_Qty    INT DEFAULT 0,
    Category_ID  INT,                       -- 1:N: Category offers Products
    FOREIGN KEY (Category_ID) REFERENCES CATEGORY(Category_ID)
);

SELLER (
    Seller_ID  INT PRIMARY KEY,
    Name       VARCHAR(100) NOT NULL,
    Rating     DECIMAL(3,2) CHECK (Rating BETWEEN 0 AND 5)
);

-- M:N: Seller sells Product (with seller-specific price)
SELLER_PRODUCT (
    Seller_ID   INT,
    Product_ID  INT,
    Seller_Price DECIMAL(10,2),
    PRIMARY KEY (Seller_ID, Product_ID),
    FOREIGN KEY (Seller_ID) REFERENCES SELLER(Seller_ID),
    FOREIGN KEY (Product_ID) REFERENCES PRODUCT(Product_ID)
);

ORDERS (
    Order_ID     INT PRIMARY KEY,
    Customer_ID  INT NOT NULL,
    Address_ID   INT NOT NULL,              -- Which address to ship to
    Order_Date   DATE DEFAULT CURRENT_DATE,
    Total_Amount DECIMAL(12,2),
    Status       VARCHAR(20) DEFAULT 'Pending'
                 CHECK (Status IN ('Pending','Shipped','Delivered','Cancelled')),
    FOREIGN KEY (Customer_ID) REFERENCES CUSTOMER(Customer_ID),
    FOREIGN KEY (Address_ID) REFERENCES CUSTOMER_ADDRESS(Address_ID)
);

-- Weak entity: ORDER_ITEM (depends on ORDER)
ORDER_ITEM (
    Order_ID     INT,
    Item_No      INT,                       -- Partial key within the order
    Product_ID   INT NOT NULL,
    Seller_ID    INT NOT NULL,
    Quantity     INT CHECK (Quantity > 0),
    Unit_Price   DECIMAL(10,2),             -- Price at time of order (not current price)
    PRIMARY KEY (Order_ID, Item_No),
    FOREIGN KEY (Order_ID) REFERENCES ORDERS(Order_ID),
    FOREIGN KEY (Product_ID) REFERENCES PRODUCT(Product_ID),
    FOREIGN KEY (Seller_ID) REFERENCES SELLER(Seller_ID)
);
```

### Design Highlights

| Feature | How it's handled |
|---|---|
| **Multi-valued attribute** (addresses) | Separate CUSTOMER_ADDRESS table with surrogate key. |
| **Recursive relationship** (category hierarchy) | Self-referencing FK: Parent_ID → CATEGORY(Category_ID). Root categories have Parent_ID = NULL. |
| **M:N with attribute** (seller-product with price) | Junction table SELLER_PRODUCT. |
| **Weak entity** (order item) | ORDER_ITEM depends on ORDERS. Composite PK = (Order_ID, Item_No). |
| **Historical price** | Unit_Price stored in ORDER_ITEM (snapshot at order time, not FK to current price). |
| **Shipping address** | Order references a specific address via Address_ID FK. |

---
---

# PART B — Normalisation Worked Examples

---

## Example 1: Step-by-Step Normalisation (UN-NORMALISED → BCNF)

### The Problem

A company stores the following data in a single flat table:

```
EMPLOYEE_PROJECT (
    Emp_ID, Emp_Name, Dept_ID, Dept_Name, Dept_Location,
    Proj_ID, Proj_Name, Hours, Salary
)
```

**Sample data:**

| Emp_ID | Emp_Name | Dept_ID | Dept_Name | Dept_Loc | Proj_ID | Proj_Name | Hours | Salary |
|---|---|---|---|---|---|---|---|---|
| 101 | Alice | D1 | Research | London | P1 | Alpha | 20 | 75000 |
| 101 | Alice | D1 | Research | London | P2 | Beta | 10 | 75000 |
| 102 | Bob | D2 | Sales | Mumbai | P1 | Alpha | 30 | 68000 |
| 103 | Carol | D1 | Research | London | P3 | Gamma | 40 | 82000 |

**Functional dependencies:**
```
Emp_ID → Emp_Name, Dept_ID, Salary
Dept_ID → Dept_Name, Dept_Location
Proj_ID → Proj_Name
Emp_ID, Proj_ID → Hours
```

### Step 1: Check 1NF

**Question:** Are all values atomic? Any repeating groups?

All values are atomic (single values per cell). No nested tables. However, there IS redundancy — Alice's name, department, and salary are repeated for each project she works on.

**Result: Already in 1NF.** ✓

### Step 2: Identify the Primary Key

The candidate key must uniquely identify each row. Let's check:

- `Emp_ID` alone? No — Alice has two rows (projects P1 and P2).
- `Proj_ID` alone? No — project P1 has two employees (Alice and Bob).
- `{Emp_ID, Proj_ID}`? Yes — each employee-project combination is unique.

**Primary Key: {Emp_ID, Proj_ID}**

### Step 3: Check 2NF (No Partial Dependencies)

**Question:** Do any non-key attributes depend on only PART of the composite key?

| FD | Depends on | Partial dependency? |
|---|---|---|
| Emp_ID → Emp_Name | Part of PK (Emp_ID only) | **YES — violates 2NF** |
| Emp_ID → Dept_ID | Part of PK (Emp_ID only) | **YES — violates 2NF** |
| Emp_ID → Salary | Part of PK (Emp_ID only) | **YES — violates 2NF** |
| Proj_ID → Proj_Name | Part of PK (Proj_ID only) | **YES — violates 2NF** |
| Dept_ID → Dept_Name | Not dependent on PK at all | Transitive (check in 3NF) |
| Dept_ID → Dept_Location | Not dependent on PK at all | Transitive (check in 3NF) |
| Emp_ID, Proj_ID → Hours | Full PK | No — this is a full dependency ✓ |

**Fix:** Decompose to remove partial dependencies.

```
EMPLOYEE (Emp_ID PK, Emp_Name, Dept_ID, Salary)
PROJECT (Proj_ID PK, Proj_Name)
EMP_PROJECT (Emp_ID, Proj_ID, Hours)  PK = {Emp_ID, Proj_ID}
```

**Result: Now in 2NF.** ✓

### Step 4: Check 3NF (No Transitive Dependencies)

**Question:** Do any non-key attributes depend on OTHER non-key attributes?

In EMPLOYEE: `Emp_ID → Dept_ID → Dept_Name, Dept_Location`

Dept_Name and Dept_Location depend on Dept_ID (a non-key attribute), NOT directly on Emp_ID. This is a **transitive dependency** — violates 3NF.

**Fix:** Decompose to remove the transitive dependency.

```
EMPLOYEE (Emp_ID PK, Emp_Name, Dept_ID FK, Salary)
DEPARTMENT (Dept_ID PK, Dept_Name, Dept_Location)
PROJECT (Proj_ID PK, Proj_Name)
EMP_PROJECT (Emp_ID FK, Proj_ID FK, Hours)  PK = {Emp_ID, Proj_ID}
```

**Result: Now in 3NF.** ✓

### Step 5: Check BCNF

**Question:** For every non-trivial FD X → Y, is X a superkey?

| FD | X | Is X a superkey? |
|---|---|---|
| Emp_ID → Emp_Name, Dept_ID, Salary | Emp_ID | Yes (PK of EMPLOYEE) ✓ |
| Dept_ID → Dept_Name, Dept_Location | Dept_ID | Yes (PK of DEPARTMENT) ✓ |
| Proj_ID → Proj_Name | Proj_ID | Yes (PK of PROJECT) ✓ |
| {Emp_ID, Proj_ID} → Hours | {Emp_ID, Proj_ID} | Yes (PK of EMP_PROJECT) ✓ |

**Result: Already in BCNF.** ✓

### Final Schema (BCNF)

```sql
DEPARTMENT (Dept_ID PK, Dept_Name, Dept_Location)
EMPLOYEE (Emp_ID PK, Emp_Name, Dept_ID FK→DEPARTMENT, Salary)
PROJECT (Proj_ID PK, Proj_Name)
EMP_PROJECT (Emp_ID FK, Proj_ID FK, Hours)  PK = (Emp_ID, Proj_ID)
```

**Anomalies eliminated:**
- ✓ No insertion anomaly: Can add a department without employees.
- ✓ No deletion anomaly: Deleting last employee doesn't lose department info.
- ✓ No update anomaly: Renaming a department changes one row in DEPARTMENT.

---

## Example 2: Finding Candidate Keys and Highest Normal Form

### Problem

Given: **R(A, B, C, D, E)** with FDs: F = {AB → C, C → D, D → E, E → A}

**Task:** Find all candidate keys. Determine the highest normal form.

### Step 1: Find Candidate Keys

An attribute that never appears on the right side of any FD must be in every candidate key. Looking at the FDs:
- Right sides: C, D, E, A
- **B never appears on the right** → B must be in every candidate key.

Now find what B⁺ (closure of {B}) determines:
- {B}⁺ = {B} — B alone doesn't determine anything.

Try {AB}:
- {A, B}⁺: Start with {A, B}
- AB → C: add C → {A, B, C}
- C → D: add D → {A, B, C, D}
- D → E: add E → {A, B, C, D, E} ← ALL attributes!
- **{AB} is a candidate key.**

Try {BC}:
- {B, C}⁺: Start with {B, C}
- C → D: add D → {B, C, D}
- D → E: add E → {B, C, D, E}
- E → A: add A → {A, B, C, D, E} ← ALL attributes!
- **{BC} is a candidate key.**

Try {BD}:
- {B, D}⁺: D → E → A, AB → C → {A, B, C, D, E} ✓
- **{BD} is a candidate key.**

Try {BE}:
- {B, E}⁺: E → A, AB → C, C → D → {A, B, C, D, E} ✓
- **{BE} is a candidate key.**

**All candidate keys: {AB}, {BC}, {BD}, {BE}**

**Prime attributes** (appear in at least one candidate key): A, B, C, D, E — ALL attributes are prime!

### Step 2: Determine Highest Normal Form

**Check 2NF:** Partial dependencies on composite keys?
- AB → C: full dependency (need both A and B) ✓
- But B is part of key {BC}, and {B} alone doesn't determine anything. No partial dependencies.
- **In 2NF.** ✓

**Check 3NF:** For each FD X → Y, either X is a superkey OR Y is a prime attribute.
- AB → C: AB is a superkey ✓
- C → D: C is not a superkey, but D is prime ✓
- D → E: D is not a superkey, but E is prime ✓
- E → A: E is not a superkey, but A is prime ✓
- **In 3NF.** ✓

**Check BCNF:** For each FD X → Y, X must be a superkey.
- AB → C: AB is a superkey ✓
- C → D: C is NOT a superkey (C⁺ = {C, D, E, A} — missing B) **✗ VIOLATES BCNF**
- **NOT in BCNF.**

**Highest normal form: 3NF** (not BCNF because C → D and C is not a superkey).

### Step 3: BCNF Decomposition

Violating FD: C → D

Apply BCNF decomposition formula:
- R1 = {C, D} with FD C → D (C is now a superkey of R1) ✓
- R2 = R - {D} = {A, B, C, E}

Check R2: FDs projected on {A, B, C, E}: AB → C, E → A
- AB → C: {A,B}⁺ in R2 = {A, B, C, E} (via AB→C, then E→A gives us nothing new but we already have all) — AB is superkey ✓
- E → A: {E}⁺ = {E, A} — E is not a superkey of R2 **✗ VIOLATES BCNF**

Decompose R2 on E → A:
- R3 = {E, A} with E → A ✓
- R4 = R2 - {A} = {B, C, E}

**Final BCNF decomposition:**
```
R1(C, D)  — Key: C
R3(E, A)  — Key: E
R4(B, C, E) — Key: {B, C} or {B, E}
```

**Trade-off:** This BCNF decomposition is NOT dependency-preserving — the FD AB → C cannot be checked within any single table (A and B are in different tables). This is the classic 3NF-vs-BCNF trade-off.

---

## Example 3: Normalisation with Real-World Scenario

### Problem: Student Course Registration

A university stores all registration data in one table:

```
REGISTRATION (
    Student_ID, Student_Name, Student_Phone,
    Course_ID, Course_Name, Credits, Instructor_Name, Instructor_Office,
    Semester, Grade
)
```

**FDs:**
```
Student_ID → Student_Name, Student_Phone
Course_ID → Course_Name, Credits
Course_ID, Semester → Instructor_Name
Instructor_Name → Instructor_Office
Student_ID, Course_ID, Semester → Grade
```

### Normalise to 3NF

**PK:** {Student_ID, Course_ID, Semester} (determines Grade and all other attributes via transitivity)

**1NF:** Already in 1NF (all atomic). ✓

**Check 2NF — Partial dependencies:**
- Student_ID → Student_Name, Student_Phone → **Partial** (depends on part of PK)
- Course_ID → Course_Name, Credits → **Partial**
- Course_ID, Semester → Instructor_Name → **Partial** (depends on part of PK — missing Student_ID)

**Decompose for 2NF:**

```
STUDENT (Student_ID PK, Student_Name, Student_Phone)
COURSE (Course_ID PK, Course_Name, Credits)
COURSE_SECTION (Course_ID, Semester, Instructor_Name)  PK = (Course_ID, Semester)
REGISTRATION (Student_ID, Course_ID, Semester, Grade)  PK = (Student_ID, Course_ID, Semester)
```

**Check 3NF — Transitive dependencies:**

In COURSE_SECTION: Course_ID, Semester → Instructor_Name → Instructor_Office
Instructor_Office transitively depends on PK through Instructor_Name (a non-key attribute).

**Decompose for 3NF:**

```
STUDENT (Student_ID PK, Student_Name, Student_Phone)
COURSE (Course_ID PK, Course_Name, Credits)
INSTRUCTOR (Instructor_Name PK, Instructor_Office)
COURSE_SECTION (Course_ID, Semester, Instructor_Name FK)  PK = (Course_ID, Semester)
REGISTRATION (Student_ID FK, Course_ID FK, Semester, Grade)  PK = (Student_ID, Course_ID, Semester)
```

**Verify 3NF:** All FDs have LHS as superkey or RHS as prime attribute. ✓

---

## Example 4: Lossless Decomposition Test

### Problem

R(A, B, C) with FDs: F = {A → B, B → C}

Decomposition: R1(A, B) and R2(B, C)

**Is this decomposition lossless?**

### Solution

**Test:** A binary decomposition of R into R1 and R2 is lossless if:

```
(R1 ∩ R2) → R1   OR   (R1 ∩ R2) → R2
```

- R1 = {A, B}
- R2 = {B, C}
- R1 ∩ R2 = {B}

Check: Does B → R1? That is, does B → A, B? We have B → C (given) but NOT B → A.
Check: Does B → R2? That is, does B → B, C? We have B → C. Yes! B → {B, C} ✓

**The decomposition IS lossless.** ✓

### Counter-Example: Lossy Decomposition

R(A, B, C) with FDs: F = {A → B}

Decomposition: R1(A, C) and R2(B, C)

- R1 ∩ R2 = {C}
- Does C → R1? No (C doesn't determine anything).
- Does C → R2? No.

**The decomposition is LOSSY.** Joining R1 and R2 on C produces spurious tuples.

---

## Example 5: Dependency Preservation Test

### Problem

R(A, B, C, D) with FDs: F = {AB → CD, C → A}

Decomposition: R1(A, B, D) and R2(A, C)

**Is this dependency-preserving?**

### Solution

Project FDs onto each decomposition:

**F1 (FDs in R1 = {A, B, D}):**
- From AB → CD: projected to {A,B,D} gives AB → D (C not in R1, so we keep only D).
- No other FDs close within {A,B,D}.
- **F1 = {AB → D}**

**F2 (FDs in R2 = {A, C}):**
- C → A is entirely within R2 ✓
- **F2 = {C → A}**

**(F1 ∪ F2)⁺ = {AB → D, C → A}⁺**

Can we derive all original FDs?
- AB → CD: Can we derive AB → C? From AB → D (in F1), we get {A,B} → {A,B,D}. But we can't get C from {A,B,D} using F1 ∪ F2. So AB → C **cannot be derived**.

**The decomposition is NOT dependency-preserving.** The FD AB → C is lost — it cannot be checked without joining R1 and R2.

---
---

# PART C — Quick Practice Problems (Try Before Looking at Answers)

---

### P1. Given R(A, B, C, D) with FDs: {A → B, B → C, C → D}. Find all candidate keys and the highest NF.

<details>
<summary>Click for answer</summary>

**Candidate key: {A}**
- A⁺ = {A, B, C, D} — A determines everything.
- No smaller set works (A is already a single attribute).

**Check NF:**
- A → B: A is superkey ✓
- B → C: B is NOT a superkey ✗ → violates BCNF
- B → C: B is not superkey, but C is not prime (only A is prime) → also violates 3NF
- But wait — is C prime? Candidate key is {A}, so prime attributes = {A} only. C is non-prime.
- B → C: B not superkey, C not prime → **violates 3NF**

Actually let's check 2NF: PK is {A} (single attribute), so no partial dependencies possible. **In 2NF.** ✓

B → C: transitive (A → B → C). **Violates 3NF.**

**Highest NF: 2NF.**
</details>

---

### P2. Given R(A, B, C, D, E) with FDs: {AB → C, C → D, D → B}. Find candidate keys.

<details>
<summary>Click for answer</summary>

Attributes never on RHS: A, E (never determined by any FD) → must be in every key.

{A, E}⁺ = {A, E} — doesn't determine B, C, or D. Need more.

Try {A, E, B}⁺: AB → C, C → D → {A, B, C, D, E} ✓ → **{ABE} is a candidate key.**

Try {A, E, C}⁺: C → D, D → B, AB → C → {A, B, C, D, E} ✓ → **{ACE} is a candidate key.**

Try {A, E, D}⁺: D → B, AB → C, C → D → {A, B, C, D, E} ✓ → **{ADE} is a candidate key.**

**Candidate keys: {ABE}, {ACE}, {ADE}**
</details>

---

### P3. Normalise: BOOK(ISBN, Title, Author_Name, Author_Country, Publisher, Publisher_City)

FDs: ISBN → Title, Publisher; ISBN → Author_Name (assume one author); Author_Name → Author_Country; Publisher → Publisher_City.

<details>
<summary>Click for answer</summary>

**PK: ISBN**

**2NF:** PK is single attribute → no partial deps. Already in 2NF ✓

**3NF check:**
- ISBN → Author_Name → Author_Country (transitive) **violates 3NF**
- ISBN → Publisher → Publisher_City (transitive) **violates 3NF**

**3NF decomposition:**
```
BOOK (ISBN PK, Title, Author_Name FK, Publisher FK)
AUTHOR (Author_Name PK, Author_Country)
PUBLISHER (Publisher PK, Publisher_City)
```

All FDs now have LHS as superkey. Also in **BCNF**. ✓
</details>

---

### P4. Is the decomposition of R(A, B, C) with F = {A → B, B → C} into R1(A, C) and R2(A, B) lossless?

<details>
<summary>Click for answer</summary>

R1 ∩ R2 = {A}

Does A → R1? A → {A, B} (given A → B, so A → AB). But does A → C? Via A → B → C, yes!
So A → {A, C} = R1 ✓

**YES, lossless.** ✓
</details>

---

### P5. Design an ER model for a Library: Books, Members, Loans, Authors (a book can have multiple authors).

<details>
<summary>Click for answer</summary>

```sql
AUTHOR (Author_ID PK, Name, Nationality)

BOOK (Book_ID PK, Title, ISBN UNIQUE, Published_Year, Genre)

-- M:N: Book has multiple Authors
BOOK_AUTHOR (Book_ID FK, Author_ID FK)  PK = (Book_ID, Author_ID)

MEMBER (Member_ID PK, Name, Email UNIQUE, Phone, Join_Date)

-- M:N with attributes: Member borrows Book
LOAN (
    Loan_ID     INT PRIMARY KEY,
    Member_ID   INT FK → MEMBER,
    Book_ID     INT FK → BOOK,
    Borrow_Date DATE NOT NULL,
    Due_Date    DATE NOT NULL,
    Return_Date DATE,                -- NULL if not yet returned
    Fine        DECIMAL(6,2) DEFAULT 0
);
```

**Key decisions:**
- Book-Author is M:N → junction table BOOK_AUTHOR.
- Loan has its own surrogate PK (Loan_ID) because the same member can borrow the same book multiple times.
- Return_Date is NULL for currently-borrowed books.
- Fine is a derived/stored attribute (could be computed from dates, but stored for simplicity).
</details>

---

*End of DBMS Design & Normalisation Practice*
