# DSA — 2-3 Hour Crash Course (Exam Ready)

> **BITS Pilani — SE ZG519 / DSECLZG519**
> You haven't attended lectures. Your exam is in 2 days. This file covers EVERYTHING — theory, solved past paper questions, class Q&A, and practice problems — topic by topic, in exam-priority order.
>
> **Exam Pattern:** Mid-sem = 6 Qs, 90 marks, 90 min | Comprehensive = 6-8 Qs, ~90 marks, 3 hours | Closed book.
>
> **Time Plan:**
> - **Hour 1** (Topics 1-4): Master Theorem, Stack Applications, Heap Operations, Sorting
> - **Hour 2** (Topics 5-8): BST & Trees, BFS/DFS, Queues, Amortized Analysis
> - **Hour 3** (Topics 9-12): Hashing, Linked Lists, Quick Sort, Graphs (Floyd-Warshall, MST, DP)

---
---

# ⏱️ HOUR 1 — Highest Yield Topics

---

## TOPIC 1: Asymptotic Notation & Master Theorem ⭐⭐⭐

> Appears in EVERY exam. At least 2-3 questions. Learn this first.

### 1.1 Theory You Need

**Growth Rate Ordering (memorize this line):**
```
1  <  log n  <  √n  <  n  <  n log n  <  n²  <  n³  <  2ⁿ  <  n!
```

**Asymptotic Notations — What They Mean:**

| Notation | English | Formal |
|----------|---------|--------|
| **O(f(n))** | "At most" — upper bound | ∃ c, n₀ > 0 : g(n) ≤ c·f(n) ∀ n ≥ n₀ |
| **Ω(f(n))** | "At least" — lower bound | ∃ c, n₀ > 0 : g(n) ≥ c·f(n) ∀ n ≥ n₀ |
| **Θ(f(n))** | "Exactly" — tight bound | c₁·f(n) ≤ g(n) ≤ c₂·f(n) |

**Also know (less common but asked in True/False):**

| Notation | English | Key difference from Big-O/Ω |
|----------|---------|---------------------------|
| **o(f(n))** | "Strictly less than" | Uses **∀c** (for ALL c), not ∃c. Example: n = o(n²) but n ≠ o(n) |
| **ω(f(n))** | "Strictly greater than" | Uses **∀c** (for ALL c). Example: n² = ω(n) but n ≠ ω(n) |

> **Tip:** Big-O/Ω use ∃c ("there exists"), little-o/ω use ∀c ("for all"). Little = strictly smaller/bigger.

**Master Theorem — The Most Important Formula:**

For T(n) = aT(n/b) + f(n):

```
Step 1: Compute n^(log_b a)
Step 2: Compare f(n) with n^(log_b a)

Case 1: f(n) < n^(log_b a)  polynomially    →  T(n) = Θ(n^(log_b a))         [leaves dominate]
Case 2: f(n) = n^(log_b a)                  →  T(n) = Θ(n^(log_b a) · log n)  [tie → ×log n]
Case 2 EXT: f(n) = n^(log_b a) · (log n)^k  →  T(n) = Θ(n^(log_b a) · (log n)^(k+1))  [add 1 to log power]
Case 3: f(n) > n^(log_b a)  polynomially    →  T(n) = Θ(f(n))                 [root dominates]
```

> **Memory trick:** Compare f(n) vs n^(log_b a) — whichever is "heavier" wins. Tie → multiply by log n.

**Log values to memorize:**

| log₂2=1 | log₂4=2 | log₂8=3 | log₂16=4 | log₃9=2 | log₃27=3 | log₄16=2 |

### 1.2 Solved Past Paper Questions

**Q1. [Past Year 2024] T(n) = 8T(n/2) + √n · log⁻²n**

```
a = 8, b = 2
n^(log_b a) = n^(log₂ 8) = n³
f(n) = n^(1/2) · (log n)^(-2)

Compare: n³ vs n^(1/2) → n³ is WAY bigger
→ Case 1: T(n) = Θ(n³)
```

**Q2. [Past Year 2026 Comp] T(n) = 9T(n/3) + n**

```
a = 9, b = 3
n^(log_b a) = n^(log₃ 9) = n²
f(n) = n

Compare: n² vs n → n² is bigger (polynomially: n = O(n^(2-1)))
→ Case 1: T(n) = Θ(n²)
```

**Q3. [Past Year 2026 Comp] T(n) = 4T(n/2) + n² log n**

```
a = 4, b = 2
n^(log_b a) = n^(log₂ 4) = n²
f(n) = n² · log n

Compare: f(n) = n² · (log n)^1 = n^(log_b a) · (log n)^k where k = 1
→ Case 2 EXTENDED: T(n) = Θ(n² · (log n)^(1+1)) = Θ(n² log² n)
```

**Q4. [Past Year 2026 Comp] T(n) = 2T(n/2) + n log n**

```
a = 2, b = 2
n^(log_b a) = n^1 = n
f(n) = n · log n = n · (log n)^1

Compare: f(n) = n · (log n)^1, k = 1
→ Case 2 EXTENDED: T(n) = Θ(n · (log n)^2) = Θ(n log² n)
```

**Q5. [Past Year 2026 Comp] T(n) = 2T(n/4) + n^0.51**

```
a = 2, b = 4
n^(log_b a) = n^(log₄ 2) = n^0.5
f(n) = n^0.51

Compare: n^0.51 vs n^0.5 → f(n) is slightly bigger
Is n^0.51 = Ω(n^(0.5+ε)) for some ε > 0? Yes, ε = 0.01
Check regularity: 2·(n/4)^0.51 = 2·n^0.51/4^0.51 ≈ 2·n^0.51/2.04 ≈ 0.98·n^0.51 < n^0.51 ✓
→ Case 3: T(n) = Θ(n^0.51)
```

### 1.3 Common Recurrences (Memorize These)

| Algorithm | Recurrence | Solution |
|-----------|-----------|----------|
| Binary Search | T(n) = T(n/2) + 1 | **Θ(log n)** |
| Merge Sort | T(n) = 2T(n/2) + n | **Θ(n log n)** |
| Quick Sort (avg) | T(n) = 2T(n/2) + n | **Θ(n log n)** |
| Strassen | T(n) = 7T(n/2) + n² | **Θ(n^2.807)** |
| Karatsuba | T(n) = 3T(n/2) + n | **Θ(n^1.585)** |

### 1.4 Practice (Try These Now)

**P1.** Solve T(n) = 16T(n/4) + n
<details><summary>Answer</summary>
a=16, b=4, n^(log₄16) = n². f(n)=n = O(n^(2-1)). Case 1 → Θ(n²)
</details>

**P2.** Solve T(n) = T(n/2) + log²n
<details><summary>Answer</summary>
a=1, b=2, n^(log₂1) = n⁰ = 1. f(n) = log²n = 1·(log n)². k=2. Case 2 Extended → Θ(log³ n)
</details>

**P3.** Solve T(n) = 3T(n/3) + n
<details><summary>Answer</summary>
a=3, b=3, n^(log₃3) = n¹. f(n) = n = Θ(n). Case 2 → Θ(n log n)
</details>

**P4.** Solve T(n) = 7T(n/2) + n²
<details><summary>Answer</summary>
a=7, b=2, n^(log₂7) = n^2.807. f(n) = n² = O(n^(2.807-0.807)). Case 1 → Θ(n^2.807)
</details>

**P5.** TRUE or FALSE: The Master Theorem can solve T(n) = T(n-1) + 1
<details><summary>Answer</summary>
FALSE. This is a subtraction recurrence (n-1, not n/b). Master Theorem requires DIVISION by a constant. This solves to T(n) = Θ(n) by direct expansion.
</details>

### 1.5 Divide and Conquer — The Paradigm

Many algorithms follow this 3-step pattern:

```
1. DIVIDE:   Break the problem into smaller subproblems
2. CONQUER:  Solve subproblems recursively (base case = solve directly)
3. COMBINE:  Merge solutions of subproblems into solution for original
```

| Algorithm | Divide | Conquer | Combine |
|-----------|--------|---------|---------|
| **Merge Sort** | Split array in half | Sort each half | Merge two sorted halves — O(n) |
| **Binary Search** | Pick middle element | Search left or right half | No combining needed |
| **Strassen's** | Split matrices into 4 sub-matrices | 7 recursive multiplications (not 8) | Add/subtract sub-results |

### 1.6 Recursion Tree Method (When Master Theorem Doesn't Apply)

**Steps:**
1. **Draw the tree** — root = f(n), each node splits into `a` children of size n/b
2. **Compute cost per level** — level 0: f(n), level 1: a·f(n/b), level 2: a²·f(n/b²), ...
3. **Count levels** — tree has log_b(n) levels
4. **Sum all levels** — look for geometric/arithmetic pattern
5. **Identify pattern:**
   - Costs **increasing** → last level (leaves) dominates → like Case 1
   - Costs **equal** → multiply by number of levels → like Case 2
   - Costs **decreasing** → root dominates → like Case 3

**Example:** T(n) = 4T(n/2) + cn

```
Level 0:  cn                           cost = cn
Level 1:  4 × c(n/2) = 2cn            cost = 2cn
Level 2:  16 × c(n/4) = 4cn           cost = 4cn
...
Level k:  cost = 2^k · cn
Levels = log₂n

Total = cn(1 + 2 + 4 + ... + 2^(log₂n)) = cn · (2n-1)/(2-1) ≈ cn · 2n
→ T(n) = Θ(n²)   [leaves dominate — geometric series with ratio 2 > 1]
```

### 1.7 Substitution Method (Guess and Prove)

**Steps:**
1. **Guess** the answer (from Master Theorem or recursion tree)
2. **Assume** it holds for all sizes < n (inductive hypothesis)
3. **Substitute** into the recurrence and simplify
4. **Prove** the bound holds for n
5. **Verify** the base case

**Example:** Prove T(n) = 2T(n/2) + n is O(n log n)

```
Guess: T(n) ≤ cn log n

Assume: T(n/2) ≤ c(n/2) log(n/2)

Substitute:
T(n) = 2T(n/2) + n
     ≤ 2 · c(n/2) log(n/2) + n
     = cn log(n/2) + n
     = cn(log n - 1) + n
     = cn log n - cn + n
     ≤ cn log n        ← TRUE when c ≥ 1 ✓
```

### 1.8 Loop Invariants (Correctness Proofs)

**What is it?** A condition that is TRUE before and after every iteration of a loop. Used to PROVE an algorithm is correct.

**3 Properties to show:**
1. **Initialization:** Invariant is true BEFORE the first iteration
2. **Maintenance:** If true before an iteration, still true after it
3. **Termination:** When loop ends, the invariant gives us the desired result

**Example — Selection Sort:**

```
SelectionSort(A, n):
    for i = 0 to n-2:
        minIdx = i
        for j = i+1 to n-1:
            if A[j] < A[minIdx]: minIdx = j
        swap(A[i], A[minIdx])
```

**Loop Invariant:** "At the start of iteration i, A[0..i-1] contains the i smallest elements in sorted order."

- **Initialization (i=0):** A[0..-1] is empty — trivially sorted ✓
- **Maintenance:** We find the minimum of A[i..n-1] and place it at A[i]. So A[0..i] now has i+1 smallest elements sorted ✓
- **Termination (i=n-1):** A[0..n-2] has n-1 smallest elements sorted. The last element must be the largest → entire array sorted ✓

---

## TOPIC 2: Stack Applications ⭐⭐⭐

> Tested in 2024 Mid & 2026 Mid. Postfix evaluation and infix-to-postfix are guaranteed.

### 2.1 Theory You Need

**Stack = LIFO (Last In, First Out)**
- push(x): add on top — O(1)
- pop(): remove & return top — O(1)
- peek(): view top — O(1)

**Operator Precedence (memorize):**
```
^ (power)     → Precedence 3, RIGHT-associative
* /           → Precedence 2, LEFT-associative
+ -           → Precedence 1, LEFT-associative
```

**Infix → Postfix (Shunting-Yard Algorithm):**
```
Scan left to right:
  Operand → straight to OUTPUT
  '('     → push to STACK
  ')'     → pop stack to output until '(' found, discard '('
  Operator → pop stack to output while top has HIGHER OR EQUAL precedence, then push operator
At end: pop everything from stack to output
```

**Postfix Evaluation:**
```
Scan left to right:
  Operand → PUSH to stack
  Operator → POP two operands (op2 first, then op1), compute op1 ○ op2, PUSH result
  
⚠️ ORDER MATTERS: op2 is popped FIRST. Result = op1 operator op2
```

### 2.2 Solved Past Paper Questions

**Q1. [Past Year 2026 Mid] Convert a+((b-c)-d) to postfix. Show stack at each step.**

| Step | Token | Stack | Output |
|------|-------|-------|--------|
| 1 | a | (empty) | a |
| 2 | + | + | a |
| 3 | ( | + ( | a |
| 4 | ( | + ( ( | a |
| 5 | b | + ( ( | a b |
| 6 | - | + ( ( - | a b |
| 7 | c | + ( ( - | a b c |
| 8 | ) | + ( | a b c - |
| 9 | - | + ( - | a b c - |
| 10 | d | + ( - | a b c - d |
| 11 | ) | + | a b c - d - |
| 12 | End | (empty) | a b c - d - + |

**Answer: a b c - d - +**

**Q2. [Past Year 2024] Evaluate postfix: A B C + C B A - + where A=1, B=2, C=3**

Expression with values: 1 2 3 + 3 2 1 - +

| Step | Token | Action | Stack |
|------|-------|--------|-------|
| 1 | 1 | Push | [1] |
| 2 | 2 | Push | [1, 2] |
| 3 | 3 | Push | [1, 2, 3] |
| 4 | + | Pop 3,2 → 2+3=5 | [1, 5] |
| 5 | 3 | Push | [1, 5, 3] |
| 6 | 2 | Push | [1, 5, 3, 2] |
| 7 | 1 | Push | [1, 5, 3, 2, 1] |
| 8 | - | Pop 1,2 → 2-1=1 | [1, 5, 3, 1] |
| 9 | + | Pop 1,3 → 3+1=4 | [1, 5, 4] |

Stack has [1, 5, 4]. Top two: 5 × 4 = **20** (if final × operator implied)

**Q3. [Past Year 2026 Mid] Fill blanks in infix-to-postfix algorithm:**

```
INFIX-TO-POSTFIX(expression):
    for each token t:
        if t is operand:       → (1) Append t to output O
        else if t is '(':      → (2) Push t onto stack S
        else if t is ')':
            while top ≠ '(':   → (3) Pop from S, append to O
                               → (4) Pop '(' from S (discard)
        else (operator):
            while S not empty AND top ≠ '(' AND
                  precedence(top) >= precedence(t):
                               → (5) Pop from S, append to O
                               → (6) Push t onto S
    while S not empty:         → (7) Pop from S, append to O
```

### 2.3 Practice

**P1.** Convert `(a + b) * (c - d) / e` to postfix
<details><summary>Answer</summary>

| Token | Stack | Output |
|-------|-------|--------|
| ( | ( | |
| a | ( | a |
| + | ( + | a |
| b | ( + | a b |
| ) | (empty) | a b + |
| * | * | a b + |
| ( | * ( | a b + |
| c | * ( | a b + c |
| - | * ( - | a b + c |
| d | * ( - | a b + c d |
| ) | * | a b + c d - |
| / | / | a b + c d - * |
| e | / | a b + c d - * e |
| End | | a b + c d - * e / |

**Answer: a b + c d - * e /**
</details>

**P2.** Evaluate postfix: `6 2 3 + - 3 8 2 / + * 2 ^ 3 +`
<details><summary>Answer</summary>

| Step | Token | Action | Stack |
|------|-------|--------|-------|
| 1 | 6 | Push | [6] |
| 2 | 2 | Push | [6,2] |
| 3 | 3 | Push | [6,2,3] |
| 4 | + | 2+3=5 | [6,5] |
| 5 | - | 6-5=1 | [1] |
| 6 | 3 | Push | [1,3] |
| 7 | 8 | Push | [1,3,8] |
| 8 | 2 | Push | [1,3,8,2] |
| 9 | / | 8/2=4 | [1,3,4] |
| 10 | + | 3+4=7 | [1,7] |
| 11 | * | 1×7=7 | [7] |
| 12 | 2 | Push | [7,2] |
| 13 | ^ | 7²=49 | [49] |
| 14 | 3 | Push | [49,3] |
| 15 | + | 49+3=52 | [52] |

**Answer: 52**. Verify: ((6-(2+3)) × (3+8/2))² + 3 = (1×7)² + 3 = 49+3 = 52 ✓
</details>

**P3.** Evaluate postfix: `5 3 + 8 2 - *`
<details><summary>Answer</summary>
5+3=8, 8-2=6, 8×6 = **48**
</details>

### 2.4 Other Stack Applications You Should Know

**Balanced Parentheses:**
```
Scan left to right:
  Opening bracket ( [ {  →  PUSH
  Closing bracket ) ] }  →  POP, check if it matches the top
  If mismatch or stack empty on pop → NOT BALANCED
At end: stack must be EMPTY for balanced
```

Example: `{[()]}` → Push {, [, ( → see ), matches ( pop → see ], matches [ pop → see }, matches { pop → stack empty → **BALANCED** ✓

Example: `{[(])}` → Push {, [, ( → see ], but top is ( → **NOT BALANCED** ✗

**Prefix Evaluation (scan RIGHT to LEFT):**
```
Scan RIGHT to LEFT:
  Operand → PUSH
  Operator → POP two (op1 first, then op2), compute op1 ○ op2, PUSH result
  ⚠️ In prefix: op1 is popped FIRST (opposite of postfix!)
```

**Infix Evaluation (Two-Stack Method):**
```
Use two stacks: VALS (values) and OPS (operators)
Scan left to right:
  Number → push to VALS
  Operator → while OPS top has ≥ precedence: APPLY_TOP, then push to OPS
  '(' → push to OPS
  ')' → APPLY_TOP until '(' found
At end: APPLY_TOP until OPS empty

APPLY_TOP: pop operator from OPS, pop 2 from VALS, compute, push result to VALS
```

**Expression Trees:**
```
Preorder traversal of expression tree → PREFIX notation
Inorder traversal of expression tree  → INFIX notation
Postorder traversal of expression tree → POSTFIX notation
```

Example tree for `(a + b) * c`:
```
        *
       / \
      +   c
     / \
    a   b

Preorder:  * + a b c  (prefix)
Inorder:   a + b * c  (infix — but needs parentheses for correct meaning)
Postorder: a b + c *  (postfix)
```

---

## TOPIC 3: Heap Operations ⭐⭐⭐

> The single most tested topic in BITS DSA exams. Know INSERT, DELETE, BUILD-HEAP, HEAP-SORT inside out.

### 3.1 Theory You Need

**Heap = Complete Binary Tree + Heap Property**
- **Max-Heap:** Parent ≥ Children (root = maximum)
- **Min-Heap:** Parent ≤ Children (root = minimum)

**Array Representation (1-indexed):**
```
Parent of i  = ⌊i/2⌋
Left child   = 2i
Right child  = 2i + 1
```

**Operations:**

| Operation | How | Time |
|-----------|-----|------|
| **Insert** | Add at end → **Bubble UP** (swap with parent while violated) | O(log n) |
| **Delete-Max** | Swap root with last → remove last → **Bubble DOWN** (heapify) | O(log n) |
| **Build-Heap** | Bottom-up heapify from ⌊n/2⌋ down to 1 | **O(n)** not O(n log n)! |
| **Find-Max** | Return root A[1] | O(1) |

**Why BUILD-HEAP is O(n):** Most nodes are near the bottom and heapify very little. The sum converges: Σ h/2^h → O(n).

**Heap Sort:**
1. Build max-heap → O(n)
2. Repeatedly: swap root with last unsorted → heapify root → n × O(log n)
3. **Total: O(n log n) | In-place | NOT stable**

### 3.2 Solved Past Paper Questions

**Q1. [Past Year — Exam Style 15 marks] MAX-HEAP DELETE correctness**

Proposed DELETE implementation:
```
DELETE_v1(A, i):
    A[i] = A[A.heap-size]
    A.heap-size = A.heap-size - 1
    MAX-HEAPIFY(A, i)     ← only sifts DOWN
```

**Is it correct? NO! Counter-example:**

```
Original heap:    [16, 4, 10, 1, 3, 9]

        16
       /  \
      4    10
     / \   /
    1   3  9

Delete i=4 (value 1): Replace with last element 9
Array becomes: [16, 4, 10, 9, 3]

MAX-HEAPIFY(4): Node 9 is a leaf → no swap.
Result: [16, 4, 10, 9, 3]

PROBLEM: Node 4 (value 9) > Node 2 (value 4 = parent) → HEAP VIOLATION!
MAX-HEAPIFY only sifts DOWN but 9 needs to go UP.
```

**Correct DELETE:**
```
DELETE(A, i):
    A[i] = A[A.heap-size]
    A.heap-size--
    if A[i] > A[PARENT(i)]:
        // New value is bigger than parent → bubble UP
        BUBBLE-UP(A, i)
    else:
        // New value might be smaller than children → sift DOWN
        MAX-HEAPIFY(A, i)
```

**Q2. Insert 10, 20, 15, 30, 40, 5, 25 into empty max-heap**

**Insert 10:** `[10]`
**Insert 20:** 20 > 10 → swap → `[20, 10]`
**Insert 15:** 15 < 20 → stop → `[20, 10, 15]`
**Insert 30:** 30 > 10 → swap; 30 > 20 → swap → `[30, 20, 15, 10]`
**Insert 40:** 40 > 20 → swap; 40 > 30 → swap → `[40, 30, 15, 10, 20]`
**Insert 5:** 5 < 15 → stop → `[40, 30, 15, 10, 20, 5]`
**Insert 25:** 25 > 15 → swap; 25 < 40 → stop → `[40, 30, 25, 10, 20, 5, 15]`

```
Final max-heap:
        40
       /  \
      30   25
     / \  / \
   10 20 5  15
```

**Q3. Build min-heap from [9, 6, 5, 0, 8, 2, 7, 1, 3]**

Start from last non-leaf = index 3 (0-indexed), heapify down:

```
Initial:       9, 6, 5, 0, 8, 2, 7, 1, 3

Heapify(3): 0 < children 1,3 → no change
Heapify(2): 5 > child 2 → swap 5↔2 → [9,6,2,0,8,5,7,1,3]
Heapify(1): 6 > child 0 → swap 6↔0 → [9,0,2,6,8,5,7,1,3]
            then 6 > child 1 → swap 6↔1 → [9,0,2,1,8,5,7,6,3]
Heapify(0): 9 > child 0 → swap 9↔0 → [0,9,2,1,8,5,7,6,3]
            then 9 > child 1 → swap 9↔1 → [0,1,2,9,8,5,7,6,3]
            then 9 > child 3 → swap 9↔3 → [0,1,2,3,8,5,7,6,9]

Final min-heap: [0, 1, 2, 3, 8, 5, 7, 6, 9]
```

### 3.3 Heap Sort Trace

**Sort [4, 1, 3, 2, 5] using Heap Sort:**

**Phase 1: Build Max-Heap**
```
Initial: [4, 1, 3, 2, 5]
Heapify(1): 1 < child 5 → swap → [4, 5, 3, 2, 1]
Heapify(0): 4 < child 5 → swap → [5, 4, 3, 2, 1]

Max-heap: [5, 4, 3, 2, 1]
```

**Phase 2: Extract max repeatedly**
```
Swap 5↔1, heapify [1,4,3,2] → [4,2,3,1] | Sorted: _,_,_,_,5
Swap 4↔1, heapify [1,2,3]   → [3,2,1]   | Sorted: _,_,_,4,5
Swap 3↔1, heapify [1,2]     → [2,1]     | Sorted: _,_,3,4,5
Swap 2↔1                     → [1]       | Sorted: _,2,3,4,5

Final sorted: [1, 2, 3, 4, 5] ✓
```

### 3.4 Practice

**P1.** Insert keys 50, 40, 60, 30, 70, 20 into an empty max-heap. Show final array.
<details><summary>Answer</summary>

Insert 50: [50]
Insert 40: 40<50, stop: [50,40]
Insert 60: 60>50, swap: [60,40,50]
Insert 30: 30<40, stop: [60,40,50,30]
Insert 70: 70>40→swap; 70>60→swap: [70,60,50,30,40]
Insert 20: 20<50, stop: [70,60,50,30,40,20]

```
        70
       /  \
      60   50
     / \  /
    30 40 20
```
</details>

**P2.** TRUE or FALSE: Build-Heap takes O(n log n) time.
<details><summary>Answer</summary>
FALSE. Build-Heap is O(n). Although each heapify is O(log n), most nodes are at the bottom and heapify very little. The sum converges to O(n).
</details>

---

## TOPIC 4: Sorting Algorithms ⭐⭐

> Know the comparison table. Quick Sort partition code is asked frequently.

### 4.1 The Table You Must Memorize

| Algorithm | Best | Average | Worst | Space | Stable? | In-place? |
|-----------|------|---------|-------|-------|---------|-----------|
| **Bubble Sort** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| **Insertion Sort** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | O(n) | **Yes** | No |
| **Quick Sort** | O(n log n) | O(n log n) | **O(n²)** | O(log n) | No | Yes |
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) | No | Yes |

**When to pick what:**
- Need **stable** sort → Merge Sort
- Need **in-place + guaranteed O(n log n)** → Heap Sort
- **Default choice** (fastest in practice) → Quick Sort
- **Small/nearly sorted** data → Insertion Sort

### 4.2 Quick Sort PARTITION (Frequently Asked as Code Completion)

```
PARTITION(A, p, r):
    pivot = A[r]                 // Last element as pivot
    i = p - 1                   // Index of smaller element
    for j = p to r-1:
        if A[j] <= pivot:
            i = i + 1
            swap(A[i], A[j])
    swap(A[i+1], A[r])          // Place pivot in correct position
    return i + 1                // Pivot index
```

**Quick Sort:**
```
QUICKSORT(A, p, r):
    if p < r:
        q = PARTITION(A, p, r)
        QUICKSORT(A, p, q-1)    // Left of pivot
        QUICKSORT(A, q+1, r)    // Right of pivot
```

**Worst case O(n²):** When array is already sorted and pivot is always min/max → T(n) = T(n-1) + n.
**Best case O(n log n):** When partition always splits evenly → T(n) = 2T(n/2) + n.

### 4.3 Three-Way Partition (Dutch National Flag)

When array has many **duplicates**, standard Quick Sort degrades. Three-way partition fixes this:

```
THREE-WAY-PARTITION(A, p, r):
    pivot = A[r]
    lt = p        // A[p..lt-1] = elements < pivot
    eq = p        // A[lt..eq-1] = elements = pivot
    gt = r        // A[gt+1..r] = elements > pivot
    
    while eq <= gt:
        if A[eq] < pivot:     swap(A[lt], A[eq]); lt++; eq++
        else if A[eq] == pivot: eq++
        else:                  swap(A[eq], A[gt]); gt--
```

### 4.4 Practice

**P1.** TRUE or FALSE: Merge Sort is the fastest comparison-based sorting algorithm.
<details><summary>Answer</summary>
TRUE in terms of worst-case guarantee (O(n log n)), but FALSE in practice — Quick Sort is typically faster due to better cache performance and lower constant factors. Also, the lower bound for comparison-based sorting is Ω(n log n), so Merge Sort, Heap Sort, and Quick Sort (average) are all optimal.
</details>

**P2.** TRUE or FALSE: Any comparison-based sort must make at least Ω(n log n) comparisons in the worst case.
<details><summary>Answer</summary>
TRUE. Decision tree argument: n! leaves → height ≥ log₂(n!) = Ω(n log n).
</details>

---
---

# ⏱️ HOUR 2 — Core Data Structures & Graph Algorithms

---

## TOPIC 5: BST & Binary Trees ⭐⭐⭐

> Tested in 2024 Mid & 2026 Mid. Tree reconstruction, traversals, BST insert/delete.

### 5.1 Theory You Need

**Binary Tree Properties:**
- Max nodes at level i = **2ⁱ**
- Max nodes in tree of height h = **2^(h+1) − 1**
- Min height with n nodes = **⌊log₂ n⌋**
- Full binary tree: L = I + 1 (leaves = internal nodes + 1)

**Binary Tree Types (know these definitions):**

| Type | Definition | Example |
|------|-----------|---------|
| **Full** | Every node has 0 or 2 children (never 1) | Decision tree |
| **Complete** | All levels full except possibly last, filled left-to-right | Heap is always a complete BT |
| **Perfect** | ALL levels completely full | 2^(h+1)−1 nodes exactly |
| **Balanced** | Height of left and right subtree differ by at most 1 | AVL tree |
| **Degenerate/Skewed** | Every node has only 1 child — basically a linked list | Worst-case BST |

> **Exam trap:** A Complete tree is NOT necessarily Full. A Full tree is NOT necessarily Complete. A Perfect tree is BOTH Full and Complete.

**BST Property:** Left < Root < Right (for every node)

| BST Operation | Average | Worst (skewed) |
|---------------|---------|----------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

**BST Delete — 3 Cases:**
1. **Leaf** → just remove
2. **One child** → replace node with its child
3. **Two children** → replace with **inorder successor** (smallest in right subtree), then delete that successor

**Tree Traversals:**

| Traversal | Order | Mnemonic | Gives |
|-----------|-------|----------|-------|
| Preorder | Root → Left → Right | **NLR** | Prefix expression |
| Inorder | Left → Root → Right | **LNR** | **Sorted order (BST)** |
| Postorder | Left → Right → Root | **LRN** | Postfix expression |
| Level-order | BFS with queue | — | Breadth-first |

**Reconstruct Tree from Traversals:**
- Inorder + Preorder → ✅ Unique tree
- Inorder + Postorder → ✅ Unique tree
- Preorder + Postorder alone → ❌ NOT unique

**Method:** First element of preorder (or last of postorder) = ROOT. Find root in inorder → everything LEFT of it = left subtree, RIGHT = right subtree. Recurse.

### 5.2 Solved Past Paper Questions

**Q1. [Past Year 2024] Construct tree from:**
- Inorder: D B E A F C
- Postorder: D E B F C A

**Step 1:** Last of postorder = **A** = root.
Find A in inorder: Left subtree = {D B E}, Right subtree = {F C}

**Step 2:** In postorder, next root from right is **C** (root of right subtree).
Find C in inorder right part: Left = {F}, Right = {}

**Step 3:** Next in postorder (going left) is **B** (root of left subtree).
Find B in inorder left part: Left = {D}, Right = {E}

```
        A
       / \
      B   C
     / \ /
    D  E F
```

**Verify:**
- Inorder (LNR): D-B-E-A-F-C ✓
- Postorder (LRN): D-E-B-F-C-A ✓

**Q2. [Past Year 2024] Print BST in descending order**

**Answer:** Use **reverse inorder traversal** (Right → Root → Left):

```
PRINT_DESCENDING(node):
    if node is NULL: return
    PRINT_DESCENDING(node.right)    // Visit right first (larger values)
    print(node.key)                  // Print current
    PRINT_DESCENDING(node.left)      // Then left (smaller values)
```

**Q3. [Past Year 2026 Mid] Binary tree array representation**

For a binary tree stored in array (0-indexed):
```
Parent of i  = ⌊(i-1)/2⌋
Left child   = 2i + 1
Right child  = 2i + 2
```

For 1-indexed:
```
Parent of i  = ⌊i/2⌋
Left child   = 2i
Right child  = 2i + 1
```

**Q4. BST Delete example — Delete 60, 30, 70 from BST:**

```
Start:          60
              /    \
            30      70
           / \       \
          20  40      80
             /
            35
```

**Delete 60** (two children): Inorder successor = 70 (smallest in right subtree). But 70 has a right child 80.
Replace 60 with 70, then 70's spot gets 80.
```
                70
              /    \
            30      80
           / \
          20  40
             /
            35
```

**Delete 30** (two children): Inorder successor = 35. Replace 30 with 35, remove original 35.
```
                70
              /    \
            35      80
           / \
          20  40
```

**Delete 70** (one child — right child 80): Replace 70 with 80.
```
                80
              /
            35
           / \
          20  40
```

### 5.3 Practice

**P1.** Given preorder: A B D E C F and inorder: D B E A F C, construct the tree.
<details><summary>Answer</summary>
First of preorder = A = root.
Inorder split: Left = {D,B,E}, Right = {F,C}
Next preorder = B = left subtree root. Inorder split: Left={D}, Right={E}
Next = D (leaf). Next = E (leaf).
Right side: C = root. Inorder: Left={F}, Right={}

```
        A
       / \
      B   C
     / \ /
    D  E F
```
</details>

**P2.** What traversal of a BST gives sorted output?
<details><summary>Answer</summary>
Inorder (Left → Root → Right)
</details>

### 5.4 BST vs Heap — Don't Confuse Them!

| Feature | BST | Heap |
|---------|-----|------|
| **Ordering** | Left < Root < Right | Parent ≥ Children (max) or Parent ≤ Children (min) |
| **Shape** | Any shape (can be skewed) | Always a **complete** binary tree |
| **Find max** | O(log n) balanced, O(n) skewed — go rightmost | **O(1)** — just return root |
| **Search** | **O(log n)** balanced — go left/right | O(n) — no ordering to guide search |
| **Sorted output** | **O(n)** — inorder traversal | O(n log n) — need repeated extraction |
| **Insert** | O(log n) | O(log n) |
| **Delete** | O(log n) | O(log n) |
| **Array storage** | Wastes space for non-complete trees | Perfect — always complete |
| **Use case** | Dictionary, ordered data, range queries | Priority queue, scheduling, sorting |

> **Key difference in one line:** BST gives you **search** and **sorted order**. Heap gives you **fast max/min** and **priority queue**.

---

## TOPIC 6: BFS & DFS ⭐⭐⭐

> Tested in 2024 Mid & 2026 Mid. Know traces with queue/stack states.

### 6.1 Theory You Need

| | BFS | DFS |
|-|-----|-----|
| **Data structure** | Queue | Stack / Recursion |
| **Strategy** | Level by level | Go deep, backtrack |
| **Time** | O(V+E) | O(V+E) |
| **Shortest path** | ✅ Yes (unweighted) | ❌ No |
| **Cycle detection** | Via visited check | Via **back edges** |
| **Pick when** | Shortest path, level-order | Cycle detection, topological sort |

**Graph Basics — Terminology You Must Know:**

```
Graph G = (V, E)  where V = set of vertices, E = set of edges

Undirected: edges are {u,v} — go both ways (friendship)
Directed:   edges are (u,v) — one way only (follows on Instagram)
Weighted:   each edge has a cost/distance
```

| Term | Meaning | Example |
|------|---------|---------|
| **Degree** | Number of edges connected to a vertex | In undirected: deg(v). In directed: in-degree + out-degree |
| **Path** | Sequence of vertices connected by edges | A→B→C→D |
| **Cycle** | Path that starts and ends at the same vertex | A→B→C→A |
| **Connected** | Path exists between every pair of vertices | |
| **Complete graph** | Edge between every pair of vertices | n(n-1)/2 edges |
| **Bipartite** | Vertices can be split into 2 groups with edges only between groups | |

**Handshaking Lemma:** In undirected graph, Σ degree = **2|E|** (each edge contributes 2 to total degree)

**Graph Representation — Which to Pick:**

| | Adjacency Matrix | Adjacency List |
|-|-------------------|----------------|
| **Space** | O(V²) | O(V + E) |
| **Check if edge exists** | **O(1)** — just look up matrix[u][v] | O(degree) — scan neighbour list |
| **List all neighbours** | O(V) — scan entire row | **O(degree)** — just iterate list |
| **Best for** | **Dense** graphs (many edges) | **Sparse** graphs (few edges) |
| **Memory** | Wastes space if few edges | Efficient |

> **Exam tip:** Most real-world graphs are sparse → adjacency list is the default choice.

**DFS Discovery & Finish Times:**
```
DFS tracks two timestamps for each vertex:
  discovery[v] = time when v is first visited
  finish[v]    = time when all of v's descendants are fully explored

Edge classification using these times:
  Back edge (u→v):    discovery[v] < discovery[u] < finish[u] < finish[v]
                       v is an ANCESTOR of u → CYCLE detected!
  Tree edge (u→v):    discovery[u] < discovery[v] < finish[v] < finish[u]
  Forward edge (u→v): v is a descendant, already finished
  Cross edge (u→v):   v is in a different branch, already finished
```

**BFS Algorithm:**
```
BFS(G, start):
    mark start as visited
    enqueue(start)
    while queue not empty:
        u = dequeue()
        for each neighbour v of u:
            if v not visited:
                mark v visited
                enqueue(v)
```

**DFS Algorithm:**
```
DFS(G, start):
    mark start as visited
    for each neighbour v of start:
        if v not visited:
            DFS(G, v)
```

**DFS Edge Classification:**
- **Tree edge:** To unvisited vertex (forms DFS tree)
- **Back edge:** To ancestor → **indicates CYCLE**
- **Forward edge:** To descendant (not tree edge)
- **Cross edge:** To vertex in different branch

### 6.2 Solved Past Paper Questions

**Q1. [Past Year 2024] BFS from vertex 0 on adjacency matrix:**

```
    0  1  2  3  4
0 [ 0  1  1  0  0 ]
1 [ 1  0  0  1  1 ]
2 [ 1  0  0  0  1 ]
3 [ 0  1  0  0  0 ]
4 [ 0  1  1  0  0 ]
```

BFS from vertex 0:

| Step | Dequeue | Queue (after processing) | Visited |
|------|---------|--------------------------|---------|
| 0 | — | [0] | {0} |
| 1 | 0 | [1, 2] | {0, 1, 2} |
| 2 | 1 | [2, 3, 4] | {0, 1, 2, 3, 4} |
| 3 | 2 | [3, 4] | (4 already visited) |
| 4 | 3 | [4] | (1 already visited) |
| 5 | 4 | [] | (1,2 already visited) |

**BFS order: 0 → 1 → 2 → 3 → 4**

**Q2. [Past Year 2026 Mid] BFS trace with letters**

Given graph: A—B, A—C, B—D, B—E, C—F, D—E, E—F. BFS from A.

| Dequeue | Enqueue | Queue |
|---------|---------|-------|
| A | B, C | [B, C] |
| B | D, E | [C, D, E] |
| C | F | [D, E, F] |
| D | — | [E, F] |
| E | — | [F] |
| F | — | [] |

**BFS order: A → B → C → D → E → F**

**Q3. [Past Year 2024] Topological Sort**

For a DAG, topological sort = linear ordering where u comes before v for every edge (u,v).

**Two methods:**
1. **DFS-based:** Run DFS, output vertices in reverse finish time
2. **Kahn's (BFS-based):** Repeatedly remove vertices with 0 in-degree

### 6.3 Practice

**P1.** Run DFS from vertex A on: A—B, A—C, B—D, C—D, D—E
<details><summary>Answer</summary>
DFS from A: Visit A → go to B → go to D → go to C (already visited from D? depends on adjacency order) → go to E.

Assuming alphabetical adjacency: A→B→D→C(visited)→E→C.
DFS order: A, B, D, E, C (one possible ordering depending on adjacency list order)
</details>

**P2.** TRUE or FALSE: BFS always finds the shortest path in a weighted graph.
<details><summary>Answer</summary>
FALSE. BFS finds shortest path only in UNWEIGHTED graphs (minimum hops). For weighted graphs, use Dijkstra's algorithm.
</details>

---

## TOPIC 7: Queue & Circular Queue ⭐⭐

> Circular queue trace asked in 2024 Mid. Two-stack queue asked frequently.

### 7.1 Theory You Need

**Queue = FIFO (First In, First Out)**
- enqueue(x): add at rear — O(1)
- dequeue(): remove from front — O(1)

**Circular Queue** — solves phantom overflow:
```
Enqueue: rear = (rear + 1) % capacity
Dequeue: front = (front + 1) % capacity
Full:    (rear + 1) % capacity == front   (or count == capacity)
Empty:   front == rear                    (or count == 0)
```

**Two-Stack Queue:**
```
S1 = inbox, S2 = outbox

ENQUEUE(x): S1.push(x)                       → O(1)
DEQUEUE():  If S2 empty → transfer all S1→S2   → O(n) worst, O(1) amortized
            Return S2.pop()
```

**Deque (Double-Ended Queue):**
- Insert and remove at **BOTH ends** — O(1)
- **Input-restricted deque:** insert at one end only, delete from both
- **Output-restricted deque:** delete from one end only, insert at both
- Used in: sliding window maximum, 0-1 BFS, palindrome check

**Priority Queue — Which Implementation to Pick:**

| Implementation | Insert | Delete-min/max | Find-min/max |
|---------------|--------|----------------|-------------|
| Unsorted array | **O(1)** | O(n) | O(n) |
| Sorted array | O(n) | **O(1)** | **O(1)** |
| **Binary Heap** ← best | O(log n) | O(log n) | **O(1)** |
| Balanced BST | O(log n) | O(log n) | O(log n) |

> **Use binary heap** for priority queue — it's the best balance of all operations.

### 7.2 Solved Past Paper Questions

**Q1. [Past Year 2024] Circular queue size 5, front=0, rear=-1. Operations:**

Enqueue(10), Enqueue(20), Enqueue(30), Enqueue(40), Enqueue(50), Dequeue(), Enqueue(60), Dequeue()

| Operation | Array [0..4] | Front | Rear |
|-----------|-------------|-------|------|
| Initial | [_, _, _, _, _] | 0 | -1 |
| Enqueue(10) | [10, _, _, _, _] | 0 | 0 |
| Enqueue(20) | [10, 20, _, _, _] | 0 | 1 |
| Enqueue(30) | [10, 20, 30, _, _] | 0 | 2 |
| Enqueue(40) | [10, 20, 30, 40, _] | 0 | 3 |
| Enqueue(50) | [10, 20, 30, 40, 50] | 0 | 4 (FULL) |
| Dequeue()→10 | [_, 20, 30, 40, 50] | 1 | 4 |
| Enqueue(60) | [60, 20, 30, 40, 50] | 1 | 0 ← **wraps!** |
| Dequeue()→20 | [60, _, 30, 40, 50] | 2 | 0 |

**Key:** After Enqueue(60), rear = (4+1)%5 = 0. Value 60 stored at index 0 — this is the circular nature!

**Q2. [Past Year 2024] Queue using two stacks — amortized analysis**

Each element in its lifetime:
- Pushed to S1: 1 time
- Popped from S1: 1 time
- Pushed to S2: 1 time
- Popped from S2: 1 time

**Total: 4 operations per element → O(1) amortized per enqueue/dequeue**

### 7.3 Practice

**P1.** Circular queue size 4, front=0, rear=-1. Operations: Enqueue(A), Enqueue(B), Enqueue(C), Enqueue(D), Enqueue(E). What happens?
<details><summary>Answer</summary>
After 4 enqueues: [A,B,C,D], front=0, rear=3, FULL.
Enqueue(E): OVERFLOW! Queue is full. Cannot insert.
</details>

---

## TOPIC 8: Amortized Analysis ⭐⭐

> Dynamic array doubling asked in 2024 Mid. Know the aggregate method.

### 8.1 Theory You Need

**Amortized analysis** gives the **average cost per operation** over a worst-case sequence of operations.

| Scenario | Key Insight | Amortized Cost |
|----------|-------------|----------------|
| **MULTIPOP stack** | Total pops ≤ total pushes across all operations | **O(1)** per op |
| **Dynamic array (doubling)** | Copy costs: 1+2+4+...+n < 2n | **O(1)** per insert |
| **Two-stack queue** | Each element: push S1, pop S1, push S2, pop S2 = 4 ops | **O(1)** per op |
| **Binary counter** | Bit i flips every 2ⁱ increments → total flips < 2n | **O(1)** per increment |

**Why doubling gives O(1) but constant increment gives O(n):**
- **Doubling:** Copy costs = 1+2+4+8+...+n = 2n-1 < 2n → geometric series → O(1) amortized
- **Constant +k:** Copy costs = k+2k+3k+...+nk/k = O(n²/k) → arithmetic series → O(n) amortized

### 8.2 Solved Past Paper Question

**Q1. [Past Year 2024] Dynamic array — aggregate method**

Start with array of size 1. Insert n elements. Array doubles when full.

```
Insert 1: size 1→1 (no copy)
Insert 2: size 1→2, copy 1 element
Insert 3: size 2→4, copy 2 elements
Insert 4: no copy (space available)
Insert 5: size 4→8, copy 4 elements
Insert 6,7,8: no copy
Insert 9: size 8→16, copy 8 elements
...
```

Copy costs: 1 + 2 + 4 + 8 + ... + n/2 + n = 2n - 1

**Total cost for n insertions** = n (basic inserts) + 2n - 1 (copies) < 3n

**Amortized cost per insert = 3n/n = O(1)** ✓

### 8.3 Practice

**P1.** TRUE or FALSE: MULTIPOP(k) on a stack of n elements always takes O(k) time, so the amortized cost per operation is O(k).
<details><summary>Answer</summary>
FALSE. While a single MULTIPOP(k) is O(min(k,n)), the amortized cost is O(1). Across any sequence of n operations (push, pop, multipop), the total number of pops (including those in multipop) can never exceed the total number of pushes, which is at most n. So total cost ≤ 2n → O(1) amortized.
</details>

---
---

# ⏱️ HOUR 3 — Remaining Exam Topics

---

## TOPIC 9: Hashing ⭐⭐

> 2026 Mid had a 15-mark hashing question. Know double hashing and quadratic probing.

### 9.1 Theory You Need

**Hash table:** Store key-value pairs with O(1) average lookup.

**Collision resolution:**
- **Chaining:** Each slot has a linked list of elements
- **Open addressing:** Find next empty slot using a probing sequence:
  - **Linear probing:** h(k,i) = (h(k) + i) mod m
  - **Quadratic probing:** h(k,i) = (h(k) + c₁·i + c₂·i²) mod m
  - **Double hashing:** h(k,i) = (h₁(k) + i·h₂(k)) mod m

**Load factor:** α = n/m (items/table size)
- Chaining: average probe = 1 + α
- Open addressing: average probe ≈ 1/(1-α)

### 9.2 Solved Past Paper Question

**Q1. [Past Year 2026 Mid — 15 marks] Double Hashing**

Table size m = 11. h₁(k) = k mod 11. h₂(k) = 7 - (k mod 7).
Insert keys: 10, 22, 31, 4, 15, 28, 17.

**Probe formula:** h(k,i) = (h₁(k) + i·h₂(k)) mod 11

| Key | h₁(k) | h₂(k) | Probes | Slot |
|-----|--------|--------|--------|------|
| 10 | 10%11=10 | 7-(10%7)=7-3=4 | i=0: slot 10 (empty) | **10** |
| 22 | 22%11=0 | 7-(22%7)=7-1=6 | i=0: slot 0 (empty) | **0** |
| 31 | 31%11=9 | 7-(31%7)=7-3=4 | i=0: slot 9 (empty) | **9** |
| 4 | 4%11=4 | 7-(4%7)=7-4=3 | i=0: slot 4 (empty) | **4** |
| 15 | 15%11=4 | 7-(15%7)=7-1=6 | i=0: slot 4 (taken!), i=1: (4+6)%11=10 (taken!), i=2: (4+12)%11=5 (empty) | **5** |
| 28 | 28%11=6 | 7-(28%7)=7-0=7 | i=0: slot 6 (empty) | **6** |
| 17 | 17%11=6 | 7-(17%7)=7-3=4 | i=0: slot 6 (taken!), i=1: (6+4)%11=10 (taken!), i=2: (6+8)%11=3 (empty) | **3** |

**Final table:**

| Index | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
|-------|---|---|---|---|---|---|---|---|---|---|---|
| Key | 22 | — | — | 17 | 4 | 15 | 28 | — | — | 31 | 10 |

### 9.3 Practice

**P1.** Hash table size 7, h(k) = k mod 7. Insert 14, 21, 28, 35 using linear probing. Show the table.
<details><summary>Answer</summary>
h(14)=0, h(21)=0→collision→slot 1, h(28)=0→1→slot 2, h(35)=0→1→2→slot 3

| 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|
| 14 | 21 | 28 | 35 | — | — | — |

This shows **primary clustering** — all keys hash to 0 and cluster together.
</details>

---

## TOPIC 10: Linked Lists ⭐⭐

> Know the types, operations, and comparison. Bug-finding questions common.

### 10.1 Theory You Need

**Types:**

| Type | Key Feature | Use Case |
|------|-------------|----------|
| **Singly** | One pointer (next) | Simple, forward traversal |
| **Doubly** | Two pointers (prev + next) | O(1) delete with pointer, LRU cache |
| **Circular** | Last→First (last.next = head) | Round-robin scheduling, Josephus problem |
| **Circular Doubly** | Both directions + circular | Most flexible — traverse any direction, no NULL |

> **Circular list difference:** No node has NULL pointer. To detect end, check if you've returned to the head.

**ADT (Abstract Data Type) concept:** An ADT defines WHAT operations a data structure supports (the interface) but not HOW it's implemented. Example: Stack ADT says "push, pop, peek" — you can implement it with array or linked list.

**Array vs Linked List:**

| | Array | Linked List |
|-|-------|-------------|
| Access by index | **O(1)** | O(n) |
| Insert at beginning | O(n) | **O(1)** |
| Memory | Cache-friendly | Scattered, pointer overhead |

### 10.2 Solved Past Paper Question

**Q1. [Past Year 2024] Find bug in doubly linked list delete:**

```c
void delete(Node* node) {
    node->prev->next = node->next;
    node->next->prev = node->prev;
    free(node);
}
```

**Bug:** No NULL check! If `node` is the **head** (prev is NULL) or **tail** (next is NULL), this crashes with null pointer dereference.

**Fix:**
```c
void delete(Node* node) {
    if (node->prev != NULL)
        node->prev->next = node->next;
    if (node->next != NULL)
        node->next->prev = node->prev;
    free(node);
}
```

### 10.3 Practice

**P1.** Reverse a singly linked list. What is the time complexity?
<details><summary>Answer</summary>

```
REVERSE(head):
    prev = NULL
    curr = head
    while curr != NULL:
        next = curr.next
        curr.next = prev
        prev = curr
        curr = next
    return prev
```

Time: O(n), Space: O(1)
</details>

---

## TOPIC 11: Algorithm Basics & Step Counting ⭐

> Foundation topic. Quick questions on loop analysis and best/worst case.

### 11.1 Theory You Need

**5 Characteristics of an Algorithm:** Input, Output, Definiteness, Finiteness, Effectiveness
> Memory: **"I O D F E — I Only Do Fine Exams"**

**RAM Model:** Single processor, constant-time operations, flat memory.

**3 Cases:** Best (min time), Average (expected), **Worst (most used — gives guarantee)**.

**Step Counting — Common Patterns:**

| Code Pattern | Complexity |
|-------------|------------|
| Single loop 1 to n | O(n) |
| Nested loops i=1..n, j=1..n | O(n²) |
| Loop halving (j = n; j>0; j/=2) | O(log n) |
| Loop i=1..n, inner j=1..i | O(n²) — sum = n(n+1)/2 |
| Loop i=1..n, inner j=1..i² | O(n³) — sum of squares |
| Outer n, inner log n | O(n log n) |

### 11.2 Solved Questions

**Q1. Time complexity of:**
```
for i = 1 to n:
    for j = 1 to i²:
        x = x + 1
```

Total = Σᵢ₌₁ⁿ i² = n(n+1)(2n+1)/6 = **Θ(n³)**

**Q2. Time complexity of:**
```
for i = 1 to n:
    j = n
    while j >= 1:
        j = j / 2
```

Outer: n times. Inner: log₂n times. **Total = Θ(n log n)**

**Q3. Time complexity of:**
```
for i = 1 to n:
    for j = 1 to n:
        for k = 1 to n:
            x = x + 1
```

Three nested loops each running n times. **Total = Θ(n³)**

---

## TOPIC 12: Advanced Topics (Comprehensive Exam) ⭐

> These appear in the comprehensive exam. Know the basics.

### 12.1 Floyd-Warshall (All-Pairs Shortest Paths)

**Idea:** For each intermediate vertex k, check if going through k gives a shorter path.

```
For k = 0 to V-1:
    For i = 0 to V-1:
        For j = 0 to V-1:
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

**Time: O(V³)** | Works with negative weights (no negative cycles).

### 12.2 Kruskal's MST (Minimum Spanning Tree)

1. Sort all edges by weight
2. For each edge (lightest first): if it doesn't form a cycle, add it
3. Stop when you have V-1 edges

**Time:** O(E log E) — dominated by sorting.

### 12.3 Prim's MST

1. Start from any vertex
2. Always add the cheapest edge connecting the tree to a non-tree vertex
3. Repeat until all vertices included

**Time:** O(E log V) with priority queue.

### 12.4 0-1 Knapsack (Dynamic Programming)

**Problem:** n items with weight wᵢ and value vᵢ. Capacity W. Maximize value.

```
dp[i][w] = max(
    dp[i-1][w],                     // Don't take item i
    dp[i-1][w - wᵢ] + vᵢ           // Take item i (if wᵢ ≤ w)
)
```

**Time: O(n·W)** | **Space: O(n·W)** (can optimize to O(W))

### 12.5 Solved Past Paper Question

**Q1. [Past Year 2026 Comp] MST using Kruskal's**

Given edges: A-B(4), A-C(8), B-C(2), B-D(5), C-D(5), C-E(7), D-E(3)

Sorted: B-C(2), D-E(3), A-B(4), B-D(5), C-D(5), C-E(7), A-C(8)

| Step | Edge | Weight | Add? |
|------|------|--------|------|
| 1 | B-C | 2 | ✅ |
| 2 | D-E | 3 | ✅ |
| 3 | A-B | 4 | ✅ |
| 4 | B-D | 5 | ✅ (connects {A,B,C} with {D,E}) |
| 5 | C-D | 5 | ❌ (cycle: C-B-D-C) |

**MST edges:** B-C(2), D-E(3), A-B(4), B-D(5). **Total weight = 14**

---
---

# 📋 QUICK REVISION — Last 15 Minutes Before Exam

## Key Formulas

| Formula | Value |
|---------|-------|
| Max nodes at level i | 2ⁱ |
| Max nodes height h | 2^(h+1) − 1 |
| Min height n nodes | ⌊log₂ n⌋ |
| Full BT: Leaves = Internal + 1 | L = I + 1 |
| Edges in tree | n − 1 |
| Handshaking lemma | Σ degree = 2|E| |
| Max edges (undirected) | n(n-1)/2 |
| Max edges (directed) | n(n-1) |
| Circular queue advance | (index + 1) % capacity |
| Geometric series | 1+r+r²+... = 1/(1-r) for |r|<1 |
| Arithmetic series | 1+2+...+n = n(n+1)/2 |
| Sum of squares | 1²+2²+...+n² = n(n+1)(2n+1)/6 |

## All Complexities in One Table

| Data Structure | Access | Search | Insert | Delete |
|---------------|--------|--------|--------|--------|
| **Array** | O(1) | O(n) | O(n) | O(n) |
| **Linked List** | O(n) | O(n) | O(1)* | O(1)* |
| **Stack** | O(n) | O(n) | O(1) | O(1) |
| **Queue** | O(n) | O(n) | O(1) | O(1) |
| **BST (balanced)** | O(log n) | O(log n) | O(log n) | O(log n) |
| **Heap** | O(n) | O(n) | O(log n) | O(log n) |
| **Hash Table** | N/A | O(1) avg | O(1) avg | O(1) avg |

*at known position

## Expression Conversion — Quick Steps

| Conversion | Method |
|------------|--------|
| Infix → Postfix | Shunting-yard: operand→output, operator→stack (pop ≥ precedence), (→push, )→pop until ( |
| Infix → Prefix | Reverse → swap ()↔)( → infix-to-postfix → reverse |
| Postfix eval | L→R: operand push, operator: pop 2 (op2 first!), compute, push |
| Prefix eval | R→L: operand push, operator: pop 2 (op1 first!), compute, push |

## Heap Quick Reference

```
INSERT:     Add at end → Bubble UP (swap with parent while larger)     O(log n)
DELETE-MAX: Swap root↔last → Remove last → Bubble DOWN (heapify)      O(log n)
BUILD-HEAP: Bottom-up heapify from ⌊n/2⌋ to 1                         O(n)
HEAP SORT:  Build max-heap + n extract-max                             O(n log n)
```

## Master Theorem — One Last Look

```
T(n) = aT(n/b) + f(n)     →     Compare f(n) with n^(log_b a)

f(n) SMALLER → Θ(n^(log_b a))        [Case 1 — leaves win]
f(n) EQUAL   → Θ(n^(log_b a) · lg n) [Case 2 — tie, ×log]
f(n) EQUAL × (lg n)^k → Θ(... · (lg n)^(k+1))  [Extended Case 2]
f(n) BIGGER  → Θ(f(n))               [Case 3 — root wins]
```

## TRUE/FALSE Quick Facts (Common Exam Questions)

1. ✅ Build-heap is O(n), NOT O(n log n)
2. ✅ Comparison-based sorting lower bound is Ω(n log n)
3. ✅ BFS finds shortest path in unweighted graphs only
4. ❌ Master Theorem works for T(n) = T(n-1) + 1 — NO, needs division not subtraction
5. ✅ Quick Sort worst case is O(n²) — when already sorted with bad pivot
6. ✅ Merge Sort is stable; Quick Sort and Heap Sort are NOT stable
7. ❌ BST search is always O(log n) — NO, worst case O(n) for skewed tree
8. ✅ DFS back edge = cycle in graph
9. ✅ Floyd-Warshall works with negative weights (but not negative cycles)
10. ✅ Inorder traversal of BST gives sorted output
11. ✅ In a full binary tree, leaves = internal nodes + 1
12. ❌ Heap is a BST — NO, heap only guarantees parent vs children, not left < right ordering
13. ✅ Adjacency list is better for sparse graphs; adjacency matrix for dense
14. ✅ A Complete binary tree is NOT necessarily a Full binary tree
15. ❌ Preorder + Postorder can uniquely reconstruct a binary tree — NO, need inorder
16. ✅ In DFS, a back edge indicates a cycle in a directed graph
17. ✅ little-o(f) means strictly less than f (for ALL constants c, not just some)
18. ✅ Loop invariant must hold: before first iteration, after each iteration, and at termination

---

*You've got this! Focus on the solved examples — exam questions follow the same patterns. 🎯*
