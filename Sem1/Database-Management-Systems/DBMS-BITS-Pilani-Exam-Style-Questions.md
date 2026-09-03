# DBMS Exam-Style Questions &mdash; BITS Pilani Pattern

> **Based on:** BITS Pilani CS F212 (Database Systems) exam papers from 2022-2023 and 2023 semesters, including mid-semester and comprehensive examinations.
>
> **Exam Pattern Observed:**
> - **Part A:** MCQs (1 mark each, 0.5 negative marking) — 15 to 20 questions
> - **Part B:** Short-answer / problem-solving (3–5 marks each)
> - **Part C:** Long-answer / design questions (8–10 marks each)
>
> **Source references:** [BITS Pilani Library question papers](https://library.bits-pilani.ac.in), [Studocu BITS CS F212](https://www.studocu.com/in/institution/birla-institute-of-technology-and-science-pilani/2689). Content was rephrased for compliance with licensing restrictions.

---
---

## PART A — Multiple Choice Questions (1 mark each, -0.5 for wrong answer)

---

### MCQ 1. The number of entity types participating in a relationship (in ER modelling) is known as the ____ of that relationship.

(a) entity set  
(b) cardinality  
(c) participation multiplicity  
(d) degree  
(e) arity

**Answer: (d) degree**

The degree of a relationship refers to the number of entity types involved. Binary = 2, Ternary = 3, etc. Note: "arity" is sometimes used as a synonym for degree, but in Elmasri/Navathe, "degree" is the standard term for ER relationships.

---

### MCQ 2. Which of the following operations is NOT a member of the Complete set of Relational Operations?

(a) Select (σ)  
(b) Join (⋈)  
(c) Cartesian Product (×)  
(d) Union (∪)  
(e) Rename (ρ)

**Answer: (b) Join**

The five fundamental (complete) operations in relational algebra are: Select (σ), Project (π), Union (∪), Set Difference (−), and Cartesian Product (×). Rename (ρ) is sometimes included. Join can be derived from Cartesian Product + Selection, so it is not a primitive operation.

---

### MCQ 3. If we have an FD {X → CB}, we can infer X → C, according to which Armstrong's Inference Rule?

(a) IR4 (Decomposition)  
(b) IR3 (Transitivity)  
(c) IR2 (Augmentation)  
(d) IR1 (Reflexivity)  
(e) IR5 (Pseudotransitivity)

**Answer: (a) IR4 (Decomposition)**

The Decomposition rule states: If X → YZ, then X → Y and X → Z. So from X → CB, we get X → C and X → B.

---

### MCQ 4. Which of the following statements is TRUE?

(a) We can have only one alternate key for a table.  
(b) We can have more than one candidate key for a table.  
(c) For a table, only one superkey is possible.  
(d) In extendible hashing, we use more than one hash function.

**Answer: (b)**

A table can have multiple candidate keys (e.g. Student_ID and Email could both be candidate keys). One is chosen as the PK; the rest are alternate keys. Every table has multiple superkeys (any superset of a candidate key is a superkey).

---

### MCQ 5. If the entity type hierarchy in EER results from the generalisation process, every entity in the super-type has membership in at least one sub-type. True or False?

**Answer: True**

Generalisation is a bottom-up process where common features of sub-types are abstracted into a super-type. In this case, the specialisation is total — every super-type entity belongs to at least one sub-type.

---

### MCQ 6. In a relation R(A, B, C, D) where {AB} is the primary key, and we have the functional dependency {D → A}. What is the highest normal form of R?

(a) 1NF  
(b) 2NF  
(c) 3NF  
(d) BCNF

**Answer: (a) 1NF**

D → A is a partial dependency issue: D (a non-key attribute) determines A (part of the primary key). Since A is part of the composite PK {AB}, and D is a non-key attribute that determines it, this violates 2NF. Wait — let's re-examine. The PK is {AB}. D is a non-key attribute. D → A means a non-key determines part of the key. For 2NF, we check if non-key attributes are fully dependent on the entire PK. Since D → A and D is not a candidate key, this is actually a violation of BCNF (determinant D is not a superkey). But is it in 3NF? In 3NF, for every FD X → Y, either X is a superkey OR Y is a prime attribute. A is a prime attribute (part of PK), so D → A does NOT violate 3NF. Hence R is in 3NF but not BCNF.

**Corrected Answer: (c) 3NF**

---

### MCQ 7. The 4th Normal Form (4NF) is based on:

(a) Join dependency  
(b) Multivalued dependency  
(c) Closure dependency  
(d) Lossless dependency

**Answer: (b) Multivalued dependency**

4NF deals with removing non-trivial multivalued dependencies. 5NF is based on join dependencies.

---

### MCQ 8. It is NOT possible to construct one primary index and one secondary index on the same table. True or False?

**Answer: False**

A table can have one clustered (primary) index and multiple secondary (non-clustered) indexes. This is standard in all relational DBMSs.

---

### MCQ 9. If we have 54,650 data blocks and 1,236 index blocks in a single-level primary indexing scheme, and block size is 1024 bytes, what is the number of block accesses (worst case) to retrieve a record with a given key?

(a) 12  
(b) 11  
(c) 10  
(d) 44  
(e) 53

**Answer: (a) 12**

With a single-level primary index of 1,236 blocks, binary search on the index requires ⌈log₂(1236)⌉ = ⌈10.27⌉ = 11 block accesses. Plus 1 block access to fetch the actual data block. Total = 11 + 1 = 12.

---

### MCQ 10. In a B-tree, record pointers can be found at:

(a) Intermediate level only  
(b) Any level  
(c) Leaf level only  
(d) Either at root or intermediate level only  
(e) Root level only

**Answer: (b) Any level**

In a B-tree (not B+ tree), data pointers exist at every node level. In a B+ tree, data pointers are only at leaf nodes. BITS exams frequently test this distinction.

---

### MCQ 11. All conflict-serializable schedules are also view-serializable. True or False?

**Answer: True**

Conflict serializability is a stricter condition. Every conflict-serializable schedule is view-serializable, but not vice versa. The question as stated "conflict serializable schedules are not view serializable" should be marked **False**.

---

### MCQ 12. In the context of ACID properties, the letter 'I' stands for:

(a) Isolation  
(b) Interference  
(c) Integrity  
(d) Identity  
(e) Inference

**Answer: (a) Isolation**

ACID = Atomicity, Consistency, Isolation, Durability.

---

### MCQ 13. In database recovery using the Immediate Modification approach, in case of failure/crash, we need REDO action only. True or False?

**Answer: False**

Immediate modification writes changes to the database before the transaction commits. On crash, we need both UNDO (for uncommitted transactions whose writes reached disk) and REDO (for committed transactions whose writes may not have reached disk).

---

### MCQ 14. Checkpointing helps in:

(a) Making checksum efficient  
(b) Making deadlock resolution efficient  
(c) Making the recovery process efficient  
(d) Making two-phase locking efficient

**Answer: (c) Making the recovery process efficient**

Checkpoints limit how far back in the log the recovery process needs to scan, significantly reducing recovery time.

---

### MCQ 15. Which normal form eliminates transitive dependencies among non-key attributes?

(a) 1NF  
(b) 2NF  
(c) 3NF  
(d) BCNF

**Answer: (c) 3NF**

3NF removes transitive dependencies: if A → B → C (where B is not a candidate key), then C transitively depends on A. 3NF requires decomposition to eliminate this.

---

### MCQ 16. A schedule is said to be recoverable if:

(a) No transaction commits before all transactions it depends on have committed  
(b) All transactions commit simultaneously  
(c) No transaction reads data written by another transaction  
(d) All locks are released before commit

**Answer: (a)**

A schedule is recoverable if, for every pair of transactions Tᵢ and Tⱼ such that Tⱼ reads a data item written by Tᵢ, the commit of Tᵢ appears before the commit of Tⱼ.

---

### MCQ 17. In a B+ tree of order p, the maximum number of keys in a leaf node is:

(a) p  
(b) p - 1  
(c) p + 1  
(d) 2p

**Answer: (b) p - 1**

A B+ tree leaf node of order p can hold at most p - 1 search key values (and p - 1 data pointers + 1 pointer to the next leaf node).

---

### MCQ 18. The HAVING clause in SQL is used to:

(a) Filter individual rows  
(b) Filter groups formed by GROUP BY  
(c) Sort the result set  
(d) Join multiple tables

**Answer: (b)**

WHERE filters rows before grouping. HAVING filters groups after GROUP BY.

---

### MCQ 19. Which type of join returns all rows from both tables with NULLs where there is no match?

(a) INNER JOIN  
(b) LEFT OUTER JOIN  
(c) RIGHT OUTER JOIN  
(d) FULL OUTER JOIN

**Answer: (d) FULL OUTER JOIN**

---

### MCQ 20. In the two-phase locking protocol, the growing phase is defined as:

(a) The phase where a transaction acquires locks and may also release locks  
(b) The phase where a transaction acquires locks but does not release any  
(c) The phase where a transaction releases all locks simultaneously  
(d) The phase where a transaction performs only read operations

**Answer: (b)**

In 2PL: Growing phase = acquire locks only (no releases). Shrinking phase = release locks only (no acquisitions).

---
---

## PART B — Short Answer / Problem Solving (3–5 marks each)

---

### B1. Lossless Decomposition Test [5 marks]

**Question:** Consider the relation `Student_Advisor(Name, Dept, Advisor)` with functional dependencies F = {Name → Dept, Name → Advisor, Advisor → Dept}. Test whether the decomposition into `Student_Prof(Name, Advisor)` and `Dep_Adv(Dept, Advisor)` is (a) lossless and (b) dependency preserving.

**Answer:**

**(a) Lossless test:**

For a binary decomposition R into R1 and R2, the decomposition is lossless if:
(R1 ∩ R2) → R1 **or** (R1 ∩ R2) → R2

- R1 = {Name, Advisor}, R2 = {Dept, Advisor}
- R1 ∩ R2 = {Advisor}
- Check: Advisor → Dept? Yes (given in F). So Advisor → R2's attributes.
- **The decomposition is LOSSLESS.**

**(b) Dependency preserving test:**

- F1 (FDs in R1): Name → Advisor
- F2 (FDs in R2): Advisor → Dept
- (F1 ∪ F2)⁺ gives us: Name → Advisor, Advisor → Dept, and by transitivity Name → Dept.
- Original F = {Name → Dept, Name → Advisor, Advisor → Dept}
- Since (F1 ∪ F2)⁺ = F⁺, **the decomposition IS dependency preserving.**

---

### B2. Normalisation Problem [4 marks]

**Question:** Find the highest normal form satisfied by R1(A, B, C) with F = {AB → C, C → A}. If not in BCNF, decompose to BCNF.

**Answer:**

- Candidate keys: AB (since AB → C, and no FD determines B alone). Also BC is a candidate key (since C → A, and BC → ABC).
- Check 3NF: For AB → C: AB is a superkey ✓. For C → A: C is not a superkey, but A is a prime attribute (part of candidate key AB). ✓
- **Highest NF: 3NF** (not BCNF because C → A and C is not a superkey).

**BCNF decomposition:**
- Violating FD: C → A
- Decompose: **R1(C, A)** with key C, and **R2(B, C)** with key BC.
- Both are now in BCNF.

---

### B3. Conservative 2PL Definition [2 marks]

**Question:** What is the Conservative Two-Phase Locking scheme?

**Answer:**

In Conservative 2PL, a transaction must acquire **all** the locks it needs before it begins execution. If it cannot obtain all locks, it waits without holding any locks. This prevents deadlocks entirely (since a transaction never holds some locks while waiting for others), but requires knowing all data items in advance, which reduces concurrency.

---

### B4. Index Block Access Calculation [3 marks]

**Question:** A file has 100,000 records. The record size is 200 bytes. The block size is 2,048 bytes. A single-level primary index is built on the key field (10 bytes). An index pointer is 6 bytes. Calculate the number of block accesses needed for a binary search on the index.

**Answer:**

- **Blocking factor (data):** ⌊2048 / 200⌋ = 10 records/block
- **Number of data blocks:** ⌈100000 / 10⌉ = 10,000 blocks
- **Number of index entries:** 10,000 (one per data block in primary index)
- **Index entry size:** 10 (key) + 6 (pointer) = 16 bytes
- **Index blocking factor:** ⌊2048 / 16⌋ = 128 entries/block
- **Number of index blocks:** ⌈10000 / 128⌉ = 79 blocks
- **Binary search on index:** ⌈log₂(79)⌉ = 7 block accesses
- **+ 1 to fetch data block = 8 total block accesses**

---

### B5. Conflict Serializability Test [5 marks]

**Question:** Given the following schedule with three transactions T1, T2, T3:

```
T1: R(A), W(A)
T2: R(A), R(B), W(B)
T3: R(B), W(B)

Schedule: R1(A), R2(A), W1(A), R2(B), R3(B), W2(B), W3(B)
```

Test whether this schedule is conflict-serializable using a precedence graph.

**Answer:**

**Identify conflicts (same data item, at least one write, different transactions):**

1. R2(A) before W1(A) → T2 → T1 (T2 reads A before T1 writes A)
2. W1(A) before ... (T1 writes A, no later conflicting ops on A from T3)
3. R3(B) before W2(B) → T3 → T2 (T3 reads B before T2 writes B)
4. W2(B) before W3(B) → T2 → T3 (T2 writes B before T3 writes B)

**Precedence graph edges:**
- T2 → T1 (from conflict 1)
- T3 → T2 (from conflict 3)
- T2 → T3 (from conflict 4)

**Cycle detected:** T2 → T3 → T2

**The schedule is NOT conflict-serializable.**

---

### B6. SQL Query Writing [3+3+3 = 9 marks]

**Question:** Given the schema:
```
Student(sid, sname, sbranch)
Company(cid, cname, clocation)
Placement(sno, cno, salary)   -- sno FK to sid, cno FK to cid
```

Write:

**(a) Tuple Relational Calculus:** Get sid, sname for students placed in at least one company located in 'Delhi'.

**(b) Relational Algebra:** Get sid, sname for students NOT placed by any company in Delhi.

**(c) SQL:** Get cid, cname for companies that have selected ALL students from 'EEE' and 'CIVIL' branches.

**Answer:**

**(a) TRC:**
```
{ s.sid, s.sname | Student(s) ∧ ∃p ∃c (Placement(p) ∧ Company(c) 
  ∧ p.sno = s.sid ∧ p.cno = c.cid ∧ c.clocation = 'Delhi') }
```

**(b) Relational Algebra:**
```
Delhi_Students = π_{sno}(Placement ⋈_{cno=cid} (σ_{clocation='Delhi'}(Company)))
All_Students = π_{sid, sname}(Student)
Result = All_Students − (All_Students ⋈_{sid=sno} Delhi_Students)
```
Or more concisely:
```
π_{sid, sname}(Student) − π_{sid, sname}(Student ⋈ Placement ⋈ σ_{clocation='Delhi'}(Company))
```

**(c) SQL (Division — "all students from EEE and CIVIL"):**
```sql
SELECT C.cid, C.cname
FROM Company C
WHERE NOT EXISTS (
    SELECT S.sid
    FROM Student S
    WHERE S.sbranch IN ('EEE', 'CIVIL')
    AND NOT EXISTS (
        SELECT 1
        FROM Placement P
        WHERE P.sno = S.sid AND P.cno = C.cid
    )
);
```

This is the classic SQL division pattern: find companies where there does NOT EXIST a student in EEE/CIVIL who is NOT placed by that company.

---
---

## PART C — Long Answer / Design Questions (8–10 marks each)

---

### C1. ARIES Recovery Algorithm [18 marks]

**Question:** Consider the following schedule with a checkpoint and crash:

```
Begin_checkpoint
End_checkpoint
T10: writes 'DEF' to Page P500 from byte 2
T20: writes 'KLM' to Page P600 from byte 1
T20: writes 'QRS' to Page P500 from byte 1
T20: commit
T10: writes 'WXY' to Page P505 from byte 2
CRASH
```

- Page size: 4 bytes
- Initial contents: P500 = 'GABC', P505 = 'STUV', P600 = 'HIJK'
- All page LSNs = -1 before checkpoint
- Before crash, only P600 was flushed to disk

Show: (a) page contents before recovery, after REDO, after UNDO. (b) Dirty Page Table and Transaction Table after Analysis. (c) Log table after complete recovery.

**Answer:**

**Log Table:**

| LSN | Prev_LSN | Txn_ID | Type | Page | Before_Image | After_Image |
|---|---|---|---|---|---|---|
| 0 | - | - | begin_chkpt | - | - | - |
| 1 | - | - | end_chkpt | - | - | - |
| 2 | NULL | T10 | UPDATE | P500 | ABC | DEF |
| 3 | NULL | T20 | UPDATE | P600 | HIJ | KLM |
| 4 | 3 | T20 | UPDATE | P500 | GDE | QRS |
| 5 | 4 | T20 | COMMIT | - | - | - |
| 6 | 2 | T10 | UPDATE | P505 | TUV | WXY |

**(a) Page contents:**

| Phase | P500 | P505 | P600 |
|---|---|---|---|
| Before recovery (after crash) | QRSC (LSN 4 applied, but LSN 2 had partial overlap) | SWXY (LSN 6 applied) | KLMK (only P600 flushed; LSN 3 applied) |
| After REDO | QRSC | SWXY | KLMK (all committed and active writes re-applied) |
| After UNDO (T10 rolled back) | GABC (T10's writes undone) | STUV (T10's write undone) | KLMK (T20 committed, not undone) |

**(b) After Analysis phase:**

Dirty Page Table (DPT):

| Page_ID | recLSN (first LSN that dirtied it) |
|---|---|
| P500 | 2 |
| P600 | 3 |
| P505 | 6 |

Transaction Table (TT):

| Txn_ID | Status | lastLSN |
|---|---|---|
| T10 | Active (needs UNDO) | 6 |
| T20 | Committed | 5 |

**(c) Recovery actions:**
- **REDO phase:** Start from min(recLSN) = LSN 2. Redo LSNs 2, 3, 4, 6 (skip if page already flushed and pageLSN >= LSN).
- **UNDO phase:** Undo T10's writes in reverse order: Undo LSN 6 (restore P505), then Undo LSN 2 (restore P500). Write CLR log records for each undo.

---

### C2. Query Optimisation [8 marks]

**Question:** For the following SQL query, give the corresponding relational algebra expression, then produce an optimised query tree applying heuristic optimisation rules.

```sql
SELECT P.pname, P.color, V.vname, V.city, S.qty
FROM Part P, Vendor V, Supply S
WHERE S.vid = V.vid AND S.pid = P.pid 
  AND P.weight > 200 AND S.qty < 30;
```

**Answer:**

**Unoptimised relational algebra:**
```
π_{pname, color, vname, city, qty}(
  σ_{S.vid=V.vid ∧ S.pid=P.pid ∧ weight>200 ∧ qty<30}(
    Part × Vendor × Supply
  )
)
```

**Optimised query tree (applying heuristic rules):**

1. **Push selections down** (apply as early as possible):
   - Apply σ_{weight>200} on Part first
   - Apply σ_{qty<30} on Supply first

2. **Convert Cartesian products + selections into joins:**
   - Part ⋈_{pid} Supply becomes a join
   - Result ⋈_{vid} Vendor becomes a join

3. **Push projections down** (carry only needed attributes):

```
Optimised tree:

π_{pname, color, vname, city, qty}
        |
    ⋈_{vid}
    /        \
⋈_{pid}      π_{vid, vname, city}(Vendor)
/          \
π_{pid, pname, color}     π_{pid, vid, qty}
(σ_{weight>200}(Part))    (σ_{qty<30}(Supply))
```

**Key optimisation rules applied:**
1. Selection pushed before joins (reduces tuple count early).
2. Cartesian products eliminated by converting to equi-joins.
3. Projections pushed down to reduce attribute width at each step.

---

### C3. Dependency Preservation Check [8 marks]

**Question:** Given R(A, B, C, D, E, G) with F = {AB → CDG, D → E, A → C, E → G}. R is decomposed into R1(A, B, D, G), R2(A, C, D), R3(E, G). Check if this decomposition is dependency preserving.

**Answer:**

**Step 1: Find F⁺ (closure of F):**
From the given FDs, we can derive:
- AB → C, AB → D, AB → G (decomposition of AB → CDG)
- D → E (given)
- A → C (given)
- E → G (given)
- D → G (transitivity: D → E → G)
- AB → E (transitivity: AB → D → E)

F⁺ = {AB → C, AB → D, AB → G, D → E, A → C, E → G, D → G, AB → E}

**Step 2: Projection of F on each decomposition:**

- F1 = projection on R1(A, B, D, G):
  - AB → D (from AB → CDG, projected to R1's attributes)
  - AB → G
  - D → G (from D → E → G)

- F2 = projection on R2(A, C, D):
  - A → C

- F3 = projection on R3(E, G):
  - E → G

**Step 3: Check if (F1 ∪ F2 ∪ F3)⁺ = F⁺**

(F1 ∪ F2 ∪ F3) = {AB → D, AB → G, D → G, A → C, E → G}

From this set, we can derive:
- AB → D ✓, AB → G ✓, D → G ✓, A → C ✓, E → G ✓
- Can we derive D → E? **NO.** D → G is available, but not D → E.
- Can we derive AB → E? Only if D → E is available, which it isn't.

Since **D → E** from the original F cannot be derived from (F1 ∪ F2 ∪ F3), the decomposition is **NOT dependency preserving**.

---

### C4. Bitmap Indexing [10 marks]

**Question:** Given a T-shirt order table for a college fest:

| ID | Gender | Design | Size | Color | Branch |
|---|---|---|---|---|---|
| 1 | F | D1 | M | RED | A7 |
| 2 | F | D1 | XL | RED | A8 |
| 3 | M | D2 | XL | BLACK | B4 |
| 4 | M | D2 | M | WHITE | B5 |
| 5 | F | D3 | S | RED | A7 |
| 6 | F | D3 | M | BLACK | A7 |

(a) Using bitmap indexing, find the number of females who ordered red or black T-shirts of size M with Design D1.  
(b) Show the bitmap vectors and AND/OR operations.

**Answer:**

**Bitmap Vectors:**

| Attribute=Value | Row 1 | Row 2 | Row 3 | Row 4 | Row 5 | Row 6 |
|---|---|---|---|---|---|---|
| Gender=F | 1 | 1 | 0 | 0 | 1 | 1 |
| Design=D1 | 1 | 1 | 0 | 0 | 0 | 0 |
| Size=M | 1 | 0 | 0 | 1 | 0 | 1 |
| Color=RED | 1 | 1 | 0 | 0 | 1 | 0 |
| Color=BLACK | 0 | 0 | 1 | 0 | 0 | 1 |

**Query: Female AND (RED OR BLACK) AND Size=M AND Design=D1**

Step 1: Color=RED OR Color=BLACK = `1 1 1 0 1 1`

Step 2: AND Gender=F = `1 1 1 0 1 1` AND `1 1 0 0 1 1` = `1 1 0 0 1 1`

Step 3: AND Size=M = `1 1 0 0 1 1` AND `1 0 0 1 0 1` = `1 0 0 0 0 1`

Step 4: AND Design=D1 = `1 0 0 0 0 1` AND `1 1 0 0 0 0` = `1 0 0 0 0 0`

**Result: 1 record (Row 1 only). Count = 1.**

---

### C5. Timestamp-Based Concurrency Control [5 marks]

**Question:** Compare the Timestamp Ordering protocol and the Two-Phase Locking protocol for the following properties. Write "Ensured" or "Not Ensured":

| Property | Timestamp Protocol | Two-Phase Locking |
|---|---|---|
| Conflict serializability | ? | ? |
| Deadlock freedom | ? | ? |
| View serializability | ? | ? |
| Recoverability | ? | ? |
| Cascadeless (no cascading rollback) | ? | ? |

**Answer:**

| Property | Timestamp Protocol | Two-Phase Locking |
|---|---|---|
| **Conflict serializability** | Ensured | Ensured |
| **Deadlock freedom** | Ensured (no waiting) | Not Ensured (can deadlock) |
| **View serializability** | Ensured (subset of CS) | Ensured (subset of CS) |
| **Recoverability** | Not Ensured (basic TO) | Not Ensured (basic 2PL) |
| **Cascadeless** | Not Ensured (basic TO) | Not Ensured (basic 2PL, need Strict 2PL) |

**Notes:** Basic Timestamp Ordering doesn't ensure recoverability (a txn may read uncommitted data). Strict 2PL (holding all exclusive locks until commit) ensures both recoverability and cascadelessness.

---

### C6. Phantom Problem [5 marks]

**Question:** Explain the Phantom problem in concurrency control. Give an example and suggest a solution.

**Answer:**

**Phantom Problem:** Occurs when a transaction T1 executes a range query (e.g. "find all employees with grade = 1"), and between two executions of the same query within T1, another transaction T2 inserts or deletes a row that matches the query predicate. T1 sees different result sets — the new row is a "phantom."

**Example:**
- T1: SELECT * FROM Employee WHERE grade = 1 → returns {Alice, Bob}
- T2: INSERT INTO Employee VALUES('Carol', 1, 56) → commits
- T1: SELECT * FROM Employee WHERE grade = 1 → returns {Alice, Bob, Carol}

Even though T1 holds shared locks on Alice and Bob's rows, T2 can insert Carol on a different page. T1 sees an inconsistent result.

**Solutions:**
1. **Index locking:** Lock the index page for grade = 1. Any insertion with grade = 1 must update this index page and will be blocked by T1's lock.
2. **Predicate locking:** Lock the logical predicate (grade = 1) rather than individual rows. Conceptually clean but hard to implement.
3. **Next-key locking (used by MySQL InnoDB):** Lock the gap between index keys to prevent insertions in that range.
4. **SERIALIZABLE isolation level:** Forces the DBMS to use one of the above techniques.

---

### C7. ER-to-Relational Mapping [6 marks]

**Question:** Map the following ER to relational schemas. FACULTY (fid, fname) and COURSE (cid, cname) have a 1:N relationship "teaches" (one faculty teaches many courses). Assume very few faculty (< 5%) actually teach courses. The min-max constraints are Faculty (0, N) — Course (1, 1). Give the most appropriate mapping and explain how to enforce the min-max constraints.

**Answer:**

**Mapping options:**

Option A (standard 1:N mapping): Add fid as FK in COURSE table.
```
FACULTY(fid, fname)
COURSE(cid, cname, fid)  -- fid FK references FACULTY
```

Option B (separate relationship table): Since < 5% of faculty teach:
```
FACULTY(fid, fname)
COURSE(cid, cname)
TEACHES(fid, cid)  -- fid FK, cid FK (cid is UNIQUE here since 1:N)
```

**Best choice: Option B** — Because very few faculty teach courses, adding fid to COURSE would result in 95% NULL values. A separate table is more space-efficient and cleaner.

**Enforcing min-max constraints:**
- **Course (1,1):** Every course must have exactly one faculty. Enforce with NOT NULL + UNIQUE on cid in TEACHES, or use a trigger to reject courses without a teaching assignment.
- **Faculty (0,N):** A faculty may teach 0 to many courses. No additional constraint needed (the FK relationship naturally allows this).
- **Triggers** are the most flexible way to enforce these min-max constraints in an RDBMS, as CHECK constraints across tables are not standard.

---

### C8. Wait-Die Deadlock Prevention [5 marks]

**Question:** Given a schedule where T1 (oldest), T2, T3 (youngest) cause a cycle in the precedence graph, explain how the wait-die scheme resolves this.

**Answer:**

**Wait-Die scheme rules (non-preemptive):**
- If Tᵢ (requesting lock) is **older** than Tⱼ (holding lock) → Tᵢ **waits**.
- If Tᵢ is **younger** than Tⱼ → Tᵢ **dies** (aborts and restarts with same timestamp).

**Application:**
- If T3 (youngest) requests a lock held by T1 (oldest): T3 is younger → **T3 dies (aborts and restarts)**.
- If T2 requests a lock held by T3: T2 is older → **T2 waits**.
- If T1 requests a lock held by T2: T1 is older → **T1 waits**.

**Resolution sequence:**
1. T3 is aborted and rolled back (releases all locks).
2. T2 can now proceed (gets the lock T3 released).
3. T1 waits for T2, then proceeds after T2 commits.
4. T3 restarts after T1 and T2 complete.

**Serial order achieved:** T1 → T2 → T3.

---
---

## Exam Preparation Tips (BITS Pilani Pattern)

1. **MCQs are tricky** — they test subtle distinctions (B-tree vs B+ tree, 2PL vs Strict 2PL, conflict vs view serializability). Read carefully.
2. **Normalisation is almost always asked** — be ready to find candidate keys, determine highest NF, and decompose to BCNF with steps.
3. **Lossless decomposition and dependency preservation** — know the formulas and apply them mechanically.
4. **Recovery (ARIES)** — practice building log tables, DPT, and TT. This is a high-marks question.
5. **SQL division pattern** — "find X that have ALL of Y" uses double NOT EXISTS. Practice this.
6. **Precedence graphs** — draw them quickly and check for cycles.
7. **Index calculations** — know the formulas for blocking factor, index levels, and binary search access count.
8. **Bitmap indexing** — practice AND/OR operations on bit vectors.

---

*End of Exam-Style Questions*
