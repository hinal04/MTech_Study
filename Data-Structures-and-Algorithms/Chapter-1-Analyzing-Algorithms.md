# Chapter 1: Analyzing Algorithms

> BITS Pilani — Department of Computer Science and Information Systems

### Prescribed Textbooks

| Code | Book | Authors | Edition |
|------|------|---------|---------|
| **T1** | Algorithm Design: Foundations, Analysis and Internet Examples | Michael T. Goodrich, Roberto Tamassia | 2006, Wiley |
| **R1** | Introduction to Algorithms (CLRS) | T.H. Cormen, C.E. Leiserson, R.L. Rivest, C. Stein | 3rd Ed, 2009, PHI |
| **R2** | Data Structures, Algorithms and Applications in Java | Sartaj Sahni | 2nd Ed, 2005, Universities Press |

### Reading Map

| Syllabus Topic | T1 Reference | R1 Reference |
|----------------|-------------|-------------|
| 1.1 Theoretical Foundation | T1: 1.1, 1.2 | — |
| 1.1.4 Algorithm Correctness | T1: 1.1.4 | — |
| 1.3 Recurrences & Master Theorem | — | R1: 4.3, 4.4, 4.5 |

---

## Table of Contents

- [1.1 Theoretical Foundation](#11-theoretical-foundation)
  - [1.1.1 Algorithms and its Specification](#111-algorithms-and-its-specification)
  - [1.1.2 Random Access Machine Model](#112-random-access-machine-model)
  - [1.1.3 Notion of Best Case, Average Case and Worst Case](#113-notion-of-best-case-average-case-and-worst-case)
  - [1.1.4 Notion of Algorithm Correctness](#114-notion-of-algorithm-correctness)
- [1.2 Characterizing Run Time](#12-characterizing-run-time)
  - [1.2.1 Use of Asymptotic Notation](#121-use-of-asymptotic-notation)
  - [1.2.2 Big-Oh, Omega and Theta Notations](#122-big-oh-omega-and-theta-notations)
- [1.3 Analyzing Recursive Algorithms](#13-analyzing-recursive-algorithms)
  - [1.3.1 Recurrence Relations](#131-recurrence-relations)
  - [1.3.2 Specifying Runtime of Recursive Algorithms](#132-specifying-runtime-of-recursive-algorithms)
  - [1.3.3 Master Theorem](#133-master-theorem)
  - [1.3.4 Solving Recurrences: Substitution Method & Recursion Tree Method](#134-solving-recurrences-substitution-method--recursion-tree-method)
- [Practice Problems with Solutions](#practice-problems-with-solutions)

---

# 1.1 Theoretical Foundation

> **T1 Reference: Chapter 1.1 — Algorithm Analysis, Chapter 1.2 — A Quick Mathematical Review**

## 1.1.1 Algorithms and its Specification

### What is an Algorithm?

An algorithm is a **well-defined computational procedure** that takes some value or set of values as **input** and produces some value or set of values as **output**. It is a sequence of computational steps that transforms the input into the output.

More formally, an algorithm must satisfy these five properties:

| Property | Description | Example |
|----------|-------------|---------|
| **Input** | Zero or more quantities are externally supplied | An array A, an integer n |
| **Output** | At least one quantity is produced | The sorted array, a maximum value |
| **Definiteness** | Each instruction is clear and unambiguous | "Set x to 5" is definite; "Set x to a nice number" is not |
| **Finiteness** | The algorithm terminates after a finite number of steps | A loop must have a valid termination condition |
| **Effectiveness** | Every instruction is basic enough to be carried out by a person using pencil and paper | Arithmetic operations, comparisons |

### Experimental Studies vs Theoretical Analysis (T1: 1.1)

Goodrich & Tamassia emphasize two approaches to studying algorithm performance:

#### Approach 1: Experimental Studies

You can study an algorithm's running time by implementing it and running it on various test inputs, recording the actual time spent.

**Methodology:**
1. Write a program implementing the algorithm
2. Run the program with inputs of varying size and composition
3. Use a system clock to get an accurate measure of actual running time
4. Plot the results (Input Size vs Time)

**Limitations of Experimental Studies:**
- You **must implement** the algorithm first, which may be difficult or time-consuming
- Results may **not be indicative** of running time on other inputs not included in the experiment
- To compare two algorithms, the **same hardware and software environment** must be used
- The experiment is **not portable** across machines

#### Approach 2: Theoretical Analysis (Preferred)

Uses a **high-level description** of the algorithm instead of an implementation.

**Advantages over experimental analysis:**
- Takes into account **all possible inputs** (not just a sample)
- Allows evaluation of speed **independent of hardware/software**
- Can study an algorithm without implementing it first
- Characterizes running time as a **function of input size n**

### Pseudocode (T1: 1.1)

Pseudocode is a high-level description language that is more structured than English prose but less detailed than a programming language.

**Pseudocode conventions used in the textbooks:**

```
Algorithm name(param1, param2, ...)
    Input:  Description of input
    Output: Description of output

    // Control flow
    if condition then
        ...
    else
        ...

    // Loops
    for variable ← start to end do
        ...

    while condition do
        ...

    repeat
        ...
    until condition

    // Method calls
    result ← MethodName(args)

    // Return
    return expression

    // Array indexing
    A[i]

    // Assignment
    x ← expression
```

### Counting Primitive Operations (T1: 1.1)

A **primitive operation** corresponds to a low-level instruction with a constant execution time. Instead of trying to determine the exact execution time, we count how many primitive operations are performed.

**What counts as a primitive operation:**
- Assigning a value to a variable
- Following an object reference
- Performing an arithmetic operation (+, -, *, /)
- Comparing two numbers
- Indexing into an array
- Calling a method
- Returning from a method

#### Detailed Example: arrayMax

```
Algorithm arrayMax(A, n)
    Input:  An array A storing n ≥ 1 integers
    Output: The maximum element in A

1.  currentMax ← A[0]              // 2 ops (index + assign)
2.  for i ← 1 to n-1 do           // 1 + n ops (assign + n comparisons i<=n-1)
3.      if A[i] > currentMax then  // 2(n-1) ops (index + compare, runs n-1 times)
4.          currentMax ← A[i]     // 2(n-1) ops worst case (index + assign)
5.  return currentMax              // 1 op
```

**Counting (worst case — when array is sorted in ascending order):**

| Line | Operation | Count |
|------|-----------|-------|
| 1 | Index + Assignment | 2 |
| 2 | Initializing i, incrementing i, comparing i | 1 + (n-1) + (n-1) = 2n - 1 |
| 3 | Array index + comparison | 2(n-1) |
| 4 | Array index + assignment (worst case: every time) | 2(n-1) |
| 5 | Return | 1 |
| **Total** | | **6n - 2** |

**Best case** (maximum is at A[0]): Line 4 never executes → **4n** operations

**Key insight:** The exact count doesn't matter much. What matters is that the running time is some **linear function of n**. Whether it is 6n - 2 or 4n, it grows linearly. This motivates asymptotic notation.

### Seven Important Functions (T1: 1.2)

Goodrich & Tamassia identify seven functions that appear repeatedly in algorithm analysis:

| Function | Name | Growth | Example Algorithm |
|----------|------|--------|-------------------|
| **1** | Constant | Does not grow | Hash table lookup, array access by index |
| **log n** | Logarithmic | Grows very slowly | Binary search |
| **n** | Linear | Grows proportionally | Linear search, finding max |
| **n log n** | Linearithmic | Slightly superlinear | Merge sort, heap sort |
| **n²** | Quadratic | Grows fast | Insertion sort, bubble sort, nested loops |
| **n³** | Cubic | Grows very fast | Naive matrix multiplication |
| **2ⁿ** | Exponential | Grows astronomically | Subset enumeration, brute-force SAT |

**Concrete comparison for n = 256:**

```
log n   = 8
n       = 256
n log n = 2,048
n²      = 65,536
n³      = 16,777,216
2ⁿ      = a number with 77 digits (larger than atoms in the universe!)
```

**The Ceiling and Floor Functions:**
- ⌊x⌋ (floor) = largest integer ≤ x. Example: ⌊3.7⌋ = 3, ⌊-1.2⌋ = -2
- ⌈x⌉ (ceiling) = smallest integer ≥ x. Example: ⌈3.2⌉ = 4, ⌈-1.2⌉ = -1

**Logarithm rules (essential for analysis):**
```
log_b(xy) = log_b(x) + log_b(y)
log_b(x/y) = log_b(x) - log_b(y)
log_b(x^a) = a · log_b(x)
log_b(a) = log_c(a) / log_c(b)          (change of base)
b^(log_b(x)) = x
```

**Summation formulas (essential for analysis):**
```
Σ(i=1 to n) i = n(n+1)/2                              — Arithmetic series
Σ(i=1 to n) i² = n(n+1)(2n+1)/6                       — Sum of squares
Σ(i=0 to n) aⁱ = (a^(n+1) - 1) / (a - 1)  for a ≠ 1  — Geometric series
Σ(i=0 to ∞) aⁱ = 1 / (1 - a)              for |a| < 1 — Infinite geometric series
Σ(i=1 to n) 1/i ≈ ln n + 0.5772...                    — Harmonic series
```

---

## 1.1.2 Random Access Machine (RAM) Model

### The Model

The RAM model is the **standard theoretical model of computation** used in algorithm analysis. It provides a clean abstraction so we can reason about algorithms without worrying about the specifics of any particular computer.

### Formal Assumptions

**1. Single Processor, Sequential Execution**
- Instructions are executed one after another
- No concurrent or parallel operations
- Each instruction takes a constant amount of time

**2. Standard Instruction Set**
Each of the following takes **exactly 1 time unit (constant time)**:
- **Arithmetic:** add, subtract, multiply, divide, modulo, floor, ceiling
- **Data movement:** load, store, copy
- **Control:** conditional branch, unconditional branch, subroutine call, return
- **Comparison:** less than, greater than, equal to, etc.

**3. Flat Memory Model**
- Unlimited memory is available
- Each memory access takes **constant time** — no distinction between cache, main memory, or disk
- Memory cells can hold arbitrarily large integers (unit-cost model) OR hold values up to some fixed word size w (log-cost model)

**4. Fixed Word Size**
- In practice, we assume the word size is w = c · log n bits, where n is the input size
- This ensures that a single word can hold an index into the input
- This is a realistic assumption for most algorithms

### Unit-Cost Model vs Log-Cost Model

| Aspect | Unit-Cost Model | Log-Cost Model |
|--------|-----------------|----------------|
| Arithmetic cost | O(1) per operation | O(log of operand size) per operation |
| When to use | Most algorithm analysis | When operands can be very large (e.g., cryptography) |
| Assumption | Numbers fit in a word | Numbers can be arbitrarily large |

In this course, we use the **unit-cost model** unless otherwise stated.

### What the RAM Model Ignores

The RAM model deliberately ignores several real-world factors:

1. **Memory hierarchy** — In reality, L1 cache access (~1 ns) is 100x faster than main memory (~100 ns), which is ~100,000x faster than disk (~10 ms)
2. **Pipelining and out-of-order execution** in modern CPUs
3. **Compiler optimizations** that can dramatically change instruction counts
4. **Virtual memory and paging** effects
5. **Multi-core parallelism**

**Why is it still useful?** Despite these simplifications, the RAM model predictions are usually **excellent predictors** of actual algorithm performance. An O(n log n) algorithm almost always beats an O(n²) algorithm in practice for large inputs, even though the RAM model ignores cache effects.

---

## 1.1.3 Notion of Best Case, Average Case and Worst Case

### Why Different Cases?

For the **same algorithm** with the **same input size n**, the running time can vary dramatically depending on the **specific input instance**. Consider searching for a value `key` in an unsorted array of n elements:

- If `key` is at position 0 → 1 comparison
- If `key` is at position n-1 → n comparisons
- If `key` is not in the array → n comparisons

We characterize this variability using three measures.

### Best Case: T_best(n)

**Definition:** The **minimum** running time over **all possible inputs** of size n.

```
T_best(n) = min { T(I) : |I| = n }
```

**Characteristics:**
- Represents the most favorable input configuration
- Useful to know but **rarely informative** for real-world performance
- Can be misleadingly optimistic
- Sometimes used to prove a lower bound on any algorithm for a problem

**Example — Insertion Sort:**
```
InsertionSort(A, n)
    for j ← 1 to n-1 do
        key ← A[j]
        i ← j - 1
        while i ≥ 0 and A[i] > key do    ← this loop may run 0 to j times
            A[i+1] ← A[i]
            i ← i - 1
        A[i+1] ← key
```

Best case: Input is **already sorted**. The while-loop body never executes (A[i] > key is always false on the first check). The running time is:
```
T_best(n) = c₁(n-1) + c₂(n-1) + c₃(n-1) + c₄(n-1) + c₇(n-1) = Θ(n)
```

### Worst Case: T_worst(n)

**Definition:** The **maximum** running time over **all possible inputs** of size n.

```
T_worst(n) = max { T(I) : |I| = n }
```

**Characteristics:**
- Gives an **upper bound guarantee**: "No matter what input you give me, the algorithm will never take longer than this."
- **Most commonly used** measure in practice and in this course
- Easier to analyze than average case (no probability assumptions needed)
- For many algorithms, the worst case occurs fairly often (e.g., in database queries, searching for absent items)

**Example — Insertion Sort:**
Worst case: Input is **sorted in reverse order**. The while-loop runs the maximum number of times — for each j, it runs j times.
```
Total inner loop iterations = 1 + 2 + 3 + ... + (n-1) = n(n-1)/2

T_worst(n) = Θ(n²)
```

### Average Case: T_avg(n)

**Definition:** The **expected** running time averaged over **all possible inputs** of size n, assuming a probability distribution over inputs.

```
T_avg(n) = Σ [P(I) · T(I)]    for all inputs I of size n
```

**Characteristics:**
- Requires knowledge of the **probability distribution** of inputs
- More realistic prediction of real-world performance
- **Harder to compute** — requires probabilistic analysis
- Often assumes **uniform distribution** (all inputs equally likely)

**Example — Linear Search (Successful):**
Assume the element is in the array and is equally likely to be at any position.

```
Probability of being at position i = 1/n    for i = 0, 1, ..., n-1
Number of comparisons if at position i = i + 1

T_avg(n) = Σ (1/n)(i+1) for i = 0 to n-1
         = (1/n) · Σ (i+1) for i = 0 to n-1
         = (1/n) · n(n+1)/2
         = (n+1)/2
         = Θ(n)
```

**Example — Insertion Sort (Average Case):**
On average, each element A[j] is greater than about half the elements in A[0..j-1]. So the while loop runs about j/2 times for each j:
```
Total ≈ Σ j/2 for j = 1 to n-1 = (1/2) · n(n-1)/2 = n(n-1)/4 = Θ(n²)
```

### Summary Comparison

| Measure | Definition | Usefulness | Computation Difficulty |
|---------|-----------|------------|----------------------|
| Best case T_best(n) | min over all inputs | Lower bound, rarely practical | Easy |
| Worst case T_worst(n) | max over all inputs | Guarantee, most commonly used | Moderate |
| Average case T_avg(n) | expected over distribution | Realistic prediction | Hard |

### Which Case to Use When?

| Situation | Recommended Case |
|-----------|-----------------|
| Safety-critical systems | **Worst case** — must guarantee deadlines |
| General purpose software | **Worst case** or **Average case** |
| Comparing algorithms theoretically | **Worst case** — simplest, fairest comparison |
| Predicting real-world performance | **Average case** — if you know the distribution |
| Proving an algorithm is fast | **Worst case** — strongest claim |
| Proving a problem is hard | **Best case** — "even the best input takes this long" |

---

## 1.1.4 Notion of Algorithm Correctness

> **T1 Reference: Section 1.1.4**

### What Does "Correct" Mean?

An algorithm is **correct** if, for every valid input instance, it **halts** (terminates) with the **correct output**.

An **incorrect** algorithm might:
1. Not halt at all for some inputs (infinite loop)
2. Halt but produce the wrong output
3. Halt with the right output only for some inputs

### Formal Specification: Preconditions and Postconditions

To prove correctness, we first need to **specify** what the algorithm should do:

- **Precondition:** What must be true **before** the algorithm executes. Describes valid inputs.
- **Postcondition:** What must be true **after** the algorithm executes. Describes the desired output.

**Example — Binary Search:**
```
Algorithm BinarySearch(A, n, key)
    Precondition:  A is a sorted array of n elements, n ≥ 1
    Postcondition: Returns index i such that A[i] = key, or -1 if key ∉ A
```

### Partial Correctness vs Total Correctness

| Type | Definition |
|------|-----------|
| **Partial correctness** | IF the algorithm terminates, THEN the output satisfies the postcondition |
| **Total correctness** | The algorithm ALWAYS terminates AND the output satisfies the postcondition |

```
Total Correctness = Partial Correctness + Termination
```

Proving termination usually involves showing that some **measure decreases** with each step (e.g., a loop counter approaches its bound, or a recursion parameter gets smaller).

### Proving Correctness with Loop Invariants

A **loop invariant** is a property (a logical assertion) that is:

1. **Initialization (Base Case):** True **before** the first iteration of the loop
2. **Maintenance (Inductive Step):** IF it is true before an iteration, it is STILL true after that iteration
3. **Termination:** When the loop terminates, the invariant, combined with the loop's termination condition, gives us a useful property that helps show the algorithm is correct

This is directly analogous to **mathematical induction**:
- Initialization ↔ Base case
- Maintenance ↔ Inductive step
- Termination ↔ We extract the final result

### Detailed Example: Proving Insertion Sort Correct

```
InsertionSort(A, n)
    for j ← 1 to n-1 do           // outer loop
        key ← A[j]
        // Insert A[j] into the sorted sequence A[0..j-1]
        i ← j - 1
        while i ≥ 0 and A[i] > key do
            A[i+1] ← A[i]
            i ← i - 1
        A[i+1] ← key
```

**Loop Invariant:** At the start of each iteration of the **for** loop with index j, the subarray A[0..j-1] consists of the elements originally in A[0..j-1], but in **sorted order**.

**Proof:**

**Initialization (j = 1):**
Before the first iteration, j = 1. The subarray A[0..0] consists of just one element, A[0]. A single element is trivially sorted. ✓

**Maintenance:**
Assume the invariant holds at the start of iteration j: A[0..j-1] is sorted.

The body of the for loop works as follows:
- `key ← A[j]` saves the element to be inserted
- The while loop shifts elements A[j-1], A[j-2], ... one position to the right, stopping when it finds the correct position for key (i.e., when A[i] ≤ key or i < 0)
- `A[i+1] ← key` places key in its correct position

After this operation, A[0..j] contains exactly the same elements as before, but now in sorted order. The invariant holds for j+1. ✓

**Termination (j = n):**
The loop terminates when j = n. By the invariant, A[0..n-1] is sorted. Since A[0..n-1] is the entire array, the algorithm has correctly sorted the array. ✓

### Example: Proving Linear Search Correct

```
LinearSearch(A, n, key)
    for i ← 0 to n-1 do
        if A[i] = key then
            return i
    return -1
```

**Loop Invariant:** At the start of each iteration with index i, the value `key` does not appear in A[0..i-1].

**Initialization (i = 0):** A[0..-1] is empty, so key trivially doesn't appear in it. ✓

**Maintenance:** If we enter iteration i, we did NOT return in any previous iteration. This means A[0], A[1], ..., A[i-1] are all different from key. If A[i] = key, we return i (correct!). If A[i] ≠ key, then key does not appear in A[0..i], maintaining the invariant for i+1. ✓

**Termination:** If the loop completes (i = n) without returning, the invariant tells us key does not appear in A[0..n-1], the entire array. So returning -1 is correct. ✓

### Proving Termination

For iterative algorithms: Show the loop variable **progresses** toward the termination condition.
- `for i ← 0 to n-1`: i increases by 1 each iteration, must reach n. ✓
- `while i ≥ 0`: i decreases by 1 each iteration, must reach -1. ✓

For recursive algorithms: Show the argument **decreases** with each recursive call and has a base case.
- `Factorial(n)` calls `Factorial(n-1)`: n decreases by 1, base case at n = 0. ✓
- `BinarySearch(A, lo, hi, key)`: the range `hi - lo` decreases by at least half each call. ✓

### Proving Correctness of Recursive Algorithms

For recursive algorithms, use **strong induction** on the input size:

**Example: MergeSort**
```
MergeSort(A, lo, hi)
    if lo < hi then
        mid ← ⌊(lo + hi) / 2⌋
        MergeSort(A, lo, mid)
        MergeSort(A, mid+1, hi)
        Merge(A, lo, mid, hi)
```

**Claim:** MergeSort correctly sorts A[lo..hi].

**Proof by strong induction on n = hi - lo + 1:**

**Base case (n = 1):** lo = hi, so A[lo..hi] has one element. It's already sorted. The algorithm returns immediately. ✓

**Inductive hypothesis:** Assume MergeSort correctly sorts any subarray of size < n.

**Inductive step:** For size n > 1:
- mid = ⌊(lo + hi) / 2⌋, so A[lo..mid] has size ⌊n/2⌋ < n and A[mid+1..hi] has size ⌈n/2⌉ < n
- By the inductive hypothesis, MergeSort(A, lo, mid) correctly sorts A[lo..mid]
- By the inductive hypothesis, MergeSort(A, mid+1, hi) correctly sorts A[mid+1..hi]
- Merge correctly combines two sorted subarrays into one sorted array (can prove separately)
- Therefore A[lo..hi] is sorted ✓

---

# 1.2 Characterizing Run Time

> **T1 Reference: Chapter 1.1 (Asymptotic Analysis section), Chapter 1.2**

## 1.2.1 Use of Asymptotic Notation

### Why Asymptotic Notation?

From our primitive operation counting of arrayMax, we got T(n) = 6n - 2. But this exact count:
- Is tedious to compute
- Depends on our counting conventions
- Includes irrelevant constants that vary by machine
- Has lower-order terms that are negligible for large n

We want to **focus on the growth rate** and ignore constant factors and lower-order terms.

**The key insight:** For large inputs, the growth rate of the running time is what determines whether an algorithm is practical.

```
T(n) = 6n - 2       →  "grows like n"     →  Θ(n)
T(n) = 3n² + 5n + 7 →  "grows like n²"    →  Θ(n²)
T(n) = 2ⁿ + n³      →  "grows like 2ⁿ"    →  Θ(2ⁿ)
```

### How to Determine Growth Rate

**Step 1:** Identify the **dominant term** (the term that grows fastest as n → ∞)

**Step 2:** Drop the **constant coefficient** of that term

**Step 3:** Drop all **lower-order terms**

| Exact Count | Dominant Term | Drop Coefficient | Growth Rate |
|-------------|---------------|------------------|-------------|
| 5n + 3 | 5n | n | Θ(n) |
| 2n² + 3n + 1 | 2n² | n² | Θ(n²) |
| n³ + 100n² + 50 | n³ | n³ | Θ(n³) |
| n log n + n | n log n | n log n | Θ(n log n) |
| 3 · 2ⁿ + n¹⁰ | 3 · 2ⁿ | 2ⁿ | Θ(2ⁿ) |

### Why Constants Don't Matter (Asymptotically)

Suppose Algorithm A takes 100n steps and Algorithm B takes 2n² steps.

| n | Algorithm A (100n) | Algorithm B (2n²) | Faster? |
|---|-------------------|-------------------|---------|
| 10 | 1,000 | 200 | B wins |
| 50 | 5,000 | 5,000 | Tie |
| 100 | 10,000 | 20,000 | **A wins** |
| 1,000 | 100,000 | 2,000,000 | **A wins by 20x** |
| 10,000 | 1,000,000 | 200,000,000 | **A wins by 200x** |

Algorithm A (O(n)) will **always** eventually beat Algorithm B (O(n²)), no matter how large the constants are. For n > 50, A is faster and the gap keeps growing.

### Growth Rate Hierarchy

The following ordering holds for sufficiently large n:

```
1 ≺ log log n ≺ log n ≺ √n ≺ n ≺ n log n ≺ n² ≺ n³ ≺ ... ≺ 2ⁿ ≺ n! ≺ nⁿ
```

Where f ≺ g means f(n) grows strictly slower than g(n).

**Practical feasibility thresholds** (assuming 10⁹ operations/second):

| Growth Rate | n = 10 | n = 100 | n = 10⁶ | n = 10⁹ | Max feasible n (in 1 second) |
|-------------|--------|---------|---------|---------|-------------------------------|
| O(1) | instant | instant | instant | instant | unlimited |
| O(log n) | instant | instant | instant | instant | unlimited |
| O(n) | instant | instant | 1 ms | 1 s | 10⁹ |
| O(n log n) | instant | instant | 20 ms | 30 s | ~4 × 10⁷ |
| O(n²) | instant | instant | 17 min | 31 years | ~31,623 |
| O(n³) | instant | 1 ms | 31 years | never | ~1,000 |
| O(2ⁿ) | instant | 4 × 10¹³ years | never | never | ~30 |

---

## 1.2.2 Big-Oh, Omega and Theta Notations

> **T1 Reference: Chapter 1.2**

These are the formal tools for comparing growth rates. Think of them as ≤, ≥, and = for functions.

### Big-Oh: O(g(n)) — Upper Bound

**Formal Definition:**

```
f(n) = O(g(n)) if there exist positive constants c and n₀ such that:
    0 ≤ f(n) ≤ c · g(n)    for all n ≥ n₀
```

**In words:** f(n) grows **no faster than** g(n), up to a constant factor, for sufficiently large n.

**Analogy:** O is like "≤" for growth rates.

**Graphical Interpretation:**
```
    ^
    |     c·g(n)
    |    /
    |   / ← f(n) is below c·g(n) from n₀ onwards
    |  /
    | /   f(n)
    |/ /
    +----+-------->
        n₀         n
```

**Example 1:** Show that 3n² + 5n + 2 = O(n²)

We need c and n₀ such that 3n² + 5n + 2 ≤ cn² for all n ≥ n₀.

```
3n² + 5n + 2 ≤ 3n² + 5n² + 2n²     (since n ≤ n² and 2 ≤ 2n² for n ≥ 1)
             = 10n²

So choose c = 10, n₀ = 1.   ✓
```

Alternatively: 3n² + 5n + 2 ≤ 4n² for all n ≥ 6. (choose c = 4, n₀ = 6) ✓

**Example 2:** Show that n = O(n²)

n ≤ 1 · n² for all n ≥ 1. Choose c = 1, n₀ = 1. ✓

**Example 3:** Show that n³ ≠ O(n²)

Suppose n³ ≤ cn² for all n ≥ n₀. Then n ≤ c for all n ≥ n₀. But n grows without bound, so no constant c can work. Contradiction. ✓

### Big-Oh: Common Mistakes

**Big-Oh gives an UPPER bound, not a tight bound:**
- n = O(n²) is TRUE (but not tight)
- n = O(n³) is also TRUE
- n = O(2ⁿ) is also TRUE
- The tightest meaningful statement is n = O(n)

**Big-Oh is NOT symmetric:**
- n = O(n²) but n² ≠ O(n)

### Big-Omega: Ω(g(n)) — Lower Bound

**Formal Definition:**

```
f(n) = Ω(g(n)) if there exist positive constants c and n₀ such that:
    0 ≤ c · g(n) ≤ f(n)    for all n ≥ n₀
```

**In words:** f(n) grows **at least as fast as** g(n).

**Analogy:** Ω is like "≥" for growth rates.

**Example:** Show that 3n² + 5n + 2 = Ω(n²)

3n² + 5n + 2 ≥ 3n² ≥ 3 · n² for all n ≥ 1. Choose c = 3, n₀ = 1. ✓

**Example:** Show that n² = Ω(n)

n² ≥ 1 · n for all n ≥ 1. Choose c = 1, n₀ = 1. ✓

### Big-Theta: Θ(g(n)) — Tight Bound

**Formal Definition:**

```
f(n) = Θ(g(n)) if there exist positive constants c₁, c₂, and n₀ such that:
    0 ≤ c₁ · g(n) ≤ f(n) ≤ c₂ · g(n)    for all n ≥ n₀
```

**In words:** f(n) grows at the **same rate** as g(n).

**Analogy:** Θ is like "=" for growth rates.

**Equivalent definition:** f(n) = Θ(g(n)) if and only if f(n) = O(g(n)) AND f(n) = Ω(g(n)).

**Example:** Show that 3n² + 5n + 2 = Θ(n²)

- Upper bound: 3n² + 5n + 2 ≤ 10n² for n ≥ 1 → O(n²) with c₂ = 10
- Lower bound: 3n² + 5n + 2 ≥ 3n² for n ≥ 1 → Ω(n²) with c₁ = 3
- Therefore Θ(n²) with c₁ = 3, c₂ = 10, n₀ = 1. ✓

### Little-oh and Little-omega

| Notation | Formal Definition | Intuition | Analogy |
|----------|------------------|-----------|---------|
| f(n) = o(g(n)) | lim(n→∞) f(n)/g(n) = 0 | f grows **strictly slower** than g | < |
| f(n) = ω(g(n)) | lim(n→∞) f(n)/g(n) = ∞ | f grows **strictly faster** than g | > |

**Key difference from Big-Oh / Big-Omega:**
- O allows the possibility of equal growth rate; o does not
- n² = O(n²) is true, but n² = o(n²) is **false**
- n = o(n²) is true because lim n/n² = lim 1/n = 0

### Summary Table: All Five Notations

| Notation | Analogy | Meaning | Example |
|----------|---------|---------|---------|
| f = O(g) | f ≤ g | f grows no faster than g | 3n = O(n²) |
| f = Ω(g) | f ≥ g | f grows at least as fast as g | n² = Ω(n) |
| f = Θ(g) | f = g | f grows at the same rate as g | 3n² + n = Θ(n²) |
| f = o(g) | f < g | f grows strictly slower than g | n = o(n²) |
| f = ω(g) | f > g | f grows strictly faster than g | n² = ω(n) |

### Important Properties

**1. Transitivity** (holds for all five notations):
```
If f(n) = O(g(n)) and g(n) = O(h(n)), then f(n) = O(h(n))
```

**2. Reflexivity** (holds for O, Ω, Θ):
```
f(n) = O(f(n)),  f(n) = Ω(f(n)),  f(n) = Θ(f(n))
```

**3. Symmetry** (holds only for Θ):
```
f(n) = Θ(g(n))  if and only if  g(n) = Θ(f(n))
```

**4. Transpose symmetry:**
```
f(n) = O(g(n))  if and only if  g(n) = Ω(f(n))
f(n) = o(g(n))  if and only if  g(n) = ω(f(n))
```

**5. Sum rule:**
```
O(f(n) + g(n)) = O(max(f(n), g(n)))
```

**6. Product rule:**
```
If f₁ = O(g₁) and f₂ = O(g₂), then f₁ · f₂ = O(g₁ · g₂)
```

### Using Limits to Determine Asymptotic Relationship

A powerful technique: compute the limit of f(n)/g(n) as n → ∞.

```
             ┌ 0        ⟹  f(n) = o(g(n))   AND  f(n) = O(g(n))
             │
lim f(n)     │ c > 0    ⟹  f(n) = Θ(g(n))
n→∞ ──── =  │
    g(n)     │ ∞        ⟹  f(n) = ω(g(n))   AND  f(n) = Ω(g(n))
             └
```

**Example:** Compare n² and n log n.
```
lim (n²) / (n log n) = lim n / log n = ∞
```
Therefore n² = ω(n log n), meaning n² grows strictly faster.

**Example:** Compare log n and √n.
```
lim (log n) / (√n) = lim (by L'Hôpital) (1/n) / (1/(2√n)) = lim 2√n / n = lim 2/√n = 0
```
Therefore log n = o(√n), meaning log n grows strictly slower than √n.

### Analyzing Code with Asymptotic Notation

**Rule 1: Sequential Composition (Sum Rule)**
```
Statement_1     // O(f(n))
Statement_2     // O(g(n))
→ Total: O(f(n) + g(n)) = O(max(f(n), g(n)))
```

**Rule 2: Conditional**
```
if condition then    // O(1)
    Block_1          // O(f(n))
else
    Block_2          // O(g(n))
→ Total: O(max(f(n), g(n)))   (worst case)
```

**Rule 3: Simple Loop**
```
for i ← 1 to n do
    Body             // O(f(n))
→ Total: O(n · f(n))
```

**Rule 4: Nested Loops**
```
for i ← 1 to n do
    for j ← 1 to n do
        Body         // O(1)
→ Total: O(n²)
```

**Rule 5: Nested Loop with Dependent Bound**
```
for i ← 1 to n do
    for j ← 1 to i do
        Body         // O(1)
→ Total = 1 + 2 + 3 + ... + n = n(n+1)/2 = O(n²)
```

**Rule 6: Loop with Halving**
```
i ← n
while i ≥ 1 do
    Body             // O(1)
    i ← i / 2
→ Total: O(log n)   (because i takes values n, n/2, n/4, ..., 1 — that's log₂n iterations)
```

**Rule 7: Nested Loop — Linear × Logarithmic**
```
for i ← 1 to n do
    j ← i
    while j ≥ 1 do
        Body         // O(1)
        j ← j / 2
→ Inner loop runs O(log i) times.
→ Total = Σ log(i) for i=1 to n = log(n!) = Θ(n log n)    (by Stirling's approximation)
```

---

# 1.3 Analyzing Recursive Algorithms

> **R1 Reference: Chapter 4 — Divide and Conquer**
> **R1: 4.3 (Substitution Method), 4.4 (Recursion Tree), 4.5 (Master Method)**

---

## Understanding Time Complexity and Recurrences — From Scratch

*Before diving into the formal methods, let's build intuition for how time complexity actually works using simple, traceable examples.*

### The Core Idea: Counting How Many Times the "Main Work" Happens

Time complexity answers one question: **as your input n grows, how does the number of operations grow?**

#### Simple Loops First

```python
# Example 1: Single loop
for i in range(n):
    print(i)         # This line runs n times
```
The print runs **n times**. So T(n) = n = **O(n)**.

```python
# Example 2: Nested loop
for i in range(n):
    for j in range(n):
        print(i, j)   # This runs n × n times
```
The print runs **n × n = n² times**. So T(n) = n² = **O(n²)**.

```python
# Example 3: Loop that halves
i = n
while i >= 1:
    print(i)
    i = i // 2        # n → n/2 → n/4 → n/8 → ... → 1
```
How many times does this run? i takes values: n, n/2, n/4, ..., 1. That's **log₂ n steps**. So T(n) = **O(log n)**.

---

### What is a Recurrence? (The Simple Version)

A recurrence is just **describing how much work a recursive function does, in terms of smaller versions of itself**.

Think of it as a recipe:
```
Total work for input n = Work I do right now + Work my recursive call does
```

---

### Traced Example 1: Factorial — T(n) = T(n-1) + 1

```python
def factorial(n):
    if n == 0:
        return 1                       # base case: constant work
    return n * factorial(n - 1)        # 1 recursive call on n-1, plus 1 multiply
```

**Tracing factorial(5):**
```
factorial(5) → does 1 multiply + calls factorial(4)
factorial(4) → does 1 multiply + calls factorial(3)
factorial(3) → does 1 multiply + calls factorial(2)
factorial(2) → does 1 multiply + calls factorial(1)
factorial(1) → does 1 multiply + calls factorial(0)
factorial(0) → returns 1 (done!)

Total: 5 multiplications. For input n: n multiplications = O(n)
```

**How to write this as a recurrence:**
```
T(n) = T(n-1) + 1
       ↑         ↑
       │         One multiplication (the work at THIS level)
       │
       The cost of solving the smaller problem factorial(n-1)

T(0) = 1    ← base case
```

**Solving by "unrolling" (just substitute repeatedly):**
```
T(n) = T(n-1) + 1
     = [T(n-2) + 1] + 1         ← replace T(n-1) with its definition
     = T(n-2) + 2
     = [T(n-3) + 1] + 2         ← replace T(n-2)
     = T(n-3) + 3
     ...pattern: after k steps...
     = T(n-k) + k

When does it stop? When n-k = 0, i.e., k = n.

T(n) = T(0) + n = 1 + n = O(n)  ✓
```

---

### Traced Example 2: Binary Search — T(n) = T(n/2) + 1

```python
def binary_search(A, lo, hi, key):
    if lo > hi:
        return -1
    mid = (lo + hi) // 2
    if A[mid] == key:
        return mid
    elif key < A[mid]:
        return binary_search(A, lo, mid-1, key)    # LEFT half only
    else:
        return binary_search(A, mid+1, hi, key)    # RIGHT half only
```

**What happens on an array of 16 elements?**
```
Call 1: search 16 elements → do 1 comparison → recurse on 8 elements
Call 2: search 8 elements  → do 1 comparison → recurse on 4 elements
Call 3: search 4 elements  → do 1 comparison → recurse on 2 elements
Call 4: search 2 elements  → do 1 comparison → recurse on 1 element
Call 5: search 1 element   → do 1 comparison → done!

Total: 5 comparisons for n = 16.
Notice: 16 → 8 → 4 → 2 → 1 = log₂ 16 = 4 halvings + 1.
```

**The recurrence:**
```
T(n) = T(n/2) + 1
       ↑         ↑
       │         One comparison (work at THIS level)
       │
       Only ONE recursive call, on HALF the data

T(1) = 1
```

**Unrolling:**
```
T(n) = T(n/2) + 1
     = [T(n/4) + 1] + 1 = T(n/4) + 2
     = [T(n/8) + 1] + 2 = T(n/8) + 3
     ...after k steps...
     = T(n/2^k) + k

Stops when n/2^k = 1  →  k = log₂ n

T(n) = T(1) + log₂ n = 1 + log n = O(log n)  ✓
```

---

### Traced Example 3: Merge Sort — T(n) = 2T(n/2) + n

This is the one that confuses most people. Let's trace it very carefully.

```python
def merge_sort(A):
    if len(A) <= 1:
        return A
    mid = len(A) // 2
    left = merge_sort(A[:mid])      # Sort first half
    right = merge_sort(A[mid:])     # Sort second half
    return merge(left, right)        # Merge them: takes n steps
```

**What happens with [5, 3, 8, 1, 2, 7, 4, 6]? (n = 8)**

```
                    merge_sort([5,3,8,1,2,7,4,6])        ← merge costs 8
                   /                                \
      merge_sort([5,3,8,1])              merge_sort([2,7,4,6])   ← 4+4 = 8
         /            \                     /            \
  merge_sort([5,3])  merge_sort([8,1])  merge_sort([2,7])  merge_sort([4,6])  ← 2+2+2+2 = 8
    /     \           /     \            /     \           /     \
  [5]    [3]        [8]    [1]         [2]    [7]        [4]    [6]   ← base cases
```

**Count the work at each level:**
```
Level 0 (top):     1 merge of 8 items  = 8 work
Level 1:           2 merges of 4 items = 8 work
Level 2:           4 merges of 2 items = 8 work
Level 3 (bottom):  8 base cases        = 8 work
                                         ──────
                         Total: 8 × 4 levels = 32
```

**Every level does n work. There are log₂ n + 1 levels.**

For n = 8: 8 × (log₂ 8 + 1) = 8 × 4 = 32 = **n × (log n + 1) = O(n log n)**.

**The recurrence:**
```
T(n) = 2T(n/2) + n
       ↑          ↑
       │          n work for merging (at THIS level)
       │
       TWO recursive calls, each on HALF the data

T(1) = 1
```

**Unrolling:**
```
T(n) = 2T(n/2) + n

Replace T(n/2) = 2T(n/4) + n/2:
T(n) = 2[2T(n/4) + n/2] + n
     = 4T(n/4) + n + n
     = 4T(n/4) + 2n

Replace T(n/4) = 2T(n/8) + n/4:
T(n) = 4[2T(n/8) + n/4] + 2n
     = 8T(n/8) + n + 2n
     = 8T(n/8) + 3n

Pattern after k steps:
T(n) = 2^k · T(n/2^k) + k·n

Stops when n/2^k = 1  →  k = log₂ n

T(n) = n · T(1) + n · log n = n + n log n = O(n log n)  ✓
```

---

### How to Read Any Recurrence — Cheat Sheet

Every recurrence tells you three things:

```
T(n) = [how many recursive calls] × T([size of each call]) + [work at this level]
```

| You see this | It means | Result |
|---|---|---|
| T(n) = T(n-1) + 1 | 1 call, size shrinks by 1, O(1) work | **O(n)** — does 1 work, n times |
| T(n) = T(n-1) + n | 1 call, size shrinks by 1, O(n) work | **O(n²)** — does n+(n-1)+...+1 |
| T(n) = T(n/2) + 1 | 1 call, size halves, O(1) work | **O(log n)** — halves log n times |
| T(n) = T(n/2) + n | 1 call, size halves, O(n) work | **O(n)** — n + n/2 + n/4 +... ≈ 2n |
| T(n) = 2T(n/2) + 1 | 2 calls, size halves, O(1) work | **O(n)** — tree has n leaves |
| T(n) = 2T(n/2) + n | 2 calls, size halves, O(n) work | **O(n log n)** — n work × log n levels |
| T(n) = 2T(n-1) + 1 | 2 calls, size shrinks by 1 | **O(2ⁿ)** — doubles every level! |

**The intuition:**
- **Halving (n/2)** = logarithmic depth → usually fast
- **Subtracting (n-1)** = linear depth → can be slow
- **Number of calls** determines if work grows, stays same, or shrinks per level

---

### Master Theorem — Just Pattern Matching

For T(n) = aT(n/b) + nᵈ, compute **log_b(a)** and compare with **d**:

```
Step 1: What are a, b, d?
   a = number of recursive calls
   b = how much size shrinks (the divisor)
   d = exponent of the non-recursive work (if work is n², then d=2)

Step 2: Compute log_b(a)

Step 3: Compare log_b(a) with d:
   • log_b(a) > d  →  T(n) = O(n^(log_b a))  "recursion wins"
   • log_b(a) = d  →  T(n) = O(n^d · log n)   "tie — multiply by log n"
   • log_b(a) < d  →  T(n) = O(n^d)           "top-level work wins"
```

**Walkthrough Example A:** T(n) = 2T(n/2) + n (merge sort)
```
a = 2 (two calls), b = 2 (halving), d = 1 (the +n is n¹)
log_b(a) = log₂(2) = 1
Compare: 1 = 1 → TIE → O(n¹ · log n) = O(n log n) ✓
```

**Walkthrough Example B:** T(n) = 4T(n/2) + n
```
a = 4, b = 2, d = 1
log₂(4) = 2
Compare: 2 > 1 → RECURSION WINS → O(n²)
Why? 4 subproblems of half size = the recursive calls pile up much faster
     than the +n work at each level.
```

**Walkthrough Example C:** T(n) = T(n/2) + n
```
a = 1, b = 2, d = 1
log₂(1) = 0
Compare: 0 < 1 → TOP-LEVEL WORK WINS → O(n)
Why? Only 1 subproblem, so work at bottom is tiny.
     n + n/2 + n/4 + ... = 2n = O(n). The first call does most work.
```

**The Master Theorem in one sentence:** *Who does more work — the recursive calls piling up at the bottom of the tree, or the non-recursive work at the top?*

---

## 1.3.1 Recurrence Relations

### What is a Recurrence Relation?

A **recurrence relation** is an equation that defines a function T(n) in terms of its value at **smaller inputs**. Recurrences arise naturally when analyzing **recursive** algorithms or **divide-and-conquer** algorithms.

**General form for divide-and-conquer:**
```
T(n) = a · T(n/b) + f(n)
```

Where:
- **a** = number of subproblems
- **n/b** = size of each subproblem
- **f(n)** = cost of dividing and combining (non-recursive work)

**Also need a base case:**
```
T(1) = Θ(1)   or   T(n) = Θ(1) for n ≤ c (some constant c)
```

### The Divide-and-Conquer Paradigm

Many important algorithms use the divide-and-conquer pattern:

1. **Divide** the problem into a number of subproblems that are smaller instances of the same problem
2. **Conquer** the subproblems by solving them recursively. If the subproblem is small enough, solve it directly (base case).
3. **Combine** the solutions to the subproblems into the solution for the original problem

### Common Recurrences and Their Sources

| Recurrence | Algorithm | Divide | Conquer | Combine | Solution |
|------------|-----------|--------|---------|---------|----------|
| T(n) = T(n-1) + O(1) | Factorial, linear recursion | — | 1 subproblem of size n-1 | O(1) | **O(n)** |
| T(n) = T(n-1) + O(n) | Selection sort (recursive) | — | 1 subproblem of size n-1 | O(n) | **O(n²)** |
| T(n) = T(n/2) + O(1) | Binary search | O(1) | 1 subproblem of size n/2 | O(1) | **O(log n)** |
| T(n) = T(n/2) + O(n) | Randomized selection (median) | O(n) | 1 subproblem of size n/2 | O(1) | **O(n)** |
| T(n) = 2T(n/2) + O(1) | Binary tree traversal | O(1) | 2 subproblems of size n/2 | O(1) | **O(n)** |
| T(n) = 2T(n/2) + O(n) | Merge sort | O(n) | 2 subproblems of size n/2 | O(n) | **O(n log n)** |
| T(n) = 2T(n-1) + O(1) | Towers of Hanoi | O(1) | 2 subproblems of size n-1 | O(1) | **O(2ⁿ)** |
| T(n) = 7T(n/2) + O(n²) | Strassen's matrix multiply | O(n²) | 7 subproblems of size n/2 | O(n²) | **O(n^2.807)** |
| T(n) = 3T(n/2) + O(n) | Karatsuba-like | O(n) | 3 subproblems of size n/2 | O(n) | **O(n^1.585)** |

### Technicalities: Floors, Ceilings, and Boundary Conditions

In practice, recurrences like T(n) = 2T(n/2) + n should really be:
```
T(n) = T(⌊n/2⌋) + T(⌈n/2⌉) + n
```

We typically **ignore** floors and ceilings because:
- They change the result by at most a constant factor
- The asymptotic solution remains the same

Similarly, boundary conditions like T(1) = 1 vs T(1) = 47 don't affect the asymptotic solution (they only change constants).

---

## 1.3.2 Specifying Runtime of Recursive Algorithms

### Step-by-Step Method to Write Recurrences

**Step 1:** Identify the **base case** and its cost
**Step 2:** Count the **number of recursive calls** made
**Step 3:** Determine the **size of each subproblem**
**Step 4:** Determine the **non-recursive work** (work done outside recursive calls)
**Step 5:** Write the recurrence relation

### Detailed Example 1: Factorial

```
Factorial(n)
    if n = 0 then return 1        ← base case: T(0) = Θ(1)
    else return n × Factorial(n-1) ← 1 recursive call, size n-1
                                     1 multiplication = Θ(1) extra work
```

```
T(n) = T(n-1) + Θ(1)
T(0) = Θ(1)
```

**Solution by repeated substitution (unrolling):**
```
T(n) = T(n-1) + c
     = T(n-2) + c + c
     = T(n-3) + c + c + c
     = T(n-k) + k·c
     ...
     = T(0) + n·c         (when k = n)
     = c + nc
     = Θ(n)
```

### Detailed Example 2: Merge Sort

```
MergeSort(A, lo, hi)
    if lo < hi then
        mid ← ⌊(lo + hi) / 2⌋
        MergeSort(A, lo, mid)        ← first half: T(n/2)
        MergeSort(A, mid+1, hi)      ← second half: T(n/2)
        Merge(A, lo, mid, hi)        ← merging: Θ(n)
```

```
T(n) = 2T(n/2) + Θ(n)
T(1) = Θ(1)
```

**Why does Merge take Θ(n)?** The Merge procedure walks through both sorted halves simultaneously, comparing elements and placing them in order. It visits each of the n elements exactly once.

### Detailed Example 3: Binary Search

```
BinarySearch(A, lo, hi, key)
    if lo > hi then return -1          ← base case: Θ(1)
    mid ← ⌊(lo + hi) / 2⌋            ← Θ(1)
    if A[mid] = key then return mid    ← Θ(1)
    else if key < A[mid] then
        return BinarySearch(A, lo, mid-1, key)    ← one subproblem of size n/2
    else
        return BinarySearch(A, mid+1, hi, key)    ← OR this one (never both)
```

```
T(n) = T(n/2) + Θ(1)       (only ONE recursive call is made)
T(1) = Θ(1)
```

### Detailed Example 4: Quick Sort (Worst Case)

```
QuickSort(A, lo, hi)
    if lo < hi then
        p ← Partition(A, lo, hi)    ← Θ(n)
        QuickSort(A, lo, p-1)       ← T(p - lo)
        QuickSort(A, p+1, hi)       ← T(hi - p)
```

**Worst case:** Partition always produces a 0 and n-1 split (happens when array is already sorted):
```
T(n) = T(0) + T(n-1) + Θ(n) = T(n-1) + Θ(n)
T(1) = Θ(1)
```

**Solution:** T(n) = Θ(n) + Θ(n-1) + ... + Θ(1) = Θ(n²)

**Average case (balanced partition):**
```
T(n) = 2T(n/2) + Θ(n) → Θ(n log n)
```

### Detailed Example 5: Maximum Subarray (Divide and Conquer)

```
FindMaxCrossingSubarray(A, lo, mid, hi)    ← Θ(n)
FindMaxSubarray(A, lo, hi)
    if lo = hi then return A[lo]           ← Θ(1)
    mid ← ⌊(lo + hi) / 2⌋
    FindMaxSubarray(A, lo, mid)            ← T(n/2)
    FindMaxSubarray(A, mid+1, hi)          ← T(n/2)
    FindMaxCrossingSubarray(A, lo, mid, hi) ← Θ(n)
```

```
T(n) = 2T(n/2) + Θ(n) → Θ(n log n)
```

---

## 1.3.3 Master Theorem

> **R1 Reference: Section 4.5 — The Master Method for Solving Recurrences**

The Master Theorem gives an **immediate answer** for recurrences of the standard divide-and-conquer form.

### The Theorem (CLRS Version)

Let a ≥ 1 and b > 1 be constants. Let f(n) be a function. Let T(n) be defined by the recurrence:

```
T(n) = a · T(n/b) + f(n)
```

where n/b means either ⌊n/b⌋ or ⌈n/b⌉. Then T(n) has the following asymptotic bounds:

**The critical value is n^(log_b a)**. This represents the total number of leaves in the recursion tree.

---

#### Case 1: f(n) is polynomially SMALLER than n^(log_b a)

```
If f(n) = O(n^(log_b a - ε)) for some constant ε > 0, then:

    T(n) = Θ(n^(log_b a))
```

**Interpretation:** The **leaves dominate**. The cost of recursion (the subproblems at the bottom of the tree) is much bigger than the non-recursive work at each level. The total cost is dominated by the number of leaves.

---

#### Case 2: f(n) is the SAME as n^(log_b a) (within a log factor)

```
If f(n) = Θ(n^(log_b a)), then:

    T(n) = Θ(n^(log_b a) · log n)
```

**Interpretation:** The work is **evenly distributed** across all levels of the recursion tree. Each level contributes about the same amount of work, and there are log_b n levels, so we multiply by log n.

*Note: The more general CLRS version allows f(n) = Θ(n^(log_b a) · (log n)^k), giving T(n) = Θ(n^(log_b a) · (log n)^(k+1)). For k = 0 we get the standard case above.*

---

#### Case 3: f(n) is polynomially LARGER than n^(log_b a)

```
If f(n) = Ω(n^(log_b a + ε)) for some constant ε > 0,
AND if a · f(n/b) ≤ c · f(n) for some constant c < 1 (the "regularity condition"),
then:

    T(n) = Θ(f(n))
```

**Interpretation:** The **root dominates**. The non-recursive work at the top level is so large that it dominates the entire computation. The subproblems contribute negligible work in comparison.

**The regularity condition** ensures that f(n) doesn't have "pathological" behavior. It says the non-recursive work shrinks by a constant factor at each level. Most functions encountered in practice satisfy this condition (polynomials, etc.).

---

### Visual Summary

```
                                    T(n) = a·T(n/b) + f(n)

    Compare f(n) with n^(log_b a):

    f(n) ≪ n^(log_b a)          f(n) ≈ n^(log_b a)          f(n) ≫ n^(log_b a)
    (polynomially smaller)       (same order)                 (polynomially larger)
           │                           │                             │
           ▼                           ▼                             ▼
    CASE 1: Leaves dominate     CASE 2: All levels equal     CASE 3: Root dominates
    T(n) = Θ(n^(log_b a))      T(n) = Θ(n^(log_b a)·lg n)  T(n) = Θ(f(n))

    The recursion does          Work splits evenly.           The divide/combine
    most of the work.           Multiply by depth.            step does most work.
```

### Detailed Worked Examples

#### Example 1: T(n) = 9T(n/3) + n

```
a = 9,  b = 3,  f(n) = n
n^(log_b a) = n^(log_3 9) = n^2

Compare: f(n) = n  vs  n^2

n = O(n^(2-ε)) with ε = 1?   →  n = O(n¹) = O(n)  ✓

→ Case 1: T(n) = Θ(n²)
```

#### Example 2: T(n) = T(2n/3) + 1

```
a = 1,  b = 3/2,  f(n) = 1
n^(log_b a) = n^(log_{3/2} 1) = n^0 = 1

Compare: f(n) = 1  vs  1

f(n) = Θ(n^0) = Θ(1)  ✓

→ Case 2: T(n) = Θ(log n)
```

#### Example 3: T(n) = 3T(n/4) + n·log n

```
a = 3,  b = 4,  f(n) = n·log n
n^(log_b a) = n^(log_4 3) = n^0.793

Compare: f(n) = n log n  vs  n^0.793

Is n·log n = Ω(n^(0.793 + ε))?
Since n·log n = Ω(n^1) and 0.793 + 0.2 = 0.993 < 1, choose ε ≈ 0.2.  ✓

Check regularity: a·f(n/b) = 3·(n/4)·log(n/4) = (3/4)n·log(n/4) ≤ (3/4)n·log n = (3/4)·f(n)
So c = 3/4 < 1.  ✓

→ Case 3: T(n) = Θ(n log n)
```

#### Example 4: T(n) = 2T(n/2) + n

```
a = 2,  b = 2,  f(n) = n
n^(log_b a) = n^(log_2 2) = n^1 = n

Compare: f(n) = n  vs  n

f(n) = Θ(n) = Θ(n^(log_b a))  ✓

→ Case 2: T(n) = Θ(n log n)     ← Merge sort!
```

#### Example 5: T(n) = 2T(n/4) + 1

```
a = 2,  b = 4,  f(n) = 1
n^(log_b a) = n^(log_4 2) = n^(1/2) = √n

Compare: f(n) = 1  vs  √n

1 = O(n^(1/2 - ε)) with ε = 1/2?  →  1 = O(n^0) = O(1)  ✓

→ Case 1: T(n) = Θ(√n)
```

#### Example 6: T(n) = 2T(n/4) + √n

```
a = 2,  b = 4,  f(n) = √n = n^(1/2)
n^(log_b a) = n^(log_4 2) = n^(1/2)

Compare: f(n) = n^(1/2)  vs  n^(1/2)

f(n) = Θ(n^(1/2))  ✓

→ Case 2: T(n) = Θ(√n · log n)
```

#### Example 7: T(n) = 2T(n/4) + n

```
a = 2,  b = 4,  f(n) = n
n^(log_b a) = n^(1/2) = √n

Compare: f(n) = n  vs  √n

n = Ω(n^(1/2 + ε)) with ε = 1/2?  →  n = Ω(n^1)  ✓

Regularity: 2·(n/4) = n/2 ≤ (1/2)·n = c·f(n) with c = 1/2  ✓

→ Case 3: T(n) = Θ(n)
```

#### Example 8: T(n) = 2T(n/4) + n²

```
a = 2,  b = 4,  f(n) = n²
n^(log_b a) = √n

Compare: f(n) = n²  vs  √n

n² = Ω(n^(1/2 + ε)) with ε = 3/2?  →  n² = Ω(n^2)  ✓

Regularity: 2·(n/4)² = 2n²/16 = n²/8 ≤ (1/8)·n² with c = 1/8  ✓

→ Case 3: T(n) = Θ(n²)
```

### Quick Reference Table

| Recurrence | a | b | f(n) | n^(log_b a) | Case | T(n) |
|------------|---|---|------|-------------|------|------|
| T = 2T(n/2) + n | 2 | 2 | n | n | 2 | Θ(n log n) |
| T = T(n/2) + 1 | 1 | 2 | 1 | 1 | 2 | Θ(log n) |
| T = 4T(n/2) + n | 4 | 2 | n | n² | 1 | Θ(n²) |
| T = T(n/2) + n | 1 | 2 | n | 1 | 3 | Θ(n) |
| T = 2T(n/2) + 1 | 2 | 2 | 1 | n | 1 | Θ(n) |
| T = 9T(n/3) + n | 9 | 3 | n | n² | 1 | Θ(n²) |
| T = 3T(n/2) + n² | 3 | 2 | n² | n^1.585 | 3 | Θ(n²) |
| T = 8T(n/2) + n² | 8 | 2 | n² | n³ | 1 | Θ(n³) |
| T = 7T(n/2) + n² | 7 | 2 | n² | n^2.807 | 1 | Θ(n^2.807) |
| T = 2T(n/4) + √n | 2 | 4 | √n | √n | 2 | Θ(√n log n) |
| T = T(2n/3) + 1 | 1 | 3/2 | 1 | 1 | 2 | Θ(log n) |
| T = 3T(n/4) + n lg n | 3 | 4 | n lg n | n^0.793 | 3 | Θ(n lg n) |

### When the Master Theorem Does NOT Apply

There are **gaps between the cases** where the Master Theorem gives no answer:

**Gap between Case 1 and Case 2:** f(n) is smaller than n^(log_b a) but NOT polynomially smaller.
```
Example: T(n) = 2T(n/2) + n/log n
Here f(n) = n/log n, and n^(log_b a) = n.
n/log n is smaller than n, but not by a polynomial factor (n/log n is NOT O(n^(1-ε)) for any ε > 0).
```

**Gap between Case 2 and Case 3:** f(n) is larger than n^(log_b a) but NOT polynomially larger.
```
Example: T(n) = 4T(n/2) + n² log n
Here n^(log_b a) = n², and f(n) = n² log n.
f(n) is larger than n², but not polynomially larger (n² log n is NOT Ω(n^(2+ε)) for any ε > 0).
```

**Non-divide form:** The Master Theorem doesn't apply to recurrences like:
```
T(n) = T(n-1) + n      (subtraction, not division)
T(n) = T(√n) + 1       (square root, not division by constant)
T(n) = T(n/3) + T(2n/3) + n   (unequal subproblems)
```

For these, use the **Substitution Method** or the **Recursion Tree Method**.

---

## 1.3.4 Solving Recurrences: Substitution Method & Recursion Tree Method

> **R1 Reference: Section 4.3 (Substitution), Section 4.4 (Recursion Tree)**

### Method 1: The Substitution Method (R1: 4.3)

The substitution method is the most **rigorous** technique. It has two steps:

1. **Guess** the form of the solution (e.g., T(n) = O(n log n))
2. **Prove** the guess is correct using **mathematical induction**

The "guess" part is the hard part. The proof is mechanical once you have a good guess. Use the Recursion Tree Method (or experience) to make good guesses.

#### Complete Example 1: Prove T(n) = 2T(⌊n/2⌋) + n is O(n log n)

**Guess:** T(n) ≤ c·n·lg n for some constant c > 0.

**Inductive hypothesis:** Assume T(k) ≤ c·k·lg k for all k < n. In particular, for k = ⌊n/2⌋:
```
T(⌊n/2⌋) ≤ c·⌊n/2⌋·lg(⌊n/2⌋)
```

**Inductive step:** Substitute into the recurrence:
```
T(n) = 2T(⌊n/2⌋) + n
     ≤ 2·(c·⌊n/2⌋·lg(⌊n/2⌋)) + n
     ≤ 2·(c·(n/2)·lg(n/2)) + n            (since ⌊n/2⌋ ≤ n/2)
     = c·n·lg(n/2) + n
     = c·n·(lg n - lg 2) + n
     = c·n·lg n - c·n + n
     = c·n·lg n + n(1 - c)
     ≤ c·n·lg n                             (holds when c ≥ 1)  ✓
```

**Base case:** We need T(n₀) ≤ c·n₀·lg n₀.
- T(1) = 1, but c·1·lg 1 = 0. So the base case fails for n₀ = 1!
- **Solution:** Choose n₀ = 2 and n₀ = 3 as base cases. T(2) ≤ c·2·lg 2 = 2c, and T(3) ≤ c·3·lg 3. Pick c large enough. ✓

#### Complete Example 2: Prove T(n) = 2T(⌊n/2⌋) + n is Ω(n lg n)

**Guess:** T(n) ≥ c·n·lg n for some constant c > 0.

```
T(n) = 2T(⌊n/2⌋) + n
     ≥ 2·(c·⌊n/2⌋·lg(⌊n/2⌋)) + n
     ≥ 2·(c·(n/2 - 1)·lg(n/2 - 1)) + n         (since ⌊n/2⌋ ≥ n/2 - 1)
     ...this gets messy.
```

**Better approach:** Guess T(n) ≥ c·n·lg n directly for the lower bound. Since we proved both O(n lg n) and Ω(n lg n), we conclude:

```
T(n) = Θ(n lg n)     ← Merge sort's exact asymptotic complexity
```

#### Complete Example 3: T(n) = T(n-1) + n is O(n²)

**Guess:** T(n) ≤ c·n²

**Inductive step:**
```
T(n) = T(n-1) + n
     ≤ c(n-1)² + n
     = cn² - 2cn + c + n
     = cn² + n(1 - 2c) + c
     ≤ cn²                    (holds when c ≥ 1, since 1 - 2c < 0)  ✓
```

#### Complete Example 4: T(n) = T(⌈n/2⌉) + 1 is O(lg n)

**Guess:** T(n) ≤ c·lg(n - a) for constants c, a.

Why not just c·lg n? Because ⌈n/2⌉ ≤ (n+1)/2, and we need a little slack. Subtracting a lower-order term helps.

```
T(n) ≤ c·lg(⌈n/2⌉ - a) + 1
     ≤ c·lg((n+1)/2 - a) + 1
     = c·lg((n + 1 - 2a)/2) + 1
     = c·lg(n + 1 - 2a) - c + 1
     ≤ c·lg(n - a)               (when c ≥ 1 and a ≥ 1)  ✓
```

### Important Technique: Subtracting a Lower-Order Term (R1: 4.3)

Sometimes the straightforward guess **fails** even though the answer is correct. The fix is to **subtract a lower-order term** from your guess.

#### Example: T(n) = 4T(n/2) + n, prove T(n) = O(n²)

**Attempt 1 (fails):**
Guess T(n) ≤ c·n².
```
T(n) = 4T(n/2) + n
     ≤ 4·c·(n/2)² + n
     = 4·c·n²/4 + n
     = cn² + n
```
We get cn² + n, which is **NOT** ≤ cn². We're stuck!

**Attempt 2 (works):**
Guess T(n) ≤ c·n² - d·n (subtract the lower-order term d·n).
```
T(n) = 4T(n/2) + n
     ≤ 4·(c·(n/2)² - d·(n/2)) + n
     = 4·(cn²/4 - dn/2) + n
     = cn² - 2dn + n
     = cn² - dn + n(1 - d)
     ≤ cn² - dn                    (when d ≥ 1)  ✓
```

**Why does this work?** The lower-order term gives us "room" to absorb the extra +n. This is a very common technique from CLRS 4.3.

### Important Technique: Changing Variables (R1: 4.3)

For unusual recurrences, a change of variables can transform them into a familiar form.

#### Example: T(n) = 3T(√n) + log n

Let **m = log n**, so n = 2^m and √n = 2^(m/2).

```
T(2^m) = 3T(2^(m/2)) + m
```

Let S(m) = T(2^m):
```
S(m) = 3S(m/2) + m
```

Now this is a standard form! By Master Theorem: a = 3, b = 2, f(m) = m.
```
m^(log_2 3) = m^1.585
```
f(m) = m = O(m^(1.585-ε)) → Case 1 → S(m) = Θ(m^(lg 3))

Back-substitute: m = lg n, so:
```
T(n) = Θ((lg n)^(lg 3))
```

---

### Method 2: The Recursion Tree Method (R1: 4.4)

The recursion tree method converts a recurrence into a **tree** of recursive calls and sums the cost at each level. It's the most **intuitive** method and is excellent for:
- Getting a **guess** for the substitution method
- Handling **unequal subproblems** where the Master Theorem fails
- Building **visual understanding** of where the work happens

#### Step-by-Step Process

1. **Draw the tree**: Each node represents the non-recursive cost at that call. Children represent recursive calls.
2. **Compute the cost at each level**: Sum the costs of all nodes at the same depth.
3. **Count the number of levels**: Determine the depth of the tree.
4. **Sum across all levels**: Add up the per-level costs.
5. **Evaluate the total**: Often involves geometric series.

---

#### Complete Example 1: T(n) = 3T(n/4) + cn²

**Tree structure:**

```
Level 0:                    cn²
                          / | \
Level 1:          c(n/4)²  c(n/4)²  c(n/4)²
                  /|\       /|\       /|\
Level 2:    9 nodes, each c(n/16)²
                  ...
Level i:    3ⁱ nodes, each c(n/4ⁱ)²
                  ...
Level log₄n: leaves (base case)
```

**Cost at each level:**

| Level | # Nodes | Cost per node | Total cost at level |
|-------|---------|--------------|-------------------|
| 0 | 1 | cn² | cn² |
| 1 | 3 | c(n/4)² = cn²/16 | 3·cn²/16 = (3/16)cn² |
| 2 | 9 | c(n/16)² = cn²/256 | 9·cn²/256 = (3/16)²cn² |
| i | 3ⁱ | c(n/4ⁱ)² | (3/16)ⁱ · cn² |
| log₄n | 3^(log₄n) = n^(log₄3) | Θ(1) | Θ(n^(log₄3)) |

**Total cost:**
```
T(n) = Σ (3/16)ⁱ · cn²   (for i = 0 to log₄n - 1)  +  Θ(n^(log₄3))

Since 3/16 < 1, this is a DECREASING geometric series.

T(n) < cn² · Σ (3/16)ⁱ   (for i = 0 to ∞)
     = cn² · 1/(1 - 3/16)
     = cn² · 16/13
     = O(n²)
```

**Verification:** The root cost cn² dominates. This matches **Master Theorem Case 3** (a=3, b=4, d=2, log₄3 ≈ 0.793 < 2). ✓

---

#### Complete Example 2: T(n) = 2T(n/2) + cn (Merge Sort)

```
Level 0:              cn                       Total: cn
                     /    \
Level 1:          cn/2    cn/2                 Total: cn
                 / \      / \
Level 2:      cn/4 cn/4 cn/4 cn/4             Total: cn
               ...                                 ...
Level i:    2ⁱ nodes, each cn/2ⁱ              Total: cn
               ...
Level lg n: n nodes, each c                   Total: cn
```

**Key observation:** Every level costs exactly cn!

```
T(n) = cn · (lg n + 1) = cn lg n + cn = Θ(n lg n)
```

The ratio between successive levels is (2 · 1/2) = 1, so costs are **equal at every level**. This is the signature of Master Theorem Case 2.

---

#### Complete Example 3: T(n) = T(n/3) + T(2n/3) + cn (Unequal Split)

The Master Theorem **cannot** handle this because the two subproblems have different sizes.

```
Level 0:                        cn                                Total: cn
                               /    \
Level 1:                  cn/3       2cn/3                        Total: cn
                         /    \     /      \
Level 2:            cn/9   2cn/9  2cn/9   4cn/9                   Total: cn
                    ...
```

**Key observations:**
- Each level sums to cn (the total of all node costs at any level)
- But the tree is NOT balanced:
  - **Shortest path** (always go left): n → n/3 → n/9 → ... → 1, depth = log₃ n
  - **Longest path** (always go right): n → 2n/3 → 4n/9 → ... → 1, depth = log_{3/2} n

**Bounding the cost:**
```
Lower bound: cn · log₃ n   (at least log₃ n full levels) = Ω(n lg n)
Upper bound: cn · log_{3/2} n (at most log_{3/2} n levels) = O(n lg n)
```

Since both log₃ n and log_{3/2} n are Θ(lg n):
```
T(n) = Θ(n lg n)
```

---

#### Complete Example 4: T(n) = T(n/2) + cn² (R1: 4.4-2)

```
Level 0:              cn²                     Total: cn²
                      |
Level 1:           c(n/2)² = cn²/4           Total: cn²/4
                      |
Level 2:           c(n/4)² = cn²/16          Total: cn²/16
                      |
Level i:           c(n/2ⁱ)²                  Total: (1/4)ⁱ · cn²
                     ...
Level lg n:        c·1 = c                    Total: c
```

```
T(n) = cn² · Σ (1/4)ⁱ   (for i = 0 to lg n)
     < cn² · 1/(1 - 1/4)
     = cn² · 4/3
     = Θ(n²)
```

The series is **decreasing** — the root dominates (Case 3 behavior).

---

#### Complete Example 5: T(n) = 4T(n/2) + cn (R1: 4.4-7)

```
Level 0:              cn                       Total: cn
                   /  |  |  \
Level 1:      cn/2 cn/2 cn/2 cn/2             Total: 4·cn/2 = 2cn
                 ...
Level i:    4ⁱ nodes, each cn/2ⁱ              Total: 4ⁱ·cn/2ⁱ = (4/2)ⁱ·cn = 2ⁱ·cn
                 ...
Level lg n: 4^(lg n) = n² leaves              Total: Θ(n²)
```

```
T(n) = cn · Σ 2ⁱ   (for i = 0 to lg n - 1) + Θ(n²)
     = cn · (2^(lg n) - 1)/(2 - 1) + Θ(n²)
     = cn · (n - 1) + Θ(n²)
     = Θ(n²)
```

The series is **increasing** — the leaves dominate (Case 1 behavior).

---

#### Complete Example 6: T(n) = 2T(n-1) + 1 (Towers of Hanoi, R1: 4.4-4)

```
Level 0:              1                        Total: 1 = 2⁰
                    /   \
Level 1:          1       1                    Total: 2 = 2¹
                / \     / \
Level 2:      1   1   1   1                   Total: 4 = 2²
               ...
Level i:    2ⁱ nodes, each cost 1             Total: 2ⁱ
               ...
Level n:    2ⁿ leaves                          Total: 2ⁿ
```

```
T(n) = Σ 2ⁱ  (for i = 0 to n)
     = 2^(n+1) - 1
     = Θ(2ⁿ)
```

Note: This is NOT a divide-and-conquer recurrence (n-1, not n/b), so the Master Theorem doesn't apply. The recursion tree works perfectly.

---

### Choosing the Right Method: Decision Flowchart

```
                  Is the recurrence of the form T(n) = aT(n/b) + f(n)?
                           /                              \
                         YES                               NO
                          |                                 |
                Can you compare f(n) with n^(log_b a)?      |
                  /           |           \                  |
                YES          YES          YES                |
              Case 1       Case 2       Case 3               |
                |            |            |                   |
            DONE!        DONE!        DONE!                   |
                                                              |
                                          Is it T(n) = aT(n-c) + f(n)?
                                           /                    \
                                          YES                    NO
                                           |                      |
                                    Use Recursion Tree        Use Recursion Tree
                                    (often exponential         to get a guess,
                                     or polynomial)           then Substitution
                                                              to prove it.
```

### Summary: The Three Methods

| Method | When to Use | Pros | Cons |
|--------|------------|------|------|
| **Master Theorem** | T(n) = aT(n/b) + f(n), standard form | Instant answer, no computation | Limited applicability, gaps between cases |
| **Recursion Tree** | Any recurrence; especially good for intuition and unequal splits | Visual, works broadly, gives numerical feel | Can be imprecise; should verify with substitution |
| **Substitution** | Any recurrence; when you need a rigorous proof | Mathematically rigorous, handles all cases | Requires a good initial guess; proofs can be tricky |

**Recommended workflow (CLRS approach):**
1. **Try the Master Theorem** first — if it applies, you're done
2. **If not**, draw a **Recursion Tree** to build intuition and get a guess
3. **Then verify** with the **Substitution Method** for a rigorous proof

---

# Practice Problems with Solutions

## Set A: Primitive Operations and Growth Rates

**A1.** Count the primitive operations in the following algorithm and give its Big-Θ running time:
```
Algorithm PrefixAverages(X, n)
    Let A be an array of n numbers
    for i ← 0 to n-1 do
        s ← 0
        for j ← 0 to i do
            s ← s + X[j]
        A[i] ← s / (i + 1)
    return A
```

**Solution:**
```
Outer loop runs n times.
For each i, inner loop runs i+1 times.
Total inner iterations = 1 + 2 + 3 + ... + n = n(n+1)/2

Cost = Σ(i=0 to n-1) O(i) = O(n²)

T(n) = Θ(n²)
```

**A2.** Order the following functions by growth rate (slowest to fastest):
```
2^(log n),  n²,  n log n,  2^n,  n^(2/3),  n!,  (log n)²,  √(2n),  n
```

**Solution:**
```
(log n)²  ≺  n^(2/3)  ≺  n  ≺  2^(log n) = n  ≺  n  ≺  n log n  ≺  n²  ≺  2ⁿ  ≺  n!
```
Wait — let's be more careful. Note that 2^(log₂ n) = n. And √(2n) = √2 · √n = Θ(√n) = Θ(n^(1/2)).
```
(log n)²  ≺  n^(1/2) [= √(2n)]  ≺  n^(2/3)  ≺  n [= 2^(log n)]  ≺  n log n  ≺  n²  ≺  2ⁿ  ≺  n!
```

## Set B: Big-Oh Proofs

**B1.** Prove that n² + 3n + 5 = O(n²).

**Solution:**
```
n² + 3n + 5 ≤ n² + 3n² + 5n²     (for n ≥ 1: n ≤ n² and 5 ≤ 5n²)
            = 9n²

Choose c = 9, n₀ = 1.  ✓
```

**B2.** Prove that 2ⁿ ≠ O(n⁴).

**Solution:**
Suppose 2ⁿ ≤ c·n⁴ for all n ≥ n₀. Then c ≥ 2ⁿ/n⁴.
But lim(n→∞) 2ⁿ/n⁴ = ∞ (exponential grows faster than polynomial).
So no finite c exists. Contradiction. ✓

**B3.** Prove or disprove: log(n!) = Θ(n log n).

**Solution:**
```
Upper bound: n! ≤ nⁿ → log(n!) ≤ n log n → log(n!) = O(n log n)

Lower bound: n! ≥ (n/2)^(n/2) (taking only the top half of the product)
→ log(n!) ≥ (n/2) log(n/2) = (n/2)(log n - 1) = Θ(n log n)
→ log(n!) = Ω(n log n)

Therefore log(n!) = Θ(n log n).  ✓
```

## Set C: Master Theorem

**C1.** Solve T(n) = 16T(n/4) + n.
```
a = 16, b = 4, f(n) = n
n^(log_4 16) = n² 
f(n) = n = O(n^(2-1)) → Case 1
T(n) = Θ(n²)
```

**C2.** Solve T(n) = 2T(n/2) + n log n.
```
a = 2, b = 2, f(n) = n log n
n^(log_2 2) = n
f(n) = n log n vs n: larger, but is it polynomially larger?
n log n = Ω(n^(1+ε))? No — log n doesn't grow polynomially.
→ Falls in the GAP — Master Theorem does NOT directly apply in basic form.
→ Use extended Case 2: f(n) = Θ(n · (log n)^1), so T(n) = Θ(n · (log n)²)
```

**C3.** Solve T(n) = 5T(n/3) + n log n.
```
a = 5, b = 3, f(n) = n log n
n^(log_3 5) = n^1.465
f(n) = n log n = O(n^(1.465-ε)) with ε ≈ 0.4? 
Since n log n = O(n^1.1) and 1.1 < 1.465 → yes → Case 1
T(n) = Θ(n^(log_3 5)) ≈ Θ(n^1.465)
```

## Set D: Recursion Tree + Substitution (CLRS Style)

**D1.** Use a recursion tree to solve T(n) = 3T(⌊n/2⌋) + n, then verify by substitution.

**Recursion tree:**
```
Level 0: n                             Total: n
Level 1: 3 × n/2                      Total: 3n/2 = (3/2)n
Level 2: 9 × n/4                      Total: 9n/4 = (3/2)²n
Level i: 3ⁱ × n/2ⁱ                    Total: (3/2)ⁱ · n
Level lg n: 3^(lg n) = n^(lg 3) leaves  Total: Θ(n^(lg 3))
```

```
T(n) = n · Σ(3/2)ⁱ for i=0 to lg n - 1 + Θ(n^(lg 3))
     = n · [(3/2)^(lg n) - 1] / (3/2 - 1) + Θ(n^(lg 3))
     = 2n · [(3/2)^(lg n) - 1] + Θ(n^(lg 3))
     = 2n · [n^(lg(3/2)) - 1] + Θ(n^(lg 3))
     = 2n^(lg 3) - 2n + Θ(n^(lg 3))
     = Θ(n^(lg 3))
```

Since 3/2 > 1, the series is INCREASING, so the leaves dominate.

**Verification by substitution:**
Guess T(n) ≤ cn^(lg 3) - dn.
```
T(n) = 3T(⌊n/2⌋) + n
     ≤ 3(c(n/2)^(lg 3) - d(n/2)) + n
     = 3 · c · n^(lg 3)/2^(lg 3) - 3dn/2 + n
     = 3 · c · n^(lg 3)/3 - 3dn/2 + n      (since 2^(lg 3) = 3)
     = c · n^(lg 3) - 3dn/2 + n
     = c · n^(lg 3) - dn + n(1 - d/2)
     ≤ c · n^(lg 3) - dn                    (when d ≥ 2)  ✓
```

**D2.** Use a recursion tree to solve T(n) = T(n-a) + T(a) + cn where a ≥ 1 is a constant.

**Recursion tree:**
```
Level 0: cn                                Total: cn
Level 1: c(n-a) + ca                      Total: cn
Level 2: c(n-2a) + ca + ca                Total: cn (still!)
...
Level i: c(n-ia) + i·ca                   Total: cn
...
Level n/a: ca + ... + ca                   Total: cn/a · ca = cn
```

All levels have cost cn, and there are n/a + 1 levels.
```
T(n) = cn · (n/a + 1) = cn²/a + cn = Θ(n²)
```

**Verification:**
Guess T(n) ≤ cn².
```
T(n) = T(n-a) + T(a) + cn
     ≤ c(n-a)² + ca² + cn
     = cn² - 2can + ca² + ca² + cn
     = cn² - 2can + 2ca² + cn
     = cn² - c(2an - 2a² - n)
     ≤ cn²                    (for large enough n: 2an - 2a² - n > 0 when n > 2a)  ✓
```

---

*Study guide prepared for Chapter 1: Analyzing Algorithms — BITS Pilani*
*References: Goodrich & Tamassia (2006) Ch 1.1, 1.2; CLRS (2009) Ch 4.3, 4.4, 4.5*
